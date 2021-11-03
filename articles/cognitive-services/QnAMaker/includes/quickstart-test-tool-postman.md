---
title: 포함 파일
description: 포함 파일
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: include
ms.custom: include file, ignite-fall-2021
ms.date: 09/13/2021
ms.openlocfilehash: cc9b4dc8d6471699c72a24017ef855caf3470971
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131043740"
---
이 Postman 기반 빠른 시작에서는 기술 자료에서 답변을 가져오는 과정을 안내합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 다음이 있어야 합니다.
    * 최신 [**Postman**](https://www.getpostman.com/).
    * Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/cognitive-services/) 계정을 만듭니다.

> * Azure Portal에서 만든 [QnA Maker 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)입니다. 리소스를 만들 때 선택한 Azure Active Directory ID, 구독, QnA 리소스 이름을 기억하세요.

   * 메타데이터 및 잡담으로 구성된 이전 [빠른 시작](../Quickstarts/add-question-metadata-portal.md)의 질문과 대답이 포함된 학습 및 게시된 기술 자료


> [!NOTE]
> 기술 자료에서 질문에 대한 대답을 생성할 준비가 되면 기술 자료를 [학습](../Quickstarts/create-publish-knowledge-base.md#save-and-train) 및 [게시](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)해야 합니다. 기술 자료가 게시되면 **게시** 페이지에 대답을 생성하기 위한 HTTP 요청 설정이 표시됩니다. **Postman** 탭에는 대답을 생성하는 데 필요한 설정이 표시됩니다.

## <a name="set-up-postman-for-requests"></a>요청을 위한 Postman 설정

이 빠른 시작은 Postman **POST** 요청에 대해 동일한 설정을 사용한 다음, 쿼리하려는 내용에 따라 서비스로 전송된 POST 본문 JSON으로 구성합니다.

다음 절차를 사용하여 Postman을 구성한 다음, 각 후속 섹션을 참조하여 POST 본문 JSON을 구성합니다.

1. 기술 자료의 **설정** 페이지에서 **Postman** 탭을 선택하여 기술 자료에서 대답을 생성하는 데 사용되는 구성을 확인합니다. Postman에서 사용하는 다음 정보를 복사합니다.

    |속성|설정|용도 및 값|
    |--|--|--|
    |`POST`| `/knowledgebases/replace-with-your-knowledge-base-id/generateAnswer`|이는 URL에 대한 HTTP 메서드 및 경로입니다.|
    |`Host`|`https://YOUR-RESOURCE_NAME.azurewebsites.net/qnamaker`|이는 URL의 호스트입니다. 호스트 및 게시 값을 연결하여 전체 generateAnswer URL을 가져옵니다.|
    |`Authorization`|`EndpointKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`|Azure에 대한 요청에 권한을 부여하는 헤더 값입니다. |
    |`Content-type`|`application/json`|콘텐츠에 대한 헤더 값입니다.|
    ||`{"question":"<Your question>"}`|JSON 개체인 POST 요청의 본문입니다. 이 값은 쿼리의 용도에 따라 다음 각 섹션에서 변경됩니다.|

1. Postman을 열고 게시된 기술 자료 설정을 사용하여 새 기본 **POST** 요청을 만듭니다. 다음 섹션에서 POST 본문 JSON을 변경하여 쿼리를 기술 자료로 변경합니다.

## <a name="use-metadata-to-filter-answer"></a>대답을 필터링하기 위한 메타데이터 사용

이전 빠른 시작에서는 두 개의 서로 다른 질문을 구분하기 위해 두 개의 QnA 쌍에 메타데이터가 추가되었습니다. 쿼리에 메타데이터를 추가하여 관련 QnA 쌍으로만 필터를 제한합니다.

1. Postman에서 `service:qna_maker`의 이름/값 쌍을 사용한 `strictFilters` 속성을 추가하여 쿼리 JSON만 변경합니다. 본문 JSON은 다음과 같아야 합니다.

    ```json
    {
        'question':'size',
        'strictFilters': [
            {
                'name':'service','value':'qna_maker'
            }
        ]
    }
    ```

    질문은 한 단어(`size`)에 불과하며, 두 개의 질문 및 대답 쌍 중 하나를 반환할 수 있습니다. `strictFilters` 배열은 `qna_maker` 답변만 줄이도록 응답에 지시합니다.

1. 응답에는 필터 조건을 충족하는 답변만 포함됩니다.

    다음 응답은 읽기 쉬운 형식으로 지정되었습니다.

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../Concepts/azure-resources.md) for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    검색어는 충족하지 않지만 필터는 충족하는 질문 및 답변 쌍이 있으면 반환되지 않습니다. 대신 일반 `No good match found in KB.` 답변이 반환됩니다.

