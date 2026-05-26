%%writefile README.md
# LAB 2 - AIoT Data Preparation + Baseline Model + Deploy Demo

**Thông tin thực hiện:**
- Sinh viên: Nguyễn Doãn Bình An
- Khóa: Sinh viên năm 3 - Đại học Đại Nam
- Lĩnh vực: AI, IoT, Cybersecurity & B.A

---

Dự án này là bài mẫu hướng dẫn quy trình chuẩn bị dữ liệu AIoT (AIoT Data Preparation), xây dựng mô hình phân loại nền tảng (Baseline Model), và triển khai mô hình dưới dạng dịch vụ API (Deploy Demo) bằng FastAPI.

## 1. Tổng Quan Luồng Xử Lý (Pipeline Architecture)

Hệ thống được thiết kế theo mô hình luồng dữ liệu khép kín từ thiết bị đầu cuối cho đến khi đưa ra quyết định điều khiển:

```text
Dữ liệu thô (Telemetry) 
  ──> Làm sạch & Kiểm tra cấu trúc (Data Cleaning & Schema Verification)
  ──> Trích xuất đặc trưng (Feature Engineering)
  ──> Phân chia dữ liệu theo chuỗi thời gian (Time-based Split Train/Test)
  ──> Huấn luyện mô hình nền tảng (Logistic Regression Baseline)
  ──> Tính toán điểm bất thường (Anomaly Score bằng Z-score)
  ──> Động cơ đưa ra quyết định & Gợi ý lệnh điều khiển (Decision Engine & Command Hint)
  ──> Xuất kết quả nhật ký (decision_log.csv) & Lưu mô hình (occupancy_baseline.joblib)
  ──> Triển khai mô hình dưới dạng API Service (FastAPI Deployment)
##2. Cấu Trúc Thư Mục Dự Án
Plaintext
lab2_aiot_public_dataset_deploy/
├── data/
│   ├── DATA_SOURCES.md
│   ├── telemetry_clean.csv                     
│   ├── feature_dataset.csv                     
│   └── occupancy_fallback_same_schema.csv      
├── notebooks/
│   └── 01_data_prep_baseline_deploy_ready.ipynb
├── src/
│   ├── __init__.py
│   ├── data_utils.py                           
│   ├── download_data.py                        
│   ├── run_training_pipeline.py                
│   ├── app.py                                  
│   ├── test_api.py                             
│   └── check_outputs.py                        
├── models/
│   └── occupancy_baseline.joblib               
├── outputs/
│   ├── metrics.json                            
│   ├── decision_log.csv                        
│   └── figures/                                
└── requirements.txt                            
##3. Khởi Tạo Môi Trường Hệ Thống
Yêu cầu hệ thống cài đặt sẵn Python >= 3.8.

Trên Windows (PowerShell)
PowerShell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
Trên macOS / Linux
Bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
##4. Quy Trình Thực Thi Dự Án
Cách 1: Chạy bằng Jupyter Notebook (Thực nghiệm)
Bash
jupyter lab
Mở tệp notebooks/01_data_prep_baseline_deploy_ready.ipynb và chọn Run -> Run All Cells.

Cách 2: Chạy nhanh qua Giao diện Dòng lệnh (CLI Pipeline)
Bash
python src/run_training_pipeline.py
python src/check_outputs.py
##5. Triển Khai và Kiểm Thử API Mô Hình (Deployment & Testing)
Bước 1: Khởi động API Server
Bash
uvicorn src.app:app --reload --host 127.0.0.1 --port 8000
Bước 2: Truy cập Giao diện Tài liệu (Swagger UI)
Mở trình duyệt web: http://127.0.0.1:8000/docs

Bước 3: Chạy Script Kiểm thử Tự động
Bash
python src/test_api.py
##6. Tiêu Chí Đánh Giá Hoàn Thành (Definition of Done)
Thực thi không lỗi: Notebook hoặc Pipeline CLI chạy mượt mà từ đầu đến cuối.

Mô hình học máy: Tệp models/occupancy_baseline.joblib được khởi tạo.

Chỉ số đánh giá: Có tệp outputs/metrics.json chứa accuracy, precision, recall, f1.

Nhật ký quyết định: Tệp outputs/decision_log.csv chứa occupancy_probability, anomaly_score, is_anomaly, decision, command_hint.

Khởi chạy Server: Dịch vụ FastAPI khởi chạy cục bộ và truy cập được vào /docs.

Kiểm thử API thành công: Chạy test_api.py trả về trạng thái PASSED.

Hiểu rõ luồng xử lý: Nắm vững chuyển đổi: Telemetry -> Features -> Model Predictive -> Decision -> Command Hint.


Từ "kf" ở cuối câu hỏi của bạn có phải là do gõ nhầm chữ "không", hay bạn muốn thay đổi/rút gọn một
