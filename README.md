# AngklungineX Arc-2 — Materi Presentasi PowerPoint

> Dokumen ini berisi materi lengkap untuk pembuatan slide presentasi
> **AngklungineX Arc-2: Web-Based Rhythm Game untuk Pelestarian Angklung**
>
> Program: I-Will (Innovative Workingspace Integrated Living Laboratory) ITENAS
> Institusi: Institut Teknologi Nasional Bandung
> Periode: 2026

---

## Slide 1: Sampul / Cover

**Judul:**
AngklungineX Arc-2
Robot Angklung Interaktif Berbasis Game Web
dengan Web Serial API untuk Pelestarian Budaya

**Tim I-Will ITENAS 2026:**
- Yuddha Wastu Pramukha (152024058)
- Muhammad Farel Firdaus (152024061)
- Reisya Putri Ramadhani (152025112)
- Adinda Candra Putri (162025004)
- Fauzan Ramadhan (332024130)

**Dosen Pembimbing:**
*(TBD)*

**Logo:**
Institut Teknologi Nasional Bandung

**Subtitle:**
Melestarikan Angklung melalui Teknologi Interaktif

---

## Slide 2: Pengertian / Penjelasan Produk

### Apa itu AngklungineX Arc-2?

**AngklungineX Arc-2** adalah generasi ketiga dari robot angklung cerdas yang dikembangkan di ITENAS. Jika sebelumnya (Arc-1) fokus pada **hand gesture recognition** berbasis PC, Arc-2 beralih ke platform **web-based rhythm game** yang lebih aksesibel.

### Konsep Utama

```
Hardware (dari Arc-1)   +   Software Baru (Arc-2)
     14 angklung asli        Web-based Rhythm Game
     Central Lock motor      Free Play + Rhythm + Learning
     Arduino Mega            Web Serial API → langsung
```

### Tiga Mode Permainan

| Mode | Konsep | Untuk Siapa |
|------|--------|-------------|
| **Free Play** | Virtual Piano — klik nada → angklung berbunyi | Semua pengunjung |
| **Rhythm Mode** | Guitar Hero — nota berjatuhan, klik tepat waktu | Gamer, anak muda |
| **Learning Mode** | Belajar nada dari nol, panduan interaktif | Pelajar, pemula |

### Perbedaan AngklungineX vs Sistem Lain

| Fitur | AngklungineX | Robot Angklung Konvensional |
|-------|-------------|----------------------------|
| Interaksi | **Dua arah** (game → bunyi, gesture → bunyi) | Satu arah (hard-coded) |
| Kontrol | Web + Hand Tracking + Auto Play | Terbatas (saklar/button) |
| Biaya | Rendah (webcam + browser saja) | Mahal (Kinect/sensor khusus) |
| Platform | **PWA** — tanpa install, buka browser langsung main | Perangkat khusus |
| Komunikasi | Web Serial API langsung (no delay) | Serial / MQTT (lambat) |

---

## Slide 3: Latar Belakang

### Capaian Arc-1 (PKM-KC 2025) — Sudah Tercapai ✓

- ✅ 14-note hardware dengan Central Lock actuators
- ✅ Hand gesture recognition (95% akurasi, 67.5ms latency)
- ✅ 7 lagu nasional di Arduino standalone
- ✅ Web app + Mobile app
- ✅ IoT power monitoring (INA219 + ESP-NOW)

### Masalah yang Masih Ada

1. **Hand tracking terbatas**
   - Baru 8 dari 14 gestur
   - DO & FA sering salah klasifikasi
   - Butuh PC dengan kamera — tidak portabel

2. **Keterbatasan akses**
   - Harus install Python + dependensi
   - Cuma jalan di Windows
   - Tidak praktis untuk pameran/demo

3. **Kurangnya daya tarik**
   - Generasi muda lebih tertarik game daripada alat musik
   - Belum ada elemen "fun" yang bikin orang mau belajar

### Solusi Arc-2

