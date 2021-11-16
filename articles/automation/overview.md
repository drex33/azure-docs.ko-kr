---
title: Azure Automation 개요
description: 이 문서에서는 Azure Automation이 무엇인지와 이를 사용하여 인프라 및 애플리케이션의 수명 주기를 자동화하는 방법을 설명합니다.
services: automation
keywords: Azure Automation, DSC, PowerShell, State Configuration, 업데이트 관리, 변경 내용 추적, DSC, 인벤토리, Runbook, Python, 그래픽
ms.date: 10/25/2021
ms.topic: overview
ms.openlocfilehash: d6a703a04aeec9b764f74a7574cde4baea875604
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491491"
---
# <a name="what-is-azure-automation"></a>Azure Automation 정의

Automation은 클라우드 운영의 세 가지 광범위한 영역에서 필요합니다.

* 배포 및 관리 - 반복 가능하고 일관된 인프라를 코드로 제공합니다.
* 응답 - 이벤트 기반 자동화를 만들어 문제를 진단하고 해결합니다.
* 오케스트레이션 - 자동화를 조정하고 다른 Azure 또는 타사 서비스 및 제품과 통합합니다.

Azure Automation은 Azure 및 비 Azure 환경에서 일관된 관리를 지원하는 클라우드 기반 자동화, 운영 체제 업데이트 및 구성 서비스를 제공합니다. 프로세스 자동화, 구성 관리, 업데이트 관리, 공유 기능 및 이기종 기능을 포함합니다.

:::image type="content" source="./media/overview/automation-overview.png" alt-text="Automation 기능 다이어그램" border="true":::

위의 요구 사항을 제공할 수 있는 여러 Azure 서비스가 있습니다. 여기서 각 서비스는 기능 집합을 포함하고 클라우드 솔루션을 빌드하기 위한 프로그래밍 가능한 플랫폼 역할을 합니다. 예를 들어 Azure Bicep 및 Resource Manager는 Azure 리소스에 대해 반복 가능하고 일관된 배포 템플릿을 개발하기 위한 언어를 제공합니다. Azure Automation은 해당 템플릿을 처리하여 Azure 리소스를 배포한 다음 배포 후 구성 작업 집합을 처리할 수 있습니다.

Automation을 통해 엔터프라이즈 워크로드 및 리소스의 배포, 운영 및 폐기 중에 완전한 제어를 할 수 있습니다.

## <a name="process-automation"></a>프로세스 Automation

Azure Automation의 프로세스 Automation을 사용하면 빈번하고, 시간이 오래 걸리고, 오류가 발생하기 쉬운 관리 작업을 자동화할 수 있습니다. 이러한 서비스를 통해 비즈니스 가치를 높이는 작업에 집중할 수 있습니다. 또한 오류를 줄이고 효율성을 높여 운영 비용을 낮출 수 있습니다. 프로세스 자동화 운영 환경은 [Azure Automation에서 Runbook 실행](automation-runbook-execution.md)에 자세히 설명되어 있습니다.

프로세스 자동화는 Azure 서비스와 엔드투엔드 프로세스를 배포, 구성 및 관리하는 데 필요한 다른 타사 시스템을 통합하도록 지원합니다. 이 서비스를 사용하면 그래픽, PowerShell 및 Python [Runbook](automation-runbook-types.md)을 작성할 수 있습니다. Windows 또는 Linux 컴퓨터에서 직접 Runbook을 실행하거나 온-프레미스 또는 기타 클라우드 환경의 리소스에 대해 실행하여 이러한 로컬 리소스를 관리하려면 컴퓨터에 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)를 배포할 수 있습니다.

[웹후크](automation-webhooks.md)를 사용하면 Azure Logic Apps, Azure Function, ITSM 제품 또는 서비스, DevOps 및 모니터링 시스템에서 자동화를 트리거하여 요청을 이행하고 지속적인 업데이트 및 운영을 보장할 수 있습니다.

## <a name="configuration-management"></a>구성 관리

Azure Automation의 구성 관리는 다음 두 가지 기능에서 지원됩니다.

* 변경 내용 추적 및 인벤토리
* Azure Automation 상태 구성

### <a name="change-tracking-and-inventory"></a>변경 내용 추적 및 인벤토리

변경 내용 추적 및 인벤토리는 기능을 결합하여 Linux 및 Windows 가상 머신 및 서버 인프라 변경 내용을 추적할 수 있도록 합니다. 이 서비스는 사용자 환경에서 서비스, 디먼, 소프트웨어, 레지스트리 및 파일에 변경한 내용을 추적하는 기능을 지원하기 때문에 원하지 않는 변경 내용을 진단하고 경고를 생성할 수 있습니다. 인벤토리 지원을 사용하면 게스트 내 리소스를 쿼리하여 설치된 애플리케이션 및 기타 구성 항목을 볼 수 있습니다. 이 기능에 대한 자세한 내용은 [변경 내용 추적 및 인벤토리](change-tracking/overview.md)를 참조하세요.

### <a name="azure-automation-state-configuration"></a>Azure Automation 상태 구성

