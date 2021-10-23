---
title: Azure Virtual Desktop 용어집 모니터링 - Azure
description: Azure Virtual Desktop용 Azure Monitor과 관련된 용어 및 개념에 대한 용어집입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 46e70166fffba7c938ea6784db6eda18e69a043f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130214760"
---
# <a name="azure-monitor-for-azure-virtual-desktop-glossary"></a>Azure Virtual Desktop용 Azure Monitor 용어집

이 문서에서는 Azure Virtual Desktop용 Azure Monitor(미리 보기)과 관련된 주요 용어와 개념을 나열하고 간략하게 설명합니다.

## <a name="alerts"></a>경고

구독에서 구성하고 [심각도 0](#severity-0-alerts)으로 분류된 모든 활성 Azure Monitor 경고는 개요 페이지에 표시됩니다. 경고를 설정하는 방법을 알아보려면 [Azure Monitor 로그 경고](../azure-monitor/alerts/alerts-log.md)를 참조하세요.

## <a name="available-sessions"></a>사용 가능한 세션

사용 가능한 세션은 호스트 풀에서 사용 가능한 세션 수를 표시합니다. 이 서비스는 VM(가상 머신) 수를 가상 머신당 허용되는 최대 세션 수와 곱한 다음, 총 세션 수를 차감하여 이 값을 계산합니다.

## <a name="connection-success"></a>연결 성공

이 항목은 연결 상태를 표시합니다. "연결 성공"이란 해당 가상 머신의 스택에서 확인한 바와 같이 연결이 호스트에 도달할 수 있음을 의미합니다. 연결 실패는 연결이 호스트에 도달할 수 없음을 의미합니다.

## <a name="daily-active-users-dau"></a>DAU(일별 활성 사용자)

지난 24시간 동안 세션을 시작한 총 사용자 수입니다.

## <a name="daily-alerts"></a>일별 경고

매일 트리거된 총 경고 수입니다.

## <a name="daily-connections-and-reconnections"></a>매일 연결 및 재연결

지난 24 시간 이내에 시작되거나 완료된 총 연결 및 재연결 수입니다.

## <a name="daily-connected-hours"></a>일별 연결 시간

지난 24시간 동안 사용자 전체에서 세션에 연결한 총 시간입니다.

## <a name="diagnostics-and-errors"></a>진단 및 오류

Azure Virtual Desktop용 Azure Monitor에 오류 또는 경고가 표시되는 경우 다음 세 가지 항목으로 분류됩니다.

- 활동 유형: 이 범주는 Azure Virtual Desktop 진단에서 오류를 분류하는 방법입니다. 이 범주는 관리 작업, 피드, 연결, 호스트 등록, 오류 및 검사점입니다. 이러한 범주에 대한 자세히 알아보려면 [진단 기능에 Log Analytics 사용](diagnostics-log-analytics.md)을 참조하세요.

- 종류: 이 범주는 오류의 위치를 표시합니다. 

     - Azure Virtual Desktop 서비스에서 "service" 또는 "ServiceError = TRUE"로 표시된 오류가 발생했습니다.
     - Azure Virtual Desktop 서비스 외부에서 "deployment" 또는 태그가 지정된 "ServiceError = FALSE"로 표시된 오류가 발생했습니다.
     - ServiceError 태그에 대한 자세한 정보는 [일반적인 오류 시나리오](./troubleshoot-set-up-overview.md)를 참조하세요.

- 소스: 이 범주는 오류가 발생한 위치에 대한 보다 구체적인 설명을 제공합니다.

     - 진단: 사용자가 배포 문제를 관찰하고 진달할 수 있도록 서비스 활동을 모니터링하고 보고하는 서비스 역할입니다.

     - RDBroker: 배포 활동 오케스트레이션, 개체 상태 유지 관리, 인증 유효성 검사 등을 담당하는 서비스 역할입니다.

     - RDGateway: 최종 사용자와 가상 머신 간의 네트워크 연결 처리를 담당하는 서비스 역할입니다.

     - RDStack: Azure Virtual Desktop 서비스와 통신할 수 있도록 VM에 설치되는 소프트웨어 구성 요소입니다.

     - 클라이언트: Azure Virtual Desktop 서비스에 대한 인터페이스를 제공하는 최종 사용자 머신에서 실행되는 소프트웨어입니다. 선택을 완료하면 게시된 리소스 목록을 표시하고 원격 데스크톱 연결을 호스팅합니다.

각 진단 문제 또는 오류에는 발생한 문제를 설명하는 메시지가 포함됩니다. 오류 문제 해결에 대한 자세한 정보는 [Azure Virtual Desktop 문제 식별 및 진단](./troubleshoot-set-up-overview.md)을 참조하세요.

## <a name="input-delay"></a>입력 지연

Azure Virtual Desktop용 Azure Monitor의 “입력 지연”은 각 세션에 대한 프로세스 성능 카운터당 입력 지원을 의미합니다. [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)의 호스트 성능 페이지에서 이 성능 카운터는 30초마다 한 번씩 서비스에 보고서를 보내도록 구성됩니다. 이러한 30초 간격을 “샘플”이라고 하며 해당 기간에서 가장 나쁜 사례를 보고합니다. 중앙값과 p95 값은 모든 샘플에서 중앙값과 95번째 백분위수를 반영합니다.

**호스트별 입력 지연** 에서 세션 호스트 행을 선택하여 페이지의 다른 모든 시각적 개체를 해당 호스트로 필터링할 수 있습니다. 또한 프로세스 이름을 선택하여 시간 차트에 따른 중앙값 입력 지연을 필터링할 수도 있습니다.

다음 범주에 지연을 적용합니다.

- 양호: 150밀리초 미만
- 허용: 150-500밀리초
- 나쁨: 500-2,000밀리초(2초 미만)
- 불량: 2,000밀리초 이상(2초 이상)

입력 지연 카운터의 작동 방식에 대한 자세한 정보는 [사용자 입력 지연 성능 카운터](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)를 참조하세요.

##  <a name="monthly-active-users-mau"></a>MAU(월간 활성 사용자)

지난 28일 동안 세션을 시작한 총 사용자 수입니다. 데이터를 30일 이하로 저장하는 경우 사용 가능한 데이터가 28일 미만인 기간 동안 예상보다 낮은 MAU 및 연결 값을 표시할 수 있습니다.

## <a name="performance-counters"></a>성능 카운터

성능 카운터는 하드웨어 구성 요소, 운영 체제 및 애플리케이션의 성능을 보여 줍니다.

다음 표에는 Azure Monitor가 Azure Virtual Desktop에 사용하는 권장 성능 카운터 및 시간 간격이 나열되어 있습니다.

|성능 카운터 이름|시간 간격|
|---|---|
|Logical Disk(C:)\\Avg. Disk Queue Length|30초|
|Logical Disk(C:)\\Avg. Disk sec/Transfer|60초|
|Logical Disk(C:)\\Current Disk Queue Length|30초|
|Memory(\*)\\Available Mbytes|30초|
|Memory(\*)\\Page Faults/sec|30초|
|Memory(\*)\\Pages/sec|30초|
|Memory(\*)\\% Committed Bytes in Use|30초|
|PhysicalDisk(\*)\\Avg. Disk Queue Length|30초|
|PhysicalDisk(\*)\\Avg. Disk sec/Read|30초|
|PhysicalDisk(\*)\\Avg. Disk sec/Transfer|30초|
|PhysicalDisk(\*)\\Avg. Disk sec/Write|30초|
|Processor Information(_Total)\\% Processor Time|30초|
|Terminal Services(\*)\\Active Sessions|60초|
|Terminal Services(\*)\\Inactive Sessions|60초|
|Terminal Services(\*)\\Total Sessions|60초|
|\*User Input Delay per Process(\*)\\Max Input Delay|30초|
|\*User Input Delay per Session(\*)\\Max Input Delay|30초|
|RemoteFX Network(\*)\\Current TCP RTT|30초|
|RemoteFX Network(\*)\\Current UDP Bandwidth|30초|

성능 카운터를 읽는 방법에 대한 자세한 정보는 [성능 카운터 구성](../azure-monitor/agents/data-sources-performance-counters.md)을 참조하세요.

입력 지연 성능 카운터에 대한 자세한 정보는 [사용자 입력 지연 성능 카운터](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)를 참조하세요.

## <a name="potential-connectivity-issues"></a>잠재적인 연결 문제

잠재적인 연결 문제는 연결 실패율이 높은 호스트, 사용자, 게시된 리소스 및 클라이언트를 보여 줍니다. "보고 기준" 필터를 선택하면 다음 열의 값을 확인하여 문제의 심각도를 평가할 수 있습니다.

- 시도(연결 시도 횟수)
- 리소스(게시된 앱 또는 데스크톱 수)
- 호스트(VM 수)
- 클라이언트

예를 들어, **사용자별** 필터를 선택하면 **시도** 열에서 각 사용자의 연결 시도를 확인할 수 있습니다.

연결 문제가 여러 호스트, 사용자, 리소스 또는 클라이언트에 걸쳐 있는 경우 이 문제가 전체 시스템에 영향을 미칠 수 있습니다. 그렇지 않으면 우선 순위가 낮은 작은 문제입니다.

항목을 선택하여 추가 정보를 볼 수도 있습니다. 이 문제와 관련된 호스트, 리소스 및 클라이언트 버전을 확인할 수 있습니다. 연결 시도 중에 보고된 모든 오류도 디스플레이에 표시됩니다.

## <a name="round-trip-time-rtt"></a>RTT(왕복 시간)

RTT(왕복 시간)는 최종 사용자의 위치와 세션 호스트의 Azure 지역 간 연결의 예상 왕복 시간입니다. 대기 시간이 가장 짧은 위치를 확인하려면 [Azure Virtual Desktop 경험 예측 도구](https://azure.microsoft.com/services/virtual-desktop/assessment/)에서 원하는 위치를 조회합니다.

## <a name="session-history"></a>세션 기록

**세션** 항목은 연결 및 연결 해제된 모든 세션의 상태를 표시합니다. **유휴 세션** 은 연결이 끊긴 세션만 표시합니다.

## <a name="severity-0-alerts"></a>심각도 0 경고

즉시 처리해야 하는 가장 긴급한 항목입니다. 이러한 문제를 해결하지 않으면 Azure Virtual Desktop 배포의 작동이 중지될 수 있습니다.

## <a name="time-to-connect"></a>연결 시간

연결 시간은 사용자가 세션을 시작하는 시점과 서비스에 로그인된 것으로 간주되는 시점 사이의 시간입니다. 새 연결을 설정하는 것은 기존 연결을 다시 설정하는 것보다 시간이 오래 걸리는 경향이 있습니다.

## <a name="user-report"></a>사용자 보고서

사용자 보고서 페이지에서는 특정 사용자의 연결 기록 및 진단 정보를 볼 수 있습니다. 각 사용자 보고서에는 사용 패턴, 사용자 피드백 및 세션 중에 발생한 모든 오류가 표시됩니다. 대부분의 작은 문제는 사용자 피드백을 통해 해결할 수 있습니다. 심층적 분석을 수행해야 하는 경우 특정 연결 ID 또는 기간에 대한 정보를 필터링할 수도 있습니다.

## <a name="users-per-core"></a>코어당 사용자 수

각 가상 머신 코어의 사용자 수입니다. 시간 경과에 따른 코어당 최대 사용자 수를 추적하면 환경에서 코어당 높은 사용자 수, 낮은 사용자 수 또는 변동하는 사용자 수를 일관되게 실행하는지 여부를 확인할 수 있습니다. 활성 사용자 수를 알면 환경을 효율적으로 자원화하고 확장할 수 있습니다.

## <a name="windows-event-logs"></a>Windows 이벤트 로그

Windows 이벤트 로그는 Windows 가상 머신에서 Log Analytics 에이전트가 수집하는 데이터 원본입니다. 이벤트는 시스템 및 애플리케이션과 같은 표준 로그뿐만 아니라 모니터링해야 하는 애플리케이션에서 만든 사용자 지정 로그에서도 수집할 수 있습니다.

다음 표에는 Azure Virtual Desktop용 Azure Monitor에 필요한 Windows 이벤트 로그가 나열되어 있습니다.

|이벤트 이름|이벤트 유형|
|---|---|
|애플리케이션|오류 및 경고|
|Microsoft-Windows-TerminalServices-RemoteConnectionManager/Admin|오류, 경고 및 정보|
|Microsoft-Windows-TerminalServices-LocalSessionManager/Operational|오류, 경고 및 정보|
|시스템|오류 및 경고|
| Microsoft-FSLogix-Apps/Operational|오류, 경고 및 정보|
|Microsoft-FSLogix-Apps/Admin|오류, 경고 및 정보|

Windows 이벤트 로그에 대한 자세한 정보는 [Windows 이벤트 레코드 속성](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 시작하려면 [Azure Virtual Desktop용 Azure Monitor을 사용하여 배포 모니터링](azure-monitor.md)을 참조하세요.
- 데이터 스토리지 비용을 예측, 측정 및 관리하려면 [예상 Azure Monitor 비용](azure-monitor-costs.md)을 참조하세요.
- 문제가 발생하는 경우 [문제 해결 가이드](troubleshoot-azure-monitor.md)에서 도움말과 알려진 문제를 확인하세요.


또한 Azure Advisor를 설정하여 일반적인 문제를 해결하거나 방지하는 방법을 알아낼 수 있습니다. [Azure Virtual Desktop에서 Azure Advisor 사용](azure-advisor.md)에서 자세히 알아보세요.

도움이 필요하거나 질문이 있는 경우 커뮤니티 리소스를 확인하세요.

- [Azure Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)에서 해당 커뮤니티에 질문하거나 제안하세요.
   
- 피드백을 남기는 방법을 알아보려면 [Azure Virtual Desktop에 대한 문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md#report-issues)을 참조하세요.

- [Azure Virtual Desktop 피드백 허브](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)에서 Azure Virtual Desktop에 대한 피드백을 남길 수도 있습니다.
