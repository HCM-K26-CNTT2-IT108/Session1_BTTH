# [BTTH] Cẩm nang sử dụng AI hiệu quả cho tân sinh viên

## Phần giải thích kiến thức nền

| Kỹ thuật | Ý nghĩa ngắn gọn |
|---|---|
| **Bản chất LLM** | LLM là "máy dự đoán từ tiếp theo" (Next Token Prediction), học xác suất chuỗi từ ở giai đoạn Pre-training, không suy nghĩ như con người |
| **Kiểm soát token** | Token là mẩu từ AI xử lý. Context Window (cửa sổ ngữ cảnh) có giới hạn, quá dài thì AI quên đoạn đầu. Cần prompt ngắn gọn, đủ ý, chia nhỏ việc |
| **Chống ảo giác** | Ảo giác (Hallucination) là AI bịa thông tin nghe rất hợp lý. Phải kiểm chứng với nguồn đáng tin |
| **Prompt CREATE** | Khung 6 phần giúp viết prompt rõ ràng (xem Phần 3) |
| **Prompt Chaining** | Chia một nhiệm vụ lớn thành nhiều prompt nhỏ nối tiếp nhau, kết quả bước trước là đầu vào bước sau |

**Vì sao dùng Prompt Chaining?**
1. Mỗi prompt nhỏ nên **ít token**, không tràn Context Window.
2. Mỗi bước tập trung một việc nên AI **ít sai và ít ảo giác** hơn.
3. Người dùng **kiểm tra từng bước** trước khi sang bước sau (đúng tinh thần Human-in-the-loop).

---

## (1) Sơ đồ các bước xâu chuỗi prompt (Prompt Chaining)

```
┌──────────────────────────────────────────────┐
│ PROMPT 0: Khởi tạo (CREATE rút gọn)           │
│ Đặt vai trò, đối tượng, giọng văn, cấu trúc   │
│ cẩm nang 3 phần                               │
└──────────────────────┬───────────────────────┘
                       ▼  (AI xác nhận khung sườn)
┌──────────────────────────────────────────────┐
│ PROMPT 1: Viết Phần 1                         │
│ "Cách AI đoán từ" (LLM hoạt động thế nào)     │
└──────────────────────┬───────────────────────┘
                       ▼  Người kiểm tra, chỉnh sửa
┌──────────────────────────────────────────────┐
│ PROMPT 2: Viết Phần 2                         │
│ "3 cách nhận diện ảo giác của AI"             │
│ (nối tiếp, giữ cùng giọng văn với Phần 1)     │
└──────────────────────┬───────────────────────┘
                       ▼  Người kiểm tra, chỉnh sửa
┌──────────────────────────────────────────────┐
│ PROMPT 3: Viết Phần 3                         │
│ "Cấu trúc prompt mẫu bằng khung CREATE"       │
└──────────────────────┬───────────────────────┘
                       ▼
┌──────────────────────────────────────────────┐
│ PROMPT 4: Tổng hợp                            │
│ Ghép 3 phần + lời mở đầu + lời kết + tóm tắt  │
└──────────────────────┬───────────────────────┘
                       ▼
        ┌─────────────────────────────┐
        │ NGƯỜI THẨM ĐỊNH CUỐI        │
        │ Kiểm chứng thông tin, sửa   │
        │ lỗi, chốt bản cẩm nang      │
        └─────────────────────────────┘
```

**Nguyên tắc nối chuỗi:** sau mỗi bước, **người dùng đọc và kiểm tra** kết quả rồi mới đưa vào bước tiếp theo. Chỉ truyền **bản tóm tắt hoặc đoạn cần thiết** sang bước sau để tiết kiệm token.

---

## (2) Nội dung các prompt thành phần

> Các prompt dưới đây dùng khung **CREATE**: **C**haracter (vai trò), **R**equest (yêu cầu), **E**xamples (ví dụ), **A**djustments (điều chỉnh/ràng buộc), **T**ype of output (định dạng đầu ra), **E**xtras (bổ sung).

