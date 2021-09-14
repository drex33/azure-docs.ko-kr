---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: be837e20579a9236cc634e9518c82f17e2273e6b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123453852"
---
Azure CLI [az webpubsub client](/cli/azure/webpubsub/client) 명령을 사용하여 이전 단계에서 만든 서비스에 대한 WebSocket 클라이언트 연결을 시작하고 다음 정보를 제공합니다.

- 허브 이름: 1~127자로 구성된 문자열입니다. 영문자 `(a-z, A-Z)`로 시작하고 영숫자 `(0-9, a-z, A-Z)` 문자 또는 밑줄 `(_)`만 포함해야 합니다.

**Hub** 는 연결된 WebSocket 연결의 논리적 집합입니다. 개념에 대한 자세한 내용은 [Hubs, 그룹 및 연결 정보](../key-concepts.md)를 참조하세요.

  > [!Important]
  > &lt;your-unique-resource-name&gt;을 이전 단계에서 만든 Web PubSub 리소스의 이름으로 바꿉니다.

- Hub 이름: **myHub1**.
- 리소스 그룹 이름: **myResourceGroup**
- 사용자 ID: **user1**

```azurecli-interactive
az webpubsub client start --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name "myHub1" --user-id "user1"
```

명령이 Web PubSub 서비스에 대한 WebSocket 연결을 설정하고, 이제 성공적으로 연결되었음을 나타내는 JSON 메시지를 받고, 다음과 같은 고유한 `connectionId`로 할당되었음을 확인할 수 있습니다.

```json
{"type":"system","event":"connected","userId":"user1","connectionId":"<your_unique_connection_id>"}
```
