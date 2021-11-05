---
title: 명령줄에서 TypeScript 함수 만들기 - Azure Functions
description: 명령줄에서 TypeScript 함수를 만든 다음, 로컬 프로젝트를 Azure Functions의 서버리스 호스팅에 게시하는 방법을 알아봅니다.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b015f74b0477cbd46ae56eb1e452c462e1cd9a13
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039395"
---
# <a name="quickstart-create-a-typescript-function-in-azure-from-the-command-line"></a>빠른 시작: 명령줄에서 Azure에 TypeScript 함수 만들기

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

이 문서에서는 명령줄 도구를 사용하여 HTTP 요청에 응답하는 TypeScript 함수를 만듭니다. 코드를 로컬로 테스트한 후 서버리스 Azure Functions 환경에 배포합니다.

이 빠른 시작을 완료하면 Azure 계정에서 약간의 비용(몇 USD 센트)이 발생합니다.

또한 이 문서의 [Visual Studio Code 기반 버전](create-first-function-vs-code-typescript.md)도 있습니다.

## <a name="configure-your-local-environment"></a>로컬 환경 구성

시작하기 전에 다음이 있어야 합니다.

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) 버전 3.x.

+ 다음 도구 중 하나를 통해 Azure 리소스를 만듭니다.

    + [Azure CLI](/cli/azure/install-azure-cli) 버전 2.4 이상.

    + [Az PowerShell 모듈](/powershell/azure/install-az-ps), 버전 5.9.0 이상.

