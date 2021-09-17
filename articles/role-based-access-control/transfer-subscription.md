---
title: 다른 Azure AD 디렉터리로 Azure 구독 양도
description: Azure 구독 및 알려진 관련 리소스를 다른 Azure AD(Azure Active Directory) 디렉터리에 양도하는 방법에 대해 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 09/04/2021
ms.author: rolyon
ms.openlocfilehash: 19e9d6c76e30828b0aac0fba139963ff25ae2cba
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123542317"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory"></a>다른 Azure AD 디렉터리로 Azure 구독 양도

조직에는 여러 Azure 구독이 있을 수 있습니다. 각 구독은 특정 Azure AD(Azure Active Directory) 디렉터리와 연결됩니다. 관리를 용이하게 하기 위해 구독을 다른 Azure AD 디렉터리로 양도하려 할 수 있습니다. 다른 Azure AD 디렉터리로 구독을 양도하는 경우 일부 리소스는 대상 디렉터리에 전송되지 않습니다. 예를 들어 Azure RBAC(역할 기반 액세스 제어)의 모든 역할 할당 및 사용자 지정 역할은 소스 디렉터리에서 **영구적으로** 삭제되며 대상 디렉터리로 전송되지 않습니다.

이 문서에서는 다른 Azure AD 디렉터리로 구독을 양도하고 전송 후 일부 리소스를 다시 만들기 위해 수행할 수 있는 기본 단계를 설명합니다.

조직의 여러 디렉터리에 대한 구독 전송을 대신 **차단** 하려는 경우 구독 정책을 구성할 수 있습니다. 자세한 내용은 [Azure 구독 정책 관리](../cost-management-billing/manage/manage-azure-subscription-policy.md)를 참조하세요.

> [!NOTE]
> Azure CSP(클라우드 솔루션 공급자) 구독의 경우 해당 구독에 대한 Azure AD 디렉터리를 변경하는 것은 지원되지 않습니다.

## <a name="overview"></a>개요

다른 Azure AD 디렉터리에 Azure 구독을 양도하는 것은 신중하게 계획하고 실행해야 하는 복잡한 프로세스입니다. 대부분의 Azure 서비스는 정상적으로 작동하거나 다른 Azure 리소스를 관리하기 위해 보안 주체(ID)가 필요합니다. 이 문서에서는 보안 주체에 크게 의존하지만 포괄적이지는 않은 대부분의 Azure 서비스를 다루어보려 합니다.

> [!IMPORTANT]
> 일부 시나리오에서는 구독을 양도하는 동안 프로세스를 완료하기 위해 가동 중지 시간이 필요할 수 있습니다. 양도에 가동 중지 시간이 필요한지 여부를 평가하려면 신중한 계획이 필요합니다.

다음 다이어그램은 구독을 다른 디렉터리로 양도할 때 수행해야 하는 기본 단계를 보여줍니다.

1. 양도 준비

1. Azure 구독을 다른 디렉터리로 양도

