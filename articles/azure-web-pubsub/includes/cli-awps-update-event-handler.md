---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 156633c04cabd3e97bca4ae6d861b007be8fe84c
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122447073"
---
Azure CLI [az webpubsub event-handler hub](/cli/azure/webpubsub/event-handler/hub) 명령을 사용하여 이벤트 처리기 설정을 업데이트합니다.

  > [!Important]
  > &lt;your-unique-resource-name&gt;을 이전 단계에서 만든 Web PubSub 리소스의 이름으로 바꿉니다.
  > &lt;domain-name&lt;을 출력된 ngrok 이름으로 바꿉니다.

```azurecli-interactive
az webpubsub event-handler hub update -n "<your-unique-resource-name>" -g "myResourceGroup" --hub-name myHub1 --template url-template="https://<domain-name>.ngrok.io/eventHandler" user-event-pattern="*" system-event-pattern="connected"
```
