yumbootstrap
============

yumbootstrap is a tool for installing Yum-based distributions (Red Hat,
CentOS, Fedora) in a chroot directory. Idea behind it is stolen from Debian's
debootstrap. It's planned for yumbootstrap to work along with templates for
`lxc-create`.

There is another project of similar purpose called
[Rinse](http://www.steve.org.uk/Software/rinse/). yumbootstrap differs from
Rinse as follows:

  * yumbootstrap uses Yum to resolve dependencies, so adding packages to
    installation list is easier (no need to track dependencies manually).
  * Using multiple Yum repositories for installation is supported.
  * yumbootstrap checks signatures on installed RPM packages.
  * yumbootstrap doesn't depend on Yum mirror to list directory contents, so
    it's less work to setup local mirror usable with yumbootstrap.

Examples of use
---------------

Listing available suites:

    # /usr/sbin/yumbootstrap --list-suites
    centos-4.0
    centos-4.9
    centos-5
    centos-6
    centos-7
    centos-72
    centos-72-from-mounted-media
    centos-72-from-mounted-media-build-machine
    fedora-17
    fedora-18
    fedora-19
    fedora-20
    fedora-21
    fedora-22
    scientific-7

For *mounted_media suites, the tool is expecting CentOS 7.2 ISO mounted to /media/cdrom

Installing *centos-6* suite to `/mnt/chroot/centos-6-chroot`:

    # /usr/sbin/yumbootstrap --verbose centos-6 /mnt/chroot/centos-6-chroot

Installing *centos-6* suite, including some custom packages:

    # /usr/sbin/yumbootstrap --verbose \
        --include=openssh-server --group=Core \
        centos-6 /mnt/chroot/centos-6-chroot

Installing *centos-6* suite, installing custom packages by hand from parent OS:

    # CHROOT=/mnt/chroot/centos-6-chroot
    # /usr/sbin/yumbootstrap --verbose --no-scripts centos-6 $CHROOT
    # yum --installroot=$CHROOT -c $CHROOT/yumbootstrap/yum.conf install ...
    # /usr/sbin/yumbootstrap --verbose --just-scripts $CHROOT

Installing yumbootstrap
-----------------------

For Debian-based distributions, *dpkg-dev*, *fakeroot*, *debhelper* (7+),
*python*, *python-setuptools*, *python-support*, and (obviously) *yum* are
required.

    dpkg-buildpackage -b -uc
    dpkg -i ../yumbootstrap*.deb

For Red Hat derivatives you need *rpm-build*.

    make srpm
    rpmbuild --rebuild yumbootstrap-*.src.rpm
    yum localinstall --nogpgcheck /usr/src/redhat/RPMS/*/yumbootstrap-*.rpm
