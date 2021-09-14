---
title: Azure 파일 동기화 서버 엔드포인트 프로비전 해제 | Microsoft Docs
description: 사용 사례에 따라 Azure 파일 동기화 서버 엔드포인트를 프로비전 해제하는 방법에 대한 지침입니다.
author: mtalasila
ms.service: storage
ms.topic: how-to
ms.date: 6/01/2021
ms.author: mtalasila
ms.subservice: files
ms.openlocfilehash: b608100ab9ec1706e65cd4930b00ef163218f64d
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258701"
---
# <a name="deprovision-your-azure-file-sync-server-endpoint"></a>Azure 파일 동기화 서버 엔드포인트 프로비전 해제

서버 엔드포인트를 제거한다는 것은 동일한 동기화 그룹에 있는 클라우드 엔드포인트(Azure 파일 공유)를 사용하여 해당 서버 위치와의 동기화를 중지하는 것을 의미합니다. 서버 엔드포인트의 프로비전을 해제하기 전에 데이터 무결성과 가용성을 유지하기 위해 먼저 수행해야 하는 몇 가지 단계가 있습니다. 이 문서에서는 여러 가지 프로비전 해제 방법과 적절한 지침을 시나리오별로 설명합니다. 자신에게 가장 적합한 사용 사례에 대한 단계를 따르세요.

현재 동기화 중인 데이터를 영구적으로 잃게 되어도 괜찮다면 서버 엔드포인트 프로비전 해제로 바로 건너뛰어도 됩니다.

> [!Warning]
> 서버 엔드포인트의 프로비전을 해제하여 동기화 문제를 해결하려고 하지 마세요. 문제 해결 도움말은 [Azure 파일 동기화 문제 해결](./file-sync-troubleshoot.md)을 참조하세요. 서버 측 또는 클라우드 측을 상대방과 완전히 동기화하지 않은 상태에서 서버 엔드포인트를 삭제하면 영구적인 데이터 손실이 발생할 수 있습니다. 서버 엔트포인트를 제거하는 것은 파괴적 작업입니다. 서버 엔드포인트 내에 계층화된 파일은 서버 엔드포인트를 다시 만든 후에 Azure 파일 공유의 해당 위치에 "다시 연결"되지 않습니다. 그러면 동기화 오류가 발생합니다. 또한 서버 엔드포인트 네임스페이스 외부에 있는 계층화된 파일은 영구적으로 손실될 수 있습니다. 클라우드 계층화를 한 번도 사용하지 않아도 계층화된 파일이 서버 엔드포인트에 있을 수 있습니다.

## <a name="scenario-1-you-intend-to-delete-your-server-endpoint-and-stop-using-your-local-server--vm"></a>시나리오 1: 서버 엔드포인트를 삭제하고 로컬 서버/VM 사용을 중지하려고 합니다.

