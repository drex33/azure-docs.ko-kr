---
title: Microsoft Defender for Cloud 문제 해결 가이드 | Microsoft Docs
description: 이 가이드는 클라우드용 Microsoft Defender 관련 문제를 해결해야 하는 IT 전문가, 보안 분석가 및 클라우드 관리자를 위한 것입니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/15/2021
ms.author: memildin
ms.openlocfilehash: b2ee3378df9d7ad35dcdcd319b4f3390d260cc8a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457110"
---
# <a name="microsoft-defender-for-cloud-troubleshooting-guide"></a>클라우드용 Microsoft Defender 문제 해결 가이드

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 가이드는 조직에서 Microsoft Defender for Cloud를 사용하고 있으며 Defender for Cloud 관련 문제를 해결해야 하는 IT(정보 기술) 전문가, 정보 보안 분석가 및 클라우드 관리자를 위한 것입니다.

Defender for Cloud는 Log Analytics 에이전트를 사용하여 데이터를 수집하고 저장합니다. 자세한 내용은 [Microsoft Defender for Cloud Platform Migration을](./enable-data-collection.md) 참조하세요. 이 문서의 정보는 Log Analytics 에이전트로 전환한 후의 Defender for Cloud 기능을 나타냅니다.

> [!TIP]
> Azure Portal Defender for Cloud 페이지의 전용 영역은 Defender for Cloud 및 Azure Defender 일반적인 문제를 해결하기 위해 계속 증가하는 데이터 정렬된 자가 지원 자료 집합을 제공합니다.
> 
> 문제가 발생하거나 지원 팀의 조언을 구하는 경우 **문제 진단 및 해결** 을 통해 솔루션을 찾는 것이 좋습니다.
> 
> :::image type="content" source="media/release-notes/solve-problems.png" alt-text="Defender for Cloud의 '문제 진단 및 해결' 페이지":::


## <a name="troubleshooting-guide"></a>문제 해결 가이드

이 가이드에서는 Defender for Cloud 관련 문제를 해결하는 방법을 설명합니다.

경고 유형:

* VMBA(Virtual Machine 동작 분석)
* 네트워크 분석
* SQL Database 및 Azure Synapse Analytics Analysis
* 컨텍스트 정보

경고 유형에 따라, 고객은 다음 리소스를 사용하여 경고를 조사하는 데 필요한 정보를 수집할 수 있습니다.

* Windows VM(가상 머신) 이벤트 뷰어의 보안 로그
* Linux의 AuditD
* Azure 활동 로그 및 공격 리소스에 대한 진단 로그를 사용하도록 설정합니다.

고객은 경고 설명 및 관련성에 대한 피드백을 공유할 수 있습니다. 경고 자체로 이동하여 **유용했나요?** 단추를 선택하고, 이유를 선택하고, 피드백을 설명하는 의견을 입력합니다. 이 피드백 채널을 지속적으로 모니터링하여 경고를 개선하고 있습니다.

## <a name="audit-log"></a>감사 로그

Defender for Cloud에서 수행되는 대부분의 문제 해결은 먼저 실패한 구성 요소에 대한 [감사 로그](../azure-monitor/essentials/platform-logs-overview.md) 레코드를 확인하여 수행됩니다. 감사 로그를 통해 다음을 확인할 수 있습니다.

* 수행된 작업
* 작업을 시작한 사람
* 작업이 발생한 시간
* 작업의 상태
* 작업을 조사하는 데 도움이 될 수 있는 기타 속성 값

감사 로그에는 리소스에서 수행된 모든 쓰기 작업(PUT, POST, DELETE)이 포함되지만 읽기 작업(GET)은 포함되지 않습니다.

## <a name="log-analytics-agent"></a>Log Analytics 에이전트

Defender for Cloud는 Log Analytics 에이전트(Azure Monitor 서비스에서 사용하는 것과 동일한 에이전트)를 사용하여 Azure 가상 머신에서 보안 데이터를 수집합니다. 데이터 수집을 활성화하고 에이전트가 대상 컴퓨터에 제대로 설치된 후 아래 프로세스가 실행되어야 합니다.

* HealthService.exe

서비스 관리 콘솔(services.msc)을 열 경우 다음과 같이 실행 중인 Log Analytics 에이전트 서비스가 나타납니다.

![권한 부여.](./media/troubleshooting-guide/troubleshooting-guide-fig5.png)

