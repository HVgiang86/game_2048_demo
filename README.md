# game_2048_demo
>  #### *Bạn quá chán các bài code  phương trình, ma trận, vẽ hình, .v.v... nhàm chán? Hãy làm một thứ gì đó vui vui, ví dụ như 1 con game đơn giản. Và 2048 là một ví dụ rất dễ thực hiện*

## I. Giới thiệu
**2048** là một trò chơi giải đố do tác giả Gabriele Cirulli, một lập trình viên web trẻ 19 tuổi người Ý, tạo ra vào tháng 3 năm 2014. Mục tiêu của trò chơi là trượt các khối vuông có mang số trên một lưới vuông để kết hợp chúng lại và tạo ra khối vuông có giá trị 2048
## II. Phân tích GamePlay
1. **2048** chơi trên một lưới vuông 4×4.
2. Mỗi lần di chuyển là một lượt, người chơi sử dụng các phím mũi tên và các khối vuông sẽ trượt theo một trong bốn hướng tương ứng (lên, xuống, trái, phải)
3. Mỗi lượt có một khối có giá trị 2 hoặc 4 sẽ xuất hiện ngẫu nhiên ở một ô trống trên lưới
4. Các khối vuông trượt theo hướng chỉ định cho đến khi chạm đến biên của lưới hoặc chạm vào khối vuông khác. Nếu hai khối vuông có cùng giá trị chạm vào nhau, chúng sẽ kết hợp lại thành một khối vuông có giá trị bằng tổng giá trị hai khối vuông đó (giá trị gấp đôi)
## III. Mô hình hoá
- Từ việc phân thích, nhận thấy đối tượng chúng ta phải thao tác là các ô trên lưới 4x4
- Mỗi ô sẽ gồm 3 chỉ số:
	1. Toạ độ theo chiều ngang hay **Số cột**
	1. Toạ độ theo chiều dọc hay **Số hàng**
	1. Giá trị của số trong ô hay **Giá trị** (ta có thể coi các ô không chứa số là có giá trị bằng 0)
> ****=> sử dụng mảng 2 chiều kích thước 4x4** là tối ưu nhất để lưu trữ các ô****

