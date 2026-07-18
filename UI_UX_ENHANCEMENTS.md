# 🎨 UI/UX Enhancements for KeyAPI

Dokumen ini berisi kumpulan usulan perbaikan antarmuka (UI) dan pengalaman pengguna (UX) untuk repository `maulinul/keyapi`. Tujuannya adalah membuat tampilan lebih **rich (kaya animasi)**, **compact (ringkas)**, **full (memanfaatkan ruang kosong)**, dan mendukung alur kerja **Input → Send → Review → View** dengan mulus.

> Status: sebagian besar usulan di dokumen ini **sudah diimplementasikan** di `index.html` (lihat PR #5–#11). Dokumen disimpan sebagai referensi desain.

---

## 📋 Daftar Isi
1. [Animated Background Particles](#1-animated-background-particles)
2. [Gradient Mesh Background](#2-gradient-mesh-background)
3. [Full-Width Layout & Compact Grid](#3-full-width-layout--compact-grid)
4. [Glassmorphism Cards with Hover Effects](#4-glassmorphism-cards-with-hover-effects)
5. [Animated Stats Cards](#5-animated-stats-cards)
6. [Floating Action Button (FAB)](#6-floating-action-button-fab)
7. [Loading Skeleton & Shimmer Effect](#7-loading-skeleton--shimmer-effect)
8. [Responsive Spacing & Padding](#8-responsive-spacing--padding)
9. [Micro-interactions on Buttons](#9-micro-interactions-on-buttons)
10. [Workflow Integration (Input → Send → Review → View)](#10-workflow-integration-input--send--review--view)
11. [Recommended Libraries](#11-recommended-libraries-opsional-tapi-ringan)

---

## 1. Animated Background Particles

Partikel yang bergerak **naik** secara halus di latar belakang untuk memberikan kedalaman (depth) dan mengisi ruang kosong secara dinamis — mode "rising embers/fireflies":

- Partikel lahir dari bawah layar, naik perlahan dengan kecepatan/ukuran/opacity acak, sedikit goyang sinusoidal (seperti gelembung/kunang-kunang), lalu memudar di atas.
- Ukuran besar = glow lembut warna accent (biru/ungu, ikut variabel tema), sesekali ada 1–2 partikel "spark" yang lebih terang.
- Parallax tipis mengikuti mouse (layer jauh bergerak lebih lambat) — kesan kedalaman.
- Tetap hemat: pause saat tab tidak aktif (`visibilitychange`), hormat `prefers-reduced-motion`, jumlah partikel menyesuaikan luas layar.

Implementasi di app memakai `<canvas>` (lebih hemat DOM daripada puluhan elemen div). Versi CSS-only sebagai alternatif:

```css
.particle {
  position: absolute;
  width: 6px; height: 6px;
  background: rgba(99, 102, 241, 0.15);
  border-radius: 50%;
  bottom: -20px;
  animation: float-up linear infinite;
}

@keyframes float-up {
  0%   { transform: translateY(0) translateX(0) rotate(0deg); opacity: 0; }
  10%  { opacity: 1; }
  90%  { opacity: 1; }
  100% { transform: translateY(-110vh) translateX(20px) rotate(360deg); opacity: 0; }
}
```

---

## 2. Gradient Mesh Background

Latar belakang hidup dengan beberapa radial-gradient yang bergeser sangat pelan (aurora drift ±70 detik, hue-rotate tipis):

```css
body::before {
  background:
    radial-gradient(at 0% 0%,   rgba(99, 102, 241, 0.15) 0px, transparent 50%),
    radial-gradient(at 100% 0%, rgba(168, 85, 247, 0.10) 0px, transparent 50%),
    radial-gradient(at 50% 120%, rgba(59, 130, 246, 0.10) 0px, transparent 50%);
  animation: aurora 70s ease-in-out infinite;
}
@keyframes aurora {
  0%,100% { transform: none scale(1);            filter: hue-rotate(0deg); }
  33%     { transform: translate(1.5%,-1%) scale(1.05); filter: hue-rotate(12deg); }
  66%     { transform: translate(-1.5%,1%) scale(1.03); filter: hue-rotate(-10deg); }
}
```

---

## 3. Full-Width Layout & Compact Grid

Memanfaatkan ruang layar secara optimal agar tidak terlihat "kosong" di layar besar. **Jangan hardcode jumlah kolom** — biarkan `auto-fill` menghitung:

```css
.wrap  { max-width: min(1700px, 95vw); margin: 0 auto; }
.groups { display: grid; grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); gap: 12px; }
```

Hasil: ~6 kolom di 1920px, 5 di 1600px, 4 di laptop, 1 di HP — otomatis, tanpa media query.

---

## 4. Glassmorphism Cards with Hover Effects

Kartu API key dengan efek kaca, accent stripe warna provider, hover lift, dan **aksi overlay** agar kartu pendek/kompak:

```css
.kcard {
  background: var(--glass);
  backdrop-filter: blur(12px) saturate(140%);
  border: 1px solid var(--glass-brd);
  border-radius: 10px;
  transition: transform .22s ease, box-shadow .22s ease;
}
.kcard:hover { transform: translateY(-3px); box-shadow: var(--shm); }

/* Aksi tidak mereservasi tempat: overlay slide-up saat hover (desktop) */
@media (hover: hover) and (pointer: fine) {
  .kcard .kactions {
    position: absolute; left: 0; right: 0; bottom: 0;
    transform: translateY(102%); opacity: 0;
    transition: transform .28s cubic-bezier(.2,.9,.3,1.08), opacity .2s;
  }
  .kcard:hover .kactions { transform: none; opacity: 1; }
}
```

---

## 5. Animated Stats Cards

Statistik (total, valid, gagal) dengan hover lift dan angka yang ber-count-up saat berubah (via `requestAnimationFrame` easing).

---

## 6. Floating Action Button (FAB)

Tombol `+` di pojok kanan bawah dengan gradient brand, scale + glow saat hover, dan rotasi 45° saat drawer terbuka:

```css
.fab {
  position: fixed; bottom: 24px; right: 24px;
  width: 54px; height: 54px; border-radius: 50%;
  background: linear-gradient(135deg, #4F6EF7, #8B5CF6);
  box-shadow: 0 8px 24px rgba(79,110,247,.45);
  transition: transform .3s cubic-bezier(.34,1.56,.64,1);
}
.fab:hover { transform: scale(1.08); }
body.drawer-open .fab { transform: rotate(45deg); }
```

---

## 7. Loading Skeleton & Shimmer Effect

Feedback visual saat key sedang dites — kilau menyapu kartu:

```css
.kcard.testing::after {
  content: ''; position: absolute; inset: 0;
  background: linear-gradient(100deg, transparent 30%, rgba(79,110,247,.22) 50%, transparent 70%);
  background-size: 250% 100%;
  animation: shimmer 1.15s linear infinite;
}
@keyframes shimmer { from { background-position: 200% 0; } to { background-position: -60% 0; } }
```

---

## 8. Responsive Spacing & Padding

- Grid `auto-fill minmax(250px, 1fr)` menangani semua breakpoint tanpa media query khusus kolom.
- Drawer input berubah jadi bottom sheet di ≤640px.
- FAB dan padding container menyesuaikan di mobile.

---

## 9. Micro-interactions on Buttons

- Ikon copy morph jadi checkmark ✔ selama ~1,6 detik + kartu terangkat.
- Tombol utama lift saat hover (`translateY(-1px)` + shadow menebal).
- Hover ikon gear ⚙️ berputar.

---

## 10. Workflow Integration (Input → Send → Review → View)

### A. Input (Slide-over Drawer)
Drawer dari sisi kanan (bottom sheet di mobile) via FAB / tombol Tambah; background `scale(.985)` + blur saat terbuka. `Ctrl+K` membuka command palette.

### B. Send (Shimmer & Progress)
Kartu yang sedang dites diberi shimmer; batch test menampilkan progress bar tipis di atas layar.

### C. Review (Rich Toasts)
- **Sukses:** toast dengan SVG checkmark yang menggambar dirinya sendiri (`stroke-dashoffset`).
- **Gagal:** toast bergetar (`shake 0.4s`) dengan aksen merah.

### D. View (FLIP Animation & View Transitions)
- FLIP saat filter/sort/hapus/drag-reorder: kartu meluncur ke posisi barunya.
- View Transitions API: kartu "membesar" jadi modal detail (shared element).

---

## 11. Recommended Libraries (Opsional tapi Ringan)

1. **Lottie Web** — animasi ilustrasi JSON (dipakai untuk empty state kunci melayang):
   ```html
   <script src="https://cdnjs.cloudflare.com/ajax/libs/bodymovin/5.12.2/lottie_light.min.js" defer></script>
   ```
2. **GSAP** — opsional untuk orkestrasi timeline kompleks (saat ini belum diperlukan; WAAPI mencukupi).
3. **CSS Variables** — dipakai untuk tema light/dark dan accent per provider:
   ```css
   :root { --accent: #4F6EF7; --glass: rgba(255,255,255,.74); }
   [data-theme="dark"] { --glass: rgba(22,24,36,.62); }
   ```

---

*Dibuat untuk: `maulinul/keyapi`*
