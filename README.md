# Capstone Project Tim CC25-CF102

# Project Overview

## Latar Belakang

Pulau Jawa memiliki banyak tempat wisata yang menarik, namun banyak wisatawan mengalami kesulitan dalam menentukan tempat wisata yang sesuai dengan minat atau preferensinya. Oleh karena itu, dibutuhkan sistem rekomendasi yang mampu memberikan saran destinasi wisata yang relevan berdasarkan deskripsi dan karakteristik tiap tempat.

## Pentingnya Proyek

Dengan adanya sistem rekomendasi berbasis konten, pengguna dapat memperoleh informasi tempat wisata yang relevan dengan cepat. Proyek ini dapat mendukung sektor pariwisata dalam meningkatkan keterjangkauan informasi destinasi serta mempersonalisasi pengalaman wisata pengguna.

## Referensi

- Ricci, Rokach, & Shapira (2015). Recommender Systems Handbook.

- Dataset: Kaggle, (https://www.kaggle.com/datasets/masdarulrizqi/tourism-data-on-javaklklddsc)

# Business Understanding

## Problem Statements

1. Bagaimana cara sistem menemukan tempat wisata yang relevan berdasarkan deskripsi dan informasi tempat yang mirip?

2. Bagaimana cara meningkatkan akurasi rekomendasi tempat wisata tanpa data interaksi pengguna?

## Goals

1. Membangun sistem rekomendasi tempat wisata berdasarkan kesamaan konten (deskripsi).

2. Memberikan rekomendasi Top-N tempat wisata yang mirip dengan tempat yang dicari pengguna.

## Solution Statements

- Sistem akan menggunakan Content-Based Filtering dengan pendekatan TF-IDF pada kolom deskripsi dan cosine similarity untuk menemukan tempat wisata yang mirip.

- Pendekatan ini tidak membutuhkan data historis pengguna, cocok untuk sistem dengan pengguna baru.

# Data Understanding

Proyek ini menggunakan dataset yang tersedia di Kaggle:

🔗 https://www.kaggle.com/datasets/masdarulrizqi/tourism-data-on-javaklklddsc

## Informasi Dataset

java.xlsx: memiliki 868 baris dan memiliki 9 kolom. 

## Variabel-Variabel pada Dataset

1. `no` - nomor urut
2. `place_name` - nama tempat wisata
3. `deskripsi` - deskripsi singkat mengenai tempat wisata
4. `review_link` - link ulasan
5. `rating` - nilai rating (0-5)
6. `total_reviews` - jumlah total ulasan
7. `address` - alamat lengkap
8. `city` - kota lokasi wisata
9. `province` - provinsi lokasi wisata
    
## Exploratory Data Analysis

- kode:
  
"df = pd.read_excel('java_tourism.xlsx')
df.head()"

Pada tahap ini membaca file Excel berisi data wisata di Jawa dan menampilkan 5 baris pertama dari dataframe df.

- kode:
  
"df.info()"

Tahap ini menampilkan informasi umum seperti jumlah baris, kolom, tipe data setiap kolom, dan jumlah data non-null (tidak kosong).

- Kode:
  
"keywords = {
    'alam': ['gunung', 'pantai', 'danau', 'air terjun', 'hutan', 'taman nasional', 'cagar alam', 'gua', 'pemandangan', 'alam', 'sungai', 'laut'],
    'buatan': ['taman hiburan', 'museum', 'monumen', 'tempat wisata', 'kebun binatang', 'waterpark', 'kolam renang', 'gedung', 'bangunan', 'buatan', 'buatan manusia', 'theme park'],
    'budaya': ['budaya', 'tradisional', 'adat', 'seni', 'pertunjukan', 'sejarah', 'kerajaan', 'festival', 'tari', 'musik', 'candi', 'batik', 'keraton'],
    'religi': ['masjid', 'gereja', 'pura', 'vihara', 'kuil', 'makam', 'ziarah', 'religi', 'agama', 'keramat', 'rohani'],
    'edukasi': ['edukasi', 'pendidikan', 'belajar', 'ilmu pengetahuan', 'sekolah', 'universitas', 'planetarium', 'observatorium', 'arkeologi', 'penelitian', 'sains', 'iptek']

}"

Fungsi ini mendefinisikan kata kunci untuk mengelompokkan tempat wisata ke dalam lima kategori:

1. alam: wisata alam seperti gunung, pantai, danau.
2. buatan: wisata buatan manusia seperti taman hiburan, museum.
3. budaya: terkait budaya/tradisi seperti candi, seni, keraton.
4. religi: tempat bernuansa agama seperti masjid, pura, makam.
5. edukasi: tempat edukatif seperti planetarium, museum sains.

- kode:
  
print("Data yang hilang di setiap kolom:")
print(df.isnull().sum())

Menampilkan jumlah nilai yang hilang (NaN) pada setiap kolom.

- kode:
  
print("\nData duplikat:")
print(df.duplicated().sum())

Menampilkan jumlah baris data yang duplikat (sama persis).

