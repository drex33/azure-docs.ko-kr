---
title: 가용성 표준 테스트 - Azure Monitor Application Insights
description: Application Insights에서 표준 테스트를 설정하여 단일 요청 테스트를 통해 웹 사이트의 가용성을 확인합니다.
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: 2a8741d874fdfad8a76465fd8127dfb4752a1a55
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799484"
---
# <a name="standard-test"></a>표준 테스트

표준 테스트는 [URL ping 테스트](monitor-web-app-availability.md)와 유사하지만 더 고급인 단일 요청 테스트입니다. 엔드포인트가 응답하는지 확인하고 성능을 측정하는 것 외에도 표준 테스트에는 SSL 인증서 유효성 검사, 자동 관리 수명 검사, HTTP 요청 동사(예: `GET`, `HEAD`, `POST` 등), 사용자 지정 헤더 및 HTTP 요청과 연결된 사용자 지정 데이터도 포함됩니다.

> [!NOTE]
> 표준 테스트는 현재 공개 미리 보기로 제공됩니다. 이러한 미리 보기 버전은 서비스 수준 계약 없이 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

> [!NOTE]
> 현재 미리 보기 기능 표준 테스트에 대한 추가 요금은 없습니다. 미리 보기 단계인 기능에 대한 가격 책정은 나중에 발표될 예정이며 청구를 시작하기 전에 공지합니다. 알림 기간 후에도 계속 표준 테스트를 사용하도록 선택하면 해당되는 요금이 청구됩니다.

가용성 테스트를 만들려면 기존 Application Insight 리소스를 사용하거나 [Application Insights 리소스를 만들어야](create-new-resource.md) 합니다.

> [!TIP]
> 현재 URL ping 테스트와 같은 다른 가용성 테스트를 사용하는 경우 표준 테스트를 다른 테스트와 함께 추가할 수 있습니다. 다른 테스트 중 하나 대신 표준 테스트를 사용하려면 표준 테스트를 추가하고 이전 테스트를 삭제합니다. 

## <a name="create-a-standard-test"></a>표준 테스트 만들기

표준 테스트를 만들려면 다음을 수행합니다. 

1. Application Insights 리소스로 이동하고 **가용성** 창을 선택합니다.
1. **표준(미리 보기) 테스트 추가** 를 선택합니다.
    
    :::image type="content" source="./media/availability-standard-test/standard-test.png" alt-text="표준 테스트 추가 탭이 열려 있는 가용성 창의 스크린샷" lightbox="./media/availability-standard-test/standard-test.png":::

1. 테스트 이름, URL 및 추가 설정(아래 설명)을 입력한 다음, **만들기** 를 선택합니다.


