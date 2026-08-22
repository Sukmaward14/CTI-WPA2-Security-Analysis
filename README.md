# 🛡️ Threat Intelligence & WPA2-Personal Security Analysis

> **Nama Mahasiswa:** Sukma Wardia Ningsih  
> **NIM:** 105841112723  
> **Mata Kuliah:** Cyber Threat Intelligence (CTI)  
> **Dosen Pengampu:** RUNAL REZKIAWAN, S.Kom., M.T  
> **Judul Proyek:** Threat Intelligence & Vulnerability Assessment on WPA2-Personal Networks: From 4-Way Handshake Capture to MitM Risk Mitigation

---

## 📋 Deskripsi Proyek

Proyek ini menganalisis tingkat keamanan protokol **WPA2-Personal (PSK)** melalui teknik penangkapan paket data (*packet capture*) dan analisis **4-Way Handshake**.

Pengujian dilakukan menggunakan **Kali Linux**, wireless adapter yang mendukung **Monitor Mode**, serta beberapa tools seperti **Aircrack-ng**, **TShark**, dan **Wireshark**.

Tujuan dari proyek ini adalah untuk menganalisis traffic jaringan wireless, memperoleh WPA2 4-Way Handshake, mengidentifikasi ANonce dan SNonce, melakukan pengujian password secara offline pada jaringan uji, serta menganalisis risiko **ARP Spoofing / Man-in-the-Middle (MitM)** dan **KRACK (Key Reinstallation Attack)**.

Hasil analisis kemudian dipetakan menggunakan **MITRE ATT&CK** dan **CVE** untuk mengetahui tingkat risiko serta menentukan rekomendasi mitigasi yang sesuai.

---

## 🛠️ Alat dan Lingkungan Pengujian

Pengujian dilakukan menggunakan perangkat dan software berikut:

- **Operating System:** Kali Linux
- **Wireless Adapter:** TP-Link TL-WN722N v2/v3
- **Chipset:** Realtek RTL8188EUS
- **Wireless Interface:** wlan0
- **Wireless Mode:** Monitor Mode
- **Tools:** Aircrack-ng, airmon-ng, airodump-ng, TShark, Wireshark
- **Wireless Security:** WPA2-Personal (PSK)
- **Channel Pengujian:** Channel 8
- **Protokol yang Dianalisis:** IEEE 802.11, EAPOL dan ARP

---

## 📡 Pengaktifan Monitor Mode

Tahap pertama pengujian adalah mengaktifkan **Monitor Mode** pada wireless adapter.

Monitor Mode memungkinkan wireless adapter untuk menangkap frame IEEE 802.11 yang berada pada channel wireless sehingga traffic jaringan dapat dianalisis.

Pada pengujian ini, wireless interface **wlan0** berhasil diaktifkan dalam Monitor Mode.

Hasil pengecekan menunjukkan:

```text
Interface : wlan0
Mode      : Monitor
Adapter   : TP-Link TL-WN722N v2/v3
Chipset   : Realtek RTL8188EUS
```

Dengan berhasilnya Monitor Mode, wireless adapter dapat digunakan untuk proses packet capture menggunakan Aircrack-ng dan Wireshark.

---

## 🔍 Probe Request & Discovery

Tahap berikutnya adalah melakukan analisis terhadap **Probe Request**.

Probe Request merupakan salah satu management frame pada IEEE 802.11 yang digunakan perangkat wireless ketika melakukan proses pencarian atau discovery jaringan Wi-Fi.

Dari packet capture yang dilakukan, frame Probe Request berhasil ditemukan. Informasi ini menunjukkan bahwa proses packet capture pada wireless interface telah berjalan dan traffic management IEEE 802.11 dapat dianalisis.

Metadata yang dapat dianalisis dari frame tersebut antara lain:

- Source Address
- Destination Address
- SSID Information
- Radio Information
- Frame Information
- Data dalam format hexadecimal/ASCII

---

## 🔐 WPA2 4-Way Handshake

Bagian utama dari pengujian adalah memperoleh **WPA2 4-Way Handshake**.

Packet capture berhasil memperoleh proses handshake lengkap antara Access Point dan perangkat client.

Hasil analisis EAPOL menunjukkan:

```text
Message 1 of 4
Message 2 of 4
Message 3 of 4
Message 4 of 4
```

Dengan demikian, WPA2 4-Way Handshake berhasil diperoleh secara lengkap.

### Message 1/4

