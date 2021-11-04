---
title: '빠른 시작: .NET용 사용자 지정 질문 답변 클라이언트 라이브러리'
description: 이 빠른 시작에서는 .NET용 QnA Maker 클라이언트 라이브러리를 시작하는 방법을 보여줍니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.  QnA Maker를 사용하면 FAQ 문서, URL 및 제품 설명서와 같은 반구조적 내용에서 질문과 대답 서비스를 사용할 수 있습니다.
ms.topic: include
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: c9125eab0ec8ccfe7ee974a9935e92e443140b9e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029832"
---
.NET용 사용자 지정 질문 답변 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

 * 기술 자료 만들기
 * 기술자료 업데이트
 * 기술 자료 게시
 * 장기 실행 작업 대기
 * 기술 자료 다운로드
 * 기술 자료에서 답변 가져오기
 * 기술 자료 삭제

[참조 설명서](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/3.0.0-preview.1) | [C# 샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 또는 현재 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* 사용자 지정 질문 및 답변에는 API 키 및 엔드포인트를 생성하기 위해 사용 설정된 사용자 지정 질문 답변 기능이 있는 [언어 리소스](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics)가 필요합니다. <!--TODO: Change link-->
    * 언어 리소스가 배포된 후 **리소스로 이동** 을 선택합니다. 애플리케이션을 QnA Maker API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.

## <a name="setting-up"></a>설치

### <a name="cli"></a>CLI

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `question-answering-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 *program.cs* 라는 단일 소스 파일을 사용하여 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -n question-answering-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

```console
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 사용자 지정 질문 답변 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 3.0.0-preview.1
```

> [!TIP]
> 한 번에 전체 빠른 시작 코드 파일을 보시겠습니까? [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs)에서 찾을 수 있으며 이 빠른 시작의 코드 예제를 포함합니다.

### <a name="using-directives"></a>Using 지시문

프로젝트 디렉터리에서 *program.cs* 파일을 열고 `using` 지시문을 추가합니다.

[!code-csharp[Dependencies](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Dependencies)]

### <a name="subscription-key-and-resource-endpoints"></a>구독 키 및 리소스 엔드포인트

애플리케이션의 `Main` 메서드에서 다음 섹션에 나와 있는 변수와 코드를 추가하여 이 빠른 시작의 일반적인 작업을 사용합니다.

<!-- TODO: Replace Link
- We use subscription key and authoring key interchangably. For more details on authoring key, follow [Keys](../concepts/azure-resources.md?tabs=v2#keys-in-qna-maker).
-->

- QNA_MAKER_ENDPOINT 값의 형식은 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`입니다. Azure Portal로 이동하여 필수 구성 요소에서 만든 Text Analytics 리소스를 찾습니다. **리소스 관리** 에서 **키 및 엔드포인트** 페이지를 선택하여 작성(구독) 키 및 엔드포인트를 찾습니다.

    > [!div class="mx-imgBorder"]
    > ![사용자 지정 QnA 작성 엔드포인트](../../../qnamaker/media/qnamaker-how-to-key-management/custom-qna-keys-and-endpoint.png)


- 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 예를 들어 [Azure Key Vault](../../../../key-vault/general/overview.md)는 보안 키 스토리지를 제공합니다.

    [!code-csharp[Set the resource key and resource name](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=Resourcevariables)]

## <a name="object-models"></a>개체 모델

[사용자 지정 질문 답변](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker)은 다음 개체 모델을 사용합니다.
* **[QnAMakerClient](#qnamakerclient-object-model)** 는 기술 자료를 만들고, 관리하고, 게시하고, 다운로드하고, 쿼리하는 개체입니다.


[!INCLUDE [Get KBinformation](../../../qnamaker/includes/quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient 개체 모델

작성 클라이언트는 키가 포함된 Microsoft.Rest.ServiceClientCredentials를 사용하여 Azure에 인증하는 [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient) 개체입니다.

클라이언트를 만든 후 [기술 자료](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) 속성을 사용하여 기술 자료를 생성, 관리 및 게시합니다.

JSON 개체를 전송하여 기술 자료를 관리합니다. 즉각적인 작업의 경우 메서드는 일반적으로 상태를 나타내는 JSON 개체를 반환합니다. 장기 실행 작업의 경우 응답은 작업 ID입니다. [요청의 상태](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype)를 확인하려면 해당 작업 ID와 함께 [client.Operations.GetDetailsAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync) 메서드를 호출합니다.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 개체 모델

 사용자 지정 질문 답변에는 **QnAMakerRuntimeClient** 개체를 사용할 필요가 없습니다. 대신 [QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync) 메서드를 호출합니다.

## <a name="code-examples"></a>코드 예제

이 코드 조각은 .NET용 QnA Maker 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [제작 클라이언트 인증](#authenticate-the-client-for-authoring-the-knowledge-base)
* [기술 자료 만들기](#create-a-knowledge-base)
* [기술 자료 업데이트](#update-a-knowledge-base)
* [기술 자료 다운로드](#download-a-knowledge-base)
* [기술 자료 게시](#publish-a-knowledge-base)
* [기술 자료 삭제](#delete-a-knowledge-base)
* [작업의 상태 가져오기](#get-status-of-an-operation)
* [기술 자료에서 답변 생성](#generate-an-answer-from-the-knowledge-base)

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>기술 자료 제작을 위한 클라이언트 인증

키를 사용하여 클라이언트 개체를 인스턴스화하고, 이를 리소스와 함께 사용하여 엔드포인트를 구성하고 엔드포인트 및 키를 사용하여 [QnAMakerClient](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient)를 만듭니다. [ServiceClientCredentials](/dotnet/api/microsoft.rest.serviceclientcredentials) 개체를 만듭니다.

[!code-csharp[Create QnAMakerClient object with key and endpoint](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=AuthorizationAuthor)]

## <a name="create-a-knowledge-base"></a>기술 자료 만들기

기술 자료에는 다음 세 개의 원본에서 [CreateKbDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto) 개체에 대한 질문 및 답변 쌍이 저장됩니다.

* **편집 콘텐츠** 의 경우 [QnADTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto) 개체를 사용합니다.
    * 메타데이터 및 후속 프롬프트를 사용하려면 이 데이터가 개별 QnA 쌍 수준에 추가되기 때문에 편집 컨텍스트를 사용합니다.
* **파일** 의 경우 [FileDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto) 개체를 사용합니다. FileDTO에는 파일 이름과 파일에 도달하기 위한 공개 URL이 포함됩니다.
* **URL** 의 경우 문자열 목록을 사용하여 공개적으로 사용 가능한 URL을 나타냅니다.

생성 단계에는 기술 자료에 대한 속성도 포함되어 있습니다.
* `defaultAnswerUsedForExtraction` - 응답을 찾을 수 없을 때 반환됩니다.
* `enableHierarchicalExtraction` - 추출된 QnA 쌍 간의 프롬프트 관계를 자동으로 만듭니다.
* `language` - 리소스의 첫 번째 기술 자료를 만들 때 Azure Search 인덱스에서 사용할 언어를 설정합니다.

[CreateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync) 메서드를 호출한 다음, 반환된 작업 ID를 [MonitorOperation](#get-status-of-an-operation) 메서드로 전달하여 상태를 폴링합니다.

다음 코드의 마지막 줄은 MonitorOperation의 응답에서 기술 자료 ID를 반환합니다.

[!code-csharp[Create a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=CreateKBMethod)]

기술 자료를 성공적으로 만들려면 위의 코드에서 참조되는 [`MonitorOperation`](#get-status-of-an-operation) 함수를 포함해야 합니다.

## <a name="update-a-knowledge-base"></a>기술 자료 업데이트

기술 자료 ID와 [add](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd), [update](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate) 및 [delete](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete) DTO 개체를 포함하는 [UpdatekbOperationDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto)를 [UpdateAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync) 메서드로 전달하여 기술 자료를 업데이트할 수 있습니다. 성공적으로 업데이트되었는지 확인하려면 [MonitorOperation](#get-status-of-an-operation) 메서드를 사용합니다.

[!code-csharp[Update a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=UpdateKBMethod)]

기술 자료를 성공적으로 업데이트하려면 위의 코드에서 참조되는 [`MonitorOperation`](#get-status-of-an-operation) 함수를 포함해야 합니다.

## <a name="download-a-knowledge-base"></a>기술 자료 다운로드

[DownloadAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync) 메서드를 사용하여 [QnADocumentsDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto) 목록으로 데이터베이스를 다운로드할 수 있습니다. 이 메소드의 결과는 파일이 아니기 때문에 **설정** 페이지에서 QnA Maker 포털의 내보내기와 동일하지 _않습니다_.

[!code-csharp[Download a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DownloadKB)]

## <a name="publish-a-knowledge-base"></a>기술 자료 게시

[PublishAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync) 메서드를 사용하여 기술 자료를 게시할 수 있습니다. 현재 저장되고 학습되며 기술 자료 ID에서 참조되는 모델을 사용하며, 이를 엔드포인트에 게시합니다. 이는 기술 자료를 쿼리하기 위해 필요한 단계입니다.

[!code-csharp[Publish a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=PublishKB)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>기술 자료에서 답변 생성

[QnAMakerClient.Knowledgebase](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase).[GenerateAnswerAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.generateanswerasync) 메서드를 사용하여 게시된 기술 자료에서 답변을 생성합니다. 이 메서드는 기술 자료 ID 및 [QueryDTO](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto)을 허용합니다. 채팅 봇에서 사용할 [Top](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top), [Context](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Context) 및 [AnswerSpanRequest](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.answerspanrequest#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_AnswerSpanRequest) 같은 QueryDTO의 추가 속성에 액세스합니다.

[!code-csharp[Generate an answer from a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=GenerateAnswer)]

<!-- TODO: Replace Link
This is a simple example querying the knowledgebase. To understand advanced querying scenarios, review [other query examples](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer).
-->

## <a name="delete-a-knowledge-base"></a>기술 자료 삭제

기술 자료 ID의 매개 변수와 함께 [DeleteAsync](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync) 메서드를 사용하여 기술 자료를 삭제할 수 있습니다.

[!code-csharp[Delete a knowledge base](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=DeleteKB)]


## <a name="get-status-of-an-operation"></a>작업의 상태 가져오기

create 및 update와 같은 일부 메서드는 프로세스가 완료될 때까지 기다리는 대신 [작업](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation)이 반환되는 데 충분한 시간을 가질 수 있습니다. 원래 메서드의 상태를 확인하려면 작업에서 [작업 ID](/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId)를 사용하여 폴링합니다(다시 시도 논리 사용).

다음 코드 블록의 루프 및 `Task.Delay`는 다시 시도 논리를 시뮬레이션하는 데 사용됩니다. 아래는 사용자의 자체 다시 시도 논리로 바꾸어야 합니다.

[!code-csharp[Monitor an operation](~/cognitive-services-quickstart-code/dotnet/QnAMaker/Preview-sdk-based-quickstart/Program.cs?name=MonitorOperation)]

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 `dotnet run` 명령을 사용하여 애플리케이션을 실행합니다.

```dotnetcli
dotnet run
```

이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/QnAMaker/Preview-sdk-based-quickstart)에서 확인할 수 있습니다.