```
❌ Arc-1: Install Python → Connect kamera → Gestur tangan → Angklung bunyi
✅ Arc-2: Buka Chrome → Connect USB → Klik game → Angklung bunyi
```

- **Zero install** — cukup browser Chrome/Edge
- **Cross-platform** — Windows, macOS, Android, Chromebook
- **Gamification** — belajar sambil bermain (Guitar Hero style)
- **Hardware sama** — tidak perlu beli komponen baru
- **Web Serial API** — langsung, tanpa delay, tanpa server

---

## Slide 4: Cara Kerja

### Alur Sistem — 3 Jalur Input, 1 Output

```
                    ┌──────────────────┐
                    │   INPUT METODE    │
                    ├──────────────────┤
                    │                  │
     ┌──────────────┼──────────────┐   │
     │              │              │   │
  FREE PLAY     RHYTHM MODE   LEARNING │
  (Klik nada)  (Falling notes) (Guide) │
     │              │              │   │
     └──────────────┼──────────────┘   │
                    │                  │
                    ▼                  │
           ┌────────────────┐         │
           │ Web Serial API │         │
           │ (USB Langsung) │         │
           └───────┬────────┘         │
                   │                  │
                   ▼                  │
          ┌──────────────────┐       │
          │   Arduino Mega   │       │
          │ (Parse command)  │       │
          └───────┬──────────┘       │
                  │                  │
                  ▼                  │
          ┌──────────────────┐       │
          │ Central Lock ×14 │       │
          │  + L293D Driver  │       │
          └───────┬──────────┘       │
                  │                  │
                  ▼                  │
          ┌──────────────────┐       │
          │ Angklung Asli ×14│       │
          │   (Bunyi Akustik)│       │
          └──────────────────┘       │
                                      │
  ─── JALUR TAMBAHAN (Arc-1) ───     │
  Kamera → MediaPipe → SVM → Serial  │
  (Masih bisa dipakai secara opsional)│
  ────────────────────────────────────┘
```

### Detail Alur: Free Play

```
User buka web → Klik "Connect" → Pilih COM port Arduino
         ↓
Klik tombol "Sol" di layar
         ↓
Browser kirim "SOL1_ON\n" via Web Serial API (115200 baud)
         ↓
Arduino terima → Parse command → Aktifkan motor Sol Rendah
         ↓
Motor push (70ms) → Tabung kena → Angklung berbunyi
         ↓
Lepas klik → Kirim "SOL1_OFF\n" → Motor berhenti
```

**Target latensi: <50ms** (dari klik ke bunyi)

### Detail Alur: Rhythm Mode

```
Game dimulai → Chart lagu (JSON) dimuat
         ↓
Nota berjatuhan di 14 lajur (1 lajur per nada)
         ↓
Saat nota mencapai garis hit → User harus klik nada yang sesuai
         ↓
Timing detection:
  Perfect (<50ms)  → 300 poin
  Great (50-100ms) → 200 poin
  Good (100-150ms) → 100 poin
  Miss (>150ms)    → 0 poin, combo putus
         ↓
Skor × Combo multiplier → Tampil di layar
```

### Detail Alur: Learning Mode

```
Pilih lesson (1-6):
  Lesson 1: Pengenalan Nama Nada
  Lesson 2: Pengenalan Bunyi Nada
  Lesson 3: Sequence 2-3 Nada
  Lesson 4: Tangga Nada
  Lesson 5: Bagian Lagu
  Lesson 6: Sight Reading
         ↓
Tampilkan instruksi + highlight nada yang harus ditekan
         ↓
User menekan nada → Sistem bandingkan expected vs actual
         ↓
Feedback: Hijau (benar) / Merah (salah) + tunjukkan jawaban benar
         ↓
Progress bar → Lesson selesai → Bintang ⭐⭐⭐
```

---

## Slide 5: Diagram Blok

### Diagram Blok Sistem Keseluruhan

