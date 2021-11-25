---
title: 명령줄에서 Java 함수 만들기 - Azure Functions
description: 명령줄에서 Java 함수를 만든 다음, 로컬 프로젝트를 Azure Functions의 서버리스 호스팅에 게시하는 방법을 알아봅니다.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-java, devx-track-azurecli, devx-track-azurepowershell, mode-other
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-java-uiex
ms.openlocfilehash: 0bc6a1b17406e5ca557ff9c2e9c7bd47f3e7bf09
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133047172"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>빠른 시작: 명령줄에서 Azure에 Java 함수 만들기

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

이 문서에서는 명령줄 도구를 사용하여 HTTP 요청에 응답하는 Java 함수를 만듭니다. 코드를 로컬로 테스트한 후 서버리스 Azure Functions 환경에 배포합니다.

Maven이 선호하는 개발 도구가 아닌 경우 Java 개발자를 위한 유사한 자습서를 확인하세요.
+ [Gradle](./functions-create-first-java-gradle.md)
+ [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions)
+ [Visual Studio Code](create-first-function-vs-code-java.md)

이 빠른 시작을 완료하면 Azure 계정에서 약간의 비용(몇 USD 센트)이 발생합니다.

## <a name="configure-your-local-environment"></a>로컬 환경 구성

시작하기 전에 다음이 있어야 합니다.

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) 버전 4.x

+ [Azure CLI](/cli/azure/install-azure-cli) 버전 2.4 이상.

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-support-on-azure) 버전 8 또는 11. `JAVA_HOME` 환경 변수는 올바른 버전의 JDK 설치 위치로 설정해야 합니다.     

