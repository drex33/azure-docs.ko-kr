---
title: Application Gateway에 대한 Azure Monitor 메트릭
description: 메트릭을 사용하여 애플리케이션 게이트웨이의 성능을 모니터링하는 방법을 알아봅니다.
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: article
ms.date: 04/19/2021
ms.author: azhussai
ms.openlocfilehash: 2e448f907e129f628c4614c9df703bf2c39ea47a
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113665100"
---
# <a name="metrics-for-application-gateway"></a>Application Gateway에 대한 메트릭

Application Gateway는 메트릭이라는 데이터 요소를 [Azure Monitor](../azure-monitor/overview.md)에 게시하여 Application Gateway 및 백 엔드 인스턴스의 성능을 향상시킵니다. 이러한 메트릭은 특정 시간에 애플리케이션 게이트웨이의 일부 측면을 설명하는 시계열 데이터의 정렬된 집합에 있는 숫자 값입니다. Application Gateway를 통해 흐르는 요청이 있는 경우 해당 메트릭을 측정하고 60초 간격으로 보냅니다. Application Gateway를 통해 흐르는 요청이 없거나 메트릭에 대한 데이터가 없는 경우 메트릭은 보고되지 않습니다. 자세한 내용은 [Azure Monitor 메트릭](../azure-monitor/essentials/data-platform-metrics.md)을 참조하세요.

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Application Gateway V2 SKU에서 지원하는 메트릭

### <a name="timing-metrics"></a>타이밍 메트릭

Application Gateway는 모두 밀리초 단위로 측정되는 요청 및 응답과 관련된 몇 가지 기본 제공 타이밍 메트릭을 제공합니다. 

:::image type="content" source="./media/application-gateway-metrics/application-gateway-metrics.png" alt-text="[Application Gateway에 대한 타이밍 메트릭의 다이어그램" border="false":::

> [!NOTE]
>
> Application Gateway에 수신기가 하나보다 많은 경우에는 항상 *수신기* 차원을 기준으로 필터링하여 의미 있는 유추를 얻기 위해 다른 대기 시간 메트릭을 비교합니다.

- **백 엔드 연결 시간**

  백 엔드 애플리케이션과의 연결을 설정하는 소요된 시간입니다. 

  여기에는 네트워크 지연 시간뿐만 아니라 백 엔드 서버의 TCP 스택에서 새 연결을 설정하는 데 소요되는 시간도 포함됩니다. TLS의 경우 핸드셰이크에 소요된 시간도 포함됩니다. 

- **백 엔드 첫 번째 바이트 응답 시간**

  백 엔드 서버에 대한 연결을 설정하기 시작하는 시간과 응답 헤더의 첫 번째 바이트를 수신하기 시작하는 시간의 간격입니다. 

  이는 *백 엔드 연결 시간*, Application Gateway에서 백 엔드에 도달하는 요청에서 소요된 시간, 백 엔드 애플리케이션이 응답하는 데 소요된 시간(서버에서 콘텐츠를 생성하여 데이터베이스 쿼리를 가져오는 데 소요된 시간), 그리고 백 엔드에서 Application Gateway에 도달하는 응답의 첫 번째 바이트에서 소요된 시간의 대략적인 합입니다.

- **백 엔드 마지막 바이트 응답 시간**

  백 엔드 서버에 대한 연결을 설정하기 시작한 시간과 응답 본문의 마지막 바이트를 수신하기 시작하는 시간의 간격입니다. 

  이는 *백 엔드 첫 번째 바이트 응답 시간* 및 데이터 전송 시간의 대략적인 합입니다(이 수는 요청된 개체의 크기와 서버 네트워크의 대기 시간에 따라 크게 다를 수 있습니다).

- **Application Gateway 총 시간**

  수신할 요청을 처리하고 응답을 보내는 데 걸리는 평균 시간입니다. 

  Application Gateway가 HTTP 요청의 첫 번째 바이트를 받은 시점부터 마지막 응답 바이트가 클라이언트로 전송된 시간까지의 간격입니다. 여기에는 Application Gateway에서 사용한 처리 시간, ‘백 엔드 마지막 바이트 응답 시간’, 모든 응답을 전송하기 위해 Application Gateway에서 사용한 시간이 포함됩니다.

- **클라이언트 RTT**

  클라이언트와 Application Gateway 간의 평균 왕복 시간입니다.



