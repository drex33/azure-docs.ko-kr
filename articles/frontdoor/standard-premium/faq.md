---
title: 'Azure Front Door: 자주 묻는 질문'
description: 이 페이지에서는 Azure Front Door에 대해 자주 묻는 질문에 대한 답을 제공합니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 05/18/2021
ms.author: duau
ms.openlocfilehash: be1ffdecb727426f914a74970a720f4f19e51824
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538635"
---
# <a name="frequently-asked-questions-for-azure-front-door-standardpremium-preview"></a>Azure Front Door 표준/프리미엄에 대한 질문과 대답(미리 보기)

이 문서에서는 Azure Front Door의 특징 및 기능에 대한 일반적인 질문에 답변합니다.

## <a name="general"></a>일반

### <a name="what-is-azure-front-door"></a>Azure Front Door란?

Azure Front Door 표준/프리미엄은 빠르고 안정적이며 안전한 최신 클라우드 CDN으로, 지능형 위협 방지 기능을 포함합니다. 정적 및 동적 콘텐츠 가속, 전체 부하 분산 및 향상된 보안을 지능형 위협 방지와 함께 전체 하이퍼 규모의 애플리케이션, API, 웹사이트 및 클라우드 서비스에 제공합니다.

### <a name="what-features-does-azure-front-door-support"></a>Azure Front Door가 지원하는 기능은 무엇인가요?

Azure Front Door 지원:

* 정적 콘텐츠 및 동적 애플리케이션 가속
* TLS/SSL 오프로딩 및 엔드투엔드 TLS
* 웹 애플리케이션 방화벽
* 쿠키 기반 세션 선호도
* URL 경로 기반 라우팅
* 무료 인증서 및 여러 도메인 관리

지원되는 기능의 전체 목록은 [Azure Front Door 개요](overview.md)를 참조하세요.

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Azure Front Door와 Azure Application Gateway의 차이점은 무엇인가요?

Front Door와 Application Gateway는 모두 계층 7(HTTP/HTTPS) 부하 분산 장치이지만 Front Door가 전체 서비스라는 점이 주요 차이점입니다. Application Gateway는 지역 서비스입니다. Front Door를 사용하면 여러 지역에 걸쳐 다양한 배율 단위/클러스터/스탬프 단위 간에 부하를 분산할 수 있습니다. Application Gateway를 사용하면 배율 단위 내의 VM/컨테이너 간에 부하를 분산할 수 있습니다.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Front Door 뒤에 Application Gateway를 배포해야 하는 경우는 언제인가요?

Front Door 뒤에 Application Gateway를 사용해야 하는 주요 시나리오는 다음과 같습니다.

* Front Door는 전체적인 수준에서만 경로 기반 부하 분산을 수행할 수 있지만 VNET(가상 네트워크) 내에서 트래픽 부하를 분산하려는 경우에는 Application Gateway를 사용해야 합니다.
* Front Door는 VM/컨테이너 수준에서 작동하지 않으므로 연결 드레이닝을 수행할 수 없습니다. 그러나 Application Gateway를 사용하면 연결 드레이닝을 수행할 수 있습니다. 
* Front Door 뒤에 Application Gateway를 사용하면 100% TLS/SSL 오프로드를 수행하고 VNET(가상 네트워크) 내에서 HTTP 요청만 라우팅할 수 있습니다.
* Front Door 및 Application Gateway는 모두 세션 선호도를 지원합니다. Front Door는 사용자 세션에서 지정된 지역의 동일한 클러스터 또는 백엔드로 결과 트래픽을 보낼 수 있습니다. Application Gateway는 트래픽을 클러스터 내의 동일한 서버로 직접 결합할 수 있습니다.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Front Door 뒤에 Azure Load Balancer를 배포할 수 있나요?

