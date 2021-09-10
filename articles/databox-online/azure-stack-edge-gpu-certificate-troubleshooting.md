---
title: GPU를 사용한 Azure Stack Edge Pro 인증서 문제 해결| Microsoft Docs
description: Azure Stack Edge Pro GPU 디바이스의 인증서 오류 문제 해결에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: b5a607b9c0a64c6872ab511f13034e9d6eeb86d7
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113360509"
---
# <a name="troubleshooting-certificate-errors"></a>인증서 오류 문제 해결

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro 디바이스에 인증서를 설치할 때 발생하는 일반적인 인증서 오류 문제 해결 방법을 제공합니다.

## <a name="common-certificate-errors"></a>일반적인 인증서 오류

다음 표에서는 일반적인 인증서 오류와 이러한 오류에 대한 정보 및 가능한 해결 방법을 자세히 설명합니다.

> [!NOTE]
> &#8220;{0}, {1}, ... , {n}&#8221;이 나오는 경우는 위치 매개 변수를 나타냅니다. 위치 매개 변수는 사용 중인 인증서에 따라 값을 사용합니다.

| 오류 코드 | Description |
|---|---|
| CertificateManagement_UntrustedCertificate | 주체 이름이 {0}인 인증서에 인증서 체인이 끊어졌습니다. 이 인증서를 업로드하기 전에 서명 체인 인증서를 업로드합니다.|
| CertificateManagement_DeviceNotRegistered| 디바이스가 활성화되지 않았습니다. 활성화 후에만 지원 인증서를 업로드할 수 있습니다.|
| CertificateManagement_ExpiredCertificate | {0} 형식의 인증서가 만료되었거나 곧 만료될 예정입니다. 인증서 만료를 확인하고 필요한 경우 새 인증서를 가져옵니다.|
| CertificateManagement_FormatMismatch | 인증서 형식이 지원되지 않습니다. 인증서 형식을 확인하고 필요한 경우 새 인증서를 가져옵니다.  {0}이(가) 필요한데 {1}이(가) 나왔습니다. |
| CertificateManagement_GenericError | 인증서 관리 작업을 수행할 수 없습니다. 몇 분 안에 이 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요. |
| CertificateManagement_HttpsBindingFailure | 주체 이름이 {0}인 인증서로 보안 https 채널을 만들지 못했습니다. 업로드한 인증서를 확인하고 필요한 경우 새 인증서를 가져옵니다. 이 오류는 디바이스 노드 인증서에서 발생합니다.|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | 주체 이름이 {0}인 인증서에는 키 사용 인증서 서명이 없어야 합니다. 인증서의 키 사용을 확인하고 필요한 경우 새 인증서를 가져옵니다. 이 오류는 서명 체인 인증서에서 발생합니다. |
| CertificateManagement_IncorrectKeyNumber | 주체 이름이 {0}인 인증서에 잘못된 키 번호 {1}이(가) 있습니다. 인증서의 키 번호를 확인하고 필요한 경우 새 인증서를 가져옵니다.|
| CertificateManagement_InvalidP7b | 업로드된 인증서 파일이 잘못되었습니다.  인증서 형식을 확인하고 필요한 경우 새 인증서를 가져옵니다.|
| CertificateManagement_KeyAlgorithmNotRSA | 이 인증서는 RSA 키 알고리즘을 사용하지 않습니다. RSA 인증서만 지원됩니다. |
| CertificateManagement_KeySizeNotSufficient | 주체 이름이 {0}인 인증서의 키 크기 {1}이(가) 충분하지 않습니다. 최소 키 크기는 4096입니다.|
| CertificateManagement_MissingClientOid | 주체 이름이 {0}인 인증서에 클라이언트 인증 OID가 없습니다. 인증서 속성을 확인하고 필요한 경우 새 인증서를 가져옵니다.|
| CertificateManagement_MissingDigitalSignatureKeyUsage | 주체 이름이 {0}인 인증서의 키 사용에 디지털 서명이 없습니다. 인증서 속성을 확인하고 필요한 경우 새 인증서를 가져옵니다. |
| CertificateManagement_MissingKeyCertSignKeyUsage | 주체 이름이 {0}인 인증서의 키 사용에 인증서 서명이 없습니다. 인증서 속성을 확인하고 필요한 경우 새 인증서를 가져옵니다.|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | 주체 이름이 {0}인 인증서의 키 사용에 키 암호화가 없습니다. 인증서 속성을 확인하고 필요한 경우 새 인증서를 가져옵니다. |
| CertificateManagement_MissingServerOid | 주체 이름이 {0}인 인증서에 서버 인증 OID가 없습니다. 인증서 속성을 확인하고 필요한 경우 새 인증서를 가져옵니다.|
| CertificateManagement_NameMismatch | 인증서 유형이 일치하지 않습니다. {0} 범위가 필요한데 {1}이(가) 나왔습니다. 적절한 인증서를 업로드합니다.|
| CertificateManagement_NoPrivateKeyPresent | 주체 이름이 {0}인 인증서에 프라이빗 키가 없습니다. 프라이빗 키가 있는 .pfx 인증서를 업로드합니다.|
| CertificateManagement_NoRSACryptoPrivateKey | 주체 이름이 {0}인 인증서의 프라이빗 키에 액세스할 수 없습니다. 지원되는 드라이브를 사용하고 있는지 확인합니다. Microsoft RSA/Schannel 암호화 공급자만 지원됩니다. |
| CertificateManagement_NotSelfSignedCertificate | 주체 이름이 {0}인 인증서가 자체 서명되지 않았습니다. 루트 인증서는 자체 서명되어야 합니다. |
| CertificateManagement_NotSupportedOnVirtualAppliance | 이 작업은 가상 디바이스에서 지원되지 않습니다. 이 오류는 Tactical Cloud Appliance에서 실행되는 Data Box Gateway에서만 서명이 발생함을 나타냅니다. 이 오류는 Windows PowerShell을 통해 디바이스를 관리하는 동안 발생합니다.|
| CertificateManagement_SelfSignedCertificate | 주체 이름이 {0}인 인증서가 자체 서명되었습니다. 올바르게 서명된 인증서를 업로드합니다.|
| CertificateManagement_SignatureAlgorithmSha1 | 주체 이름이 {0}인 인증서에 지원되지 않는 서명 알고리즘이 있습니다. SHA1-RSA는 지원되지 않습니다. |
| CertificateManagement_SubjectNamesInvalid | 주체 이름이 {0}인 인증서에 {1} 인증서에 대한 올바른 주체 이름 또는 주체 대체 이름이 없습니다. 업로드한 인증서를 확인하고 필요한 경우 새 인증서를 가져옵니다. 또한 DNS 이름이 SANS 이름과 일치하는지도 확인해야 합니다.|
| CertificateManagement_UnreadableCertificate | {0} 형식의 인증서를 읽을 수 없습니다. 이 오류는 인증서를 읽을 수 없거나 손상된 경우에 발생합니다. 새 인증서를 가져옵니다.|
| CertificateSubjectNotFound | 주체 이름이 {0}인 인증서를 찾을 수 없습니다. 새 인증서를 가져옵니다.|
| CertificateRotationGenericFailure | 하나 이상의 인증서 순환이 실패했습니다. 몇 분 안에 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요.|
| CertificateImportFailure | 지문 {0}이(가) 있는 인증서를 노드 {1}에서 가져오지 않았습니다. 문제가 지속되면 Microsoft 지원에 문의하세요. |
| CertificateApplyFailure | 지문 {0}이(가) 있는 인증서를 노드 {1}에 적용하지 않았습니다. 문제가 지속되면 Microsoft 지원에 문의하세요.|
| NodeNotReachable | {0}에서 인증서의 유효성을 검사할 수 없습니다. 시스템 하드웨어 및 소프트웨어 상태를 확인합니다.|


## <a name="next-steps"></a>다음 단계

- [인증서 요구 사항](azure-stack-edge-gpu-certificate-requirements.md)을 검토합니다.
- [디바이스 로그, 진단 테스트를 사용하여 문제를 해결합니다](azure-stack-edge-gpu-troubleshoot.md).
