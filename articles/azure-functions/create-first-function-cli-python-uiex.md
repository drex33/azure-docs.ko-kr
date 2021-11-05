---
title: 명령줄에서 Azure Functions용 Python 함수 만들기
description: 명령줄에서 Python 함수를 만들고 로컬 프로젝트를 Azure Functions의 서버리스 호스팅에 게시하는 방법을 알아봅니다.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
- devx-track-azurepowershell
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 951a0cec92d531aa980b519df2a046355997c175
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039338"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>빠른 시작: 명령줄에서 Azure에 Python 함수 만들기

> [!div class="op_single_selector" title1="함수 언어를 선택합니다. "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

이 문서에서는 명령줄 도구를 사용하여 HTTP 요청에 응답하는 Python 함수를 만듭니다. 코드를 로컬에서 테스트한 후 다음에 배포합니다. <abbr title="서버의 모든 세부 정보가 애플리케이션 개발자에게 투명하게 공개되어 코드 배포 및 관리 프로세스가 간소화되는 런타임 컴퓨팅 환경.">서버를 사용하지 않음</abbr> 환경 <abbr title="애플리케이션을 위한 저렴한 서버리스 컴퓨팅 환경을 제공하는 Azure의 서비스.">Azure 기능</abbr>.

이 빠른 시작을 완료하면 Azure 계정에서 약간의 비용(몇 USD 센트)이 발생합니다.

또한 이 문서의 [Visual Studio Code 기반 버전](create-first-function-vs-code-python.md)도 있습니다.

## <a name="1-configure-your-environment"></a>1. 환경 구성

시작하기 전에 다음이 있어야 합니다.

+ Azure <abbr title="Azure 사용량에 대한 청구 정보를 유지 관리하는 프로필.">account</abbr> 활성 <abbr title="Azure에서 리소스를 관리하는 기본 조직 구조이며 일반적으로 조직 내의 개인 또는 부서와 연결됩니다.">subscription</abbr>. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) 버전 3.x.

+ 그리고 <abbr title="로컬 개발 컴퓨터에서 Azure 리소스로 작업하기 위한 플랫폼 간 명령줄 도구 집합(Azure Portal 대용).">Azure CLI</abbr> 또는 <abbr title="로컬 개발 컴퓨터에서 Azure 리소스로 작업하기 위한 명령을 제공하는 PowerShell 모듈(Azure Portal 대용).">Azure PowerShell</abbr> Azure 리소스 생성용:

    + [Azure CLI](/cli/azure/install-azure-cli) 버전 2.4 이상.

    + [Az PowerShell 모듈](/powershell/azure/install-az-ps), 버전 5.9.0 이상.

