#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <string.h>

struct barang
{
    char nama_barang[50];
    int harga_barang;
    int jumlah_barang;
    double diskon_barang;
};
int harga_barang[] = {5000, 2000, 1000, 1000, 500};

int index = 0;
struct barang pembelian[100];
int total_jumlah_barang = 0;
int total_harga_barang = 0;
double jumlah_diskon = 0;
int total_harga_final = 0;

void resetData();
void pesanBarang(int kode_barang);
void hitungDiskon(struct barang *barang);
void hitungTotal();
void clearScreen();
int main()
{
    // Tetapkan seed untuk fungsi rand() menggunakan waktu saat ini
    srand(time(NULL));

    printf("Selamat datang di toko skhenssa\n");
    printf("Silahkan pilih barang yang anda inginkan\n");
    printf("=========================================================\n");
    printf("|No.    | Barang                        | Harga         |\n");
    printf("---------------------------------------------------------\n");
    printf("|1      | Buku Tulis                    | Rp. 5000      |\n");
    printf("|2      | Pensil                        | Rp. 2000      |\n");
    printf("|3      | Penghapus                     | Rp. 1000      |\n");
    printf("|4      | Penggaris                     | Rp. 1000      |\n");
    printf("|5      | Bujur Sangkar                 | Rp. 500       |\n");
    printf("=========================================================\n\n");
    printf("99. Struk Pembayaran\n");
    printf("55. Reset Pilihan\n");
    printf("00. Keluar\n\n");
    printf("=========================================================\n\n");

    int kode_barang;
    while (1) {
        printf("\n input pilihan yang anda inginkan: ");
        scanf("%d", &kode_barang);

        if (kode_barang == 99) {
            hitungTotal();
            break;
        } else if (kode_barang == 55) {
            // bisa pakai cls kalau windows, kalau berbagai OS pakai \033[H itu
           system("cls");
             resetData();
           // clearScreen();
            main();
            break;
        } else if (kode_barang == 0) {
            exit(EXIT_SUCCESS);
        } else if (kode_barang >= 1 && kode_barang <= 5) {
            pesanBarang(kode_barang);
        } else {
            printf("Terjadi kesalahan input, menu no.%d tidak ada dalam daftar barang\n", kode_barang);
            printf("--input kembali pls--");
        }
    }
    return 0;
}
//fungsi reset data setelah di clearscreen
void resetData() {
    index = 0;
    total_jumlah_barang = 0;
    total_harga_barang = 0;
    jumlah_diskon = 0;
    total_harga_final = 0;
}
// Fungsi untuk menukar dua elemen
void swap(struct barang *a, struct barang *b) {
    struct barang temp = *a;
    *a = *b;
    *b = temp;
}
// Fungsi untuk mengurutkan produk berdasarkan jumlah terbanyak menggunakan Bubble Sort
void sortBarangByJumlahTerbanyak(struct barang arr[], int n) {
    int i, j;
    for (i = 0; i < n-1; i++) {
        for (j = 0; j < n-i-1; j++) {
            if (arr[j].jumlah_barang < arr[j+1].jumlah_barang) {
                swap(&arr[j], &arr[j+1]);
            }
        }
    }
}
void pesanBarang(int kode_barang) {
    // Mencari apakah barang sudah pernah dipesan sebelumnya
    int i;
    for (i = 0; i < index; i++) {
        if (strcmp(pembelian[i].nama_barang, (kode_barang == 1) ? "Buku Tulis" :
                                                (kode_barang == 2) ? "Pensil" :
                                                (kode_barang == 3) ? "Penghapus" :
                                                (kode_barang == 4) ? "Penggaris" : "Bujur Sangkar") == 0) {
            // Jika sudah pernah dipesan, tambahkan jumlah pesanan yang baru ke jumlah pesanan yang sudah ada
            printf("Pemesanan %s\nJumlah pesan : ", (kode_barang == 1) ? "Buku Tulis" :
                                                       (kode_barang == 2) ? "Pensil" :
                                                       (kode_barang == 3) ? "Penghapus" :
                                                       (kode_barang == 4) ? "Penggaris" : "Bujur Sangkar");
            int tambahan;
            scanf("%d", &tambahan);
            pembelian[i].jumlah_barang += tambahan;

            total_jumlah_barang += tambahan; // Menambahkan jumlah pesanan baru ke total jumlah barang
            total_harga_barang += tambahan * pembelian[i].harga_barang; // Menambahkan harga pesanan baru ke total harga barang
            hitungDiskon(&pembelian[i]); // Menghitung diskon kembali setelah penambahan pesanan
            return;
        }
    }

    // Jika barang belum pernah dipesan sebelumnya, tambahkan sebagai pesanan baru
    printf("\nPemesanan ");
    switch (kode_barang) {
        case 1:
            printf("Buku Tulis");
            break;
        case 2:
            printf("Pensil");
            break;
        case 3:
            printf("Penghapus");
            break;
        case 4:
            printf("Penggaris");
            break;
        case 5:
            printf("Bujur Sangkar");
            break;
    }
    printf("\nJumlah pesan : ");
    scanf("%d", &pembelian[index].jumlah_barang);
    strcpy(pembelian[index].nama_barang, (kode_barang == 1) ? "Buku Tulis" :
                                          (kode_barang == 2) ? "Pensil" :
                                          (kode_barang == 3) ? "Penghapus" :
                                          (kode_barang == 4) ? "Penggaris" : "Bujur Sangkar");
    pembelian[index].harga_barang = harga_barang[kode_barang - 1];
    total_jumlah_barang += pembelian[index].jumlah_barang; // Menambahkan jumlah pesanan baru ke total jumlah barang
    total_harga_barang += pembelian[index].jumlah_barang * pembelian[index].harga_barang; // Menambahkan harga pesanan baru ke total harga barang

    hitungDiskon(&pembelian[index]); // Menghitung diskon untuk pesanan baru
    index++;
}


