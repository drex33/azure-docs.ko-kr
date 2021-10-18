---
title: 이메일을 보내는 방법
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Azure Synapse 파이프라인을 사용하여 이메일을 보내는 방법을 알아봅니다.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 06/07/2021
ms.openlocfilehash: 64f7fcf5cfd5c24cb8d34e29572f7fbb1314414a
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668906"
---
# <a name="send-an-email-with-an-azure-data-factory-or-azure-synapse-pipeline"></a>Azure Data Factory 또는 Azure Synapse 파이프라인을 사용하여 이메일 보내기

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

파이프라인을 실행 중 또는 실행 후에 알림을 보내야 하는 경우가 많습니다. 알림은 사전 경고를 제공하고 문제를 발견하기 위한 사후 모니터링의 필요성을 줄여줍니다.  이 문서에서는 Azure Data Factory 또는 Azure Synapse 파이프라인에서 이메일 알림을 구성하는 방법을 보여줍니다. 

## <a name="prerequisites"></a>사전 요구 사항

- **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
- **논리 앱**. 파이프라인에서 전자 메일 보내기를 트리거하려면 [Logic Apps](../logic-apps/logic-apps-overview.md)를 사용하여 워크플로를 정의합니다. 논리 앱 워크플로를 만드는 방법에 대한 자세한 내용은 [논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 참조하세요.

## <a name="create-the-email-workflow-in-your-logic-app"></a>논리 앱에서 이메일 워크플로 만들기

`SendEmailFromPipeline`이라는 논리 앱 워크플로를 만듭니다. 워크플로 트리거를 `When an HTTP request is received`로 정의하고 `Office 365 Outlook – Send an email (V2)` 작업을 추가합니다.

:::image type="content" source="media/how-to-send-email/logic-app-workflow-designer.png" alt-text="이메일 보내기(V2)를 사용하여 논리 앱 워크플로 디자이너를 표시합니다. HTTP 요청 트리거의 동작입니다.":::

HTTP 요청 트리거의 경우 `Request Body JSON Schema`에 대해 다음 JSON을 제공합니다.

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

논리 앱 디자이너의 HTTP 요청은 다음과 같아야 합니다.

:::image type="content" source="media/how-to-send-email/logic-app-http-request-trigger.png" alt-text="요청 본문 JSON 스키마 필드가 채워진 HTTP 요청 트리거에 대한 논리 앱 워크플로 디자이너를 표시합니다.":::

**이메일 보내기(V2)** 작업의 경우 요청 본문 JSON 스키마의 속성을 사용하여 이메일의 형식을 지정하는 방법을 사용자 지정합니다.

:::image type="content" source="media/how-to-send-email/logic-app-email-action.png" alt-text="이메일 보내기(V2) 작업에 대한 논리 앱 워크플로 디자이너를 표시합니다.":::

워크플로를 저장합니다. 새 워크플로에 대한 워크플로 URL을 기록한 후, 다음을 수행합니다.

:::image type="content" source="media/how-to-send-email/logic-app-workflow-url.png" alt-text="워크플로 URL이 강조 표시된 논리 앱 워크플로 개요 탭을 표시합니다.":::

## <a name="create-a-pipeline-to-trigger-your-logic-app-email-workflow"></a>논리 앱 이메일 워크플로를 트리거하는 파이프라인 만들기

이메일을 보내는 논리 앱 워크플로를 만든 후에는 **웹** 활동을 사용하여 파이프라인에서 트리거할 수 있습니다.  

1. 새 파이프라인을 만들고 **일반** 범주에서 **웹** 활동을 찾아 편집 캔버스로 끌어옵니다.

1. 새 **Web1** 활동을 선택한 다음, **설정** 탭을 선택합니다.

   **URL** 필드에서 이전에 만든 논리 앱 워크플로의 URL을 제공합니다.

   **본문** 에 다음 JSON을 제공합니다.
    ```json
       {
        "message" : "This is a custom dynamic message from your pipeline with run ID @{pipeline().RunId}.",
        "dataFactoryName" : "@{pipeline().DataFactory}", 
        "pipelineName" : "@{pipeline().Pipeline}", 
        "receiver" : "@{pipeline().parameters.receiver}"
       }
    ```
    
    동적 식을 사용하여 파이프라인의 이벤트에 대한 유용한 메시지를 생성합니다.  여기서 JSON 형식은 논리 앱에서 정의한 JSON 형식과 일치하며, 필요에 따라 사용자 지정할 수도 있습니다.
    
    :::image type="content" source="media/how-to-send-email/pipeline-with-web-activity-calling-logic-app.png" alt-text="논리 앱 워크플로 URL 및 JSON 메시지 본문으로 구성된 웹 작업을 사용하여 파이프라인을 표시합니다.":::

1. 파이프라인 디자이너의 배경 영역을 선택하여 파이프라인 속성 페이지를 선택하고 수신자라는 새 매개 변수를 추가하여 이메일 주소를 기본값으로 제공합니다.
   
   이 예제에서는 임의로 정의한 파이프라인 매개 변수에서 수신자 이메일을 제공합니다.  수신자 값은 모든 식 또는 연결된 데이터 원본에서 가져올 수 있습니다.

   :::image type="content" source="media/how-to-send-email/pipeline-receiver-email-parameter.png" alt-text="파이프라인 디자이너에서 수신자 매개 변수의 구성을 보여줍니다.":::

1. 파이프라인을 게시한 다음, 수동으로 트리거하여 이메일이 예상대로 전송되는지 확인합니다.

   :::image type="content" source="media/how-to-send-email/pipeline-manually-trigger.png" alt-text="파이프라인을 수동으로 트리거하는 방법을 보여줍니다."::: 

## <a name="add-dynamic-messages-with-system-variables-and-expressions"></a>시스템 변수 및 식을 사용하여 동적 메시지 추가

[시스템 변수](control-flow-system-variables.md) 및 [식](control-flow-expression-language-functions.md)을 사용하여 메시지를 동적으로 만들 수 있습니다. 예를 들어:  

-   ``@activity("CopyData").output.errors[0].Message``

-   ``@activity("DataFlow").error.Message``

위의 식은 복사 작업 실패에서 관련 오류 메시지를 반환합니다. 이 메시지는 이메일을 전송하는 웹 작업으로 리디렉션될 수 있습니다. 자세한 내용은 [복사 작업 출력 속성](copy-activity-monitoring.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

[파이프라인에서 Teams 알림을 보내는 방법](how-to-send-notifications-to-teams.md)