![](https://upload.wikimedia.org/wikipedia/commons/thumb/6/64/2048_Screenshot.png/220px-2048_Screenshot.png)

## IV. Hoạt động
###### 1. Cùng xét yếu tốc quan trọng nhất: hoạt động của game khi người dùng nhất 1 phím mũi tên
- Cách để lấy được phím người dùng bấm khá đơn giản, bạn có thể tham khảo ở link sau: https://nguyenvanhieu.vn/bat-su-kien-ban-phim-trong-c-cpp/
#####  - Bây giờ giả sử người chơi bấm phím mũi tên sang trái
Cùng xét hàng đầu tiên của mảng 2 chiều:
Các ô sẽ thay đổi vị trí theo các nguyên tắc:
	1. xét theo thứ tự thừ trái sang phải
	2. Các ô sẽ di chuyển sang trái đến khi gặp một ô cùng giá trị thì cộng vào nhau
	3. Nếu không gặp ô cùng giá trị thì di chuyển tới **BIÊN**
	4. Nếu gặp ô có giá trị khác 0 và khác với ô đang xét thì dừng lại bên cạnh. (Có thể coi đây là biên)
##### **Như vậy những thao tác chúng ta cần phải làm là:**
	-  xét ô đầu tiên có giá trị tính từ trái qua phải
	- kiểm tra nó có thể cộng với ô nào ở bên trái nó không
	- Nếu không cộng được với ô nào, nó sẽ di chuyển về sát biên
	- Cập nhật lại vị trí biên: (Ô có giá trị khác sẽ di chuyển đến và va vào ô này, khi đó ô này có thể coi là biên của các ô khác)
##### **Code mẫu:**


    for (int i=0; i<4; i++)
        {
            int border = 0;
            for (int j=0; j<4; j++)
            {
                if (arr[i][j] != 0)
                {
                    bool canAdd = false;
                    //check if can add with another cell
                    for (int k=j-1; k>=border; k--)
                    {
                        if (arr[i][k] != 0 && arr[i][k] != arr[i][j])    break;
                        if (arr[i][k] == arr[i][j])
                        {
                            arr[i][k] += arr[i][j];
                            arr[i][j] = 0;
                            canAdd = true;
                            canMove = true;
                            score+=arr[i][k];
                            border=k+1;
                            break;
                        }
                    }
                    //if cant add, move by director
                    if (!canAdd)
                    {
    
                        for (int k=border; k<j; k++)
                        {
                            if (arr[i][k] == 0)
                            {
                                arr[i][k] = arr[i][j];
                                arr[i][j] = 0;
                                canMove = true;
                                break;
                            }
                        }
                    }
                }
            }
        }
> Trong đoạn code trên tôi có sử dụng thêm 1 biến canMove để kiểm tra rằng các ô có thể di chuyển được theo hướng đó hay không, nó sẽ phục vụ việc sinh ra 1 ô ngẫu nhiên mới

###### 2. Nảy sinh vấn đề
Bên trên chúng ta chỉ xét khi người chơi nhấn mũi tên sang trái, vậy các hướng khác thì sao?
> *Nếu viết code như vậy cho từng hướng thì chương trình sẽ cực kỳ phức tạp và rối. Do mỗi hướng chúng ta sẽ phải sẽ theo chiều khác nhau, có hướng xét từng hàng, có hướng xét từng cột*
###### **Phương án giải quyết là gì??**


------------

> Đến đây, bạn có thể nhận thấy, chúng ta đang dùng ma trận vuông 4x4 để lưu các ô, và nếu chúng ta xoay ma trận này sang trái hoặc sang phải một số lần thì hướng di chuyển sẽ trở thành sang trái như trường hợp trên.

**hay nói cách khác, chúng ta sẽ xử lý như sau đối với từng trường hợp sang phải, lên trên và xuống dưới:**
> 1. Lên trên:
	- Xoay ma trận sang trái 1 lần rồi thực hiện như trên

> 2. Sang phải
	- Xoay ma trận sang trái 2 lần rồi thực hiện

> 3. Xuống dưới 
	- Xoay ma trận sang phải 1 lần rồi thực hiện

**Tất nhiên sau khi xoay sang các hướng khác thì phải xoay trở về như cũ**
##### 3. Sinh ngẫu nhiên ô mới sau khi di chuyển
Khá đơn giản, sử dụng hàm sinh ngẫu nhiên để sinh ra ngẫu nhiên 1 vị trí trong lưới và sinh ra ngẫu nhiên 1 trong 2 số **2** và **4**
Tất nhiên phải kiểm tra vị trí ngẫu nhiên mình sinh ra đã có giá trị hay chưa
## **V. Thiết kế giao diện**
2 cách:
	- Dùng cửa sổ console
	
	- Thiết kế màn hình đồ hoạ với thư viện <winbgim.h> hoặc <graphics.h> tuỳ các bạn:
	
	Cách cài đặt thư viện xem tại đây: https://anotepad.com/notes/xqmsmekg
##**VI. Khái quát lại chương trình**
- Chương trình sẽ sử dụng các biến cục bộ:
**int arr[4][4];**			Mảng lưu các ô

**int undo[4][4];**			Mảng lưu các ô trước khi di chuyển, phục vụ chức năng undo

**int score = 0;**			 Lưu điểm số

**int maxValue;**		Lưu giá trị của ô lớn nhất (đã đạt 2048 hay chưa)

**bool gameStatus = true;**			Trạng thái của game (kết thúc hay chưa)

#### Các hàm trong chương trình:

**void updateGrid();

void turnLeftMatrix(int arr[][4],int soLan);

void turnRightMatrix(int arr[][4],int soLan);

void doUp();

void doDown();

void doLeft();

void doRight();

void loop();

void drawGame();

bool isEmpty(int i,int j);

void spawnNumber();

void initGame();

void updateUndo();

void undo();

void checkGame();**
## **VII. Demo**
https://ideone.com/0KZswH
