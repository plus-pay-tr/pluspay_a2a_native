# 📦 PlusPay A2A Android SDK

PlusPay A2A Android SDK, POS+ cihazları ile ödeme, EFT, sipariş işlemleri, parametre güncelleme ve gün sonu operasyonlarını uygulamanızdan yönetmenizi sağlar.

Bu doküman SDK’nın kurulumu, temel kullanımı ve API referansını içerir.

---

# 📌 Kurulum

`.aar` dosyasını projenizin **libs** klasörüne ekleyin.

**app/build.gradle**

```gradle
dependencies {
    implementation(files("libs/pluspay_a2a-debug.aar"))
}
```

---

# 🚀 SDK Başlatma

Activity içerisinde istemci oluşturulmalı ve initialize edilmelidir.

## Java

```java
PPA2AClient client = new PPA2AClient(this);
client.initialize();
```

## Kotlin

```kotlin
val client = PPA2AClient(this)
client.initialize()
```

---

# 💳 EFT Satış (Ödeme Başlatma)

```java
PPEftPaymentRequestModel request =
        PPEftPaymentRequestModel.toRequest(
                100.0,
                "POS",
                "CC",
                UUID.randomUUID().toString(),
                0,
                0,
                "your_token",
                "P14240701371"
        );

try {
    client.startPayment(
            request.toJsonString(),
            new PPA2ACallback<PPStartPaymentResponseModel>() {

                @Override
                public void onSuccess(PPStartPaymentResponseModel result) {
                    Log.i("PPA2AClient", "Ödeme başarılı: " + result);
                }

                @Override
                public void onError(PlusPayException e) {
                    Log.e("PPA2AClient",
                            "Hata: " + e.getError_code() +
                            " : " + e.getError_message());
                }
            });

} catch (Exception e) {
    Log.e("PPA2AClient", "SDK çağrısı başarısız: " + e.getMessage());
}
```

---

# ❌ EFT İptal

```java
try {
    PPEftCancelRequestModel request =
            PPEftCancelRequestModel.toRequest(
                    "",
                    100,
                    "token",
                    "P14240701371"
            );

    client.cancelEftPayment(
            request.toJsonString(),
            new PPA2ACallback<PPStartPaymentResponseModel>() {

                @Override
                public void onSuccess(PPStartPaymentResponseModel result) {
                    Log.i("PPA2AClient", "İptal başarılı: " + result);
                }

                @Override
                public void onError(PlusPayException e) {
                    Log.e("PPA2AClient",
                            "Hata: " + e.getError_code() +
                            " : " + e.getError_message());
                }
            });

} catch (Exception e) {
    Log.e("PPA2AClient", "SDK çağrısı başarısız: " + e.getMessage());
}
```

---

# ⚙ Parametre Güncelleme

```java
List<String> types = Arrays.asList(
        PPParameterTypes.bank.name(),
        PPParameterTypes.multinet.name()
);

PPParameterRequestModel request =
        PPParameterRequestModel.toRequest(
                types,
                false,
                "P14240701371",
                "client_token"
        );

try {
    client.triggerParameters(
            request.toJsonString(),
            new PPA2ACallback<PPParametersResponseModel>() {

                @Override
                public void onSuccess(PPParametersResponseModel result) {
                    Log.i("PPA2AClient",
                            "Parametreler güncellendi: " + result);
                }

                @Override
                public void onError(PlusPayException e) {
                    Log.e("PPA2AClient",
                            "Hata: " + e.getError_code() +
                            " : " + e.getError_message());
                }
            });

} catch (Exception e) {
    Log.e("PPA2AClient", "SDK çağrısı başarısız: " + e.getMessage());
}
```

---

# 🧾 Gün Sonu İşlemi

```java
List<String> types = Arrays.asList(
        PPEodType.CASH.name(),
        PPEodType.POS.name()
);

PPEodRequestModel request =
        PPEodRequestModel.toRequest(
                types,
                false,
                "token",
                "P14240701371"
        );

try {
    client.triggerEod(
            request.toJsonString(),
            new PPA2ACallback<PPEodResponseModel>() {

                @Override
                public void onSuccess(PPEodResponseModel result) {
                    Log.i("PPA2AClient", "Gün sonu başarılı: " + result);
                }

                @Override
                public void onError(PlusPayException e) {
                    Log.e("PPA2AClient",
                            "Hata: " + e.getError_code() +
                            " : " + e.getError_message());
                }
            });

} catch (Exception e) {
    Log.e("PPA2AClient", "SDK çağrısı başarısız: " + e.getMessage());
}
```

---

# 🔚 Activity Kapatılırken

SDK kaynaklarını serbest bırakın.

## Kotlin

```kotlin
override fun onDestroy() {
    super.onDestroy()
    client.dispose()
}
```

## Java

```java
@Override
protected void onDestroy() {
    super.onDestroy();
    client.dispose();
}
```

---

