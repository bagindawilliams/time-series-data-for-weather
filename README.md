# time-series-data-for-weather
Dataset yang digunakan:

•	Dataset yang digunakan dalam proyek ini berasal dari Open-Meteo API yang menyediakan data cuaca berbasis time-series selama 90 hari terakhir.

•	Dataset ini mencakup berbagai fitur cuaca yang sangat berguna dalam prediksi suhu, antara lain:

o	temperature_2m: Suhu pada ketinggian 2 meter di atas permukaan tanah.

o	relative_humidity_2m: Kelembaban relatif udara pada ketinggian 2 meter.

o	dew_point_2m: Titik embun udara pada ketinggian 2 meter.

o	wind_speed_10m: Kecepatan angin pada ketinggian 10 meter.

o	pressure_msl: Tekanan udara pada permukaan laut.
o	cloud_cover: Persentase langit yang tertutup awan.


Fitur tambahan yang digunakan:

•	rain, snowfall, visibility, soil_temperature, dan fitur lainnya juga tersedia dan dapat digunakan, tergantung pada relevansi dengan model.
Format Dataset:

•	Data disediakan dalam bentuk time-series yang mencakup informasi setiap jam atau harian selama periode yang telah ditentukan (90 hari pada contoh ini). Setiap baris dalam dataset berisi nilai untuk beberapa fitur cuaca pada waktu tertentu.

Alasan Pemilihan Dataset
1.	Data Cuaca Relevansi Tinggi untuk Prediksi Suhu:
o	Dataset ini berfokus pada data cuaca, yang sangat relevan untuk memprediksi suhu, karena suhu sangat dipengaruhi oleh faktor-faktor seperti kelembaban, titik embun, kecepatan angin, dan tekanan udara.
2.	Ketersediaan Time-Series:

o	Dataset ini dalam format time-series, yang sangat cocok untuk analisis data menggunakan algoritma deep learning seperti LSTM (Long Short-Term Memory) yang dapat menangkap pola dalam data berurutan atau sekuensial.
3.	Data Sumber Terpercaya dan Terbaru:
o	Data diambil langsung dari Open-Meteo API yang menyediakan data cuaca global yang dapat diakses secara langsung dan otomatis diperbarui. Ini membuat dataset ini sangat berguna untuk aplikasi waktu nyata dan prediksi suhu.

Alasan Pemilihan metode dengan LSTM (Long Short-Term Memory)?
1.	LSTM untuk Data Time-Series:
o	LSTM adalah jenis Recurrent Neural Network (RNN) yang sangat efektif untuk data berurutan, seperti time-series. LSTM dirancang untuk mengingat informasi dalam jangka panjang, yang sangat penting untuk menangkap pola musiman atau tren dalam data cuaca.
2.	Kemampuan Mengatasi Vanishing Gradient:
o	Berbeda dengan RNN biasa, LSTM dapat mengatasi masalah vanishing gradient yang sering terjadi dalam pelatihan jaringan saraf dengan data time-series panjang. Hal ini memungkinkan model untuk belajar dari data dalam jangka waktu yang lebih lama.
3.	Kemampuan Mengatasi Noise dan Variabilitas Data:
o	Data cuaca biasanya mengandung banyak fluktuasi dan noise. LSTM efektif dalam menangani fluktuasi ini dan masih dapat mengidentifikasi pola-pola yang relevan untuk prediksi suhu.

Tahapan 1: Memuat Dataset
Penjelasan:
Dataset digunakan dari Open-Meteo API, yang berisi data cuaca (time-series) selama 90 hari terakhir. Variabel yang digunakan meliputi suhu (temperature_2m), kelembaban (relative_humidity_2m), tekanan udara (pressure_msl), kecepatan angin (wind_speed_10m), dan variabel terkait lainnya.
•	Permasalahan: Data dari API memiliki beberapa nilai kosong dan kolom yang tidak relevan untuk prediksi suhu.
•	Solusi: Data kosong diisi pada tahap preprocessing, dan seleksi fitur dilakukan pada tahap berikutnya.
•	Hasil: Dataset berhasil dimuat dengan 90 hari data cuaca dalam format tabel.
________________________________________