![Gambar](image/a.png)

- kode:
  
print("\nStatistik Deskriptif:")
print(df.describe())

Menampilkan statistik deskriptif untuk kolom numerik, seperti mean, std, min, dan quartiles.

![Gambar](image/b.png)

- kode:
   
plt.figure(figsize=(10, 6))
sns.histplot(df['rating'], bins=20, kde=True)
plt.title('Distribusi Rating Tempat Wisata')
plt.xlabel('Rating')
plt.ylabel('Frekuensi')
plt.show()

plt.figure(figsize=(10, 6))
sns.histplot(df['total_reviews'], bins=20, kde=True)
plt.title('Distribusi Jumlah Ulasan Tempat Wisata')
plt.xlabel('Jumlah Ulasan')
plt.ylabel('Frekuensi')
plt.show()

Pada tahap ini menampilkan histogram rating tempat wisata dengan garis KDE (kurva sebaran), lalu juga menampilkan distribusi jumlah ulasan dari tempat wisata.

![Gambar](image/c.png)

![Gambar](image/d.png)

- kode:
  
plt.figure(figsize=(12, 6))
sns.countplot(y='province', data=df, order=df['province'].value_counts().index)
plt.title('Distribusi Tempat Wisata Berdasarkan Provinsi')
plt.xlabel('Jumlah Tempat Wisata')
plt.ylabel('Provinsi')
plt.show()

Menampilkan jumlah tempat wisata per provinsi dalam bentuk bar horizontal. Provinsi diurutkan berdasarkan jumlah terbanyak.

![Gambar](image/e.png)

- kode:
  
corr_matrix = df[['rating', 'total_reviews']].corr()

plt.figure(figsize=(8, 6))
sns.heatmap(corr_matrix, annot=True, cmap='coolwarm', fmt='.2f')
plt.title('Korelasi Antara Rating dan Jumlah Ulasan')
plt.show()

Menghitung korelasi Pearson antara kolom rating dan total_reviews. Menampilkan matriks korelasi dalam bentuk heatmap, lengkap dengan nilai korelasinya.

![Gambar](image/f.png)

- kode:
  
category_columns = ['alam', 'buatan', 'budaya', 'religi', 'edukasi']
category_counts = df[category_columns].sum()

plt.figure(figsize=(10, 6))
category_counts.plot(kind='bar')
plt.title('Distribusi Tempat Wisata Berdasarkan Tipe Kategori')
plt.ylabel('Jumlah Tempat Wisata')
plt.xlabel('Kategori')
plt.xticks(rotation=45)
plt.show()

Menjumlahkan nilai di setiap kolom kategori. Diasumsikan bahwa kolom-kolom ini berisi nilai biner (0/1), menunjukkan apakah tempat wisata tersebut termasuk dalam kategori tertentu. Menampilkan jumlah tempat wisata untuk masing-masing kategori dalam bentuk bar chart.

![Gambar](image/g.png)


# Data Preparation

- Tokenizer dan Sequence Preparation 

Pada tahap ini mempersiapkan data teks dengan mengubah deskripsi tempat wisata menjadi urutan angka menggunakan Tokenizer dan membatasi jumlah kata hingga 1000 kata yang paling sering muncul. Kemudian, urutan angka tersebut dipadatkan agar memiliki panjang yang konsisten, yaitu 50 kata, sehingga siap digunakan dalam pelatihan model machine learning. 

- Split train-val (simple split)

Pada tahap ini yaitu membagi data menjadi dua bagian: data pelatihan (train) dan data validasi (val). 80% dari data digunakan untuk pelatihan (x_train, y_train), sementara 20% sisanya digunakan untuk validasi (x_val, y_val).

# Modeling

- Model Building (LSTM)

Menghasilkan output: 

![Gambar](image/h.png)

Pada tahap ini yaitu membangun model LSTM untuk klasifikasi teks dengan input berupa deskripsi tempat wisata. Model dimulai dengan embedding layer untuk representasi kata, dilanjutkan dengan LSTM layer untuk memproses urutan teks, dan dropout layer untuk mencegah overfitting. Kemudian, dense layer diikuti dengan output layer yang menggunakan fungsi aktivasi sigmoid untuk memprediksi 5 kategori tipe wisata. Model dikompilasi dengan binary crossentropy dan adam optimizer, serta menggunakan accuracy sebagai metrik evaluasi.

- Training (MODIFIKASI)

Pada tahapan training ini yaitu melatih model LSTM dengan early stopping untuk menghentikan pelatihan jika val_loss tidak membaik selama 3 epoch. Pelatihan dilakukan selama 10 epoch dengan batch size 16, menggunakan data validasi untuk evaluasi.

# Evaluation

Menghasilkan output: 

![Gambar](image/i.png)

Yang dimana mengevaluasi model LSTM pada data validasi dengan menghitung loss dan akurasi, lalu mengonversi prediksi probabilitas menjadi prediksi biner. Laporan klasifikasi kemudian ditampilkan untuk masing-masing kategori tipe wisata, memberikan metrik seperti precision, recall, dan f1-score.

