---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.openlocfilehash: 549104e9a0721d8edb58ad57cede0e4790cad4cf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455868"
---
## <a name="add-azure-cli-extensions"></a>Azure CLI 확장 추가

[Azure Cloud Shell](../articles/cloud-shell/quickstart.md)에서 Bash 환경을 시작합니다.

[![새 창에서 Cloud Shell 시작](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com)

이러한 CLI 명령은 아직 핵심 CLI 집합의 일부가 아니므로 다음 명령을 통해 추가합니다.

```azurecli-interactive
az extension add --upgrade --yes --name customlocation
az extension remove --name appservice-kube
az extension add --upgrade --yes --name appservice-kube
```