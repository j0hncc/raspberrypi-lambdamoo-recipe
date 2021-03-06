RaspberryPi / LambdaMoo Recipe
===============================

Instructions on running LambdaMoo server cleanly on your Raspberry Pi.

Download the Raspberian image:

    wget http://downloads.raspberrypi.org/images/raspbian/2012-08-16-wheezy-raspbian/2012-08-16-wheezy-raspbian.zip 

'dd' it to the SD card and resize partition the root to completely fill media, otherwise you're stuck with only 2GB of storage on a much larger SD card.  Resizing can be done using a resize tool (in Linux, gparted) or when you first boot the pi...

1. figure out it's IP
2. ssh in (usr/pass is pi/raspberry)
3. run 'sudo raspi-config' and select 'expand_rootfs'

As user root, install some required packages and create a new user named 'moo':

    apt-get install bison telnet ncompress
    adduser moo

As the new user 'moo', get the LambdaMoo server and build in _/home/moo_:

    wget  http://downloads.sourceforge.net/project/lambdamoo/lambdamoo/1.8.1/LambdaMOO-1.8.1.tar.gz?r=http%3A%2F%2Fsourceforge.net%2Fprojects%2Flambdamoo%2F&ts=1345924386&use_mirror=superb-dca2
    tar -zxvf LambdaMOO-1.8.1.tar.gz
    cd ./MOO-1.8.1
    ./configure
    make

As user root get and install the moo start/stop script:

    wget https://raw.github.com/heow/raspberrypi-lambdamoo-recipe/master/moo
    mv moo /etc/init.d/
    chmod +x /etc/init.d/moo
    update-rc.d -f moo defaults

Edit the install script, there are just 3 variables you may want to fiddle with:

    MOONAME=yibmoo
    MOOPORT=7777
    MOOROOT=/home/moo/MOO-1.8.1

As yourself, goto [FreeDNS](http://freedns.afraid.org) and sign up for an account.  _mooo.in_ is an open and awesome domain.  Once you register a subdomain, say _yib.mooo.in_ save the dynamic dns key, it'll look like this:

    U053WEFVTOHUDUlBQU5pSEtEdzo4MjAwNjYy

As user root, add the following line to the to _/etc/rc.local_  It registers your RaspberryPi's IP address with FreeDNS allowing it to be found by name, otherwise you'll have to manually find it on the network.  Substitute your key in the obvious place:

    wget --background -qO- /dev/null -q --read-timeout=0.0 --waitretry=5 --tries=400 http://freedns.afraid.org/dynamic/update.php?INSERTYOURFREEDNSKEY\&address=`hostname -I` 2>&1 > /tmp/register.txt

As user moo:  download LambdaCore or install your own.  It should share the same name as _$MOONAME_.  For instance, if _$MOONAME=yibmoo_, the core file needs to be _/home/moo/yibmoo.db_

    wget http://ftp.lambda.moo.mud.org/pub/MOO/LambdaCore-latest.db
    mv LambdaCore-latest.db yibmoo.db

### May a million moos flourish!
