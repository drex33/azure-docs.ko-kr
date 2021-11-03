---
author: craigshoemaker
ms.service: app-service
ms.topic: include
ms.date: 10/25/2021
ms.author: cshoe
ms.openlocfilehash: 67821760435fdac37c51400190c1afe44996d014
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052977"
---
### <a name="get-fully-qualified-domain-name"></a>정규화 된 도메인 이름 가져오기

이 `az containerapp show` 명령은 컨테이너 앱의 정규화 된 도메인 이름을 반환 합니다.

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az containerapp show \
  --resource-group <RESOURCE_GROUP_NAME> \
  --name <CONTAINER_APP_NAME> \
  --query configuration.ingress.fqdn
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
az containerapp show `
  --resource-group <RESOURCE_GROUP_NAME> `
  --name <CONTAINER_APP_NAME> `
  --query configuration.ingress.fqdn
```

---

이 예제에서는로 둘러싸인 자리 표시자를 `<>` 사용자의 값으로 바꿉니다.

이 명령에서 반환 된 값은 다음 예제와 같은 도메인 이름과 유사 합니다.

```console
myapp.happyhill-70162bb9.canadacentral.azurecontainerapps.io
```