설치된 에이전트의 버전을 확인하려면 **작업 관리자** 를 열고, **프로세스** 탭에서 **Log Analytics 에이전트 서비스** 를 찾고, 마우스 오른쪽 단추로 클릭하고, **속성** 을 클릭합니다. **세부 정보** 탭에서 아래와 같이 파일 버전을 확인합니다.

![파일.](./media/troubleshooting-guide/troubleshooting-guide-fig6.png)

## <a name="log-analytics-agent-installation-scenarios"></a>Log Analytics 에이전트 설치 시나리오

컴퓨터에 Log Analytics 에이전트를 설치하는 경우 다른 결과를 생성할 수 있는 두 가지 설치 시나리오가 있습니다. 지원되는 시나리오는 다음과 같습니다.

* **Defender for Cloud에 의해 자동으로 설치된 에이전트:** 이 시나리오에서는 Defender for Cloud 및 로그 검색의 두 위치에서 경고를 볼 수 있습니다. 리소스가 속한 구독의 보안 정책에 구성된 이메일 주소로 이메일 알림을 받게 됩니다.

* **Azure에 있는 VM에 수동으로 설치된 에이전트:** 이 시나리오에서는 2017년 2월 이전에 수동으로 다운로드하여 설치한 에이전트를 사용하는 경우 작업 영역이 속한 구독을 필터링하는 경우에만 Defender for Cloud 포털에서 경고를 볼 수 있습니다. 리소스가 속한 구독에서 필터링하는 경우 경고가 표시되지 않습니다. 작업 영역이 속한 구독의 보안 정책에 구성된 이메일 주소로 이메일 알림을 받게 됩니다.

> [!NOTE]
> 두 번째 시나리오에서 설명한 동작을 방지하려면 최신 버전의 에이전트를 다운로드해야 합니다.

## <a name="monitoring-agent-health-issues"></a>에이전트 상태 문제 모니터링 <a name="mon-agent"></a>

**모니터링 상태는** Defender for Cloud가 자동 프로비저닝을 위해 초기화된 VM 및 컴퓨터를 성공적으로 모니터링할 수 없는 이유를 정의합니다. 다음 표에서는 **모니터링 상태** 값, 설명 및 해결 단계를 보여 줍니다.

