---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: mikben
ms.openlocfilehash: dfdeedd058131912db6884a49cf92ac1020b6801
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113762408"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet/).
- [Azure CLI](/cli/azure/install-azure-cli-windows?tabs=azure-cli) 

## <a name="create-azure-communication-services-resource"></a>Azure Communication Services 리소스 만들기

Azure Communication Services 리소스를 만들려면 [Azure CLI에 로그인합니다](/cli/azure/authenticate-azure-cli). ```az login``` 명령을 사용하고 자격 증명을 제공하여 터미널을 통해 이 작업을 수행할 수 있습니다. 다음 명령을 실행하여 리소스를 만듭니다.

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

특정 구독을 선택하려는 경우 ```--subscription``` 플래그를 지정하고 구독 ID를 제공할 수도 있습니다.
```
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup> --subscription "<subscriptionID>"
```

다음 옵션을 사용하여 Communication Services 리소스를 구성할 수 있습니다.

* 리소스 그룹
* Communication Services 리소스의 이름
* 리소스가 연결되는 지리적 위치

다음 단계에서는 리소스에 태그를 할당할 수 있습니다. 태그를 사용하여 Azure 리소스를 구성할 수 있습니다. 태그에 대한 자세한 내용은 [리소스 태그 지정 설명서](../../../azure-resource-manager/management/tag-resources.md)를 참조하세요.

## <a name="manage-your-communication-services-resource"></a>Communication Services 리소스 관리

Communication Services 리소스에 태그를 추가하려면 다음 명령을 실행합니다. 특정 구독을 대상으로 지정할 수도 있습니다.

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal1" --resource-group "<resourceGroup>"

az communication update --name "<communicationName>" --tags newTag="newVal2" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>" --subscription "<subscriptionID>"
```

추가 명령에 대한 자세한 내용은 [az communication](/cli/azure/communication)을 참조하세요.