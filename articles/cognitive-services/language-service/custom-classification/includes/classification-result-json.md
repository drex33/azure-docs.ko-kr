---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/17/2021
ms.author: aahi
ms.openlocfilehash: 5ea8452df4364b99d1a309216d403ea06df68625
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132757233"
---
#### <a name="text-classification-task-results"></a>텍스트 분류 작업 결과

Get 결과 호출에서 반환된 응답은 다음 매개 변수가 있는 JSON 문서입니다.

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
    "customMultiClassificationTasks": [
    {
        "lastUpdateDateTime": "2021-05-19T14:32:25.579Z",
        "name": "MyJobName",
        "status": "completed",
        "results": {
            "documents": [
                {
                    "id": "doc1",
                    "classes": [
                        {
                            "category": "Class_1",
                            "confidenceScore": 0.0551877357
                        }
                    ],
                    "warnings": []
                },
                {
                    "id": "doc2",
                    "classes": [
                        {
                            "category": "Class_1",
                            "confidenceScore": 0.0551877357
                        },
                                                    {
                            "category": "Class_2",
                            "confidenceScore": 0.0551877357
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