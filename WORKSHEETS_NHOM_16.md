# BỘ WORKSHEETS THẢO LUẬN NHÓM - NHÓM 16 (AICB-P1)

## WORKSHEET 0: Learning Timeline & Chủ đề (Hoạt động 1)
- **Danh sách kỹ năng tự tin của nhóm:** 
    1. Lập trình Python & Tích hợp API (AI Engineering).
    2. Hiểu biết sâu về quy trình tư vấn & tuyển sinh giáo dục.
- **Mô tả sản phẩm:** AI Admission Assistant - Trợ lý AI dựa trên công nghệ RAG giúp tra cứu quy chế tuyển sinh, điểm chuẩn, học phí và học bổng.
- **Lý do chọn chủ đề:** "Mùa tuyển sinh đã và đang đến gần", từ đó đặt ra yêu cầu cần một hệ thống hỗ trợ 24/7 cho các câu hỏi thường gặp về tuyển sinh.

**Trả lời câu hỏi bắt buộc:**
* **Sản phẩm này giải quyết bài toán gì?** Giải quyết việc quá tải kênh tư vấn trực tiếp trong mùa cao điểm và cung cấp thông tin chính xác từ hàng ngàn trang quy chế một cách tức thì.
* **Ai là người dùng chính?** Học sinh cuối cấp (lớp 12), phụ huynh và cán bộ tư vấn tuyển sinh cần tra cứu nhanh.
* **Vì sao chủ đề này phù hợp để phân tích deployment và cost?** Vì có sự biến động traffic cực lớn theo mùa (Seasonal traffic) và đòi hỏi sự đánh đổi giữa độ chính xác (Cost cho RAG/LLM cao) và tốc độ phản hồi.

---

## WORKSHEET 1: Enterprise Deployment Clinic (Hoạt động 2)
- **Bối cảnh tổ chức:** Phòng Tuyển sinh của trường đại học, sử dụng hệ thống Portal và CRM để quản lý học sinh.
- **Dữ liệu hệ thống:** Quy chế tuyển sinh (PDF/Web), Điểm chuẩn lịch sử, Thông tin cá nhân học sinh (CRM).
- **Đánh giá mức độ nhạy cảm:** **CAO** đối với dữ liệu cá nhân (Nghị định 13) và **TRUNG BÌNH** đối với dữ liệu quy chế (cần đảm bảo tính chính xác tuyệt đối).
- **Lựa chọn mô hình:** **Public Cloud (Hybrid Cloud nếu cần kết nối CRM nội bộ)**.
- **2 Lý do chọn:** 
    1. Khả năng **Elasticity (Tự động co giãn)** cực tốt để xử lý traffic tăng 100 lần vào mùa cao điểm.
    2. Tận dụng các dịch vụ AI Managed Services (như Gemini/OpenAI API) để triển khai nhanh chóng.

**4 Ràng buộc lớn nhất:**
1. **Tính chính xác & Trích dẫn (Hallucination Control):** Phải trích xuất nguồn gốc tài liệu.
2. **Khả năng chịu tải theo mùa (Scalability):** Traffic mùa cao điểm (tháng 4 - tháng 7).
3. **Tính cập nhật (Content Freshness):** Dữ liệu thay đổi theo từng đợt xét tuyển.
4. **Bảo mật dữ liệu cá nhân:** Tuân thủ pháp luật về bảo vệ dữ liệu.

---

## WORKSHEET 2: Cost Anatomy Lab (Hoạt động 3)
- **Ước lượng quy mô:** 500 req/ngày (thường) -> 50.000 req/ngày (cao điểm).
- **Lớp chi phí (Cost Layers):** Compute (App Hosting), Storage (Vector DB), AI Token, Human Review, Maintenance.
- **Dự toán MVP:** ~$72/tháng.

**Trả lời câu hỏi bắt buộc:**
* **Cost driver lớn nhất:** **AI Token phí** (đặc biệt là Input token do context của RAG thường rất lớn).
* **Hidden cost dễ bị quên:** Phí duy trì Vector Database và phí nhân sự thực hiện **Ground-truth labeling** để đánh giá chất lượng AI.
* **Ước lượng lạc quan:** Đang ước lượng phí Token theo trung bình, nhưng nếu người dùng hỏi những câu phức tạp cần nạp nhiều tài liệu, chi phí có thể x3-x5.

---

## WORKSHEET 3: Cost Optimization Debate (Hoạt động 4)
**Các chiến lược được chọn:**
1. **Semantic Caching:**
    - *Tiết kiệm:* Giảm 40-60% phí Token cho các câu hỏi trùng lặp (FAQ).
    - *Lợi ích:* Tốc độ phản hồi gần như tức thì.
    - *Trade-off:* Có thể trả về thông tin cũ nếu cache chưa kịp xóa khi quy chế đổi.
    - *Thời điểm:* Áp dụng ngay lập tức (Day 1).
2. **Model Routing:**
    - *Tiết kiệm:* Dùng Gemini Flash (rẻ) cho câu hỏi chào hỏi, dùng Pro (đắt) cho câu hỏi quy chế.
    - *Lợi ích:* Tối ưu chi phí mà không giảm chất lượng câu trả lời khó.
    - *Trade-off:* Cần thêm một bước phân loại đầu vào (Latency tăng nhẹ).
    - *Thời điểm:* Áp dụng khi traffic đạt trên 2.000 req/ngày.
3. **Context Caching:**
    - *Tiết kiệm:* Giảm phí nạp lại các bộ quy chế dày hàng trăm trang.
    - *Lợi ích:* Tiết kiệm đáng kể cho các phiên chat dài.
    - *Trade-off:* Chỉ khả dụng trên một số Provider (như Gemini).
    - *Thời điểm:* Áp dụng khi triển khai các bộ tài liệu cố định.

---

## WORKSHEET 4: Scaling & Reliability Tabletop (Hoạt động 5)
**Các tình huống và phản ứng:**
1. **Traffic tăng đột biến (Peak):**
    - *Tác động:* Hệ thống chậm hoặc báo lỗi 504.
    - *Phản ứng:* Auto-scaling Serverless, dùng Message Queue để điều phối request.
2. **Provider API Timeout/Lỗi:**
    - *Tác động:* AI không trả lời được.
    - *Phản ứng:* **Fallback Proposal:** Tự động chuyển sang mô hình dự phòng (OpenAI/Local Model) hoặc trả về bộ FAQ tĩnh đã được chuẩn bị sẵn.
3. **Response chậm:** 
    - *Tác động:* User thoát trang do chờ đợi lâu.
    - *Phản ứng:* Áp dụng Streaming và cập nhật UI thông báo tiến trình.

**Metric theo dõi:** Citation Rate (Độ chính xác), Latency (Tốc độ), Token Usage (Chi phí), Error Rate.

---

## WORKSHEET 5: Skills Map & Track Direction (Hoạt động 6)
**Tự chấm điểm năng lực (1-5):**
| Thành viên | Business/Product | Infra/Data/Ops | AI Engineering |
|------------|------------------|----------------|----------------|
| TV 1       | 4                | 3              | 5              |
| TV 2       | 5                | 2              | 4              |
| TV 3       | 3                | 5              | 3              |

- **Điểm mạnh chung:** Khả năng kết hợp giữa am hiểu nghiệp vụ giáo dục, kỹ thuật AI Engineering và hạ tầng Cloud.
- **Track Phase 2:** **AI Engineering / Application Track**.
- **2 Kỹ năng cần bổ sung:** 
    1. Tối ưu Retrieval (Advanced RAG).
    2. DevOps cho AI (LLMOps) để quản lý vòng đời mô hình.
