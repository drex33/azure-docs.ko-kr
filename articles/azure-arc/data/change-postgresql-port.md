---
title: PostgreSQL 포트 변경
description: Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹이 수신 대기하는 포트를 변경합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 47a64b9e1207536e951f61059d472a88e9f9d8c5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537227"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>서버 그룹이 수신 대기하는 포트 변경 

포트 변경은 서버 그룹의 표준 편집 작업입니다. 포트를 변경하려면 다음 명령을 실행합니다.
```azurecli
 az postgres arc-server edit -n <server group name> --port <desired port number> --k8s-namespace <namespace> --use-k8s
```

예를 들어 서버 그룹의 이름이 _postgres01_ 이고 포트 _866_ 에서 수신 대기하기를 원한다고 가정해 보겠습니다. 이 경우 다음 명령을 실행합니다.
```azurecli
 az postgres arc-server edit -n postgres01 --port 866 --k8s-namespace <namespace> --use-k8s
```

## <a name="verify-that-the-port-was-changed"></a>포트가 변경되었는지 확인

포트가 변경되었는지 확인하려면 다음 명령을 실행하여 서버 그룹의 구성을 표시합니다.
```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```

해당 명령의 출력에서 서버 그룹 사양의 "서비스" 섹션에 있는 "포트" 항목에 대해 표시된 포트 번호를 확인합니다.
또는 서버 그룹 사양의 상태 섹션에 있는 externalEndpoint 항목에서 IP 주소 다음에 구성한 포트 번호가 있는지 확인할 수 있습니다.

위의 예제를 계속 진행하여 다음 명령을 실행합니다.
```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace <namespace> --use-k8s
```

여기에서 참조하는 포트 866이 표시됩니다.

```console
"service": {
      "port": 866,
      "type": "LoadBalancer"
    },
```
다음이 표시됩니다.

```console
"status": {
    "externalEndpoint": "12.678.345.09:866",
    "logSearchDashboard": "https://12.345.678.90:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))",
    "metricsDashboard": "https://12.345.678.90:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01",
    "readyPods": "3/3",
    "state": "Ready"
  }
```
## <a name="next-steps"></a>다음 단계
- [서버 그룹에 연결하는 방법](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)을 읽어 봅니다.
- 문서의 방법\관리\구성 및 크기 조정 섹션에서 서버 그룹의 다른 측면을 구성하는 방법에 대해 읽어 봅니다.
