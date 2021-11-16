---
title: '빠른 시작: .NET용 질문 답변 클라이언트 라이브러리'
description: 이 빠른 시작에서는 .NET용 질문 답변 클라이언트 라이브러리를 시작하는 방법을 보여 줍니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. 질문 답변을 사용하면 FAQ 문서, URL 및 제품 설명서와 같은 반정형 콘텐츠에서 질문-대답 서비스를 구동할 수 있습니다.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: include
ms.date: 11/11/2021
ms.openlocfilehash: e374ef91c414b9d352b3c0cddd8af59da3e699a0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354055"
---
이 빠른 시작을 .NET용 질문 답변 클라이언트 라이브러리에 사용하여 다음을 수행합니다.

* 기술 자료에서 답변을 얻습니다.
* 질문과 함께 보내는 텍스트 본문에서 답변을 얻습니다.
* 질문에 대한 답변의 신뢰도 점수를 얻습니다.

 [API 참조 설명서][questionanswering_refdocs]|[소스 코드][questionanswering_client_src] | [패키지(NuGet)][questionanswering_nuget_package]  | [샘플][questionanswering_samples] |

[questionanswering_nuget_package]: https://nuget.org/packages/Azure.AI.Language.
[questionanswering_refdocs]: https://docs.microsoft.com/dotnet/api/Azure.AI.Language.QuestionAnswering/
[questionanswering_client_src]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/cognitivelanguage/Azure.AI.Language.QuestionAnswering/src/
[questionanswering_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/cognitivelanguage/Azure.AI.Language.QuestionAnswering/samples/README.md

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 또는 현재 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* 질문 답변 - API 키와 엔드포인트를 생성하기 위해 사용자 지정 질문 답변 기능이 사용하도록 설정된 [언어 리소스](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics)가 필요합니다. <!--TODO: Change link-->
    * 언어 리소스가 배포된 후 **리소스로 이동** 을 선택합니다. API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
* 쿼리할 기존 기술 자료. 기술 자료가 설치되지 않은 경우 [**Language Studio 빠른 시작**](../quickstart/sdk.md)의 지침을 따를 수 있습니다. 또는 이 [Surface 사용자 가이드 URL](https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf)을 데이터 원본으로 사용하는 기술 자료를 추가합니다.

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
dotnet add package Azure.AI.Language.QuestionAnswering
```

## <a name="query-a-knowledge-base"></a>기술 자료 쿼리

#### <a name="generate-an-answer-from-a-knowledge-base"></a>기술 자료에서 답변 생성

아래 예제에서는 질문에 대한 답변을 얻기 위해 `GetAnswers`를 사용하여 기술 자료를 쿼리할 수 있습니다.

아래 코드를 업데이트하고, 다음 변수에 대한 고유한 값을 제공해야 합니다.

|변수 이름 | 값 |
|--------------------------|-------------|
| `endpoint`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트 예제는 `https://southcentralus.api.cognitive.microsoft.com/`입니다.|
| `credential` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 가동 중지 시간이 0인 안전한 키 회전을 위해 항상 두 개의 유효한 키가 있어야 합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `projectName` | 질문 답변 프로젝트의 이름입니다.|
| `deploymentName`             | 두 개의 가능한 값은 `test` 및 `production`입니다. `production`은 **Language Studio** > **질문 답변** > **기술 자료 배포** 에서 기술 자료를 배포했는지 여부에 따라 달라집니다.|

프로젝트 디렉터리에서 *program.cs* 파일을 열고, 다음 코드로 바꿉니다.

```csharp
using Azure;
using Azure.AI.Language.QuestionAnswering;
using System;

namespace question_answering
{
    class Program
    {
        static void Main(string[] args)
        {

            Uri endpoint = new Uri("https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/");
            AzureKeyCredential credential = new AzureKeyCredential("{YOUR-LANGUAGE-RESOURCE-KEY}");
            string projectName = "{YOUR-PROJECT-NAME}";
            string deploymentName = "production";

            string question = "How long should my Surface battery last?";

            QuestionAnsweringClient client = new QuestionAnsweringClient(endpoint, credential);
            QuestionAnsweringProject project = new QuestionAnsweringProject(projectName, deploymentName);

            Response<AnswersResult> response = client.GetAnswers(question, project);

            foreach (KnowledgeBaseAnswer answer in response.Value.Answers)
            {
                Console.WriteLine($"Q:{question}");
                Console.WriteLine($"A:{answer.Answer}");
            }
        }
    }
}
```

이 예제의 변수는 하드 코딩되어 있습니다. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 예를 들어 [Azure Key Vault](../../../../key-vault/general/overview.md)는 보안 키 스토리지를 제공합니다.

먼저 `Program.cs`를 위의 코드로 업데이트하고 올바른 변수 값으로 바꿉니다. 애플리케이션 디렉터리에서 `dotnet run` 명령을 사용하여 애플리케이션을 실행합니다.

```console
dotnet run
```

응답은 다음과 같습니다.

```console
Q: How much battery life do I have left?
A: If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
```

질문 답변에서 이 답변이 올바른 응답이라고 확신하는 정도에 대한 정보를 확인하려면 추가 출력 문을 기존 출력 문 아래에 추가합니다.

```csharp
Console.WriteLine($"Q:{question}");
Console.WriteLine($"A:{answer.Answer}");
Console.WriteLine($"({answer.Confidence})"); // add this line
```

`dotnet run`을 다시 실행하면 이제 신뢰도 점수가 포함된 결과가 표시됩니다.

```console
Q:How much battery life do I have left?
A:If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
(0.9185)
```

신뢰도 점수는 0과 1 사이의 값을 반환합니다. 이를 백분율로 생각하여 100을 곱합니다. 이 경우 0.9185의 신뢰도 점수는 질문 답변에서 기술 자료를 기반으로 한 질문에 대한 올바른 답변이라고 91.85% 확신한다는 것을 의미합니다.

신뢰도 점수가 특정 임계값 미만인 답변을 제외하려면 `AnswerOptions`을 사용하여 `ConfidenceScoreThreshold` 속성을 추가합니다.

```csharp
QuestionAnsweringClient client = new QuestionAnsweringClient(endpoint, credential);
QuestionAnsweringProject project = new QuestionAnsweringProject(projectName, deploymentName);
AnswersOptions options = new AnswersOptions(); //Add this line
options.ConfidenceThreshold = 0.95; //Add this line

Response<AnswersResult> response = client.GetAnswers(question, project, options); //Add the additional options parameter
```

이전 코드 실행에서 신뢰도 점수가 `.9185`임을 알고 있으므로 임계값을 `.95`로 설정하면 [기본 답변](../how-to/change-default-answer.md)이 반환됩니다.

```console
Q:How much battery life do I have left?
A:No good match found in KB
(0)
```

## <a name="query-text-without-a-knowledge-base"></a>기술 자료 없이 텍스트 쿼리

`GetAnswersFromText`를 사용하여 기술 자료 없이 질문 답변을 사용할 수도 있습니다. 이 경우 요청이 보내지면 답변을 검색하려는 질문 및 관련 텍스트 레코드 모두를 질문 답변에 제공합니다.

다음 예제에서는 `endpoint` 및 `credential`에 대한 변수만 수정하면 됩니다.

```csharp
using Azure;
using Azure.AI.Language.QuestionAnswering;
using System;
using System.Collections.Generic;


namespace questionansweringcsharp
{
    class Program
    {
        static void Main(string[] args)
        {

            Uri endpoint = new Uri("https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/");
            AzureKeyCredential credential = new AzureKeyCredential("YOUR-LANGUAGE-RESOURCE-KEY");
            QuestionAnsweringClient client = new QuestionAnsweringClient(endpoint, credential);

            IEnumerable<TextDocument> records = new[]
            {
                new TextDocument("doc1", "Power and charging.It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. " +
                         "It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it"),
                new TextDocument("doc2", "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. " +
                         "The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface."),
            };

            AnswersFromTextOptions options = new AnswersFromTextOptions("How long does it takes to charge a surface?", records);
            Response<AnswersFromTextResult> response = client.GetAnswersFromText(options);

           foreach (TextAnswer answer in response.Value.Answers)
            {
                if (answer.Confidence > .9)
                {
                    string BestAnswer = response.Value.Answers[0].Answer;

                    Console.WriteLine($"Q:{options.Question}");
                    Console.WriteLine($"A:{BestAnswer}");
                    Console.WriteLine($"Confidence Score: ({response.Value.Answers[0].Confidence:P2})"); //:P2 converts the result to a percentage with 2 decimals of accuracy. 
                    break;
                }
                else
                {
                    Console.WriteLine($"Q:{options.Question}");
                    Console.WriteLine("No answers met the requested confidence score.");
                    break;
                }
            }

        }
    }
}
```

위의 코드를 실행하려면 `Program.cs`를 위의 스크립트 블록 내용으로 바꾸고, 필수 구성 요소의 일부로 만든 언어 리소스에 해당하도록 `endpoint` 및 `credential` 변수를 수정합니다.

이 경우 모든 답변을 반복하고 0.9보다 큰 가장 높은 신뢰도 점수를 가진 답변만 반환합니다. `GetAnswersFromText`에서 사용할 수 있는 옵션에 대해 자세히 알아보세요.
