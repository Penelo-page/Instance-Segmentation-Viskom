# Segmentasi Instance Indoor - Visi Komputer

**Sitti Aminah · 5054241041**

Repository ini berisi pengerjaan tugas segmentasi instance pada lingkungan indoor menggunakan YOLOv8n-seg, YOLOv8s-seg, dan Mask R-CNN ResNet-50 FPN. Eksperimen menggunakan ukuran input 320, 640, dan 960, dengan evaluasi pada test set resmi serta 44 foto pribadi.

## Isi repository

```text
.
├── README.md
├── notebooks/
│   ├── training.ipynb          # Train 9 model
│   ├── evaluasi.ipynb          # Evaluasi dengan tes set sendiri
│   └── demo.ipynb              # untuk inferensi saat demo
├── hasil/
│   ├── hasil_evaluasi_semua.csv
│   ├── domain_shift_shared_classes.csv
│   ├── eksperimen_confidence_dan_iou.csv
│   ├── grafik/                
        ├── perbandingan_mask_AP.png
        └── domain_shift_shared.png
│   └── perbandingan_model/     # Prediksi sembilan konfigurasi
├── analisis/
│   ├── kegagalan.md            # Pembahasan lima kasus kegagalan & eksperiman
│   └── gambar/                 # Foto, GT, dan prediksi
├── logs/                       
│   ├── waktu_training.csv      # Ringkasan durasi training
│   └── [9 folder konfigurasi berisi results.csv]
├── bukti/                      # Proses anotasi, metadata, dan foto sulit
├── logbook.md                  # Hipotesis, kegiatan, kendala, dan keputusan
├── deklarasi_AI.md
└── requirements.txt
```

## Code

Notebook dijalankan di Kaggle menggunakan GPU Tesla T4. Menambahkan dataset dan output training yang diperlukan melalui Add Input.

Untuk evaluasi, memakai `EVALUATION_ONLY = True` agar training tidak diulang. Notebook demo digunakan untuk menerima foto baru dan mencoba perubahan model atau confidence.

## Dataset

Data resmi menggunakan [Panoptic Indoor Segmentation versi 5](https://universe.roboflow.com/panopticindoor/panoptic-indoor-segmentation/dataset/5). Test set pribadi dianotasi secara manual menggunakan Roboflow dengan kelas yang mengikuti dataset training.
