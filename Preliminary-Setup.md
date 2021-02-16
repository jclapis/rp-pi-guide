# Preliminary Setup

To run a Rocket Pool node on a Raspberry Pi, you'll need to first have a working Raspberry Pi. 
If you already have one up and running - great! You can skip down to the end of this page, where you get to choose a Rocket Pool configuration.
If you're starting from scratch, then read on.


## What You'll Need

At a minimum, these are the recommended components that you'll need to buy in order to run Rocket Pool on a Pi:
- A **Raspberry Pi 4 Model B**, preferably the **8 GB model** (though for this setup, a 4 GB will work alright).
- A **USB-C power supply** for the Pi. You want one that provides **at least 3 amps**.
- A **MicroSD card**. It doesn't have to be big, 16 GB is plenty and they're pretty cheap now... but it should be at least a **Class 10 (U1)**.
- A **MicroSD to USB** adapter for your PC. This is needed so you can install the Operating System onto the card before loading it into the Pi.
  If your PC already has one of these, then you don't need to pick up a new one.
- Some **heatsinks**. You're going to be running the Pi under heavy load 24/7, and it's going to get hot. Heatsinks will help so it doesn't throttle itself.
- A 40mm **fan**. Same as the above, the goal is to keep things cool while running your Rocket Pool node.
- A **case with a fan mount** to tie it all together.


You can get a lot of this stuff bundled together for convenience - for example, [Canakit offers a kit](https://www.amazon.com/CanaKit-Raspberry-8GB-Starter-Kit/dp/B08956GVXN) with many components included.
However, you might be able to get it all cheaper if you get the parts separately (and if you have the equipment, you can [3D print your own Pi case](https://www.thingiverse.com/thing:3793664).)

Other components you'll need:
- A **USB 3.0+ Solid State Drive**. The general recommendation is for a **1 TB drive**, but if it's in your budget, a **2 TB drive** is more future-proof.
  - The [Samsung T5](https://www.amazon.com/Samsung-T5-Portable-SSD-MU-PA1T0B/dp/B073H552FJ) is a good example one that will work well.
  - Using a SATA SSD with a SATA-to-USB adapter is kind of hit-or-miss because of [problems like this](https://www.raspberrypi.org/forums/viewtopic.php?f=28&t=245931).
    If you go this route, I've included a performance test you can use to check if it will work or not in the **[[Advanced Setup]]** section.
- An **ethernet cable** for internet access. It should be at least **Cat 5e** rated.
  - Running a node over Wi-Fi is **not recommended**, but if you have no other option, you can do it instead of using an ethernet cable.
- A **UPS** to act as a power source if you ever lose electricity.
  The Pi really doesn't draw much power, so even a small UPS will last for a while, but generally the bigger, the better. Go with as big of a UPS as you can afford.
  Also, I recommend you **attach your modem, router, and other network equipment** to it as well - not much point keeping your Pi alive if your router dies.

Depending on your location, sales, your choice of SSD, and how many of these things you already have, you're probably going to end up spending **around $200 to $500 USD** for a complete setup.


## Installing the Operating System

There are a few varieties of Linux OS that support the Raspberry Pi. For this guide, we're going to stick to **Ubuntu 20.04**.
Ubuntu is a tried-and-true OS that's used around the world, and 20.04 is (at the time of this writing) the latest of the Long Term Support (LTS) versions, which means it will keep getting security patches for a very long time.
If you'd rather stick with a different flavor of Linux like Raspbian, feel free to follow the existing installation guides for that - just keep in mind that this guide is built for Ubuntu, so not all of the instructions may match your OS.

Your big choice here is going to be whether to run the Desktop or the Server image.
The Desktop image comes with a GUI, which means you'll get a desktop environment with graphics, mouse support, menus, and so on.
The Server image is stripped down so it only comes with the bare essentials - no desktop, no mouse, just a terminal. 
It is lighter than the Desktop image and easier on the system resources, so for this guide, I'm going to use the **Server image**.

The fine folks at Canonical have written up [a wonderful guide on how to install the Ubuntu Server image onto a Pi](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#1-overview). 

Follow steps 1 through 4 of the guide above **(do NOT do step 5, because we don't want a desktop on this machine)**.
For the Operating System image, you want to select **Ubuntu Server 20.04.2 LTS (RPi 3/4/400) 64-bit server OS with long-term support for arm64 architectures**.

Once that's complete, your Pi is up and running, and ready to run Rocket Pool! Next up, you need to decide which configuration you want to use.


## Choosing a Rocket Pool Configuration

Rocket Pool has two ways to run: **with Docker**, and **without Docker**. Each has their advantages and disadvantages, so read about them below and choose which one seems best for you.


### Running with Docker

[Docker](https://www.docker.com/resources/what-container) is a neat system that lets people bundle up programs into a fancy box called a Container.
The program, all of its dependencies, its file system layout... all of it gets boxed up into a nice little package.
The Docker engine service can fire these little packages up and run the program inside of them when you're ready to go.

By default, Rocket Pool uses Docker for its setup.
It will download a Docker container for your ETH1 client of choice, one for your ETH2 client of choice, one with all of the special Rocket Pool sauce.
Since they all run in containers, it's extremely easy to install them, start them, stop them, upgrade them, and delete them without actually touching your Raspberry Pi's installed programs or system configuration at all.
Rocket Pool's handy installer script will handle all of the setup, configuration, networking, and permissions management for you with this configuration.
All you need to do is choose which clients you want to use, and press go!

It's also possible to use your own ETH1 and/or ETH2 clients outside of Docker if you already have them, and point Rocket Pool at those.

However, it does come with one downside (sort of).
None of the vendors for ETH1 and ETH2 clients produce official Docker containers that support the Raspberry Pi's ARM64 processor.
That means the Rocket Pool has to build them from scratch and upload them manually each time there's a new version of a client.
Sometimes this might take a while. Either way, you have to trust the images that Rocket Pool team produce.

That being said, **for most people, this is the configuration you should take.** It is by far the easiest to use.

If you want to use the Docker configuration, **[[follow these steps to get set up]]**.


### Running Native (without Docker)

As convenient as Docker is, Rocket Pool doesn't *need* to be run with it.
It's completely possible to set up your own ETH1 and ETH2 clients and the Rocket Pool stack as services running directly on your Pi.
No Docker, no containers, just you, the clients, and Rocket Pool.

The main advantages of doing this are:
- Complete control over the configuration. You pick the user accounts, the permissions, the file system layout, the versions of everything... it's all up to you.
- Easier to run custom / development versions of the ETH clients or Rocket Pool itself that you built from source.

The disadvantages are:
- A lot more effort to set up.
- Requires more technical knowledge to really understand what you're doing when you follow each of the instructions.
  I suppose you could just follow them without understanding what they do, but that seems like a bad idea.
- Not *officially* supported, so you'll have to head to the Rocket Pool Discord to ask for help. There's not nearly as much documentation to help you in case you break something.

In my opinion, this configuration should only be used if you're a developer, if you want to run the absolute latest development prototypes for the clients or for Rocket Pool, or you just *really* don't like Docker.

If you want to use the Native configuration, **[[follow these steps to get set up]]**.