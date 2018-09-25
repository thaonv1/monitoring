# Một số ghi chép về linux

## Mục lục

## Linux process management

### Thế nào là 1 process

Process là một instance execution chạy trên processor. Nó dùng tất cả resource mà kernel có để hoàn thành task được giao.

Tất cả các process trên Linux được quản lí bởi kiến trúc "task_struct" hay còn được gọi là "process descriptor". Nó chứa tất cả các thông tin để process có thể chạy được.

<img src="https://i.imgur.com/fyzTGO9.png">

### Vòng đời của 1 process

Tất cả các process đều có vòng đời của nó:  creation, execution, termination, và removal.

<img src="https://i.imgur.com/Yi6qW6O.png">

### Thread

Thread là một đơn vị thực hiện được tạo ra trong một tiến trình duy nhất. Nó chạy song song với các thread khác trong cùng một tiến trình. Chúng có thể chia sẻ cùng một tài nguyên như bộ nhớ, không gian địa chỉ, tệp mở, v.v. Chúng có thể truy cập cùng một bộ dữ liệu ứng dụng. Một thread cũng được gọi là Light Weight Process (LWP).

Về cơ bản thì việc tạo 1 thread sẽ ko tốn hiệu năng như việc tạo một process bởi nó ko hề copy resource.

<img src="https://i.imgur.com/6Snd72p.png">

### Process priority and nice level

Process priority là con số quyết định thứ tự của các process sẽ được handle bở cpu, có 2 loại priority là dynamic và static. Process nào có priority cao hơn sẽ có cơ hội lớn hơn được chạy trên cpu.

Kernel sẽ tự điều chỉnh các dynamic priority dựa vào một thuật toán có sẵn. Các user process có thể thay đổi static priority bằng cách thay đổi nice level. Process có static priority cao hơn sẽ phải đợi lâu hơn. Linux có nice level từ 19 (thấp nhất) tới -20 (cao nhất).

### Context switching

Trong quá trình thực hiện process, thông tin về process đang chạy được lưu trong registers trên bộ xử lý và cache của nó. Tập dữ liệu được nạp vào register cho quá trình thực hiện được gọi context. Để chuyển đổi các tiến trình, context của tiến trình đang chạy được lưu trữ và context của tiến trình chạy tiếp theo được khôi phục vào register. process descriptor và kernel mode stack được sử dụng để lưu trữ context. Quá trình chuyển đổi này được gọi là Context switching. Có quá nhiều Context switching ảnh hưởng tới hiệu năng bởi vì bộ vi xử lý phải flush register và cache của nó mỗi lần để nhường chỗ cho quy trình mới.

### Interrupt handling

Việc ngắt kết nối là một trong những task có độ ưu tiên cao nhất trong linux. Nó sẽ báo với kernel để ngắt quá trình thực thi tiến trình càng nhanh càng tốt. Khi mà kernel nhận được tín hiệu Interrupt, nó sẽ phải chuyển process đang chạy sang cái mới, đây là context switching và nếu nhiều thì có thể gay ảnh hưởng tới hiệu năng.

Trong môi tr multiprocessor, Interrupt được quản lí bởi từng processor. Việc gán chặt Interrupt vào một processor có thể tăng hiệu năng.

### Process state

- TASK_RUNNING

- TASK_STOPPED

- TASK_INTERRUPTIBLE

- TASK_UNINTERRUPTIBLE

- TASK_ZOMBIE

<img src="https://i.imgur.com/h0HaurX.png">

### Zombie processes

Khi mà một process bị terminated, nó sẽ mất một time nhất định để hoàn thành các task, trog quá trình đó process được gọi là zombie. Nếu gặp phải trường hợp ko thể kill process vì nó được coi là đã chết, bạn có thể kill tiến trình cha của nó. tuy nhiên nếu tiến trình cha của nó là init, có thể bạn sẽ phải reboot lại máy (reboot giải quyết mọi vấn đề :D ).

## Linux memory architecture

### Physical and virtual memory

Việc lựa chọn giữa 32bit và 64bit đang trở thành vấn đề phổ biến. Người dùng thường quan tâm tới việc tận dụng được hơn 4gb virtual memory.

Đối với kiến trúc 32 bit, kernel chỉ có thể giải quyết trực tiếp gần 1 gb đầu của memory vật lý, các mem lớn hơn sẽ phải map (ánh xạ) vào phần đó (gọi là ZONE_NORMAL). Phần map này hoàn toàn trong suốt vs ứng dụng, tuy nhiên việc allocate memory ở ZONE_HIGHMEM có thể gây vấn đề về hiệu năng.

Việc ko cần map các gb lớn hơn vào ZONE_NORMAL giúp kiến trúc 64 bit giải quyết vấn đề về hiệu năng.

<img src="https://i.imgur.com/m2KoKkp.png">

### Virtual memory addressing layout

Trên kiến trúc 32 bit, số địa chỉ tối đa mà 1 process có thể truy cập chỉ là 4gb. Trên thực thế thì nó sẽ được chia ra 3gb cho user space và 1 gb cho kernel space. Kiến trúc 64 bit thì ko có giới hạn nào cả.

