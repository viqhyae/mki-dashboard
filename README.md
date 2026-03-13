

# Panduan Refactoring: Pemisahan Komponen dan Integrasi Laravel Inertia

Dokumen ini berisi panduan untuk merapikan struktur kode dari file `app.docx` (atau `app.jsx`) yang sebelumnya menggabungkan seluruh logika autentikasi, manajemen brand, produk, kategori, hingga peta dalam satu file. 

Tujuan utama dari refactoring ini adalah meningkatkan *maintainability* dan memudahkan pengelolaan kode ke depannya.

## 1. Struktur Folder Baru
Silakan buat struktur folder berikut di dalam `resources/js/` untuk memisahkan tanggung jawab setiap file:

* **`Components/`**: Untuk komponen UI kecil yang digunakan berulang kali (contoh: `Sidebar`, `Header`, `Button`).
* **`Pages/`**: Untuk tampilan halaman utama aplikasi (contoh: `Dashboard`, `BrandManagement`).
* **`Constants/`**: Untuk menyimpan data statis agar tidak memenuhi file logika utama.

## 2. Strategi Pemisahan Kode (Code Splitting)

Berikut adalah bagian-bagian yang harus dipindahkan dari file utama ke file komponen masing-masing:

### A. Data Statis
**Lokasi Baru:** `resources/js/Constants/index.js`  
Cari dan pindahkan variabel berikut:
* `const INITIAL_CATEGORY_DATA`
* `const PRODUCT_SPEC_SCHEMA`

### B. Komponen Sidebar
**Lokasi Baru:** `resources/js/Components/Sidebar.jsx`  
Pindahkan blok kode yang merender menu navigasi (ikon `LayoutDashboard`, `Building2`, `Package`, dll).  
* **Ciri:** Bagian yang mengelola state `activeTab` dan `setActiveTab`.

### C. Komponen Modal
**Lokasi Baru:** `resources/js/Components/Modals/`  
Pindahkan kode `createPortal` yang menangani konfirmasi hapus atau tambah data.  
* **Ciri:** Bagian yang menggunakan `confirmObj` dan ikon `AlertCircle`.

### D. Halaman Utama
**Lokasi Baru:** `resources/js/Pages/Dashboard.jsx`  
Gunakan file ini sebagai *entry point* atau "pengatur lalu lintas" yang memanggil `Sidebar` dan merender konten berdasarkan tab yang dipilih.

---

## 3. Integrasi ke Laravel (Inertia.js)

Untuk memastikan data tersimpan secara permanen di database (bukan sekadar `useState`), kita akan menggunakan form helper dari Inertia.

### Contoh Implementasi `useForm` (AddBrandModal.jsx):

```javascript
import { useForm } from '@inertiajs/react';

const { data, setData, post, processing } = useForm({
    name: '',
    brand_code: '',
    owner_name: '',
    description: '',
});

const submit = (e) => {
    e.preventDefault();
    // Mengirim data ke BrandController di Laravel
    post(route('brands.store')); 
};

```

## Langkah Selanjutnya

1. **Refactor Sidebar**: Pisahkan komponen Sidebar terlebih dahulu dan pastikan muncul dengan benar di dashboard.
2. **Manajemen Brand**: Buat file `BrandManagement.jsx` khusus untuk menampilkan tabel brand yang datanya ditarik langsung dari database melalui Controller Laravel.

