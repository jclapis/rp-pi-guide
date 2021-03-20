# Setting up Rocket Pool on a Raspberry Pi
### Guide v2.0 - for RP Beta 3.0

![](images/Logo-small.png)

## *Navigation*
- **Overview**
- [Preliminary Setup](Preliminary-Setup.md)
- [Preparing the OS](Preparing-the-OS.md)
  - [Installing Rocket Pool with Docker](Docker.md)
  - [Installing Rocket Pool Natively](Native.md)
- [Overclocking the Pi](Overclocking.md)
- [Setting Up a Grafana Dashboard](Grafana.md)
- [Securing and Maintaining your Node](Security.md)


# Overview

This guide is meant to help set up a Rocket Pool node using a Raspberry Pi.
While the common convention is that this cannot be done reliably, I've worked tirelessly to tweak and optimize a whole lot of settings and have come across a configuration that seems to work very well!

This setup will run **a full ETH1 node (Geth)** and **a full ETH2 node (Nimbus)** on the Pi, making your system contribute to the health of the Etherum network while simultaneously acting as a Rocket Pool node operator.

Whether you're a veteran systems administrator or someone that's new to Linux, don't worry.
This guide will walk you through every step of the process, including what hardware you need to buy, and explain what's going on.


## TL;DR

For people that want a quick view of what configuration to use, here's the summary - note that the rest of the guide tells you *how* to do these things, this is just a quick summary of *what* to do:

- Grab a Pi (8 GB is preferable) and relevant accessories (listed in the [Preliminary Setup](Preliminary-Setup.md) section)
- Grab a USB 3.0+ SSD like the [Samsung T5](https://www.amazon.com/Samsung-T5-Portable-SSD-MU-PA1T0B/dp/B073H552FJ), 1TB is fine
- Install Ubuntu 20.04 Server for arm64
- Format the SSD as `ext4` and set up a mount point for it
- Set up some swap space on it, set `vm.swappiness=6` and set `vm.vfs_cache_pressure=10`
- Install Rocket Pool, either with Docker or natively on your OS
- For Docker users (**the normal, easy choice for inexperienced people**) 
  - Install Rocket Pool using my script that adds support for ARM processors (like the Pi has)
  - Modify docker so it stores all of its stuff on the SSD
  - Choose `geth` as your ETH1 client
  - Choose `nimbus` as your ETH2 client
- For Native users (**the more optimized, but harder to maintain choice for experienced users**)
  - Install Geth as a systemd service
    - Set up a system account for it (no login, no shell)
    - Run it with `--cache 256` for a 4 GB Pi, or `--cache 512` for an 8 GB Pi, and `--maxpeers 12`
    - Use `taskset 0x0c` on it to restrict it to CPU cores 2 and 3
    - Use `ionice -c 3` on it to give it a lower priority on SSD usage
  - Install Nimbus as a systemd service
    - Set up a system account for it (no login, no shell)
    - Run it with `--max-peers=80`
    - Use `taskset 0x01` on it to give it CPU core 0
    - Use `ionice -c 2 -n 0` on it to give it maximum priority on the SSD
  - Install Rocket Pool
    - Put the CLI and daemon into `/usr/local/bin`
    - Add services for the node and the watchtower, using the Nimbus service account
    - Modify the Rocket Pool configuration to match your setup
  - Optionally, install Prometheus and Grafana for metrics viewing


## Change Log

### v2.0 - March 16 2021
- Added updates for Rocket Pool Beta 3.0
- Added a fanless case suggestion
- Added the TL;DR to the overview page
- Added `ionice` to the list of tweaks


## Acknowledgements

I want to thank the following members of the Rocket Pool Discord:
- **RamiRond**, for inspiring me to do this in the first place
- **jakepospischil** for helping me understand Rocket Pool's codebase
- **GreyWizard**, for convincing me to try and beat his NUC with a $80 SBC
- **astoneta**, **contraband.eth**, **robinh00d.SG**, and **Ken** for being my guinea pigs