## <a name="use-debug-query-property"></a>디버그 쿼리 속성 사용

> [!NOTE]
>종속성에 디버그 속성을 사용하지 않는 것이 좋습니다. 이 속성은 제품 팀의 문제 해결을 돕기 위해 추가되었습니다.

디버그 정보를 사용하면 반환된 대답이 어떻게 결정되었는지 이해할 수 있습니다. 유용하지만 반드시 필요한 것은 아닙니다. 디버그 정보를 사용하여 대답을 생성하려면 `debug` 속성을 추가합니다.

1. Postman에서 `debug` 속성을 추가하여 본문 JSON만 변경합니다. JSON은 다음과 같아야 합니다.

    ```json
    {
        'question':'size',
        'Debug': {
            'Enable':true
        }

    }
    ```

1. 응답에는 대답에 대한 관련 정보가 포함됩니다. 다음 JSON 출력에서는 일부 디버그 정보를 줄임표로 바꾸었습니다.

    ```console
    {
        "answers": [
            {
                "questions": [
                    "How do I share a knowledge base with others?"
                ],
                "answer": "Sharing works at the level of a QnA Maker service, that is, all knowledge bases in the service will be shared.",
                "score": 56.07,
                "id": 5,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": {
            "userQuery": {
                "question": "How do I programmatically update my Knowledge Base?",
                "top": 1,
                "userId": null,
                "strictFilters": [],
                "isTest": false,
                "debug": {
                    "enable": true,
                    "recordL1SearchLatency": false,
                    "mockQnaL1Content": null
                },
                "rankerType": 0,
                "context": null,
                "qnaId": 0,
                "scoreThreshold": 0.0
            },
            "rankerInfo": {
                "specialFuzzyQuery": "how do i programmatically~6 update my knowledge base",
                "synonyms": "what s...",
                "rankerLanguage": "English",
                "rankerFileName": "https://qnamakerstore.blob.core.windows.net/qnamakerdata/rankers/ranker-English.ini",
                "rankersDirectory": "D:\\home\\site\\wwwroot\\Data\\QnAMaker\\rd0003ffa60fc45.24.0\\RankerData\\Rankers",
                "allQnAsfeatureValues": {
                    "WordnetSimilarity": {
                        "5": 0.54706300120043716,...
                    },
                    ...
                },
                "rankerVersion": "V2",
                "rankerModelType": "TreeEnsemble",
                "rankerType": 0,
                "indexResultsCount": 25,
                "reRankerResultsCount": 1
            },
            "runtimeVersion": "5.24.0",
            "indexDebugInfo": {
                "indexDefinition": {
                    "name": "064a4112-bd65-42e8-b01d-141c4c9cd09e",
                    "fields": [...
                    ],
                    "scoringProfiles": [],
                    "defaultScoringProfile": null,
                    "corsOptions": null,
                    "suggesters": [],
                    "analyzers": [],
                    "tokenizers": [],
                    "tokenFilters": [],
                    "charFilters": [],
                    "@odata.etag": "\"0x8D7A920EA5EE6FE\""
                },
                "qnaCount": 117,
                "parameters": {},
                "azureSearchResult": {
                    "continuationToken": null,
                    "@odata.count": null,
                    "@search.coverage": null,
                    "@search.facets": null,
                    "@search.nextPageParameters": null,
                    "value": [...],
                    "@odata.nextLink": null
                }
            },
            "l1SearchLatencyInMs": 0,
            "qnaL1Results": {...}
        },
        "activeLearningEnabled": true
    }
    ```

## <a name="use-test-knowledge-base"></a>테스트 기술 자료 사용

테스트 기술 자료에서 대답을 가져오려면 `isTest` 본문 속성을 사용합니다.

Postman에서 `isTest` 속성을 추가하여 본문 JSON만 변경합니다. JSON은 다음과 같아야 합니다.

```json
{
    'question':'size',
    'isTest': true
}
```

JSON 응답은 게시된 기술 자료 쿼리와 동일한 스키마를 사용합니다.

> [!NOTE]
> 테스트 및 게시된 기술 자료가 정확히 동일한 경우 테스트 인덱스가 리소스의 모든 기술 자료에서 공유되기 때문에 약간의 변형이 있을 수 있습니다.

## <a name="query-for-a-chit-chat-answer"></a>잡담 대답을 위한 쿼리

1. Postman에서 본문 JSON만 사용자의 대화 종료 문으로 변경합니다. JSON은 다음과 같아야 합니다.

    ```json
    {
        'question':'thank you'
    }
    ```

