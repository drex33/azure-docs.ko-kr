---
title: 명령줄에서 Python 함수 만들기 - Azure Functions
description: 명령줄에서 Python 함수를 만든 다음, 로컬 프로젝트를 Azure Functions의 서버리스 호스팅에 게시하는 방법을 알아봅니다.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
- devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-python-uiex
ms.openlocfilehash: cd67f81868d43ece3638d3d46ed07d4044642b19
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123468351"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>빠른 시작: 명령줄에서 Azure에 Python 함수 만들기

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

이 문서에서는 명령줄 도구를 사용하여 HTTP 요청에 응답하는 Python 함수를 만듭니다. 코드를 로컬로 테스트한 후 서버리스 Azure Functions 환경에 배포합니다.

이 빠른 시작을 완료하면 Azure 계정에서 약간의 비용(몇 USD 센트)이 발생합니다.

또한 이 문서의 [Visual Studio Code 기반 버전](create-first-function-vs-code-python.md)도 있습니다.

## <a name="configure-your-local-environment"></a>로컬 환경 구성

시작하기 전에 다음이 있어야 합니다.

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) 버전 3.x.
  
+ 다음 도구 중 하나를 통해 Azure 리소스를 만듭니다.

    + [Azure CLI](/cli/azure/install-azure-cli) 버전 2.4 이상.

    + [Azure PowerShell](/powershell/azure/install-az-ps) 버전 5.0 이상.