```text
Access Point → Client
```

Pada tahap pertama, Access Point mengirimkan **ANonce (Authenticator Nonce)** kepada client.

### Message 2/4

```text
Client → Access Point
```

Client kemudian mengirimkan **SNonce (Supplicant Nonce)** beserta **MIC (Message Integrity Code)** kepada Access Point.

### Message 3/4

```text
Access Point → Client
```

Access Point melakukan konfirmasi terhadap proses pembentukan key dan mengirimkan informasi yang diperlukan untuk proses instalasi key.

### Message 4/4

```text
Client → Access Point
```

Client mengirimkan konfirmasi terakhir kepada Access Point sehingga proses WPA2 4-Way Handshake selesai dan komunikasi terenkripsi dapat dimulai.

---

## 🔑 Analisis ANonce dan SNonce

Setelah WPA2 4-Way Handshake berhasil diperoleh, analisis dilanjutkan terhadap nilai **ANonce** dan **SNonce** pada paket EAPOL.

Pada hasil packet capture:

```text
Message 1/4
AP → Client
ANonce dikirim oleh Access Point
```

Sedangkan:

```text
Message 2/4
Client → AP
SNonce + MIC dikirim oleh Client
```

ANonce dan SNonce merupakan nilai yang digunakan dalam proses pembentukan key pada WPA2.

Analisis menggunakan TShark dan Wireshark berhasil menunjukkan keberadaan nilai nonce pada paket EAPOL yang diperoleh.

---

## 🔓 WPA2 Handshake & Offline Password Test

Setelah memperoleh WPA2 4-Way Handshake lengkap, dilakukan pengujian password secara **offline** pada jaringan laboratorium.

Pengujian dilakukan menggunakan capture handshake yang telah diperoleh dan wordlist terbatas yang dibuat khusus untuk jaringan pengujian.

Hasil pengujian menunjukkan:

```text
WPA Handshake : VALID
Password Test : SUCCESS
KEY FOUND     : YES
```

Hal ini menunjukkan bahwa WPA2-Personal dengan PSK yang lemah atau mudah ditebak memiliki risiko terhadap pengujian kandidat password secara offline apabila pihak yang tidak berwenang berhasil memperoleh WPA2 handshake.

Password asli jaringan pengujian tidak ditampilkan dalam dokumentasi.

---

## ⚠️ Threat Intelligence & Mapping Kerentanan

Berdasarkan hasil packet capture dan pengujian yang dilakukan, beberapa threat vector dapat dipetakan sebagai berikut:

| Threat Vector | MITRE ATT&CK / CVE | Tingkat Risiko | Deskripsi |
| :--- | :--- | :--- | :--- |
| **WPA2 Handshake Capture & Offline Password Testing** | T1110.001 | 🔴 **HIGH** | WPA2 handshake dapat digunakan untuk melakukan pengujian kandidat PSK secara offline apabila password yang digunakan lemah atau mudah ditebak. |
| **ARP Spoofing / MitM** | T1557.002 | 🟠 **MEDIUM-HIGH** | Manipulasi pemetaan IP dan MAC pada ARP dapat digunakan untuk mengalihkan traffic jaringan lokal. |
| **KRACK (Key Reinstallation Attack)** | CVE-2017-13077 | 🔴 **HIGH** | Kerentanan pada implementasi WPA2 tertentu dapat menyebabkan proses instalasi ulang key pada mekanisme 4-Way Handshake. |

---

## 🌐 ARP Spoofing / Man-in-the-Middle Analysis

Analisis berikutnya dilakukan terhadap risiko **ARP Spoofing / Man-in-the-Middle (MitM)**.

ARP (*Address Resolution Protocol*) digunakan pada jaringan lokal untuk melakukan pemetaan alamat IPv4 terhadap MAC Address.

ARP tidak memiliki mekanisme autentikasi yang kuat untuk memastikan bahwa informasi pemetaan IP dan MAC yang diterima selalu berasal dari perangkat yang benar.

Kondisi tersebut dapat menimbulkan risiko:

```text
ARP Spoofing
ARP Cache Poisoning
Adversary-in-the-Middle
Man-in-the-Middle
```

Threat ini dipetakan ke:

```text
MITRE ATT&CK
T1557.002 - ARP Cache Poisoning
```

Tingkat risiko yang digunakan dalam analisis:

```text
MEDIUM-HIGH
```

