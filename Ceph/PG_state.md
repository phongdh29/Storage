- Placement Group (PG): CEPH sẽ nhóm các object vào PG để quản lý. Tuỳ theo số bản replicate mà PG sẽ phân bổ vào 2 hoặc 3 OSD khác nhau


    <img src="images/PG.png">


- Creating: Khi tạo 1 pool, Ceph sẽ tạo ra PG theo số PG đã chỉ định.

- Peering: Để CEPH có thể xác định trạng thái của PG, các OSD(2 hoặc 3 tùy vào replicate) sẽ thực hiện thỏa thuận về trạng thái của object và metadata trong PG. Khi CEPH hoàn thành peering, có nghĩa là các OSD đều đồng ý về trạng thái hiện tại của PG.

    <img src="images/peering.png">

- Active: sau khi thực hiện thành công quá trình peering, PG có thể có trạng thái active. Các data trên các PG đã ở trạng thái sẵn sàng và thực hiện thành công việc replicate.

- Clean: Khi trạng thái PG là clean, các OSD là thỏa thuận thành công(peering). Ceph đã replicate tất cả các objects trong PG số lần chính xác

- Degraded: khi một OSD bị down, trong quá trình chờ trạng thái OSD trở về up , PG sẽ đi vào trạng thái Degraded. Nếu sau 600s, OSD không trở về trạng thái up, CEPH sẽ thực hiện khôi phục PG sang một OSD , đảm bảo số replicate, sau khi thỏa thuận (peering) thành công, PG trở về trạng thái bình thường(active+clean)

- Recovering : khi một OSD có trạng thái down sau 600s, quá trình Degraded sẽ được chuyển về Recovering, sẽ thực hiện replicate tới các OSD khác

- Backfilling :  khi một OSD mới dược thêm vào cụm, CEPH sẽ cố gắng thực hiện cân bằng tải lại hệ thống bằng cash di chuyển một số PG sang OSD mới.