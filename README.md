# I. Continual Learning (Học liên tục)
*Continual Learning* (học liên tục) là quá trình cập nhật mô hình khi có dữ liệu mới; điều này giúp cho mô hình cập nhật khi có phát sinh dữ liệu theo thời gian thực. Với bất kì một mô hình học máy có giám sát nào, quá trình này đều có thể được áp dụng.

- Tên là học liên tục, nhưng không phải quá trình này sẽ được áp dụng tại mọi thời điểm mà thường sẽ được áp dụng một cách định kì (ví dụ như 512 hoặc 1024 mircros-batches) tùy thuộc vào bài toán cũng như mô hình để chọn ra khoảng thời gian định kì hợp lí nhất.
## Lí do áp dụng Continual Learning
Lí do chủ yếu là để bắt kịp với sự thay đổi của dữ liệu, ví dụ như trường hợp sự thay đổi của dữ liệu là nhanh chóng và không đoán trước được; thu thập dữ liệu cho một sự kiện; thu thập dữ liệu cho đối tượng mới mà không hề có dữ liệu lịch sử liên quan.

## Các kiểu huấn luyện với Continual Learning
### 1. Huấn luyện bất trạng thái - Stateless retraining
Thực hiện huấn luyện lại mô hình với trọng số khởi tạo ngẫu nhiên và dữ liệu mới. Đây là phương pháp thường được sử dụng. Ngoài ra, các dữ liệu cũ cũng có thể được tập hợp lại và thực hiện huấn luyện theo cách này để hiệu chỉnh mô hình.

### 2. Huấn luyện trạng thái - Stateful training (hay còn gọi là fine-tuning, incremental learning)
Sử dụng trọng số rút ra từ lần huấn luyện trước đó và dữ liệu mới.

## Các thách thức
- Cần thu thập dữ liệu chất lượng (được xử lý, chuẩn hóa và gắn nhãn đầy đủ).
- Vấn đề các luồng dữ liệu có nguồn gốc khác nhau, cùng lưu vào một kho lưu trữ cần được đồng bộ về mọi mặt.
- Mô hình cần được thử nghiệm trên dữ liệu mới, dựa thêm vào tần suất cập nhật để đánh giá kĩ lưỡng trước khi xuất bản mới.
- Những dữ liệu thống kê như cực tiểu, cực đại, phương sai cần được xem xét để lựa chọn một kĩ thuật scale dữ liệu mới.
- Cần đánh giá kĩ để lựa chọn một thuật toán tối ưu nhất, nhanh nhất khi thực hiện cập nhật dữ liệu.

## Bốn giai đoạn của Continual Learning

### 1. Giai đoạn 1: Mô hình bắt đầu thể hiện hiệu suất kém hơn mong đợi

### 2. Giai đoạn 2: Lên lịch trình cố định và viết mã nhằm tự động huấn luyện lại mô hình theo phương pháp stateless retraining

Các bước cấp cao của tập lệnh này là:
1. Kéo dữ liệu về.
2. Giảm mẫu hoặc lấy mẫu dữ liệu nếu cần thiết.
3. Trích xuất các tính năng.
4. Xử lý hoặc chú thích nhãn để tạo dữ liệu huấn luyện.
5. Bắt đầu quá trình đào tạo.
6. Đánh giá mô hình mới.
7. Triển khai nó.

### 3. Giai đoạn 3: Lên lịch trình cố định và viết mã nhằm tự động huấn luyện lại mô hình theo phương pháp stateful training

### 4. Giai đoạn 4: Tiếp tục theo dõi (dựa vào các yếu tối như chu kỳ thời gian mô hình kém hiệu quả, hiệu suất, ...) để điều chỉnh lịch trình tái huấn luyện mô hình

# II. Testing Production (Kiểm thử môi trường sản phảm)
Sau cập nhật, mô hình cần phải được thử nghiệm để đảm bảo rằng nó an toàn và tốt hơn so với mô hình hiện tại đang được sử dụng. Quá trình này sẽ tương tự với CI/CD ở trong Công nghệ phần mềm.

## Các chiến lược kiểm thử

### 1. Shadow Deployment
- **Ý tưởng:** Triển khai mô hình mới song song với mô hình hiện tại để đánh giá, so sánh. Kết quả dự đoán là kết quả được cho là tốt nhất giữa hai mô hình (request nhận được của hai mô hình là như nhau).
- **Ưu điểm**
	- Ý tưởng đơn giản, an toàn ngay cả khi mô hình mới gặp lỗi.
	- It is conceptually simple.
	- Mô hình mới sẽ nhận được dữ liệu đầy đủ như mô hình hiện tại
- **Nhược điểm**
	- Không thể được sử dụng khi đo lường hiệu suất của mô hình mà phụ thuộc vào việc quan sát cách người dùng tương tác với các dự đoán.
	- Kỹ thuật này tốn kém tài nguyên khi chạy vì nó tăng gấp đôi số lượng dự đoán ở cùng thời điểm.

