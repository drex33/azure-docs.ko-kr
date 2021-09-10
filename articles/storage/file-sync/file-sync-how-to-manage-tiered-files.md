---
title: Azure 파일 동기화 계층화된 파일을 관리하는 방법 | Microsoft Docs
description: 계층화된 파일을 관리하는 데 도움되는 팁 및 PowerShell commandlet
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8ba54fa398d42aea43a93d3b3369f21f4d2168ec
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122538003"
---
# <a name="how-to-manage-tiered-files"></a>계층화된 파일을 관리하는 방법

이 문서에서는 계층화된 파일 관리와 관련된 질문이 있는 사용자를 위한 지침을 제공합니다. 클라우드 계층화와 관련된 개념 질문은 [Azure Files FAQ](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)를 참조하세요.

## <a name="how-to-check-if-your-files-are-being-tiered"></a>파일이 계층화되고 있는지 확인하는 방법

설정된 정책에 따라 파일을 계층화해야 하는지 여부를 한 시간에 한 번 평가합니다. 새 서버 엔드포인트를 만들 때 다음과 같은 두 가지 상황이 발생할 수 있습니다.

처음으로 새 서버 엔드포인트를 만들면 파일이 해당 서버 위치에 있는 경우가 많습니다. 클라우드 계층화를 시작하려면 먼저 파일을 업로드해야 합니다. 사용 가능한 볼륨 공간 정책은 모든 파일의 초기 업로드가 완료된 다음에야 해당 작업을 시작합니다. 그러나 선택적인 날짜 정책은 파일이 업로드되는 즉시 개별 파일에서 작업을 시작합니다. 여기에도 1시간 간격이 적용됩니다. 

새 서버 엔드포인트를 추가할 때 포함된 데이터를 사용하여 빈 서버 위치를 Azure 파일 공유에 연결했을 수 있습니다. 서버로 초기 다운로드를 수행하는 동안 네임스페이스를 다운로드하고 콘텐츠를 회수하도록 선택하면 네임스페이스가 다운로드된 후 파일은 사용 가능한 볼륨 공간 정책 및 선택적 날짜 정책 한도에 도달할 때까지 마지막으로 수정한 타임스탬프에 따라 회수됩니다.

