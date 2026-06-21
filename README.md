# Praktikum-2-Komputasi-Numerik

soal : 

Salah satu kelemahan dari metode Trapezoidal adalah kita harus menggunakan jumlah interval yang besar untuk memperoleh akurasi yang diharapkan. Buatlah sebuah program komputer untuk menjelaskan bagaimana metode Integrasi Romberg dapat mengatasi kelemahan tersebut.

PANDUAN PENGGUNAAN PROGRAM INTEGRASI ROMBERG BERBASIS C
Program ini dirancang untuk menghitung nilai integrasi numerik dari suatu fungsi matematika menggunakan **Metode Integrasi Romberg (Ekstrapolasi Richardson)** berbasis susunan matriks T(p,q) sekaligus mengekspor hasilnya ke dalam file laporan eksternal secara otomatis. Berikut adalah langkah-langkah penggunaannya:

1. Tahap Persiapan (Mengatur Fungsi Integrasi)
Sebelum menjalankan program, fungsi matematika yang akan dihitung nilai integralnya harus dimasukkan secara langsung (**hardcode**) ke dalam kode sumber (source code).
Buka kode program dan temukan baris fungsi makro **#define f(x) (1.0 / (x))** di bagian paling atas (di bawah deklarasi library).
**Ganti rumus di dalam kurung paling kanan** dengan persamaan matematika yang menjadi soal praktikum.
Contoh: Untuk mencari integral dari fungsi f(x) = x^2, ubah baris tersebut menjadi **#define f(x) (pow(x, 2))**
**Penting:** Setiap kali persamaan matematika diubah, program **wajib di-compile ulang** agar sistem membaca rumus baru yang dimasukkan.

2. Tahap Eksekusi (Kompilasi dan Jalankan)
Lakukan proses kompilasi (**Compile dan Run**) menggunakan software IDE C/C++ bawaan (seperti Dev-C++, Code::Blocks, atau VS Code).
Tunggu beberapa saat hingga jendela terminal berwarna hitam muncul di layar.

3. Format Masukkan (Input) Terminal
Setelah terminal terbuka, program akan meminta pengguna untuk memasukkan parameter spesifik secara berurutan. Ketik angka yang diminta, lalu **tekan Enter** pada setiap langkahnya:
**Masukkan Batas Bawah (x0) dan Batas Atas (xn):** Ketik nilai batas bawah integrasi diikuti dengan nilai batas atas, dipisahkan oleh spasi (misalnya: 1.0 2.0).
(Catatan Keamanan 1: Program memiliki proteksi otomatis. **Jika nilai batas bawah lebih besar atau sama dengan batas atas (x0 >= xn)**, program akan mencetak pesan **ERROR** dan berhenti).
(Catatan Keamanan 2: Karena fungsi bawaan adalah 1/x yang asimtotik/tak terhingga di x=0, **jika rentang integrasi Anda melewati atau menyentuh angka 0 (x0 <= 0 dan xn >= 0)**, program akan memunculkan pesan **ERROR fatal** dan langsung keluar demi menghindari pembagian dengan nol).
**Masukkan Ordo p dan q yang Dibutuhkan T(p,q):** Ketik tingkat ordo baris (p) dan kolom (q) matriks Romberg yang Anda inginkan, dipisahkan oleh spasi (misalnya: 4 4).
(Catatan Batasan Memori: Program memiliki limitasi array. **Nilai p harus berada di rentang 1 hingga 9** karena batas alokasi memori array t[10][10]. Selain itu, **ordo q tidak boleh bernilai negatif dan tidak boleh lebih besar dari nilai p**).

4. Tahap Pengambilan Hasil (Output)
Setelah parameter selesai dimasukkan, program akan langsung melakukan perhitungan komputasi ekstrapolasi secara instan dan mengeluarkan dua bentuk hasil (output):
**Output Notifikasi Terminal:** Di layar terminal akan tercetak pesan konfirmasi keberhasilan: **[SUKSES] Perhitungan selesai. Silakan buka file hasil_romberg.csv yang baru saja dibuat.**
**Output Dokumen Tabel (CSV):** Program secara otomatis menyusun seluruh baris dan kolom matriks ke dalam sebuah file dokumen spreadsheet bernama **hasil_romberg.csv**. File ini akan langsung tersimpan di dalam **folder yang sama** dengan file program C Anda. Dokumen ini menyajikan:
**Analisis Teoretis:** Penjelasan komparatif otomatis mengenai kelemahan metode Trapesium konvensional (Kolom 0) dibandingkan dengan efisiensi Solusi Romberg (Kolom 1 dan seterusnya).
**Tabel Matriks Romberg:** Struktur tabel rapi yang memetakan nilai konvergensi integrasi dari ordo interval i=0 hingga i=p, serta kolom koreksi k=0 hingga k=q.
**Kesimpulan Akhir:** Menampilkan perbandingan performa langsung antara hasil akhir hitungan Metode Trapesium dengan akurasi tinggi dari hasil akhir Metode Romberg.


