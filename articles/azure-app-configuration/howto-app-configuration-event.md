---
title: App Configuration 데이터 변경 알림에 Event Grid 사용
description: Azure App Configuration 이벤트 구독을 사용하여 키-값 수정 이벤트를 웹 엔드포인트로 보내는 방법에 대해 알아보기
services: azure-app-configuration
author: AlexandraKemperMS
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 03/04/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: cc9acfdbfc88c0631a66717f8af0dd6eb7b98afc
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257217"
---
# <a name="use-event-grid-for-app-configuration-data-change-notifications"></a>App Configuration 데이터 변경 알림에 Event Grid 사용

이 문서에서는 웹 엔드포인트에 키-값 수정 이벤트를 보내도록 Azure App Configuration 이벤트 구독을 설정하는 방법을 알아봅니다. Azure App Configuration 사용자는 키-값이 수정될 때마다 내보내지는 이벤트를 구독할 수 있습니다. 해당 이벤트는 웹후크, Azure Functions, Azure Storage Queues 또는 Azure Event Grid에서 지원하는 다른 이벤트 처리기를 트리거할 수 있습니다. 일반적으로 이벤트 데이터를 처리하고 작업을 수행하는 엔드포인트에 이벤트를 보냅니다. 그러나 이 문서를 간소화하기 위해 메시지를 수집하고 표시하는 웹앱에 이벤트를 보냅니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/). Azure Cloud Shell을 사용할 수도 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서는 최신 버전의 Azure CLI(2.0.70 이상)를 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

Cloud Shell을 사용하지 않는 경우 먼저 `az login`을 사용하여 로그인해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Event Grid 토픽은 Azure 리소스이며 Azure 리소스 그룹에 배치해야 합니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

[az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만듭니다. 

다음 예에서는 *westus* 위치에 `<resource_group_name>`이라는 리소스 그룹을 만듭니다.  `<resource_group_name>`을 리소스 그룹의 고유한 이름으로 바꿉니다.

```azurecli-interactive
az group create --name <resource_group_name> --location westus
```

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

`<appconfig_name>`을 내 구성 저장소의 고유한 이름으로 바꾸고, `<resource_group_name>`을 이전에 만든 리소스 그룹으로 바꿉니다. 이름은 DNS 이름으로 사용되므로 고유해야 합니다.

```azurecli-interactive
az appconfig create \
  --name <appconfig_name> \
  --location westus \
  --resource-group <resource_group_name> \
  --sku free
```

## <a name="create-a-message-endpoint"></a>메시지 엔드포인트 만들기

토픽을 구독하기 전에 이벤트 메시지에 대한 엔드포인트를 만들어 보겠습니다. 일반적으로 엔드포인트는 이벤트 데이터를 기반으로 작업을 수행합니다. 이 빠른 시작을 간소화하기 위해 이벤트 메시지를 표시하는 [미리 작성된 웹앱](https://github.com/Azure-Samples/azure-event-grid-viewer)을 배포합니다. 배포된 솔루션은 App Service 계획, App Service 웹앱 및 GitHub의 소스 코드를 포함합니다.

`<your-site-name>`을 웹앱의 고유한 이름으로 바꿉니다. 웹앱 이름은 DNS 항목의 일부이므로 고유해야 합니다.

```azurecli-interactive
$sitename=<your-site-name>

az deployment group create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 배포가 성공된 후 실행하는지 확인하려면 웹앱을 봅니다. 웹 브라우저에서 `https://<your-site-name>.azurewebsites.net`으로 이동합니다.

현재 표시된 메시지가 없는 사이트가 표시되어야 합니다.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store"></a>App Configuration 저장소 구독

항목을 구독하여 Event Grid에 추적하려는 이벤트와 해당 이벤트를 보낼 위치를 알립니다. 다음 예제에서는 앞에서 만든 App Configuration을 구독하고, 웹앱의 URL을 이벤트 알림에 대한 엔드포인트로 전달합니다. `<event_subscription_name>`을 이벤트 구독의 이름으로 바꿉니다. `<resource_group_name>` 및 `<appconfig_name>`에는 앞에서 만든 값을 사용합니다.

웹앱에 대한 엔드포인트는 접미사 `/api/updates/`를 포함해야 합니다.

```azurecli-interactive
appconfigId=$(az appconfig show --name <appconfig_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

웹앱을 다시 확인하고, 구독 유효성 검사 이벤트를 보냈음을 확인합니다. 눈 모양 아이콘을 선택하여 이벤트 데이터를 확장합니다. Event Grid는 유효성 검사 이벤트를 보내므로 엔드포인트는 이벤트 데이터를 수신하려는 것을 확인할 수 있습니다. 웹앱은 구독의 유효성을 검사하는 코드를 포함합니다.

![구독 이벤트 보기](./media/quickstarts/event-grid/view-subscription-event.png)

## <a name="trigger-an-app-configuration-event"></a>App Configuration 이벤트 트리거

이제 이벤트를 트리거하여 Event Grid가 메시지를 사용자 엔드포인트에 어떻게 배포하는지 살펴 보겠습니다. 앞에서 나온 `<appconfig_name>`을 사용하여 키-값을 만듭니다.

```azurecli-interactive
az appconfig kv set --name <appconfig_name> --key Foo --value Bar --yes
```

이벤트를 트리거했고 Event Grid가 구독할 때 구성한 엔드포인트로 메시지를 보냈습니다. 웹앱을 확인하여 방금 전송한 이벤트를 봅니다.

```json
[{
  "id": "deb8e00d-8c64-4b6e-9cab-282259c7674f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/eventDemoGroup/providers/microsoft.appconfiguration/configurationstores/{appconfig-name}",
  "subject": "https://{appconfig-name}.azconfig.io/kv/Foo",
  "data": {
    "key": "Foo",
    "etag": "a1LIDdNEIV6wCnfv3xaip7fMXD3"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T18:59:54Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="clean-up-resources"></a>리소스 정리
이 App Configuration과 이벤트 구독을 계속 사용할 계획이라면 이 문서에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 명령을 사용하여 이 문서에서 만든 리소스를 삭제합니다.

`<resource_group_name>`을 위에서 만든 리소스 그룹으로 바꿉니다.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>다음 단계

토픽과 이벤트 구독을 만드는 방법을 알아보았으므로, 이번에는 키-값 이벤트 및 Event Grid로 할 수 있는 일에 대해 자세히 알아보세요.

- [키-값 이벤트에 대응](concept-app-configuration-event.md)
- [Event Grid 정보](../event-grid/overview.md)
- [Azure Event Grid 처리기](../event-grid/event-handlers.md)
