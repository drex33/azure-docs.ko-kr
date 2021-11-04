---
title: CLI를 사용하여 간접 모드 Azure Arc 사용 Managed Instance 업그레이드
description: CLI를 사용하여 간접 모드 Azure Arc 사용 Managed Instance 업그레이드
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: daff60736185f448183d24207099670884402795
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570087"
---
# <a name="upgrade-an-indirect-mode-azure-arc-enabled-managed-instance-using-the-cli"></a>CLI를 사용하여 간접 모드 Azure Arc 사용 Managed Instance 업그레이드

## <a name="prerequisites"></a>사전 요구 사항

### <a name="install-tools"></a>도구 설치

이 문서의 작업을 진행하려면 다음을 설치해야 합니다.

- [Azure CLI(az)](/cli/azure/install-azure-cli)
- [ `arcdata` Azure CLI 대한 확장](install-arcdata-extension.md)

## <a name="limitations"></a>제한 사항

Azure Arc 데이터 컨트롤러를 새 버전으로 업그레이드해야 Managed Instance 업그레이드할 수 있습니다.

현재는 한 번에 하나의 Managed Instance 업그레이드할 수 있습니다.

## <a name="upgrade-the-managed-instance"></a>Managed Instance 업그레이드

먼저 건성 실행을 수행할 수 있습니다. 그러면 버전 스키마의 유효성을 검사하고 업그레이드할 인스턴스를 나열합니다.

````cli
az sql mi-arc upgrade --name <instance name> --k8s-namespace <namespace> --dry-run --use-k8s
````

다음과 같이 출력됩니다.

```output
Preparing to upgrade sql sqlmi-1 in namespace arc to data controller version.
****Dry Run****1 instance(s) would be upgraded by this commandsqlmi-1 would be upgraded to 20211024.1.
```

### <a name="general-purpose"></a>범용

Managed Instance 업그레이드하려면 다음 명령을 사용합니다.

````cli
az sql mi-arc upgrade --name <instance name> --desired-version <version> --k8s-namespace <namespace> --use-k8s
````

예제:

````cli
az sql mi-arc upgrade --name instance1 --target v1.0.0.20211028 --k8s-namespace arc1 --use-k8s
````

## <a name="monitor"></a>모니터

kubectl 또는 CLI를 통해 업그레이드 진행 상황을 모니터링할 수 있습니다.

### <a name="kubectl"></a>kubectl

```console
kubectl describe sqlmi --namespace <namespace>
```

### <a name="cli"></a>CLI

```cli
az sql mi-arc show --name <instance name> --k8s-namespace <namespace> --use-k8s
```

### <a name="output"></a>출력

명령의 출력에는 리소스 정보가 표시됩니다. 업그레이드 정보는 상태가 됩니다.

업그레이드하는 동안 가 ```State``` ```Updating``` 표시되고 ```Running Version``` 현재 버전이 됩니다.

```output
Status:
  Log Search Dashboard:  https://30.88.222.48:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:sqlmi-1'))
  Metrics Dashboard:     https://30.88.221.32:3000/d/40q72HnGk/sql-managed-instance-metrics?var-hostname=sqlmi-1-0
  Observed Generation:   2
  Primary Endpoint:      30.76.129.38,1433
  Ready Replicas:        1/1
  Running Version:       v1.0.0_2021-07-30
  State:                 Updating
```

업그레이드가 완료되면 가 ```State``` ```Ready``` 표시되고 가 ```Running Version``` 새 버전이 됩니다.

```output
Status:
  Log Search Dashboard:  https://30.88.222.48:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:sqlmi-1'))
  Metrics Dashboard:     https://30.88.221.32:3000/d/40q72HnGk/sql-managed-instance-metrics?var-hostname=sqlmi-1-0
  Observed Generation:   2
  Primary Endpoint:      30.76.129.38,1433
  Ready Replicas:        1/1
  Running Version:       20211024.1
  State:                 Ready
```

## <a name="troubleshoot-upgrade-problems"></a>업그레이드 문제 해결

업그레이드하는 데 문제가 발생하는 경우 [문제 해결 가이드](troubleshoot-guide.md)를 참조하세요.
