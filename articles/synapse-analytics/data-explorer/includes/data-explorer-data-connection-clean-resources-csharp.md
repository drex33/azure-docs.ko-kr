---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 07541f46023564aba220fb82bd927e414ba339d5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482988"
---
## <a name="clean-up-resources"></a>리소스 정리

데이터 연결을 삭제하려면 다음 명령을 사용합니다.

```csharp
kustoManagementClient.DataConnections.Delete(resourceGroupName, clusterName, databaseName, dataConnectionName);
```
