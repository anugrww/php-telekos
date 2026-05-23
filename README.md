# DOKUMENTASI API TELEKOS UNTUK PHP

Sumber: https://api.telekos.my.id/docs/

---

# AUTENTIKASI

Kirim API key di setiap request.

## Header (Direkomendasikan)

```http
GET https://api.telekos.my.id/api/balance
x-api-key: telekos_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## Query Parameter

```http
GET https://api.telekos.my.id/api/balance?apikey=telekos_xxxxxxxx
```

---

# BASE URL

```txt
https://api.telekos.my.id
```

---

# RESPON STANDARD

## Success

```json
{
  "ok": true,
  "data": {
    
  }
}
```

## Error

```json
{
  "ok": false,
  "error": "KODE_ERROR"
}
```

---

# ENDPOINT: CEK SALDO

- **Method:** `GET`
- **URL:** `https://api.telekos.my.id/api/balance`

## Parameter

| Nama | Tipe | Wajib | Keterangan |
|---|---|---|---|
| x-api-key | header | Ya | API key kamu |

## Contoh Request

```http
GET https://api.telekos.my.id/api/balance
x-api-key: telekos_xxxxxxxxxxxxxxxxx
```

## Respon

```json
{
  "ok": true,
  "saldo": 50000
}
```

---

# ENDPOINT: DAFTAR NEGARA

- **Method:** `GET`
- **URL:** `https://api.telekos.my.id/api/countries`

## Contoh Request

```http
GET https://api.telekos.my.id/api/countries
```

## Respon

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

# ENDPOINT: DAFTAR LAYANAN

- **Method:** `GET`
- **URL:** `https://api.telekos.my.id/api/services`

## Parameter

| Nama | Tipe | Wajib | Keterangan |
|---|---|---|---|
| country | number/string | Ya | ID atau ISO negara, contoh: `6` atau `id` |

## Contoh Request

```http
GET https://api.telekos.my.id/api/services?country=id
```

## Respon

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

# ENDPOINT: BUAT ORDER

- **Method:** `POST`
- **URL:** `https://api.telekos.my.id/api/order`

## Parameter

| Nama | Tipe | Wajib | Keterangan |
|---|---|---|---|
| service | string | Ya | Kode layanan, contoh: `wa`, `ig`, `fb` |
| country | number/string | Ya | ID atau ISO negara, contoh: `6` atau `id` |

## Contoh Request

```json
{
  "service": "wa",
  "country": 6
}
```

## Respon

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

# ENDPOINT: CEK OTP

- **Method:** `GET`
- **URL:** `https://api.telekos.my.id/api/status/:activationId`

## Parameter

| Nama | Tipe | Wajib | Keterangan |
|---|---|---|---|
| activationId | string | Ya | ID dari `POST /api/order` |

---

## Respon Menunggu OTP

```json
{
  "ok": true,
  "activationId": "48291047",
  "state": "waiting"
}
```

## Respon OTP Diterima

```json
{
  "ok": true,
  "activationId": "48291047",
  "state": "ok",
  "otp": "847291"
}
```

## Respon Expired / Dibatalkan

```json
{
  "ok": true,
  "activationId": "48291047",
  "state": "expired"
}
```

---

# ENDPOINT: BATALKAN ORDER

- **Method:** `POST`
- **URL:** `https://api.telekos.my.id/api/cancel`

## Parameter

| Nama | Tipe | Wajib | Keterangan |
|---|---|---|---|
| activationId | string | Ya | ID dari `POST /api/order` |

## Contoh Request

```json
{
  "activationId": "48291047"
}
```

## Respon

```json
{
  "ok": true,
  "activationId": "48291047",
  "refunded": 3500,
  "saldoSisa": 53500
}
```

---

# KODE ERROR

| Kode Error | Keterangan |
|---|---|
| INVALID_KEY | API key tidak valid |
| MISSING_PARAM | Parameter wajib tidak ada |
| NO_SALDO | Saldo tidak cukup |
| NO_STOK | Stok nomor habis |
| ORDER_FAILED | Gagal membuat order — coba lagi |
| ORDER_NOT_FOUND | activationId tidak ditemukan |
| CANCEL_ERROR | Gagal membatalkan order — coba lagi |
| STATUS_ERROR | Gagal mengecek status — coba lagi |
| SERVICE_ERROR | Gagal mengambil data layanan — coba lagi |
| SERVER_ERROR | Kesalahan internal server |

---

# REFERENSI NEGARA

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

# CATATAN UNTUK PHP

- Gunakan cURL atau Guzzle untuk request HTTP.
- Simpan `activationId` dari respon order untuk cek OTP dan cancel.
- Poll endpoint status tiap 5–10 detik sampai OTP masuk atau expired.
- Order expired otomatis dalam 15 menit jika tidak ada OTP, saldo dikembalikan.
- Jangan expose API key ke publik.
- Semua transaksi menggunakan saldo akun Telekos.
- Daftar lengkap negara dapat diambil real-time lewat `GET /api/countries`.

---

# CONTOH IMPLEMENTASI PHP (cURL)

## Cek Saldo

```php
<?php

$apiKey = "telekos_xxxxxxxxxxxxxxxxx";

$ch = curl_init();

curl_setopt_array($ch, [
    CURLOPT_URL => "https://api.telekos.my.id/api/balance",
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_HTTPHEADER => [
        "x-api-key: $apiKey"
    ]
]);

$response = curl_exec($ch);

curl_close($ch);

echo $response;
```

---

## Buat Order

```php
<?php

$apiKey = "telekos_xxxxxxxxxxxxxxxxx";

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
        "x-api-key: $apiKey"
    ]
]);

$response = curl_exec($ch);

curl_close($ch);

echo $response;
```

---

## Cek OTP

```php
<?php

$apiKey = "telekos_xxxxxxxxxxxxxxxxx";
$activationId = "48291047";

$ch = curl_init();

curl_setopt_array($ch, [
    CURLOPT_URL => "https://api.telekos.my.id/api/status/$activationId",
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_HTTPHEADER => [
        "x-api-key: $apiKey"
    ]
]);

$response = curl_exec($ch);

curl_close($ch);

echo $response;
```

---

## Cancel Order

```php
<?php

$apiKey = "telekos_xxxxxxxxxxxxxxxxx";

$data = [
    "activationId" => "48291047"
];

$ch = curl_init();

curl_setopt_array($ch, [
    CURLOPT_URL => "https://api.telekos.my.id/api/cancel",
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POST => true,
    CURLOPT_POSTFIELDS => json_encode($data),
    CURLOPT_HTTPHEADER => [
        "Content-Type: application/json",
        "x-api-key: $apiKey"
    ]
]);

$response = curl_exec($ch);

curl_close($ch);

echo $response;
```