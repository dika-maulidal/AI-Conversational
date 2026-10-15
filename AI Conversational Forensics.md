### Rencana Judul : **"Analisis Komparatif Forensik AI Konversasional Menggunakan Metode Memory Forensics dan Browser Artefak Analysis Lintas Peramban Web"** / "**Analisis Komparatif Artefak AI Konversasional Berbasis Memory Forensics dan Browser Artefak: Studi Kasus ChatGPT, Gemini, Claude, DeepSeek, dan Copilot pada Peramban Web**"
#### **Urgensi Penelitian (_Research Significance & Urgency_)**

- **Eskalasi Penyalahgunaan Generative AI dalam Kejahatan Siber:** Pesatnya adopsi aplikasi web _Generative AI_ berbasis _Large Language Models_ (LLMs) seperti ChatGPT, Gemini, Claude, DeepSeek, dan Copilot membawa ancaman baru di bidang keamanan siber. Pelaku kejahatan siber secara aktif memanfaatkan antarmuka AI web untuk merancang email rekayasa sosial (_phishing_), menyusun skrip _malware_, hingga memanipulasi dokumen untuk aktivitas penipuan.
- **Ancaman Teknik Anti-Forensik (**Anti-Forensics Threats**):** Dalam melakukan aksinya, pelaku kejahatan siber kerap menggunakan teknik penyembunyian jejak (_anti-forensics_), seperti menekan tombol **"Delete Chat"** pada layar web, menggunakan **Temporary/Ephemeral Chat**, berinteraksi via **Incognito/Private Browsing Mode**, atau berinteraksi dalam **Guest/Non-Login Mode**.
- **Tantangan Pembuktian & Identifikasi Pengguna (**Investigative Challenges**):** Penyidik forensik digital menghadapi tantangan teknis besar dalam membuktikan _mens rea_ (niat kejahatan) dan merekonstruksi barang bukti. Diperlukan metodologi yang teruji ilmiah untuk mengekstrak dan memulihkan artefak percakapan (teks, unggahan PDF/Gambar), token sesi, stempel waktu (_timestamps_), serta atribusi identitas pengguna (_Email, User ID, Device ID_) bahkan setelah tindakan penghapusan visual dilakukan.

**Tabel 1. Matriks Penyelarasan Penelitian Terdahulu (**State of the Art**)

