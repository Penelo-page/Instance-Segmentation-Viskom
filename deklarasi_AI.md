# Deklarasi Penggunaan AI

Saya menggunakan ChatGPT untuk membantu membuat code training, evaluasi, dan demo. Foto pribadi dipilih atau diambil oleh saya dan anotasi polygon dibuat secara manual di Roboflow.

## Prompt utama
"Buatkan code untuk training model (...) yang kompatibel untuk di run di Kaggle."

### 1 Error COCO loadRes pada evaluasi mask

Evaluasi `yolov8n_seg_640_resmi` berhenti pada fungsi `score_coco`, ketika menjalankan:

```python
predictions = gt.loadRes(records)
```

**Penyebab:** pada alur evaluasi ini, records dipakai kembali untuk scope `all` dan `shared`. `loadRes` dapat menambahkan field seperti `bbox` ke records yang diterimanya. Ketika field `bbox` berbentuk array NumPy, pemeriksaan terhadap list kosong pada pemanggilan berikutnya menimbulkan error.

**Perbaikan:** membuat records baru untuk setiap pemanggilan. Untuk evaluasi segmentasi, data yang diteruskan hanya ID gambar, ID kelas, score, dan salinan segmentation. Field `bbox` yang ditambahkan sebelumnya tidak ikut diteruskan.

### 2 Ukuran anotasi dan gambar pada Mask R CNN

**Masalah pada kode awal:** pembaca dataset mengasumsikan ukuran gambar aktual selalu sama dengan ukuran pada JSON COCO. Padahal, ketika keduanya berbeda, bounding box dan mask tidak dapat langsung dipakai dalam koordinat gambar aktual.

**Perbaikan:** menghitung skala koordinat, menyesuaikan bounding box, dan mengubah ukuran mask biner dengan nearest-neighbor agar nilainya tetap berupa label mask.