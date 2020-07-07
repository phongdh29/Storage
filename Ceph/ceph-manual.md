- 0: Cai NTP, tắt SElinux

        yum -y install chrony

    - vi /etc/chrony.conf
        - node 1:

                server 1.vn.poo.ntp.org iburst
                server 0.asia.pool.ntp.org iburst
                server 3.asia.pool.ntp.org iburst

        - node n:

                server ceph-1 iburst

    - restart chrony

allow 172.16.14.0/24

- 1: add repo

        [ceph]
        name=Ceph packages for $basearch
        baseurl=https://download.ceph.com/rpm-{ceph-release}/{distro}/$basearch
        enabled=1
        priority=2
        gpgcheck=1
        gpgkey=https://download.ceph.com/keys/release.asc

        [ceph-noarch]
        name=Ceph noarch packages
        baseurl=https://download.ceph.com/rpm-{ceph-release}/{distro}/noarch
        enabled=1
        priority=2
        gpgcheck=1
        gpgkey=https://download.ceph.com/keys/release.asc

        [ceph-source]
        name=Ceph source packages
        baseurl=https://download.ceph.com/rpm-{ceph-release}/{distro}/SRPMS
        enabled=0
        priority=2
        gpgcheck=1
        gpgkey=https://download.ceph.com/keys/release.asc

- 2 yum update
- 3 yum install ceph
- 4 Install pre-requisite packages: yum install snappy leveldb gdisk python-argparse gperftools-libs

- 5: Setup mon
- 6:
