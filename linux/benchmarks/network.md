Copied from [How do you test the network speed between two boxes?](https://askubuntu.com/questions/7976/how-do-you-test-the-network-speed-between-two-boxes)

I use `iperf`. It's a client server arrangement in that you run it in server mode at one end and connect to its from another computer on the other side of the network.

One both machines run:

    sudo apt-get install iperf

We'll start an `iperf` server on one of the machines:

    iperf -s

And then on the other computer, tell `iperf` to connect as a client:

    iperf -c <address of other computer>

On the client machine, you'll see something like this:

    oli@bert:~$ iperf -c tim
    ------------------------------------------------------------
    Client connecting to tim, TCP port 5001
    TCP window size: 16.0 KByte (default)
    ------------------------------------------------------------
    [  3] local 192.168.0.4 port 37248 connected with 192.168.0.5 port 5001
    [ ID] Interval       Transfer     Bandwidth
    [  3]  0.0-10.0 sec  1.04 GBytes    893 Mbits/sec

Of course, if you're running a firewall on the server machine, you'll need to allow connections on port 5001 or change the port with the `-p` flag.

-------

You can do pretty much the same thing with plain old `nc` (netcat) if you're that way inclined. On the server machine:

    nc -vvlnp 12345 >/dev/null

And the client can pipe a gigabyte of zeros through `dd` over the `nc` tunnel.

    dd if=/dev/zero bs=1M count=1K | nc -vvn 10.10.0.2 12345

As demod:

    $ dd if=/dev/zero bs=1M count=1K | nc -vvn 10.10.0.2 12345
    Connection to 10.10.0.2 12345 port [tcp/*] succeeded!
    1024+0 records in
    1024+0 records out
    1073741824 bytes (1.1 GB) copied, 9.11995 s, 118 MB/s

The timing there is given by `dd` but it should be accurate enough as it can only output as fast the pipe will take it. If you're unhappy with that you could wrap the whole thing up in a `time` call.

Remember that the result is in mega*bytes* so multiply it by 8 to get a mega*bits*-per-second speed. The demo above is running at 944mbps.