[Azure Automation State Configuration](automation-dsc-overview.md)은 엔터프라이즈 환경에 필요한 서비스를 제공하는 PowerShell DSC(Desired State Configuration)에 대한 클라우드 기반 기능입니다. 이 기능을 사용하면 Azure Automation에서 DSC 리소스를 관리하고 Azure 클라우드의 DSC 끌어오기 서버에서 가상 머신이나 실제 머신에 구성을 적용할 수 있습니다.

## <a name="update-management"></a>업데이트 관리

Azure Automation에는 하이브리드 환경의 Windows 및 Linux 시스템용 [업데이트 관리](./update-management/overview.md) 기능이 포함되어 있습니다. 업데이트 관리는 Azure와 기타 클라우드 및 온-프레미스에서 업데이트 규정 준수에 대한 가시성을 제공합니다. 이 기능을 사용하면 정의한 유지 관리 기간 내에 업데이트 설치를 오케스트레이션하는 예약된 배포를 만들 수 있습니다. 특정 머신에 업데이트를 설치하지 않아야 하는 경우에는 업데이트 관리 기능을 사용하여 배포에서 제외할 수 있습니다.

## <a name="shared-capabilities"></a>공유 기능

Azure Automation은 다양한 공유 기능을 제공하며, 여기에는 공유 리소스, 역할 기반 액세스 제어, 유연한 일정 예약, 소스 제어 통합, 감사 및 태그 지정 기능이 포함됩니다.

### <a name="shared-resources"></a><a name="shared-resources"></a>공유 리소스

Azure Automation은 대규모 환경을 더 쉽게 자동화하고 구성할 수 있게 하는 일단의 공유 리소스로 구성됩니다.

* **[일정](./shared-resources/schedules.md)** - 미리 정의한 시간에 자동화 작업을 트리거합니다.
* **[모듈](./shared-resources/modules.md)** - Azure 및 기타 시스템을 관리합니다. Microsoft, 타사, 커뮤니티, 사용자 정의 cmdlet 및 DSC 리소스에 대한 Automation 계정으로 모듈을 가져올 수 있습니다.
* **[모듈 갤러리](automation-runbook-gallery.md)** - PowerShell 갤러리와 네이티브 통합을 지원하기 때문에 Runbook을 볼 수 있고 Automation 계정으로 가져올 수 있습니다. 갤러리를 사용하면 PowerShell 갤러리 및 Microsoft 스크립트 센터에서 프로세스를 빠르게 통합하고 제작할 수 있습니다.
* **[Python 2 및 3 패키지](python-packages.md)** - Automation 계정에 대해 Python 2 및 3 Runbook을 지원합니다.
* **[자격 증명](./shared-resources/credentials.md)** - Runbook 및 구성이 런타임 시 사용할 수 있는 중요한 정보를 안전하게 저장합니다.
* **[연결](automation-connections.md)** - 시스템 연결을 위한 공통 정보의 이름 값 쌍을 저장합니다. 런타임 시 사용할 Runbook 및 구성의 연결은 모듈 작성자가 정의합니다.
* **[인증서](./shared-resources/certificates.md)** - 런타임 시 Runbook 또는 DSC 구성이 액세스하는 경우 배포된 리소스을 보호하고 인증하는 데 사용되는 정보를 정의합니다.
* **[변수](./shared-resources/variables.md)** - Runbook 및 구성 전체에서 사용할 수 있는 콘텐츠를 보관합니다. Runbook과 이를 참조하는 구성을 수정하지 않고도 변수 값을 변경할 수 있습니다.

### <a name="role-based-access-control"></a>역할 기반 액세스 제어

Azure Automation은 Azure RBAC(Azure 역할 기반 액세스 제어)를 지원하여 Automation 계정 및 해당 리소스에 대한 액세스를 제어합니다. Automation 계정, Runbooks 및 작업에서 Azure RBAC를 구성하는 방법을 자세히 알아보려면 [Azure Automation의 역할 기반 액세스 제어](automation-role-based-access-control.md)를 참조하세요.

### <a name="source-control-integration"></a>소스 제어 통합

Azure Automation은 [소스 제어 통합](source-control-integration.md)을 지원합니다. 이 기능은 구성을 코드로 승격하며, 이렇게 하면 Runbook 또는 구성을 소스 제어 시스템에 체크인할 수 있습니다.

## <a name="heterogeneous-support-windows-and-linux"></a>이기종 지원(Windows 및 Linux)

Automation은 Azure 외부, 회사 네트워크 또는 기타 클라우드 공급자의 Windows 및 Linux 물리적 서버와 가상 머신에서 작동하도록 설계되었습니다. 배포된 워크로드와 워크로드를 실행하는 운영 체제를 자동화하고 구성하는 일관된 방법을 제공합니다. Azure Automation의 Hybrid Runbook Worker 기능을 사용하면 역할을 호스팅하는 비 Azure 물리적 서버 또는 가상 머신에서 Runbook을 직접 실행할 수 있으며 환경의 리소스에 대해 이러한 로컬 리소스를 관리할 수 있습니다.

