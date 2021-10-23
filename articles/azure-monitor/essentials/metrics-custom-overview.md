---
title: Azure Monitor의 사용자 지정 메트릭 (미리 보기)
description: Azure Monitor의 사용자 지정 메트릭과 모델링 방법에 대해 알아봅니다.
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: cacb1d9af577a90d8e9030cf00e51dda58763041
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256632"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Azure Monitor의 사용자 지정 메트릭 (미리 보기)

Azure에서 리소스 및 애플리케이션을 배포하는 동안 성능 및 상태에 대한 정보를 얻기 위해 원격 분석 수집을 시작할 수 있습니다. Azure는 몇 가지 메트릭을 기본적으로 제공합니다. 이러한 메트릭을 [표준 또는 플랫폼](./metrics-supported.md)이라고 합니다. 그러나 제한적입니다. 

더욱 심층적인 인사이트를 제공하려면 일부 사용자 지정 성능 지표 또는 비즈니스 관련 메트릭을 수집하는 것이 좋습니다. 이러한 *사용자 지정* 메트릭은 응용 프로그램 원격 분석, Azure 리소스에서 실행 되는 에이전트 또는 외부 모니터링 시스템을 통해 수집할 수 있습니다. 그런 다음 Azure Monitor에 직접 제출할 수 있습니다. Azure Monitor에 사용자 지정 메트릭이 게시 된 후 Azure 리소스 및 응용 프로그램에 대 한 사용자 지정 메트릭을 표준 Azure 메트릭과 나란히 탐색, 쿼리 및 경고할 수 있습니다.

Azure Monitor 사용자 지정 메트릭은 현재 공개 미리 보기로 제공 됩니다. 

## <a name="methods-to-send-custom-metrics"></a>사용자 지정 메트릭을 보내는 방법

다음과 같은 여러 가지 방법으로 사용자 지정 메트릭을 Azure Monitor로 보낼 수 있습니다.
- Azure Application Insights SDK를 사용하여 애플리케이션을 계측하고 사용자 지정 원격 분석을 Azure Monitor로 보냅니다. 
- [Windows 또는 Linux Azure VM](../agents/azure-monitor-agent-overview.md)에 Azure Monitor 에이전트 (미리 보기)를 설치 하 고 [데이터 수집 규칙](../agents/data-collection-rule-azure-monitor-agent.md) 을 사용 하 여 성능 카운터를 Azure Monitor 메트릭에 보냅니다.
- [AZURE VM](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md), [가상 머신 확장 집합](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md), [클래식 VM](../essentials/collect-custom-metrics-guestos-vm-classic.md)또는 [클래식 클라우드 서비스](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)에 Azure 진단 확장을 설치 하 고 성능 카운터를 Azure Monitor으로 보냅니다. 
- Azure Linux VM에 [InfluxData Telegraf 에이전트](../essentials/collect-custom-metrics-linux-telegraf.md) 를 설치 하 고 Azure Monitor 출력 플러그 인을 사용 하 여 메트릭을 보냅니다.
- 사용자 지정 메트릭을 [Azure Monitor REST API`https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`, ](./metrics-store-custom-rest-api.md)에 직접 보냅니다.

## <a name="pricing-model-and-retention"></a>가격 책정 모델 및 보존

사용자 지정 메트릭 및 메트릭 쿼리에 대 한 대금 청구를 사용 하도록 설정 하는 경우에 대 한 자세한 내용은 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)를 참조 하세요. 요약 하자면, 표준 메트릭 (플랫폼 메트릭)을 Azure Monitor 메트릭 저장소에 수집 하는 비용은 없지만 사용자 지정 메트릭은 일반 공급으로 전환 될 때 비용을 초래 합니다. 메트릭 API에 대 한 쿼리는 비용이 발생 합니다.

