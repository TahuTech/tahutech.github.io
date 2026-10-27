# tahutech.github.io

Source situs profil & blog **Nur Ali Sholikhin**, dibuat dengan [Hugo](https://gohugo.io/)
dan tema [hugo-profile](https://github.com/gurusabarish/hugo-profile).
Situs live: <https://tahutech.github.io>

Setiap push ke branch `main` otomatis di-build dan di-deploy oleh GitHub Actions
(`.github/workflows/hugo.yml`). Tidak perlu build manual atau `deploy.sh`.

## Struktur folder

```
.
├── .github/workflows/hugo.yml   # build & deploy otomatis ke GitHub Pages
├── archetypes/blogs.md          # template front matter untuk post baru
├── content/
│   ├── blogs/                   # semua post blog (.md)
│   └── gallery.md               # halaman gallery (masih draft)
├── layouts/partials/            # override tema (jangan edit themes/)
├── static/                      # file statis: gambar, resume.pdf, dll.
│   └── images/
├── themes/hugo-profile/         # tema (git submodule, jangan diedit)
└── hugo.yaml                    # konfigurasi situs + isi section profil
```

## Menjalankan secara lokal

1. Install **Hugo extended v0.152.2** (versi yang sama dengan yang dipakai GitHub Actions):
   <https://github.com/gohugoio/hugo/releases/tag/v0.152.2>
   Cek dengan `hugo version` (harus ada tulisan `+extended`).
2. Clone repo **beserta submodule tema**:
   ```bash
   git clone --recurse-submodules git@github.com:TahuTech/tahutech.github.io.git
   cd tahutech.github.io
   ```
   Kalau sudah terlanjur clone tanpa `--recurse-submodules`, jalankan:
   ```bash
   git submodule update --init --recursive
   ```
3. Jalankan server lokal (`-D` ikut menampilkan post yang masih draft):
   ```bash
   hugo server -D
   ```
4. Buka <http://localhost:1313>. Halaman otomatis refresh setiap file disimpan.

Untuk mengecek hasil build produksi: `hugo --minify` (hasilnya di folder `public/`,
yang sudah di-ignore oleh git).

## Menambah post baru

1. Buat file post dari template:
   ```bash
   hugo new content blogs/judul-post-saya.md
   ```
   File baru muncul di `content/blogs/judul-post-saya.md`. Nama file menjadi URL:
   `https://tahutech.github.io/blogs/judul-post-saya/`.
2. Buka file tersebut dan isi front matter:
   ```yaml
   ---
   title: 'Judul Post Saya'
   date: '2026-10-27T10:00:00+07:00'
   draft: true            # ubah ke false kalau sudah siap terbit
   author: "Nur Ali Sholikhin"
   description: "Ringkasan 1 kalimat"
   image: "/images/blogs/sampul.jpg"   # opsional, boleh dikosongkan ""
   tags: ["hugo", "tutorial"]
   toc: false             # true untuk menampilkan daftar isi
   ---
   ```
3. Tulis isi post di bawah `---` memakai Markdown.
4. Kalau memakai gambar, simpan di `static/images/blogs/` lalu panggil dengan
   `![keterangan](/images/blogs/nama-file.jpg)`.
5. Cek di lokal dengan `hugo server -D`.
6. Ubah `draft: true` menjadi `draft: false`.
7. Commit dan push:
   ```bash
   git add content/blogs/judul-post-saya.md static/images/blogs/
   git commit -m "Tambah post: Judul Post Saya"
   git push
   ```
8. Tunggu 1-2 menit, lalu cek progresnya di tab **Actions** di GitHub. Post akan muncul
   di `/blogs` dan di "Recent Posts" pada footer.

> Post dengan `draft: true` atau `date` di masa depan **tidak** akan diterbitkan.

## Mengedit section profil

Semua isi halaman utama ada di `hugo.yaml` di bawah `params:`.

| Section       | Kunci di `hugo.yaml`  | Catatan |
|---------------|-----------------------|---------|
| Hero (atas)   | `params.hero`         | `title`, `subtitle`, `content`, `image`, tombol Resume, ikon sosial |
| About         | `params.about`        | `content`, `image`, daftar `skills.items` |
| Experience    | `params.experience`   | daftar `items` → `company` → `jobs` |
| Education     | `params.education`    | daftar `items` |
| Projects      | `params.projects`     | daftar `items` |
| Achievements  | `params.achievements` | daftar `items` |
| Contact       | `params.contact`      | `email` untuk tombol "Mail me" |
| Footer        | `params.footer`       | link sosial media |

- Section bisa disembunyikan dengan `enable: false`. Menunya di navbar ikut hilang.
- Gambar disimpan di `static/images/` dan dipanggil dengan path `/images/nama-file`.
- **Resume**: taruh file di `static/resume.pdf`, lalu ubah `params.hero.button.enable`
  menjadi `true`.
- **Gallery**: isi `content/gallery.md`, ubah `draft` menjadi `false`, lalu aktifkan
  menu Gallery di bagian `Menus` di `hugo.yaml`.
- **Animasi**: section halaman utama memakai efek fade-up (AOS) dari
  `layouts/partials/head/extensions.html`. Animasi otomatis mati untuk pengunjung yang
  mengaktifkan "kurangi gerakan" (reduce motion) di perangkatnya. Untuk mematikan
  animasi sepenuhnya, tambahkan `aos: false` di bawah `params:`.
- Jangan mengedit file di dalam `themes/`. Untuk mengubah tampilan, salin file layout
  yang ingin diubah ke folder `layouts/` dengan path yang sama, lalu edit salinannya.

## Deploy

Deploy berjalan otomatis lewat GitHub Actions setiap ada push ke `main`.
Workflow juga bisa dijalankan manual: tab **Actions** → **Deploy Hugo site to Pages** →
**Run workflow**.

Pengaturan yang dibutuhkan (cukup sekali): **Settings → Pages → Build and deployment →
Source = "GitHub Actions"**.

Kalau deploy gagal, buka tab **Actions** dan lihat log langkah yang berwarna merah.
Penyebab yang sering terjadi:
- Error di `hugo.yaml` (indentasi YAML salah). Coba `hugo` di lokal dulu.
- Submodule tema tidak ter-commit. Jalankan `git submodule status`.

## Update tema (opsional)

Tema dikunci di commit tertentu supaya tampilan tidak berubah tiba-tiba. Untuk update:
```bash
cd themes/hugo-profile
git fetch && git checkout <commit-atau-tag-baru>
cd ../..
hugo server -D          # cek tampilan dulu
git add themes/hugo-profile
git commit -m "Update tema hugo-profile"
```
