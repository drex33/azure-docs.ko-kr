---
title: Azure PowerShell을 통해 Azure Stack Edge Pro GPU에 대한 인증서 만들기 | Microsoft Docs
description: Azure PowerShell cmdlet을 사용하여 Azure Stack Edge Pro GPU 디바이스에 대한 인증서를 만드는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: fc7ec5574e0f0223a66bc4e42ce9029db0ae4fc6
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113364073"
---
# <a name="use-certificates-with-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에서 인증서 사용

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure PowerShell cmdlet을 사용하여 사용자 고유의 인증서를 만드는 절차를 설명합니다. 이 문서에는 Azure Stack Edge 디바이스에서 사용자 고유의 인증서를 가져올 계획인 경우 따라야 하는 지침이 포함되어 있습니다.

인증서는 디바이스와 해당 디바이스에 액세스하는 클라이언트 간의 통신을 신뢰할 수 있으며, 암호화된 정보를 올바른 서버에 전송하고 있음을 나타냅니다. Azure Stack Edge 디바이스가 처음 구성되면 자체 서명된 인증서가 자동으로 생성됩니다. 필요에 따라 사용자 고유의 인증서를 가져올 수 있습니다. 

다음 방법 중 하나를 사용하여 디바이스에 대한 사용자 고유의 인증서를 만들 수 있습니다.

 - Azure PowerShell cmdlet을 사용합니다.
 - Azure Stack Hub Readiness Checker 도구를 사용하여 인증 기관에서 인증서를 발급하는 데 도움이 되는 CSR(인증서 서명 요청)을 만듭니다. 

이 문서에서는 Azure PowerShell cmdlet을 사용하여 사용자 고유의 인증서를 만드는 방법에 대해서만 설명합니다. 

## <a name="prerequisites"></a>필수 구성 요소

사용자 고유의 인증서를 가져오기 전에 다음을 확인합니다.

- [Azure Stack Edge 디바이스에서 사용할 수 있는 인증서 유형](azure-stack-edge-gpu-certificates-overview.md)을 잘 알고 있어야 합니다.
- [각 인증서 유형에 대한 인증서 요구 사항](azure-stack-edge-gpu-certificate-requirements.md)을 검토해야 합니다.


## <a name="create-certificates"></a>인증서 만들기

다음 섹션에서는 서명 체인 및 엔드포인트 인증서를 만드는 절차에 대해 설명합니다.


### <a name="certificate-workflow"></a>인증서 워크플로

사용자 환경에서 작동하는 디바이스에 대한 인증서를 만드는 정의된 방법이 있습니다. IT 관리자가 제공하는 인증서를 사용할 수 있습니다. 

**개발 또는 테스트용으로만 Windows PowerShell을 사용하여 인증서를 로컬 시스템에 만들 수도 있습니다.** 클라이언트에 대한 인증서를 만드는 동안 다음 지침을 따릅니다.

1. 다음 유형의 인증서를 만들 수 있습니다.

    - 단일 FQDN(정규화된 도메인 이름)으로 사용할 수 있는 단일 인증서를 만듭니다. 예를 들어 *mydomain.com* 입니다.
    - 주 도메인 이름과 여러 하위 도메인을 보호하는 와일드카드 인증서도 만듭니다. 예를 들어 * *.mydomain.com* 입니다.
    - 여러 도메인 이름을 단일 인증서에 포함하는 SAN(주체 대체 이름) 인증서를 만듭니다. 

2. 사용자 고유의 인증서를 가져오는 경우 서명 체인에 대한 루트 인증서가 필요합니다. [서명 체인 인증서 만들기](#create-signing-chain-certificate) 단계를 참조하세요.

3. 다음으로 어플라이언스, Blob 및 Azure Resource Manager의 로컬 UI에 대한 엔드포인트 인증서를 만들 수 있습니다. 어플라이언스, Blob 및 Azure Resource Manager에 대해 세 개의 개별 인증서를 만들거나, 세 개의 엔드포인트 모두에 대해 하나의 인증서를 만들 수 있습니다. 자세한 단계는 [서명 및 엔드포인트 인증서 만들기](#create-signed-endpoint-certificates)를 참조하세요.

4. 세 개의 개별 인증서를 만드는지, 아니면 하나의 인증서를 만드는지에 관계없이 각 인증서 유형에 대해 제공된 지침에 따라 SN(주체 이름) 및 SAN(주체 대체 이름)을 지정합니다. 

### <a name="create-signing-chain-certificate"></a>서명 체인 인증서 만들기

이러한 인증서는 관리자 모드에서 실행되는 Windows PowerShell을 통해 만듭니다. **이 방법으로 만든 인증서는 개발 또는 테스트 용으로만 사용해야 합니다.**

서명 체인 인증서는 한 번만 만들면 됩니다. 다른 엔드포인트 인증서는 서명을 위해 이 인증서를 참조합니다.
 

```azurepowershell
$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=RootCert" -HashAlgorithm sha256 -KeyLength 2048 -CertStoreLocation "Cert:\LocalMachine\My" -KeyUsageProperty Sign -KeyUsage CertSign
```


### <a name="create-signed-endpoint-certificates"></a>서명된 엔드포인트 인증서 만들기

이러한 인증서는 관리자 모드에서 실행되는 Windows PowerShell을 통해 만듭니다.

다음 예제에서는 다음 항목을 사용하여 디바이스에 대한 엔드포인트 인증서를 만듭니다.
    - 디바이스 이름: `DBE-HWDC1T2`
    - DNS 도메인: `microsoftdatabox.com`

디바이스에 대한 인증서를 만들려면 디바이스에 대한 이름 및 DNS 도메인으로 바꿉니다.
 
**Blob 엔드포인트 인증서**

개인 저장소에서 Blob 엔드포인트에 대한 인증서를 만듭니다.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "*.blob.$AppName.$domain" -Subject "CN=*.blob.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**Azure Resource Manager 엔드포인트 인증서**

개인 저장소에서 Azure Resource Manager 엔드포인트에 대한 인증서를 만듭니다.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "management.$AppName.$domain","login.$AppName.$domain" -Subject "CN=management.$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**디바이스 로컬 웹 UI 인증서**

개인 저장소에서 디바이스의 로컬 웹 UI에 대한 인증서를 만듭니다.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

**모든 엔드포인트에 대한 단일 다중 SAN 인증서**

개인 저장소에서 모든 엔드포인트에 대한 단일 인증서를 만듭니다.

```azurepowershell
$AppName = "DBE-HWDC1T2"
$domain = "microsoftdatabox.com"
$DeviceSerial = "HWDC1T2"

New-SelfSignedCertificate -Type Custom -DnsName "$AppName.$domain","$DeviceSerial.$domain","management.$AppName.$domain","login.$AppName.$domain","*.blob.$AppName.$domain" -Subject "CN=$AppName.$domain" -KeyExportPolicy Exportable  -HashAlgorithm sha256 -KeyLength 2048  -CertStoreLocation "Cert:\LocalMachine\My" -Signer $cert -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1")
```

인증서가 만들어지면 다음 단계는 인증서를 Azure Stack Edge Pro GPU 디바이스에 업로드하는 것입니다.

## <a name="next-steps"></a>다음 단계

[디바이스에 인증서를 업로드합니다](azure-stack-edge-gpu-manage-certificates.md).
