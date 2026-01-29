# 🎬 ANIME DATA PIPELINE 

## 1. Tổng quan dự án

Dự án này xây dựng một **Big Data pipeline hoàn chỉnh** sử dụng **Apache Spark** để xử lý dữ liệu Anime lấy từ **Kaggle**, sau đó lưu trữ vào **HDFS** và **so sánh hiệu năng giữa các định dạng lưu trữ dữ liệu (CSV, Parquet, ORC)**.

Project tập trung vào 3 mục tiêu chính:

- Xử lý & phân tích dữ liệu lớn bằng **Spark SQL / DataFrame API**
- Thiết kế pipeline từ dữ liệu thô → dữ liệu phân tích
- Đánh giá **hiệu năng đọc/ghi & dung lượng lưu trữ** của các định dạng dữ liệu phổ biến trong Big Data

---

## 2. Kiến trúc Data Pipeline

![Data Pipeline](dataflow.png)

Luồng xử lý dữ liệu:

```
Kaggle Dataset
     │
     ▼
Spark Read CSV
     │
     ▼
Data Preprocessing & Analysis (PySpark)
     │
     ├── Aggregation / Join / Window Function
     ├── Explode / Pivot
     │
     ▼
Output DataFrames
     │
     ├── CSV
     ├── Parquet
     ├── ORC
     │
     ▼
HDFS Storage
     │
     ▼
Performance Evaluation
```

---

## 3. Dataset

Nguồn dữ liệu: **Kaggle – Anime Recommendation / Rating Dataset**

### 3.1 Các file chính

- `animes.csv`: thông tin anime
- `ratings.csv`: dữ liệu đánh giá của người dùng
- `id_to_genreids.json`: mapping thể loại

### 3.2 Schema chính

**Anime**
- animeID
- title
- type
- year
- score
- episodes
- genres

**Rating**
- userID
- animeID
- rating

---

## 4. Công nghệ sử dụng

- **Apache Spark (PySpark)**
- **HDFS**
- **Python**
- **Pandas, Matplotlib, Seaborn** (Visualization)
- **Kaggle Dataset**

---

## 5. Cấu hình Spark

```python
SparkSession.builder \
    .appName('Analysis anime sentiment') \
    .config('spark.driver.memory', '6g') \
    .config('spark.executor.memory', '6g') \
    .config('spark.executor.cores', 4) \
    .config('spark.sql.shuffle.partitions', '10') \
    .getOrCreate()
```

---

## 6. Xử lý & phân tích dữ liệu

### 6.1 Join & xử lý dữ liệu

- Join `ratings` với `animes` theo `animeID`
- Sử dụng `broadcast join` để tối ưu hiệu năng

### 6.2 Top Anime được đánh giá cao nhất

- Tính **điểm đánh giá trung bình** và **số lượt đánh giá**
- Sử dụng **Window Function** để loại bỏ trùng lặp rating
- Lấy **Top 10 Anime có rating cao nhất**

Output:
- `output/parquet/animes_best_rated/`
- `output/csv/animes_best_rated/`

---

### 6.3 Phân tích loại phim theo người dùng & năm

- Chuyển cột `genres` từ JSON → Array
- `explode` thể loại
- `pivot` theo `type`
- Xác định **loại phim user xem nhiều nhất theo từng năm**

Output:
- `output/parquet/type_per_year/`

---

### 6.4 Phân tích rating theo thể loại & người dùng

- Trung bình rating theo `(userID, genres)`
- Trực quan hóa bằng **heatmap**

Output:
- `output/parquet/genres_user/`

---

## 7. Lưu trữ dữ liệu trên HDFS

Dữ liệu đầu ra được lưu trên HDFS với nhiều định dạng:

```
output/
├── csv/
├── parquet/
├── orc/
└── evaluate/
```

---

## 8. So sánh hiệu năng CSV – Parquet – ORC

### 8.1 Tiêu chí đánh giá

- ⏱️ Thời gian ghi (Write Time)
- ⏱️ Thời gian đọc (Read Time)
- 💾 Dung lượng lưu trữ (Size)

### 8.2 Quy trình đánh giá

- Ghi cùng một DataFrame sang 3 định dạng
- Đo thời gian ghi & đọc
- Đo dung lượng lưu trữ bằng hệ thống file

### 8.3 Kết quả tổng quan

| Format   | Write Time | Read Time | Size |
|--------|-----------|----------|------|
| CSV    | Chậm       | Chậm      | Lớn  |
| Parquet| Nhanh      | Nhanh     | Nhỏ  |
| ORC    | Nhanh      | Rất nhanh | Nhỏ  |

### 8.4 Kết luận

- ❌ **CSV**: chỉ phù hợp cho dữ liệu nhỏ, không tối ưu Big Data
- ✅ **Parquet / ORC**: tối ưu lưu trữ & truy vấn
- 🔥 **ORC** cho hiệu năng đọc tốt nhất trong phân tích

---

## 9. Visualization

- Bar chart: Top anime rating
- Heatmap: User – Genre rating
- Biểu đồ so sánh hiệu năng định dạng dữ liệu

---

## 10. Kết luận chung

Dự án mô phỏng **một pipeline Big Data thực tế**, bao gồm:

- Xử lý dữ liệu lớn bằng Apache Spark
- Lưu trữ phân tán với HDFS
- Đánh giá hiệu năng các định dạng dữ liệu

📌 Phù hợp cho:
- Portfolio **Data Engineer / Big Data Engineer**
- Bài tập lớn môn **Big Data / Distributed System**
- Case study về **Spark & Data Storage**

---

✍️ **Author:** Hoàng Minh Hải - minhhaiit1k68@gmail.com