파일이 Azure 파일 공유로 계층화되었는지 여부를 확인하는 몇 가지 방법이 있습니다.
    
   *  **파일의 파일 특성을 확인합니다.**
     파일을 마우스 오른쪽 단추로 클릭하고 **세부 정보** 로 이동한 다음 아래쪽의 **특성** 속성으로 스크롤합니다. 계층화된 파일에는 다음과 같은 특성 집합이 적용됩니다.     
        
        | 특성 문자 | attribute | 정의 |
        |:----------------:|-----------|------------|
        | A | 보관 | 파일을 백업 소프트웨어로 백업해야 함을 나타냅니다. 이 특성은 파일이 계층화되는지 또는 디스크에 완전히 저장되는지에 관계없이 항상 설정됩니다. |
        | P | 스파스 파일 | 파일이 스파스 파일인지를 나타냅니다. 스파스 파일은 디스크 스트림의 파일이 대부분 비어 있을 때 효율적으로 사용하기 위해 NTFS가 제공하는 특수한 형식의 파일입니다. Azure 파일 동기화는 파일이 완전히 계층화되거나 부분적으로 회수되기 때문에 스파스 파일을 사용합니다. 완전히 계층화된 파일에서 파일 스트림은 클라우드에 저장됩니다. 부분적으로 회수된 파일에서 파일의 해당 부분은 이미 디스크에 있습니다. 멀티미디어 플레이어 또는 압축 유틸리티와 같은 애플리케이션에 의해 파일이 부분적으로 읽힐 때 이러한 현상이 발생할 수 있습니다. 파일이 디스크에 완전히 회수되면 Azure 파일 동기화는 스파스 파일에서 일반 파일로 변환합니다. 이 특성은 Windows Server 2016 이상에서만 설정됩니다.|
        | M | 데이터 액세스 시 회수 | 파일의 데이터가 로컬 스토리지에 완전히 표시되지 않음을 나타냅니다. 파일을 읽으면 서버 엔드포인트가 연결된 Azure 파일 공유에서 적어도 몇 개의 파일 내용을 가져오게 됩니다. 이 특성은 Windows Server 2019에서만 설정됩니다. |
        | L | 재분석 지점 | 파일에 재분석 지점이 있음을 나타냅니다. 재분석 지점은 파일 시스템 필터에서 사용되는 특별한 포인터입니다. Azure 파일 동기화는 재분석 지점을 사용하여 Azure 파일 동기화 파일 시스템 필터(StorageSync.sys)에 파일이 저장되는 클라우드 위치를 정의합니다. 원활한 액세스를 지원합니다. 사용자는 Azure 파일 동기화가 사용되는지 또는 Azure 파일 공유에 있는 파일에 액세스하는 방법을 알 필요가 없습니다. 파일을 완전하게 회수되면 Azure 파일 동기화는 파일에서 재분석 지점을 제거합니다. |
        | O | 오프라인 | 파일 콘텐츠 일부 또는 전체가 디스크에 저장되지 않음을 나타냅니다. 파일을 완전하게 회수되면 Azure 파일 동기화는 이 특성을 제거합니다. |

        ![세부 정보 탭이 선택되어 있는 파일에 대한 속성 대화 상자](../files/media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > **특성** 필드를 파일 탐색기의 표 화면에 추가하여 폴더의 모든 파일에 대한 특성을 볼 수 있습니다. 이렇게 하려면 기존 열을 마우스 오른쪽 단추로 클릭하고(예: **크기**) **더 보기** 를 선택한 다음 드롭다운 목록에서 **특성** 을 선택합니다.
        
        > [!NOTE]
        > 이러한 모든 특성은 부분적으로 회수된 파일에서도 표시됩니다.
        
   * **`fsutil`을 사용하여 파일에 대한 재분석 지점을 확인합니다.**
       이전 옵션에서 설명된 것과 같이 계층화된 파일은 항상 재분석 지점 집합을 가집니다. 재분석 지점은 Azure 파일 동기화 파일 시스템 필터 드라이버(StorageSync.sys)가 서버에 로컬로 저장되지 않은 Azure 파일 공유에서 콘텐츠를 검색할 수 있도록 합니다. 

       파일에 재분석 지점이 있는지 확인하려면 관리자 권한 명령 프롬프트 또는 PowerShell 창에서 `fsutil` 유틸리티를 실행합니다.

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       파일에 재분석 지점이 있으면 **재분석 태그 값: 0x8000001e** 가 표시됩니다. 이 16진수 값은 Azure 파일 동기화에서 소유하는 재분석 지점 값입니다. 출력에는 Azure 파일 공유의 파일에 대한 경로를 나타내는 재분석 데이터도 포함됩니다.
        
        > [!WARNING]  
        > `fsutil reparsepoint` 유틸리티 명령에는 재분석 지점을 삭제하는 기능도 있습니다. Azure 파일 동기화 엔지니어링 팀에서 요청하는 경우가 아니면 이 명령을 실행하지 마십시오. 이 명령을 실행하면 데이터가 손실될 수 있습니다. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>클라우드 계층화 마지막 액세스 시간 추적에서 애플리케이션을 제외하는 방법

애플리케이션이 파일에 액세스하면 파일에 대한 마지막 액세스 시간이 클라우드 계층화 데이터베이스에서 업데이트됩니다. 파일 시스템을 검사하는 애플리케이션(예: 바이러스 백신)으로 인해 모든 파일의 마지막 액세스 시간이 동일해지면 파일이 계층화될 때 영향을 줄 수 있습니다.

마지막 액세스 시간 추적에서 애플리케이션을 제외하려면 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync 아래에 있는 적절한 레지스트리 설정에 프로세스 이름을 추가합니다.

v11 및 v12 릴리스의 경우 HeatTrackingProcessNameExclusionList 레지스트리 설정에 프로세스 제외를 추가합니다.
예: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

v13 릴리스 이상의 경우 HeatTrackingProcessNamesExclusionList 레지스트리 설정에 프로세스 제외를 추가합니다.
예: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNamesExclusionList /t REG_SZ /d "SampleApp.exe,AnotherApp.exe" /f

> [!NOTE]
> 데이터 중복 제거 및 FSRM(파일 서버 리소스 관리자) 프로세스는 기본적으로 제외됩니다. 프로세스 제외 목록의 변경 사항은 5분마다 시스템에 적용됩니다.

## <a name="how-to-access-the-heat-store"></a>열 저장소에 액세스하는 방법

클라우드 계층화는 파일의 마지막 액세스 시간 및 액세스 빈도를 사용하여 계층화할 파일을 결정합니다. 클라우드 계층화 필터 드라이버(storagesync.sys)는 마지막 액세스 시간을 추적하고 클라우드 계층화 열 저장소에 정보를 기록합니다. 서버-로컬 PowerShell cmdlet을 사용하여 열 저장소를 검색하고 CSV 파일에 저장할 수 있습니다.

동일한 볼륨의 모든 파일에 대해 단일 열 저장소가 있습니다. 이 열 저장소는 매우 커질 수 있습니다. “가장 쿨한” 항목 수만 검색해야 하는 경우 -Limit와 숫자를 사용하고 하위 경로 및 볼륨 루트로도 필터링하는 것이 좋습니다.

- PowerShell 모듈 가져오기:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- 사용 가능한 볼륨 공간: 사용 가능한 볼륨 공간 정책을 사용하여 파일을 계층화할 순서 가져오기:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- 날짜 정책: 날짜 정책을 사용하여 파일을 계층화할 순서 가져오기:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- 특정 파일에 대한 열 저장소 정보 찾기:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- 마지막 액세스 시간을 기준으로 내림차순으로 모든 파일 표시:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- PowerShell을 통한 백그라운드 회수 또는 요청 시 회수로 계층화된 파일을 회수할 순서 표시:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>파일 또는 디렉터리를 강제로 계층화하는 방법

> [!NOTE]
> 계층화할 디렉터리를 선택하면 현재 디렉터리에 있는 파일만 계층화됩니다. 해당 시간 이후에 만들어진 파일은 자동으로 계층화되지 않습니다.

클라우드 계층화 기능이 활성화된 경우 클라우드 계층화는 클라우드 엔드포인트에 지정된 사용 가능한 볼륨 공간 비율에 맞게 마지막 액세스 및 수정 시간을 기준으로 파일을 자동으로 계층화합니다. 그러나 경우에 따라 파일을 강제로 계층화하려는 경우도 있을 수 있습니다. 장시간 다시 사용하지 않으려는 큰 파일을 저장하고 다른 파일 및 폴더에 사용하기 위해 볼륨 공간을 확보하려는 경우에 이러한 강제 계층화가 유용할 수 있습니다. 다음 PowerShell 명령을 사용하여 강제로 계층화할 수 있습니다.

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>계층화된 파일을 디스크로 회수하는 방법

디스크로 파일을 회수하는 가장 쉬운 방법은 파일을 여는 것입니다. Azure 파일 동기화 파일 시스템 필터(StorageSync.sys)는 사용자의 별다른 작업 없이도 Azure 파일 공유에서 파일을 원활하게 다운로드합니다. 부분적으로 읽거나 스트리밍할 수 있는 파일 형식(예: 멀티미디어 또는 .zip 파일)의 경우 파일 열기만으로 전체 파일이 다운로드되지는 않습니다.

파일이 로컬 디스크에 완전히 다운로드되도록 하려면 PowerShell을 사용하여 파일을 강제로 완전히 회수해야 합니다. 이 옵션은 여러 파일을 한 번에 회수하려는 경우(예: 폴더의 모든 파일)에도 유용할 수 있습니다. Azure 파일 동기화가 설치되어 있는 서버 노드로 PowerShell 세션을 열고 다음 PowerShell 명령을 실행합니다.
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
선택적 매개 변수:
* `-Order CloudTieringPolicy`는 가장 최근에 수정하거나 액세스한 파일을 먼저 회수하며 현재 계층화 정책에서 허용됩니다. 
    * 사용 가능한 볼륨 공간 정책을 구성한 경우 파일은 사용 가능한 볼륨 공간 정책 설정에 도달할 때까지 회수됩니다. 예를 들어 사용 가능한 볼륨 정책 설정이 20%인 경우 사용 가능한 볼륨 공간이 20%에 도달하면 회수가 중지됩니다.  
    * 사용 가능한 볼륨 공간 및 날짜 정책을 구성한 경우 파일은 사용 가능한 볼륨 공간 또는 날짜 정책 설정에 도달할 때까지 회수됩니다. 예를 들어 사용 가능한 볼륨 정책 설정이 20%이고 날짜 정책이 7일인 경우 사용 가능한 볼륨 공간이 20%에 도달하거나 7일 이내에 액세스하거나 수정한 모든 파일이 로컬이면 회수가 중지됩니다.
* `-ThreadCount`는 병렬로 회수할 수 있는 파일 수를 결정합니다.
* `-PerFileRetryCount`는 현재 차단된 파일에 대해 회수가 시도되는 빈도를 결정합니다.
* `-PerFileRetryDelaySeconds`는 회수 재시도 사이의 간격(초)을 결정하며 항상 위의 매개 변수와 함께 사용해야 합니다.

예제:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- 서버를 호스트하는 로컬 볼륨에 모든 계층화된 데이터를 기억할 충분한 사용 가능한 공간이 없으면 `Invoke-StorageSyncFileRecall` cmdlet이 실패합니다.  

> [!Note]
> 계층화된 파일을 회수하려면 네트워크 대역폭이 1Mbps 이상이어야 합니다. 네트워크 대역폭이 1Mbps 미만이면 시간 제한 오류로 인해 파일을 회수하지 못할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Files에 대한 FAQ(질문과 대답)](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
