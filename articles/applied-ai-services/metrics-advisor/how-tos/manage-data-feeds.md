---
title: Metrics Advisor에서 데이터 피드 관리
titleSuffix: Azure Cognitive Services
description: Metrics Advisor에 추가된 데이터 피드를 관리하는 방법을 알아봅니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 04/20/2021
ms.author: mbullwin
ms.openlocfilehash: dadefe25926b5a3e3c81cef21ff5d030692ccde4
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342030"
---
# <a name="how-to-manage-your-data-feeds"></a>방법: 데이터 피드 관리

Metrics Advisor에서 온보딩된 데이터 피드를 관리하는 방법에 대해 알아봅니다. 이 문서에서는 Metrics Advisor에서 데이터 피드를 관리하는 과정을 안내합니다.

## <a name="edit-a-data-feed"></a>데이터 피드 편집

> [!NOTE]
> 데이터 피드를 만든 후에는 다음 세부 정보를 변경할 수 없습니다. 
> * 데이터 피드 ID
> * 만든 시간
> * 차원
> * 원본 유형
> * 세분성

데이터 피드는 해당 관리자만 변경할 수 있습니다. 

데이터 피드 목록 페이지에서 데이터 피드를 **일시 중지, 재활성화, 삭제** 할 수 있습니다.

* **일시 중지/재활성화**: **일시 중지/재생** 단추를 선택하여 데이터 피드를 일시 중지/재활성화합니다.

* **삭제**: **삭제** 단추를 선택하여 데이터 피드를 삭제합니다.

수집 시작 시간을 변경하는 경우 스키마를 다시 확인해야 합니다. 데이터 피드 세부 정보 페이지에서 **편집** 을 클릭하여 변경할 수 있습니다.

##  <a name="backfill-your-data-feed"></a>데이터 피드 백필

실패한 수집을 수정하거나 기존 데이터를 재정의하기 위해 **백필** 단추를 선택하여 타임스탬프에 대한 즉각적인 수집을 트리거합니다.
- 시작 시간은 포함됩니다.
- 종료 시간은 제외됩니다.
- 변칙 검색이 선택한 범위에서만 다시 트리거됩니다.

:::image type="content" source="../media/datafeeds/backfill-datafeed.png" alt-text="데이터 피드 백필":::

## <a name="manage-permission-of-a-data-feed"></a>데이터 피드의 권한 관리

작업 영역 액세스는 인증을 위해 Azure Active Directory를 사용하는 Metrics Advisor 리소스에 의해 제어됩니다. 또 다른 권한 제어 계층이 메트릭 데이터에 적용됩니다.

Metrics Advisor를 사용하면 서로 다른 데이터 피드에 있는 여러 그룹의 사용자에게 권한을 부여할 수 있습니다. 역할에는 다음과 같은 두 가지 유형이 있습니다. 

- **관리자**: 수정 및 삭제를 포함하여 데이터 피드를 관리할 수 있는 전체 권한이 있습니다.
- **뷰어**: 데이터 피드의 읽기 전용 보기에 액세스할 수 있습니다.
 

## <a name="advanced-settings"></a>고급 설정

새 데이터 피드를 만들 때 몇 가지 선택적 고급 설정이 있으며, 이러한 설정은 데이터 피드 세부 정보 페이지에서 수정할 수 있습니다.

### <a name="ingestion-options"></a>수집 옵션

* **수집 시간 오프셋**: 기본적으로 데이터는 지정된 세분성에 따라 수집됩니다. 예를 들어 *매일* 타임스탬프를 포함하는 메트릭은 타임스탬프 1일 후에 수집됩니다. 오프셋을 사용할 경우 *양수* 를 사용하여 수집 시간을 지연하거나 *음수* 를 사용하여 수집 시간을 앞으로 당길 수 있습니다.

* **최대 동시성**: 데이터 원본이 제한된 동시성을 지원하는 경우 이 매개 변수를 설정합니다. 그렇지 않으면 기본 설정을 그대로 둡니다.

* **다음 이후 다시 시도 중지**: 데이터 수집에 실패한 경우 일정 기간 내에 자동으로 다시 시도됩니다. 이 기간의 시작은 첫 번째 데이터 수집이 발생한 시간입니다. 기간의 길이는 세분성에 따라 정의됩니다. 기본값(-1)을 사용하는 경우 값은 아래와 같이 세분성에 따라 결정됩니다.
    
    | 세분성       | 다음 시간 후 다시 시도 중지           |
    | :------------ | :--------------- |
    | 매일, 사용자 지정(1일 이상), 매주, 매월, 매년     | 7 일          |
    | 매시간, 사용자 지정(1일 미만)       | 72시간 |

* **최소 다시 시도 간격**: 원본에서 데이터를 다시 가져올 때 최소 간격을 지정할 수 있습니다. 기본값(-1)이 아닌 값을 사용하는 경우 다시 시도 간격은 아래와 같이 세분성에 따라 결정됩니다.
    
    | 세분성       | 최소 다시 시도 간격           |
    | :------------ | :--------------- |
    | 매일, 사용자 지정(1일 이상), 매주, 매월     | 30분          |
    | 매시간, 사용자 지정(1일 미만)      | 10분 |
    | 매년 | 1일          |
 
### <a name="fill-gap-when-detecting"></a>검색 시 간격 채우기: 

> [!NOTE]
> 이 설정은 데이터 원본에 영향을 주지 않으며 포털에 표시되는 데이터 차트에도 영향을 주지 않습니다. 자동 채우기는 변칙 검색 중에만 발생합니다.

