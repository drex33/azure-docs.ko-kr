---
title: Azure Firewall 프리미엄 인증서
description: Azure Firewall 프리미엄에서 TLS 검사를 적절히 구성하려면 중간 CA 인증서를 구성하고 설치해야 합니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 08/02/2021
ms.author: victorh
ms.openlocfilehash: 0826e58f4b61e0a99064226a8488f1c3fb499cd7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536191"
---
# <a name="azure-firewall-premium-certificates"></a>Azure Firewall 프리미엄 인증서 

Azure Firewall 프리미엄 TLS 검사를 올바르게 구성하려면 유효한 중간 CA 인증서를 제공하고 Azure Key Vault에 보관해야 합니다.

## <a name="certificates-used-by-azure-firewall-premium"></a>Azure Firewall 프리미엄에서 사용하는 인증서

일반적인 배포에는 다음과 같은 세 가지 유형의 인증서가 사용됩니다.

- **중간 CA 인증서(CA 인증서)**

   인증 기관(CA)은 디지털 인증서에 서명하는 신뢰받는 조직입니다. CA는 인증서를 요청하는 회사 또는 개인의 신원과 적법성을 확인합니다. 확인에 성공하면 CA는 서명된 인증서를 발급합니다. SSL/TLS 핸드셰이크 도중 서버가 클라이언트(예: 웹 브라우저)에 인증서를 제시하면 클라이언트는 ‘알려진 좋은’ 서명자 목록과 대조하여 서명을 확인하려고 시도합니다. 일반적으로 웹 브라우저는 호스트를 식별하기 위해 암묵적으로 신뢰할 수 있는 CA 목록을 갖고 있습니다. 자신의 인증서에 직접 서명하는 일부 사이트에서처럼 목록에 올라온 인증 기관이 없는 경우, 브라우저는 알려진 기관에서 인증서에 서명하지 않았음을 사용자에게 알리고 검증되지 않은 사이트와 통신을 계속할지 여부를 사용자에게 묻습니다.

- **서버 인증서(웹 사이트 인증서)**

   특정 도메인 이름과 연관된 인증서. 웹 사이트에 유효한 인증서가 있으면 인증 기관에서 웹 주소가 실제로 해당 조직에 속해 있는지 확인하는 단계를 수행했음을 의미합니다. 보안 웹 사이트의 링크를 따라가거나 URL을 입력하면, 브라우저가 인증서에 다음 특성이 있는지 확인합니다.
   - 웹 사이트 주소가 인증서의 주소와 일치합니다.
   - 인증서에 브라우저가 ‘신뢰할 수 있는’ 기관으로 인식하는 인증 기관의 서명이 있습니다.
   
   사용자가 신뢰할 수 없는 인증서를 사용하여 서버에 연결하는 경우가 있습니다. 그럼 Azure Firewall은 서버가 연결을 종료한 것처럼 연결을 끊습니다.

- **루트 CA 인증서(루트 인증서)**

   인증 기관은 트리 구조 형식으로 여러 인증서를 발급할 수 있습니다. 루트 인증서는 트리의 최상위 인증서입니다.

Azure Firewall 프리미엄은 아웃바운드 HTTP/S 트래픽을 가로막고 `www.website.com`에 대한 서버 인증서를 자동으로 만들 수 있습니다. 이 인증서는 사용자가 제공하는 중간 CA 인증서를 사용하여 만들어집니다. 이 프로시저를 수행하려면 최종 사용자 브라우저 및 클라이언트 애플리케이션이 조직의 루트 CA 인증서 또는 중간 CA 인증서를 신뢰해야 합니다. 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="인증서 프로세스":::

## <a name="intermediate-ca-certificate-requirements"></a>중간 CA 인증서 요구 사항

CA 인증서가 다음 요구 사항을 준수하는지 확인하세요.

- Key Vault 비밀로 배포되는 경우, 인증서 및 프라이빗 키와 함께 Pkcs12(암호 없는 PFX)를 사용해야 합니다.

- 단일 인증서여야 하며 전체 인증서 체인을 포함하지 않습니다.  

- 앞으로 1년 동안 유효해야 합니다.  

