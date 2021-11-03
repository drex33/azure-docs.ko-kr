---
title: '빠른 시작: LUIS(Language Understanding) SDK 클라이언트 라이브러리 및 REST API'
description: LUIS SDK 클라이언트 라이브러리 및 REST API를 사용하여 LUIS 앱을 만들고 쿼리합니다.
ms.topic: quickstart
ms.date: 10/20/2021
ms.service: cognitive-services
ms.author: aahi
manager: nitinme
ms.subservice: language-understanding
author: aahill
keywords: Azure, 인공 지능, ai, 자연어 처리, nlp, LUIS, azure luis, 자연어 이해, ai 챗봇, 챗봇 작성자, 자연어 이해
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020, ignite-fall-2021
zone_pivot_groups: programming-languages-set-luis
ms.openlocfilehash: c98bbd885bb2646ff32c856aecc219cae326d01c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043873"
---
# <a name="quickstart-language-understanding-luis-client-libraries-and-rest-api"></a>빠른 시작: LUIS(Language Understanding) 클라이언트 라이브러리 및 REST API

> [!NOTE]
> 이제 최신 버전의 Language Understanding 기능을 Azure Cognitive Service for Language의 일부로 사용할 수 있습니다. 자세한 내용은 [Azure Cognitive Service for Language 설명서](../language-service/index.yml)를 참조하세요. 언어 서비스 내의 언어 이해 기능에 대해서는 [대화형 Language Understanding](../language-service/conversational-language-understanding/overview.md), [사용자 지정 명명된 엔터티 인식](../language-service/custom-named-entity-recognition/overview.md) 및 [사용자 지정 분류](../language-service/custom-classification/overview.md)를 참조하세요.

C#, Python 또는 JavaScript를 통해 이 빠른 시작을 사용하여 LUIS SDK 클라이언트 라이브러리로 Azure LUIS AI(인공 지능) 앱을 만들고 쿼리합니다. 또한 cURL을 사용하여 REST API를 통해 요청을 보낼 수 있습니다.

LUIS(Language Understanding)를 사용하면 사용자의 대화형 자연어 텍스트에 NLP(자연어 처리)를 적용하여 전반적인 의미를 예측하고 관련된 세부 정보를 끌어올 수 있습니다.

* **작성** 클라이언트 라이브러리 및 REST API를 사용하면 LUIS 앱을 생성, 편집, 학습 및 게시할 수 있습니다.
* **예측 런타임** 클라이언트 라이브러리 및 REST API를 사용하면 게시된 앱을 쿼리할 수 있습니다.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

::: zone pivot="rest-api"
[!INCLUDE [LUIS development with REST API](./includes/rest-api.md)]
::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

[LUIS 포털](https://www.luis.ai)에서 앱을 삭제하고 [Azure Portal](https://portal.azure.com/)에서 Azure 리소스를 삭제할 수 있습니다.

REST API를 사용 중인 경우 빠른 시작을 완료한 후 파일 시스템에서 `ExampleUtterances.JSON` 파일을 삭제합니다.

## <a name="troubleshooting"></a>문제 해결

* 클라이언트 라이브러리에 대한 인증 - 인증 오류는 일반적으로 잘못된 키 및 엔드포인트가 사용되었음을 나타냅니다. 이 빠른 시작은 편의상 예측 런타임에 작성 키와 엔드포인트를 사용하지만 월별 할당량을 아직 사용하지 않은 경우에만 작동합니다. 작성 키와 엔드포인트를 사용할 수 없는 경우 예측 런타임 SDK 클라이언트 라이브러리에 액세스할 때 예측 런타임 키와 엔드포인트를 사용해야 합니다.
* 엔터티 만들기 - 이 자습서에 사용되는 중첩된 기계 학습 엔터티를 만드는 동안 오류가 발생하면 코드를 복사하고 다른 엔터티를 만들기 위해 코드를 변경하지 않았는지 확인합니다.
* 예제 발화 만들기 - 이 자습서에 사용되는 레이블이 지정된 예제 발화를 만드는 동안 오류가 발생하면 코드를 복사하고 다른 레이블이 지정된 예제를 만들기 위해 코드를 변경하지 않았는지 확인합니다.
* 학습 - 학습 오류가 발생하는 경우 이는 일반적으로 빈 앱(예: 예제 발화가 있는 의도 없음) 또는 형식이 잘못된 의도 또는 엔터티가 포함된 앱을 나타냅니다.
* 기타 오류 - 코드가 텍스트 및 JSON 개체를 사용하여 클라이언트 라이브러리를 호출하므로 코드를 변경하지 않았는지 확인합니다.

기타 오류 - 이전 목록에서 다루지 않은 오류가 발생하면 이 페이지의 하단에 피드백을 제공하여 알려주세요. 설치한 클라이언트 라이브러리의 프로그래밍 언어와 버전을 포함합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [LUIS에 대한 반복적인 앱 개발](./luis-concept-app-iteration.md)
