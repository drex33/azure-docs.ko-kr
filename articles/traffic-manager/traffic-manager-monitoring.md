---
title: Azure Traffic Manager 엔드포인트 모니터링 | Microsoft Docs
description: 이 문서는 Traffic Manager가 엔드포인트 모니터링 및 자동 엔드포인트 장애 조치를 사용하여 Azure 고객이 고가용성 애플리케이션을 어떻게 배포할 수 있도록 하는지 이해하는 데 도움이 됩니다.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2021
ms.author: duau
ms.openlocfilehash: ce4ff8cfa4bee895e17cd7ad22c54ff777d210ff
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435185"
---
# <a name="traffic-manager-endpoint-monitoring"></a>Traffic Manager 엔드포인트 모니터링

Azure Traffic Manager에는 기본 제공된 엔드포인트 모니터링 및 자동 엔드포인트 장애 조치가 포함됩니다. 이 기능을 통해 Azure 지역 장애를 포함하여 엔드포인트 장애에 대해 복원력 있는 고가용성 애플리케이션을 제공할 수 있습니다.

## <a name="configure-endpoint-monitoring"></a>엔드포인트 모니터링 구성

엔드포인트 모니터링을 구성하려면 Traffic Manager 프로필에서 다음 설정을 지정해야 합니다.

* **프로토콜**. HTTP, HTTPS 또는 TCP를 Traffic Manager가 상태 검사를 위해 엔드포인트를 검색할 때 사용하는 프로토콜로 선택합니다. HTTPS 모니터링은 TLS/SSL 인증서가 유효한지 여부를 확인하지 않으며 인증서가 있는지만 확인합니다.
* **포트**. 요청에 사용될 포트를 선택합니다.
* **경로**. 이 구성 설정은 경로 설정을 지정해야 하는 HTTP 및 HTTPS 프로토콜에 대해서만 유효합니다. TCP 모니터링 프로토콜에 대해 이 설정을 제공하면 오류가 발생합니다. HTTP 및 HTTPS 프로토콜의 경우 모니터링 기능이 액세스하는 웹 페이지 또는 파일의 상대 경로와 이름을 제공합니다. 슬래시(/)는 상대 경로로 유효한 항목입니다. 이 값은 파일이 루트 디렉터리(기본값)에 있음을 나타냅니다.
* **사용자 지정 헤더 설정**. 이 구성 설정을 사용하면 Traffic Manager가 프로필 아래의 엔드포인트에 보내는 상태 확인에 특정 HTTP 헤더를 추가할 수 있습니다. 사용자 지정 헤더는 해당 프로필에서 모든 엔드포인트에 적용할 수 있는 프로필 수준에서 및/또는 해당 엔드포인트에만 적용할 수 있는 엔드포인트 수준에서 지정할 수 있습니다. 다중 테넌트 환경에서 엔드포인트의 상태 검사를 위해 사용자 지정 헤더를 사용할 수 있습니다. 이렇게 하면 호스트 헤더를 지정하여 대상으로 올바르게 라우팅할 수 있습니다. HTTP(S) 요청 및 프로세스에서 다르게 발생한 Traffic Manager를 식별하는 데 사용할 수 있는 고유한 헤더를 추가하여 이 설정을 사용할 수도 있습니다. 쉼표로 구분된 최대 8개의 헤더:값 쌍을 지정할 수 있습니다. 예: "헤더1:값1, 헤더2: 값2". 

   > 참고: 사용자 지정 `Host` 헤더에 별표 문자(\*)를 사용하는 것은 지원되지 않습니다.