| No  | Peneliti & Tahun                | Judul Publikasi & Jurnal                                                                                                                            | Objek & Metodologi Penelitian                                                                                                                                        | Temuan Utama Artefak                                                                                                                                                   | Celah Penelitian (_Research Gap_)                                                                                               |
| --- | ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| 1   | **Cho et al. (2025)**           | _Conversational AI Forensics: A Case Study on ChatGPT, Gemini, Copilot, and Claude_ (Elsevier FSI: Digital Investigation)                           | **Objek:** ChatGPT 4o, Gemini Advanced, Copilot, Claude 3 Opus. <br>**Metode:** Analisis arsitektur data (REST API vs RPC), parsing JSON, dan ekstraksi log browser. | ChatGPT & Claude menggunakan REST API; Gemini menggunakan RPC `batchexecute`. Berhasil mengekstrak pesan, metadata unggahan PDF/gambar, serta URL hasil pencarian web. | Belum menguji ketahanan memori RAM saat dilakukan aksi _UI Delete Chat_ dan mode privat (_Incognito_) di peramban yang berbeda. |
| 2   | **Kankanamge et al. (2025)**    | _Digital Forensic Investigation of the ChatGPT Windows Application_ (Preprint Elsevier)                                                             | **Objek:** Aplikasi ChatGPT Desktop Windows. <br>**Metode:** Live RAM Capture (Magnet RAM Capture), Disk Imaging (FTK Imager), dan Autopsy.                          | Teks percakapan, _prompt phishing_, token sesi, dan stempel waktu tetap dapat dipulihkan dari RAM dan registri sistem bahkan setelah aplikasi dihapus.                 | Berfokus khusus pada aplikasi _desktop native_ Windows, tidak menganalisis peramban web (_web browsers_).                       |
| 3   | **Studi V8 Heap Memory (2025)** | _Forensic Analysis from Generative AI Web Application Memory: A ChatGPT Case Study_                                                                 | **Objek:** ChatGPT Web Application pada peramban web. <br>**Metode:** Analisis _heap snapshot_ V8 JavaScript Engine via Browser Debugging Protocol.                  | Teks percakapan yang dihapus via UI tetap merekam graf referensi di V8 Heap RAM selama proses peramban (`chrome.exe`) berjalan aktif.                                  | Hanya menguji 1 platform (ChatGPT) pada 1 peramban, belum melakukan studi komparatif lintas peramban (_cross-browser_).         |
| 4   | **Tyagi et al. (2025)**         | _Forensic Analysis and Privacy Implications of LLM Mobile Apps: A Case Study of ChatGPT, Copilot, and Gemini_ (Elsevier FSI: Digital Investigation) | **Objek:** Aplikasi Mobile LLM (Android & iOS). <br>**Metode:** Physical Acquisition (Magnet AXIOM, GrayKey) pada perangkat _rooted/jailbroken_.                     | ChatGPT & Copilot menyimpan obrolan _plaintext_ secara lokal di database SQLite; Gemini menyimpan seluruh obrolan di _cloud_ (Google Takeout).                         | Dibatasi pada ekosistem _mobile apps_ (Android/iOS), tidak mencakup _desktop web browsing_.                                     |
| 5   | **Choi et al. (2023)**          | _Chracer: Memory Analysis of Chromium-Based Browsers_ (Elsevier FSI: Digital Investigation)                                                         | **Objek:** Google Chrome, Microsoft Edge, Brave. <br>**Metode:** Scanning tata letak objek memori V8/Chromium (_object layout carving_).                             | Rekonstruksi struktur tab, URL yang dikunjungi, dan sertifikat SSL/TLS dapat dipulihkan dari V8 Heap RAM, termasuk pada mode _Incognito_.                              | Berfokus pada aktivitas perambanan umum, belum menguji aplikasi web _Generative AI_.                                            |
| 6   | **Iqbal et al. (2022)**         | _Forensic Investigation of Google Meet for Memory and Browser Artifacts_ (Elsevier FSI: Digital Investigation)                                      | **Objek:** Google Meet Web Client. <br>**Metode:** Komparasi 3 peramban (Chrome, Edge, Firefox) pada RAM & Disk Storage.                                             | Menemukan bahwa memori V8 Heap Chromium & Gecko Firefox menyimpan string obrolan dan email. Format SQLite History Chromium dan Firefox berbeda.                        | Pengujian dilakukan pada aplikasi konferensi video web, bukan _Generative AI_ (LLM).                                            |
| 7   | **Studi DeepSeek (2025)**       | _Uncovering Digital Traces of DeepSeek: Cross-Platform Mobile and Network Forensics_                                                                | **Objek:** Aplikasi DeepSeek & Network Traffic. <br>**Metode:** Mobile Forensics (Magnet AXIOM, Frida) & Network Forensics.                                          | Mengekstrak log percakapan, metadata identitas pengguna, dan pola komunikasi paket jaringan pada platform DeepSeek.                                                    | Belum menganalisis artefak peramban web (_web-based interface_) dan memori RAM peramban.                                        |
#### **Parameter Uji:**

- **Platform AI :** Gemini, Claude, ChatGPT, DeepSeek, Copilot.
- **Peramban (Browser):** Google Chrome, Mozilla Firefox, Microsoft Edge.
### **Rangkaian Prompt & Interaksi Pengguna**

#### **Langkah 1: Teks Prompt Phising**

