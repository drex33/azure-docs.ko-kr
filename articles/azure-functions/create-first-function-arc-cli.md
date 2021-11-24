---
title: '빠른 시작: Azure Arc에서 함수 앱 만들기'
description: 첫 번째 함수 앱을 배포하여 Azure Arc에서 Azure Functions를 시작합니다.
ms.topic: quickstart
ms.date: 05/10/2021
ms.custom: mode-other
ms.openlocfilehash: 704767ba601c16735bb4df97210836039e7fb9e1
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133049335"
---
# <a name="create-your-first-function-on-azure-arc-preview"></a>Azure Arc에서 첫 번째 함수 만들기(미리 보기)

이 빠른 시작에서는 Azure Functions 프로젝트를 만들어 [Azure Arc 지원 Kubernetes 클러스터](../azure-arc/kubernetes/overview.md)에서 실행되는 함수 앱에 배포합니다. 자세한 내용은 [Azure Arc App Service, Functions 및 Logic Apps](../app-service/overview-arc-integration.md)를 참조하세요. 이 시나리오는 Linux에서 실행되는 함수 앱만 지원합니다.   

> [!NOTE]
> Azure Arc 지원 Kubernetes 클러스터에서 함수를 실행하기 위한 지원은 현재 미리 보기로 제공됩니다.  
>  
> Azure Arc 지원 Kubernetes 클러스터에 PowerShell 함수 프로젝트를 게시하는 방식은 현재 지원되지 않습니다. Azure Arc 지원 Kubernetes 클러스터에 PowerShell 함수를 배포해야 하는 경우 [컨테이너에서 함수 앱을 만듭니다](create-first-function-arc-custom-container.md). 

## <a name="prerequisites"></a>필수 구성 요소

로컬 컴퓨터:

# <a name="c"></a>[C\#](#tab/csharp)

