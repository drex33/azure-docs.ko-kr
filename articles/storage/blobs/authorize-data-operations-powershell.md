---
title: Azure AD 자격 증명을 통해 PowerShell 명령을 실행하여 Blob 데이터에 액세스하기
titleSuffix: Azure Storage
description: PowerShell에서는 Azure AD 자격 증명으로 로그인하여 Azure Storage의 Blob 데이터에 대한 명령을 실행할 수 있습니다. 세션에 액세스 토큰이 제공되고 호출 작업에 권한을 부여하는 데 사용됩니다. 사용 권한은 Azure AD 보안 주체에 할당된 Azure 역할에 따라 다릅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.openlocfilehash: dae63f442c2e2df068cc3f17bb5355abd4b7de77
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615627"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-data"></a>Azure AD 자격 증명을 통해 PowerShell 명령을 실행하여 Blob 데이터에 액세스하기

Azure Storage는 Azure AD(Azure Active Directory) 자격 증명을 사용하여 로그인한 후 스크립팅 명령을 실행하도록 지원하는 PowerShell용 확장 기능을 제공합니다. Azure AD 자격 증명을 사용하여 PowerShell에 로그인하면 OAuth 2.0 액세스 토큰이 반환됩니다. 이 토큰은 PowerShell에서 Blob 스토리지에 대한 후속 데이터 작업에 권한을 부여하는 데 자동으로 사용됩니다. 지원되는 작업의 경우, 더 이상 명령과 함께 계정 키 또는 SAS 토큰을 전달할 필요가 없습니다.

Azure RBAC(Azure 역할 기반 액세스 제어)를 통해 Azure AD 보안 주체에 Blob 데이터에 대한 사용 권한을 할당할 수 있습니다. Azure Storage의 Azure 역할에 대한 자세한 내용은 [Blob 데이터에 액세스하기 위한 Azure 역할 할당](assign-azure-role-data-access.md)을 참조하세요.

## <a name="supported-operations"></a>지원되는 작업

Blob 데이터에 작업에 대한 Azure Storage 확장을 지원합니다. 호출할 수 있는 작업은 PowerShell에 로그인하는 데 사용되는 Azure AD 보안 주체가 부여받은 권한에 따라 달라집니다. Azure Storage 컨테이너에 대한 권한은 Azure RBAC를 통해 할당됩니다. 예를 들어, **Blob Data Reader** 역할에 할당되면 컨테이너에서 데이터를 읽는 스크립팅 명령을 실행할 수 있습니다. **Blob Data Contributor** 역할에 할당되면 컨테이너 혹은 컨테이터에 포함된 데이터를 읽거나, 쓰거나, 삭제하는 스크립팅 명령을 실행할 수 있습니다.

컨테이너의 각 Azure Storage 작업에 필요한 사용 권한에 대한 자세한 내용은 [OAuth 토큰을 이용한 스토리지 작업 호출](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)을 참조하세요.

> [!IMPORTANT]
> Azure Resource Manager **읽기 전용** 잠금을 사용하여 스토리지 계정이 잠긴 경우, 해당 스토리지 계정에 대한 [키 나열](/rest/api/storagerp/storageaccounts/listkeys) 작업이 허용되지 않습니다. **키 나열** 은 POST 작업으로, 계정에 대해 **읽기 전용** 잠금이 설정된 경우 모든 POST 작업이 차단됩니다. 그렇기 때문에 계정이 **읽기 전용** 잠금으로 잠겨 있으면, 계정 키가 없는 사용자는 Azure AD 자격 증명을 사용하여 Blob 데이터에 액세스해야 합니다. PowerShell에서 `-UseConnectedAccount` 매개 변수를 포함하여 Azure AD 자격 증명을 사용한 **AzureStorageContext** 개체를 만듭니다.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Azure AD 자격 증명을 사용하여 PowerShell 명령 호출

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure PowerShell를 사용하여 로그인한 뒤 Azure AD 자격 증명을 사용하여 Azure Storage에 대해 후속 작업을 실행하려면, 스토리지 계정을 참조하는 스토리지 컨텍스트를 만들고 `-UseConnectedAccount` 매개 변수를 포함합니다.

다음 예제에서는 Azure AD 자격 증명을 사용하여 Azure PowerShell에서 새 스토리지 계정으로 컨테이너를 만드는 방법을 보여 줍니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하여 Azure 계정에 로그인합니다.

    ```powershell
    Connect-AzAccount
    ```

    PowerShell을 사용하여 Azure에 로그인하는 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 로그인](/powershell/azure/authenticate-azureps)을 참조하세요.

1. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 호출하여 Azure 리소스 그룹을 만듭니다.

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)를 호출하여 스토리지 계정을 만듭니다.

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)를 호출하여 새 스토리지 계정을 지정하는 스토리지 계정 컨텍스트를 가져옵니다. 스토리지 계정에서 작업할 때는 반복적으로 자격 증명을 전달하는 대신 컨텍스트를 참조합니다. Azure AD 자격 증명을 사용하여 후속 데이터 작업을 호출하는 `-UseConnectedAccount` 매개 변수를 포함합니다.

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. 컨테이너를 만들기 전에 [Storage Blob 데이터 참가자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 역할을 자신에게 할당합니다. 계정 소유자라도 스토리지 계정에 대한 데이터 작업을 수행하려면 명시적 권한이 필요합니다. Azure 역할을 할당하는 방법에 관한 자세한 내용은 [Blob 데이터에 액세스하기 위한 Azure 역할 할당](assign-azure-role-data-access.md)을 참조하세요.

    > [!IMPORTANT]
    > Azure 역할 할당을 전파하는 데 몇 분 정도 걸릴 수 있습니다.

1. [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)를 호출하여 컨테이너를 만듭니다. 이 호출에서는 이전 단계에서 만든 컨텍스트를 사용하므로 컨테이너는 Azure AD 자격 증명을 사용하여 만들게 됩니다.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>다음 단계

- [Blob 데이터에 액세스하기 위한 Azure 역할 할당](assign-azure-role-data-access.md)
- [Azure 리소스에 대한 관리 ID를 사용하여 Blob 및 큐 데이터에 대한 액세스 권한 부여](../common/storage-auth-aad-msi.md)
