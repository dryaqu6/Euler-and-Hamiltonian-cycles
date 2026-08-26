# Bài toán định tuyến giao hàng — chu trình Euler/Hamilton và TSP

Cài đặt và so sánh các thuật toán định tuyến trên đồ thị có trọng số, đóng gói trong lớp `VisualizedDeliveryProblemSolver` (nhận ma trận kề n×n, quy ước `0` = không có cạnh).

Bài toán thực tế: cho một mạng lưới các điểm giao hàng, tìm lộ trình đi qua tất cả các điểm và quay về kho xuất phát với tổng quãng đường nhỏ nhất.

## 🧮 Thuật toán đã cài đặt

| Thuật toán | Loại | Ghi chú |
| :--- | :--- | :--- |
| **Floyd–Warshall** | đường đi ngắn nhất mọi cặp | tiền xử lý cho TSP trên đồ thị **không đầy đủ** |
| **TSP — Quy hoạch động (Held–Karp)** | **lời giải chính xác** | bitmask DP trên trạng thái `(tập đã thăm, đỉnh cuối)`, O(2ⁿ·n²), giới hạn n ≤ 20 |
| **TSP — Nearest Neighbor** | heuristic tham lam | O(n²), dùng làm tour khởi tạo |
| **TSP — 2-opt** | tìm kiếm cục bộ | đảo đoạn, first-improvement, tối đa 1000 vòng lặp |
| **Chu trình Euler — Hierholzer** | duyệt đồ thị | kiểm tra điều kiện mọi đỉnh bậc chẵn, duyệt bằng stack và xoá cạnh dần bằng `Counter` (chịu được đa cạnh), O(V+E) |
| **Chu trình Hamilton** | backtracking | đệ quy có mảng `visited`, kiểm tra cạnh đóng vòng; **không cắt tỉa** → O(n!) tệ nhất |
| *`is_strongly_connected`* | kiểm tra liên thông | DFS bằng stack từ **mọi** đỉnh → O(n³) |

Do TSP chạy trên ma trận khoảng cách đã qua Floyd–Warshall, bài toán **không đòi hỏi đồ thị đầy đủ** — phù hợp với mạng lưới giao hàng thực tế, nơi không phải điểm nào cũng nối trực tiếp với nhau.

## 📊 So sánh

`compare_algorithms_visual()` chạy cả ba lời giải TSP trên **cùng một đồ thị** và vẽ tour cạnh nhau, làm rõ **đánh đổi giữa chất lượng lời giải và chi phí tính toán**:

- **Quy hoạch động** cho tour tối ưu, nhưng độ phức tạp O(2ⁿ·n²) khiến nó chỉ khả thi khi n ≤ 15 trong phần so sánh (n ≤ 20 nếu chạy riêng).
- **Nearest Neighbor + 2-opt** chạy được ở quy mô lớn hơn nhiều và cho tour gần tối ưu, nhưng không có bảo đảm về khoảng cách tới lời giải tối ưu.

Phần demo có trực quan hoá và animation lộ trình bằng `matplotlib` / `networkx`.

## ⚠️ Hạn chế đã biết

- `tsp_2opt` tính lại **toàn bộ** chi phí tour cho mỗi phép thử (`calculate_path_cost`) thay vì cập nhật delta O(1) → mỗi vòng lặp tốn thêm một hệ số n.
- 2-opt dừng ở **cải thiện đầu tiên** (first-improvement); chưa thử best-improvement, Or-opt hay 3-opt.
- Phần so sánh đối chiếu **chi phí tour**, chưa đo thời gian chạy thực tế.
- `find_hamiltonian_cycle` dùng backtracking thuần, không cắt tỉa — chi phí tăng rất nhanh theo n.
- **Không nhất quán hướng của đồ thị:** `find_eulerian_cycle` xử lý đồ thị như **vô hướng** (chỉ duyệt nửa trên ma trận), trong khi `is_strongly_connected` và các lời giải TSP xử lý như **có hướng**. Với ma trận đối xứng thì không ảnh hưởng, nhưng nếu nhập ma trận bất đối xứng thì kết quả Euler sẽ không khớp với phần còn lại.

## 📁 Cấu trúc

```
src/     main_PPS.ipynb    toàn bộ cài đặt + demo có trực quan hoá
report/  final_PPS.pdf     báo cáo
```

## 📖 Nội dung lý thuyết trong báo cáo

1. Hệ thống định nghĩa: ma trận kề, đồ thị liên thông, chu trình.
2. Điều kiện tồn tại chu trình Euler / Hamilton và thuật toán tìm.
3. Ứng dụng vào bài toán tìm đường đi và bài toán phân phát hàng hoá.
