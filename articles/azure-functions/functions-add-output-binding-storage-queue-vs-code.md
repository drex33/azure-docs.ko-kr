---
title: Visual Studio Code를 사용하여 Azure Storage에 Azure Functions 연결
description: Visual Studio Code 프로젝트에 출력 바인딩을 추가하여 Azure Functions를 Azure Queue Storage에 연결하는 방법을 알아봅니다.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-js, mode-other
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 2fd924e5b69b5160cbb2bf13d171441a13264727
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133049241"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Visual Studio Code를 사용하여 Azure Storage에 Azure Functions 연결

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

이 문서에서는 Visual Studio Code를 사용하여 이전의 빠른 시작 문서에서 만든 함수에 Azure Storage를 연결하는 방법을 보여줍니다. 이 함수에 추가되는 출력 바인딩은 HTTP 요청의 데이터를 Azure Queue storage 큐의 메시지에 씁니다. 

대부분의 바인딩은 Functions에서 바인딩된 서비스에 액세스할 때 사용할 저장된 연결 문자열이 필요합니다. 이 부분을 간편하게 해결하려면 함수 앱으로 만든 Storage 계정을 사용합니다. 이 계정에 대한 연결은 이미 `AzureWebJobsStorage` 앱 설정에 저장되어 있습니다.  

## <a name="configure-your-local-environment"></a>로컬 환경 구성

이 문서를 시작하기 전에 다음 요구 사항을 충족해야 합니다.

* [Visual Studio Code용 Azure Storage 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)을 설치합니다.

