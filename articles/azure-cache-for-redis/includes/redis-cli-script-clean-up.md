---
author: curib
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: cauribeg
ms.openlocfilehash: 0e4adb1df1186fe9fa912745356bf92c8c643a67
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532697"
---
## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, Azure Cache for Redis 인스턴스 및 리소스 그룹에서 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name contosoGroup
```