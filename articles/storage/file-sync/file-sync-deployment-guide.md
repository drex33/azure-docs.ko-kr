---
title: Azure 파일 동기화 배포 | Microsoft Docs
description: Azure Portal, PowerShell 또는 Azure CLI를 사용하여 처음부터 끝까지 Azure 파일 동기화를 배포하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 42ca317d4838513bb23bed9f5aa1028cb964d382
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113768577"
---
# <a name="deploy-azure-file-sync"></a>Azure 파일 동기화 배포
Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서에 설명된 단계를 완료하기 전에 [Azure Files 배포에 대한 계획](../files/storage-files-planning.md) 및 [Azure 파일 동기화 배포에 대한 계획](file-sync-planning.md)을 읽어보는 것이 좋습니다.

## <a name="prerequisites"></a>필수 구성 요소

# <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure 파일 동기화를 배포하려는 동일한 지역에 Azure 파일 공유에 대한 자세한 내용은 다음을 참조하세요.
    - [지역 가용성](file-sync-planning.md#azure-file-sync-region-availability)에서 Azure 파일 동기화를 참조하세요.
    - [파일 공유 만들기](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)에서 파일 공유를 만드는 방법에 대한 단계별 설명을 참조하세요.
1. Azure 파일 동기화와 동기화할 Windows Server 또는 Windows Server 클러스터의 지원되는 인스턴스 하나 이상. 지원되는 Windows Server 버전 및 권장 시스템 자원에 대한 자세한 내용은 [Windows 파일 서버 고려 사항](file-sync-planning.md#windows-file-server-considerations)을 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Azure 파일 동기화를 배포하려는 동일한 지역에 Azure 파일 공유에 대한 자세한 내용은 다음을 참조하세요.
    - [지역 가용성](file-sync-planning.md#azure-file-sync-region-availability)에서 Azure 파일 동기화를 참조하세요.
    - [파일 공유 만들기](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)에서 파일 공유를 만드는 방법에 대한 단계별 설명을 참조하세요.
1. Azure 파일 동기화와 동기화할 Windows Server 또는 Windows Server 클러스터의 지원되는 인스턴스 하나 이상. 지원되는 Windows Server 버전 및 권장 시스템 자원에 대한 자세한 내용은 [Windows 파일 서버 고려 사항](file-sync-planning.md#windows-file-server-considerations)을 참조하세요.

1. Az PowerShell 모듈은 PowerShell 5.1 또는 PowerShell 6 이상과 함께 사용할 수 있습니다. Windows가 아닌 시스템을 포함하여 지원되는 모든 시스템에서 Azure 파일 동기화용 Az PowerShell 모듈을 사용할 수 있지만 등록 중인 Windows Server 인스턴스에서 항상 서버 등록 cmdlet이 실행되어야 합니다(직접 또는 PowerShell 원격 기능을 통해 실행 가능). Windows Server 2012 R2에서 **$PSVersionTable** 개체의 **PSVersion** 속성 값을 확인하여 PowerShell 5.1.\* 이상을 실행하고 있는지 확인할 수 있습니다.

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Windows Server 2012 R2를 처음 설치하면 대부분이 그렇듯이, **PSVersion** 값이 5.1.\*보다 낮으면 [WMF(Windows Management Framework) 5.1](https://www.microsoft.com/download/details.aspx?id=54616)을 다운로드한 후 설치하여 쉽게 업그레이드할 수 있습니다. Windows Server 2012 R2에 대해 다운로드하고 설치할 적절한 패키지는 **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu** 입니다. 

    PowerShell 6 이상은 지원되는 모든 시스템에서 사용할 수 있으며 [GitHub 페이지](https://github.com/PowerShell/PowerShell#get-powershell)를 통해 다운로드할 수 있습니다. 

    > [!Important]  
    > PowerShell에서 직접 등록하는 대신 서버 등록 UI를 사용하려는 경우 PowerShell 5.1을 사용해야 합니다.

1. PowerShell 5.1을 사용하도록 선택한 경우 .NET 4.7.2 이상이 설치되어 있어야 합니다. 시스템의 [.NET Framework 버전 및 종속성](/dotnet/framework/migration-guide/versions-and-dependencies)에 대해 자세히 알아보세요.

    > [!Important]  
    > Windows Server Core에 .NET 4.7.2 이상을 설치하는 경우 `quiet` 및 `norestart` 플래그를 사용하여 설치해야 합니다. 그러지 않으면 설치가 실패합니다. 예를 들어 .NET 4.8을 설치하는 경우 명령은 다음과 같습니다.
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. Az PowerShell. [Azure PowerShell 설치 및 구성](/powershell/azure/install-Az-ps)의 안내에 따라 설치할 수 있습니다.
     
    > [!Note]  
    > 이제 Az.StorageSync 모듈은 Az PowerShell 모듈을 설치할 때 자동으로 설치됩니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Azure 파일 동기화를 배포하려는 동일한 지역에 Azure 파일 공유에 대한 자세한 내용은 다음을 참조하세요.
    - [지역 가용성](file-sync-planning.md#azure-file-sync-region-availability)에서 Azure 파일 동기화를 참조하세요.
    - [파일 공유 만들기](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)에서 파일 공유를 만드는 방법에 대한 단계별 설명을 참조하세요.
1. Azure 파일 동기화와 동기화할 Windows Server 또는 Windows Server 클러스터의 지원되는 인스턴스 하나 이상. 지원되는 Windows Server 버전 및 권장 시스템 자원에 대한 자세한 내용은 [Windows 파일 서버 고려 사항](file-sync-planning.md#windows-file-server-considerations)을 참조하세요.

1. [Azure CLI 설치](/cli/azure/install-azure-cli)

   원한다면 Azure Cloud Shell을 사용하여 이 자습서의 단계를 완료해도 됩니다.  Azure Cloud Shell은 브라우저를 통해 사용할 수 있는 대화형 셸 환경입니다.  다음 방법 중 하나를 사용하여 Cloud Shell을 시작합니다.

   - 코드 블록의 오른쪽 위 모서리에서 **사용** 을 선택합니다. **사용해 보기** 를 클릭하면 Azure Cloud Shell이 열리지만 코드를 Cloud Shell에 자동으로 복사하지는 않습니다.

   - [https://shell.azure.com](https://shell.azure.com)으로 이동하여 Cloud Shell 열기

   - [Azure Portal](https://portal.azure.com)의 오른쪽 위 모서리에 있는 메뉴 모음에서 **Cloud Shell** 단추 선택

1. 로그인합니다.

   로컬에 설치된 CLI를 사용하는 경우 [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 로그인합니다.

   ```azurecli
   az login
   ```

    터미널에 표시된 단계에 따라 인증 프로세스를 완료합니다.

1. [az filesync](/cli/azure/storagesync) Azure CLI 확장을 설치합니다.

   ```azurecli
   az extension add --name storagesync
   ```

   **storagesync** 확장 참조를 설치하면 다음 경고가 표시됩니다.

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Azure 파일 동기화에 사용할 Windows Server 준비
장애 조치(failover) 클러스터의 각 서버 노드를 포함하여 Azure 파일 동기화에 사용할 각 서버에 대해 **Internet Explorer 보안 강화 구성** 을 사용하지 않도록 설정합니다. 초기 서버 등록에만 필요합니다. 서버가 등록된 후에 사용하도록 다시 설정할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
> [!Note]  
> Windows Server Core에 Azure 파일 동기화를 배포하는 경우 이 단계를 건너뛸 수 있습니다.

1. 서버 관리자를 엽니다.
2. **로컬 서버** 를 클릭합니다.  
    ![서버 관리자 UI 왼쪽에 있는 "로컬 서버"](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-1.png)
3. **속성** 하위 창에서 **IE 보안 강화 구성** 링크를 선택합니다.  
    ![서버 관리자 UI의 "IE 보안 강화 구성" 창](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-2.png)
4. **Internet Explorer 보안 강화 구성** 대화 상자에서 **관리자** 와 **사용자** 에 대해 **사용 안 함** 을 선택합니다.  
    ![“해제"가 선택된 Internet Explorer 보안 강화 구성 팝업 창](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Internet Explorer 보안 강화 구성을 해제하려면 관리자 권한 PowerShell 세션에서 다음 명령을 실행합니다.

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Portal 또는 PowerShell에 대한 지침을 따릅니다.

---

## <a name="deploy-the-storage-sync-service"></a>스토리지 동기화 서비스 배포 
Azure 파일 동기화 배포에서 가장 먼저 할 일은 선택한 그룹의 리소스 그룹에 **스토리지 동기화 서비스** 리소스를 배치하는 것입니다. 되도록이면 최소한으로 프로비전하는 것이 좋습니다. 서버와 이 리소스 사이에 트러스트 관계를 만들 것이므로 서버를 한 스토리지 동기화 서비스에만 등록할 수 있습니다. 결과적으로, 서버 그룹을 분리하는 데 필요한 만큼 스토리지 동기화 서비스를 배포하는 것이 좋습니다. 서로 다른 스토리지 동기화 서비스의 서버를 서로 동기화할 수 없다는 점을 기억해야 합니다.

> [!Note]
> 스토리지 동기화 서비스는 배포된 구독 및 리소스 그룹에서 액세스 권한을 상속합니다. 누가 액세스 권한을 갖고 있는지 신중하게 확인하는 것이 좋습니다. 쓰기 액세스 권한이 있는 엔터티는 등록된 서버에서 이 스토리지 동기화 서비스로 새 파일 집합의 동기화를 시작하고, 파일에 액세스할 수 있는 Azure Storage로 데이터가 흐르도록 만들 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
스토리지 동기화 서비스를 배포하려면 [Azure Portal](https://portal.azure.com/)로 이동하고 *리소스 만들기* 를 클릭한 다음 Azure 파일 동기화를 검색합니다. 검색 결과에서 **Azure 파일 동기화** 를 선택한 다음 **만들기** 를 선택하여 **스토리지 동기화 배포** 탭을 엽니다.

열리는 창에 다음 정보를 입력합니다.

- **이름**: 스토리지 동기화 서비스의 고유 이름(지역별)입니다.
- **구독**: 스토리지 동기화 서비스를 만들 구독입니다. 조직의 구성 전략에 따라 하나 이상의 구독에 대한 액세스 권한이 있을 수 있습니다. Azure 구독은 각 클라우드 서비스(예: Azure Files)에 대한 비용을 청구하는 가장 기본적인 컨테이너입니다.
- **리소스 그룹**: 리소스 그룹은 스토리지 계정 또는 스토리지 동기화 서비스와 같은 Azure 리소스의 논리적 그룹입니다. Azure 파일 동기화에 대해 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용할 수 있습니다(HR 리소스나 특정 프로젝트에 리소스를 그룹화하는 등, 리소스 그룹을 조직의 리소스를 로컬로 격리하는 컨테이너로 사용하는 것이 권장됨).
- **위치**: Azure 파일 동기화를 배포하려는 지역입니다. 이 목록에는 지원되는 지역만 나와 있습니다.

작업이 끝나면 **만들기** 를 선택하여 스토리지 동기화 서비스를 배포합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
`<Az_Region>`, `<RG_Name>` 및 `<my_storage_sync_service>`를 고유한 값으로 바꾼 후 다음 명령을 사용하여 스토리지 동기화 서비스를 만들고 배포합니다.

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Portal 또는 PowerShell에 대한 지침을 따릅니다.

---

## <a name="install-the-azure-file-sync-agent"></a>Azure 파일 동기화 에이전트 설치
Azure 파일 동기화 에이전트는 Windows Server가 Azure 파일 공유와 동기화되도록 하는 다운로드 가능 패키지입니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)
[Microsoft 다운로드 센터](https://go.microsoft.com/fwlink/?linkid=858257)에서 에이전트를 다운로드할 수 있습니다. 다운로드가 완료되면 MSI 패키지를 두 번 클릭하여 Azure 파일 동기화 에이전트 설치를 시작합니다.

> [!Important]  
> 장애 조치(Failover) 클러스터에서 Azure 파일 동기화를 사용하려는 경우 Azure 파일 동기화 에이전트를 클러스터의 모든 노드에 설치해야 합니다. Azure 파일 동기화에서 작동하도록 클러스터의 각 노드를 등록해야 합니다.

다음을 수행하는 것이 좋습니다.
- 문제 해결 및 서버 유지 관리를 간소화하려면 기본 설치 경로(C:\Program Files\Azure\StorageSyncAgent)를 유지하세요.
- Azure 파일 동기화를 최신 상태로 유지하도록 Microsoft 업데이트를 사용하도록 설정하세요. 기능 업데이트 및 핫픽스를 비롯한 Azure 파일 동기화 에이전트에 대한 모든 업데이트는 Microsoft 업데이트에서 수행됩니다. Azure 파일 동기화에 대한 최신 업데이트를 설치하는 것이 좋습니다. 자세한 내용은 [Azure 파일 동기화 업데이트 정책](file-sync-planning.md#azure-file-sync-agent-update-policy)을 참조하세요.

Azure 파일 동기화 에이전트 설치를 마치면 서버 등록 UI가 자동으로 열립니다. 등록하려면 스토리지 동기화 서비스가 있어야 합니다. 스토리지 동기화 서비스를 만드는 방법은 다음 섹션을 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
다음 PowerShell 코드를 실행하여 사용하는 OS에 맞는 Azure 파일 동기화 에이전트 버전을 다운로드하고 시스템에 설치합니다.

> [!Important]  
> 장애 조치(Failover) 클러스터에서 Azure 파일 동기화를 사용하려는 경우 Azure 파일 동기화 에이전트를 클러스터의 모든 노드에 설치해야 합니다. Azure 파일 동기화에서 작동하도록 클러스터의 각 노드를 등록해야 합니다.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Portal 또는 PowerShell에 대한 지침을 따릅니다.

---

## <a name="register-windows-server-with-storage-sync-service"></a>스토리지 동기화 서비스에 Windows Server 등록
스토리지 동기화 서비스에 Windows Server를 등록하면 서버(또는 클러스터)와 스토리지 동기화 서비스 간에 트러스트 관계가 설정됩니다. 서버는 한 스토리지 동기화 서비스에만 등록할 수 있으며 동일한 스토리지 동기화 서비스에 연결된 다른 서버 및 Azure 파일 공유와 동기화할 수 있습니다.

> [!Note]
> 서버 등록 시 Azure 자격 증명을 사용하여 스토리지 동기화 서비스와 Windows Server 사이에 트러스트 관계가 생성되지만, 그 이후 서버는 고유의 ID를 만들어서 사용하며 이 ID는 서버의 등록 상태가 유지되고 현재 공유 액세스 서명 토큰(Storage SAS)이 유효한 동안에는 유효성을 유지합니다. 서버가 등록되지 않으면 새 SAS 토큰을 발급할 수 없으며, 따라서 Azure 파일 공유에 액세스하는 서버 기능이 제거되고 모든 동기화가 중지됩니다.

서버를 등록하는 관리자는 지정된 스토리지 동기화 서비스에 대한 관리 역할 **소유자** 또는 **기여자** 의 구성원이어야 합니다. 이 설정은 스토리지 동기화 서비스에 대한 Azure Portal의 **액세스 제어(IAM)** 에서 구성할 수 있습니다.

또한 서버를 등록할 수 있는 관리자와 스토리지 동기화 서비스에서 동기화를 구성할 수 있는 관리자를 구별할 수도 있습니다. 이를 위해서는 서버 등록만 허용된 관리자를 나열하고 사용자 지정 역할에 다음 권한을 부여하는 사용자 지정 역할을 만들어야 합니다.

* "Microsoft.StorageSync/storageSyncServices/registeredServers/write"
* "Microsoft.StorageSync/storageSyncServices/read"
* "Microsoft.StorageSync/storageSyncServices/workflows/read"
* "Microsoft.StorageSync/storageSyncServices/workflows/operations/read"

# <a name="portal"></a>[포털](#tab/azure-portal)
Azure 파일 동기화 에이전트 설치 후 서버 등록 UI가 자동으로 열립니다. 그렇지 않은 경우 파일 위치(C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe)에서 수동으로 열 수 있습니다. 서버 등록 UI가 열리면 **로그인** 을 선택하여 시작합니다.

로그인한 후에 다음 정보를 묻는 메시지가 나타납니다.

![서버 등록 UI 스크린샷](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure 구독**: 스토리지 동기화 서비스가 포함된 구독입니다([스토리지 동기화 서비스 배포](#deploy-the-storage-sync-service) 참조). 
- **리소스 그룹**: 스토리지 동기화 서비스를 포함하는 리소스 그룹입니다.
- **스토리지 동기화 서비스**: 등록하려는 스토리지 동기화 서비스의 이름입니다.

적절한 정보를 선택한 후 **등록** 을 선택하여 서버 등록을 완료합니다. 등록 프로세스의 일부로 추가 로그인을 요구하는 메시지가 표시됩니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure Portal 또는 PowerShell에 대한 지침을 따릅니다.

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>동기화 그룹 및 클라우드 엔드포인트 만들기
동기화 그룹은 파일 집합에 대한 동기화 토폴로지를 정의합니다. 동기화 그룹 내 엔드포인트는 서로 동기화된 상태를 유지합니다. 동기화 그룹은 Azure 파일 공유를 나타내는 하나의 클라우드 엔드포인트와 하나 이상의 서버 엔드포인트를 포함해야 합니다. 서버 엔드포인트는 등록된 서버의 경로를 나타냅니다. 한 서버가 여러 동기화 그룹에서 서버 엔드포인트를 가질 수 있습니다. 원하는 동기화 토폴로지를 적절하게 설명하는 데 필요한 만큼 동기화 그룹을 만들 수 있습니다.

클라우드 엔드포인트는 Azure 파일 공유에 대한 포인터입니다. 모든 서버 엔드포인트는 클라우드 엔드포인트와 동기화되어 클라우드 엔드포인트를 허브로 만듭니다. Azure 파일 공유에 대한 스토리지 계정은 스토리지 동기화 서비스와 동일한 영역에 있어야 합니다. Azure 파일 공유 전체가 동기화되지만, 한 가지 예외가 있습니다. NTFS 볼륨의 숨겨진 "시스템 볼륨 정보" 폴더와 비슷한 특수 폴더가 프로비전됩니다. 이 디렉터리를 ".SystemShareInformation"이라고 부릅니다. 이 디렉터리에는 다른 엔드포인트와 동기화되지 않는 중요한 동기화 메타데이터가 포함되어 있습니다. 이 디렉터리를 사용하거나 삭제하지 마세요!

> [!Important]  
> 동기화 그룹의 클라우드 엔드포인트 또는 서버 엔드포인트를 변경할 수 있고, 파일이 동기화 그룹의 다른 엔드포인트와 동기화되도록 할 수 있습니다. 클라우드 엔드포인트(Azure 파일 공유)를 직접 변경하는 경우 변경 사항은 먼저 Azure 파일 동기화 변경 내용 검색 작업으로 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩만 시작됩니다. 자세한 내용은 [Azure Files 질문과 대답](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#afs-change-detection)을 참조하세요.

클라우드 엔드포인트를 만드는 관리자는 클라우드 엔드포인트가 가리키는 Azure 파일 공유가 포함된 스토리지 계정에 대한 관리 역할 **소유자** 의 구성원이어야 합니다. 이 설정은 스토리지 계정에 대한 Azure Portal의 **액세스 제어(IAM)** 에서 구성할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
동기화 그룹을 만들려면 [Azure Portal](https://portal.azure.com/)에서 스토리지 동기화 서비스로 이동한 후 **+동기화 그룹** 을 선택합니다.

![Azure Portal에서 새 동기화 그룹 만들기](media/storage-sync-files-deployment-guide/create-sync-group-part-1.png)

열리는 창에서 다음 정보를 입력하여 클라우드 엔드포인트가 있는 동기화 그룹을 만듭니다.

- **동기화 그룹 이름**: 만들 동기화 그룹의 이름입니다. 이 이름은 스토리지 동기화 서비스 내에서 고유해야 하지만 사용자에게 논리적인 어떤 이름도 될 수 있습니다.
- **구독**: [스토리지 동기화 서비스 배포](#deploy-the-storage-sync-service)에서 스토리지 동기화 서비스를 배포한 구독입니다.
- **스토리지 계정**: **스토리지 계정 선택** 을 선택하면 동기화할 Azure 파일 공유가 있는 스토리지 계정을 선택할 수 있는 또 다른 창이 나타납니다.
- **Azure 파일 공유**: 동기화할 Azure 파일 공유의 이름입니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
동기화 그룹을 만들려면 다음 PowerShell을 실행합니다. `<my-sync-group>`을 원하는 동기화 그룹 이름으로 바꿉니다.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

동기화 그룹이 만들어지면 클라우드 엔드포인트를 만들 수 있습니다. `<my-storage-account>` 및 `<my-file-share>`를 예상 값으로 바꿉니다.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az storagesync sync-group](/cli/azure/storagesync/sync-group#az_storagesync_sync_group_create) 명령을 사용하여 새 동기화 그룹을 만듭니다.  리소스 그룹을 모든 CLI 명령에 대해 기본값으로 설정하려면 [az configure](/cli/azure/reference-index#az_configure)를 사용합니다.

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

[az storagesync sync-group cloud-endpoint](/cli/azure/storagesync/sync-group/cloud-endpoint#az_storagesync_sync_group_cloud_endpoint_create) 명령을 사용하여 새 클라우드 엔드포인트를 만듭니다.

```azurecli
az storagesync sync-group cloud-endpoint create --resource-group myResourceGroup \
                                                --storage-sync-service myStorageSyncServiceName \
                                                --sync-group-name mySyncGroupName \
                                                --name myNewCloudEndpointName \
                                                --storage-account mystorageaccountname \
                                                --azure-file-share-name azure-file-share-name
```

---

## <a name="create-a-server-endpoint"></a>서버 엔드포인트 만들기
서버 엔드포인트는 서버 볼륨의 폴더와 같이 등록된 서버의 특정 위치를 나타냅니다. 서버 엔드포인트에는 다음 조건이 적용됩니다.

- 서버 엔드포인트는 탑재된 공유가 아닌 등록된 서버의 경로여야 합니다. NAS(Network Attached Storage)는 지원되지 않습니다.
- 서버 엔드포인트는 시스템 볼륨에 있을 수 있지만 시스템 볼륨의 서버 엔드포인트는 클라우드 계층을 사용하지 않을 수 있습니다.
- 볼륨에 서버 엔드포인트를 설정한 후 경로 또는 드라이브 문자를 변경하는 것은 지원되지 않습니다. 등록된 서버에서 최종 경로를 사용하고 있는지 확인합니다.
- 등록된 서버는 여러 서버 엔드포인트를 지원할 수 있지만 동기화 그룹은 지정된 시간에 등록된 서버당 하나의 서버 엔드포인트만 가질 수 있습니다. 동기화 그룹 내의 다른 서버 엔드포인트는 서로 다른 등록된 서버에 있어야 합니다.

[!INCLUDE [storage-files-sync-create-server-endpoint](../../../includes/storage-files-sync-create-server-endpoint.md)]

## <a name="configure-firewall-and-virtual-network-settings"></a>방화벽 및 가상 네트워크 설정 구성

### <a name="portal"></a>포털
방화벽 및 가상 네트워크 설정에서 작동하도록 Azure 파일 동기화를 구성하려면 다음을 수행하세요.

1. Azure Portal에서 보호하려는 스토리지 계정으로 이동합니다.
1. 왼쪽 메뉴에서 **네트워킹** 을 선택합니다.
1. **선택한 네트워크** 아래 **다음에서 액세스 허용** 에서.
1. **주소 범위** 섹션 아래에 서버 IP 또는 가상 네트워크가 나열되어 있는지 확인합니다.
1. **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용** 이 선택되어 있는지 확인합니다.
1. **저장** 을 선택하여 설정을 저장합니다.

    ![Azure 파일 동기화를 사용하도록 방화벽 및 가상 네트워크 설정 구성](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Azure 파일 동기화를 사용한 온보딩
전체 파일 충실도와 ACL(액세스 제어 목록)을 유지하면서 가동 중지 시간 없이 Azure 파일 동기화에 처음 온보딩하는 권장 단계는 다음과 같습니다.
 
1. 스토리지 동기화 서비스를 배포합니다.
1. 동기화 그룹을 생성합니다.
1. 전체 데이터 집합이 있는 서버에 Azure 파일 동기화 에이전트를 설치합니다.
1. 해당 서버를 등록하고 공유에 서버 엔드포인트를 만듭니다. 
1. 전체 업로드를 Azure 파일 공유(클라우드 엔드포인트)에 동기화합니다.  
1. 초기 업로드가 완료된 후 나머지 서버에 각각 Azure 파일 동기화 에이전트를 설치합니다.
1. 나머지 서버에 각각 새 파일 공유를 만듭니다.
1. 원하는 경우 클라우드 계층화 정책을 사용하여 새 파일 공유에 서버 엔드포인트를 만듭니다. 이 단계를 수행하려면 초기 설정에 사용할 수 있는 추가 스토리지가 필요합니다.
1. 실제 데이터 전송 없이 Azure 파일 동기화 에이전트가 전체 네임스페이스를 신속하게 복원하도록 합니다. 전체 네임스페이스가 동기화된 후 동기화 엔진은 서버 엔드포인트에 대한 클라우드 계층화 정책에 따라 로컬 디스크 공간을 채웁니다. 
1. 동기화를 완료하고 필요에 따라 토폴로지를 테스트합니다. 
1. 사용자와 애플리케이션을 이 새로운 공유로 리디렉션합니다.
1. 필요에 따라 서버에서 중복 공유를 삭제할 수 있습니다.
 
초기 온보딩을 위한 추가 스토리지가 없으며 기존 공유에 연결하려는 경우 Azure 파일 공유의 데이터를 사전 시드할 수 있습니다. 이 접근 방법은 가동 중지 시간이 허용되고 초기 온보딩 프로세스 중에 서버 공유의 데이터가 변경되지 않는 것이 확실한 경우에만 사용하는 것이 좋습니다. 
 
1. 온보딩 프로세스 중에 서버의 데이터를 변경할 수 없도록 합니다.
1. SMB를 통한 데이터 전송 도구를 사용하여 서버 데이터로 Azure 파일 공유를 사전 시드합니다. 예를 들어 Robocopy를 사용할 수 있습니다. 또한 REST를 통해 AzCopy을 사용할 수도 있습니다. 적절한 스위치와 함께 AzCopy를 사용하여 ACL 타임스탬프 및 특성을 유지해야 합니다.
1. 원하는 서버 엔드포인트에서 기존 공유를 가리켜 Azure 파일 동기화 토폴로지를 만듭니다.
1. 모든 엔드포인트에서 동기화가 조정 프로세스를 완료하도록 합니다. 
1. 조정이 완료되면 변경 내용을 위해 공유를 열 수 있습니다.
 
현재 사전 시드 방식에는 몇 가지 제한 사항이 있습니다. 
- 동기화 토폴로지가 완전히 작동되어 실행하기 전의 서버 데이터 변경으로 인해 서버 엔드포인트에서 충돌이 발생할 수 있습니다.  
- 클라우드 엔드포인트를 만든 후 Azure 파일 동기화는 초기 동기화를 시작하기 전에 클라우드에서 파일을 검색하는 프로세스를 실행합니다. 이 프로세스를 완료하는 데 걸리는 시간은 네트워크 속도, 사용 가능한 대역폭, 파일 및 폴더 수와 같은 다양한 요인에 따라 달라집니다. 미리 보기 릴리스의 대략적인 추정에서 검색 프로세스는 약 10개 파일/초의 속도로 실행됩니다. 따라서 클라우드에 데이터를 사전 시드할 경우 사전 시드가 빠르게 실행되더라도 시스템이 정상적으로 실행되기까지 오랜 시간이 걸릴 수 있습니다.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>이전 버전 및 VSS(볼륨 섀도 복사본 서비스)를 통한 셀프 서비스 복원

> [!IMPORTANT]
> 다음 정보는 스토리지 동기화 에이전트 버전 9 이상에서만 사용할 수 있습니다. 9보다 낮은 버전에는 StorageSyncSelfService cmdlet이 없습니다.

이전 버전은 볼륨의 서버 측 VSS 스냅샷을 활용하여 복원 가능한 버전의 파일을 SMB 클라이언트에 제공하는 Windows 기능입니다.
이를 통해 IT 관리자의 복원에 의존하지 않고 정보 근로자가 직접 셀프 서비스 복원이라고 하는 강력한 시나리오를 수행할 수 있습니다.

VSS 스냅샷 및 이전 버전은 Azure 파일 동기화와 독립적으로 작동합니다. 그러나 클라우드 계층화는 호환 모드로 설정되어야 합니다. 동일한 볼륨에 여러 Azure 파일 동기화 서버 엔드포인트가 있을 수 있습니다. 클라우드 계층화를 계획하거나 사용 중인 서버 엔드포인트가 하나라도 있으면 각 볼륨에 대해 다음 PowerShell 호출을 수행해야 합니다.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

VSS 스냅샷은 전체 볼륨에 대해 생성됩니다. 기본적으로 스냅샷을 저장할 공간이 충분하면 주어진 볼륨에 대해 최대 64개의 스냅샷이 존재할 수 있습니다. VSS는 이를 자동으로 처리합니다. 기본 스냅샷 일정은 월요일부터 금요일까지 하루에 두 개의 스냅샷을 생성합니다. 이 일정은 Windows 예약 작업을 통해 구성할 수 있습니다. 위의 PowerShell cmdlet은 다음 두 가지 작업을 수행합니다.
1. 지정된 볼륨에서 Azure 파일 동기화 클라우드 계층화를 구성하여 이전 버전과 호환되도록 하고 파일이 서버의 클라우드에 계층화된 경우에도 이전 버전에서 복원할 수 있도록 보장합니다. 
1. 기본 VSS 일정을 사용하도록 설정합니다. 그런 다음 나중에 수정할 수 있습니다. 

> [!Note]  
> 유의해야 할 두 가지 중요한 사항은 다음과 같습니다.
>- -Force 매개 변수를 사용하고 VSS가 현재 활성화된 경우 현재 VSS 스냅샷 일정을 덮어쓰고 기본 일정으로 바꿉니다. cmdlet을 실행하기 전에 사용자 지정 구성을 저장해야 합니다.
> - 클러스터 노드에서 이 cmdlet을 사용하는 경우 클러스터의 다른 모든 노드에서도 실행해야 합니다. 

다음 cmdlet을 실행하여 셀프 서비스 복원 호환성이 사용되는지 확인할 수 있습니다.

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

서버의 모든 볼륨과 각각에 대한 클라우드 계층화가 호환 가능한 일 수를 나열합니다. 이 수치는 볼륨당 가능한 최대 스냅샷 수 및 기본 스냅샷 일정을 기반으로 자동 계산됩니다. 따라서 기본적으로 정보 근로자에게 제공된 모든 이전 버전을 사용하여 복원할 수 있습니다. 더 많은 스냅샷을 찍도록 기본 일정을 변경하는 경우에도 마찬가지입니다.
그러나 호환 가능한 기간(일)보다 오래된 볼륨에서 사용 가능한 스냅샷이 생성되도록 일정을 변경하면 사용자는 이전 스냅샷(이전 버전)을 사용하여 복원할 수 없습니다.

> [!Note]
> 셀프 서비스 복원을 사용하도록 설정하면 Azure Storage 사용량 및 요금에 영향을 미칠 수 있습니다. 이 영향은 현재 서버에 계층화된 파일로 제한됩니다. 이 기능을 사용하면 클라우드의 파일 버전을 이전 버전(VSS 스냅샷) 항목을 통해 참조할 수 있습니다.
>
> 이 기능을 사용하지 않는 경우 호환 가능한 기간(일)이 경과할 때까지 Azure Storage 사용량이 천천히 감소합니다. 이 속도를 높일 수 있는 방법은 없습니다. 

볼륨당 VSS 스냅샷의 기본 최대 수(64)로 설정한 경우 이를 사용하는 기본 일정은 볼륨에 저장할 수 있는 VSS 스냅샷 수에 따라 정보 작업자가 이전 버전에서 복원할 수 있는 최대 45일의 시간이 됩니다.

볼륨당 최대 64개의 VSS 스냅샷의 설정이 적절하지 않은 경우 [레지스트리 키를 통해 해당 값을 변경](/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies)합니다.
새 제한을 적용하려면 cmdlet을 다시 실행하여 이전에 활성화된 모든 볼륨에서 이전 버전 호환성을 활성화하고 -Force 플래그를 사용하여 볼륨당 VSS 스냅샷의 새로운 최대 수를 사용해야 합니다. 이렇게 하면 새로 계산된 호환 일 수가 산출됩니다. 이 변경 사항은 새로 계층화된 파일에만 적용되며 VSS 일정에 대한 사용자 지정 내용을 덮어씁니다.

<a id="proactive-recall"></a>
## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>Azure 파일 공유에서 새 파일 및 변경된 파일 사전 회수

에이전트 버전 11을 사용하면 서버 엔드포인트에서 새 모드를 사용할 수 있습니다. 이 모드를 사용하면 전 세계에 분산된 회사에서 로컬 사용자가 파일에 액세스하기 전에도 원격 지역의 서버 캐시를 미리 채울 수 있습니다. 서버 엔드포인트에서 활성화된 경우 이 모드를 사용하면 이 서버가 Azure 파일 공유에서 만들어지거나 변경된 파일을 호출합니다.

### <a name="scenario"></a>시나리오

회사는 전 세계에 분산되어 미국과 인도에 지사를 두고 있습니다. 정보 근로자는 오전(미국 시간)에 새로운 프로젝트를 위해 새 폴더와 새 파일을 만들고 하루 종일 작업합니다. Azure 파일 동기화는 폴더 및 파일을 Azure 파일 공유(클라우드 엔드포인트)와 동기화합니다. 인도의 정보 근로자는 자신의 시간대에 따라 프로젝트 작업을 계속합니다. 아침에 도착하면 인도 팀이 로컬 캐시에서 효율적으로 작업할 수 있도록 로컬 Azure 파일 동기화가 사용하도록 설정된 인도의 서버가 이러한 새 파일을 로컬에서 사용할 수 있어야 합니다. 이 모드를 사용하도록 설정하면 요청 시 회수로 인해 초기 파일 액세스 속도가 느려지는 것을 방지하고 Azure 파일 공유에서 파일이 변경되거나 생성되는 즉시 서버가 파일을 사전에 회수할 수 있습니다.

> [!IMPORTANT]
> 서버에서 긴밀하게 Azure 파일 공유의 변경 내용을 추적할 경우 송신 트래픽과 Azure의 청구액이 증가할 수 있음을 알아야 합니다. 서버로 회수된 파일이 실제로 로컬에서 필요하지 않은 경우 서버에 대한 불필요한 회수는 부정적인 결과를 초래할 수 있습니다. 클라우드의 최근 변경 사항으로 서버의 캐시를 미리 채울 경우 해당 서버의 파일을 사용하는 사용자 또는 애플리케이션에 긍정적인 영향을 미친다면 이 모드를 사용하세요.

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>Azure 파일 공유에서 변경된 사항을 사전에 회수하도록 서버 엔드포인트 설정

# <a name="portal"></a>[포털](#tab/proactive-portal)

1. [Azure Portal](https://portal.azure.com/)에서 스토리지 동기화 서비스로 이동하고 올바른 동기화 그룹을 선택한 다음 Azure 파일 공유(클라우드 엔드포인트)의 변경 사항을 면밀히 추적하려는 서버 엔드포인트를 식별합니다.
1. 클라우드 계층화 섹션에서 "Azure 파일 공유 다운로드" 항목을 찾습니다. 현재 선택된 모드가 표시되고 Azure 파일 공유 변경 사항을 보다 밀접하게 추적하고 사전에 서버에 회수하도록 변경할 수 있습니다.

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="현재 유효한 서버 엔드포인트에 대한 Azure 파일 공유 다운로드 동작과 이를 변경할 수 있는 메뉴를 여는 버튼이 표시된 이미지":::

# <a name="powershell"></a>[PowerShell](#tab/proactive-powershell)

[AzStorageSyncServerEndpoint](/powershell/module/az.storagesync/set-azstoragesyncserverendpoint) cmdlet을 통해 PowerShell에서 서버 엔드포인트 속성을 수정할 수 있습니다.

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>DFS 복제(DFS-R) 배포를 Azure 파일 동기화로 마이그레이션
DFS-R 배포를 Azure 파일 동기화로 마이그레이션하려면

1. 바꾸려는 DFS-R 토폴로지를 나타내는 동기화 그룹을 만듭니다.
1. 마이그레이션할 DFS-R 토폴로지의 전체 데이터 집합이 있는 서버에서 시작합니다. 해당 서버에 Azure 파일 동기화를 설치합니다.
1. 해당 서버를 등록하고 마이그레이션할 첫 번째 서버에 대한 서버 엔드포인트를 만듭니다. 클라우드 계층화를 활성화하지 마세요.
1. Azure 파일 공유(클라우드 엔드포인트)에 대한 모든 데이터 동기화를 수행합니다.
1. 나머지 DFS-R 서버에 각각 Azure 파일 동기화 에이전트를 설치하고 등록합니다.
1. DFS-R을 사용하지 않도록 설정합니다. 
1. 각 DFS-R 서버에 서버 엔드포인트를 만듭니다. 클라우드 계층화를 활성화하지 마세요.
1. 동기화를 완료하고 필요에 따라 토폴로지를 테스트합니다.
1. DFS-R을 사용 중지합니다.
1. 이제 클라우드 계층화를 원하는 모든 서버 엔드포인트에서 사용할 수 있습니다.

자세한 내용은 [분산 파일 시스템(DFS)과의 Azure 파일 동기화 interop](file-sync-planning.md#distributed-file-system-dfs)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 서버 엔드포인트 만들기](file-sync-server-endpoint-create.md)
- [Azure 파일 동기화로 서버 등록 또는 등록 취소](file-sync-server-registration.md)
- [Azure 파일 동기화 모니터링](file-sync-monitoring.md)