|설정 | 설명 |
|--------|-------------|
|**URL** |  URL은 테스트하려는 웹 페이지일 수 있지만 공용 인터넷에서 볼 수 있어야 합니다. URL에 쿼리 문자열을 포함할 수 있습니다. 따라서 데이터베이스 사용 등을 연습해 볼 수 있습니다. URL이 리디렉션으로 확인되면 최대 10개의 리디렉션을 따릅니다.|
|**종속 요청 구문 분석**| 테스트에서 테스트 대상 웹 페이지의 일부인 이미지, 스크립트, 스타일 파일 및 기타 파일을 요청합니다. 기록된 응답 시간에는 이러한 파일을 가져오는 데 걸리는 시간이 포함됩니다. 전체 테스트의 시간 제한 내에서 이러한 모든 리소스를 성공적으로 다운로드할 수 없는 경우 테스트에 실패합니다. 옵션을 선택하지 않으면 테스트는 지정한 URL에서만 파일을 요청합니다. 이 옵션을 사용하면 더 엄격한 검사를 실시할 수 있습니다. 수동으로 사이트를 검색할 때 눈에 띄지 않는 경우에는 테스트가 실패할 수 있습니다. |
|**재시도 사용**| 테스트에 실패하면 잠시 후에 다시 시도합니다. 연속 된 세 번의 시도가 실패하는 경우에 실패가 보고됩니다. 후속 테스트는 일반적인 테스트 빈도로 수행됩니다. 다음 성공까지 다시 시도는 일시적으로 중단됩니다. 이 규칙은 각 테스트 위치에서 독립적으로 적용됩니다. **이 옵션을 권장합니다**. 평균 실패의 약 80%는 다시 시도에서 사라집니다.|
| **SSL 인증서 유효성 검사** | 웹 사이트에서 SSL 인증서를 확인하여 올바르게 설치되고, 유효하고, 신뢰할 수 있으며, 사용자에게 오류를 제공하지 않는지 확인할 수 있습니다. |
| **자동 관리 수명 검사** | 이렇게 하면 SSL 인증서가 만료되기 전에 설정된 기간을 정의할 수 있습니다. 만료되면 테스트가 실패합니다. |
|**테스트 빈도**| 각 테스트 위치에서 테스트를 실행하는 빈도를 설정합니다. 5분에 5번의 테스트를 하는 기본 빈도로 사이트를 평균 1분마다 테스트합니다.|
|**테스트 위치**|  서버가 URL로 웹 요청을 보내는 곳입니다. 웹 사이트 문제를 네트워크 문제와 구분할 수 있도록 **적어도 5개 이상의 테스트 위치를 권장** 합니다. 최대 16 개의 위치를 선택할 수 있습니다.|
| **사용자 지정 헤더** | 운영 매개 변수를 정의하는 키 값 쌍 |
| **HTTP 요청 동사** | 요청으로 수행하려는 작업을 나타냅니다. |
| **요청 본문** | HTTP 요청과 연결된 사용자 지정 데이터 사용자 고유의 파일을 업로드하거나, 콘텐츠를 입력하거나, 이 기능을 비활성화할 수 있습니다. |

## <a name="success-criteria"></a>성공 조건

|설정| 설명|
|-------|------------|
| **테스트 시간 제한** |느린 응답에 대한 알림을 받으려면 이 값을 감소시킵니다. 해당 기간 내에 사이트에서 응답을 받지 못한 경우 테스트는 실패로 계산됩니다. **종속 요청 구문 분석** 을 선택한 경우 모든 이미지, 스타일 파일, 스크립트 및 다른 종속된 리소스도 해당 기간 내에 받아야 합니다.|
| **HTTP 응답** | 성공으로 계산되어 반환된 상태 코드입니다. 200은 일반적인 웹 페이지의 반환을 나타내는 코드입니다.|
| **콘텐츠 일치** | "Welcome!"과 같은 문자열 정확한 대/소문자 구분 일치가 모든 응답에서 발생하는지 테스트합니다. 와일드카드 없는 일반 문자열이어야 합니다. 페이지 내용이 변경되면 업데이트해야 할 수 있습니다. **콘텐츠 일치에서는 영어 문자만 지원됩니다.** |

## <a name="alerts"></a>경고

|설정| 설명|
|-------|------------|
|**거의 실시간(미리 보기)** | 거의 실시간으로 경고를 사용하는 것이 좋습니다. 이 유형의 경고 구성은 가용성 테스트를 만든 후에 수행됩니다.  |
|**경고 위치 임계값**|최소 3/5 위치를 사용하는 것이 좋습니다. 경고 위치 임계값과 테스트 위치 수 사이의 최적 관계는 **경고 위치 임계값** = **테스트 위치 수 - 2이고, 최소 테스트 위치 수는 5입니다.**|

## <a name="location-population-tags"></a>위치 채우기 태그

Azure Resource Manager를 사용하여 가용성 URL ping 테스트를 배포할 때 지리적 위치 특성에 대해 다음과 같은 채우기 태그를 사용할 수 있습니다.

### <a name="azure-gov"></a>Azure gov

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

#### <a name="azure"></a>Azure

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

가용성 테스트 결과는 선 보기와 산점도 보기 모두를 사용하여 시각화할 수 있습니다.