```
#include <stdio.h>
#include <math.h>

#define f(x) (1.0 / (x))

int main() {
    float x0, xn, t[10][10], h, sm, sl, a;
    int i, k, c, m, p, q;

    printf("Masukkan batas bawah (x0) dan batas atas (xn): ");
    scanf("%f %f", &x0, &xn);

    if (x0 >= xn) {
        printf("ERROR: Batas bawah (x0) harus lebih kecil dari batas atas (xn).\n");
        return 1;
    }

    if (x0 <= 0 && xn >= 0) {
        printf("ERROR: Rentang integrasi tidak boleh menyentuh atau melewati 0 "
               "(fungsi 1/x bernilai tak terhingga di x=0).\n");
        return 1;
    }

    printf("Masukkan ordo p dan q yang dibutuhkan T(p,q): ");
    scanf("%d %d", &p, &q);

    if (p < 1 || p > 9) {
        printf("ERROR: Ordo p harus berada di rentang 1 hingga 9 "
               "(karena batas maksimal memori array t[10][10]).\n");
        return 1;
    }
    if (q < 0 || q > p) {
        printf("ERROR: Ordo q tidak boleh negatif dan tidak boleh lebih besar dari p "
               "(maksimal q untuk saat ini adalah %d).\n", p);
        return 1;
    }

    /* Hitung kolom trapesium (k=0) */
    h = xn - x0;
    t[0][0] = h / 2.0 * (f(x0) + f(xn));

    for (i = 1; i <= p; i++) {
        sl = (int)pow(2, i - 1);
        sm = 0;
        for (k = 1; k <= sl; k++) {
            a = x0 + (2 * k - 1) * h / pow(2, i);
            sm = sm + f(a);
        }
        t[i][0] = t[i - 1][0] / 2.0 + sm * h / pow(2, i);
    }

    for (i = 1; i <= p; i++) {
        for (k = 1; k <= i && k <= q; k++) {
            t[i][k] = (pow(4, k) * t[i][k - 1] - t[i - 1][k - 1]) / (pow(4, k) - 1);
        }
    }

    /* Tulis hasil ke CSV */
    FILE *fp = fopen("hasil_romberg.csv", "w");
    if (fp == NULL) {
        printf("ERROR: Gagal membuat file hasil,\n"
               "pastikan file hasil_romberg.csv yang ada tidak terbuka!\n");
        return 1;
    }

    fprintf(fp, "ROMBERG\n\n");
    fprintf(fp, "Kelemahan Trapesium:;"
                "Metode Trapesium (Kolom 0) mengharuskan kita memperbanyak interval "
                "secara drastis (ke arah bawah) agar nilai konvergen dan akurat.\n");
    fprintf(fp, "Solusi Romberg:;"
                "Ekstrapolasi Romberg (Kolom 1 dan seterusnya) memanipulasi error "
                "sehingga nilai akurat jauh lebih cepat didapat dengan menggeser data "
                "ke kanan tanpa harus memperbanyak interval seperti pada metode trapesium.\n\n");

    fprintf(fp, "Ordo Interval (i)");
    for (k = 0; k <= q; k++) {
        fprintf(fp, ";k=%d", k);
    }
    fprintf(fp, "\n");

    for (i = 0; i <= p; i++) {
        fprintf(fp, "%d", i);
        for (k = 0; k <= i && k <= q; k++) {
            fprintf(fp, ";%f", t[i][k]);
        }
        fprintf(fp, "\n");
    }

    fprintf(fp, "\nKESIMPULAN\n");
    fprintf(fp, "Trapesium (Iterasi Terbanyak) =;%f\n", t[p][0]);
    fprintf(fp, "Romberg (Hasil Akhir) =;%f\n",         t[p][q]);
    fclose(fp);

    printf("\n[SUKSES] Perhitungan selesai.\n");
    printf("Silakan buka file 'hasil_romberg.csv' yang baru saja dibuat.\n");
    return 0;
}

```