+ [Node.js](https://nodejs.org/), 활성 LTS 및 유지 관리 LTS 버전(8.11.1 및 10.14.1 권장).

### <a name="prerequisite-check"></a>필수 구성 요소 확인

Azure 리소스를 만드는 데 Azure CLI 또는 Azure PowerShell을 사용하는지 여부에 따라 필수 구성 요소를 확인합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ 터미널 또는 명령 창에서 `func --version`을 실행하여 Azure Functions Core Tools가 3.x인지 확인합니다.

+ `az --version`을 실행하여 Azure CLI 버전이 2.4 이상인지 확인합니다.

+ `az login`을 실행하여 Azure에 로그인하고 활성 구독을 확인합니다.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ 터미널 또는 명령 창에서 `func --version`을 실행하여 Azure Functions Core Tools가 3.x인지 확인합니다.

+ `(Get-Module -ListAvailable Az).Version`을 실행하고 버전 5.0 이상을 확인합니다.

+ `Connect-AzAccount`을 실행하여 Azure에 로그인하고 활성 구독을 확인합니다.

---

## <a name="create-a-local-function-project"></a>로컬 함수 프로젝트 만들기

Azure Functions에서 함수 프로젝트는 각각 특정 트리거에 응답하는 하나 이상의 개별 함수에 대한 컨테이너입니다. 프로젝트의 모든 함수는 동일한 로컬 및 호스팅 구성을 공유합니다. 이 섹션에서는 단일 함수가 포함된 함수 프로젝트를 만듭니다.

1. 다음과 같이 `func init` 명령을 실행하여 지정된 런타임에 *LocalFunctionProj* 폴더에 함수 프로젝트를 만듭니다.

    ```console
    func init LocalFunctionProj --typescript
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

#### <a name="indexts"></a>index.ts

*index.ts* 는 *function.json* 의 구성에 따라 트리거되는 함수를 내보냅니다.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

HTTP 트리거의 경우 함수는 *function.json* 에 정의된 **HttpRequest** 유형의 `req` 변수에 요청 데이터를 받습니다. *function.json* 에 `$return`으로 정의된 반환 개체는 응답입니다.

#### <a name="functionjson"></a>function.json

*function.json* 은 트리거 형식을 포함하여 함수의 입력 및 출력 `bindings`를 정의하는 구성 파일입니다.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::

각 바인딩에는 명령, 형식 및 고유한 이름이 필요합니다. HTTP 트리거의 입력 바인딩은 [`httpTrigger`](functions-bindings-http-webhook-trigger.md) 형식이고, 출력 바인딩은 [`http`](functions-bindings-http-webhook-output.md) 형식입니다.

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

1. *LocalFunctionProj* 폴더에서 로컬 Azure Functions 런타임 호스트를 시작하여 함수를 실행합니다.

    ```console
    npm install
    npm start
    ```

    출력의 끝 부분에 다음 줄이 표시됩니다.

    <pre>
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...

    </pre>

    >[!NOTE]
    > HttpExample이 아래와 같이 표시되지 않으면 프로젝트의 루트 폴더 외부에서 호스트를 시작했을 가능성이 높습니다. 이 경우 **Ctrl**+**C** 를 사용하여 호스트를 중지하고, 프로젝트의 루트 폴더로 이동하여 이전 명령을 다시 실행합니다.

1. 이 출력에서 `HttpExample` 함수의 URL을 브라우저로 복사하고 `?name=<your-name>` 쿼리 문자열을 추가하여 전체 URL을 `http://localhost:7071/api/HttpExample?name=Functions`와 같이 만듭니다. 브라우저에서 `Hello Functions`와 같은 메시지가 표시됩니다.

    ![브라우저에서 로컬로 실행되는 함수의 결과](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    프로젝트를 시작한 터미널에도 요청 시 로그 출력이 표시됩니다.

1. 준비가 되면 **Ctrl**+**C** 를 사용하고 `y`를 선택하여 함수 호스트를 중지합니다.

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Azure에서 함수 앱을 만듭니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location <REGION> --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 명령은 Azure에서 함수 앱을 만듭니다. Node.js 10을 사용하는 경우 `--runtime-version`도 `10`으로 변경합니다.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location '<REGION>'
    ```

    [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) cmdlet은 Azure에서 함수 앱을 만듭니다. Node.js 10을 사용하는 경우 `-RuntimeVersion`을 `10`으로 변경합니다.

    ---

    이전 예제에서는 `<STORAGE_NAME>`을 이전 단계에서 사용한 계정의 이름으로 바꾸고, `<APP_NAME>`을 적절하고 전역적으로 고유한 이름으로 바꿉니다. `<APP_NAME>`은 함수 앱의 기본 DNS 도메인이기도 합니다.

    이 명령은 [Azure Functions 소비 계획](consumption-plan.md)에 따라 지정된 언어 런타임을 실행하는 함수 앱을 만듭니다. 여기서 발생하는 사용량에 대한 비용은 무료입니다. 또한 이 명령은 동일한 리소스 그룹에 연결된 Azure Application Insights 인스턴스를 프로비저닝하여 함수 앱을 모니터링하고 로그를 볼 수 있습니다. 자세한 내용은 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요. 인스턴스를 활성화할 때까지 비용이 발생하지 않습니다.

## <a name="deploy-the-function-project-to-azure"></a>Azure에 함수 프로젝트 배포

Core Tools를 사용하여 Azure에 프로젝트를 배포하기 전에 TypeScript 소스 파일에서 프로덕션이 준비된 JavaScript 파일 빌드를 만듭니다.

1. 다음 명령을 사용하여 배포할 TypeScript 프로젝트를 준비합니다.

    ```console
    npm run build:production
    ```

1. 이제 필요한 리소스가 있으면 [func azure functionapp publish](functions-run-local.md#project-file-deployment) 명령을 사용하여 로컬 함수 프로젝트를 Azure의 함수 앱에 배포할 수 있습니다. 다음 예제에서 `<APP_NAME>`을 앱 이름으로 바꿉니다.

    ```console
    func azure functionapp publish <APP_NAME>
    ```

    "... 이름의 앱을 찾을 수 없습니다."라는 오류가 표시되면 Azure에서 이전 `az functionapp create` 명령 후에 앱을 완전히 초기화하지 않았을 수 있으므로 몇 초 정도 기다렸다가 다시 시도하세요.

    publish 명령은 다음 출력과 비슷한 결과를 표시합니다(간단히 하기 위해 잘랐음).

    <pre>
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
            Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
    </pre>

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Storage 큐에 연결](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-typescript)
