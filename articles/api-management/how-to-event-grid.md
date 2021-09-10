---
title: Azure API Management에서 Event Grid로 이벤트 보내기
description: 이 빠른 시작에서는 Azure API Management 인스턴스에 대해 Event Grid 이벤트를 사용하도록 설정한 다음, 이벤트를 샘플 애플리케이션으로 보냅니다.
author: dlepow
ms.topic: how-to
ms.service: api-management
ms.author: danlep
ms.date: 07/12/2021
ms.custom: ''
ms.openlocfilehash: 9e81fbe26c8b98a0694789567cef9126d5ca02fc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567218"
---
# <a name="send-events-from-api-management-to-event-grid-preview"></a>API Management에서 Event Grid로 이벤트 보내기(미리 보기)

API Management는 이벤트 알림을 다른 서비스로 보내고 다운스트림 프로세스를 트리거할 수 있도록 [Azure Event Grid](../event-grid/overview.md)와 통합됩니다. Event Grid는 게시-구독 모델을 사용하는 완전 관리형 이벤트 라우팅 서비스입니다. Event Grid에는 [Azure Functions](../azure-functions/functions-overview.md) 및 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)와 같은 Azure 서비스에 대한 기본 제공 지원이 있어 웹후크를 사용하여 외부 Azure 서비스에 이벤트 경고를 제공할 수 있습니다.

예를 들어 Event Grid와의 통합을 사용하여 데이터베이스를 업데이트하고, 청구 계정을 만들고, 사용자가 API Management 인스턴스에 추가될 때마다 이메일 알림을 보내는 애플리케이션을 빌드할 수 있습니다.

이 문서에서는 API Management 인스턴스의 Event Grid 이벤트를 구독하고, 이벤트를 트리거하고, 데이터를 처리하는 엔드포인트로 이벤트를 보냅니다. 간단하게 유지하려면 메시지를 수집하고 표시하는 샘플 웹앱에 이벤트를 보냅니다.

:::image type="content" source="media/how-to-event-grid/event-grid-viewer-intro.png" alt-text="Event Grid 뷰어의 API Management 이벤트":::

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
- API Management 서비스가 아직 없는 경우 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md) 빠른 시작을 완료합니다.
- API Management 인스턴스에서 [시스템 할당 관리 ID](api-management-howto-use-managed-service-identity.md#create-a-system-assigned-managed-identity)를 사용하도록 설정합니다.
- 샘플 엔드포인트를 배포할 리소스 그룹이 없는 경우 [리소스 그룹](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)을 만듭니다.

## <a name="create-an-event-endpoint"></a>이벤트 엔드포인트 만들기

이 섹션에서는 Resource Manager 템플릿을 사용하여 Azure App Service에 미리 작성된 샘플 웹 애플리케이션을 배포합니다. 나중에 API Management 인스턴스의 Event Grid 이벤트를 구독하고 이벤트가 전송되는 엔드포인트로 이 앱을 지정합니다.

샘플 앱을 배포하려면 Azure CLI, Azure PowerShell 또는 Azure Portal을 사용하면 됩니다. 다음 예제에서는 Azure CLI에서 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) 명령을 사용합니다.

* `RESOURCE_GROUP_NAME`을 기존 리소스 그룹의 이름으로 설정합니다.
* `SITE_NAME`을 웹앱에 대한 고유한 이름으로 설정합니다.

  사이트 이름은 웹앱의 FQDN(정규화된 도메인 이름)의 일부를 형성하기 때문에 Azure 내에서 고유해야 합니다. 이후 섹션에서 이벤트를 보려면 웹 브라우저에서 앱의 FQDN으로 이동합니다.

```azurecli-interactive
RESOURCE_GROUP_NAME=<your-resource-group-name>
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

배포에 성공하면(몇 분 정도 걸릴 수 있음) 브라우저를 열고 웹앱으로 이동하여 실행 중인지 확인합니다.

`https://<your-site-name>.azurewebsites.net`

이벤트 메시지가 표시되지 않고 렌더링된 샘플 앱이 표시되어야 합니다.

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-api-management-events"></a>API Management 이벤트 구독

Event Grid에서 *항목* 을 구독하여 추적하려는 이벤트와 이벤트를 보낼 위치를 알립니다. 여기에서 API Management 인스턴스의 이벤트에 대한 구독을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.
1. **이벤트(미리 보기) > + 이벤트 구독** 을 선택합니다. 
1. **기본** 탭에서
    * 이벤트 구독에 대한 설명이 포함된 **이름** 을 입력합니다.
    * **이벤트 유형** 에서 하나 이상의 API Management 이벤트 유형을 선택하여 Event Grid에 보냅니다. 이 문서의 예제에서는 최소한 **Microsoft.APIManagement.ProductCreated** 를 선택합니다. 
    * **엔드포인트 세부 정보** 에서 **Web Hook** 이벤트 유형을 선택하고 **엔드포인트 선택** 을 클릭한 다음, 웹앱 URL과 `api/updates`를 차례로 입력합니다. 예: `https://myapp.azurewebsites.net/api/updates`.
    * **선택 확인** 을 선택합니다.
1. 나머지 탭의 설정을 해당 기본값으로 그대로 두고 **만들기** 를 선택합니다.

    :::image type="content" source="media/how-to-event-grid/create-event-subscription.png" alt-text="Azure Portal에서 이벤트 구독 만들기":::

## <a name="trigger-and-view-events"></a>트리거 및 뷰 이벤트

샘플 앱이 작동 중이고 Event Grid를 사용하여 API Management 인스턴스를 구독했으므로 이제 이벤트를 생성할 수 있습니다.

예를 들어 API Management 인스턴스에서 [제품을 만듭니다](./api-management-howto-add-products.md). 이벤트 구독에 **Microsoft.APIManagement.ProductCreated** 이벤트가 포함된 경우 제품을 만들면 웹앱 엔드포인트로 푸시되는 이벤트가 트리거됩니다. 

Event Grid 뷰어 웹앱으로 이동하면 `ProductCreated` 이벤트가 표시되어야 합니다. 이벤트 옆의 단추를 선택하여 세부 정보를 표시합니다. 

:::image type="content" source="media/how-to-event-grid/event-grid-viewer-product-created.png" alt-text="Event Grid 뷰어의 제품 만들기 이벤트":::

## <a name="event-grid-event-schema"></a>Event Grid 이벤트 스키마

API Management 이벤트 데이터에는 이벤트를 트리거한 API Management 리소스를 식별하는 `resourceUri`가 포함됩니다. API Management 이벤트 메시지 스키마에 대한 자세한 내용은 Event Grid 설명서를 참조하세요.

[API Management에 대한 Azure Event Grid 이벤트 스키마](../event-grid/event-schema-api-management.md)

## <a name="next-steps"></a>다음 단계

* [세 가지 Azure 메시징 서비스인 Event Grid, Event Hubs 및 Service Bus 중에서 선택합니다.](../event-grid/compare-messaging-services.md)
* [이벤트 구독](../event-grid/subscribe-through-portal.md)에 대해 자세히 알아보세요.