---
title: Azure TLS 인증서 변경
description: Azure TLS 인증서 변경
services: security
author: msmbaldwin
tags: azure-resource-manager
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 09/13/2021
ms.author: mbaldwin
ms.openlocfilehash: 01acde63371fea6a8c3f752caf31fa883e5e617e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641221"
---
# <a name="azure-tls-certificate-changes"></a>Azure TLS 인증서 변경  

Microsoft는 다른 루트 CA(인증 기관)의 TLS 인증서를 사용하도록 Azure 서비스를 업데이트하 고 있습니다. 이와 같은 변경의 이유는 현재 CA 인증서가 CA/브라우저 포럼 기준 요구 사항 중 하나를 준수하지 않기 때문으로, 2021년 2월 15일에 철회됩니다.

## <a name="when-will-this-change-happen"></a>언제 변경되나요?

기존 Azure 엔드포인트는 2020년 8월 13일 이후 단계적으로 전환되었습니다. 새로 만든 모든 Azure TLS/SSL 엔드포인트에는 새로운 루트 CA에 연결하는 업데이트된 인증서가 포함되어 있습니다.

모든 Azure 서비스는 해당 변경의 영향을 받습니다. 특정 서비스에 대한 추가 세부 정보:

- Azure AD([Azure Active Directory](../../active-directory/index.yml)) 서비스는 2020년 7월 7일에 이 전환을 시작했습니다.
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) 및 [DPS](../../iot-dps/index.yml)는 Baltimore CyberTrust Root CA에 그대로 남아 있지만 중간 Ca는 변경됩니다. [자세한 내용을 보려면 여기를 클릭하세요](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456).
- [Azure Storage](../../storage/index.yml)에 대 한 자세한 [내용을 보려면 여기를 클릭](https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-critical-changes-are-almost-here-and-why-you/ba-p/2741581)하세요.
- [Azure Cache for Redis](../../azure-cache-for-redis/index.yml)는 Baltimore CyberTrust Root CA에 그대로 남아 있지만 중간 CA는 변경됩니다. [자세한 내용을 보려면 여기를 클릭하세요](../../azure-cache-for-redis/cache-whats-new.md).
- Azure Instance Metadata Service는 Baltimore CyberTrust Root CA에 그대로 남아 있지만 중간 CA는 변경됩니다. [자세한 내용을 보려면 여기를 클릭하세요](/answers/questions/172717/action-required-for-attested-data-tls-with-azure-i.html).

> [!IMPORTANT]
> Azure Storage에 연결 하려고 할 때 연결 오류가 발생 하지 않도록 하려면이 변경 후 응용 프로그램을 업데이트 해야 할 수 있습니다.
https://techcommunity.microsoft.com/t5/azure-storage/azure-storage-tls-critical-changes-are-almost-here-and-why-you/ba-p/2741581
## <a name="what-is-changing"></a>변경되는 내용

현재 Azure 서비스에서 사용하는 대부분의 TLS 인증서는 다음과 같은 루트 CA에 연결됩니다.

| CA의 일반 이름 | 지문(SHA1) |
|--|--|
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

Azure 서비스에서 사용하는 TLS 인증서는 다음과 같은 루트 CA 중 하나에 연결됩니다.

| CA의 일반 이름 | 지문(SHA1) |
|--|--|
| [DigiCert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert Global Root CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST Root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20ECC%20Root%20Certificate%20Authority%202017.crt) | 999a64c37ff47d9fab95f14769891460eec4c3c5 |

## <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>기존의 중간 지문을 언제 사용 중지할 수 있나요?

현재 CA 인증서는 2021년 2월 15일까지 해지되지 *않습니다*. 이 날짜가 지나면 코드에서 이전 지문을 제거할 수 있습니다.

이 날짜가 변경될 경우 새로운 해지 날짜에 대한 알림이 제공됩니다.

## <a name="will-this-change-affect-me"></a>이 변경이 내게 영향을 주나요? 

**대부분의 Azure 고객에게는 영향을 주지 않을 것** 으로 예상됩니다.  그러나 허용되는 CA 목록을 명시적으로 지정하는 경우 애플리케이션에 영향을 줄 수 있습니다. 이 방법을 인증서 고정이라고 합니다.

다음과 같은 방법으로 애플리케이션이 영향을 받는지 알아볼 수 있습니다.

- [여기](https://www.microsoft.com/pki/mscorp/cps/default.htm)에 있는 Microsoft IT TLS CA의 지문, 일반 이름 및 기타 인증서 속성을 소스코드에서 검색합니다. 일치하는 항목이 있으면 애플리케이션이 영향을 받습니다. 이 문제를 해결하려면 새 CA를 포함하도록 소스 코드를 업데이트합니다. 모범 사례에 따라 긴급하게 CA를 추가 또는 편집할 수 있어야 합니다. 업계 규정을 준수하려면 CA 인증서를 7일 이내에 바꿔야 하므로, 고정 방식을 사용하는 고객은 신속하게 대응해야 합니다.

- Azure API 또는 다른 Azure 서비스와 통합되는 애플리케이션이 있고 해당 애플리케이션이 인증서 고정을 사용하는지 확실하지 않은 경우 애플리케이션 공급업체에 확인하세요.

- Azure 서비스와 통신하는 여러 운영 체제 및 언어 런타임은 다음과 같은 새 루트를 사용하여 인증서 체인을 올바르게 빌드하는 추가 단계가 필요할 수 있습니다.
    - **Linux**: 여러 배포판에서는 /etc/ssl/certs에 CA를 추가해야 합니다. 자세한 지침은 해당 배포판의 설명서를 참조하세요.
    - **Java**: 위에 나열된 CA가 Java 키 저장소에 들어 있어야 합니다.
    - **연결이 끊어진 환경에서 실행되는 Windows**: 연결이 끊어진 환경에서 실행되는 시스템은 새로운 루트를 신뢰할 수 있는 루트 인증 기관 저장소에 추가하고, 중간 인증서를 중간 인증 기관 저장소에 추가해야 합니다.
    - **Android**: 사용하는 디바이스의 설명서와 Android 버전을 확인합니다.
    - **기타 하드웨어 디바이스, 특히 IoT**: 디바이스 제조업체에 문의하세요.

- 특정 CRL(인증서 해지 목록) 다운로드 및/또는 OCSP(온라인 인증서 상태 프로토콜) 확인 위치에 대한 아웃바운드 호출만 허용하도록 방화벽 규칙이 설정된 환경의 경우 다음 CRL 및 OCSP URL을 허용해야 합니다.

    - http://crl3&#46;digicert&#46;com
    - http://crl4&#46;digicert&#46;com
    - http://ocsp&#46;digicert&#46;com
    - http://www&#46;d-trust&#46;net
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com
    - http://www&#46;microsoft&#46;com/pkiops

## <a name="next-steps"></a>다음 단계

추가 문의는 [지원](https://azure.microsoft.com/support/options/)을 통해 하시기 바랍니다.
