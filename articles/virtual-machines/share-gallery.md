---
title: RBAC를 사용 하 여 갤러리 공유
description: RBAC (역할 기반 액세스 제어)를 사용 하 여 갤러리를 공유 하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: 01c98ba8d619573c241e5a8fdfa2f774b4aed22d
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452689"
---
# <a name="use-rbac-to-share-image-gallery-resources"></a>RBAC를 사용 하 여 이미지 갤러리 리소스 공유

공유 이미지 갤러리, 이미지 정의 및 이미지 버전은 모든 리소스 이므로 기본 제공 되는 네이티브 Azure RBAC 컨트롤을 사용 하 여 공유할 수 있습니다. Azure RBAC를 사용하면 다른 사용자, 서비스 주체, 그룹에 리소스를 공유할 수 있습니다. 리소스가 생성된 테넌트 외부의 개별 리소스에 대한 액세스를 공유할 수도 있습니다. 사용자에게 공유 이미지 버전에 대한 액세스 권한이 부여되면 사용자는 VM 또는 Virtual Machine Scale Set를 배포할 수 있습니다.  

최상의 환경을 위해 갤러리 수준에서 공유하는 것이 좋습니다. 개별 이미지 버전을 공유하는 것은 좋지 않습니다. Azure RBAC에 대한 자세한 내용은 [Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

사용자가 조직 외부에 있는 경우 조직에 가입하기 위한 이메일 초대를 받게 됩니다. 사용자는 초대를 수락해야 하며, 그러면 갤러리와 해당 리소스 목록에 있는 모든 이미지 정의 및 버전을 볼 수 있습니다.

## <a name="share-a-gallery"></a>갤러리 공유

### <a name="portal"></a>[포털](#tab/portal)

사용자가 조직 외부에 있는 경우 조직에 가입하기 위한 이메일 초대를 받게 됩니다. 사용자는 초대를 수락해야 하며, 그러면 갤러리와 해당 리소스 목록에 있는 모든 이미지 정의 및 버전을 볼 수 있습니다.

1. 이미지 갤러리에 대 한 페이지의 왼쪽 메뉴에서 **액세스 제어 (IAM)** 를 선택 합니다. 
1. **역할 할당 추가** 아래에서 **추가** 를 선택합니다. **역할 할당 추가** 창이 열립니다. 
1. **역할** 아래에서 **읽기 권한자** 를 선택합니다.
1. **다음에 대한 액세스 할당** 은 기본값 **Azure AD 사용자, 그룹 또는 서비스 주체** 그대로 둡니다.
1. **선택** 아래에서 초대하려는 사용자의 이메일 주소를 입력합니다.
1. 사용자가 조직 외부에 있는 경우 **이 사용자에게 Microsoft에서 공동 작업할 수 있게 해주는 이메일이 전송됩니다.** 라는 메시지가 표시됩니다. 사용자와 이메일 주소를 선택하고 **저장** 을 클릭합니다.


### <a name="cli"></a>[CLI](#tab/cli)

갤러리의 개체 ID를 가져오려면 [az sig show](/cli/azure/sig#az_sig_show)를 사용합니다.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

이메일 주소 및 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)와 함께 개체 ID를 범위로 사용하여 사용자에게 공유 이미지 갤러리에 대한 액세스 권한을 부여합니다. `<email-address>` 및 `<gallery iD>`를 사용자 고유의 정보로 바꿉니다.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

RBAC를 사용하여 리소스를 공유하는 방법에 대한 자세한 내용은 [RBAC 및 Azure CLI를 사용하여 액세스 관리](../role-based-access-control/role-assignments-cli.md)를 참조하세요.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

이메일 주소 및 [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet을 사용하여 사용자의 개체 ID를 가져온 다음, [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment)를 사용하여 갤러리에 대한 액세스 권한을 제공합니다. 이 예제에서 alinne_montes@contoso.com 예제 이메일을 사용자 고유의 정보로 바꿉니다.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName

```

---


## <a name="next-steps"></a>다음 단계

[이미지 정의와 이미지 버전](image-version.md)을 만듭니다.

[Azure Image Builder(미리 보기)](./image-builder-overview.md)는 이미지 버전 생성을 자동화하는 데 도움이 되며, [기존 이미지 버전에서 새 이미지를 생성](./windows/image-builder-gallery-update-image-version.md)하고 업데이트하는 데도 사용할 수 있습니다. 

또한 템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수도 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [공유 이미지 갤러리 만들기](https://azure.microsoft.com/resources/templates/sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/sig-image-version-create/)
