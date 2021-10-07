---
title: '빠른 시작: Visual Studio를 사용하여 Azure에서 첫 번째 C# 함수 만들기'
description: 이 빠른 시작에서는 Visual Studio를 사용하여 .NET Core 3.1에서 실행되는 Azure Functions에 C# HTTP 트리거 함수를 만들고 게시하는 방법에 대해 알아봅니다.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/14/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f, contperf-fy21q3-portal
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./functions-create-your-first-function-visual-studio-uiex
zone_pivot_groups: runtime-version-programming-functions
ms.openlocfilehash: fb969f494c350d253d688d3a5379c30513aa64fd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671263"
---
# <a name="quickstart-create-your-first-c-function-in-azure-using-visual-studio"></a>빠른 시작: Visual Studio를 사용하여 Azure에서 첫 번째 C# 함수 만들기

[!INCLUDE [functions-runtime-version-dotnet](../../includes/functions-runtime-version-dotnet.md)]

Azure Functions를 사용하면 Azure의 서버리스 환경에서 C# 코드를 실행할 수 있습니다. 

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Visual Studio를 사용하여 C# 클래스 라이브러리 프로젝트를 만듭니다.
> * HTTP 요청에 응답하는 함수를 만듭니다. 
> * 코드를 로컬로 실행하여 함수 동작을 확인합니다.
> * 코드 프로젝트를 Azure Functions에 배포합니다.
 
::: zone pivot="programming-runtime-functions-v3"
[!INCLUDE [functions-dotnet-execution-model](../../includes/functions-dotnet-execution-model.md)]
::: zone-end
::: zone pivot="programming-runtime-functions-v4"
> [!NOTE]
> 현재 Azure Functions 4.0는 미리 보기 상태이며 Visual Studio는 [In Process 실행 모델](functions-dotnet-class-library.md)을 사용하여 .NET 6에서만 실행되는 C# 함수 생성을 지원합니다.
::: zone-end

이 빠른 시작을 완료하면 Azure 계정에서 약간의 비용(몇 USD 센트)이 발생합니다.

또한 이 문서의 [Visual Studio Code 기반 버전](create-first-function-vs-code-csharp.md)도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