```
┌────────────────────────────────────────────────────────────────────────────┐
│                          ANGKLUNGINEX ARC-2 SYSTEM                          │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌───────────────────────────────────────────────────────────────────┐     │
│  │                     WEB BROWSER (PWA)                              │     │
│  │                                                                     │     │
│  │  ┌──────────┐ ┌────────────┐ ┌──────────┐ ┌──────────────────┐   │     │
│  │  │Free Play │ │Rhythm Mode│ │Learning  │ │ Hand Tracking    │   │     │
│  │  │(Canvas)  │ │(Canvas)   │ │Mode      │ │ (Arc-1, optional)│   │     │
│  │  └────┬─────┘ └─────┬──────┘ └────┬─────┘ └────────┬─────────┘   │     │
│  │       │             │            │                 │              │     │
│  │       └─────────────┼────────────┘                 │              │     │
│  │                     │                              │              │     │
│  │              ┌──────▼──────┐                ┌──────▼──────┐       │     │
│  │              │Web Serial API│               │MediaPipe+SVM│       │     │
│  │              │(navigator   │                │(Python, PC) │       │     │
│  │              │ .serial)    │                │             │       │     │
│  │              └──────┬──────┘                └──────┬──────┘       │     │
│  └─────────────────────┼──────────────────────────────┼──────────────┘     │
│                        │ USB                          │ USB                │
└────────────────────────┼──────────────────────────────┼────────────────────┘
                         │                              │
                    ┌────▼──────────────────────────────▼─────┐
                    │           ARDUINO MEGA 2560              │
                    │   Firmware: Serial → Parse → PWM         │
                    │   (115200 baud, custom protocol)         │
                    │   OR Firmata (untuk hand tracking)        │
                    └───────────────────┬──────────────────────┘
                                        │
                    ┌───────────────────▼──────────────────────┐
                    │         MOTOR DRIVER L293D (×4)          │
                    │         (14 channels, H-Bridge)          │
                    └───────────────────┬──────────────────────┘
                                        │
                    ┌───────────────────▼──────────────────────┐
                    │         CENTRAL LOCK SOLENOID ×14         │
                    │         (Push-pull, 12V, PWM 70)          │
                    └───────────────────┬──────────────────────┘
                                        │
                    ┌───────────────────▼──────────────────────┐
                    │         ANGLUNG TUBES ×14                 │
                    │         (Sol rendah – Mi tinggi)          │
                    └──────────────────────────────────────────┘
```

### Diagram Blok Elektronik

```
                          Power Supply 12V 30A
                                │
                     ┌──────────┴──────────┐
                     │                     │
               Step Down LM2596       Motor Driver
               (→ 5V untuk Mega)       L293D (×4)
                     │                     │
                     ▼                     ▼
              Arduino Mega 2560     Central Lock ×14
                     │                     │
                     │           ┌─────────┴──────────┐
                     │           │                    │
               ESP32 (sensor)  Angklung Tubes (14)
                  (opsional)

      KOMUNIKASI:
      Browser ─── USB (Web Serial) ───→ Arduino Mega
      Kamera ─── USB (Opsional) ───→ PC (Hand Tracking)
      ESP32 ─── ESP-NOW ───→ ESP32 lain (Sensor Data)
```

---

## Slide 6: Rencana Pengembangan

### Tahap 1: Foundation (Sprint 1-2) — Sedang Dikerjakan

- [ ] **Web Serial API module** — connect/disconnect/send ke Arduino
- [ ] **Arduino firmware update** — custom serial protocol (bukan Firmata)
- [ ] **Free Play Mode** — 14 note buttons, sustain, audio preview
- [ ] Basic UI framework (routing, theming)

**Target:** Free Play end-to-end working (browser → Arduino → bunyi)

### Tahap 2: Rhythm Engine (Sprint 3-5)

- [ ] **Chart format & parser** — JSON-based note charts
- [ ] **Timing engine** — BPM, beat subdivision, note scheduling
- [ ] **Falling note renderer** — Canvas/WebGL animation
- [ ] **Scoring system** — Perfect/Great/Good/Miss + combo multiplier
- [ ] **Health bar & results screen**

**Target:** First playable Rhythm Mode with 2 test songs

### Tahap 3: Content & Learning (Sprint 6-8)

