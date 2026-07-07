# Data Base Sealer — Red Shift (Web App Mandiri)

Panduan ini untuk membuat aplikasi ini jadi **website beneran** dengan link publik, yang:
- Bisa diakses siapa saja (tidak perlu akun Claude)
- Datanya **sama untuk semua orang** (tersimpan di database, bukan per-perangkat)
- Tetap jalan meskipun akun Claude Anda tidak aktif

Total waktu pengerjaan: **±20–30 menit**, tidak perlu jago coding, cukup ikuti langkah demi langkah.

---

## Yang dibutuhkan

1. Akun **Supabase** (gratis) — untuk database
2. Akun **GitHub** (gratis) — untuk menyimpan kode
3. Akun **Vercel** (gratis) — untuk deploy website

---

## LANGKAH 1 — Buat Database di Supabase

1. Buka https://supabase.com → klik **Start your project** → daftar/login (bisa pakai akun Google).
2. Klik **New Project**.
   - Nama project: bebas, misal `sealer-red-shift`
   - Database Password: buat password, **simpan baik-baik**
   - Region: pilih yang paling dekat (misal Singapore)
   - Klik **Create new project** (tunggu 1-2 menit sampai siap)
3. Setelah project siap, di sidebar kiri klik **SQL Editor** → **New query**.
4. Copy-paste script di bawah ini, lalu klik **Run**:

```sql
create table sealer_records (
  date text primary key,
  data jsonb not null,
  updated_at timestamptz default now()
);

alter table sealer_records enable row level security;

create policy "Allow all read" on sealer_records
  for select using (true);

create policy "Allow all write" on sealer_records
  for insert with check (true);

create policy "Allow all update" on sealer_records
  for update using (true);

create policy "Allow all delete" on sealer_records
  for delete using (true);
```

   > Catatan keamanan: policy di atas mengizinkan **siapa saja yang punya link** untuk baca/tulis/hapus data — cocok untuk tim internal yang saling percaya. Kalau nanti butuh proteksi login, bisa ditambahkan belakangan (tinggal bilang ke saya).

5. Di sidebar kiri, klik **Project Settings** (ikon gerigi) → **API**.
   - Catat **Project URL** (bentuknya `https://xxxxx.supabase.co`)
   - Catat **anon public key** (deretan huruf/angka panjang)

---

## LANGKAH 2 — Siapkan Kode di GitHub

1. Extract file `sealer-web.zip` yang saya kasih ke folder di komputer Anda.
2. Buka https://github.com → daftar/login → klik **New repository**.
   - Nama: `sealer-red-shift-app`
   - Pilih **Public** atau **Private** (bebas)
   - Klik **Create repository**
3. Di halaman repo baru, klik **uploading an existing file** → drag semua isi folder `sealer-web` (kecuali folder `node_modules` kalau ada) ke situ → klik **Commit changes**.

   *(Kalau familiar dengan Git, bisa juga pakai `git init`, `git add .`, `git commit`, `git push` — hasilnya sama saja.)*

---

## LANGKAH 3 — Deploy ke Vercel

1. Buka https://vercel.com → daftar/login (pilih **Continue with GitHub** supaya langsung terhubung).
2. Klik **Add New** → **Project**.
3. Pilih repo `sealer-red-shift-app` yang tadi dibuat → klik **Import**.
4. Di bagian **Environment Variables**, tambahkan 2 baris:
   | Name | Value |
   |---|---|
   | `VITE_SUPABASE_URL` | *(Project URL dari Supabase tadi)* |
   | `VITE_SUPABASE_ANON_KEY` | *(anon public key dari Supabase tadi)* |
5. Klik **Deploy**. Tunggu ±1 menit.
6. Setelah selesai, Vercel kasih link seperti `https://sealer-red-shift-app.vercel.app` — **itu link publik Anda**, siap dibagikan ke siapa saja.

---

## Setelah itu

- Setiap orang yang buka link tersebut akan **melihat & mengisi data yang sama** (real-time, langsung sinkron antar pengguna).
- Kalau nanti mau update kode/tampilan, tinggal ubah file di GitHub → Vercel otomatis deploy ulang.
- Link ini **tidak tergantung akun Claude sama sekali** — jalan sendiri di server Vercel + Supabase.

---

## Kalau Ada Kendala

Kirim pesan error yang muncul (screenshot atau teks) ke saya di chat Claude, saya bantu telusuri.
