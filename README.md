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
Organisasi memerlukan orkestrasi kontainer karena:
- **Skalabilitas:** Dapat dengan mudah menambah/mengurangi jumlah replika sesuai beban kerja tanpa konfigurasi manual yang rumit
- **High Availability:** Jika satu server/container gagal, sistem tetap berjalan karena ada replika di server lain
- **Load Balancing Otomatis:** Traffic didistribusikan secara otomatis ke berbagai replika
- **Manajemen Terpusat:** Mengelola banyak container di banyak server sebagai satu sistem tunggal
- **Self-Healing:** Container yang gagal akan diganti otomatis tanpa intervensi manual
- **Efisiensi Sumber Daya:** Pemanfaatan resource server lebih optimal dengan penjadwalan otomatis

### 2. Jelaskan peran Manager Node dan Worker Node, serta apa yang terjadi apabila sebuah Worker Node mengalami kegagalan (down)?

*Jawaban:*
- **Manager Node:** Bertugas mengelola seluruh klaster, membuat keputusan penjadwalan (scheduling) task ke worker node, menyimpan state klaster, dan menangani orkestrasi. Manager menggunakan algoritma Raft consensus untuk memastikan konsistensi data antar manager.
- **Worker Node:** Menerima dan menjalankan task (container) yang dijadwalkan oleh manager. Worker hanya menjalankan task tanpa ikut dalam pengambilan keputusan orkestrasi.

Jika Worker Node mengalami kegagalan (down):
- Task yang berjalan di worker tersebut akan terdeteksi gagal oleh manager
- Swarm akan otomatis menjadwalkan ulang task tersebut ke worker node lain yang masih aktif
- Ini adalah fitur self-healing yang memastikan jumlah replika tetap sesuai yang diinginkan

### 3. Apa fungsi Overlay Network dalam Docker Swarm dan mengapa jaringan ini penting untuk komunikasi antar-container di node yang berbeda?

*Jawaban:*
Overlay Network adalah jaringan virtual yang mencakup seluruh klaster Swarm dan menghubungkan container yang berjalan di node yang berbeda. Fungsinya:
- Mengizinkan container di node berbeda untuk saling berkomunikasi seolah-olah berada di jaringan yang sama
- Menyediakan load balancing internal untuk service
- Mendukung service discovery (container dapat menemukan service lain melalui nama service)

Jaringan ini penting karena:
- Tanpa overlay network, container di node berbeda tidak dapat saling berkomunikasi langsung
- Menggunakan enkripsi untuk keamanan komunikasi antar node
- Memungkinkan aplikasi yang terdiri dari beberapa service (misal: web + database) untuk berjalan terdistribusi di beberapa node tetap dapat saling berkomunikasi

---

## D. Kegiatan Praktik

Lakukan langkah-langkah berikut pada laboratorium/virtual machine yang telah disediakan (minimal 2 node: 1 manager dan 1 worker, atau gunakan 1 node untuk simulasi dasar).

### Langkah 1: Inisialisasi Swarm

Jalankan perintah berikut pada calon manager node, lalu catat hasilnya.

```bash
docker swarm init
```

**Catat token join yang dihasilkan:**
```
SWMTKN-1-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```
Catat IP address manager: `192.168.x.x:2377`

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
```
ID                            HOSTNAME   STATUS    AVAILABILITY   MANAGER STATUS
xxxx *                        manager    Ready     Active         Leader
xxxx                          worker     Ready     Active
```
Terlihat ada 2 node: 1 manager (status Leader) dan 1 worker (status Active)

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
```
docker service ls:
ID             NAME   MODE         REPLICAS   IMAGE             PORTS
xxxx           web    replicated   3/3        nginx:latest      *:8080->80/tcp

docker service ps web:
ID             NAME      IMAGE      NODE      DESIRED STATE   CURRENT STATE
xxxx.1         web.1     nginx     manager   Running         Running
xxxx.2         web.2     nginx     worker    Running         Running
xxxx.3         web.3     nginx     manager   Running         Running
```
Service web berjalan dengan 3 replika, terdistribusi di manager dan worker node

### Langkah 4: Scaling Service

Ubah jumlah replika service menjadi 5, lalu amati perubahannya.

```bash
docker service scale web=5
```

**Apa yang terjadi setelah perintah scale dijalankan? Jelaskan hasil pengamatanmu:**
Swarm otomatis menambah 2 replika baru. Hasil `docker service ps web` menunjukkan 5 task berjalan (3 yang lama + 2 yang baru). Swarm mendistribusikan task baru ke node yang tersedia berdasarkan beban kerja dan resource availability. Scaling dilakukan hampir instan tanpa perlu konfigurasi manual.

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
Setelah menghapus salah satu container, Swarm mendeteksi bahwa jumlah replika (4) kurang dari yang diinginkan (5). Swarm otomatis membuat task baru untuk menggantikan yang dihapus. Beberapa detik kemudian, `docker service ps web` menunjukkan kembali 5 task berjalan. Ini membuktikan fitur self-healing Swarm yang menjaga jumlah replika tetap sesuai yang diinginkan.

---

## E. Evaluasi Pemahaman

Jawablah pertanyaan berikut secara individu.

### 1. Sebutkan minimal 4 komponen utama dalam arsitektur Docker Swarm beserta fungsinya masing-masing.

