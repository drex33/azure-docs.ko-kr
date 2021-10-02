---
title: Java 및 Gradle을 사용하여 Azure에 함수 게시
description: Java 및 Gradle을 사용하여 Azure에 HTTP 트리거 함수를 만들어 게시합니다.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: d47de5dd9af7b6d769c31d4909103616293a5a60
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361664"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Java 및 Gradle을 사용하여 함수를 만들고 Azure에 게시

이 문서에서는 Gradle 명령줄 도구를 사용하여 Java 함수 프로젝트를 만들고 Azure Functions에 게시하는 방법을 보여줍니다. 완료되면 함수 코드는 [서버리스 호스팅 계획](consumption-plan.md)의 Azure에서 실행되고 HTTP 요청에 의해 트리거됩니다. 

> [!NOTE]
> Gradle이 선호하는 개발 도구가 아닌 경우 [Maven](./create-first-function-cli-java.md), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) 및 [VS Code](./create-first-function-vs-code-java.md)를 사용하여 Java 개발자를 위한 비슷한 자습서를 확인하세요.

## <a name="prerequisites"></a>사전 요구 사항

Java를 사용하여 함수를 개발하려면 다음을 설치해야 합니다.

- [Java Developer Kit](/azure/developer/java/fundamentals/java-support-on-azure), 버전 8
- [Azure CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) 버전 2.6.666 이상
- [Gradle](https://gradle.org/), 버전 6.8 이상

활성 상태인 Azure 구독도 필요합니다. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> 이 퀵 스타트를 완료하려면 JAVA_HOME 환경 변수를 JDK 설치 위치로 설정해야 합니다.

## <a name="prepare-a-functions-project"></a>Functions 프로젝트 준비

다음 명령을 사용하여 샘플 프로젝트를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

`build.gradle`을 열고 Azure에 배포할 때 도메인 이름 충돌을 방지하기 위해 다음 섹션의 `appName`을 고유한 이름으로 변경합니다. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

텍스트 편집기에서 *src/main/java* 경로에서 새 Function.java 파일을 열고 생성된 코드를 검토합니다. 이 코드는 요청의 본문을 에코하는 [HTTP 트리거](functions-bindings-http-webhook.md) 함수입니다. 

> [!div class="nextstepaction"]
> [문제가 발생했습니다.](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=generate-project)

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

다음 명령을 실행하여 빌드한 다음, 함수 프로젝트를 실행합니다.

```bash
gradle jar --info
gradle azureFunctionsRun
```
프로젝트를 로컬로 실행할 때 Azure Functions Core Tools에서 다음과 같은 출력이 표시됩니다.

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

새 터미널 창에서 다음 cURL 명령을 사용하여 명령줄에서 함수를 트리거합니다.

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

예상 출력은 다음과 같습니다.

<pre>
Hello, AzureFunctions
</pre>

> [!NOTE]
> authLevel을 `FUNCTION` 또는 `ADMIN`로 설정하면 로컬에서 실행하는 중에는 [함수 키](functions-bindings-http-webhook-trigger.md#authorization-keys)가 필요하지 않습니다.  

터미널에서 `Ctrl+C`를 사용하여 함수 코드를 중지합니다.

> [!div class="nextstepaction"]
> [문제가 발생했습니다.](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Azure에 함수 배포

함수 앱 및 관련 리소스는 함수 앱을 처음 배포할 때 Azure에 생성됩니다. 배포하기 전에 [az login](/cli/azure/authenticate-azure-cli) Azure CLI 명령을 사용하여 Azure 구독에 로그인합니다. 

```azurecli
az login
```

> [!TIP]
> 계정에서 여러 구독에 액세스할 수 있는 경우 [az account set](/cli/azure/account#az_account_set)을 사용하여 이 세션에 대한 기본 구독을 설정합니다. 

다음 명령을 사용하여 프로젝트를 새 함수 앱에 배포합니다. 

```bash
gradle azureFunctionsDeploy
```

이렇게 하면 build.gradle 파일의 값을 기반으로 Azure에서 다음 리소스가 생성됩니다.

+ 리소스 그룹 지정한 _resourceGroup_ 을 사용하여 명명됩니다.
+ Storage 계정 함수에 필요합니다. 이름은 스토리지 계정 이름 요구 사항에 따라 임의로 생성됩니다.
+ App Service 계획 지정된 _지역의_ 함수 앱에 대한 서버리스 소비 계획 호스팅입니다. 이름은 임의로 생성됩니다.
+ 함수 앱 함수 앱은 함수에 대한 배포 및 실행 단위입니다. 이름은 임의로 생성된 번호와 함께 추가되는 _appName_ 입니다. 

또한 배포는 프로젝트 파일을 패키지하고 패키지에서 실행 모드가 활성화되어 [zip 배포](functions-deployment-technologies.md#zip-deploy)를 사용하여 새 함수 앱에 배포합니다.

샘플 프로젝트의 HTTP 트리거에 대한 authLevel은 `ANONYMOUS`로, 인증을 건너뜁니다. 그러나 `FUNCTION` 또는 `ADMIN`와 같이 다른 authLevel을 사용하는 경우 HTTP를 통해 함수 엔드포인트를 호출하는 함수 키를 가져와야 합니다. 함수 키를 가져오는 가장 쉬운 방법은 [Azure Portal]에서 가져오는 것입니다.

> [!div class="nextstepaction"]
> [문제가 발생했습니다.](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=deploy)

## <a name="get-the-http-trigger-url"></a>HTTP 트리거 URL 가져오기

Azure Portal에서 함수 키를 사용하여 함수를 트리거하는 데 필요한 URL을 가져올 수 있습니다. 

1. [Azure Portal]로 이동하여 로그인하고, 함수 앱의 _appName_ 을 페이지 맨 위에 있는 **검색** 에 입력하고 enter 키를 누릅니다.
 
1. 함수 앱에서 **함수를** 선택하고, 함수를 선택한 다음, 오른쪽 위에서 **함수 URL 받기를** 클릭합니다. 

    :::image type="content" source="./media/functions-create-first-java-gradle/get-function-url-portal.png" alt-text="Azure Portal에서 함수 URL 복사":::

1. **기본(함수 키)** 을 선택하고 **복사** 를 선택합니다. 

이제 복사된 URL을 사용하여 함수에 액세스할 수 있습니다.

## <a name="verify-the-function-in-azure"></a>Azure에서 함수 확인

`cURL`을 사용하여 Azure에서 실행되는 함수 앱을 확인하려면 아래 샘플의 URL을 포털에서 복사한 URL로 바꿉니다.

```console
curl -w "\n" http://azure-functions-sample-demo.azurewebsites.net/api/HttpExample --data AzureFunctions
```

이는 요청 본문에 `AzureFunctions`를 사용하여 POST 요청을 함수 엔드포인트로 보냅니다. 다음 응답이 표시됩니다.

<pre>
Hello, AzureFunctions
</pre>

> [!div class="nextstepaction"]
> [문제가 발생했습니다.](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=verify-deployment)

## <a name="next-steps"></a>다음 단계

HTTP 트리거 함수를 사용하여 Java 함수 프로젝트를 만들고, 로컬 머신에서 실행하고, Azure에 배포했습니다. 이제 아래 방법으로 함수를 확장하겠습니다.

> [!div class="nextstepaction"]
> [Azure Storage 큐 출력 바인딩 추가](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure Portal]: https://portal.azure.com
