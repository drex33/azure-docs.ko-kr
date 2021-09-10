---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 785114c797fba5c84e97f5a0d0cc1a34dc14d540
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785580"
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

> [!Note]
> Web PubSub 서비스는 아직 미리 보기로 제공되므로 “명령에 확장 webpubsub가 필요합니다. 지금 설치하시겠습니까? 명령은 확장이 설치된 후 계속 실행됩니다. (Y/n).”라는 경고가 CLI 콘솔에 표시될 수 있습니다. Y를 눌러 확장을 설치하세요.

이 명령의 출력에는 새로 만든 리소스의 속성이 표시됩니다. 아래에 나열된 두 개의 속성을 기록합니다.

- **리소스 이름**: 위의 `--name` 매개 변수에 제공한 이름입니다.
- **hostName**: 이 예제에서 호스트 이름은 `<your-unique-resource-name>.webpubsub.azure.com/`입니다.

이때 Azure 계정은 이 새 리소스에서 모든 작업을 수행할 권한이 있는 유일한 계정입니다.