사용자 지정 메트릭은 [플랫폼 메트릭과 동일한 시간](../essentials/data-platform-metrics.md#retention-of-metrics) 동안 보존됩니다. 

> [!NOTE]  
> Application Insights SDK를 통해 Azure Monitor로 전송되는 메트릭은 수집 로그 데이터로 청구됩니다. [사용자 지정 메트릭 차원에 대 한 경고를 사용 하도록 설정](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) 된 Application Insights 기능이 선택 된 경우에만 추가 메트릭 요금이 발생 합니다. 이 확인란은 사용자 지정 메트릭 API를 사용 하 여 더 복잡 한 경고를 허용 하 여 Azure Monitor 메트릭 데이터베이스로 데이터를 보냅니다.  [Application Insights 가격 책정 모델](../app/pricing.md#pricing-model) 및 [해당 지역의 가격](https://azure.microsoft.com/pricing/details/monitor/)에 대해 자세히 알아봅니다.

## <a name="how-to-send-custom-metrics"></a>사용자 지정 메트릭을 보내는 방법

Azure Monitor에 사용자 지정 메트릭을 보내는 경우 메트릭에 보고 된 각 데이터 요소 또는 값은 다음 정보를 포함 해야 합니다.

### <a name="authentication"></a>인증
사용자 지정 메트릭을 Azure Monitor로 전송하려면 메트릭을 전송하는 엔터티에 유효한 Azure AD(Azure Active Directory) 토큰이 요청의 **전달자** 헤더에 있어야 합니다. 유효한 전달자 토큰을 획득 하는 데 지원 되는 방법은 다음과 같습니다.

- [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md) 관리 되는 id를 사용 하 여 리소스에 특정 작업을 수행할 수 있는 권한을 부여할 수 있습니다. 예를 들어, 리소스가 해당 지표를 내보낼 수 있도록 허용합니다. 리소스 또는 관리 되는 id에 다른 리소스에 대 한 사용 권한을 **Publisher 모니터링 메트릭을** 부여할 수 있습니다. 이 사용 권한을 사용 하는 경우 관리 id는 다른 리소스에 대 한 메트릭을 내보낼 수도 있습니다.
- [AZURE AD 서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)입니다. 이 시나리오에서는 Azure 리소스에 대한 메트릭을 내보내는 권한을 Azure AD 애플리케이션 또는 서비스에 할당할 수 있습니다. 요청을 인증하기 위해 Azure Monitor는 Azure AD 공개 키를 사용하여 애플리케이션 토큰의 유효성을 검사합니다. 기존 **모니터링 메트릭 게시자** 역할에는 이 사용 권한이 이미 있으며, Azure Portal에서 사용할 수 있습니다. 

  서비스 주체는 사용자 지정 메트릭을 내보낼 리소스에 따라 필요한 범위에서 **모니터링 메트릭 게시자** 역할을 부여받을 수 있습니다. 범위의 예로 구독, 리소스 그룹 또는 특정 리소스가 있습니다.

> [!TIP]  
> 사용자 지정 메트릭을 내보내는 Azure AD 토큰을 요청 하는 경우 토큰이 요청 된 대상 그룹 또는 리소스가 인지 확인 `https://monitoring.azure.com/` 합니다. 후행 슬래시를 포함 해야 합니다.

### <a name="subject"></a>제목
Subject 속성은 사용자 지정 메트릭이 보고 되는 Azure 리소스 ID를 캡처합니다. 이 정보는 API 호출의 URL로 인코딩됩니다. 각 API는 단일 Azure 리소스에 대해서만 메트릭 값을 제출할 수 있습니다.

> [!NOTE]  
> 리소스 그룹 또는 구독의 리소스 ID에 대해서는 사용자 지정 메트릭을 내보낼 수 없습니다.

### <a name="region"></a>지역
Region 속성은 메트릭을 내보내는 리소스가 배포 되는 Azure 지역을 캡처합니다. 리소스를 배포 하는 지역과 동일한 Azure Monitor 지역 끝점으로 메트릭을 내보내야 합니다. 예를 들어, 미국 서부에 배포된 VM에 대한 사용자 지정 메트릭은 WestUS 지역별 Azure Monitor 엔드포인트에 전송되어야 합니다. 또한 지역 정보는 API 호출의 URL에 인코딩됩니다.

> [!NOTE]  
> 공개 미리 보기 중에는 Azure 지역의 하위 집합 에서만 사용자 지정 메트릭을 사용할 수 있습니다. 지원 되는 지역 목록은 [이 문서의 이후 섹션](#supported-regions)에서 설명 합니다.

### <a name="timestamp"></a>타임스탬프
Azure Monitor에 전송되는 각 데이터 요소는 타임스탬프를 사용하여 표시되어야 합니다. 이 타임 스탬프는 메트릭 값을 측정 하거나 수집 하는 날짜 및 시간을 캡처합니다. Azure Monitor는 과거 20분 및 미래 5분까지의 타임스탬프가 지정된 메트릭 데이터를 허용합니다. 타임스탬프는 ISO 8601 형식이어야 합니다.

### <a name="namespace"></a>네임스페이스
네임스페이스는 유사한 메트릭을 함께 분류 또는 그룹화하는 방법입니다. 네임스페이스를 사용하면 각기 다른 인사이트 또는 성능 지표를 수집할 수 있는 메트릭 그룹을 격리할 수 있습니다. 예를 들어, 앱을 프로파일링하는 메모리 사용 메트릭을 추적하는 **ContosoMemoryMetrics** 라는 네임스페이스가 있을 수 있습니다. **contosoapptransaction** 이라는 또 다른 네임스페이스는 애플리케이션의 사용자 트랜잭션에 대한 모든 메트릭을 추적할 수 있습니다.

### <a name="name"></a>Name
Name 속성은 보고 되는 메트릭의 이름입니다. 일반적으로 이름은 측정 대상을 식별하기에 충분한 정보를 제공합니다. 예는 VM에서 사용 되는 메모리 바이트 수를 측정 하는 메트릭입니다. 메트릭 이름은 **사용 중인 메모리 바이트** 일 수 있습니다.

### <a name="dimension-keys"></a>차원 키
차원은 수집 되는 메트릭에 대 한 추가 특성을 설명 하는 데 도움이 되는 키/값 쌍입니다. 추가적인 특성을 사용하면 메트릭에 대한 자세한 정보를 수집하여 더욱 심층적인 인사이트를 얻을 수 있습니다. 

예를 들어, **사용 중인 메모리 바이트** 메트릭에는 VM의 각 프로세스에서 사용 중인 메모리 바이트 수를 캡처하는 **프로세스** 라는 차원 키가 있을 수 있습니다. 이 키를 사용하면 메트릭을 필터링해 메모리별 프로세스 양을 확인하거나 메모리 사용량에 따른 상위 5개 프로세스를 식별할 수 있습니다.

차원은 선택 사항이 며 모든 메트릭에 차원이 있는 것은 아닙니다. 사용자 지정 메트릭에는 최대 10개의 차원이 있을 수 있습니다.

### <a name="dimension-values"></a>차원 값
메트릭 데이터 요소를 보고 하는 경우 보고 된 메트릭의 각 차원 키에 해당 하는 차원 값이 있습니다. 예를 들어 VM에서 ContosoApp가 사용 하는 메모리를 보고할 수 있습니다.

* 메트릭 이름은 **사용 중인 메모리 바이트** 가 됩니다.
* 차원 키는 **프로세스** 가 됩니다.
* 차원 값은 **ContosoApp.exe** 가 됩니다.

메트릭 값을 게시 하는 경우 차원 키 당 차원 값을 하나만 지정할 수 있습니다. VM의 여러 프로세스에 대해 수집한 메모리 사용률이 동일한 경우 해당 타임스탬프에 여러 개의 메트릭 값을 보고할 수 있습니다. 각 메트릭 값은 **프로세스** 차원 키에 대해 다른 차원 값을 지정합니다.

차원은 선택 사항 이지만 메트릭 게시에서 차원 키를 정의 하는 경우 해당 차원 값은 필수입니다.

### <a name="metric-values"></a>메트릭 값
Azure Monitor는 모든 메트릭을 1 분 단위 간격으로 저장 합니다. 지정 된 분 동안 메트릭이 여러 번 샘플링 되어야 할 수 있습니다. 예를 들면 CPU 사용률이 있습니다. 또는 로그인 트랜잭션 대기 시간과 같은 여러 불연속 이벤트에 대해 메트릭을 측정 해야 할 수 있습니다. 

Azure Monitor에서 내보내고 비용을 지불 해야 하는 원시 값의 수를 제한 하려면 로컬에서 값을 미리 집계 하 고 내보낼 수 있습니다.

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

응용 프로그램에서 로컬로 사전 집계할 수 없고 컬렉션에서 각 불연속 샘플과 이벤트를 즉시 내보내야 하는 경우 원시 측정값을 내보낼 수 있습니다. 예를 들어, 앱에서 로그인 트랜잭션이 발생할 때마다 단일 측정값만 사용하여 메트릭을 Azure Monitor에 게시합니다. 따라서 12 밀리초를 걸린 로그인 트랜잭션의 경우 메트릭 게시는 다음과 같습니다.
* 최소: 12
* 최대: 12
* 합계: 12
* 개수: 1

이 프로세스를 사용 하면 지정 된 분 동안 동일한 메트릭/차원 조합에 대 한 여러 값을 내보낼 수 있습니다. 그러면 Azure Monitor는 지정 된 시간 (분) 동안 내보낸 모든 원시 값을 가져와서 집계 합니다.

### <a name="sample-custom-metric-publication"></a>샘플 사용자 지정 메트릭 게시
다음 예제에서는 가상 머신에 대한 **메모리 프로필** 메트릭 네임스페이스에 **사용 중인 메모리 바이트** 라는 사용자 지정 메트릭을 만듭니다. 이 메트릭에는 **프로세스** 라는 단일 차원이 있습니다. 타임 스탬프의 경우 두 프로세스에 대 한 메트릭 값을 내보냅니다.

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

## <a name="custom-metric-definitions"></a>사용자 지정 메트릭 정의
내보내기 전에 사용자 지정 메트릭을 Azure Monitor에서 미리 정의할 필요가 없습니다. 게시된 각 메트릭 데이터 요소에는 네임스페이스, 이름 및 차원 정보가 포함되어 있습니다. 따라서 사용자 지정 메트릭을 Azure Monitor에 처음 내보낼 때 메트릭 정의가 자동으로 생성됩니다. 메트릭 정의를 통해 메트릭이 내보내지는 모든 리소스에서이 메트릭 정의를 검색할 수 있습니다.

> [!NOTE]  
> Azure Monitor는 사용자 지정 메트릭에 대한 **단위** 정의를 아직 지원하지 않습니다.

## <a name="using-custom-metrics"></a>사용자 지정 메트릭 사용
Azure Monitor에 사용자 지정 메트릭을 제출한 후 Azure Portal를 통해 검색 하 고 Azure Monitor REST Api를 통해 쿼리할 수 있습니다. 특정 조건이 충족되면 알리도록 메트릭에 대한 경고를 만들 수도 있습니다.

> [!NOTE]
> 사용자 지정 메트릭을 보려면 읽기 권한자 또는 참가자 역할이 있어야 합니다. [Monitoring Reader](../../role-based-access-control/built-in-roles.md#monitoring-reader)를 참조합니다. 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Azure Portal을 통해 사용자 지정 메트릭 찾아보기
1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. **모니터** 창을 선택합니다.
3. **메트릭** 을 선택합니다.
4. 사용자 지정 메트릭을 내보낸 리소스를 선택 합니다.
5. 사용자 지정 메트릭에 대한 메트릭 네임스페이스를 선택합니다.
6. 사용자 지정 메트릭을 선택합니다.

Azure Portal에서 메트릭을 보는 방법에 대 한 자세한 내용은 [Azure 메트릭 탐색기 시작](./metrics-getting-started.md)하기를 참조 하세요.

## <a name="supported-regions"></a>지원되는 지역
공개 미리 보기 중 사용자 지정 메트릭을 게시할 수 있는 기능은 일부 Azure 지역에서만 사용할 수 있습니다. 이 제한 사항은 지원되는 지역 중 하나의 리소스에 대해서만 메트릭을 게시할 수 있음을 의미합니다. Azure 지역에 대 한 자세한 내용은 [azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/)을 참조 하세요. 

다음 표에서는 사용자 지정 메트릭에 대해 지원 되는 Azure 지역을 나열 합니다. 또한 해당 지역의 리소스에 대 한 메트릭을 게시 해야 하는 해당 끝점을 나열 합니다. 끝점 접두사에 사용 되는 Azure 지역 코드는 공백이 제거 된 지역 이름입니다. 

|Azure 지역 |지역별 엔드포인트 접두사|
|---|---|
| 모든 퍼블릭 클라우드 지역 | https://<azure_region_code>.monitoring.azure.com |

## <a name="latency-and-storage-retention"></a>대기 시간 및 스토리지 보존

새로 추가 된 메트릭 또는 메트릭에 새로 추가 된 차원이 표시 되는 데 최대 3 분 정도 걸릴 수 있습니다. 데이터는 시스템에 있는 후 30 초에서 99 퍼센트 미만으로 표시 되어야 합니다. 

메트릭을 삭제하거나 차원을 제거하는 경우 변경 내용이 시스템에서 삭제되기까지 1주에서 1달 정도 소요될 수 있습니다.

## <a name="quotas-and-limits"></a>할당량 및 제한
Azure Monitor는 사용자 지정 메트릭에 대해 다음과 같은 사용량 한도를 적용합니다.

|범주|제한|
|---|---|
|배포한 모든 지역에서 구독의 총 활성 시계열|50,000|
|메트릭당 차원 키|10|
|메트릭 네임스페이스, 메트릭 이름, 차원 키 및 차원 값의 문자열 길이|256자|

활성 시계열은 지난 12시간 동안 메트릭 값이 게시된 메트릭, 차원 키 또는 차원 값의 고유한 조합으로 정의됩니다.

시계열에서 5만의 제한을 이해 하려면 다음 메트릭을 고려 합니다.

> 차원이 있는 서버 응답 시간: 지역, 부서, *CustomerID*

이 메트릭을 사용 하는 경우 10 개의 지역, 20 개 부서 및 100 고객이 있는 경우 10 x 20 x 100 = 2000 시계열을 제공 합니다. 

100 지역, 200 부서 및 2000 고객이 있는 경우이 메트릭에 대해서만 제한을 초과 하 여 100 x 200 x 2000 = 4000만 시계열을 제공 합니다. 

다시 말하지만, 이 제한은 개별 메트릭에 해당하지 않습니다. 구독 및 지역 전체에서 이러한 모든 메트릭의 합계를 위한 것입니다.  

## <a name="design-limitations-and-considerations"></a>디자인 제한 사항 및 고려 사항

**감사를 위해 Application Insights 사용** Application Insights 원격 분석 파이프라인은 성능 영향을 최소화 하 고 응용 프로그램 모니터링에서 네트워크 트래픽을 제한 하는 데 최적화 되어 있습니다. 따라서 초기 데이터 세트가 너무 커지면 이를 제한하거나 샘플링합니다(원격 분석의 일부만 사용하고 나머지는 무시함). 이 동작으로 인해 일부 레코드를 삭제할 가능성이 있으므로 감사 용도로 사용할 수 없습니다. 

**이름에 변수가 있는 메트릭입니다**. 변수를 메트릭 이름의 일부로 사용 하지 마세요. 대신 상수를 사용 합니다. 변수가 값을 변경할 때마다 Azure Monitor는 새 메트릭을 생성 합니다. 그러면 Azure Monitor는 메트릭 수에 대 한 제한을 빠르게 적중 합니다. 일반적으로 개발자가 메트릭 이름에 변수를 포함 하려는 경우 하나의 메트릭 내에서 여러 시계열을 추적 하 고 변수 메트릭 이름 대신 차원을 사용 해야 합니다. 

**높은 카디널리티 메트릭 차원** 입니다. 차원에 유효한 값이 너무 많은 메트릭은 ( *높은 카디널리티*) 5만 제한에 도달할 가능성이 훨씬 높습니다. 일반적으로 차원에는 지속적으로 변화하는 값을 사용해서는 안됩니다. 예를 들어 Timestamp는 차원이 되어서는 안 됩니다. 서버, 고객 또는 제품 ID를 사용할 수 있습니다. 단, 이러한 유형의 수가 더 적은 경우에만 가능 합니다. 

시험 삼아 이러한 데이터를 그래프에 모두 표시하는지 자문해 보세요. 서버 10대 또는 100대가 있는 경우 비교를 위해 그래프에 모두 표시하는 것이 유용할 수 있습니다. 그러나 1000가 있는 경우 결과 그래프를 읽을 수 없거나 읽을 수 없습니다. 가장 좋은 방법은 100 보다 작은 값을 유지 하는 것입니다. 300개까지는 애매합니다. 300개 이상을 사용해야 하는 경우 Azure Monitor 사용자 지정 로그를 사용하세요.   

이름 또는 고급 카디널리티 차원에 변수가 있으면 다음과 같은 상황이 발생할 수 있습니다.
- 조정으로 인해 메트릭이 불안정 해질 수 있습니다.
- 메트릭 탐색기 작동 하지 않습니다.
- 경고 및 알림을 예측할 수 없게 됩니다.
- 비용은 예기치 않게 늘어날 수 있습니다. Microsoft는이 기능이 공개 미리 보기로 제공 되는 동안 차원이 있는 사용자 지정 메트릭에 대해 요금을 청구 하지 않습니다. 그러나 나중에 요금이 청구 된 후에는 예기치 않은 요금이 발생 합니다. 이 계획은 모니터링 되는 시계열 수와 수행 된 API 호출 수에 따라 메트릭 사용에 대 한 비용을 청구 하는 것입니다.

메트릭 이름 또는 차원 값이 식별자 또는 상위 카디널리티 차원으로 잘못 채워진 경우 변수 부분을 제거 하 여 쉽게 수정할 수 있습니다.

그러나 높은 카디널리티가 시나리오에 필수적 이면 집계 된 메트릭이 적절 한 선택이 아닐 수 있습니다. 사용자 지정 로그를 사용 하 여 전환 합니다 (즉, [trackEvent](../app/api-custom-events-metrics.md#trackevent)를 사용 하는 지 수 메트릭 API 호출). 그러나 로그에서 값을 집계 하지 않으므로 모든 단일 항목이 저장 됩니다. 결과적으로 작은 기간의 로그 (예: 100만 초)가 많은 경우 제한 및 수집 지연이 발생할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
다양 한 서비스의 사용자 지정 메트릭 사용: 
 - [가상 머신](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
 - [가상 머신 크기 집합](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure 가상 머신 (클래식)](../essentials/collect-custom-metrics-guestos-vm-classic.md)
 - [Telegraf 에이전트를 사용 하는 Linux 가상 컴퓨터](../essentials/collect-custom-metrics-linux-telegraf.md)
 - [REST API](./metrics-store-custom-rest-api.md)
 - [클래식 클라우드 서비스](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)