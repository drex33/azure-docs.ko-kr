---
title: 진단 설정 및 작업 영역 구성
description: Azure Data Factory를 모니터링 하도록 진단 설정 및 Log Analytics 작업 영역을 구성 하는 방법에 대해 알아봅니다.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 7c982c0cb94ed5cf4a473265b1b414edbe0295a0
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400537"
---
# <a name="configure-diagnostic-settings-and-a-workspace"></a>진단 설정 및 작업 영역 구성

데이터 팩터리에 대한 진단 설정을 생성하거나 추가합니다.

1. Azure Portal에서 **모니터** 로 이동합니다. **설정**  >  **진단 설정** 을 선택 합니다.

1. 진단 설정을 지정하려는 데이터 팩터리를 선택합니다.

1. 선택한 데이터 팩터리에 설정이 없는 경우 설정을 생성하라는 메시지가 표시됩니다. **진단 켜기** 를 선택합니다.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image1.png" alt-text="설정이 없는 경우 진단 설정 만들기를 보여 주는 스크린샷":::

   데이터 팩터리에 대한 기존 설정이 있는 경우 데이터 팩터리에 대해 이미 구성된 설정 목록이 표시됩니다. **진단 설정 추가** 를 선택합니다.

   :::image type="content" source="media/data-factory-monitor-oms/add-diagnostic-setting.png" alt-text="설정이 있는 경우 진단 설정 추가를 보여 주는 스크린샷":::

1. 설정에 이름을 지정하고 **Log Analytics에 보내기** 를 선택한 후 **Log Analytics 작업 영역** 에서 작업 영역을 선택합니다.

    * _Azure-Diagnostics_ 모드에서 진단 로그는 _AzureDiagnostics_ 테이블로 흐릅니다.
    * _Resource-Specific_ 모드에서 Azure Data Factory의 진단 로그는 다음 테이블로 흐릅니다.
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      Log Analytics 테이블에 보낼 워크로드와 관련된 다양한 로그를 선택할 수 있습니다. 예: 
        - ssis (SQL Server Integration Services)를 사용 하지 않는 경우 ssis 로그를 선택할 필요가 없습니다. 
        - SSIS IR (통합 런타임) 시작, 중지 또는 유지 관리 작업을 기록 하려면 SSIS IR 로그를 선택 합니다. 
        - SQL Server Management Studio, SQL Server 에이전트 또는 기타 지정 된 도구에서 T-SQL를 통해 ssis 패키지 실행을 호출 하는 경우 ssis 패키지 로그를 선택 합니다. 
        - Data Factory 파이프라인에서 SSIS 패키지 실행 작업을 통해 SSIS 패키지 실행을 호출 하는 경우 모든 로그를 선택 합니다.

    * _Allmetrics_ 을 선택 하는 경우 다양 한 Data Factory 메트릭을 사용 하 여 경고를 모니터링 하거나 발생 시킬 수 있습니다. 이러한 메트릭에는 Data Factory 활동, 파이프라인 및 트리거 실행에 대 한 메트릭과 SSIS IR 작업 및 SSIS 패키지 실행에 대 한 메트릭이 포함 됩니다.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image2.png" alt-text="설정의 이름을 지정 하 고 Log Analytics 작업 영역을 선택 하는 스크린샷":::

    > [!NOTE]
    > Azure 로그 테이블에는 500 개 이상의 열을 사용할 수 없기 때문에 _리소스 특정 모드_ 를 선택 하는 것 *이 좋습니다* . 자세한 내용은 [Azure 진단 로그 참조](/azure/azure-monitor/reference/tables/azurediagnostics)를 참조 하세요.

1. **저장** 을 선택합니다.

잠시 후 이 데이터 팩터리의 설정 목록에 새 설정이 나타납니다. 새 이벤트 데이터가 생성되는 즉시 진단 로그가 해당 작업 영역으로 스트리밍됩니다. 이벤트가 내보내지는 시점에서 Log Analytics에 표시되는 시점까지 최대 15분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Monitor REST API를 통해 진단 로그를 설정 합니다.](monitor-logs-rest.md)
