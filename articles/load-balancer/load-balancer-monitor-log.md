---
title: 공용 부하 분산 장치에 대한 작업, 이벤트 및 카운터 모니터링
titleSuffix: Azure Load Balancer
description: Azure Load Balancer에 대한 로깅을 사용하도록 설정하는 방법을 알아봅니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 9eacbcbb73acffb5625d2f2a7e8594c27992307e
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112466612"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Azure 표준 Load Balancer용 Azure Monitor 로그

서로 다른 유형의 Azure Monitor 로그를 사용하여 Azure 표준 Load Balancer를 관리하고 문제를 해결할 수 있습니다. 로그는 이벤트 허브 또는 Log Analytics 작업 영역으로 스트리밍할 수 있습니다. Azure Blob Storage에서 모든 로그를 추출하고 Excel 및 Power BI와 같은 도구에서 볼 수 있습니다. 

로그 유형은 다음과 같습니다.

* **활동 로그:** Azure 구독에 제출되는 모든 활동을 해당 상태와 함께 볼 수 있습니다. 자세한 내용은 [리소스에 대한 작업을 모니터링하기 위해 활동 로그 보기](../azure-resource-manager/management/view-activity-logs.md)를 참조하세요. 활동 로그는 기본적으로 사용하도록 설정되며 Azure Portal에서 볼 수 있습니다. 이러한 로그는 Azure Basic Load Balancer와 표준 Load Balancer 모두에 사용할 수 있습니다.
* **표준 Load Balancer 메트릭:** 이 로그를 사용하여 표준 Load Balancer에 대한 로그로 내보낸 메트릭을 쿼리할 수 있습니다. 이러한 로그는 표준 Load Balancer에만 사용할 수 있습니다.

> [!IMPORTANT]
> 상태 프로브 및 Load Balancer 경고 이벤트 로그는 현재 작동하지 않으며 [Azure Load Balancer에 대한 알려진 문제](whats-new.md#known-issues)에 나열되어 있습니다. 

> [!IMPORTANT]
> 로그는 Azure Resource Manager 배포 모델에 배포된 리소스에만 사용할 수 있습니다. 클래식 배포 모델에서 리소스에 대한 로그를 사용할 수 없습니다. 배포 모델에 대한 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 이해](../azure-resource-manager/management/deployment-models.md)를 참조하세요.

## <a name="enable-logging"></a>로깅 사용

활동 로깅은 모든 Resource Manager 리소스에 대해 사용하도록 설정됩니다. 이러한 로그를 통해 사용 가능한 데이터 수집을 시작하려면 이벤트 및 상태 프로브 로깅을 사용하도록 설정합니다. 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 부하 분산 장치가 아직 없으면, 계속하기 전에 [부하 분산 장치를 만듭니다](./quickstart-load-balancer-standard-public-portal.md) .
1. 포털에서 **리소스 그룹** 을 선택합니다.
2. 부하 분산 장치가 있는 **\<resource-group-name>** 을 선택합니다.
3. 부하 분산 장치를 선택합니다.
4. **활동 로그** > **진단 설정** 을 선택합니다.
5. **진단 설정** 창의 **진단 설정** 에서 **+ 진단 설정 추가** 를 선택합니다.
6. **진단 설정** 생성 창의 **이름** 상자에 **myLBDiagnostics** 를 입력합니다.
7. **진단 설정** 에 대한 세 가지 옵션이 있습니다. 하나, 두 개 또는 세 가지를 모두 선택하고 요구 사항에 맞게 각각 구성할 수 있습니다.

   * **스토리지 계정에 보관**. 이 프로세스에 대해 이미 만들어진 스토리지 계정이 필요합니다. 스토리지 계정을 만들려면 [스토리지 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal)를 참조하세요.
     1. **스토리지 계정에 보관** 확인란을 선택합니다.
     2. **구성** 을 선택하여 **스토리지 계정 선택** 창을 엽니다.
     3. **구독** 드롭다운 목록에서 스토리지 계정이 만들어진 구독을 선택합니다.
     4. **스토리지 계정** 드롭다운 목록에서 스토리지 계정 이름을 선택합니다.
     5. **확인** 을 선택합니다.

   * **이벤트 허브로 스트림**. 이 프로세스에 대한 이벤트 허브가 이미 생성되어 있어야 합니다. 이벤트 허브를 만들려면 [빠른 시작: Azure Portal을 사용하여 이벤트 허브 만들기](../event-hubs/event-hubs-create.md)를 참조하세요.
     1. **이벤트 허브로의 스트림** 확인란을 선택합니다.
     2. **구성** 을 선택하여 **이벤트 허브 선택** 창을 엽니다.
     3. **구독** 드롭다운 목록에서 이벤트 허브가 만들어진 구독을 선택합니다.
     4. **이벤트 허브 네임스페이스 선택** 드롭다운 목록에서 네임스페이스를 선택합니다.
     5. **이벤트 허브 정책 이름 선택** 드롭다운 목록에서 이름을 선택합니다.
     6. **확인** 을 선택합니다.

   * **Log Analytics에 보내기**. 이 프로세스에 대한 로그 분석 작업 영역을 만들고 구성해야 합니다. Log Analytics 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)를 참조하세요.
     1. **Log Analytics에 보내기** 확인란을 선택합니다.
     2. **구독** 드롭다운 목록에서 Log Analytics 작업 영역이 있는 구독을 선택합니다.
     3. **Log Analytics 작업 영역** 드롭다운 목록에서 작업 영역을 선택합니다.

