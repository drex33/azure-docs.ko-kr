---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2d3c7cdcebc244aefb6ef5b2ceb7b931344a8960
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593720"
---
Azure CLI [az keyvault create](/cli/azure/keyvault#az_keyvault_create) 명령을 사용하여 이전 단계에서 리소스 그룹에 Key Vault를 만듭니다. 몇 가지 정보를 제공해야 합니다.

- 키 자격 증명 모음 이름: 숫자(0-9), 문자(a-z, A-Z) 및 하이픈(-)만 포함할 수 있는 3~24자의 문자열

  > [!Important]
  > 각 Key Vault마다 고유한 이름이 있어야 합니다. 다음 예제에서 \<your-unique-keyvault-name\>을 키 자격 증명 모음의 이름으로 바꿉니다.

- 리소스 그룹 이름: **myResourceGroup**
- 위치: **EastUS**

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

이 명령의 출력은 새로 만든 키 자격 증명 모음의 속성을 보여줍니다. 아래에 나열된 두 개의 속성을 기록합니다.

- **자격 증명 모음 이름**: 위의 --name 매개 변수에 제공한 이름입니다.
- **자격 증명 모음 URI**: 이 예제에서 이는 https://&lt;your-unique-keyvault-name&gt;.vault.azure.net/입니다. REST API를 통해 사용자 자격 증명 모음을 사용하는 애플리케이션은 URI를 사용해야 합니다.

이때 사용자의 Azure 계정은 이 새 자격 증명 모음에서 모든 작업을 수행할 권한이 있는 유일한 계정입니다.
