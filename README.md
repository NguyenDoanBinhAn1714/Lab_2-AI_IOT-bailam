# LAB 2 - AIoT Data Preparation + Baseline Model + Deploy Demo

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
lab2_aiot_public_dataset_deploy/
├── data/
│   ├── DATA_SOURCES.md
│   ├── telemetry_clean.csv                     # Dữ liệu IoT đã làm sạch
│   ├── feature_dataset.csv                     # Bộ dữ liệu đặc trưng phục vụ train/test
│   └── occupancy_fallback_same_schema.csv      # Dữ liệu dự phòng khi mất kết nối mạng
├── notebooks/
│   └── 01_data_prep_baseline_deploy_ready.ipynb # Notebook thực nghiệm và phân tích dữ liệu
├── src/
│   ├── __init__.py
│   ├── data_utils.py                           # Các hàm xử lý dữ liệu, train mô hình, tính toán logic
│   ├── download_data.py                        # Script tải public dataset hoặc khởi tạo dữ liệu dự phòng
│   ├── run_training_pipeline.py                # Pipeline thực thi tự động không qua giao diện notebook
│   ├── app.py                                  # Ứng dụng FastAPI để phục vụ mô hình (Inference Service)
│   ├── test_api.py                             # Script tự động kiểm thử hiệu năng và độ chính xác của API
│   └── check_outputs.py                        # Công cụ kiểm tra tính toàn vẹn của kết quả đầu ra
├── models/
│   └── occupancy_baseline.joblib               # Tệp lưu trữ mô hình sau khi huấn luyện thành công
├── outputs/
│   ├── metrics.json                            # Các chỉ số đánh giá (Accuracy, Precision, Recall, F1)
│   ├── decision_log.csv                        # Nhật ký quyết định chi tiết từ mô hình và động cơ Z-score
│   └── figures/                                # Các biểu đồ trực quan hóa dữ liệu và đánh giá
│       ├── 01_co2_time_series.png
│       ├── 02_confusion_matrix.png
│       └── 03_occupancy_probability.png
└── requirements.txt                            # Danh sách thư viện Python phụ thuộc
# Tạo môi trường ảo
python -m venv .venv

# Kích hoạt môi trường ảo
.\.venv\Scripts\Activate.ps1

# Cập nhật pip và cài đặt thư viện phụ thuộc
python -m pip install --upgrade pip
pip install -r requirements.txt
# Tạo môi trường ảo
python3 -m venv .venv

# Kích hoạt môi trường ảo
source .venv/bin/activate

# Cập nhật pip và cài đặt thư viện phụ thuộc
python -m pip install --upgrade pip
pip install -r requirements.txt

jupyter lab

python src/run_training_pipeline.py

python src/check_outputs.py

uvicorn src.app:app --reload --host 127.0.0.1 --port 8000

python src/test_api.py