시리즈가 연속되지 않을 때도 있습니다. 누락된 데이터 포인트가 있는 경우 Metrics Advisor는 정확도를 개선하기 위해 변칙 검색 전에 지정된 값으로 채웁니다.
옵션은 다음과 같습니다. 

* 이전의 실제 데이터 포인트 값을 사용합니다. 이는 기본적으로 사용됩니다.
* 특정 값을 사용합니다.

### <a name="action-link-template"></a>작업 링크 템플릿: 

작업 링크 템플릿은 자리 표시자 `%datafeed`, `%metric`, `%timestamp`, `%detect_config` 및 `%tagset`로 구성된 실행 가능한 HTTP URL을 미리 정의하는 데 사용됩니다. 템플릿을 사용하여 변칙 또는 인시던트에서 특정 URL로 리디렉션하여 드릴다운할 수 있습니다.

:::image type="content" source="../media/action-link-template.png" alt-text="작업 링크 템플릿" lightbox="../media/action-link-template.png":::

작업 링크를 입력한 후 인시던트 목록의 작업 옵션 및 진단 트리의 오른쪽 클릭 메뉴에서 **작업 링크로 이동** 을 클릭합니다. 작업 링크 템플릿의 자리 표시자를 변칙 또는 인시던트에 해당하는 값으로 바꿉니다.

| 자리표시자 | 예 | 설명 |
| ---------- | -------- | ------- |
| `%datafeed` | - | 데이터 피드 ID |
| `%metric` | - | 메트릭 ID |
| `%detect_config` | - | 검색 구성 ID |
| `%timestamp` | - | 영구 인시던트의 변칙 또는 종료 시간의 타임스탬프 |
| `%tagset` | `%tagset`, <br> `[%tagset.get("Dim1")]`, <br> `[ %tagset.get("Dim1", "filterVal")]` | 인시던트의 변칙 또는 상위 변칙의 차원 값.   <br> `filterVal`은 대괄호 내에서 일치하는 값을 필터링하는 데 사용됩니다.   |

예제:

* 작업 링크 템플릿이 `https://action-link/metric/%metric?detectConfigId=%detect_config`인 경우:
  * 작업 링크 `https://action-link/metric/1234?detectConfigId=2345`는 메트릭 `1234`의 변칙 또는 인시던트로 이동하고 구성 `2345`를 검색합니다.

* 작업 링크 템플릿이 `https://action-link?[Dim1=%tagset.get('Dim1','')&][Dim2=%tagset.get('Dim2','')]`인 경우:
    * 변칙이 `{ "Dim1": "Val1", "Dim2": "Val2" }`인 경우 작업 링크는 `https://action-link?Dim1=Val1&Dim2=Val2`입니다. 
    * 빈 문자열인 차원 값에 대해 `[Dim1=***&]`를 건너뛰므로 변칙이 `{ "Dim1": "", "Dim2": "Val2" }`인 경우 작업 링크는 `https://action-link?Dim2=Val2`입니다. 

* 작업 링크 템플릿이 `https://action-link?filter=[Name/Dim1 eq '%tagset.get('Dim1','')' and ][Name/Dim2 eq '%tagset.get('Dim2','')']`인 경우: 
    * 변칙이 `{ "Dim1": "Val1", "Dim2": "Val2" }`인 경우 작업 링크는 `https://action-link?filter=Name/Dim1 eq 'Val1' and Name/Dim2 eq 'Val2'`입니다. 
    * 빈 문자열인 차원 값에 대해 `[Name/Dim1 eq '***' and ]`를 건너뛰므로 변칙이 `{ "Dim1": "", "Dim2": "Val2" }`인 경우 작업 링크는 `https://action-link?filter=Name/Dim2 eq 'Val2'`입니다. 
   
### <a name="data-feed-not-available-alert-settings"></a>"데이터 피드를 사용할 수 없음" 경고 설정

데이터 피드가 수집을 시작한 시점부터 지정된 유예 기간 내에 원본에서 수집된 데이터가 없으면 데이터 피드는 사용할 수 없는 것으로 간주됩니다. 이 경우 경고가 트리거됩니다.

경고를 구성하려면 먼저 [후크를 만들어야](alerts.md#create-a-hook) 합니다. 경고는 구성된 후크를 통해 전송됩니다.

* **유예 기간**: 유예 기간 설정은 데이터 포인트가 수집되지 않은 경우 경고를 보낼 시기를 결정하는 데 사용됩니다. 참조 포인트는 최초 수집 시간입니다. 수집에 실패하면 Metrics Advisor가 세분성에 지정된 간격으로 계속 시도합니다. 유예 기간이 지난 후에도 계속 실패할 경우 경고가 전송됩니다.

* **자동 다시 알림**: 이 옵션을 0으로 설정하면 *사용할 수 없음* 인 타임스탬프마다 경고가 트리거됩니다. 0이 아닌 설정을 지정하면 지정된 설정에 따라 *사용할 수 없음* 인 첫 번째 타임스탬프 이후 연속 타임스탬프가 트리거되지 않습니다.

## <a name="next-steps"></a>다음 단계
- [메트릭을 구성하고 검색 구성 미세 조정](configure-metrics.md)
- [피드백을 사용하여 변칙 검색 조정](anomaly-feedback.md)
- [인시던트 진단](diagnose-an-incident.md)
