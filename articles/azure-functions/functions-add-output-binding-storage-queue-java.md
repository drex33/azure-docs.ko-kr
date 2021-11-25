---
title: Java 함수를 Azure Storage에 연결
description: Queue storage 출력 바인딩을 사용하여 Azure Storage에 HTTP 트리거 Java 함수를 연결하는 방법에 대해 알아봅니다.
author: KarlErickson
ms.custom: devx-track-java, mode-other
ms.author: karler
ms.date: 10/14/2019
ms.topic: quickstart
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: 7aaa116aadcc7a72ac5c497aa98ed501323fb42a
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133076194"
---
# <a name="connect-your-java-function-to-azure-storage"></a>Java 함수를 Azure Storage에 연결

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

[이전의 빠른 시작 문서](./create-first-function-cli-java.md?tabs=bash,browser)에서 Azure Storage 큐를 사용하여 만든 함수를 통합하는 방법을 보여줍니다. 이 함수에 추가하는 출력 바인딩은 HTTP 요청의 데이터를 큐의 메시지에 씁니다.

대부분의 바인딩은 Functions에서 바인딩된 서비스에 액세스할 때 사용할 저장된 연결 문자열이 필요합니다. 이 연결을 더 쉽게 만들려면 함수 앱에서 만든 Storage 계정을 사용합니다. 이 계정에 대한 연결은 이미 `AzureWebJobsStorage` 앱 설정에 저장되어 있습니다.  

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 시작하기에 앞서 [Java 빠른 시작의 1부](./create-first-function-cli-java.md?tabs=bash,browser) 단계를 먼저 완료합니다.

## <a name="download-the-function-app-settings"></a>함수 앱 설정 다운로드

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>확장 번들 사용

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

이제 Storage 출력 바인딩을 프로젝트에 추가할 수 있습니다.

## <a name="add-an-output-binding"></a>출력 바인딩 추가

[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]

## <a name="add-code-that-uses-the-output-binding"></a>출력 바인딩을 사용하는 코드 추가

[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]

이제 새 출력 바인딩을 로컬로 사용해 볼 준비가 되었습니다.

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

이전과 마찬가지로 다음 명령을 사용하여 프로젝트를 빌드하고 로컬로 Functions 런타임을 시작합니다.

# <a name="maven"></a>[Maven](#tab/maven)
```bash
mvn clean package 
mvn azure-functions:run
```
# <a name="gradle"></a>[Gradle](#tab/gradle) 
```bash
gradle jar --info
gradle azureFunctionsRun
```
---

> [!NOTE]  
> host.json에서 확장 번들을 사용하도록 설정했으므로 시작 시 [Storage 바인딩 확장](functions-bindings-storage-blob.md#add-to-your-functions-app)이 다른 Microsoft 바인딩 확장과 함께 자동으로 다운로드되어 설치되었습니다.

이전과 마찬가지로 새 터미널 창에서 cURL을 사용하여 명령줄에서 함수를 트리거합니다.

```CMD
curl -w "\n" http://localhost:7071/api/HttpTrigger-Java --data AzureFunctions
```

이번에는 출력 바인딩이 Storage 계정에 `outqueue`라는 큐를 만들고 똑같은 이 문자열을 사용하여 메시지를 추가합니다.

다음으로, Azure CLI를 사용하여 새 큐를 살펴보고 메시지가 추가되었는 확인합니다. 큐는 [Microsoft Azure Storage Explorer][Azure Storage Explorer] 또는 [Azure Portal](https://portal.azure.com)을 사용하여 확인할 수도 있습니다.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>프로젝트 다시 배포 

게시된 앱을 업데이트하려면 다음 명령을 다시 실행합니다.  

# <a name="maven"></a>[Maven](#tab/maven)  
```bash
mvn azure-functions:deploy
```
# <a name="gradle"></a>[Gradle](#tab/gradle)  
```bash
gradle azureFunctionsDeploy
```
---

마찬가지로 cURL을 사용하여 배포된 함수를 테스트할 수 있습니다. 이전과 마찬가지로 다음 예제와 같이 POST 요청의 본문에 있는 `AzureFunctions` 값을 URL에 전달합니다.

```bash
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpTrigger-Java?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

[Storage 큐 메시지를 검사](#query-the-storage-queue)하여 출력 바인딩이 예상대로 큐에 새 메시지를 생성하는지 다시 확인할 수 있습니다.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>다음 단계

데이터를 Storage 큐에 쓰도록 HTTP 트리거 함수를 업데이트했습니다. Java를 사용하여 Azure Functions를 개발하는 방법에 대한 자세한 내용은 [Azure Functions Java 개발자 가이드](functions-reference-java.md) 및 [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)을 참조하세요. Java의 전체 함수 프로젝트 예제는 [Java Functions 샘플](/samples/browse/?products=azure-functions&languages=Java)을 참조하세요. 

다음으로, 함수 앱에 Application Insights 모니터링을 사용하도록 설정해야 합니다.

> [!div class="nextstepaction"]
> [Application Insights 통합 사용](configure-monitoring.md#add-to-an-existing-function-app)


[Azure Storage Explorer]: https://storageexplorer.com/