void hitungDiskon(struct barang *barang) {
    if (barang->jumlah_barang > 5) {
        barang->diskon_barang = barang->harga_barang * barang->jumlah_barang * 0.15;
    } else if (barang->jumlah_barang > 3) {
        barang->diskon_barang = barang->harga_barang * barang->jumlah_barang * 0.10;
    } else {
        barang->diskon_barang = 0;
    }

    jumlah_diskon += barang->diskon_barang;
}

void hitungTotal() {

    sortBarangByJumlahTerbanyak(pembelian, index);
    printf("Rekap Pembelian:\n");
    printf("====================================================================\n");
    printf("|No.    | Jumlah  | Barang                        | Harga        | Diskon\n");
    printf("--------------------------------------------------------------------\n");

    for (int i = 0; i < index; i++) {
        printf("| %d\t\t|%-7d\t\t|%-30s\t\t|Rp. %-8d\t\t|Rp. %.2lf\t\t|\n", i + 1, pembelian[i].jumlah_barang, pembelian[i].nama_barang, pembelian[i].harga_barang * pembelian[i].jumlah_barang, pembelian[i].diskon_barang);
    }
    printf("====================================================================\n\n");

    printf("Total jumlah barang : %d\n", total_jumlah_barang);
    printf("Total harga barang  : %d\n", total_harga_barang);
    printf("Total jumlah diskon : %.2lf\n", jumlah_diskon);

    // Menghitung total harga final setelah diskon
    int total_harga_final = total_harga_barang - jumlah_diskon;

    // Menampilkan total harga final
    printf("Total harga final   : %d\n", total_harga_final);
    printf("====================================================================\n\n");

    // Input jumlah uang bayar
    int bayar;
    printf("Masukkan jumlah uang yang dibayarkan: ");
    scanf("%d", &bayar);

    int kembalian = bayar - total_harga_final;
    printf("Kembalian: %d\n", kembalian);
    printf("Struk Pembayaran:\n");

    // Menetapkan ID Struk menjadi lima angka dengan membatasi hasil dari rand()
    int id_struk = rand() % 100000; // Membatasi hasil menjadi 5 digit (0-99999)

    time_t t = time(NULL); // Mengambil waktu saat ini
    struct tm tm = *localtime(&t); // Konversi waktu ke dalam struktur tm

    printf("ID Struk: %05d\n", id_struk); // Menggunakan format "%05d" untuk memastikan ada lima digit
    printf("Waktu/Hari/jam: %d-%02d-%02d %02d:%02d:%02d\n", tm.tm_year + 1900, tm.tm_mon + 1, tm.tm_mday, tm.tm_hour, tm.tm_min, tm.tm_sec); // Menampilkan waktu saat ini

    printf("====================================================================\n");
    printf("|No.    | Jumlah  | Barang                        | Harga        | Diskon\n");
    printf("--------------------------------------------------------------------\n");
    for (int i = 0; i < index; i++) {
        printf("|%d      | %-7d | %-30s | Rp. %-8d | Rp. %.2lf\n", i+1, pembelian[i].jumlah_barang, pembelian[i].nama_barang, pembelian[i].harga_barang * pembelian[i].jumlah_barang, pembelian[i].diskon_barang);
    }
    printf("====================================================================\n\n");

    printf("Total jumlah barang : %d\n", total_jumlah_barang);
    printf("Total harga barang  : %d\n", total_harga_barang);
    printf("Total jumlah diskon : %.2lf\n", jumlah_diskon);
    printf("Total harga final   : %d\n", total_harga_final);
    printf("Jumlah harga yang dibayar : %d\n", bayar);
    printf("Kembalian : %d\n", kembalian);
    printf("====================================================================\n\n");

    // Membuat nama file dengan format "Struk_IDStruk.txt"
    char nama_file[20];
    sprintf(nama_file, "Struk_%05d.txt", id_struk); // Menggunakan format "%05d" untuk memastikan ada lima digit

    // Menyimpan struk pembayaran ke dalam file teks
    FILE *file = fopen(nama_file, "w");

    if (file != NULL) {
        fprintf(file, "Struk Pembayaran:\n");
        fprintf(file, "ID Struk: %05d\n", id_struk); // Menggunakan format "%05d" untuk memastikan ada lima digit
        fprintf(file, "Waktu/Hari/jam: %d-%02d-%02d %02d:%02d:%02d\n", tm.tm_year + 1900, tm.tm_mon + 1, tm.tm_mday, tm.tm_hour, tm.tm_min, tm.tm_sec);
        fprintf(file, "====================================================================\n");
        fprintf(file, "|No.    | Jumlah  | Barang                        | Harga        | Diskon\n");
        fprintf(file, "--------------------------------------------------------------------\n");
        for (int i = 0; i < index; i++) {
            fprintf(file, "|%d      | %-7d | %-30s | Rp. %-8d | Rp. %.2lf\n", i+1, pembelian[i].jumlah_barang, pembelian[i].nama_barang, pembelian[i].harga_barang * pembelian[i].jumlah_barang, pembelian[i].diskon_barang);
        }
        fprintf(file, "====================================================================\n\n");
        fprintf(file, "Total jumlah barang : %d\n", total_jumlah_barang);
        fprintf(file, "Total harga barang  : %d\n", total_harga_barang);
        fprintf(file, "Total jumlah diskon : %.2lf\n", jumlah_diskon);
        fprintf(file, "Total harga final  : %d\n", total_harga_final);
        fprintf(file, "Jumlah harga yang dibayar : %d\n", bayar);
        fprintf(file, "Kembalian : %d\n", kembalian);
        fprintf(file, "====================================================================\n\n");

        fclose(file);
        printf("Struk pembayaran telah disimpan dalam file %s.\n", nama_file);
    } else {
        printf("Gagal menyimpan struk pembayaran.\n");
    }
}