Tahapan 2: Preprocessing Data
Penjelasan:
Dataset diproses untuk memastikan data bersih dan sesuai untuk digunakan pada model deep learning.
•	Langkah-Langkah:
1.	Mengisi Nilai Kosong: Nilai kosong diisi menggunakan metode interpolasi linier.
2.	Normalisasi: Semua data dinormalisasi ke rentang [0, 1] menggunakan MinMaxScaler.
3.	Format Time-Series: Dataset diubah menjadi struktur time-series dengan window size 24 jam, artinya model akan memprediksi suhu berdasarkan 24 jam terakhir.
•	Hasil: Dataset siap digunakan, dengan nilai yang bersih dan terstandardisasi.
________________________________________

Tahapan 3: Identifikasi Fitur Penting
Penjelasan:
Matriks korelasi digunakan untuk menentukan hubungan antara fitur dan target variabel (temperature_2m).
•	Langkah-Langkah:
o	Korelasi dihitung untuk semua fitur terhadap suhu.
o	Fitur dengan korelasi tinggi dipilih untuk model, sedangkan fitur dengan korelasi rendah diabaikan.
•	Hasil:
o	Fitur yang dipilih:
1.	dew_point_2m (korelasi = 0.87)
2.	apparent_temperature (korelasi = 0.84)
3.	relative_humidity_2m (korelasi = 0.73)
4.	pressure_msl (korelasi = -0.45)
________________________________________

Tahapan 4: Pembuatan Model Deep Learning
Penjelasan:
Model deep learning menggunakan arsitektur LSTM (Long Short-Term Memory), yang dirancang untuk menangkap pola temporal dalam data time-series.
•	Alasan Pemilihan Metode:
o	LSTM dapat mengingat pola data jangka panjang dan pendek, sehingga cocok untuk data time-series.
o	Dropout digunakan untuk mengurangi overfitting.
•	Arsitektur Model:
o	Input Layer: Dimensi (24, 4) (24 jam terakhir, 4 fitur).
o	Hidden Layer:
	LSTM dengan 64 unit dan Dropout 20%.
	LSTM dengan 32 unit dan Dropout 20%.
o	Output Layer: Dense dengan 1 unit untuk prediksi suhu.
________________________________________

Tahapan 5: Pelatihan Lanjutan
Penjelasan:
Model dilatih menggunakan data pelatihan dengan konfigurasi berikut:
•	Epoch: 100 (jumlah iterasi pelatihan).
•	Batch Size: 16 (untuk pembelajaran yang rinci).
•	Optimizer: Adam dengan learning rate 0.001.
•	Loss Function: Mean Squared Error (MSE).
•	Hasil:
o	Model dilatih selama 100 epoch tanpa overfitting, berdasarkan grafik loss (Training Loss ≈ Validation Loss).
________________________________________

Tahapan 6: Evaluasi dan Visualisasi
Penjelasan:
Model dievaluasi menggunakan data pengujian dengan metrik berikut:
1.	MSE (Mean Squared Error): Mengukur rata-rata kesalahan kuadrat.
2.	RMSE (Root Mean Squared Error): Akar kuadrat MSE untuk interpretasi mudah.
3.	MAE (Mean Absolute Error): Rata-rata selisih absolut antara prediksi dan aktual.
4.	MAPE (Mean Absolute Percentage Error): Persentase rata-rata kesalahan prediksi.
5.	R² (Coefficient of Determination): Seberapa baik model menjelaskan variansi data.
Hasil Evaluasi:
•	MSE: 0.0018
•	RMSE: 0.042
•	MAE: 0.031
•	MAPE: 1.62%
•	R²: 0.95
Visualisasi:
•	Scatter Plot: Prediksi mendekati nilai aktual.
•	Histogram Residuals: Kesalahan terkonsentrasi di sekitar 0.
•	Boxplot Kesalahan: Menunjukkan distribusi kesalahan yang kecil.
________________________________________
Tahapan 7: Dokumentasi dan Kesimpulan
Penjelasan:
Model disimpan dalam format .keras untuk keperluan deployment, dan pipeline prediksi data baru disiapkan.
Kesimpulan:
•	Model LSTM memiliki performa yang sangat baik dengan nilai R² = 0.95 dan MAPE = 1.62%.
•	Visualisasi menunjukkan bahwa model mampu menangkap pola data dengan akurasi tinggi.
•	Pipeline prediksi mempermudah pengguna untuk memproses data baru tanpa pelatihan ulang.