### Prompt 0: Khởi tạo

```
[Character] Bạn là một giảng viên Công nghệ thông tin thân thiện, giàu kinh
nghiệm hướng dẫn tân sinh viên.

[Request] Chúng ta sẽ cùng viết một cuốn cẩm nang ngắn tên "Học lập trình
cùng AI an toàn và hiệu quả" dành cho sinh viên năm nhất. Cẩm nang gồm 3 phần:
(1) Cách AI đoán từ, (2) 3 cách nhận diện ảo giác của AI, (3) Cấu trúc prompt
mẫu theo khung CREATE.

[Adjustments] Văn phong dễ hiểu, gần gũi, tránh thuật ngữ khó mà không giải
thích. Mỗi phần dưới 250 chữ. Mình sẽ yêu cầu từng phần một.

[Type of output] Hãy xác nhận bạn đã hiểu bằng 2-3 câu, chưa viết nội dung.
```

### Prompt 1: Phần 1 - Cách AI đoán từ

```
[Character] Giảng viên CNTT thân thiện (như đã đặt ở trên).

[Request] Hãy viết Phần 1 của cẩm nang: "Cách AI đoán từ". Giải thích LLM
hoạt động theo cơ chế Next Token Prediction, khái niệm Pre-training, token
và Context Window. Kết thúc bằng 1 lời khuyên giúp tiết kiệm token.

[Examples] Dùng ví dụ: câu "Con mèo đang ngồi trên..." thì AI tính xác suất:
"thảm" 85%, "ghế" 10%, "mái nhà" 5%. Có thể dùng thêm ví dụ AI cộng sai số
lớn (vì AI không phải máy tính bỏ túi).

[Adjustments] Dưới 250 chữ. Không nói AI "suy nghĩ" hay "hiểu" như người.

[Type of output] Tiêu đề "Phần 1", các đoạn ngắn, có 1 bảng nhỏ nếu cần.

[Extras] Cuối bài thêm 1 câu hỏi tự kiểm tra cho sinh viên.
```

### Prompt 2: Phần 2 - 3 cách nhận diện ảo giác

```
[Character] Giảng viên CNTT thân thiện, giữ cùng giọng văn với Phần 1.

[Request] Viết Phần 2: "3 cách nhận diện ảo giác của AI". Giải thích ngắn
ảo giác (Hallucination) là gì, rồi nêu 3 cách nhận biết và kiểm chứng.

[Examples] Gợi ý 3 cách: (1) Thông tin quá cụ thể nhưng không có nguồn
(tên thư viện, hàm, đường link nghe lạ); (2) Chạy thử code thấy lỗi hoặc hàm
không tồn tại; (3) Hỏi lại hoặc tra chéo với tài liệu chính thức thì AI trả
lời mâu thuẫn.

[Adjustments] Dưới 250 chữ. Mỗi cách kèm 1 ví dụ trong lập trình và cách
kiểm chứng. Không bịa tên thư viện hay đường link thật.

[Type of output] Tiêu đề "Phần 2", danh sách đánh số 3 mục.

[Extras] Thêm 1 lưu ý: AI luôn trả lời rất tự tin dù sai.
```

### Prompt 3: Phần 3 - Khung CREATE

```
[Character] Giảng viên CNTT thân thiện, giữ cùng giọng văn.

[Request] Viết Phần 3: "Cấu trúc prompt mẫu tối ưu bằng khung CREATE".
Giải thích 6 thành phần C-R-E-A-T-E và đưa 1 prompt mẫu hoàn chỉnh cho bài
toán lập trình cụ thể.

[Examples] Bài toán mẫu: nhờ AI giải thích và sửa lỗi vòng lặp for trong
Python cho người mới học.

[Adjustments] Dưới 300 chữ. Prompt mẫu viết rõ từng nhãn [Character],
[Request], [Examples], [Adjustments], [Type of output], [Extras].

[Type of output] Tiêu đề "Phần 3", 1 bảng giải thích 6 thành phần, 1 khối
code chứa prompt mẫu.

[Extras] Thêm lời nhắc: không dán thông tin cá nhân vào prompt.
```