- 최소 크기가 4,096바이트인 RSA 프라이빗 키여야 합니다.  

- `KeyUsage` 확장은 `KeyCertSign` 플래그(RFC 5280; 4.2.1.3 키 사용)와 함께 Critical로 표시되어야 합니다.

- 인증서에 `BasicContraints` 확장이 Critical로 표시되어 있어야 합니다(RFC 5280; 4.2.1.9 기본 제약 조건).  

- `CA` 플래그를 TRUE로 설정해야 합니다.

- 경로 길이는 1보다 크거나 같아야 합니다.

## <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](../key-vault/general/overview.md)는 비밀, 키 및 TLS/SSL 인증서를 보호하는 데 사용할 수 있는 플랫폼 관리 비밀 저장소입니다. Azure Firewall 프리미엄은 방화벽 정책에 연결된 서버 인증서에 대해 Key Vault와의 통합을 지원합니다.
 
키 자격 증명 모음을 구성하려면:

- 키 쌍이 있는 기존 인증서를 키 자격 증명 모음으로 가져와야 합니다. 
- 또는 암호 없이 베이스 64로 인코딩된 PFX 파일로 저장된 키 자격 증명 모음 비밀을 사용할 수도 있습니다.  PFX 파일은 프라이빗 키와 퍼블릭 키를 모두 포함하는 디지털 인증서입니다.
- CA 인증서 가져오기를 사용하면 인증서 만료 날짜를 기준으로 알림을 구성할 수 있으므로 사용하는 것이 좋습니다.
- 인증서 또는 비밀을 가져온 후에는 키 자격 증명 모음에 액세스 정책을 정의하여 ID에 인증서/비밀에 대한 get 액세스 권한을 부여해야 합니다.
- 제공된 CA 인증서는 Azure 워크로드에서 신뢰해야 합니다. 올바르게 배포되었는지 확인합니다.

