---
title: '빠른 시작: 사용자 지정 컨테이너에서 Azure Arc 함수 앱 만들기'
description: 사용자 지정 Linux 컨테이너에서 첫 번째 함수 앱을 배포하여 Azure Arc에서 Azure Functions를 시작합니다.
ms.topic: quickstart
ms.date: 05/11/2021
ms.openlocfilehash: a22907387a647ca46a5784661324d3f6edcc3594
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706934"
---
# <a name="create-your-first-function-on-azure-arc-using-a-custom-container-preview"></a>사용자 지정 컨테이너를 사용하여 Azure Arc 첫 번째 함수 만들기(미리 보기)

이 빠른 시작에서는 사용자 지정 컨테이너에서 실행되는 Azure Functions 프로젝트를 만들어 Docker Hub 계정에서 [Azure Arc 지원 Kubernetes 클러스터](../azure-arc/kubernetes/overview.md)에 배포합니다. 자세한 내용은 [Azure Arc App Service, Functions 및 Logic Apps](../app-service/overview-arc-integration.md)를 참조하세요. 이 시나리오는 Linux에서 실행되는 함수 앱만 지원합니다.   

> [!NOTE]
> Azure Arc 지원 Kubernetes 클러스터에서 함수를 실행하기 위한 지원은 현재 미리 보기로 제공됩니다.  

## <a name="prerequisites"></a>필수 구성 요소

로컬 컴퓨터:

# <a name="c"></a>[C\#](#tab/csharp)

