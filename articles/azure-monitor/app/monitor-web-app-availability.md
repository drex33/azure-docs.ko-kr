---
title: URL ping 테스트를 사용하여 가용성 모니터링 - Azure Monitor
description: Application Insights에서 ping 테스트를 설정합니다. 웹 사이트가 사용할 수 없게 되거나 느리게 응답하는 경우 알림이 제공됩니다.
ms.topic: conceptual
ms.date: 07/13/2021
ms.reviewer: sdash
ms.openlocfilehash: 55e396f7d1af6bc05d722e90d7dad99b2ac8b2d7
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567880"
---
# <a name="monitor-availability-with-url-ping-tests"></a>URL ping 테스트를 사용하여 가용성 모니터링

*URL ping 테스트* 는 다소 잘못된 명칭입니다. 이러한 테스트에서는 ICMP(Internet Control Message Protocol)를 사용하여 사이트의 가용성을 확인하지 않습니다. 대신 고급 HTTP 요청 기능을 사용하여 엔드포인트가 응답하는지 여부를 확인합니다. 해당 응답과 관련된 성능을 측정합니다. 또한 종속 요청 구문 분석, 재시도 허용과 같은 고급 기능과 결합된 사용자 지정 성공 조건을 설정하는 기능을 추가합니다.

가용성 테스트를 만들려면 기존 Application Insight 리소스를 사용하거나 [Application Insights 리소스를 만들어야](create-new-resource.md) 합니다.

> [!NOTE]
> URL ping 테스트는 클래식 테스트로 분류됩니다. **가용성** 창의 **클래식 테스트 추가** 아래에서 이 테스트를 찾을 수 있습니다. 추가 고급 기능에 대해서는 [표준 테스트(미리 보기)](availability-standard-tests.md)를 참조하세요.
 
## <a name="create-a-test"></a>테스트 만들기

첫 번째 가용성 요청을 만들려면
1. Application Insights 리소스에서 **가용성** 창을 열고   **클래식 테스트 추가** 를 선택합니다.

    :::image type="content" source="./media/monitor-web-app-availability/create-test.png" alt-text="가용성 창 및 클래식 테스트를 추가하는 단추를 보여 주는 스크린샷" lightbox ="./media/monitor-web-app-availability/create-test.png":::
1. 테스트 이름을 지정하고 **SKU** 에 대해 **URL ping** 을 선택합니다.
1. 테스트하려는 URL을 입력합니다.
1. 다음 표에 설명된 설정을 사용자의 요구에 맞게 조정하고 **만들기** 를 선택합니다.

   |설정| 설명 |
   |----|----|
   |**URL** |  URL은 테스트하려는 웹 페이지일 수 있지만 공용 인터넷에서 볼 수 있어야 합니다. URL에 쿼리 문자열을 포함할 수 있습니다. 데이터베이스 사용 등을 연습해 볼 수 있습니다. URL이 리디렉션으로 확인되면 최대 10개의 리디렉션을 따를 수 있습니다.|
   |**종속 요청 구문 분석**| 테스트에서 테스트 대상 웹 페이지의 일부인 이미지, 스크립트, 스타일 파일 및 기타 파일을 요청합니다. 기록된 응답 시간에는 이러한 파일을 가져오는 데 걸리는 시간이 포함됩니다. 전체 테스트의 시간 제한 내에서 이러한 모든 리소스를 성공적으로 다운로드할 수 없는 경우 테스트에 실패합니다. 옵션을 선택하지 않으면 테스트는 지정한 URL에서만 파일을 요청합니다. 이 옵션을 사용하면 더 엄격한 검사를 실시할 수 있습니다. 수동으로 사이트를 검색하지 못하는 경우에는 테스트가 실패할 수 있습니다.
   |**재시도 사용**|테스트에 실패하면 잠시 후에 다시 시도합니다. 연속 된 세 번의 시도가 실패하는 경우에 실패가 보고됩니다. 후속 테스트는 일반적인 테스트 빈도로 수행됩니다. 다음 성공까지 다시 시도는 일시적으로 중단됩니다. 이 규칙은 각 테스트 위치에서 독립적으로 적용됩니다. *이 옵션을 권장합니다*. 평균적으로 실패의 약 80%가 다시 시도 시 사라집니다.|
   |**테스트 빈도**| 이 설정은 각 테스트 위치에서 테스트를 실행하는 빈도를 결정합니다. 5분에 5번의 테스트를 하는 기본 빈도로 사이트를 평균 1분마다 테스트합니다.|
   |**테스트 위치**| 이 설정의 값은 서버에서 웹 요청을 URL로 보내는 위치입니다. 웹 사이트의 문제를 네트워크 문제와 구별할 수 있도록 *최소 5개의 테스트 위치를 권장* 합니다. 최대 16 개의 위치를 선택할 수 있습니다.

