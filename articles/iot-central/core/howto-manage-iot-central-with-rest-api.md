---
title: REST API를 사용 하 여 IoT Central 응용 프로그램 관리 및 모니터링 | Microsoft Docs
description: 이 문서에서는 REST API를 사용 하 여 IoT Central 응용 프로그램을 만들고 관리 하는 방법을 설명 합니다. REST API를 사용 하 여 시스템 할당 관리 id를 응용 프로그램에 추가할 수도 있습니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/25/2021
ms.topic: how-to
ms.openlocfilehash: a4df2346346d048bcd4e01e898636c1668720c4c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103698"
---
# <a name="use-the-rest-api-to-create-and-manage-iot-central-applications"></a>REST API를 사용 하 여 IoT Central 응용 프로그램 만들기 및 관리

[제어 평면 REST API](/rest/api/iotcentral/2021-06-01controlplane/apps) 를 사용 하 여 IoT Central 응용 프로그램을 만들고 관리할 수 있습니다. REST API를 사용 하 여 관리 되는 id를 응용 프로그램에 추가할 수도 있습니다.

이 API를 사용 하려면 리소스에 대 한 전달자 토큰이 필요 `management.azure.com` 합니다. 전달자 토큰을 가져오기 위해 Azure CLI를 사용할 수 있습니다.

```azurecli
az account get-access-token --resource https://management.azure.com
```

## <a name="list-your-applications"></a>응용 프로그램 나열

구독에서 IoT Central 응용 프로그램 목록을 가져오려면 다음을 수행 합니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.IoTCentral/iotApps?api-version=2021-06-01
```

리소스 그룹의 IoT Central 응용 프로그램 목록을 가져오려면 다음을 수행 합니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.IoTCentral/iotApps?api-version=2021-06-01
```

개별 응용 프로그램의 세부 정보를 검색할 수 있습니다.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.IoTCentral/iotApps/{applicationName}?api-version=2021-06-01
```

## <a name="create-an-iot-central-application"></a>IoT Central 애플리케이션 만들기

시스템 할당 [관리 id](../../active-directory/managed-identities-azure-resources/overview.md)를 사용 하 여 IoT Central 응용 프로그램을 만들려면 다음을 수행 합니다.

```http
PUT https://management.azure.com/subscriptions/<your subscription id>/resourceGroups/<your resource group name>/providers/Microsoft.IoTCentral/iotApps/<your application name>?api-version=2021-06-01
```

다음 페이로드는 관리 id를 포함 하 여 새 응용 프로그램에 대 한 구성을 보여 줍니다.

```json
{
  "location": "eastus",
  "sku": {
    "name": "ST2"
  },
  "properties": {
    "displayName": "Contoso IoT Central App",
    "subdomain": "my-iot-central-app",
    "template": "iotc-pnp-preview@1.0.0"
  },
  "identity": {
    "type": "SystemAssigned"
  }
}
```

## <a name="modify-an-iot-central-application"></a>IoT Central 응용 프로그램 수정

기존 IoT Central 응용 프로그램을 수정할 수 있습니다. 다음 예제에서는 표시 이름을 변경 하 고 시스템 할당 관리 id를 사용 하도록 설정 하는 방법을 보여 줍니다.

```http
PATCH https://management.azure.com/subscriptions/<your subscription id>/resourceGroups/<your resource group name>/providers/Microsoft.IoTCentral/iotApps/<your application name>?api-version=2021-06-01
```

다음 페이로드를 사용 하 여 표시 이름을 변경 하 고 시스템 할당 관리 id를 사용 하도록 설정 합니다.

```json
{
  "properties": {
    "displayName": "Contoso IoT Central App"
  },
  "identity": {
    "type": "SystemAssigned"
  }
}
```

> [!NOTE]
> 지역에서 만든 IoT Central 응용 프로그램에만 관리 되는 id를 추가할 수 있습니다. 모든 새 응용 프로그램은 한 지역에 생성 됩니다. 자세히 알아보려면 [업데이트](https://azure.microsoft.com/updates/azure-iot-central-new-and-updated-features-august-2021/)를 참조 하세요.

## <a name="delete-an-iot-central-application"></a>IoT Central 응용 프로그램 삭제

IoT Central 응용 프로그램을 삭제 하려면 다음을 사용 합니다.

```http
DELETE https://management.azure.com/subscriptions/<your subscription id>/resourceGroups/<your resource group name>/providers/Microsoft.IoTCentral/iotApps/<your application name>?api-version=2021-06-01
```

## <a name="next-steps"></a>다음 단계

이제 REST API를 사용 하 여 Azure IoT 중앙 응용 프로그램을 만들고 관리 하는 방법을 배웠으므로 제안 된 다음 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [IoT Central REST API를 사용하여 사용자 및 역할을 관리하는 방법](howto-manage-users-roles-with-rest-api.md)