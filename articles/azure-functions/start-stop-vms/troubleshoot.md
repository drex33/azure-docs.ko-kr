---
title: VM 시작/중지(미리 보기) 문제 해결
description: 이 문서에서는 Azure VM의 시작/중지(미리 보기) 기능에서 발생하는 문제를 해결하는 방법을 설명합니다.
services: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.topic: conceptual
ms.openlocfilehash: 2908f9a8383897ae0bda162e6f980e02d6c81bc1
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112966992"
---
# <a name="troubleshoot-common-issues-with-startstop-vms-preview"></a>VM 시작/중지(미리 보기)와 관련된 일반적인 문제 해결

이 문서에서는 VM 시작/중지(미리 보기)를 설치하고 구성하는 동안 발생할 수 있는 문제를 해결하는 방법에 대한 정보를 제공합니다. 일반 정보는 [VM 시작/중지 개요](overview.md)를 참조하세요.

## <a name="general-validation-and-troubleshooting"></a>일반적인 유효성 검사 및 문제 해결

이 섹션에서는 일정 시나리오와 관련된 일반적인 문제를 해결하는 방법을 다루고 근본 원인을 파악하는 데 도움을 줍니다.

### <a name="azure-dashboard"></a>Azure 대시보드

Azure 공유 대시보드를 검토하여 시작할 수 있습니다. VM v2 시작/중지(미리 보기)의 일부로 배포되는 Azure 공유 대시보드는 VM에서 수행되는 각 작업의 상태를 쉽고 빠르게 확인하는 방법입니다. VM에서 실행된 최근 작업을 모두 보려면 **VM에서 최근 시도된 작업** 타일을 참조하세요. Application Insights 리소스에서 데이터를 풀할 때 데이터가 보고서에 표시되는 데 5분 정도 대기 시간이 걸립니다.

### <a name="logic-apps"></a>Logic Apps

시작/중지 시나리오를 지원하는 데 사용하는 Logic Apps에 따라 실행 기록을 검토하여 예약된 시작/종료 시나리오가 하나 이상의 대상 VM에 대해 성공적으로 완료되지 않은 이유를 식별할 수 있습니다. 이를 자세히 검토하는 방법에 대한 자세한 내용은 [Logic Apps 실행 기록](../../logic-apps/monitor-logic-apps.md#review-runs-history)을 참조하세요.

### <a name="azure-storage"></a>Azure Storage

VM v2 시작/중지(미리 보기)에 사용되는 Azure 스토리지 계정의 **requestsstoretable** 테이블에 기록된 VM에서 수행된 작업에 대한 세부 정보를 검토할 수 있습니다. 레코드를 보려면 다음 단계를 수행합니다.

1. Azure Portal의 저장소 계정으로 이동하고 계정의 왼쪽 창에 있는 **저장소 탐색기(미리 보기)** 를 선택합니다.
1. **테이블** 을 선택한 다음 **requeststoretable** 을 선택합니다.
1. 테이블의 각 레코드는 논리 앱 시나리오에 정의된 대상 범위에 따라 Azure VM에 대해 수행되는 시작/중지 동작을 나타냅니다. 레코드 속성(예: 타임스탬프, 동작 또는 TARGETTOPLEVELRESOURCENAME) 중 하나를 기준으로 결과를 필터링할 수 있습니다.

### <a name="azure-functions"></a>Azure Functions

VM을 시작하고 실행을 중지하는 Azure Functions의 호출에 대한 최신 세부 정보를 검토할 수 있습니다. 먼저 실행 흐름을 검토해 보겠습니다.

**예약된** 시나리오와 **순차화된** 시나리오의 실행 흐름은 동일한 함수에 의해 제어됩니다. 페이로드 스키마가 수행할 시나리오를 결정합니다. **예약된** 시나리오의 경우 실행 흐름은 다음과 같습니다. **예약된** HTTP > **VirtualMachineRequestOrchestrator** 큐 > **VirtualMachineRequestExecutor** 큐.

논리 앱에서 **예약된** HTTP 함수는 페이로드 스키마로 호출됩니다. **예약된** HTTP 함수가 요청을 수신하고 나면 정보를 **오케스트레이터** 큐 함수로 보냅니다. 그러면 각 VM에 여러 큐가 생성되어 작업을 수행합니다.

호출 세부 정보를 보려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Azure Functions** 로 이동합니다.
1. 목록에서 VM v2 시작/중지(미리 보기)에 대한 함수 앱을 선택합니다.
1. 왼쪽 창에서 **함수** 를 선택합니다.
1. 목록에는 각 시나리오에 연결된 여러 함수가 표시됩니다. **예약된** HTTP 함수를 선택합니다.
1. 그런 다음, 왼쪽 창에서 **모니터링** 을 선택합니다.
1. 최신 실행 추적을 선택하여 호출 세부 정보를 확인하고 메시지 섹션에서 자세한 로깅을 확인합니다.
1. 각 함수에 대해 실행 흐름을 검토하는 과정의 일부로 앞에서 설명한 동일한 단계를 반복합니다.

Azure Functions 모니터링에 대한 자세한 내용은 [Application Insights에서 Azure Functions 원격 분석 데이터 분석](../../azure-functions/analyze-telemetry-data.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Functions 및 논리 앱 모니터링에 대한 자세한 내용을 알아보세요.

* [Azure Functions 모니터링](../../azure-functions/functions-monitoring.md)

* [Azure Functions에 대한 모니터링을 구성하는 방법](../../azure-functions/configure-monitoring.md)

* [논리 앱 모니터링](../../logic-apps/monitor-logic-apps.md)

* 배포 중에 문제가 발생하거나 VM 시작/중지 v2(미리 보기)를 사용할 때 문제가 발생하거나 관련 질문이 있는 경우 [GitHub](https://github.com/microsoft/startstopv2-deployments/issues)에서 문제를 제출할 수 있습니다. 이 미리 보기 버전에서는 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)에서 Azure 지원 인시던트를 제출할 수 없습니다. 
