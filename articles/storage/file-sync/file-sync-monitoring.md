---
title: Azure 파일 동기화 모니터링 | Microsoft Docs
description: Azure Monitor, 스토리지 동기화 서비스, Windows Server를 사용하여 Azure 파일 동기화 배포를 모니터링하는 방법을 검토합니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4f339427a7839e3b622b2d33bf3ef8bd137d39dc
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258419"
---
# <a name="monitor-azure-file-sync"></a>Azure 파일 동기화 모니터링

Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서에서는 Azure Monitor, 스토리지 동기화 서비스, Windows Server를 사용하여 Azure 파일 동기화 배포를 모니터링하는 방법을 설명합니다.

이 가이드에서는 다음 시나리오를 다룹니다. 
- Azure Monitor에서 Azure 파일 동기화 메트릭 보기
- Azure Monitor에서 위험 상태를 사전에 알리는 경고 만들기
- Azure Portal을 사용하여 Azure 파일 동기화 배포의 상태 확인
- Windows 서버에서 이벤트 로그 및 성능 카운터를 사용하여 Azure 파일 동기화 배포의 상태를 모니터링하는 방법 

## <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../../azure-monitor/overview.md)를 사용하여 메트릭을 보고 동기화, 클라우드 계층화, 서버 연결에 대한 경고를 구성합니다.  

### <a name="metrics"></a>메트릭

Azure 파일 동기화의 메트릭은 기본적으로 사용하도록 설정되며 15분마다 Azure Monitor로 전송됩니다.

**Azure Monitor에서 Azure 파일 동기화 메트릭을 보는 방법**
1. **Azure Portal** 에서 **스토리지 동기화 서비스** 로 이동하고 **메트릭** 을 클릭합니다.
2. **메트릭** 드롭다운을 클릭하고 보려는 메트릭을 선택합니다.

![Azure 파일 동기화 메트릭의 스크린샷](media/storage-sync-files-troubleshoot/file-sync-metrics.png)

Azure Monitor에서 사용 가능한 Azure 파일 동기화용 메트릭은 다음과 같습니다.

| 메트릭 이름 | Description |
|-|-|
| 동기화되는 바이트 수 | 전송되는 데이터 크기(업로드 및 다운로드)입니다.<br><br>단위: 바이트<br>집계 형식: 합계<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |
| 클라우드 계층화 캐시 적중률 | 전체 파일이 아니라 캐시에서 제공된 바이트와 클라우드에서 회수된 바이트의 백분율입니다.<br><br>단위: 백분율<br>집계 유형: 평균<br>적용 가능한 차원: 서버 엔드포인트 이름, 서버 이름, 동기화 그룹 이름 |
| 클라우드 계층화 회수 크기 | 회수되는 데이터의 크기입니다.<br><br>단위: 바이트<br>집계 형식: 합계<br>적용 가능한 차원: 서버 이름, 동기화 그룹 이름 |
| 애플리케이션별 클라우드 계층화 회수 크기 | 애플리케이션에서 회수한 데이터 크기입니다.<br><br>단위: 바이트<br>집계 형식: 합계<br>적용 가능한 차원: 애플리케이션 이름, 서버 이름, 동기화 그룹 이름 |
| 클라우드 계층화 회수 성공률 | 성공한 회수 요청의 백분율입니다.<br><br>단위: 백분율<br>집계 유형: 평균<br>적용 가능한 차원: 서버 엔드포인트 이름, 서버 이름, 동기화 그룹 이름 |
| 클라우드 계층화 회수 처리량 | 데이터 회수 처리량의 크기입니다.<br><br>단위: 바이트<br>집계 형식: 합계<br>적용 가능한 차원: 서버 이름, 동기화 그룹 이름 |
| 동기화 상태가 아닌 파일 | 동기화할 수 없는 파일의 수입니다.<br><br>단위: 개수<br>집계 형식: 평균, 합계<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |
| 동기화된 파일 수 | 전송되는 파일 수(업로드 및 다운로드)<br><br>단위: 개수<br>집계 형식: 합계<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |
| 서버 캐시 크기 | 서버에 캐시된 데이터의 크기입니다.<br><br>단위: 바이트<br>집계 유형: 평균<br>적용 가능한 차원: 서버 엔드포인트 이름, 서버 이름, 동기화 그룹 이름 |
| 서버 온라인 상태 | 서버에서 수신된 하트비트의 수입니다.<br><br>단위: 개수<br>집계 유형: 최대<br>적용 가능한 차원: 서버 이름 |
| 동기화 세션 결과 | 동기화 세션 결과입니다(1=정상 완료된 동기화 세션, 0=실패한 동기화 세션).<br><br>단위: 개수<br>집계 형식: 최대<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |

