---
title: 명령줄에서 Java 함수 만들기 - Azure Functions
description: 명령줄에서 Java 함수를 만든 다음, 로컬 프로젝트를 Azure Functions의 서버리스 호스팅에 게시하는 방법을 알아봅니다.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-java, devx-track-azurepowershell, mode-other
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7325ace504c6cb1e387f3d0e9bd321a3bf43dec5
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133049106"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>빠른 시작: 명령줄에서 Azure에 Java 함수 만들기

> [!div class="op_single_selector" title1="함수 언어를 선택합니다. "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

명령줄 도구를 사용하여 HTTP 요청에 응답하는 Java 함수를 만듭니다. 코드를 로컬에서 테스트한 다음, 서버리스 Azure Functions 환경에 배포합니다.

이 빠른 시작을 완료하면 Azure 계정에 미화 몇 센트 이하로 소액의 비용이 부과됩니다. <abbr title="Azure 사용량에 대한 청구 정보를 유지 관리하는 프로필.">Azure 계정</abbr>.

Maven이 선호하는 개발 도구가 아닌 경우 [Gradle](./functions-create-first-java-gradle.md), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) 및 [Visual Studio Code](create-first-function-vs-code-java.md)를 사용하여 Java 개발자를 위한 비슷한 자습서를 확인하세요.

## <a name="1-prepare-your-environment"></a>1. 환경 준비

시작하기 전에 다음이 있어야 합니다.

+ 활성 상태인 Azure 계정 <abbr title="Azure에서 리소스를 관리하는 기본 조직 구조이며 일반적으로 조직 내의 개인 또는 부서와 연결됩니다.">subscription</abbr>. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) 버전 3.x.

+ [Azure CLI](/cli/azure/install-azure-cli) 버전 2.4 이상.

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-support-on-azure) 버전 8 또는 11. `JAVA_HOME` 환경 변수는 올바른 버전의 JDK 설치 위치로 설정해야 합니다.

