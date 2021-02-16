# Advanced Setup

This section contains various diagnostics and tweaks you can run to test and/or boost your Pi's behavior.


## Testing a SATA-to-USB SSD

If you decided to use a SATA SSD because you happen to have one, or found one on sale, and have connected it with a SATA-to-USB adapter, then use these instructions to test if it is fast enough to work well as a staking drive.

***Note:** I strongly encourage you to run this on your Pi and not on a different Linux machine, so you get real, authentic performance metrics as they would appear while running your node.* 

For this test, we're going to run a tool called [fio](https://linux.die.net/man/1/fio).
Note that your SSD will already need to be mounted to your Pi in order to test the SSD's performance.

Run the following commands (assuming a Debian-based system like Ubuntu):
```
$ sudo apt install fio
$ cd <your SSD's mount point>
$ fio --randrepeat=1 --ioengine=libaio --direct=1 --gtod_reduce=1 --name=test --filename=test --bs=4k --iodepth=64 --size=4G --readwrite=randrw --rwmixread=75
$ rm test
```

This will run for a while, depending on how fast your SSD is. An acceptable SSD should take about 45 seconds or so.
The test will output a bunch of stuff, but I've highlighted the important part here:

```
test: (groupid=0, jobs=1): err= 0: pid=109712: Mon Feb 15 23:02:34 2021
  read: IOPS=15.5k, BW=60.4MiB/s (63.4MB/s)(3070MiB/50789msec)
   bw (  KiB/s): min=53664, max=65904, per=99.97%, avg=61878.79, stdev=2147.15, samples=101
   iops        : min=13416, max=16476, avg=15469.69, stdev=536.79, samples=101
  write: IOPS=5171, BW=20.2MiB/s (21.2MB/s)(1026MiB/50789msec); 0 zone resets
   bw (  KiB/s): min=17976, max=21944, per=99.97%, avg=20678.89, stdev=788.59, samples=101
   iops        : min= 4494, max= 5486, avg=5169.71, stdev=197.16, samples=101
```

The things to look for are **`read: IOPS=15.5k, BW=60.4MiB/s`** and **`write: IOPS=5171, BW=20.2MiB/s`**.
To be considered a good SSD for running a node, the **read IOPS should be about 15k** and the **write IOPS should be about 5k**.
The read bandwidth should be around 60 MiB/s, and the write bandwidth should be around 20 MiB/s.
It's possible that you can still run a node with slightly lower numbers, but if you go too much lower, the ETH2 client will begin to stall and you will start missing random attestations.


## Overclocking the Pi

TBD