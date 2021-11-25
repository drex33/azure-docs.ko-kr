---
title: 빠른 시작 - Azure Communication Services를 사용하여 Azure Logic Apps에서 SMS 메시지 보내기
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 Azure Communication Services 커넥터를 사용하여 Azure Logic Apps 워크플로에서 SMS 메시지를 보내는 방법을 알아봅니다.
author: tophpalmer
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: mode-other
ms.openlocfilehash: d890b6ae3a5c2265ae8ed108a74fda3a77997b19
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133048634"
---
# <a name="quickstart-send-sms-messages-in-azure-logic-apps-with-azure-communication-services"></a>빠른 시작: Azure Communication Services를 사용하여 Azure Logic Apps에서 SMS 메시지 보내기

[Azure Communication Services SMS](../../overview.md) 커넥터 및 [Azure Logic Apps](../../../logic-apps/logic-apps-overview.md)를 사용하면 SMS 메시지를 보낼 수 있는 자동화된 워크플로를 만들 수 있습니다. 이 빠른 시작에서는 논리 앱 워크플로의 첫 번째 단계인 트리거 이벤트에 응답하여 문자 메시지를 자동으로 보내는 방법을 보여 줍니다. 트리거 이벤트는 들어오는 이메일 메시지, 되풀이 일정, [Azure Event Grid](../../../event-grid/overview.md) 리소스 이벤트 또는 기타 [Azure Logic Apps에서 지원하는 트리거](/connectors/connector-reference/connector-reference-logicapps-connectors)일 수 있습니다.

:::image type="content" source="./media/logic-app/azure-communication-services-connector.png" alt-text="Logic Apps 디자이너가 열려 있는 Azure Portal에서 SMS 보내기 작업을 Azure Communication Services 커넥터에 사용하는 논리 앱의 예를 보여 주는 스크린샷입니다.":::

이 빠른 시작에서는 커넥터를 사용하여 트리거에 응답하는 데 중점을 두지만, 커넥터를 사용하여 워크플로에서 트리거를 따르는 단계인 다른 작업에 응답할 수도 있습니다. Logic Apps를 처음 접하는 경우 시작하기 전에 [Azure Logic Apps란?](../../../logic-apps/logic-apps-overview.md)을 검토하세요.