* **예상 상태 코드 범위**. 이 설정을 사용하면 200-299, 301-301 형식으로 여러 성공 코드 범위를 지정할 수 있습니다. 상태 검사가 수행될 때 이러한 상태 코드가 엔드포인트의 응답으로 수신되는 경우 Traffic Manager는 해당 엔드포인트를 정상으로 표시합니다. 최대 8개의 상태 코드 범위를 지정할 수 있습니다. 이 설정은 HTTP 및 HTTPS 프로토콜과 모든 엔드포인트에 적용할 수 있습니다. 이 설정은 Traffic Manager 프로필 수준이며, 기본적으로 값 200은 성공 상태 코드로 정의됩니다.
* **프로브 간격**. 이 값은 Traffic Manager 검색 에이전트에서 엔드포인트의 상태를 검사하는 빈도를 지정합니다. 여기서 30초(일반 검색) 및 10초(빠른 검색)의 두 값을 지정할 수 있습니다. 값을 지정하지 않으면 프로필이 기본값인 30초로 설정됩니다. 빠른 검색 가격 책정에 대한 자세한 내용은 [Traffic Manager 가격 책정](https://azure.microsoft.com/pricing/details/traffic-manager) 페이지를 참조하세요.
* **허용되는 오류 수**. 이 값은 Traffic Manager 검색 에이전트가 해당 엔드포인트를 비정상 상태로 표시하기 전에 허용하는 오류 수를 지정합니다. 해당 값의 범위는 0에서 9 사이일 수 있습니다. 값이 0이면 단일 모니터링 오류로 인해 해당 엔드포인트가 비정상 상태로 표시될 수 있습니다. 값을 지정하지 않으면 기본값 3이 사용됩니다.
* **프로브 시간 제한**. 이 속성은 엔드포인트에 대한 상태 프로브 검사를 실패로 간주하기 전에 Traffic Manager 검색 에이전트가 기다려야 하는 시간을 지정합니다. 검색 간격이 30초로 설정된 경우 제한 시간 값을 5-10초 사이로 설정할 수 있습니다. 값을 지정하지 않으면 기본값 10초가 사용됩니다. 검색 간격이 10초로 설정된 경우 제한 시간 값을 5-9초 사이로 설정할 수 있습니다. 제한 시간 값을 지정하지 않으면 기본값 9초가 사용됩니다.

    ![Traffic Manager 엔드포인트 모니터링](./media/traffic-manager-monitoring/endpoint-monitoring-settings.png)

    **그림: Traffic Manager 엔드포인트 모니터링**

## <a name="how-endpoint-monitoring-works"></a>엔드포인트 모니터링의 작동 방식

모니터링 프로토콜이 HTTP 또는 HTTPS로 설정된 경우 Traffic Manager 검색 에이전트는 주어진 프로토콜, 포트 및 상대 경로를 사용하여 엔드포인트에 GET 요청을 수행합니다. 검색 에이전트가 200-OK 응답 또는 **예상 상태 코드 \*범위** 에 구성된 응답 중 하나를 수신하면 엔드포인트가 정상으로 간주됩니다. 응답이 다른 값이거나 제한 시간 내에 응답이 수신되지 않으면 Traffic Manager 검색 에이전트는 허용되는 오류 수 설정에 따라 다시 시도합니다. 이 설정이 0이면 다시 시도가 수행되지 않습니다. 연속 오류 수가 허용되는 오류 수 설정보다 크면 해당 엔드포인트는 비정상 상태로 표시됩니다.

모니터링 프로토콜이 TCP인 경우 Traffic Manager는 지정된 포트를 사용하여 TCP 연결 요청을 만듭니다. 엔드포인트가 연결 설정에 대한 응답으로 요청에 응답하면 해당 상태 검사가 성공으로 표시됩니다. Traffic Manager 검색 에이전트는 TCP 연결을 다시 설정합니다. 응답이 다른 값이거나 제한 시간 내에 응답이 수신되지 않는 경우 Traffic Manager 검색 에이전트는 허용되는 오류 수 설정에 따라 다시 시도합니다. 이 설정은 0이면 다시 시도되지 않습니다. 연속 오류 수가 허용되는 오류 수 설정보다 크면 해당 엔드포인트는 비정상 상태로 표시됩니다.

모든 경우에 Traffic Manager는 다중 위치에서 프로브합니다. 연속적인 실패는 각 지역 내에서 발생하는 상황을 결정합니다. 따라서 엔드포인트가 검색 간격에 사용된 설정보다 더 높은 빈도로 Traffic Manager에서 상태 프로브를 수신합니다.

>[!NOTE]
>HTTP 또는 HTTPS 모니터링 프로토콜의 경우 엔드포인트 쪽의 일반적인 방식은 애플리케이션 내에서 사용자 지정 페이지를 구현하는 것입니다(예: /health.aspx). 이 경로를 모니터링에 사용하여 성능 카운터 검사 또는 데이터베이스 가용성 확인과 같은 애플리케이션별 검사를 수행할 수 있습니다. 이러한 사용자 지정 검사에 따르면 페이지는 적절한 HTTP 상태 코드를 반환합니다.

Traffic Manager 프로필의 모든 엔드포인트는 모니터링 설정을 공유합니다. 서로 다른 엔드포인트에 대해 다양한 모니터링 설정을 사용해야 하는 경우 [중첩된 Traffic Manager 프로필](traffic-manager-nested-profiles.md#example-5-per-endpoint-monitoring-settings)을 만들 수 있습니다.

## <a name="endpoint-and-profile-status"></a>엔드포인트 및 프로필 상태

Traffic Manager 프로필 및 엔드포인트를 사용하거나 사용하지 않도록 설정할 수 있습니다. 그러나 Traffic Manager의 자동 설정 및 프로세스에 따라 엔드포인트 상태가 변경될 수도 있습니다.

### <a name="endpoint-status"></a>엔드포인트 상태

특정 엔드포인트를 사용하거나 사용하지 않도록 설정할 수 있습니다. 정상 상태인 기본 서비스는 영향을 받지 않습니다. 엔드포인트의 상태를 변경하여 Traffic Manager 프로필에서의 엔드포인트 가용성을 제어합니다. 엔드포인트 상태를 사용하지 않도록 설정되면 Traffic Manager는 상태를 검사하지 않으며 엔드포인트는 DNS 응답에 포함되지 않습니다.

### <a name="profile-status"></a>프로필 상태

프로필 상태 설정을 사용하여 특정 프로필을 사용하거나 사용하지 않도록 설정할 수 있습니다. 엔드포인트 상태가 단일 엔드포인트에 영향을 주지만 프로필 상태는 모든 엔드포인트를 포함한 전체 프로필에 영향을 줍니다. 프로필을 사용하지 않도록 설정하면 엔드포인트의 상태가 검사되지 않으며 DNS 응답에 엔드포인트가 포함되지 않습니다. [NXDOMAIN](https://tools.ietf.org/html/rfc2308) 응답 코드가 DNS 쿼리에 대해 반환됩니다.

### <a name="endpoint-monitor-status"></a>엔드포인트 모니터 상태

엔드포인트 모니터 상태는 엔드포인트의 현재 상태를 보여 주는 Traffic Manager 생성 값입니다. 이 설정은 수동으로 변경할 수 없습니다. 엔드포인트 모니터 상태는 엔드포인트 모니터링의 결과와 구성된 엔드포인트 상태가 조합된 것입니다. 엔드포인트 모니터 상태의 가능한 값은 다음 표에 나와 있습니다.

| 프로필 상태 | 엔드포인트 상태 | 엔드포인트 모니터 상태 | 참고 |
| --- | --- | --- | --- |
| 사용 안 함 |사용 |비활성 |프로필이 사용되지 않도록 설정되었습니다. 엔드포인트 상태는 Enabled여도 프로필 상태(Disabled)가 우선적으로 적용됩니다. 사용하지 않도록 설정된 프로필의 엔드포인트는 모니터링되지 않습니다. NXDOMAIN 응답 코드가 DNS 쿼리에 대해 반환됩니다. |
| &lt;일부&gt; |사용 안 함 |사용 안 함 |엔드포인트가 사용되지 않습니다. 사용하지 않도록 설정된 엔드포인트는 모니터링되지 않습니다. 엔드포인트는 트래픽을 수신하지 않으므로 DNS 응답에 포함되지 않습니다. |
| 사용 |사용 |온라인 |엔드포인트는 모니터링되며 정상 상태입니다. DNS 응답에 포함되며 트래픽을 수신할 수 있습니다. |
| 사용 |사용 |성능 저하됨 |엔드포인트 모니터링 상태 검사에 실패했습니다. 엔드포인트가 DNS 응답에 포함되지 않으며 트래픽을 수신하지 않습니다. <br>모든 엔드포인트가 저하된 경우는 예외입니다. 이 경우 모두 쿼리 응답에서 반환된 것으로 간주됩니다).</br>|
| 사용 |사용 |CheckingEndpoint |엔드포인트가 모니터링되지만 첫 번째 프로브 결과가 아직 수신되지 않았습니다. CheckingEndpoint는 일반적으로 프로필에서 엔드포인트를 추가하거나 사용하도록 설정한 직후에 나타나는 일시적 상태입니다. 이 상태의 엔드포인트는 DNS 응답에 포함되며 트래픽을 수신할 수 있습니다. |
| 사용 |사용 |중지됨 |엔드포인트가 가리키는 웹앱이 실행되고 있지 않습니다. 웹앱 설정을 확인합니다. 이 상태는 엔드포인트가 중첩된 엔드포인트 형식이고 자식 프로필이 사용되지 않거나 비활성 상태인 경우에도 발생할 수 있습니다. <br>중지됨 상태의 엔드포인트는 모니터링되지 않습니다. DNS 응답에 포함되지 않으며 트래픽을 수신하지 않습니다. 모든 엔드포인트가 저하된 경우는 예외입니다. 이 경우 모두 쿼리 응답에서 반환된 것으로 간주됩니다.</br>|

중첩된 엔드포인트의 엔드포인트 모니터 상태가 계산되는 방식에 대한 자세한 내용은 [중첩 Traffic Manager 프로필](traffic-manager-nested-profiles.md)을 참조하세요.

>[!NOTE]
> 중지된 엔드포인트 상태는 웹 애플리케이션이 표준 계층 이상에서 실행되고 있지 않은 경우 App Service에서 발생할 수 있습니다. 자세한 내용은 [App Service와 Traffic Manager 통합](../app-service/web-sites-traffic-manager.md)을 참조하세요.

### <a name="profile-monitor-status"></a>프로필 모니터 상태

프로필 모니터 상태는 구성된 프로필 상태와 모든 엔드포인트에 대한 엔드포인트 모니터 상태 값을 조합한 것입니다. 다음 표에서는 이 옵션에 사용할 수 있는 값을 설명합니다.

| 프로필 상태(구성된 대로) | 엔드포인트 모니터 상태 | 프로필 모니터 상태 | 참고 |
| --- | --- | --- | --- |
| 사용 안 함 |&lt;일부&gt; 또는 정의된 엔드포인트가 없는 프로필 |사용 안 함 |프로필이 사용되지 않도록 설정되었습니다. |
| 사용 |하나 이상의 엔드포인트 상태가 Degraded입니다. |성능 저하됨 |엔드포인트에 주의가 필요한지 확인하려면 개별 엔드포인트 상태 값을 검토합니다. |
| 사용 |하나 이상의 엔드포인트 상태가 Online입니다. Degraded 상태의 엔드포인트가 없습니다. |온라인 |서비스가 트래픽을 허용합니다. 추가적인 조치가 필요하지 않습니다. |
| 사용 |하나 이상의 엔드포인트 상태가 “CheckingEndpoint”입니다. Online 또는 Degraded 상태인 엔드포인트가 없습니다. |CheckingEndpoints |이러한 전환 상태는 프로필을 만들거나 사용하도록 설정하면 발생합니다. 처음으로 엔드포인트 상태가 검사됩니다. |
| 사용 |프로필의 모든 엔드포인트 상태는 Disabled 또는 Stopped이거나 프로필에 정의된 엔드포인트가 없습니다. |비활성 |엔드포인트가 활성 상태가 아니지만 프로필은 여전히 Enabled입니다. |

## <a name="endpoint-failover-and-recovery"></a>엔드포인트 장애 조치 및 복구

Traffic Manager는 비정상 엔드포인트를 포함하는 모든 엔드포인트의 상태를 정기적으로 검사합니다. Traffic Manager는 엔드포인트가 정상 상태가 되는지 감지한 후 다시 회전에 포함시킵니다.

다음 이벤트 중 하나가 발생할 경우 엔드포인트가 비정상 상태입니다.

- 모니터링 프로토콜이 HTTP 또는 HTTPS인 경우:
    - 200이 아닌 응답 또는 **예상 상태 코드 범위** 설정에 지정된 상태 범위를 포함하지 않는 응답이 수신됩니다. (다른 2xx 코드 또는 301/302 리디렉션 포함).
- 모니터링 프로토콜이 TCP인 경우: 
    - 연결 설정을 시도하기 위해 Traffic Manager에서 보낸 SYN 요청에 대한 응답으로 ACK 또는 SYN-ACK 이외의 응답이 수신됩니다.
- 시간 초과. 
- 엔드포인트에 도달하지 못하게 하는 다른 모든 연결 문제입니다.

실패한 검사의 문제 해결에 대한 자세한 내용은 [Azure Traffic Manager의 Degraded 상태 문제 해결](traffic-manager-troubleshooting-degraded.md)을 참조하세요. 

다음 그림의 타임라인은 다음 설정이 있는 Traffic Manager 엔드포인트의 모니터링 프로세스에 대한 자세한 설명입니다.

* 모니터링 프로토콜은 HTTP입니다.
* 검색 간격은 30초입니다.
* 허용되는 오류 수는 3입니다.
* 제한 시간 값은 10초입니다.
* DNS TTL은 30초입니다.

![Traffic Manager 엔드포인트 장애 조치 및 장애 복구 순서](./media/traffic-manager-monitoring/timeline.png)

**그림: Traffic Manager 엔드포인트 장애 조치(failover) 및 복구 순서**

1. **GET**. 각 엔드포인트에 대해 Traffic Manager 모니터링 시스템은 모니터링 설정에 지정된 경로에서 GET 요청을 수행합니다.
2. **200 정상 또는 사용자 지정 코드 범위가 Traffic Manager 프로필 모니터링 설정을 지정했습니다**. 모니터링 시스템은 HTTP 200 OK 또는 모니터링 설정에 지정된 범위의 상태 코드가 10초 이내에 반환될 것으로 예상합니다. 이 응답을 수신하면 서비스를 사용할 수 있는 것으로 인식합니다.
3. **확인 간격 30초**. 엔드포인트 상태 검사가 30초마다 반복됩니다.
4. **서비스를 사용할 수 없음**. 서비스를 사용할 수 없게 됩니다. Traffic Manager는 다음 상태 검사까지 알지 못합니다.
5. **모니터링 경로 액세스 시도**. 모니터링 시스템이 GET 요청을 수행하지만 10초의 제한 시간 내에 응답을 받지 못합니다. 30초 간격으로 3회 더 시도합니다. 그 중 한 번의 시도가 성공하면 시도 횟수가 다시 설정됩니다.
6. **상태가 Degraded로 설정**. 네 번째 연속 실패 후 모니터링 시스템은 사용할 수 없는 엔드포인트를 Degraded로 표시합니다.
7. **트래픽이 다른 엔드포인트로 우회됨**. Traffic Manager DNS 이름 서버가 업데이트되고 Traffic Manager에 의해 DNS 쿼리에 대한 응답으로 엔드포인트가 더 이상 반환되지 않습니다. 새 연결은 사용 가능한 다른 엔드포인트로 전달됩니다. 그러나 이 엔드포인트를 포함하는 이전 DNS 응답은 재귀 DNS 서버 및 DNS 클라이언트에 의해 계속 캐시될 수 있으므로 클라이언트는 DNS 캐시가 만료될 때까지 엔드포인트를 계속 사용합니다. DNS 캐시가 만료되면 클라이언트는 새 DNS 쿼리를 만들고 다른 엔드포인트로 전달됩니다. 캐시 기간은 Traffic Manager 프로필의 TTL 설정에 의해 제어됩니다(예: 30초).
8. **계속 상태 검사**. Traffic Manager는 Degraded 상태에 있는 엔드포인트의 상태를 계속해서 검사합니다. Traffic Manager는 엔드포인트가 정상 상태가 되면 이를 감지합니다.
9. **서비스가 다시 온라인 상태가 됩니다**. 서비스를 사용할 수 있습니다. 모니터링 시스템에서 다음 상태 검사를 수행할 때까지 엔드포인트는 Traffic Manager에서 Degraded 상태로 유지됩니다.
10. **서비스에 대한 트래픽 다시 시작**. Traffic Manager는 GET 요청을 전송하고 200 OK 상태 응답을 수신합니다. 서비스는 정상 상태로 돌아왔습니다. Traffic Manager 이름 서버는 업데이트되고 DNS 응답에서 서비스의 DNS 이름을 전달하기 시작합니다. 다른 엔드포인트를 반환하는 캐시된 DNS 응답이 만료되고 다른 엔드포인트에 대한 기존 연결이 종료되면 트래픽이 엔드포인트로 돌아갑니다.

    > [!NOTE]
    > Traffic Manager는 DNS 수준에서 작동하므로 모든 엔드포인트에 대한 기존 연결에 영향을 미칠 수 없습니다. 엔드포인트 간에 트래픽을 전달할 때는(프로필 설정을 변경하거나 장애 조치 또는 장애 복구 중) Traffic Manager가 사용 가능한 엔드포인트에 새 연결을 전달합니다. 그러나 다른 엔드포인트는 해당 세션이 종료될 때까지 기존 연결을 통해 트래픽을 계속 수신할 수 있습니다. 트래픽이 기존 연결에서 비워지도록 하기 위해 애플리케이션은 각 엔드포인트에 사용된 세션 기간을 제한해야 합니다.

## <a name="traffic-routing-methods"></a>트래픽 라우팅 방법

엔드포인트가 Degraded 상태가 되면 더 이상 DNS 쿼리에 대한 응답으로 반환되지 않습니다. 대신 대체 엔드포인트가 선택되어 반환됩니다. 프로필에 구성된 트래픽 라우팅 방법은 대체 엔드포인트를 선택하는 방법을 결정합니다.

* **우선 순위**. 엔드포인트는 우선 순위가 정해진 목록을 구성합니다. 목록에 사용 가능한 첫 번째 엔드포인트는 항상 반환됩니다. 엔드포인트 상태가 Degraded면 사용 가능한 다음 엔드포인트가 반환됩니다.
* **가중치 적용**. 사용 가능한 엔드포인트는 할당된 가중치와 사용 가능한 다른 엔드포인트의 가중치를 기반으로 무작위로 선택됩니다.
* **성능**. 최종 사용자에게 가장 가까운 엔드포인트가 반환됩니다. 해당 엔드포인트를 사용할 수 없는 경우 Traffic Manager가 트래픽을 다음으로 가까운 Azure 지역의 엔드포인트로 이동합니다. [중첩 Traffic Manager 프로필](traffic-manager-nested-profiles.md#example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region)을 사용하여 성능 트래픽 라우팅에 대한 대체 장애 조치 계획을 구성할 수 있습니다.
* **지리적**. 쿼리 요청 IP에 따라 지리적 위치를 제공하도록 매핑된 엔드포인트가 반환됩니다. 해당 엔드포인트를 사용할 수 없는 경우 지리적 위치가 프로필의 한 엔드포인트에만 매핑될 수 있으므로 다른 엔드포인트가 장애 조치(failover)하도록 선택되지 않습니다. (자세한 내용은 [FAQ](traffic-manager-FAQs.md#traffic-manager-geographic-traffic-routing-method) 참조). 모범 사례로, 지리적 라우팅을 사용하는 경우 고객이 둘 이상의 엔드포인트를 프로필 엔드포인트로 지정하여 중첩된 Traffic Manager 프로필을 사용하는 것이 좋습니다.
* **다중값** IPv4/IPv6 주소에 매핑된 여러 엔드포인트가 반환됩니다. 이 프로필에 대한 쿼리가 수신되면 지정한 **응답의 최대 레코드 수** 값에 따라 정상 엔드포인트가 반환됩니다. 응답의 기본 수는 두 개의 엔드포인트입니다.
* **서브넷** IP 주소 범위 집합에 매핑된 엔드포인트가 반환됩니다. 해당 IP 주소에서 요청이 수신되면 해당 IP 주소에 대해 매핑된 엔드포인트가 반환됩니다. 

자세한 내용은 [Traffic Manager 트래픽 라우팅 방법](traffic-manager-routing-methods.md)을 참조하세요.

> [!NOTE]
> 일반적인 트래픽 라우팅 동작의 한 가지 예외는 모든 적격 엔드포인트가 Degraded 상태에 있을 때 발생합니다. Traffic Manager는 “최선”을 다하고 *모든 Degraded 상태 엔드포인트가 실제로 Online 상태인 것처럼 응답* 합니다. 이 동작은 DNS 응답에 어떠한 엔드포인트도 반환하지 않는 것보다 바람직한 방법입니다. Disabled 또는 Stopped 엔드포인트는 모니터링되지 않으므로 트래픽에 적합한 것으로 간주되지 않습니다.
>
> 이 문제는 다음과 같은 서비스의 부적절한 구성 때문에 주로 발생합니다.
>
> * ACL[액세스 제어 목록]이 Traffic Manager 상태 검사를 차단합니다.
> * Traffic Manager 프로필에서 모니터링 포트 또는 프로토콜이 잘못 구성되었습니다.
>
> 이러한 동작은 Traffic Manager 상태 검사가 제대로 구성되지 않는 경우 발생하며 트래픽 라우팅에서 Traffic Manager가 제대로 *작동하는* 것처럼 보일 수 있습니다. 하지만 이 경우에 전체 애플리케이션 가용성에 영향을 미치는 엔드포인트 장애 조치는 발생할 수 없습니다. 프로필에 Degraded 상태 대신 Online 상태가 표시되는지 확인하는 것이 중요합니다. Online 상태는 Traffic Manager 상태 검사가 예상대로 작동 중임을 나타냅니다.

실패한 상태 검사의 문제 해결에 대한 자세한 내용은 [Azure Traffic Manager의 Degraded 상태 문제 해결](traffic-manager-troubleshooting-degraded.md)을 참조하세요.

## <a name="faqs"></a>FAQ

* [Traffic Manager 자체는 Azure 하위 지역 장애에 복원력이 있나요?](./traffic-manager-faqs.md#is-traffic-manager-resilient-to-azure-region-failures)

* [리소스 그룹의 위치 선택은 Traffic Manager에 어떤 영향을 주나요?](./traffic-manager-faqs.md#how-does-the-choice-of-resource-group-location-affect-traffic-manager)

* [각 엔드포인트의 현재 상태를 확인하려면 어떻게 해야 하나요?](./traffic-manager-faqs.md#how-do-i-determine-the-current-health-of-each-endpoint)

* [HTTPS 엔드포인트를 모니터링할 수 있나요?](./traffic-manager-faqs.md#can-i-monitor-https-endpoints)

* [엔드포인트를 추가할 때 IP 주소 또는 DNS 이름을 사용하나요?](./traffic-manager-faqs.md#do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint)

* [엔드포인트를 추가할 때 사용할 수 있는 IP 주소의 형식은 무엇인가요?](./traffic-manager-faqs.md#what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint)

* [단일 프로필 내에서 서로 다른 엔드포인트 주소 지정 형식을 사용할 수 있나요?](./traffic-manager-faqs.md#can-i-use-different-endpoint-addressing-types-within-a-single-profile)

* [들어오는 쿼리의 레코드 형식이 엔드포인트의 주소 지정 형식과 연결된 레코드 형식과 다른 경우에는 어떻게 되나요?](./traffic-manager-faqs.md#what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints)

* [중첩된 프로필에서 IPv4/IPv6으로 주소 지정된 엔드포인트를 사용한 프로필을 사용할 수 있나요?](./traffic-manager-faqs.md#can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile)

* [Traffic Manager 프로필에서 웹 애플리케이션 엔드포인트를 중지했지만 다시 시작한 후에도 트래픽을 받지 못하고 있습니다. 이 문제를 해결하려면 어떻게 해야 하나요?](./traffic-manager-faqs.md#i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this)

* [애플리케이션에 HTTP 또는 HTTPS에 대한 지원이 없는 경우에도 Traffic Manager를 사용할 수 있나요?](./traffic-manager-faqs.md#can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https)

* [TCP 모니터링을 사용하는 경우 엔드포인트에서 어떤 특정 응답이 필요한가요?](./traffic-manager-faqs.md#what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring)

* [Traffic Manager는 사용자를 비정상 엔드포인트에서 얼마나 빨리 이동하나요?](./traffic-manager-faqs.md#how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint)

* [프로필의 엔드포인트마다 다른 모니터링 설정을 지정하려면 어떻게 하나요?](./traffic-manager-faqs.md#how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile)

* [Traffic Manager 상태 검사에서 HTTP 헤더를 어떻게 내 엔드포인트에 할당할 수 있나요?](./traffic-manager-faqs.md#how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints)

* [엔드포인트 상태 검사에 어떤 호스트 헤더가 사용되나요?](./traffic-manager-faqs.md#what-host-header-do-endpoint-health-checks-use)

* [상태 검사가 시작되는 IP 주소는 무엇인가요?](./traffic-manager-faqs.md#what-are-the-ip-addresses-from-which-the-health-checks-originate)

* [Traffic Manager에서 내 엔드포인트에 대해 수행하는 예상 상태 검사 수는 몇 개인가요?](./traffic-manager-faqs.md#how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager)

* [내 엔드포인트 중 하나가 중단된 경우 알림을 받으려면 어떻게 해야 할까요?](./traffic-manager-faqs.md#how-can-i-get-notified-if-one-of-my-endpoints-goes-down)

## <a name="next-steps"></a>다음 단계

[Traffic Manager 작동 방식](traffic-manager-how-it-works.md)

Traffic Manager가 지원하는 [트래픽 라우팅 방법](traffic-manager-routing-methods.md) 에 대해 자세히 알아봅니다.

[Traffic Manager 프로필을 만드는](traffic-manager-manage-profiles.md)

Traffic Manager 엔드포인트에서 [Degraded 상태 문제를 해결](traffic-manager-troubleshooting-degraded.md)합니다.
