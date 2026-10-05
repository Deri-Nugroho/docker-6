# LKPD 6 - Pengantar Konsep Docker Swarm

## Informasi Mata Pelajaran
- **Mata Pelajaran:** DevOps Engineer
- **Topik:** Konsep Docker Swarm (Basic Container Orchestration)
- **Kelas / Semester:** ______________________
- **Nama Siswa:** ______________________
- **Kelompok:** ______________________
- **Alokasi Waktu:** 2 x 45 menit

---

## A. Capaian Pembelajaran & Tujuan Pembelajaran

### Capaian Pembelajaran
Peserta didik mampu memahami konsep orkestrasi kontainer (container orchestration) serta menerapkan Docker Swarm untuk mengelola kontainer dalam skala klaster (multi-node).

### Tujuan Pembelajaran
1. Peserta didik dapat menjelaskan konsep dan tujuan Docker Swarm sebagai alat orkestrasi kontainer.
2. Peserta didik dapat mengidentifikasi komponen-komponen utama dalam arsitektur Docker Swarm (node, manager, worker, service, task).
3. Peserta didik dapat membedakan mode Swarm dengan Docker standalone (single-host).
4. Peserta didik dapat mempraktikkan inisialisasi Swarm, membuat service, dan melakukan scaling replica.

---

## B. Materi Ringkas

### 1. Apa itu Docker Swarm?

Docker Swarm adalah alat orkestrasi kontainer bawaan Docker yang memungkinkan beberapa Docker Engine (disebut node) digabungkan menjadi satu klaster (cluster) yang dikelola sebagai satu sistem virtual tunggal. Dengan Swarm, aplikasi berbasis kontainer dapat dijalankan secara terdistribusi, memiliki kemampuan load balancing otomatis, self-healing (memulihkan kontainer yang gagal), dan scaling (menambah/mengurangi jumlah replika kontainer) dengan mudah.

### 2. Komponen Utama Arsitektur Swarm

- **Node:** satu instance Docker Engine yang tergabung dalam Swarm. Terbagi menjadi Manager Node dan Worker Node.
- **Manager Node:** bertugas mengelola state klaster, menjadwalkan (schedule) task ke worker, serta menangani orkestrasi. Menggunakan algoritma konsensus Raft agar tetap konsisten meski ada manager yang gagal.
- **Worker Node:** menerima dan menjalankan task (kontainer) yang diberikan oleh manager. Worker tidak ikut membuat keputusan orkestrasi.
- **Service:** definisi tugas yang akan dijalankan pada node, misalnya menjalankan image tertentu dengan jumlah replika tertentu.
- **Task:** satu instance kontainer yang merupakan unit kerja terkecil yang dijadwalkan oleh Swarm ke sebuah node.
- **Replica:** jumlah salinan (instance) dari sebuah service yang dijalankan secara bersamaan untuk keperluan load balancing/high availability.
- **Overlay Network:** jaringan virtual yang menghubungkan container antar node berbeda dalam satu Swarm sehingga dapat saling berkomunikasi.
- **Stack:** kumpulan service yang saling terkait, biasanya didefinisikan dalam satu file docker-compose.yml dan dijalankan bersama di atas Swarm.

### 3. Perbedaan Docker Standalone vs Docker Swarm

| Aspek | Docker Standalone | Docker Swarm |
|-------|------------------|--------------|
| Cakupan | Satu host/mesin | Banyak host (cluster/multi-node) |
| Penjadwalan | Manual per container | Otomatis oleh manager node |
| Skalabilitas | Terbatas, manual | Mudah, dengan perintah scale |
| Pemulihan otomatis | Tidak ada | Ada (self-healing) |
| Load balancing | Perlu alat tambahan | Bawaan (built-in) |

---

## C. Kegiatan Diskusi Kelompok

Diskusikan bersama kelompokmu, lalu tuliskan jawaban pada kolom yang tersedia.

### 1. Mengapa sebuah organisasi memerlukan orkestrasi kontainer seperti Docker Swarm dibandingkan hanya menjalankan container secara manual di satu server?

*Jawaban:*
_________________________________________________________________________
_________________________________________________________________________
_________________________________________________________________________

