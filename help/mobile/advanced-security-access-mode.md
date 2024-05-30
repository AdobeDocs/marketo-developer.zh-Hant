---
title: 進階安全性存取模式
feature: "Mobile Marketing"
description: 「進階安全性存取模式的詳細資料」
source-git-commit: 2185972a272b64908d6aac8818641af07c807ac2
workflow-type: tm+mt
source-wordcount: '303'
ht-degree: 0%

---


# 進階安全性存取模式

Marketo SDK會公開設定和移除安全性簽名的方法。 您也可以使用公用程式方法來擷取裝置ID。 登入時，裝置ID應隨電子郵件傳送至客戶伺服器，以用於計算安全性簽章。 SDK應使用指向上述演演算法的點選新端點，以擷取必要欄位來例項化簽名物件。 如果已在Marketo Mobile Admin中啟用安全性存取模式，則必須在SDK中設定此簽章。

## 安全存取模式設定

必須先實作此設定，才能透過「Marketo管理員>行動應用程式和裝置」頁面啟用安全存取模式。 下列進一步的步驟說明完成安全性驗證程式所需的程式：

安全存取模式需要在客戶伺服器端實作簽章演演算法，進而提供端點以擷取存取金鑰、計算的簽章、到期時間戳記及電子郵件。 此演演算法需要使用者存取金鑰、存取密碼、電子郵件、時間戳記和裝置ID才能執行計算。 客戶負責設定端點、實作演演算法以預先形成簽名計算，並更新到期時間戳記。

```python
import argparse
import datetime
import hashlib
import hmac


ACCESS_KEY = 'Your Access Key'
ACCESS_SECRET = 'Your access secret'

# Key should not be unicode
def get_signing_key(timestamp):
    return 'MKTO' + ACCESS_SECRET + str(timestamp)

def get_string_to_sign(email, uuid):
    return email + uuid

def get_hmac(key, string_to_sign):
    return hmac.new(key, string_to_sign.encode('utf-8'), hashlib.sha256).hexdigest()

def get_epoch_plus_day():
    epoch = datetime.datetime.utcfromtimestamp(0)
    valid_until_dt = datetime.datetime.utcnow() + datetime.timedelta(days=1)
    return long((valid_until_dt - epoch).total_seconds())

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument("-e", "--email", required=True, help="email address")
    parser.add_argument("-u", "--uuid", required=True, help="Device install id")
    parser.add_argument("-t", "--timestamp", type=int, help="Valid until timestamp")
    args = parser.parse_args()
    string_to_sign = get_string_to_sign(args.email, args.uuid)
    if not args.timestamp:
        valid_until = get_epoch_plus_day()
    else:
        valid_until = args.timestamp
    signing_key = get_signing_key(valid_until)
    hmac_string = get_hmac(signing_key, string_to_sign)
    print 'HMAC is ', hmac_string
```

Marketo SDK公開新的方法來設定和移除安全性簽章。 您也可以使用公用程式方法來擷取裝置ID。 登入時，裝置ID應隨電子郵件傳送至客戶伺服器，以用於計算安全性簽章。 SDK應使用指向上述演演算法的點選新端點，以擷取必要欄位來例項化簽名物件。 如果已在Marketo Mobile Admin中啟用安全性存取模式，則必須在SDK中設定此簽章。

### iOS

```
Marketo * sharedInstance =[Marketo sharedInstance];

// set secure signature
MKTSecuritySignature *signature =
[[MKTSecuritySignature alloc] initWithAccessKey:<ACCESS_KEY> signature:<SIGNATURE_TOKEN> timestamp:<EXPIRY_TIMESTAMP> email:<EMAIL>];
[sharedInstance setSecureSignature:signature];

// remove signature
[sharedInstance removeSecureSignature];

// get device id
[sharedInstance getDeviceId];
```

```
let sharedInstance = Marketo.sharedInstance()

 // set secure signature
let signature = MKTSecuritySignature(accessKey: <ACCESS_KEY>, signature: <SIGNATURE_TOKEN> , timestamp: <EXPIRY_TIMESTAMP>, email: <EMAIL>)
sharedInstance.setSecureSignature(signature)

// remove signature
[sharedInstance removeSecureSignature];

// get device id
sharedInstance.getDeviceId()
```

### Android

```
Marketo sdk = Marketo.getInstance(getApplicationContext());

// set signature
MarketoConfig.SecureMode secureMode = new MarketoConfig.SecureMode();
secureMode.setAccessKey(<ACCESS_KEY>);
secureMode.setEmail(<EMAIL_ADDRESS>);
secureMode.setSignature(<SIGNATURE_TOKEN>);
secureMode.setTimestamp(<EXPIRY_DATE>);
if (secureMode.isValid()) {
  sdk.setSecureSignature(secureMode);
}

// remove signature
sdk.removeSecureSignature();

// get device id
sdk.getDeviceId();
```