+ [Azure Functions에서 지원하는 Python 버전](supported-languages.md#languages-by-runtime-version)

### <a name="prerequisite-check"></a>필수 구성 요소 확인

Azure 리소스를 만드는 데 Azure CLI 또는 Azure PowerShell을 사용하는지 여부에 따라 필수 구성 요소를 확인합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ 터미널 또는 명령 창에서 `func --version`을 실행하여 Azure Functions Core Tools가 3.x인지 확인합니다.

+ `az --version`을 실행하여 Azure CLI 버전이 2.4 이상인지 확인합니다.

+ `az login`을 실행하여 Azure에 로그인하고 활성 구독을 확인합니다.

+ `python --version`(Linux/macOS) 또는 `py --version`(Windows)을 실행하여 Python 버전이 3.8.x, 3.7.x 또는 3.6.x로 보고되는지 확인합니다.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ 터미널 또는 명령 창에서 `func --version`을 실행하여 Azure Functions Core Tools가 3.x인지 확인합니다.

+ `(Get-Module -ListAvailable Az).Version`을 실행하고 버전 5.0 이상을 확인합니다. 

+ `Connect-AzAccount`을 실행하여 Azure에 로그인하고 활성 구독을 확인합니다.

+ `python --version`(Linux/macOS) 또는 `py --version`(Windows)을 실행하여 Python 버전이 3.8.x, 3.7.x 또는 3.6.x로 보고되는지 확인합니다.

---

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>가상 환경 만들기 및 활성화

적절한 폴더에서 다음 명령을 실행하여 `.venv`라는 가상 환경을 만들고 활성화합니다. Azure Functions에서 지원하는 Python 3.8, 3.7 또는 3.6을 사용해야 합니다.

# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Python에서 venv 패키지를 Linux 배포에 설치하지 않은 경우 다음 명령을 실행합니다.

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

활성화된 가상 환경에서 이후의 모든 명령을 실행합니다. 

## <a name="create-a-local-function-project"></a>로컬 함수 프로젝트 만들기

Azure Functions에서 함수 프로젝트는 각각 특정 트리거에 응답하는 하나 이상의 개별 함수에 대한 컨테이너입니다. 프로젝트의 모든 함수는 동일한 로컬 및 호스팅 구성을 공유합니다. 이 섹션에서는 단일 함수가 포함된 함수 프로젝트를 만듭니다.

1. 다음과 같이 `func init` 명령을 실행하여 지정된 런타임에 *LocalFunctionProj* 폴더에 함수 프로젝트를 만듭니다.  

    ```console
    func init LocalFunctionProj --python
    ```

1. 프로젝트 폴더로 이동합니다.

    ```console
    cd LocalFunctionProj
    ```
    
    이 폴더에는 [local.settings.json](functions-develop-local.md#local-settings-file) 및 [host.json](functions-host-json.md)이라는 구성 파일을 포함하여 프로젝트의 다양한 파일이 있습니다. *local.settings.json* 은 Azure에서 다운로드한 비밀을 포함할 수 있으므로 이 파일은 기본적으로 *.gitignore* 파일의 원본 제어에서 제외됩니다.

1. 다음 명령을 사용하여 함수를 프로젝트에 추가합니다. 여기서 `--name` 인수는 함수(HttpExample)의 고유 이름이고, `--template` 인수는 함수의 트리거(HTTP)를 지정합니다.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    
    `func new`는 프로젝트의 선택된 언어에 적합한 코드 파일과 *function.json* 이라는 구성 파일을 포함하는 함수 이름과 일치하는 하위 폴더를 만듭니다.

### <a name="optional-examine-the-file-contents"></a>(선택 사항) 파일 내용 검사

원하는 경우 [로컬에서 함수 실행](#run-the-function-locally)으로 건너뛰고, 나중에 파일 내용을 검사할 수 있습니다.

#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py* 에는 *function.json* 의 구성에 따라 트리거되는 `main()` Python 함수가 포함되어 있습니다.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

HTTP 트리거의 경우 함수는 *function.json* 에 정의된 `req` 변수에 요청 데이터를 받습니다. `req`는 [azure.functions.HttpRequest 클래스](/python/api/azure-functions/azure.functions.httprequest)의 인스턴스입니다. *function.json* 에 `$return`으로 정의되는 반환 개체는 [azure.functions.HttpResponse 클래스](/python/api/azure-functions/azure.functions.httpresponse)의 인스턴스입니다. 자세한 내용은 [Azure Functions HTTP 트리거 및 바인딩](./functions-bindings-http-webhook.md?tabs=python)을 참조하세요.

#### <a name="functionjson"></a>function.json

*function.json* 은 트리거 형식을 포함하여 함수의 입력 및 출력 `bindings`를 정의하는 구성 파일입니다.

원하는 경우 다른 Python 파일을 호출하도록 `scriptFile`을 변경할 수 있습니다.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

각 바인딩에는 명령, 형식 및 고유한 이름이 필요합니다. HTTP 트리거의 입력 바인딩은 [`httpTrigger`](functions-bindings-http-webhook-trigger.md) 형식이고, 출력 바인딩은 [`http`](functions-bindings-http-webhook-output.md) 형식입니다.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>함수를 지원하는 Azure 리소스 만들기

함수 코드를 Azure에 배포하기 전에 다음 세 가지 리소스를 만들어야 합니다.

- 리소스 그룹 - 관련 리소스에 대한 논리 컨테이너입니다.
- 스토리지 계정 - 프로젝트에 대한 상태 및 기타 정보를 유지 관리합니다.
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
    
1. Azure CLI를 사용할 때 만들어진 리소스의 이름을 자동으로 추적하는 `param-persist` 옵션을 켤 수 있습니다. 자세한 내용은 [Azure CLI 지속형 매개 변수](/cli/azure/param-persist-howto)를 참조하세요.  

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az config param-persist on
    ```
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    
    이 기능은 Azure PowerShell에서 사용할 수 없습니다.
    
    ---

1. 선택한 지역에 `AzureFunctionsQuickstart-rg`라는 리소스 그룹을 만듭니다. 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location <REGION>
    ```
 
    [az group create](/cli/azure/group#az_group_create) 명령은 리소스 그룹을 만듭니다. 위의 명령에서 [az account list-locations](/cli/azure/account#az_account_list_locations) 명령에서 반환된 사용 가능한 지역 코드를 사용하여 `<REGION>`을 가까운 지역으로 바꿉니다.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location '<REGION>'
    ```

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령은 리소스 그룹을 만듭니다. 일반적으로 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) cmdlet에서 반환된 사용 가능한 지역을 사용하여 가까운 지역에 리소스 그룹과 리소스를 만듭니다.

    ---

    > [!NOTE]
    > Linux 및 Windows 앱을 동일한 리소스 그룹에 호스트할 수 없습니다. Windows 함수 앱 또는 웹앱이 포함된 `AzureFunctionsQuickstart-rg`이라는 기존 리소스 그룹이 있는 경우 다른 리소스 그룹을 사용해야 합니다.

1. 범용 스토리지 계정을 리소스 그룹 및 지역에 만듭니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령은 스토리지 계정을 만듭니다. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location <REGION>
    ```

    [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet은 스토리지 계정을 만듭니다.

    ---

    이전 예제에서 `<STORAGE_NAME>`을 사용자에게 적절하고 Azure Storage에서 고유한 이름으로 바꿉니다. 이름은 3~24자의 숫자와 소문자만 포함해야 합니다. `Standard_LRS`는 범용 계정을 지정하며, [Functions로 지원](storage-considerations.md#storage-account-requirements)됩니다.
    
    이 빠른 시작에 대해 스토리지 계정은 약간의 비용(몇 USD 센트)만 발생합니다.

1. Azure에서 함수 앱을 만듭니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --os-type linux
    ```
    
    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 명령은 Azure에서 함수 앱을 만듭니다. Python 3.7 또는 3.6을 사용하는 경우 `--runtime-version`을 `3.7` 또는 `3.6`으로 각각 변경합니다. Python 함수는 기본값인 Windows에서 실행할 수 없기 때문에 `--os-type linux`를 제공해야 합니다.
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location '<REGION>'
    ```
    
    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cmdlet은 Azure에서 함수 앱을 만듭니다. Python 3.7 또는 3.6을 사용하는 경우 `-RuntimeVersion`을 `3.7` 또는 `3.6`으로 각각 변경합니다.

    ---
    
    이전 예에서 `<APP_NAME>`을 적절하고 전역적으로 고유한 이름으로 바꿉니다.  `<APP_NAME>`은 함수 앱의 기본 DNS 도메인이기도 합니다. 
    
    이 명령은 [Azure Functions 소비 계획](consumption-plan.md)에 따라 지정된 언어 런타임을 실행하는 함수 앱을 만듭니다. 여기서 발생하는 사용량에 대한 비용은 무료입니다. 또한 이 명령은 동일한 리소스 그룹에 연결된 Azure Application Insights 인스턴스를 프로비저닝하여 함수 앱을 모니터링하고 로그를 볼 수 있습니다. 자세한 내용은 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요. 인스턴스를 활성화할 때까지 비용이 발생하지 않습니다.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

다음 명령을 실행하여 Azure Portal의 Application Insights에서 거의 실시간으로 [스트리밍 로그](functions-run-local.md#enable-streaming-logs)를 확인합니다.

```console
func azure functionapp logstream <APP_NAME> --browser
```

별도의 터미널 창 또는 브라우저에서 원격 함수를 다시 호출합니다. Azure의 함수 실행에 대한 자세한 로그가 터미널에 표시됩니다. 

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Storage 큐에 연결](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[문제가 있나요? 알려주세요.](https://aka.ms/python-functions-qs-survey)
