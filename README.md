# Praktikum-2-Komputasi-Numerik

```
#include <stdio.h>
#include <math.h>
#define f(x) 1 / (x)
int main(){
  float x0, xn, t[10][10], h, sm, sl, a;
  int i, k, c, m, p, q;
  printf("Masukkan batas bawah (x0) dan batas atas (xn): ");
  scanf("%f%f", &x0, &xn);


  if (x0 >= xn){
    printf("ERROR: Batas bawah (x0) harus lebih kecil dari batas atas (xn).\n");
    return 1;
  }

  if (x0 <= 0 && xn >= 0){
    printf("ERROR: Rentang integrasi tidak boleh menyentuh atau melewati 0 (fungsi 1/x
    bernilai tak terhingga di x=0).\n");
    return 1;
  }


printf("Masukkan ordo p dan q yang dibutuhkan T(p,q): ");
scanf("%d%d", &p, &q);
if (p < 1 || p > 9){
  printf("ERROR: Ordo p harus berada di rentang 1 hingga 9 (karena batas maksimal
  memori array t[10][10]).\n");
  return 1;
}
if (q < 0 || q > p){
  printf("ERROR: Ordo q tidak boleh negatif dan tidak boleh lebih besar dari p (maksimal q
  untuk saat ini adalah %d).\n", p);
  return 1;
}
h = xn - x0;
t[0][0] = h / 2 * ((f(x0)) + (f(xn)));
for (i = 1; i <= p; i++){
  sl = pow(2, i - 1);
  sm = 0;
  for (k = 1; k <= sl; k++){
    a = x0 + (2 * k - 1) * h / pow(2, i);
    sm = sm + (f(a));
  }
  t[i][0] = t[i - 1][0] / 2 + sm * h / pow(2, i);
}
for (c = 1; c <= p; c++){
  for (k = 1; k <= c && k <= q; k++){
  m = c - k;
  t[m + k][k] = (pow(4, k) * t[m + k][k - 1] - t[m + k - 1][k - 1]) / (pow(4, k) - 1);
  }
}
FILE *fp = fopen("hasil_romberg.csv", "w");
if (fp == NULL)
{
  printf("ERROR: Gagal membuat file hasil, \npastikan file hasil_romberg.csv yang ada tidak
  terbuka!\n");
  return 1;
}
fprintf(fp, "ROMBERG\n\n");
fprintf(fp, "Kelemahan Trapesium:;Metode Trapesium (Kolom 0) mengharuskan kita
memperbanyak interval secara drastis (ke arah bawah) agar nilai konvergen dan akurat.\n");
fprintf(fp, "Solusi Romberg:;Ekstrapolasi Romberg (Kolom 1 dan seterusnya) memanipulasi
error sehingga nilai akurat jauh lebih cepat didapat dengan menggeser data ke kanan tanpa
harus memperbanyak interval seperti pada metode trapesium.\n\n");
fprintf(fp, "Ordo Interval (i)");
for (k = 0; k <= q; k++){
  fprintf(fp, ";k=%d", k);
}
fprintf(fp, "\n");
for (i = 0; i <= p; i++){
  fprintf(fp, "%d", i);
  for (k = 0; k <= i && k <= q; k++){
    fprintf(fp, ";%f", t[i][k]);
  }
fprintf(fp, "\n");
}

  fprintf(fp, "\nKESIMPULAN\n");
  fprintf(fp, "Trapesium (Iterasi Terbanyak) =;%f\n", t[p][0]);
  fprintf(fp, "Romberg (Hasil Akhir) =;%f\n", t[p][q]);
  fclose(fp);
  printf("\n[SUKSES] Perhitungan selesai.\n");
  printf("Silakan buka file 'hasil_romberg.csv' yang baru saja dibuat.\n");
return 0;
}
```
