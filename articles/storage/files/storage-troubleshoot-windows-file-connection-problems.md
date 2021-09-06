---
title: Windows에서 Azure Files 문제 해결
description: Windows에서 Azure Files 문제 해결 Windows 클라이언트에서 연결할 때 Azure Files와 관련된 일반적인 문제를 확인하고 가능한 해결 방법을 참조합니다. SMB 공유에만 해당
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/13/2019
ms.author: jeffpatt
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b1541acc9ab6871418d1cb750d74d285f3228f92
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529561"
---
# <a name="troubleshoot-azure-files-problems-in-windows-smb"></a>Windows에서 Azure Files 문제 해결(SMB)

이 문서에서는 Windows 클라이언트에서 연결할 때 Microsoft Azure Files와 관련하여 발생하는 일반적인 문제를 보여 줍니다. 또한 이러한 문제의 가능한 원인과 해결 방법을 제공합니다. 이 문서의 문제 해결 단계 외에도 [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows)를 사용하여 Windows 클라이언트 환경의 필수 구성 요소가 올바른지 확인할 수 있습니다. AzFileDiagnostics는 이 문서에서 설명하는 대부분의 현상을 자동으로 감지하고 최적의 성능을 얻도록 환경을 설정하는 데 도움이 됩니다.

> [!IMPORTANT]
> 이 문서의 내용은 SMB 공유에만 적용됩니다. NFS 공유에 대한 자세한 내용은 [Azure NFS 파일 공유 문제 해결](storage-troubleshooting-files-nfs.md)을 참조하세요.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Azure 파일 공유를 탑재할 때 오류 5 발생

파일 공유를 탑재하려고 하면 다음 오류가 표시될 수 있습니다.

- 시스템 오류 5가 발생했습니다. 액세스가 거부되었습니다.

### <a name="cause-1-unencrypted-communication-channel"></a>원인 1: 암호화되지 않은 통신 채널

보안상 이유로, 통신 채널이 암호화되지 않고 Azure 파일 공유가 있는 같은 데이터 센터에서 연결 시도가 이루어지지 않을 경우 Azure 파일 공유에 대한 연결이 차단됩니다. 스토리지 계정에서 [보안 전송 필요](../common/storage-require-secure-transfer.md) 설정을 사용하도록 설정한 경우에도 동일한 데이터 센터 내의 암호화되지 않은 연결을 차단할 수 있습니다. 사용자의 클라이언트 OS가 SMB 암호화를 지원하는 경우에만 암호화된 통신 채널이 제공됩니다.

각 시스템의 Windows 8, Windows Server 2012 이후 버전은 암호화를 지원하는 SMB 3.x가 포함된 요청을 협상합니다.

### <a name="solution-for-cause-1"></a>원인 1의 해결 방법

1. SMB 암호화를 지원하는 클라이언트(Windows 8, Windows Server 2012 이상)에서 연결하거나, Azure 파일 공유에 사용되는 Azure Storage 계정과 동일한 데이터 센터에 있는 가상 머신에서 연결합니다.
2. 클라이언트가 SMB 암호화를 지원하지 않는 경우 스토리지 계정에서 [보안 전송 필요](../common/storage-require-secure-transfer.md) 설정이 사용하지 않도록 설정되었는지 확인합니다.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>원인 2: 가상 네트워크 또는 방화벽 규칙이 스토리지 계정에서 사용하도록 설정됨 

가상 네트워크(VNET) 및 방화벽 규칙이 스토리지 계정에 구성된 경우, 클라이언트 IP 주소 또는 가상 네트워크에 액세스가 허용되지 않았다면 네트워크 트래픽이 거부됩니다.

### <a name="solution-for-cause-2"></a>원인 2의 해결 방법

가상 네트워크 및 방화벽 규칙이 스토리지 계정에 제대로 구성되어 있는지 확인합니다. 가상 네트워크 또는 방화벽 규칙에서 문제가 발생하는지 테스트하려면 일시적으로 스토리지 계정의 설정을 **모든 네트워크에서 액세스 허용** 으로 변경합니다. 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../common/storage-network-security.md)을 참조하세요.

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>원인 3: ID 기반 인증을 사용할 때 공유 수준 권한이 올바르지 않음

사용자가 AD(Active Directory) 또는 Azure AD DS(Azure Active Directory Domain Services) 인증을 사용하여 Azure 파일 공유에 액세스하는 경우 공유 수준 권한이 올바르지 않으면 "액세스 거부됨" 오류와 함께 파일 공유에 대한 액세스가 실패합니다. 

### <a name="solution-for-cause-3"></a>원인 3의 해결 방법

사용 권한이 올바르게 구성되었는지 확인합니다.

- **AD(Active Directory)** : [ID에 공유 수준 권한 할당](./storage-files-identity-ad-ds-assign-permissions.md)을 참조하세요.

    공유 수준 권한 할당은 Azure AD Connect를 사용하여 AD(Active Directory)에서 Azure AD(Azure Active Directory)로 동기화된 그룹 및 사용자에 대해 지원됩니다.  공유 수준 권한이 할당된 그룹 및 사용자가 지원되지 않는 "클라우드 전용" 그룹이 아닌지 확인합니다.
