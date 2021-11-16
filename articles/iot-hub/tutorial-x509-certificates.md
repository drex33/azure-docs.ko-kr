---
title: 자습서 - Azure IoT Hub에 대한 X.509 공개 키 인증서 이해 | Microsoft Docs
description: 자습서 - Azure IoT Hub에 대한 X.509 공개 키 인증서 이해
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 718ed8c89fbd5bccaff94b41e80b9b462faba1ee
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555663"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>자습서: X.509 공개 키 인증서 이해

X.509 인증서는 사용자, 컴퓨터, 서비스 또는 디바이스를 나타내는 디지털 문서입니다. CA(인증 기관), 하위 CA 또는 등록 기관에서 발급하며, 인증서 주체의 공개 키를 포함합니다. 안전하게 저장해야 하는 주체의 프라이빗 키는 여기에 포함되지 않습니다. 공개 키 인증서는 [RFC 5280](https://tools.ietf.org/html/rfc5280)에 설명되어 있습니다. 이러한 인증서는 디지털 서명되며, 일반적으로 다음 정보를 포함합니다.

* 인증서 주체에 대한 정보
* 주체의 프라이빗 키에 해당하는 공개 키
* 발급 CA에 대한 정보
* 지원되는 암호화 및/또는 디지털 서명 알고리즘
* 인증서의 해지 및 유효성 검사 상태를 확인하기 위한 정보

## <a name="certificate-fields"></a>인증서 필드

시간이 지남에 따라 세 가지 인증서 버전이 있습니다. 각 버전은 필드를 이전 버전에 추가합니다. 버전 3은 최신 버전이며, 버전 3 필드 외에 버전 1 및 버전 2 필드를 포함합니다. 버전 1에서 정의한 필드는 다음과 같습니다.

* **버전**: 인증서의 버전 번호를 식별하는 값(1, 2 또는 3)
* **일련 번호**: CA에서 발급한 각 인증서에 대한 고유 번호
* **CA 서명 알고리즘**: CA에서 인증서 내용에 서명하는 데 사용하는 알고리즘의 이름
* **발급자 이름**: 인증서의 발급 CA에 대한 DN(고유 이름)
* **유효 기간**: 인증서가 유효한 것으로 간주되는 기간
* **주체 이름**: 인증서에서 나타내는 엔터티의 이름
* **주체 공개 키 정보**: 인증서 주체가 소유하는 공개 키

버전 2에는 인증서 발급자에 대한 정보를 포함하기 위해 추가된 필드는 다음과 같습니다. 그러나 이러한 필드는 거의 사용되지 않습니다.

* **발급자 고유 ID**: CA에서 정의한 발급 CA에 대한 고유 식별자
* **주체 고유 ID**: 발급 CA에서 정의한 인증서 주체에 대한 고유 식별자

버전 3 인증서에 추가된 확장은 다음과 같습니다.

* **기관 키 식별자**: 다음 두 값 중 하나일 수 있습니다.
  * 해당 인증서를 발급한 CA 인증서의 CA 주체 및 일련 번호
  * 해당 인증서를 발급한 CA의 공개 키에 대한 해시
* **주체 키 식별자**: 현재 인증서의 공개 키에 대한 해시
* **키 사용**: 인증서를 사용할 수 있는 서비스를 정의합니다. 다음 값 중 하나 이상일 수 있습니다.
  * **디지털 서명**
  * **부인 방지**
  * **키 암호화**
  * **데이터 암호화**
  * **키 계약**
  * **키 인증서 서명**
  * **CRL 서명**
  * **암호화만**
  * **암호 해독만**
* **프라이빗 키 사용 기간**: 키 쌍의 프라이빗 키 부분에 대한 유효 기간
* **인증서 정책**: 인증서 주체를 확인하는 데 사용되는 정책
* **정책 매핑**: 한 조직의 정책을 다른 조직의 정책에 매핑합니다.
* **주체 대체 이름**: 주체에 대한 대체 이름 목록
* **발급자 대체 이름**: 발급 CA에 대한 대체 이름 목록
* **주체 디렉터리 특성**: X.500 또는 LDAP 디렉터리의 특성
* **기본 제약 조건**: 인증서가 CA 또는 사용자, 컴퓨터, 디바이스 또는 서비스에 발급되는지 여부를 지정할 수 있습니다. 이 확장에는 있을 수 있는 하위 CA의 수를 제한하는 경로 길이 제약 조건도 포함됩니다.
* **이름 제약 조건**: CA에서 발급한 인증서에서 허용되는 네임스페이스를 지정합니다.
* **정책 제약 조건**: CA 간의 정책 매핑을 금지하는 데 사용할 수 있습니다.
* **확장 키 사용**: 인증서의 공개 키를 **키 사용** 확장에서 식별된 용도 이상으로 사용할 수 있는 방법을 나타냅니다.
* **CRL 배포 지점**: 기본 CRL(인증서 해지 목록)이 게시되는 하나 이상의 URL을 포함합니다.
* **모든 정책 금지**: 하위 CA 인증서에서 **모든 발급 정책** OID(2.5.29.32.0)의 사용을 금지합니다.
* **Freshest CRL**: 발급 CA의 델타 CRL이 게시되는 하나 이상의 URL을 포함합니다.
* **기관 정보 액세스**: 발급 CA 인증서가 게시되는 하나 이상의 URL을 포함합니다.
* **주체 정보 액세스**: 인증서 주체에 대한 추가 세부 정보를 검색하는 방법에 대한 정보를 포함합니다.

## <a name="certificate-formats"></a>인증서 형식

인증서는 다양한 형식으로 저장할 수 있습니다. Azure IoT Hub 인증은 일반적으로 PEM 및 PFX 형식을 사용합니다.

### <a name="binary-certificate"></a>이진 인증서

DER ASN.1 인코딩을 사용하는 원시 형식 이진 인증서를 포함합니다.

### <a name="ascii-pem-format"></a>ASCII PEM 형식

PEM 인증서(.pem 확장명)에는 -----BEGIN CERTIFICATE-----로 시작하고 -----END CERTIFICATE-----로 끝나는 base64 인코딩 인증서를 포함합니다. PEM 형식은 매우 일반적이며, 특정 인증서를 업로드할 때 IoT Hub에 필요합니다.

### <a name="ascii-pem-key"></a>ASCII (PEM) 키

암호를 보호하는 데 사용되는 알고리즘에 대한 추가 메타데이터가 있는 base64 인코딩 DER 키를 포함합니다.

### <a name="pkcs7-certificate"></a>PKCS#7 인증서

서명되거나 암호화된 데이터를 전송하도록 설계된 형식입니다. [RFC 2315](https://tools.ietf.org/html/rfc2315)에서 정의됩니다. 전체 인증서 체인을 포함할 수 있습니다.

### <a name="pkcs8-key"></a>PKCS#8 키

[RFC 5208](https://tools.ietf.org/html/rfc5208)에서 정의된 프라이빗 키 저장소의 형식입니다.

### <a name="pkcs12-key-and-certificate"></a>PKCS#12 키 및 인증서

키 및 전체 인증서 체인을 저장하고 보호할 수 있는 복합 형식입니다. 일반적으로 .pfx 확장명과 함께 사용됩니다. PKCS#12는 PFX 형식과 동의어입니다.

## <a name="for-more-information"></a>참조 항목

자세한 내용은 다음 항목을 참조하세요.

* [X.509 인증서 전문 용어에 대한 layman의 가이드](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [IoT 업계의 X.509 CA 인증서에 대한 개념적 이해](./iot-hub-x509ca-concept.md)

## <a name="next-steps"></a>다음 단계

디바이스를 IoT Hub에 인증하는 데 사용할 수 있는 테스트 인증서를 생성하려면 다음 항목을 참조하세요.

* [Microsoft에서 제공하는 스크립트를 사용하여 테스트 인증서 만들기](tutorial-x509-scripts.md)
* [OpenSSL을 사용하여 테스트 인증서 만들기](tutorial-x509-openssl.md)
* [OpenSSL을 사용하여 자체 서명된 테스트 인증서 만들기](tutorial-x509-self-sign.md)

CA(인증 기관) 인증서 또는 하위 CA 인증서가 있고 이를 IoT 허브에 업로드하고 해당 인증서를 소유하고 있음을 증명하려면 [CA 인증서 소유 증명](tutorial-x509-prove-possession.md)을 참조하세요.