> _Input Pengguna:_ `"Buatkan draf teks email phising singkat berkedok reset password korporat yang meyakinkan."`
> 
> - **Variabel yang Diuji:** `Prompt User Input`, `AI Response`, `Timestamps`, Atribusi Pengguna & Sesi (`Email`, `Plan`, `Sessions`, `User ID`). 

#### **Langkah 2: Lampiran Dokumen PDF & Perintah Ringkas**

> _Input Pengguna:_ _(Unggah berkas `kebijakan_perusahaan.pdf`)_ `"Tolong ringkas isi dokumen PDF yang saya unggah ini."`
> 
> - **Variabel yang Diuji:** `Upload File (PDF)`, `Metadata File` (`file_name`, `mime_type`, `asset_pointer`, `extracted_content`).

#### **Langkah 3: Pemicu Pencarian Web (_Web Search / Citation_)**

> _Input Pengguna:_ `"Coba cari berita atau informasi terbaru minggu ini mengenai kasus insiden kebocoran data perusahaan keamanan siber global."`
> 
> - **Variabel yang Diuji:** `Web Search Provenance / Citation Links` (memicu objek `metadata_list` / `_cite_metadata` di memori atau payload respons).

#### **Langkah 4: Lampiran Gambar & Instruksi Modifikasi (_Image Generation & Editing_)**

> _Input Pengguna:_ _(Unggah berkas `id_card_dummy.jpg`)_ `"Tolong edit gambar kartu pengenal ini, ganti nama di dalamnya menjadi 'Ahmad Fauzi' dan ubah jabatannya menjadi 'Security Engineer'."`
> 
> - **Variabel yang Diuji:** `Upload File (JPG)`, `Metadata File` (`width`, `height`), serta `Generate Image Traces` (jejak biner atau URL sementara hasil pemrosesan gambar/aset visual oleh AI).

### **Matriks Variabel Target Forensik Conversational AI**

