# 🚀 Dokumentasi API Telekos untuk PHP

> Dokumentasi resmi API Telekos untuk integrasi layanan OTP & virtual number menggunakan PHP.  
> Source: https://api.telekos.my.id/docs/

---

# 📌 Base URL

```txt
https://api.telekos.my.id
```

---

# 🔐 Autentikasi API

Kirim API Key di setiap request.

## ✅ Menggunakan Header (Direkomendasikan)

```http
GET https://api.telekos.my.id/api/balance
x-api-key: telekos_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

---

## ✅ Menggunakan Query Parameter

```http
GET https://api.telekos.my.id/api/balance?apikey=telekos_xxxxxxxx
```

---

# 📦 Format Respon Standard

## ✅ Success

```json
{
  "ok": true,
  "data": {}
}
```

---

## ❌ Error

```json
{
  "ok": false,
  "error": "KODE_ERROR"
}
```

---

# 💰 Endpoint — Cek Saldo

## 📍 Request

```http
GET /api/balance
```

### Header

| Parameter | Type | Wajib | Keterangan |
|---|---|---|---|
| x-api-key | string | ✅ | API Key akun Telekos |

---

## 🌐 Full URL

```txt
https://api.telekos.my.id/api/balance
```

---

## ✅ Contoh Respon

```json
{
  "ok": true,
  "saldo": 50000
}
```

---

# 🌍 Endpoint — Daftar Negara

## 📍 Request

```http
GET /api/countries
```

---

## 🌐 Full URL

```txt
https://api.telekos.my.id/api/countries
```

---

## ✅ Contoh Respon

```json
{
  "ok": true,
  "countries": [
    {
      "id": 6,
      "name": "Indonesia",
      "iso": "id"
    },
    {
      "id": 4,
      "name": "Philippines",
      "iso": "ph"
    }
  ]
}
```

---

# 🛒 Endpoint — Daftar Layanan

## 📍 Request

```http
GET /api/services
```

---

## 📥 Parameter

| Parameter | Type | Wajib | Keterangan |
|---|---|---|---|
| country | number/string | ✅ | ID atau ISO negara |

### Contoh

```txt
6
```

atau

```txt
id
```

---

## 🌐 Full URL

```txt
https://api.telekos.my.id/api/services
```

---

## ✅ Contoh Respon

```json
{
  "ok": true,
  "country": {
    "id": 6,
    "name": "Indonesia",
    "iso": "id"
  },
  "services": [
    {
      "code": "wa",
      "name": "WhatsApp",
      "price": 3500,
      "stock": 142
    },
    {
      "code": "ig",
      "name": "Instagram",
      "price": 2500,
      "stock": 87
    }
  ]
}
```

---

# 📲 Endpoint — Buat Order

## 📍 Request

```http
POST /api/order
```

---

## 📥 Parameter

| Parameter | Type | Wajib | Keterangan |
|---|---|---|---|
| service | string | ✅ | Kode layanan |
| country | number/string | ✅ | ID atau ISO negara |

---

## ✅ Contoh Request

```json
{
  "service": "wa",
  "country": 6
}
```

---

## 🌐 Full URL

```txt
https://api.telekos.my.id/api/order
```

---

## ✅ Contoh Respon

```json
{
  "ok": true,
  "activationId": "48291047",
  "phone": "+6281234567890",
  "service": "wa",
  "country": {
    "id": 6,
    "name": "Indonesia",
    "iso": "id"
  },
  "price": 3500,
  "saldoSisa": 46500
}
```

---

# 📩 Endpoint — Cek OTP

## 📍 Request

```http
GET /api/status/:activationId
```

---

## 📥 Parameter

| Parameter | Type | Wajib | Keterangan |
|---|---|---|---|
| activationId | string | ✅ | ID dari endpoint order |

---

## 🌐 Full URL

```txt
https://api.telekos.my.id/api/status/:activationId
```

---

# ⏳ Respon Menunggu OTP

```json
{
  "ok": true,
  "activationId": "48291047",
  "state": "waiting"
}
```

---

# ✅ Respon OTP Diterima

```json
{
  "ok": true,
  "activationId": "48291047",
  "state": "ok",
  "otp": "847291"
}
```

---

# ❌ Respon Expired / Dibatalkan

```json
{
  "ok": true,
  "activationId": "48291047",
  "state": "expired"
}
```

---

# 🚫 Endpoint — Cancel Order

## 📍 Request

```http
POST /api/cancel
```

---

## 📥 Parameter

| Parameter | Type | Wajib | Keterangan |
|---|---|---|---|
| activationId | string | ✅ | ID order |

---

## ✅ Contoh Request

```json
{
  "activationId": "48291047"
}
```

---

## 🌐 Full URL

```txt
https://api.telekos.my.id/api/cancel
```

---

## ✅ Contoh Respon

```json
{
  "ok": true,
  "activationId": "48291047",
  "refunded": 3500,
  "saldoSisa": 53500
}
```

---

# ⚠️ Kode Error API

| Error Code | Keterangan |
|---|---|
| INVALID_KEY | API key tidak valid |
| MISSING_PARAM | Parameter wajib tidak ada |
| NO_SALDO | Saldo tidak cukup |
| NO_STOK | Stok nomor habis |
| ORDER_FAILED | Gagal membuat order |
| ORDER_NOT_FOUND | activationId tidak ditemukan |
| CANCEL_ERROR | Gagal membatalkan order |
| STATUS_ERROR | Gagal mengecek status |
| SERVICE_ERROR | Gagal mengambil layanan |
| SERVER_ERROR | Kesalahan internal server |

---

# 🌎 Referensi Negara

| ID | Negara | ISO |
|---|---|---|
| 6 | Indonesia | id |
| 4 | Philippines | ph |
| 33 | Colombia | co |
| 52 | Thailand | th |
| 36 | Canada | ca |
| 10 | Vietnam | vn |
| 14 | Hong Kong | hk |
| 7 | Malaysia | my |
| 16 | United Kingdom | gb |
| 31 | South Africa | za |

---

# 🧠 Catatan Penting untuk PHP

- Gunakan `cURL` atau `Guzzle` untuk request HTTP.
- Simpan `activationId` dari order.
- Gunakan endpoint status untuk cek OTP.
- Polling endpoint status setiap `5-10 detik`.
- Order otomatis expired dalam `15 menit`.
- Saldo otomatis kembali jika order expired.
- Jangan pernah expose API key ke publik.
- Semua transaksi menggunakan saldo akun Telekos.
- Daftar negara terbaru bisa diambil realtime dari endpoint:

```txt
GET /api/countries
```

---

# 🧪 Contoh Request PHP (cURL)

## ✅ Cek Saldo

```php
<?php

