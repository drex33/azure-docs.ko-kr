---
title: Azure Functions 런타임 버전을 대상으로 지정하는 방법
description: Azure Functions는 여러 버전의 런타임을 지원합니다. Azure에서 호스팅된 함수 앱의 런타임 버전을 지정하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 07/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6f608822a8c9b348c16021e64a9fe6a63170d6d6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646970"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions 런타임 버전을 대상으로 지정하는 방법

함수 앱은 특정 Azure Functions 런타임 버전에서 실행됩니다. [3.x, 2.x 및 1.x](functions-versions.md), 세 가지 주 버전이 있습니다. 기본적으로 함수 앱은 3.x 버전 런타임으로 작성됩니다. 이 문서에는 선택한 버전에서 실행할 함수 앱을 Azure에서 구성하는 방법을 설명합니다. 특정 버전에 대한 로컬 개발 환경을 구성하는 방법에 대한 자세한 내용은 [로컬로 Azure Functions 코딩 및 테스트](functions-run-local.md)를 참조하세요.

특정 버전을 수동으로 대상으로 지정하는 방법은 Windows 또는 Linux를 실행하고 있는지 여부에 따라 다릅니다.

## <a name="automatic-and-manual-version-updates"></a>자동 및 수동 버전 업데이트

[Linux](#manual-version-updates-on-linux)에서 함수 앱을 실행하는 경우 이 섹션은 적용되지 않습니다.

Azure Functions를 통해 함수 앱의 `FUNCTIONS_EXTENSION_VERSION` 애플리케이션 설정을 사용하여 Windows의 특정 버전 런타임을 대상으로 지정할 수 있습니다. 함수 앱을 새 버전으로 변경하도록 명시적으로 선택할 때까지 지정된 주 버전에서 유지합니다. 주 버전만 지정하면 새로운 런타임 부 버전을 사용할 수 있을 때 함수 앱이 해당 런타임으로 자동 업데이트됩니다. 새로운 부 버전에는 주요 변경 내용이 도입되지 않습니다. 

부 버전(예: "2.0.12345")을 지정하면 버전을 명시적으로 변경할 때까지 함수 앱은 특정 버전으로 고정됩니다. 이전 부 버전은 프로덕션 환경에서 정기적으로 제거됩니다. 부 버전이 제거되면 함수 앱은 `FUNCTIONS_EXTENSION_VERSION`에서 설정된 버전 대신 최신 버전으로 돌아갑니다. 따라서 특정 부 버전이 필요한 함수 앱의 문제를 신속하게 해결해야 합니다. 그런 다음 주 버전을 대상으로 지정할 수 있습니다. 부 버전 제거는 [App Service 공지](https://github.com/Azure/app-service-announcements/issues)에서 공지됩니다.

> [!NOTE]
> Azure Functions의 특정 주 버전을 고정한 후 Visual Studio를 사용하여 Azure에 게시하려고 하면 최신 버전으로 업데이트하거나 게시를 취소하라는 대화 상자가 표시됩니다. 이를 방지하려면 `.csproj` 파일에 `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` 속성을 추가합니다.

새 버전을 공개적으로 사용할 수 있으면 포털에서 메시지를 표시하여 해당 버전으로 업그레이드할 수 있는 기회를 제공합니다. 새 버전으로 변경한 후에 `FUNCTIONS_EXTENSION_VERSION` 애플리케이션 설정을 사용하여 이전 버전으로 다시 변경할 수 있습니다.

다음 표에서는 자동 업데이트를 사용 설정하는 각 주 버전에 대한 `FUNCTIONS_EXTENSION_VERSION` 값이 표시됩니다.

| 주 버전 | `FUNCTIONS_EXTENSION_VERSION` 값 | 추가 구성  |
| ------------- | ----------------------------------- | ------------------------- |
| 4.x           | `~4`                                | [Windows에서 .net 6을 사용 하도록 설정 합니다.](./functions-versions.md#migrating-from-3x-to-4x) |
| 3.x           | `~3`                                |                           |
| 2.x           | `~2`                                |                           |
| 1.x           | `~1`                                |                           |

런타임 버전을 변경하면 함수 앱을 다시 시작하게 됩니다.

>[!NOTE]
>.NET Function 앱은 .NET Core 3.1에 대한 자동 업그레이드를 옵트아웃하기 위해 `~2.0`에 고정됩니다. 자세히 알아보려면 [Functions v2.x 고려 사항](functions-dotnet-class-library.md#functions-v2x-considerations)을 참조하세요.  

## <a name="view-and-update-the-current-runtime-version"></a>현재 런타임 버전 확인 및 업데이트

[Linux](#manual-version-updates-on-linux)에서 함수 앱을 실행하는 경우 이 섹션은 적용되지 않습니다.

함수 앱에 사용되는 런타임 버전을 변경할 수 있습니다. 주요 변경 내용이 발생할 가능성이 있으므로 함수 앱에서 함수를 만들기 전에는 런타임 버전만 변경할 수 있습니다. 

> [!IMPORTANT]
> 런타임 버전은 `FUNCTIONS_EXTENSION_VERSION` 설정에서 결정되지만, 설정을 직접 변경하지 않고 Azure Portal에서 이 변경을 수행해야 합니다. 그 이유는 포털에서 변경 내용이 확인되고 필요에 따라 관련된 다른 변경 내용이 적용되기 때문입니다.

# <a name="portal"></a>[포털](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Azure Portal을 사용하여 이미 함수가 포함된 함수 앱에 대한 런타임 버전을 변경할 수 없습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Azure CLI에서 `FUNCTIONS_EXTENSION_VERSION`을 확인하고 설정할 수도 있습니다.  

Azure CLI를 사용하여 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 명령을 실행해 현재 런타임 버전을 확인합니다.

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

이 코드에서 `<function_app>`을 함수 앱 이름으로 바꿉니다. 또한 `<my_resource_group>`을 함수 앱의 리소스 그룹 이름으로 바꿉니다. 

다음 출력의 `FUNCTIONS_EXTENSION_VERSION`은 보기 편하도록 잘린 상태입니다.

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

함수 앱에서 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 명령으로 함수 앱의 `FUNCTIONS_EXTENSION_VERSION` 설정을 업데이트할 수 있습니다.

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

`<FUNCTION_APP>`은 함수 앱 이름으로 바꿉니다. 또한 `<RESOURCE_GROUP>`을 함수 앱의 리소스 그룹 이름으로 바꿉니다. 또한 `<VERSION>`을 특정 버전, 즉, `~3`, `~2` 또는 `~1`로 바꿉니다.

이전 코드 예제에서 **시도** 를 선택하여 [Azure Cloud Shell](../cloud-shell/overview.md)의 명령을 실행합니다. 또한 [로컬로 Azure CLI](/cli/azure/install-azure-cli)를 실행하여 이 명령을 실행할 수 있습니다. 로컬로 실행하는 경우 먼저 [az login](/cli/azure/reference-index#az_login)을 실행하여 로그인해야 합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Azure Functions 런타임을 확인하려면 다음 cmdlet을 사용합니다. 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

`<FUNCTION_APP>`을 함수 이름 및 `<RESOURCE_GROUP>`으로 바꿉니다. `FUNCTIONS_EXTENSION_VERSION` 설정의 현재 값이 해시 테이블에 반환됩니다.

Functions 런타임을 변경하려면 다음 스크립트를 사용합니다.

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

이전과 같이 `<FUNCTION_APP>`을 함수 앱의 이름으로 바꾸고 `<RESOURCE_GROUP>`을 리소스 그룹의 이름으로 바꿉니다. 또한 `<VERSION>`을 특정 버전 또는 주 버전으로 바꿉니다(예: `~2` 또는 `~3`). 반환된 해시 테이블 `FUNCTIONS_EXTENSION_VERSION` 설정에서 업데이트된 값을 확인할 수 있습니다. 

---

애플리케이션 설정이 변경된 후 함수 앱이 다시 시작됩니다.

## <a name="manual-version-updates-on-linux"></a>Linux의 수동 버전 업데이트

특정 호스트 버전에 Linux 함수 앱을 고정하려면 사이트 구성의 'LinuxFxVersion' 필드에 이미지 URL을 지정합니다. 예를 들어 노드 10 함수 앱을 고정하여 호스트 버전 3.0.13142를 전달하려면

**Linux App Service/탄력적 프리미엄 앱** - `LinuxFxVersion`을 `DOCKER|mcr.microsoft.com/azure-functions/node:3.0.13142-node10-appservice`로 설정합니다.

**Linux 사용 앱** - `LinuxFxVersion`을 `DOCKER|mcr.microsoft.com/azure-functions/mesh:3.0.13142-node10`으로 설정합니다.

# <a name="portal"></a>[포털](#tab/portal)

함수 앱에 대한 사이트 구성 설정 보기 및 수정은 Azure Portal에서 지원되지 않습니다. 대신 Azure CLI를 사용하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Azure CLI를 사용하여 `LinuxFxVersion`을 보고 설정할 수 있습니다.  

현재 런타임 버전을 보려면 [az functionapp config show](/cli/azure/functionapp/config) 명령과 사용합니다.

```azurecli-interactive
az functionapp config show --name <function_app> \
--resource-group <my_resource_group> --query 'linuxFxVersion' -o tsv
```

이 코드에서 `<function_app>`을 함수 앱 이름으로 바꿉니다. 또한 `<my_resource_group>`을 함수 앱의 리소스 그룹 이름으로 바꿉니다. `linuxFxVersion`의 현재 값이 반환됩니다.

함수 앱에서 `linuxFxVersion` 설정을 업데이트하려면 [az functionapp config set](/cli/azure/functionapp/config) 명령을 사용합니다.

```azurecli-interactive
az functionapp config set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--linux-fx-version <LINUX_FX_VERSION>
```

`<FUNCTION_APP>`은 함수 앱 이름으로 바꿉니다. 또한 `<RESOURCE_GROUP>`을 함수 앱의 리소스 그룹 이름으로 바꿉니다. 또한 `<LINUX_FX_VERSION>`을 위에서 설명한 것과 같이 특정 이미지의 값으로 바꿉니다.

앞의 코드 샘플에서 **사용해 보세요.** 를 선택하여 [Azure Cloud Shell](../cloud-shell/overview.md)에서 이 명령을 실행할 수 있습니다. 또한 [Azure CLI locally(로컬로 Azure CLI 설치)](/cli/azure/install-azure-cli)를 사용하면 [az login](/cli/azure/reference-index#az_login)을 실행하여 로그인한 후 이 명령을 실행할 수도 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

현재는 Azure PowerShell을 사용하여 `linuxFxVersion`을 설정할 수 없습니다. 대신 Azure CLI를 사용하세요.

---

사이트 구성이 변경된 후 함수 앱이 다시 시작됩니다.

> [!NOTE]
> 사용 계획에서 실행 중인 앱의 경우 `LinuxFxVersion`을 특정 이미지로 설정하면 콜드 부팅 시간이 증가할 수 있습니다. 특정 이미지에 고정하면 Functions에서 콜드 부팅 최적화를 사용할 수 없기 때문입니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [로컬 개발 환경에서 2.0 런타임을 대상으로 지정](functions-run-local.md)

> [!div class="nextstepaction"]
> [런타임 버전에 대한 릴리스 정보 참조](https://github.com/Azure/azure-webjobs-sdk-script/releases)