### <a name="alerts"></a>경고

경고는 모니터링 데이터에서 중요한 조건이 발견되면 사전에 알려줍니다. Azure Monitor에서 경고를 구성하는 방법에 대한 자세한 내용은 [Overview of alerts in Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md)(Microsoft Azure의 경고 개요)를 참조하세요.

**Azure 파일 동기화에 대한 경고를 만드는 방법**

1. **Azure Portal** 에서 **스토리지 동기화 서비스** 로 이동합니다. 
2. 모니터링 섹션에서 **경고**, **+ 새 경고 규칙** 을 차례로 클릭합니다.
3. **조건 선택** 을 클릭하고 경고에 대한 다음 정보를 제공합니다. 
    - **메트릭**
    - **차원 이름**
    - **경고 논리**
4. **작업 그룹 선택** 을 클릭하고, 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어 작업 그룹(메일, SMS 등)을 경고에 추가합니다.
5. **경고 세부 정보**(예: **경고 규칙 이름**, **설명**, **심각도**)를 입력합니다.
6. **경고 규칙 만들기** 를 클릭하여 경고를 만듭니다.  

다음 표에는 모니터링할 몇 가지 예제 시나리오와 경고에 사용할 적절한 메트릭이 나와 있습니다.

| 시나리오 | 경고에 사용할 메트릭 |
|-|-|
| 포털에서 서버 엔드포인트 상태에 오류 표시 | 동기화 세션 결과 |
| 파일이 서버 또는 클라우드 엔드포인트와 동기화되지 않음 | 동기화 상태가 아닌 파일 |
| 등록된 서버가 스토리지 동기화 서비스와 통신하지 못함 | 서버 온라인 상태 |
| 클라우드 계층화 회수 크기가 하루에 500GiB를 초과함  | 클라우드 계층화 회수 크기 |

