---
title: Azure Portal에서 Blob 데이터에 대한 액세스 권한을 부여하는 방법을 선택합니다.
titleSuffix: Azure Storage
description: Azure Portal을 사용하여 Blob 데이터에 액세스하는 경우 포털은 내부 Azure Storage에 요청합니다. Azure Storage에 대한 요청은 Azure AD 계정 또는 스토리지 계정 액세스 키를 사용하여 인증되고 권한이 부여될 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/08/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: contperf-fy21q1
ms.openlocfilehash: 35ea4317b78a9f732d095d9f024d7465ebd1828e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615703"
---
# <a name="choose-how-to-authorize-access-to-blob-data-in-the-azure-portal"></a>Azure Portal에서 Blob 데이터에 대한 액세스 권한을 부여하는 방법을 선택합니다.

[Azure Portal](https://portal.azure.com)을 사용하여 Blob 데이터에 액세스하는 경우 포털은 내부 Azure Storage에 요청합니다. Azure Storage에 대한 요청은 Azure AD 계정 또는 스토리지 계정 액세스 키를 사용하여 인증될 수 있습니다. 포털은 사용 중인 방법을 나타내며, 적절한 사용 권한이 있는 경우 두 방법 사이를 전환할 수 있습니다.

Azure Portal에서 개별 Blob 업로드 작업에 권한을 부여하는 방법을 지정할 수도 있습니다. 기본적으로 포털은 Blob 업로드 작업에 권한을 부여하기 위해 이미 사용 중인 방법을 사용하지만 Blob을 업로드할 때 이 설정을 변경하는 옵션이 있습니다.

## <a name="permissions-needed-to-access-blob-data"></a>Blob 데이터에 액세스하는 데 필요한 권한

Azure Portal에서 Blob 데이터에 대한 액세스 권한을 부여하려는 방법에 따라 특정 권한이 필요합니다. 대부분의 경우 권한은 Azure 역할 기반 액세스 제어(Azure RBAC)를 통해 제공됩니다. Azure RBAC에 대한 자세한 내용은 [Azure RBAC](../../role-based-access-control/overview.md)(Azure 역할 기반 액세스 제어)를 참조하세요.

### <a name="use-the-account-access-key"></a>계정 액세스 키 사용

계정 액세스 키를 사용하여 Blob 데이터에 액세스하려면 Azure RBAC 작업 **Microsoft.Storage/storageAccounts/listkeys/action** 이 포함된 Azure 역할을 할당받아야 합니다. Azure 역할은 기본 제공 또는 사용자 지정 역할일 수 있습니다. **Microsoft.Storage/storageAccounts/listkeys/action** 을 지원하는 기본 제공 역할에는 최소 권한에서 최대 권한 순으로 다음이 포함됩니다.

- [읽기 권한자 및 데이터 액세스](../../role-based-access-control/built-in-roles.md#reader-and-data-access) 역할
- [스토리지 계정 Contributor 역할](../../role-based-access-control/built-in-roles.md#storage-account-contributor)
- Azure Resource Manager [Contributor 역할](../../role-based-access-control/built-in-roles.md#contributor)
- Azure Resource Manager [Owner 역할](../../role-based-access-control/built-in-roles.md#owner)

Azure Portal에서 Blob 데이터에 액세스를 시도하면 포털은 먼저 **Microsoft.Storage/storageAccounts/listkeys/action** 으로 역할이 할당되었는지 여부를 확인합니다. 이 작업으로 역할이 할당된 경우 포털은 계정 키를 사용하여 Blob 데이터에 액세스합니다. 역할이 할당되지 않은 상태라면, 포털은 Azure AD 계정을 사용하여 데이터에 액세스하려고 시도합니다.

> [!IMPORTANT]
> Azure Resource Manager **읽기 전용** 잠금을 사용하여 스토리지 계정이 잠긴 경우, 해당 스토리지 계정에 대한 [키 나열](/rest/api/storagerp/storageaccounts/listkeys) 작업이 허용되지 않습니다. **키 나열** 은 POST 작업으로, 계정에 대해 **읽기 전용** 잠금이 설정된 경우 모든 POST 작업이 차단됩니다. 이런 이유로 계정이 **읽기 전용** 잠금으로 잠긴 경우 사용자는 포털에서 Azure AD 자격 증명을 사용하여 Blob 데이터에 액세스해야 합니다. Azure AD를 사용하여 포털에서 Blob 데이터에 액세스하는 방법에 대해 자세히 알고 싶다면 [Azure AD 계정 사용](#use-your-azure-ad-account)을 참조하세요.

> [!NOTE]
> 클래식 구독 관리자 역할인 서비스 관리자 및 공동 관리자에는 Azure Resource Manager [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할에 해당하는 항목이 포함됩니다. **Owner** 역할은 **Microsoft.Storage/storageAccounts/listkeys/action** 을 비롯한 모든 작업을 포함하므로 관리 역할 중 하나를 가진 사용자가 계정 키를 사용하여 Blob 데이터에 액세스할 수 있습니다. 자세한 내용은 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)을 참조하세요.

### <a name="use-your-azure-ad-account"></a>Azure AD 계정 사용

Azure AD 계정을 사용하여 Azure Portal에서 Blob 데이터에 액세스하려면 다음 두 가지를 모두 충족해야 합니다.

- Blob 데이터에 대한 액세스를 제공하는 기본 제공 또는 사용자 지정 역할을 할당 받았습니다.
- 최소한 스토리지 계정 수준 또는 더 높은 수준으로 범위가 지정된 Azure Resource Manager [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할을 할당 받았습니다. **Reader** 역할은 가장 제한된 사용 권한을 부여하지만, 스토리지 계정 관리 리소스에 대한 액세스 권한을 부여하는 다른 Azure Resource Manager 역할도 허용됩니다.

Azure Resource Manager **읽기 권한자** 역할은 사용자가 스토리지 계정 리소스를 볼 수 있지만 수정할 수는 없도록 허용합니다. Azure Storage에서 데이터에 대한 읽기 권한은 제공하지 않고 계정 관리 리소스에 대해서만 제공합니다. 사용자가 Azure Portal에서 Blob 컨테이너로 이동할 수 있도록 **읽기 권한자** 역할이 필요합니다.

Blob 데이터에 대한 액세스를 지원하는 기본 제공 역할에 관한 내용은 [Azure Active Directory를 사용하여 Blob에 대한 액세스 권한 부여](authorize-access-azure-active-directory.md)를 참조하세요.

사용자 지정 역할은 기본 제공 역할이 제공하는 권한과 동일하되 다른 조합으로 지원합니다. Azure 사용자 지정 역할을 생성하는 방법에 대해 자세히 알고 싶다면, [Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md) 및 [Azure 리소스에 대한 역할 정의 이해](../../role-based-access-control/role-definitions.md)를 참조하세요.

> [!NOTE]
> Azure Portal의 Storage Explorer 미리 보기 버전은 Azure AD 자격 증명을 사용한 Blob 데이터 확인 및 수정 기능을 지원하지 않습니다. Azure Portal의 Storage Explorer는 항상 계정 키를 사용하여 데이터에 액세스합니다. Azure Portal의 Storage Explorer를 사용하려면 **Microsoft.Storage/storageAccounts/listkeys/action** 이 포함된 역할을 할당받아야 합니다.

## <a name="navigate-to-blobs-in-the-azure-portal"></a>Azure Portal에서 Blob으로 이동

포털에서 Blob 데이터를 보려면 스토리지 계정에 대한 **개요** 로 이동하여 **Blob** 링크를 클릭합니다. 또는 메뉴에서 **컨테이너** 섹션으로 이동할 수도 있습니다.

:::image type="content" source="media/authorize-data-operations-portal/blob-access-portal.png" alt-text="Azure Portal에서 Blob 데이터로 이동하는 방법을 보여 주는 스크린샷":::

## <a name="determine-the-current-authentication-method"></a>현재 인증 방법 결정

컨테이너로 이동하면 Azure Portal에서 현재 인증에 계정 액세스 키를 사용하는지 아니면 Azure AD 계정을 사용하는지 표시합니다.

### <a name="authenticate-with-the-account-access-key"></a>계정 액세스 키를 사용하여 인증

계정 액세스 키를 사용하여 인증하려는 경우, 포털에서 인증 방법으로 지정된 **액세스 키** 가 표시됩니다.

:::image type="content" source="media/authorize-data-operations-portal/auth-method-access-key.png" alt-text="계정 키를 사용하여 현재 컨테이너에 액세스하는 사용자를 보여 주는 스크린샷":::

Azure AD 계정을 사용하도록 전환하려면 이미지에 강조 표시된 링크를 클릭합니다. 사용자에게 할당된 Azure 역할을 통해 적절한 권한이 있는 경우 계속 진행할 수 있습니다. 적절한 권한이 없다면, 다음과 같은 오류 메시지가 표시됩니다.

:::image type="content" source="media/authorize-data-operations-portal/auth-error-azure-ad.png" alt-text="Azure AD 계정이 액세스를 지원하지 않는 경우 표시되는 오류":::

Azure AD 계정이 볼 수 있는 권한이 없는 경우 목록에 Blob이 표시되지 않습니다. **액세스 키로 전환** 링크를 클릭하여 액세스 키를 인증에 다시 사용합니다.

### <a name="authenticate-with-your-azure-ad-account"></a>Azure AD 계정을 사용하여 인증

Azure AD 계정을 사용하여 인증하려는 경우, 포털에서 인증 방법으로 지정된 **Azure AD 사용자 계정** 이 표시됩니다.

:::image type="content" source="media/authorize-data-operations-portal/auth-method-azure-ad.png" alt-text="Azure AD 계정을 사용하여 현재 컨테이너에 액세스하는 사용자를 보여 주는 스크린샷":::

계정 액세스 키를 사용하도록 전환하려면, 이미지에 강조 표시된 링크를 클릭합니다. 계정 키에 대한 액세스 권한이 있는 경우, 계속 진행할 수 있습니다. 그러나 계정 키에 대한 액세스 권한이 없는 경우 다음과 같은 오류 메시지가 표시됩니다.

:::image type="content" source="media/authorize-data-operations-portal/auth-error-access-key.png" alt-text="계정 키에 대한 액세스 권한이 없는 경우 표시되는 오류":::

계정 키에 대한 액세스 권한이 없는 경우 목록에 Blob이 표시되지 않습니다. **Azure AD 사용자 계정으로 전환** 링크를 클릭하여 Azure AD 계정을 인증에 다시 사용합니다.

## <a name="specify-how-to-authorize-a-blob-upload-operation"></a>Blob 업로드 작업에 권한을 부여하는 방법 지정

Azure Portal에서 Blob을 업로드할 때 계정 액세스 키 또는 Azure AD 자격 증명을 사용하여 해당 작업을 인증하고 권한을 부여할지 여부를 지정할 수 있습니다. 기본적으로 포털은 [현재 인증 방법 확인](#determine-the-current-authentication-method)에 표시된 대로 현재 인증 방법을 사용합니다.

Blob 업로드 작업에 권한을 부여하는 방법을 지정하려면 다음 단계를 수행합니다.

1. Azure Portal에서 Blob을 업로드하려는 컨테이너로 이동합니다.
1. **업로드** 단추를 선택합니다.
1. **고급** 섹션을 확장하여 Blob에 대한 고급 속성을 표시합니다.
1. **인증 유형** 필드에서 다음 이미지와 같이 Azure AD 계정을 사용하거나 계정 액세스 키를 사용하여 업로드 작업에 권한을 부여할지 여부를 나타냅니다.

    :::image type="content" source="media/authorize-data-operations-portal/auth-blob-upload.png" alt-text="Blob 업로드에서 권한 부여 방법을 변경하는 방법을 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [Azure Storage의 데이터에 대한 액세스 권한 부여](../common/authorize-data-access.md)
- [Blob 데이터에 액세스하기 위한 Azure 역할 할당](assign-azure-role-data-access.md)