### 2. Jelaskan peran Manager Node dan Worker Node, serta apa yang terjadi apabila sebuah Worker Node mengalami kegagalan (down)?

*Jawaban:*
_________________________________________________________________________
_________________________________________________________________________
_________________________________________________________________________

### 3. Apa fungsi Overlay Network dalam Docker Swarm dan mengapa jaringan ini penting untuk komunikasi antar-container di node yang berbeda?

*Jawaban:*
_________________________________________________________________________
_________________________________________________________________________
_________________________________________________________________________

---

## D. Kegiatan Praktik

Lakukan langkah-langkah berikut pada laboratorium/virtual machine yang telah disediakan (minimal 2 node: 1 manager dan 1 worker, atau gunakan 1 node untuk simulasi dasar).

### Langkah 1: Inisialisasi Swarm

Jalankan perintah berikut pada calon manager node, lalu catat hasilnya.

```bash
docker swarm init
```

**Catat token join yang dihasilkan:**
_________________________________________________________________________

### Langkah 2: Bergabung sebagai Worker

Pada node lain, jalankan perintah:
```bash
docker swarm join --token <token> <ip-manager>:2377
```

dengan token yang diperoleh. Tuliskan hasil verifikasi menggunakan perintah:
```bash
docker node ls
```

**Hasil verifikasi:**
_________________________________________________________________________

### Langkah 3: Membuat Service

Buat sebuah service (misalnya nginx) dengan 3 replika menggunakan perintah:

```bash
docker service create --name web --replicas 3 -p 8080:80 nginx
```

Periksa status service dengan:
```bash
docker service ls
docker service ps web
```

**Tuliskan hasil pengamatanmu (nama container, node tempat berjalan, status):**
_________________________________________________________________________
_________________________________________________________________________
_________________________________________________________________________

### Langkah 4: Scaling Service

Ubah jumlah replika service menjadi 5, lalu amati perubahannya.

```bash
docker service scale web=5
```

**Apa yang terjadi setelah perintah scale dijalankan? Jelaskan hasil pengamatanmu:**
_________________________________________________________________________
_________________________________________________________________________
_________________________________________________________________________

### Langkah 5: Simulasi Self-Healing

Matikan salah satu container/task dari service tersebut secara manual, lalu amati apakah Swarm membuat replika baru secara otomatis.

```bash
# Hapus salah satu task
docker service ps web
docker rm -f <container-id>

# Amati apakah replika baru dibuat otomatis
docker service ps web
```

**Hasil pengamatan:**
_________________________________________________________________________
_________________________________________________________________________

---

## E. Evaluasi Pemahaman

Jawablah pertanyaan berikut secara individu.

### 1. Sebutkan minimal 4 komponen utama dalam arsitektur Docker Swarm beserta fungsinya masing-masing.

*Jawaban:*
_________________________________________________________________________
_________________________________________________________________________
_________________________________________________________________________

### 2. Jelaskan perbedaan antara 'service' dan 'task' dalam Docker Swarm.

*Jawaban:*
_________________________________________________________________________
_________________________________________________________________________
_________________________________________________________________________

### 3. Mengapa jumlah manager node yang disarankan berjumlah ganjil (misalnya 3 atau 5)? Kaitkan jawabanmu dengan algoritma Raft consensus.

*Jawaban:*
_________________________________________________________________________
_________________________________________________________________________
_________________________________________________________________________

### 4. Sebuah perusahaan menjalankan aplikasi web pada 1 replika saja. Apa risikonya, dan bagaimana Docker Swarm dapat mengatasi risiko tersebut?

*Jawaban:*
_________________________________________________________________________
_________________________________________________________________________
_________________________________________________________________________

---

## F. Refleksi

Tuliskan hal baru yang kamu pelajari hari ini serta bagian yang masih perlu kamu pelajari lebih lanjut.

**Hal baru yang dipelajari:**
_________________________________________________________________________
_________________________________________________________________________

**Bagian yang masih perlu dipelajari lebih lanjut:**
_________________________________________________________________________
_________________________________________________________________________

---

## Paraf Guru

Paraf Guru: _______________        Nilai: _______