# 📚 İstemci API Referansı

| Metod             | İstek Modeli                | Yanıt Modeli                | Açıklama           |
| ----------------- | --------------------------- | --------------------------- | ------------------ |
| startPayment      | PPStartPaymentRequestModel  | PPStartPaymentResponseModel | Ödeme başlat       |
| cancelPayment     | PPCancelPaymentRequestModel | PPStartPaymentResponseModel | Ödeme iptal        |
| startEftPayment   | PPEftPaymentRequestModel    | PPStartPaymentResponseModel | EFT ödeme          |
| cancelEftPayment  | PPEftCancelRequestModel     | PPStartPaymentResponseModel | EFT iptal          |
| startOrderPayment | PPOrderPaymentRequestModel  | PPOrderPaymentResponseModel | Sipariş ödeme      |
| triggerEod        | PPEodRequestModel           | PPEodResponseModel          | Gün sonu           |
| triggerParameters | PPParameterRequestModel     | PPParametersResponseModel   | Parametre güncelle |

---

# 📦 Yanıt Modelleri

## PPStartPaymentResponseModel

```
id, orderCode, paymentType, paymentMethod,
totalAmount, totalPaid, amountDue,
isPartial, partialType, source, status,
actionStatus, invoice, payment, delivery
```

## PPOrderPaymentResponseModel

```
grandTotal, status, orderCode,
totalAmount, totalPaid, amountDue, results
```

## PPEodResponseModel

```
results → PPEodResponseItem listesi
```

## PPParametersResponseModel

```
results → parametre güncelleme sonuçları
```

---

# 🔢 Enum Tanımları

**PPPaymentType**
POS, PAYCELL, HEPSIPAY, CASH, ONLINE, BANK_TRANSFER, MULTINET, …

**PPPaymentMethod**
CC, CASH, QR, NFC, MOBILE, ONLINE, …

**PPEodType**
POS, CASH, ONLINE, MULTINET, …

**PPParameterTypes**
bank, multinet, metropol, paye, iwallet

**PPPartialPaymentType**
AMOUNT, PRODUCT

**PPOrderStatusEnum**
CANCEL, NOT_RESPONSE, WAITING, SUCCESS

---

# ⚠ Hata Kodları

| Kod                 | Açıklama              |
| ------------------- | --------------------- |
| LAUNCH_INTENT_ERROR | POS+ başlatılamadı    |
| PP-A2A-PARSE        | Yanıt parse edilemedi |
| PP-A2A-*            | POS+ hata kodları     |

---

# 📄 JSON Referansları

⚠ Bu JSON modelleri için, Header yapısı .toRequest() işlevi ile SDK tarafından otomatik oluşturulur. Manuel üretmeniz gerekmez. Yalnızca referans amaçlıdır.

## EFT Ödeme

```json
{
  "data": {
    "total_amount": 100,
    "payment_type": "POS",
    "payment_method": "CC",
    "transaction_id": "uuid",
    "tax_rate": 0,
    "installment": 0
  },
  "header": {
    "transaction_type": "POST_EFTPOS",
    "client_token": "token",
    "serial_no": "serial"
  }
}
```

## Standart Ödeme

```json
{
  "data": {
    "payment_type": "POS",
    "payment_method": "CC",
    "total_amount": 100
  },
  "header": {
    "transaction_type": "POST_PAYMENT_START",
    "client_token": "token",
    "serial_no": "serial",
    "order_code": "order"
  }
}
```

## EFT İptal

```json
{
  "data": { "total_amount": 100 },
  "header": {
    "transaction_type": "POST_EFTPOS_CANCEL",
    "client_token": "token",
    "serial_no": "serial",
    "transaction_id": "tx"
  }
}
```

## Ödeme İptal

```json
{
  "data": {
    "transaction_id": "tx",
    "note": "reason"
  },
  "header": {
    "transaction_type": "POST_PAYMENT_CANCEL",
    "client_token": "token",
    "serial_no": "serial",
    "order_code": "order"
  }
}
```

## Parametre Güncelleme

```json
{
  "data": {
    "is_all": false,
    "types": ["bank", "multinet"]
  },
  "header": {
    "transaction_type": "PARAMETERS",
    "client_token": "token",
    "serial_no": "serial"
  }
}
```

## Gün Sonu

```json
{
  "data": {
    "is_all": false,
    "types": ["POS", "CASH"]
  },
  "header": {
    "transaction_type": "EOD",
    "client_token": "token",
    "serial_no": "serial"
  }
}
```

## Sipariş Ödeme

```json
{
  "header": {
    "transaction_type": "ORDER_PAYMENT",
    "client_token": "token",
    "serial_no": "serial",
    "order_code": "order"
  }
}
```

---

# ✅ Notlar

* Her işlem benzersiz transaction ID kullanmalıdır
* Callback / exception yönetimi zorunludur
* Activity destroy sırasında `dispose()` çağrılmalıdır

---
