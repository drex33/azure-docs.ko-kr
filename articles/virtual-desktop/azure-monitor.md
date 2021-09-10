---
title: Monitor for Azure Virtual Desktop Monitor 사용 - Azure
description: Azure Monitor for Azure Virtual Desktop 사용 방법
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: b985ec9e88f4285da326fcf41e19141e61a0dd10
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113564197"
---
# <a name="use-azure-monitor-for-azure-virtual-desktop-to-monitor-your-deployment"></a>Azure Monitor for Azure Virtual Desktop을 사용하여 배포 모니터링

Azure Monitor for Azure Virtual Desktop은 IT 전문가가 Azure Virtual Desktop 환경을 이해하는 데 도움이 되는 Azure Monitor Workbooks 기반의 대시보드입니다. 이 토픽에서는 Azure Virtual Desktop 환경을 모니터링하기 위해 Azure Monitor for Azure Virtual Desktop을 설정하는 방법을 안내합니다.

## <a name="requirements"></a>요구 사항

Azure Monitor for Azure Virtual Desktop 사용을 시작하기 전에 다음 사항을 설정해야 합니다.

- 모니터링하는 모든 Azure Virtual Desktop 환경은 Azure Resource Manager와 호환되는 최신 Azure Virtual Desktop 릴리스를 기반으로 해야 합니다.
- 하나 이상의 구성된 Log Analytics 작업 영역. Azure Virtual Desktop 세션 호스트에 대해 지정된 Log Analytics 작업 영역을 사용하여 Azure Virtual Desktop 배포의 세션 호스트에서만 성능 카운터와 이벤트가 수집되도록 합니다.
- Log Analytics 작업 영역에서 다음 항목에 대한 데이터 수집을 사용하도록 설정합니다.
    - Azure Virtual Desktop 환경의 진단
    - Azure Virtual Desktop 세션 호스트의 권장 성능 카운터
    - Azure Virtual Desktop 세션 호스트의 권장 Windows 이벤트 로그

 이 문서에서 설명하는 데이터 설정 프로세스는 Azure Virtual Desktop을 모니터링하는 데 필요한 유일한 프로세스입니다. Log Analytics 작업 영역으로 데이터를 보내는 다른 모든 항목을 사용하지 않도록 설정하여 비용을 절감할 수 있습니다.

사용자 환경에 대한 Azure Monitor for Azure Virtual Desktop을 모니터링하려면 다음 읽기 액세스 권한도 필요합니다.

- Azure Virtual Desktop 리소스를 보유하는 Azure 구독에 대한 읽기 액세스 권한
- Azure Virtual Desktop 세션 호스트를 보유하는 구독의 리소스 그룹에 대한 읽기 액세스 권한
- Log Analytics 작업 영역에 대한 읽기 액세스 권한

>[!NOTE]
> 읽기 액세스 권한만 있으면 관리자가 데이터를 볼 수 있습니다. Azure Virtual Desktop 포털에서 리소스를 관리하려면 다른 액세스 권한이 필요합니다.

## <a name="open-azure-monitor-for-azure-virtual-desktop"></a>Azure Monitor for Azure Virtual Desktop 열기

다음 방법 중 하나로 Azure Monitor for Azure Virtual Desktop을 열 수 있습니다.

