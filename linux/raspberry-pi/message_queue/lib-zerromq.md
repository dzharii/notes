# ZerroMQ on Raspberry PI

* [http://osdevlab.blogspot.com/2015/12/how-to-install-zeromq-package-in.html](http://osdevlab.blogspot.com/2015/12/how-to-install-zeromq-package-in.html)
* [MonsieurV/ZeroMQ-RPi](https://github.com/MonsieurV/ZeroMQ-RPi)
* [A quick and dirty introduction to ZeroMQ](https://blog.scottlogic.com/2015/03/20/ZeroMQ-Quick-Intro.html)
* [Slides: ZeroMQ Is The Answer](https://www.slideshare.net/IanBarber/zeromq-is-the-answer)
* [Slides: Overview of ZeroMQ](https://www.slideshare.net/pieterh/overview-of-zeromq)

```sh
LIBSODIUM_VER=1.0.16
ZMQ_VER=4.1.6
ZMQ_FORK=zeromq4-1
cd ~
echo "installing dependencies"
sudo apt-get install libtool pkg-config build-essential autoconf automake -y

if [ ! -f libsodium-${LIBSODIUM_VER}.tar.gz ]; then
    echo "download libsodium"
    wget https://github.com/jedisct1/libsodium/releases/download/${LIBSODIUM_VER}/libsodium-${LIBSODIUM_VER}.tar.gz
fi

tar -zxvf libsodium-${LIBSODIUM_VER}.tar.gz
cd libsodium-${LIBSODIUM_VER}
./configure
make
sudo make install

echo "zerromq"
cd ~
if [ ! -f zeromq-${ZMQ_VER}.tar.gz ]; then
    echo "download zmq"
    wget https://github.com/zeromq/${ZMQ_FORK}/releases/download/v${ZMQ_VER}/zeromq-${ZMQ_VER}.tar.gz
fi
tar -zxvf zeromq-${ZMQ_VER}.tar.gz
cd zeromq-${ZMQ_VER}
./configure
make
sudo make install
sudo ldconfig

```
