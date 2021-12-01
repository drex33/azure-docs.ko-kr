---
title: Azure 직접 라우팅 인프라 요구 사항 - Azure Communication Services
description: Azure Communication Services 직접 라우팅 구성에 대한 인프라 요구 사항 숙지
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: pstn
ms.openlocfilehash: b03779f9c56d2ebcc4d070165cdf29a54e78d269
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386902"
---
# <a name="azure-direct-routing-infrastructure-requirements"></a>Azure 직접 라우팅 인프라 요구 사항 

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

 
이 문서에서는 Azure 직접 라우팅 배포를 계획할 때 명심해야 하는 인프라, 라이선스 및 SBC(Session Border Controller) 연결 세부 정보에 대해 설명합니다.


## <a name="infrastructure-requirements"></a>인프라 요구 사항
다음 표에는 Azure 직접 라우팅을 배포하기 위해 지원되는 SBC, 도메인 및 기타 네트워크 연결 요구 사항에 대한 인프라 요구 사항이 나와 있습니다.  

|인프라 요구 사항|다음이 필요합니다.|
|:--- |:--- |
|SBC(세션 경계 컨트롤러)|지원되는 SBC입니다. 자세한 내용은 [지원되는 SBC](#supported-session-border-controllers-sbcs)를 참조하세요.|
|SBC에 연결된 전화 통신 트렁크|SBC에 연결된 하나 이상의 전화 통신 트렁크입니다. SBC는 한쪽 끝에서 직접 라우팅을 통해 Azure Communication Service에 연결합니다. 또한 SBC는 PBX, 아날로그 전화 통신 어댑터 등과 같은 타사 전화 통신 엔터티에도 연결할 수 있습니다. SBC에 연결된 모든 PSTN 연결 옵션이 작동합니다. (SBC에 대한 PSTN 트렁크 구성은 SBC 공급업체 또는 트렁크 공급자에게 문의하세요.)|
|Azure 구독|ACS 리소스와 Communication Services 리소스에 대한 구성 및 연결을 만드는 데 사용하는 Azure 구독입니다.|
|Communication Services 액세스 토큰|호출을 수행하려면 `voip` 범위를 포함하는 유효한 액세스 토큰이 필요합니다. [액세스 토큰](../identity-model.md#access-tokens) 참조|
|SBC에 대한 공용 IP 주소|SBC에 연결하는 데 사용할 수 있는 공용 IP 주소입니다. SBC의 유형에 따라 SBC에서 NAT를 사용할 수 있습니다.|
|SBC의 FQDN(정규화된 도메인 이름)|FQDN의 도메인 부분이 Microsoft 365 또는 Office 365 조직의 등록된 도메인과 일치하지 않는 SBC의 FQDN입니다. 자세한 내용은 [SBC 도메인 이름](#sbc-domain-names)을 참조하세요.|
|SBC의 퍼블릭 DNS 항목 |SBC FQDN을 공용 IP 주소에 매핑하는 퍼블릭 DNS 항목입니다. |
|SBC의 신뢰할 수 있는 퍼블릭 인증서 |Azure 직접 라우팅과의 모든 통신에 사용되는 SBC의 인증서입니다. 자세한 내용은 [SBC의 신뢰할 수 있는 퍼블릭 인증서](#public-trusted-certificate-for-the-sbc)를 참조하세요.|
|SIP 신호 및 미디어를 위한 방화벽 IP 주소 및 포트 |SBC는 클라우드의 다음 서비스와 통신합니다.<br/><br/>신호를 처리하는 SIP 프록시<br/>미디어를 처리하는 미디어 프로세서<br/><br/>이러한 두 서비스는 이 문서의 뒷부분에 설명된 Microsoft Cloud에 별도의 IP 주소가 있습니다.


## <a name="sbc-domain-names"></a>SBC 도메인 이름

Office 365가 없는 고객은 퍼블릭 인증서를 가져올 수 있는 모든 도메인 이름을 사용할 수 있습니다.

다음 표에서는 테넌트에 등록된 DNS 이름의 예, 해당 이름을 SBC의 FQDN(정규화된 도메인 이름)으로 사용할 수 있는지 여부, 유효한 FQDN 이름의 예를 보여줍니다.

|DNS 이름|SBC FQDN에 사용 가능|FQDN 이름의 예|
|:--- |:--- |:--- |
contoso.com|예|**올바른 이름:**<br/>sbc1.contoso.com<br/>ssbcs15.contoso.com<br/>europe.contoso.com|
|contoso.onmicrosoft.com|아니요|SBC 이름에는 *.onmicrosoft.com 도메인을 사용할 수 없음

Office 365 고객의 경우 SBC 도메인 이름이 Office 365 테넌트의 도메인에 등록된 이름과 일치하지 않습니다. 다음은 Office 365 및 Azure Communication Service 공존성의 예입니다.

|Office 365에 등록된 도메인|Teams의 SBC FQDN 예|Azure Communication Services의 SBC FQDN 이름 예|
|:--- |:--- |:--- |
**contoso.com**(두 번째 수준 도메인)|**sbc.contoso.com**(두 번째 수준 도메인의 이름)|**sbc.acs.contoso.com**(세 번째 수준 도메인의 이름)<br/>**sbc.fabrikam.com**(다른 도메인 내의 모든 이름)|
|**o365.contoso.com**(세 번째 수준 도메인)|**sbc.o365.contoso.com**(세 번째 수준 도메인의 이름)|**sbc.contoso.com**(두 번째 수준 도메인의 이름)<br/>**sbc.acs.o365.contoso.com**(네 번째 수준 도메인의 이름)<br/>**sbc.fabrikam.com**(다른 도메인 내의 모든 이름)

SBC 페어링은 Communication Services 리소스 수준에서 작동합니다. 즉, 단일 Communication Services 리소스에 여러 개의 SBC를 연결할 수 있지만 단일 SBC를 둘 이상의 Communication Services 리소스에 연결할 수는 없습니다. 여러 리소스에 연결하려면 고유한 SBC FQDN이 필요합니다.

## <a name="public-trusted-certificate-for-the-sbc"></a>SBC의 신뢰할 수 있는 퍼블릭 인증서

CSR(인증 서명 요청)을 생성하여 SBC의 인증서를 요청하는 것이 좋습니다. SBC의 CSR을 생성하는 방법에 대한 자세한 내용은 SBC 공급업체에서 제공하는 상호 연결 지침 또는 설명서를 참조하세요. 

  > [!NOTE]
  > 대부분의 CA(인증 기관)에서는 크기가 2,048 이상인 프라이빗 키를 요구합니다. CSR을 생성할 때 이 점을 반드시 기억하세요.

인증서는 CN(일반 이름) 또는 SAN(주체 대체 이름) 필드에 SBC FQDN이 있어야 합니다. 인증서는 중간 공급자가 아닌 인증 기관에서 직접 발급해야 합니다.

또는 Communication Services 직접 라우팅이 CN 및/또는 SAN의 와일드카드를 지원하고, 와일드카드가 표준 [RFC HTTP Over TLS](https://tools.ietf.org/html/rfc2818#section-3.1)를 준수해야 합니다. 

예제에서는 SBC FQDN `sbc.contoso.com`과 일치하지만 `sbc.test.contoso.com`과는 일치하지 않는 `\*.contoso.com`을 사용합니다.

인증서는 다음 루트 인증 기관 중 하나에서 생성해야 합니다.

- AffirmTrust
- AddTrust External CA Root
- Baltimore CyberTrust Root*
- Buypass
- Cybertrust
- Class 3 Public Primary Certification Authority
- Comodo Secure Root CA
- Deutsche Telekom 
- DigiCert Global Root CA
- DigiCert High Assurance EV Root CA
- Entrust
- GlobalSign
- Go Daddy
- GeoTrust
- Verisign, Inc. 
- SSL.com
- Starfield
- Symantec Enterprise Mobile Root for Microsoft 
- SwissSign
- Thawte Timestamping CA
- Trustwave
- TeliaSonera 
- T-Systems International GmbH(Deutsche Telekom)
- QuoVadis
- USERTrust RSA Certification Authority
- Hongkong Post Root CA 1,2,3
- Sectigo 루트 CA

Microsoft는 고객의 요청에 따라 인증 기관을 추가하기 위해 노력하고 있습니다. 

## <a name="sip-signaling-fqdns"></a>SIP 신호: FQDN 

Communication Services 직접 라우팅의 연결점은 다음 세 가지 FQDN입니다.

- **sip.pstnhub.microsoft.com** – 글로벌 FQDN – 먼저 시도해야 합니다. SBC에서 이 이름을 확인하도록 요청을 보내면 Microsoft Azure DNS 서버가 SBC에 할당된 기본 Azure 데이터 센터를 가리키는 IP 주소를 반환합니다. 할당은 데이터 센터의 성능 메트릭과 SBC에 대한 지리적 근접성을 바탕으로 이루어집니다. 반환된 IP 주소는 1차 FQDN에 해당합니다.
- **sip2.pstnhub.microsoft.com** – 2차 FQDN – 두 번째 우선 순위 영역에 지리적으로 매핑됩니다.
- **sip3.pstnhub.microsoft.com** – 3차 FQDN – 세 번째 우선 순위 영역에 지리적으로 매핑됩니다.

이러한 3개의 FQDN을 순서대로 배치해야 합니다.

- 최적의 환경을 제공합니다(첫 번째 FQDN을 쿼리하여 로드를 줄이고 할당된 SBC 데이터 센터에 가장 가깝게 위치).
- 일시적인 문제가 발생한 데이터 센터에 대한 SBC의 연결이 설정된 경우 장애 조치(failover)를 제공합니다. 자세한 내용은 아래의 [장애 조치(failover) 메커니즘](#failover-mechanism-for-sip-signaling)을 참조하세요.  

FQDN – sip.pstnhub.microsoft.com, sip2.pstnhub.microsoft.com 및 sip3.pstnhub.microsoft.com – 다음 IP 주소 중 하나로 확인됩니다.

- `52.112.0.0/14 (IP addresses from 52.112.0.1 to 52.115.255.254)`
- `52.120.0.0/14 (IP addresses from 52.120.0.1 to 52.123.255.254)`

신호 처리를 위해 이러한 모든 IP 주소 범위에 대한 방화벽 포트를 열어 해당 주소에서 들어오고 나가는 트래픽을 허용합니다. 방화벽에서 DNS 이름을 지원하는 경우 FQDN `sip-all.pstnhub.microsoft.com`은 이러한 모든 IP 주소를 확인합니다. 

## <a name="sip-signaling-ports"></a>SIP 신호: 포트

Communication Services Azure 직접 라우팅에 다음 포트를 사용합니다.

|트래픽|시작|대상|원본 포트|대상 포트|
|:--- |:--- |:--- |:--- |:--- |
|SIP/TLS|SIP 프록시|SBC|1024 – 65535|SBC에 정의됨(Office 365 GCC High/DoD 전용 포트 5061을 사용해야 함)|
SIP/TLS|SBC|SIP 프록시|SBC에 정의됨|5061|

### <a name="failover-mechanism-for-sip-signaling"></a>SIP 신호 처리를 위한 장애 조치(Failover) 메커니즘

SBC는 DNS 쿼리를 수행하여 sip.pstnhub.microsoft.com을 확인합니다. SBC 위치 및 데이터 센터 성능 메트릭에 따라 1차 데이터 센터가 선택됩니다. 1차 데이터 센터에서 문제가 발생할 경우 SBC는 두 번째로 할당된 데이터 센터를 확인하는 sip2.pstnhub.microsoft.com을 시도하며, 드문 경우지만 두 지역의 데이터 센터를 사용할 수 없는 경우 SBC는 3차 데이터 센터 IP를 제공하는 마지막 FQDN(sip3.pstnhub.microsoft.com)을 다시 시도합니다.

## <a name="media-traffic-ip-and-port-ranges"></a>미디어 트래픽: IP 및 포트 범위

미디어 트래픽은 미디어 프로세서라고 하는 별도의 서비스를 오갑니다. Communication Services용 미디어 프로세서를 게시하는 순간에는 모든 Azure IP 주소를 사용할 수 있습니다. [전체 주소 목록](https://www.microsoft.com/download/details.aspx?id=56519)을 다운로드합니다.

### <a name="port-range"></a>포트 범위
미디어 프로세서의 포트 범위는 다음 표에 나와 있습니다. 

|트래픽|시작|대상|원본 포트|대상 포트|
|:--- |:--- |:--- |:--- |:--- |
|UDP/SRTP|미디어 프로세서|SBC|3478-3481 및 49152 – 53247|SBC에 정의됨|
|UDP/SRTP|SBC|미디어 프로세서|SBC에 정의됨|3478-3481 및 49152 – 53247|

  > [!NOTE]
  > Microsoft는 SBC에서 동시 호출당 두 개 이상의 포트를 권장합니다.


## <a name="media-traffic-media-processors-geography"></a>미디어 트래픽: 미디어 프로세서 지리

미디어 트래픽은 미디어 프로세서라는 구성 요소를 통해 흐릅니다. 미디어 프로세서는 SIP 프록시와 동일한 데이터 센터에 배치됩니다.
- 미국(미국 서부 및 미국 동부 데이터 센터)
- 유럽(암스테르담 및 더블린 데이터 센터)
- 아시아(싱가포르 및 홍콩 SAR 데이터 센터)
- 오스트레일리아(오스트레일리아 동부 및 남동부 데이터 센터)
- 일본(일본 동부 및 서부 데이터 센터)



## <a name="media-traffic-codecs"></a>미디어 트래픽: 코덱

### <a name="leg-between-sbc-and-cloud-media-processor-or-microsoft-teams-client"></a>SBC와 클라우드 미디어 프로세서나 Microsoft Teams 클라이언트 간의 레그.

세션 경계 컨트롤러와 클라우드 미디어 프로세서 간의 레그에 있는 Azure 직접 라우팅 인터페이스는 다음과 같은 코덱을 사용할 수 있습니다.

- SILK, G.711, G.722, G.729

제품에서 원치 않는 코덱을 제외하여 세션 경계 컨트롤러의 특정 코덱을 강제로 사용할 수 있습니다.

### <a name="leg-between-communication-services-calling-sdk-app-and-cloud-media-processor"></a>Communication Services 호출 SDK 앱과 클라우드 미디어 프로세서 간 레그

클라우드 미디어 프로세서와 Communication Services 호출 SDK 앱 간의 레그에는 G.722가 사용됩니다. Microsoft는 이 레그에 대해 더 많은 코덱을 추가하는 작업을 진행하고 있습니다. 

## <a name="supported-session-border-controllers-sbcs"></a>지원되는 SBC(세션 경계 컨트롤러)

- [Azure Communication Services 직접 라우팅에 대해 인증된 세션 경계 컨트롤러](./certified-session-border-controllers.md)

## <a name="next-steps"></a>다음 단계

### <a name="conceptual-documentation"></a>개념 설명서

- [전화 통신 개념](./telephony-concept.md)
- [Azure Communication Services의 전화 번호 유형](./plan-solution.md)
- [세션 테두리 컨트롤러 페어링 및 음성 라우팅 구성](./direct-routing-provisioning.md)
- [가격](../pricing.md)

### <a name="quickstarts"></a>빠른 시작

- [휴대폰에 전화 걸기](../../quickstarts/voice-video-calling/pstn-call.md)