8. **진단 설정** 창의 **메트릭** 섹션에서 **AllMetrics** 확인란을 선택합니다.

9. 모든 항목이 올바른지 확인한 다음, **진단 설정** 만들기 창의 위쪽에서 **저장** 을 선택합니다.

## <a name="view-and-analyze-the-activity-log"></a>활동 로그 보기 및 분석

활동 로그는 기본적으로 생성됩니다. [이 문서의 지침에 따라](../azure-monitor/essentials/activity-log.md) 구독 수준에서 내보내도록 구성할 수 있습니다. [리소스에 대한 작업을 모니터링하기 위해 활동 로그 보기](../azure-resource-manager/management/view-activity-logs.md) 문서를 읽어 이러한 로그에 대해 자세히 알아보세요.

다음 방법 중 하나를 사용하여 활동 로그 데이터를 확인하고 분석할 수 있습니다.

* **Azure 도구** - Azure PowerShell, Azure CLI, Azure REST API 또는 Azure Portal을 통해 활동 로그에서 정보를 검색합니다. [Resource Manager로 작업 감사](../azure-resource-manager/management/view-activity-logs.md) 문서는 각 방법에 대한 단계별 지침을 제공합니다.
* **Power BI:**[Power BI](https://powerbi.microsoft.com/pricing) 계정이 아직 없는 경우에는 무료로 사용해볼 수 있습니다. [Power BI에 대한 Azure 감사 로그 통합](https://powerbi.microsoft.com/integrations/azure-audit-logs/)을 사용하여 미리 구성된 대시보드로 데이터를 분석할 수 있습니다. 또는 요구 사항에 맞게 보기를 사용자 지정할 수 있습니다.

## <a name="view-and-analyze-metrics-as-logs"></a>메트릭을 로그로 보기 및 분석
Azure Monitor에서 내보내기 기능을 사용하여 Load Balancer 메트릭을 내보낼 수 있습니다. 이러한 메트릭은 1분 간격의 각 샘플링 간격에 대한 로그 항목을 생성합니다.

메트릭-로그 내보내기는 리소스 수준에서 사용하도록 설정됩니다. 이러한 로그를 사용하도록 설정하려면:

1. **진단 설정** 창으로 이동합니다.
1. 리소스 그룹별로 필터링한 다음, 메트릭 내보내기를 사용하도록 설정하려는 Load Balancer 인스턴스를 선택합니다. 
1. Load Balancer에 대한 진단 설정 페이지가 켜져 있으면 **AllMetrics** 를 선택하여 적격 메트릭을 로그로 내보냅니다.

메트릭 내보내기 제한 사항은 이 문서의 [제한 사항](#limitations) 섹션을 참조하세요.

표준 Load Balancer의 진단 설정에서 **AllMetrics** 를 사용하도록 설정하고 나면 이벤트 허브 또는 Log Analytics 작업 영역을 사용하는 경우 이러한 로그는 **AzureMetrics** 테이블에 채워집니다. 

스토리지로 내보내는 경우 스토리지 계정에 연결하고 이벤트 및 상태 프로브 로그에 대한 JSON 로그 항목을 검색합니다. JSON 파일을 다운로드한 후 CSV로 변환하여 Excel, Power BI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다. 

> [!TIP]
> Visual Studio를 익숙하게 사용할 수 있고 C#에서 상수 및 변수에 대한 값 변경에 대한 기본 개념이 있는 경우 GitHub에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter)를 사용할 수 있습니다.

## <a name="stream-to-an-event-hub"></a>이벤트 허브로 스트림
진단 정보를 이벤트 허브로 스트리밍하는 경우 Azure Monitor 통합을 사용하여 파트너 SIEM 도구의 중앙 집중식 로그 분석에 사용할 수 있습니다. 자세한 내용은 [Azure 모니터링 데이터를 이벤트 허브로 스트리밍](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)을 참조하세요.

## <a name="send-to-log-analytics"></a>Log Analytics에 보내기
Azure의 리소스에 대한 진단 정보를 Log Analytics 작업 영역에 직접 보낼 수 있습니다. 해당 작업 영역에서 문제 해결 및 분석을 위한 정보에 대해 복잡한 쿼리를 실행할 수 있습니다. 자세한 내용은 [Azure Monitor의 Log Analytics 작업 영역에서 Azure 리소스 로그 수집](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)을 참조하세요.

## <a name="limitations"></a>제한 사항
Azure Load Balancer에 대한 메트릭-로그 내보내기 기능에는 다음과 같은 제한 사항이 있습니다.
* 현재 메트릭은 로그로 내보낼 때 내부 이름을 통해 표시됩니다. 아래 표에서 매핑을 찾을 수 있습니다.
* 메트릭의 차원은 유지되지 않습니다. 예를 들어 **DipAvailability**(상태 프로브 상태)와 같은 메트릭을 사용하는 경우 백 엔드 IP 주소를 사용하여 분할하거나 볼 수 없습니다.
* 사용된 SNAT 포트 및 할당된 SNAT 포트에 대한 메트릭은 현재 로그로 내보내기에 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계
* [부하 분산 장치에 사용 가능한 메트릭 검토](./load-balancer-standard-diagnostics.md)
* [Azure Monitor 지침에 따라 쿼리 만들기 및 테스트](../azure-monitor/logs/log-query-overview.md)