### 2. A/B Testing
- **Ý tưởng:** Phân chia một cách ngẫu nhiên các request cho hai mô hình tạm gọi là A và B. Từ đó, so sánh đánh giá chúng (request nhận được của hai mô hình là khác nhau).
- **Ưu điểm:**
	- Vì dự đoán được cung cấp cho người dùng nên kỹ thuật này cho phép bạn nắm bắt đầy đủ cách người dùng phản ứng với các mô hình khác nhau.
  - Thử nghiệm A/B rất dễ hiểu và có rất nhiều thư viện cũng như tài liệu xung quanh nó.
  - Chạy rất rẻ vì chỉ có một dự đoán cho mỗi yêu cầu.
  - Bạn sẽ không cần phải xem xét các trường hợp đặc biệt phát sinh từ các yêu cầu suy luận song song cho các chế độ dự đoán trực tuyến (xem nhược điểm của việc triển khai bóng).
- **Nhược điểm:**
	- Kém an toàn hơn Shadow Deployment khi không thể chắc chắn được kết quả dự đoán trả ra có phải là tối ưu nhất hay không

### 3. Canary Release
- **Ý tưởng:** Triển khai mô hình mới cạnh mô hình hiện tại, ban đầu chưa đưa request về mô hình mới mà dần dần di chuyển qua cho đến khi tất cả request đều di chuyển về mô hình mới. Trong quá trình di chuyển nếu mô hình mới lỗi hoặc không cho kết quả như mong muốn thì lại di chuyển về mô hình hiện tại.
- **Ưu điểm:**
	- Dễ thực hiện, tiết kiệm tài nguyên
	- Có thể kết hợp với A/B testing để thay đổi lượng lưu lượng truy cập mà mỗi mô hình đang sử dụng một cách tự động.
- **Nhược điểm:**
	- Có khả năng gặp khó khăn trong việc xác định sự khác biệt về hiệu suất giữa hai mô hình.
	- Nếu việc di chuyển request không được giám sát cẩn thận, nó sẽ phát sinh rủi ro, tuy nhiên có thể rollback lại được trạng thái trước.

### 4. Interleaving Experiments 
- **Ý tưởng:** Người dùng sẽ nhận được các dự đoán xen kẽ từ cả mô hình A và mô hình B. Sau đó, chúng tôi theo dõi hoạt động của từng mô hình bằng cách đo lường mức độ ưu tiên của người dùng với từng mô hình dự đoán của mô hình (xem người dùng lựa chọn đề xuất của mô hình nào nhiều hơn)
- **Ưu điểm:**
	- Dễ dàng xác định mô hình tốt nhất một cách đáng tin cậy dựa trên sự công bằng khi đề xuất đưa ra là xen kẽ.).
- **Nhược điểm:**
	- Triển khai phức tạp hơn A/B testing.
	- Tăng gấp đôi tài nguyên cho việc tính toán.
	- Chỉ phù hợp với nhiệm vụ đưa ra đề xuất để người dùng lựa chọn, không hiệu quả với nhiệm vụ hồi quy.

### 5. Bandits
- **Ý tưởng:** Bandits là một thuật toán theo dõi hiệu suất hiện tại của từng biến thể mô hình và đưa ra quyết định linh hoạt đối với mọi yêu cầu về việc nên sử dụng mô hình có hiệu suất cao nhất cho đến nay. Bandits quyết định sử dụng mô hình nào là dựa vào chi phí cơ hội (**opportunity cost**), chi phí cơ hội càng thấp thì mô hình càng được ưu tiên. Bandit có nhiều thuật toán. Đơn giản nhất là `epsilon-greedy`. Hai thuật toán phổ biến và mạnh mẽ nhất là `Thompson Sampling` và `Upper Confidence Bound (UCB)`.
- **Ưu điểm:**
	- Bandits cần ít dữ liệu mất mát hơn testing A/B để xác định mô hình nào tốt hơn. (Nếu A/B testing cần 630k mẫu dữ liệu để đạt 95% độ tin cậy thì bandits chỉ cần 12k mẫu).
	- Bandits are more data efficient while simultaneously minimising your **opportunity cost**. In many cases bandits are considered optimal.
	- So sánh với A/B testing, bandits are safer because if a model is really bad, the algorithm will select it less often. Also convergence will be faster so you will be able to eliminate the bad challenger quickly.
- **Nhược điểm:**
	- So với tất cả các chiến lược khác, Bandits khó thực hiện hơn nhiều do cần phải truyền phản hồi vào thuật toán một cách liên tục.
	- Chỉ sử dụng với trường hợp đưa ra dự đoán trực tuyến, cần các vòng phản hồi ngắn để xác định xem dự đoán có tốt hay không và cơ chế truyền phản hồi đó vào thuật toán kẻ cướp để cập nhật kết quả của từng mô hình
	- Không an toàn vì các mô hình mới chiếm lưu lượng truy cập một cách trực tiếp.
   
[Link Reference](https://github.com/serodriguez68/designing-ml-systems-summary/blob/main/09-continual-learning-and-test-in-production.md)
