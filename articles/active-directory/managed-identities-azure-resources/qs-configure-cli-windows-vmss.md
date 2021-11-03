---
title: 가상 머신 확장 집합에서 관리 ID 구성 - Azure CLI - Azure AD
description: Azure CLI를 사용하여 Azure 가상 머신 확장 집합에서 시스템 및 사용자 할당 관리 ID를 구성하기 위한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2835fcc2d39a47923c7d808f4d54d011cbf80f10
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131063255"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Azure CLI를 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대한 관리 ID 구성

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 리소스용 관리 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 Azure CLI를 사용하여 Azure 가상 머신 확장 집합에서 Azure 리소스 작업에 대해 다음과 같은 관리 ID를 수행하는 방법을 알아봅니다.
- Azure 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하거나 사용하지 않도록 설정
- Azure 가상 머신 확장 집합에서 사용자 할당 관리 ID 추가 및 제거

아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 리소스에 대한 관리 ID에 익숙하지 않은 경우 [Azure 리소스에 대한 관리 ID란?](overview.md)을 참조하세요. 시스템 할당 및 사용자 할당 관리 ID 형식에 대한 자세한 내용은 [관리 ID 형식](overview.md#managed-identity-types)을 참조하세요.

- 이 문서의 관리 작업을 수행하려면 계정에 다음과 같은 Azure 역할 기반 액세스 제어가 할당되어야 합니다.

  - [가상 머신 참가자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): 가상 머신 확장 집합을 만들고, 가상 머신 확장 집합에서 시스템 및/또는 사용자가 할당한 관리 ID를 사용하도록 설정하고 제거합니다.

  - [관리 ID 참가자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할: 사용자 할당 관리 ID를 만듭니다.

  - [관리 ID 운영자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 역할: 가상 머신 확장 집합에 사용자 할당 관리 ID를 할당하거나 이 집합에서 사용자 할당 관리 ID를 제거합니다.

  > [!NOTE]
  > 추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

이 섹션에서는 Azure CLI를 사용하여 Azure 가상 머신 확장 집합에 시스템 할당 관리 ID를 사용 및 사용하지 않도록 설정하는 방법을 알아봅니다.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합을 만드는 동안 시스템 할당 관리 ID를 사용하도록 설정

시스템 할당 관리 ID를 사용하도록 설정된 가상 머신 확장 집합을 만들려면:

1. [az group create](/cli/azure/group/#az_group_create)를 사용하여 가상 머신 확장 집합 및 관련 리소스를 포함하고 배포하기 위한 [리소스 그룹](../../azure-resource-manager/management/overview.md#terminology)을 만듭니다. 대신 사용하려는 리소스 그룹이 이미 있다면 이 단계를 건너뛰어도 됩니다.

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

1. 가상 머신 확장 집합을 [만듭니다](/cli/azure/vmss/#az_vmss_create). 다음 예제에서는 `--assign-identity` 매개 변수에서 요청한 대로 시스템 할당 관리 ID를 사용하여 *myVMSS* 라는 가상 머신 확장 집합을 만듭니다. `--admin-username` 및 `--admin-password` 매개 변수는 가상 머신 로그인을 위한 관리자 이름 및 암호 계정을 지정합니다. 이러한 값은 사용자 환경에 적절하게 업데이트합니다. 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>기존 Azure 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하도록 설정

기존 Azure 가상 머신 확장 집합에서 시스템 할당 관리 ID를 [사용하도록 설정](/cli/azure/vmss/identity/#az_vmss_identity_assign)해야 하는 경우:

```azurecli-interactive
az vmss identity assign -g myResourceGroup -n myVMSS
```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하지 않도록 설정

시스템 할당 관리 ID는 더 이상 필요하지 않고 사용자 할당 관리 ID는 여전히 필요한 가상 머신 확장 집합이 있는 경우 다음 명령을 사용합니다.

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

시스템 할당 관리 ID는 더 이상 필요하지 않고 사용자 할당 관리 ID가 없는 가상 머신이 있는 경우 다음 명령을 사용합니다.

> [!NOTE]
> `none` 값은 대/소문자를 구분합니다. 소문자여야 합니다. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

## <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

이 섹션에서는 Azure CLI를 사용하여 사용자 할당 관리 ID를 사용하도록 설정하고 제거하는 방법을 알아봅니다.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>가상 머신 확장 집합을 만드는 동안 사용자 할당 관리 ID를 할당합니다.

이 섹션에서는 가상 머신 확장 집합을 만들고 사용자 할당 관리 ID를 가상 머신 확장 집합에 할당하는 과정을 안내합니다. 사용하려는 가상 머신 확장 집합이 이미 있는 경우 이 섹션을 건너뛰고 다음 단계를 진행합니다.

1. 사용하려는 리소스 그룹이 이미 있다면 이 단계를 건너뛰어도 됩니다. [az group create](/cli/azure/group/#az_group_create)를 사용하여 사용자 할당 관리 ID를 포함하고 배포하는 데 사용할 [리소스 그룹](~/articles/azure-resource-manager/management/overview.md#terminology)을 만듭니다. `<RESOURCE GROUP>` 및 `<LOCATION>` 매개 변수 값을 원하는 값으로 바꾸세요. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. [az identity create](/cli/azure/identity#az_identity_create)를 사용하여 사용자 할당 관리 ID를 만듭니다.  `-g` 매개 변수는 사용자 할당 관리 ID가 만들어진 리소스 그룹을 지정하고 `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꾸세요.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   응답에는 다음과 같이 생성된 사용자가 할당한 관리 ID에 대한 세부 정보가 포함됩니다. 사용자 할당 관리 ID에 할당된 리소스 `id` 값은 다음 단계에서 사용됩니다.

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

3. 가상 머신 확장 집합을 [만듭니다](/cli/azure/vmss/#az_vmss_create). 다음 예제에서는 `--assign-identity` 매개 변수에서 지정한 대로 새 사용자 할당 관리 ID와 연결된 가상 머신 확장 집합을 만듭니다. `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` 및 `<USER ASSIGNED IDENTITY>` 매개 변수 값을 원하는 값으로 바꾸세요. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>기존 가상 머신 확장 집합에 사용자 할당 관리 ID 할당

1. [az identity create](/cli/azure/identity#az_identity_create)를 사용하여 사용자 할당 관리 ID를 만듭니다.  `-g` 매개 변수는 사용자 할당 관리 ID가 만들어진 리소스 그룹을 지정하고 `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꾸세요.

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```

   응답에는 다음과 같이 생성된 사용자가 할당한 관리 ID에 대한 세부 정보가 포함됩니다.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. 가상 머신 확장 집합에 사용자 할당 관리 ID를 [할당](/cli/azure/vmss/identity)합니다. `<RESOURCE GROUP>` 및 `<VIRTUAL MACHINE SCALE SET NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<USER ASSIGNED IDENTITY>`는 이전 단계에서 만든 대로 사용자 할당 ID의 리소스 `name` 속성입니다.

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Azure 가상 머신 확장 집합에서 사용자가 할당한 관리 ID 제거

가상 머신 확장 집합에서 사용자 할당 관리 ID를 [제거](/cli/azure/vmss/identity#az_vmss_identity_remove)하려면 `az vmss identity remove`를 사용합니다. 가상 머신 확장 집합에 할당된 유일한 사용자 할당 관리 ID인 경우 `UserAssigned`는 ID 유형 값에서 제거됩니다.  `<RESOURCE GROUP>` 및 `<VIRTUAL MACHINE SCALE SET NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<USER ASSIGNED IDENTITY>`는 사용자 할당 관리 ID의 `name` 속성이며 `az vmss identity show`를 사용하여 가상 머신 확장 집합의 ID 섹션에서 찾을 수 있습니다.

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VIRTUAL MACHINE SCALE SET NAME> --identities <USER ASSIGNED IDENTITY>
```

가상 머신 확장 집합에 시스템 할당 관리 ID가 없고 모든 사용자 할당 관리 ID를 제거하려는 경우 다음 명령을 사용합니다.

> [!NOTE]
> `none` 값은 대/소문자를 구분합니다. 소문자여야 합니다.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

가상 머신 확장 집합에 시스템 할당 및 사용자 할당 관리 ID가 모두 있는 경우 시스템 할당 관리 ID만 사용하도록 전환하여 모든 사용자 할당 관리 ID를 제거할 수 있습니다. 다음 명령을 사용합니다.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID 개요](overview.md)
- Azure 가상 머신 확장 집합 만들기에 대한 빠른 시작은 [CLI를 사용하여 가상 머신 확장 집합 만들기](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)를 참조하세요.