1. 응답에는 점수 및 대답이 포함됩니다.

    ```json
    {
      "answers": [
          {
              "questions": [
                  "I thank you",
                  "Oh, thank you",
                  "My sincere thanks",
                  "My humblest thanks to you",
                  "Marvelous, thanks",
                  "Marvelous, thank you kindly",
                  "Marvelous, thank you",
                  "Many thanks to you",
                  "Many thanks",
                  "Kthx",
                  "I'm grateful, thanks",
                  "Ahh, thanks",
                  "I'm grateful for that, thank you",
                  "Perfecto, thanks",
                  "I appreciate you",
                  "I appreciate that",
                  "I appreciate it",
                  "I am very thankful for that",
                  "How kind, thank you",
                  "Great, thanks",
                  "Great, thank you",
                  "Gracias",
                  "Gotcha, thanks",
                  "Gotcha, thank you",
                  "Awesome thanks!",
                  "I'm grateful for that, thank you kindly",
                  "thank you pal",
                  "Wonderful, thank you!",
                  "Wonderful, thank you very much",
                  "Why thank you",
                  "Thx",
                  "Thnx",
                  "That's very kind",
                  "That's great, thanks",
                  "That is lovely, thanks",
                  "That is awesome, thanks!",
                  "Thanks bot",
                  "Thanks a lot",
                  "Okay, thanks!",
                  "Thank you so much",
                  "Perfect, thanks",
                  "Thank you my friend",
                  "Thank you kindly",
                  "Thank you for that",
                  "Thank you bot",
                  "Thank you",
                  "Right on, thanks very much",
                  "Right on, thanks a lot",
                  "Radical, thanks",
                  "Rad, thanks",
                  "Rad thank you",
                  "Wonderful, thanks!",
                  "Thanks"
              ],
              "answer": "You're welcome.",
              "score": 100.0,
              "id": 75,
              "source": "qna_chitchat_Professional.tsv",
              "metadata": [
                  {
                      "name": "editorial",
                      "value": "chitchat"
                  }
              ],
              "context": {
                  "isContextOnly": false,
                  "prompts": []
              }
          }
      ],
      "debugInfo": null,
      "activeLearningEnabled": true
    }
    ```

    `Thank you`의 질문은 잡담 질문과 정확히 일치하므로 QnA Maker는 신뢰도 점수 100으로, 완전히 신뢰할 수 있습니다. QnA Maker 관련된 모든 질문과 잡담 메타데이터 태그 정보가 포함된 메타데이터 속성도 반환했습니다.

## <a name="use-threshold-and-default-answer"></a>임계값 및 기본 대답 사용

대답에 대한 최소 임계값을 요청할 수 있습니다. 임계값이 충족되지 않으면 기본 대답이 반환됩니다.

1. Postman에서 본문 JSON만 사용자의 대화 종료 문으로 변경합니다. JSON은 다음과 같아야 합니다.

    ```json
    {
        'question':'size',
        'scoreThreshold':80.00
    }
    ```

    질문의 점수가 71%이므로 기술 자료는 답변을 찾지 않고, 기술 자료를 만들 때 사용자가 제공한 기본 대답을 반환해야 합니다.

    점수 및 대답을 포함한 반환된 JSON 응답은 다음과 같습니다.

    ```json
    {
        "answers": [
            {
                "questions": [],
                "answer": "No good match found in KB.",
                "score": 0.0,
                "id": -1,
                "source": null,
                "metadata": []
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```

    QnA Maker가 신뢰가 없음을 의미하는 `0`의 점수를 반환했으며, 기본 응답도 반환했습니다.

1. 임계값을 60%로 변경하고 쿼리를 다시 요청합니다.

    ```json
    {
        'question':'size',
        'scoreThreshold':60.00
    }
    ```

    반환된 JSON에서 대답을 찾았습니다.

    ```json
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](../Concepts/azure-resources.md) for more details.",
                "score": 71.1,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "server",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null,
        "activeLearningEnabled": true
    }
    ```
## <a name="use-unstructured-data-sources"></a>비정형 데이터 원본을 사용합니다.
    
이제 QnA를 추출하는 데 사용할 수 없는 구조화되지 않은 문서를 추가하는 기능을 지원합니다. 사용자는 쿼리에 대한 응답을 가져올 때 GenerateAnswer API에 구조화되지 않은 데이터 집합을 포함하거나 제외하도록 선택할 수 있습니다. GA 서비스에서는 비정형 데이터 세트를 지원하지 않습니다. 사용자 지정 질문 답변에서만 지원됩니다.
