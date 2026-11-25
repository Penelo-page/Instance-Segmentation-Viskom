# Logbook Tugas Segmentasi Instance Indoor

## Hipotesis

16 November 2026

Saya menduga Mask R-CNN ResNet-50 FPN akan menghasilkan segmentasi yang lebih baik dibandingkan YOLOv8n-seg dan YOLOv8s-seg, terutama pada objek kecil atau saling menutupi seperti buku dan botol. Mask R-CNN memproses region objek secara terpisah, sehingga diperkirakan dapat membedakan instance yang berdekatan dengan lebih baik.

Saya juga menduga ukuran input 960 akan memberikan hasil mask terbaik pada ketiga model karena lebih banyak detail gambar dipertahankan dibandingkan input 320 dan 640. Namun, waktu pemrosesannya diperkirakan lebih lama.

Di antara kedua varian YOLO, YOLOv8s-seg diperkirakan memperoleh AP lebih tinggi daripada YOLOv8n-seg karena kapasitas modelnya lebih besar.

## Alur

| Tanggal atau tahap | Kegiatan | Kendala dan keputusan | 
| --- | --- | --- |
| 16 November 2026 | Mulai mengambil beberapa foto ruangan untuk test set pribadi. | Pengumpulan foto belum selesai. Sebagian foto lama milik sendiri juga dipilih untuk melengkapi variasi kondisi ruangan. |
| Selama pengumpulan foto | Memilih YOLOv8n-seg, YOLOv8s-seg, dan Mask R-CNN ResNet-50 FPN. Menyiapkan ukuran input 320, 640, dan 960 sesuai digit terakhir NRP. | Training dimulai sambil menunggu test set pribadi lengkap. |
| 18 November 2026 | Menjalankan sembilan konfigurasi training di Kaggle dengan GPU Tesla T4, masing-masing selama 30 epoch. | Menggunakan validation set untuk memilih checkpoint. Log training dan checkpoint disimpan agar bisa digunakan kembali pada evaluasi. |
| Tahap anotasi | Membuat anotasi polygon manual menggunakan Roboflow. | Beberapa objek tidak memiliki kelas yang langsung sesuai. Nama kelas dan anotasi disesuaikan dengan kelas target pada dataset training. Jika tidak ada kelasnya maka tidak diberi masking sendiri. |
| 23 November 2026 | Menyelesaikan anotasi 44 foto pribadi. Satu foto dari kumpulan awal dihapus. | Memeriksa bahwa 44 foto yang digunakan sudah dianotasi, kemudian memasukkan semuanya ke testing split. |
| 23 November 2026 | Membuat versi dataset dan mengunduh format COCO Segmentation. | Ground truth disimpan sebagai polygon pada JSON. Dataset ini hanya digunakan untuk testing. |
| 23 November 2026 | Menyiapkan notebook evaluasi terpisah dengan output training dan test set pribadi sebagai Input. | Menggunakan mode `EVALUATION_ONLY = True` agar pemanggilan training dilewati dan checkpoint yang sudah ada digunakan kembali. |
| 23 November 2026 | Memperbaiki error dan bug. | `loadRes` menerima records yang digunakan kembali dan memunculkan error pada field `bbox`. Perbaikannya membuat `clean_records` baru sebelum setiap pemanggilan `loadRes`. |
| 23 November 2026 | Menyelesaikan evaluasi pada test resmi dan 44 foto pribadi. | Membandingkan hasil pada semua kelas dan kelas yang muncul di kedua test set untuk membahas domain shift. |
| 24 November 2026 | Menambahkan eksperimen confidence pada lima foto kegagalan YOLOv8s-seg 640. | Membandingkan confidence 0,10; 0,25; dan 0,50 pada prediksi tersimpan. |
| 24 November 2026 | Menyiapkan dan menjalankan notebook demo foto baru. | Membuat buat notebook yang menerima foto baru serta memungkinkan perubahan model dan confidence. |

## Catatan waktu training

Training menggunakan GPU Tesla T4 di Kaggle. Waktu berikut diambil dari `results.csv`. Nilai `time` pada epoch terakhir untuk YOLO dan jumlah kolom `seconds` untuk Mask R-CNN. Durasi mencakup proses training dan validasi, bukan pengukuran utilisasi GPU.

| Model | Input | Durasi dalam menit |
| --- | --- | ---: |
| YOLOv8n-seg | 320 | 4,91 |
| YOLOv8n-seg | 640 | 5,75 |
| YOLOv8n-seg | 960 | 7,98 |
| YOLOv8s-seg | 320 | 5,09 |
| YOLOv8s-seg | 640 | 6,35 |
| YOLOv8s-seg | 960 | 10,97 |
| Mask R-CNN ResNet-50 FPN | 320 | 35,66 |
| Mask R-CNN ResNet-50 FPN | 640 | 43,53 |
| Mask R-CNN ResNet-50 FPN | 960 | 60,18 |

Total berdasarkan nilai sebelum pembulatan adalah **180,42 menit**, sekitar 3 jam. Waktu instalasi, persiapan data, evaluasi lanjutan, dan demo tidak termasuk dalam total ini.

## Hasil eksperimen kecil

Eksperimen memakai lima foto kegagalan YOLOv8s-seg 640 dengan total 30 objek ground truth. Prediksi dicocokkan berdasarkan kelas yang sama dan mask IoU minimal 0,50.

| Confidence | TP | FP | FN |
| --- | ---: | ---: | ---: |
| 0,10 | 3 | 56 | 27 |
| 0,25 | 0 | 24 | 30 |
| 0,50 | 0 | 11 | 30 |

Menurunkan confidence memulihkan sebagian objek, tetapi menambah banyak prediksi salah. Hasil ini menunjukkan bahwa kegagalan pada lima foto tersebut tidak hanya disebabkan threshold confidence. Eksperimen ini belum menguji pengaruh resolusi prototype mask secara langsung.

## Kesimpulan

Dugaan bahwa Mask R-CNN akan unggul hanya sesuai pada hasil keseluruhan test pribadi: Mask R-CNN 960 memperoleh AP50–95 tertinggi sebesar 16,56%. Pada test resmi, hasil tertinggi justru diperoleh YOLOv8s-seg 960 sebesar 10,53%. Keunggulan khusus pada kelas buku atau botol masih perlu diperiksa melalui AP per kelas.

Dugaan bahwa input 960 selalu memberikan hasil terbaik tidak sepenuhnya sesuai. YOLOv8n-seg memperoleh hasil terbaik pada input 640 di kedua test set, sedangkan YOLOv8s-seg memperoleh hasil pribadi terbaik pada input 640. Jadi, penambahan resolusi tidak selalu meningkatkan AP pada konfigurasi yang diuji.
