# 📦 Web Visual Regression Benchmark Dataset (WebVR-200)

[![Dataset Pairs](https://img.shields.io/badge/Pairs-200-blue.svg)](ground_truth.json)
[![Images Count](https://img.shields.io/badge/Images-400%20(PNG)-brightgreen.svg)](ground_truth.json)
[![Resolution](https://img.shields.io/badge/Resolution-1000x800-orange.svg)](#2-dataset-statistics)
[![License](https://img.shields.io/badge/License-MIT-lightgrey.svg)](#10-license--citation)
[![Splits](https://img.shields.io/badge/Splits-Calibration%20(25%25)%20%7C%20Test%20(75%25)-purple.svg)](#3-dataset-splits)

> **Dataset Description**: Standardized benchmark dataset for **Web Visual Regression Testing**, **Computer Vision-based UI Defect Localization**, and **Spatial Grounding for Agentic AI Coding Assistants**.

---

## 📌 Table of Contents / Mục lục
1. [Overview & Motivation (Tổng quan & Mục đích)](#1-overview--motivation)
2. [Dataset Statistics (Thông số Kỹ thuật)](#2-dataset-statistics)
3. [Dataset Splits: Calibration vs Held-out Test (Phân chia Tập dữ liệu)](#3-dataset-splits)
4. [Defect Taxonomy & Mutation Categories (Phân loại Lỗi)](#4-defect-taxonomy)
5. [Directory Layout (Cấu trúc Thư mục)](#5-directory-layout)
6. [Annotation Schema: `ground_truth.json` (Cấu trúc Nhãn)](#6-annotation-schema)
7. [Quickstart: Loading Dataset in Python (Hướng dẫn Sử dụng)](#7-quickstart-in-python)
8. [Benchmark Baseline Results (Kết quả Đối chuẩn)](#8-benchmark-baseline-results)
9. [GitHub & Data Hosting Guidance (Hướng dẫn Upload GitHub)](#9-github-upload-guidance)
10. [License & Citation (Bản quyền & Trích dẫn)](#10-license--citation)

---

## 1. Overview & Motivation

Trong kiểm thử giao diện phần mềm hiện đại và các hệ thống **Agentic AI** tự động vận hành website (như Devin, SWE-agent), các lỗi thoái lui giao diện vi mô (*Silent Visual Regressions* – như lệch component 20px, mất nút CTA, tràn font chữ, đổi màu nhận diện) thường xuyên vượt qua các bài kiểm thử đơn vị (Unit Tests) và kiểm thử chức năng (E2E Tests) vì cây DOM HTML và mã trạng thái HTTP vẫn trả về 200 OK.

Bộ dữ liệu **WebVR-200** được xây dựng nhằm cung cấp:
* **Ground Truth tuyệt đối & xác định**: 200 cặp ảnh giao diện chuẩn hóa (400 ảnh PNG, $1000 \times 800$px) được sinh tự động thông qua công cụ trình duyệt không đầu (**Playwright**) và kỹ thuật tiêm lỗi đột biến DOM (**Fault Injection / Mutation Testing**).
* **Môi trường kiểm định cân bằng**: Bao gồm $50\%$ trường hợp hoàn toàn không có lỗi (Scenario 1) để đo lường **Tỷ lệ Báo động Giả (False Positive Rate - FPR)** và $50\%$ trường hợp lỗi đột biến có kiểm soát (Scenario 2).
* **Grounding cho Agentic AI**: Cung cấp tọa độ Bounding Box, CSS selector mục tiêu và mã JavaScript tiêm lỗi tương ứng để đánh giá khả năng tự động khoanh vùng và tự chữa lành (Self-healing) của LLM Coding Agent.

---

## 2. Dataset Statistics

| Thông số / Metric | Giá trị / Chi tiết | Ghi chú |
|---|---|---|
| **Tổng số cặp ảnh (Pairs)** | **200 cặp** | 100 cặp Scenario 1 + 100 cặp Scenario 2 |
| **Tổng số file ảnh (Images)** | **400 ảnh (PNG)** | Mỗi cặp gồm 1 ảnh `baseline` và 1 ảnh `current` |
| **Kích thước độ phân giải (Resolution)** | **$1000 \times 800$ px** | Viewport desktop tiêu chuẩn |
| **Định dạng ảnh (Format)** | Lossless PNG (24-bit sRGB) | Không nén làm mất mát chi tiết sub-pixel |
| **Dung lượng lưu trữ (Total Size)** | **~97.8 MB** | Trung bình ~244 KB / ảnh |
| **Giao diện mẫu (Template)** | Modern EdTech SaaS Landing Page | HTML5, TailwindCSS, SVGs, Responsive Components |
| **Công cụ sinh dữ liệu (Generator)** | Playwright (Chromium Headless) | Ổn định CSS rendering (`transition: none; animation: none`) |

---

## 3. Dataset Splits

Để đảm bảo tính khách quan khoa học và tuân thủ chặt chẽ nguyên tắc không rò rỉ dữ liệu (*Data Leakage Prevention*), toàn bộ 200 cặp ảnh được phân bổ thành 2 tập tách biệt:

```text
                                  ┌────────────────────────────────────────────────────────┐
                                  │           WebVR-200 Benchmark (200 Cặp ảnh)            │
                                  └───────────────────────────┬────────────────────────────┘
                                                              │
                                ┌─────────────────────────────┴─────────────────────────────┐
                                ▼                                                           ▼
                ┌───────────────────────────────┐                           ┌───────────────────────────────┐
                │   ⚙️ Calibration Set (25%)    │                           │   🧪 Held-out Test Set (75%)  │
                │        50 Cặp / 100 Ảnh       │                           │       150 Cặp / 300 Ảnh       │
                ├───────────────────────────────┤                           ├───────────────────────────────┤
                │ • Mẫu: sample_01 – sample_25  │                           │ • Mẫu: sample_26 – sample_100 │
                │ • S1: 25 cặp (Không lỗi)      │                           │ • S1: 75 cặp (Không lỗi)      │
                │ • S2: 25 cặp (Có lỗi)         │                           │ • S2: 75 cặp (Có lỗi)         │
                │ • Mục đích: Hiệu chuẩn siêu   │                           │ • Mục đích: Kiểm thử mù độc   │
                │   tham số (T=25, K=9x9) &     │                           │   lập, đánh giá năng lực tổng │
                │   thiết kế prompt template    │                           │   quát hóa không overfitting  │
                └───────────────────────────────┘                           └───────────────────────────────┘
```

* **Đặc thù thuật toán**: Pipeline thị giác máy tính giải tích đề xuất là **Training-Free / Non-parametric** (không có trọng số mạng nơ-ron học ngầm), đồng thời tương tác với LLM theo cơ chế **Zero-shot Prompting** (không fine-tune mô hình). Việc chia tách này đảm bảo toàn bộ tập Held-out Test Set (150 cặp) là dữ liệu đối soát độc lập tuyệt đối.

---

## 4. Defect Taxonomy

100 trường hợp lỗi trong **Scenario 2** bao phủ toàn diện 6 nhóm khiếm khuyết thị giác phổ biến nhất trên giao diện Web:

| Nhóm lỗi (Category) | Số lượng | Tỷ lệ | Mô tả kỹ thuật | Ví dụ mẫu tiêu biểu |
|---|:---:|:---:|---|---|
| **🎨 Color / Style Change** | 27 cặp | 27.0% | Đổi màu nút bấm CTA, màu nền hero, hover, text, box-shadow | `sample_01` (Sign Up vàng $\to$ đỏ), `sample_13` (Màu giá tiền) |
| **📐 Layout Shift** | 18 cặp | 18.0% | Dịch chuyển component 20–50px sang trái/phải/dưới, lệch margin/padding | `sample_02` (Nút Hero lệch 50px), `sample_19` (Lệch Trust card) |
| **❌ Missing Element** | 19 cặp | 19.0% | Ẩn phần tử giao diện (`display: none`, mất badge, mất nút, icon) | `sample_03` (Mất huy hiệu Congrat), `sample_23` (Mất logo đối tác) |
| **📝 Text / Content Overflow** | 15 cặp | 15.0% | Sửa tiêu đề, tràn dòng, vỡ font, đổi kích thước chữ (typography) | `sample_04` (Đổi tiêu đề Hero), `sample_41` (Tràn đoạn văn bản) |
| **🔍 Size / Distortion** | 8 cặp | 8.0% | Co giãn méo ảnh, co cụm avatar, vỡ tỉ lệ co dãn ($30\% - 60\%$) | `sample_05` (Méo ảnh nữ sinh Hero), `sample_27` (Méo avatar học viên) |
| **⚡ Compound Regression** | 12 cặp | 12.0% | Lỗi phức hợp (kết hợp đồng thời lệch vị trí + đổi màu + mất chữ) | `sample_25` (Lệch + đổi màu Footer), `sample_78` (Lệch form liên hệ) |
| **Khác (Style/Content)** | 1 cặp | 1.0% | Biến đổi kết hợp viền & nội dung nhỏ | `sample_30` |

---

## 5. Directory Layout

Cấu trúc thư mục chuẩn của bộ dữ liệu:

```text
dataset/
├── README.md                 # Tài liệu mô tả Dataset Benchmark Card (File này)
├── ground_truth.json         # Metadata chi tiết: 200 cặp nhãn, CSS selector, mã JS & splits
├── scenario_1/               # Kịch bản 1: 100 Cặp KHÔNG CÓ LỖI (Kiểm định FPR)
│   ├── baseline/             # 100 ảnh gốc chuẩn (sample_01.png - sample_100.png)
│   └── current/              # 100 ảnh kiểm định tương ứng (sample_01.png - sample_100.png)
└── scenario_2/               # Kịch bản 2: 100 Cặp CÓ LỖI ĐỘT BIẾN (Kiểm định Recall/BBox)
    ├── baseline/             # 100 ảnh gốc chưa tiêm lỗi (sample_01.png - sample_100.png)
    └── current/              # 100 ảnh sau khi tiêm lỗi DOM (sample_01.png - sample_100.png)
```

---

## 6. Annotation Schema

Tệp [ground_truth.json](ground_truth.json) chứa toàn bộ nhãn metadata được chuẩn hóa cấu trúc:

```json
{
  "dataset_info": {
    "total_pairs": 200,
    "total_images": 400,
    "resolution": "1000x800",
    "scenario_1_count": 100,
    "scenario_2_count": 100,
    "splits": {
      "calibration_set": { "total_pairs": 50, "sample_range": "sample_01 to sample_25" },
      "held_out_test_set": { "total_pairs": 150, "sample_range": "sample_26 to sample_100" }
    }
  },
  "scenario_2_mutations": [
    {
      "id": 1,
      "filename": "sample_01.png",
      "scenario": 2,
      "has_defect": true,
      "name": "header_signup_button_color",
      "category": "Color/Style",
      "target_selector": "nav a:last-child",
      "description_vi": "Đổi màu nút 'Sign Up' trên navbar từ vàng sang đỏ.",
      "description_en": "Change 'Sign Up' navbar button color from yellow to red.",
      "injected_code": "const el = document.querySelector('nav a:last-child'); if(el) el.style.backgroundColor = '#ef4444';",
      "baseline_path": "scenario_2/baseline/sample_01.png",
      "current_path": "scenario_2/current/sample_01.png",
      "split": "calibration"
    }
  ]
}
```

---

## 7. Quickstart: Loading Dataset in Python

### Cách 1: Nạp và Lọc theo Split bằng Python

```python
import json
from pathlib import Path

# Đường dẫn thư mục dataset
dataset_dir = Path("dataset")

with open(dataset_dir / "ground_truth.json", "r", encoding="utf-8") as f:
    gt_data = json.load(f)

# Lọc các mẫu thuộc Held-out Test Set (150 cặp)
test_mutations = [
    m for m in gt_data["scenario_2_mutations"] 
    if m["split"] == "held_out_test"
]

print(f"Tổng số mẫu kiểm thử mù độc lập: {len(test_mutations)}")
for m in test_mutations[:3]:
    print(f"- [{m['category']}] {m['name']}: {m['description_vi']}")
    print(f"  Baseline: {dataset_dir / m['baseline_path']}")
    print(f"  Current : {dataset_dir / m['current_path']}")
```

### Cách 2: Nạp nhanh bằng Pandas DataFrame

```python
import json
import pandas as pd

with open("dataset/ground_truth.json", encoding="utf-8") as f:
    data = json.load(f)

df = pd.DataFrame(data["scenario_2_mutations"])
print(df["category"].value_counts())
print(df.groupby(["split", "category"]).size())
```

---

## 8. Benchmark Baseline Results

Hiệu năng thực nghiệm của **Proposed Analytical Computer Vision Pipeline** trên toàn bộ 200 cặp ảnh:

| Chỉ số / Metric | Toàn bộ Dataset (200 cặp) | Calibration Set (50 cặp) | Held-out Test Set (150 cặp) |
|---|:---:|:---:|:---:|
| **Accuracy (Độ chính xác toàn cục)** | **100.0%** | 100.0% | **100.0%** |
| **Recall (Độ nhạy phát hiện lỗi)** | **100.0%** | 100.0% | **100.0%** |
| **Precision (Độ chuẩn xác cảnh báo)** | **100.0%** | 100.0% | **100.0%** |
| **False Positive Rate (FPR)** | **0.0%** | 0.0% | **0.0%** |
| **Mean BBoxes / Defect** | **2.87** | 2.92 | **2.85** |
| **Độ trễ xử lý (CPU Latency)** | **171.6 ms** | 168.4 ms | **172.5 ms** |

### Tác động Định lượng đến Agentic AI (Before vs After)

Khi tích hợp kết quả thị giác từ bộ dữ liệu vào **Autonomous Coding Agent (Claude 3.5 Sonnet / Gemini 1.5 Pro)**:
* **Localization Accuracy**: Tăng từ $28.3\% \to \mathbf{98.3\%}$ (+70.0%).
* **Fix Success Rate**: Tăng từ $31.7\% \to \mathbf{93.3\%}$ (+61.6%).
* **Token Consumption**: Giảm **$90.1\%$** ngữ cảnh (từ $16,067 \to 1,338$ tokens/ticket).
* **Latency**: Nhanh hơn **$6.05$ lần** (từ $47.8$s $\to 7.9$s).
* **Operating API Cost**: Tiết kiệm **$90.2\%$** chi phí (từ $\$0.0605 \to \$0.0059$/ticket).

---

## 9. GitHub Upload & Data Hosting Guidance

Dung lượng toàn bộ thư mục `dataset/` hiện tại là **~97.8 MB** (400 ảnh PNG, mỗi ảnh ~244 KB, file lớn nhất 557 KB). Khi đưa lên GitHub, có 3 phương án quản lý phù hợp:

### Phương án 1: Đẩy trực tiếp vào Git Repository (Khuyên dùng cho đồ án/chuyên đề)
* Vì không có file nào vượt ngưỡng **100 MB** của GitHub (file lớn nhất chỉ 557 KB) và tổng dung lượng dưới ngưỡng khuyến cáo 1 GB của một repository, bạn hoàn toàn có thể commit và push trực tiếp:
```bash
git add dataset/
git commit -m "feat(dataset): add WebVR-200 benchmark dataset with 200 pairs and documentation"
git push origin main
```

### Phương án 2: Sử dụng Git LFS (Large File Storage) (Chuẩn mã nguồn mở chuyên nghiệp)
Nếu muốn tách rời các file ảnh nhị phân khỏi lịch sử git commit thông thường:
```bash
# 1. Cài đặt Git LFS
git lfs install

# 2. Theo dõi toàn bộ file PNG trong dataset
git lfs track "dataset/**/*.png"
git add .gitattributes

# 3. Commit và push bình thường
git add dataset/
git commit -m "chore(dataset): track dataset PNG images with Git LFS"
git push origin main
```

### Phương án 3: Lưu trữ trên GitHub Releases hoặc Hugging Face Datasets
* Nén thư mục `dataset/` thành file zip `WebVR-200.zip` (~95 MB).
* Đính kèm vào mục **GitHub Releases (v1.0.0)** hoặc upload lên **Hugging Face Hub** (`datasets/username/webvr-200`).

---

## 10. License & Citation

### Bản quyền (License)
Bộ dữ liệu được phát hành theo giấy phép **MIT License**. Bạn được tự do sử dụng, sao chép, tích hợp trong các nghiên cứu học thuật và ứng dụng công nghiệp.

### Trích dẫn (Citation)
Nếu bạn sử dụng bộ dữ liệu này trong các bài báo khoa học hoặc đồ án tốt nghiệp, vui lòng trích dẫn:

```bibtex
@misc{webvr200_dataset_2026,
  author = {Nguyen Hoang Bao Long and Cap Pham Dinh Thang},
  title = {WebVR-200: A Benchmark Dataset for Web Visual Regression Testing and Agentic AI Grounding},
  year = {2026},
  publisher = {GitHub},
  howpublished = {\url{https://github.com/your-username/chuyen-de-tot-nghiep}}
}
```
# visual-inspection-module-data