| 모니터링 상태 | Description | 해결 단계: |
|---|---|---|
| 보류 중인 에이전트 설치 | Log Analytics 에이전트 설치가 여전히 실행 중입니다.  설치하는 데 몇 시간이 걸릴 수 있습니다. | 자동 설치가 완료될 때까지 기다립니다. |
| 전원 상태 끄기 | VM이 중지됩니다.  Log Analytics 에이전트는 실행 중인 VM에만 설치할 수 있습니다. | VM을 다시 시작합니다. |
| 누락되었거나 잘못된 Azure VM 에이전트 | Log Analytics 에이전트가 아직 설치되지 않았습니다.  Defender for Cloud가 확장을 설치하려면 유효한 Azure VM 에이전트가 필요합니다. | Azure VM 에이전트를 VM에 설치, 다시 설치 또는 업그레이드합니다. |
| 설치할 준비가 되지 않은 VM 상태  | VM을 설치할 준비가 되지 않았기 때문에 Log Analytics 에이전트가 아직 설치되지 않았습니다. VM 에이전트 또는 VM 프로비전 문제로 인해 VM을 설치할 준비가 되지 않았습니다. | VM의 상태를 확인합니다. 포털의 **Virtual Machines** 로 돌아가서 상태 정보에 대한 VM을 선택합니다. |
|설치 실패 - 일반 오류 | Log Analytics 에이전트가 설치되었지만 오류로 인해 실패했습니다. | 수동으로 [확장을 설치하거나 확장을](../azure-monitor/vm/monitor-virtual-machine.md#agents) 제거하면 Defender for Cloud가 다시 설치를 시도합니다. |
| 설치 실패 - 로컬 에이전트가 이미 설치되어 있음 | Log Analytics 에이전트를 설치하지 못했습니다. Defender for Cloud가 VM에 이미 설치된 로컬 에이전트(Log Analytics 또는 System Center Operations Manager)를 식별했습니다. VM이 두 개의 개별 작업 영역에 보고하는 멀티 호밍 구성을 방지하기 위해 Log Analytics 에이전트 설치가 중지되었습니다. | 두 가지 해결 방법이 있습니다. [수동으로 확장을 설치](../azure-monitor/vm/monitor-virtual-machine.md#agents)하고 원하는 작업 영역에 연결합니다. 또는 원하는 작업 영역을 기본 작업 영역으로 설정하고 에이전트의 자동 프로비전을 사용하도록 설정합니다.  [자동 프로비전 사용](enable-data-collection.md)을 참조하세요. |
| 에이전트에서 작업 영역에 연결할 수 없음 | Log Analytics 에이전트가 설치되었지만 네트워크 연결로 인해 실패했습니다.  인터넷 액세스가 있는지 또는 에이전트에 대해 유효한 HTTP 프록시가 구성되어 있는지 확인합니다. | [모니터링 에이전트 네트워크 요구 사항]을 참조하세요. |
| 누락되었거나 알 수 없는 작업 영역에 연결된 에이전트 | Defender for Cloud는 VM에 설치된 Log Analytics 에이전트가 액세스 권한이 없는 작업 영역에 연결되어 있음을 확인했습니다. | 이는 두 가지 경우에 발생할 수 있습니다. 하나는 작업 영역이 삭제되어 더 이상 존재하지 않습니다. 올바른 작업 영역으로 에이전트를 다시 설치하거나 에이전트를 제거하고 Defender for Cloud가 자동 프로비저닝 설치를 완료하도록 허용합니다. 두 번째 경우는 작업 영역이 Defender for Cloud에 권한이 없는 구독의 일부인 경우입니다. Defender for Cloud는 Microsoft 보안 리소스 공급자가 액세스할 수 있도록 구독을 요구합니다. 이 경우 Microsoft 보안 리소스 공급자에 대한 구독을 등록하여 해당 구독을 사용합니다. 이 작업은 API, PowerShell, 포털에서 수행하거나 Defender for Cloud **개요** 대시보드에서 구독을 필터링하여 수행할 수 있습니다. 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)을 참조하세요. |
| 에이전트가 응답하지 않거나 ID가 누락되었음 | Defender for Cloud는 에이전트가 설치되어 있더라도 VM에서 검색된 보안 데이터를 검색할 수 없습니다. | 에이전트에서 하트비트를 포함한 모든 데이터를 보고하지 않습니다. 에이전트가 손상되었거나 트래픽을 차단하는 것이 있습니다. 또는 에이전트가 데이터를 보고하지만 Azure 리소스 ID가 누락되어 있으므로 Azure VM에 데이터를 일치시킬 수 없습니다. Linux 문제를 해결하려면 [Linux용 Log Analytics 에이전트에 대한 문제 해결 가이드](https://github.com/microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal)를 참조하세요. Windows 문제를 해결하려면 [Windows 가상 머신 문제 해결](../virtual-machines/extensions/oms-windows.md#troubleshoot-and-support)을 참조하세요. |
| 에이전트가 설치되지 않음 | 데이터 수집을 사용하지 않도록 설정되었습니다. | 보안 정책에서 데이터 수집을 설정하거나 Log Analytics 에이전트를 수동으로 설치합니다. |

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>모니터링 에이전트 네트워크 요구 사항 문제 해결 <a name="mon-network-req"></a>

에이전트를 클라우드에 연결 하 고 등록 하려면 포트 번호 및 도메인 Url을 비롯 한 네트워크 리소스에 대 한 액세스 권한이 있어야 합니다.

* 프록시 서버의 경우 적절한 프록시 서버 리소스가 에이전트 설정에 구성되어 있는지 확인해야 합니다. [프록시 설정을 변경하는 방법](../azure-monitor/agents/agent-windows.md)에 대한 자세한 내용은 이 문서를 참조하세요.
* 인터넷에 대한 액세스를 제한하는 방화벽을 사용하여 경우 Log Analytics에 대한 액세스를 허용하도록 방화벽을 구성해야 합니다. 에이전트 설정에서 아무 작업도 필요하지 않습니다.

다음 표에서는 통신에 필요한 리소스를 보여줍니다.

| 에이전트 리소스 | 포트 | HTTPS 검사 무시 |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | 예 |
| *.oms.opinsights.azure.com | 443 | 예 |
| \*.blob.core.windows.net | 443 | 예 |
| \* .azure-automation.net | 443 | 예 |

에이전트와 온보딩 문제가 발생하는 경우 [Operations Management Suite 온보딩 문제를 해결하는 방법](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) 문서를 참조하도록 합니다.

## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>제대로 작동하지 않는 엔드포인트 보호 문제 해결

게스트 에이전트는 [Microsoft 맬웨어 방지 프로그램](../security/fundamentals/antimalware.md) 확장에서 수행하는 모든 작업의 부모 프로세스입니다. 게스트 에이전트 프로세스가 실패하면 게스트 에이전트의 자식 프로세스로 실행되는 Microsoft 맬웨어 방지 프로그램도 실패할 수 있습니다.  이와 같은 경우 다음 옵션을 확인하는 것이 좋습니다.

* 대상 VM이 사용자 지정 이미지이고 VM 작성자가 게스트 에이전트를 설치하지 않은 경우.
* 대상이 Windows VM이 아니라 Linux VM이면 Linux VM에 Windows 버전의 맬웨어 방지 확장을 설치하는 작업이 실패합니다. Linux 게스트 에이전트는 OS 버전 및 필수 패키지에 대한 요구 사항이 있으며, 이러한 요구 사항이 충족되지 않으면 VM 에이전트가 작동하지 않습니다.
* VM이 이전 버전의 게스트 에이전트를 사용하여 만들어진 경우. 이 경우 일부 오래된 에이전트는 자동으로 최신 버전으로 업데이트할 수 없으며 이로 인해 문제가 발생할 수 있습니다. 사용자 고유의 이미지를 만들 때에는 항상 최신 버전의 게스트 에이전트를 사용해야 합니다.
* 일부 타사 관리 소프트웨어는 게스트 에이전트를 사용하지 않도록 설정하거나 특정 파일 위치에 대한 액세스를 차단할 수 있습니다. VM에 타사 에이전트가 설치되어 있으면 해당 에이전트가 제외 목록에 있는지 확인합니다.
* 특정 방화벽 설정 또는 NSG(네트워크 보안 그룹)가 게스트 에이전트와 주고 받는 네트워크 트래픽을 차단할 수 있습니다.
* 특정 ACL(Access Control 목록)에서 디스크 액세스를 차단할 수 있습니다.
* 디스크 공간이 부족하여 게스트 에이전트가 제대로 작동하지 못할 수 있습니다.

Microsoft 맬웨어 방지 프로그램 사용자 인터페이스는 기본적으로 사용하지 않도록 설정되며, 사용하도록 설정하는 방법에 대한 자세한 내용은 [배포 후 Azure Resource Manager에서 Microsoft 맬웨어 방지 프로그램 사용자 인터페이스를 사용하도록 설정](/archive/blogs/azuresecurity/enabling-microsoft-antimalware-user-interface-post-deployment)을 참조하세요.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>대시보드 로딩 문제 해결

클라우드 대시보드 용 Defender를 로드 하는 데 문제가 발생 하는 경우 구독을 클라우드에 등록 하는 사용자 (즉, 구독을 사용 하 여 클라우드 용 Defender를 연 첫 번째 사용자)와 데이터 수집을 켜는 사용자가 구독의 *소유자* 또는 *참가자* 여야 합니다. 또한 사용자는 이때부터 구독에서 *리더* 를 사용하여 dashboard/alerts/recommendation/policy를 볼 수 있습니다.

## <a name="contacting-microsoft-support"></a>Microsoft 지원에 문의

일부 문제는이 문서에서 제공 하는 지침을 사용 하 여 확인할 수 있습니다. 다른 사용자는 Defender for Cloud public [Microsoft Q&페이지를](/answers/topics/azure-security-center.html)참조 하세요. 그러나 추가로 문제 해결이 필요한 경우 아래와 같이 **Azure Portal** 을 사용하여 새로운 지원 요청을 열 수 있습니다.

![Microsoft 지원](./media/troubleshooting-guide/troubleshooting-guide-fig2.png)

## <a name="see-also"></a>참고 항목

이 페이지에서는 Microsoft Defender for Cloud에서 보안 정책을 구성 하는 방법에 대해 알아보았습니다. 클라우드 용 Microsoft Defender에 대 한 자세한 내용은 다음을 참조 하세요.

* [Microsoft Defender For Cloud의 보안 경고 관리 및 대응](managing-and-responding-alerts.md) -보안 경고를 관리 하 고 대응 하는 방법을 알아봅니다.
* [Microsoft Defender for Cloud에서 경고 유효성 검사](alert-validation.md)
* [Microsoft Defender For CLOUD FAQ](faq-general.yml) -서비스 사용에 대 한 질문과 대답을 찾습니다.
