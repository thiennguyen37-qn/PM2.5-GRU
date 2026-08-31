# Data — Mô tả dữ liệu

Thư mục này chứa dữ liệu chất lượng không khí (Air Quality) lấy từ **Open-Meteo Air Quality API**, tại vị trí:

| Trường | Giá trị |
|---|---|
| latitude | 13.800003 |
| longitude | 109.20001 |
| elevation | 9.0 m |
| timezone | Asia/Bangkok (GMT+7) |

Có 2 file CSV: `raw_data.csv` (dữ liệu thô) và `cleaned_data.csv` (dữ liệu đã làm sạch, dùng cho EDA/modeling).

---

## 1. `raw_data.csv` — Dữ liệu thô

Dữ liệu gốc tải trực tiếp từ API, theo giờ, từ `2016-01-01T00:00` đến `2026-07-26T23:00` (92,640 dòng).

- 3 dòng đầu file là **metadata vị trí** (latitude, longitude, elevation, utc_offset_seconds, timezone, timezone_abbreviation), không phải dữ liệu bảng — cần `skiprows=3` khi đọc bằng pandas.
- Nhiều dòng đầu (2016 → giữa 2022) toàn giá trị `NaN` vì trạm/API chưa có dữ liệu ở giai đoạn đó.
- Các cột chỉ số có 34,865 dòng không rỗng.

### Danh sách cột

| Cột | Ý nghĩa | Đơn vị |
|---|---|---|
| `time` | Thời điểm đo (ISO 8601, theo giờ) | — |
| `pm10 (μg/m³)` | Bụi mịn PM10 (đường kính ≤ 10 μm) | μg/m³ |
| `pm2_5 (μg/m³)` | Bụi mịn PM2.5 (đường kính ≤ 2.5 μm) | μg/m³ |
| `carbon_monoxide (μg/m³)` | Khí Carbon Monoxide (CO) | μg/m³ |
| `nitrogen_dioxide (μg/m³)` | Khí Nitrogen Dioxide (NO₂) | μg/m³ |
| `sulphur_dioxide (μg/m³)` | Khí Sulphur Dioxide (SO₂) | μg/m³ |
| `ozone (μg/m³)` | Khí Ozone (O₃) tầng mặt đất | μg/m³ |
| `uv_index_clear_sky ()` | Chỉ số UV giả định trời quang mây (không mây) | không đơn vị |
| `uv_index ()` | Chỉ số UV thực tế (có tính đến mây) | không đơn vị |
| `dust (μg/m³)` | Nồng độ bụi (dust aerosol) | μg/m³ |
| `aerosol_optical_depth ()` | Độ dày quang học sol khí (AOD) — mức độ khí quyển bị che khuất bởi hạt lơ lửng | không đơn vị |

---

## 2. `cleaned_data.csv` — Dữ liệu đã làm sạch

Được tạo ra bởi notebook [`01_cleaning_raw_data.ipynb`](../01_cleaning_raw_data.ipynb) từ `raw_data.csv`, qua các bước:

1. Đọc `raw_data.csv`, bỏ qua 3 dòng metadata.
2. Loại bỏ các dòng mà **toàn bộ** các cột chỉ số (trừ `time`) đều là `NaN` → cắt bỏ giai đoạn 2016 – đầu 2022-08 không có dữ liệu.
3. Loại bỏ các cột còn chứa `NaN` (không cột nào bị loại trong dữ liệu hiện tại — toàn bộ 10 cột chỉ số đều đầy đủ).
4. Chuyển `time` sang kiểu datetime, tách thêm 4 cột: `day`, `month`, `year`, `hour`.
5. Kiểm tra tính liên tục của chuỗi thời gian (cách đều 1 giờ) → đạt `True`.
6. Lưu kết quả ra `cleaned_data.csv`.

Kết quả: **34,865 dòng × 15 cột**, từ `2022-08-04 07:00:00` đến `2026-07-26 23:00:00`, liên tục theo từng giờ, không có giá trị thiếu.

### Danh sách cột

| Cột | Ý nghĩa |
|---|---|
| `time` | Thời điểm đo (datetime) |
| `day` | Ngày trong tháng (tách từ `time`) |
| `month` | Tháng (tách từ `time`) |
| `year` | Năm (tách từ `time`) |
| `hour` | Giờ trong ngày (tách từ `time`) |
| `pm10 (μg/m³)` | Bụi mịn PM10 |
| `pm2_5 (μg/m³)` | Bụi mịn PM2.5 |
| `carbon_monoxide (μg/m³)` | Khí CO |
| `nitrogen_dioxide (μg/m³)` | Khí NO₂ |
| `sulphur_dioxide (μg/m³)` | Khí SO₂ |
| `ozone (μg/m³)` | Khí O₃ |
| `uv_index_clear_sky ()` | Chỉ số UV trời quang mây |
| `uv_index ()` | Chỉ số UV thực tế |
| `dust (μg/m³)` | Nồng độ bụi |
| `aerosol_optical_depth ()` | Độ dày quang học sol khí (AOD) |


