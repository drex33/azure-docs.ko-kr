---
title: Azure Arc 지원 서버를 계획하고 배포하는 방법
description: Azure Arc 지원 서버에서 많은 수의 머신을 사용하도록 설정하여 Azure에서 필수적인 보안, 관리, 모니터링 기능의 구성을 간소화하는 방법을 알아봅니다.
ms.date: 08/27/2021
ms.topic: conceptual
ms.openlocfilehash: 64fb65de34c03158131ee7670898f98b2dcf9f9d
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132870210"
---
# <a name="plan-and-deploy-azure-arc-enabled-servers"></a>Azure Arc 사용 서버 계획 및 배포

IT 인프라 서비스나 비즈니스 애플리케이션을 배포하는 것은 어떤 회사에서든 어려워 하는 작업입니다. 이 작업을 잘 실행하고 예기치 못한 상황과 계획에 없던 비용 지출을 방지하려면 가능한 한 최선의 준비 상태를 유지하도록 철저한 계획을 수립해야 합니다. 규모와 관계없이 Azure Arc 지원 서버에 대한 계획을 세우려면 작업을 성공적으로 완료하기 위해 충족해야 하는 디자인 및 배포 기준을 계획에 포함해야 합니다.

배포를 원활하게 진행하려면 다음 사항을 명확하게 이해한 상태로 계획을 수립해야 합니다.

* 역할 및 책임
* 네트워크 및 시스템 요구 사항을 충족하는지 확인하기 위한 물리적 서버 또는 가상 머신의 인벤토리
* 성공적인 배포 및 지속적인 관리를 가능하게 하기 위해 필요한 기술 집합 및 교육
* 수용 기준 및 성공 추적 방법
* 배포를 자동화하는 데 사용할 도구 또는 방법
* 지연, 중단 등을 방지하기 위한 위험 식별 및 완화 계획
* 배포 시에 중단을 방지하는 방법
* 중요한 문제가 발생하는 경우에 대비하는 에스컬레이션 경로

이 문서의 목적은 환경 내의 여러 프로덕션 물리적 서버 또는 가상 머신에서 Azure Arc 지원 서버를 성공적으로 배포할 준비를 갖추었는지 확인하는 것입니다.

대규모 배포 권장 사항에 대한 자세한 내용은 아래 비디오를 참조할 수도 있습니다.

