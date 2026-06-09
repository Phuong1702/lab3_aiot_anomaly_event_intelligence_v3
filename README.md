# LAB 3 - AIoT Anomaly Detection & Event Intelligence

## 1. Giới thiệu dự án

Dự án **LAB 3 - AIoT Anomaly Detection & Event Intelligence** là bài thực hành thuộc học phần **Triển khai, phát triển ứng dụng AI và IoT**.

Mục tiêu của bài lab là xây dựng một hệ thống phát hiện bất thường trong dữ liệu cảm biến IoT, sau đó chuyển kết quả từ mô hình AI thành các sự kiện có ý nghĩa để hỗ trợ cảnh báo và ra quyết định.

Lab 3 tập trung vào bài toán **Anomaly Detection**, tức là phát hiện những dữ liệu hoặc hành vi bất thường trong hệ thống IoT.

Ví dụ:

* Nhiệt độ cảm biến tăng đột ngột.
* Cảm biến bị kẹt giá trị trong thời gian dài.
* Dữ liệu đo được khác xa so với trạng thái bình thường.
* Hệ thống cần cảnh báo cho người vận hành kiểm tra.

---

## 2. Mục tiêu bài lab

Sau khi hoàn thành Lab 3, sinh viên có thể:

* Hiểu bài toán phát hiện bất thường trong hệ thống IoT.
* Biết cách xử lý dữ liệu cảm biến dạng chuỗi thời gian.
* Huấn luyện mô hình phát hiện bất thường bằng Isolation Forest.
* Tạo điểm bất thường `anomaly_score`.
* Xác định dữ liệu có bất thường hay không thông qua `is_anomaly`.
* Phân loại sự kiện bằng `event_type`.
* Đánh giá mức độ nghiêm trọng bằng `severity`.
* Đưa ra quyết định xử lý bằng `decision`.
* Ghi log sự kiện bất thường ra file CSV.
* Triển khai mô hình AI bằng FastAPI.
* Kiểm tra API bằng Swagger UI tại `/docs`.

---

## 3. Công nghệ sử dụng

Dự án sử dụng các công nghệ chính sau:

* Python
* Pandas
* NumPy
* Scikit-learn
* Isolation Forest
* FastAPI
* Uvicorn
* Matplotlib
* Joblib
* Jupyter Notebook

---

## 4. Dataset sử dụng

Bài lab sử dụng dữ liệu cảm biến nhiệt độ môi trường trong hệ thống IoT.

Dataset có dạng dữ liệu chuỗi thời gian, gồm các thông tin như:

* Thời gian đo dữ liệu.
* Giá trị nhiệt độ.
* Nhãn hoặc điểm bất thường sau khi xử lý.

Dữ liệu được dùng để huấn luyện và kiểm tra mô hình phát hiện bất thường.

---

## 5. Cấu trúc thư mục dự án

```text
LAB3/
│
├── data/
│   ├── ambient_temperature_system_failure_labeled.csv
│   └── sample_ambient_temperature_system_failure.csv
│
├── diagrams/
│   ├── lab2_vs_lab3_pipeline.png
│   ├── lab3_event_pipeline_v3.png
│   ├── lab3_pipeline.png
│   ├── model_output_to_event.png
│   ├── score_to_severity_decision.png
│   └── test_vs_deploy.png
│
├── figures/
│   ├── anomaly_detection_result.png
│   └── anomaly_score_over_time.png
│
├── models/
│   ├── anomaly_model_bundle_iforest_v2.joblib
│   ├── isolation_forest_iforest_v1.joblib
│   └── mlp_autoencoder_demo.joblib
│
├── notebooks/
│   └── 01_anomaly_detection_event_intelligence.ipynb
│
├── outputs/
│   ├── anomaly_event_log.csv
│   ├── api_test_result.json
│   ├── autoencoder_metrics.json
│   ├── autoencoder_test_predictions.csv
│   ├── iforest_metrics.json
│   └── iforest_test_predictions.csv
│
├── src/
│   ├── app.py
│   ├── download_data.py
│   ├── plot_results.py
│   ├── test_api.py
│   ├── test_api_local.py
│   ├── train_anomaly.py
│   └── utils.py
│
├── requirements.txt
└── README.md
```

---

## 6. Luồng xử lý của hệ thống

Luồng xử lý chính của Lab 3:

```text
Dữ liệu cảm biến IoT
        ↓
Tiền xử lý dữ liệu
        ↓
Tạo đặc trưng cho model
        ↓
Huấn luyện mô hình Isolation Forest
        ↓
Tính anomaly_score
        ↓
So sánh với threshold
        ↓
Xác định is_anomaly
        ↓
Sinh event_type, severity, decision
        ↓
Ghi log sự kiện
        ↓
Triển khai API bằng FastAPI
```

---

## 7. Ý nghĩa các đầu ra chính

### 7.1. anomaly_score

`anomaly_score` là điểm bất thường do mô hình AI tạo ra.

Điểm này cho biết mức độ khác thường của dữ liệu cảm biến.

Ví dụ:

```json
{
  "anomaly_score": 0.82
}
```

Điểm càng cao thì khả năng bất thường càng lớn.

---

### 7.2. is_anomaly

`is_anomaly` là kết quả xác định dữ liệu có bất thường hay không.

