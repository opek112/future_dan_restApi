# 📢 Modul 04: Pengolahan Data Asinkronus dengan `Future` dan `FutureBuilder`

Proyek ini merupakan bagian dari **Modul 04 (Mobile Programming)** yang mengimplementasikan konsep pemrosesan data asinkronus (*asynchronous programming*) menggunakan kelas `Future` serta penanganan status UI (*state management*) secara reaktif menggunakan widget `FutureBuilder` di Flutter.

---

## 🎯 Pokok Pembahasan & Implementasi

1. **Konsep `Future` & `async/await`**:
   - Mengambil data dari REST API / Web Service secara *non-blocking*.
   - Mengembalikan data bertipe `Future<List<Announcement>>`.
2. **Pola 4-State UI (`FutureBuilder`)**:
   - Menangani 4 kondisi UI secara eksplisit berdasarkan `ConnectionState` dan data `AsyncSnapshot`:
     1. **Memuat Data (*Loading*)**: Menampilkan indikator pemrosesan saat data sedang diambil dari server.
     2. **Gagal Memuat (*Error*)**: Menampilkan pesan kesalahan dan tombol *Retry* saat koneksi gagal atau server *down*.
     3. **Data Kosong (*Empty*)**: Menampilkan tampilan khusus saat data berhasil diambil namun bernilai kosong/tidak ada hasil filter.
     4. **Berhasil Memuat (*Success*)**: Merender daftar kartu pengumuman (`AnnouncementCard`) menggunakan `ListView.builder`.
3. **Penyegaran Data (*Pull-to-Refresh*)**:
   - Mengintegrasikan `RefreshIndicator` untuk memuat ulang `Future` secara interaktif.

---

## 📸 Dokumentasi Tampilan Aplikasi (4 State)

| 1. Memuat Data (Loading) | 2. Gagal Memuat (Error) |
| :---: | :---: |
| ![Memuat Data](./screenshots/loading.png) | ![Gagal Memuat](./screenshots/error.png) |
| *Indikator pengerjaan asinkronus* | *Penanganan exception/koneksi terputus* |

| 3. Data Kosong / URL Salah (Empty) | 4. Berhasil Memuat (Success) |
| :---: | :---: |
| ![Data Kosong](./screenshots/empty.png) | ![Berhasil Memuat](./screenshots/success.png) |
| *Tampilan ketika data bernilai [ ]* | *Tampilan daftar pengumuman utuh* |

---

## 🛠️ Cuplikan Kode Utama (`FutureBuilder`)

```dart
FutureBuilder<List<Announcement>>(
  future: _futurePengumuman,
  builder: (context, snapshot) {
    // 1. STATE: LOADING
    if (snapshot.connectionState != ConnectionState.done) {
      return const Center(child: CircularProgressIndicator());
    }

    // 2. STATE: ERROR
    if (snapshot.hasError) {
      return Center(
        child: Text('Gagal Memuat: ${snapshot.error}'),
      );
    }

    final data = snapshot.data ?? [];

    // 3. STATE: EMPTY
    if (data.isEmpty) {
      return const Center(child: Text('Data tidak ditemukan'));
    }

    // 4. STATE: SUCCESS
    return ListView.builder(
      itemCount: data.length,
      itemBuilder: (context, index) => AnnouncementCard(
        announcement: data[index],
        onTap: () {},
      ),
    );
  },
)