+ [Apache Maven](https://maven.apache.org) 버전 3.0 이상

### <a name="prerequisite-check"></a>필수 구성 요소 확인

+ 터미널 또는 명령 창에서 `func --version`을 실행하여 다음을 확인합니다. <abbr title="로컬 컴퓨터에서 Azure Functions로 작업하기 위한 명령줄 도구 집합.">Azure Functions Core Tools</abbr> 버전 3.x입니다.

+ `az --version`을 실행하여 Azure CLI 버전이 2.4 이상인지 확인합니다.

+ `az login`을 실행하여 Azure에 로그인하고 활성 구독을 확인합니다.

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2. 로컬 함수 프로젝트 만들기

Azure Functions에서 함수 프로젝트는 각각 특정 항목에 응답하는 하나 이상의 개별 함수에 대한 컨테이너입니다. <abbr title="함수의 코드를 호출하는 이벤트의 유형(예: HTTP 요청, 큐 메시지 또는 특정 시간).">트리거</abbr>. 프로젝트의 모든 함수는 동일한 로컬 및 호스팅 구성을 공유합니다. 이 섹션에서는 단일 함수가 포함된 함수 프로젝트를 만듭니다.

1. 빈 폴더에서 다음 명령을 실행하여 [Maven archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)으로부터 Functions 프로젝트를 생성합니다. 

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
    ```

    ---

    <br/>
    <details>
    <summary><strong>Java 11에서 함수를 실행하려면</strong></summary>

    Java 11에서 함수를 실행하려면 `-DjavaVersion=11`을 사용합니다. 자세히 알아보려면 [Java 버전](functions-reference-java.md#java-versions)을 참조하세요.
    </details>

1. Maven은 배포 시 프로젝트 생성 완료를 위해 필요한 값을 요청합니다.
    메시지가 표시되면 다음 값을 제공합니다.

    | prompt | 값 | Description |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Java에 대한 [패키지 명명 규칙](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7)에 따라 모든 프로젝트에서 프로젝트를 고유하게 식별하는 값입니다. |
    | **artifactId** | `fabrikam-functions` | 버전 번호가 없는 jar의 이름인 값입니다. |
    | **version** | `1.0-SNAPSHOT` | 기본값을 선택합니다. |
    | **package** | `com.fabrikam` | 생성된 함수 코드에 대한 Java 패키지인 값입니다. 기본값을 사용하세요. |

1. `Y`를 입력하거나 Enter 키를 눌러 확인합니다.

    Maven은 이름이 _artifactId_ 인 새 폴더에 프로젝트 파일을 만드는데, 이 예제에서는 `fabrikam-functions`입니다. 

1. 프로젝트 폴더로 이동합니다.

    ```console
    cd fabrikam-functions
    ```

<br/>
<details>
<summary><strong>LocalFunctionProj 폴더에 생성되는 항목은 무엇인가요?</strong></summary>

이 폴더에는 *Function.java*, *FunctionTest.java* 및 *pom.xml* 같은 프로젝트에 대한 다양한 파일이 포함되어 있습니다. [local.settings.json](functions-develop-local.md#local-settings-file) 및 [host.json](functions-host-json.md)이라는 구성 파일도 있습니다. *local.settings.json* 은 Azure에서 다운로드한 비밀을 포함할 수 있으므로 이 파일은 기본적으로 *.gitignore* 파일의 원본 제어에서 제외됩니다.
</details>

<br/>
<details>
<summary><strong>Function.java에 대한 코드</strong></summary>

*Function.java* 에는 `request` 변수에서 요청 데이터를 수신하는 `run` 메서드가 포함되며, 트리거 동작을 정의하는 [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) 주석으로 데코레이트된 [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage)입니다. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

응답 메시지는 [HttpResponseMessage](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API에서 생성됩니다.

원형 역시 함수의 단위 테스트를 생성합니다. 바인딩을 추가하거나 프로젝트에 새 함수를 추가하도록 함수를 변경하는 경우에는 *FunctionTest.java* 파일에서 테스트도 수정해야 합니다.
</details>

<br/>
<details>
<summary><strong>pom.xml에 대한 코드</strong></summary>

앱을 호스트하기 위해 만든 Azure 리소스의 설정은 생성된 *pom.xml* 파일에 있는 `com.microsoft.azure`의 **groupId** 를 사용하여 플러그 인의 **configuration** 요소에 정의됩니다. 예를 들어 아래 구성 요소는 `westus`의 `java-functions-group` 리소스 그룹에 함수 앱을 만들도록 Maven 기반 배포에 지시합니다. <abbr title="리소스가 할당된 특정 Azure 데이터 센터에 대한 지리적 참조.">region</abbr>. 함수 앱 자체는 `java-functions-app-service-plan` 플랜에서 호스트되는 Windows에서 실행되며, 이 플랜은 기본적으로 서버리스 사용 플랜입니다.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

초기 배포 전에 `runtime.os`를 `windows`에서 `linux`로 변경하는 등의 방법으로 이러한 설정을 변경하면 Azure에서 리소스를 만드는 방식을 제어할 수 있습니다. Maven 플러그 인에서 지원하는 전체 설정 목록은 [구성 세부 정보](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)를 참조하세요.
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3. 로컬에서 함수 실행

1. *LocalFunctionProj* 폴더에서 로컬 Azure Functions 런타임 호스트를 시작하여 **함수를 실행** 합니다.

    ```console
    mvn clean package
    mvn azure-functions:run
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

    HttpExample이 위와 같이 표시되지 않으면 프로젝트의 루트 폴더 외부에서 호스트를 시작했을 가능성이 높습니다. 이 경우 <kbd>Ctrl+C</kbd>를 사용하여 호스트를 중지하고, 프로젝트의 루트 폴더로 이동하여 이전 명령을 다시 실행합니다.

1. 이 출력에서 `HttpExample` 함수의 **URL을 브라우저에 복사** 하고 `?name=<YOUR_NAME>` 쿼리 문자열을 추가하여 전체 URL을 `http://localhost:7071/api/HttpExample?name=Functions`와 같이 만듭니다. 브라우저에서 `Hello Functions`와 같은 메시지가 표시됩니다.

    ![브라우저에서 로컬로 실행되는 함수의 결과](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    프로젝트를 시작한 터미널에도 요청 시 로그 출력이 표시됩니다.

1. 완료되면 <kbd>Ctrl+C</kbd>를 사용하고 <kbd>y</kbd>를 선택하여 함수 호스트를 중지합니다.

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4. Azure에 함수 프로젝트 배포

함수 앱 및 관련 리소스는 함수 프로젝트를 처음 배포할 때 Azure에 생성됩니다. 앱을 호스트하기 위해 만든 Azure 리소스의 설정은 *pom.xml* 파일에 정의됩니다. 이 문서에서는 기본값을 적용합니다.

<br/>
<details>
<summary><strong>Linux에서 실행되는 함수 앱을 만들려면</strong></summary>

Windows 대신 Linux에서 실행되는 함수 앱을 만들려면 *pom.xml* 파일의 `runtime.os` 요소를 `windows`에서 `linux`로 변경합니다. 사용 플랜에서 Linux를 실행할 수 있는 지역은 [여기](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)서 확인할 수 있습니다. Linux에서 실행되는 앱과 Windows에서 실행되는 앱이 동일한 리소스 그룹에 있으면 안 됩니다.
</details>

1. 배포하기 전에 Azure CLI 또는 Azure PowerShell을 사용하여 Azure 구독에 로그인합니다. 

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

1. 다음 명령을 사용하여 프로젝트를 새 함수 앱에 배포합니다.

    ```console
    mvn azure-functions:deploy
    ```

    배포에서는 프로젝트 파일을 패키징한 후 [zip 배포](functions-deployment-technologies.md#zip-deploy)를 사용하여 새 함수 앱에 배포합니다. 코드는 Azure의 배포 패키지에서 실행됩니다.

<br/>
<details>
<summary><strong>Azure에 무엇이 생성되나요?</strong></summary>

+ 리소스 그룹 이름은 _java-functions-group_ 입니다.
+ Storage 계정 함수에 필요합니다. 이름은 스토리지 계정 이름 요구 사항에 따라 임의로 생성됩니다.
+ 호스팅 계획. _westus_ 지역의 함수 앱에 대한 서버리스 호스팅입니다. 이름은 _java-functions-app-service-plan_ 입니다.
+ 함수 앱 함수 앱은 함수에 대한 배포 및 실행 단위입니다. 이름은 _artifactId_ 에 따라 임의로 생성된 이름에 임의로 생성된 번호를 붙여서 지정됩니다.
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5. Azure에서 함수 호출

함수에서 HTTP 트리거를 사용하므로 브라우저 또는 다음과 같은 도구를 사용하여 **URL에 HTTP 요청을 전송하여 호출** 합니다. <abbr title="URL에 대한 HTTP 요청을 생성하는 명령줄 도구. https://curl.se/ 참조">curl</abbr>.

# <a name="browser"></a>[브라우저](#tab/browser)

`publish` 명령의 출력에 표시된 **호출 URL** 전체를 브라우저 주소 표시줄에 복사하여 `&name=Functions` 쿼리 매개 변수를 추가합니다. 브라우저에서 함수를 로컬로 실행했을 때와 비슷한 출력이 표시됩니다.

![브라우저에서 보여 주는 Azure에서 실행되는 함수의 출력](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

**호출 URL** 을 사용하고 `&name=Functions` 매개 변수를 추가하여 [`curl`](https://curl.haxx.se/)을 실행합니다. 명령의 출력은 "Hello Functions" 텍스트여야 합니다.

![curl을 사용한 Azure에서 실행되는 함수의 출력](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6. 리소스 정리

[다음 단계](#next-steps)로 진행하고 Azure Storage를 추가하는 경우 <abbr title="Azure Storage에서 큐에 메시지를 만들 수 있도록 스토리지 큐에 함수를 연결하는 방법.">큐 출력 바인딩</abbr>이미 완료한 항목을 기반으로 하는 경우에는 모든 리소스를 제자리에 유지합니다.

그렇지 않으면 추가 비용이 발생하지 않도록 다음 명령을 사용하여 리소스 그룹 및 포함된 모든 리소스를 삭제합니다.

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

<br>
<hr/>

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Storage 큐에 연결](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
