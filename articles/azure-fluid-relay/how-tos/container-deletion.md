---
author: trngo
description: Az-cli를 사용 하 여 개별 컨테이너를 삭제 하는 방법을 알아봅니다.
title: 유체 컨테이너 삭제
ms.author: trngo
ms.date: 09/28/2021
ms.service: azure-fluid
ms.topic: reference
ms.openlocfilehash: 8a94c06bd80b6997c5bd80cbb147bf68ccf91a4b
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728668"
---
# <a name="delete-fluid-containers-in-microsoft-azure-fluid-relay-server"></a>Microsoft Azure 유체 Relay 서버에서 유체 컨테이너 삭제

> [!NOTE]
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.

이 시나리오에서는 기존 유체 컨테이너를 삭제 합니다. 컨테이너를 삭제 하면 컨테이너를 참조 하는 응용 프로그램에서 더 이상 컨테이너 또는 해당 데이터에 액세스할 수 없습니다. 

## <a name="requirements-to-delete-a-fluid-container"></a>유체 컨테이너를 삭제 하기 위한 요구 사항
- 시작하려면 [Azure CLI](/cli/azure/install-azure-cli)를 설치해야 합니다. Azure CLI 이미 설치 되어 있는 경우를 실행 하 여 버전이 2.0.67 이상 인지 확인 하세요 `az version` .
- 유체 컨테이너를 삭제 하려면 응용 프로그램 및 해당 클라이언트가 더 이상 컨테이너에 연결 되어 있지 않은지 확인 해야 합니다.

## <a name="list-the-containers-within-a-fluid-relay-resource"></a>유체 Relay 리소스 내의 컨테이너 나열
유체 Relay 리소스에 속하는 모든 컨테이너를 보려면 다음 명령을 실행할 수 있습니다.
```
az rest --method get --uri https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.FluidRelay/FluidRelayServers/<frsResourceName>/FluidRelayContainers?api-version=<apiVersion>
```
**subscriptionId**: 유체 Relay 리소스가 속한 Azure 구독의 ID입니다.

**resourceGroupName**: 유체 Relay 리소스가 있는 리소스 그룹의 이름입니다.

**Frsresourcename**: 유체 Relay 리소스의 이름입니다. 이는 유체 Relay 리소스의 tenantId와 다릅니다.

**apiVersion**: 리소스 공급자의 API 버전입니다. 지원 되는 최소 버전은 **2021-08-30-미리 보기** 입니다.  


## <a name="sample-output"></a>샘플 출력
출력에는 유체 Relay 리소스 및 해당 속성에 속한 컨테이너 목록이 포함 됩니다.
```json
{
  "value": [
    {
      "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.FluidRelay/fluidRelayServers/<frsResourcename>/fluidRelayContainers/<containerId>",
      "name": "<containerId>",
      "properties": {
        "frsContainerId": "<containerId>",
        "frsTenantId": "<frsResourceTenantId>"
      },
      "resourceGroup": "<resourceGroupname>",
      "type": "Microsoft.FluidRelay/fluidRelayServers/fluidRelayContainers"
    },
    ...
  ]
}
```


## <a name="delete-an-existing-container"></a>기존 컨테이너 삭제
컨테이너를 삭제 하려면 위의 출력에서 컨테이너의 **containerId** 를 식별 하 고 다음 명령을 실행 해야 합니다. 
```
az rest --method delete --uri https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.FluidRelay/FluidRelayServers/<frsResourceName>/FluidRelayContainers/<frsContainerId>?api-version=<api-version>
```
  **Frscontainerid**: 삭제할 유체 컨테이너의 ID입니다. 