- [ ] **15 song charts** (Easy, Medium, Hard, Expert)
- [ ] **Learning Mode** — 6 lessons, progressive difficulty
- [ ] **Chart editor tool** (internal)
- [ ] **Difficulty balancing**

**Target:** Learning Mode complete + full song library

### Tahap 4: Polish & Release (Sprint 9-12)

- [ ] **PWA support** — installable, offline mode
- [ ] **UI refinement** — animations, responsive design
- [ ] **User testing** — real hardware, latency optimization
- [ ] **Documentation** — user guide, demo video
- [ ] **Deployment**

**Target:** v1.0 production release

### Fitur yang Dibawa dari Arc-1 (Sudah Jadi)

| Fitur | Status |
|-------|--------|
| 14-note hardware + Central Lock | ✅ Siap pakai |
| Standalone song player (7 lagu) | ✅ Bisa dipicu dari web |
| Hand gesture recognition (8 nada) | ✅ Opsional, tetap bisa dipakai |
| IoT power monitoring | ✅ Opsional |

---

## Slide 7: Timeline / Roadmap

### Timeline Arc-2 (6-12 Bulan)

| No | Kegiatan | Bulan 1 | Bulan 2 | Bulan 3 | Bulan 4 | Bulan 5 | Bulan 6 | Bulan 7-12 |
|----|----------|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:----------:|
| 1 | Web Serial + Free Play | ████████ | | | | | | |
| 2 | Arduino Firmware | ████████ | | | | | | |
| 3 | Rhythm Engine | | ████████ | ████████ | | | | |
| 4 | Chart System | | | ████████ | | | | |
| 5 | Song Charts Creation | | | | ████████ | ████████ | | |
| 6 | Learning Mode | | | | ████████ | ████████ | | |
| 7 | UI/UX Polish + PWA | | | | | | ████████ | ████████ |
| 8 | Testing & Deployment | | | | | | | ████████ |

### Milestone

| Bulan | Milestone |
|-------|-----------|
| **Bulan 1-2** | Free Play berfungsi (browser → Arduino → angklung) |
| **Bulan 3-5** | Rhythm Mode playable dengan 2 lagu |
| **Bulan 5-8** | 15 lagu + Learning Mode selesai |
| **Bulan 9-12** | v1.0 rilis — PWA, offline, dokumentasi |

### Capaian yang SUDAH ADA (bukan dikerjakan lagi)

| Milestone | Status |
|-----------|--------|
| 14-note hardware (Central Lock + Mega) | ✅ Selesai (Arc-1) |
| Arduino song player (7 lagu nasional) | ✅ Selesai (Arc-1) |
| Hand gesture recognition (95% akurasi) | ✅ Selesai (Arc-1) |
| IoT power monitoring | ✅ Selesai (Arc-1) |

### Capaian yang AKAN DATANG (Arc-2)

| Milestone | Status |
|-----------|--------|
| Web Serial API → Arduino | 🔜 Sprint 1-2 |
| Free Play Mode | 🔜 Sprint 1-2 |
| Rhythm Mode | 🔜 Sprint 3-5 |
| 15 song charts | 🔜 Sprint 5-8 |
| Learning Mode | 🔜 Sprint 6-8 |
| PWA + offline | 🔜 Sprint 9-10 |
| v1.0 rilis | 🔜 Sprint 11-12 |

---

## Slide 8: Tim

### Tim Arc-2 (I-Will ITENAS 2026)

| Nama | NRP | Peran |
|------|-----|-------|
| Yuddha Wastu Pramukha | 152024058 | Ketua / Full Stack |
| Muhammad Farel Firdaus | 152024061 | Frontend Development |
| Reisya Putri Ramadhani | 152025112 | Game Design & Content |
| Adinda Candra Putri | 162025004 | Backend & Testing |
| Fauzan Ramadhan | 332024130 | Hardware & Mekanik |

### Regenerasi dari Arc-1

Arc-1 (PKM-KC 2025) → Arc-2 (I-Will 2026)

