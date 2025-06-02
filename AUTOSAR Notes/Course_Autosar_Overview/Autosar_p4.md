
<h1><summary>7. BSWM Module</summary></h1>
<details>
<h2><summary>7.1. Intoduction</summary></h2>
<details>

Thì giờ mình sẽ đi giải thích chức năng BSWM module chắc cũng là phần quan trọng nhất ròi :v vì các phần sau cũm dell hiểu gì đâu

- Thì BSW Mode Manager là module thực thi việc quản lí Vehicle Mode và Application Mode(đây là các mode có trong xe thoi thì t đoán ví dụ mình thay đổi mode như nào thì SWC hay xe của mình cũm sẽ thay đổi tương ứng và BSW sẽ quản lý 2 cái mode này)
- BSWM sẽ chịu trách nhiệm ứng xử các mode requests từ SWC hoặc các BSW Modules

</details>
</details>

<h1><summary>12.NvM</summary></h1>
<details>
<h2><summary>12.1.NvM Intoduction</summary></h2>
<details>

**Explain about NVRAM Manager**

- Là nơi lưu trữ data sau khi mình tắt nguồn, thì mọi data mình sẽ lưu trữ bên trong Non-volatile memory
- Và cơ bản thì cái NvM này là dùng để quản li các cái dữ liệu non volatile này. Thì cơ bản các application (SWC) chỉ có thể truy cập tới Non-volatile memory khi nó thông qua NVRAM Manager. Cơ bản module này sẽ cung cấp các cái service(synchronous/asynchronous) cho việc quản lý cũm như duy trì các dữ liệu. Và tất cả các cái tựu tượng và protection đều được xử lý bơi NvM.
- Thì các non volatile memory được tạo thành các block. Thì mỗi block sẽ có 1 ID hoặc kiểu admin để quản lý data.
- Và NVRAM Manager cũm quanr lý quá trình đồng bộ giữa RAM và NV Memory.

**So sánh Volatile Memory và Non-Volatile Memory**

Thì so sánh cái này giống như so sánh giữa RAM và Flash vậy.
- Volatile memory: thì sẽ mất dữ liệu khi tắt nguồn, xong các kiểu lưu trữ thì cũm là lưu trữ tạm thời như trên RAM đóa, có thể tí bị thay thế bởi ứng dụng khác. Nhưng mà nó chạy nhanh hơn, cần ít không gian lưu trữ, và nó gần như trực tiếp ảnh hưởng đến hiệu suâts của hệ thống.
- Non-Volatile Memory: thì sẽ không mất dữ liệu khi tắt nguồn, và các dữ liệu được lưu trũ chặn chẽ hơn kiểu khó mất dữ liệu. Nhưng mà nó sẽ có tốc độ chậm hơn so với RAM. Và trong quá trình run-time gần như là nó chỉ được đọc thoi, trừ 1 số ứng dụng riêng ghi toàn bộ vào.

**So sánh Flash memory and EEPROM**
Thì 2 cái này đều lưu rữ non-volatile memory thì Flash sẽ có tốc độ truy cập tới Memory chậm hơn so với EEPROM. Ngoài ra điểm lưu í lơn nhất là Flash sẽ xóa từng khối, còn EEPROM là xóa theo byte, flash cũm sẽ rẻ hơn nữa hehe.
</details>

<h2><summary>12.2. Memory Stack Introduce</summary></h2>
<details>

![System diagram](./Overview_memory_stack.png)

Thì dựa vào sơ đồ ta có thê thấy các lớp như nào, thì ta có câu hỏi là MEMIF làm gì? Thì nó sẽ là lớp trừu tượng của cả 2 EEPROM và Flash. Ví dụ như SWC gọi xuống là muốn lưu vào bộ nhớ không mất dữ liệu. Thì NvM
sẽ xử lý yêu cầu này và gửi xuống cho MemIf và Memif có nhiệm vụ là phân bổ xuống đúng module ví dụ như bên SWC gọi(Fee_Write() thì phải gọi trên flash). Và điều này giúp NvM sẽ không cần quan tâm cách phân luồng truyền -> tái sử dụng cao hơn.


</details>

<h2><summary>12.3. NvM Interaction with other Modules</summary></h2>
<details>

![System Diagram](./NvM_Interact.png)

Dựa vào hình này ta có thể thấy các module sẽ làm việc được với NVM

- EcuC: dùng cho việc init, statup and shutdown
- SchM: dùng cho việc lập lịch vận hành của ECU
- MemIf: truy cập vaò memory
- Dem: dùng để log những lỗi tới NvM operations
- Crc: này check để ktra xem truyền data có đungs không.
Ngoài những cái trên thì NvM cũm có thể làm việc với BswM, Det and Csm. Và nó còn làm việc được với all các SWCs, bằng việc sử dụng NvM thông qua RTE.

