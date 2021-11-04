---
title: Azure 파일 동기화 문제 해결 | Microsoft Docs
description: Windows Server를 Azure 파일 공유의 빠른 캐시로 변환하는 데 사용할 수 있는 Azure 파일 동기화의 배포에서 일반적인 문제를 해결합니다.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 11/2/2021
ms.author: jeffpatt
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3069ffa1fc7e32d2ac683ecbc90f14f39625ddf4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424820"
---
# <a name="troubleshoot-azure-file-sync"></a>Azure 파일 동기화 문제 해결
Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서는 Azure 파일 동기화 배포 시 발생할 수 있는 문제를 해결하는 데 도움을 주기 위해 작성되었습니다. 또한 문제를 좀 더 자세히 조사해야 할 경우 시스템에서 중요한 로그를 수집하는 방법을 설명합니다. 질문에 대한 답을 찾지 못한 경우 다음 채널을 통해 (제시된 채널 순서에 따라) 문의할 수 있습니다.

- [Microsoft Q&Azure Files 대한 질문 페이지입니다.](/answers/products/azure?product=storage)
- Microsoft 지원 새 지원 요청을 만들려면 Azure Portal의 **도움말** 탭에서 **도움말 + 지원** 단추를 선택한 다음 **새 지원 요청** 을 선택합니다.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>서버의 Azure 파일 동기화에 문제가 발생했습니다(동기화, 클라우드 계층화 등). 서버 엔드포인트를 제거하고 다시 만들어야 하나요?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>에이전트 설치 및 서버 등록
<a id="agent-installation-failures"></a>**에이전트 설치 오류 해결**  
Azure 파일 동기화 에이전트 설치가 실패할 경우 관리자 권한 명령 프롬프트에서 다음 명령을 실행하여 에이전트 설치 동안 로깅을 켭니다.

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

installer.log를 검토하여 설치 실패의 원인을 확인합니다.

<a id="agent-installation-gpo"></a>**오류로 인해 에이전트 설치 실패: 스토리지 동기화 에이전트 설치 마법사가 오류로 인해 중간에 종료됨**

에이전트 설치 로그에 다음 오류가 기록됩니다.

```
CAQuietExec64:  + CategoryInfo          : SecurityError: (:) , PSSecurityException
CAQuietExec64:  + FullyQualifiedErrorId : UnauthorizedAccess
CAQuietExec64:  Error 0x80070001: Command line returned an error.
```

