---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/17/2021
ms.author: aahi
ms.openlocfilehash: d68d8980fad6c5510f7a46b055f26f075775b8d4
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132758619"
---
#### <a name="custom-ner-task-results"></a>사용자 지정 NER 작업 결과

GET 결과 호출에서 반환된 응답은 다음 매개 변수가 있는 JSON 문서입니다.

```json
{
    "createdDateTime": "2021-05-19T14:32:25.578Z",
    "displayName": "MyJobName",
    "expirationDateTime": "2021-05-19T14:32:25.578Z",
    "jobId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
    "lastUpdateDateTime": "2021-05-19T14:32:25.578Z",
    "status": "completed",
    "errors": [],
    "tasks": {
        "details": {
            "name": "MyJobName",
            "lastUpdateDateTime": "2021-03-29T19:50:23Z",
            "status": "completed"
        },
        "completed": 1,
        "failed": 0,
        "inProgress": 0,
        "total": 1,
        "tasks": {
    "customEntityRecognitionTasks": [
        {
            "lastUpdateDateTime": "2021-05-19T14:32:25.579Z",
            "name": "MyJobName",
            "status": "completed",
            "results": {
               "documents": [
                        {
                            "id": "doc1",
                            "entities": [
                                {
                                    "text": "Government",
                                    "category": "restaurant_name",
                                    "offset": 23,
                                    "length": 10,
                                    "confidenceScore": 0.0551877357
                                }
                            ],
                            "warnings": []
                        },
                        {
                            "id": "doc2",
                            "entities": [
                                {
                                    "text": "David Schmidt",
                                    "category": "artist",
                                    "offset": 0,
                                    "length": 13,
                                    "confidenceScore": 0.8022353
                                }
                            ],
                            "warnings": []
                        }
                    ],
                "errors": [],
                "statistics": {
                    "documentsCount":0,
                    "erroneousDocumentsCount":0,
                    "transactionsCount":0
                }
                    }
                }
            ]
        }
    }
```