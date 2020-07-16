- chrony


- disable selinux


- repo:

        cat <<EOF> /etc/yum.repos.d/ceph.repo
        [ceph]
        name=Ceph packages for $basearch
        baseurl=https://download.ceph.com/rpm-nautilus/el7/x86_64/
        enabled=1
        priority=2
        gpgcheck=1
        gpgkey=https://download.ceph.com/keys/release.asc

        [ceph-noarch]
        name=Ceph noarch packages
        baseurl=https://download.ceph.com/rpm-nautilus/el7/noarch
        enabled=1
        priority=2
        gpgcheck=1
        gpgkey=https://download.ceph.com/keys/release.asc

        [ceph-source]
        name=Ceph source packages
        baseurl=https://download.ceph.com/rpm-nautilus/el7/SRPMS
        enabled=0
        priority=2
        gpgcheck=1
        gpgkey=https://download.ceph.com/keys/release.asc
        EOF

- install:

        sudo yum install -y epel-release
        sudo yum install -y ceph-deploy

- user:

        - useradd -m -s /bin/bash ceph-deploy
        - passwd ceph-deploy
        - echo "ceph-deploy ALL = (root) NOPASSWD:ALL" | tee /etc/sudoers.d/ceph-deploy
        - chmod 0440 /etc/sudoers.d/ceph-deploy
        - su - ceph-deploy


- node 1:

        ssh-keygen

        ssh-copy-id ceph-deploy@ceph-1
        ssh-copy-id ceph-deploy@ceph-2
        ssh-copy-id ceph-deploy@ceph-3

