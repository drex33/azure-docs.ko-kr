---
title: '빠른 시작: Ruby용 QnA Maker 클라이언트 라이브러리'
description: 이 빠른 시작에서는 Ruby용 QnA Maker 클라이언트 라이브러리를 시작하는 방법을 보여줍니다.
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.date: 09/04/2020
ms.author: v-jawe
ms.openlocfilehash: b0038126e72bbf059635c69c7421136d4da674af
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288395"
---
Ruby용 QnA Maker 클라이언트 라이브러리를 사용하여 다음을 수행할 수 있습니다.

* 기술 자료 만들기
* 기술자료 업데이트
* 기술 자료 게시
* 예측 런타임 엔드포인트 키 가져오기
* 장기 실행 작업 대기
* 기술 자료 다운로드
* 답변 구하기
* 기술 자료 삭제

[라이브러리 소스 코드(제작)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated) | [라이브러리 소스 코드(런타임)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated) | [패키지(제작)](https://rubygems.org/gems/azure_cognitiveservices_qnamaker) | [패키지(런타임)](https://rubygems.org/gems/azure_cognitiveservices_qnamakerruntime) | [Ruby 샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/ruby/qnamaker)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* [Ruby 2.x](https://www.ruby-lang.org/)
* Azure 구독을 보유한 후에는 Azure Portal에서 [QnA Maker 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)를 만들어 제작 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 선택합니다.
    * 애플리케이션을 QnA Maker API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

## <a name="setting-up"></a>설치

### <a name="install-the-client-libraries"></a>클라이언트 라이브러리 설치

Ruby를 설치한 후 다음을 사용하여 클라이언트 라이브러리를 설치할 수 있습니다.

```console
gem install azure_cognitiveservices_qnamaker
gem install azure_cognitiveservices_qnamakerruntime
```

### <a name="create-a-new-ruby-application"></a>새 Ruby 애플리케이션 만들기

새 파일 `quickstart.rb`를 만들고 다음 라이브러리를 가져옵니다.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Dependencies":::

리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다.

> [!IMPORTANT]
> Azure Portal로 이동하여 필수 구성 요소에서 만든 QnA Maker 리소스의 키와 엔드포인트를 찾습니다. 리소스의 **키 및 엔드포인트** 페이지의 **리소스 관리** 아래에 있습니다.
> 기술 자료를 만들려면 전체 키가 필요합니다. 엔드포인트의 리소스 이름만 필요합니다. 형식은 `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`입니다.
> 완료되면 코드에서 키를 제거하고 공개적으로 게시하지 마세요. 프로덕션의 경우 자격 증명을 안전하게 저장하고 액세스하는 방법을 사용하는 것이 좋습니다. 예를 들어 [Azure Key Vault](../../../key-vault/general/overview.md)는 보안 키 스토리지를 제공합니다.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Resourcevariables":::

## <a name="object-models"></a>개체 모델

QnA Maker는 다음과 같은 두 가지 개체 모델을 사용합니다.
* **[QnAMakerClient](#qnamakerclient-object-model)** 는 기술 자료를 만들고, 관리하고, 게시하고, 다운로드하는 개체입니다.
* **[QnAMakerRuntime](#qnamakerruntimeclient-object-model)** 은 GenerateAnswer API를 사용하여 기술 자료를 쿼리하고 Train API([활성 학습](../how-to/use-active-learning.md)의 일부로)를 사용하여 새로운 제안된 질문을 보내는 개체입니다.

[!INCLUDE [Get KBinformation](./quickstart-sdk-cognitive-model.md)]

### <a name="qnamakerclient-object-model"></a>QnAMakerClient 개체 모델

제작 QnA Maker 클라이언트는 키가 포함된 MsRest::ServiceClientCredentials를 사용하여 Azure에 인증하는 [QnAMakerClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/qnamaker_client.rb) 개체입니다.

클라이언트가 생성되면 클라이언트의 [knowledgebase](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb) 속성 메서드를 사용하여 기술 자료를 생성, 관리 및 게시합니다.

즉각적인 작업의 경우 메서드는 일반적으로 결과(있는 경우)를 반환합니다. 장기 실행 작업의 경우 응답은 [작업](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb) 개체입니다. `operation.operation_id` 값과 함께 [operations.get_details](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/operations.rb#L33) 메서드를 호출하여 [요청의 상태](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation_state_type.rb)를 확인합니다.

### <a name="qnamakerruntimeclient-object-model"></a>QnAMakerRuntimeClient 개체 모델

런타임 QnA Maker 클라이언트는 [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb) 개체입니다.

제작 클라이언트를 사용하여 기술 자료를 게시한 후에는 런타임 클라이언트의 [runtime.generate_answer](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/runtime.rb#L34) 메서드를 사용하여 기술 자료에서 답변을 가져옵니다.

`generate_answer`를 호출할 때 `custom_headers` 선택적 매개 변수에 대한 해시를 전달합니다. 이 해시에는 키 `Authorization` 및 값 `EndpointKey YOUR_ENDPOINT_KEY`가 포함되어야 합니다. YOUR_ENDPOINT_KEY 값의 경우 제작 클라이언트를 사용하여 [endpoint_keys.get_keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32)를 호출합니다.

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>기술 자료 제작을 위한 클라이언트 인증

제작 엔드포인트 및 구독 키를 사용하여 클라이언트를 인스턴스화합니다.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationAuthor":::

## <a name="create-a-knowledge-base"></a>기술 자료 만들기

기술 자료에는 다음 세 개의 원본에서 [CreateKbDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/create_kb_dto.rb) 개체에 대한 질문 및 답변 쌍이 저장됩니다.

* **편집 콘텐츠** 의 경우 [QnADTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adto.rb) 개체를 사용합니다.
    * 메타데이터 및 후속 프롬프트를 사용하려면 이 데이터가 개별 QnA 쌍 수준에 추가되기 때문에 편집 컨텍스트를 사용합니다.
* **파일** 의 경우 [FileDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/file_dto.rb) 개체를 사용합니다. FileDTO에는 파일에 연결하기 위한 파일 이름과 공용 URL이 포함됩니다.
* **URL** 의 경우 문자열 목록을 사용하여 공개적으로 사용 가능한 URL을 나타냅니다.

[create](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L554) 메서드를 호출한 다음, 반환된 작업의 `operation_id` 속성을 [operations.get_details](#get-status-of-an-operation) 메서드로 전달하여 상태를 폴링합니다.

다음 코드의 마지막 줄은 기술 자료 ID를 반환합니다.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="CreateKBMethod":::

## <a name="update-a-knowledge-base"></a>기술 자료 업데이트

[knowledgebase.update](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L455)를 호출하고 기술 자료 ID와 [UpdateKbOperationDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dto.rb) 개체를 전달하여 기술 자료를 업데이트할 수 있습니다. 그러면 해당 개체에 다음이 포함될 수 있습니다.
- [add](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoadd.rb)
- [update](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtoupdate.rb)
- [delete](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/update_kb_operation_dtodelete.rb)

반환된 작업의 `operation_id` 속성을 [operations.get_details](#get-status-of-an-operation) 메서드로 전달하여 상태를 폴링합니다.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="UpdateKBMethod":::

## <a name="download-a-knowledge-base"></a>기술 자료 다운로드

[knowledgebase.download](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L651) 메서드를 사용하여 [QnADocumentsDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/qn_adocuments_dto.rb) 목록으로 데이터베이스를 다운로드합니다. 이 메서드의 결과가 TSV 파일이 아니므로 QnA Maker 포털에 있는 **설정** 페이지의 내보내기와 동일하지 않습니다.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DownloadKB":::

## <a name="publish-a-knowledge-base"></a>기술 자료 게시

[knowledgebase.publish](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L283) 메서드를 사용하여 기술 자료를 게시합니다. 기술 자료 ID에서 참조하는 현재 저장 및 학습된 모델을 사용하며, 이를 엔드포인트에 게시합니다.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="PublishKB":::

## <a name="get-query-runtime-key"></a>쿼리 런타임 키 가져오기

기술 자료가 게시된 후에는 기술 항목을 쿼리하기 위한 런타임 엔드포인트 키가 필요합니다. 이는 제작 클라이언트를 만드는 데 사용되는 구독 키와 다릅니다.

[endpoint_keys.get_keys](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/endpoint_keys.rb#L32) 메서드를 사용하여 [EndpointKeysDTO](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/endpoint_keys_dto.rb) 개체를 가져옵니다.

개체에서 반환된 키 속성 중 하나를 사용하여 기술 자료를 쿼리합니다.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GetQueryEndpointKey":::

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>응답을 생성하기 위한 런타임 인증

기술 자료를 쿼리하는 [QnAMakerRuntimeClient](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamakerruntime/lib/4.0/generated/azure_cognitiveservices_qnamakerruntime/qnamaker_runtime_client.rb)를 만들어 답변을 생성하거나 활성 학습으로부터 학습합니다.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="AuthorizationQuery":::

QnAMakerRuntimeClient를 사용하여 기술 자료에서 답변을 얻거나 새로 제안된 질문을 [활성 학습](../index.yml)에 대한 기술 자료에 보냅니다.

## <a name="generate-an-answer-from-the-knowledge-base"></a>기술 자료에서 답변 생성

RuntimeClient.runtime.generateAnswer 메서드를 사용하여 게시된 기술 자료에서 답변을 생성합니다. 이 메서드는 기술 자료 ID 및 QueryDTO를 허용합니다. 채팅 봇에서 사용할 Top 및 Context와 같은 QueryDTO의 추가 속성에 액세스합니다.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="GenerateAnswer":::

이는 기술 자료를 쿼리하는 간단한 예제입니다. 고급 쿼리 시나리오를 이해하려면 [기타 쿼리 예제](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md?pivots=url-test-tool-curl#use-curl-to-query-for-a-chit-chat-answer)를 검토하세요.

## <a name="delete-a-knowledge-base"></a>기술 자료 삭제

기술 자료 ID의 매개 변수와 함께 [knowledgebase.delete](https://github.com/Azure/azure-sdk-for-ruby/blob/20b8b81287d272587ace808434c14b039d014e12/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/knowledgebase.rb#L205) 메서드를 사용하여 기술 자료를 삭제합니다.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="DeleteKB":::

## <a name="get-status-of-an-operation"></a>작업의 상태 가져오기

create 및 update와 같은 일부 메서드는 프로세스가 완료될 때까지 기다리는 대신 [작업](https://github.com/Azure/azure-sdk-for-ruby/blob/master/data/azure_cognitiveservices_qnamaker/lib/4.0/generated/azure_cognitiveservices_qnamaker/models/operation.rb)이 반환되는 데 충분한 시간을 가질 수 있습니다. 원래 메서드의 상태를 확인하려면 작업에서 작업 ID를 사용하여 폴링합니다(다시 시도 논리 사용).

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="MonitorOperation":::

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션의 주요 방법은 다음과 같습니다.

:::code language="ruby" source="~/cognitive-services-quickstart-code/ruby/qnamaker/sdk/quickstart.rb" id="Main":::

빠른 시작 파일에서 ruby 명령을 사용하여 애플리케이션을 실행합니다.

```console
ruby quickstart.rb
```

이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/ruby/qnamaker/sdk/quickstart.rb)에서 확인할 수 있습니다.