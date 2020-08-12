# 1. Cài đặt các tùy chọn

- Để  thay thế các option mặc định trong ceph, ta có thể sử dụng `ceph osd set <FLAG>`. Để hủy các cài đặt mà ta đã chỉnh, sử dụng command: `ceph osd unset <FLAG>`

- Các option:

    - noin: ngăn không cho OSD mới đc thêm vào cluster CEPH

    - noout: ngăn không cho OSD out khỏi cluster

    - noup: ngăn không cho OSD có trạng thái up

    - nodown: ngăn không cho OSD có trạng thái down

    - full: làm cho cluster đến ngưỡng full tỉ lệ, ngăn các hoạt động viết

    - pause: CEPH sẽ tạm ngưng việc đọc và ghi, nhưng không ảnh hưởng tới trạng thái OSD

    - nobackfill: CEPH sẽ ngăn các hoạt động backfill

    - norebalance: CEPH sẽ ngăn các hoạt động rebalance

    - norecover: CEPH sẽ ngăn các hoạt động recovery

    - noscrub: CEPH sẽ ngăn các hoạt động scrub

    - nodeep-scrub: CEPH sẽ ngăn các hoạt động deep-scrub

    - notieragent: 

# 2. Các trường hợp sử dụng

- noout: nếu quá thời gian báo cáo mà OSD không gửi báo cáo tới mon, OSD sẽ bị đánh dấu là out. Nếu điều này xảy ra sai, set noout để ngăn OSD bị đánh dấu `out` trong khi chờ fix lỗi.

- nodown: Vấn đề về đường truyền có thể làm gián đoạn quy trình `heartbeat`, OSD vẫn `up` nhưng có thể bị đánh dấu `down`. Set nodown để ngăn OSD bị đánh đấu down trong khi chờ fix lỗi

- full: Nếu cluster gần đạt đến `full_ratio`, ta sẽ set `full` rồi sau đó mở rộng dung lượng cluster

- pause: Khi muốn khắc phục sự cố mà không muốn hoạt động đọc, ghi xảy ra => set pause

- nobackfill: Nếu cần OSD down tạm thời, set nobackfill để CEPH không hoạt động backfill trong khi OSD đó bị down.

- norecover: Nếu muốn thay thế OSD và không muốn PG được recover sang OSD khác trong cluster => set recover để ngăn CEPH recover PG sang OSD khác.

- noscrub và nodeep-scrub: Nếu muốn ngăn việc scrub trong quá trình tải cao do recovery, backfill => set noscrub và nodeep-scrub

- 