Risiko utamanya adalah traffic jaringan lokal dapat dialihkan melalui perangkat lain apabila serangan ARP Spoofing berhasil dilakukan.

---

## 💥 KRACK (Key Reinstallation Attack)

Selain risiko password dan ARP Spoofing, proyek ini juga melakukan analisis terhadap **KRACK (Key Reinstallation Attack)**.

Kerentanan yang dianalisis adalah:

```text
CVE-2017-13077
```

KRACK berkaitan dengan mekanisme WPA2 4-Way Handshake pada implementasi perangkat yang rentan.

Serangan memanfaatkan kondisi tertentu pada proses instalasi ulang key sehingga dapat memengaruhi keamanan komunikasi WPA2.

Pada proyek ini, KRACK dianalisis sebagai **Threat Intelligence / Vulnerability Analysis** berdasarkan proses WPA2 4-Way Handshake yang berhasil diperoleh.

Tingkat risiko:

```text
HIGH
```

---

## 🛡️ Rekomendasi Mitigasi (Intelligence Action)

Berdasarkan hasil analisis, beberapa langkah mitigasi yang direkomendasikan adalah sebagai berikut.

### 1. Password Policy

Gunakan **Pre-Shared Key (PSK)** yang panjang dan sulit ditebak.

Disarankan menggunakan password minimal **16–20 karakter** dengan kombinasi:

- Huruf besar
- Huruf kecil
- Angka
- Simbol

Password yang kuat dapat mengurangi kemungkinan keberhasilan pengujian kandidat password secara offline.

### 2. Patch & Firmware Update

Firmware Access Point, router, dan sistem operasi client harus diperbarui secara rutin.

Pembaruan keamanan diperlukan untuk menutup kerentanan yang telah diketahui, termasuk implementasi perangkat yang terdampak KRACK.

### 3. Migrasi ke WPA3-Personal

Jika perangkat mendukung, jaringan disarankan menggunakan **WPA3-Personal**.

WPA3 menggunakan mekanisme **Simultaneous Authentication of Equals (SAE)** yang meningkatkan perlindungan terhadap pengujian password secara offline dibandingkan mekanisme PSK pada WPA2-Personal.

### 4. Wireless IDS/IPS

Implementasikan **Wireless Intrusion Detection System (WIDS)** atau **Wireless Intrusion Prevention System (WIPS)** untuk membantu mendeteksi aktivitas wireless yang mencurigakan.

Monitoring dapat membantu mendeteksi:

- Rogue Access Point
- Aktivitas wireless yang tidak normal
- Perubahan konfigurasi jaringan
- Aktivitas monitoring yang mencurigakan

### 5. Perlindungan ARP

Untuk mengurangi risiko ARP Spoofing / MitM, dapat digunakan:

- Network Segmentation
- Dynamic ARP Inspection
- DHCP Snooping
- IDS/IPS
- HTTPS/TLS untuk komunikasi sensitif

---

## 📝 Kesimpulan

Berdasarkan pengujian yang telah dilakukan, wireless adapter berhasil diaktifkan dalam **Monitor Mode** dan digunakan untuk melakukan packet capture pada jaringan WPA2-Personal.

Packet capture berhasil memperoleh **WPA2 4-Way Handshake lengkap**, yaitu Message 1/4 sampai Message 4/4.

Analisis EAPOL juga berhasil mengidentifikasi proses pertukaran **ANonce**, **SNonce**, dan **MIC** antara Access Point dan client.

Handshake yang diperoleh kemudian digunakan dalam pengujian kandidat password secara offline pada jaringan laboratorium. Hasil pengujian menunjukkan bahwa PSK yang terdapat dalam wordlist dapat divalidasi menggunakan handshake yang telah diperoleh.

Dari sisi Threat Intelligence, proyek ini juga menganalisis risiko **ARP Spoofing / MitM (T1557.002)** dan **KRACK (CVE-2017-13077)**.

Berdasarkan hasil tersebut, keamanan WPA2-Personal tidak hanya bergantung pada protokol yang digunakan, tetapi juga pada kekuatan password, keamanan implementasi perangkat, pembaruan firmware, serta konfigurasi jaringan.

Mitigasi yang direkomendasikan adalah menggunakan PSK yang kuat, memperbarui firmware dan sistem operasi secara rutin, melakukan monitoring jaringan, menerapkan perlindungan terhadap ARP Spoofing, dan mempertimbangkan migrasi ke **WPA3-Personal** apabila perangkat mendukung.