> [!NOTE]
> 이 빠른 시작을 완료하면 Azure 계정에서 USD 센트 이하의 작은 비용이 발생합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. 아직 없는 경우 [체험 Azure 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- 활성 Azure Communication Services 리소스. 아직 없는 경우 [Communication Services 리소스를 만듭니다](../create-communication-resource.md).

- 활성 Logic Apps 리소스(논리 앱). 아직 없는 경우 [비어 있는 논리 앱을 만들고 사용하려는 트리거를 포함합니다](../../../logic-apps/quickstart-create-first-logic-app-workflow.md). 현재 Azure Communication Services SMS 커넥터는 작업만 제공하므로 논리 앱에는 적어도 하나의 트리거가 필요합니다.

  이 빠른 시작에서는 [Office 365 Outlook 커넥터](/connectors/office365/)에서 사용할 수 있는 **새 이메일이 도착하는 경우** 트리거를 사용합니다.

- SMS 사용 전화 번호. 아직 없는 경우 [전화 번호를 가져옵니다](./get-phone-number.md).

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

## <a name="add-an-sms-action"></a>SMS 작업 추가

Azure Communication Services SMS 커넥터를 사용하여 워크플로의 새 단계로 **SMS 보내기** 작업을 추가하려면 Logic Apps 디자이너에서 논리 앱 워크플로가 열려 있는 [Azure Portal](https://portal.azure.com)에서 다음 단계를 수행합니다.

1. 새 작업을 추가하려는 디자이너 단계에서 **새 단계** 를 선택합니다. 또는 새 작업을 단계 사이에 추가하려면 포인터를 해당 단계 사이의 화살표 위로 이동하고, 더하기 기호( **+** )를 선택한 다음, **작업 추가** 를 선택합니다.

1. **작업 선택** 검색 상자에서 `Azure Communication Services`를 입력합니다. 작업 목록에서 **SMS 보내기** 를 선택합니다.

   :::image type="content" source="./media/logic-app/select-send-sms-action.png" alt-text="SMS 보내기 작업이 선택된 Logic Apps 디자이너 및 Azure Communication Services 커넥터를 보여 주는 스크린샷입니다.":::

1. 이제 Communication Services 리소스에 대한 연결을 만듭니다.
    1. 동일한 구독 내에서:

       1. 연결 이름을 제공합니다.

       1. Azure Communication Services 리소스를 선택합니다.

       1. **만들기** 를 선택합니다.

       :::image type="content" source="./media/logic-app/send-sms-configuration.png" alt-text="샘플 정보가 포함된 SMS 보내기 작업 구성을 보여 주는 스크린샷입니다.":::

    1. Communication Services 리소스의 연결 문자열 사용:
        
        1. 연결 이름을 제공합니다.
        
        1. 드롭다운 옵션에서 ConnectionString 인증을 선택합니다.
        
        1. Communication Services 리소스의 연결 문자열을 입력합니다.
        
        1. **만들기** 를 선택합니다.
        
        :::image type="content" source="./media/logic-app/connection-string-auth.png" alt-text="연결 문자열 인증 구성을 보여 주는 스크린샷":::
        
    1. 서비스 주체 사용([서비스 주체 만들기 참조](../identity/service-principal-from-cli.md)):
        1. 연결 이름을 제공합니다.
        
        1. 드롭다운 옵션에서 서비스 주체(Azure AD 애플리케이션) 인증을 선택합니다.
        
        1. 서비스 주체의 테넌트 ID, 클라이언트 ID 및 클라이언트 비밀을 입력합니다.
        
        1. Communication Services 리소스의 Communication Services 엔드포인트 URL 값을 입력합니다.
        
        1. **만들기** 를 선택합니다.
        
        :::image type="content" source="./media/logic-app/service-principal-auth.png" alt-text="서비스 주체 인증 구성을 보여 주는 스크린샷":::     

1. **SMS 보내기** 작업에서 다음 정보를 제공합니다. 

   * 원본 및 대상 전화 번호. 테스트를 위해 자신의 전화 번호를 대상 전화 번호로 사용할 수 있습니다.

   * 보내려는 메시지 내용(예: "Logic Apps에서 안녕하세요!")

   예제 정보가 포함된 **SMS 보내기** 작업은 다음과 같습니다.

   :::image type="content" source="./media/logic-app/send-sms-action.png" alt-text="샘플 정보가 포함된 SMS 보내기 작업을 보여 주는 스크린샷입니다.":::

1. 완료되면 디자이너 도구 모음에서 **저장** 을 선택합니다.

다음으로, 테스트를 위해 논리 앱 워크플로를 실행합니다.

## <a name="test-your-logic-app"></a>논리 앱 테스트

워크플로를 수동으로 시작하려면 디자이너 도구 모음에서 **실행** 을 선택합니다. 또는 트리거가 실행될 때까지 기다릴 수 있습니다. 두 경우 모두에서 워크플로는 SMS 메시지를 지정된 대상 전화 번호로 보내야 합니다. 자세한 내용은 [워크플로를 실행하는 방법](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#run-workflow)을 검토하세요.

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 제거하려면 Communication Services 리소스 또는 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 해당 그룹의 다른 리소스도 삭제됩니다. 자세한 내용은 [Communication Services 리소스를 정리하는 방법](../create-communication-resource.md#clean-up-resources)을 검토하세요.

논리 앱 워크플로 및 관련 리소스를 정리하려면 [Logic Apps 리소스를 정리하는 방법](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#clean-up-resources)을 검토하세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Logic Apps 및 Azure Communication Services를 사용하여 SMS 메시지를 보내는 방법을 알아보았습니다. 자세히 알아보려면 SMS 이벤트 구독으로 계속 진행합니다.

> [!div class="nextstepaction"]
> [SMS 이벤트 구독](./handle-sms-events.md)

Azure Communication Services의 SMS에 대한 자세한 내용은 다음 문서를 참조하세요.

- [SMS 개념](../../concepts/telephony-sms/concepts.md)
- [전화 번호 유형](../../concepts/telephony-sms/plan-solution.md)
- [SMS SDK](../../concepts/telephony-sms/sdk-features.md)
