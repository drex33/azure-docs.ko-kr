---
title: Azure Monitor의 사용자 지정 메트릭(미리 보기)
description: Azure Monitor의 사용자 지정 메트릭 및 모델링하는 방법을 알아봅니다.
author: anirudhcavale
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: a456db938d1607565525aea7ab2e9b43bc368aeb
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122530222"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Azure Monitor의 사용자 지정 메트릭(미리 보기)

Azure에서 리소스 및 애플리케이션을 배포하는 동안 성능 및 상태에 대한 정보를 얻기 위해 원격 분석 수집을 시작할 수 있습니다. Azure는 몇 가지 메트릭을 기본적으로 제공합니다. 이러한 메트릭을 [표준 또는 플랫폼](./metrics-supported.md)이라고 합니다. 그러나 메트릭은 본질적으로 제한되어 있습니다. 

더욱 심층적인 인사이트를 제공하려면 일부 사용자 지정 성능 지표 또는 비즈니스 관련 메트릭을 수집하는 것이 좋습니다. 이러한 **사용자 지정** 메트릭은 애플리케이션 원격 분석, Azure 리소스에서 실행되는 에이전트 또는 외부 모니터링 시스템을 통해서도 수집되어 Azure Monitor로 직접 전송될 수 있습니다. Azure Monitor에 게시된 후에는 Azure에서 내보낸 표준 메트릭과 나란히, Azure 리소스 및 애플리케이션에 대한 사용자 지정 메트릭을 찾아보고 쿼리 및 경고할 수 있습니다.

Azure Monitor 사용자 지정 메트릭은 공개 미리 보기로 제공됩니다. 

## <a name="methods-to-send-custom-metrics"></a>사용자 지정 메트릭을 보내는 방법

다음과 같은 여러 가지 방법으로 사용자 지정 메트릭을 Azure Monitor로 보낼 수 있습니다.
- Azure Application Insights SDK를 사용하여 애플리케이션을 계측하고 사용자 지정 원격 분석을 Azure Monitor로 보냅니다. 
- [Windows 또는 Linux Azure VM](../agents/azure-monitor-agent-overview.md)에 Azure Monitor 에이전트(미리 보기)를 설치하고 [데이터 수집 규칙](../agents/data-collection-rule-azure-monitor-agent.md)을 사용하여 Azure Monitor 메트릭에 성능 카운터를 보냅니다.
- [Azure VM](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md), [가상 머신 확장 집합](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md), [클래식 VM](../essentials/collect-custom-metrics-guestos-vm-classic.md) 또는 [클래식 Cloud Services](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)에 WAD(Microsoft Azure Diagnostics) 확장을 설치하고 성능 카운터를 Azure Monitor로 보냅니다. 
- Azure Linux VM에 [InfluxData Telegraf 에이전트](../essentials/collect-custom-metrics-linux-telegraf.md)를 설치하고 Azure Monitor 출력 플러그 인을 사용하여 메트릭을 보냅니다.
- 사용자 지정 메트릭을 [Azure Monitor REST API`https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`, ](./metrics-store-custom-rest-api.md)에 직접 보냅니다.

## <a name="pricing-model-and-retention"></a>가격 책정 모델 및 보존

사용자 지정 메트릭 및 메트릭 쿼리에 대해 대금이 언제 청구되는지에 관한 자세한 내용은 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)를 확인하세요. 이 페이지에서는 사용자 지정 메트릭 및 메트릭 쿼리를 비롯한 모든 메트릭에 대한 특정 가격 정보가 제공됩니다. 요약하면, 표준 메트릭(플랫폼 메트릭)을 Azure Monitor 메트릭 저장소에 수집하는 데는 비용이 발생하지 않지만, 사용자 지정 메트릭이 일반 공급으로 전환될 때는 비용이 발생합니다. 메트릭 API 쿼리에는 비용이 발생합니다.

