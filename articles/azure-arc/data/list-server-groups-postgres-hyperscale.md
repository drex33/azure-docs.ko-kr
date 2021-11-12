---
title: Azure Arc 데이터 컨트롤러에서 만든 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 나열
description: Azure Arc 데이터 컨트롤러에서 만든 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 나열
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 9b479950bc5abc47cfc0f86a21262511a74f6852
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279307"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러에서 만든 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 나열

이 문서에서는 Arc 데이터 컨트롤러에서 만든 서버 그룹 목록을 검색하는 방법을 설명합니다.

이 목록을 검색하려면 Arc 데이터 컨트롤러에 연결된 후 다음 방법 중 하나를 사용합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azure-cli-extension-az"></a>Azure CLI 확장(az)이 있는 CLI에서

이 명령의 일반적인 형식은 다음과 같습니다.
```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

다음과 같은 출력을 반환합니다.
```console
[
  {
    "name": "postgres01",
    "replicas": 1,
    "state": "Ready",
    "workers": 4
  }
]
```
이 명령에 사용할 수 있는 매개 변수에 대한 자세한 내용을 보려면 다음을 실행하세요.
```azurecli
az postgres arc-server list --help
```

## <a name="from-cli-with-kubectl"></a>kubectl을 사용하는 CLI에서
다음 명령 중 하나를 실행합니다.

**Postgres의 버전과 무관하게 서버 그룹을 나열하려면 다음을 실행합니다.**
```console
kubectl get postgresqls -n <namespace>
```
다음과 같은 출력을 반환합니다.
```console
NAME         STATE   READY-PODS   PRIMARY-ENDPOINT     AGE
postgres01   Ready   5/5          12.345.67.890:5432   12d
```

## <a name="next-steps"></a>다음 단계:

* [연결 엔드포인트를 가져오고 연결 문자열을 형성하여 서버 그룹에 연결하는 방법에 대한 문서 참조](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹의 구성을 표시하는 방법에 대한 문서 참조](show-configuration-postgresql-hyperscale-server-group.md)