Azure Front Door에는 트래픽을 라우팅할 공용 VIP 또는 공개적으로 사용할 수 있는 DNS 이름이 필요합니다. Front Door 뒤에 Azure Load Balancer를 배포하는 것은 일반적인 사용 사례입니다.

### <a name="what-protocols-does-azure-front-door-support"></a>Azure Front Door가 지원하는 프로토콜은 무엇인가요?

Azure Front Door는 HTTP, HTTPS, HTTP/2를 지원합니다.

### <a name="how-does-azure-front-door-support-http2"></a>Azure Front Door는 HTTP/2를 어떻게 지원하나요?

HTTP/2 프로토콜 지원은 Azure Front Door에 연결하는 클라이언트에만 사용할 수 있습니다. 백 엔드 풀의 백 엔드에 대한 통신은 HTTP/1.1을 통해 이루어집니다. HTTP/2 지원은 기본적으로 활성화되어 있습니다.

### <a name="what-resources-are-supported-today-as-part-of-an-origin-group"></a>현재 원본 그룹의 일부로 어떤 리소스가 지원되나요?

원본 그룹은 두 가지 원본 유형으로 구성될 수 있습니다.

- 퍼블릭 원본에는 스토리지 계정, App Service 앱, Kubernetes 인스턴스 또는 퍼블릭 연결이 있는 기타 사용자 지정 호스트 이름이 포함됩니다. 이 원본은 공용 IP 주소 또는 공개적으로 확인할 수 있는 DNS 호스트 이름을 통해 정의해야 합니다. 원본 그룹의 구성원은 멤버는 퍼블릭 연결이 있는 한 가용성 영역, 지역에서 배포되거나 Azure 외부에도 배포될 수 있습니다. 퍼블릭 원본은 Azure Front Door 표준 및 프리미엄 계층에서 지원됩니다.
- Azure Front Door(프리미엄)를 사용하는 경우 [Private Link 원본](concept-private-link.md)을 사용할 수 있습니다.

### <a name="what-regions-is-the-service-available-in"></a>어떤 지역에서 서비스를 사용할 수 있습니까?

Azure Front Door는 전체 서비스이며 특정 Azure 지역에 고정되지 않습니다. Front Door를 만드는 동안 지정해야 하는 유일한 위치는 리소스 그룹 위치입니다. 해당 위치는 기본적으로 리소스 그룹에 대한 메타데이터를 저장할 위치를 지정합니다. Front Door 리소스 자체는 전역 리소스로 만들어지고 구성은 모든 에지 위치에 전체적으로 배포됩니다. 

### <a name="where-are-the-edge-locations-for-azure-front-door"></a>Azure Front Door 에지 위치는 어디인가요?

Azure Front Door 에지 위치의 전체 목록은 [Azure Front Door 에지 위치](edge-locations.md)를 참조하세요.

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure Front Door는 내 애플리케이션에 대한 전용 배포인가요? 아니면 고객 간에 공유되나요?

Azure Front Door는 전역적으로 분산된 다중 테넌트 서비스입니다. Front Door 인프라는 모든 고객 간에 공유됩니다. Front Door 프로필을 만들어 애플리케이션에 필요한 특정 구성을 정의합니다. Front Door를 변경해도 기타 Front Door 구성에는 영향을 미치지 않습니다.

### <a name="is-http-https-redirection-supported"></a>HTTP->HTTPS 리디렉션이 지원되나요?

예. 실제로 Azure Front Door는 호스트, 경로, 쿼리 문자열 리디렉션 및 URL 리디렉션의 일부를 지원합니다. [URL 리디렉션](concept-rule-set-url-redirect-and-rewrite.md)에 대해 자세히 알아보세요. 

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>내 백 엔드에 Azure Front Door만 액세스하도록 잠그려면 어떻게 해야 할까요?

특정 Front Door 인스턴스의 트래픽만 수락하도록 애플리케이션을 잠그는 최고의 방법은 프라이빗 엔드포인트를 통해 애플리케이션을 게시하는 것입니다. Front Door와 애플리케이션 사이의 네트워크 트래픽은 VNet과 Microsoft 백본 네트워크의 프라이빗 링크를 통과하며 공용 인터넷 노출을 방지합니다.