# Memprediksi label dari input user 

Tahapan ini mendefinisikan fungsi predict_labels yang mengubah input teks menjadi urutan angka, memprediksi probabilitas dengan model LSTM, dan mengonversinya menjadi prediksi biner menggunakan ambang batas 0.5. Fungsi ini mengembalikan hasil prediksi dalam bentuk biner dan probabilitas untuk setiap tipe wisata.

# Rekomendasi berbasis similarity dengan threshold

Untuk tahapan ini yaitu mendefinisikan fungsi recommend_places yang memberikan rekomendasi tempat wisata berdasarkan kemiripan deskripsi dengan input pengguna. Fungsi ini memfilter data berdasarkan provinsi dan tipe wisata, lalu menghitung cosine similarity antara deskripsi input dan dataset. Tempat dengan skor kemiripan di atas threshold akan dikembalikan sebagai rekomendasi. Jika tidak ada tempat yang cocok, pesan yang sesuai akan ditampilkan.

# Interaksi user (input langsung)

Menghasilkan output:

![Gambar](image/j.png)

Dari tahapan ini memungkinkan pengguna untuk memasukkan provinsi, tipe wisata, dan deskripsi tempat wisata. Sistem kemudian memvalidasi input, memprediksi tipe wisata berdasarkan deskripsi, dan memberikan rekomendasi tempat wisata yang sesuai dengan provinsi dan tipe yang dipilih. Hasil rekomendasi mencakup nama tempat, provinsi, deskripsi, gambar, dan skor kemiripan.

# Evaluasi Terhadap Business Understanding

-  Kesesuaian dengan Problem Statements

1. Menemukan Tempat Wisata yang Relevan
Sistem berhasil merekomendasikan tempat wisata yang relevan berdasarkan kemiripan deskripsi menggunakan teknik TF-IDF dan cosine similarity. Saat pengguna memberikan deskripsi tempat yang diinginkan, sistem mampu mengembalikan daftar tempat wisata yang memiliki kemiripan kata kunci dan konteks dengan input tersebut. Ini membuktikan bahwa sistem dapat menyelesaikan masalah utama yaitu menemukan tempat wisata yang mirip berdasarkan konten.

2. Tanpa Data Interaksi Pengguna
Sistem tidak menggunakan data historis pengguna (seperti rating, klik, atau histori kunjungan). Oleh karena itu, sistem cocok untuk cold-start user (pengguna baru), menjawab kebutuhan untuk memberikan rekomendasi akurat tanpa ketergantungan pada data interaksi. Hal ini sesuai dengan tantangan kedua dalam problem statement.

-  Kesesuaian dengan Goals

1. Sistem Berbasis Kesamaan Konten
Sistem yang dibangun sepenuhnya berbasis konten (Content-Based Filtering), dengan input utama berupa deskripsi tempat wisata. Proses vektorisasi dengan TF-IDF dan perhitungan similarity berhasil membangun pondasi rekomendasi berdasarkan kemiripan teks, sesuai dengan goal utama.

2. Rekomendasi Top-N
Fungsi recommend_places() berhasil mengembalikan Top-N destinasi wisata dengan skor similarity tertinggi. Pengujian juga menunjukkan bahwa hasilnya konsisten dan sesuai konteks dari input pengguna, menunjukkan bahwa tujuan sistem tercapai.

- Validasi terhadap Solution Statements

1. Penggunaan TF-IDF sebagai representasi teks dan cosine similarity sebagai metode pengukuran kemiripan berhasil diterapkan dan bekerja efektif dalam konteks data destinasi wisata.

2. Karena tidak memerlukan data pengguna, sistem ini dapat langsung digunakan dalam berbagai skenario awal, terutama di sistem atau aplikasi baru yang belum memiliki data pengguna aktif. Hal ini mendukung solusi yang dicetuskan di awal.

Kesimpulan yang di dapat yaitu Sistem rekomendasi yang dikembangkan telah sepenuhnya selaras dengan pemahaman bisnis dan berhasil menjawab permasalahan utama yang diajukan. Tanpa memerlukan data interaksi pengguna, sistem mampu menyarankan tempat wisata yang relevan dan sesuai dengan deskripsi input pengguna. Dengan demikian, pendekatan berbasis konten yang digunakan dalam proyek ini efektif dan aplikatif untuk mendukung kebutuhan industri pariwisata berbasis digital.

# Kesimpulan

Proyek ini berhasil membangun sistem rekomendasi tempat wisata di Pulau Jawa menggunakan pendekatan Content-Based Filtering. Dengan memanfaatkan deskripsi tempat dan cosine similarity, sistem mampu memberikan rekomendasi yang relevan dan informatif. Pendekatan ini sangat cocok untuk sistem yang belum memiliki data interaksi pengguna, serta memberikan dasar awal yang kuat untuk pengembangan lebih lanjut menggunakan teknik hybrid atau collaborative filtering.


