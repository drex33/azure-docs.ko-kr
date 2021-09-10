---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 2b12b25b973d9e56de0241c1e088e2409137b086
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788252"
---
### <a name="publish-messages-and-manage-the-clients"></a>메시지 게시 및 클라이언트 관리

Azure CLI는 클라이언트 연결을 관리하는 [az webpubsub service](/cli/azure/webpubsub/service) 명령도 제공합니다.

**또 다른** CLI 명령을 열어 클라이언트로 메시지를 브로드캐스트할 수 있습니다.

- 허브 이름: **myHub1**.
- 리소스 그룹 이름: **myResourceGroup**

```azurecli-interactive
az webpubsub service broadcast --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --hub-name myHub1 --payload "Hello World"
```

이전 CLI 명령으로 다시 전환하면 클라이언트가 메시지를 받는 것을 확인할 수 있습니다.
```JSON
{"type":"message","from":"server","dataType":"text","data":"Hello World"}
```

`--help` 옵션을 사용하여 사용 가능한 모든 명령을 나열하고 나열된 명령을 사용할 수도 있습니다.

```azurecli-interactive
az webpubsub service --help
```
