---
title: 사용자 지정 이미지를 사용하여 Linux에서 Azure Functions 만들기
description: 사용자 지정 Linux 이미지에서 실행되는 Azure Functions를 만드는 방법을 알아봅니다.
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: a6e3ad07f9ba46bd15fe662f370ae2ffc3deb4a8
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830541"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>사용자 지정 컨테이너를 사용하여 Linux에서 함수 만들기

이 자습서에서는 Linux 기본 이미지를 사용하여 사용자 지정 Docker 컨테이너로 코드를 만들어 Azure Functions에 배포합니다. 함수에 특정 언어 버전이 필요하거나 기본 제공 이미지에서 제공하지 않는 특정 종속성 또는 구성이 있는 경우 일반적으로 사용자 지정 이미지를 사용합니다.

::: zone pivot="programming-language-other"
Azure Functions는 [사용자 지정 처리기](functions-custom-handlers.md)를 사용하여 모든 언어 또는 런타임을 지원합니다. 이 자습서에서 사용되는 R 프로그래밍 언어와 같은 일부 언어의 경우 사용자 지정 컨테이너를 사용해야 하는 종속성으로 런타임 또는 추가 라이브러리를 설치해야 합니다.
::: zone-end

사용자 지정 Linux 컨테이너에 함수 코드를 배포하려면 [프리미엄 플랜](functions-premium-plan.md) 또는 [전용(App Service) 플랜](dedicated-plan.md) 호스팅이 필요합니다. 이 자습서를 완료하면 Azure 계정에 몇 달러(미국)의 비용이 발생하며, 이 비용은 완료될 때 [리소스 정리](#clean-up-resources)를 통해 최소화할 수 있습니다.

또한 [Linux에서 호스팅되는 첫 번째 함수 만들기](./create-first-function-cli-csharp.md?pivots=programming-language-python)에서 설명한 대로 기본 Azure App Service 컨테이너를 사용할 수도 있습니다. Azure Functions에 대해 지원되는 기본 이미지는 [Azure Functions 기본 이미지 리포지토리](https://hub.docker.com/_/microsoft-azure-functions-base)에 나와 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Azure Functions Core Tools를 사용하여 함수 앱 및 Dockerfile 만들기
> * Docker를 사용하여 사용자 지정 이미지 빌드
> * 컨테이너 레지스트리에 사용자 지정 이미지 게시
> * Azure에서 함수 앱을 지원하는 리소스 만들기
> * Docker 허브에서 함수 앱 배포
> * 함수 앱에 애플리케이션 설정 추가
> * 지속적인 배포 사용
> * 컨테이너에 SSH 연결 사용
> * Queue Storage 출력 바인딩 추가 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Azure Functions Core Tools를 사용하여 함수 앱 및 Dockerfile 만들기
> * Docker를 사용하여 사용자 지정 이미지 빌드
> * 컨테이너 레지스트리에 사용자 지정 이미지 게시
> * Azure에서 함수 앱을 지원하는 리소스 만들기
> * Docker 허브에서 함수 앱 배포
> * 함수 앱에 애플리케이션 설정 추가
> * 지속적인 배포 사용
> * 컨테이너에 SSH 연결 사용
::: zone-end

이 자습서는 Windows, macOS 또는 Linux를 실행하는 모든 컴퓨터에서 수행할 수 있습니다. 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ [Docker ID](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>로컬 함수 프로젝트 만들기 및 테스트

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
터미널 또는 명령 프롬프트에서 선택한 언어에 대해 다음 명령을 실행하여 함수 앱 프로젝트를 현재 폴더에 만듭니다.  
::: zone-end  
::: zone pivot="programming-language-csharp"  

# <a name="in-process"></a>[In-Process](#tab/in-process)
```console
func init --worker-runtime dotnet --docker
```

# <a name="isolated-process"></a>[격리된 프로세스](#tab/isolated-process)
```console
func init --worker-runtime dotnet-isolated --docker
```
---
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
빈 폴더에서 다음 명령을 실행하여 [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)으로부터 Functions 프로젝트를 생성합니다.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

`-DjavaVersion` 매개 변수는 함수 런타임에 사용할 Java 버전을 알려줍니다. Java 11에서 함수를 실행하려면 `-DjavaVersion=11`을 사용합니다. `-DjavaVersion`을 지정하지 않으면 Maven은 기본적으로 Java 8로 설정됩니다. 자세한 내용은 [Java 버전](functions-reference-java.md#java-versions)을 참조하세요.

> [!IMPORTANT]
> 이 문서를 완료하려면 `JAVA_HOME` 환경 변수를 올바른 버전의 JDK 설치 위치로 설정해야 합니다.

Maven은 배포 시 프로젝트 생성 완료를 위해 필요한 값을 요청합니다.   
메시지가 표시되면 다음 값을 제공합니다.

| prompt | 값 | Description |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Java에 대한 [패키지 명명 규칙](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7)에 따라 모든 프로젝트에서 프로젝트를 고유하게 식별하는 값입니다. |
| **artifactId** | `fabrikam-functions` | 버전 번호가 없는 jar의 이름인 값입니다. |
| **version** | `1.0-SNAPSHOT` | 기본값을 선택합니다. |
| **package** | `com.fabrikam.functions` | 생성된 함수 코드에 대한 Java 패키지인 값입니다. 기본값을 사용하세요. |

`Y`를 입력하거나 Enter 키를 눌러 확인합니다.

Maven은 이름이 _artifactId_ 인 새 폴더에 프로젝트 파일을 만드는데, 이 예제에서는 `fabrikam-functions`입니다. 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init --worker-runtime custom --docker
```
::: zone-end

`--docker` 옵션은 프로젝트에 대한 `Dockerfile`을 생성하는데, 이는 Azure Functions 및 선택한 런타임에서 사용하는 데 적합한 사용자 지정 컨테이너를 정의합니다.

::: zone pivot="programming-language-java"  
프로젝트 폴더로 이동합니다.

```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp"  

# <a name="in-process"></a>[In-Process](#tab/in-process)
Dockerfile을 변경할 필요가 없습니다.
# <a name="isolated-process"></a>[격리된 프로세스](#tab/isolated-process)
Dockerfile을 열고 다음 줄이 아직 없으면 첫 번째 `FROM` 문 다음에 추가합니다.

```docker
# Build requires 3.1 SDK
COPY --from=mcr.microsoft.com/dotnet/core/sdk:3.1 /usr/share/dotnet /usr/share/dotnet
```
---
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
다음 명령을 사용하여 함수를 프로젝트에 추가합니다. 여기서 `--name` 인수는 함수의 고유 이름이고, `--template` 인수는 함수의 트리거를 지정합니다. `func new`는 프로젝트에 C# 코드 파일을 만듭니다.

```console
func new --name HttpExample --template "HTTP trigger" --authlevel anonymous
```
::: zone-end

::: zone pivot="programming-language-other,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
다음 명령을 사용하여 함수를 프로젝트에 추가합니다. 여기서 `--name` 인수는 함수의 고유 이름이고, `--template` 인수는 함수의 트리거를 지정합니다. `func new`는 *function.json* 이라는 구성 파일을 포함하는 함수 이름과 일치하는 하위 폴더를 만듭니다.

```console
func new --name HttpExample --template "HTTP trigger" --authlevel anonymous
```
::: zone-end  
::: zone pivot="programming-language-other" 
텍스트 편집기에서 *handler.R* 이라는 프로젝트 폴더에 파일을 만듭니다. 해당 콘텐츠로 다음을 추가합니다.

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT <- strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)

http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) {
    list(body=paste(
      "Hello,",
      if(substr(request$QUERY_STRING,1,6)=="?name=") 
        substr(request$QUERY_STRING,7,40) else "World",
      sep=" "))
  }
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

*host.json* 에서 `customHandler` 섹션을 수정하여 사용자 지정 처리기의 시작 명령을 구성합니다.

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

함수를 로컬로 테스트하려면 프로젝트 폴더의 루트에서 로컬 Azure Functions 런타임 호스트를 시작합니다. 
::: zone pivot="programming-language-csharp"  
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 

출력에 `HttpExample` 엔드포인트가 표시되면 `http://localhost:7071/api/HttpExample?name=Functions`로 이동합니다. 브라우저에서 `name` 쿼리 매개 변수에 제공된 값인 `Functions`를 다시 에코하는 "hello" 메시지를 표시해야 합니다.

**Ctrl**-**C** 를 사용하여 호스트를 중지합니다.

## <a name="build-the-container-image-and-test-locally"></a>컨테이너 이미지 빌드 및 로컬로 테스트

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
(선택 사항) 프로젝트 폴더의 루트에서 *Dockerfile* 을 검사합니다. Dockerfile은 Linux에서 함수 앱을 실행하는 데 필요한 환경을 설명합니다.  Azure Functions에 대해 지원되는 기본 이미지의 전체 목록은 [Azure Functions 기본 이미지 페이지](https://hub.docker.com/_/microsoft-azure-functions-base)에 나와 있습니다.
::: zone-end

::: zone pivot="programming-language-other"
프로젝트 폴더의 루트에서 *Dockerfile* 을 검사합니다. Dockerfile은 Linux에서 함수 앱을 실행하는 데 필요한 환경을 설명합니다. 사용자 지정 처리기 애플리케이션은 `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` 이미지를 기본으로 사용합니다.

*Dockerfile* 을 수정하여 R을 설치합니다. *Dockerfile* 의 콘텐츠를 다음으로 바꿉니다.

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

루트 프로젝트 폴더에서 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 명령을 실행하고 이름(`azurefunctionsimage`) 및 태그(`v1.0.0`)를 입력합니다. `<DOCKER_ID>`를 Docker 허브 계정 ID로 바꿉니다. 이 명령은 컨테이너에 대한 Docker 이미지를 빌드합니다.

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

명령이 완료되면 새 컨테이너를 로컬에서 실행할 수 있습니다.
    
빌드를 테스트하려면 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 명령을 사용하여 로컬 컨테이너에서 이미지를 실행합니다. 여기서 `<DOCKER_ID`를 Docker ID로 다시 바꾸고, `-p 8080:80` 포트 인수를 추가합니다.

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp"
# <a name="in-process"></a>[In-Process](#tab/in-process)
로컬 컨테이너에서 이미지가 시작되면 `http://localhost:8080/api/HttpExample?name=Functions`로 이동합니다. 이 경우 이전과 동일한 "hello" 메시지가 표시되어야 합니다. 만든 HTTP 트리거 함수에 익명 권한 부여가 사용되기 때문에 액세스 키를 가져올 필요 없이 컨테이너에서 실행되는 함수를 호출할 수 있습니다. 자세한 내용은 [권한 부여 키]를 참조하세요.
# <a name="isolated-process"></a>[격리된 프로세스](#tab/isolated-process)
로컬 컨테이너에서 이미지가 시작되면 `http://localhost:8080/api/HttpExample`로 이동합니다. 이 경우 이전과 동일한 인사 메시지가 표시되어야 합니다. 만든 HTTP 트리거 함수에 익명 권한 부여가 사용되기 때문에 액세스 키를 가져올 필요 없이 컨테이너에서 실행되는 함수를 호출할 수 있습니다. 자세한 내용은 [권한 부여 키]를 참조하세요.

---
::: zone-end
::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
로컬 컨테이너에서 이미지가 시작되면 `http://localhost:8080/api/HttpExample?name=Functions`로 이동합니다. 이 경우 이전과 동일한 "hello" 메시지가 표시되어야 합니다. 만든 HTTP 트리거 함수에 익명 권한 부여가 사용되기 때문에 액세스 키를 가져올 필요 없이 컨테이너에서 실행되는 함수를 호출할 수 있습니다. 자세한 내용은 [권한 부여 키]를 참조하세요.
::: zone-end  

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

## <a name="create-supporting-azure-resources-for-your-function"></a>함수를 지원하는 Azure 리소스 만들기

함수 코드를 Azure에 배포하기 전에 다음 세 가지 리소스를 만들어야 합니다.

- [리소스 그룹](../azure-resource-manager/management/overview.md) - 관련 리소스에 대한 논리 컨테이너입니다.
- [스토리지 계정](../storage/common/storage-account-create.md) - 함수에 대한 상태 및 기타 정보를 유지 관리합니다.
- 함수 앱 - 함수 코드를 실행할 수 있는 환경을 제공합니다. 함수 앱은 로컬 함수 프로젝트에 매핑되며, 함수를 논리적 단위로 그룹화하여 리소스를 더 쉽게 관리, 배포 및 공유할 수 있습니다.

다음 명령을 사용하여 이러한 항목을 만듭니다. Azure CLI와 PowerShell이 모두 지원됩니다.

1. 아직 로그인하지 않은 경우 Azure에 로그인합니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    [az login](/cli/azure/reference-index#az_login) 명령은 Azure 계정에 로그인합니다.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet은 Azure 계정에 로그인합니다.

    ---

1. 선택한 지역에 `AzureFunctionsContainers-rg`라는 리소스 그룹을 만듭니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location <REGION>
    ```
 
    [az group create](/cli/azure/group#az_group_create) 명령은 리소스 그룹을 만듭니다. 위의 명령에서 [az account list-locations](/cli/azure/account#az_account_list_locations) 명령에서 반환된 사용 가능한 지역 코드를 사용하여 `<REGION>`을 가까운 지역으로 바꿉니다.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsContainers-rg -Location <REGION>
    ```

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령은 리소스 그룹을 만듭니다. 일반적으로 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) cmdlet에서 반환된 사용 가능한 지역을 사용하여 가까운 지역에 리소스 그룹과 리소스를 만듭니다.

    ---

1. 범용 스토리지 계정을 리소스 그룹 및 지역에 만듭니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location <REGION> --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령은 스토리지 계정을 만듭니다. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsContainers-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location <REGION>
    ```

    [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet은 스토리지 계정을 만듭니다.

    ---

    이전 예제에서 `<STORAGE_NAME>`을 사용자에게 적절하고 Azure Storage에서 고유한 이름으로 바꿉니다. 이름은 3~24자의 숫자와 소문자만 포함해야 합니다. `Standard_LRS`는 범용 계정을 지정하며, [Functions로 지원](storage-considerations.md#storage-account-requirements)됩니다.
    
1. 명령을 사용하여 **탄력적 프리미엄 1** 가격 책정 계층(`--sku EP1`), `<REGION>` 및 Linux 컨테이너(`--is-linux`)에서 `myPremiumPlan`라는 Azure Functions 프리미엄 플랜을 만듭니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location <REGION> --number-of-workers 1 --sku EP1 --is-linux
    ```
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```powershell
    New-AzFunctionAppPlan -ResourceGroupName AzureFunctionsContainers-rg -Name MyPremiumPlan -Location <REGION> -Sku EP1 -WorkerType Linux
    ```
    ---
    여기서는 필요에 따라 확장할 수 있는 프리미엄 요금제를 사용합니다. 호스팅에 대한 자세한 내용은 [Azure Functions 호스팅 계획 비교](functions-scale.md)를 참조하세요. 비용을 계산하려면 [Functions 가격 페이지](https://azure.microsoft.com/pricing/details/functions/)를 참조하세요.

    또한 이 명령은 동일한 리소스 그룹에 연결된 Azure Application Insights 인스턴스를 프로비저닝하여 함수 앱을 모니터링하고 로그를 볼 수 있습니다. 자세한 내용은 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요. 인스턴스를 활성화할 때까지 비용이 발생하지 않습니다.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>이미지를 사용하여 Azure에서 함수 앱 만들기 및 구성

Azure의 함수 앱은 호스팅 계획에서 함수 실행을 관리합니다. 이 섹션에서는 이전 섹션의 Azure 리소스를 사용하여 Docker Hub의 이미지에서 함수 앱을 만들고 Azure Storage에 대한 연결 문자열을 사용하여 구성합니다.

1. 다음 명령을 사용하여 함수 앱을 만듭니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp create --name <APP_NAME> --storage-account <STORAGE_NAME> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --deployment-container-image-name <DOCKER_ID>/azurefunctionsimage:v1.0.0
    ```

    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 명령에서 *deployment-container-image-name* 매개 변수는 함수 앱에 사용할 이미지를 지정합니다. [az functionapp config container show](/cli/azure/functionapp/config/container#az_functionapp_config_container_show) 명령을 사용하여 배포에 사용되는 이미지에 대한 정보를 볼 수 있습니다. 또한 [az functionapp config container set](/cli/azure/functionapp/config/container#az_functionapp_config_container_set) 명령을 사용하여 다른 이미지에서 배포할 수도 있습니다.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -PlanName myPremiumPlan -StorageAccount <STORAGE_NAME> -DockerImageName <DOCKER_ID>/azurefunctionsimage:v1.0.0
    ```
    ---
    
    이 예제에서는 `<STORAGE_NAME>`을 이전 섹션에서 스토리지 계정에 대해 사용한 이름으로 바꿉니다. 또한 `<APP_NAME>`을 적절하고 전역적으로 고유한 이름으로 바꾸고, `<DOCKER_ID>`를 DockerHub ID로 바꿉니다.    
    
    > [!TIP]  
    > host.json 파일의 [`DisableColor` 설정](functions-host-json.md#console)을 사용하여 ANSI 제어 문자가 컨테이너 로그에 기록되지 않도록 할 수 있습니다. 

1. 다음 명령을 사용하여 만든 스토리지 계정에 대한 연결 문자열을 가져옵니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <STORAGE_NAME> --query connectionString --output tsv
    ```

    [az storage account show-connection-string](/cli/azure/storage/account) 명령어를 사용하여 스토리지 계정의 연결 문자열이 반환됩니다. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    $storage_name = "glengagtestdockerstorage"
    $key = (Get-AzStorageAccountKey -ResourceGroupName AzureFunctionsContainers-rg -Name $storage_name)[0].Value
    $string = "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName=" + $storage_name + ";AccountKey=" + $key
    Write-Output($string) 
    ```
    [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) cmdlet으로 반환된 키는 스토리지 계정에 대해 연결 문자열을 생성하는 데 사용됩니다.

    ---    

    `<STORAGE_NAME>`을 이전에 만든 스토리지 계정의 이름으로 바꿉니다.

1. 다음 명령을 사용하여 이 설정을 함수 앱에 추가합니다. 
 
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp config appsettings set --name <APP_NAME> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<CONNECTION_STRING>
    ```
    [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_ppsettings_set) 명령이 설정을 만듭니다. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    Update-AzFunctionAppSetting -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -AppSetting @{"AzureWebJobsStorage"="<CONNECTION_STRING>"}
    ```
    [Update-AzFunctionAppSetting](/powershell/module/az.functions/update-azfunctionappsetting) cmdlet이 설정을 만듭니다.

    ---

    이 명령어에서 `<APP_NAME>`을 함수 앱의 이름으로 바꾸고 `<CONNECTION_STRING>`을 이전 단계의 연결 문자열로 바꿉니다. 연결은 `DefaultEndpointProtocol=`로 시작하는 긴 인코딩된 문자열입니다.
 

1. 이제 함수에서 이 연결 문자열을 사용하여 스토리지 계정에 액세스할 수 있습니다.

> [!NOTE]    
> 사용자 지정 이미지를 프라이빗 컨테이너 계정에 게시하는 경우 연결 문자열에 Dockerfile의 환경 변수를 대신 사용해야 합니다. 자세한 내용은 [ENV 명령](https://docs.docker.com/engine/reference/builder/#env)을 참조하세요. `DOCKER_REGISTRY_SERVER_USERNAME` 및 `DOCKER_REGISTRY_SERVER_PASSWORD` 변수도 설정해야 합니다. 값을 사용하려면 이미지를 다시 빌드하고, 이미지를 레지스트리로 푸시한 다음, Azure에서 함수 앱을 다시 시작해야 합니다.

## <a name="verify-your-functions-on-azure"></a>Azure에서 함수 확인

이미지가 Azure에서 함수 앱에 배포되었으므로 이제 HTTP 요청을 통해 이전과 같이 함수를 호출할 수 있습니다.
브라우저에서 다음과 같이 URL로 이동합니다.

::: zone pivot="programming-language-java,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
`https://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`  
::: zone-end  
::: zone pivot="programming-language-csharp"  
# <a name="in-process"></a>[In-Process](#tab/in-process) 
`https://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`
# <a name="isolated-process"></a>[격리된 프로세스](#tab/isolated-process)
`https://<APP_NAME>.azurewebsites.net/api/HttpExample`

---
:::zone-end  

`<APP_NAME>`은 함수 앱 이름으로 바꿉니다. 이 URL로 이동할 때 브라우저는 함수를 로컬로 실행할 때와 유사한 출력을 표시해야 합니다.

## <a name="enable-continuous-deployment-to-azure"></a>Azure로의 지속적인 배포 사용

레지스트리의 이미지를 업데이트할 때마다 Azure Functions에서 이미지 배포를 자동으로 업데이트하도록 설정할 수 있습니다.

1. 다음 명령을 사용하여 연속 배포를 사용하도록 설정하고 웹후크 URL을 가져옵니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <APP_NAME> --resource-group AzureFunctionsContainers-rg
    ```
    
    [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_config) 명령은 연속 배포를 사용하도록 설정하고 배포 웹후크 URL을 반환합니다. 이 URL은 나중에 [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az_functionapp_deployment_container_show_cd_url) 명령을 사용하여 검색할 수 있습니다.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    ```azurepowershell
    Update-AzFunctionAppSetting -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg -AppSetting @{"DOCKER_ENABLE_CI" = "true"}
    Get-AzWebAppContainerContinuousDeploymentUrl -Name <APP_NAME> -ResourceGroupName AzureFunctionsContainers-rg
    ```
    
    `DOCKER_ENABLE_CI` 애플리케이션 설정은 컨테이너 리포지토리에서 연속 배포가 사용하도록 설정되었는지 여부를 제어합니다.  [Get-AzWebAppContainerContinuousDeploymentUrl](/powershell/module/az.websites/get-azwebappcontainercontinuousdeploymenturl) cmdlet은 배포 웹후크의 URL을 반환합니다.

    ---    

    이전과 같이 `<APP_NAME>`을 함수 앱 이름으로 바꿉니다. 

1. 배포 웹후크 URL을 클립보드에 복사합니다.

1. [Docker Hub](https://hub.docker.com/)를 열고, 로그인하여, 탐색 모음에서  **리포지토리** 를 선택합니다. 이미지를 찾아 선택하고, **웹후크** 탭을 선택하고, **웹후크 이름** 을 지정하고, URL을 **웹후크 URL** 에 붙여넣은 다음, **만들기** 를 선택합니다.

    ![DockerHub 리포지토리에서 웹후크 추가](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. 웹후크 세트를 사용하면 Docker Hub에서 이미지를 업데이트할 때마다 Azure Functions에서 해당 이미지를 다시 배포합니다.

## <a name="enable-ssh-connections"></a>SSH 연결 사용

SSH를 사용하면 컨테이너와 클라이언트 간의 보안 통신을 설정할 수 있습니다. SSH를 사용하면 App Service 고급 도구(Kudu)를 사용하여 컨테이너에 연결할 수 있습니다. SSH를 사용하여 컨테이너에 쉽게 연결할 수 있도록 Azure Functions는 이미 SSH를 사용하도록 설정된 기본 이미지를 제공합니다. Dockerfile을 편집한 다음, 해당 이미지를 다시 빌드하여 다시 배포하기만 하면 됩니다. 그런 다음, 고급 도구(Kudu)를 통해 컨테이너에 연결할 수 있습니다.

1. Dockerfile에서 `-appservice` 문자열을 `FROM` 명령의 기본 이미지에 추가합니다.

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end
    
1. `docker build` 명령을 다시 사용하여 이미지를 다시 빌드합니다. 여기서 `<docker_id>`를 Docker ID로 바꿉니다.

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. 업데이트된 이미지를 Docker Hub로 푸시합니다. 이 경우 이미지의 업데이트된 세그먼트만 업로드해야 하는 첫 번째 푸시에 비해 훨씬 더 짧은 시간이 걸립니다.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions에서 자동으로 이미지를 함수 앱으로 다시 배포합니다. 이 프로세스는 1분 이내에 수행됩니다.

1. 브라우저에서 `https://<app_name>.scm.azurewebsites.net/`을 엽니다. 여기서 `<app_name>`을 고유한 이름으로 바꿉니다. 이 URL은 함수 앱 컨테이너의 고급 도구(Kudu) 엔드포인트입니다.

1. Azure 계정에 로그인한 다음, **SSH** 를 선택하여 컨테이너와의 연결을 설정합니다. Azure에서 아직도 컨테이너 이미지를 업데이트하고 있는 경우 연결하는 데 몇 분 정도 걸릴 수 있습니다.

1. 컨테이너와의 연결이 설정되면 `top` 명령을 실행하여 현재 실행 중인 프로세스를 확인합니다. 

    ![SSH 세션에서 실행되는 Linux top 명령](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-azure-queue-storage"></a>Azure Queue Storage에 쓰기

Azure Functions를 사용하면 고유한 통합 코드를 작성하지 않고도 다른 Azure 서비스 및 리소스에 함수를 연결할 수 있습니다. 입력과 출력을 모두 나타내는 이러한 *바인딩* 은 함수 정의 내에서 선언됩니다. 바인딩의 데이터는 함수에 매개 변수로 제공됩니다. *트리거* 는 특수한 형식의 입력 바인딩입니다. 함수에는 하나의 트리거만 있지만, 여러 개의 입력 및 출력 바인딩이 있을 수 있습니다. 자세한 내용은 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md)을 참조하세요.

이 섹션에서는 함수를 Azure Queue Storage와 통합하는 방법을 보여줍니다. 이 함수에 추가하는 출력 바인딩은 HTTP 요청의 데이터를 큐의 메시지에 씁니다.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>바인딩 확장 등록
::: zone-end 

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>출력 바인딩을 사용하는 코드 추가

큐 바인딩을 정의했으면 이제 바인딩 매개 변수를 사용하여 큐에 메시지를 쓰도록 함수를 업데이트할 수 있습니다.
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>레지스트리의 이미지 업데이트

1. 루트 폴더에서 `docker build`를 다시 실행하고, 이번에는 태그의 버전을 `v1.0.1`로 업데이트합니다. 이전과 같이 `<docker_id>`를 Docker Hub 계정 ID로 바꿉니다.

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. `docker push`를 사용하여 업데이트된 이미지를 리포지토리로 다시 푸시합니다.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. 지속적인 업데이트를 구성했으므로 레지스트리의 이미지를 다시 업데이트하면 Azure에서 함수 앱이 자동으로 업데이트됩니다.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Azure Storage 큐의 메시지 보기

브라우저에서 이전과 동일한 URL을 사용하여 함수를 호출합니다. 함수 코드의 해당 부분을 수정하지 않았으므로 브라우저에서 이전과 동일한 응답을 표시해야 합니다. 그러나 추가된 코드에서 `name` URL 매개 변수를 사용하는 메시지를 `outqueue` 스토리지 큐에 작성했습니다.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스를 사용하여 Azure Function을 계속 사용하려면 해당 리소스를 모두 그대로 유지할 수 있습니다. Azure Functions 프리미엄 요금제를 만들었으므로 매일 1~2USD의 지속적인 비용이 발생합니다.

지속적인 비용을 방지하려면 `AzureFunctionsContainer-rg` 리소스 그룹을 삭제하여 해당 그룹의 모든 리소스를 정리합니다. 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>다음 단계

+ [함수 모니터링](functions-monitoring.md)
+ [비율 크기 조정 및 호스팅 옵션](functions-scale.md)
+ [Kubernetes 기반 서버리스 호스팅](functions-kubernetes-keda.md)

[권한 부여 키]: functions-bindings-http-webhook-trigger.md#authorization-keys