### Prompt 4: Tổng hợp

```
[Request] Ghép Phần 1, 2, 3 ở trên thành cẩm nang hoàn chỉnh "Học lập trình
cùng AI an toàn và hiệu quả". Thêm lời mở đầu (3 câu), bảng tóm tắt 5 nguyên
tắc vàng, và lời kết (2 câu).

[Adjustments] Giữ nguyên nội dung các phần, chỉ chỉnh câu chữ cho liền mạch.
Không thêm thông tin mới chưa kiểm chứng.

[Type of output] Văn bản Markdown có tiêu đề rõ ràng.
```

---

## (3) Cuốn cẩm nang hoàn chỉnh (kết quả từ AI sau khi thẩm định)

---

# Học lập trình cùng AI an toàn và hiệu quả
### Cẩm nang ngắn dành cho tân sinh viên

Chào mừng bạn đến với hành trình lập trình! AI là một người bạn học rất hữu ích, nhưng chỉ khi bạn hiểu nó và dùng đúng cách. Cẩm nang này giúp bạn hiểu AI hoạt động ra sao, tránh bị AI "dẫn lạc đường" và biết cách ra lệnh để có kết quả tốt.

---

## Phần 1: Cách AI đoán từ

AI trò chuyện như ChatGPT là một **mô hình ngôn ngữ lớn (LLM)**. Nó **không suy nghĩ như con người**. Nó là một **máy dự đoán từ tiếp theo** (*Next Token Prediction*).

Khi bạn viết "Con mèo đang ngồi trên...", AI tính xác suất từ nào hay đứng tiếp theo dựa trên hàng tỷ văn bản đã học ở giai đoạn **Pre-training**:

| Từ tiếp theo | Xác suất |
|---|---|
| thảm | 85% |
| ghế | 10% |
| mái nhà | 5% |

AI chọn "thảm" vì xác suất cao nhất, không phải vì nó "hiểu" con mèo.

**Hệ quả quan trọng:** AI không phải máy tính bỏ túi. Hỏi "15.480 + 28.750" nó có thể trả lời sai vì chỉ đang đoán chuỗi số trông hợp lý. Với phép tính, hãy nhờ AI viết code Python rồi chạy.

**Token và Context Window:** AI đọc văn bản theo từng mẩu nhỏ gọi là **token**. Mỗi cuộc trò chuyện có một "bộ nhớ ngắn hạn" giới hạn gọi là **Context Window**. Hội thoại quá dài thì AI dễ quên phần đầu.

**Mẹo tiết kiệm token:** chia việc lớn thành nhiều câu lệnh nhỏ, mỗi lần chỉ đưa thông tin cần thiết.

*Tự kiểm tra: Vì sao AI có thể tính sai một phép cộng đơn giản?*

---

## Phần 2: 3 cách nhận diện ảo giác của AI

**Ảo giác (Hallucination)** là khi AI **bịa ra thông tin sai nhưng nghe rất hợp lý và tự tin**. Vì AI chỉ đoán từ nên nó có thể "đoán" ra thứ không có thật.

**1. Thông tin quá cụ thể nhưng không có nguồn.**
Ví dụ: AI giới thiệu một hàm `list.sort_smart()` trong Python nghe rất hợp lý. *Cách kiểm chứng:* tìm hàm đó trong tài liệu chính thức. Nếu không có, đó là ảo giác.

**2. Code chạy thử bị lỗi hoặc hàm không tồn tại.**
Ví dụ: AI đưa đoạn code dùng thư viện lạ và báo lỗi `ModuleNotFoundError`. *Cách kiểm chứng:* luôn chạy thử code, không copy rồi tin ngay.

**3. Hỏi lại hoặc tra chéo thì câu trả lời mâu thuẫn.**
Ví dụ: hỏi lại cùng câu hỏi bằng cách khác mà AI cho đáp án khác nhau. *Cách kiểm chứng:* đối chiếu với tài liệu chính thức, sách giáo trình hoặc một nguồn đáng tin thứ hai.

