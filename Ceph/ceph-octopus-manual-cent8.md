# Install cluster ceph octopus v15

- Kiểm tra `firewalld, iptables, hostname, chronyd`
- Khai báo từng host vào `/etc/host`

## 1. Cài đặt ceph

- `dnf update`
- Tải repo: `dnf -y install centos-release-ceph-octopus epel-release`

- Cài ceph: `dnf -y install ceph`

- Kiểm tra: `ceph -v`

## 2. Setup cluster

- Tạo UUID: `uuidgen`

- Tạo file config cho ceph: `/etc/ceph/ceph.conf`:

    - Điền `uuid` vừa tạo ở trên vào `fsid`
    - Điền hostname các node mon vào `mon initial members`
    - Điền IP các node mon vào `mon host`
    - cluster network: dải mạng riêng cho cluster ceph thực hiện các công việc backfill, recovery,...
    - Các option còn lại tùy chọn
    - VD:

    ```
    [global]
    fsid = ea7ae86d-3dd6-44dd-ae43-9ec88de68a02
    mon initial members = phongdh-ceph-octopus-3,phongdh-ceph-octopus-2,phongdh-ceph-octopus-1
    mon host = 10.5.10.253,10.5.10.67,10.5.9.247

    public network = 10.5.8.0/22
    cluster network = 10.5.8.0/22

    auth cluster required = cephx
    auth service required = cephx
    auth client required = cephx
    osd journal size = 1024
    osd pool default size = 3
    osd pool default min size = 2
    osd pool default pg num = 333
    osd pool default pgp num = 333
    osd crush chooseleaf type = 1
    ```



- Tạo keyring cho ceph mon: `sudo ceph-authtool --create-keyring /tmp/ceph.mon.keyring --gen-key -n mon. --cap mon 'allow *'`

- Tạo keyring cho ceph admin: `sudo ceph-authtool --create-keyring /etc/ceph/ceph.client.admin.keyring --gen-key -n client.admin --cap mon 'allow *' --cap osd 'allow *' --cap mds 'allow *' --cap mgr 'allow *'`

- Bootstrap-osd keyring: `sudo ceph-authtool --create-keyring /var/lib/ceph/bootstrap-osd/ceph.keyring --gen-key -n client.bootstrap-osd --cap mon 'profile bootstrap-osd' --cap mgr 'allow r'`

- Add key vào ceph.mon.keyring: 

    `sudo ceph-authtool /tmp/ceph.mon.keyring --import-keyring /etc/ceph/ceph.client.admin.keyring`

    `sudo ceph-authtool /tmp/ceph.mon.keyring --import-keyring /var/lib/ceph/bootstrap-osd/ceph.keyring`

- Phân quyền thư mục ceph.mon.keyring: `sudo chown ceph. /tmp/ceph.mon.keyring`

- Tạo monmap: `monmaptool --create --add {hostname} {ip-address} --fsid {uuid} /tmp/monmap`

    - VD: `monmaptool --create --add phongdh-ceph-octopus-1 --fsid ea7ae86d-3dd6-44dd-ae43-9ec88de68a02 /tmp/monmap`

- Tạo thư mục: `sudo mkdir /var/lib/ceph/mon/{cluster-name}-{hostname}`

    - VD: `sudo -u ceph mkdir /var/lib/ceph/mon/ceph-phongdh-ceph-octopus-1`

- Populate mon với monmap và keyring: `sudo -u ceph ceph-mon --mkfs -i {hostname} --monmap /tmp/monmap --keyring /tmp/ceph.mon.keyring`

    - VD: `sudo -u ceph ceph-mon --mkfs -i phongdh-ceph-octopus-1 --monmap /tmp/monmap --keyring /tmp/ceph.mon.keyring`

- Start mon: `sudo systemctl start ceph-mon@phongdh-ceph-octopus-1`

- Bật Messenger v2 Protocol(msgr2): `ceph mon enable-msgr2`

- Kiểm tra: `ceph -s`

## 2.Add mon

- Copy file `/etc/ceph/ceph.conf` và `/etc/ceph/ceph.client.admin.keyring` từ ceph mon đang chạy và phân quyền cho user ceph 2 file đó.

- Tạo thư mục: `sudo mkdir /var/lib/ceph/mon/{cluster-name}-{hostname}`

- Lấy keyring: `ceph auth get mon. -o /tmp/ceph.mon.keyring`

- Lấy monmap: `ceph mon getmap -o /tmp/monmap`

- Chạy lệnh: `sudo ceph-mon -i {hostname} --mkfs --monmap {tmp}/{map-filename} --keyring {tmp}/{key-filename}`

- Start new mon: `ceph-mon -i {hostname} --public-addr {IPADDR}`

## 3.Install ceph mgr

- Tạo thư mục cho ceph mgr :`mkdir /var/lib/ceph/mgr/{cluster-name}-{hostname}`

    - VD: `mkdir /var/lib/ceph/mgr/ceph-phongdh-ceph-octopus-1`

- Tạo auth key cho ceph mgr: `ceph auth get-or-create mgr.{hostname} mon 'allow profile mgr' osd 'allow *' mds 'allow *'`

    - VD: `ceph auth get-or-create mgr.phongdh-ceph-octopus-1 mon 'allow profile mgr' osd 'allow *' mds 'allow *'`

- Chuyển key vào thư mục ceph: `ceph auth get-or-create mgr.{hostname} > /etc/ceph/ceph.mgr.admin.keyring`

    - VD: `ceph auth get-or-create mgr.phongdh-ceph-octopus-1 > /etc/ceph/ceph.mgr.admin.keyring`

- Copy keyring vào thư mục ceph mgr: `cp /etc/ceph/ceph.mgr.admin.keyring /var/lib/ceph/mgr/{cluster-name}-{hostname}/keyring`

- Phân quyền cho các thư mục ceph mgr: 

    - `chown ceph. /etc/ceph/ceph.mgr.admin.keyring`
    - `chown -R ceph. /var/lib/ceph/mgr/{cluster-name}-{hostname}`

- Start và enable ceph mgr:

    - `systemctl start ceph-mon@phongdh-ceph-octopus-1`
    - `systemctl enable ceph-mon@phongdh-ceph-octopus-1`

- Active mgr: `ceph-mgr -i {hostname}`