이 문제는 [PowerShell 실행 정책](/powershell/module/microsoft.powershell.core/about/about_execution_policies#use-group-policy-to-manage-execution-policy)이 그룹 정책을 사용하여 구성되고 정책 설정이 “서명한 스크립트만 허용”인 경우에 발생합니다. Azure 파일 동기화 에이전트에 포함된 모든 스크립트가 서명됩니다. 설치 관리자가 실행 정책 무시 설정을 사용하여 스크립트 실행을 수행하고 있기 때문에 Azure 파일 동기화 에이전트 설치가 실패합니다.

이 문제를 해결하려면 서버에서 [스크립트 실행 켜기](/powershell/module/microsoft.powershell.core/about/about_execution_policies#use-group-policy-to-manage-execution-policy) 그룹 정책 설정을 일시적으로 사용하지 않도록 설정합니다. 에이전트 설치가 완료되면 그룹 정책 설정을 다시 사용하도록 설정할 수 있습니다.

<a id="agent-installation-on-DC"></a>**Active Directory 도메인 컨트롤러에 에이전트 설치 실패**  
PDC 역할 소유자가 Windows Server 2008 R2 이하 OS 버전에 있는 Active Directory 도메인 컨트롤러에 동기화 에이전트를 설치하려고 하면 동기화 에이전트를 설치할 수 없는 문제가 발생할 수 있습니다.

이 문제를 해결하려면 PDC 역할을 Windows Server 2012 R2 이상을 실행 중인 다른 도메인 컨트롤러로 전송한 다음, 동기화를 설치합니다.

<a id="parameter-is-incorrect"></a>**다음 오류로 인해 Windows Server 2012 R2의 볼륨에 액세스하지 못합니다. 매개 변수가 올바르지 않습니다.**  
Windows Server 2012 R2에서 서버 엔드포인트를 만든 후 볼륨에 액세스할 때 다음 오류가 발생합니다.

드라이버 문자 :\에 액세스할 수 없습니다.  
매개 변수가 올바르지 않습니다.

이 문제를 해결하려면 [KB2919355](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014)를 설치하고 서버를 다시 시작합니다. 최신 업데이트가 이미 설치되어 있기 때문에 이 업데이트가 설치되지 않으면 Windows 업데이트로 이동하여 Windows Server 2012 R2용 최신 업데이트를 설치한 다음, 서버를 다시 시작합니다.

<a id="server-registration-missing-subscriptions"></a>**서버 등록에 모든 Azure 구독이 나열되지 않음**  
ServerRegistration.exe를 사용하여 서버를 등록하는 경우 Azure 구독 드롭다운을 클릭하면 구독이 누락됩니다.

이 문제는 ServerRegistration.exe가 처음 5개의 Azure AD 테넌트에서만 구독을 검색하기 때문에 발생합니다. 

서버에서 서버 등록 테넌트 한도를 늘리려면 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync에서 ServerRegistrationTenantLimit라는 DWORD 값을 5보다 큰 값으로 만듭니다.

다음 PowerShell 명령을 사용하여 서버를 등록하면 이 문제도 해결할 수 있습니다.

```powershell
Connect-AzAccount -Subscription "<guid>" -Tenant "<guid>"
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

<a id="server-registration-prerequisites"></a>**서버 등록에 다음 메시지가 표시됩니다. "필수 구성 요소가 누락되었습니다."**  
이 메시지는 Az 또는 AzureRM PowerShell 모듈이 PowerShell 5.1에 설치되지 않은 경우 나타납니다. 

> [!Note]  
> ServerRegistration.exe는 PowerShell 6.x를 지원하지 않습니다. PowerShell 6.x에서 AzStorageSyncServer cmdlet을 사용하여 서버를 등록할 수 있습니다.

PowerShell 5.1에 Az 또는 AzureRM 모듈을 설치하려면 다음 단계를 수행합니다.

1. 관리자 권한 명령 프롬프트에서 **powershell** 을 입력하고 Enter 키를 누릅니다.
2. 설명서에 따라 최신 Az 또는 AzureRM 모듈을 설치합니다.
    - [Az 모듈(.NET 4.7.2 필요)](/powershell/azure/install-az-ps)
    - [AzureRM 모듈](https://go.microsoft.com/fwlink/?linkid=856959)
3. ServerRegistration.exe를 실행하고 마법사를 완료하여 서버를 Storage 동기화 서비스에 등록합니다.

<a id="server-already-registered"></a>**서버 등록에 “서버가 이미 등록되었습니다.”라는 메시지가 표시됩니다.** 

![“This server is already registered”(서버가 이미 등록되었습니다.) 오류 메시지가 있는 서버 등록 대화 상자의 스크린샷](media/storage-sync-files-troubleshoot/server-registration-1.png)

서버가 이전에 Storage 동기화 서비스에 등록된 경우 이 메시지가 표시됩니다. 현재 Storage 동기화 서비스에서 서버 등록을 취소하고 새 Storage 동기화 서비스에 등록하려면 [Azure 파일 동기화로 서버 등록 해제](file-sync-server-registration.md#unregister-the-server-with-storage-sync-service)에 설명된 단계를 완료하세요.

스토리지 동기화 서비스의 **등록된 서버** 아래에서 서버가 나열되지 않으면, 등록을 취소하려는 서버에서 다음 PowerShell 명령을 실행합니다.

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> 서버가 클러스터에 속할 경우 선택적 *Reset-StorageSyncServer -CleanClusterRegistration* 매개 변수를 사용하여 클러스터 등록도 제거합니다.

<a id="web-site-not-trusted"></a>**서버를 등록할 때 "웹 사이트를 신뢰할 수 없습니다." 응답이 여러 번 표시됩니다. 그 이유는 무엇일까요?**  
이 문제는 서버 등록 중에 **Internet Explorer 보안 강화** 정책이 사용되도록 설정되었기 때문에 발생합니다. **Internet Explorer 보안 강화** 정책을 사용하지 않도록 적절히 설정하는 방법에 대한 자세한 내용은 [Azure 파일 동기화와 함께 사용하도록 Windows Server 준비](file-sync-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) 및 [Azure 파일 동기화를 배포하는 방법](file-sync-deployment-guide.md)을 참조하세요.

<a id="server-registration-missing"></a>**Azure Portal의 등록된 서버 아래에서 서버가 나열되지 않습니다.**  
스토리지 동기화 서비스에 대한 **등록된 서버** 아래에서 서버가 나열되지 않으면 다음을 수행합니다.
1. 등록하려는 서버에 로그인합니다.
2. 파일 탐색기를 열고 Storage 동기화 에이전트 설치 디렉터리(기본 위치 C:\Program Files\Azure\StorageSyncAgent) 로 이동합니다. 
3. ServerRegistration.exe를 실행하고 마법사를 완료하여 서버를 Storage 동기화 서비스에 등록합니다.

## <a name="sync-group-management"></a>동기화 그룹 관리

### <a name="cloud-endpoint-creation-errors"></a>클라우드 엔드포인트 만들기 오류

<a id="cloud-endpoint-using-share"></a>**다음 오류로 인해 클라우드 엔드포인트 생성 실패: “지정된 Azure FileShare가 이미 다른 클라우드 엔드포인트에서 사용되고 있습니다.”**  
Azure 파일 공유가 다른 클라우드 엔드포인트에서 이미 사용되고 있으면 이 오류가 발생합니다. 

이 메시지가 표시되고 Azure 파일 공유가 현재 클라우드 엔드포인트에서 사용되고 있지 않으면 다음 단계를 수행하여 Azure 파일 공유에서 Azure 파일 동기화 메타데이터를 지웁니다.

> [!Warning]  
> 클라우드 엔드포인트에서 현재 사용 중인 Azure 파일 공유에서 메타데이터를 삭제하면 Azure 파일 동기화 작업이 실패합니다. 그런 다음 이 파일 공유를 다른 동기화 그룹의 동기화에 사용하면 이전 동기화 그룹에 있는 파일의 데이터가 거의 확실히 손실됩니다.

1. Azure Portal에서 Azure 파일 공유로 이동합니다.  
2. Azure 파일 공유를 마우스 오른쪽 단추로 클릭하고 **메타데이터 편집** 을 선택합니다.
3. **SyncService** 를 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택합니다.

<a id="cloud-endpoint-authfailed"></a>**다음 오류로 인해 클라우드 엔드포인트 생성 실패: “AuthorizationFailed”**  
이 오류는 사용자 계정에 클라우드 엔드포인트 생성을 위한 권한이 없는 경우 발생합니다. 

클라우드 엔드포인트를 만들려면 사용자 계정에 다음과 같은 Microsoft 인증 권한이 있어야 합니다.  
* 읽기: 역할 정의 가져오기
* 쓰기: 사용자 지정 역할 정의 만들기 또는 업데이트
* 읽기: 역할 할당 가져오기
* 쓰기: 역할 할당 만들기

다음과 같은 기본 제공 역할은 필요한 Microsoft 인증 권한을 갖고 있습니다.  
* 소유자
* 사용자 액세스 관리자

사용자의 계정 역할에 필요한 사용 권한이 있는지 확인하려면  
1. Azure Portal에서 **리소스 그룹** 을 선택합니다.
2. 스토리지 계정이 있는 리소스 그룹을 선택하고 **IAM(Access Control)** 를 선택합니다.
3. **역할 할당** 탭을 선택합니다.
4. 사용자 계정에 대한 **역할**(예: 소유자 또는 Contributor)을 선택합니다.
5. **리소스 공급자** 목록에서 **Microsoft 인증** 을 선택합니다. 
    * **역할 할당** 에 **읽기** 권한과 **쓰기** 권한이 있어야 합니다.
    * **역할 정의** 에 **읽기**  권한과 **쓰기** 권한이 있어야 합니다.

### <a name="server-endpoint-creation-and-deletion-errors"></a>서버 엔드포인트 만들기 및 삭제 오류

<a id="-2134375898"></a>**다음 오류로 인해 서버 엔드포인트 생성 실패: "MgmtServerJobFailed"(오류 코드: -2134375898 또는 0x80c80226)**  
이 오류는 서버 엔드포인트 경로가 시스템 볼륨에 있고 클라우드 계층화를 사용하도록 설정한 경우 발생합니다. 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.

<a id="-2147024894"></a>**다음 오류로 인해 서버 엔드포인트 생성 실패: "MgmtServerJobFailed"(오류 코드: -2147024894 or 0x80070002)**  
지정된 서버 엔드포인트 경로가 잘못된 경우 이 오류가 발생합니다. 지정된 서버 엔드포인트 경로가 로컬로 연결된 NTFS 볼륨인지 확인합니다. 참고로, Azure 파일 동기화는 매핑된 드라이브를 서버 엔드포인트 경로로 지원하지 않습니다.

<a id="-2134375640"></a>**다음 오류로 인해 서버 엔드포인트 생성 실패: "MgmtServerJobFailed"(오류 코드: -2134375640 또는 0x80c80328)**  
지정된 서버 엔드포인트 경로가 NTFS 볼륨이 아닌 경우 이 오류가 발생합니다. 지정된 서버 엔드포인트 경로가 로컬로 연결된 NTFS 볼륨인지 확인합니다. 참고로, Azure 파일 동기화는 매핑된 드라이브를 서버 엔드포인트 경로로 지원하지 않습니다.

<a id="-2134347507"></a>**다음 오류로 인해 서버 엔드포인트 생성 실패: "MgmtServerJobFailed"(오류 코드: -2134347507 또는 0x80c8710d)**  
이 오류는 Azure 파일 동기화가 압축된 System Volume Information 폴더가 있는 볼륨의 서버 엔드포인트를 지원하지 않기 때문에 발생합니다. 이 문제를 해결하려면 System Volume Information 폴더의 압축을 해제합니다. System Volume Information 폴더가 볼륨에서 유일하게 압축된 폴더인 경우 다음 단계를 수행합니다.

1. [PsExec](/sysinternals/downloads/psexec) 도구를 다운로드합니다.
2. 관리자 권한 명령 프롬프트에서 다음 명령을 실행하여 시스템 계정으로 실행되는 명령 프롬프트를 시작합니다. **PsExec.exe -i -s -d cmd**
3. 시스템 계정으로 실행되는 명령 프롬프트에서 다음 명령을 입력하고 Enter 키를 누릅니다.   
    **cd /d "drive letter:\System Volume Information"**  
    **compact /u /s**

<a id="-2134376345"></a>**다음 오류로 인해 서버 엔드포인트 생성 실패: "MgmtServerJobFailed"(오류 코드: -2134376345 또는 0x80C80067)**  
서버당 서버 엔드포인트 수 제한에 도달하면 이 오류가 발생합니다. Azure 파일 동기화는 현재 서버당 최대 30개의 서버 엔드포인트를 지원합니다. 자세한 내용은 [Azure 파일 동기화 크기 조정 목표](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-scale-targets)를 참조하세요.

<a id="-2134376427"></a>**다음 오류로 인해 서버 엔드포인트 생성 실패: "MgmtServerJobFailed"(오류 코드: -2134376427 또는 0x80c80015)**  
이 오류는 다른 서버 엔드포인트에서 지정된 서버 엔드포인트 경로를 이미 동기화하고 있는 경우에 발생합니다. Azure 파일 동기화는 동일한 디렉터리 또는 볼륨을 동기화하는 여러 서버 엔드포인트를 지원하지 않습니다.

<a id="-2160590967"></a>**다음 오류로 인해 서버 엔드포인트 생성 실패: "MgmtServerJobFailed"(오류 코드: -2160590967 또는 0x80c80077)**  
서버 엔드포인트 경로에 분리되고 계층화된 파일이 포함되어 있는 경우 이 오류가 발생합니다. 서버 엔드포인트가 최근에 제거된 경우 분리되고 계층화된 파일 정리가 완료될 때까지 기다립니다. 분리된 계층화된 파일 정리가 시작되면 원격 분석 이벤트 로그에 이벤트 ID 6662가 기록됩니다. 분리된 계층화된 파일 정리가 완료되고 경로를 사용하여 서버 엔드포인트를 다시 만들 수 있는 경우 이벤트 ID 6661이 기록됩니다. 계층화된 파일 정리가 완료된 후 서버 엔드포인트 만들기가 실패하거나 이벤트 로그 롤오버로 인해 원격 분석 이벤트 로그에서 이벤트 ID 6661을 찾을 수 없는 경우 [서버 엔드포인트를 삭제한 후 서버에서 계층화된 파일에 액세스할 수 없음](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) 섹션에 설명된 단계를 수행하여 분리된 계층화된 파일을 제거합니다.

<a id="-2134347757"></a>**다음 오류로 인해 서버 엔드포인트 삭제 실패: "MgmtServerJobExpired"(오류 코드: -2134347757 또는 0x80c87013)**  
이 오류는 서버가 오프라인 상태이거나 네트워크에 연결되어 있지 않은 경우 발생합니다. 서버를 더 이상 사용할 수 없는 경우 Azure Portal에서 서버의 등록을 취소하여 서버 엔드포인트를 삭제합니다. 서버 엔드포인트를 삭제하려면 [Azure 파일 동기화에서 서버 등록 취소](file-sync-server-registration.md#unregister-the-server-with-storage-sync-service)에 설명된 단계를 따릅니다.

### <a name="server-endpoint-health"></a>서버 엔드포인트 상태

<a id="server-endpoint-provisioningfailed"></a>**서버 엔드포인트 속성 페이지를 열거나 클라우드 계층화 정책을 업데이트할 수 없습니다.**  
이 문제는 서버 엔드포인트에서 관리 작업이 실패하는 경우 발생합니다. Azure Portal에서 서버 엔드포인트 속성 페이지가 열리지 않는 경우, 서버에서 PowerShell 명령을 사용하여 서버 엔드포인트를 업데이트하면 이 문제가 해결될 수 있습니다. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**서버 엔드포인트가 "활동 없음" 또는 "보류 중" 상태이며, 등록된 서버 블레이드의 서버 상태가 "오프라인으로 나타남"입니다.**  

이 문제는 스토리지 동기화 모니터 프로세스(AzureStorageSyncMonitor.exe)가 실행되고 있지 않거나 서버가 Azure 파일 동기화 서비스에 액세스할 수 없는 경우 발생할 수 있습니다.

포털에서 "오프라인으로 나타남"으로 표시되는 서버의 원격 분석 이벤트 로그(이벤트 뷰어의 Applications and Services\Microsoft\FileSync\Agent에 있음)에서 이벤트 ID 9301을 확인하여 서버가 Azure 파일 동기화 서비스에 액세스할 수 없는 이유를 확인합니다. 

- **GetNextJob completed with status: 0** 이 기록되는 경우 서버는 Azure 파일 동기화 서비스와 통신할 수 있는 것입니다. 
    - 서버에서 작업 관리자를 열고 Storage 동기화 모니터링 (AzureStorageSyncMonitor.exe) 프로세스가 실행 중인지 확인합니다. 프로세스가 실행되지 않으면 먼저 서버를 다시 시작합니다. 서버를 다시 시작해도 문제가 해결되지 않으면 최신 Azure 파일 동기화 [에이전트 버전](file-sync-release-notes.md)으로 업그레이드합니다. 

- **GetNextJob completed with status: -2134347756** 이 로그되는 경우 서버는 방화벽, 프록시, TLS 암호화 도구 모음 순서 구성 때문에 Azure 파일 동기화 서비스와 통신할 수 없습니다. 
    - 서버가 방화벽 뒤에 있는 경우 포트 443 아웃 바운드가 허용되는지 확인합니다. 방화벽이 트래픽을 특정 도메인으로 제한하는 경우 방화벽 [설명서](file-sync-firewall-and-proxy.md#firewall)에 나열된 도메인에 액세스할 수 있는지 확인합니다.
    - 서버가 프록시 뒤에 있는 경우 프록시 [설명서](file-sync-firewall-and-proxy.md#proxy)에 있는 단계에 따라 머신 전체 또는 앱 별 프록시 설정을 구성합니다.
    - Test-StorageSyncNetworkConnectivity cmdlet을 사용하여 서비스 엔드포인트에 대한 네트워크 연결을 확인합니다. 자세한 내용은 [서비스 엔드포인트에 대한 네트워크 연결 테스트](file-sync-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints)를 참조하세요.
    - TLS 암호화 도구 모음 순서가 서버에 구성된 경우 그룹 정책 또는 TLS cmdlet을 사용하여 암호화 도구 모음을 추가할 수 있습니다.
        - 그룹 정책을 사용하려면 [그룹 정책을 사용하여 TLS 암호화 도구 모음 순서 구성](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-group-policy)을 참조하세요.
        - TLS cmdlet을 사용하려면 [TLS PowerShell Cmdlet을 사용하여 TLS 암호화 도구 모음 순서 구성](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-tls-powershell-cmdlets)을 참조하세요.
    
        Azure 파일 동기화는 현재 TLS 1.2 프로토콜용으로 다음과 같은 암호화 도구 모음을 지원합니다.  
        - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256  

- **GetNextJob completed with status: -2134347764** 가 기록되는 경우 서버는 인증서 만료 또는 삭제 때문에 Azure 파일 동기화 서비스와 통신할 수 없는 것입니다.  
    - 서버에서 다음 PowerShell 명령을 실행하여 인증에 사용되는 인증서를 다시 설정합니다.
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**서버 엔드포인트의 상태가 "활동 없음"이며, 등록된 서버 블레이드의 서버 상태가 "온라인"입니다.**  

서버 엔드포인트 상태가 "활동 없음"이면 서버 엔드포인트에서 지난 2시간 동안 동기화 활동을 기록하지 않은 것입니다.

서버의 현재 동기화 활동을 확인하려면 [현재 동기화 세션의 진행률을 모니터링 하려면 어떻게 해야 하나요?](#how-do-i-monitor-the-progress-of-a-current-sync-session)를 참조하세요.

서버 엔드포인트가 버그로 인해 또는 시스템 리소스가 부족하여 몇 시간 동안 동기화 작업을 기록하지 못할 수 있습니다. 최신 Azure 파일 동기화 [에이전트 버전](file-sync-release-notes.md)이 설치되어 있는지 확인합니다. 문제가 지속되면 지원 요청을 개설하세요.

> [!Note]  
> 등록된 서버 블레이드의 서버 상태가 "오프라인으로 나타남"인 경우 [서버 엔드포인트가 "활동 없음" 또는 "보류 중" 상태이며, 등록된 서버 블레이드의 서버 상태가 "오프라인으로 나타남"입니다.](#server-endpoint-noactivity) 섹션에서 설명하는 단계를 수행하세요.

## <a name="sync"></a>동기화
<a id="afs-change-detection"></a>**SMB 또는 포털을 통해 Azure 파일 공유에 직접 파일을 만든 경우 해당 파일이 동기화 그룹의 서버에 동기화되는 데 얼마나 걸리나요?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**서버 엔드포인트 상태가 여러 시간 동안 보류 중 상태**  
클라우드 엔드포인트를 만들고 데이터가 포함된 Azure 파일 공유를 사용하는 경우 이 문제가 발생할 수 있습니다. Azure 파일 공유의 변경 내용을 검색하는 변경 내용 열거형 작업이 완료되어야만 클라우드 엔드포인트와 서버 엔드포인트 간에 파일을 동기화할 수 있습니다. 작업 완료 시간은 Azure 파일 공유의 네임스페이스 크기에 따라 달라집니다. 변경 열거형 작업이 완료되면 서버 엔드포인트 상태를 업데이트해야 합니다.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>동기화 상태를 모니터링하려면 어떻게 해야 하나요?
# <a name="portal"></a>[포털](#tab/portal1)
각 동기화 그룹 내에서 개별 서버 엔드포인트로 드릴다운하여 마지막으로 완료된 동기화 세션의 상태를 볼 수 있습니다. 녹색 상태 열과 동기화 상태가 아닌 파일 값 0은 동기화가 예상대로 작동함을 나타냅니다. 이와 다른 경우 아래에서 일반적인 동기화 오류 목록 및 동기화되지 않는 파일 처리 방법을 살펴보세요. 

![Azure Portal 스크린샷](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Server](#tab/server)
서버의 원격 분석 로그로 이동합니다. `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`의 이벤트 뷰어에서 찾을 수 있습니다. 이벤트 9102는 완료된 동기화 세션에 해당합니다. 동기화의 마지막 상태를 보려면 ID가 9102인 최신 이벤트를 살펴보세요. SyncDirection은 이 세션이 업로드인지 아니면 다운로드인지 알려줍니다. `HResult`가 0이면 동기화 세션이 성공한 것입니다. `HResult`가 0 이외의 값이면 동기화 중에 오류가 발생했다는 의미입니다. 일반적인 오류 목록은 아래를 참조하세요. PerItemErrorCount가 0보다 크면 일부 파일 또는 폴더가 올바르게 동기화되지 않은 것입니다. `HResult`는 0이지만 PerItemErrorCount가 0보다 클 수 있습니다.

아래는 성공적인 업로드의 예입니다. 간단하게 설명하기 위해, 각 9102 이벤트에 포함된 값 중 일부만 아래에 나열되어 있습니다. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

반대로, 실패한 업로드는 다음과 비슷합니다.

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

동기화 세션이 전체적으로 실패하거나 PerItemErrorCount의 값이 0이 아니지만, 여전히 동기화가 계속 진행되고 일부 파일이 성공적으로 동기화되는 경우가 가끔 있습니다. *Applied* 필드(AppliedFileCount, AppliedDirCount, AppliedTombstoneCount 및 AppliedSizeBytes)를 살펴보면 진행 상황을 확인할 수 있습니다. 이러한 필드는 세션이 얼마나 성공적으로 진행되고 있는지 설명합니다. 여러 동기화 세션이 연속으로 실패하는데 *Applied* 횟수는 증가하는 경우 지원 티켓을 개설하기 전에 동기화될 때까지 좀 더 기다렸다가 다시 시도해 보세요.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>현재 동기화 세션의 진행률을 모니터링 하려면 어떻게 해야 하나요?
# <a name="portal"></a>[포털](#tab/portal1)
동기화 그룹 내에서 궁금한 서버 엔드포인트로 이동하고, 동기화 작업 섹션을 살펴보고 현재 세션에서 업로드 또는 다운로드된 파일 수를 확인합니다. 이 상태는 약 5분 지연되며, 동기화 세션이 이 시간 내에 완료될 정도로 작은 경우에는 포털에서 보고되지 않을 수 있습니다. 

# <a name="server"></a>[Server](#tab/server)
원격 분석 로그온 서버에서 가장 최근의 9302 이벤트를 조회합니다(이벤트 뷰어에서 Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry로 이동). 이 이벤트는 현재 동기화 세션의 상태를 나타냅니다. TotalItemCount는 동기화할 파일 수를 나타내고, AppliedItemCount는 지금까지 동기화된 파일 수를 나타내고, PerItemErrorCount는 동기화에 실패한 파일 수를 나타냅니다(처리 방법은 아래를 참조).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>서버가 서로 동기화되었는지 확인하는 방법이 있나요?
# <a name="portal"></a>[포털](#tab/portal1)
특정 동기화 그룹의 각 서버에 대해 다음 사항을 확인합니다.
- 업로드 및 다운로드에 대한 마지막 동기화 시도 타임스탬프가 최근입니다.
- 업로드 및 다운로드의 상태가 모두 녹색입니다.
- 동기화 작업 필드에 남아 있는 동기화할 파일이 거의 없거나 하나도 없습니다.
- 동기화 상태가 아닌 파일 필드의 값이 업로드 및 다운로드 모두 0입니다.

# <a name="server"></a>[Server](#tab/server)
완료된 동기화 세션을 살펴보세요. 각 서버에 대한 원격 분석 이벤트 로그에 9102 이벤트로 표시됩니다(이벤트 뷰어에서 `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`로 이동). 

1. 특정 서버에서 최신 업로드 및 다운로드 세션이 완료되었는지 확인합니다. 이렇게 하려면 `HResult` 및 PerItemErrorCount가 업로드와 다운로드 모두 0인지 확인합니다(SyncDirection 필드는 특정 세션이 업로드 세션인지 아니면 다운로드 세션인지 나타냄). 최근에 완료된 동기화 세션이 보이지 않는 경우 현재 동기화 세션이 진행 중일 가능성이 높으며, 대량의 데이터를 방금 추가 또는 수정한 경우에 예상되는 동작입니다.
2. 서버가 클라우드를 통해 완전히 최신 상태가 되고 어느 방향으로든 동기화할 변경 내용이 없으면 빈 동기화 세션이 표시됩니다. 이를 나타내는 것은 업로드 및 다운로드 이벤트로, 모든 동기화* 필드(SyncFileCount, SyncDirCount, SyncTombstoneCount 및 SyncSizeBytes)가 0이면 동기화할 내용이 하나도 없다는 뜻입니다. 변동률이 높은 서버에는 항상 동기화할 내용이 있으므로 빈 동기화 세션이 발생하지 않을 수 있습니다. 동기화 작업이 없는 경우 30분마다 발생해야 합니다. 
3. 모든 서버가 클라우드를 통해 최신 상태가 되면, 다시 말해서 최신 업로드 및 다운로드 세션이 빈 동기화 세션이면 시스템 전체가 동기화되었다고 확실하게 말할 수 있습니다. 
    
Azure 파일 공유에서 직접 변경하는 경우 Azure 파일 동기화는 24시간마다 발생하는 변경 열거형이 실행될 때까지 이러한 변경 내용을 감지하지 못합니다. Azure 파일 공유에서 최근에 직접 변경한 내용이 없으면 서버는 클라우드를 통해 최신 상태가 되었다고 말할 수 있습니다.

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>동기화되지 않는 파일 또는 폴더가 있는지 확인하려면 어떻게 하나요?
서버의 PerItemErrorCount 또는 포털의 동기화 상태가 아닌 파일 수가 특정 동기화 세션에서 0보다 크면 일부 항목이 동기화에 실패한다는 뜻입니다. 파일 및 폴더에 동기화를 막는 특징이 있을 수 있습니다. 이러한 특징은 영구적일 수 있으며, 파일 또는 폴더 이름에서 지원되지 않는 문자를 제거하는 것처럼 동기화를 다시 시작하기 위한 명시적 조치가 필요합니다. 또한 이러한 특징이 일시적일 수 있습니다. 즉, 파일 또는 폴더가 자동으로 동기화를 다시 시작할 수 있습니다. 예를 들어 핸들이 열려 있는 파일은 파일이 닫히면 자동으로 동기화를 다시 시작합니다. Azure 파일 동기화 엔진이 이러한 문제를 발견하면 오류 로그가 생성되며, 이 오류 로그를 구문 분석하면 현재 올바르게 동기화되지 않는 항목이 나열됩니다.

이러한 오류를 살펴보려면 **FileSyncErrorsReport.ps1** PowerShell 스크립트(Azure 파일 동기화 에이전트의 에이전트 설치 디렉터리에 있음)를 실행하여 열려 있는 핸들, 지원되지 않는 문자 또는 기타 문제로 동기화에 실패한 파일을 식별합니다. ItemPath 필드는 루트 동기화 디렉터리를 기준으로 파일의 위치를 알려줍니다. 아래에서 수정 단계에 대한 일반적인 동기화 오류 목록을 참조하세요.

> [!Note]  
> FileSyncErrorsReport.ps1 스크립트가 "파일 오류가 없음"을 반환하거나 동기화 그룹에 대한 항목당 오류가 나열되지 않으면 원인은 다음 중 하나입니다.
>
>- 원인 1: 마지막으로 완료된 동기화 세션에 항목당 오류가 없습니다. 포털이 곧 업데이트되어 동기화 상태가 아닌 파일을 0개로 표시할 것입니다. 기본적으로 FileSyncErrorsReport.ps1 스크립트는 마지막으로 완료된 동기화 세션에 대한 항목별 오류만 표시합니다. 모든 동기화 세션에 대한 항목별 오류를 보려면 -ReportAllErrors 매개 변수를 사용합니다.
>    - 원격 분석 이벤트 로그에서 최신 [이벤트 ID 9102](?tabs=server%252cazure-portal#broken-sync)를 확인하여 PerItemErrorCount가 0인지 확인합니다. 
>
>- 원인 2: 항목별 오류가 너무 많아 서버의 ItemResults 이벤트 로그가 래핑되었기 때문에 더 이상 이벤트 로그에 이 동기화 그룹에 오류가 포함되지 않습니다.
>    - 이 문제를 방지하려면 ItemResults 이벤트 로그 크기를 늘립니다. ItemResults 이벤트 로그는 이벤트 뷰어의 "Applications and Services Logs\Microsoft\FileSync\Agent"에서 찾을 수 있습니다. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>파일/디렉터리별 동기화 오류 문제 해결
**ItemResults 로그 - 항목별 동기화 오류**  

| HRESULT | HRESULT(10진) | 오류 문자열 | 문제 | 재구성 |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | 서버에서 계층화된 파일에 액세스할 수 없습니다. 이 문제는 서버 엔드포인트를 삭제하기 전에 계층화된 파일이 회수되지 않은 경우에 발생합니다. | 이 문제를 해결하려면 [서버 엔드포인트를 삭제한 후 서버에서 계층화된 파일에 액세스할 수 없음](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)을 참조하세요. |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | 종속 폴더가 아직 동기화되지 않아 파일 또는 디렉터리 변경 내용을 동기화할 수 없습니다. 이 항목은 종속 변경 내용이 동기화된 후 동기화됩니다. | 아무 조치도 취할 필요가 없습니다. 이 오류가 며칠 동안 지속되면 FileSyncErrorsReport.ps1 PowerShell 스크립트를 사용하여 종속 폴더가 아직 동기화되지 않은 이유를 확인합니다. |
| 0x80C8028A | -2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | 종속 폴더가 아직 동기화되지 않아 파일 또는 디렉터리 변경 내용을 동기화할 수 없습니다. 이 항목은 종속 변경 내용이 동기화된 후 동기화됩니다. | 아무 조치도 취할 필요가 없습니다. 이 오류가 며칠 동안 지속되면 FileSyncErrorsReport.ps1 PowerShell 스크립트를 사용하여 종속 폴더가 아직 동기화되지 않은 이유를 확인합니다. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | 종속 폴더가 아직 동기화되지 않고 동기화 세션이 실패하여 파일 또는 디렉터리 변경 내용을 동기화할 수 없습니다. 이 항목은 종속 변경 내용이 동기화된 후 동기화됩니다. | 아무 조치도 취할 필요가 없습니다. 이 오류가 지속되면 동기화 세션 오류를 조사하세요. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | 파일 또는 디렉터리 이름이 잘못되었습니다. | 의심스러운 파일 또는 디렉터리 이름을 변경하세요. 자세한 내용은 [지원되지 않는 문자 처리](?tabs=portal1%252cazure-portal#handling-unsupported-characters)를 참조하세요. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | 파일 또는 디렉터리 이름이 잘못되었습니다. | 의심스러운 파일 또는 디렉터리 이름을 변경하세요. 자세한 내용은 [지원되지 않는 문자 처리](?tabs=portal1%252cazure-portal#handling-unsupported-characters)를 참조하세요. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | 파일이 사용 중이므로 동기화할 수 없습니다. 파일이 더 이상 사용되지 않을 때 동기화됩니다. | 아무 조치도 취할 필요가 없습니다. Azure 파일 동기화는 핸들이 열려 있는 파일을 동기화하기 위해 하루 한 번 서버에 임시 VSS 스냅샷을 만듭니다. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | 파일이 변경되었지만 아직 동기화에서 변경 내용을 발견하지 못했습니다. 이 변경 내용이 발견되면 동기화가 복구됩니다. | 아무 조치도 취할 필요가 없습니다. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | 파일이 삭제되었고 동기화가 변경 내용을 인식하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 변경 내용 검색으로 파일이 삭제된 것이 감지되면 동기화에서 이 오류 기록을 중지합니다. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | 대상에서 이미 항목이 삭제되었고 동기화가 변경 내용을 인식하지 못했기 때문에 파일 또는 디렉터리 삭제를 동기화할 수 없습니다. | 아무 조치도 취할 필요가 없습니다. 대상에서 변경 검색이 실행되고 동기화가 항목이 삭제되었음을 감지하면 동기화에서 이 오류 기록을 중지합니다. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | 파일 또는 디렉터리를 건너 뛰었지만 다음 동기화 세션에서 동기화됩니다. 항목을 다운로드할 때 이 오류가 보고되는 경우 파일 또는 디렉터리 이름이 잘못된 것일 수 있습니다. | 파일을 업로드할 때 이 오류가 보고되는 경우에는 아무 조치도 필요하지 않습니다. 파일을 다운로드할 때 오류가 보고되는 경우 해당 파일 또는 디렉터리의 이름을 바꿉니다. 자세한 내용은 [지원되지 않는 문자 처리](?tabs=portal1%252cazure-portal#handling-unsupported-characters)를 참조하세요. |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | 대상에 항목이 이미 존재하고 동기화가 변경 내용을 인식하지 못했기 때문에 파일 또는 디렉터리 만들기를 동기화할 수 없습니다. | 아무 조치도 취할 필요가 없습니다. 대상에서 변경 검색이 실행되고 동기화가 이 새 항목을 인식하면 동기화에서 이 오류 기록을 중지합니다. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Azure 파일 공유 제한에 도달하여 파일을 동기화할 수 없습니다. | 이 문제를 해결하려면 문제 해결 가이드의 [Azure 파일 공유 스토리지 용량 한도에 도달했습니다](?tabs=portal1%252cazure-portal#-2134351810) 섹션을 참조하세요. |
| 0x80c83008 | -2134364152 | ECS_E_CANNOT_CREATE_AZURE_STAGED_FILE | Azure 파일 공유 제한에 도달하여 파일을 동기화할 수 없습니다.  | 이 문제를 해결하려면 문제 해결 가이드의 [Azure 파일 공유 스토리지 용량 한도에 도달했습니다](?tabs=portal1%252cazure-portal#-2134351810) 섹션을 참조하세요. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | 파일이 지원되지 않는 솔루션(예: NTFS EFS)에 의해 암호화됩니다. | 파일 암호를 해독하고 지원되는 암호화 솔루션을 사용합니다. 지원 솔루션 목록은 계획 가이드의 [암호화](file-sync-planning.md#encryption) 섹션을 참조하세요. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | 파일이 DFS-R 읽기 전용 복제 폴더에 있습니다. | 파일이 DFS-R 읽기 전용 복제 폴더에 있습니다. Azure 파일 동기화는 DFS-R 읽기 전용 복제 폴더에서 서버 엔드포인트를 지원하지 않습니다. 자세한 내용은 [계획 지침](file-sync-planning.md#distributed-file-system-dfs)을 참조하세요. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | 파일이 삭제 보류 중 상태입니다. | 아무 조치도 취할 필요가 없습니다. 열린 파일 핸들이 닫히면 파일이 삭제됩니다. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | 스토리지 계정에 방화벽 및 가상 네트워크 설정이 사용하도록 설정되어 있고 서버에 스토리지 계정에 대한 액세스 권한이 없어 파일을 동기화할 수 없습니다. | 배포 가이드의 [방화벽 및 가상 네트워크 설정 구성](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) 섹션에 설명된 단계에 따라 서버 IP 주소 또는 가상 네트워크를 추가합니다. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | 보안 설명자 크기가 64KiB 한도를 초과하여 파일을 동기화할 수 없습니다. | 이 문제를 해결하려면 파일의 ACE(액세스 제어 항목)를 제거하여 보안 설명자 크기를 줄입니다. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | 예기치 않은 오류로 인해 파일을 동기화할 수 없습니다. | 오류가 몇 시간 동안 지속되면 지원 사례를 개설하세요. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | 파일이 사용 중이므로 동기화할 수 없습니다. 파일이 더 이상 사용되지 않을 때 동기화됩니다. | 아무 조치도 취할 필요가 없습니다. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | 동기화 중에 파일이 변경되었으므로 다시 동기화해야 합니다. | 아무 조치도 취할 필요가 없습니다. |
| 0x80070017 | -2147024873 | ERROR_CRC | CRC 오류로 인해 파일을 동기화할 수 없습니다. 이 오류는 서버 엔드포인트를 삭제하기 전에 계층화된 파일이 회수되지 않았거나 파일이 손상된 경우에 발생할 수 있습니다. | 이 문제를 해결하려면 [서버 엔드포인트를 삭제한 후 서버에서 계층화된 파일에 액세스할 수 없음](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)을 참조하여 분리된 계층화된 파일을 제거합니다. 분리된 계층화된 파일을 제거한 후에도 오류가 계속 발생하면 볼륨에서 [chkdsk](/windows-server/administration/windows-commands/chkdsk)를 실행합니다. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | 충돌 파일이 최대 수에 도달했기 때문에 파일을 동기화할 수 없습니다. Azure 파일 동기화는 파일당 100개의 충돌 파일을 지원합니다. 파일 충돌에 대해 자세히 알아보려면 Azure 파일 동기화 [FAQ](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#afs-conflict-resolution)를 참조하세요. | 이 문제를 해결하려면 충돌 파일 수를 줄입니다. 충돌 파일 수가 100 미만이면 파일이 동기화됩니다. |
| 0x80c8027d | -2134375811 | ECS_E_DIRECTORY_RENAME_FAILED | 디렉터리 내의 파일 또는 폴더에 열린 핸들이 있으므로 디렉터리 이름 변경을 동기화할 수 없습니다. | 아무 조치도 취할 필요가 없습니다. 디렉터리 내의 열려 있는 파일 핸들이 모두 닫히면 디렉터리 이름 변경이 동기화됩니다. |
| 0x800700de | -2147024674 | ERROR_BAD_FILE_TYPE | 서버의 계층화된 파일은 Azure 파일 공유에 더 이상 존재하지 않는 파일 버전을 참조하므로 액세스할 수 없습니다. | 이 문제는 계층화된 파일이 Windows Server의 백업에서 복원된 경우에 발생할 수 있습니다. 문제를 해결하려면 Azure 파일 공유의 스냅샷에서 파일을 복원합니다. |

#### <a name="handling-unsupported-characters"></a>지원되지 않는 처리 문자
지원되지 않는 문자로 인해 **FileSyncErrorsReport.ps1** PowerShell 스크립트에서 항목별 동기화 오류(오류 코드 0x8007007b 또는 0x80c80255)를 표시하면 각 파일 이름에서 오류가 있는 문자를 제거하거나 이름을 변경해야 합니다. 이러한 문자는 대부분 표준 시각적 개체 인코딩이 없으므로 PowerShell이 이러한 문자를 물음표 또는 빈 사각형으로 인쇄할 수 있습니다. 
> [!Note]  
> [평가 도구](file-sync-planning.md#evaluation-cmdlet)는 지원되지 않는 문자를 식별하는 데 사용될 수 있습니다. 데이터 세트에 잘못된 문자가 포함된 파일이 여러 개 있는 경우 [ScanUnsupportedChars](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars) 스크립트를 사용하여 지원되지 않는 문자가 포함된 파일의 이름을 바꿉니다.

아래 표에서 Azure 파일 동기화에서 지원하지 않는 모든 유니코드 문자가 포함되어 있습니다.

| 문자 집합 | 문자 수 |
|---------------|-----------------|
| 0x00000000 - 0x0000001F(제어 문자) | 32 |
| 0x0000FDD0 - 0x0000FDDD(아라비아식 표현꼴-a) | 14 |
| <ul><li>0x00000022(따옴표)</li><li>0x0000002A(별표)</li><li>0x0000002F(슬래시)</li><li>0x0000003A(콜론)</li><li>0x0000003C(보다 작음)</li><li>0x0000003E(보다 큼)</li><li>0x0000003F(물음표)</li><li>0x0000005C(백슬래시)</li><li>0x0000007C(파이프 또는 막대)</li></ul> | 9 |
| <ul><li>0x0004FFFE - 0x0004FFFF = 2(noncharacter)</li><li>0x0008FFFE - 0x0008FFFF = 2(noncharacter)</li><li>0x000CFFFE - 0x000CFFFF = 2(noncharacter)</li><li>0x0010FFFE - 0x0010FFFF = 2(noncharacter)</li></ul> | 8 |
| <ul><li>0x0000009D(`osc` operating system command)</li><li>0x00000090(dcs device control string)</li><li>0x0000008F(ss3 single shift three)</li><li>0x00000081(high octet preset)</li><li>0x0000007F(del delete)</li><li>0x0000008D(ri reverse line feed)</li></ul> | 6 |
| 0x0000FFF0, 0x0000FFFD, 0x0000FFFE, 0x0000FFFF(특수) | 4 |
| 마침표로 끝나는 파일 또는 디렉터리 | 1 |

### <a name="common-sync-errors"></a>일반적인 동기화 오류
<a id="-2147023673"></a>**동기화 세션이 취소되었습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT(10진)** | -2147023673 | 
| **오류 문자열** | ERROR_CANCELLED |
| **재구성 필요** | 예 |

서버 다시 시작 또는 업데이트 중, VSS 스냅샷 등의 다양한 이유로 동기화 세션이 실패할 수 있습니다. 이 오류는 후속 조치가 필요한 것처럼 보이지만, 여러 시간 동안 유지되지 않는 이상 무시해도 됩니다.

<a id="-2147012889"></a>**서비스와 연결할 수 없습니다.**    

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT(10진)** | -2147012889 | 
| **오류 문자열** | WININET_E_NAME_NOT_RESOLVED |
| **재구성 필요** | 예 |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

> [!Note]  
> Azure 파일 동기화 서비스에 대한 네트워크 연결이 복원되더라도 동기화가 즉시 재개되지 않을 수 있습니다. 서버 엔드포인트 위치 내에서 변경 내용이 검색되지 않는 경우 기본적으로 Azure 파일 동기화는 30분마다 동기화 세션을 시작합니다. 동기화 세션을 강제로 시작하려면 스토리지 동기화 에이전트(FileSyncSvc) 서비스를 다시 시작하거나 서버 엔드포인트 위치 내의 파일 또는 디렉터리를 변경합니다.

<a id="-2134376372"></a>**사용자 요청이 서비스에 의해 제한되었습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT(10진)** | -2134376372 |
| **오류 문자열** | ECS_E_USER_REQUEST_THROTTLED |
| **재구성 필요** | 예 |

아무 조치도 필요 없습니다. 서버가 다시 시도합니다. 이 오류가 몇 시간 동안 지속되면 지원 요청을 만드세요.

<a id="-2134364160"></a>**작업이 중단되어 동기화하지 못했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c83000 |
| **HRESULT(10진)** | -2134364160 |
| **오류 문자열** | ECS_E_OPERATION_ABORTED |
| **재구성 필요** | 예 |

사용자가 조치할 필요는 없습니다. 이 오류가 몇 시간 동안 지속되면 지원 요청을 만드세요.

<a id="-2134364043"></a>**변경 검색이 복원 후 작업을 완료할 때까지 동기화가 차단됩니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT(10진)** | -2134364043 |
| **오류 문자열** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **재구성 필요** | 예 |

사용자가 조치할 필요는 없습니다. Azure Backup을 사용하여 파일 또는 파일 공유(클라우드 엔드포인트)를 복원할 때 Azure 파일 공유에서 변경 검색이 완료될 때까지 동기화가 차단됩니다. 변경 검색은 복원이 완료되는 즉시 실행되며 기간은 파일 공유의 파일 수를 기반으로 합니다

<a id="-2147216747"></a>**동기화 데이터베이스가 언로드되어 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT(10진)** | -2147216747 |
| **오류 문자열** | SYNC_E_METADATA_INVALID_OPERATION |
| **재구성 필요** | 예 |

이 오류는 일반적으로 백업 애플리케이션에서 VSS 스냅샷을 만들고 동기화 데이터베이스가 언로드될 때 발생합니다. 이 오류가 몇 시간 동안 지속되면 지원 요청을 만드세요.

<a id="-2134364065"></a>**동기화에서 클라우드 엔드포인트에 지정된 Azure 파일 공유에 액세스할 수 없습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT(10진)** | -2134364065 |
| **오류 문자열** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 동기화 에이전트가 Azure 파일 공유에 액세스할 수 없어서 발생하며, 액세스할 수 없는 이유는 Azure 파일 공유 또는 Azure 파일 공유를 호스팅하는 스토리지 계정이 더 이상 존재하지 않기 때문입니다. 다음 단계를 진행하여 이 오류를 해결할 수 있습니다.

1. [스토리지 계정이 있는지 확인합니다.](#troubleshoot-storage-account)
2. [Azure 파일 공유가 있는지 확인합니다.](#troubleshoot-azure-file-share)
3. [Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](#troubleshoot-rbac)
4. [스토리지 계정에 방화벽 및 가상 네트워크 설정이 제대로 구성되어 있는지 확인합니다(사용하도록 설정된 경우).](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**요청에 이 작업을 수행할 수 있는 권한이 없어 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT(10진)** | -2134351804 |
| **오류 문자열** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 동기화 에이전트가 Azure 파일 공유에 액세스할 수 있는 권한이 없기 때문에 발생합니다. 다음 단계를 진행하여 이 오류를 해결할 수 있습니다.

1. [스토리지 계정이 있는지 확인합니다.](#troubleshoot-storage-account)
2. [Azure 파일 공유가 있는지 확인합니다.](#troubleshoot-azure-file-share)
3. [스토리지 계정에 방화벽 및 가상 네트워크 설정이 제대로 구성되어 있는지 확인합니다(사용하도록 설정된 경우).](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**사용된 스토리지 계정 이름을 확인할 수 없습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT(10진)** | -2134364064 |
| **오류 문자열** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **재구성 필요** | 예 |

1. 서버에서 스토리지 DNS 이름을 확인할 수 있는지 확인합니다.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [스토리지 계정이 있는지 확인합니다.](#troubleshoot-storage-account)
3. [스토리지 계정에 방화벽 및 가상 네트워크 설정이 제대로 구성되어 있는지 확인합니다(사용하도록 설정된 경우).](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

> [!Note]  
> Azure 파일 동기화 서비스에 대한 네트워크 연결이 복원되더라도 동기화가 즉시 재개되지 않을 수 있습니다. 서버 엔드포인트 위치 내에서 변경 내용이 검색되지 않는 경우 기본적으로 Azure 파일 동기화는 30분마다 동기화 세션을 시작합니다. 동기화 세션을 강제로 시작하려면 스토리지 동기화 에이전트(FileSyncSvc) 서비스를 다시 시작하거나 서버 엔드포인트 위치 내의 파일 또는 디렉터리를 변경합니다.

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**스토리지 계정에 액세스하는 동안 알 수 없는 오류가 발생했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT(10진)** | -2134364022 |
| **오류 문자열** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **재구성 필요** | 예 |

1. [스토리지 계정이 있는지 확인합니다.](#troubleshoot-storage-account)
2. [스토리지 계정에 방화벽 및 가상 네트워크 설정이 제대로 구성되어 있는지 확인합니다(사용하도록 설정된 경우).](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**스토리지 계정이 잠겨 있어 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT(10진)** | -2134364014 |
| **오류 문자열** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **재구성 필요** | 예 |

스토리지 계정에 읽기 전용 [리소스 잠금](../../azure-resource-manager/management/lock-resources.md)이 있는 경우에 이 오류가 발생합니다. 이 문제를 해결하려면 스토리지 계정의 읽기 전용 리소스 잠금을 제거합니다. 

<a id="-1906441138"></a>**동기화 데이터베이스에 문제가 있어 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT(10진)** | -1906441138 |
| **오류 문자열** | JET_errWriteConflict |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 동기화에서 사용하는 내부 데이터베이스에 문제가 있을 때 발생합니다. 이 문제가 발생할 경우 지원 요청을 만드시면 이 문제를 해결할 수 있도록 연락을 드리겠습니다.

<a id="-2134364053"></a>**서버에 설치된 Azure 파일 동기화 에이전트 버전이 지원되지 않습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT(10진)** | -2134364053 |
| **오류 문자열** | ECS_E_AGENT_VERSION_BLOCKED |
| **재구성 필요** | 예 |

서버에 설치된 Azure 파일 동기화 에이전트 버전이 지원되지 않으면 이 오류가 발생합니다. 이 문제를 해결하려면 [지원되는 에이전트 버전](file-sync-release-notes.md#supported-versions)으로 [업그레이드](file-sync-release-notes.md#azure-file-sync-agent-update-policy)합니다.

<a id="-2134351810"></a>**Azure 파일 공유 스토리지 용량 한도에 도달했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT(10진)** | -2134351810 |
| **오류 문자열** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **재구성 필요** | 예 |

| Error | 코드 |
|-|-|
| **HRESULT** | 0x80c80249 |
| **HRESULT(10진)** | -2134375863 |
| **오류 문자열** | ECS_E_NOT_ENOUGH_REMOTE_STORAGE |
| **재구성 필요** | 예 |

Azure 파일 공유 스토리지 한도에 도달하면 이러한 오류와 함께 동기화 세션이 실패하며, Azure 파일 공유에 할당량이 적용되거나 사용량이 Azure 파일 공유의 한도를 초과할 때 발생할 수 있습니다. 자세한 내용은 [Azure 파일 공유의 현재 한도](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)를 참조하세요.

1. 스토리지 동기화 서비스 내에서 동기화 그룹으로 이동합니다.
2. 동기화 그룹 내에서 클라우드 엔드포인트를 선택합니다.
3. 열리는 창에서 Azure 파일 공유 이름을 확인합니다.
4. 연결된 스토리지 계정을 선택합니다. 이 연결이 실패하면 참조되는 스토리지 계정이 제거된 것입니다.

    ![스토리지 계정에 연결된 클라우드 엔드포인트 세부 정보를 보여주는 스크린샷.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. **파일** 을 선택하여 파일 공유 목록을 봅니다.
6. 클라우드 엔드포인트가 참조하는 Azure 파일 공유에 대한 행의 끝에서 세 점을 클릭합니다.
7. **사용량** 이 **할당량** 미만인지 확인합니다. 다른 할당량이 지정되지 않은 이상, 할당량은 [Azure 파일 공유의 최대 크기](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)와 일치합니다.

    ![Azure 파일 공유 속성의 스크린샷.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

공유가 가득 차고 할당량이 설정되지 않은 경우 이 문제를 해결하는 한 가지 방법은 현재 서버 엔드포인트의 각 하위 폴더를 자체 동기화 그룹의 고유한 서버 엔드포인트에 배치하는 것입니다. 이렇게 하면 각 하위 폴더가 개별 Azure 파일 공유와 동기화됩니다.

<a id="-2134351824"></a>**Azure 파일 공유를 찾을 수 없습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT(10진)** | -2134351824 |
| **오류 문자열** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 공유에 액세스할 수 없을 때 발생합니다. 문제 해결 방법:

1. [스토리지 계정이 있는지 확인합니다.](#troubleshoot-storage-account)
2. [Azure 파일 공유가 있는지 확인합니다.](#troubleshoot-azure-file-share)

Azure 파일 공유가 삭제된 경우 새 파일 공유를 만든 후 동기화 그룹을 다시 만들어야 합니다. 

<a id="-2134364042"></a>**이 Azure 구독이 일시 중단된 동안 동기화가 일시 중단되었습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT(10진)** | -2134364042 |
| **오류 문자열** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **재구성 필요** | 예 |

이 오류는 Azure 구독이 일시 중단되면 발생합니다. Azure 구독이 복원되면 동기화가 다시 활성화됩니다. 자세한 내용은 [내 Azure 구독이 비활성화된 이유 및 다시 활성화하는 방법은 무엇인가요?](../../cost-management-billing/manage/subscription-disabled.md)를 참조하세요.

<a id="-2134375618"></a>**스토리지 계정에 방화벽 또는 가상 네트워크가 구성되어 있습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c8033e |
| **HRESULT(10진)** | -2134375618 |
| **오류 문자열** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **재구성 필요** | 예 |

이 오류는 스토리지 계정 방화벽 때문에 또는 스토리지 계정이 가상 네트워크에 속하기 때문에 Azure 파일 공유에 액세스할 수 없을 때 발생합니다. 스토리지 계정에 방화벽 및 가상 네트워크 설정이 제대로 구성되어 있는지 확인합니다. 자세한 내용은 [방화벽 및 가상 네트워크 설정 구성](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)을 참조하세요. 

<a id="-2134375911"></a>**동기화 데이터베이스에 문제가 있어 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT(10진)** | -2134375911 |
| **오류 문자열** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **재구성 필요** | 예 |

일반적으로 이 오류는 저절로 해결되며, 다음과 같은 경우에 발생할 수 있습니다.

* 동기화 그룹의 서버에서 다수의 파일이 변경되었습니다.
* 개별 파일 및 디렉터리에 수많은 오류가 있습니다.

이 문제가 여러 시간 동안 지속되는 경우 지원 요청을 만드시면 이 문제를 해결할 수 있도록 연락을 드리겠습니다.

<a id="-2146762487"></a>**서버가 보안 연결을 설정하지 못했습니다. 클라우드 서비스가 예기치 않은 인증서를 받았습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT(10진)** | -2146762487 |
| **오류 문자열** | CERT_E_UNTRUSTEDROOT |
| **재구성 필요** | 예 |

이 오류는 조직에서 TLS 종료 프록시를 사용 중이거나 악의적인 엔터티가 서버와 Azure 파일 동기화 서비스 간 트래픽을 가로채는 경우에 발생할 수 있습니다. 이 문제가 발생할 것으로 확신한다면(조직에서 TLS 종료 프록시를 사용하므로) 레지스트리를 재정의하여 인증서 확인을 건너뛸 수 있습니다.

1. SkipVerifyingPinnedRootCertificate 레지스트리 값을 만듭니다.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. 등록된 서버에서 동기화 서비스를 다시 시작합니다.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

이 레지스트리 값을 설정하면 Azure 파일 동기화 에이전트에서 서버와 클라우드 서비스 간에 데이터를 전송할 때 로컬에서 신뢰할 수 있는 모든 TLS/SSL 인증서를 수락합니다.

<a id="-2147012894"></a>**서비스와 연결할 수 없습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT(10진)** | -2147012894 |
| **오류 문자열** | WININET_E_TIMEOUT |
| **재구성 필요** | 예 |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

> [!Note]  
> Azure 파일 동기화 서비스에 대한 네트워크 연결이 복원되더라도 동기화가 즉시 재개되지 않을 수 있습니다. 서버 엔드포인트 위치 내에서 변경 내용이 검색되지 않는 경우 기본적으로 Azure 파일 동기화는 30분마다 동기화 세션을 시작합니다. 동기화 세션을 강제로 시작하려면 스토리지 동기화 에이전트(FileSyncSvc) 서비스를 다시 시작하거나 서버 엔드포인트 위치 내의 파일 또는 디렉터리를 변경합니다.

<a id="-2147012721"></a>**서버가 Azure 파일 동기화 서비스의 응답을 디코딩할 수 없어 동기화가 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80072f8f |
| **HRESULT(10진)** | -2147012721 |
| **오류 문자열** | WININET_E_DECODING_FAILED |
| **재구성 필요** | 예 |

일반적으로 이 오류는 네트워크 프록시가 Azure 파일 동기화 서비스의 응답을 수정하는 경우에 발생합니다. 프록시 구성을 확인하세요.

<a id="-2134375680"></a>**인증에 문제가 있어 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT(10진)** | -2134375680 |
| **오류 문자열** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **재구성 필요** | 예 |

이 오류는 일반적으로 서버 시간이 올바르지 않기 때문에 발생합니다. 서버를 가상 머신에서 실행하는 경우 호스트의 시간이 올바른지 확인합니다.

<a id="-2134364040"></a>**인증서 만료로 인해 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT(10진)** | -2134364040 |
| **오류 문자열** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **재구성 필요** | 예 |

이 오류는 인증에 사용되는 인증서가 만료된 경우에 발생합니다.

인증서가 만료되었는지 확인하려면 다음 단계를 수행합니다.  
1. 인증서 MMC 스냅인을 열고, 컴퓨터 계정을 선택하고, Certificates (Local Computer)\Personal\Certificates로 이동합니다.
2. 클라이언트 인증 인증서가 만료되었는지 확인합니다.

클라이언트 인증 인증서가 만료된 경우 다음 단계를 수행하여 문제를 해결합니다.

1. Azure 파일 동기화 에이전트 버전 4.0.1.0 이상이 설치되어 있는지 확인합니다.
2. 서버에서 다음 PowerShell 명령을 실행합니다.

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**인증 인증서를 찾을 수 없어 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT(10진)** | -2134375896 |
| **오류 문자열** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **재구성 필요** | 예 |

이 오류는 인증에 사용된 인증서를 찾을 수 없기 때문에 발생합니다.

이 문제를 해결하려면 다음 단계를 수행합니다.

1. Azure 파일 동기화 에이전트 버전 4.0.1.0 이상이 설치되어 있는지 확인합니다.
2. 서버에서 다음 PowerShell 명령을 실행합니다.

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**인증 ID를 찾을 수 없어 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT(10진)** | -2134364039 |
| **오류 문자열** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **재구성 필요** | 예 |

이 오류는 서버 엔드포인트 삭제가 실패하여 엔드포인트가 현재 부분적으로 삭제된 상태이기 때문에 발생합니다. 이 문제를 해결하려면 서버 엔드포인트를 다시 삭제합니다.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**서버 엔드포인트가 있는 볼륨의 디스크 공간이 부족합니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT(10진)** | -1906441711 |
| **오류 문자열** | JET_errLogDiskFull |
| **재구성 필요** | 예 |

| Error | 코드 |
|-|-|
| **HRESULT** | 0x80c8031a |
| **HRESULT(10진)** | -2134375654 |
| **오류 문자열** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **재구성 필요** | 예 |

볼륨의 디스크 공간이 부족하거나 디스크 할당량 한도에 도달했기 때문에 이러한 오류 중 하나와 함께 동기화 세션이 실패합니다. 이 오류는 일반적으로 서버 엔드포인트 외부의 파일이 볼륨의 공간을 모두 사용할 때 발생합니다. 서버 엔드포인트를 추가하거나, 파일을 다른 볼륨으로 이동하거나, 서버 엔드포인트가 있는 볼륨의 크기를 늘려서 볼륨의 공간을 확보해야 합니다. [파일 서버 리소스 관리자](/windows-server/storage/fsrm/fsrm-overview) 또는 [NTFS 할당량](/windows-server/administration/windows-commands/fsutil-quota)을 사용하여 볼륨에서 디스크 할당량이 구성된 경우 할당량 한도를 늘려야 합니다.

<a id="-2134364145"></a><a id="replica-not-ready"></a> **서비스를 이 서버 엔드포인트와 동기화할 준비가 완료되지 않았습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT(10진)** | -2134364145 |
| **오류 문자열** | ECS_E_REPLICA_NOT_READY |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 공유에 이미 존재하는 콘텐츠를 사용하여 클라우드 엔드포인트를 만들었기 때문에 발생합니다. Azure 파일 동기화는 서버 엔드포인트에서 초기 동기화를 진행하도록 허용하기 전에 Azure 파일 공유에서 모든 콘텐츠를 검사해야 합니다.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**여러 개별 파일의 문제로 인해 동기화가 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT(10진)** | -2134375877 |
| **오류 문자열** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **재구성 필요** | 예 |

| Error | 코드 |
|-|-|
| **HRESULT** | 0x80c8021c |
| **HRESULT(10진)** | -2134375908 |
| **오류 문자열** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **재구성 필요** | 예 |

| Error | 코드 |
|-|-|
| **HRESULT** | 0x80c80253 |
| **HRESULT(10진)** | -2134375853 |
| **오류 문자열** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **재구성 필요** | 예 |

항목별 오류와 동기화하는 데 실패한 파일이 많은 경우 동기화 세션이 해당 오류 중 하나 때문에 실패합니다. 항목별 오류를 해결하려면 [동기화되지 않는 특정 파일이나 폴더가 있는지 확인하는 방법](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) 섹션에 설명된 단계를 수행합니다. 동기화 오류 ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED가 있으면 지원 사례를 엽니다.

> [!NOTE]
> Azure 파일 동기화는 핸들이 열려 있는 파일을 동기화하기 위해 하루 한 번 서버에 임시 VSS 스냅샷을 만듭니다.

<a id="-2134376423"></a>**서버 엔드포인트 경로에 문제가 있어 동기화가 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT(10진)** | -2134376423 |
| **오류 문자열** | ECS_E_SYNC_INVALID_PATH |
| **재구성 필요** | 예 |

경로가 존재하는지, 로컬 NTFS 볼륨에 있는지, 그리고 재분석 지점이나 기존 서버 엔드포인트가 아닌지 확인하세요.

<a id="-2134375817"></a>**필터 드라이버 버전이 에이전트 버전과 호환되지 않아 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT(10진)** | -2134375817 |
| **오류 문자열** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **재구성 필요** | 예 |

로드된 클라우드 계층화 필터 드라이버(StorageSync.sys) 버전이 스토리지 동기화 에이전트(FileSyncSvc) 서비스와 호환되지 않아 이 오류가 발생합니다. Azure 파일 동기화 에이전트가 업그레이드된 경우 서버를 다시 시작하여 설치를 완료합니다. 오류가 계속 발생하면 에이전트를 제거하고 서버를 다시 시작한 후 Azure 파일 동기화 에이전트를 다시 설치합니다.

<a id="-2134376373"></a>**서비스를 현재 사용할 수 없습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT(10진)** | -2134376373 |
| **오류 문자열** | ECS_E_SERVICE_UNAVAILABLE |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 동기화 서비스를 사용할 수 없기 때문에 발생합니다. 이 오류는 Azure 파일 동기화 서비스를 다시 사용할 수 있는 경우 자동으로 해결됩니다.

> [!Note]  
> Azure 파일 동기화 서비스에 대한 네트워크 연결이 복원되더라도 동기화가 즉시 재개되지 않을 수 있습니다. 서버 엔드포인트 위치 내에서 변경 내용이 검색되지 않는 경우 기본적으로 Azure 파일 동기화는 30분마다 동기화 세션을 시작합니다. 동기화 세션을 강제로 시작하려면 스토리지 동기화 에이전트(FileSyncSvc) 서비스를 다시 시작하거나 서버 엔드포인트 위치 내의 파일 또는 디렉터리를 변경합니다.

<a id="-2146233088"></a>**예외로 인해 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT(10진)** | -2146233088 |
| **오류 문자열** | COR_E_EXCEPTION |
| **재구성 필요** | 예 |

이 오류는 예외로 인해 동기화가 실패한 경우에 발생합니다. 이 오류가 몇 시간 동안 지속되면 지원 요청을 만드세요.

<a id="-2134364045"></a>**스토리지 계정이 다른 지역으로 장애 조치(failover)되어 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT(10진)** | -2134364045 |
| **오류 문자열** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **재구성 필요** | 예 |

스토리지 계정이 다른 지역으로 장애 조치(failover)된 경우에 이 오류가 발생합니다. Azure 파일 동기화는 스토리지 계정 장애 조치(failover) 기능을 지원하지 않습니다. Azure 파일 동기화에서 클라우드 엔드포인트로 사용되는 Azure 파일 공유를 포함하는 스토리지 계정은 장애 조치(failover)하지 않아야 합니다. 이러한 계정을 장애 조치(failover)하면 동기화가 더 이상 진행되지 않고, 새로 계층화된 파일의 경우 예기치 않은 데이터 손실이 발생할 수도 있습니다. 이 문제를 해결하려면 스토리지 계정을 주 지역으로 이동합니다.

<a id="-2134375922"></a>**동기화 데이터베이스에 일시적인 문제가 있어 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT(10진)** | -2134375922 |
| **오류 문자열** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **재구성 필요** | 예 |

이 오류는 동기화 데이터베이스에 내부 문제가 있어서 발생합니다. 이 오류는 동기화를 다시 시도하면 자동으로 해결됩니다. 이 문제가 오래 지속되는 경우 지원 요청을 만드시면 이 문제를 해결할 수 있도록 연락을 드리겠습니다.

<a id="-2134364024"></a>**Azure Active Directory 테넌트 변경 때문에 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT(10진)** | -2134364024 | 
| **오류 문자열** | ECS_E_INVALID_AAD_TENANT |
| **재구성 필요** | 예 |

Azure 파일 동기화 에이전트가 최신 버전인지 확인합니다. Azure 파일 동기화는 에이전트 V10부터 구독을 다른 Azure Active Directory 테넌트로 이동하는 것을 지원합니다.
 
최신 에이전트 버전을 사용하는 경우 Microsoft.StorageSync 애플리케이션에 스토리지 계정에 대한 액세스 권한을 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인](#troubleshoot-rbac) 참조).

<a id="-2134364010"></a>**방화벽 및 가상 네트워크 예외가 구성되지 않아 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT(10진)** | -2134364010 | 
| **오류 문자열** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **재구성 필요** | 예 |

이 오류는 스토리지 계정에서 방화벽 및 가상 네트워크 설정을 사용하도록 설정하고 "신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용" 예외가 선택되어 있지 않은 경우에 발생합니다. 이 문제를 해결하려면 배포 가이드의 [방화벽 및 가상 네트워크 설정 구성](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) 섹션에 설명된 단계를 수행합니다.

<a id="-2147024891"></a>**시스템 볼륨 정보 폴더에 대한 권한이 잘못되어 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT(10진)** | -2147024891 |
| **오류 문자열** | ERROR_ACCESS_DENIED |
| **재구성 필요** | 예 |

이 오류는 NT AUTHORITY\SYSTEM 계정에 서버 엔드포인트가 있는 볼륨의 시스템 볼륨 정보 폴더에 대한 권한이 없는 경우에 발생할 수 있습니다. 개별 파일이 ERROR_ACCESS_DENIED 때문에 동기화되지 않는 경우 [파일/디렉터리별 동기화 오류 문제 해결](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors) 섹션에 설명된 단계를 수행합니다.

이 문제를 해결하려면 다음 단계를 수행합니다.

1. [PsExec](/sysinternals/downloads/psexec) 도구를 다운로드합니다.
2. 관리자 권한 명령 프롬프트에서 `PsExec.exe -i -s -d cmd` 명령을 실행하여 시스템 계정으로 명령 프롬프트를 시작합니다.
3. 시스템 계정에서 실행되는 명령 프롬프트에서 `cacls "drive letter:\system volume information" /T /C` 명령을 실행하여 NT AUTHORITY\SYSTEM 계정에 System Volume Information 폴더에 대한 액세스 권한이 없는지 확인합니다.
4. NT AUTHORITY\SYSTEM 계정에 System Volume Information 폴더에 대한 액세스 권한이 없는 경우 `cacls  "drive letter:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM:F"` 명령을 실행합니다.
    - 액세스 거부로 인해 4단계가 실패하는 경우 `takeown /A /R /F "drive letter:\System Volume Information"` 명령을 실행하여 System Volume Information 폴더의 소유권을 가져온 다음, 4단계를 반복합니다.

<a id="-2134375810"></a>**Azure 파일 공유가 삭제 후 다시 생성되었으므로 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT(10진)** | -2134375810 |
| **오류 문자열** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 동기화가 동일한 동기화 그룹에서 Azure 파일 공유를 삭제하고 다시 만드는 작업을 지원하지 않기 때문에 발생합니다. 

이 문제를 해결하려면 다음 단계를 수행하여 동기화 그룹을 삭제하고 다시 만듭니다.

1. 동기화 그룹에서 모든 서버 엔드포인트를 삭제합니다.
2. 클라우드 엔드포인트를 삭제합니다. 
3. 동기화 그룹을 삭제합니다.
4. 서버 엔드포인트에서 클라우드 계층화를 사용하도록 설정한 경우 [서버 엔드포인트를 삭제한 후 서버에서 계층화된 파일에 액세스할 수 없음](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) 섹션에 설명된 단계를 수행하여 서버에서 분리되고 계층화된 파일을 삭제합니다.
5. 동기화 그룹을 다시 만듭니다.

<a id="-2134375852"></a>**동기화 중에 이전 상태로 복원된 복제본을 발견했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c80254 |
| **HRESULT(10진)** | -2134375852 |
| **오류 문자열** | ECS_E_SYNC_REPLICA_BACK_IN_TIME |
| **재구성 필요** | 예 |

사용자가 조치할 필요는 없습니다. 이 오류는 동기화 중에 이전 상태로 복원된 복제본을 발견할 때 발생합니다. 이제 동기화가 조정 모드로 전환됩니다. 이 모드에서는 Azure 파일 공유의 콘텐츠와 서버 엔드포인트의 데이터를 병합하여 동기화 관계를 다시 만듭니다. 조정 모드가 트리거되면 네임스페이스 크기에 따라 프로세스가 매우 오래 걸릴 수 있습니다. 정기적인 동기화는 조정이 완료될 때까지 발생하지 않으며, Azure 파일 공유와 서버 엔드포인트 간에 서로 다른(마지막으로 수정된 시간 또는 크기) 파일이 있으면 파일 충돌이 발생합니다.

<a id="-2145844941"></a>**HTTP 요청이 리디렉션되어 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT(10진)** | -2145844941 |
| **오류 문자열** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **재구성 필요** | 예 |

이 오류는 Azure 파일 동기화가 HTTP 리디렉션(3xx 상태 코드)을 지원하지 않기 때문에 발생합니다. 이 문제를 해결하려면 프록시 서버 또는 네트워크 디바이스에서 HTTP 리디렉션을 사용하지 않도록 설정합니다.

<a id="-2134364027"></a>**오프라인 데이터 전송 도중 시간 초과가 발생했지만 아직 진행 중입니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT(10진)** | -2134364027 |
| **오류 문자열** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **재구성 필요** | 예 |

이 오류는 데이터 수집 작업이 시간 제한을 초과하는 경우에 발생합니다. 동기화가 진행 중인 경우(AppliedItemCount가 0보다 큼) 이 오류를 무시할 수 있습니다. [현재 동기화 세션의 진행률을 모니터링하려면 어떻게 해야 하나요?](#how-do-i-monitor-the-progress-of-a-current-sync-session)를 참조하세요.

<a id="-2134375814"></a>**서버에서 서버 엔드포인트 경로를 찾을 수 없으므로 동기화에 실패했습니다.**  

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80c8027a |
| **HRESULT(10진)** | -2134375814 |
| **오류 문자열** | ECS_E_SYNC_ROOT_DIRECTORY_NOT_FOUND |
| **재구성 필요** | 예 |

이 오류는 서버 엔드포인트 경로로 사용된 디렉터리의 이름이 바뀌거나 삭제되면 발생합니다. 디렉터리의 이름이 바뀌면 디렉터리 이름을 다시 원래 이름으로 바꾸고 스토리지 동기화 에이전트 서비스(FileSyncSvc)를 다시 시작합니다.

디렉터리가 삭제되면 다음 단계를 수행하여 기존 서버 엔드포인트를 제거하고 새 경로를 사용하여 새 서버 엔드포인트를 만듭니다.

1. [서버 엔드포인트 제거](file-sync-server-endpoint-delete.md)에 설명된 단계를 수행하여 동기화 그룹에서 서버 엔드포인트를 제거합니다.
1. [서버 엔드포인트 추가](file-sync-server-endpoint-create.md)에 설명된 단계를 수행하여 동기화 그룹에 서버 엔드포인트를 만듭니다.

<a id="-2134375783"></a>**서버 경로가 비어 있어 서버 엔드포인트 프로비저닝이 실패했습니다.**

| 오류 | 코드 |
|-|-|
| **HRESULT** | 0x80C80299 |
| **HRESULT(10진)** | -2134375783 |
| **오류 문자열** | ECS_E_SYNC_AUTHORITATIVE_UPLOAD_EMPTY_SET |
| **재구성 필요** | 예 |

다음 조건이 충족되면 이 오류 코드와 함께 서버 엔드포인트 프로비저닝이 실패합니다. 
* 이 서버 엔드포인트는 초기 동기화 모드 [서버 권한](file-sync-server-endpoint-create.md#initial-sync-section)을 사용하여 프로비저닝되었습니다.
* 로컬 서버 경로가 비어 있거나 동기화 가능한 것으로 인식되는 항목이 없습니다.

이 프로비저닝 오류는 Azure 파일 공유에서 사용할 수도 있는 모든 콘텐츠를 삭제하지 못하도록 차단합니다. 서버 권한 업로드는 이미 시드된 클라우드 위치를 서버 위치의 업데이트로 보완하는 특수 모드입니다. 이 [마이그레이션 가이드](../files/storage-files-migration-server-hybrid-databox.md)를 검토하여 이 모드가 어떤 시나리오를 위해 개발되었는지 알아보세요.

1. [서버 엔드포인트 제거](file-sync-server-endpoint-delete.md)에 설명된 단계를 수행하여 동기화 그룹에서 서버 엔드포인트를 제거합니다.
1. [서버 엔드포인트 추가](file-sync-server-endpoint-create.md)에 설명된 단계를 수행하여 동기화 그룹에 서버 엔드포인트를 만듭니다.

### <a name="common-troubleshooting-steps"></a>일반적인 문제 해결 단계
<a id="troubleshoot-storage-account"></a>**스토리지 계정이 있는지 확인합니다.**  
# <a name="portal"></a>[포털](#tab/azure-portal)
1. 스토리지 동기화 서비스 내에서 동기화 그룹으로 이동합니다.
2. 동기화 그룹 내에서 클라우드 엔드포인트를 선택합니다.
3. 열리는 창에서 Azure 파일 공유 이름을 확인합니다.
4. 연결된 스토리지 계정을 선택합니다. 이 연결이 실패하면 참조되는 스토리지 계정이 제거된 것입니다.
    ![스토리지 계정에 연결된 클라우드 엔드포인트 세부 정보를 보여주는 스크린샷.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Azure 파일 공유가 있는지 확인합니다.**  
# <a name="portal"></a>[포털](#tab/azure-portal)
1. 왼쪽의 목차에서 **개요** 를 클릭하여 기본 스토리지 계정 페이지로 돌아갑니다.
2. **파일** 을 선택하여 파일 공유 목록을 봅니다.
3. 클라우드 엔드포인트에서 참조하는 파일 공유가 파일 공유 목록에 나타나는지 확인합니다(위의 1단계에서 적어두었어야 합니다).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.**  
# <a name="portal"></a>[포털](#tab/azure-portal)
1. 왼쪽 목차에서 **액세스 제어(IAM)** 를 클릭합니다.
1. **역할 할당** 탭을 클릭하여 스토리지 계정에 액세스할 수 있는 사용자 및 애플리케이션(‘서비스 주체’)을 나열합니다.
1. **Microsoft.StorageSync** 또는 **하이브리드 파일 동기화 서비스**(이전 애플리케이션 이름)가 **읽기 권한자 및 데이터 액세스** 역할이 있는 목록에 나타나는지 확인합니다. 

    ![스토리지 계정의 액세스 제어 탭에 표시된 하이브리드 파일 동기화 서비스의 서비스 사용자 스크린샷](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    **Microsoft.StorageSync** 또는 **하이브리드 파일 동기화** 서비스가 목록에 나타나지 않으면 다음 단계를 수행합니다.

    - **추가** 를 클릭합니다.
    - **역할** 필드에서 **읽기 권한자 및 데이터 액세스** 를 선택합니다.
    - **선택** 필드에 **Microsoft.StorageSync** 를 입력하고 역할을 선택한 다음 **저장** 을 클릭합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

## <a name="cloud-tiering"></a>클라우드 계층화 
클라우드 계층화에는 다음 두 가지 경로가 있습니다.

- 파일이 계층화되는 데 실패할 수 있습니다, 즉 Azure 파일 동기화에서 파일을 Azure Files로 성공적으로 계층화하지 못합니다.
- 파일을 회수하는 데 실패할 수 있습니다. 즉, 사용자가 계층화된 파일에 액세스하려고 하면 Azure 파일 동기화 파일 시스템 필터(StorageSync.sys)에서 데이터를 다운로드하지 못합니다.

오류 경로 중 하나를 통해 발생할 수 있는 두 가지 주요 오류 클래스가 있습니다.

- 클라우드 스토리지 오류
    - *일시적인 스토리지 서비스 가용성 문제* - 자세한 내용은 [Azure Storage에 대한 SLA(서비스 수준 계약)](https://azure.microsoft.com/support/legal/sla/storage/v1_5/)를 참조하세요.
    - *액세스할 수 없는 Azure 파일 공유* - 일반적으로 Azure 파일 공유가 여전히 동기화 그룹의 클라우드 엔드포인트일 때 Azure 파일 공유를 삭제하면 이 오류가 발생합니다.
    - *액세스할 수 없는 스토리지 계정* - 일반적으로 동기화 계정의 클라우드 엔드포인트인 Azure 파일 공유가 있는데 스토리지 계정을 삭제하면 이 오류가 발생합니다. 
- 서버 오류 
  - *Azure 파일 동기화 파일 시스템 필터(StorageSync.sys)가 로드되지 않음* - 계층화/회수 요청에 응답하려면 Azure 파일 동기화 파일 시스템 필터를 로드해야 합니다. 로드되지 않는 필터는 여러 가지 이유로 발생할 수 있지만, 가장 일반적인 이유는 관리자가 필터를 수동으로 언로드했기 때문입니다. Azure 파일 동기화가 제대로 작동하려면 Azure 파일 동기화 파일 시스템 파일 필터가 항상 로드되어야 합니다.
  - *누락되었거나 손상되었거나 끊어진 재분석 지점* - 재분석 지점은 다음 두 부분으로 구성된 파일의 특별한 데이터 구조입니다.
    1. 재분석 태그: Azure 파일 동기화 파일 시스템 필터(StorageSync.sys)가 파일에 대한 IO 작업 일부를 수행해야 하는 운영 체제를 나타냅니다. 
    2. 재분석 데이터: 파일 시스템 필터에 연결된 클라우드 엔드포인트(Azure 파일 공유)에 있는 파일의 URI를 나타냅니다. 
        
       재분석 지점이 손상될 수 있는 가장 일반적인 방법은 관리자가 태그 또는 해당 데이터를 수정하려고 하는 경우입니다. 
  - *네트워크 연결 문제* - 파일을 계층화하거나 회수하려면 서버에 인터넷 연결이 있어야 합니다.

다음 섹션에서는 클라우드 계층화 문제를 해결하고 클라우드 스토리지 문제 또는 서버 문제인지 여부를 확인하는 방법에 대해 설명합니다.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>서버의 계층화 작업을 모니터링하는 방법  
서버의 계층화 작업을 모니터링하려면 이벤트 뷰어의 Applications and Services\Microsoft\FileSync\Agent에 있는 원격 분석 이벤트 로그에서 이벤트 ID 9003, 9016 및 9029를 사용합니다.

- 이벤트 ID 9003은 서버 엔드포인트에 대한 오류 분포를 제공합니다. 총 오류 수, ErrorCode 등을 예로 들 수 있습니다. 한 이벤트는 오류 코드별로 기록됩니다.
- 이벤트 ID 9016은 볼륨에 대한 고스팅 결과를 제공합니다. 사용 가능한 공간 비율, 세션에서 고스팅된 파일 수, 고스팅에 실패한 파일 수 등을 예로 들 수 있습니다.
- 이벤트 ID 9029는 서버 엔드포인트의 고스팅 세션 정보를 제공합니다. 세션에서 시도된 파일 수, 세션에서 계층화된 파일 수, 이미 계층화된 파일 수 등을 예로 들 수 있습니다.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>서버의 회수 작업을 모니터링하는 방법
서버의 회수 작업을 모니터링하려면 이벤트 뷰어의 Applications and Services\Microsoft\FileSync\Agent에 있는 원격 분석 이벤트 로그에서 이벤트 ID 9005, 9006,9009 및 9059를 사용합니다.

- 9005 이벤트 ID는 서버 엔드포인트에 대한 회수 안정성을 제공합니다. 액세스된 고유한 파일 수, 액세스에 실패한 고유한 파일 수 등을 예로 들 수 있습니다.
- 이벤트 ID 9006은 서버 엔드포인트에 대한 회수 오류 분포를 제공합니다. 실패한 요청 수, ErrorCode 등을 예로 들 수 있습니다. 한 이벤트는 오류 코드별로 기록됩니다.
- 이벤트 ID 9009는 서버 엔드포인트의 회수 세션 정보를 제공합니다. DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed 등을 예로 들 수 있습니다.
- 이벤트 ID 9059는 서버 엔드포인트의 애플리케이션 회수 분포를 제공합니다. ShareId, Application Name, TotalEgressNetworkBytes 등을 예로 들 수 있습니다.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>계층화에 실패한 파일의 문제 해결 방법
파일이 Azure Files로 계층화되지 못한 경우:

1. 이벤트 뷰어에서 Applications and Services\Microsoft\FileSync\Agent 아래에 있는 원격 분석, 운영 및 진단 이벤트 로그를 검토합니다. 
   1. Azure 파일 공유에 파일이 있는지 확인합니다.

      > [!NOTE]
      > 계층화하려면 파일을 먼저 Azure 파일 공유에 동기화해야 합니다.

   2. 서버가 인터넷에 연결되어 있는지 확인합니다. 
   3. Azure 파일 동기화 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
       - 관리자 권한의 명령 프롬프트에서 `fltmc`를 실행합니다. StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

> [!NOTE]
> 파일이 계층화에 실패하면 이벤트 ID 9003이 원격 분석 이벤트 로그에 1시간에 한 번 기록됩니다(오류 코드 하나당 이벤트 하나가 기록). [계층화 오류 및 수정](#tiering-errors-and-remediation) 섹션을 확인하여 오류 코드에 대한 수정 단계가 나열되는지 확인합니다.

### <a name="tiering-errors-and-remediation"></a>계층화 오류 및 수정

| HRESULT | HRESULT(10진) | 오류 문자열 | 문제 | 재구성 |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | -2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | 초기 업로드가 진행 중이므로 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 초기 업로드가 완료되면 파일이 계층화됩니다. |
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | 사용 중이므로 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 파일이 더 이상 사용되지 않을 때 계층화됩니다. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | 동기화로 제외되어 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 동기화 제외 목록의 파일은 계층화할 수 없습니다. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | 서버에서 찾을 수 없어 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 오류가 계속 발생하면 서버에 파일이 있는지 확인합니다. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | Azure 파일 공유에서 삭제되었기 때문에 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 다음 다운로드 동기화 세션이 실행될 때 파일이 서버에서 삭제됩니다. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | 네트워크 문제로 인해 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 오류가 계속되면 Azure 파일 공유에 대한 네트워크 연결을 확인합니다. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | 네트워크 문제로 인해 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 오류가 계속되면 Azure 파일 공유에 대한 네트워크 연결을 확인합니다. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | 액세스 거부 오류로 인해 파일을 계층화하지 못했습니다. 파일이 DFS-R 읽기 전용 복제 폴더에 있는 경우 이 오류가 발생할 수 있습니다. | Azure 파일 동기화는 DFS-R 읽기 전용 복제 폴더에서 서버 엔드포인트를 지원하지 않습니다. 자세한 내용은 [계획 지침](file-sync-planning.md#distributed-file-system-dfs)을 참조하세요. |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | 네트워크 문제로 인해 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 오류가 계속되면 Azure 파일 공유에 대한 네트워크 연결을 확인합니다. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | 파일 크기가 지원되는 크기보다 작으므로 파일을 계층화하지 못했습니다. | 에이전트 버전이 9.0 미만인 경우 지원되는 최소 파일 크기는 64KiB입니다. 에이전트 버전이 9.0 이상인 경우 지원되는 최소 파일 크기는 파일 시스템 클러스터 크기(이중 파일 시스템 클러스터 크기)를 기반으로 합니다. 예를 들어 파일 시스템 클러스터 크기가 4KiB이면 최소 파일 크기는 8KiB입니다. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | Azure Storage 문제로 인해 파일을 계층화하지 못했습니다. | 오류가 지속되면 지원 요청을 작성합니다. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | 동시에 회수되었기 때문에 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 회수가 완료되고 파일이 더 이상 사용되지 않을 때 파일이 계층화됩니다. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | Azure 파일 공유와 동기화되지 않았으므로 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 파일은 Azure 파일 공유에 동기화된 후 계층화됩니다. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | 클라우드 계층화 필터 드라이버(storagesync.sys)가 실행되고 있지 않으므로 파일을 계층화하지 못했습니다. | 이 문제를 해결하려면 관리자 권한 명령 프롬프트를 열고 다음 명령을 실행합니다. `fltmc load storagesync`<br>fltmc 명령을 실행할 때 Azure 파일 동기화 필터 드라이버를 로드하지 못한 경우 Azure 파일 동기화 에이전트를 제거하고, 서버를 다시 시작하고, Azure 파일 동기화 에이전트를 다시 설치합니다. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | 서버 엔드포인트가 있는 볼륨의 디스크 공간이 부족하기 때문에 파일을 계층화하지 못했습니다. | 문제를 해결하려면 서버 엔드포인트가 있는 볼륨에서 100MiB 이상의 디스크 공간을 확보합니다. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | Azure 파일 공유와 동기화되지 않았으므로 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 파일은 Azure 파일 공유에 동기화된 후 계층화됩니다. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | 지원되지 않는 재분석 지점이므로 파일을 계층화하지 못했습니다. | 파일이 데이터 중복 제거 재분석 지점인 경우 [계획 지침](file-sync-planning.md#data-deduplication)의 단계에 따라 데이터 중복 제거 지원을 사용하도록 설정합니다. 데이터 중복 제거 이외의 재분석 지점이 있는 파일은 지원되지 않으며 계층화되지 않습니다.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | 파일이 수정되었으므로 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 수정된 파일이 Azure 파일 공유에 동기화되면 파일이 계층화됩니다. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | Azure 파일 공유와 동기화되지 않았으므로 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 파일은 Azure 파일 공유에 동기화된 후 계층화됩니다. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | 네트워크 문제로 인해 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 오류가 계속되면 Azure 파일 공유에 대한 네트워크 연결을 확인합니다. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | 파일이 수정되었으므로 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 수정된 파일이 Azure 파일 공유에 동기화되면 파일이 계층화됩니다. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | 시스템 리소스가 부족하여 파일을 계층화하지 못했습니다. | 오류가 계속 발생하면 시스템 리소스를 소모하는 애플리케이션 또는 커널 모드 드라이버를 조사합니다. |
| 0x8e5e03fe | -1906441218 | JET_errDiskIO | 클라우드 계층화 데이터베이스에 쓸 때 I/O 오류 때문에 파일을 계층화하지 못했습니다. | 오류가 지속되면 볼륨에서 chkdsk를 실행하고 스토리지 하드웨어를 확인합니다. |
| 0x8e5e0442 | -1906441150 | JET_errInstanceUnavailable | 클라우드 계층화 데이터베이스가 실행되고 있지 않기 때문에 파일을 계층화하지 못했습니다. | 이 문제를 해결하려면 FileSyncSvc 서비스 또는 서버를 다시 시작합니다. 오류가 지속되면 볼륨에서 chkdsk를 실행하고 스토리지 하드웨어를 확인합니다. |
| 0x80C80285 | -2160591493 | ECS_E_GHOSTING_SKIPPED_BY_CUSTOM_EXCLUSION_LIST | 파일 형식이 계층화에서 제외되므로 파일을 계층화할 수 없습니다. | 이 파일 형식을 사용하여 파일을 계층화하려면 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync 아래에 있는 GhostingExclusionList 레지스트리 설정을 수정합니다. |
| 0x80C86050 | -2160615504 | ECS_E_REPLICA_NOT_READY_FOR_TIERING | 현재 동기화 모드가 초기 업로드 또는 조정 모드이므로 파일을 계층화하지 못했습니다. | 아무 조치도 취할 필요가 없습니다. 동기화에서 초기 업로드 또는 조정이 완료되면 파일이 계층화됩니다. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>회수에 실패한 파일의 문제 해결 방법  
파일을 회수하지 못한 경우:
1. 이벤트 뷰어에서 Applications and Services\Microsoft\FileSync\Agent 아래에 있는 원격 분석, 운영 및 진단 이벤트 로그를 검토합니다.
    1. Azure 파일 공유에 파일이 있는지 확인합니다.
    2. 서버가 인터넷에 연결되어 있는지 확인합니다. 
    3. 서비스 MMC 스냅인을 열고 스토리지 동기화 에이전트 서비스(FileSyncSvc)가 실행 중인지 확인합니다.
    4. Azure 파일 동기화 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
        - 관리자 권한의 명령 프롬프트에서 `fltmc`를 실행합니다. StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

> [!NOTE]
> 파일이 회수에 실패하면 이벤트 ID 9006이 원격 분석 이벤트 로그에 1시간에 한 번 기록됩니다(오류 코드 하나당 이벤트 하나가 기록). [회수 오류 및 수정](#recall-errors-and-remediation) 섹션을 확인하여 오류 코드에 대한 수정 단계가 나열되는지 확인합니다.

### <a name="recall-errors-and-remediation"></a>회수 오류 및 수정

| HRESULT | HRESULT(10진) | 오류 문자열 | 문제 | 재구성 |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | I/O 시간 제한으로 인해 파일을 회수하지 못했습니다. 이 문제는 서버 리소스 제약 조건, 네트워크 연결 불량, Azure Storage 문제(예: 제한) 등 여러 가지 이유로 발생할 수 있습니다. | 아무 조치도 취할 필요가 없습니다. 오류가 몇 시간 동안 지속되면 지원 사례를 열어 주세요. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | 네트워크 문제로 인해 파일을 회수하지 못했습니다.  | 오류가 계속되면 Azure 파일 공유에 대한 네트워크 연결을 확인합니다. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | 서버 엔드포인트가 삭제되어 파일을 회수하지 못했습니다. | 이 문제를 해결하려면 [서버 엔드포인트를 삭제한 후 서버에서 계층화된 파일에 액세스할 수 없음](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)을 참조하세요. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | 액세스 거부 오류로 인해 파일을 회수하지 못했습니다. 스토리지 계정에 방화벽 및 가상 네트워크 설정이 사용하도록 설정되어 있고 서버에 스토리지 계정에 대한 액세스 권한이 없는 경우 이 문제가 발생할 수 있습니다. | 이 문제를 해결하려면 배포 가이드의 [방화벽 및 가상 네트워크 설정 구성](file-sync-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) 섹션에 설명된 단계에 따라 서버 IP 주소 또는 가상 네트워크를 추가합니다. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Azure 파일 공유에서 액세스할 수 없기 때문에 파일을 회수하지 못했습니다. | 이 문제를 해결하려면 파일이 Azure 파일 공유에 있는지 확인합니다. 파일이 Azure 파일 공유에 있으면 최신 Azure 파일 동기화 [에이전트 버전](file-sync-release-notes.md#supported-versions)으로 업그레이드합니다. |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | 스토리지 계정에 대한 권한 부여 실패로 인해 파일을 회수하지 못했습니다. | 이 문제를 해결하려면 [Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지](?tabs=portal1%252cazure-portal#troubleshoot-rbac) 확인합니다. |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Azure 파일 공유에 액세스할 수 없으므로 파일을 회수하지 못했습니다. | 파일 공유가 존재하고 액세스할 수 있는지 확인합니다. 파일 공유를 삭제하고 다시 만든 경우 [Azure 파일 공유가 삭제 후 다시 생성되었으므로 동기화에 실패했습니다.](?tabs=portal1%252cazure-portal#-2134375810) 섹션에 설명된 단계를 수행합니다. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | 시스템 리소스가 부족하여 파일을 회수하지 못했습니다. | 오류가 계속 발생하면 시스템 리소스를 소모하는 애플리케이션 또는 커널 모드 드라이버를 조사합니다. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | 메모리가 부족하여 파일을 회수하지 못했습니다. | 오류가 계속 발생하면 메모리 부족 상태를 유발하는 애플리케이션 또는 커널 모드 드라이버를 조사합니다. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | 디스크 공간이 부족하여 파일을 회수하지 못했습니다. | 이 문제를 해결하려면 파일을 다른 볼륨으로 이동하여 볼륨의 공간을 확보하거나, 볼륨의 크기를 늘리거나, Invoke-StorageSyncCloudTiering cmdlet을 사용하여 파일을 강제로 계층화합니다. |
| 0x80072f8f | -2147012721 | WININET_E_DECODING_FAILED | 서버가 Azure 파일 동기화 서비스의 응답을 디코딩할 수 없어 파일을 회수하지 못했습니다. | 일반적으로 이 오류는 네트워크 프록시가 Azure 파일 동기화 서비스의 응답을 수정하는 경우에 발생합니다. 프록시 구성을 확인하세요. |
| 0x80090352 | -2146892974 | SEC_E_ISSUING_CA_UNTRUSTED | 조직에서 TLS 종료 프록시를 사용하거나 악의적인 엔터티가 서버와 Azure 파일 동기화 서비스 간 트래픽을 가로채고 있어서 파일을 회수하지 못했습니다. | 조직에서 TLS 종료 프록시를 사용하기 때문에 충분히 예상할 수 있는 오류인 경우 [CERT_E_UNTRUSTEDROOT](#-2146762487) 오류에 대해 문서화된 단계에 따라 이 문제를 해결하세요. |
| 0x80c86047 | -2134351801 | ECS_E_AZURE_SHARE_SNAPSHOT_NOT_FOUND | 파일이 더 이상 Azure 파일 공유에 없는 파일 버전을 참조하므로 파일을 회수하지 못했습니다. | 이 문제는 계층화된 파일이 Windows Server의 백업에서 복원된 경우에 발생할 수 있습니다. 문제를 해결하려면 Azure 파일 공유의 스냅샷에서 파일을 복원합니다. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>서버 엔드포인트를 삭제한 후 서버에서 계층화된 파일에 액세스할 수 없음
서버에서 계층화된 파일은 서버 엔드포인트를 삭제하기 전에 파일이 회수되지 않은 경우 사용할 수 없게 됩니다.

계층화된 파일에 액세스할 수 없는 경우 기록되는 오류
- 파일을 동기화할 때 ItemResults 이벤트 로그에 오류 코드 -2147942467(0x80070043 - ERROR_BAD_NET_NAME)이 기록됩니다.
- 파일을 회수할 때 RecallResults 이벤트 로그에 오류 코드 -2134376393(0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND)이 기록됩니다.

다음 조건이 충족되면 계층화된 파일에 대한 액세스를 복원할 수 있습니다.
- 서버 엔드포인트가 지난 30일 이내에 삭제됨
- 클라우드 엔드포인트가 삭제되지 않음 
- 파일 공유가 삭제되지 않음
- 동기화 그룹이 삭제되지 않았습니다.

위의 조건을 충족하는 경우 30일 이내에 동일한 동기화 그룹 내의 서버에서 동일한 경로에 서버 엔드포인트를 다시 만들어 서버에 있는 파일에 대한 액세스를 복원할 수 있습니다. 

위의 조건이 충족되지 않으면 서버에서 계층화된 파일이 이제 분리되기 때문에 액세스를 복원하지 못할 수 있습니다. 분리된 계층화된 파일을 제거하려면 아래 지침을 따르세요.

**참고 사항**
- 서버에서 계층화된 파일에 액세스할 수 없는 경우 Azure 파일 공유에 직접 액세스하면 전체 파일에 액세스할 수 있습니다.
- 나중에 분리된 계층화된 파일을 방지하려면 서버 엔드포인트를 삭제할 때 [서버 엔드포인트 제거](file-sync-server-endpoint-delete.md)에 설명된 단계를 따르세요.

<a id="get-orphaned"></a>**분리된 계층화된 파일 목록을 가져오는 방법** 

1. Azure 파일 동기화 에이전트 버전 v5.1 이상이 설치되어 있는지 확인합니다.
2. 다음 PowerShell 명령을 실행하여 분리된 계층화된 파일을 나열합니다.
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. 파일이 삭제된 후 백업에서 복원해야 하는 경우를 대비해 OrphanTieredFiles.txt 출력 파일을 저장합니다.

<a id="remove-orphaned"></a>**분리된 계층화된 파일을 제거하는 방법** 

*옵션 1:* 분리된 계층화된 파일 삭제

이 옵션은 Windows Server에서 분리된 계층화된 파일을 삭제하지만 30일 후 다시 만들기로 인해 존재하거나 다른 동기화 그룹에 연결된 경우에는 서버 엔드포인트를 제거해야 합니다. 파일 충돌은 서버 엔드포인트를 다시 만들기 전에 Windows Server 또는 Azure 파일 공유에서 파일이 업데이트되는 경우에 발생합니다.

1. Azure 파일 동기화 에이전트 버전 v5.1 이상이 설치되어 있는지 확인합니다.
2. Azure 파일 공유 및 서버 엔드포인트 위치를 백업합니다.
3. [서버 엔드포인트 제거](file-sync-server-endpoint-delete.md)에 설명된 단계를 수행하여 동기화 그룹에서 서버 엔드포인트를 제거합니다(있는 경우).

> [!Warning]  
> Remove-StorageSyncOrphanedTieredFiles cmdlet을 사용하기 전에 서버 엔드포인트를 제거하지 않은 경우 서버에서 분리된 계층화된 파일을 삭제하면 Azure 파일 공유에서 전체 파일이 삭제됩니다. 

4. 다음 PowerShell 명령을 실행하여 분리된 계층화된 파일을 나열합니다.

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. 파일이 삭제된 후 백업에서 복원해야 하는 경우를 대비해 OrphanTieredFiles.txt 출력 파일을 저장합니다.
6. 다음 PowerShell 명령을 실행하여 분리된 계층화된 파일을 삭제합니다.

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**참고 사항** 
- 서버에서 수정되었지만 Azure 파일 공유에 동기화되지 않은 계층화된 파일이 삭제됩니다.
- 액세스할 수 있는 계층화된 파일(분리된 파일 아님)은 삭제되지 않습니다.
- 계층화되지 않은 파일은 서버에 유지됩니다.

7. 선택 사항: 3단계에서 삭제한 경우 서버 엔드포인트를 다시 만듭니다.

*옵션 2:* Azure 파일 공유를 탑재하고 서버에서 분리된 파일을 로컬로 복사

이 옵션은 서버 엔드포인트를 제거하지 않아도 되지만 전체 파일을 로컬로 복사하는 데 충분한 디스크 공간이 필요합니다.

1. 분리된 계층화된 파일을 포함하는 Windows Server에서 Azure 파일 공유를 [탑재](../files/storage-how-to-use-files-windows.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)합니다.
2. 다음 PowerShell 명령을 실행하여 분리된 계층화된 파일을 나열합니다.
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. OrphanTieredFiles.txt 출력 파일을 사용하여 서버에서 분리된 계층화된 파일을 식별합니다.
4. Azure 파일 공유에서 Windows Server로 전체 파일을 복사하여 분리된 계층화된 파일을 덮어씁니다.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>서버에서 예기치 않게 회수되는 파일을 문제 해결하는 방법  
바이러스 백신, 백업, 그리고 대량의 파일을 읽는 기타 애플리케이션은 오프라인 특성 건너뛰기 및 해당 파일의 내용 읽기 건너뛰기를 사용하지 않을 경우 의도치 않은 회수가 발생합니다. 이 옵션을 지원하는 제품에 대한 오프라인 파일 건너뛰기는 바이러스 백신 검사 또는 백업 작업 등을 수행하는 동안 의도치 않은 회수를 피하는 데 도움이 됩니다.

오프라인 파일 읽기를 건너뛰도록 솔루션을 구성하는 방법은 소프트웨어 공급업체에 문의하세요.

파일 탐색기에서 파일을 탐색할 경우 등의 기타 시나리오에서도 의도치 않은 회수가 발생할 수 있습니다. 서버의 파일 탐색기에서 클라우드 계층화 파일이 있는 폴더를 열면 의도치 않은 회수가 발생할 수 있습니다. 서버에서 바이러스 백신 솔루션이 사용 가능하게 설정된 경우에 이러한 회수가 발생하기 더 쉽습니다.

> [!NOTE]
>애플리케이션으로 인해 회수가 발생하는지를 확인하려면 원격 분석 이벤트 로그에서 이벤트 ID 9059를 사용합니다. 서버 엔드포인트의 애플리케이션 회수 분포를 제공하는 이 이벤트는 1시간마다 기록됩니다.

### <a name="process-exclusions-for-azure-file-sync"></a>Azure 파일 동기화 대한 프로세스 제외

Azure 파일 동기화에서 액세스하는 파일의 검사를 건너뛰도록 바이러스 백신 또는 다른 애플리케이션을 구성하려면 다음 프로세스를 제외하도록 구성합니다.

- C:\Program Files\Azure\StorageSyncAgent\AfsAutoUpdater.exe
- C:\Program Files\Azure\StorageSyncAgent\FileSyncSvc.exe
- C:\Program Files\Azure\StorageSyncAgent\MAAgent\MonAgentLauncher.exe
- C:\Program Files\Azure\StorageSyncAgent\MAAgent\MonAgentHost.exe
- C:\Program Files\Azure\StorageSyncAgent\MAAgent\MonAgentManager.exe
- C:\Program Files\Azure\StorageSyncAgent\MAAgent\MonAgentCore.exe
- C:\Program Files\Azure\StorageSyncAgent\MAAgent\Extensions\XSyncMonitoringExtension\AzureStorageSyncMonitor.exe

### <a name="tls-12-required-for-azure-file-sync"></a>Azure 파일 동기화에 TLS 1.2 필요

[레지스트리 설정](/windows-server/security/tls/tls-registry-settings)에서 서버의 TLS 설정을 확인할 수 있습니다. 

프록시를 사용하는 경우 프록시의 설명서를 참조하여 TLS 1.2를 사용하도록 구성되어 있는지 확인합니다.

## <a name="general-troubleshooting"></a>일반적인 문제 해결
서버에서 Azure 파일 동기화에 문제가 발생하는 경우 먼저 다음 단계를 완료합니다.
1. 이벤트 뷰어에서 원격 분석, 운영 및 진단 이벤트 로그를 검토합니다.
    - 동기화, 계층화 및 회수 문제가 Applications and Services\Microsoft\FileSync\Agent의 원격 분석 진단 및 운영 이벤트 로그에 기록됩니다.
    - 서버 관리(예: 구성 설정)와 관련된 문제는 Applications and Services\Microsoft\FileSync\Management의 운영 및 진단 이벤트 로그에 기록됩니다.
2. 서버에서 Azure 파일 동기화 서비스가 실행 중인지 확인합니다.
    - 서비스 MMC 스냅인을 열고 Storage 동기화 에이전트 서비스(FileSyncSvc)가 실행 중인지 확인합니다.
3. Azure 파일 동기화 필터 드라이버(StorageSync.sys 및 StorageSyncGuard.sys)가 실행 중인지 확인합니다.
    - 관리자 권한의 명령 프롬프트에서 `fltmc`를 실행합니다. StorageSync.sys 및 StorageSyncGuard.sys 파일 시스템 필터 드라이버가 나열되는지 확인합니다.

문제가 해결되지 않으면 AFSDiag 도구를 실행하고 추가 진단을 위해 해당 사례에 할당된 지원 엔지니어에게 .zip 파일 출력을 보냅니다.

AFSDiag를 실행하려면 아래 단계를 수행합니다.

에이전트 버전 v11 이상의 경우:
1. 관리자 권한으로 PowerShell 창을 열고 다음 명령을 실행합니다(각 명령 후 Enter 키 누름).

    > [!NOTE]
    >AFSDiag는 로그를 수집하기 전에 출력 디렉터리를 만든 후 그 안에 임시 폴더를 만들고 실행 후에 임시 폴더를 삭제합니다. 데이터를 포함하지 않는 출력 위치를 지정하세요.
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. 이슈를 재현합니다. 작업을 완료하면 **D** 키를 입력합니다.
3. 로그 및 추적 파일을 포함하는 .zip 파일은 사용자가 지정한 출력 디렉터리에 저장됩니다. 

에이전트 버전 v10 이전의 경우:
1. AFSDiag 출력이 저장될 디렉터리를 만듭니다(예: C:\output).
    > [!NOTE]
    >AFSDiag는 로그를 수집하기 전에 출력 디렉터리의 모든 내용을 삭제합니다. 데이터를 포함하지 않는 출력 위치를 지정하세요.
2. 관리자 권한으로 PowerShell 창을 열고 다음 명령을 실행합니다(각 명령 후 Enter 키 누름).

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Azure 파일 동기화 커널 모드 추적 수준에 대해 **1** 을 입력하고(더 자세한 추적을 만들기 위해 다르게 지정되지 않은 경우) Enter 키를 누릅니다.
4. Azure 파일 동기화 사용자 모드 추적 수준에 대해 **1** 을 입력하고(더 자세한 추적을 만들기 위해 다르게 지정되지 않은 경우) Enter 키를 누릅니다.
5. 이슈를 재현합니다. 작업을 완료하면 **D** 키를 입력합니다.
6. 로그 및 추적 파일을 포함하는 .zip 파일은 사용자가 지정한 출력 디렉터리에 저장됩니다.


## <a name="see-also"></a>참고 항목
- [Azure 파일 동기화 모니터링](file-sync-monitoring.md)
- [Windows에서 Azure Files 문제 해결](../files/storage-troubleshoot-windows-file-connection-problems.md)
- [Linux에서 Azure Files 문제 해결](../files/storage-troubleshoot-linux-file-connection-problems.md)
