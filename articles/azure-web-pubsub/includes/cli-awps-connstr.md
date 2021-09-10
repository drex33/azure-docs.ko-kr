---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 4bfab858862bfb08cfaa2e9dc685b8ade7c0325e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785575"
---
> [!Important]
> 연결 문자열에는 애플리케이션이 Azure Web PubSub 서비스에 액세스하는 데 필요한 권한 부여 정보가 포함됩니다. 연결 문자열 내의 액세스 키는 서비스의 루트 암호와 비슷합니다. 항상 액세스 키를 보호해야 합니다. Azure Key Vault를 사용하여 키를 안전하게 관리하고 회전합니다. 액세스 키를 다른 사용자에게 배포하거나 하드 코딩하거나 다른 사용자가 액세스할 수 있는 일반 텍스트로 저장하지 않도록 합니다. 키가 손상되었다고 생각되면 키를 교체하세요.

Azure CLI [az webpubsub key](/cli/azure/webpubsub#az_webpubsub_key) 명령을 사용하여 서비스의 **ConnectionString** 을 가져옵니다.

```azurecli-interactive
az webpubsub key show --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --query primaryConnectionString
```