이러한 메트릭을 사용하여 관찰된 속도가 클라이언트 네트워크, Application Gateway 성능, 백 엔드 네트워크 및 백엔드 서버 TCP 스택 포화, 백 엔드 애플리케이션 성능 또는 큰 파일 크기 때문인지 여부를 확인할 수 있습니다.

예를 들어 *백 엔드 첫 번째 바이트 응답 시간* 추세에 스파이크가 있지만 *백 엔드 연결 시간* 추세가 안정적인 경우, Application Gateway와 백 엔드 간의 지연 시간과 연결 설정에 소요된 시간이 안정적임을 유추할 수 있으며, 백 엔드 애플리케이션의 응답 시간 증가로 인해 스파이크가 발생합니다. 반면에 *백 엔드 첫 번째 바이트 응답 시간* 에 스파이크가 *백 엔드 연결 시간* 의 해당 스파이크와 연결되어 있는 경우 Application Gateway와 백 엔드 서버 사이의 네트워크 또는 백 엔드 서버 TCP 스택이 포화 상태임을 추론할 수 있습니다. 

*백 엔드 마지막 바이트 응답 시간* 에 스파이크가 있지만 *백 엔드 첫 번째 바이트 응답 시간* 은 안정적인 경우 스파이크가 요청되는 파일이 더 크기 때문이라고 추론할 수 있습니다.

마찬가지로 *Application Gateway 총 시간* 에 스파이크가 있지만 *백 엔드 마지막 바이트 응답 시간* 이 안정적인 경우 이는 Application Gateway의 성능 병목 현상 또는 클라이언트와 Application Gateway 사이에 있는 네트워크의 병목 현상의 징후일 수 있습니다. 또한 *클라이언트 RTT* 에 해당하는 스파이크가 있는 경우 클라이언트와 Application Gateway 간의 네트워크로 인해 성능이 저하되는 것을 나타냅니다.

### <a name="application-gateway-metrics"></a>Application Gateway 메트릭

Application Gateway에는 다음 메트릭이 지원됩니다.

- **받은 바이트 수**

   Application Gateway가 클라이언트에서 받은 바이트 수

- **보낸 바이트 수**

   Application Gateway가 클라이언트에 보낸 바이트 수

- **클라이언트 TLS 프로토콜**

   Application Gateway와 연결을 설정한 클라이언트에서 시작한 TLS 및 비 TLS 요청 수입니다. TLS 프로토콜 배포를 보려면 차원 TLS 프로토콜로 필터링합니다.

- **현재 용량 단위**

   트래픽 부하를 분산하는 데 사용되는 용량 단위 수입니다. 용량 단위에는 컴퓨팅 단위, 영구 연결 및 처리량의 세 가지 결정 요인이 있습니다. 각 용량 단위는 최대 1개의 컴퓨팅 단위 또는 2,500개의 영구 연결 또는 2.22Mbps의 처리량으로 구성됩니다.

- **현재 컴퓨팅 단위**

   사용된 프로세서 용량 수입니다. 컴퓨팅 단위에 영향을 주는 요소는 TLS 연결/초, URL 다시 쓰기 계산 및 WAF 규칙 처리입니다. 

- **현재 연결**

   클라이언트에서 Application Gateway로 활성 상태인 총 동시 연결 수
   
- **예상 청구 용량 단위**

  v2 SKU를 사용하는 경우 가격 책정 모델은 사용량에 따라 결정됩니다. 용량 단위는 고정 비용 외에 청구되는 소비 기반 비용을 측정합니다. *예상 청구 용량 단위* 는 청구 비용을 산정하는 데 사용되는 용량 단위의 수를 나타냅니다. 이 값은 *현재 용량 단위*(트래픽 부하를 분산하는 데 필요한 용량 단위)와 *고정 청구 가능 용량 단위*(프로비저닝된 최소 용량 단위) 중에서 더 큰 값으로 계산됩니다.

- **실패한 요청**

  Application Gateway에서 5xx 서버 오류 코드와 함께 제공된 요청 수입니다. 여기에는 Application Gateway에서 생성된 5xx 코드뿐만 아니라 백 엔드에서 생성된 5xx 코드도 포함됩니다. 각/특정 백 엔드 풀-http 설정 조합의 수를 표시하도록 요청 수를 추가로 필터링할 수 있습니다.
   
