---
title: Azure Portal을 사용하여 데이터에 액세스하기 위한 Azure 역할 할당
titleSuffix: Azure Storage
description: Azure Portal을 사용하여 Azure RBAC(Azure 역할 기반 액세스 제어)를 통해 Azure Active Directory 보안 주체에 권한을 할당하는 방법을 알아봅니다. Azure Storage는 Azure AD를 통해 인증을 위한 기본 제공 및 Azure 사용자 지정 역할을 지원합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 3beba41dbd5b28d2c03d45652f6cf2eff85ba5d5
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109633299"
---
# <a name="use-the-azure-portal-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Azure Portal을 사용하여 Blob 및 큐 데이터에 액세스하기 위한 Azure 역할 할당

Azure AD(Azure Active Directory)는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 통해 보안 리소스에 대한 액세스 권한을 부여합니다. Azure Storage는 Blob 또는 큐 데이터에 액세스하는 데 사용되는 일반 권한 집합을 포함하는 Azure 기본 제공 역할 집합을 정의합니다.

Azure AD 보안 주체에 Azure 역할을 할당하면 Azure는 해당 보안 주체에 대한 리소스에 액세스 권한을 부여합니다. 액세스 권한은 구독, 리소스 그룹, 스토리지 계정 또는 개별 컨테이너나 큐의 수준에 범위를 지정할 수 있습니다. Azure AD 보안 주체는 사용자, 그룹, 애플리케이션 서비스 주체, 또는 [Azure 리소스의 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

이 문서에서는 Azure Portal을 사용하여 Azure 역할을 할당하는 방법을 설명합니다. Azure Portal은 Azure 역할을 할당하고 스토리지 리소스에 대한 액세스를 관리하기 위한 간단한 인터페이스를 제공합니다. Azure 명령줄 도구 또는 Azure Storage 관리 API를 사용하여 Blob 및 큐 리소스에 대한 Azure 역할을 할당할 수도 있습니다. 스토리지 리소스의 Azure 역할에 대한 자세한 내용은 [Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스 권한 부여](storage-auth-aad.md)를 참조하세요.

## <a name="azure-roles-for-blobs-and-queues"></a>Blob 및 큐를 위한 Azure 역할

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>리소스 범위 결정

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 역할 할당

역할 할당의 적절한 범위를 결정한 후 Azure Portal에서 해당 리소스로 이동합니다. 리소스에 대한 **IAM(액세스 제어)** 설정을 표시하고 다음 지침에 따라 역할 할당을 관리합니다.

1. 적절한 Azure Storage Azure 역할을 할당하여 Azure AD 보안 주체에 대한 액세스 권한을 부여합니다.

1. Azure AD 자격 증명을 사용하여 Azure Portal을 통해 컨테이너 또는 큐에 액세스해야 하는 사용자에게 Azure Resource Manager [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할을 할당합니다.

이어지는 섹션에서는 각 단계에 대해 자세히 설명합니다.

> [!IMPORTANT]
> Azure Storage 계정을 만들어도 Azure AD를 통해 데이터에 액세스할 수 있는 권한이 자동으로 할당되지는 않습니다. Azure Storage에 Azure 역할을 자신에게 명시적으로 할당해야 합니다. 구독, 리소스 그룹, 스토리지 계정 또는 컨테이너나 큐 수준으로 지정할 수 있습니다.
>
> Azure Portal에서 데이터 액세스를 위한 계정 키를 사용할 수도 있으므로 사용자가 데이터 액세스 역할을 자신에게 할당하기 전에 Azure Portal을 통해 스토리지 계정의 데이터에 액세스할 수 있습니다. 자세한 내용은 [Azure Portal에서 Blob 데이터에 대한 액세스 권한을 부여하는 방법 선택](../blobs/authorize-data-operations-portal.md)을 참조하세요.
>
> 스토리지 계정이 Azure Resource Manager 읽기 전용 잠금으로 잠긴 경우, 잠금은 스토리지 계정 또는 데이터 컨테이너(Blob 컨테이너 또는 큐)로 범위가 지정된 Azure RBAC 역할의 할당을 방지합니다.

### <a name="assign-an-azure-built-in-role"></a>Azure 기본 제공 역할 할당

보안 주체에 역할을 할당하기 전에 부여하는 사용 권한의 범위를 고려해야 합니다. [리소스 범위 결정](#determine-resource-scope) 섹션을 검토하여 적절한 범위를 결정합니다.

여기에 표시된 프로시저는 컨테이너로 범위가 지정된 역할을 할당하지만 큐로 범위가 지정된 역할을 할당하기 위해 동일한 단계를 수행할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 **데이터 스토리지** 아래에서 **Blob 컨테이너** 를 선택합니다.
1. 역할을 할당하려는 컨테이너를 찾아 컨테이너의 설정을 표시합니다.
1. **액세스 제어(IAM)** 를 선택하여 컨테이너에 대한 액세스 제어 설정을 표시합니다. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/portal-access-control-container.png" alt-text="컨테이너 액세스 제어 설정을 보여 주는 스크린샷":::

1. **추가** 를 클릭한 다음, **역할 할당 추가** 를 클릭하여 새 역할을 추가합니다.
1. **역할 할당 추가** 창에서 할당할 Azure Storage 역할을 선택합니다. 그런 다음 해당 역할을 할당할 보안 주체를 검색하여 찾습니다.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/add-rbac-role.png" alt-text="Azure 역할을 할당하는 방법을 보여 주는 스크린샷":::

1. **저장** 을 클릭합니다. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지에서는 추가된 사용자에게 이제 *sample-container* 라는 컨테이너의 데이터에 대한 읽기 권한이 있음을 보여줍니다.

    :::image type="content" source="media/storage-auth-aad-rbac-portal/container-scoped-role.png" alt-text="역할에 할당된 사용자의 목록을 보여 주는 스크린샷":::

유사한 단계에 따라 스토리지 계정, 리소스 그룹 또는 구독으로 범위가 지정된 역할을 할당할 수 있습니다.

### <a name="assign-the-reader-role-for-portal-access"></a>포털 액세스에 대한 읽기 권한자 역할 할당

Azure Storage에 대한 기본 제공 역할 또는 사용자 지정 역할을 보안 주체에 할당하면 해당 보안 주체에게 스토리지 계정의 데이터에 대한 작업을 수행할 수 있는 권한을 부여합니다. 기본 제공 **데이터 읽기 권한자** 역할은 컨테이너 또는 큐에 있는 데이터에 대한 읽기 권한을 부여하고, 기본 제공 **데이터 기여자** 역할은 컨테이너 또는 큐에 대한 읽기, 쓰기 및 삭제 권한을 부여합니다. 사용 권한의 범위는 지정된 리소스로 지정됩니다.  
예를 들어 **샘플 컨테이너** 라는 컨테이너 수준에서 사용자 Mary에게 **스토리지 Blob 데이터 기여자** 역할을 할당하면 Mary에게 해당 컨테이너의 모든 Blob에 대한 읽기, 쓰기 및 삭제 권한이 부여됩니다.

그러나 Mary가 Azure Portal Blob을 보려는 경우에는 **스토리지 Blob 데이터 기여자** 역할 자체에서 포털을 통해 Blob으로 이동하는 데 필요한 권한이 제공되지 않습니다. 포털을 탐색하고 여기에 표시되는 다른 리소스를 보려면 Azure AD 권한이 추가로 필요합니다.

사용자가 Azure Portal의 Blob에 액세스할 수 있어야 하는 경우 해당 사용자에게 스토리지 계정 이상의 수준에서 추가 Azure 역할인 [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할을 할당합니다. **읽기 권한자** 역할은 사용자가 스토리지 계정 리소스를 볼 수 있지만 수정할 수는 없도록 허용하는 Azure Resource Manager 역할입니다. Azure Storage에서 데이터에 대한 읽기 권한은 제공하지 않고 계정 관리 리소스에 대해서만 제공합니다.

다음 단계에 따라 사용자가 Azure Portal에서 Blob에 액세스할 수 있도록 **읽기 권한자** 역할을 할당합니다. 이 예에서 할당은 스토리지 계정으로 범위가 지정됩니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.
2. **액세스 제어(IAM)** 를 선택하여 스토리지 계정에 대한 액세스 제어 설정을 표시합니다. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다.
3. **추가** 를 클릭한 다음, **역할 할당 추가** 를 클릭하여 새 역할을 추가합니다.
4. **역할 할당 추가** 창에서 **읽기 권한자** 역할을 선택합니다. 
5. **액세스 할당** 필드에서 **Azure AD 사용자, 그룹 또는 서비스 주체** 를 선택합니다.
6. 역할을 할당할 보안 주체를 검색하여 찾습니다.
7. 역할 할당을 저장합니다.

**읽기 권한자** 역할 할당은 Azure Portal을 사용하여 Blob 또는 큐에 액세스해야 하는 사용자에게만 필요합니다.

> [!IMPORTANT]
> Azure Portal의 Storage Explorer 미리 보기 버전은 Azure AD 자격 증명을 사용하여 Blob 또는 큐 데이터를 보고 수정하는 것을 지원하지 않습니다. Azure Portal의 Storage Explorer는 항상 계정 키로 데이터에 액세스합니다. Azure Portal의 Storage Explorer를 사용하려면 **Microsoft.Storage/storageAccounts/listkeys/action** 이 포함된 역할을 할당받아야 합니다.

## <a name="next-steps"></a>다음 단계

- 스토리지 리소스의 Azure 역할에 대한 자세한 내용은 [Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스 권한 부여](storage-auth-aad.md)를 참조하세요. 
- Azure RBAC에 대해 자세히 알아보려면 [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)을 참조하세요.
- Azure PowerShell, Azure CLI 또는 REST API를 사용하여 할당하는 방법 및 Azure 역할 할당을 관리하는 방법을 알아보려면 다음과 같은 문서를 참조하세요.
    - [Azure PowerShell 모듈을 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-powershell.md)
    - [Azure CLI를 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-cli.md)
    - [REST API를 사용하여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-rest.md)
- 스토리지 애플리케이션 내에서 컨테이너와 큐에 대한 액세스 권한을 부여하는 방법을 알아보려면 [Azure Storage 애플리케이션에서 Azure AD 사용](storage-auth-aad-app.md)을 참조하세요.
