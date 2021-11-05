---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 10/19/2021
ms.author: cephalin
ms.openlocfilehash: a1128c7e2e8a89c8d60e629437a12e2c1378babc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456002"
---
## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 Azure 리소스를 리소스 그룹에 만들었습니다. 나중에 이러한 리소스가 필요하지 않으면 다음 PowerShell 명령을 실행하여 리소스 그룹을 삭제합니다.

```azurecli-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

이 명령을 실행하는 데 1분 정도 걸릴 수 있습니다.