자세한 정보는 [프라이빗 링크를 사용한 Front Door 원본 보안](concept-private-link.md)을 참조하세요.  

특정 Front Door의 트래픽만 수락하도록 애플리케이션을 잠그는 다른 방법은 백엔드에 대한 IP ACL 설정입니다. 그런 다음, Front Door가 보낸 헤더의 값 'X-Azure-FDID'로 백엔드의 트래픽을 제한합니다. 이러한 단계는 아래에 자세히 설명되어 있습니다.

* 백 엔드에 IP ACL을 구성하여 Azure Front Door의 백 엔드 IP 주소 공간 및 Azure 인프라 서비스의 트래픽만 허용합니다. 백엔드를 ACLing하려면 아래의 IP 세부 정보를 참조하세요.
 
    * Front Door의 IPv4 백엔드 IP 주소 범위에 대한 [Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519)에 있는 *AzureFrontDoor.Backend* 섹션을 참조하세요. 서비스 태그 *AzureFrontDoor.Backend* 를 [네트워크 보안 그룹](../../virtual-network/network-security-groups-overview.md#security-rules)에서 사용할 수도 있습니다.
    * 가상화된 호스트 IP 주소를 통한 Azure의 [기본 인프라 서비스](../../virtual-network/network-security-groups-overview.md#azure-platform-considerations): `168.63.129.16` 및 `169.254.169.254`

    > [!WARNING]
    > Front Door의 백 엔드 IP 공간은 나중에 변경될 수 있지만, 여기서는 그러기 전에 [Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519)와 통합합니다. 변경 또는 업데이트에 대한 [Azure IP 범위 및 서비스 태그](https://www.microsoft.com/download/details.aspx?id=56519)를 구독하는 것이 좋습니다.

* API 버전 `2020-01-01` 이상을 사용하여 Front Door에 가져오기 작업을 수행합니다. API 호출에서 `frontdoorID` 필드를 찾습니다. 수신 헤더 '**X-Azure-FDID**'는 Front Door가 `frontdoorID` 필드의 값과 함께 백엔드로 전송합니다. 또한 Front Door 포털 페이지의 개요 섹션에서 `Front Door ID` 값을 찾을 수 있습니다. 

* 백 엔드 웹 서버에 규칙 필터링을 적용하여 결과 ‘X-Azure-FDID’ 헤더 값을 기준으로 트래픽을 제한합니다.

  [Microsoft IIS(인터넷 정보 서비스)](https://www.iis.net/)에 대한 예제는 다음과 같습니다.

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Front Door의 수명 동안 애니캐스트 IP가 변경될 수 있나요?

Front Door의 프런트 엔드 애니캐스트 IP는 Front Door 수명 동안 일반적으로 변경되지 않으며 고정적인 상태로 유지될 수 있습니다. 그러나 IP가 유지된다는 **보장은 없습니다**. IP에 대한 직접적인 종속성을 취하지 마십시오.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Azure Front Door는 고정적인 IP 또는 전용 IP를 지원하나요?

아니요, Azure Front Door는 현재 고정적 또는 전용 프런트 엔드 애니캐스트 IP를 지원하지 않습니다. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Azure Front Door는 X-Forwarded-For 헤더를 지원하나요?

예, Azure Front Door는 X-Forwarded-For, X-Forwarded-Host, X-Forwarded-Proto 헤더를 지원합니다. X-Forwarded-For 헤더가 이미 있는 경우에는 Front Door가 클라이언트 소켓 IP를 해당 헤더에 추가합니다. 없는 경우에는 클라이언트 소켓 IP를 포함한 헤더를 값으로 추가합니다. X-Forwarded-Host 및 X-Forwarded-Proto의 경우 값이 재정의됩니다.  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Azure Front Door를 배포하는 데 얼마나 걸리나요? Front Door는 업데이트 중에도 작동하나요?

대부분의 규칙 엔진 구성 업데이트는 20분 이내에 완료됩니다. 업데이트가 완료되는 즉시 규칙이 적용된다고 볼 수 있습니다. 

 > [!Note]  
  > 대부분의 사용자 지정 TLS/SSL 인증서 업데이트는 전체적으로 배포되는 데 몇 분에서 한 시간까지 걸립니다.

경로 또는 백엔드 풀에 대한 모든 업데이트는 원활히 진행되며 새 구성이 올바른 경우 가동 중지 시간이 없습니다. '관리형 Azure Front Door'에서 '사용자 고유의 인증서 사용'으로 전환하거나 그 반대로 전환하지 않는 한 인증서 업데이트로 인한 중단은 발생하지 않습니다.


## <a name="configuration"></a>구성

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure Front Door는 가상 네트워크 내에서 트래픽 부하를 분산하거나 라우팅할 수 있나요?

Azure Front Door(표준)에는 트래픽을 라우팅하기 위해 공용 IP 또는 공개적으로 확인 가능한 DNS 이름이 필요합니다. Azure Front Door는 가상 네트워크의 리소스로 직접 라우팅할 수 없습니다. 공용 IP와 함께 Application Gateway 또는 Azure Load Balancer를 사용하여 이 문제를 해결할 수 있습니다.

Azure Front Door(프리미엄)는 [Private Link 원본](concept-private-link.md)에 대한 트래픽 라우팅을 지원합니다.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Azure Front Door에 대한 여러 시간 제한 및 제한 사항이 어떻게 되나요?

[Azure Front Door에 대한 시간 제한 및 제한 사항](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits) 관련 문서를 통해 확인해 보세요.

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>규칙이 Front Door 규칙 엔진에 추가된 후 적용되기까지 얼마나 걸리나요?

대부분의 규칙 엔진 구성 업데이트는 20분 이내에 완료됩니다. 업데이트가 완료되는 즉시 규칙이 적용된다고 볼 수 있습니다. 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-front-door-behind-my-azure-cdn"></a>Azure CDN을 Front Door 프로필 뒤에, 또는 Front Door를 Azure CDN 뒤에 구성할 수 있나요?

두 서비스 모두 요청에 응답할 때 동일한 Azure 에지 사이트를 사용 하므로 Azure Front Door 및 Azure CDN과 함께 구성할 수 없습니다. 

## <a name="performance"></a>성능

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Azure Front Door는 고가용성 및 확장성을 어떻게 지원하나요?

Azure Front Door는 애플리케이션의 확장성 요구에 맞게 용량을 크게 분산하는 전체로 분산된 다중 테넌트 플랫폼입니다. Microsoft의 글로벌 네트워크 에지에서 제공되는 Front Door는 전체 애플리케이션을 장애 조치(failover)하거나 지역 또는 다른 클라우드에서 개별 마이크로 서비스를 장애 조치할 수 있도록 지원하는 전역 부하 분산 기능을 제공합니다.

## <a name="tls-configuration"></a>TLS 구성

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Azure Front Door가 지원하는 TLS 버전은 무엇인가요?

2019년 9월 이후에 만들어진 모든 Front Door 프로필은 기본적으로 TLS 1.2 이상을 사용합니다.

Front Door는 TLS 버전 1.0, 1.1 및 1.2를 지원합니다. TLS 1.3은 아직 지원되지 않습니다.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Azure Front Door에서 지원되는 인증서는 무엇인가요?

Front Door 사용자 지정 도메인에서 HTTPS 프로토콜을 사용하도록 설정하려면 Azure Front Door에서 관리하는 인증서를 선택하거나 고유한 인증서를 사용할 수 있습니다.
Front Door 관리형 옵션은 Digicert를 통해 표준 TLS/SSL 인증서를 프로비저닝하고 Front Door의 키 자격 증명 모음에 저장합니다. 사용자 고유의 인증서를 사용하도록 선택하는 경우 지원되는 CA에서 인증서를 온보딩할 수 있으며, 여기에 표준 TLS, 확장된 유효성 검사 인증서 또는 와일드카드 인증서를 사용할 수 있습니다. 자체 서명된 인증서는 지원되지 않습니다.

### <a name="does-front-door-support-autorotation-of-certificates"></a>Front Door는 인증서의 자동 순환을 지원하나요?

Front Door 관리형 인증서 옵션의 경우 인증서는 Front Door에 의해 자동 순환됩니다. Front Door 관리 인증서를 사용하고 있으며 인증서 만료 날짜가 60일 미만인 경우 지원 티켓을 제출합니다.

사용자 지정 TLS/SSL 인증서의 경우 자동 순환은 지원되지 않습니다. 지정된 사용자 지정 도메인에 대해 처음 설정하는 방법과 마찬가지로 Key Vault의 올바른 인증서 버전을 Front Door로 지정할 필요가 있습니다. Front Door 서비스 주체가 여전히 Key Vault에 액세스할 수 있는지 확인합니다. Front Door가 업데이트된 인증서 출시 작업으로 인해 인증서의 주체 이름 또는 SAN이 변경되지 않으면 프로덕션 중단 시간이 발생하지 않습니다.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Azure Front Door에서 지원되는 현재 암호 그룹은 무엇인가요?

TLS 1.2의 경우 지원되는 암호 그룹은 다음과 같습니다. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

TLS1.0/1.1이 설정된 사용자 지정 도메인을 사용하면 다음과 같은 암호 도구 모음이 지원됩니다.

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>TLS 프로토콜 버전을 제어하는 TLS 정책을 구성할 수 있나요?

Azure Portal 또는 [Azure REST API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion)를 사용하여 사용자 지정 도메인 HTTPS 설정의 Azure Front Door 최소 TLS 버전을 구성할 수 있습니다. 현재는 1.0과 1.2 중에서 선택할 수 있습니다.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>특정 암호 모음만 지원하도록 Front Door를 구성할 수 있나요?

아니요, 특정 암호 모음에 대한 Front Door 구성은 지원되지 않습니다. 인증 기관(예: Verisign, Entrust 또는 Digicert)에서 사용자 고유의 사용자 지정 TLS/SSL 인증서를 얻을 수 있습니다. 그런 다음 인증서를 생성할 때 특정 암호 그룹을 인증서에 표시합니다. 

### <a name="does-front-door-support-ocsp-stapling"></a>Front Door가 OCSP 스테이플링을 지원하나요?

예, OCSP 스테이플링은 기본적으로 Front Door에 의해 지원되며 구성이 필요하지 않습니다.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure Front Door에서 백 엔드에 대해 트래픽의 재암호화를 지원하나요?

예, Azure Front Door는 TLS/SSL 오프로드와 백엔드 트래픽을 다시 암호화하는 엔드투엔드 TLS를 지원합니다. 백엔드 연결은 공용 IP를 통해 이루어지므로 HTTPS를 전달 프로토콜로 사용하도록 Front Door를 구성하는 것이 좋습니다.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Front Door는 HTTPS 연결 시 백 엔드에서 자체 서명된 인증서를 지원하나요?

아니요, 자체 서명된 인증서는 Front Door에서 지원되지 않으며 다음 두 가지 모두에 제한이 적용됩니다.

* **백엔드**: HTTPS 또는 HTTPS 상태 프로브로 트래픽을 전달하거나 캐싱을 사용하도록 설정된 라우팅 규칙을 위해 원본에서 캐시를 채우는 경우 자체 서명된 인증서를 사용할 수 없습니다.
* **프런트엔드**: 사용자 지정 도메인에서 HTTPS를 사용하도록 설정하기 위해 사용자 고유의 사용자 지정 TLS/SSL 인증서를 사용하는 경우 자체 서명된 인증서를 사용할 수 없습니다.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>백 엔드에 대한 HTTPS 트래픽이 실패하는 이유는 무엇인가요?

상태 프로브 또는 전달 요청을 사용해 백 엔드에 대한 HTTPS 연결에 성공했지만 HTTPS 트래픽이 실패하는 경우에는 두 가지 이유가 있을 수 있습니다.

* **인증서 주체 이름 불일치**: HTTPS 연결의 경우 Front Door는 백 엔드 호스트 이름과 일치하는 주체 이름을 가진 유효한 CA에서 인증서를 제공할 것을 요구합니다. 예를 들어 백엔드 호스트 이름이 `myapp-centralus.contosonews.net`로 설정되고, TLS 핸드셰이크 중 백엔드에서 제공하는 인증서의 주체 이름에 `myapp-centralus.contosonews.net` 또는 `*myapp-centralus*.contosonews.net`이 없는 경우가 있습니다. 이 경우 Front Door는 연결을 거부하고 오류가 발생합니다. 
    * **솔루션**: 규정 준수 관점에서는 권장되지 않지만 Front Door의 인증서 주체 이름 확인을 사용하지 않도록 설정하여 이 오류를 해결할 수 있습니다. 이 옵션은 Azure Portal의 설정 및 API의 BackendPoolsSettings에서 찾을 수 있습니다.
* **잘못된 CA의 백엔드 호스팅 인증서**: Front Door를 사용하는 백엔드에서는 [유효한 인증 기관](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT)의 인증서만 사용할 수 있습니다. 내부 CA 또는 자체 서명된 인증서는 허용되지 않습니다.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Azure Front Door에 클라이언트/상호 인증을 사용할 수 있나요?

아니요. Azure Front Door는 [RFC 5246](https://tools.ietf.org/html/rfc5246)의 클라이언트/상호 인증을 도입한 TLS 1.2를 지원하지만, 현재까지는 Azure Front Door에서 클라이언트/상호 인증을 지원하지 않습니다.

## <a name="diagnostics-and-logging"></a>진단 및 로깅

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Azure Front Door에 어떤 유형의 메트릭 및 로그를 사용할 수 있나요?

로그 및 기타 진단 기능에 대한 자세한 정보는 Front Door에 대한 모니터링 메트릭 및 로그를 참조하세요.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>진단 로그에서 보존 정책은 무엇인가요?

진단 로그는 고객 스토리지 계정으로 전달되고 고객은 기본 설정에 따라 보존 정책을 설정할 수 있습니다. 진단 로그를 이벤트 허브 또는 Azure Monitor 로그에도 전송할 수 있습니다. 자세한 정보는 [Azure Front Door 로깅](how-to-logs.md)을 참조하세요.

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Azure Front Door에 감사 로그를 사용하려면 어떻게 해야 할까요?

Azure Front Door에 감사 로그를 사용할 수 있습니다. 포털에서 감사 로그에 액세스하려면 Front Door의 메뉴 페이지에서 **활동 로그** 를 선택합니다. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Azure Front Door에 경고를 설정할 수 있나요?

예, Azure Front Door는 경고를 지원합니다. 경고는 메트릭에 대해 구성됩니다. 

## <a name="billing"></a>결제

### <a name="will-i-be-billed-for-the-azure-front-door-resources-that-are-disabled"></a>사용하지 않도록 설정된 Azure Front Door 리소스에 대한 요금이 청구되나요?

Front Door 프로필과 같은 Azure Front Door 리소스는 사용하지 않도록 설정된 경우 요금이 청구되지 않습니다.

## <a name="next-steps"></a>다음 단계

[Front Door 표준/프리미엄을 만드는](create-front-door-portal.md) 방법을 알아봅니다.
