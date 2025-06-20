-----

# Aplikasi Dashboard Manajemen Produk
Aplikasi web Next.js dengan Supabase sebagai backend untuk dashboard manajemen produk.

## Fitur
  * Autentikasi Pengguna (Sign-in/Logout)
  * Peran Pengguna (Admin & User)
  * Manajemen Produk (CRUD) khusus Admin
  * Tampilan Daftar Produk untuk semua pengguna

## Teknologi
  * Next.js
  * TypeScript
  * Supabase
  * Tailwind CSS

-----

## Requirements
  * Node.js (v18+)
  * npm
  * Akun Supabase

-----
## Instalasi & Konfigurasi
1.  **Clone Repositori**
    ```bash
    git clone https://github.com/tuungg/UAS-CSP.git
    cd UAS-CSP
    ```

2.  **Install Dependencies**
    ```bash
    npm install
    ```

3.  **Konfigurasi Supabase**
      * Buat proyek baru di Supabase.
      * Jalankan query berikut di **SQL Editor**:
        ```sql
        -- Membuat tabel products
        CREATE TABLE public.products (
          id BIGINT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
          nama_produk TEXT NOT NULL,
          harga_satuan BIGINT NOT NULL,
          quantity INT NOT NULL,
          created_at TIMESTAMPTZ DEFAULT NOW()
        );

        -- Membuat fungsi untuk cek role
        create or replace function get_my_role()
        returns text
        language sql
        stable
        as $$
          select coalesce(auth.jwt() -> 'app_metadata' ->> 'role', 'user')::text
        $$;
        ```

      * Aktifkan **Row Level Security (RLS)** pada tabel `products`.
      * Buat **dua policy** berikut untuk tabel `products`:
        1.  **Policy untuk Read (semua user):** Gunakan template "Enable read access to everyone".
        2.  **Policy untuk Admin (semua aksi):**
              * **Policy Name**: `Admin can manage products`
              * **Allowed operation**: `ALL`
              * **`USING` expression**: `true`
              * **`WITH CHECK` expression**: `get_my_role() = 'admin'`
      * Buat pengguna di menu **Authentication**. Untuk memberikan role admin, jalankan query berikut di **SQL Editor**:
        ```sql
        -- Ganti UID dengan UID user admin Anda
        UPDATE auth.users
        SET raw_app_meta_data = raw_app_meta_data || '{"role": "admin"}'::jsonb
        WHERE id = 'UID-user-admin-anda';
        ```

4.  **Konfigurasi Environment Variables**
      * Buat file `.env.local` di root proyek.
      * Isi dengan kredensial Supabase Anda.
        ```
        NEXT_PUBLIC_SUPABASE_URL=URL_PROYEK_SUPABASE_ANDA
        NEXT_PUBLIC_SUPABASE_ANON_KEY=KUNCI_ANON_PUBLIK_ANDA
        ```

5.  **Jalankan Aplikasi**
      * Restart server development Anda jika sedang berjalan.
    <!-- end list -->
    ```bash
    npm run dev
    ```