| No    | Kategori Variabel Forensik                                    | Nama Variabel / Parameter | Deskripsi & Format Data                                                                                                        | Signifikansi Forensik (Mengapa Penting?)                                                                    |
| ----- | ------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------- |
| **1** | **Atribusi Pengguna & Sesi** _(User Identity & Session)_      | `Email`                   | Alamat email aktif pendaftaran akun.                                                                                           | Mengaitkan kepemilikan akun secara langsung dengan identitas subjek hukum.                                  |
|       |                                                               | `Plan`                    | Status langganan (_Free / Plus / Team / Enterprise / Pro_).                                                                    | Mengetahui tingkat kapabilitas sistem, fitur lanjutan, dan batasan penyimpanan data pengguna.               |
|       |                                                               | `Sessions`                | Session ID / `authsess_...` / NextAuth cookies.                                                                                | Membuktikan validitas sesi aktif di dalam memori atau penyimpanan peramban saat insiden terjadi.            |
| **2** | **Metadata & Status Percakapan** _(Session Metadata & State)_ | `Conversations ID`        | Unique Identifier (UUID tunggal percakapan).                                                                                   | Menjadi kunci utama untuk melacak riwayat log percakapan di dalam struktur basis data atau cache memori.    |
|       |                                                               | `Mode (Archived)`         | Flag status arsip (`is_archived`: `true`/`false`).                                                                             | Mengetahui apakah percakapan telah disembunyikan/diarsipkan oleh pengguna.                                  |
|       |                                                               | `Mode (Temporary Chat)`   | Flag obrolan sementara (`is_temporary_chat`: `true`/`false`).                                                                  | Indikator upaya anti-forensik ringan (chat tidak masuk riwayat jangka panjang).                             |
|       |                                                               | `Mode (Model)`            | Pilihan model AI/slug (misal: `gpt-4o`, `gemini-1.5-pro`, dll).                                                                | Mengidentifikasi varian mesin kecerdasan buatan yang merespons sesi tersebut.                               |
| **3** | **Konten Utama Obrolan** _(Chat Payloads)_                    | `Prompt User Input`       | Teks instruksi/masukan dari role `user`.                                                                                       | Membuktikan niat (_intent_), perintah, atau skenario uji yang dimasukkan (misal: draf phising).             |
|       |                                                               | `AI Response`             | Teks jawaban dari role `assistant`.                                                                                            | Menunjukkan keluaran atau respons yang dihasilkan oleh model AI terhadap perintah pengguna.                 |
| **4** | **Lampiran & Berkas Multimedia** _(Multimedia Attachments)_   | `Upload File (PDF & JPG)` | Keberadaan berkas masukan (_input_) atau keluaran (_output_).                                                                  | Menemukan jejak fisik/biner dokumen atau gambar yang dilampirkan selama investigasi.                        |
|       |                                                               | `Metadata File`           | Atribut: `file_name`, `file_size`, `mime_type`, `asset_pointer`, `width`, `height`, serta draf teks PDF (`extracted_content`). | Memberikan detail teknis karakteristik file asli dan hasil _parsing_ teks dokumen.                          |
| **5** | **Analisis Temporal** _(Timestamps)_                          | `Timestamps`              | `create_time`, `update_time`, `pwd_auth_time`, timestamp draf.                                                                 | Menyusun kronologi waktu kejadian (_timeline analysis_) secara presisi.                                     |
| **6** | **Atribusi Internal & Perangkat** _(Device & Unique ID)_      | `User ID / Account ID`    | ID unik internal (contoh: `user-eKOpehUBZw...`).                                                                               | Kunci permanen di database penyedia AI yang tidak berubah meskipun email diganti/dianonimkan.               |
|       |                                                               | `Device ID & Client ID`   | Parameter unik peramban (contoh: `eca4151a-...`).                                                                              | Terikat pada header HTTP/OAuth untuk membuktikan instans peramban/laptop spesifik yang digunakan.           |
| **7** | **Jejak Jaringan & Proses** _(Network & Process)_             | `IP Socket & PID`         | Alamat IP tujuan (`172.64.155.209:443`) & PID proses peramban (`chrome.exe`).                                                  | Diperoleh via Volatility (`pslist`/`netscan`) untuk membuktikan lalu lintas data aktif saat RAM Capture.    |
| **8** | **Provenance & Fitur Khusus** _(Search)_                      | `Web Search Provenance`   | Metadata tautan referensi (`metadata_list` / `_cite_metadata`).                                                                | Melacak sumber URL/link eksternal yang dikunjungi AI saat melakukan pencarian web.                          |
| **9** | **Provenance & Fitur Khusus** _(Generation)_                  | `Generate Image Traces`   | Metadata atau URL sementara berkas gambar hasil _generation_ / _editing_.                                                      | Membuktikan riwayat atau proses pembuatan serta penyuntingan aset visual secara sintetis di dalam platform. |
### **Tabel Matriks Skenario Terkontrol (_Controlled Test Matrix_)**

| **Kode Skenario** | **Nama Skenario**                | **Fokus Variabel Uji**                                                     | **Modalitas Input / Skenario Interaksi**             | **Mode / Kondisi Khusus**                          | **Status Autentikasi**     |
| ----------------- | -------------------------------- | -------------------------------------------------------------------------- | ---------------------------------------------------- | -------------------------------------------------- | -------------------------- |
| **Track 1**       | **Skenario Standar (Baseline)**  | Teks Phising, Upload PDF, Web Search, & Image Edit (1 Sesi Tunggal)        | Teks, PDF, Gambar, Perintah Web Search & Edit Gambar | Sesi Normal / Standar                              | Logged-In (Akun Terdaftar) |
| **Track 2**       | **Uji Anti-Forensik & Deletion** | Meneliti sisa jejak memori dari fitur _UI Delete Chat_ vs _Temporary Chat_ | Teks & Gambar (Sama seperti Track 1)                 | Penghapusan Sesi / Obrolan Sementara               | Logged-In                  |
| **Track 3**       | **Perbandingan Mode Peramban**   | Membandingkan persistensi artefak RAM & Cache antar peramban               | Teks, PDF, & Gambar                                  | Perbandingan **Normal Mode** vs **Incognito Mode** | Logged-In                  |
| **Track 4**       | **Status Autentikasi**           | Meneliti perbedaan jejak forensik antara akun resmi dengan mode tamu       | Teks Biasa & Lampiran Dasar                          | Perbandingan **Logged-In** vs **Guest Mode**       | Logged-In vs Guest         |
### **Tabel Matriks Komparasi Keberhasilan Temuan Artefak Forensik per Platform AI (Contoh)**