Ví dụ:

```json
{
  "is_anomaly": true
}
```

Ý nghĩa:

* `true`: dữ liệu bất thường.
* `false`: dữ liệu bình thường.

---

### 7.3. event_type

`event_type` cho biết loại sự kiện xảy ra trong hệ thống.

Ví dụ:

```text
NORMAL_TELEMETRY
SPIKE_ANOMALY
SENSOR_STUCK
UNKNOWN_ANOMALY
```

Ý nghĩa:

* `NORMAL_TELEMETRY`: dữ liệu bình thường.
* `SPIKE_ANOMALY`: dữ liệu tăng hoặc giảm đột ngột.
* `SENSOR_STUCK`: cảm biến bị kẹt giá trị.
* `UNKNOWN_ANOMALY`: bất thường chưa xác định rõ loại.

---

### 7.4. severity

`severity` cho biết mức độ nghiêm trọng của bất thường.

Ví dụ:

```text
LOW
MEDIUM
HIGH
CRITICAL
```

Ý nghĩa:

* `LOW`: mức thấp.
* `MEDIUM`: mức trung bình.
* `HIGH`: mức cao.
* `CRITICAL`: mức nghiêm trọng.

---

### 7.5. decision

`decision` là quyết định xử lý của hệ thống sau khi phát hiện sự kiện.

Ví dụ:

```text
NO_ALERT
LOG_ONLY
ALERT_OPERATOR
REQUIRE_HUMAN_CHECK
```

Ý nghĩa:

* `NO_ALERT`: không cần cảnh báo.
* `LOG_ONLY`: chỉ ghi log.
* `ALERT_OPERATOR`: cảnh báo cho người vận hành.
* `REQUIRE_HUMAN_CHECK`: cần con người kiểm tra lại.

---

## 8. Cài đặt môi trường

### Bước 1: Clone project từ GitHub

```bash
git clone https://github.com/Phuong1702/LAB3.git
cd LAB3
```

Nếu tên repository khác, thay link GitHub bằng link repository của bạn.

---

### Bước 2: Tạo môi trường ảo

```bash
python -m venv .venv
```

---

### Bước 3: Kích hoạt môi trường ảo

Trên Windows:

```bash
.venv\Scripts\activate
```

Trên macOS hoặc Linux:

```bash
source .venv/bin/activate
```

---

### Bước 4: Cài đặt thư viện

```bash
pip install -r requirements.txt
```

---

## 9. Cách chạy project

### Bước 1: Chuẩn bị dữ liệu

```bash
python src/download_data.py
```

Lệnh này dùng để tải hoặc chuẩn bị dữ liệu cho bài lab.

---

### Bước 2: Huấn luyện mô hình

```bash
python src/train_anomaly.py
```

Sau khi chạy xong, model sẽ được lưu trong thư mục:

```text
models/
```

Các file kết quả sẽ được lưu trong thư mục:

```text
outputs/
```

---

### Bước 3: Vẽ biểu đồ kết quả

```bash
python src/plot_results.py
```

Sau khi chạy xong, biểu đồ sẽ được lưu trong thư mục:

```text
figures/
```

---

## 10. Chạy FastAPI

Để chạy API, sử dụng lệnh:

```bash
uvicorn src.app:app --reload
```

Sau đó mở trình duyệt và truy cập:

```text
http://127.0.0.1:8000/docs
```

Tại đây có thể kiểm tra các API của hệ thống.

---

## 11. Các API chính

### 11.1. Kiểm tra trạng thái hệ thống

```text
GET /health
```

Kết quả mẫu:

```json
{
  "status": "ok",
  "model_loaded": true
}
```

API này dùng để kiểm tra server và model đã hoạt động hay chưa.

---

### 11.2. Xem thông tin model

```text
GET /model-info
```

API này trả về thông tin về model, threshold, feature đầu vào và các thông số liên quan.

---

### 11.3. Phát hiện bất thường

```text
POST /detect-anomaly
```

API này nhận dữ liệu cảm biến đầu vào và trả về kết quả phát hiện bất thường.

Kết quả mẫu:

```json
{
  "model_output": {
    "anomaly_score": 0.82,
    "threshold_used": 0.55,
    "is_anomaly": true
  },
  "event": {
    "event_type": "SPIKE_ANOMALY",
    "severity": "HIGH",
    "decision": "ALERT_OPERATOR"
  }
}
```

---

## 12. Test API

Có thể test API bằng script:

```bash
python src/test_api.py
```

Hoặc test local bằng:

```bash
python src/test_api_local.py
```

Kết quả test API sẽ được lưu vào:

```text
outputs/api_test_result.json
```

---

## 13. Kết quả đầu ra của bài lab

Sau khi chạy đầy đủ project, hệ thống tạo ra các file kết quả như:

```text
models/anomaly_model_bundle_iforest_v2.joblib
models/isolation_forest_iforest_v1.joblib
outputs/iforest_metrics.json
outputs/autoencoder_metrics.json
outputs/iforest_test_predictions.csv
outputs/autoencoder_test_predictions.csv
outputs/anomaly_event_log.csv
outputs/api_test_result.json
figures/anomaly_detection_result.png
figures/anomaly_score_over_time.png
```

---