<img src="https://i.imgur.com/g4FFW52.png">

### Virtual memory manager

Trên thực tế Physical memory thường được ẩn đi vì os sẽ map nó với virtual memory. Như đã nói, application sẽ ko allocate memory nhưng nó sẽ request một memory maptreen kernel cũng như virtual memory.
Hình dưới cho thấy virtual ko cần nhât thiết phải được map với physical memory. Nếu ứng dụng của bạn allocate một lượng lớn memory, một vài trong số chúng có thể  được map với swap file trên disk.

application ko trực tiếp ghi xuống disk, nó sẽ ghi vào cache và buffer. `pdflush` kernel threads sẽ flush out toàn bộ dữ liệu trên cache/buffers sang disk nếu nó có time hoặc file size vượt ngoài giới hạn của cache/buffer.

<img src="https://i.imgur.com/8TMr3hx.png">

Phần lớn các os khác sẽ dành ra 1 lượng nhất định cho disk cache, linux thì ko như vậy. Mặc định nó sẽ allocate toàn bộ free memory làm disk cache. Ngoài ra, việc swap trong linuc được sử dụng cho thấy linux đang giải quyết tốt hơn vấn đề hiệu năng.

### Page frame allocation

Page là 1 nhóm addresses trong physical memory (page frame) hoặc virtual mem có size khoảng 4K bytes. Khi một process request một số lượng page, nếu nó còn đủ lượng page free, nó sẽ được cấp nhanh chóng, nhưng nếu ko đủ, kernel sẽ lấy nó từ những process khác hoặc page cache.

### Buddy system

Là thứ giúp kernel manage free pages.

<img src="https://i.imgur.com/8ofJcc1.png">

### Page frame reclaiming

Khi mà pages unavailable khi có request, kernel sẽ có gắng get pages bằng cách release các certain pages (đã được dùng nhưng hiện tại ko còn dùng nữa và vẫn được đánh dấu là active).
Quá trình này gọi là page frame reclaiming và được phụ trách bởi function try_to_free_page() và kswapd kernel thread.

Nó có 1 thuật toán để lấy ra các page, những page ít được sử dụng nhất sẽ được lấy ra trước. kswapd sẽ check danh sách active và inactive list page. Bạn có thể xem bằng câu lệnh `vmstat -a`

### swap

Như đã nói thì virtual memory bao gồm cả physical mem và swap. Linux sẽ check các memory page đã được allocate nhưng ko được sử dụng thường xuyên và move nó qua swap. Như vậy thì nếu swap của bạn đang được dùng, bạn ko cần thiết phải đưa ra cảnh báo hoặc quá lo lắng vì linux đang sử dụng tài nguyên khá tốt.

## Linux file systems

### Virtual file system

VFS là lớp abstraction giữa user process và các định dạng linux file system implementation.

<img src="https://i.imgur.com/CLPaQUU.png">

### Journaling

Trong non-Journaling file system, linux kernel sẽ ghi vào metadata trước rồi mới ghi nội dung thực. Tuy nhiên có thể sẽ rất nguy hiểm nếu quá trình ghi vào metadata bị crash gây tình trạng mất đồng bộ.
Journaling system giải quyết được vấn đề này bằng cách ghi dữ liệu thay đổi vào journal area trước. Tất nhiên là nó sẽ làm giảm hiệu năng, còn tùy thuộc avfo dung lượng được ghi nữa.

<img src="https://i.imgur.com/pcihrk3.png">

## Disk I/O subsystem

### I/O subsystem architecture

<img src="https://i.imgur.com/nS4isVp.png">

### Cache

Việc processor phát triển quá nhanh khiến tốc độ của các thành phần khác bị tụt lại. Vì thế cache được sinh ra để giải quyết vấn đề, nhưng dữ liệu thường xuyên được sử dụng sẽ được lưu lại để tránh việc access vào memory bị chậm.

#### Memory hierarchy

Thay vì để cpu access trực tiếp xuống disk, việc đặt l1, l2 cache, ram hoặc 1 vài caches khác ở giữa sẽ tăng hiệu năng lên rất nhiều.

<img src="https://i.imgur.com/CZ6LXRf.png">

#### Locality of reference

Kĩ thuật này dựa vào 2 nguyên tắc sau:

- Những dữ liệu được dùng gần nhất sẽ được cache lại
- Những dữ liệu liên quan tới dữ liệu đã được dùng sẽ được cache lại

<img src="https://i.imgur.com/Vt8zFQr.png">

#### Flushing a dirty buffer

Khi mà process thay đổi dữ liệu thì nó sẽ thay đổi ở trên ram trước, thời điểm này thì dữ liệu tại đây (dirty buffer) và tại disk là khác nhau và chúng cần được đồng bộ càng sớm càng tốt. Quá trình đó được gọi là flush.

<img src="https://i.imgur.com/v30kuoA.png">
