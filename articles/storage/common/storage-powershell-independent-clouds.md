---
title: PowerShell을 사용하여 Azure 독립 클라우드에서 데이터 관리
titleSuffix: Azure Storage
description: Azure PowerShell을 사용하여 중국 클라우드, 정부 클라우드, 독일 클라우드에서 스토리지를 관리합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 181dd9531ec8aa3630ff1ef3e3356ead5120c512
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605965"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>PowerShell을 사용하여 Azure 독립 클라우드에서 Storage 관리

대부분의 사람들은 전역 Azure 배포에 Azure Public Cloud를 사용합니다. 또한 통치권 등의 사유로 인한 몇 가지 Microsoft Azure 독립 배포도 존재합니다. 이러한 독립 배포를 "환경"이라고 칭합니다. 다음 목록에서는 현재 사용할 수 있는 독립 클라우드에 대해 자세히 설명합니다.

- [Azure Government 클라우드](https://azure.microsoft.com/features/gov/)
- [중국 21Vianet이 운영하는 Azure China 21Vianet Cloud](http://www.windowsazure.cn/)
- [Azure German Cloud](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>독립 클라우드 사용

독립 클라우드 중 한 곳에서 Azure Storage를 사용하려면 Azure Public이 아닌 해당 클라우드에 연결합니다. Azure Public이 아닌 독립 클라우드 중 하나를 사용하려면

- 연결할 *환경* 을 지정합니다.
- 사용 가능한 지역을 결정하여 사용합니다.
- Azure Public과 달리 정확한 엔드포인트 접미사를 사용합니다.

이 예제에는 Azure PowerShell 모듈 Az 버전 0.7 이상이 필요합니다. PowerShell 창에서 `Get-Module -ListAvailable Az`을 실행하여 버전을 확인합니다. 나타나는 항목이 없거나 업그레이드가 필요한 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

## <a name="log-in-to-azure"></a>Azure에 로그인

[Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) cmdlet을 실행하여 사용 가능한 Azure 환경을 확인합니다.

```powershell
Get-AzEnvironment
```

연결하려는 클라우드에 대한 액세스 권한이 있는 계정에 로그인하고 환경을 설정합니다. 이 예제에서는 Azure Government Cloud를 사용하는 계정에 로그인하는 방법을 보여 줍니다.

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

중국 클라우드에 액세스하려면 **AzureChinaCloud** 환경을 사용합니다. 독일 클라우드에 액세스하려면 **AzureGermanCloud** 를 사용합니다.

이 시점에서 스토리지 계정을 만들 위치나 다른 리소스 목록이 필요한 경우 [Get-AzLocation](/powershell/module/az.resources/get-azlocation)을 사용하여 선택한 클라우드에 사용 가능한 위치를 쿼리할 수 있습니다.

```powershell
Get-AzLocation | select Location, DisplayName
```

다음 표에서는 독일 클라우드에 대해 반환되는 위치를 보여 줍니다.

|Location | 표시 이름 |
|----|----|
| `germanycentral` | 독일 중부|
| `germanynortheast` | 독일 북동부 |

## <a name="endpoint-suffix"></a>엔드포인트 접미사

이러한 각각의 환경에 대한 엔드포인트 접미사는 Azure Public 엔드포인트와 다릅니다. 예를 들어 Azure Public의 Blob 엔드포인트 접미사는 **blob.core.windows.net** 입니다. 독일 클라우드의 Blob 엔드포인트 접미사는 **blob.core.usgovcloudapi.net** 합니다.

### <a name="get-endpoint-using-get-azenvironment"></a>Get-AzEnvironment를 사용하여 엔드포인트 가져오기

[Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment)를 사용하여 엔드포인트 접미사를 검색합니다. 엔드포인트는 환경의 *StorageEndpointSuffix* 속성입니다.

다음 코드 조각은 엔드포인트 접미사를 검색하는 방법을 보여 줍니다. 이러한 모든 명령은 “core.cloudapp.net” 또는 “core.cloudapi.de” 등과 같은 항목을 반환합니다. 스토리지 서비스에 접미사를 추가하여 해당 서비스에 액세스합니다. 예를 들어 "queue.core.cloudapi.de"는 독일 클라우드의 큐 서비스에 액세스하게 됩니다.

이 코드 조각은 각각에 대한 모든 환경과 엔드포인트 접미사를 검색합니다.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

이 명령은 다음 결과를 반환합니다.

| 이름| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

지정된 환경에 대해 모든 속성을 검색하려면 **Get-AzEnvironment** 를 호출하고 클라우드 이름을 지정합니다. 이 코드 조각은 속성 목록을 반환합니다. 목록에서 **StorageEndpointSuffix** 를 찾습니다. 다음은 독일 클라우드에 대한 예제입니다.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

결과는 다음 값과 유사합니다.

|속성 이름|값|
|----|----|
| 이름 | `AzureGermanCloud` |
| EnableAdfsAuthentication | `False` |
| ActiveDirectoryServiceEndpointResourceI | `http://management.core.cloudapi.de/` |
| GalleryURL | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublishSettingsFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix | `.database.cloudapi.de` |
| **StorageEndpointSuffix** | `core.cloudapi.de` |
| ... | ... |

스토리지 엔드포인트 접미사 속성만 검색하려면 특정 클라우드를 검색하고 해당 속성 하나만 요청합니다.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

이 명령은 다음 정보를 반환합니다.

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>스토리지 계정에서 엔드포인트를 가져오기

엔드포인트를 가져오기 위해 스토리지 계정의 속성을 조사할 수도 있습니다.

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

정부 클라우드의 스토리지 계정의 경우 이 명령은 다음 출력을 반환합니다.

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>환경 설정 후

이제 PowerShell을 사용하여 스토리지 계정을 관리하고 Blob, 큐, 파일, 테이블 데이터에 액세스할 수 있습니다. 자세한 내용은 [Az.Storage](/powershell/module/az.storage)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이 연습에 대해 새 리소스 그룹과 스토리지 계정을 만든 후에는 리소스 그룹을 제거하여 자산을 둘 다 제거할 수 있습니다. 리소스 그룹을 삭제하면 그룹 안에 포함된 모든 리소스가 삭제됩니다.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

- [PowerShell 세션 간 사용자 로그인 유지](/powershell/azure/context-persistence)
- [Azure Government 스토리지](../../azure-government/compare-azure-government-global-azure.md)
- [Microsoft Azure Government 개발자 가이드](../../azure-government/documentation-government-developer-guide.md)
- [Azure China 21Vianet 애플리케이션 개발자 노트](https://msdn.microsoft.com/library/azure/dn578439.aspx)
- [Azure Germany 설명서](../../germany/germany-welcome.md)