여기서 목표는 클라우드 엔드포인트의 데이터를 최신 상태로 만드는 것입니다. 대신 서버 엔드포인트에서 전체 파일 세트를 최신 상태로 만들려면 [시나리오 2: 서버 엔드포인트를 삭제하고 이 특정 Azure 파일 공유 사용을 중지하려고 합니다](#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share)를 참조하세요.

이 범주에 속하는 몇 가지 사용 사례는 다음과 같습니다.
-   Azure 파일 공유로 마이그레이션
-   서버리스로 전환
-   나머지 동기화 그룹은 그대로 유지하면서 특정 서버 엔드포인트 경로의 사용 중단

이 시나리오에서는 서버 엔드포인트를 삭제하기 전에 먼저 사용자 액세스 차단, 특수 VSS 업로드 세션 시작 및 최종 동기화 세션이 완료될 때까지 대기의 세 가지 단계를 수행하게 됩니다.

### <a name="remove-user-access-to-your-server-endpoint"></a>서버 엔드포인트에 대한 사용자 액세스 차단

서버 엔드포인트의 프로비전을 해제하기 전에 서버의 모든 변경 사항을 클라우드에 동기화할 수 있는지 확인해야 합니다. 클라우드가 따라 잡을 수 있도록 만드는 첫 번째 단계는 서버 엔드포인트의 파일 및 폴더에 대한 추가 변경 기회를 제거하는 것입니다. 

액세스를 차단하면 가동 중지 시간을 초래합니다. 가동 중지 시간을 줄이기 위해 사용자 액세스를 클라우드 엔드포인트로 리디렉션하는 것을 고려해볼 수도 있습니다. 

자체 기록을 위해 사용자 액세스를 차단한 날짜와 시간을 기록하고 다음 섹션으로 넘어갑니다.

### <a name="initiate-a-special-volume-snapshot-service-vss-upload-session"></a>특수 VSS(볼륨 스냅샷 서비스) 업로드 세션 시작

Azure 파일 동기화는 매일 서버에 임시 VSS 스냅샷을 만들어 핸들이 열린 파일을 동기화합니다. 최종 동기화 세션에서 최신 데이터를 업로드하고 항목당 오류를 줄이기 위해 VSS 업로드를 위한 특수 세션을 시작합니다. 이렇게 하면 또한 스냅샷이 생성된 후 시작되는 특수 동기화 업로드 세션도 트리거됩니다.  

이렇게 하려면 로컬 서버에서 **작업 스케줄러** 를 열고 **Microsoft\StorageSync** 로 이동하여 `VssSyncScheduledTask` 작업을 마우스 오른쪽 단추로 클릭하고 **실행** 을 선택합니다.

> [!Important]
> 이 단계를 완료한 날짜와 시간을 기록해둡니다. 다음 섹션에서 필요합니다.

![VSS 업로드 세션을 예약하는 스크린샷](media/file-sync-server-endpoint-delete/vss-task-scheduler.png)

### <a name="wait-for-a-final-sync-upload-session-to-complete"></a>최종 동기화 업로드 세션 완료 시까지 대기

클라우드에 최신 데이터가 존재하도록 하기 위해 최종 동기화 업로드 세션이 완료될 때까지 기다려야 합니다. 

동기화 세션의 상태를 확인하려면 로컬 서버에서 **이벤트 뷰어** 를 엽니다. 원격 분석 이벤트 로그 **(Applications and Services\Microsoft\FileSync\Agent)** 로 이동합니다. VSS 업로드 세션을 수동으로 시작한 후 발생한 9102 이벤트(‘sync direction’ = upload, ‘HResult’ = 0, ‘PerItemErrorCount’ = 0)가 표시되는지 확인합니다.

![최종 동기화 세션이 완료되었는지 확인하는 스크린샷.](media/file-sync-server-endpoint-delete/event-viewer.png)

‘PerItemErrorCount’가 0보다 크면 파일이 동기화되지 않는 것입니다. 동기화되지 않는 파일을 확인하려면 **FileSyncErrorsReport.ps1** 을 사용합니다. 이 PowerShell 스크립트는 일반적으로 Azure 파일 동기화 에이전트가 설치된 서버의 **C:\Program Files\Azure\StorageSyncAgent\FileSyncErrorsReport.ps1** 경로에 있습니다.

해당 파일이 중요하지 않은 경우 서버 엔드포인트를 삭제해도 됩니다. 파일이 중요한 경우 오류를 수정하고 다른 9102 이벤트(‘sync direction’ = upload, ‘HResult’ = 0, ‘PerItemErrorCount’ = 0)가 발생할 때까지 기다린 후 서버 엔드포인트를 삭제합니다.

## <a name="scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share"></a>시나리오 2: 서버 엔드포인트를 삭제하고 이 특정 Azure 파일 공유 사용을 중지하려고 합니다.

여기서 목표는 로컬 서버/VM의 데이터를 최신 상태로 만드는 것입니다. 대신 클라우드 엔드포인트에서 전체 파일 세트를 최신 상태로 만들려면 [시나리오 1: 서버 엔드포인트를 삭제하고 로컬 서버/VM 사용을 중지합니다](#scenario-1-you-intend-to-delete-your-server-endpoint-and-stop-using-your-local-server--vm)를 참조하세요.

이 시나리오에서는 서버 엔드포인트를 삭제하기 전에 먼저 클라우드 계층화를 사용하지 않도록 설정, 계층화된 파일 회수, 클라우드 변경 검색 시작, 최종 동기화 세션이 완료될 때까지 대기의 4가지 단계를 수행하게 됩니다.

### <a name="disable-cloud-tiering"></a>클라우드 계층화를 사용하지 않도록 설정
프로비전을 해제하려는 서버 엔드포인트의 **서버 엔드포인트 속성** 에서 클라우드 계층화 섹션으로 이동하여 클라우드 계층화를 사용하지 않도록 설정합니다.

### <a name="recall-all-tiered-files"></a>계층화된 모든 파일 회수
클라우드 계층화를 사용하지 않는 경우에도 모든 파일이 로컬에 저장되도록 계층화된 파일을 모두 회수해야 합니다.

파일을 회수하기 전에 모든 파일을 로컬로 저장할 수 있는 충분한 여유 공간이 있는지 확인합니다. 여유 공간은 클라우드의 Azure 파일 공유 크기에서 서버의 캐시된 크기를 뺀 크기 정도여야 합니다.

**Invoke-StorageSyncFileRecall** PowerShell cmdlet을 사용하고 **SyncGroupName** 매개 변수를 지정하여 모든 파일을 회수합니다. 
```powershell
Invoke-StorageSyncFileRecall -SyncGroupName "samplesyncgroupname"
```
이 cmdlet의 실행이 완료되면 다음 섹션으로 넘어갈 수 있습니다.

### <a name="initiate-cloud-change-detection"></a>클라우드 변경 검색 시작
클라우드에서 변경 검색을 시작하면 최신 변경 사항이 동기화됩니다.

Invoke-AzStorageSyncChangeDetection cmdlet를 사용하면 변경 검색을 시작할 수 있습니다. 

```powershell
Invoke-AzStorageSyncChangeDetection -ResourceGroupName "myResourceGroup" -StorageSyncServiceName "myStorageSyncServiceName" -SyncGroupName "mySyncGroupName" -Path "Data","Reporting\Templates" 
```

이 단계는 완료하는 데 시간이 걸릴 수 있습니다. 

> [!Important]
> 이렇게 시작된 클라우드 변경 검색 검사가 완료되면 완료된 날짜와 시간을 기록해둡니다. 이 정보는 다음 섹션에서 필요합니다.

### <a name="wait-for-a-final-sync-session-to-complete"></a>최종 동기화 세션이 완료될 때까지 대기
로컬 서버의 데이터를 최신 상태로 만들려면 최종 동기화 업로드 세션이 완료될 때까지 기다려야 합니다. 

이를 확인하려면 로컬 서버의 **이벤트 뷰어** 로 이동합니다. 원격 분석 이벤트 로그 **(Applications and Services\Microsoft\FileSync\Agent)** 로 이동합니다. 날짜/시간 클라우드 변경 검색이 완료된 후 발생한 9102 이벤트(‘sync direction’ = download, ‘HResult’ = 0, ‘PerItemErrorCount’ = 0)가 표시되는지 확인합니다.

![최종 동기화 세션이 완료되었는지 확인하는 스크린샷.](media/file-sync-server-endpoint-delete/event-viewer.png)

‘PerItemErrorCount’가 0보다 크면 파일이 동기화되지 않는 것입니다. 동기화되지 않는 파일을 확인하려면 **FileSyncErrorsReport.ps1** 을 사용합니다. 이 PowerShell 스크립트는 일반적으로 Azure 파일 동기화 에이전트가 설치된 서버의 **C:\Program Files\Azure\StorageSyncAgent\FileSyncErrorsReport.ps1** 경로에 있습니다.

해당 파일이 중요하지 않은 경우 서버 엔드포인트를 삭제해도 됩니다. 파일이 중요한 경우 오류를 수정하고 다른 9102 이벤트(‘sync direction’= download, ‘HResult’= 0, ‘PerItemErrorCount’= 0)가 발생할 때까지 기다린 후 서버 엔드포인트를 삭제합니다.

## <a name="next-steps"></a>다음 단계
* [Azure 파일 동기화 토폴로지 수정](./file-sync-modify-sync-topology.md)