+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)
+ [Azure Functions Core Tools](functions-run-local.md#v2) 버전 3.0.3245 이상.
+ [Azure CLI](/cli/azure/install-azure-cli) 버전 2.4 이상.
+ [Docker](https://docs.docker.com/install/)  
+ [Docker ID](https://hub.docker.com/signup)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ [Node.js](https://nodejs.org/) 버전 12. Node.js 버전 10도 지원됩니다.
+ [Azure Functions Core Tools](functions-run-local.md#v2) 버전 3.0.3245 이상.
+ [Azure CLI](/cli/azure/install-azure-cli) 버전 2.4 이상.
+ [Docker](https://docs.docker.com/install/)  
+ [Docker ID](https://hub.docker.com/signup)

# <a name="python"></a>[Python](#tab/python)

+ [Azure Functions에서 지원하는 Python 버전](supported-languages.md#languages-by-runtime-version)
+ [Azure Functions Core Tools](functions-run-local.md#v2) 버전 3.0.3245 이상.
+ [Azure CLI](/cli/azure/install-azure-cli) 버전 2.4 이상.
+ [Docker](https://docs.docker.com/install/)  
+ [Docker ID](https://hub.docker.com/signup)

---

[!INCLUDE [functions-arc-create-environment](../../includes/functions-arc-create-environment.md)]

[!INCLUDE [app-service-arc-cli-install-extensions](../../includes/app-service-arc-cli-install-extensions.md)]

## <a name="create-the-local-function-project"></a>로컬 함수 프로젝트 만들기

Azure Functions에서 함수 프로젝트는 각각 특정 트리거에 응답하는 하나 이상의 개별 함수에 대한 컨텍스트입니다. 프로젝트의 모든 함수는 동일한 로컬 및 호스팅 구성을 공유합니다. 이 섹션에서는 단일 함수가 포함된 함수 프로젝트를 만듭니다.

1. 다음과 같이 `func init` 명령을 실행하여 지정된 런타임에 *LocalFunctionProj* 폴더에 함수 프로젝트를 만듭니다.  

    # <a name="c"></a>[C\#](#tab/csharp)

    ```console
    func init LocalFunctionProj --dotnet --docker
    ```
    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    ```console
    func init LocalFunctionProj --javascript --docker
    ```

    # <a name="python"></a>[Python](#tab/python)

    Python에는 가상 환경과 Bash 및 Windows 명령줄에 서로 다른 명령이 필요합니다.
    
     + Bash: 

        ```bash
        python -m venv .venv
        source .venv/bin/activate
        ```
    
     + 명령줄:

        ```cmd
        py -m venv .venv
        .venv\scripts\activate
        ```  

    이제 가상 환경 내에서 프로젝트를 만들겠습니다. 
    
    ```console
    func init LocalFunctionProj --python --docker
    ```
    ---

    `--docker` 옵션은 프로젝트에 대한 `Dockerfile`을 생성하는데, 이는 Azure Functions 및 선택한 런타임에서 사용하는 데 적합한 사용자 지정 컨테이너를 정의합니다.

1. 프로젝트 폴더로 이동합니다.

    ```console
    cd LocalFunctionProj
    ```

    이 폴더에는 [local.settings.json](functions-develop-local.md#local-settings-file) 및 [host.json](functions-host-json.md)이라는 구성 파일을 포함하여 프로젝트의 Dockerfile 기타 파일이 있습니다. 기본적으로 *local.settings.json* 파일은 *.gitignore* 파일의 소스 제어에서 제외됩니다. 이는 파일에 Azure에서 다운로드한 비밀이 포함될 수 있기 때문입니다.

1. 생성된 `Dockerfile`을 열고 기본 이미지의 `3.0` 태그를 찾습니다. `3.0` 태그가 있는 경우 `3.0.15885` 태그로 바꿉니다. 예를 들어, JavaScript 애플리케이션에서 Docker 파일은 `FROM mcr.microsoft.com/azure-functions/node:3.0.15885`를 포함하도록 수정해야 합니다. 이 버전의 기본 이미지는 Azure Arc 지원 Kubernetes 클러스터에 대한 배포를 지원합니다. 

1. 다음 명령을 사용하여 함수를 프로젝트에 추가합니다. 여기서 `--name` 인수는 함수(HttpExample)의 고유 이름이고, `--template` 인수는 함수의 트리거(HTTP)를 지정합니다.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```  

## <a name="build-the-container-image-and-test-locally"></a>컨테이너 이미지 빌드 및 로컬로 테스트

프로젝트 루트의 Dockerfile은 컨테이너에서 함수 앱을 실행하는 데 필요한 환경을 설명합니다. Azure Functions에 대해 지원되는 기본 이미지의 전체 목록은 [Azure Functions 기본 이미지 페이지](https://hub.docker.com/_/microsoft-azure-functions-base)에 나와 있습니다.

1. 루트 프로젝트 폴더에서 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 명령을 실행하고 이름(`azurefunctionsimage`) 및 태그(`v1.0.0`)를 입력합니다.   

    다음 명령은 컨테이너에 대한 Docker 이미지를 빌드합니다.

    ```console
    docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
    ```

    이 예제에서는 `<DOCKER_ID>`를 Docker Hub 계정 ID로 바꿉니다. 명령이 완료되면 새 컨테이너를 로컬에서 실행할 수 있습니다.
    
1. 빌드를 테스트하려면 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 로컬 컨테이너에서 이미지를 실행합니다. 여기서 `-p 8080:80` 포트 인수를 추가합니다.

    ```console
    docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
    ```

    `<DOCKER_ID`를 Docker ID로 바꾸고 포트 인수 `-p 8080:80`를 추가합니다.

1. 로컬 컨테이너에서 이미지가 실행되면 `http://localhost:8080/api/HttpExample?name=Functions`로 이동합니다. 이 경우 이전과 동일한 "hello" 메시지가 표시되어야 합니다. HTTP 트리거 함수는 익명 권한 부여를 사용하므로 컨테이너에서 실행되고 있어도 함수를 호출할 수 있습니다. 함수 액세스 키 설정은 컨테이너에서 로컬로 실행할 때 적용됩니다. 함수를 호출하는 데 문제가 있는 경우 [함수에 대한 액세스](functions-bindings-http-webhook-trigger.md#authorization-keys)가 익명으로 설정되어 있는지 확인합니다.  

컨테이너에서 함수 앱이 확인되면 **Ctrl**+**C** 를 사용하여 docker를 중지합니다.

## <a name="push-the-image-to-docker-hub"></a>Docker Hub에 이미지 푸시

Docker Hub는 이미지를 호스팅하고 이미지 및 컨테이너 서비스를 제공하는 컨테이너 레지스트리입니다. Azure에 배포하는 기능이 포함된 이미지를 공유하려면 해당 이미지를 레지스트리로 푸시해야 합니다.

1. 아직 Docker에 로그인하지 않은 경우 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 명령을 사용하여 이 작업을 수행합니다. 여기서 `<docker_id>`를 Docker ID로 바꿉니다. 이 명령은 사용자 이름과 암호를 입력하라는 메시지를 표시합니다. “로그인했습니다.”라는 메시지를 통해 로그인했음을 확인할 수 있습니다.

    ```console
    docker login
    ```
    
1. 로그인했으면 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 명령을 사용하여 이미지를 Docker Hub로 푸시합니다. 여기서 `<docker_id>`를 Docker ID로 다시 바꿉니다.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. 네트워크 속도에 따라 이미지를 처음 푸시하는 데 몇 분 정도 걸릴 수 있습니다(이후에 변경 내용을 푸시하는 속도는 훨씬 더 빠름). 기다리는 동안 다음 섹션으로 진행하여 다른 터미널에서 Azure 리소스를 만들 수 있습니다.

[!INCLUDE [functions-arc-get-custom-location](../../includes/functions-arc-get-custom-location.md)]

## <a name="create-azure-resources"></a>Azure 리소스 만들기 

새 App Service Kubernetes 환경에 컨테이너를 배포하려면 먼저 다음 두 개의 리소스를 추가로 만들어야 합니다.

- [스토리지 계정](../storage/common/storage-account-create.md) - 현재, 도구에 필요하며 환경의 일부가 아닙니다.
- 컨테이너를 실행하기 위한 컨텍스트를 제공하는 함수 앱. 함수 앱은 App Service Kubernetes 환경에서 실행되고 로컬 함수 프로젝트에 매핑됩니다. 함수 앱을 통해 함수를 논리 단위로 그룹화하여 더욱 쉽게 관리, 배포 및 리소스 공유할 수 있습니다.

> [!NOTE]
> 함수 앱은 전용(App Service) 플랜의 App Service Kubernetes 환경에서 실행됩니다. 기존 계획 없이 함수 앱을 만들면 사용자 계획이 만들어집니다.  

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
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime dotnet --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime node --runtime-version 12 --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
```

# <a name="python"></a>[Python](#tab/python)  
```azurecli
az functionapp create --resource-group MyResourceGroup --name <APP_NAME> --custom-location <CUSTOM_LOCATION_ID> --storage-account <STORAGE_NAME> --functions-version 3 --runtime python --runtime-version 3.8 --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
```
---

이 예제에서 `<CUSTOM_LOCATION_ID>`를 App Service Kubernetes 환경에 대해 결정한 사용자 지정 위치의 ID로 바꿉니다. 또한 `<STORAGE_NAME>`을 이전 단계에서 사용한 계정의 이름으로 바꾸고, `<APP_NAME>`을 사용자에게 맞는 전역적으로 고유한 이름으로 바꾼 다음 `<DOCKER_ID>`를 Docker Hub ID로 바꿉니다. 

*deployment-container-image-name* 매개 변수는 함수 앱에 사용할 이미지를 지정합니다. [az functionapp config container show](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) 명령을 사용하여 배포에 사용되는 이미지에 대한 정보를 볼 수 있습니다. 또한 [az functionapp config container set](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) 명령을 사용하여 다른 이미지에서 배포할 수도 있습니다.

함수 앱을 처음 만들 때 Docker Hub에서 초기 이미지를 가져옵니다. Docker Hub에서 [Azure에 연속 배포를 사용하도록 설정](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure)할 수도 있습니다.  

이미지에서 SSH를 사용하도록 설정하는 방법은 [SSH 연결 사용](functions-create-function-linux-custom-image.md#enable-ssh-connections)을 참조하세요.

### <a name="set-required-app-settings"></a>필요한 앱 설정 구성

다음 명령을 실행하여 스토리지 계정 연결 문자열에 대한 앱 설정을 만듭니다.

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <STORAGE_NAME> --query connectionString --output tsv)
az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
```

이 코드는 로컬 컴퓨터의 Bash 또는 Cloud Shell에서 실행해야 합니다. `<STORAGE_NAME>`을 스토리지 계정의 이름으로 바꾸고, `<APP_NAME>`을 함수 앱 이름으로 바꿉니다.  

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