* [Azure Storage Explorer](https://storageexplorer.com/)를 설치합니다. Storage Explorer는 출력 바인딩에서 생성하는 큐 메시지를 검토하는 데 사용하는 도구입니다. Storage Explorer는 macOS, Windows, Linux 기반 운영 체제에서 지원됩니다.

::: zone pivot="programming-language-csharp"
* [.NET Core CLI 도구](/dotnet/core/tools/?tabs=netcore2x)를 설치합니다.
::: zone-end

::: zone pivot="programming-language-csharp"  
* [Visual Studio Code 빠른 시작의 1부](create-first-function-vs-code-csharp.md)의 단계를 완료합니다. 
::: zone-end  
::: zone pivot="programming-language-javascript"  
* [Visual Studio Code 빠른 시작의 1부](create-first-function-vs-code-node.md)의 단계를 완료합니다. 
::: zone-end   
::: zone pivot="programming-language-java"  
* [Visual Studio Code 빠른 시작의 1부](create-first-function-vs-code-java.md)의 단계를 완료합니다. 
::: zone-end   
::: zone pivot="programming-language-typescript"  
* [Visual Studio Code 빠른 시작의 1부](create-first-function-vs-code-typescript.md)의 단계를 완료합니다. 
::: zone-end   
::: zone pivot="programming-language-python"  
* [Visual Studio Code 빠른 시작의 1부](create-first-function-vs-code-python.md)의 단계를 완료합니다. 
::: zone-end   
::: zone pivot="programming-language-powershell"  
* [Visual Studio Code 빠른 시작의 1부](create-first-function-vs-code-powershell.md)의 단계를 완료합니다. 
::: zone-end   

이 문서에서는 Visual Studio Code에서 Azure 구독에 이미 로그인했다고 가정합니다. 명령 팔레트에서 `Azure: Sign In`을 실행하여 로그인할 수 있습니다. 

## <a name="download-the-function-app-settings"></a>함수 앱 설정 다운로드

[이전의 빠른 시작 문서](./create-first-function-vs-code-csharp.md)에서는 Azure에서 필요한 스토리지 계정과 함께 함수 앱을 만들었습니다. 이 계정의 연결 문자열은 Azure의 앱 설정에 안전하게 저장됩니다. 이 문서에서는 같은 계정의 Storage 큐에 메시지를 작성합니다. 함수를 로컬로 실행할 때 Storage 계정에 연결하려면 앱 설정을 local.settings.json 파일에 다운로드해야 합니다. 

1. F1 키를 눌러 명령 팔레트를 연 다음, `Azure Functions: Download Remote Settings....` 명령을 검색하고 실행합니다. 

1. 이전 문서에서 만든 함수 앱을 선택합니다. **모두 예** 를 선택하여 기존 로컬 설정을 덮어씁니다. 

    > [!IMPORTANT]  
    > local.settings.json 파일은 비밀을 포함하고 있으므로 절대 게시되지 않으며, 소스 제어에서 제외됩니다.

1. 스토리지 계정 연결 문자열 값에 대한 키인 `AzureWebJobsStorage` 값을 복사합니다. 이 연결을 사용하여 출력 바인딩이 예상대로 작동하는지 확인합니다.

## <a name="register-binding-extensions"></a>바인딩 확장 등록

Queue storage 출력 바인딩을 사용하므로 프로젝트를 실행하기 전에 스토리지 바인딩 확장을 설치해야 합니다. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

프로젝트가 미리 정의된 확장 패키지 세트를 자동으로 설치하는 [확장 번들](functions-bindings-register.md#extension-bundles)을 사용하도록 구성되었습니다. 

확장 번들 사용은 프로젝트의 루트에 있는 host.json 파일에서 사용하도록 설정되며, 다음과 같이 표시됩니다.

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

::: zone-end

이제 프로젝트에 스토리지 출력 바인딩을 추가할 수 있습니다.

## <a name="add-an-output-binding"></a>출력 바인딩 추가

Functions에서 각 바인딩 형식의 `direction`, `type` 및 고유한 `name`을 function.json 파일에 정의해야 합니다. 이러한 특성을 정의하는 방법은 함수 앱의 언어에 따라 달라집니다.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [functions-add-output-binding-java](../../includes/functions-add-output-binding-java.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>출력 바인딩을 사용하는 코드 추가

바인딩이 정의되면 바인딩의 `name`을 사용하여 함수 시그니처의 특성으로 액세스할 수 있습니다. 출력 바인딩을 사용하면 인증을 받거나 큐 참조를 가져오거나 데이터를 쓸 때 Azure Storage SDK 코드를 사용할 필요가 없습니다. Functions 런타임 및 큐 출력 바인딩이 이러한 작업을 알아서 처리합니다.

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"  

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

::: zone-end  

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-storage-binding-java-code](../../includes/functions-add-storage-binding-java-code.md)]

## <a name="update-the-tests"></a>테스트 업데이트

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end  

::: zone pivot="programming-language-csharp"  

[!INCLUDE [functions-run-function-test-local-vs-code-csharp](../../includes/functions-run-function-test-local-vs-code-csharp.md)]

::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"  
## <a name="run-the-function-locally"></a>로컬에서 함수 실행

1. 이전 문서에서와 같이 <kbd>F5</kbd>를 눌러 함수 앱 프로젝트와 Core Tools를 시작합니다. 

1. Core Tools가 실행 중인 상태에서 **Azure: Functions** 영역으로 이동합니다. **Functions** 에서 **로컬 프로젝트** > **Functions** 를 확장합니다. `HttpExample` 함수를 마우스 오른쪽 단추로 클릭(Mac에서는 Ctrl 클릭)하고 **지금 함수 실행...** 을 선택합니다.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Visual Studio Code에서 지금 함수 실행":::

1. **요청 본문 입력** 에서 `{ "name": "Azure" }`의 요청 메시지 본문 값이 표시됩니다. Enter를 눌러 이 요청 메시지를 함수로 보냅니다.  
 
1. 응답이 반환된 후 <kbd>Ctrl + C</kbd>를 눌러 Core Tools를 중지합니다.

스토리지 연결 문자열을 사용하므로 함수는 로컬로 실행될 때 Azure 스토리지 계정에 연결됩니다. **outqueue** 라는 새 큐는 출력 바인딩이 처음 사용될 때 함수 런타임에 의해 스토리지 계정에 만들어집니다. Storage Explorer를 사용하여 새 메시지와 함께 큐가 만들어졌는지 확인합니다.

::: zone-end

### <a name="connect-storage-explorer-to-your-account"></a>Storage Explorer를 계정에 연결

Azure Storage Explorer를 이미 설치했고 Azure 계정에 연결한 경우 이 섹션을 건너뜁니다.

1. [Azure Storage Explorer](https://storageexplorer.com/) 도구를 실행하여 왼쪽에 있는 연결 아이콘을 선택한 다음, **계정 추가** 를 선택합니다.

    ![Microsoft Azure Storage Explorer에 Azure 계정 추가](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. **연결** 대화 상자에서 **Azure 계정 추가** 를 선택한 다음, **Azure 환경** 과 **로그인...** 을 차례대로 선택합니다. 

    ![Azure 계정에 로그인](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

계정에 성공적으로 로그인하면 계정에 연결된 모든 Azure 구독이 표시됩니다.

### <a name="examine-the-output-queue"></a>출력 큐 검토

1. Visual Studio Code에서 F1 키를 눌러 명령 팔레트를 연 다음, `Azure Storage: Open in Storage Explorer` 명령을 검색하여 실행하고 스토리지 계정 이름을 선택합니다. 스토리지 계정이 Azure Storage Explorer에서 열립니다.  

1. **큐** 노드를 확장한 다음 이름이 **outqueue** 인 큐를 선택합니다. 

   이 큐에는 HTTP 트리거 함수를 실행했을 때 만들어진 큐 출력 바인딩 메시지가 포함되어 있습니다. 기본 `name` 값 Azure로 함수를 호출했다면 큐 메시지는 ‘함수에 전달된 이름:  Azure’입니다.

    ![Azure Storage Explorer에 표시되는 큐 메시지](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. 함수를 다시 실행한 다음, 다른 요청을 보내면 큐에 새 메시지가 표시되는 것을 알 수 있습니다.  

이제 업데이트된 함수 앱을 Azure에 다시 게시할 차례입니다.

## <a name="redeploy-and-verify-the-updated-app"></a>업데이트된 앱 다시 배포 및 확인

1. Visual Studio Code에서 F1 키를 눌러 명령 팔레트를 엽니다. 명령 팔레트에서 `Azure Functions: Deploy to function app...`을 검색하여 선택합니다.

1. 처음 문서에서 만든 함수 앱을 선택합니다. 동일한 앱에 프로젝트를 다시 배포하므로 **배포** 를 선택하여 파일 덮어쓰기에 대한 경고를 해제합니다.

1. 배포가 완료되면 **지금 함수 실행...** 기능을 사용하여 Azure에서 함수를 트리거할 수 있습니다.

1. 다시 [스토리지 큐의 메시지를 보고](#examine-the-output-queue) 출력 바인딩이 큐에 새 메시지를 다시 생성하는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure에서 *리소스* 는 함수 앱, 함수, 스토리지 계정 등을 참조합니다. 리소스는 *리소스 그룹* 으로 그룹화되며 그룹을 삭제하면 그룹의 모든 항목을 삭제할 수 있습니다.

이러한 빠른 시작을 완료하기 위해 리소스를 만들었습니다. [계정 상태](https://azure.microsoft.com/account/) 및 [서비스 가격 책정](https://azure.microsoft.com/pricing/)에 따라 리소스에 대해 요금이 청구될 수 있습니다. 리소스가 더 이상 필요하지 않게 되면 다음과 같이 삭제합니다.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>다음 단계

Storage 큐에 데이터를 쓰도록 HTTP 트리거 함수를 업데이트했습니다. 이제 Visual Studio Code를 사용하여 함수 개발에 대해 자세히 알아봅니다.

+ [Visual Studio Code를 사용하여 Azure Functions 개발](functions-develop-vs-code.md)

+ [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
::: zone pivot="programming-language-csharp"  
+ [C# 전체 함수 프로젝트에 대한 예제](/samples/browse/?products=azure-functions&languages=csharp)

+ [Azure Functions C# 개발자 참조](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [JavaScript 전체 함수 프로젝트에 대한 예제](/samples/browse/?products=azure-functions&languages=javascript)

+ [Azure Functions JavaScript 개발자 가이드](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Java의 전체 함수 프로젝트에 대한 예제](/samples/browse/?products=azure-functions&languages=java).

+ [Azure Functions Java 개발자 가이드](functions-reference-java.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [TypeScript 전체 함수 프로젝트에 대한 예제](/samples/browse/?products=azure-functions&languages=typescript)

+ [Azure Functions TypeScript 개발자 가이드](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Python의 전체 함수 프로젝트에 대한 예제](/samples/browse/?products=azure-functions&languages=python)

+ [Azure Functions Python 개발자 가이드](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [PowerShell 전체 함수 프로젝트에 대한 예제](/samples/browse/?products=azure-functions&languages=azurepowershell)

+ [Azure Functions PowerShell 개발자 가이드](functions-reference-powershell.md) 
::: zone-end