Azure Firewall이 사용자 대신 Key Vault에서 인증서를 검색하는 데 사용하는 기존 사용자가 할당한 관리 ID를 생성하거나 재사용할 수 있습니다. 자세한 내용은 [Azure 리소스의 관리 ID란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조하세요. 

## <a name="configure-a-certificate-in-your-policy"></a>정책에서 인증서 구성

방화벽 프리미엄 정책에서 CA 인증서를 구성하려면 정책을 선택한 다음 **TLS 검사** 를 선택하세요. **TLS 검사** 페이지에서 **사용** 을 선택합니다. 그리고 Azure Key Vault에서 다음 그림과 같이 CA 인증서를 선택합니다.

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Azure Firewall 프리미엄 개요 다이어그램":::
 
> [!IMPORTANT]
> Azure Portal에서 인증서를 확인하고 구성하려면 Azure 사용자 계정을 Key Vault 액세스 정책에 추가해야 합니다. **비밀 사용 권한** 아래에서 사용자 계정에 **가져오기** 및 **목록** 을 지정합니다.
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Azure Key Vault 액세스 정책":::

## <a name="create-your-own-self-signed-ca-certificate"></a>자체 서명된 CA 인증서 만들기

자체 인증서를 만들어 TLS 검사를 테스트하고 확인하는 데 도움이 되려면 다음 스크립트를 사용하여 자체 서명된 루트 CA 및 중간 CA를 생성할 수 있습니다.

> [!IMPORTANT]
> 프로덕션의 경우 기업 PKI를 사용하여 중간 CA 인증서를 생성해야 합니다. 기업 PKI는 기존 인프라를 활용하고 모든 엔드포인트 머신에 루트 CA를 배포합니다. 자세한 내용은 [Azure Firewall에 대한 Enterprise CA 인증서 배포 및 구성](premium-deploy-certificates-enterprise-ca.md)을 참조하세요.

이 스크립트에는 두 가지 버전이 있습니다.
- bash 스크립트 `cert.sh` 
- PowerShell 스크립트 `cert.ps1` 

 또한 두 스크립트 모두 `openssl.cnf` 구성 파일을 사용합니다. 스크립트를 사용하려면 `openssl.cnf` 및 `cert.sh` 또는 `cert.ps1`의 콘텐츠를 로컬 컴퓨터에 복사하세요.

스크립트는 다음 파일을 생성합니다.
- rootCA.crt/rootCA.key - 루트 CA 퍼블릭 인증서 및 프라이빗 키.
- interCA.crt/interCA.key - 중간 CA 퍼블릭 인증서 및 프라이빗 키
- interCA.pfx - 방화벽에 사용할 중간 CA pkcs12 패키지

> [!IMPORTANT]
> rootCA.key는 안전한 오프라인 위치에 저장해야 합니다. 스크립트는 유효 기간이 1,024일인 인증서를 생성합니다.
> 스크립트를 사용하려면 로컬 머신에 openssl 이진 파일이 설치되어 있어야 합니다. 자세한 내용은 https://www.openssl.org/을 참조하세요.
> 
인증서를 만든 후에 다음 위치에 배포합니다.
- rootCA.crt - 엔드포인트 머신에 배포합니다(퍼블릭 인증서에만 해당).
- interCA.pfx - Key Vault에서 인증서로 가져오고 방화벽 정책에 할당합니다.

### <a name="opensslcnf"></a>**openssl.cnf**
```
[ req ]
default_bits        = 4096
distinguished_name  = req_distinguished_name
string_mask         = utf8only
default_md          = sha512

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name
localityName                    = Locality Name
0.organizationName              = Organization Name
organizationalUnitName          = Organizational Unit Name
commonName                      = Common Name
emailAddress                    = Email Address

[ rootCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ interCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:1
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ server_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:false
keyUsage = critical, digitalSignature
extendedKeyUsage = serverAuth
```

###  <a name="bash-script---certsh"></a>Bash 스크립트 - cert.sh 
```bash
#!/bin/bash

# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 1024 -out rootCA.crt -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA" -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA"

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 1024 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password "pass:"

echo ""
echo "================"
echo "Successfully generated root and intermediate CA certificates"
echo "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
echo "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
echo "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
echo "================"
```

### <a name="powershell---certps1"></a>PowerShell - cert.ps1
```powershell
# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 3650 -out rootCA.crt -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA' -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA'

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 3650 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password 'pass:'

Write-Host ""
Write-Host "================"
Write-Host "Successfully generated root and intermediate CA certificates"
Write-Host "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
Write-Host "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
Write-Host "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
Write-Host "================"

```

## <a name="certificate-auto-generation-preview"></a>인증서 자동 생성(미리 보기)

비프로덕션 배포의 경우 다음 세 가지 리소스를 자동으로 만드는 Azure Firewall 프리미엄 인증 자동 생성 메커니즘을 사용할 수 있습니다.

- 관리 ID
- Key Vault
- 자체 서명된 루트 CA 인증서

새로운 미리 보기 관리 ID를 선택하기만 하면 프리미엄 정책에서 세 가지 리소스를 함께 연결하고 TLS 검사를 설정합니다. 

:::image type="content" source="media/premium-certificates/auto-gen-certs.png" alt-text="자동 생성된 인증서":::

## <a name="troubleshooting"></a>문제 해결

CA 인증서가 유효하지만 TLS 검사에서 FQDN 또는 URL에 액세스할 수 없는 경우 다음을 항목을 확인합니다.

- 웹 서버 인증서가 유효한지 확인합니다.  

- 루트 CA 인증서가 클라이언트 운영 체제에 설치되어 있는지 확인합니다.  

- 브라우저 또는 HTTPS 클라이언트에 유효한 루트 인증서가 있는지 확인합니다. Firefox 및 일부 다른 브라우저에는 특수 인증 정책이 있을 수 있습니다.  

- 애플리케이션 규칙의 URL 대상 유형이 올바른 경로 및 대상 HTML 페이지에 포함된 기타 하이퍼링크를 포함하는지 확인하세요. 전체 필수 URL 경로를 쉽게 검사하기 위해 와일드카드를 사용할 수 있습니다.  


## <a name="next-steps"></a>다음 단계

- [Azure Firewall 프리미엄 기능](premium-features.md)에 대해 자세히 알아보기
