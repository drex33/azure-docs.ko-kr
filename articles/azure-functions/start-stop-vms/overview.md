---
title: VM v2 시작/중지(미리 보기) 개요
description: 이 문서에서는 일정에 따라 Azure Resource Manager 및 클래식 VM을 시작하거나 중지하는 두 가지 버전의 VM 시작/중지(미리 보기) 기능을 설명합니다.
ms.topic: conceptual
ms.service: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.openlocfilehash: e71f6b6dde1ae12a68f425dcb372cca73456de73
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858135"
---
# <a name="startstop-vms-v2-preview-overview"></a>VM v2 시작/중지(미리 보기) 개요

VM v2 시작/중지(미리 보기) 기능은 여러 구독에서 Azure VM(가상 머신)을 시작하거나 중지합니다. 사용자 정의 일정에 따라 Azure VM을 시작 또는 중지하고, [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)를 통해 인사이트를 제공하고, [작업 그룹](../../azure-monitor/alerts/action-groups.md)을 사용하여 선택적 알림을 전송합니다. 이 기능은 대부분의 시나리오에서 Azure Resource Manager VM 및 클래식 VM을 관리할 수 있습니다.

이 새 버전의 VM v2 시작/중지(미리 보기)는 VM 비용을 최적화하려는 고객에게 분산된 저렴한 자동화 옵션을 제공합니다. Azure Automation에서 사용할 수 있는 [원래 버전](../../automation/automation-solution-vm-management.md)과 동일한 기능을 모두 제공하지만 Azure에서 최신 기술을 활용하도록 설계되었습니다.

## <a name="overview"></a>개요

VM v2 시작/중지(미리 보기)가 다시 디자인되고 [이전 버전](../../automation/automation-solution-vm-management.md)에서 요구하는 Azure Automation 또는 Azure Monitor 로그에 종속되지 않습니다. 이 버전은 VM 시작 및 중지 실행을 처리하는 [Azure Functions](../../azure-functions/functions-overview.md)에 의존합니다.