- [aka.ms/azmonwvdi](https://aka.ms/azmonwvdi)로 이동합니다.
- Azure Portal에서 **Azure Virtual Desktop** 을 검색하고 선택한 다음, **인사이트** 를 선택합니다.
- Azure Portal에서 **Azure Monitor** 를 검색하고 선택합니다. **인사이트** 에서 **인사이트 허브** 를 선택하고 **Azure Virtual Desktop** 을 선택합니다.
페이지가 열리면 모니터링하려는 환경의 **구독**, **리소스 그룹**, **호스트 풀** 및 **시간 범위** 를 입력합니다.

>[!NOTE]
>Azure Virtual Desktop은 현재 한 번에 하나의 구독, 리소스 그룹 및 호스트 풀만 모니터링하도록 지원합니다. 모니터링하려는 환경을 찾을 수 없으면 알려진 기능 요청 및 문제에 대한 [문제 해결 설명서](troubleshoot-azure-monitor.md)를 참조하세요.

## <a name="log-analytics-settings"></a>Log Analytics 설정

Azure Monitor for Azure Virtual Desktop 사용을 시작하려면 하나 이상의 Log Analytics 작업 영역이 필요합니다. Azure Virtual Desktop 세션 호스트에 대해 지정된 Log Analytics 작업 영역을 사용하여 Azure Virtual Desktop 배포의 세션 호스트에서만 성능 카운터와 이벤트가 수집되도록 합니다. 작업 영역을 이미 설정한 경우 [구성 통합 문서를 사용하여 설정](#set-up-using-the-configuration-workbook)으로 건너뜁니다. 작업 영역을 설정하려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../azure-monitor/logs/quick-create-workspace.md)를 참조하세요.

>[!NOTE]
>Log Analytics에 대한 표준 스토리지 요금이 적용됩니다. 시작하려면 종량제 모델을 선택한 후 배포를 확장하고 더 많은 데이터를 가져올 때 조정하는 것이 좋습니다. 자세히 알아보려면 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조하세요.

## <a name="set-up-using-the-configuration-workbook"></a>구성 통합 문서를 사용하여 설정

Azure Monitor for Azure Virtual Desktop을 처음 여는 경우 Azure Virtual Desktop 환경에 대해 Azure Monitor를 설정해야 합니다. 리소스를 구성하려면 다음을 수행합니다.

1. Azure Portal([aka.ms/azmonwvdi](https://aka.ms/azmonwvdi))에서 Azure Monitor for Azure Virtual Desktop을 열고 **구성 통합 문서** 를 선택합니다.
2. **구독**, **리소스 그룹** 및 **호스트 풀** 에서 구성할 환경을 선택합니다.

구성 통합 문서는 모니터링 환경을 설정하고 설정 프로세스를 완료한 후 구성을 확인할 수 있도록 합니다. 대시보드의 항목이 제대로 표시되지 않거나 제품 그룹에서 새 설정이 필요한 업데이트를 게시하는 경우 구성을 확인하는 것이 중요합니다.

### <a name="resource-diagnostic-settings"></a>리소스 진단 설정

Azure Virtual Desktop 인프라에서 정보를 수집하려면 Azure Virtual Desktop 호스트 풀 및 작업 영역(Log Analytics 작업 영역이 아닌 Azure Virtual Desktop 작업 영역)에서 몇 가지 진단 설정을 사용해야 합니다. 호스트 풀, 작업 영역 및 기타 Azure Virtual Desktop 리소스 개체에 대해 자세히 알아보려면 [환경 가이드](environment-setup.md)를 참조하세요.

[Log Analytics에 Azure Virtual Desktop 진단 보내기](diagnostics-log-analytics.md)에서 Azure Virtual Desktop 진단과 지원되는 진단 테이블에 대해 자세히 알아볼 수 있습니다.

구성 통합 문서에서 리소스 진단 설정을 지정하려면 다음을 수행합니다. 

1. 구성 통합 문서에서 **리소스 진단 설정** 탭을 선택합니다. 
2. **Log Analytics 작업 영역** 을 선택하여 Azure Virtual Desktop 진단을 보냅니다. 

#### <a name="host-pool-diagnostic-settings"></a>호스트 풀 진단 설정

구성 통합 문서의 리소스 진단 설정 섹션을 사용하여 호스트 풀 진단을 설정하려면 다음을 수행합니다.

1. **호스트 풀** 에서 Azure Virtual Desktop 진단이 사용되는지 확인합니다. 사용되지 않는 경우 "선택한 호스트 풀에 대한 기존 진단 구성이 없습니다."라는 오류 메시지가 표시됩니다. 지원되는 다음 진단 테이블을 사용해야 합니다.

    - 검사점
    - Error
    - 관리
    - 연결
    - HostRegistration
    - AgentHealthStatus
    
    >[!NOTE]
    > 오류 메시지가 표시되지 않으면 2~4단계를 수행할 필요가 없습니다.

2. **호스트 풀 구성** 을 선택합니다.
3. **배포** 를 선택합니다.
4. 구성 통합 문서를 새로 고칩니다.

#### <a name="workspace-diagnostic-settings"></a>작업 영역 진단 설정 

구성 통합 문서의 리소스 진단 설정 섹션을 사용하여 작업 영역 진단을 설정하려면 다음을 수행합니다.

 1. **작업 영역** 에서 Azure Virtual Desktop 작업 영역에 Azure Virtual Desktop 진단이 사용되는지 확인합니다. 사용되지 않는 경우 "선택한 작업 영역에 대한 기존 진단 구성이 없습니다."라는 오류 메시지가 표시됩니다. 지원되는 다음 진단 테이블을 사용해야 합니다.
 
    - 검사점
    - Error
    - 관리
    - 피드
    
    >[!NOTE]
    > 오류 메시지가 표시되지 않으면 2~4단계를 수행할 필요가 없습니다.

2. **작업 영역 구성** 을 선택합니다.
3. **배포** 를 선택합니다.
4. 구성 통합 문서를 새로 고칩니다.

### <a name="session-host-data-settings"></a>세션 호스트 데이터 설정

Azure Virtual Desktop 세션 호스트에 대한 정보를 수집하려면 호스트 풀의 모든 세션 호스트에 Log Analytics 에이전트를 설치하고, 세션 호스트가 Log Analytics 작업 영역으로 보내고 있는지 확인하고, 성능 데이터와 Windows 이벤트 로그를 수집하도록 Log Analytics 에이전트 설정을 구성해야 합니다.

세션 호스트 데이터를 보내는 대상 Log Analytics 작업 영역이 진단 데이터를 보내는 대상과 동일하지 않아도 됩니다. Azure Virtual Desktop 환경 외부에 Azure 세션 호스트가 있는 경우 Azure Virtual Desktop 세션 호스트에 대해 지정된 Log Analytics 작업 영역을 두는 것이 좋습니다. 

세션 호스트 데이터를 수집할 Log Analytics 작업 영역을 설정하려면 다음을 수행합니다. 

1. 구성 통합 문서에서 **세션 호스트 데이터 설정** 탭을 선택합니다. 
2. 세션 호스트 데이터를 보낼 대상 **Log Analytics 작업 영역** 을 선택합니다. 

#### <a name="session-hosts"></a>세션 호스트

호스트 풀의 모든 세션 호스트에 Log Analytics 에이전트를 설치하고 해당 호스트에서 선택한 Log Analytics 작업 영역으로 데이터를 보내야 합니다. 호스트 풀의 모든 세션 호스트에 대해 Log Analytics가 구성되지 않은 경우 "호스트 풀의 일부 호스트가 선택한 Log Analytics 작업 영역에 데이터를 보내고 있지 않습니다."라는 메시지와 함께 **세션 호스트 데이터 설정** 맨 위에 **세션 호스트** 섹션이 표시됩니다.

>[!NOTE]
> **세션 호스트** 섹션이나 오류 메시지가 표시되지 않으면 모든 세션 호스트가 올바르게 설정된 것입니다. [작업 영역 성능 카운터](#workspace-performance-counters)에 대한 지침 설정으로 건너뜁니다.

구성 통합 문서를 사용하여 남은 세션 호스트를 설정하려면 다음을 수행합니다.

1. **작업 영역에 호스트 추가** 를 선택합니다. 
2. 구성 통합 문서를 새로 고칩니다.

>[!NOTE]
>Log Analytics 확장을 설치하려면 호스트 컴퓨터가 실행 중이어야 합니다. 자동 배포가 작동하지 않는 경우 대신 호스트에 확장을 수동으로 설치할 수 있습니다. 확장을 수동으로 설치하는 방법 대해 알아보려면 [Windows용 Log Analytics 가상 머신 확장](../virtual-machines/extensions/oms-windows.md)을 참조하세요.

#### <a name="workspace-performance-counters"></a>작업 영역 성능 카운터

세션 호스트에서 성능 정보를 수집하여 Log Analytics 작업 영역으로 보내려면 특정 성능 카운터를 사용하도록 설정해야 합니다.

성능 카운터를 이미 사용하도록 설정한 상태에서 해당 카운터를 제거하려면 [성능 카운터 구성](../azure-monitor/agents/data-sources-performance-counters.md)의 지침을 따르세요. 동일한 위치에서 성능 카운터를 추가하고 제거할 수 있습니다.

구성 통합 문서를 사용하여 성능 카운터를 설정하려면 다음을 수행합니다. 

1. 구성 문서의 **작업 영역 성능 카운터** 에서 **구성된 카운터** 를 보고 이미 Log Analytics 작업 영역으로 보내도록 설정된 카운터를 확인합니다. **누락된 카운터** 를 보고 필요한 모든 카운터를 사용하도록 설정했는지 확인합니다.
2. 누락된 카운터가 있는 경우 **성능 카운터 구성** 을 선택합니다.
3. **구성 적용** 을 선택합니다.
4. 구성 통합 문서를 새로 고칩니다.
5. **누락된 카운터** 목록을 보고 필요한 모든 카운터를 사용하도록 설정했는지 확인합니다. 

#### <a name="configure-windows-event-logs"></a>Windows 이벤트 로그 구성

세션 호스트에서 오류, 경고 및 정보를 수집하고 Log Analytics 작업 영역으로 보내려면 특정 Windows 이벤트 로그도 사용하도록 설정해야 합니다.

Windows 이벤트 로그를 이미 사용하도록 설정했고 이를 제거하려면 [Windows 이벤트 로그 구성](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)의 지침을 따르세요.  동일한 위치에서 Windows 이벤트 로그를 추가하고 제거할 수 있습니다.

구성 통합 문서를 사용하여 Windows 이벤트 로그를 설정하려면 다음을 수행합니다.

1. **Windows 이벤트 로그 구성** 에서 **구성된 이벤트 로그** 를 보고 Log Analytics 작업 영역으로 보내도록 이미 설정한 이벤트 로그를 확인합니다. **누락된 이벤트 로그** 를 보고 모든 Windows 이벤트 로그를 사용하도록 설정했는지 확인합니다.
2. 누락된 Windows 이벤트 로그가 없는 경우 **이벤트 구성** 을 선택합니다.
3. **배포** 를 선택합니다.
4. 구성 통합 문서를 새로 고칩니다.
5. **누락된 이벤트 로그** 목록을 보고 필요한 모든 Windows 이벤트 로그를 사용하도록 설정했는지 확인합니다. 

>[!NOTE]
>자동 이벤트 배포가 실패하는 경우 구성 통합 문서에서 **에이전트 구성 열기** 를 선택하여 누락된 Windows 이벤트 로그를 수동으로 추가합니다. 

## <a name="optional-configure-alerts"></a>선택 사항: 경고 구성

Azure Monitor for Azure Virtual Desktop을 사용하면 Azure Virtual Desktop 데이터의 컨텍스트에서 선택한 구독 내에서 발생하는 Azure Monitor 경고를 모니터링할 수 있습니다. Azure Monitor 경고는 Azure 구독에 대한 선택적 기능이며 Azure Monitor for Azure Virtual Desktop과 별도로 설정해야 합니다. Azure Monitor 경고 프레임워크를 사용하여 Azure Virtual Desktop 이벤트, 진단 및 리소스에 대한 사용자 지정 경고를 설정할 수 있습니다. Azure Monitor 경고에 대한 자세한 내용은 [Azure Monitor 로그 경고](../azure-monitor/alerts/alerts-log.md)를 참조하세요.

## <a name="diagnostic-and-usage-data"></a>진단 및 사용량 현황 데이터

Microsoft는 Azure Monitor 서비스를 사용하여 사용 현황 및 성능 데이터를 자동으로 수집합니다. Microsoft는 이 데이터를 사용하여 서비스의 품질, 보안 및 무결성을 개선합니다.

정확하고 효율적인 문제 해결 기능을 제공하기 위해 수집된 데이터에는 포털 세션 ID, Azure Active Directory 사용자 ID 및 이벤트가 발생한 포털 탭의 이름이 포함됩니다. 이름, 주소 또는 기타 연락처 정보는 수집하지 않습니다.

데이터 수집 및 사용에 대한 자세한 내용은 [Microsoft Online Services 개인정보처리방침](https://privacy.microsoft.com/privacystatement)을 참조하세요.

>[!NOTE]
>서비스에서 수집된 개인 데이터의 보기 또는 삭제에 대해 알아보려면 [GDPR에 대한 Azure 데이터 주체 요청](/microsoft-365/compliance/gdpr-dsr-azure)을 참조하세요. GDPR에 대한 자세한 내용은 [Service Trust Portal의 GDPR 섹션](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 Azure Virtual Desktop 환경에 대해 Azure Monitor가 구성되었습니다. 다음은 환경 모니터링을 시작하는 데 도움이 될 수 있는 몇 가지 리소스입니다.

- Azure Monitor for Azure Virtual Desktop과 관련된 용어 및 개념에 대해 자세히 알아보려면 [용어집](azure-monitor-glossary.md)을 확인하세요.
- 데이터 스토리지 비용을 예측, 측정 및 관리하려면 [예상 Azure Monitor 비용](azure-monitor-costs.md)을 참조하세요.
- 문제가 발생하는 경우 [문제 해결 가이드](troubleshoot-azure-monitor.md)에서 도움말과 알려진 문제를 확인하세요.
- 각 버전 업데이트의 새로운 내용을 보려면 [Azure Virtual Desktop용 Azure Monitor의 새로운 내용](whats-new-azure-monitor.md)을 참조하세요.