+ [Apache Maven](https://maven.apache.org) 버전 3.0 이상

### <a name="prerequisite-check"></a>필수 구성 요소 확인

+ 터미널 또는 명령 창에서 `func --version`을 실행하여 Azure Functions Core Tools가 4.x인지 확인합니다.

+ `az --version`을 실행하여 Azure CLI 버전이 2.4 이상인지 확인합니다.

+ `az login`을 실행하여 Azure에 로그인하고 활성 구독을 확인합니다.

## <a name="create-a-local-function-project"></a>로컬 함수 프로젝트 만들기

Azure Functions에서 함수 프로젝트는 각각 특정 트리거에 응답하는 하나 이상의 개별 함수에 대한 컨테이너입니다. 프로젝트의 모든 함수는 동일한 로컬 및 호스팅 구성을 공유합니다. 이 섹션에서는 단일 함수가 포함된 함수 프로젝트를 만듭니다.

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

    > [!IMPORTANT]
    > + Java 11에서 함수를 실행하려면 `-DjavaVersion=11`을 사용합니다. 자세히 알아보려면 [Java 버전](functions-reference-java.md#java-versions)을 참조하세요. 
    > + 이 문서를 완료하려면 `JAVA_HOME` 환경 변수를 올바른 버전의 JDK 설치 위치로 설정해야 합니다.

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

    이 폴더에는 [local.settings.json](functions-develop-local.md#local-settings-file) 및 [host.json](functions-host-json.md)이라는 구성 파일을 포함하여 프로젝트의 다양한 파일이 있습니다. *local.settings.json* 은 Azure에서 다운로드한 비밀을 포함할 수 있으므로 이 파일은 기본적으로 *.gitignore* 파일의 원본 제어에서 제외됩니다.

### <a name="optional-examine-the-file-contents"></a>(선택 사항) 파일 내용 검사

원하는 경우 [로컬에서 함수 실행](#run-the-function-locally)으로 건너뛰고, 나중에 파일 내용을 검사할 수 있습니다.

#### <a name="functionjava"></a>Function.java
*Function.java* 에는 `request` 변수에서 요청 데이터를 수신하는 `run` 메서드가 포함되며, 트리거 동작을 정의하는 [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) 주석으로 데코레이트된 [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage)입니다. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

응답 메시지는 [HttpResponseMessage](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) API에서 생성됩니다.

#### <a name="pomxml"></a>pom.xml

앱을 호스트하기 위해 만든 Azure 리소스의 설정은 생성된 pom.xml 파일에 있는 `com.microsoft.azure`의 **groupId** 를 사용하여 플러그 인의 **configuration** 요소에 정의됩니다. 예를 들어 아래 구성 요소는 `westus` 지역의 `java-functions-group` 리소스 그룹에 함수 앱을 만들도록 Maven 기반 배포에 지시합니다. 함수 앱 자체는 `java-functions-app-service-plan` 플랜에서 호스트되는 Windows에서 실행되며, 이 플랜은 기본적으로 서버리스 사용 플랜입니다.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

초기 배포 전에 `runtime.os`를 `windows`에서 `linux`로 변경하는 등의 방법으로 이러한 설정을 변경하면 Azure에서 리소스를 만드는 방식을 제어할 수 있습니다. Maven 플러그 인에서 지원하는 전체 설정 목록은 [구성 세부 정보](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)를 참조하세요.

#### <a name="functiontestjava"></a>FunctionTest.java

원형 역시 함수의 단위 테스트를 생성합니다. 바인딩을 추가하거나 프로젝트에 새 함수를 추가하도록 함수를 변경하는 경우에는 *FunctionTest.java* 파일에서 테스트도 수정해야 합니다.

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

1. *LocalFunctionProj* 폴더에서 로컬 Azure Functions 런타임 호스트를 시작하여 함수를 실행합니다.

    ```console
    mvn clean package
    mvn azure-functions:run
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
    
    > [!NOTE]  
    > HttpExample이 위와 같이 표시되지 않으면 프로젝트의 루트 폴더 외부에서 호스트를 시작했을 가능성이 높습니다. 이 경우 **Ctrl**+**C** 를 사용하여 호스트를 중지하고, 프로젝트의 루트 폴더로 이동하여 이전 명령을 다시 실행합니다.

1. 이 출력에서 `HttpExample` 함수의 URL을 브라우저로 복사하고 `?name=<YOUR_NAME>` 쿼리 문자열을 추가하여 전체 URL을 `http://localhost:7071/api/HttpExample?name=Functions`와 같이 만듭니다. 브라우저는 쿼리 문자열 값을 다시 에코하는 메시지를 표시해야 합니다. 프로젝트를 시작한 터미널에도 요청 시 로그 출력이 표시됩니다.

1. 완료되면 **Ctrl**+**C** 를 사용하고 `y`를 선택하여 함수 호스트를 중지합니다.

## <a name="deploy-the-function-project-to-azure"></a>Azure에 함수 프로젝트 배포

함수 앱 및 관련 리소스는 함수 프로젝트를 처음 배포할 때 Azure에 생성됩니다. 앱을 호스트하기 위해 만든 Azure 리소스의 설정은 [pom.xml 파일](#pomxml)에 정의됩니다. 이 문서에서는 기본값을 적용합니다.

> [!TIP]
> Windows 대신 Linux에서 실행되는 함수 앱을 만들려면 pom.xml 파일의 `runtime.os` 요소를 `windows`에서 `linux`로 변경합니다. 사용 플랜에서 Linux를 실행할 수 있는 지역은 [여기](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions)서 확인할 수 있습니다. Linux에서 실행되는 앱과 Windows에서 실행되는 앱이 동일한 리소스 그룹에 있으면 안 됩니다.

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
    
    그러면 Azure에서 다음 리소스가 생성됩니다.
    
    + 리소스 그룹 이름은 _java-functions-group_ 입니다.
    + Storage 계정 함수에 필요합니다. 이름은 스토리지 계정 이름 요구 사항에 따라 임의로 생성됩니다.
    + 호스팅 계획. _westus_ 지역의 함수 앱에 대한 서버리스 호스팅입니다. 이름은 _java-functions-app-service-plan_ 입니다.
    + 함수 앱 함수 앱은 함수에 대한 배포 및 실행 단위입니다. 이름은 _artifactId_ 에 따라 임의로 생성된 이름에 임의로 생성된 번호를 붙여서 지정됩니다.
    
    배포에서는 프로젝트 파일을 패키징한 후 [zip 배포](functions-deployment-technologies.md#zip-deploy)를 사용하여 새 함수 앱에 배포합니다. 코드는 Azure의 배포 패키지에서 실행됩니다.

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

## <a name="clean-up-resources"></a>리소스 정리

[다음 단계](#next-steps)를 계속 진행하고 Azure Storage 큐 출력 바인딩을 추가하는 경우 이미 수행한 작업을 기반으로 하여 빌드되는 모든 리소스를 그대로 유지합니다.

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

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Storage 큐에 연결](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