몇 분 후에 **새로 고침** 을 선택하여 테스트 결과를 볼 수 있습니다.

:::image type="content" source="./media/monitor-web-app-availability/availability-refresh-002.png" alt-text="스크린샷에는 가용성 페이지가 표시되며, 새로 고침 단추가 강조 표시됩니다.":::

산점도 보기에서는 진단 테스트 단계의 세부 정보가 포함된 테스트 결과 샘플을 보여줍니다. 테스트 엔진은 실패한 테스트에 대한 진단 정보를 저장합니다. 성공한 테스트의 경우 실행의 하위 집합에 대한 진단 정보가 저장됩니다. 녹색/빨간색 점 위로 마우스를 이동하면 테스트, 테스트 이름 및 위치를 볼 수 있습니다.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="줄 뷰" border="false":::

특정 테스트, 위치를 선택하거나 기간을 줄여 대상 기간에서 더 많은 결과를 볼 수 있습니다. 검색 탐색기를 사용하여 모든 실행의 결과를 확인하거나 분석 쿼리를 사용하여 이 데이터에 대한 사용자 지정 보고서를 실행합니다.

## <a name="inspect-and-edit-tests"></a>테스트 검사 및 편집

테스트를 편집하거나 일시적으로 비활성화하거나 삭제하려면 테스트 이름 옆에 있는 줄임표를 선택합니다. 구성 변경 내용이 모든 테스트 에이전트로 전파되는 데 최대 20분이 걸릴 수 있습니다.

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="테스트 세부 정보 보기, 웹 테스트 편집 및 사용 안 함" border="false":::

서비스에 대한 유지 관리를 수행하는 동안 가용성 테스트 또는 관련된 경고 규칙을 사용하지 않도록 설정할 수 있습니다.

## <a name="if-you-see-failures"></a>오류가 표시되는 경우

빨간 점을 클릭합니다.

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="엔드투엔드 트랜잭션 세부 정보 스크린샷" border="false":::

가용성 테스트 결과에서 모든 구성 요소에서 트랜잭션 세부 정보를 볼 수 있습니다. 다음을 수행할 수 있습니다.

* 문제 해결 보고서를 검토하여 테스트 실패를 일으킨 원인을 확인합니다. 애플리케이션을 계속 사용할 수 있습니다.
* 서버로부터 수신한 응답을 검사합니다.
* 실패한 가용성 테스트를 처리하는 동안 수집된 상관 관련된 서버 쪽 원격 분석 데이터로 실패를 진단합니다.
* Git 또는 Azure Boards에 문제 또는 작업 항목을 기록하여 문제를 추적합니다. 버그에는 이 이벤트에 대한 링크가 포함됩니다.
* 웹 테스트 결과를 Visual Studio에서 엽니다.

엔드투엔드 트랜잭션 진단 환경에 대해 자세히 알아보려면 [트랜잭션 진단 설명서](./transaction-diagnostics.md)를 참조하세요.

가상 가용성 테스트를 실패하게 만든 서버 쪽 예외 세부 정보를 확인하려면 예외 행을 선택합니다. 다양한 코드 수준 진단에 대한 [디버그 스냅샷](./snapshot-debugger.md)을 가져올 수도 있습니다.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="서버 쪽 진단":::

원시 결과 외에도 [메트릭 탐색기](../essentials/metrics-getting-started.md)에서 두 가지 주요 가용성 메트릭을 볼 수 있습니다.

* 가용성: 모든 테스트 실행에서 성공한 테스트의 비율입니다.
* 테스트 지속 시간: 모든 테스트 실행에서의 평균 테스트 지속 시간입니다.

## <a name="next-steps"></a>다음 단계

* [가용성 경고](availability-alerts.md)
* [다중 단계 웹 테스트](availability-multistep.md)
* [문제 해결](troubleshoot-availability.md)
* [웹 테스트 Azure Resource Manager 템플릿](/azure/templates/microsoft.insights/webtests?tabs=json)