+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)
+ [Azure Functions Core Tools](functions-run-local.md#v2) 버전 3.0.3245 이상.
+ [Azure CLI](/cli/azure/install-azure-cli) 버전 2.4 이상.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ [Node.js](https://nodejs.org/) 버전 12. Node.js 버전 10도 지원됩니다.
+ [Azure Functions Core Tools](functions-run-local.md#v2) 버전 3.0.3245 이상.
+ [Azure CLI](/cli/azure/install-azure-cli) 버전 2.4 이상.

# <a name="python"></a>[Python](#tab/python)

+ [Azure Functions에서 지원하는 Python 버전](supported-languages.md#languages-by-runtime-version)
+ [Azure Functions Core Tools](functions-run-local.md#v2) 버전 3.0.3245 이상.
+ [Azure CLI](/cli/azure/install-azure-cli) 버전 2.4 이상.

---

[!INCLUDE [functions-arc-create-environment](../../includes/functions-arc-create-environment.md)]

[!INCLUDE [app-service-arc-cli-install-extensions](../../includes/app-service-arc-cli-install-extensions.md)]

## <a name="create-the-local-function-project"></a>로컬 함수 프로젝트 만들기

Azure Functions에서 함수 프로젝트는 각각 특정 트리거에 응답하는 하나 이상의 개별 함수에 대한 배포 및 실행 단위입니다. 프로젝트의 모든 함수는 동일한 로컬 및 호스팅 구성을 공유합니다. 이 섹션에서는 단일 함수가 포함된 함수 프로젝트를 만듭니다.

1. 다음과 같이 `func init` 명령을 실행하여 지정된 런타임에 *LocalFunctionProj* 폴더에 함수 프로젝트를 만듭니다.  

    # <a name="c"></a>[C\#](#tab/csharp)

    ```console
    func init LocalFunctionProj --dotnet
    ```
    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    ```console
    func init LocalFunctionProj --javascript
    ```

    # <a name="python"></a>[Python](#tab/python)

    Python에는 가상 환경과 bash 및 Windows 명령줄에서 서로 다른 명령이 필요합니다.
    
     + bash: 

        ```bash
        python -m venv .venv
        source .venv/bin/activate
        ```
    
     + 명령줄:

        ```cmd
        py -m venv .venv
        .venv\scripts\activate
        ```  

    이제 가상 환경 내에서 프로젝트를 만듭니다. 
    
    ```console
    func init LocalFunctionProj --python
    ```

    ---

1. 프로젝트 폴더로 이동합니다.

    ```console
    cd LocalFunctionProj
    ```

    이 폴더에는 [local.settings.json](functions-develop-local.md#local-settings-file) 및 [host.json](functions-host-json.md)이라는 구성 파일을 포함하여 프로젝트의 다양한 파일이 있습니다. 기본적으로 *local.settings.json* 파일은 *.gitignore* 파일의 소스 제어에서 제외됩니다. 이는 파일에 Azure에서 다운로드한 비밀이 포함될 수 있기 때문입니다.

1. 다음 명령을 사용하여 함수를 프로젝트에 추가합니다. 여기서 `--name` 인수는 함수(HttpExample)의 고유 이름이고, `--template` 인수는 함수의 트리거(HTTP)를 지정합니다.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```  
[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-arc-get-custom-location](../../includes/functions-arc-get-custom-location.md)]

## <a name="create-azure-resources"></a>Azure 리소스 만들기 

새 App Service Kubernetes 환경에 함수 코드를 배포하려면 먼저 다음 두 개의 리소스를 추가로 만들어야 합니다.

- [스토리지 계정](../storage/common/storage-account-create.md) - 현재, 도구에 필요하며 환경의 일부가 아닙니다.
- 함수 앱 - 함수 코드를 실행하기 위한 컨텍스트를 제공합니다. 함수 앱은 App Service Kubernetes 환경에서 실행되고 로컬 함수 프로젝트에 매핑됩니다. 함수 앱을 통해 함수를 논리 단위로 그룹화하여 더욱 쉽게 관리, 배포 및 리소스 공유할 수 있습니다.

> [!NOTE]
> 함수 앱은 전용(App Service) 플랜의 App Service Kubernetes 환경에서 실행됩니다. 기존 플랜 없이 함수 앱을 만들면 올바른 플랜이 만들어집니다.  

### <a name="create-storage-account"></a>Storage 계정 만들기

[az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 사용하여 리소스 그룹 및 지역에 범용 스토리지 계정을 만듭니다.

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

> [!NOTE]  
> 스토리지 계정은 현재 Azure Functions 도구에 필요합니다. 

이전 예제에서 `<STORAGE_NAME>`을 사용자에게 적절하고 Azure Storage에서 고유한 이름으로 바꿉니다. 이름은 3~24자의 숫자와 소문자만 포함해야 합니다. `Standard_LRS`는 범용 계정을 지정하며, [Functions로 지원](storage-considerations.md#storage-account-requirements)됩니다. `--location` 값은 표준 Azure 지역입니다. 

### <a name="create-the-function-app"></a>함수 앱 만들기

[az functionapp create](/cli/azure/functionapp#az_functionapp_create) 명령을 실행하여 환경에서 새 함수 앱을 만듭니다.

# <a name="c"></a>[C\#](#tab/csharp)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime dotnet 
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime node --runtime-version 12
```

# <a name="python"></a>[Python](#tab/python)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime python --runtime-version 3.8
```
---

이 예제에서 `<CUSTOM_LOCATION_ID>`를 App Service Kubernetes 환경에 대해 결정한 사용자 지정 위치의 ID로 바꿉니다. 또한 `<STORAGE_NAME>`을 이전 단계에서 사용한 계정의 이름으로 바꾸고, `<APP_NAME>`을 적절하고 전역적으로 고유한 이름으로 바꿉니다. 

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

Azure Arc 지원 Kubernetes 클러스터에서 전체 배포를 완료하는 데 다소 시간이 걸릴 수 있으므로 다음 명령을 다시 실행하여 게시된 함수를 확인할 수 있습니다.

```command
func azure functionapp list-functions
``` 

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

## <a name="next-steps"></a>다음 단계

이제 함수 앱이 Azure Arc 지원 App Service Kubernetes 환경의 컨테이너에서 실행되고 있으므로, Queue Storage 출력 바인딩을 추가하여 Azure Storage에 연결할 수 있습니다.

# <a name="c"></a>[C\#](#tab/csharp)  

> [!div class="nextstepaction"]
> [Azure Storage 큐에 연결](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)  

> [!div class="nextstepaction"]
> [Azure Storage 큐에 연결](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-javascript)

# <a name="python"></a>[Python](#tab/python)  

> [!div class="nextstepaction"]
> [Azure Storage 큐에 연결](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

---
