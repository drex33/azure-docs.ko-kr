---
title: Azure CLI를 사용하여 Blob 데이터에 대한 액세스 권한을 부여하는 방법 선택
titleSuffix: Azure Storage
description: Azure CLI를 사용하여 Blob 데이터에 대한 데이터 작업 권한을 부여하는 방법을 지정합니다. Azure AD 자격 증명을 사용하여 계정 액세스 키 또는 SAS(공유 액세스 서명) 토큰으로 데이터 작업에 권한을 부여할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/12/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 537259315a766da179a47defc277da5abecc4779
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859155"
---
# <a name="choose-how-to-authorize-access-to-blob-data-with-azure-cli"></a>Azure CLI를 사용하여 Blob 데이터에 대한 액세스 권한을 부여하는 방법 선택

Azure Storage는 Blob 데이터에 대한 작업 권한을 부여하는 방법을 지정할 수 있는 Azure CLI를 제공합니다. 다음과 같은 방법으로 데이터 작업에 권한을 부여할 수 있습니다.

- Azure AD(Azure Active Directory) 보안 주체를 사용합니다. Microsoft는 Azure AD 자격 증명을 사용하여 보안을 강화하고 사용 편의성을 높이는 것이 좋습니다.
- 계정 액세스 키 또는 SAS(공유 액세스 서명) 토큰을 사용합니다.

## <a name="specify-how-data-operations-are-authorized"></a>데이터 작업에 권한을 부여하는 방법 지정

Blob 데이터를 읽고 쓰기 위한 Azure CLI 명령에는 선택적 `--auth-mode`매개 변수가 있습니다. 데이터 작업에 권한을 부여하는 방법을 표시하려면 선택적 매개 변수를 지정합니다.

- Azure AD 보안 주체를 사용하여 로그인하려면 `--auth-mode`매개 변수`login`를 설정합니다(권장).
- 권한 부여에 사용할 계정 액세스 키를 검색하려는 경우 `--auth-mode`매개 변수를 레거시`key` 값으로 설정합니다. `--auth-mode`매개 변수를 생략하는 경우에도 Azure CLI는 액세스 키를 검색하려고 시도합니다.

`--auth-mode`매개 변수를 사용하려면 Azure CLI 버전 2.0.46 이상을 설치했는지 확인합니다. `az --version`을 실행하여 설치된 버전을 확인합니다.

> [!NOTE]
> Azure Resource Manager **읽기 전용** 잠금을 사용하여 스토리지 계정이 잠긴 경우, 해당 스토리지 계정에 대한 [키 나열](/rest/api/storagerp/storageaccounts/listkeys) 작업이 허용되지 않습니다. **키 나열** 은 POST 작업으로, 계정에 대해 **읽기 전용** 잠금이 설정된 경우 모든 POST 작업이 차단됩니다. 이러한 이유로 계정이 **ReadOnly** 잠금으로 잠겨 있는 경우 계정 키를 아직 소유하지 않은 사용자는 Azure AD 자격 증명을 사용하여 Blob 데이터에 액세스해야 합니다.

