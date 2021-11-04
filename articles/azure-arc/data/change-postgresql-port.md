---
title: PostgreSQL 포트 변경
description: Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹이 수신 대기하는 포트를 변경합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 516c296bc18a3a38b0a6bbe303313119fe2bce1d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561805"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>서버 그룹이 수신 대기하는 포트 변경 

포트를 변경 하려면 서버 그룹을 편집 합니다. 예를 들어 다음 명령을 실행합니다.

```azurecli
 az postgres arc-server edit -n <server group name> --port <desired port number> --k8s-namespace <namespace> --use-k8s
```

서버 그룹의 이름이 _postgres01_ 이 고 포트 _866_ 에서 수신 대기 하도록 하려는 경우. 다음 명령 실행:

```azurecli
 az postgres arc-server edit -n postgres01 --port 866 --k8s-namespace arc --use-k8s
```

## <a name="verify-that-the-port-was-changed"></a>포트가 변경되었는지 확인

포트가 변경되었는지 확인하려면 다음 명령을 실행하여 서버 그룹의 구성을 표시합니다.

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```

해당 명령의 출력에서 서버 그룹 사양의 "서비스" 섹션에 있는 "포트" 항목에 대해 표시된 포트 번호를 확인합니다.

또는 `externalEndpoint` 서버 그룹 사양의 상태 섹션 항목에서 IP 주소 뒤에 구성한 포트 번호를 확인할 수 있습니다.

그림으로 위의 예제를 계속 하려면 명령을 실행 합니다.

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace arc --use-k8s
```

이 명령은 포트 866을 반환 합니다.

```output
"services": {
      "primary": {
        "port": 866,
        "type": "LoadBalancer"
      }
    }
```

또한에 대 한 값을 기록해 둡니다 `primaryEndpoint` .

```output
"primaryEndpoint": "12.345.67.890:866",
```

## <a name="next-steps"></a>다음 단계
- [서버 그룹에 연결하는 방법](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)을 읽어 봅니다.
- 문서의 방법\관리\구성 및 크기 조정 섹션에서 서버 그룹의 다른 측면을 구성하는 방법에 대해 읽어 봅니다.