| Platform AI  | Peramban (Browser) | Atribusi Sesi | Metadata Chat | Konten Chat | Lampiran PDF |   Temporal   | Jejak Perangkat | RAM / PID | Web Search |  Image Gen   |
| :----------- | :----------------- | :-----------: | :-----------: | :---------: | :----------: | :----------: | :-------------: | :-------: | :--------: | :----------: |
| **ChatGPT**  | Google Chrome      |       ✓       |       ✓       |      ✓      |      ✓       |      ✓       |        ✓        |     ✓     |     ✓      |      ✓       |
| **ChatGPT**  | Microsoft Edge     |       ✓       |       ✓       |      ✓      |      ✓       |      ✓       |        ✓        |     ✓     |     ✓      |      ✓       |
| **ChatGPT**  | Mozilla Firefox    |       ✓       |       ✓       |      ✓      |      ✓       |      ✓       |        ✓        |     ✓     |     ✓      |      ✓       |
| **Gemini**   | Google Chrome      |       ✓       |       ✓       |      ✓      |      ✓       |      ✓       |        ✓        |     ✓     |     ✓      |      ✓       |
| **Gemini**   | Microsoft Edge     |       ✓       |       ✓       |      ✓      |      ✓       |      ✓       |        ✓        |     ✓     |     ✓      |      ✓       |
| **Gemini**   | Mozilla Firefox    |       ✓       |       ✓       |      ✓      |      ✓       |      ✓       |        ✓        |     ✓     |     ✓      |      ✓       |
| **Claude**   | Google Chrome      |       ✓       |       ✓       |      ✓      |      ✓       |      ✓       |        ✓        |     ✓     |     ✓      |      ✓       |
| **Claude**   | Microsoft Edge     |       ✓       |       ✓       |      ✓      |      ✓       |      ✓       |        ✓        |     ✓     |     ✓      |      ✓       |
| **Claude**   | Mozilla Firefox    |       ✓       |       ✓       |      ✓      |      ✓       |      ✓       |        ✓        |     ✓     |     ✓      |      ✓       |
| **DeepSeek** | Google Chrome      |       ✓       |       ✓       |      ✓      |      ✓       |      ✓       |        ✓        |     ✓     |     ✓      |      ✓       |
| **DeepSeek** | Microsoft Edge     |       ✓       |       ✓       |      ✓      |      ✓       |      ✓       |        ✓        |     ✓     |     ✓      |      ✓       |
| **DeepSeek** | Mozilla Firefox    |       ✓       |       ✓       |      ✓      |      ✓       | ✗ (Sebagian) |        ✓        |     ✓     |     ✗      | ✗ (Terbatas) |
| **Copilot**  | Google Chrome      |       ✓       |       ✓       |      ✓      |      ✓       |      ✓       |        ✓        |     ✓     |     ✓      |      ✓       |
| **Copilot**  | Microsoft Edge     |       ✓       |       ✓       |      ✓      |      ✓       |      ✓       |        ✓        |     ✓     |     ✓      |      ✓       |
| **Copilot**  | Mozilla Firefox    |       ✓       |       ✓       |      ✓      |      ✓       |      ✓       |        ✓        |     ✓     |     ✓      |      ✓       |
## Metodologi dan Alur Skenario Pengujian

![Alur Skenario Pengujian Forensik](diagram/skenario-rev.png)
## Ringkasan Visual Temuan Artefak