[Arc 지원 서버](../azure-arc/servers/overview.md)를 통해 비 Azure 컴퓨터에 일관된 배포 및 관리 환경을 제공합니다. VM 확장 프레임워크를 사용하여 Automation 서비스와 통합하여 Hybrid Runbook Worker 역할을 배포하고 업데이트 관리 및 변경 내용 추적 및 인벤토리에 대한 온보딩을 간소화할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

Azure Automation은 인프라와 애플리케이션의 수명 주기 전반에 걸친 관리를 지원합니다. 일반적인 시나리오는 다음과 같습니다.

* **작업 예약** - VM 또는 서비스를 야간에 중지하고 주간, 주간 또는 월간 반복적인 유지 관리 워크플로 중에 활성화합니다.
* **Runbook 작성** - PowerShell, PowerShell Workflow, 그래픽, Python 2 및 3, 그리고 DSC Runbook을 공용 언어로 작성합니다.
* **리소스 빌드 및 배포** - Runbook 및 Azure Resource Manager 템플릿을 사용하여 하이브리드 환경 전반에 가상 머신을 배포합니다. Jenkins 및 Azure DevOps와 같은 개발 도구에 통합합니다.
* **VM 구성** - 인프라 및 애플리케이션에 대해 구성을 사용하여 Windows 및 Linux 머신을 평가하고 구성합니다.
* **지식 공유** - 조직에서 워크로드를 제공하고 유지 관리하는 방법에 대한 지식을 시스템에 전달합니다.
* **인벤토리 검색** - 배포된 리소스의 대상 지정, 보고 및 규정 준수에 대한 전체 인벤토리를 가져옵니다.
* **변경 찾기** - 잘못된 구성을 유발할 수 있는 컴퓨터 변경을 식별 및 격리하고 운영 규정 준수를 개선합니다. 문제를 수정하거나 관리 시스템으로 에스컬레이션합니다.
* **정기 유지 관리** - 오래된 데이터 또는 오래된 데이터 삭제 또는 SQL 데이터베이스의 인덱스 재지정과 같이 정해진 시간 간격으로 수행해야 하는 작업을 실행합니다.
* **경고에 응답** - 비용 기반, 시스템 기반, 서비스 기반 및/또는 리소스 사용률 경고가 생성될 때 응답을 조정합니다.
* **하이브리드 자동화** - SQL Server, Active Directory, SharePoint Server 등과 같은 온-프레미스 서버 및 서비스를 관리하거나 자동화합니다.
* **Azure 리소스 수명 주기 관리** - IaaS 및 PaaS 서비스용
* **개발/테스트 자동화 시나리오** - 리소스 시작 및 시작, 리소스 확장 등
* **거버넌스 관련 자동화** - 태그, 잠금 등을 자동으로 적용하거나 업데이트합니다.
* **Azure Site Recovery** - Site Recovery DR 워크플로에 정의된 사전/사후 스크립트를 조정합니다.
* **Windows Virtual Desktop** - 사용률에 따라 VM 확장을 조정하거나 VM을 시작/중지합니다.

요구 사항에 따라 다음 Azure 서비스 중 하나 이상이 Azure Automation과 통합되거나 이를 보완하는 데 도움이 됩니다.

* [Azure Arc 지원 서버](../azure-arc/servers/overview.md)를 사용하면 업데이트 관리, 변경 내용 추적 및 인벤토리, Hybrid Runbook Worker 역할에 하이브리드 컴퓨터를 간편하게 온보딩할 수 있습니다.
* [Azure Alerts 작업 그룹](../azure-monitor/alerts/action-groups.md)은 경고가 발생하면 Automation Runbook을 시작할 수 있습니다.
* [Azure Monitor](../azure-monitor/overview.md): 추가 분석을 위해 Automation 계정에서 메트릭 및 로그 데이터를 수집하고 원격 측정에 대한 조치를 취합니다. 업데이트 관리, 변경 내용 추적 및 인벤토리와 같은 Automation 기능은 Log Analytics 작업 영역에 의존하여 기능 요소를 제공합니다.
* [Azure Policy](../governance/policy/samples/built-in-policies.md)에는 Automation 계정에 대한 다양한 보안 표준을 준수하도록 설정하고 유지하는 데 도움이 되는 이니셔티브 정의가 포함되어 있습니다.
* [Azure Site Recovery](../site-recovery/site-recovery-runbook-automation.md)는 Azure Automation Runbook을 사용하여 복구 계획을 자동화할 수 있습니다.

이러한 Azure 서비스는 HTTP 웹후크 또는 API 메서드를 사용하여 Automation 작업 및 Runbook 리소스와 함께 작동할 수 있습니다.

* [Azure Logic Apps](/azure/connectors/built-in)
* [Azure Power Apps](/connectors/azureautomation)
* [Azure Event Grid](../event-grid/handler-webhooks.md)
* [Azure Power Automate](/connectors/azureautomation)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-azure-automation"></a>Azure Automation에 대한 가격 책정

Azure Automation과 관련된 가격은 [가격 책정](https://azure.microsoft.com/pricing/details/automation/) 페이지에서 검토할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Automation 계정 만들기](./quickstarts/create-account-portal.md)
