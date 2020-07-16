# 
- 0: Cai NTP, tắt SElinux

        yum -y install chrony

    - vi /etc/chrony.conf

        - node 1:

                server 1.vn.poo.ntp.org iburst
                server 0.asia.pool.ntp.org iburst
                server 3.asia.pool.ntp.org iburst
                allow CIDR

        - node n:

                server ceph-1 iburst

    - restart chrony



- 1: add repo

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

- 2 yum update

- 3 yum install ceph

- 4 Install pre-requisite packages: yum install snappy leveldb gdisk python-argparse gperftools-libs

- 5: Setup mon
- 6: Setup mgr



- Tạo bucket: `ceph osd crush add-bucket bucket1(name) root`

- Tạo crush rule vào bucket vừa tạo: `ceph osd crush rule create-replicated rule_3(name) bucket1 host`

- Tạo pool vào crush rule vừa tạo: `ceph osd pool create rbd1(name) 20(số PG) rule_3(crush_rule_name)`


- Add osd:

        - Zap disk: for i in {c..p}; do ceph-volume lvm zap /dev/sd${i} --destroy; done 
        
        - Add disk: for i in {c..p}; do ceph-volume lvm prepare --data /dev/sd$i; done

        - Add osd vao crush rule vừa tạo: ceph osd crush add osd.0 0.04(weight) root=bucket1 host=phongdh-test-ceph-1
        - Active osd: 
        
                - cat /var/lib/ceph/osd/ceph-0/fsid
                - ceph-volume lvm activate 0 a0971f17-11ed-40a9-a707-b075f3b961ec


 



- reweight: `ceph osd crush reweight osd.2 0.04`

- Xóa osd:

        - SSH lên host chứa osd
        - ceph osd crush reweight osd.412 0.0(để PG =0, PG chuyển tới osd khác)
        - ceph osd out x
        - ceph osd crush remove osd.x
        - systemctl stop ceph-osd.x
        - ceph auth del osd.x
        - ceph osd rm x
        - ceph-volume lvm zap /dev/sd${x} --destroy


- Tạo block device

        - rbd create --size 1024 rbd1(pool)/img1(name img)
        - rbd ls rbd1(list img trong pool rbd1)
        - rbd feature disable rbd1/img1 object-map fast-diff deep-flatten(disable img1 trong pool rbd1 để map)
        - rbd map rbd1/img1 (map để sử dụng) => /dev/rbdx
        - mkfs.xfs /dev/rbdx (tạo file system)
        - mount /dev/rbdx /mnt (mount để sử dụng)
        
        => Dữ liệu được lưu trên img1 => map xong mount img1 trên bất kỳ host nào đều có thể sử dụng
        
        - Nếu dữ liệu đang được lưu trên 2 host và có sự thay đổi thì umount,rbd unmap rbd1/img1 và map + mount lại trên host chưa đc update để có thể thấy dữ liệu mới => Sử dụng xong thì nên unmap và umount để dữ liệu có thể update khi sử dụng ở lần sau

