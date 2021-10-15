---
title: Bicep 모듈에 대한 프라이빗 레지스트리 만들기
description: 프라이빗 Bicep 모듈에 대한 Azure 컨테이너 레지스트리를 설정하는 방법 알아보기
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: f6ffec3027a4b9776b85840fdb17fcb767eef137
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067894"
---
# <a name="create-private-registry-for-bicep-modules-preview"></a>Bicep 모듈용 프라이빗 레지스트리 만들기(미리 보기)

조직 내에서 [모듈을](modules.md) 공유하려면 프라이빗 모듈 레지스트리를 만들 수 있습니다. 해당 레지스트리에 모듈을 게시하고 모듈을 배포해야 하는 사용자에게 읽기 권한을 부여합니다. 레지스트리에서 모듈을 공유한 후에는 Bicep 파일에서 모듈을 참조할 수 있습니다.

모듈 레지스트리를 사용하려면 Bicep CLI 버전 **0.4.1008 이상이어야** 합니다.

## <a name="configure-private-registry"></a>프라이빗 레지스트리 구성

Bicep 레지스트리는 [ACR(Azure Container Registry)에서](../../container-registry/container-registry-intro.md)호스트됩니다. 다음 단계를 사용하여 모듈에 대한 레지스트리를 구성합니다.

1. 컨테이너 레지스트리가 이미 있는 경우 사용할 수 있습니다. 컨테이너 레지스트리를 만들어야 하는 경우 [빠른 시작: Bicep 파일을 사용하여 컨테이너 레지스트리 만들기를](../../container-registry/container-registry-get-started-bicep.md)참조하세요. 

   모듈 레지스트리에 사용 가능한 레지스트리 S SKU를 사용할 수 있습니다. 레지스트리 [지역 복제는](../../container-registry/container-registry-geo-replication.md) 사용자에게 로컬 상태 또는 핫 백업을 제공합니다.

1. 로그인 서버 이름을 가져옵니다. Bicep 파일에서 레지스트리에 연결할 때 이 이름이 필요합니다. 

   로그인 서버 이름을 얻으려면 [Get-AzContainerRegistry 를](/powershell/module/az.containerregistry/get-azcontainerregistry)사용합니다.

   ```azurepowershell
   Get-AzContainerRegistry -ResourceGroupName "<resource-group-name>" -Name "<registry-name>"
   ```

   또는 [az acr show를](/cli/azure/acr#az_acr_show)사용합니다.

   ```azurecli
   az acr show --resource-group <resource-group-name> --name <registry-name>
   ```

   로그인 서버 이름의 형식은 `<registry-name>.azurecr.io` 입니다.

- 레지스트리에 모듈을 게시하려면 이미지를 **푸시할** 수 있는 권한이 있어야 합니다. 레지스트리에서 모듈을 배포하려면 이미지를 **끌어올** 수 있는 권한이 있어야 합니다. 적절한 액세스 권한을 부여하는 역할에 대한 자세한 내용은 [Azure Container Registry 역할 및 사용 권한을 참조하세요.](../../container-registry/container-registry-roles.md)

- 모듈을 배포하는 데 사용하는 계정 유형에 따라 사용되는 자격 증명을 사용자 지정해야 할 수 있습니다. 이러한 자격 증명은 레지스트리에서 모듈을 얻는 데 필요합니다. 기본적으로 자격 증명은 Azure CLI 또는 Azure PowerShell 얻습니다. **bicepconfig.json** 파일에서 자격 증명을 얻기 위한 우선 순위를 사용자 지정할 수 있습니다. 자세한 내용은 [모듈 복원을 위한 자격 증명을 참조하세요.](bicep-config.md#credentials-for-restoring-modules)

- 보안을 강화하려면 프라이빗 엔드포인트를 통해 액세스해야 할 수 있습니다. 자세한 내용은 [Azure Private Link 사용하여 Azure Container Registry에 비공개로 커넥트](../../container-registry/container-registry-private-link.md)참조하세요.

## <a name="publish-files-to-registry"></a>레지스트리에 파일 게시

컨테이너 레지스트리를 설정하면 파일을 게시할 수 있습니다. [publish](bicep-cli.md#publish) 명령을 사용하고 모듈로 사용하려는 Bicep 파일을 제공합니다. 레지스트리에서 모듈의 대상 위치를 지정합니다.

```azurecli
az bicep publish storage.bicep --target br/exampleregistry.azurecr.io/bicep/modules/storage:v1
```

## <a name="view-files-in-registry"></a>레지스트리에서 파일 보기

포털에서 게시된 모듈을 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 컨테이너 **레지스트리를 검색합니다.**
1. 레지스트리를 선택합니다.
1. 왼쪽 메뉴에서 **리포지토리를** 선택합니다.
1. 모듈 경로(리포지토리)를 선택합니다.  앞의 예제에서 모듈 경로 이름은 **bicep/modules/storage** 입니다.
1. 태그를 선택합니다. 앞의 예제에서 태그는 **v1** 입니다.
1. **아티팩트 참조는** Bicep 파일에서 사용할 참조와 일치합니다.

   ![Bicep 모듈 레지스트리 아티팩트 참조](./media/private-module-registry/bicep-module-registry-artifact-reference.png)

이제 Bicep 파일에서 레지스트리의 파일을 참조할 준비가 되었습니다. 외부 모듈을 참조하는 데 사용할 구문의 예는 [Bicep 모듈을 참조하세요.](modules.md)

## <a name="next-steps"></a>다음 단계

* 모듈에 대한 자세한 내용은 [Bicep 모듈을 참조하세요.](modules.md)
* 모듈 레지스트리에 대한 별칭을 구성하려면 [Bicep 구성 파일 에서 사용자 지정 설정 추가를](private-module-registry.md)참조하세요.
* 모듈 게시 및 복원에 대한 자세한 내용은 [Bicep CLI 명령 를 참조하세요.](bicep-cli.md)
