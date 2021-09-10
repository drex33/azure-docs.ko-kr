---
title: 자습서 - OpenSSL을 사용하여 Azure IoT Hub에 대한 X.509 테스트 인증서 만들기 | Microsoft Docs
description: 자습서 - OpenSSL을 사용하여 Azure IoT 허브에 대한 CA 및 디바이스 인증서 만들기
author: v-gpettibone
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: adbb2979fc9e097fa0abf2675759ba1f7aad8a0c
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123310560"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>자습서: OpenSSL을 사용하여 테스트 인증서 만들기

신뢰할 수 있는 인증 기관에서 X.509 인증서를 구입할 수 있지만, 사용자 고유의 테스트 인증서 계층 구조를 만들거나 자체 서명된 인증서를 사용하는 것이 IoT 허브 디바이스 인증을 테스트하는 데 적합합니다. 다음 예제에서는 [OpenSSL](https://www.openssl.org/) 및 [OpenSSL Cookbook](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html)을 사용하여 CA(인증 기관), 하위 CA 및 디바이스 인증서를 만듭니다. 그런 다음, 예제에서 하위 CA 및 디바이스 인증서를 인증서 계층 구조에 서명합니다. 이는 예제 용도로만 제공됩니다.

## <a name="step-1---create-the-root-ca-directory-structure"></a>1단계 - 루트 CA 디렉터리 구조 만들기

인증 기관에 대한 디렉터리 구조를 만듭니다.

* **certs** 는 새 인증서를 저장합니다.
* **db** 디렉터리는 인증서 데이터베이스에 사용됩니다.
* **private** 디렉터리는 CA 프라이빗 키를 저장합니다.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>2단계 - 루트 CA 구성 파일 만들기

CA를 만들기 전에 구성 파일을 만들어 rootca 디렉터리에 `rootca.conf`로 저장합니다.

```xml
[default]
name                     = rootca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Root CA"

[ca_default]
home                     = ../rootca 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = none
default_days             = 3650
default_crl_days         = 365
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>3단계 - 루트 CA 만들기

먼저 키 및 CSR(인증서 서명 요청)을 rootca 디렉터리에 생성합니다.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

다음으로 자체 서명된 CA 인증서를 만듭니다. 자체 서명은 테스트 용도에 적합합니다. 명령줄에서 ca_ext 구성 파일 확장명을 지정합니다. 이는 인증서가 루트 CA용이며, 인증서 및 CRL(인증서 해지 목록)에 서명하는 데 사용할 수 있음을 나타냅니다. 인증서에 서명하고, 데이터베이스에 커밋합니다.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>4단계 - 하위 CA 디렉터리 구조 만들기

하위 CA에 대한 디렉터리 구조를 만듭니다.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>5단계 - 하위 CA 구성 파일 만들기

구성 파일을 만들어 `subca` 디렉터리에 subca.conf로 저장합니다.

```bash
[default]
name                     = subca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Subordinate CA"

[ca_default]
home                     = . 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = copy 
default_days             = 365
default_crl_days         = 90 
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash
```

## <a name="step-6---create-a-subordinate-ca"></a>6단계 - 하위 CA 만들기

새 일련 번호를 하위 CA 인증서에 대한 `rootca/db/serial` 파일에 만듭니다.

```bash
  openssl rand -hex 16 > ../rootca/db/serial
```

>[!IMPORTANT]
>모든 하위 CA 인증서 및 사용자가 만드는 모든 디바이스 인증서에 대해 새 일련 번호를 만들어야 합니다. 인증서마다 일련 번호가 달라야 합니다.

다음 예제에서는 하위 또는 등록 CA를 만드는 방법을 보여 줍니다. 루트 CA를 사용하여 인증서에 서명할 수 있으므로 하위 CA를 반드시 만들 필요가 없습니다. 그러나 하위 CA가 있으면 루트 CA가 오프라인으로 유지되고 하위 CA에서 클라이언트 인증서를 발급하는 실제 인증서 계층 구조를 모방합니다.

구성 파일을 사용하여 키 및 CSR(인증서 서명 요청)을 생성합니다.

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

CSR을 루트 CA에 제출하고, 루트 CA를 사용하여 하위 CA 인증서를 발급하고 서명합니다. 명령줄에서 확장 스위치에 대한 sub_ca_ext를 지정합니다. 확장은 인증서 및 CRL(인증서 해지 목록)에 서명할 수 있는 CA에 대한 인증서임을 나타냅니다. 메시지가 표시되면 인증서에 서명하고 데이터베이스에 커밋합니다.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>7 단계 - 소유 증명 입증

이제 루트 CA 인증서와 하위 CA 인증서가 모두 있습니다. 둘 중 하나를 사용하여 디바이스 인증서에 서명할 수 있습니다. 선택한 항목을 IoT Hub에 업로드해야 합니다. 다음 단계에서는 하위 CA 인증서를 사용하고 있다고 가정합니다. 하위 CA 인증서를 IoT Hub에 업로드하고 등록하려면 다음을 수행합니다.

1. Azure Portal에서 IoT Hub로 이동하고, **설정 > 인증서** 를 차례로 선택합니다.

2. **추가** 를 선택하여 새 하위 CA 인증서를 추가합니다.

3. **인증서 이름** 필드에서 표시 이름을 입력하고, 이전에 만든 PEM 인증서 파일을 선택합니다.

> [!NOTE]
> 위에서 만든 .crt 인증서는 .pem 인증서와 동일합니다. 소유를 증명하기 위해 인증서를 업로드할 때 확장자를 간단히 변경하거나 다음 OpenSSL 명령을 사용할 수 있습니다.

```bash
openssl x509 -in mycert.crt -out mycert.pem -outform PEM
```

4. **저장** 을 선택합니다. 인증서가 인증서 목록에 **확인되지 않음** 상태로 표시됩니다. 확인 프로세스는 사용자가 인증서를 소유하고 있음을 증명합니다.

   
5. 인증서를 선택하여 **인증서 세부 정보** 대화 상자를 표시합니다.

6. **확인 코드 생성** 을 선택합니다. 자세한 내용은 [CA 인증서 소유 증명](tutorial-x509-prove-possession.md)을 참조하세요.

7. 확인 코드를 클립보드에 복사합니다. 확인 코드를 인증서 주체로 설정해야 합니다. 예를 들어 확인 코드가 BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A인 경우 9단계에서 표시한 대로 인증서 주체로 추가합니다.

8. 프라이빗 키를 생성합니다.

  ```bash
    $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
  ```

9. 프라이빗 키에서 CSR(인증서 서명 요청)을 생성합니다. 인증서의 주체로 확인 코드를 추가합니다.

  ```bash
  openssl req -new -key pop.key -out pop.csr
  
    -----
    Country Name (2 letter code) [XX]:.
    State or Province Name (full name) []:.
    Locality Name (eg, city) [Default City]:.
    Organization Name (eg, company) [Default Company Ltd]:.
    Organizational Unit Name (eg, section) []:.
    Common Name (eg, your name or your server hostname) []:BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A
    Email Address []:

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
 
  ```

10. 루트 CA 구성 파일 및 소유 증명 인증서의 CSR을 사용하여 인증서를 만듭니다.

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

11. **인증서 세부 정보** 보기에서 새 인증서를 선택합니다. PEM 파일을 찾으려면 인증서 폴더로 이동합니다.

12. 인증서가 업로드되면 **확인** 을 선택합니다. CA 인증서 상태가 **확인됨** 으로 변경됩니다.

## <a name="step-8---create-a-device-in-your-iot-hub"></a>8단계 - IoT Hub에서 디바이스 만들기

Azure Portal에서 IoT Hub로 이동하고, 다음 값을 사용하여 새 IoT 디바이스 ID를 만듭니다.

1. 디바이스 인증서의 주체 이름과 일치하는 **디바이스 ID** 를 제공합니다.

1. **X.509 CA 서명됨** 인증 유형을 선택합니다.

1. **저장** 을 선택합니다.

## <a name="step-9---create-a-client-device-certificate"></a>9단계 - 클라이언트 디바이스 인증서 만들기

클라이언트 인증서를 생성하려면 먼저 프라이빗 키를 생성해야 합니다. 다음 명령에서는 OpenSSL을 사용하여 프라이빗 키를 만드는 방법을 보여 줍니다. 키를 subca 디렉터리에 만듭니다.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

키에 대한 CSR(인증서 서명 요청)을 만듭니다. 챌린지 암호 또는 회사 이름(선택 사항)을 입력할 필요가 없습니다. 그러나 일반 이름 필드에서 디바이스 ID를 입력해야 합니다.

```bash
openssl req -new -key device.key -out device.csr

-----
Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server hostname) []:`<your device ID>`
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

CSR이 필요한지 확인합니다.

```bash
openssl req -text -in device.csr -noout
```

인증서 계층 구조에 서명하기 위해 CSR을 하위 CA에 보냅니다. `-extensions` 스위치에서 `client_ext`를 지정합니다. 발급된 인증서의 `Basic Constraints`는 이 인증서가 CA에 대한 인증서가 아님을 나타냅니다. 여러 인증서에 서명하는 경우 각 인증서를 생성하기 전에 openssl `rand -hex 16 > db/serial` 명령을 사용하여 일련 번호를 업데이트해야 합니다.

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>다음 단계

[인증서 인증 테스트](tutorial-x509-test-certificate.md)로 이동하여 인증서가 디바이스를 IoT Hub에 인증할 수 있는지 확인합니다.
