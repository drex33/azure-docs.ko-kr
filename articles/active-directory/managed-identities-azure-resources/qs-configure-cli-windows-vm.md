---
title: Azure CLI를 사용하여 Azure VM에서 관리 ID 구성 - Azure AD
description: Azure CLI를 사용하여 Azure VM에서 시스템 및 사용자 할당 관리 ID를 구성하기 위한 단계별 지침을 제공합니다.
author: barclayn
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 205661a32d938352ae81073668843c569825cc8e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679611"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-azure-cli"></a>Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 리소스용 관리 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 다음과 같은 관리 ID 작업을 수행하는 방법을 알아봅니다.

- Azure VM에서 시스템 할당 관리 ID를 사용 및 사용하지 않도록 설정
- Azure VM에서 사용자 할당 관리 ID 추가 및 제거

아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 리소스에 대한 관리 ID에 익숙하지 않은 경우 [Azure 리소스에 대한 관리 ID란?](overview.md)을 참조하세요. 시스템 할당 및 사용자 할당 관리 ID 형식에 대한 자세한 내용은 [관리 ID 형식](overview.md#managed-identity-types)을 참조하세요.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

이 섹션에서는 Azure CLI를 사용하여 Azure VM에서 시스템 할당 관리 ID를 사용 및 사용하지 않도록 설정하는 방법을 알아봅니다.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Azure VM 생성 중에 시스템 할당 관리 ID를 사용하도록 설정

시스템 할당 관리 ID를 사용하도록 설정된 Azure VM을 만들려면 계정에 [Virtual Machine 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할 할당이 필요합니다.  추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. [az group create](/cli/azure/group/#az_group_create)를 사용하여 VM 및 관련 리소스를 포함하고 배포하는 데 사용할 [리소스 그룹](../../azure-resource-manager/management/overview.md#terminology)을 만듭니다. 대신 사용하려는 리소스 그룹이 이미 있다면 이 단계를 건너뛰어도 됩니다.

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. [az vm create](/cli/azure/vm/#az_vm_create)를 사용하여 VM을 만듭니다. 다음 예제에서는 `--assign-identity` 매개 변수의 요청에 따라 시스템 할당 관리 ID를 사용하여 *myVM* 이라는 VM을 만듭니다. `--admin-username` 및 `--admin-password` 매개 변수는 가상 머신 로그인을 위한 관리자 이름 및 암호 계정을 지정합니다. 이러한 값은 사용자 환경에 적절하게 업데이트합니다. 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>기존 Azure VM에서 시스템 할당 관리 ID를 사용하도록 설정

VM에서 시스템 할당 관리 ID를 사용하도록 설정하려면 계정에 [가상 머신 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할 할당이 필요합니다.  추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. Azure CLI를 로컬 콘솔에서 사용하는 경우 [az login](/cli/azure/reference-index#az_login)을 사용하여 먼저 Azure에 로그인합니다. VM을 포함하는 Azure 구독과 연결된 계정을 사용합니다.

   ```azurecli-interactive
   az login
   ```

2. [az vm identity assign](/cli/azure/vm/identity/)을 `identity assign` 명령과 함께 사용하여 기존 VM에 시스템 할당 ID를 사용하도록 설정합니다.

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Azure VM에서 시스템 할당 ID를 사용하지 않도록 설정

VM에서 시스템 할당 관리 ID를 사용하지 않도록 설정하려면 계정에 [가상 머신 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할 할당이 필요합니다.  추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

시스템 할당 ID는 더 이상 필요하지 않고 사용자 할당 ID는 여전히 필요한 가상 머신이 있는 경우 다음 명령을 사용합니다.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

가상 머신에 시스템 할당 ID가 더 이상 필요하지 않고 사용자 할당 ID가 없는 경우 다음 명령을 사용합니다.

> [!NOTE]
> `none` 값은 대/소문자를 구분합니다. 소문자여야 합니다. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```


## <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

이 섹션에서는 Azure CLI를 사용하여 Azure VM에서 사용자 할당 관리 ID를 추가하고 제거하는 방법을 알아봅니다. VM과 다른 RG에서 사용자 할당 관리 ID를 만드는 경우 관리 ID의 URL을 사용하여 VM에 할당해야 합니다.
예: `--identities "/subscriptions/<SUBID>/resourcegroups/<RESROURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER_ASSIGNED_ID_NAME>"`

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Azure VM 생성 중에 사용자 할당 관리 ID 할당

VM을 만드는 중 VM에 사용자 할당 ID를 할당하려면 계정에 [가상 머신 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 및 [관리 ID 운영자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 역할 할당이 필요합니다. 추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. 사용하려는 리소스 그룹이 이미 있다면 이 단계를 건너뛰어도 됩니다. [az group create](/cli/azure/group/#az_group_create)를 사용하여 사용자 할당 관리 ID를 포함하고 배포하는 데 사용할 [리소스 그룹](~/articles/azure-resource-manager/management/overview.md#terminology)을 만듭니다. `<RESOURCE GROUP>` 및 `<LOCATION>` 매개 변수 값을 원하는 값으로 바꾸세요. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. [az identity create](/cli/azure/identity#az_identity_create)를 사용하여 사용자 할당 관리 ID를 만듭니다.  `-g` 매개 변수는 사용자 할당 관리 ID가 만들어진 리소스 그룹을 지정하고 `-n` 매개 변수는 그 이름을 지정합니다.    
    
   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   응답에는 다음과 같이 생성된 사용자가 할당한 관리 ID에 대한 세부 정보가 포함됩니다. 사용자 할당 관리 ID에 할당된 리소스 ID 값은 다음 단계에서 사용됩니다.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<myUserAssignedIdentity>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
       "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
       "location": "westcentralus",
       "name": "<USER ASSIGNED IDENTITY NAME>",
       "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
       "resourceGroup": "<RESOURCE GROUP>",
       "tags": {},
       "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
       "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. [az vm create](/cli/azure/vm/#az_vm_create)를 사용하여 VM을 만듭니다. 다음 예제에서는 `--assign-identity` 매개 변수에서 지정한 대로 새 사용자 할당 ID와 연결된 VM을 만듭니다. `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY NAME>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>기존 Azure VM에 사용자 할당 관리 ID 할당

VM에 사용자 할당 ID를 할당하려면 계정에 [가상 머신 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 및 [관리 ID 운영자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 역할 할당이 필요합니다. 추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

1. [az identity create](/cli/azure/identity#az_identity_create)를 사용하여 사용자 할당 ID를 만듭니다.  `-g` 매개 변수는 사용자 할당 ID가 만들어진 리소스 그룹을 지정하고 `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꾸세요.

    > [!IMPORTANT]
    > 이름에 특수 문자(예: 밑줄)가 있는 사용자 할당 관리 ID를 만드는 기능은 현재 지원되지 않습니다. 영숫자 문자를 사용하세요. 업데이트를 다시 확인하세요.  자세한 내용은 [FAQ 및 알려진 문제](known-issues.md)를 참조하세요.

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
   응답에는 다음과 같이 생성된 사용자가 할당한 관리 ID에 대한 세부 정보가 포함됩니다. 

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. [az vm identity assign](/cli/azure/vm)을 사용하여 VM에 사용자 할당 ID를 할당합니다. `<RESOURCE GROUP>` 및 `<VM NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<USER ASSIGNED IDENTITY NAME>`은 이전 단계에서 만든 대로 사용자 할당 관리 ID의 리소스 `name` 속성입니다. VM과 다른 RG에서 사용자 할당 관리 ID를 만든 경우 관리 ID의 URL을 사용해야 합니다.

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Azure VM에서 사용자 할당 관리 ID 제거

VM의 사용자 할당 ID를 제거하려면 계정에 [가상 머신 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할 할당이 필요합니다. 

가상 머신에 할당된 유일한 사용자 할당 관리 ID인 경우 `UserAssigned`는 ID 유형 값에서 제거됩니다.  `<RESOURCE GROUP>` 및 `<VM NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<USER ASSIGNED IDENTITY>`는 사용자 할당 ID의 `name` 속성이며 `az vm identity show`를 사용하여 가상 머신의 ID 섹션에서 찾을 수 있습니다.

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <USER ASSIGNED IDENTITY>
```

VM에 시스템 할당 관리 ID가 없고 모든 사용자 할당 ID를 제거하려는 경우 다음 명령을 사용합니다.

> [!NOTE]
> `none` 값은 대/소문자를 구분합니다. 소문자여야 합니다.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

VM에 시스템 할당 및 사용자 할당 ID가 모두 있는 경우 시스템 할당만 사용하도록 전환하여 모든 사용자 할당 ID를 제거할 수 있습니다. 다음 명령을 사용합니다.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>다음 단계
- [Azure 리소스에 대한 관리 ID 개요](overview.md)
- 전체 Azure VM 만들기 퀵 스타트는 다음 참조: 
  - [CLI를 사용하여 Windows 가상 머신 만들기](../../virtual-machines/windows/quick-create-cli.md)  
  - [CLI를 사용하여 Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-cli.md)