</details>

<h2><summary>12.4. Flow of Read and Wite Instructions</summary></h2>
<details>

Bài này chỉ xem thoi.

</details>

<h2><summary>12.5. Basic storage objects</summary></h2>
<details>

Phần này sẽ nói về các storage objects cơ bản 
**RAM Block**
- Thì cái vùng RAM block này sẽ có những đặc điểm cơ bản của RAM là nơi lưu trữ data và CRC value(check lỗi). Thì vùng RAM này có chứa cả những dữ liệu thay đổi được và không. Lạ ở đây cái nó bảo RAM block data được gắn chính xác 1 SWC hoặc BSW module, ở đây có thể nó muốn bảo là mỗi cái SWC sẽ có 1 vùng nhớ RAM riêng biệt.

**NV Block**
- NV block là 1 block non volatile, và nó cũm là logical block được tạo trong Fee/Ea(Flash/EEPROM).
- Thì NV block header sẽ nằm đầu tiên trong NV block nếu mà cơ chế Static Block ID enabled (tức là cái header này là optional có cũm được mà không có cũm được)
- NV block CRC (optinal) nó sẽ được config dựa trên việc người dùng yêu cầu hay không, và nó dùng để ktra lỗi của block.
- Cuối cung thì nội dung của NV Block thì nó sẽ lưu dữ iệu từ người dùng thoi, nhưng í là nó sẽ lưu dữ liệu trực tiếp kiểu lưu trong quá trình run luôn áa. Tức là người dùng ở SWC chỉ cần gọi thông qua API NvM như NvM_WriteBlock là oke hết.
 
**ROM Block**
- Thì cái ROM này cũm như Flash thoi, lưu dữ liệu. Và khác cái là nó sẽ không thể được thay đổi trong quá trình run-time. Nó được sử dụng để lưu data mặc định trong trường hợp NV Block bị lỗi.

</details>

<h2><summary>12.6. Admintration Block</summary></h2>
<details>
Theo như chatgpt thì cái này nó là để lưu trư thông tin quản lý của NV Block thoi, chẳng hạn như quản lí trạng thái hợp lệ của dữ liệu, CRC checksum để kiểm trra lỗi, phiên bản hoặc counter, marker flag khi mà ghi chưa xong.
Nói chung là kể cả đọc xong cái ở trong udemy thì t vẫn thấy nó quản lý bộ nhớ thoi và đọc thì nó bao gồm cả RAM cũm có admintration nữa thì phải.

</details>
<h2><summary>12.7.Block Management Types</summary></h2>
<details>

Khả năng từ bài này sẽ xem tổng quan thoi nên sẽ không có các phần bên dưới

</details>



</details>


<h1><summary>CAN</summary></h1>
<details>

Thì cái phần time quanta ảo quá nên t phải ghi lại
Thì nói qua là các node sẽ đồng bộ về mặt time với nhau, nhưng mà CAN bus là đường dây vật lý nên nhiều lúc sẽ có sai số.
Thì 1 cái bit sẽ có 4 segment:
- sync segment: dùng để bảo à đến lúc bit này bắt đầu này (theo đúng thời gian định sẵn) nhưng trên thực tế đường CAN Bus lúc đó có thể bị lệch.
- propagation segment: cơ bản cái này nó giúp kéo dài thời gian, để cho các Node ở xa nhau về mặt vật lý có đủ thời gian nhận ra nhau. (Ví dụ dây CAN dài 20m ECU A phát tín hiệu cho ECU B, dù trong dây điện đi với tốc độ ánh sáng nhưng vẫn có độ trễ trong việc gửi tín hiệu -> Node B có thể đọc sớm quá -> tạo ra propagation để kéo dài thời gian đọc).
- phase segment 1: dùng để bù thời gian theo đơn vị time quanta mà số lượng bù được bao nhiêu dựa vào SJW(1-4QT tức là nếu nó được set là 1 QT thì bù vào tối đa chỉ có 1QT thoi)
- phase segment 2: dùng để bớt thời gian tính theo đơn vị QT (trường hợp này xảy ra nếu sync segment đến muộn hơn dự kiến thì phải rút ngắn lại)

Oke giờ ví dụ nhóo thì giả sử 2 Node A(gửi), B(nhận) mình thiết lập nó như nhau. Giờ Node A nó gửi nhanh đúng không, tức là cái CAN bus nó xuống mức 0 sớm hơn dự kiến, thì bên B nó mới thấy là à, mức tín hiệu đang bị sớm và từ đó nó sẽ dựa vào cái thời gian mà cả 2 thống nhất và thời gian thực tế truyền nhận mà cái Node B hay node nhận chính là người điều chỉnh Phase Segment 1,2.

</details>

