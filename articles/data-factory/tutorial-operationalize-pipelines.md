---
title: 데이터 파이프라인 운영
description: 데이터 파이프라인에 대한 서비스 수준 계약을 제공하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: tutorial
ms.date: 09/22/2021
ms.openlocfilehash: d80d2abf4d51d99fb207ec728e29b362079ba64a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207548"
---
# <a name="deliver-service-level-agreement-for-data-pipelines"></a>데이터 파이프라인에 대한 서비스 수준 계약 제공

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory는 전 세계 기업들의 사랑과 신뢰를 받고 있습니다. 스케일 아웃 서버리스 데이터 통합 및 데이터 변환을 위한 Azure의 기본 클라우드 ETL 서비스이며 데이터를 준비하고, 처리하고, 엔터프라이즈 데이터 웨어하우스 또는 데이터 레이크에 로드하는 데 널리 사용됩니다.

데이터 파이프라인이 [CI/CD(연속 통합 및 지속적인 업데이트)](continuous-integration-delivery.md)를 통해 Git 모드에서 또는 라이브 모드에서 직접 게시되면 일반적으로 Autopilot에서 실행됩니다. 데이터 파이프라인은 미리 정의된 일정에 따라 [일정 트리거](how-to-create-schedule-trigger.md) 또는 [연속 창 트리거](how-to-create-tumbling-window-trigger.md)를 사용하여 실행되거나, 이벤트 기반 아키텍처에서 [스토리지 이벤트 트리거](how-to-create-event-trigger.md) 또는 [사용자 지정 이벤트 트리거](how-to-create-custom-event-trigger.md)를 사용하여 실행됩니다. 데이터 파이프라인에는 비즈니스 보고서 또는 데이터 분석과 기계 학습 프로젝트에 사용할 데이터를 준비하는 중요한 워크로드가 위임됩니다.

## <a name="preemptive-warnings-for-long-running-jobs"></a>장기 실행 작업에 대한 선제적 경고

이러한 데이터 파이프라인에 대한 SLA(서비스 수준 계약)를 제공할 때 다음과 같은 두 가지 중요한 문제가 있습니다.

* 작업의 컴퓨팅 환경(예: 저장 프로시저 작업의 경우 SQL)에서 전체 데이터 파이프라인의 속도를 제한하여 파이프라인 SLA를 위태롭게 만들 수 있습니다.
* 파이프라인 개발자가 항상 팩터리를 적극적으로 모니터링하면서 SLA에 미달하게 될 장기 실행 파이프라인을 사전에 찾는 것은 아닙니다.

이러한 문제를 해결하기 위해, 파이프라인이 올바르게 구성될 경우 SLA에 미달하면 파이프라인 실행에서 _경과된 시간 파이프라인 실행_ 메트릭을 내보냅니다. [Data Factory 경고](monitor-metrics-alerts.md#data-factory-alerts)와 결합하면 데이터 파이프라인 개발자는 고객에게 SLA를 보다 잘 제공할 수 있습니다. 즉, 파이프라인이 실행되어야 하는 기간을 알려주시면 파이프라인이 예상보다 오래 실행될 때 미리 알려드립니다.

작성 단계에서 각 파이프라인에 대한 경고를 만들고 싶은 경우 파이프라인 캔버스의 빈 공간을 클릭하여 파이프라인 설정으로 이동합니다.

:::image type="content" source="media/tutorial-operationalize-pipelines/elapsed-time-1-set-up.png" alt-text="파이프라인의 예상 실행 기간을 지정하는 방법을 보여주는 스크린샷":::

__설정__ 탭에서 경과 시간 메트릭을 확인하고, 예상 파이프라인 실행 기간을 `D.HH:MM:SS` 형식으로 지정합니다. 비즈니스 SLA, 즉, 비즈니스 요구를 충족하기 위해 파이프라인 실행에 허용되는 시간으로 설정하는 것이 좋습니다. 파이프라인 실행 기간이 이 설정을 초과하면 Data Factory가 Azure Monitor에서 _경과된 시간 파이프라인 실행_ 메트릭(메트릭 ID: `PipelineElapsedTimeRuns`)을 기록합니다. 즉, 파이프라인이 최종적으로 완료되기 전에 장기 실행 파이프라인에 대한 알림이 _선제적으로_ 제공됩니다.

일부 파이프라인은 더 많은 단계를 포함하고 있거나 더 많은 데이터를 이동하기 때문에 다른 파이프라인보다 완료하는 데 더 오래 걸린다는 것을 저희도 알고 있습니다. 한 가지 정의로 장기 실행 파이프라인을 모두 해결할 수는 없습니다. SLA가 필요한 파이프라인마다 임계값을 정의하는 것이 좋습니다. 또한 특정 파이프라인에 대한 메트릭을 로깅할 때 예상 실행 기간에 대한 사용자 정의 설정과 비교합니다.

> [!NOTE]
> 이것은 파이프라인별 옵트인 기능입니다. 앞서 언급한 파이프라인의 예상 실행 기간이 지정되지 않으면 파이프라인에 대한 메트릭이 기록되지 않습니다.

단계에 따라 메트릭에 대한 [Data Factory 경고](monitor-metrics-alerts.md#data-factory-alerts)를 설정하세요. 개입하여 SLA를 충족하기 위한 단계를 수행하라는 알림이 이메일 또는 SMS를 통해 엔지니어에게 제공됩니다.

## <a name="next-steps"></a>다음 단계

[Data Factory 메트릭 및 경고](monitor-metrics-alerts.md)

[시각적으로 모니터링](monitor-visually.md#alerts)