| Arc-1 | → | Arc-2 |
|-------|---|-------|
| Rainova Rahaniawan (Ketua) | | Yuddha Wastu Pramukha (Ketua) |
| Shandy Handika | | Muhammad Farel Firdaus |
| Melvina Cheda Rismayanta | | Reisya Putri Ramadhani |
| Yuddha Wastu Pramukha | | Adinda Candra Putri |
| | | Fauzan Ramadhan |

**Dosen Pembimbing:** *(TBD)*

---

## Slide 9: Spesifikasi Teknis

### Hardware (Carried Over from Arc-1)

| Komponen | Spesifikasi |
|----------|-------------|
| Mikrokontroler | Arduino Mega 2560 |
| Aktuator | Central Lock solenoid × 14 |
| Motor Driver | L293D × 4 (14 channel) |
| Power Supply | 12V 30A |
| Regulator | LM2596 (Step Down 12V → 5V) |
| Rangka | Akrilik + 3D Print (PLA+) |
| Angklung | 14 nada (Sol rendah – Mi tinggi) |

### Software Stack

| Layer | Teknologi |
|-------|-----------|
| Frontend | HTML5 + CSS3 + JavaScript (Canvas/WebGL) |
| Serial | Web Serial API |
| Audio | Web Audio API |
| Charts | JSON format |
| PWA | Service Worker + manifest.json |
| Arduino | C++ (Custom serial protocol) |

### Performance Target

| Metrik | Arc-1 (Tercapai) | Arc-2 (Target) |
|--------|:----------------:|:--------------:|
| Latensi | 67.5 ms (gesture→bunyi) | <50 ms (klik→bunyi) |
| Akurasi | 95% (8 gestur) | N/A (input digital) |
| Platform | Windows only | Cross-platform (browser) |

---

## Slide 10: Manfaat & Dampak

### Manfaat

1. **Edukasi**
   - Media pembelajaran angklung yang interaktif dan menyenangkan
   - Learning Mode: dari tidak tahu nada → bisa main lagu
   - Mengatasi kekurangan instruktur angklung

2. **Teknologi**
   - Web Serial API + Game Engine = inovasi alat musik digital
   - Penerapan pertama game rhythm pada alat musik tradisional
   - Cross-platform, zero-install

3. **Budaya**
   - Pelestarian angklung melalui pendekatan generasi digital
   - Jembatan antara tradisi dan teknologi
   - Potensi replikasi ke alat musik tradisional lain

4. **Aksesibilitas**
   - Siapa pun bisa main angklung — tanpa harus bisa memegang atau menggoyangkan
   - Cukup HP/Chromebook + browser

### Dampak Jangka Panjang

- **Regenerasi pemain angklung** — anak muda tertarik lewat game
- **Model untuk alat tradisional lain** — gamelan, sasando, kolintang bisa menggunakan pendekatan serupa
- **Publikasi ilmiah** — potensi conference paper tentang game-based cultural preservation

---

## Slide 11: Kesimpulan

### Kesimpulan

1. AngklungineX adalah bukti bahwa **teknologi dan budaya bisa berjalan beriringan**
2. Arc-1 membuktikan konsep: hand gesture + AI untuk main angklung (95% akurasi, 67.5ms ✓)
3. Arc-2 melanjutkan dengan pendekatan **lebih aksesibel**: game web, zero-install, langsung main
4. Hardware (14 nada + Central Lock) sudah siap — **fokus sekarang di software dan konten**
5. Tim baru, semangat baru, target: **v1.0 dalam 6-12 bulan**

### Pesan Penutup

> "Angklung bukan sekadar alat musik — ini warisan dunia yang harus terus hidup.
> Dengan teknologi, kita tidak hanya melestarikan, tapi juga membuatnya relevan
> untuk generasi yang akan datang."

---

## Slide 12: Terima Kasih

**Terima Kasih**

**AngklungineX**
Melestarikan Angklung melalui Teknologi Interaktif

Tim I-Will Institut Teknologi Nasional Bandung 2026

*"Dari Indonesia untuk Dunia"*