+ Azure Functions 버전 3.x에서 모두 지원되는 [Python 3.8(64비트)](https://www.python.org/downloads/release/python-382/), [Python 3.7(64비트)](https://www.python.org/downloads/release/python-375/), [Python 3.6(64비트)](https://www.python.org/downloads/release/python-368/).

### <a name="11-prerequisite-check"></a>1.1 필수 구성 요소 확인

Azure 리소스를 만드는 데 Azure CLI 또는 Azure PowerShell을 사용하는지 여부에 따라 필수 구성 요소를 확인합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ 터미널 또는 명령 창에서 `func --version`을 실행하여 다음을 확인합니다. <abbr title="로컬 컴퓨터에서 Azure Functions로 작업하기 위한 명령줄 도구 집합.">Azure Functions Core Tools</abbr> 버전 3.x입니다.

+ `az --version`을 실행하여 Azure CLI 버전이 2.4 이상인지 확인합니다.

+ `az login`을 실행하여 Azure에 로그인하고 활성 구독을 확인합니다.

+ `python --version`(Linux/macOS) 또는 `py --version`(Windows)을 실행하여 Python 버전이 3.8.x, 3.7.x 또는 3.6.x로 보고되는지 확인합니다.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ 터미널 또는 명령 창에서 `func --version`을 실행하여 다음을 확인합니다. <abbr title="로컬 컴퓨터에서 Azure Functions로 작업하기 위한 명령줄 도구 집합.">Azure Functions Core Tools</abbr> 버전 3.x입니다.

+ `(Get-Module -ListAvailable Az).Version`을 실행하고 버전 5.0 이상을 확인합니다.

+ `Connect-AzAccount`을 실행하여 Azure에 로그인하고 활성 구독을 확인합니다.

+ `python --version`(Linux/macOS) 또는 `py --version`(Windows)을 실행하여 Python 버전이 3.8.x, 3.7.x 또는 3.6.x로 보고되는지 확인합니다.

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a>가상 환경 만들기 및 활성화

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

<br/>

---

## <a name="3-create-a-local-function-project"></a>3. 로컬 함수 프로젝트 만들기

이 섹션에서는 로컬 항목을 만듭니다. <abbr title="함께 배포하고 관리할 수 있는 하나 이상의 개별 함수에 대한 논리적 컨테이너.">Azure Functions 프로젝트</abbr> Python. 프로젝트의 각 함수는 특정 항목에 응답합니다. <abbr title="함수의 코드를 호출하는 이벤트의 유형(예: HTTP 요청, 큐 메시지 또는 특정 시간).">트리거</abbr>.

1. `func init` 명령을 실행하여 지정된 런타임에 *LocalFunctionProj* 폴더에 함수 프로젝트를 만듭니다.

    ```console
    func init LocalFunctionProj --python
    ```

1. 프로젝트 폴더로 이동합니다.

    ```console
    cd LocalFunctionProj
    ```

    <br/>
    <details>
    <summary><strong>LocalFunctionProj 폴더에 생성되는 항목은 무엇인가요?</strong></summary>

    이 폴더에는 [local.settings.json](functions-develop-local.md#local-settings-file) 및 [host.json](functions-host-json.md)이라는 구성 파일을 포함하여 프로젝트의 다양한 파일이 있습니다. *local.settings.json* 은 Azure에서 다운로드한 비밀을 포함할 수 있으므로 이 파일은 기본적으로 *.gitignore* 파일의 원본 제어에서 제외됩니다.
    </details>

1. 다음 명령을 사용하여 프로젝트에 함수를 추가합니다.

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```
    `--name` 인수는 함수(HttpExample)의 고유한 이름입니다.

    `--template` 인수는 함수의 트리거(HTTP)를 지정합니다.

    `func new`는 함수 코드와 구성 파일 이름 *function.json* 을 사용하여 *\_\_init\_\_.py* 파일이 포함된 함수 이름과 일치하는 하위 폴더를 만듭니다.

    <br/>
    <details>
    <summary><strong>__init__.py에 대한 코드</strong></summary>

    *\_\_init\_\_.py* 에는 *function.json* 의 구성에 따라 트리거되는 `main()` Python 함수가 포함되어 있습니다.

    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

    HTTP 트리거의 경우 함수는 *function.json* 에 정의된 `req` 변수에 요청 데이터를 받습니다. `req`는 [azure.functions.HttpRequest 클래스](/python/api/azure-functions/azure.functions.httprequest)의 인스턴스입니다. *function.json* 에 `$return`으로 정의되는 반환 개체는 [azure.functions.HttpResponse 클래스](/python/api/azure-functions/azure.functions.httpresponse)의 인스턴스입니다. 자세한 내용은 [Azure Functions HTTP 트리거 및 바인딩](./functions-bindings-http-webhook.md?tabs=python)을 참조하세요.
    </details>

    <br/>
    <details>
    <summary><strong>function.json에 대한 코드</strong></summary>

    *function.json* 은 다음을 정의하는 구성 파일입니다. <abbr title="함수와 기타 리소스 간의 선언적 연결. 입력 바인딩은 함수에 데이터를 제공합니다. 출력 바인딩은 함수에서 다른 리소스로 데이터를 제공합니다.">입력 및 출력 바인딩</abbr> 트리거 유형을 포함하는 함수용.

    원하는 경우 다른 Python 파일을 호출하도록 `scriptFile`을 변경할 수 있습니다.

    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

    각 바인딩에는 명령, 형식 및 고유한 이름이 필요합니다. HTTP 트리거의 입력 바인딩은 [`httpTrigger`](functions-bindings-http-webhook-trigger.md) 형식이고, 출력 바인딩은 [`http`](functions-bindings-http-webhook-output.md) 형식입니다.
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. 로컬에서 함수 실행

1. *LocalFunctionProj* 폴더에서 로컬 Azure Functions 런타임 호스트를 시작하여 함수를 실행합니다.

    ```
    func start
    ```

    출력의 끝 부분에 다음 줄이 표시됩니다.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...

    </pre>

    <br/>
    <details>
    <summary><strong>출력에 HttpExample이 표시되지 않음</strong></summary>

    HttpExample이 표시되지 않는다면 프로젝트의 루트 폴더 외부에서 호스트를 시작했을 가능성이 높습니다. 이 경우 <kbd>Ctrl+C</kbd>를 사용하여 호스트를 중지하고, 프로젝트의 루트 폴더로 이동하여 이전 명령을 다시 실행합니다.
    </details>

1. 이 출력에서 **HttpExample** 함수의 URL을 브라우저에 복사하고 **?name=<YOUR_NAME>** 쿼리 문자열을 추가하여 전체 URL을 **http://localhost:7071/api/HttpExample?name=Functions** 와 같이 만듭니다. 브라우저에 **Hello Functions** 같은 메시지가 표시됩니다.

    ![브라우저에서 로컬로 실행되는 함수의 결과](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. 프로젝트를 시작한 터미널에도 요청 시 로그 출력이 표시됩니다.

1. 완료되면 <kbd>Ctrl+C</kbd>를 사용하고 <kbd>y</kbd>를 선택하여 함수 호스트를 중지합니다.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. 함수를 지원하는 Azure 리소스 만들기

함수 코드를 Azure에 배포하기 전에 다음을 만들어야 합니다. <abbr title="한 단위로 관리할 수 있는 관련 Azure 리소스에 대한 논리적 컨테이너.">리소스 그룹</abbr>, <abbr title="모든 Azure Storage 데이터 개체를 포함하는 계정. 스토리지 계정은 스토리지 데이터의 고유한 네임스페이스를 제공합니다.">스토리지 계정 만들기</abbr>및 <abbr title="함수가 실행되는 기본 컴퓨팅 환경을 제공하며 Azure에서 서버리스 함수를 호스팅하는 클라우드 리소스.">함수 앱</abbr> 다음 명령을 사용합니다.

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

1. `westeurope` 지역에 `AzureFunctionsQuickstart-rg`라는 리소스 그룹을 만듭니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```

    [az group create](/cli/azure/group#az_group_create) 명령은 리소스 그룹을 만듭니다. 일반적으로 리소스 그룹 및 리소스를 만듭니다. <abbr title="리소스가 할당된 특정 Azure 데이터 센터에 대한 지리적 참조.">region</abbr> 가까운 곳. `az account list-locations` 명령에서 반환된 사용 가능한 지역 사용

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령은 리소스 그룹을 만듭니다. 일반적으로 [Get-AzLocation](/powershell/module/az.resources/get-azlocation) cmdlet에서 반환된 사용 가능한 지역을 사용하여 가까운 지역에 리소스 그룹과 리소스를 만듭니다.

    ---

    Linux 및 Windows 앱을 동일한 리소스 그룹에 호스트할 수 없습니다. Windows 함수 앱 또는 웹앱이 포함된 `AzureFunctionsQuickstart-rg`이라는 기존 리소스 그룹이 있는 경우 다른 리소스 그룹을 사용해야 합니다.

1. 리소스 그룹 및 지역에 범용 Azure Storage 계정을 만듭니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령은 스토리지 계정을 만듭니다.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet은 스토리지 계정을 만듭니다.

    ---

    `<STORAGE_NAME>`을 적절한 이름으로 바꿉니다. <abbr title="이 이름은 모든 Azure 고객이 전역적으로 사용하는 모든 스토리지 계정에서 고유해야 합니다. 예를 들어 contosobizappstorage20과 같이 개인 또는 회사 이름, 애플리케이션 이름 및 숫자 식별자를 조합하여 사용할 수 있습니다.">Azure Storage에서 고유함</abbr>. 이름은 3~24자의 숫자와 소문자만 포함해야 합니다. `Standard_LRS`는 범용 계정을 지정하며, [Functions로 지원](storage-considerations.md#storage-account-requirements)됩니다.

    이 빠른 시작에 대해 스토리지 계정은 약간의 비용(몇 USD 센트)만 발생합니다.

1. Azure에서 함수 앱을 만듭니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```

    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 명령은 Azure에서 함수 앱을 만듭니다. Python 3.7 또는 3.6을 사용하는 경우 `--runtime-version`을 `3.7` 또는 `3.6`으로 각각 변경합니다.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```

    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cmdlet은 Azure에서 함수 앱을 만듭니다. Python 3.7 또는 3.6을 사용하는 경우 `-RuntimeVersion`을 `3.7` 또는 `3.6`으로 각각 변경합니다.

    ---

    `<STORAGE_NAME>`을 이전 단계에서 사용한 계정의 이름으로 바꿉니다.

    `<APP_NAME>`을 다음 항목으로 바꿉니다. <abbr title="모든 Azure 고객에 대해 전역적으로 고유한 이름이어야 합니다. 예를 들어 contoso-bizapp-func-20과 같이 개인 또는 조직 이름, 애플리케이션 이름 및 숫자 식별자를 조합하여 사용할 수 있습니다.">사용자에게 적합한 전역적으로 고유한 이름</abbr>. `<APP_NAME>`은 함수 앱의 기본 DNS 도메인이기도 합니다.

    <br/>
    <details>
    <summary><strong>Azure에 프로비저닝된 리소스의 비용은 얼마인가요?</strong></summary>

    이 명령은 [Azure Functions 소비 계획](functions-scale.md#overview-of-plans)에 따라 지정된 언어 런타임을 실행하는 함수 앱을 만듭니다. 여기서 발생하는 사용량에 대한 비용은 무료입니다. 또한 이 명령은 동일한 리소스 그룹에 연결된 Azure Application Insights 인스턴스를 프로비저닝하여 함수 앱을 모니터링하고 로그를 볼 수 있습니다. 자세한 내용은 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요. 인스턴스를 활성화할 때까지 비용이 발생하지 않습니다.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Azure에 함수 프로젝트 배포

Azure에서 함수 앱을 성공적으로 만들었으면 이제 [func azure functionapp publish](functions-run-local.md#project-file-deployment) 명령을 사용하여 **로컬 함수 프로젝트를 배포할** 준비가 되었습니다.

다음 예제에서 `<APP_NAME>`을 앱 이름으로 바꿉니다.

```console
func azure functionapp publish <APP_NAME>
```

`publish` 명령은 다음 출력과 비슷한 결과를 표시합니다(보기 편하게 자름).

<pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>

<br/>

---

## <a name="7-invoke-the-function-on-azure"></a>7. Azure에서 함수 호출

함수에서 HTTP 트리거를 사용하므로 브라우저 또는 다음과 같은 도구를 사용하여 URL에 HTTP 요청을 전송하여 호출합니다. <abbr title="URL에 대한 HTTP 요청을 생성하는 명령줄 도구. https://curl.se/ 참조">curl</abbr>.

# <a name="browser"></a>[브라우저](#tab/browser)

`publish` 명령의 출력에 표시된 **호출 URL** 전체를 브라우저 주소 표시줄에 복사하고 **&name=Functions** 쿼리 매개 변수를 추가합니다. 브라우저에서 함수를 로컬로 실행했을 때와 비슷한 출력이 표시됩니다.

![브라우저에서 보여 주는 Azure에서 실행되는 함수의 출력](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

**호출 URL** 을 사용하고 **&name=Functions** 매개 변수를 추가하여 [`curl`](https://curl.haxx.se/)을 실행합니다. 명령의 출력은 "Hello Functions" 텍스트여야 합니다.

![curl을 사용한 Azure에서 실행되는 함수의 출력](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7.1 실시간 스트리밍 로그 보기

다음 명령을 실행하여 Azure Portal의 Application Insights에서 거의 실시간으로 [스트리밍 로그](functions-run-local.md#enable-streaming-logs)를 확인합니다.

```console
func azure functionapp logstream <APP_NAME> --browser
```

`<APP_NAME>`은 함수 앱 이름으로 바꿉니다.

별도의 터미널 창 또는 브라우저에서 원격 함수를 다시 호출합니다. Azure의 함수 실행에 대한 자세한 로그가 터미널에 표시됩니다.

<br/>

---

## <a name="8-clean-up-resources"></a>8. 리소스 정리

[다음 단계](#next-steps)로 진행하고 <abbr title="큐에 메시지를 만들 수 있도록 스토리지 큐에 함수를 연결하는 방법. "> Azure Storage 큐 출력 바인딩</abbr>이미 완료한 항목을 기반으로 하는 경우에는 모든 리소스를 제자리에 유지합니다.

그렇지 않으면 추가 비용이 발생하지 않도록 다음 명령을 사용하여 리소스 그룹 및 포함된 모든 리소스를 삭제합니다.

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

<br/>

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Storage 큐에 연결](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[문제가 있나요? 알려주세요.](https://aka.ms/python-functions-qs-survey)
