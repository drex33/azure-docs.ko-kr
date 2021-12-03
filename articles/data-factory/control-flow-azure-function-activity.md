---
title: Azure 함수 작업
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure 함수 작업을 사용하여 Azure Data Factory 또는 Azure Synapse Analytics 파이프라인에서 Azure 함수를 실행하는 방법 알아보기
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 8bb4321372411ea9f26757cf1eb4b2d75f435848
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133517132"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Azure Data Factory의 Azure 함수 작업
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Azure 함수 작업을 사용하면 Azure Data Factory 또는 Synapse 파이프라인에서 [Azure Functions](../azure-functions/functions-overview.md)를 실행할 수 있습니다. Azure Function을 실행하려면 연결된 서비스 연결을 만들어야 합니다.  그런 다음, 실행하려는 Azure Function을 지정하는 활동과 함께 연결된 서비스를 사용할 수 있습니다.

이 기능에 대한 소개 및 데모는 다음 비디오(8분)를 시청하세요.

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure 함수의 연결된 서비스


Azure 함수의 반환 형식은 유효한 `JObject`여야 합니다. ([JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm)는 `JObject`가 *아님* 에 유의) `JObject` 이외의 모든 반환 형식이 실패하고 *응답 콘텐츠가 유효한 JObject가 아닙니다* 사용자 오류가 발생합니다.

함수 키는 각각이 함수 앱 내에서 별도의 고유 키나 마스터 키를 갖는 함수 이름에 대한 보안 액세스를 제공합니다. 관리 ID는 전체 함수 앱에 대한 보안 액세스를 제공합니다. 사용자는 함수 이름에 액세스하려면 키를 제공해야 합니다. 자세한 내용은 함수 액세스 키에 대한 자세한 내용은 함수 설명서를 [참조하세요.](../azure-functions/functions-bindings-http-webhook-trigger.md?tabs=csharp#configuration)


| **속성** | **설명** | **필수** |
| --- | --- | --- |
| Type   | type 속성을 다음으로 설정해야 합니다. **AzureFunction** | Yes |
| 함수 앱 URL | Azure 함수 앱의 URL입니다. 형식은 `https://<accountname>.azurewebsites.net`입니다. 이 URL은 Azure Portal에서 함수 앱을 볼 때 **URL** 섹션 아래에 표시되는 값입니다.  | Yes |
| 함수 키 | Azure 함수의 액세스 키입니다. 해당 함수의 **관리** 섹션을 클릭하고 **기능 키** 또는 **호스트 키** 를 복사합니다. 자세한 내용은 다음을 참조하세요. [Azure Functions HTTP 트리거 및 바인딩](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | Yes |
|   |   |   |

## <a name="azure-function-activity"></a>Azure 함수 작업

| **속성**  | **설명** | **허용된 값** | **필수** |
| --- | --- | --- | --- |
| Name  | 파이프라인의 작업 이름입니다.  | String | 예 |
| Type  | 작업의 형식은 'AzureFunctionActivity'입니다. | String | 예 |
| 연결된 서비스 | 해당하는 Azure 함수 앱에 대한 Azure Function 연결된 서비스입니다.  | 연결된 서비스 참조 | Yes |
| 함수 이름  | Azure 함수 앱에서 이 작업이 호출하는 함수의 이름입니다. | String | 예 |
| 메서드  | 함수 호출에 대한 REST API 메서드입니다. | 문자열 지원 형식: ‘GET’, ‘POST’, ‘PUT’   | Yes |
| 헤더  | 요청에 전송되는 헤더입니다. 예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 씁니다. "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | 문자열(또는 resultType 문자열이 있는 식) | No |
| 본문  | 함수 api 메서드에 대한 요청과 함께 전송되는 본문  | 문자열(또는 resultType 문자열이 있는 식) 또는 개체   | PUT/POST 메서드에 필요합니다. |
|   |   |   | |

[요청 페이로드 스키마](control-flow-web-activity.md#request-payload-schema) 섹션에서 요청 페이로드의 스키마를 참조하세요.

## <a name="routing-and-queries"></a>라우팅 및 쿼리

Azure 함수 작업은 **라우팅** 을 지원합니다. 예를 들어, Azure 함수에 엔드포인트 `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>`가 있는 경우, Azure 함수 작업에 사용할 `functionName`은 `<functionName>/<value>`입니다. 런타임에 원하는 `functionName`을 제공하도록 이 함수를 매개 변수화할 수 있습니다.

>[!NOTE]
>`functionName`Durable Functions 라우팅 정보를 포함하려면 JSON 정의에 있는 함수 바인딩의 **경로** 속성에서 를 가져와야 합니다.  경로 세부 정보를 포함하지 않고 를 사용하면 `functionName` 함수 앱을 찾을 수 없으므로 오류가 발생합니다.

또한 Azure 함수 작업은 **쿼리** 를 지원합니다. 쿼리는 의 일부로 포함되어야 `functionName` 합니다. 예를 들어, 함수 이름이 `HttpTriggerCSharp`고 포함할 쿼리가 `name=hello`면 Azure 함수 작업에서 `functionName`을 `HttpTriggerCSharp?name=hello`로 구성할 수 있습니다. 이 함수는 매개 변수화하여 런타임에 값을 결정할 수 있습니다.

## <a name="timeout-and-long-running-functions"></a>타임아웃 및 장기 실행 함수

Azure Functions는 설정에서 구성한 `functionTimeout` 설정에 관계없이 230초 후 시간이 초과됩니다. 자세한 내용은 [이 문서](../azure-functions/functions-versions.md#timeout)를 참조하세요. 이 동작을 해결하려면 비동기 패턴을 따르거나 Durable Functions를 사용합니다. Durable Functions 이점은 자체 상태 추적 메커니즘을 제공하므로 사용자 고유의 상태 추적을 구현할 필요가 없다는 것입니다.

[이 문서](../azure-functions/durable/durable-functions-overview.md)에서 Durable Functions에 대해 자세히 알아보세요. Azure 함수 작업을 설정하여 Durable Function을 호출할 수 있습니다. 이 함수는 [이 예제](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery)와 같이 다른 URI를 사용하는 응답을 반환합니다. `statusQueryGetUri`는 함수가 실행되는 동안 HTTP Status 202를 반환하므로 웹 작업을 사용하여 함수의 상태를 폴링할 수 있습니다. 필드가 로 설정된 웹 활동을 `url` `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri` 설정합니다. Durable Function이 완료되면 함수의 출력이 웹 작업의 출력이 됩니다.


## <a name="sample"></a>샘플

Azure 함수를 사용하여 tar 파일의 콘텐츠를 추출하는 샘플은 [여기](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[파이프라인 및 작업](concepts-pipelines-activities.md)에서 지원되는 작업에 대해 자세히 알아보세요.