퍼블릭 인터넷에서 URL을 볼 수 없는 경우 테스트 트랜잭션만 허용하도록 방화벽을 열 수 있습니다. 가용성 테스트 에이전트에 대한 방화벽 예외에 대해 자세히 알아보려면 [IP 주소 가이드](./ip-addresses.md#availability-tests)를 참조하세요.

> [!NOTE]
> 적어도 5개 이상의 위치에서 테스트하는 것이 좋습니다. 이 방식은 특정 위치의 일시적인 문제 때문에 잘못된 경보가 발생하지 않도록 예방하는 데 도움이 됩니다. 또한 *테스트 위치 수를 경고 위치 임계값 + 2* 로 하는 것이 최적의 구성이라는 사실을 확인했습니다.

## <a name="success-criteria"></a>성공 조건

|설정| 설명 |
|----|----|
| **테스트 시간 제한** |느린 응답에 대한 알림을 받으려면 이 값을 감소시킵니다. 해당 기간 내에 사이트에서 응답을 받지 못한 경우 테스트는 실패로 계산됩니다. **종속 요청 구문 분석** 을 선택한 경우 모든 이미지, 스타일 파일, 스크립트 및 다른 종속된 리소스도 해당 기간 내에 받아야 합니다.|
| **HTTP 응답** | 성공으로 계산되어 반환된 상태 코드입니다. 일반적인 웹 페이지의 반환을 나타내는 코드는 200입니다.|
| **콘텐츠 일치** | 문자열에 대한 정확한 대/소문자 구분 일치가 모든 응답에서 발생하는지 테스트합니다. 와일드카드 없는 일반 문자열이어야 합니다(예: "Welcome!"). 페이지 내용이 변경되면 업데이트해야 할 수 있습니다. *콘텐츠 일치에서는 영어 문자만 지원합니다.* |

## <a name="alerts"></a>경고

|설정| 설명 |
|----|----|
|**거의 실시간(미리 보기)** | 거의 실시간으로 작동하는 경고를 사용하는 것이 좋습니다. 가용성 테스트를 만든 후 이 유형의 경고를 구성합니다.  |
|**경고 위치 임계값**| 경고 위치 임계값과 테스트 위치 수 사이의 최적 관계는 경고 위치 임계값 = 테스트 위치 수 - 2이고, 최소 테스트 위치 수는 5입니다.|

## <a name="location-population-tags"></a>위치 채우기 태그

Azure Resource Manager를 사용하여 가용성 URL ping 테스트를 배포할 때 지리적 위치 특성에 대해 다음과 같은 채우기 태그를 사용할 수 있습니다.

### <a name="azure-government"></a>Azure Government

| 표시 이름   | 채우기 이름     |
|----------------|---------------------|
| USGov 버지니아 | usgov-va-azr        |
| USGov 애리조나  | usgov-phx-azr       |
| USGov 텍사스    | usgov-tx-azr        |
| 미국 국방부 동부     | usgov-ddeast-azr    |
| 미국 국방부 중부  | usgov-ddcentral-azr |

### <a name="azure-china"></a>Azure 중국

| 표시 이름   | 채우기 이름     |
|----------------|---------------------|
| 중국 동부     | mc-cne-azr          |
| 중국 동부 2   | mc-cne2-azr         |
| 중국 북부    | mc-cnn-azr          |
| 중국 북부 2  | mc-cnn2-azr         |

### <a name="azure"></a>Azure

| 표시 이름                           | 채우기 이름   |
|----------------------------------------|-------------------|
| 오스트레일리아 동부                         | emea-au-syd-edge  |
| 브라질 남부                           | latam-br-gru-edge |
| 미국 중부                             | us-fl-mia-edge    |
| 동아시아                              | apac-hk-hkn-azr   |
| 미국 동부                                | us-va-ash-azr     |
| 프랑스 남부(이전 프랑스 중부) | emea-ch-zrh-edge  |
| 프랑스 중부                         | emea-fr-pra-edge  |
| 일본 동부                             | apac-jp-kaw-edge  |
| 북유럽                           | emea-gb-db3-azr   |
| 미국 중북부                       | us-il-ch1-azr     |
| 미국 중남부                       | us-tx-sn1-azr     |
| 동남 아시아                         | apac-sg-sin-azr   |
| 영국 서부                                | emea-se-sto-edge  |
| 서유럽                            | emea-nl-ams-azr   |
| 미국 서부                                | us-ca-sjc-azr     |
| 영국 남부                               | emea-ru-msa-edge  |

## <a name="see-your-availability-test-results"></a>가용성 테스트 결과 참조

꺾은선형 보기 및 산점도 보기를 통해 가용성 테스트 결과를 시각화할 수 있습니다.

몇 분 후에 **새로 고침** 을 선택하여 테스트 결과를 볼 수 있습니다.

:::image type="content" source="./media/monitor-web-app-availability/refresh.png" alt-text="새로 고침 단추가 강조 표시된 가용성 페이지를 보여 주는 스크린샷" lightbox="./media/monitor-web-app-availability/refresh.png":::

산점도 보기에서는 진단 테스트 단계의 세부 정보가 포함된 테스트 결과 샘플을 보여 줍니다. 테스트 엔진은 실패한 테스트에 대한 진단 정보를 저장합니다. 성공한 테스트의 경우 실행의 하위 집합에 대한 진단 정보가 저장됩니다. 녹색 또는 빨간색 점 위로 마우스를 이동하면 테스트 이름 및 위치를 볼 수 있습니다.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="꺾은선형 보기를 보여 주는 스크린샷" border="false":::

특정 테스트 또는 위치를 선택하거나 기간을 줄여 대상 기간에서 더 많은 결과를 볼 수 있습니다. 검색 탐색기를 사용하여 모든 실행의 결과를 확인하거나 분석 쿼리를 사용하여 이 데이터에 대한 사용자 지정 보고서를 실행합니다.

## <a name="inspect-and-edit-tests"></a>테스트 검사 및 편집

테스트를 편집하거나 일시적으로 사용하지 않도록 설정하거나 삭제하려면 테스트 이름 옆에 있는 줄임표( **...** )를 선택합니다. 구성 변경이 수행되고 해당 변경 내용이 모든 테스트 에이전트로 전파되는 데 최대 20분이 걸릴 수 있습니다.

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="테스트를 편집하고 사용하지 않도록 설정하는 옵션이 있는 테스트 세부 정보를 보여 주는 스크린샷" border="false":::

서비스에 대한 유지 관리를 수행하는 동안 가용성 테스트 또는 관련된 경고 규칙을 사용하지 않도록 설정할 수 있습니다.

## <a name="actions-if-you-see-failures"></a>오류가 표시되는 경우 수행할 작업

빨간 점을 클릭합니다.

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="엔드투엔드 트랜잭션 세부 정보에 대한 탭 스크린샷" border="false":::

가용성 테스트 결과에서 모든 구성 요소에서 트랜잭션 세부 정보를 볼 수 있습니다. 그러면 다음을 수행할 수 있습니다.

* 문제 해결 보고서를 검토하여 테스트 실패를 일으킨 원인을 확인합니다. 이 기간 동안 애플리케이션을 계속 사용할 수 있습니다.
* 서버로부터 수신한 응답을 검사합니다.
* 실패한 가용성 테스트를 처리하는 동안 수집된 상관 관련된 서버 쪽 원격 분석 데이터로 실패를 진단합니다.
* Git 또는 Azure Boards에 문제 또는 작업 항목을 기록하여 문제를 추적합니다. 버그에는 이 이벤트에 대한 링크가 포함됩니다.
* 웹 테스트 결과를 Visual Studio에서 엽니다.

엔드투엔드 트랜잭션 진단 환경에 대해 자세히 알아보려면 [트랜잭션 진단 설명서](./transaction-diagnostics.md)를 참조하세요.

가상 가용성 테스트를 실패하게 만든 서버 쪽 예외 세부 정보를 확인하려면 예외 행을 선택합니다. 다양한 코드 수준 진단에 대한 [디버그 스냅샷](./snapshot-debugger.md)을 가져올 수도 있습니다.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="서버 쪽 진단을 보여 주는 스크린샷":::

원시 결과 외에도 [메트릭 탐색기](../essentials/metrics-getting-started.md)에서 두 가지 주요 가용성 메트릭을 볼 수 있습니다.

- **가용성**: 모든 테스트 실행에서 성공한 테스트의 비율입니다.
- **테스트 지속 시간**: 모든 테스트 실행에서의 평균 테스트 지속 시간입니다.

## <a name="automation"></a>Automation

* [PowerShell 스크립트를 사용하여 가용성 테스트를 자동으로 설정](./powershell.md#add-an-availability-test)합니다.
* 경고가 발생하면 호출되는 [웹후크](../alerts/alerts-webhooks.md)를 설정합니다.


## <a name="next-steps"></a>다음 단계

* [가용성 경고](availability-alerts.md)
* [다중 단계 웹 테스트](availability-multistep.md)
* [문제 해결](troubleshoot-availability.md)
* [웹 테스트 Azure Resource Manager 템플릿](/azure/templates/microsoft.insights/webtests?tabs=json)