**Lưu ý:** AI luôn trả lời rất tự tin dù sai. Giọng điệu tự tin **không** có nghĩa là đúng.

---

## Phần 3: Cấu trúc prompt mẫu bằng khung CREATE

Một prompt tốt giống một đơn đặt hàng rõ ràng. Khung **CREATE** giúp bạn không bỏ sót ý:

| Chữ cái | Thành phần | Ý nghĩa |
|---|---|---|
| **C** | Character | Vai trò bạn muốn AI đóng (giảng viên, lập trình viên...) |
| **R** | Request | Yêu cầu cụ thể bạn cần AI làm |
| **E** | Examples | Ví dụ minh họa hoặc dữ liệu mẫu |
| **A** | Adjustments | Điều chỉnh, ràng buộc (độ dài, giọng văn, điều không được làm) |
| **T** | Type of output | Định dạng kết quả (bảng, danh sách, code...) |
| **E** | Extras | Yêu cầu bổ sung (kiểm tra lại, hỏi ngược nếu thiếu thông tin) |

**Prompt mẫu (giải thích và sửa lỗi vòng lặp for):**

```
[Character] Bạn là giảng viên Python kiên nhẫn dạy người mới bắt đầu.

[Request] Giải thích vì sao đoạn code dưới đây in ra thiếu số 5, rồi sửa lại.

[Examples]
for i in range(1, 5):
    print(i)
Kết quả mong muốn: in ra 1, 2, 3, 4, 5.

[Adjustments] Giải thích ngắn gọn, dưới 150 chữ, dùng từ đơn giản,
không dùng thư viện ngoài.

[Type of output] Gồm 2 phần: "Nguyên nhân" và "Code đã sửa" (trong khối code).

[Extras] Nếu thiếu thông tin thì hãy hỏi lại tôi trước khi trả lời.
Cuối cùng gợi ý 1 bài tập nhỏ để tôi luyện thêm.
```

**Nhắc nhở:** không dán thông tin cá nhân (họ tên, số điện thoại, mật khẩu, khóa API) vào prompt.

---

## 5 nguyên tắc vàng khi học lập trình cùng AI

| # | Nguyên tắc | Lý do |
|---|---|---|
| 1 | AI là máy đoán từ, không phải máy tính hay chuyên gia vô cảm | Hiểu bản chất để không tin mù quáng |
| 2 | Chia nhỏ câu lệnh, chỉ đưa thông tin cần thiết | Tiết kiệm token, tránh tràn Context Window |
| 3 | Luôn kiểm chứng, chạy thử code, tra tài liệu chính thức | Chống ảo giác |
| 4 | Viết prompt theo khung CREATE | Kết quả rõ ràng, đúng ý |
| 5 | Không đưa dữ liệu cá nhân vào AI, và bạn là người chịu trách nhiệm cuối cùng | An toàn và trung thực học thuật |

**Lời kết:** AI giúp bạn học nhanh hơn, nhưng người hiểu bài và chịu trách nhiệm với bài làm vẫn là bạn. Hãy dùng AI như một người hướng dẫn, đừng dùng nó để làm hộ.

---

## Tóm tắt nộp bài

- **(1) Sơ đồ xâu chuỗi:** Prompt 0 (khởi tạo) → Prompt 1 (Phần 1) → Prompt 2 (Phần 2) → Prompt 3 (Phần 3) → Prompt 4 (tổng hợp) → người thẩm định cuối. Có bước người kiểm tra giữa các prompt.
- **(2) Prompt thành phần:** 5 prompt viết theo khung CREATE, mỗi prompt giới hạn độ dài để kiểm soát token.
- **(3) Cẩm nang hoàn chỉnh:** gồm lời mở đầu, 3 phần theo yêu cầu, bảng 5 nguyên tắc vàng và lời kết, tích hợp đủ 4 kỹ thuật: bản chất LLM, kiểm soát token, chống ảo giác, prompt CREATE.