::: zone pivot="programming-runtime-functions-v3"
+ [Visual Studio 2019](https://azure.microsoft.com/downloads/). 설치하는 동안 **Azure 개발** 워크로드를 선택해야 합니다. 
::: zone-end
::: zone pivot="programming-runtime-functions-v4"
+ [Visual Studio 2022 Preview 3 이상](https://azure.microsoft.com/downloads/). 설치하는 동안 **Azure 개발** 워크로드를 선택해야 합니다. 
::: zone-end

+ [Azure 구독](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing). 아직 계정이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/dotnet/)을 만듭니다.

## <a name="create-a-function-app-project"></a>함수 앱 프로젝트 만들기

::: zone pivot="programming-runtime-functions-v3"
[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]
::: zone-end

::: zone pivot="programming-runtime-functions-v4"
Visual Studio의 Azure Functions 프로젝트 템플릿은 Azure에서 함수 앱에 게시할 수 있는 C# 클래스 라이브러리 프로젝트를 만듭니다. 함수 앱을 사용하면 함수를 논리 단위로 그룹화하여 더 쉽게 리소스를 관리, 배포, 크기 조정 및 공유할 수 있습니다.

1. Visual Studio 메뉴에서 **파일** > **새로 만들기** > **프로젝트** 를 차례로 선택합니다.

1. **새 프로젝트 만들기** 에서 검색 상자에 *함수* 를 입력하고, **Azure Functions** 템플릿을 선택한 다음, **다음** 을 선택합니다.

1. **새 프로젝트 구성** 에서 프로젝트에 대한 **프로젝트 이름** 을 입력한 다음, **만들기** 를 선택합니다. 함수 앱 이름은 C# 네임스페이스로 유효해야 하므로 밑줄, 하이픈 또는 기타 영숫자가 아닌 문자는 사용하지 마세요.

1. **새 Azure Functions 애플리케이션 만들기** 설정에 대해서는 다음 표의 값을 사용합니다.

    | 설정      | Value  | 설명                      |
    | ------------ |  ------- |----------------------------------------- |
    | **.NET 버전** | **.NET 6** | 이 값은 Azure Functions 런타임 버전 4.x에서 In-Process로 실행되는 함수 프로젝트를 만듭니다. Azure Functions 1.x는 .NET Framework를 지원합니다. 자세한 내용은 [Azure Functions 런타임 버전 개요](./functions-versions.md)를 참조하세요.   |
    | **함수 템플릿** | **HTTP 트리거** | 이 값은 HTTP 요청에 의해 트리거되는 함수를 만듭니다. |
    | **스토리지 계정(AzureWebJobsStorage)**  | **스토리지 에뮬레이터** | Azure의 함수 앱에는 스토리지 계정이 필요하기 때문에 Azure에 프로젝트를 게시할 때 할당되거나 생성됩니다. HTTP 트리거는 Azure Storage 계정 연결 문자열을 사용하지 않습니다. 다른 모든 트리거 형식에는 유효한 Azure Storage 계정 연결 문자열이 필요합니다.  |
    | **권한 부여 수준** | **익명** | 만들어진 함수를 모든 클라이언트에서 키를 제공하지 않고 트리거할 수 있습니다. 이 권한 부여 설정을 통해 새 함수를 쉽게 테스트할 수 있습니다. 키 및 권한 부여에 대한 자세한 내용은 [권한 부여 키](./functions-bindings-http-webhook-trigger.md#authorization-keys) 및 [HTTP 및 웹후크 바인딩](./functions-bindings-http-webhook.md)을 참조하세요. |
    
    :::image type="content" source="../../includes/media/functions-vs-tools-create/functions-project-settings-v4.png" alt-text="Azure Functions 프로젝트 설정":::

    **권한 부여 수준** 을 **익명** 으로 설정했는지 확인합니다. 기본 수준인 **함수** 를 선택하면 함수 엔드포인트에 액세스하도록 요구하는 요청에 [함수 키](./functions-bindings-http-webhook-trigger.md#authorization-keys)를 제공해야 합니다.

1. **만들기** 를 선택하여 함수 프로젝트 및 HTTP 트리거 함수를 만듭니다.

::: zone-end

Visual Studio는 HTTP 트리거 함수 형식에 대한 상용구 코드가 포함된 프로젝트와 클래스를 만듭니다. 상용구 코드는 요청 본문 또는 쿼리 문자열의 값을 포함하는 HTTP 응답을 보냅니다. `HttpTrigger` 특성은 HTTP 요청에서 함수를 트리거하도록 지정합니다.

## <a name="rename-the-function"></a>함수 이름 바꾸기

`FunctionName` 메서드 특성은 함수 이름을 설정하며, 생성되는 기본값은 `Function1`입니다. 프로젝트를 만들 때 도구를 사용하여 기본 함수 이름을 재정의할 수 없으므로 함수 클래스, 파일 및 메타데이터에 따라 더욱 적절한 이름을 생성해야 합니다.

1. **파일 탐색기** 에서 Function1.cs 파일을 마우스 오른쪽 단추로 클릭하고 이름을 `HttpExample.cs`로 바꿉니다.

1. 코드에서 Function1 클래스의 이름을 `HttpExample`로 바꿉니다.

1. `HttpTrigger` 메서드 `Run`에서 `FunctionName` 메서드 특성을 `HttpExample`로 바꿉니다. 

함수 정의는 이제 다음 코드와 같이 표시됩니다.

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs" range="15-18"::: 

이제 함수의 이름을 바꾸었으므로 로컬 컴퓨터에서 이를 테스트할 수 있습니다.

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

Visual Studio는 Azure Functions Core Tools와 통합되므로 Azure Functions 런타임 전체를 사용하여 함수를 로컬로 테스트할 수 있습니다.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

함수가 로컬 컴퓨터에서 제대로 실행되는지 확인한 후에 해당 프로젝트를 Azure에 게시해야 합니다.

## <a name="publish-the-project-to-azure"></a>Azure에 프로젝트 게시

프로젝트를 게시하려면 먼저 Azure 구독에 함수 앱이 있어야 합니다. 프로젝트를 처음 게시할 때 Visual Studio 게시에서 함수 앱을 만듭니다.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="verify-your-function-in-azure"></a>Azure에서 함수 확인

1. 클라우드 탐색기에서 새 함수 앱을 선택해야 합니다. 그렇지 않은 경우 구독 > **App Services** 를 확장하고 새 함수 앱을 선택합니다.

1. 마우스 오른쪽 단추로 함수 앱을 클릭하고 **브라우저에서 열기** 를 선택합니다. 그러면 기본 웹 브라우저에서 함수 앱의 루트가 열리고 함수 앱이 실행 중임을 나타내는 페이지가 표시됩니다. 

    ::: zone pivot="programming-runtime-functions-v3"
    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="함수 앱 실행 중":::
    ::: zone-end
    
    ::: zone pivot="programming-runtime-functions-v4"
    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure-v4.png" alt-text="함수 앱 실행 중":::
    ::: zone-end

1. 브라우저의 주소 표시줄에서 `/api/HttpExample?name=Functions` 문자열을 기준 URL에 추가하고 요청을 실행합니다.

    HTTP 트리거 함수를 호출하는 URL은 다음과 같은 형식입니다.

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

1. 이 URL로 이동하면 다음 예제와 같이 함수에서 반환된 원격 GET 요청에 대한 브라우저의 응답이 표시됩니다.

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="브라우저의 함수 응답":::

## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 빠른 시작은 이 빠른 시작을 기반으로 구성됩니다. 이후의 빠른 시작, 자습서 또는 기타 이 빠른 시작에서 만든 서비스를 사용하여 작업하려는 경우 이 빠른 시작에서 만든 리소스를 정리하지 않습니다.

Azure에서 *리소스* 란 함수 앱, 함수, 스토리지 계정 등을 의미합니다. 리소스는 *리소스 그룹* 으로 그룹화되며 그룹을 삭제하면 그룹의 모든 항목을 삭제할 수 있습니다. 

이러한 빠른 시작을 완료하기 위해 리소스를 만들었습니다. [계정 상태](https://azure.microsoft.com/account/) 및 [서비스 가격 책정](https://azure.microsoft.com/pricing/)에 따라 리소스에 대해 요금이 청구될 수 있습니다. 

[!INCLUDE [functions-vstools-cleanup](../../includes/functions-vstools-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Visual Studio를 사용하여 Azure에서 간단한 HTTP 트리거 함수가 있는 C# 함수 앱을 만들고 게시했습니다. 

다음 문서로 이동하여 함수에 Azure Storage 큐 바인딩을 추가하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [함수에 Azure Storage 큐 바인딩 추가](functions-add-output-binding-storage-queue-vs.md)

