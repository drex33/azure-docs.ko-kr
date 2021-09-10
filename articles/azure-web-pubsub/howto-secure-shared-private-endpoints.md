---
title: 공유 프라이빗 엔드포인트를 통해 Azure Web PubSub 아웃바운드 트래픽 보호
titleSuffix: Azure Web PubSub Service
description: 공용 네트워크로 이동하는 트래픽을 방지하기 위해 공유 프라이빗 엔드포인트를 통해 Azure Web PubSub 아웃바운드 트래픽을 보호하는 방법
author: ArchangelSDY
ms.service: azure-web-pubsub
ms.topic: article
ms.date: 07/13/2021
ms.author: dayshen
ms.openlocfilehash: 2f48b2b0f21c389df30cc11d79a919cdf94317a7
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342430"
---
# <a name="secure-azure-web-pubsub-outbound-traffic-through-shared-private-endpoints"></a>공유 프라이빗 엔드포인트를 통해 Azure Web PubSub 아웃바운드 트래픽 보호

Azure Web PubSub 서비스에서 [이벤트 처리기](https://azure.github.io/azure-webpubsub/concepts/service-internals#event_handler)를 사용하는 경우 업스트림에 아웃바운드 트래픽이 있을 수 있습니다. 가상 네트워크 목록에서 연결을 허용하고 공용 네트워크에서 시작되는 외부 연결을 거부하도록 Azure Web App 및 Azure Functions 같은 업스트림을 구성할 수 있습니다. 이러한 엔드포인트에 도달하는 아웃바운드 [프라이빗 엔드포인트 연결](../private-link/private-endpoint-overview.md)을 만들 수 있습니다.

   :::image type="content" alt-text="공유 프라이빗 엔드포인트 개요." source="media\howto-secure-shared-private-endpoints\shared-private-endpoint-overview.png" border="false" :::

이 아웃바운드 방법은 다음 요구 사항을 충족해야 합니다.

+ 업스트림이 Azure Web App 또는 Azure Function이어야 합니다.

+ Azure Web PubSub Service 서비스는 표준 계층에 있어야 합니다.

+ Azure Web App 또는 Azure Function은 특정 SKU에 있어야 합니다. [Azure Web App용 프라이빗 엔드포인트 사용](../app-service/networking/private-endpoint.md)을 참조하세요.

## <a name="shared-private-link-resources-management-apis"></a>공유 프라이빗 링크 리소스 관리 API

Azure Web PubSub Service API를 통해 만들어진 보안 리소스의 프라이빗 엔드포인트를 *공유 프라이빗 링크 리소스* 라고 합니다. 이는 [Azure Private Link 서비스](https://azure.microsoft.com/services/private-link/)와 통합된 Azure Function처럼 리소스에 대한 액세스를 ‘공유’하기 때문입니다. 이러한 프라이빗 엔드포인트는 Azure Web PubSub Service 실행 환경 내에서 만들어지며, 사용자에게 직접 표시되지 않습니다.

현재는 관리 REST API를 사용하여 *공유 프라이빗 링크 리소스* 를 만들거나 삭제할 수 있습니다. 이 문서의 나머지 부분에서는 [Azure CLI](/cli/azure/)를 사용하여 REST API 호출을 설명합니다.

> [!NOTE]
> 이 문서의 예는 다음과 같은 가정을 기반으로 합니다.
> * 이 Azure Web PubSub Service의 리소스 ID는 _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/webPubSub/contoso-webpubsub입니다.
> * 업스트림 Azure Function의 리소스 ID는 _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func입니다.

예제의 나머지 부분에서는 _contoso-webpubsub_ 서비스를 구성하여 해당 업스트림 호출이 공용 네트워크가 아닌 프라이빗 엔드포인트를 통과할 수 있도록 하는 방법을 보여 줍니다.

### <a name="step-1-create-a-shared-private-link-resource-to-the-function"></a>1단계: Function에 대한 공유 프라이빗 링크 리소스 만들기

[Azure CLI](/cli/azure/)로 다음 API 호출을 수행하여 공유 프라이빗 링크 리소스를 만들 수 있습니다.

```dotnetcli
az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/webPubSub/contoso-webpubsub/sharedPrivateLinkResources/func-pe?api-version=2021-06-01-preview --body @create-pe.json
```

API에 대한 요청 본문을 나타내는 *create-pe.js* 파일의 내용은 다음과 같습니다.

```json
{
      "name": "func-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func",
        "groupId": "sites",
        "requestMessage": "please approve"
      }
}
```

아웃바운드 프라이빗 엔드포인트를 만드는 프로세스는 장기 실행(비동기) 작업입니다. 모든 비동기 Azure 작업과 마찬가지로 `PUT` 호출은 다음과 같은 `Azure-AsyncOperation` 헤더 값을 반환합니다.

```plaintext
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/webPubSub/contoso-webpubsub/operationStatuses/c0786383-8d5f-4554-8d17-f16fcf482fb2?api-version=2021-06-01-preview"
```

이 URI를 주기적으로 폴링하여 작업 상태를 가져올 수 있습니다.

CLI를 사용하는 경우 `Azure-AsyncOperationHeader` 값을 수동으로 쿼리하면 상태를 폴링할 수 있습니다.

```donetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/webPubSub/contoso-webpubsub/operationStatuses/c0786383-8d5f-4554-8d17-f16fcf482fb2?api-version=2021-06-01-preview
```

다음 단계로 진행하기 전에 상태가 ‘성공’으로 변경될 때까지 기다립니다.

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-function"></a>2a단계: Function에 대한 프라이빗 엔드포인트 연결 승인

> [!NOTE]
> 이 섹션에서는 Azure Portal을 사용하여 프라이빗 엔드포인트에서 Azure Function으로 이동하는 승인 흐름을 안내합니다. 또는 App Service 공급자를 통해 사용할 수 있는 [REST API](/rest/api/appservice/web-apps/approve-or-reject-private-endpoint-connection)를 사용할 수 있습니다.

> [!IMPORTANT]
> 프라이빗 엔드포인트 연결을 승인한 후에는 공용 네트워크에서 Function에 더 이상 액세스할 수 없습니다. Function 엔드포인트에 액세스하려면 자체 가상 네트워크에서 다른 프라이빗 엔드포인트를 만들어야 할 수도 있습니다.

1. Azure Portal에서 함수 앱의 **네트워킹** 탭을 선택하고 **프라이빗 엔드포인트 연결** 로 이동합니다. **프라이빗 엔드포인트 연결 구성** 을 클릭합니다. 비동기 작업이 성공한 후에는 이전 API 호출의 요청 메시지와 함께 프라이빗 엔드포인트 연결에 대한 요청이 있어야 합니다.

   :::image type="content" alt-text="프라이빗 엔드포인트 연결 창을 보여 주는 Azure Portal의 스크린샷." source="media\howto-secure-shared-private-endpoints\portal-function-approve-private-endpoint.png" lightbox="media\howto-secure-shared-private-endpoints\portal-function-approve-private-endpoint.png" :::

1. Azure Web PubSub Service에서 만든 프라이빗 엔드포인트를 선택합니다. **프라이빗 엔드포인트** 열에서 이전 API에 지정된 이름으로 프라이빗 엔드포인트 연결을 식별하고 **승인** 을 선택합니다.

   다음 스크린샷과 같이 프라이빗 엔드포인트 연결이 표시되는지 확인합니다. 포털에서 상태를 업데이트하는 데 1~2분 정도 걸릴 수 있습니다.

   :::image type="content" alt-text="프라이빗 엔드포인트 연결 창의 승인됨 상태를 보여 주는 Azure Portal의 스크린샷." source="media\howto-secure-shared-private-endpoints\portal-function-approved-private-endpoint.png" lightbox="media\howto-secure-shared-private-endpoints\portal-function-approved-private-endpoint.png" :::

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>2b단계: 공유 프라이빗 링크 리소스의 상태 쿼리

승인이 Azure Web PubSub Service로 전파되는 데 몇 분 정도 걸립니다. 승인 후 공유 프라이빗 링크 리소스가 업데이트되었는지 확인하기 위해 GET API를 사용하여 ‘연결 상태’도 가져올 수 있습니다.

```dotnetcli
az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.SignalRService/webPubSub/contoso-webpubsub/sharedPrivateLinkResources/func-pe?api-version=2021-06-01-preview
```

그러면 연결 상태가 “속성” 섹션 아래에 “상태”로 표시되는 JSON이 반환됩니다.

```json
{
      "name": "func-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Web/sites/contoso-func",
        "groupId": "sites",
        "requestMessage": "please approve",
        "status": "Approved",
        "provisioningState": "Succeeded"
      }
}

```

리소스의 ‘프로비저닝 상태’(`properties.provisioningState`)가 `Succeeded`이고 ‘연결 상태’(`properties.status`)가 `Approved`이면 공유 프라이빗 링크 리소스가 작동하고 Azure Web PubSub Service가 프라이빗 엔드포인트를 통해 통신할 수 있음을 의미합니다.

### <a name="step-3-verify-upstream-calls-are-from-a-private-ip"></a>3단계: 개인 IP에서 업스트림 호출 확인

프라이빗 엔드포인트가 설정되면 업스트림 쪽에서 `X-Forwarded-For` 헤더를 확인하여 개인 IP에서 들어오는 호출을 확인할 수 있습니다.

:::image type="content" alt-text="개인 IP에서 들어오는 요청을 보여 주는 Azure Portal의 스크린샷." source="media\howto-secure-shared-private-endpoints\portal-function-log.png" :::

## <a name="next-steps"></a>다음 단계

프라이빗 엔드포인트에 대해 자세히 알아보세요.

+ [프라이빗 엔드포인트란 무엇인가요?](../private-link/private-endpoint-overview.md)