# GIBEI UISI — Rebuild v2

Beranda Next.js berdasarkan desain Stitch GIBEI UISI, dengan layout responsif,
menu mobile yang dapat dioperasikan lewat keyboard, serta font dan logo lokal.
Implementasi UI berada pada branch `feature/beranda-responsive`.

## Branch proyek

| Branch | Isi dan kegunaan |
| --- | --- |
| `main` | Baseline rebuild setelah penggabungan starter v2. |
| `demo/website-v1` | Snapshot seluruh kode demo v1 dari `main` pada commit `ec4f8de`. |
| `feature/beranda-responsive` | Implementasi Beranda responsif untuk direview sebelum digabung ke `main`. |

Ketiganya berada dalam repository yang sama. Branch demo mempertahankan source
v1 saat rebuild dikembangkan di `main`; deployment demo merupakan
konfigurasi terpisah. `demo-project` tetap tersedia sebagai branch historis.
Branch fitur UI belum di-push atau digabung ke `main` oleh implementasi ini.

Untuk menjalankan demo v1 bersamaan dengan rebuild, gunakan checkout/worktree
terpisah beserta dependency dan environment v1. Environment v1 telah disimpan
dalam snapshot lokal yang disebut di bagian pemulihan; jangan salin ke Git.

## Menjalankan proyek

Gunakan Node.js `24.22.1` (lihat `.node-version`) dan pnpm `10.24.0`.

```sh
pnpm install --frozen-lockfile
pnpm dev
```

Buka http://127.0.0.1:3000. Beranda ini belum membutuhkan `.env`, database,
akun admin, CMS, seed, atau layanan eksternal. Statistik, publikasi, agenda dan
mitra ilustrasi diberi label contoh; tombol yang menunggu konten resmi belum aktif.

Script development dan build memakai Webpack karena worker PostCSS Turbopack
gagal dijalankan pada mesin Windows ini. Framework tetap Next.js App Router.
Cache pnpm lokal disimpan di `.pnpm-store/` melalui `.npmrc` dan diabaikan Git.

```sh
pnpm lint
pnpm typecheck
pnpm build
pnpm start
```

## Struktur aplikasi

- `src/app/layout.tsx`: root layout dan metadata.
- `src/app/page.tsx`: susunan bagian Beranda sebagai Server Components.
- `src/components/home/`: satu file per section; `page.tsx` hanya menyusun urutannya.
- `src/content/site-contact.ts`: satu sumber daftar media sosial untuk Kontak dan footer.
- `src/components/site/`: branding, ikon SVG, header/menu dan footer.
- `src/app/globals.css`: token visual dan layout mobile-first 320 px–ultrawide.
- `src/assets/fonts/`: Inter dan Plus Jakarta Sans lokal beserta lisensi OFL.
- `public/images/`: logo hasil ekspor Stitch.
- `tests/browser/beranda.mjs`: regresi responsif, menu, keyboard dan axe.
- `docs/` dan `tasks/`: PRD, rencana rebuild, dan histori v1.

Stack awal: Next.js App Router, React, TypeScript, Tailwind CSS, ESLint.
Payload, dashboard lama, route API lama, library database, dan skrip seed/migrasi
lama telah dikeluarkan dari aplikasi aktif. Library backend dan autentikasi baru
belum dipilih.

## Menghubungkan Supabase

Gunakan environment yang terpisah untuk development dan jangan mengarahkan aplikasi
rebuild ke data produksi lama.

1. Buat proyek baru di [Supabase](https://supabase.com/dashboard).
2. Salin Project URL dan publishable key (atau anon key) dari **Project Settings >
   API**.
3. Buat `.env.local` dari `.env.example`:

   ```sh
   cp .env.example .env.local
   ```

4. Isi `NEXT_PUBLIC_SUPABASE_URL` dan
   `NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY` pada `.env.local`.
5. Jangan pernah menyimpan `service_role` key atau secret database pada variabel
   `NEXT_PUBLIC_*`.

Client aman untuk akses publik tersedia di
`src/lib/supabase/client.ts`. Fungsi tersebut sengaja tidak membaca credential saat
build; variabel wajib baru diverifikasi ketika client benar-benar dibuat.

## Acuan pekerjaan

- [PRD v1.1](docs/PRD-v1.1.md)
- [Keputusan rebuild](docs/rebuild-v2.md)
- [Kanban rebuild](https://github.com/users/silkiy/projects/5/views/6)
- [Rincian task RB](tasks/github-project-5/rebuild-v2/README.md)

Dokumentasi dan hasil tes v1 merupakan histori, bukan bukti kelulusan v2.
Dashboard, role Admin/Media, alur editorial, dan halaman publik lain akan dibangun berikutnya.

Beranda sudah menyertakan section Kontak (`#kontak`) dengan empat nama kanal
media sosial yang diberikan pemilik dan keterangan "Tautan resmi akan
dikonfirmasi." URL eksternal belum diaktifkan. Rincian delapan file section ada
di [kontrak UI](docs/technical/beranda-ui.md#kepemilikan-file-section).

Section utama mempunyai tinggi minimal satu viewport di bawah header; konten
yang panjang tetap memanjang alami. Klik menu menempatkan section tujuan tepat
di bawah header sehingga potongan section tetangga tidak ikut terlihat setelah
perpindahan selesai. Mitra dan footer tetap mengikuti panjang isinya.

## Verifikasi UI

Kontrak UI: [Beranda v2](docs/technical/beranda-ui.md).
Hasil runtime dan batas verifikasi: [laporan Beranda](docs/reports/beranda-ui.md).

Jalankan `pnpm test:ui` dengan server aktif. Skrip memakai Playwright dan
`@axe-core/playwright` yang tersedia pada lingkungan tool, tanpa menambah
dependency aplikasi. Jika modul tersedia di luar project, arahkan
`UI_PLAYWRIGHT_MODULE` dan `UI_AXE_MODULE` ke berkas entry modul tersebut.
`UI_BASE_URL` opsional (default `http://127.0.0.1:3000`), dan
`UI_BROWSER_CHANNEL` default `chrome`. Bukti/screenshot ditulis ke
`artifacts/beranda-ui/` yang diabaikan Git. Contoh perintah lokal ada pada laporan.

## Pemulihan implementasi lama

Source v1 tersimpan dalam Git pada commit
`ec4f8dee1fc6c5754b02b2c56fae5fde502d1494` (baseline `main` saat setup).
Snapshot lokal sebelum reset juga disimpan di
`artifacts/rebuild-baseline-20261116/`, termasuk konfigurasi privat dan perubahan
`next-env.d.ts` sebelumnya. Folder ini diabaikan Git dan tidak boleh dipublikasikan.
Environment lama dipindahkan ke snapshot agar tidak dimuat oleh starter baru.

Database, media, dan backup lama tidak dihapus. Jangan menghubungkan rebuild ke
database/media produksi sebelum isolasi target baru diputuskan di RB-001/RB-006.
Belum ada deployment produksi.
