---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 1c9875f6c3c6b69098c915d4257ed05e3ca0abc4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788249"
---
### <a name="connect-to-the-service"></a>서비스에 연결

[!INCLUDE [az webpubsub client](cli-awps-client-connect.md)]

이 기능을 활용하고 `joingroup <group-name>`을 사용하여 그룹에 참가하고 `sendtogroup <group-name>`을 사용하여 그룹에 메시지를 보냅니다.

```azurecli
joingroup group1
```

```azurecli
sendtogroup group1 hello
```

[!INCLUDE [publish messages](cli-awps-service.md)]
