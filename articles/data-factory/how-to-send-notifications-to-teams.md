---
title: Microsoft Teams 채널에 알림을 보내는 방법
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Synapse Analytics 파이프라인에서 Microsoft Teams 채널로 알림을 보내는 방법에 대해 알아봅니다.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.custom: synapse
ms.topic: how-to
ms.subservice: tutorials
ms.date: 09/29/2021
ms.openlocfilehash: d1a23e166c322a4a74c3ec175ea672d78ed23de9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372876"
---
# <a name="send-notifications-to-a-microsoft-teams-channel-from-an-azure-data-factory-or-synapse-analytics-pipeline"></a>Azure Data Factory 또는 Synapse Analytics 파이프라인에서 Microsoft Teams 채널로 알림 보내기

파이프라인을 실행 중 또는 실행한 후에 알림을 보내야 하는 경우가 많습니다. 알림은 사전 경고를 제공하고 문제를 검색하기 위한 사후 모니터링의 필요성을 줄입니다.  데이터 팩터리 또는 Synapse 파이프라인이 호출할 수 있는 [논리 앱을 사용하여 이메일 알림을 보내는 방법을](tutorial-control-flow-portal.md#create-email-workflow-endpoints) 알아볼 수 있습니다.  또한 많은 기업에서 협업에 Microsoft Teams 점점 더 많이 사용하고 있습니다.  이 문서에서는 파이프라인 경고에서 Microsoft Teams 알림을 구성하는 방법을 보여줍니다. 

## <a name="prerequisites"></a>필수 구성 요소

파이프라인에서 Teams 알림을 보내려면 먼저 Teams 채널에 대한 [들어오는 Webhook를](/microsoftteams/platform/webhooks-and-connectors/how-to/connectors-using) 만들어야 합니다. 이 목적을 위해 새 Teams 채널을 만들어야 하는 경우 [Teams 설명서를 참조하세요.](https://support.microsoft.com/office/create-a-channel-in-teams-fda0b75e-5b90-4fb8-8857-7e102b014525)  

1.  Microsoft Teams 열고 앱 탭으로 이동합니다.  "들어오는 Webhook"를 검색하고 들어오는 Webhook 커넥터를 선택합니다.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-incoming-webhook-connector.png" alt-text="Teams 앱 탭 아래에 들어오는 Webhook 앱을 표시합니다.":::

1.  알림을 보내려는 Teams 사이트에 커넥터를 추가합니다.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-add-connector-to-site.png" alt-text="&quot;들어오는 Webhook 앱에 대한 팀에 추가 단추를 강조 &quot; 표시합니다.":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-prod-notifications.png" alt-text="Teams 들어오는 Webhook 앱 구성 대화 상자에서 팀 선택 프롬프트를 표시합니다.":::

1.  Webhook 이름을 적절하게 지정하고 필요에 따라 아이콘을 업로드하여 메시지를 식별합니다.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-add-icon.png" alt-text="들어오는 웹후크 옵션 페이지에서 이름 속성, 선택적 이미지 업로드 및 만들기 단추를 강조 &quot; &quot; 표시합니다.":::  

1.  나중에 ADF에서 사용하기 위해 생성될 때 생성되는 웹후크 URL의 저장소를 복사합니다.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-copy-webhook-url.png" alt-text="만든 후 들어오는 웹후크 옵션 페이지에서 새 웹후크 URL을 표시합니다.":::

1.  webhook 커넥터를 추가하는 채널에서 알림을 볼 수 있습니다.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-channel-notification.png" alt-text="webhook 커넥터를 추가한 Teams 채널에 알림을 표시합니다.":::
        
## <a name="steps-to-send-notifications-on-teams-channel-from-a-pipeline"></a>파이프라인에서 Teams 채널에 알림을 보내는 단계:

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

1.  템플릿 에서 새 **파이프라인을** 만듭니다. 템플릿 갤러리는 팀 알림을 쉽게 시작할 수 있는 파이프라인 템플릿을 제공합니다.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/pipeline-from-template.png" alt-text="Azure Data Factory &quot; &quot; Studio의 템플릿에서 파이프라인 메뉴를 표시합니다.":::

1.  "teams"를 검색한 다음, **Microsoft Teams 템플릿에서 채널에 알림 보내기를** 선택하고 사용합니다.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-dialog.png" alt-text="템플릿 &quot; 갤러리의 Microsoft Teams &quot; 템플릿에서 채널에 알림 보내기를 표시합니다.":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-template.png" alt-text="템플릿 &quot; &quot; 갤러리에서 선택한 후 Microsoft Teams 템플릿 세부 정보에서 채널에 알림 보내기를 표시합니다.":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-webhook-properties.png" alt-text="Microsoft Teams 템플릿의 채널에 알림 보내기에서 만든 파이프라인의 속성을 &quot; &quot; 표시합니다.":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)

1.  템플릿 에서 새 **파이프라인을** 만듭니다. 템플릿 갤러리는 팀 알림을 쉽게 시작할 수 있는 파이프라인 템플릿을 제공합니다.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/pipeline-from-template-synapse.png" alt-text="Azure Data Factory &quot; &quot; Studio의 템플릿에서 파이프라인 메뉴를 표시합니다.":::

1.  "teams"를 검색한 다음, **Microsoft Teams 템플릿에서 채널에 알림 보내기를** 선택하고 사용합니다.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-dialog-synapse.png" alt-text="템플릿 &quot; 갤러리의 Microsoft Teams &quot; 템플릿에서 채널에 알림 보내기를 표시합니다.":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/send-notification-template-synapse.png" alt-text="템플릿 &quot; &quot; 갤러리에서 선택한 후 Microsoft Teams 템플릿 세부 정보에서 채널에 알림 보내기를 표시합니다.":::
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-webhook-properties.png" alt-text="Microsoft Teams 템플릿의 채널에 알림 보내기에서 만든 파이프라인의 속성을 &quot; &quot; 표시합니다.":::

---

3.  관련 매개 변수의 기본값은 현재 Data Factory **구독 ID,** **리소스 그룹** 및 **Teams 웹후크 URL(필수** 구성 [요소](#prerequisites)참조)을 추가하는 것이 좋습니다.
    
    :::image type="content" source="media/how-to-send-notifications-to-teams/webhook-recommended-properties.png" alt-text="Microsoft Teams 템플릿의 채널에 알림 보내기에서 만든 파이프라인의 권장 속성을 &quot; &quot; 표시합니다.":::

    이러한 매개 변수는 모니터링 URL을 구성하는 데 사용됩니다. 파이프라인이 속한 동일한 데이터 팩터리에서 유효한 구독 및 리소스 그룹을 제공하지 않는다고 가정합니다. 이 경우 알림에 유효한 파이프라인 모니터링 URL이 포함되지 않지만 메시지는 계속 작동합니다.  또한 이러한 매개 변수를 추가하면 항상 다른 파이프라인에서 해당 값을 전달할 필요가 없습니다. 메타데이터 기반 접근 방식을 통해 해당 값을 제어하려면 그에 따라 값을 수정해야 합니다.
    
1.  파이프라인 **실행** 활동을 Teams 채널에서 알림을 보낼 파이프라인에 추가합니다. Microsoft Teams 템플릿의 **채널에 알림 보내기에서** 생성된 파이프라인을 **파이프라인 실행** 활동의 **호출된 파이프라인으로** 선택합니다.

     :::image type="content" source="media/how-to-send-notifications-to-teams/execute-pipeline-activity.png" alt-text="Microsoft Teams &quot; &quot; 템플릿의 채널에 알림 보내기에서 만든 파이프라인의 파이프라인 실행 활동을 &quot; &quot; 표시합니다.":::

1.  작업 유형에 따라 필요에 따라 매개 변수를 사용자 지정합니다.

    :::image type="content" source="media/how-to-send-notifications-to-teams/customize-parameters-by-activity-type.png" alt-text="Microsoft Teams 템플릿의 채널에 알림 보내기에서 만든 파이프라인의 매개 변수 사용자 지정을 &quot; &quot; 표시합니다.":::   
  
1.  Teams 알림을 받습니다.

    :::image type="content" source="media/how-to-send-notifications-to-teams/teams-notifications-view-pipeline-run.png" alt-text="Teams 채널에서 파이프라인 알림을 표시합니다.":::
## <a name="add-dynamic-messages-with-system-variables-and-expressions"></a>시스템 변수 및 식을 통해 동적 메시지 추가

[시스템 변수 및 식을](control-flow-system-variables.md) 사용하여 메시지를 동적으로 만들 수 있습니다. [](control-flow-expression-language-functions.md) 예를 들면 다음과 같습니다.  

-   ``@activity("CopyData").output.errors[0].Message``

-   ``@activity("DataFlow").error.Message``

위의 식은 Teams 채널에서 알림으로 보낼 수 있는 오류로부터 관련 오류 메시지를 반환합니다. 자세한 내용은 [출력 속성 복사 작업](copy-activity-monitoring.md) 문서를 참조하세요.

또한 Microsoft Teams 지원되는 [알림 페이로드 스키마를](https://adaptivecards.io/explorer/AdaptiveCard.html) 검토하고 위의 템플릿을 필요에 맞게 추가로 사용자 지정하는 것이 좋습니다.