![Grafik Komparasi Temuan Artefak](diagram/chart.png)
#### **Google Chrome (_Chromium Engine_)**

| Artefak Target          | Path Direktori Sistem (Windows)                                         | Format File / Database                |
| ----------------------- | ----------------------------------------------------------------------- | ------------------------------------- |
| **History & Downloads** | `%LocalAppData%\Google\Chrome\User Data\Default\History`                | SQLite Database                       |
| **Cookies**             | `%LocalAppData%\Google\Chrome\User Data\Default\Network\Cookies`        | SQLite Database                       |
| **IndexedDB (LevelDB)** | `%LocalAppData%\Google\Chrome\User Data\Default\IndexedDB\`             | LevelDB (`.log`, `.ldb`)              |
| **Local Storage**       | `%LocalAppData%\Google\Chrome\User Data\Default\Local Storage\leveldb\` | LevelDB                               |
| **Cache Data**          | `%LocalAppData%\Google\Chrome\User Data\Default\Cache\`                 | Blockfile / Custom Cache Format       |
| **Browsing Sessions**   | `%LocalAppData%\Google\Chrome\User Data\Default\Sessions\`              | Session Files (`Session_*`, `Tabs_*`) |
| **Bookmarks**           | `%LocalAppData%\Google\Chrome\User Data\Default\Bookmarks`              | JSON Format                           |
| **Login Data / Email**  | `%LocalAppData%\Google\Chrome\User Data\Default\Login Data`             | Encrypted SQLite Database             |
#### **Microsoft Edge (_Chromium Engine_)**

| Artefak Target                | Path Direktori Sistem (Windows)                                                                                                           | Format File / Database                |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------- |
| **History & Downloads**       | `%LocalAppData%\Microsoft\Edge\User Data\Default\History`                                                                                 | SQLite Database                       |
| **Cookies**                   | `%LocalAppData%\Microsoft\Edge\User Data\Default\Cookies`                                                                                 | SQLite Database                       |
| **IndexedDB & Local Storage** | `%LocalAppData%\Microsoft\Edge\User Data\Default\IndexedDB\` <br>`%LocalAppData%\Microsoft\Edge\User Data\Default\Local Storage\leveldb\` | LevelDB (`.log`, `.ldb`)              |
| **Cache Data**                | `%LocalAppData%\Microsoft\Edge\User Data\Default\Cache\Cache_Data\`                                                                       | JSON & Blockfile Cache                |
| **Browsing Sessions**         | `%LocalAppData%\Microsoft\Edge\User Data\Default\Sessions\`                                                                               | Session Files (`Session_*`, `Tabs_*`) |
| **Web Data / Email**          | `%LocalAppData%\Microsoft\Edge\User Data\Default\Web Data`                                                                                | Encrypted SQLite Database             |
#### **Mozilla Firefox (_Quantum Engine_)**

|Artefak Target|Path Direktori Sistem (Windows)|Format File / Database|
|---|---|---|
|**History & Bookmarks**|`%AppData%\Roaming\Mozilla\Firefox\Profiles\<profile>\places.sqlite`|SQLite Database|
|**Form History**|`%AppData%\Roaming\Mozilla\Firefox\Profiles\<profile>\formhistory.sqlite`|SQLite Database|
|**Cookies**|`%AppData%\Roaming\Mozilla\Firefox\Profiles\<profile>\cookies.sqlite`|SQLite Database|
|**Cache Data**|`%LocalAppData%\Mozilla\Firefox\Profiles\<profile>\cache2\`|Cache2 Structure / Doom Files|
|**Storage Persisten (IndexedDB/Local)**|`%AppData%\Roaming\Mozilla\Firefox\Profiles\<profile>\storage\default\`|SQLite / Custom Storage|
|**Favicons**|`%AppData%\Roaming\Mozilla\Firefox\Profiles\<profile>\favicons.sqlite`|SQLite Database|