> [!IMPORTANT]
> `--auth-mode`매개 변수를 생략 또는 `key`로 설정하는 경우, Azure CLI은 권한 부여를 위해 계정 액세스 키를 사용하려고 시도합니다. 해당 경우 명령 또는 **AZURE_STORAGE_KEY** 환경 변수에 액세스 키를 제공하는 것이 좋습니다. 환경 변수에 대한 자세한 내용은 [권한 부여 매개 변수에 대한 환경 변수 설정](#set-environment-variables-for-authorization-parameters) 섹션을 참조하세요.
>
> 액세스 키를 제공하지 않으면 Azure CLI은 Azure Storage 리소스 공급자를 호출하여 각 작업에 대해 검색하려고 시도합니다. 리소스 공급자를 호출해야 하는 데이터 작업을 많이 수행하면 제한이 발생할 수 있습니다. 리소스 공급자 제한에 대한 자세한 내용은 [Azure Storage 리소스 공급자에 대한 스케일링 가능성 및 성능 목표](../common/scalability-targets-resource-provider.md)를 참조하세요.

## <a name="authorize-with-azure-ad-credentials"></a>Azure AD 자격 증명을 사용하여 권한 부여

Azure AD 자격 증명을 사용하여 Azure CLI에 로그인하면 OAuth 2.0 액세스 토큰이 반환됩니다. 해당 토큰은 Azure CLI에서 Blob 또는 Queue Storage에 대한 후속 데이터 작업에 권한을 부여하는 데 자동으로 사용됩니다. 지원되는 작업의 경우, 더 이상 명령과 함께 계정 키 또는 SAS 토큰을 전달할 필요가 없습니다.

Azure RBAC(Azure 역할 기반 액세스 제어)를 통해 Azure AD 보안 주체에 Blob 데이터에 대한 사용 권한을 할당할 수 있습니다. Azure Storage에서 Azure 역할을 할당하는 방법에 관한 자세한 내용은 [Blob 데이터 액세스를 위해 Azure 역할 할당](assign-azure-role-data-access.md)을 참조하세요.

### <a name="permissions-for-calling-data-operations"></a>데이터 작업 호출에 대한 사용 권한

Blob 데이터에 작업에 대한 Azure Storage 확장을 지원합니다. 호출할 수 있는 작업은 Azure CLI 로그인하는 Azure AD 보안 주체에 부여된 권한에 따라 달라집니다. Azure Storage 컨테이너에 대한 권한은 Azure RBAC를 통해 할당됩니다. 예를 들어, **Storage Blob Data Reader** 역할이 할당되면 컨테이너에서 데이터를 읽는 스크립팅 명령을 실행할 수 있습니다. **Storage Blob Data Contributor** 역할이 할당된 경우 컨테이너 또는 컨테이너에 포함된 데이터를 읽거나, 쓰거나, 삭제하는 스크립팅 명령을 실행할 수 있습니다.

컨테이너의 각 Azure Storage 작업에 필요한 사용 권한에 대한 자세한 내용은 [OAuth 토큰을 이용한 스토리지 작업 호출](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)을 참조하세요.

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>예제: Azure AD 자격 증명을 사용하여 컨테이너를 만들도록 작업 권한 부여

다음 예제에서는 Azure AD 자격 증명을 사용하여 Azure CLI에서 컨테이너를 만드는 방법을 보여 줍니다. 컨테이너를 만들려면 Azure CLI 로그인해야 하며 리소스 그룹 및 스토리지 계정이 필요합니다. 해당 리소스를 만드는 방법을 알아보려면, [빠른 시작: Azure CLI을 사용하여 Blob 생성, 다운로드, 나열하기](../blobs/storage-quickstart-blobs-cli.md)를 참조하세요.

1. 컨테이너를 만들기 전에 [Storage Blob 데이터 참가자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 역할을 자신에게 할당합니다. 계정 소유자라도 스토리지 계정에 대한 데이터 작업을 수행하려면 명시적 권한이 필요합니다. Azure 역할을 할당하는 방법에 관한 자세한 내용은 [Blob 데이터에 액세스하기 위한 Azure 역할 할당](assign-azure-role-data-access.md)을 참조하세요.

    > [!IMPORTANT]
    > Azure 역할 할당을 전파하는 데 몇 분 정도 걸릴 수 있습니다.

1. `login`를 `--auth-mode`매개 변수로 설정함으로써, [az storage container create](/cli/azure/storage/container#az_storage_container_create)명령을 호출하여 Azure AD 자격 증명을 사용한 컨테이너를 만듭니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>계정 액세스 키를 사용하여 권한 부여

계정 키를 소유하는 경우 모든 Azure Storage 데이터 작업을 호출할 수 있습니다. 일반적으로 계정 키를 사용하는 것은 안전하지 않습니다. 계정 키가 손상되면, 계정에 있는 모든 데이터가 손상될 수 있습니다.

다음 예제는 계정 액세스 키를 사용하여 컨테이너를 만드는 방법을 보여 줍니다. 계정 키를 지정하고 다음 `key`값으로 `--auth-mode`매개 변수를 제공합니다.

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!IMPORTANT]
> Azure Resource Manager **읽기 전용** 잠금을 사용하여 스토리지 계정이 잠긴 경우, 해당 스토리지 계정에 대한 [키 나열](/rest/api/storagerp/storageaccounts/listkeys) 작업이 허용되지 않습니다. **키 나열** 은 POST 작업으로, 계정에 대해 **읽기 전용** 잠금이 설정된 경우 모든 POST 작업이 차단됩니다. 이런 이유로 인해 계정이 **읽기 전용** 으로 잠겨 있는 경우 사용자는 Azure AD 자격 증명을 사용하여 데이터에 액세스해야 합니다.

## <a name="authorize-with-a-sas-token"></a>SAS 토큰을 사용하여 권한 부여

SAS 토큰을 소유하는 경우 SAS에서 허용하는 데이터 작업을 호출할 수 있습니다. 다음 예제는 SAS 토큰을 사용하여 컨테이너를 만드는 방법을 보여 줍니다.

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>권한 부여 매개 변수에 대한 환경 변수 설정

환경 변수에서 권한 부여 매개 변수를 지정하여 Azure Storage 데이터 작업에 대한 모든 호출에 포함되지 않도록 할 수 있습니다. 다음 테이블에서는 사용 가능한 환경 변수를 설명합니다.

| 환경 변수                  | Description                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    스토리지 계정 이름입니다. 해당 변수는 스토리지 계정 키 또는 SAS 토큰과 함께 사용해야 합니다. 둘 다 없는 경우, Azure CLI는 인증된 Azure AD 계정으로 스토리지 계정 액세스 키 검색을 시도합니다. 한 번에 많은 명령을 실행되는 경우, Azure Storage 리소스 공급자 제한이 발생할 수 있습니다. 리소스 공급자 제한에 대한 자세한 내용은 [Azure Storage 리소스 공급자에 대한 스케일링 가능성 및 성능 목표](../common/scalability-targets-resource-provider.md)를 참조하세요.             |
|    AZURE_STORAGE_KEY                  |    스토리지 계정 키입니다. 해당 변수는 스토리지 계정 이름과 함께 사용해야 합니다.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    스토리지 계정 키 또는 SAS 토큰을 포함하는 연결 문자열입니다. 해당 변수는 스토리지 계정 이름과 함께 사용해야 합니다.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    SAS(공유 액세스 서명) 토큰입니다. 해당 변수는 스토리지 계정 이름과 함께 사용해야 합니다.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    명령을 실행할 때 사용되는 권한 부여 모드입니다. 허용되는 값은 `login`(권장) 또는 `key`입니다. `login`을 지정하는 경우, Azure CLI는 Azure AD 자격 증명을 사용하여 데이터 작업에 권한을 부여합니다. 레거시`key` 모드를 지정하는 경우, Azure CLI는 계정 액세스 키에 대한 쿼리를 시도하고 키를 사용하여 명령에 권한을 부여합니다.    |

## <a name="next-steps"></a>다음 단계

- [Blob 데이터에 액세스하기 위한 Azure 역할 할당](assign-azure-role-data-access.md)
- [Azure 리소스에 대한 관리 ID를 사용하여 Blob 데이터에 대한 액세스 권한 부여](authorize-managed-identity.md)
