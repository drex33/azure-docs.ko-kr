---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 10/07/2021
ms.author: danlep
ms.openlocfilehash: eb85c0df13a90afb08a3730ff58457f9897650ae
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053195"
---
명령 출력에는 레지스트리 연결 문자열 및 관련 설정이 포함됩니다. 다음 예제 출력에서는 부모 레지스트리 *contosoregistry* 가 있는 *myconnectedregistry* 라는 연결된 레지스트리에 대한 연결 문자열을 보여줍니다.

```json
{
  "ACR_REGISTRY_CONNECTION_STRING": "ConnectedRegistryName=myconnectedregistry;SyncTokenName=myconnectedregistry-sync-token;SyncTokenPassword=xxxxxxxxxxxxxxxx;ParentGatewayEndpoint=contosoregistry.eastus.data.azurecr.io;ParentEndpointProtocol=https"
}
```