$ch = curl_init();

curl_setopt_array($ch, [
    CURLOPT_URL => "https://api.telekos.my.id/api/balance",
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_HTTPHEADER => [
        "x-api-key: telekos_xxxxxxxxxxxxxxxxx"
    ]
]);

$response = curl_exec($ch);

curl_close($ch);

echo $response;
```

---

# 🧪 Contoh Buat Order PHP

```php
<?php

$data = [
    "service" => "wa",
    "country" => 6
];

$ch = curl_init();

curl_setopt_array($ch, [
    CURLOPT_URL => "https://api.telekos.my.id/api/order",
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST => true,
    CURLOPT_POSTFIELDS => json_encode($data),
    CURLOPT_HTTPHEADER => [
        "Content-Type: application/json",
        "x-api-key: telekos_xxxxxxxxxxxxxxxxx"
    ]
]);

$response = curl_exec($ch);

curl_close($ch);

echo $response;
```

---

# 🧪 Contoh Cek OTP PHP

```php
<?php

$activationId = "48291047";

$ch = curl_init();

curl_setopt_array($ch, [
    CURLOPT_URL => "https://api.telekos.my.id/api/status/$activationId",
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_HTTPHEADER => [
        "x-api-key: telekos_xxxxxxxxxxxxxxxxx"
    ]
]);

$response = curl_exec($ch);

curl_close($ch);

echo $response;
```

---

# 🎯 Penutup

Dokumentasi ini dibuat agar integrasi API Telekos lebih mudah digunakan pada project PHP maupun backend lainnya.

Semoga membantu 🚀