*Jawaban:*
1. **Node:** Instance Docker Engine yang tergabung dalam Swarm. Dapat berupa Manager atau Worker.
2. **Manager Node:** Mengelola state klaster, menjadwalkan task ke worker, dan menangani orkestrasi menggunakan algoritma Raft.
3. **Worker Node:** Menerima dan menjalankan task yang dijadwalkan oleh manager. Tidak ikut dalam pengambilan keputusan orkestrasi.
4. **Service:** Definisi tugas yang akan dijalankan, termasuk image yang digunakan dan jumlah replika yang diinginkan.
5. **Task:** Unit kerja terkecil yang merupakan satu instance container yang dijadwalkan oleh Swarm ke sebuah node.
6. **Overlay Network:** Jaringan virtual yang menghubungkan container antar node berbeda dalam Swarm.
7. **Replica:** Salinan instance dari service yang berjalan untuk load balancing dan high availability.

### 2. Jelaskan perbedaan antara 'service' dan 'task' dalam Docker Swarm.

*Jawaban:*
- **Service** adalah definisi atau template dari apa yang ingin dijalankan. Service mencakup image yang digunakan, jumlah replika yang diinginkan, port mapping, network, dan konfigurasi lainnya. Service adalah abstraksi level tinggi yang didefinisikan oleh user.
- **Task** adalah instance aktual dari container yang berjalan. Task adalah unit kerja terkecil yang dijadwalkan oleh Swarm ke sebuah node tertentu. Setiap replica dalam service diwujudkan sebagai satu atau lebih task.
- Hubungan: Service = "apa yang ingin dijalankan", Task = "instance yang sedang berjalan". Service dengan 3 replicas akan menghasilkan 3 task.

### 3. Mengapa jumlah manager node yang disarankan berjumlah ganjil (misalnya 3 atau 5)? Kaitkan jawabanmu dengan algoritma Raft consensus.

*Jawaban:*
Jumlah manager node ganjil disarankan karena algoritma Raft consensus membutuhkan mayoritas (quorum) untuk mencapai konsensus keputusan. Dengan jumlah ganjil:
- Selalu ada mayoritas yang jelas (misal: 3 node = 2 adalah mayoritas, 5 node = 3 adalah mayoritas)
- Menghindari kondisi split-brain (terjadi tie vote jika jumlah genap)
- Memastikan klaster tetap dapat mengambil keputusan meskipun ada beberapa manager yang gagal
- Dengan 3 manager, klaster dapat toleransi 1 manager gagal tetap tetap beroperasi
- Dengan 5 manager, klaster dapat toleransi 2 manager gagal tetap tetap beroperasi

### 4. Sebuah perusahaan menjalankan aplikasi web pada 1 replika saja. Apa risikonya, dan bagaimana Docker Swarm dapat mengatasi risiko tersebut?

*Jawaban:*
**Risiko 1 replika:**
- **Single Point of Failure:** Jika container/server gagal, aplikasi tidak dapat diakses sama sekali
- **Downtime:** Tidak ada backup yang siap menggantikan, menyebabkan downtime sampai manual recovery
- **Bottleneck:** Satu container menangani semua traffic, dapat menyebabkan performa buruk di high load
- **Tidak ada High Availability:** Tidak ada redundancy, SLA sulit terpenuhi

**Bagaimana Docker Swarm mengatasi:**
- **Multiple Replicas:** Dengan menjalankan 3+ replika, jika satu gagal, yang lain tetap melayani traffic
- **Self-Healing:** Container yang gagal otomatis diganti tanpa intervensi manual
- **Load Balancing:** Traffic didistribusikan otomatis ke berbagai replika, menghindari bottleneck
- **High Availability:** Aplikasi tetap dapat diakses meskipun ada node/container yang gagal
- **Easy Scaling:** Dapat dengan mudah menambah replika saat traffic tinggi dan mengurangi saat traffic rendah

---

## F. Refleksi

Tuliskan hal baru yang kamu pelajari hari ini serta bagian yang masih perlu kamu pelajari lebih lanjut.

**Hal baru yang dipelajari:**
- Docker Swarm adalah alat orkestrasi bawaan Docker untuk mengelola container dalam skala klaster (multi-node)
- Arsitektur Swarm terdiri dari Manager Node (mengelola klaster) dan Worker Node (menjalankan task)
- Swarm memiliki fitur self-healing otomatis untuk mengganti container yang gagal
- Scaling service dapat dilakukan dengan satu perintah sederhana (`docker service scale`)
- Overlay network memungkinkan komunikasi antar container di node berbeda
- Service adalah definisi tingkat tinggi, sedangkan Task adalah instance aktual yang berjalan
- Jumlah manager node harus ganjil untuk algoritma Raft consensus
- Swarm menyediakan load balancing otomatis untuk service

**Bagian yang masih perlu dipelajari lebih lanjut:**
- Konfigurasi health check untuk service
- Menggunakan docker-compose.yml dengan docker stack deploy
- Rolling update untuk update tanpa downtime
- Resource limits dan constraints pada service
- Monitoring dan logging di Swarm
- Secret management untuk data sensitif
- Config management untuk konfigurasi aplikasi
- Advanced networking (service discovery, DNS)
- Backup dan restore klaster Swarm

---

## Paraf Guru

Paraf Guru: _______________        Nilai: _______
