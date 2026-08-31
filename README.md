# Ứng dụng mạng GRU dự báo nồng độ PM2.5 tại thành phố Quy Nhơn

Tiểu luận học phần **Học sâu và Ứng dụng** — dự báo nồng độ PM2.5 theo chuỗi thời gian bằng kiến trúc hybrid (EWMA Trend + Direct Multi-output GRU học Residual).

## Cấu trúc project

```
├── 01_cleaning_raw_data.ipynb      # Làm sạch dữ liệu
├── 02_eda.ipynb                    # Phân tích khám phá dữ liệu (EDA)
├── 03_training_and_evaluation.ipynb # Xây dựng, huấn luyện, đánh giá mô hình hybrid
├── data/                           # Dữ liệu (xem data/README.md)
├── report/                         # Báo cáo LaTeX
└── requirements.txt
```

## Notebooks

| Notebook | Vai trò |
|---|---|
| [`01_cleaning_raw_data.ipynb`](01_cleaning_raw_data.ipynb) | Đọc `raw_data.csv`, loại bỏ dòng/cột thiếu dữ liệu, tách đặc trưng thời gian, lưu ra `cleaned_data.csv`. |
| [`02_eda.ipynb`](02_eda.ipynb) | Phân tích khám phá dữ liệu: thống kê mô tả, tương quan, xu hướng, tính chu kỳ (ACF/PACF, Decomposition), phân bố theo mùa, đối chiếu ngưỡng WHO/QCVN. Sinh toàn bộ hình/bảng dùng ở Mục 2.1–2.2 của báo cáo. |
| [`03_training_and_evaluation.ipynb`](03_training_and_evaluation.ipynb) | Toàn bộ pipeline mô hình cuối cùng: gộp dữ liệu theo ngày, tách Xu hướng (EWMA) / Residual, chia Train/Validate/Test, Grid Search siêu tham số bằng sliding-fold cross-validation, huấn luyện GRU, đánh giá walk-forward + fine-tune trên dữ liệu mới. Sinh toàn bộ hình/bảng dùng ở Mục 2.3–2.6 của báo cáo. |

## Dữ liệu

Xem chi tiết tại [`data/README.md`](data/README.md). Tóm tắt: dữ liệu chất lượng không khí theo giờ tại Quy Nhơn, lấy từ Open-Meteo Air Quality API, gồm `raw_data.csv`/`cleaned_data.csv` (huấn luyện) và `new_data.csv`/`new_data_cleaned.csv` (tập Test hoàn toàn mới, out-of-sample).

Model cuối cùng được lưu tại `data/processed/gru_hybrid_daily_h7_lb28_decomp.pt`.

## Báo cáo

[`report/bao_cao.tex`](report/bao_cao.tex) (biên dịch ra [`report/bao_cao.pdf`](report/bao_cao.pdf)) gồm 2 chương:

- **Chương 1 — Cơ sở lý thuyết**: RNN, LSTM, GRU, hai chiến lược dự báo đa bước (Recursive GRU, Direct Multi-output GRU).
- **Chương 2 — Ứng dụng dự báo nồng độ PM2.5**: mô tả dữ liệu, EDA, tiền xử lý (kiến trúc hybrid EWMA + GRU), xây dựng và tinh chỉnh mô hình, đánh giá walk-forward trên dữ liệu mới.

Hình vẽ dùng trong báo cáo nằm ở `report/figures/`, sinh trực tiếp từ 2 notebook `02_eda.ipynb` và `03_training_and_evaluation.ipynb`.

### Kết quả chính

Mô hình hybrid (EWMA Trend, span=7 + Direct Multi-output GRU học Residual, hidden_size=32, lookback=28 ngày, horizon=7 ngày), đánh giá walk-forward trên 28 ngày dữ liệu mới hoàn toàn:

| Chỉ số | Giá trị |
|---|---|
| MAE | 2.784 µg/m³ |
| RMSE | 3.553 µg/m³ |
| Residual mean / std | −2.364 / 2.653 µg/m³ |

## Cài đặt

```bash
pip install -r requirements.txt
```

Chạy lần lượt 3 notebook theo đúng thứ tự `01` → `02` → `03`.