관리 ID는 이 Azure Functions 애플리케이션에 대한 Azure AD(Azure Active Directory)에서 만들어지며, VM v2 시작/중지(미리 보기)를 허용하여 논리 앱, Azure VM 등의 다른 Azure AD 보호 리소스에 쉽게 액세스할 수 있습니다. Azure AD의 관리 ID에 관한 자세한 내용은 [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

다음 표에서와 같이 기능에 포함된 일정 및 시퀀스 시나리오를 지원하기 위해 HTTP 트리거 엔드포인트 함수가 생성됩니다.

|속성 |트리거 |Description |
|-----|--------|------------|
|예약됨 |HTTP |이 함수는 예약된 시나리오와 시퀀싱된 시나리오 모두에 사용할 수 있습니다(페이로드 스키마로 구분). 논리 앱에서 호출되는 진입점 함수이며 페이로드를 사용하여 VM 시작 또는 중지 작업을 처리합니다. |
|AutoStop |HTTP |이 함수는 논리 앱에서 호출되는 진입점 함수인 **AutoStop** 시나리오를 지원합니다.|
|AutoStopVM |HTTP |이 함수는 경고 조건이 true인 경우 VM 경고에 의해 자동으로 트리거됩니다.|
|VirtualMachineRequestOrchestrator |큐 |이 함수는 **Scheduled** 함수에서 페이로드 정보를 가져오고 VM 시작 및 중지 요청을 오케스트레이션합니다.|
|VirtualMachineRequestExecutor |큐 |이 함수는 VM에서 실제 시작 및 중지 작업을 수행합니다.|
|CreateAutoStopAlertExecutor |큐 |이 함수는 **AutoStop** 함수에서 페이로드 정보를 가져와 VM에 대한 경고를 만듭니다.|
|HeartBeatAvailabilityTest |타이머 |이 함수는 기본 HTTP 함수의 가용성을 모니터링합니다.|
|CostAnalyticsFunction |타이머 |이 함수는 V2 시작/중지 솔루션을 월 단위로 실행하는 데 드는 비용을 계산합니다.|
|SavingsAnalyticsFunction |타이머 |이 함수는 시작/중지 V2 솔루션이 달성된 총 절감액을 월 단위로 계산합니다.|
|VirtualMachineSavingsFunction |큐 |이 함수는 시작/중지 V2 솔루션이 달성된 VM에 대한 실제 절감 계산을 수행합니다.|

예를 들어 **Scheduled** HTTP 트리거 함수는 일정 및 시퀀스 시나리오를 처리하는 데 사용됩니다. 마찬가지로 **AutoStop** HTTP 트리거 함수는 자동 중지 시나리오를 처리합니다.

큐 기반 트리거 함수는 이 기능을 지원하는 데 필요합니다. 모든 타이머 기반 트리거는 가용성 테스트를 수행하고 시스템 상태를 모니터링하는 데 사용됩니다.

 [Azure Logic Apps](../../logic-apps/logic-apps-overview.md)는 JSON 페이로드를 사용하여 함수를 호출하여 VM 작업 수행에 대한 시작 및 중지 일정을 구성하고 관리하는 데 사용됩니다. 기본적으로 초기 배포 중에는 다음 시나리오에 대한 총 5개의 Logic Apps가 생성됩니다.

- **예약 -** 시작 및 중지 작업은 Azure Resource Manager 및 클래식 VM에 대해 지정한 일정에 따라 수행됩니다. **ststv2_vms_Scheduled_start** 및 **ststv2_vms_Scheduled_stop** 은 예약된 시작 및 중지를 구성합니다.

- **시퀀스됨** - 시작 및 중지 작업은 미리 정의된 시퀀싱 태그가 있는 VM을 대상으로 하는 일정을 기반으로 합니다. 두 개의 명명된 태그(**sequencestart** 및 **sequencestop**)만 지원됩니다. **ststv2_vms_Sequenced_start** 및 **ststv2_vms_Sequenced_stop** 은 시퀀싱된 시작 및 중지를 구성합니다. 

    시퀀스 기능을 사용하는 적절한 방법은 시퀀스에서 시작하려는 각 VM에 **sequencestart라는** 태그를 만드는 것입니다. 태그 값은 해당 범위의 각 VM에 대해 1에서 N까지의 정수여야 합니다. 태그는 선택 사항이며, 없는 경우 VM은 단순히 시퀀싱에 참여하지 않습니다. 태그 이름만 다른 VM을 중지하고 이 경우 **sequencestop을** 사용하는 경우에도 동일한 기준이 적용됩니다. 시작 및 중지 작업을 수행하려면 각 VM의 태그를 모두 구성해야 합니다.

    예를 들어 다음 표에서는 시퀀스가 반대인 두 VM이 동일한 순서로 실행되는 방법을 보여 있습니다.

    :::image type="content" source="media/overview/sequence-settings-table.png" alt-text="시퀀스 설정 태그 예제를 보여 주는 표":::

    > [!NOTE]
    > 이 시나리오는 Azure Resource Manager VM만 지원합니다.

- **AutoStop** - 이 기능은 CPU 사용률에 따라 Azure Resource Manager 및 클래식 VM 모두에 대해 중지 작업을 수행하는 데만 사용됩니다. 또한 VM에 대한 경고를 생성하고 조건에 따라 중지 동작을 수행하기 위해 경고가 트리거되는 예약 기반 *작업 수행* 일 수도 있습니다. **ststv2_vms_AutoStop** 은 자동 중지 기능을 구성합니다.

각 시작/중지 작업은 하나 이상의 구독, 리소스 그룹 또는 VM 목록 할당을 지원합니다.

Functions에 필요한 Azure Storage 계정은 다음과 같은 두 가지 용도로 VM v2 시작/중지(미리 보기)에서도 사용됩니다.

   - Azure Table Storage를 사용하여 실행 작업 메타데이터(즉, VM 시작/중지 작업)를 저장합니다.

   - Azure Queue Storage를 사용하여 Azure Functions 큐 기반 트리거를 지원합니다.

함수 앱 실행의 추적 로그인 모든 원격 분석 데이터가 연결된 Application Insights 인스턴스로 전송됩니다. 공유 [Azure 대시보드](../../azure-portal/azure-portal-dashboards.md)에 제공되는 미리 정의된 시각화 집합에서 Application Insights에 저장된 원격 분석 데이터를 볼 수 있습니다.

:::image type="content" source="media/overview/shared-dashboard-sml.png" alt-text="VM 시작/중지 공유 상태 대시보드":::

또한 VM에서 수행된 작업의 결과로 이메일 알림이 전송됩니다.

## <a name="new-releases"></a>새 릴리스

새 버전의 VM v2 시작/중지(미리 보기)가 릴리스되면 인스턴스는 수동으로 다시 배포하지 않고도 자동으로 업데이트됩니다.

## <a name="supported-scoping-options"></a>지원되는 범위 지정 옵션

### <a name="subscription"></a>Subscription

구독에 대한 범위 지정은 전체 구독의 모든 VM에서 시작 및 중지 작업을 수행해야 하는 경우 사용할 수 있으며, 필요한 경우 여러 구독을 선택할 수 있습니다.  

또한 제외할 VM 목록을 지정할 수 있으며 작업에서 이를 무시합니다. 와일드카드 문자를 사용하여 동시에 무시할 수 있는 모든 이름을 지정할 수도 있습니다.

### <a name="resource-group"></a>Resource group

리소스 그룹에 대한 범위 지정은 하나 이상의 리소스 그룹 이름을 지정하고 하나 이상의 구독에서 모든 VM에 대해 시작 및 중지 작업을 수행해야 하는 경우에 사용할 수 있습니다.

또한 제외할 VM 목록을 지정할 수 있으며 작업에서 이를 무시합니다. 와일드카드 문자를 사용하여 동시에 무시할 수 있는 모든 이름을 지정할 수도 있습니다.

### <a name="vmlist"></a>VMList

VM 목록 지정은 특정 가상 머신 집합 및 여러 구독에서 시작 및 중지 작업을 수행해야 하는 경우에 사용할 수 있습니다. 이 옵션은 제외할 VM 목록을 지정하는 것을 지원하지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 포함된 Azure 계정이 있어야 합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/).

- 계정에 구독에서 [기여자](../../role-based-access-control/built-in-roles.md#contributor) 권한이 부여되었습니다.

- VM v2 시작/중지(미리 보기)는 Azure Functions에 대한 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=functions) 페이지에 나열된 모든 Azure 글로벌 및 US Government 클라우드 지역에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 기능을 배포하려면 [VM 시작/중지 배포](deploy.md)(미리 보기)를 참조하세요.