> [!VIDEO https://www.youtube.com/embed/Cf1jUPOB_vs]

## <a name="prerequisites"></a>필수 구성 요소

* 머신이 Connected Machine 에이전트에 대해 [지원되는 운영 체제](agent-overview.md#supported-operating-systems)를 실행합니다.
* 머신이 직접적이든 프록시 서버를 통해서든 온-프레미스 네트워크 또는 다른 클라우드 환경에서 Azure의 리소스에 대한 연결을 보유하고 있습니다.
* Azure 연결 된 컴퓨터 에이전트를 설치 하 고 구성 하려면 컴퓨터에 대 한 관리자 권한 (즉, 관리자 또는 루트) 권한이 있는 계정입니다.
* 머신을 등록하려면 **Azure Connected Machine Onboarding** 역할의 멤버여야 합니다.
* 머신을 읽고, 수정하고, 삭제하려면 **Azure Connected Machine 리소스 관리자** 역할의 구성원이어야 합니다.

## <a name="pilot"></a>파일럿

모든 프로덕션 머신에 배포하려면, 환경에서 광범위하게 채택하기 전에 본 배포 프로세스를 평가하는 것부터 시작해야 합니다. 파일럿으로 회사에서 비즈니스를 수행하는 데 있어 중요하지 않은 머신의 대표적인 샘플링을 파악합니다. 파일럿을 실행하고 영향을 평가하려면 충분한 시간을 확보하는 것이 좋으며, 당사는 최소한 30일을 권장합니다.

파일럿의 범위 및 세부 정보를 설명하는 공식 계획을 구성합니다. 다음은 시작 과정을 지원하기 위해 계획에 포함해야 하는 항목의 샘플입니다.

* 목적 - 파일럿이 필요하다는 결정을 내리게끔 하는 비즈니스 및 기술 요인을 설명합니다.
* 선택 기준 - 파일럿을 통해 표시되는 솔루션의 측면을 선택하는 데 사용되는 기준을 지정합니다.
* 범위 - 파일럿의 범위를 설명합니다. 여기에는 솔루션 구성 요소, 예상 일정, 파일럿 기간 및 대상으로 하는 머신 수 등이 포함되지만 이에 국한되는 것은 아닙니다.
* 성공 기준 및 메트릭 - 파일럿의 성공 기준과 성공 수준을 결정하는 데 사용되는 특정 측정값을 정의합니다.
* 교육 계획 - 파일럿 진행 중에 Azure와 해당 서비스를 처음 접하는 시스템 엔지니어, 관리자 등의 교육에 대한 계획을 설명합니다.
* 전환 계획 - 파일럿에서 프로덕션으로의 전환을 안내하는 데 사용되는 전략과 기준을 설명합니다.
* 롤백 - 파일럿을 배포 전 상태로 롤백하는 절차를 설명합니다.
* 위험 - 파일럿 수행 및 프로덕션 배포와 관련하여 식별된 모든 위험을 나열합니다.

## <a name="phase-1-build-a-foundation"></a>1단계: 기반 빌드

이 단계에서 시스템 엔지니어 또는 관리자는 Azure Arc 사용 서버 및 기타 Azure 서비스에서 관리 하도록 컴퓨터를 설정 하기 전에 조직의 Azure 구독에서 핵심 기능을 사용 하도록 설정 하 여 기반을 시작 합니다.

|Task |세부 정보 |Duration |
|-----|-------|---------|
| [리소스 그룹 만들기](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Azure Arc 사용 서버만 포함 하 고 이러한 리소스에 대 한 중앙 집중식 관리 및 모니터링을 위한 전용 리소스 그룹입니다. | 1시간 |
| 머신을 구성하는 데 도움이 되는 [태그](../../azure-resource-manager/management/tag-resources.md)를 적용합니다. | Azure Arc 사용 서버를 관리 하는 복잡성을 줄이고 관리 결정을 간소화 하는 데 도움이 되는 IT에 맞춘 [태그 지정 전략](/azure/cloud-adoption-framework/decision-guides/resource-tagging/) 을 평가 하 고 개발 합니다. | 1일 |
| [Azure Monitor 로그](../../azure-monitor/logs/data-platform-logs.md) 디자인 및 배포 | [디자인 및 배포 고려 사항](../../azure-monitor/logs/design-logs-deployment.md)을 평가하여 조직에서 하이브리드 서버 및 머신에서 수집된 로그 데이터를 저장하기 위해 기존의 Log Analytics 작업 영역을 사용하거나 다른 Log Analytics 작업 영역을 구현할지 결정합니다.<sup>1</sup> | 1일 |
| [Azure Policy](../../governance/policy/overview.md) 거버넌스 계획 개발 | Azure Policy를 사용하여 구독 또는 리소스 그룹 범위에서 하이브리드 서버 및 머신의 거버넌스를 구현하는 방법을 결정합니다. | 1일 |
| RBAC([역할 기반 액세스 제어](../../role-based-access-control/overview.md)) 구성 | 액세스 계획을 개발 하 여 Azure Arc 사용 서버를 관리 하 고 다른 Azure 서비스 및 솔루션에서 데이터를 볼 수 있는 권한을 가진 사용자를 제어 합니다. | 1일 |
| Log Analytics 에이전트가 이미 설치된 머신 식별 | [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md)에서 다음 로그 쿼리를 실행하여 기존 Log Analytics 에이전트 배포를 확장 관리형 에이전트로 변환하도록 지원합니다.<br> 하트비트 <br> &#124; where TimeGenerated > ago(30d) <br> &#124; where ResourceType == "machines" and (ComputerEnvironment == "Non-Azure") <br> &#124; summarize by Computer, ResourceProvider, ResourceType, ComputerEnvironment | 1시간 |

<sup>1</sup> Log Analytics 작업 영역 디자인을 평가 하는 과정에서 중요 한 고려 사항은 업데이트 관리 및 변경 내용 추적 및 인벤토리 기능을 지 원하는 Azure Automation와 클라우드 및 microsoft 센티널 용 microsoft Defender와의 통합입니다. 조직에 이미 Automation 계정이 있고 Log Analytics 작업 영역에 연결된 관리 기능을 사용하도록 설정한 경우, 기존 리소스를 사용하는 것과 중복 계정, 작업 영역 등을 생성하는 것을 비교하여 관리 작업을 중앙 집중화하고 간소화하며 비용을 최소화할 수 있는지를 평가할 수 있습니다.

## <a name="phase-2-deploy-azure-arc-enabled-servers"></a>2 단계: Azure Arc 사용 서버 배포

다음으로, Azure 연결 된 컴퓨터 에이전트를 준비 하 고 배포 하 여 1 단계에 추가 된 기반에 추가 합니다.

|Task |세부 정보 |Duration |
|-----|-------|---------|
| 미리 정의된 설치 스크립트 다운로드 | Connected Machine 에이전트의 대규모 배포를 위해 미리 정의된 설치 스크립트를 검토하고 사용자 지정하여 자동화된 배포 요구 사항을 지원합니다.<br><br> 대규모 온보딩 리소스 샘플:<br><br> <ul><li> [대규모 기본 배포 스크립트](onboard-service-principal.md)</ul></li> <ul><li>[VMware vSphere Windows Server VM 대규모 온보딩](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_win/_index.md)</ul></li> <ul><li>[VMware vSphere Linux VM 대규모 온보딩](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_linux/_index.md)</ul></li> <ul><li>[Ansible을 사용하여 AWS EC2 인스턴스 대규모 온보딩](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/aws_scaled_ansible/_index.md)</ul></li> <ul><li>[PowerShell 원격 기능을 사용하여 대규모 배포](./onboard-powershell.md)(Windows에만 해당)</ul></li>| 요구 사항, 조직 프로세스(예: 변경 및 릴리스 관리) 및 사용된 자동화 방법에 따라 1일 이상이 소요됩니다. |
| [서비스 주체 만들기](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Azure PowerShell 또는 포털을 사용하여 비대화형으로 머신을 연결하는 서비스 주체를 만듭니다.| 1시간 |
| 대상 서버 및 머신의 Connected Machine 에이전트 배포 |자동화 도구를 사용하여 서버에 스크립트를 배포하고 Azure에 연결합니다.| 릴리스 계획에 따라, 그리고 단계별 출시를 따르는 경우 1일 이상이 소요됩니다. |

## <a name="phase-3-manage-and-operate"></a>3단계: 관리 및 운영

3단계에서는 관리자 또는 시스템 엔지니어가 수명 주기 동안 Connected Machine 에이전트와 머신을 관리하고 운영하는 수동 작업을 자동화하도록 지원합니다.

|Task |세부 정보 |Duration |
|-----|-------|---------|
|Resource Health 경고 만들기 |서버에서 Azure로의 하트비트 전송을 15분 이상 중지하는 경우에는 오프라인 상태이거나, 네트워크 연결이 차단되었거나, 에이전트가 실행 중이지 않을 수 있습니다. 해당 인시던트에 응답하고 인시던트를 조사하는 방법에 대한 계획을 수립하고 [Resource Health 경고](../..//service-health/resource-health-alert-monitor-guide.md)를 사용하여 시작 시 알림을 받을 수 있습니다.<br><br> 경고를 구성할 때 다음을 지정합니다.<br> **리소스 종류** = **Azure Arc 지원 서버**<br> **현재 리소스 상태** = **사용할 수 없음**<br> **이전 리소스 상태** = **사용 가능함** | 1시간 |
|Azure Advisor 경고 만들기 | 최상의 환경과 가장 최근의 보안 및 버그 수정을 위해 Azure Connected Machine 에이전트를 최신 상태로 유지하는 것이 좋습니다. 만료된 에이전트는 [Azure Advisor 경고](../../advisor/advisor-alerts-portal.md)로 식별됩니다.<br><br> 경고를 구성할 때 다음을 지정합니다.<br> **권장 사항 유형**  =  **최신 버전의 Azure Connected Machine 에이전트로 업그레이드** | 1시간 |
|구독 또는 리소스 그룹 범위에 [Azure 정책 할당](../../governance/policy/assign-policy-portal.md) |구독 또는 리소스 그룹 범위에 **VM용 Azure Monitor 사용 설정** [정책](../../azure-monitor/vm/vminsights-enable-policy.md)(및 요구를 충족하는 다른 항목)을 할당합니다. Azure Policy를 사용하면 환경 전반에 걸쳐 VM 인사이트에 필요한 에이전트를 설치하는 정책 정의를 할당할 수 있습니다.| 상황에 따라 다름 |
|[Azure Arc 지원 서버에 업데이트 관리 사용](../../automation/update-management/enable-from-automation-account.md) |Azure Automation 업데이트 관리 구성하여 Azure Arc 지원 서버에 등록된 Windows 및 Linux 가상 머신에 대한 운영 체제 업데이트를 관리합니다. | 15분 |

## <a name="next-steps"></a>다음 단계

* 문제 해결에 대한 정보는 [Connected Machine 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)에서 찾을 수 있습니다.

* Azure Automation [State Configuration](../../automation/automation-dsc-overview.md) 및 기타 지원되는 [Azure VM 확장](manage-vm-extensions.md)과 같은 다른 Azure 서비스를 사용하여 배포를 간소화하는 방법을 알아봅니다.