- **고정 청구 가능 용량 단위**

  Application Gateway 구성에서 *최소 배율 단위* 설정(한 인스턴스는 10 개의 용량 단위로 변환됨)에 따라 프로비저닝된 최소 용량 단위 수입니다.
   
 - **초당 새 연결 수**

   클라이언트에서 Application Gateway로 설정되고 Application Gateway에서 백 엔드 멤버로 설정된 초당 평균 새 TCP 연결 수입니다.


- **응답 상태**

   Application Gateway에서 반환된 HTTP 응답 상태입니다. 2xx, 3xx, 4xx 및 5xx 범주로 응답을 표시하도록 응답 상태 코드 분산을 더욱 세분화할 수 있습니다.

- **처리량**

   Application Gateway에서 제공하는 초당 바이트 수

- **총 요청 수**

   Application Gateway가 제공하는 성공한 요청 수입니다. 각/특정 백 엔드 풀-http 설정 조합의 수를 표시하도록 요청 수를 추가로 필터링할 수 있습니다.

### <a name="backend-metrics"></a>백 엔드 메트릭

Application Gateway에는 다음 메트릭이 지원됩니다.

- **백 엔드 응답 상태**

  백 엔드에서 반환된 HTTP 응답 상태 코드의 수입니다. Application Gateway에서 생성한 응답 코드는 여기에 포함되지 않습니다. 2xx, 3xx, 4xx 및 5xx 범주로 응답을 표시하도록 응답 상태 코드 분산을 더욱 세분화할 수 있습니다.

- **정상 호스트 수**

  상태 프로브에서 정상으로 확인된 백 엔드 수입니다. 특정 백 엔드 풀의 정상 호스트 수를 표시하도록 백 엔드 풀 기준으로 필터링할 수 있습니다.

- **비정상 호스트 수**

  상태 프로브에서 비정상으로 확인된 백 엔드 수입니다. 특정 백 엔드 풀의 비정상 호스트 수를 표시하도록 백 엔드 풀 기준으로 필터링할 수 있습니다.
  
- **분당 요청 수/정상 호스트**

  백 엔드 풀의 각 정상 멤버에서 받은 평균 요청 수(1분)입니다. *BackendPool HttpSettings* 차원을 사용하여 백 엔드 풀을 지정해야 합니다.  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Application Gateway V1 SKU에서 지원하는 메트릭

### <a name="application-gateway-metrics"></a>Application Gateway 메트릭

Application Gateway에는 다음 메트릭이 지원됩니다.

- **CPU 사용률**

  Application Gateway에 할당된 CPU의 사용률을 표시합니다.  정상적인 조건에서 CPU 사용량은 Application Gateway 뒤에 호스트되는 웹 사이트에서 대기 시간이 발생하고 클라이언트 경험에 방해가 될 수 있으므로 일반적으로 90%를 초과해서는 안 됩니다. 인스턴스 수를 늘리거나 더 큰 SKU 크기로 이동하거나 둘 다 수행하여 Application Gateway 구성을 수정함으로써 CPU 사용률을 간접적으로 제어하거나 향상시킬 수 있습니다.

- **현재 연결**

  Application Gateway와 설정된 현재 연결 수

- **실패한 요청**

  연결 문제로 실패한 요청 수입니다. 이 수에는 "요청 시간 초과" HTTP 설정 및 Application Gateway와 백 엔드 간의 연결 문제로 인해 실패한 요청이 초과되어 실패한 요청이 포함됩니다. 정상적인 백 엔드를 사용할 수 없기 때문에 이 수에는 오류가 포함되지 않습니다. 백 엔드의 4xx 및 5xx 응답도 이 메트릭의 일부로 간주되지 않습니다.

- **응답 상태**

  Application Gateway에서 반환된 HTTP 응답 상태입니다. 2xx, 3xx, 4xx 및 5xx 범주로 응답을 표시하도록 응답 상태 코드 분산을 더욱 세분화할 수 있습니다.

- **처리량**

  Application Gateway에서 제공하는 초당 바이트 수

- **총 요청 수**

  Application Gateway가 제공하는 성공한 요청 수입니다. 각/특정 백 엔드 풀-http 설정 조합의 수를 표시하도록 요청 수를 추가로 필터링할 수 있습니다.