해당 시나리오에 대한 경고를 만드는 방법에 대한 지침은 [경고 예](#alert-examples) 섹션을 참조하세요.

## <a name="storage-sync-service"></a>스토리지 동기화 서비스

**Azure Portal** 에서 Azure 파일 동기화 배포의 상태를 보려면 **스토리지 동기화 서비스** 로 이동하여 다음 정보를 사용할 수 있습니다.

- 등록된 서버 상태
- 서버 엔드포인트 상태
    - 동기화 상태가 아닌 파일
    - 동기화 작업
    - 클라우드 계층화 효율성
    - 계층화되지 않은 파일
    - 회수 오류
- 메트릭

### <a name="registered-server-health"></a>등록된 서버 상태

포털에서 **등록된 서버 상태** 를 보려면 **스토리지 동기화 서비스** 의 **등록된 서버** 섹션으로 이동합니다.

![등록된 서버 상태 스크린샷](media/storage-sync-files-troubleshoot/file-sync-registered-servers.png)

- **등록된 서버** 상태가 **온라인** 이면 서버가 서비스와 정상적으로 통신하고 있는 것입니다.
- **등록된 서버** 상태가 **Appears Offline**(오프라인으로 표시)이면 스토리지 동기화 모니터 프로세스(AzureStorageSyncMonitor.exe)가 실행되고 있지 않거나 서버가 Azure 파일 동기화 서비스에 액세스할 수 없는 것입니다. 지침은 [문제 해결 설명서](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity)를 참조하세요.

### <a name="server-endpoint-health"></a>서버 엔드포인트 상태

포털에서 **서버 엔드포인트** 의 상태를 보려면 **스토리지 동기화 서비스** 의 **동기화 그룹** 섹션으로 이동하여 **동기화 그룹** 을 선택합니다.

![서버 엔드포인트 상태의 스크린샷](media/storage-sync-files-troubleshoot/file-sync-server-endpoint-health.png)

- 포털의 **서버 엔드포인트 상태** 및 **동기화 작업** 은 서버의 원격 분석 이벤트 로그에 기록되는 동기화 이벤트(ID 9102 및 9302)를 기준으로 합니다. 오류 취소됨과 같은 일시적인 오류로 인해 동기화 세션이 실패하는 경우 현재 동기화 세션이 진행 중인 동안(파일이 적용됨) 포털에서 서버 엔드포인트가 계속 **정상** 으로 표시됩니다. 이벤트 ID 9302는 동기화 진행률 이벤트이고 이벤트 ID 9102는 동기화 세션이 완료되면 기록됩니다.  자세한 내용은 [sync health](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync)(동기화 상태) 및 [sync progress](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)(동기화 진행률)를 참조하세요. 서버 엔드포인트 상태가 **오류** 또는 **No Activity**(작업 없음)로 표시되는 경우 [문제 해결 설명서](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#common-sync-errors)의 지침을 참조하세요.
- 포털에서 **동기화 상태가 아닌 파일** 수는 서버의 원격 분석 이벤트 로그에 기록된 이벤트 ID 9121을 기반으로 합니다. 이 이벤트는 동기화 세션이 완료되면 각 항목별 오류에 대해 기록됩니다. 항목별 오류를 해결하려면 [How do I see if there are specific files or folders that are not syncing?](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)(동기화되지 않는 특정 파일이나 폴더가 있는지 확인하는 방법)을 참조하세요.
- 포털에서 **클라우드 계층화 효율성** 을 보려면 **서버 엔드포인트 속성** 으로 이동하여 **클라우드 계층화** 섹션으로 이동합니다. 클라우드 계층화 효율성에 제공되는 데이터는 서버의 원격 분석 이벤트 로그에 기록된 이벤트 ID 9071을 기반으로 합니다. 자세한 내용은 [Monitor cloud tiering](file-sync-monitor-cloud-tiering.md)(클라우드 계층화 모니터링)을 참조하세요.
- 포털에서 **계층화되지 않은 파일** 및 **회수 오류** 를 보려면 **서버 엔드포인트 속성** 으로 이동하여 **클라우드 계층화** 섹션으로 이동합니다. **계층화되지 않은 파일** 은 서버의 원격 분석 이벤트 로그에 기록된 이벤트 ID 9003을 기반으로 하고 **회수 오류** 는 이벤트 ID 9006을 기반으로 합니다. 계층화나 회수에 실패한 파일을 조사하려면 [How to troubleshoot files that fail to tier](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-tier)(계층화에 실패한 파일의 문제 해결 방법) 및 [How to troubleshoot files that fail to be recalled](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled)(회수에 실패한 파일의 문제 해결 방법)를 참조하세요.

### <a name="metric-charts"></a>메트릭 차트

- 다음 메트릭 차트는 스토리지 동기화 서비스 포털에서 볼 수 있습니다.

  | 메트릭 이름 | Description | 블레이드 이름 |
  |-|-|-|
  | 동기화되는 바이트 수 | 전송되는 데이터 크기(업로드 및 다운로드) | 동기화 그룹, 서버 엔드포인트 |
  | 클라우드 계층화 회수 | 회수되는 데이터 크기 | 등록된 서버 |
  | 동기화 상태가 아닌 파일 | 동기화할 수 없는 파일 수 | 서버 엔드포인트 |
  | 동기화된 파일 수 | 전송되는 파일 수(업로드 및 다운로드) | 동기화 그룹, 서버 엔드포인트 |
  | 서버 온라인 상태 | 서버에서 수신된 하트비트 수 | 등록된 서버 |

- 자세한 내용은 [Azure Monitor](#azure-monitor)를 참조하세요.

  > [!Note]  
  > 스토리지 동기화 서비스 포털에서 제공하는 차트의 시간 범위는 24시간입니다. 다른 시간 범위 또는 차원을 보려면 Azure Monitor를 사용하세요.

## <a name="windows-server"></a>Windows Server

Azure 파일 동기화 에이전트가 설치된 **Windows Server** 에서 **이벤트 로그** 및 **성능 카운터** 를 사용하여 해당 서버에서 서버 엔드포인트의 상태를 볼 수 있습니다.

### <a name="event-logs"></a>이벤트 로그

서버의 원격 분석 이벤트 로그를 사용하여 등록된 서버, 동기화 및 클라우드 계층화 상태를 모니터링합니다. 원격 분석 이벤트 로그는 *Applications and Services\Microsoft\FileSync\Agent* 아래 이벤트 뷰어에 있습니다.

동기화 상태

- 동기화 시스템이 완료되면 이벤트 ID 9102가 기록됩니다. 이 이벤트를 사용하여 동기화 세션이 성공적이었는지(**HResult = 0**) 아니면 항목별 동기화 오류가 발생했는지(**PerItemErrorCount**)를 확인할 수 있습니다. 자세한 내용은 [sync health](file-sync-troubleshoot.md?tabs=server%252cazure-portal#broken-sync)(동기화 상태) 및 [per-item errors](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)(항목별 오류) 설명서를 참조하세요.

  > [!Note]  
  > 동기화 세션이 전반적으로 실패하거나 PerItemErrorCount가 0이 아닌 경우도 있습니다. 그러나 여전히 진행되고 있고 일부 파일은 동기화됩니다. AppliedFileCount, AppliedDirCount, AppliedTombstoneCount, AppliedSizeBytes 등과 같은 적용됨 필드에서 해당 내용을 확인할 수 있습니다. 해당 필드를 통해 성공한 세션의 양을 알 수 있습니다. 여러 동기화 세션이 연속으로 실패하고 적용됨 횟수는 증가하는 경우 지원 티켓을 개설하기 전에 동기화 시간을 지정하고 다시 시도해 보세요.

- 동기화 세션이 완료되면 각 항목별 오류에 대해 이벤트 ID 9121이 기록됩니다. 이 이벤트를 사용하면 이 오류와 동기화되지 않는 파일 수를 확인할 수 있습니다(**PersistentCount** 및 **TransientCount**). 지속되는 항목별 오류를 조사해야 합니다. [How do I see if there are specific files or folders that are not syncing?](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing)(동기화되지 않는 특정 파일이나 폴더가 있는지 확인하는 방법)을 참조하세요.

- 활성 동기화 세션이 있으면 5~10분마다 이벤트 ID 9302가 기록됩니다. 이 이벤트를 사용하면 동기화할 항목의 수(**TotalItemCount**), 지금까지 동기화된 항목 수(**AppliedItemCount**), 항목별 오류로 인해 동기화하지 못한 항목 수(**PerItemErrorCount**)를 확인할 수 있습니다. 동기화가 진행되지 않는 경우(**AppliedItemCount=0**) 동기화 세션은 실패하며, 이벤트 ID 9102가 기록되고 오류가 발생합니다. 자세한 내용은 [sync progress documentation](file-sync-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)(동기화 진행률 설명서)을 참조하세요.

등록된 서버 상태

- 서버가 서비스에 작업을 쿼리하면 30초마다 이벤트 ID 9301이 기록됩니다. GetNextJob 완료 시의 상태가 **status = 0** 이면 서버가 서비스와 통신할 수 있는 것입니다. GetNextJob 완료 시 오류가 발생하면 [문제 해결 설명서](file-sync-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity)에서 지침을 참조하세요.

클라우드 계층화 상태

- 서버의 계층화 작업을 모니터링하려면 이벤트 뷰어의 *Applications and Services\Microsoft\FileSync\Agent* 에 있는 원격 분석 이벤트 로그에서 이벤트 ID 9003, 9016, 9029를 사용합니다.

  - 이벤트 ID 9003은 서버 엔드포인트에 대한 오류 분포를 제공합니다. 예: 총 오류 수, ErrorCode. 한 이벤트는 오류 코드별로 기록됩니다.
  - 이벤트 ID 9016은 볼륨에 대한 고스팅 결과를 제공합니다. 예: 사용 가능한 공간 비율, 세션에서 고스팅된 파일 수, 고스팅에 실패한 파일 수
  - 이벤트 ID 9029는 서버 엔드포인트의 고스팅 세션 정보를 제공합니다. 예: 세션에서 시도된 파일 수, 세션에서 계층화된 파일 수, 이미 계층화된 파일 수

- 서버의 회수 작업을 모니터링하려면 이벤트 뷰어의 *Applications and Services\Microsoft\FileSync\Agent* 에 있는 원격 분석 이벤트 로그에서 이벤트 ID 9005, 9006, 9009, 9059, 9071을 사용합니다.

  - 9005 이벤트 ID는 서버 엔드포인트에 대한 회수 안정성을 제공합니다. 예: 액세스된 고유한 파일 수, 액세스에 실패한 고유한 파일 수
  - 이벤트 ID 9006은 서버 엔드포인트에 대한 회수 오류 분포를 제공합니다. 예: 실패한 요청 수, ErrorCode. 한 이벤트는 오류 코드별로 기록됩니다.
  - 이벤트 ID 9009는 서버 엔드포인트의 회수 세션 정보를 제공합니다. 예: DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed
  - 이벤트 ID 9059는 서버 엔드포인트의 애플리케이션 회수 분포를 제공합니다. 예: ShareId, Application Name, TotalEgressNetworkBytes
  - 이벤트 ID 9071은 서버 엔드포인트의 클라우드 계층화 효율성을 제공합니다. 예: TotalDistinctFileCountCacheHit, TotalDistinctFileCountCacheMiss, TotalCacheHitBytes TotalCacheMissBytes

### <a name="performance-counters"></a>성능 카운터

동기화 활동을 모니터링할 서버에서 Azure 파일 동기화 성능 카운터를 사용합니다.

서버에서 Azure 파일 동기화 성능 카운터를 보려면 성능 모니터(Perfmon.exe)를 엽니다. **AFS Bytes Transferred** 및 **AFS Sync Operations** 개체 아래에서 카운터를 확인할 수 있습니다.

성능 모니터에서 사용 가능한 Azure 파일 동기화용 성능 카운터는 다음과 같습니다.

| 성능 개체\카운터 이름 | Description |
|-|-|
| AFS Bytes Transferred\Downloaded Bytes/sec | 초당 다운로드한 바이트 수입니다. |
| AFS Bytes Transferred\Uploaded Bytes/sec | 초당 업로드한 바이트 수입니다. |
| AFS Bytes Transferred\Total Bytes/sec | 초당 총 바이트 수(업로드/다운로드)입니다. |
| AFS Sync Operations\Downloaded Sync Files/sec | 초당 다운로드한 파일 수입니다. |
| AFS Sync Operations\Uploaded Sync Files/sec | 초당 업로드한 파일 수입니다. |
| AFS Sync Operations\Total Sync File Operations/sec | 동기화된 파일의 총 수(업로드/다운로드)입니다. |

## <a name="alert-examples"></a>경고 예제
이 섹션에서는 Azure 파일 동기화에 대한 경고의 몇 가지 예제를 제공합니다.

  > [!Note]  
  > 경고를 만들었지만 노이즈가 너무 많은 경우 임계값 및 경고 논리를 조정합니다.

### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>포털에서 서버 엔드포인트 상태에 오류가 표시되는 경우 경고를 만드는 방법

1. **Azure Portal** 에서 해당 **스토리지 동기화 서비스** 로 이동합니다. 
2. **모니터링** 섹션으로 이동하고 **경고** 를 클릭합니다. 
3. **+ 새 경고 규칙** 을 클릭하여 새 경고를 만듭니다. 
4. **조건 선택** 을 클릭하여 조건을 구성합니다.
5. **신호 논리 구성** 블레이드 내의 신호 이름 아래에서 **Sync session result**(동기화 세션 결과)를 클릭합니다.  
6. 다음 차원 구성을 선택합니다. 
     - 차원 이름: **서버 엔드포인트 이름**  
     - 연산자: **=** 
     - 차원 값: **모든 현재 및 미래 값**  
7. **경고 논리** 로 이동하고 다음을 완료합니다. 
     - 임계값을 **정적** 으로 설정 
     - 연산자: **보다 작음** 
     - 집계 형식: **최대**  
     - 임계값: **1** 
     - 평가 기준: 집계 세분성 = **24시간** | 평가 빈도 = **매시간** 
     - **완료** 를 클릭합니다. 
8. **작업 그룹 선택** 을 클릭하고, 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어 작업 그룹(메일, SMS 등)을 경고에 추가합니다.
9. **경고 세부 정보**(예: **경고 규칙 이름**, **설명**, **심각도**)를 입력합니다.
10. **경고 규칙 만들기** 를 클릭합니다. 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>파일이 서버 또는 클라우드 엔드포인트와 동기화되지 않는 경우 경고를 만드는 방법

1. **Azure Portal** 에서 해당 **스토리지 동기화 서비스** 로 이동합니다. 
2. **모니터링** 섹션으로 이동하고 **경고** 를 클릭합니다. 
3. **+ 새 경고 규칙** 을 클릭하여 새 경고를 만듭니다. 
4. **조건 선택** 을 클릭하여 조건을 구성합니다.
5. **신호 논리 구성** 블레이드 내의 신호 이름 아래에서 **동기화 상태가 아닌 파일** 을 클릭합니다.  
6. 다음 차원 구성을 선택합니다. 
     - 차원 이름: **서버 엔드포인트 이름**  
     - 연산자: **=** 
     - 차원 값: **모든 현재 및 미래 값**  
7. **경고 논리** 로 이동하고 다음을 완료합니다. 
     - 임계값을 **정적** 으로 설정 
     - 연산자: **보다 큼** 
     - 집계 형식: **평균**  
     - 임계값: **100** 
     - 평가 기준: 집계 세분성 = **5분** | 평가 빈도 = **5분마다** 
     - **완료** 를 클릭합니다. 
8. **작업 그룹 선택** 을 클릭하고, 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어 작업 그룹(메일, SMS 등)을 경고에 추가합니다.
9. **경고 세부 정보**(예: **경고 규칙 이름**, **설명**, **심각도**)를 입력합니다.
10. **경고 규칙 만들기** 를 클릭합니다. 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>등록된 서버가 스토리지 동기화 서비스와 통신하지 못하는 경우 경고를 만드는 방법

1. **Azure Portal** 에서 해당 **스토리지 동기화 서비스** 로 이동합니다. 
2. **모니터링** 섹션으로 이동하고 **경고** 를 클릭합니다. 
3. **+ 새 경고 규칙** 을 클릭하여 새 경고를 만듭니다. 
4. **조건 선택** 을 클릭하여 조건을 구성합니다.
5. **신호 논리 구성** 블레이드 내의 신호 이름 아래에서 **서버 온라인 상태** 를 클릭합니다.  
6. 다음 차원 구성을 선택합니다. 
     - 차원 이름: **서버 이름**  
     - 연산자: **=** 
     - 차원 값: **모든 현재 및 미래 값**  
7. **경고 논리** 로 이동하고 다음을 완료합니다. 
     - 임계값을 **정적** 으로 설정 
     - 연산자: **보다 작음** 
     - 집계 형식: **최대**  
     - 임계값(바이트): **1** 
     - 평가 기준: 집계 세분성 = **1시간** | 평가 빈도 = **30분마다** 
         - 메트릭은 15~20분마다 Azure Monitor로 전송됩니다. **평가 빈도** 를 30분 미만으로 설정하지 마세요(거짓 경고가 생성됨).
     - **완료** 를 클릭합니다. 
8. **작업 그룹 선택** 을 클릭하고, 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어 작업 그룹(메일, SMS 등)을 경고에 추가합니다.
9. **경고 세부 정보**(예: **경고 규칙 이름**, **설명**, **심각도**)를 입력합니다.
10. **경고 규칙 만들기** 를 클릭합니다. 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>클라우드 계층화 회수 크기가 하루에 500GiB를 초과한 경우 경고를 만드는 방법

1. **Azure Portal** 에서 해당 **스토리지 동기화 서비스** 로 이동합니다. 
2. **모니터링** 섹션으로 이동하고 **경고** 를 클릭합니다. 
3. **+ 새 경고 규칙** 을 클릭하여 새 경고를 만듭니다. 
4. **조건 선택** 을 클릭하여 조건을 구성합니다.
5. **신호 논리 구성** 블레이드 내의 신호 이름 아래에서 **Cloud tiering recall size**(클라우드 계층화 회수 크기)를 클릭합니다.  
6. 다음 차원 구성을 선택합니다. 
     - 차원 이름: **서버 이름**  
     - 연산자: **=** 
     - 차원 값: **모든 현재 및 미래 값**  
7. **경고 논리** 로 이동하고 다음을 완료합니다. 
     - 임계값을 **정적** 으로 설정 
     - 연산자: **보다 큼** 
     - 집계 형식: **총계**  
     - 임계값(바이트): **67108864000** 
     - 평가 기준: 집계 세분성 = **24시간** | 평가 빈도 = **매시간** 
     - **완료** 를 클릭합니다. 
8. **작업 그룹 선택** 을 클릭하고, 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어 작업 그룹(메일, SMS 등)을 경고에 추가합니다.
9. **경고 세부 정보**(예: **경고 규칙 이름**, **설명**, **심각도**)를 입력합니다.
10. **경고 규칙 만들기** 를 클릭합니다. 

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 배포 계획](file-sync-planning.md)
- [방화벽 및 프록시 설정 고려](file-sync-firewall-and-proxy.md)
- [Azure 파일 동기화 배포](file-sync-deployment-guide.md)
- [Azure 파일 동기화 문제 해결](file-sync-troubleshoot.md)