1. 역할 할당, 사용자 지정 역할 및 관리| ID와 같은 대상 디렉터리에 리소스를 다시 만듭니다.

    ![구독 다이어그램 양도](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>다른 디렉터리에 구독을 양도할지 여부 결정

다음은 구독을 양도할 수 있는 몇 가지 이유입니다.

- 회사 합병 또는 인수로 인해 주 Azure AD 디렉터리에서 획득한 구독을 관리하려고 합니다.
- 조직의 누군가가 구독을 만들고 관리를 특정 Azure AD 디렉터리에 통합하려고 합니다.
- 특정 구독 ID 또는 URL에 종속된 애플리케이션이 있으며 애플리케이션 구성이나 코드를 수정하기가 쉽지 않습니다.
- 비즈니스의 일부가 별도의 회사로 분할되었으며 일부 리소스를 다른 Azure AD 디렉터리로 이동해야 합니다.
- 보안 격리를 위해 다른 Azure AD 디렉터리에서 일부 리소스를 관리하려고 합니다.

### <a name="alternate-approaches"></a>대체 방법

구독을 양도하려면 프로세스가 완료될 때까지 가동 중지 시간이 필요합니다. 시나리오에 따라 다음과 같은 대체 방법을 고려할 수 있습니다.

- 리소스를 다시 만들고 대상 디렉터리 및 구독에 데이터를 복사합니다.
- 다중 디렉터리 아키텍처를 채택하고 소스 디렉터리에 구독을 유지합니다. 대상 디렉터리의 사용자가 소스 디렉터리의 구독에 액세스할 수 있도록 Azure Lighthouse를 사용하여 리소스를 위임합니다. 자세한 내용은 [엔터프라이즈 시나리오의 Azure Lighthouse](../lighthouse/concepts/enterprise.md)를 참조하세요.

### <a name="understand-the-impact-of-transferring-a-subscription"></a>구독 양도의 영향 이해

여러 Azure 리소스는 구독 또는 디렉터리에 대한 종속성이 있습니다. 다음 표는 상황에 따라 구독을 양도할 때의 알려진 영향을 보여 줍니다. 이 문서의 단계를 수행하여 구독 양도 이전에 존재했던 리소스 중 일부를 다시 만들 수 있습니다.

> [!IMPORTANT]
> 이 섹션에서는 구독에 종속된 알려진 Azure 서비스 또는 리소스를 나열합니다. Azure의 리소스 종류가 지속적으로 진화하기 때문에 환경에 대한 호환성이 손상되는 변경을 일으킬 수 있는 추가 종속성이 여기에 나열되지 않을 수 있습니다. 

| 서비스 또는 리소스 | 영향 받음 | Recoverable | 영향을 받고 있나요? | 수행 가능한 작업 |
| --------- | --------- | --------- | --------- | --------- |
| 역할 할당 | 예 | 예 | [역할 할당 나열](#save-all-role-assignments) | 모든 역할 할당이 영구적으로 삭제됩니다. 사용자, 그룹 및 서비스 주체를 대상 디렉터리의 해당 개체에 매핑해야 합니다. 역할 할당을 다시 만들어야 합니다. |
| 사용자 지정 역할 | 예 | 예 | [사용자 지정 역할 나열](#save-custom-roles) | 모든 사용자 지정 역할은 영구적으로 삭제됩니다. 사용자 지정 역할 및 역할 할당을 다시 만들어야 합니다. |
| 시스템 할당 관리 ID | 예 | 예 | [관리 ID 나열](#list-role-assignments-for-managed-identities) | 관리 ID를 사용하지 않도록 설정했다가 다시 사용하도록 설정해야 합니다. 역할 할당을 다시 만들어야 합니다. |
| 사용자 할당 관리 ID | 예 | 예 | [관리 ID 나열](#list-role-assignments-for-managed-identities) | 관리 ID를 삭제하고 다시 만든 다음 적절한 리소스에 연결해야 합니다. 역할 할당을 다시 만들어야 합니다. |
| Azure Key Vault | 예 | 예 | [Key Vault 액세스 정책 나열](#list-key-vaults) | 키 자격 증명 모음과 연결된 테넌트 ID를 업데이트해야 합니다. 새 액세스 정책을 제거하고 추가해야 합니다. |
| Azure AD 인증 통합을 사용하도록 설정된 Azure SQL 데이터베이스 | 예 | 예 | [Azure AD 인증을 사용하여 Azure SQL 데이터베이스 확인](#list-azure-sql-databases-with-azure-ad-authentication) | Azure AD 인증이 사용하도록 설정된 Azure SQL Database는 다른 디렉터리로 전송할 수 없습니다. 자세한 내용은 [Azure Active Directory 인증 사용](../azure-sql/database/authentication-aad-overview.md)을 참조하세요. | 
| Azure Storage 및 Azure Data Lake Storage Gen2 | 예 | 예 |  | ACL을 다시 만들어야 합니다. |
| Azure Data Lake Storage Gen1 | 예 | 예 |  | ACL을 다시 만들어야 합니다. |
| Azure 파일 | 예 | 예 |  | ACL을 다시 만들어야 합니다. |
| Azure 파일 동기화 | 예 | 예 |  | 저장소 동기화 서비스 및/또는 저장소 계정은 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. 자세한 내용은 [Azure Files FAQ(질문과 대답)](../storage/files/storage-files-faq.md#azure-file-sync)를 참조하세요. |
| Azure Managed Disks | 예 | 예 |  |  디스크 암호화 집합을 사용하여 고객 관리형 키로 Managed Disks를 암호화하는 경우, 디스크 암호화 집합과 연결된 시스템 할당 ID를 사용하지 않도록 설정하고 다시 사용하도록 설정해야 합니다. 그리고 역할 할당을 다시 만들어야 합니다. 즉, Key Vault에서 디스크 암호화 집합에 필요한 사용 권한을 다시 부여합니다. |
| Azure Kubernetes Service | 예 | 아니요 |  | AKS 클러스터 및 연결된 해당 리소스는 다른 디렉터리로 전송할 수 없습니다. 자세한 내용은 [AKS(Azure Kubernetes Service)에 대한 질문과 대답](../aks/faq.md)을 참조하세요. |
| Azure Policy | 예 | 예 | 사용자 지정 정의, 할당, 예외 및 규정 준수 데이터를 비롯한 모든 Azure Policy 개체입니다. | 정의를 [내보내고](../governance/policy/how-to/export-resources.md), 가져오고, 다시 할당해야 합니다. 그런 다음 새 정책 할당과 필요한 [정책 예외](../governance/policy/concepts/exemption-structure.md)를 만듭니다. |
| Azure Active Directory Domain Services | 예 | 아니요 |  | Azure AD Domain Services 관리되는 도메인은 다른 디렉터리로 전송할 수 없습니다. 자세한 내용은 [Azure AD(Active Directory) Domain Services에 대한 FAQ(자주 묻는 질문)](../active-directory-domain-services/faqs.yml)를 참조하세요. |
| 앱 등록 | 예 | 예 |  |  |

> [!WARNING]
> 양도 중인 동일한 구독에 **없는** 키 자격 증명 모음에 대한 종속성이 있는 리소스(예: 스토리지 계정 또는 SQL 데이터베이스)에 미사용 암호화를 사용하는 경우 복구할 수 없는 시나리오가 발생할 수 있습니다. 이러한 상황이 발생하는 경우 다른 키 자격 증명 모음을 사용하거나 고객 관리형 키를 일시적으로 사용하지 않도록 설정하여 이 복구할 수 없는 시나리오를 방지해야 합니다.

구독을 양도할 때 영향을 받는 일부 Azure 리소스 목록을 얻으려면 [Azure Resource Graph](../governance/resource-graph/overview.md)쿼리를 실행할 수도 있습니다. 샘플 쿼리는 [Azure 구독을 전송할 때 영향을 받은 리소스 나열을 참조하세요.](../governance/resource-graph/samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)

## <a name="prerequisites"></a>필수 구성 요소

이러한 단계를 완료하려면 다음이 필요합니다.

- [Azure Cloud Shell의 Bash](../cloud-shell/overview.md) 또는 [Azure CLI](/cli/azure)
- 소스 디렉터리에서 전송하려는 구독의 계정 관리자
- 디렉터리를 변경하는 사용자의 원본 및 대상 디렉터리 모두에 있는 사용자 계정

## <a name="step-1-prepare-for-the-transfer"></a>1단계: 양도 준비

### <a name="sign-in-to-source-directory"></a>소스 디렉터리에 로그인

1. 관리자 권한으로 Azure AD에 로그인합니다.

1. [az account list](/cli/azure/account#az_account_list) 명령을 사용하여 구독 목록을 가져옵니다.

    ```azurecli
    az account list --output table
    ```

1. [az account set](/cli/azure/account#az_account_set)를 사용하여 양도하려는 활성 구독을 설정합니다.

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-azure-resource-graph-extension"></a>Azure Resource Graph 확장을 설치합니다.

 [Azure Resource Graph](../governance/resource-graph/index.yml)의 Azure CLI 확장인 *resource-graph* 를 사용하면 [az graph](/cli/azure/graph) 명령을 사용하여 Azure Resource Manager에서 관리하는 리소스를 쿼리할 수 있습니다. 이후 단계에서 이 명령을 사용합니다.

1. [az extension list](/cli/azure/extension#az_extension_list)를 사용하여 *resource-graph* 확장이 설치되어 있는지 확인합니다.

    ```azurecli
    az extension list
    ```

1. 그렇지 않으면 *resource-graph* 확장을 설치합니다.

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>모든 역할 할당 저장

1. [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list)를 사용하여 모든 역할 할당(상속된 역할 할당 포함)을 나열합니다.

    목록을 보다 쉽게 검토할 수 있도록 출력을 JSON, TSV 또는 표로 내보낼 수 있습니다. 자세한 내용은 [Azure RBAC 및 Azure CLI를 사용하여 역할 할당 나열](role-assignments-list-cli.md)을 참조하세요.

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. 역할 할당 목록을 저장합니다.

    구독을 양도할 때 모든 역할 할당이 **영구적** 으로 삭제되므로 복사본을 저장하는 것이 중요합니다.

1. 역할 할당의 목록을 검토합니다. 대상 디렉터리에는 역할 할당이 필요하지 않을 수 있습니다.

### <a name="save-custom-roles"></a>사용자 지정 역할 저장

1. [az role definition list](/cli/azure/role/definition#az_role_definition_list)를 사용하여 사용자 지정 역할을 나열합니다. 자세한 내용은 [Azure CLI를 사용하여 사용자 지정 역할 만들기 또는 업데이트](custom-roles-cli.md)를 참조하세요.

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. 대상 디렉터리에 필요한 각 사용자 지정 역할을 별도의 JSON 파일로 저장합니다.

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. 사용자 지정 역할 파일의 복사본을 만듭니다.

1. 다음 형식을 사용하도록 각 복사본을 수정합니다.

    나중에 이러한 파일을 사용하여 대상 디렉터리에 사용자 지정 역할을 다시 만듭니다.

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>사용자, 그룹 및 서비스 주체 매핑 결정

1. 역할 할당 목록에 따라 대상 디렉터리에서 매핑할 사용자, 그룹 및 서비스 주체를 결정합니다.

    각 역할 할당의 `principalType` 속성을 살펴보면 보안 주체의 유형을 식별할 수 있습니다.

1. 필요한 경우 대상 디렉터리에서 필요한 모든 사용자, 그룹 또는 서비스 주체를 만듭니다.

### <a name="list-role-assignments-for-managed-identities"></a>관리 ID에 대한 역할 할당 나열

구독이 다른 디렉터리로 양도되면 관리 ID는 업데이트되지 않습니다. 결과적으로, 기존의 시스템에서 할당하거나 사용자가 할당한 관리 ID가 모두 손상됩니다. 양도 후 시스템 할당 관리 ID를 다시 사용하도록 설정할 수 있습니다. 사용자 할당 관리 ID의 경우 대상 디렉터리에서 다시 만들고 연결해야 합니다.

1. [관리 ID를 지원하는 Azure 서비스 목록](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)을 검토하여 관리 ID를 사용할 수 있는 위치를 확인합니다.

1. [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list)를 사용하여 시스템 할당 및 사용자 할당 관리 ID를 나열합니다.

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. 관리 ID 목록에서 시스템이 할당한 ID 및 사용자가 할당한 ID를 확인합니다. 다음 조건을 사용하여 형식을 결정할 수 있습니다.

    | 조건 | 관리 ID 유형 |
    | --- | --- |
    | `alternativeNames` 속성에는 `isExplicit=False`가 포함됩니다. | 시스템 할당 |
    | `alternativeNames` 속성에는 `isExplicit`가 포함되지 않습니다. | 시스템 할당 |
    | `alternativeNames` 속성에는 `isExplicit=True`가 포함됩니다. | 사용자 할당 |

    [az identity list](/cli/azure/identity#az_identity_list)를 사용하여 사용자 할당 관리 ID만 나열할 수도 있습니다. 자세한 내용은 [Azure CLI를 사용하여 사용자 할당 관리 ID 만들기, 나열 또는 삭제](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)를 참조하세요.

    ```azurecli
    az identity list
    ```

1. 관리 ID의 `objectId` 값 목록을 가져옵니다.

1. 역할 할당 목록을 검색하여 관리 ID에 대한 역할 할당이 있는지 확인합니다.

### <a name="list-key-vaults"></a>주요 자격 증명 모음 나열

키 자격 증명 모음을 만들 때 생성된 해당 구독에 대한 기본 Azure Active Directory 테넌트 ID에 자동으로 연결됩니다. 모든 액세스 정책 항목은 이 테넌트 ID에 연결됩니다. 자세한 내용은 [Azure Key Vault를 다른 구독으로 이동](../key-vault/general/move-subscription.md)을 참조하세요.

> [!WARNING]
> 양도 중인 동일한 구독에 **없는** 키 자격 증명 모음에 대한 종속성이 있는 리소스(예: 스토리지 계정 또는 SQL 데이터베이스)에 미사용 암호화를 사용하는 경우 복구할 수 없는 시나리오가 발생할 수 있습니다. 이러한 상황이 발생하는 경우 다른 키 자격 증명 모음을 사용하거나 고객 관리형 키를 일시적으로 사용하지 않도록 설정하여 이 복구할 수 없는 시나리오를 방지해야 합니다.

- 키 자격 증명 모음을 사용하는 경우 [az keyvault show](/cli/azure/keyvault#az_keyvault_show)를 사용하여 액세스 정책을 나열합니다. 자세한 내용은 [Key Vault 액세스 정책 할당](../key-vault/general/assign-access-policy-cli.md)을 참조하세요.

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Azure AD 인증을 사용하여 Azure SQL 데이터베이스 나열

- [az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) 및 [az graph](/cli/azure/graph) 확장을 사용하여 Azure AD 인증 통합을 사용하도록 설정된 Azure SQL 데이터베이스를 사용하고 있는지 확인합니다. 자세한 내용은 [SQL을 사용하여 Azure Active Directory 인증 구성 및 관리](../azure-sql/database/authentication-aad-configure.md)를 참조하세요.

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>ACL 나열

1. Azure Data Lake Storage Gen1을 사용하는 경우 Azure Portal 또는 PowerShell을 사용하여 모든 파일에 적용되는 ACL을 나열합니다.

1. Azure Data Lake Storage Gen2를 사용하는 경우 Azure Portal 또는 PowerShell을 사용하여 모든 파일에 적용되는 ACL을 나열합니다.

1. Azure Files를 사용하는 경우 모든 파일에 적용되는 ACL을 나열합니다.

### <a name="list-other-known-resources"></a>기타 알려진 리소스 나열

1. [az account show](/cli/azure/account#az_account_show)를 사용하여 구독 ID를 가져옵니다(`bash`에서).

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"//' -e 's/\r$//')
    ```
    
1. [az graph](/cli/azure/graph) 확장을 사용하여 알려진 Azure AD 디렉터리 종속성이 있는 다른 Azure 리소스를 나열합니다(`bash`에서).

    ```azurecli
    az graph query -q 'resources 
        | where type != "microsoft.azureactivedirectory/b2cdirectories" 
        | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true 
        | project name, type, kind, identity, tenantId, properties.tenantId' --subscriptions $subscriptionId --output yaml
    ```

## <a name="step-2-transfer-the-subscription"></a>2단계: 구독 양도

이 단계에서는 구독을 소스 디렉터리에서 대상 디렉터리로 양도합니다. 이러한 단계는 청구 소유권 또한 양도할지 여부에 따라 달라집니다.

> [!WARNING]
> 구독을 양도하면 소스 디렉터리의 모든 역할 할당이 **영구적으로** 삭제되며 복원할 수 없습니다. 구독을 양도한 후에는 다시 되돌릴 수 없습니다. 이 단계를 수행하기 전에 이전 단계를 완료해야 합니다.

1. 또한 청구 소유권 또한 다른 계정으로 양도할지 여부를 결정합니다.

1. 다른 디렉터리로 구독을 양도합니다.

    - 현재 청구 소유권을 유지하려면 [Azure Active Directory 테넌트에 Azure 구독 연결 또는 추가](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)의 단계를 따르세요.
    - 또한 청구 소유권을 양도하려면 [Azure 구독의 청구 소유권을 다른 계정에 양도](../cost-management-billing/manage/billing-subscription-transfer.md)의 단계를 따르세요. 다른 디렉터리로 구독을 양도하려면 **Azure AD 테넌트 구독** 확인란을 선택해야 합니다.

1. 구독 양도가 완료되면 이 문서로 돌아와서 대상 디렉터리에 리소스를 다시 만듭니다.

## <a name="step-3-re-create-resources"></a>3단계: 리소스 다시 만들기

### <a name="sign-in-to-target-directory"></a>대상 디렉터리에 로그인

1. 대상 디렉터리에서 이전 요청을 수락한 사용자로 로그인합니다.

    이전 요청을 수락한 새 계정의 사용자에게만 리소스를 관리할 수 있는 액세스 권한이 제공됩니다.

1. [az account list](/cli/azure/account#az_account_list) 명령을 사용하여 구독 목록을 가져옵니다.

    ```azurecli
    az account list --output table
    ```

1. [az account set](/cli/azure/account#az_account_set)으로 사용하려는 활성 구독을 설정합니다.

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>사용자 지정 역할 만들기
        
- [az role definition create](/cli/azure/role/definition#az_role_definition_create)을 사용하여 이전에 만든 파일에서 각 사용자 지정 역할을 만듭니다. 자세한 내용은 [Azure CLI를 사용하여 사용자 지정 역할 만들기 또는 업데이트](custom-roles-cli.md)를 참조하세요.

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="assign-roles"></a>역할 할당

- [az role assign create](/cli/azure/role/assignment#az_role_assignment_create)을 사용하여 사용자, 그룹 및 서비스 주체에게 역할을 할당합니다. 자세한 내용은 [Azure CLI를 사용하여 Azure 역할 할당](role-assignments-cli.md)을 참조하세요.

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>시스템 할당 관리 ID 업데이트

1. 시스템 할당 관리 ID를 사용하지 않도록 설정하고 다시 사용하도록 설정합니다.

    | Azure 서비스 | 자세한 정보 | 
    | --- | --- |
    | 가상 머신 | [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | 가상 머신 크기 집합 | [Azure CLI를 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | 기타 서비스 | [Azure 리소스에 대한 관리 ID를 지원하는 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. [az role assign create](/cli/azure/role/assignment#az_role_assignment_create)를 사용하여 시스템 할당 관리 ID에 역할을 할당합니다. 자세한 내용은 [Azure CLI를 사용하여 리소스에 대한 관리 ID 액세스 할당](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)을 참조하세요.

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>사용자 할당 관리 ID 업데이트

1. 사용자 할당 관리 ID를 삭제하고, 다시 만들고, 연결합니다.

    | Azure 서비스 | 자세한 정보 | 
    | --- | --- |
    | 가상 머신 | [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | 가상 머신 크기 집합 | [Azure CLI를 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | 기타 서비스 | [Azure 리소스에 대한 관리 ID를 지원하는 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Azure CLI를 사용하여 사용자 할당 관리 ID 생성, 나열 또는 삭제](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. [az role assign create](/cli/azure/role/assignment#az_role_assignment_create)를 사용하여 사용자 할당 관리 ID에 역할을 할당합니다. 자세한 내용은 [Azure CLI를 사용하여 리소스에 대한 관리 ID 액세스 할당](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)을 참조하세요.

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>키 자격 증명 모음 업데이트

이 섹션에서는 키 자격 증명 모음을 업데이트하는 기본 단계에 대해 설명합니다. 자세한 내용은 [Azure Key Vault를 다른 구독으로 이동](../key-vault/general/move-subscription.md)을 참조하세요.

1. 이 구독에 있는 모든 기존 키 자격 증명 모음과 연결된 테넌트 ID를 대상 디렉터리로 업데이트합니다.

1. 모든 기존 액세스 정책 항목을 제거합니다.

1. 대상 디렉터리와 연결된 새 액세스 정책 항목을 추가합니다.

### <a name="update-acls"></a>ACL 업데이트

1. Azure Data Lake Storage Gen1을 사용하는 경우 적절한 ACL을 할당합니다. 자세한 내용은 [Azure Data Lake Storage Gen1에 저장된 데이터 보안](../data-lake-store/data-lake-store-secure-data.md)을 참조하세요.

1. Azure Data Lake Storage Gen2를 사용하는 경우 적절한 ACL을 할당합니다. 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](../storage/blobs/data-lake-storage-access-control.md)를 참조하세요.

1. Azure Files를 사용하는 경우 적절한 ACL을 할당합니다.

### <a name="review-other-security-methods"></a>기타 보안 방법 검토

양도 과정에서 역할 할당이 제거되더라도 원래 소유자 계정에 속한 사용자는 다음을 비롯한 다른 보안 방법을 통해 구독에 계속 액세스할 수 있습니다.

- 스토리지와 같은 서비스에 대한 액세스 키.
- 구독 리소스에 대한 관리자 액세스를 사용자에게 부여하는 [관리 인증서](../cloud-services/cloud-services-certs-create.md).
- Azure Virtual Machines 같은 서비스에 대한 원격 액세스 자격 증명.

대상 디렉터리에서 액세스할 수 없도록 소스 디렉터리의 사용자에 대한 액세스 권한을 제거하려면 자격 증명을 회전하는 것을 고려해야 합니다. 자격 증명을 업데이트하기 전까지는 사용자가 양도한 후에도 계속 액세스할 수 있습니다.

1. 스토리지 계정 액세스 키 회전 자세한 내용은 [스토리지 계정 액세스 키 관리](../storage/common/storage-account-keys-manage.md)를 참조하세요.

1. Azure SQL Database 또는 Azure Service Bus Messaging과 같은 다른 서비스에 대한 액세스 키를 사용하는 경우 액세스 키를 회전합니다.

1. 비밀을 사용하는 리소스의 경우 리소스에 대한 설정을 열고 암호를 업데이트합니다.

1. 인증서를 사용하는 리소스의 경우 인증서를 업데이트합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 구독의 청구 소유권을 다른 계정에 양도](../cost-management-billing/manage/billing-subscription-transfer.md)
- [구독자와 CSP 간에 Azure 구독 전송](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Azure Active Directory 테넌트에 Azure 구독 연결 또는 추가](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
- [엔터프라이즈 시나리오의 Azure Lighthouse](../lighthouse/concepts/enterprise.md)
