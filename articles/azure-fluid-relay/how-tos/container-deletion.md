---
author: trngo
description: az-cli를 사용하여 개별 컨테이너를 삭제하는 방법 알아보기
title: 유체 컨테이너 삭제
ms.author: trngo
ms.date: 09/28/2021
ms.service: azure-fluid
ms.topic: reference
ms.openlocfilehash: 35b6ee09860b2acfe766f1ca80722f74cc3f8974
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129663027"
---
# <a name="delete-fluid-containers-in-microsoft-azure-fluid-relay-server"></a>Microsoft Azure Fluid Relay 서버에서 유체 컨테이너 삭제
이 시나리오에서는 기존 유체 컨테이너를 삭제합니다. 컨테이너가 삭제되면 컨테이너를 참조하는 애플리케이션은 더 이상 컨테이너 또는 해당 데이터에 액세스할 수 없습니다. 

## <a name="requirements-to-delete-a-fluid-container"></a>유체 컨테이너를 삭제하기 위한 요구 사항
- 시작하려면 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)를 설치해야 합니다. Azure CLI 이미 설치되어 있는 경우 을 실행하여 버전이 2.0.67 이상인지 `az version` 확인하세요.
- Fluid 컨테이너를 삭제하려면 애플리케이션과 해당 클라이언트가 컨테이너에 더 이상 연결되어 있지 않은지 확인해야 합니다.

## <a name="list-the-containers-within-a-fluid-relay-resource"></a>Fluid Relay 리소스 내의 컨테이너 나열
Fluid Relay 리소스에 속하는 모든 컨테이너를 보려면 다음 명령을 실행합니다.
```
az rest --method get --uri https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.FluidRelay/FluidRelayServers/<frsResourceName>/FluidRelayContainers?api-version=<apiVersion>
```
**subscriptionId:** Fluid Relay 리소스가 속한 Azure 구독의 ID입니다.

**resourceGroupName:** Fluid Relay 리소스가 있는 리소스 그룹의 이름입니다.

**frsResourceName:** Fluid Relay 리소스의 이름입니다. 이는 Fluid Relay 리소스의 tenantId와 다릅니다.

**apiVersion:** 리소스 공급자의 API 버전입니다. 지원되는 최소 버전은 **2021-08-30-preview 입니다.**  


## <a name="sample-output"></a>샘플 출력
출력에는 Fluid Relay 리소스 및 해당 속성에 속하는 컨테이너 목록이 포함됩니다.
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
컨테이너를 삭제하려면 위의 출력에서 컨테이너의 **containerId를** 식별하고 다음 명령을 실행해야 합니다. 
```
az rest --method delete --uri https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.FluidRelay/FluidRelayServers/<frsResourceName>/FluidRelayContainers/<frsContainerId>?api-version=<api-version>
```
  **frsContainerId**: 삭제할 유체 컨테이너의 ID입니다. 
