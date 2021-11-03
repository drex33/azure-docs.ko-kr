---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 3671a5d221afa89c776dca1bc3a17b965b621c5b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423998"
---
Azure CLI [az webpubsub create](/cli/azure/webpubsub#az_webpubsub_create) 명령을 사용하여 이전 단계의 리소스 그룹에 Web PubSub를 만들고 다음 정보를 제공합니다.

- 리소스 이름: 숫자(0-9), 문자(a-z, A-Z) 및 하이픈(-)만 사용할 수 있는 3~24자의 문자열입니다.

  > [!Important]
  > 각 Web PubSub 리소스에는 고유한 이름이 있어야 합니다. 다음 예제에서 &lt;your-unique-resource-name&gt;을 Web PubSub의 이름으로 바꿉니다.

- 리소스 그룹 이름: **myResourceGroup**
- 위치: **EastUS**
- SKU: **Free_F1**

```azurecli-interactive
az webpubsub create --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --location "EastUS" --sku Free_F1
```

이 명령의 출력에는 새로 만든 리소스의 속성이 표시됩니다. 아래에 나열된 두 개의 속성을 기록합니다.

- **리소스 이름**: 위의 `--name` 매개 변수에 제공한 이름입니다.
- **hostName**: 이 예제에서 호스트 이름은 `<your-unique-resource-name>.webpubsub.azure.com/`입니다.

이때 Azure 계정은 이 새 리소스에서 모든 작업을 수행할 권한이 있는 유일한 계정입니다.