- **웹 애플리케이션 방화벽 차단 요청 수**
- **웹 애플리케이션 방화벽 차단 요청 배포**
- **웹 애플리케이션 방화벽 총 규칙 배포**

### <a name="backend-metrics"></a>백 엔드 메트릭

Application Gateway에는 다음 메트릭이 지원됩니다.

- **정상 호스트 수**

  상태 프로브에서 정상으로 확인된 백 엔드 수입니다. 특정 백 엔드 풀의 정상 호스트 수를 표시하도록 백 엔드 풀 기준으로 필터링할 수 있습니다.

- **비정상 호스트 수**

  상태 프로브에서 비정상으로 확인된 백 엔드 수입니다. 특정 백 엔드 풀의 비정상 호스트 수를 표시하도록 백 엔드 풀 기준으로 필터링할 수 있습니다.

## <a name="metrics-visualization"></a>메트릭 시각화

애플리케이션 게이트웨이를 찾아 **모니터링** 아래에서 **메트릭** 을 선택합니다. 사용 가능한 값을 보려면 **메트릭** 드롭다운 목록을 선택합니다.

다음 이미지에서는 최근 30분 동안 표시된 세 가지 메트릭을 포함한 예제가 표시됩니다.

:::image type="content" source="media/application-gateway-diagnostics/figure5.png" alt-text="메트릭 보기" lightbox="media/application-gateway-diagnostics/figure5-lb.png":::

현재 지원되는 메트릭 목록을 보려면 [Azure Monitor에서 지원되는 메트릭](../azure-monitor/essentials/metrics-supported.md)을 참조하세요.

### <a name="alert-rules-on-metrics"></a>메트릭 기반 경고 규칙

리소스에 대한 메트릭을 기반으로 하는 경고 규칙을 시작할 수 있습니다. 예를 들어 애플리케이션 게이트웨이의 처리량이 지정된 기간 동안 임계값보다 높거나 낮거나 같을 때 경고에서 웹후크를 호출하거나 관리자에게 전자 메일을 보낼 수 있습니다.

다음 예제에서는 처리량이 임계값을 위반하면 관리자에게 전자 메일을 보내는 경고 규칙을 만드는 과정을 설명합니다.

1. **메트릭 경고 추가** 를 선택하여 **규칙 추가** 페이지를 엽니다. 또한 메트릭 페이지에서 이 페이지에 연결할 수도 있습니다.

   !["메트릭 경고 추가" 단추][6]

2. **규칙 추가** 페이지에서 이름, 조건 및 알림 섹션을 입력하고 **확인** 을 선택합니다.

   * **조건** 선택기에서 **보다 큼**, **보다 크거나 같음**, **보다 작음**, **보다 작거나 같음** 의 네 가지 값 중 하나를 선택합니다.

   * **기간** 선택기에서 5분에서 6시간 사이의 기간까지 선택합니다.

   * **전자 메일 소유자, 참가자 및 읽기 권한자** 를 선택하면 해당 리소스에 액세스할 수 있는 사용자에 따라 동적으로 전자 메일을 보낼 수 있습니다. 그렇지 않으면 **추가 관리자 전자 메일** 상자에서 쉼표로 구분된 사용자 목록을 제공할 수도 있습니다.

   ![규칙 추가 페이지][7]

임계값을 위반하면 다음 이미지와 비슷한 전자 메일이 도착합니다.

![위반된 임계값에 대한 전자 메일][8]

메트릭 경고를 만들면 경고 목록이 표시됩니다. 모든 경고 규칙에 대한 개요도 제공됩니다.

![경고 및 규칙 목록][9]

경고 알림에 대한 자세한 내용은 [경고 알림 받기](../azure-monitor/alerts/alerts-overview.md)를 참조하세요.

웹후크에 대한 자세한 내용 및 경고와 함께 웹후크를 사용하는 방법을 알아보려면 [Azure 메트릭 경고에 대한 웹후크 구성](../azure-monitor/alerts/alerts-webhooks.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor 로그](../azure-monitor/insights/azure-networking-analytics.md)를 사용하여 카운터 및 이벤트 로그를 시각화합니다.
* [Power BI를 사용하여 Azure 활동 로그 시각화](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) 블로그 게시물
* [Power BI 등에서 Azure 활동 로그 보기 및 분석](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) 블로그 게시물

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