- **Azure AD DS(Azure Active Directory Domain Services)** : [ID에 액세스 권한 할당](./storage-files-identity-auth-active-directory-domain-service-enable.md?tabs=azure-portal#assign-access-permissions-to-an-identity)을 참조하세요.

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Azure 파일 공유를 탑재 또는 탑재 해제하는 경우 오류 53, 오류 67 또는 오류 87 발생

온-프레미스 또는 다른 데이터 센터에서 파일 공유를 탑재하려고 할 때 다음과 같은 오류가 발생할 수 있습니다.

- 시스템 오류 53이 발생했습니다. 네트워크 경로를 찾을 수 없습니다.
- 시스템 오류 67이 발생했습니다. 네트워크 이름을 찾을 수 없습니다.
- 시스템 오류 87이 발생했습니다. 매개 변수가 올바르지 않습니다.

### <a name="cause-1-port-445-is-blocked"></a>원인 1: 포트 445 차단

시스템 오류 53 또는 시스템 오류 67은 Azure Files 데이터 센터에 대한 포트 445 아웃바운드 통신이 차단될 경우 발생할 수 있습니다. 포트 445에서 시작되는 액세스를 허용하거나 거부하는 ISP에 대한 요약을 확인하려면 [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)으로 이동합니다.

방화벽 또는 ISP가 포트 445를 차단하는지 확인하려면 [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) 도구 또는 `Test-NetConnection` cmdlet을 사용합니다. 

`Test-NetConnection` cmdlet을 사용하려면 Azure PowerShell 모듈을 설치해야 합니다. 자세한 내용은 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 잊지 말고 `<your-storage-account-name>` 및 `<your-resource-group-name>`을 스토리지 계정과 관련된 이름으로 바꿔야 합니다.

   
```azurepowershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
# $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
# or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
```
    
연결되면 다음 출력이 표시됩니다.
    
  
```azurepowershell
ComputerName     : <your-storage-account-name>
RemoteAddress    : <storage-account-ip-address>
RemotePort       : 445
InterfaceAlias   : <your-network-interface>
SourceAddress    : <your-ip-address>
TcpTestSucceeded : True
```
 

> [!Note]  
> 위의 명령은 스토리지 계정의 현재 IP 주소를 반환합니다. 이 IP 주소가 동일하게 유지된다는 보장이 없으며, 언제든지 변경될 수 있습니다. 이 IP 주소를 스크립트로 또는 방화벽 구성으로 하드 코딩하지 마세요.

### <a name="solution-for-cause-1"></a>원인 1의 해결 방법

#### <a name="solution-1---use-azure-file-sync"></a>해결 방법 1 - Azure 파일 동기화 사용
Azure 파일 동기화는 온-프레미스 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환할 수 있습니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. Azure 파일 동기화는 포트 443을 통해 작동하므로 포트 445가 차단된 클라이언트에서 Azure Files에 액세스하는 방법으로 사용할 수 있습니다. [Azure 파일 동기화를 설정하는 방법 알아보기](../file-sync/file-sync-extend-servers.md)

#### <a name="solution-2---use-vpn"></a>해결 방법 2 - VPN 사용
특정 스토리지 계정에 대한 VPN을 설정하면 트래픽이 인터넷을 통하지 않고 보안 터널을 통해 이동합니다. [VPN 설정 지침](storage-files-configure-p2s-vpn-windows.md)에 따라 Windows에서 Azure Files에 액세스합니다.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>해결 방법 3 - ISP/IT 관리자의 도움을 받아 포트 445의 차단 해제
IT 부서 또는 ISP와 협력하여 [Azure IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 대한 포트 445 아웃바운드를 엽니다.

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>해결 방법 4 - Storage Explorer/Powershell 같은 REST API 기반 도구 사용
Azure Files는 SMB 외에 REST도 지원합니다. REST 액세스는 포트 443(표준 TCP)을 통해 작동합니다. 다양한 UI 환경을 가능하게 하는 REST API를 사용하여 작성된 다양한 도구가 있습니다. [Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)가 그중 하나입니다. [Storage Explorer를 다운로드하여 설치하고](https://azure.microsoft.com/features/storage-explorer/) Azure Files에서 지원하는 파일 공유에 연결합니다. [PowerShell](./storage-how-to-use-files-powershell.md) 및 REST API를 사용할 수도 있습니다.

### <a name="cause-2-ntlmv1-is-enabled"></a>원인 2: NTLMv1 사용

클라이언트에서 NTLMv1 통신이 사용될 경우 시스템 오류 53 또는 시스템 오류 87이 발생할 수 있습니다. Azure Files는 NTLMv2 인증만 지원합니다. NTLMv1을 사용하도록 설정하면 클라이언트 보안이 약화됩니다. 따라서 Azure Files에 대한 통신이 차단됩니다. 

이것이 오류의 원인인지 확인하려면 다음 레지스트리 하위 키가 3 미만의 값으로 설정되어 있지 않은지 확인합니다.

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

자세한 내용은 TechNet에서 [LmCompatibilityLevel](/previous-versions/windows/it-pro/windows-2000-server/cc960646(v=technet.10)) 항목을 참조하세요.

### <a name="solution-for-cause-2"></a>원인 2의 해결 방법

**LmCompatibilityLevel** 값을 다음 레지스트리 하위 키의 기본값인 3으로 되돌립니다.

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>오류 1816 - 할당량이 부족하여 이 명령을 처리할 수 없습니다.

### <a name="cause"></a>원인

Azure 파일 공유의 파일 또는 디렉터리에 허용되는 동시 열린 핸들의 상한값에 도달하는 경우 오류 1816이 발생합니다. 자세한 내용은 [Azure Files 크기 조정 목표](./storage-files-scale-targets.md#azure-files-scale-targets)을 참조하세요.

### <a name="solution"></a>해결 방법

일부 핸들을 닫아 동시 열린 핸들 수를 줄이고 다시 시도하세요. 자세한 내용은 [Microsoft Azure Storage 성능 및 확장성 검사 목록](../blobs/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.

파일 공유, 디렉터리 또는 파일에 대한 열린 핸들을 보려면 [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell cmdlet을 사용합니다.  

파일 공유, 디렉터리 또는 파일에 대한 열린 핸들을 닫으려면 [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell cmdlet을 사용합니다.

> [!Note]  
> Get-AzStorageFileHandle 및 Close-AzStorageFileHandle cmdlet에는 Az PowerShell 모듈 버전 2.4 이상에 포함되어 있습니다. 최신 Az PowerShell 모듈을 설치하려면 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Azure 파일 공유에 액세스하거나 삭제하려고 할 때 "액세스 권한 없음" 오류 발생  
포털에서 Azure 파일 공유에 액세스하거나 삭제하려고 할 때 다음 오류가 발생할 수 있습니다.

액세스 권한 없음  
오류 코드: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>원인 1: 가상 네트워크 또는 방화벽 규칙이 스토리지 계정에서 사용하도록 설정됨

### <a name="solution-for-cause-1"></a>원인 1의 해결 방법

가상 네트워크 및 방화벽 규칙이 스토리지 계정에 제대로 구성되어 있는지 확인합니다. 가상 네트워크 또는 방화벽 규칙에서 문제가 발생하는지 테스트하려면 일시적으로 스토리지 계정의 설정을 **모든 네트워크에서 액세스 허용** 으로 변경합니다. 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../common/storage-network-security.md)을 참조하세요.

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>원인 2: 사용자 계정에 스토리지 계정에 대한 액세스 권한이 없음

### <a name="solution-for-cause-2"></a>원인 2의 해결 방법

Azure 파일 공유가 있는 스토리지 계정을 찾아 **액세스 제어(IAM)** 를 클릭한 다음, 사용자 계정에 스토리지 계정에 대한 액세스 권한이 있는지 확인합니다. 자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 스토리지 계정을 보호하는 방법](../blobs/security-recommendations.md#data-protection)을 참조하세요.

## <a name="unable-to-modify-or-delete-an-azure-file-share-or-share-snapshots-because-of-locks-or-leases"></a>잠금 또는 임대로 인해 Azure 파일 공유(또는 공유 스냅샷)을 수정하거나 삭제할 수 없음
Azure Files는 Azure 파일 공유 및 공유 스냅샷을 실수로 수정 또는 삭제하는 것을 방지하기 위해 두 가지 방법을 제공합니다. 

- **스토리지 계정 리소스 잠금**: 스토리지 계정을 포함한 모든 Azure 리소스는 [리소스 잠금](../../azure-resource-manager/management/lock-resources.md)을 지원합니다. 관리자 또는 Azure Backup과 같은 부가 가치 서비스에 의해 스토리지 계정에 잠금을 설정할 수 있습니다. 리소스 잠금의 두 가지 변형에는 스토리지 계정 및 해당 리소스에 대한 모든 수정을 방지하는 수정과 스토리지 계정 및 해당 리소스의 삭제만 방지하는 삭제가 있습니다. `Microsoft.Storage` 리소스 공급자를 통해 공유를 수정하거나 삭제하면 Azure 파일 공유 및 공유 스냅샷에 리소스 잠금이 적용됩니다. 대부분의 포털 작업, 이름에 `Rm`이 있는 Azure Files용 Azure PowerShell cmdlet(예: `Get-AzRmStorageShare`) 및 `share-rm` 명령 그룹(예: `az storage share-rm list`)의 Azure CLI 명령은 `Microsoft.Storage` 리소스 공급자를 사용합니다. Storage Explorer, 이름에 `Rm`이 없는 레거시 Azure Files PowerShell 관리 cmdlet(예: `Get-AzStorageShare`) 및 `share` 명령 그룹 아래의 레거시 Azure Files CLI 명령(예: `az storage share list`)과 같은 일부 도구와 유틸리티는 `Microsoft.Storage` 리소스 제공자와 리소스 잠금을 바이패스하는 FileREST API의 레거시 API를 사용합니다. FileREST API에 노출된 레거시 관리 API에 대한 자세한 내용은 [Azure Files의 컨트롤 플레인](/rest/api/storageservices/file-service-rest-api#control-plane)을 참조하세요.

- **공유/공유 스냅샷 임대**: 공유 임대는 Azure 파일 공유 및 파일 공유 스냅샷에 대한 일종의 독점 잠금입니다. 스크립트를 통해 API를 호출하거나 Azure Backup과 같은 부가 가치 서비스를 통해 관리자가 개별 Azure 파일 공유 또는 파일 공유 스냅샷에 임대를 설정할 수 있습니다. Azure 파일 공유 또는 파일 공유 스냅샷에 임대를 설정하는 경우 *임대 ID* 를 통해 파일 공유/공유 스냅샷을 수정하거나 삭제할 수 있습니다. 사용자는 임대 ID가 필요한 수정 작업 전에 임대를 해제하거나 임대 ID가 필요하지 않은 임대를 해제할 수도 있습니다. 공유 임대에 관한 자세한 내용은 [공유 임대](/rest/api/storageservices/lease-share)를 참조하세요.

리소스 잠금 및 임대는 스토리지 계정/Azure 파일 공유에서 의도한 관리자 작업을 방해할 수 있기 때문에 Azure Backup과 같은 부가 가치 서비스에 의해 수동으로 또는 자동으로 리소스에 배치되었을 수 있는 리소스 잠금/임대를 제거할 수 있습니다. 다음 스크립트는 모든 리소스 잠금 및 임대를 제거합니다. `<resource-group>` 및 `<storage-account>`를 사용자 환경에 적합한 값으로 바꿔야 합니다.

다음 스크립트를 실행하려면 Azure Storage PowerShell 모듈의 [3.10.1-preview 버전을 설치](https://www.powershellgallery.com/packages/Az.Storage/3.10.1-preview)해야 합니다.

> [!Important]  
> Azure Files 리소스에서 리소스 잠금 및 공유/공유 스냅샷 임대를 취하는 부가 가치 서비스는 주기적으로 잠금 및 임대를 다시 적용할 수 있습니다. 부가 가치 서비스를 통해 잠긴 리소스를 수정하거나 삭제하면 Azure Backup에서 관리되는 공유 스냅샷 삭제 같이 해당 서비스의 정기적인 작업에 영향을 줄 수 있습니다.

```PowerShell
# Parameters for storage account resource
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName

# Remove resource locks
Get-AzResourceLock `
        -ResourceType "Microsoft.Storage/storageAccounts" `
        -ResourceGroupName $storageAccount.ResourceGroupName `
        -ResourceName $storageAccount.StorageAccountName | `
    Remove-AzResourceLock -Force | `
    Out-Null

# Remove share and share snapshot leases
Get-AzStorageShare -Context $storageAccount.Context | `
    Where-Object { $_.Name -eq $fileShareName } | `
    ForEach-Object {
        try {
            $leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($_.ShareClient)
            $leaseClient.Break() | Out-Null
        } catch { }
    }
```

<a id="open-handles"></a>
## <a name="unable-to-modify-moverename-or-delete-a-file-or-directory"></a>파일 또는 디렉터리를 수정, 이동/이름 변경 또는 삭제할 수 없음
파일 공유의 주요 목적 중 하나는 여러 사용자 및 애플리케이션이 공유의 파일 및 디렉터리와 동시에 상호 작용하는 것입니다. 이러한 상호 작용을 지원하기 위해 파일 공유는 파일 및 디렉터리에 대한 액세스를 중재하는 여러 가지 방법을 제공합니다.

SMB를 통해 탑재된 Azure 파일 공유에서 파일을 열면 애플리케이션/운영 체제가 파일에 대한 참조인 파일 핸들을 요청합니다. 무엇보다도 애플리케이션은 파일 핸들을 요청할 때 파일 공유 모드를 지정합니다. 이 모드는 Azure Files에서 적용하는 파일에 대한 액세스의 독점성 수준을 지정합니다. 

- `None`: 단독으로 액세스할 수 있습니다. 
- `Read`: 파일을 연 동안 다른 사용자가 읽을 수 있습니다.
- `Write`: 파일을 연 동안 다른 사용자가 쓸 수 있습니다. 
- `ReadWrite`: `Read` 및 `Write` 공유 모드의 조합입니다.
- `Delete`: 파일을 연 동안 다른 사용자가 삭제할 수 있습니다. 

상태 비저장 프로토콜인 FileREST 프로토콜에는 파일 핸들 개념이 없지만 스크립트, 애플리케이션 또는 서비스에서 사용할 수 있는 파일 및 폴더에 대한 액세스를 중재하는 유사한 메커니즘인 파일 임대를 제공합니다. 파일이 임대되면 `None` 파일 공유 모드를 사용하여 파일 핸들과 동일하게 처리됩니다. 

파일 핸들과 임대가 중요한 용도로 사용되지만 파일 핸들과 임대가 분리될 수 있습니다. 이 경우 파일 수정 또는 삭제에 문제가 발생할 수 있습니다. 다음과 같은 오류 메시지가 표시될 수 있습니다.

- 다른 프로세스가 파일을 사용 중이기 때문에 프로세스가 액세스할 수 없습니다.
- 다른 프로그램에서 이 파일이 열려 있으므로 작업을 완료할 수 없습니다.
- 다른 사용자가 편집할 수 없도록 문서가 잠겨 있습니다.
- 지정된 리소스는 SMB 클라이언트에서 삭제되도록 표시됩니다.

해결 방법은 연결 없는 파일 핸들로 인한 문제인지 또는 임대로 인한 문제인지에 따라 다릅니다. 

### <a name="cause-1"></a>원인 1
파일 핸들로 인해 파일/디렉터리가 수정되거나 삭제되지 않습니다. [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell cmdlet을 사용하여 열린 핸들을 볼 수 있습니다. 

모든 SMB 클라이언트가 파일/디렉터리에서 열린 핸들을 닫아도 문제가 계속 발생하는 경우 파일 핸들을 강제로 닫을 수 있습니다.

### <a name="solution-1"></a>해결 방법 1
파일 핸들을 강제로 닫으려면 [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell cmdlet을 사용합니다. 

> [!Note]  
> Get-AzStorageFileHandle 및 Close-AzStorageFileHandle cmdlet에는 Az PowerShell 모듈 버전 2.4 이상에 포함되어 있습니다. 최신 Az PowerShell 모듈을 설치하려면 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

### <a name="cause-2"></a>원인 2
파일 임대로 인해 파일을 수정하거나 삭제할 수 없습니다. 다음 PowerShell을 사용하여 파일에 파일 임대가 있는지 확인할 수 있으며 `<resource-group>`, `<storage-account>`, `<file-share>` 및 `<path-to-file>`를 사용자 환경에 적합한 값으로 바꿀 수 있습니다.

```PowerShell
# Set variables 
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$fileShareName = "<file-share>"
$fileForLease = "<path-to-file>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName

# Get reference to file
$file = Get-AzStorageFile `
        -Context $storageAccount.Context `
        -ShareName $fileShareName `
        -Path $fileForLease

$fileClient = $file.ShareFileClient

# Check if the file has a file lease
$fileClient.GetProperties().Value
```

파일에 임대가 있는 경우 반환되는 개체에 다음 속성이 포함되어야 합니다.

```Output
LeaseDuration         : Infinite
LeaseState            : Leased
LeaseStatus           : Locked
```

### <a name="solution-2"></a>해결 방법 2
파일에서 임대를 제거하려면 임대를 해제하거나 임대를 중단할 수 있습니다. 임대를 해제하려면 임대를 만들 때 설정한 임대의 LeaseId가 필요합니다. LeaseId는 임대를 중단할 필요가 없습니다.

다음 예는 원인 2에 표시된 파일에 대한 임대를 중단하는 방법을 보여줍니다(이 예제에서는 원인 2의 PowerShell 변수를 계속 사용).

```PowerShell
$leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($fileClient)
$leaseClient.Break() | Out-Null
```

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Windows에서 Azure Files와 서로 파일을 복사하는 속도 느림

Azure File 서비스에 파일을 전송하려고 하면 성능 저하가 발생할 수 있습니다.

- 최소 I/O 크기에 대한 특정 요구 사항이 없을 경우 최적 성능을 위해 I/O 크기로 1MiB를 사용하는 것이 좋습니다.
-   쓰기를 사용하여 확장 중인 파일의 최종 크기를 알고 파일에 아직 기록되지 않은 꼬리에 0이 포함될 때 소프트웨어에 호환성 문제가 발생하지 않는다면 모든 쓰기를 확장 쓰기로 설정하는 대신 파일 크기를 미리 설정합니다.
-   copy 메서드를 다음과 같이 올바르게 사용합니다.
    -   두 파일 공유 간의 전송에는 [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 사용합니다.
    -   온-프레미스 컴퓨터와 파일 공유 간에는 [Robocopy](./storage-how-to-create-file-share.md)를 사용합니다.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Windows 8.1 또는 Windows Server 2012 R2에 대한 고려 사항

Windows 8.1 또는 Windows Server 2012 R2를 실행 중인 클라이언트에서는 핫픽스 [KB3114025](https://support.microsoft.com/help/3114025)가 설치되어 있는지 확인합니다. 이 핫픽스는 핸들 만들기 및 닫기 성능을 개선합니다.

다음 스크립트를 실행하여 핫픽스가 설치되었는지 확인할 수 있습니다.

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

핫픽스가 설치된 경우 다음 출력이 표시됩니다.

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> 2015년 12월부터 Azure Marketplace의 Windows Server 2012 R2 이미지에는 핫픽스 KB3114025가 기본적으로 설치되어 있습니다.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>"내 컴퓨터" 또는 "이 PC"에 드라이브 문자를 가진 폴더가 없습니다.

net use를 사용하여 관리자 권한으로 Azure 파일 공유를 매핑하는 경우 공유가 누락될 수 있습니다.

### <a name="cause"></a>원인

기본적으로 Windows File Explorer는 관리자 권한으로 실행되지 않습니다. 관리자 명령 프롬프트에서 net use를 실행할 경우 네트워크 드라이브를 관리자 권한으로 매핑합니다. 매핑된 드라이브는 사용자 중심이므로 다른 사용자 계정으로 탑재될 경우 로그인된 사용자 계정에 드라이브가 표시되지 않습니다.

### <a name="solution"></a>해결 방법
비관리자 명령줄에서 공유를 탑재하세요. 또는 [이 TechNet 항목](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee844140(v=ws.10))에 따라 **EnableLinkedConnections** 레지스트리 값을 구성할 수 있습니다.

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>내 스토리지 계정에 슬래시(/)가 포함되는 경우 net use 명령이 실패합니다.

### <a name="cause"></a>원인

net use 명령은 슬래시(/)를 명령줄 옵션으로 해석합니다. 사용자 계정 이름이 슬래시로 시작되면 드라이브 매핑에 실패합니다.

### <a name="solution"></a>해결 방법

다음 단계 중 하나를 사용하여 문제를 해결할 수 있습니다.

- 다음 PowerShell 명령을 실행합니다.

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  배치 파일에서 다음과 같은 방식으로 명령을 실행할 수 있습니다.

  `Echo new-smbMapping ... | powershell -command –`

- 슬래시(/)가 첫 번째 문자가 아닐 경우 키 주위에 큰따옴표를 배치하여 이 문제를 해결합니다. 슬래시(/)가 첫 번째 문자일 경우 대화형 모드를 사용하여 암호를 개별적으로 입력하거나 키를 다시 생성하여 슬래시(/) 문자로 시작되지 않는 키를 얻습니다.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>애플리케이션 또는 서비스가 탑재된 Azure Files 드라이브에 액세스할 수 없습니다.

### <a name="cause"></a>원인

드라이브는 사용자별로 탑재됩니다. 애플리케이션 또는 서비스가 드라이브를 탑재한 계정이 아닌 다른 사용자 계정으로 실행되는 경우 애플리케이션에는 드라이브가 표시되지 않습니다.

### <a name="solution"></a>해결 방법

다음 솔루션 중 하나를 사용하세요.

-   애플리케이션을 포함하는 동일한 사용자 계정에서 드라이브를 탑재합니다. PsExec와 같은 도구를 사용할 수 있습니다.
- net use 명령의 사용자 이름 및 암호 매개 변수에 스토리지 계정 이름 및 키를 전달합니다.
- cmdkey 명령을 사용하여 자격 증명 관리자에 자격 증명을 추가합니다. 대화형 로그인 또는 `runas`를 통해 서비스 계정 컨텍스트의 명령줄에서 이를 수행합니다.
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- 매핑된 드라이브 문자를 사용하지 않고 직접 공유를 매핑합니다. 일부 애플리케이션은 드라이브 문자에 제대로 연결되지 않을 수 있으므로 전체 UNC 경로를 사용하는 것이 더 안정적일 수 있습니다. 

  `net use * \\storage-account-name.file.core.windows.net\share`

다음과 같은 지침을 따르고 시스템/네트워크 서비스 계정에 net use를 실행하면 다음과 같은 오류 메시지가 표시될 수 있습니다. "시스템 오류 1312가 발생했습니다. 지정된 로그온 세션이 없습니다. 이미 종료되었을 수 있습니다." 이 문제가 발생하면 net use에 전달되는 사용자 이름에 도메인 정보(예: &quot;[스토리지 계정 이름].file.core.windows.net&quot;)가 포함되는지 확인합니다.

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>“암호화를 지원하지 않는 대상에 파일을 복사하는 중임” 오류 발생

네트워크를 통해 파일이 복사되면 파일은 원본 컴퓨터에서 암호를 해독하고, 일반 텍스트로 전송되어 대상에서 다시 암호화됩니다. 하지만 암호화된 파일을 복사하려 하는 경우 다음 오류가 발생할 수 있습니다. "암호화를 지원하지 않는 대상에 파일을 복사하고 있습니다."

### <a name="cause"></a>원인
EFS(파일 시스템 암호화)를 사용하는 경우 이 문제가 발생할 수 있습니다. BitLocker로 암호화된 파일을 Azure Files로 복사할 수 있습니다. 하지만 Azure Files는 NTFS EFS를 지원하지 않습니다.

### <a name="workaround"></a>해결 방법
파일을 네트워크로 복사하려면 먼저 파일을 암호 해독해야 합니다. 다음 방법 중 하나를 사용합니다.

- **copy /d** 명령을 사용합니다. 암호화된 파일을 대상에서 암호 해독된 파일로 저장할 수 있습니다.
- 다음 레지스트리 키를 설정합니다.
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Value type = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Value = 1

레지스트리 키를 설정하면 네트워크 공유에 만들어진 모든 복사 작업에 적용됩니다.

## <a name="slow-enumeration-of-files-and-folders"></a>파일 및 폴더의 느린 열거

### <a name="cause"></a>원인

이 문제는 클라이언트 머신에서 대규모 디렉터리에 대한 캐시가 충분하지 않을 때 발생할 수 있습니다.

### <a name="solution"></a>해결 방법

이 문제를 해결하려면 **DirectoryCacheEntrySizeMax** 레지스트리 값을 조정하여 클라이언트 머신에 더 큰 디렉터리 목록의 캐시를 허용합니다.

- 위치: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- 값 이름: DirectoryCacheEntrySizeMax 
- 값 형식: DWORD
 
 
예를 들어 0x100000으로 설정하고 성능이 향상되는지 확인할 수 있습니다.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Azure Files에 대해 Azure AD DS(Azure Active Directory Domain Service) 인증을 사용하도록 설정할 때 "테넌트 ID가 aad-tenant-id인 활성 테넌트를 찾을 수 없음"이라는 AadDsTenantNotFound 오류 발생

### <a name="cause"></a>원인

연결된 구독의 Azure AD 테넌트에 [Azure AD Domain Service(Azure AD DS)](../../active-directory-domain-services/overview.md)가 생성되지 않은 스토리지 계정에서 [Azure Files에 대한 Azure AD DS(Azure Active Directory Domain Service) 인증을 사용하도록 설정](storage-files-identity-auth-active-directory-domain-service-enable.md)하면 AadDsTenantNotFound 오류가 발생합니다.  

### <a name="solution"></a>해결 방법

스토리지 계정이 배포된 구독의 Azure AD 테넌트에서 Azure AD DS를 사용하도록 설정합니다. 관리되는 도메인을 만들려면 Azure AD 테넌트의 관리자 권한이 필요합니다. Azure AD 테넌트의 관리자가 아닌 경우 관리자에게 문의하고 단계별 지침에 따라 [Azure Active Directory Domain Services 관리되는 도메인 만들기 및 구성](../../active-directory-domain-services/tutorial-create-instance.md)을 수행하세요.

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>AD 자격 증명으로 Azure Files를 탑재할 수 없음 

### <a name="self-diagnostics-steps"></a>자체 진단 단계
첫째, [Azure Files AD 인증을 사용하도록 설정](./storage-files-identity-auth-active-directory-enable.md)하는 4단계를 모두 수행했는지 확인합니다.

둘째, [스토리지 계정 키를 사용하여 Azure 파일 공유 탑재](./storage-how-to-use-files-windows.md)를 시도합니다. 탑재할 수 없는 경우 [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows)을 다운로드하여 클라이언트 실행 환경의 유효성을 검사하고, Azure Files에 대한 액세스 오류를 발생시키는 호환되지 않는 클라이언트 구성을 검색할 수 있으며, 자체 수정에 대한 규범적인 지침을 제공하고, 진단 추적을 수집합니다.

셋째, Debug-AzStorageAccountAuth cmdlet을 실행하여 로그온한 AD 사용자로 AD 구성에 대한 일련의 기본 검사를 수행할 수 있습니다. 이 cmdlet은 [AzFilesHybrid v0.1.2 이상 버전](https://github.com/Azure-Samples/azure-files-samples/releases)에서 지원됩니다. 대상 스토리지 계정에 대한 소유자 권한이 있는 AD 사용자를 통해 이 cmdlet을 실행해야 합니다.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
cmdlet은 아래의 검사를 순서대로 수행하고 실패에 대한 지침을 제공합니다.
1. CheckADObjectPasswordIsCorrect: 스토리지 계정을 나타내는 AD ID에 구성된 암호가 스토리지 계정 kerb1 또는 kerb2 키의 암호와 일치하는지 확인합니다. 암호가 잘못된 경우 [Update-AzStorageAccountADObjectPassword](./storage-files-identity-ad-ds-update-password.md)를 실행하여 암호를 다시 설정할 수 있습니다. 
2. CheckADObject: 스토리지 계정을 나타내는 개체가 Active Directory에 있고 올바른 SPN(서비스 주체 이름)이 있는지 확인합니다. SPN이 올바르게 설정되지 않은 경우 디버그 cmdlet에서 반환된 Set-AD cmdlet을 실행하여 SPN을 구성합니다.
3. CheckDomainJoined: 클라이언트 컴퓨터가 AD에 연결된 도메인인지 확인합니다. 컴퓨터가 AD에 도메인에 가입되어 있지 않은 경우 도메인 가입 지침은 이 [문서](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)를 참조하세요.
4. CheckPort445Connectivity: SMB 연결을 위해 포트 445가 열려 있는지 확인합니다. 필요한 포트가 열려 있지 않은 경우 Azure Files와의 연결 문제에 대한 문제 해결 도구 [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows)을 참조하세요.
5. CheckSidHasAadUser: 로그온한 AD 사용자가 Azure AD와 동기화되었는지 확인합니다. 특정 AD 사용자가 Azure AD와 동기화되었는지 여부를 확인하려면 입력 매개 변수에서 -UserName 및 -Domain을 지정합니다. 
6. CheckGetKerberosTicket: 스토리지 계정에 연결하기 위한 Kerberos 티켓을 가져옵니다. 유효한 Kerberos 토큰이 없는 경우 klist get cifs/storage-account-name.file.core.windows.net cmdlet을 실행하고 오류 코드를 검사하여 티켓 검색 실패의 근본 원인을 찾습니다.
7. CheckStorageAccountDomainJoined: AD 인증이 사용하도록 설정되어 있고 계정의 AD 속성이 채워져 있는지 확인합니다. 채워져 있지 않은 경우 [여기](./storage-files-identity-ad-ds-enable.md)의 지침을 참조하여 Azure Files에서 AD DS 인증을 사용하도록 설정합니다. 
8. CheckUserRbacAssignment: AD 사용자에게 Azure Files에 액세스할 수 있는 공유 수준 권한을 제공하기 위한 적절한 RBAC 역할이 할당되어 있는지 확인합니다. 할당되어 있지 않은 경우 [여기](./storage-files-identity-ad-ds-assign-permissions.md)의 지침을 참조하여 공유 수준 권한을 구성합니다. (AzFilesHybrid v0.2.3+ 버전에서 지원)
9. CheckUserFileAccess: AD 사용자에게 Azure Files에 액세스할 수 있는 적절한 디렉터리/파일 권한(Windows ACL)이 있는지 확인합니다. 권한이 없는 경우 [여기](./storage-files-identity-ad-ds-configure-permissions.md)의 지침을 참조하여 디렉터리/파일 수준 권한을 구성합니다. (AzFilesHybrid v0.2.3+ 버전에서 지원)

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Windows 파일 탐색기를 사용하여 디렉터리/파일 수준 권한(Windows ACL)을 구성할 수 없습니다.

### <a name="symptom"></a>증상

탑재된 파일 공유에서 파일 탐색기를 사용하여 Windows ACL을 구성하려고 할 때 아래 설명된 현상 중 하나가 발생할 수 있습니다.
- 보안 탭에서 권한 편집을 클릭할 때 권한 마법사가 로드되지 않습니다. 
- 새 사용자 또는 그룹을 선택하려고 할 때 도메인 위치에 올바른 AD DS 도메인이 표시되지 않습니다. 

### <a name="solution"></a>해결 방법

해결 방법으로 [icacls 도구](/windows-server/administration/windows-commands/icacls)를 사용하여 디렉터리/파일 수준 권한을 구성하는 것이 좋습니다. 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>Join-AzStorageAccountForAuth cmdlet을 실행할 때 발생하는 오류

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>오류: "디렉터리 서비스에서 상대 식별자를 할당할 수 없음"

RID 마스터 FSMO 역할이 있는 도메인 컨트롤러를 사용할 수 없거나 도메인에서 제거되고 백업에서 복원된 경우 이 오류가 발생할 수 있습니다.  모든 도메인 컨트롤러가 실행 중이고 사용 가능한지 확인합니다.

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>오류: "이름이 지정되지 않았으므로 위치 매개 변수를 바인딩할 수 없습니다."

이 오류는 Join-AzStorageAccountforAuth 명령의 구문 오류에 의해 트리거될 수 있습니다.  명령에서 맞춤법 오류 또는 구문 오류를 확인하고 최신 버전의 AzFilesHybrid 모듈(https://github.com/Azure-Samples/azure-files-samples/releases) )이 설치되어 있는지 확인합니다.  

## <a name="azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption"></a>AES 256 Kerberos 암호화에 대한 Azure Files 온-프레미스 AD DS 인증 지원

[AzFilesHybrid 모듈 v 0.2.2](https://github.com/Azure-Samples/azure-files-samples/releases)를 사용하여 Azure Files 온-프레미스 AD DS 인증에 대한 AES 256 Kerberos 암호화 지원이 도입되었습니다. v0.2.2보다 낮은 모듈 버전으로 AD DS 인증을 사용하도록 설정한 경우 최신 AzFilesHybrid 모듈(v0.2.2 이상)을 다운로드하고 아래 PowerShell을 실행해야 합니다. 아직 스토리지 계정에서 AD DS 인증을 사용하도록 설정하지 않은 경우 이 [지침](./storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module)에 따라 설정할 수 있습니다. 

```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName
```

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의
도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