사용자 지정 메트릭은 [플랫폼 메트릭과 동일한 시간](../essentials/data-platform-metrics.md#retention-of-metrics) 동안 보존됩니다. 

> [!NOTE]  
> Application Insights SDK를 통해 Azure Monitor로 전송되는 메트릭은 수집 로그 데이터로 청구됩니다. Application Insights의 [사용자 지정 메트릭 차원의 경고를 사용하도록 설정](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) 기능이 선택된 경우에만 추가 메트릭 요금이 발생합니다. 이 확인란은 더 복잡한 경고를 허용하기 위해 사용자 지정 메트릭 API를 사용하여 Azure Monitor 메트릭 데이터베이스로 데이터를 전송합니다.  [Application Insights 가격 책정 모델](../app/pricing.md#pricing-model) 및 [해당 지역의 가격](https://azure.microsoft.com/pricing/details/monitor/)에 대해 자세히 알아봅니다.


## <a name="how-to-send-custom-metrics"></a>사용자 지정 메트릭을 보내는 방법

Azure Monitor에 사용자 지정 메트릭을 보낼 때 보고되는 각 데이터 요소 또는 값은 다음 정보를 포함해야 합니다.

### <a name="authentication"></a>인증
사용자 지정 메트릭을 Azure Monitor로 전송하려면 메트릭을 전송하는 엔터티에 유효한 Azure AD(Azure Active Directory) 토큰이 요청의 **전달자** 헤더에 있어야 합니다. 유효한 전달자 토큰을 획득하기 위한 지원되는 몇 가지 방법이 있습니다.
1. [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md) Azure 리소스 자체(예: VM)에 ID를 제공합니다. MSI(관리 서비스 ID)는 특정 작업을 수행할 수 있는 권한을 리소스에 제공하도록 설계되었습니다. 예를 들어, 리소스가 해당 지표를 내보낼 수 있도록 허용합니다. 리소스 또는 해당 MSI에 다른 리소스에 대한 **모니터링 메트릭 게시자** 권한을 부여할 수 있습니다. 이 권한을 사용하면 MSI가 다른 리소스에 대한 지표도 내보낼 수 있습니다.
2. [Azure AD 서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md). 이 시나리오에서는 Azure 리소스에 대한 메트릭을 내보내는 권한을 Azure AD 애플리케이션 또는 서비스에 할당할 수 있습니다.
요청을 인증하기 위해 Azure Monitor는 Azure AD 공개 키를 사용하여 애플리케이션 토큰의 유효성을 검사합니다. 기존 **모니터링 메트릭 게시자** 역할에는 이 사용 권한이 이미 있으며, Azure Portal에서 사용할 수 있습니다. 서비스 주체는 사용자 지정 메트릭을 내보낼 리소스에 따라 필요한 범위에서 **모니터링 메트릭 게시자** 역할을 부여받을 수 있습니다. 범위의 예로 구독, 리소스 그룹 또는 특정 리소스가 있습니다.

> [!TIP]  
> 사용자 지정 메트릭을 내보내기 위해 Azure AD 토큰을 요청하는 경우 토큰이 요청되는 대상 그룹 또는 리소스가 `https://monitoring.azure.com/`이어야 합니다. 후행 슬래시(‘/’)를 포함해야 합니다.

### <a name="subject"></a>제목
이 속성은 사용자 지정 메트릭이 보고되는 Azure 리소스 ID를 캡처합니다. 이 정보는 수행되는 API 호출의 URL에 인코딩됩니다. 각 API는 단일 Azure 리소스에 대한 메트릭 값만 전송할 수 있습니다.

> [!NOTE]  
> 리소스 그룹 또는 구독의 리소스 ID에 대해서는 사용자 지정 메트릭을 내보낼 수 없습니다.


### <a name="region"></a>지역
이 속성은 메트릭을 내보내는 리소스가 배포된 Azure 지역을 캡처합니다. 메트릭은 리소스가 배포되는 지역과 동일한 Azure Monitor 지역 엔드포인트에 배포되어야 합니다. 예를 들어, 미국 서부에 배포된 VM에 대한 사용자 지정 메트릭은 WestUS 지역별 Azure Monitor 엔드포인트에 전송되어야 합니다. 또한 지역 정보는 API 호출의 URL에 인코딩됩니다.

> [!NOTE]  
> 공개 미리 보기 중에는 일부 Azure 지역에서만 사용자 지정 메트릭을 사용할 수 있습니다. 지원되는 지역 목록은 이 문서의 뒷부분에 설명되어 있습니다.
>
>

### <a name="timestamp"></a>타임스탬프
Azure Monitor에 전송되는 각 데이터 요소는 타임스탬프를 사용하여 표시되어야 합니다. 이 타임스탬프는 메트릭 값이 측정 또는 수집된 날짜/시간을 캡처합니다. Azure Monitor는 과거 20분 및 미래 5분까지의 타임스탬프가 지정된 메트릭 데이터를 허용합니다. 타임스탬프는 ISO 8601 형식이어야 합니다.

### <a name="namespace"></a>네임스페이스
네임스페이스는 유사한 메트릭을 함께 분류 또는 그룹화하는 방법입니다. 네임스페이스를 사용하면 각기 다른 인사이트 또는 성능 지표를 수집할 수 있는 메트릭 그룹을 격리할 수 있습니다. 예를 들어, 앱을 프로파일링하는 메모리 사용 메트릭을 추적하는 **ContosoMemoryMetrics** 라는 네임스페이스가 있을 수 있습니다. **contosoapptransaction** 이라는 또 다른 네임스페이스는 애플리케이션의 사용자 트랜잭션에 대한 모든 메트릭을 추적할 수 있습니다.

### <a name="name"></a>Name
**이름** 은 보고되는 메트릭의 이름입니다. 일반적으로 이름은 측정 대상을 식별하기에 충분한 정보를 제공합니다. 예를 들어, 지정된 VM에서 사용된 메모리 바이트 수를 측정하는 메트릭이 있습니다. 메트릭 이름은 **사용 중인 메모리 바이트** 일 수 있습니다.

### <a name="dimension-keys"></a>차원 키
차원은 수집하는 메트릭에 관한 추가적인 특성을 설명하는 데 유용한 키 또는 값 쌍입니다. 추가적인 특성을 사용하면 메트릭에 대한 자세한 정보를 수집하여 더욱 심층적인 인사이트를 얻을 수 있습니다. 예를 들어, **사용 중인 메모리 바이트** 메트릭에는 VM의 각 프로세스에서 사용 중인 메모리 바이트 수를 캡처하는 **프로세스** 라는 차원 키가 있을 수 있습니다. 이 키를 사용하면 메트릭을 필터링해 메모리별 프로세스 양을 확인하거나 메모리 사용량에 따른 상위 5개 프로세스를 식별할 수 있습니다.
차원은 선택 사항이며, 모든 메트릭에 차원이 포함되는 것은 아닙니다. 사용자 지정 메트릭에는 최대 10개의 차원이 있을 수 있습니다.

### <a name="dimension-values"></a>차원 값
메트릭 데이터 요소를 보고하는 경우 보고되는 메트릭의 각 차원 키에 해당하는 차원 값이 있습니다. 예를 들어, VM의 ContosoApp에서 사용하는 메모리를 보고하려는 경우

* 메트릭 이름은 **사용 중인 메모리 바이트** 가 됩니다.
* 차원 키는 **프로세스** 가 됩니다.
* 차원 값은 **ContosoApp.exe** 가 됩니다.

메트릭 값을 게시하는 경우 차원 키당 단일 차원 값만 지정할 수 있습니다. VM의 여러 프로세스에 대해 수집한 메모리 사용률이 동일한 경우 해당 타임스탬프에 여러 개의 메트릭 값을 보고할 수 있습니다. 각 메트릭 값은 **프로세스** 차원 키에 대해 다른 차원 값을 지정합니다.
차원은 선택 사항이며, 모든 메트릭에 차원이 포함되는 것은 아닙니다. 메트릭 게시물에서 차원 키를 정의하는 경우 해당 차원 값은 필수입니다.

### <a name="metric-values"></a>메트릭 값
Azure Monitor는 1분 단위 간격으로 모든 메트릭을 저장합니다. 지정된 1분 동안 메트릭을 여러 번 샘플링해야 하는 경우도 있습니다. 예를 들면 CPU 사용률이 있습니다. 또는 많은 불연속 이벤트에 대해 측정해야 할 수도 있습니다. 로그인 트랜잭션 대기 시간을 예로 들 수 있습니다. 내보내야 하는 원시 값의 수를 제한하고 Azure Monitor에 대한 요금을 지불하려면 다음과 같은 값을 로컬로 미리 집계하고 내보낼 수 있습니다.

* **최소**: 1분 동안 모든 샘플 및 측정값에서 관찰된 최솟값입니다.
* **최대**: 1분 동안 모든 샘플 및 측정값에서 관찰된 최댓값입니다.
* **합계**: 1분 동안 모든 샘플 및 측정값에서 관찰된 모든 값의 합계입니다.
* **개수**: 1분 동안 수행된 샘플 및 측정값 수입니다.

예를 들어, 지정된 1분 동안 앱에 대한 로그인 트랜잭션이 4개 있었다면 각 트랜잭션에 대해 측정된 대기 시간은 다음과 같을 수 있습니다.

|트랜잭션 1|트랜잭션 2|트랜잭션 3|트랜잭션 4|
|---|---|---|---|
|7ms|4ms|13ms|16밀리초|

Azure Monitor에 대한 결과 메트릭 게시는 다음과 같습니다.
* 최소: 4
* 최대: 16
* 합계: 40
* 개수: 4

애플리케이션이 로컬로 미리 집계할 수 없고 각 불연속 샘플 또는 이벤트를 수집되는 즉시 내보내야 하는 경우 원시 측정 값을 내보낼 수 있습니다. 예를 들어, 앱에서 로그인 트랜잭션이 발생할 때마다 단일 측정값만 사용하여 메트릭을 Azure Monitor에 게시합니다. 따라서 12밀리초가 걸리는 로그인 트랜잭션의 경우 메트릭 게시는 다음과 같습니다.
* 최소: 12
* 최대: 12
* 합계: 12
* 개수: 1

이 프로세스에서는 지정된 1분 동안 동일한 메트릭 및 차원 조합에 대해 여러 개의 값을 내보낼 수 있습니다. 그런 다음, Azure Monitor에서 지정된 1분 동안 내보낸 모든 원시 값을 가져와 집계합니다.

### <a name="sample-custom-metric-publication"></a>샘플 사용자 지정 메트릭 게시
다음 예제에서는 가상 머신에 대한 **메모리 프로필** 메트릭 네임스페이스에 **사용 중인 메모리 바이트** 라는 사용자 지정 메트릭을 만듭니다. 이 메트릭에는 **프로세스** 라는 단일 차원이 있습니다. 지정된 타임스탬프에서 다음 두 가지 프로세스에 대한 메트릭 값을 내보냅니다.

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"
        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights, 진단 확장 및 InfluxData Telegraf 에이전트는 올바른 지역별 엔드포인트에 대한 메트릭 값을 내보내고 각 내보내기에 위의 속성을 모두 포함하도록 이미 구성되어 있습니다.
>
>

## <a name="custom-metric-definitions"></a>사용자 지정 메트릭 정의
내보내기 전에 사용자 지정 메트릭을 Azure Monitor에서 미리 정의할 필요가 없습니다. 게시된 각 메트릭 데이터 요소에는 네임스페이스, 이름 및 차원 정보가 포함되어 있습니다. 따라서 사용자 지정 메트릭을 Azure Monitor에 처음 내보낼 때 메트릭 정의가 자동으로 생성됩니다. 그런 다음, 메트릭 정의를 통해 메트릭을 내보낸 모든 리소스에서 이 메트릭 정의를 검색할 수 있습니다.

> [!NOTE]  
> Azure Monitor는 사용자 지정 메트릭에 대한 **단위** 정의를 아직 지원하지 않습니다.

## <a name="using-custom-metrics"></a>사용자 지정 메트릭 사용
사용자 지정 메트릭을 Azure Monitor에 전송한 후 Azure Portal을 통해 검색하고 Azure Monitor REST API를 통해 쿼리할 수 있습니다. 특정 조건이 충족되면 알리도록 메트릭에 대한 경고를 만들 수도 있습니다.

> [!NOTE]
> 사용자 지정 메트릭을 보려면 읽기 권한자 또는 참가자 역할이어야 합니다. [Monitoring Reader](../../role-based-access-control/built-in-roles.md#monitoring-reader)를 참조합니다. 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Azure Portal을 통해 사용자 지정 메트릭 찾아보기
1.    [Azure Portal](https://portal.azure.com)로 이동합니다.
2.    **모니터** 창을 선택합니다.
3.    **메트릭** 을 선택합니다.
4.    사용자 지정 메트릭을 내보낸 리소스를 선택합니다.
5.    사용자 지정 메트릭에 대한 메트릭 네임스페이스를 선택합니다.
6.    사용자 지정 메트릭을 선택합니다.

> [!NOTE]
> Azure Portal에서 메트릭을 보는 자세한 방법은 [Azure 메트릭 탐색기 시작](./metrics-getting-started.md)을 참조하세요.

## <a name="supported-regions"></a>지원되는 지역
공개 미리 보기 중 사용자 지정 메트릭을 게시할 수 있는 기능은 일부 Azure 지역에서만 사용할 수 있습니다. 이 제한 사항은 지원되는 지역 중 하나의 리소스에 대해서만 메트릭을 게시할 수 있음을 의미합니다. Azure 지역에 대한 자세한 정보는 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)을 참조하세요. 아래 엔드포인트에서 사용되는 Azure 지역 코드는 공백이 제거된 지역 이름입니다. 다음 표에는 사용자 지정 메트릭에 대해 지원되는 Azure 지역이 나열되어 있습니다. 이러한 지역의 리소스에 대한 메트릭이 게시되어야 하는 해당 엔드포인트도 나와 있습니다.

|Azure 지역 |지역별 엔드포인트 접두사|
|---|---|
| 모든 퍼블릭 클라우드 지역 | https://<azure_region_code>.monitoring.azure.com |


## <a name="latency-and-storage-retention"></a>대기 시간 및 스토리지 보존

새 메트릭 또는 메트릭에 추가되는 새 차원을 추가하면 표시되기까지 최대 2~3분이 소요될 수 있습니다. 시스템에서 데이터는 99%의 경우 30초 이내 표시되어야 합니다. 

메트릭을 삭제하거나 차원을 제거하는 경우 변경 내용이 시스템에서 삭제되기까지 1주에서 1달 정도 소요될 수 있습니다.

## <a name="quotas-and-limits"></a>할당량 및 제한
Azure Monitor는 사용자 지정 메트릭에 대해 다음과 같은 사용량 한도를 적용합니다.

|범주|제한|
|---|---|
|활성 시계열/구독/지역|50,000|
|메트릭당 차원 키|10|
|메트릭 네임스페이스, 메트릭 이름, 차원 키 및 차원 값의 문자열 길이|256자|

활성 시계열은 지난 12시간 동안 메트릭 값이 게시된 메트릭, 차원 키 또는 차원 값의 고유한 조합으로 정의됩니다.

50,000 시계열 제한을 이해하려면 다음 메트릭을 고려하세요.

차원이 있는 서버 응답 시간: 지역, 부서, *CustomerID*

이 메트릭 사용 시, 10개 지역, 20개 부서 및 100명의 고객이 있는 경우 시계열은 10x20x100=2000입니다. 

100개 지역, 200개 부서 및 2000명의 고객의 경우, 시계열은 100x200x2000=40,000,000으로 이 메트릭 하나에 대해서만으로도 제한을 훨씬 초과합니다. 

다시 말하지만, 이 제한은 개별 메트릭에 해당하지 않습니다. 이는 구독과 지역에 대한 모든 메트릭의 합계에 대한 것입니다.  

## <a name="design-limitations-and-considerations"></a>디자인 제한 사항 및 고려 사항

**감사 목적으로 Application Insights를 사용하지 마십시오.** - Application Insights 원격 분석 파이프라인은 성능 영향을 최소화하고 네트워크 트래픽이 애플리케이션을 모니터링하지 못하도록 제한하기 위해 최적화되어 있습니다. 따라서 초기 데이터 세트가 너무 커지면 이를 제한하거나 샘플링합니다(원격 분석의 일부만 사용하고 나머지는 무시함). 이로 인해 일부 레코드가 삭제될 가능성이 있기 때문에 감사 목적으로는 사용할 수 없습니다. 

**이름에 변수가 있는 메트릭의 경우** 변수를 메트릭 이름의 일부로 사용하지 마십시오. 대신 상수를 사용합니다. 변수가 해당 값을 변경할 때마다 Azure Monitor는 새 메트릭을 생성하여 메트릭 수에 대한 제한에 빠르게 도달합니다. 일반적으로 개발자가 메트릭 이름에 변수를 포함하려는 경우 하나의 메트릭 내에서 여러 개의 시계열을 추적하려고 하며 변수 메트릭 이름 대신 차원을 사용해야 합니다. 

**높은 카디널리티 메트릭 차원의 경우**, 차원에 유효한 값이 너무 많은 메트릭은(예: 높은 카디널리티) 50,000 제한에 도달할 가능성이 훨씬 높습니다. 일반적으로 차원에는 지속적으로 변화하는 값을 사용해서는 안됩니다. 예를 들어 타임스탬프는 절대로 차원이 되어서는 안 됩니다. 서버 고객 또는 productid는 이러한 유형의 개수가 더 적은 경우에만 사용할 수 있습니다. 시험 삼아 이러한 데이터를 그래프에 모두 표시하는지 자문해 보세요.  서버 10대 또는 100대가 있는 경우 비교를 위해 그래프에 모두 표시하는 것이 유용할 수 있습니다. 그러나 1000대가 있는 경우에는 결과 그래프를 읽을 수 없거나 어려울 수 있습니다. 유효한 100개 이하의 값을 유지하는 것이 가장 효율적입니다. 300개까지는 애매합니다.  300개 이상을 사용해야 하는 경우 Azure Monitor 사용자 지정 로그를 사용하세요.   

이름 또는 높은 카디널리티 차원에 변수가 있는 경우 다음 문제가 발생할 수 있습니다.
- 제한으로 인해 불안정한 메트릭
- 메트릭 탐색기가 작동하지 않음
- 예측이 불가능한 경고 및 알림
- 비용은 예기치 않게 증가할 수 있습니다. 이 기능이 공개 미리 보기로 제공되는 동안 Microsoft는 차원이 있는 사용자 지정 메트릭에 대해 요금을 청구하지 않습니다. 그러나 나중에 요금이 청구되기 시작하면 예기치 않은 요금이 발생합니다. 계획은 모니터링되는 시계열 수 및 API 호출 수에 따라 메트릭 사용에 대한 비용을 청구하는 것입니다.

## <a name="next-steps"></a>다음 단계
다음과 같은 다양한 서비스의 사용자 지정 메트릭을 사용합니다. 
 - [Virtual Machines](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
 - [가상 머신 크기 집합](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure Virtual Machines(클래식)](../essentials/collect-custom-metrics-guestos-vm-classic.md)
 - [Telegraf 에이전트를 사용하는 Linux 가상 머신](../essentials/collect-custom-metrics-linux-telegraf.md)
 - [REST API](./metrics-store-custom-rest-api.md)
 - [클래식 Cloud Services](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)
