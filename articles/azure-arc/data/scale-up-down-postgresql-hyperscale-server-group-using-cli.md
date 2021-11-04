---
title: CLI(az 또는 kubectl)를 사용하여 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 스케일 업 및 스케일 다운
description: CLI(az 또는 kubectl)를 사용하여 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 스케일 업 및 스케일 다운
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 1c33c2f4bf89b76abf40d12146965114ba4918b0
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564502"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-az-or-kubectl"></a>CLI(az 또는 kubectl)를 사용하여 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 스케일 업 및 스케일 다운

서버 그룹의 특성 또는 정의를 변경해야 하는 경우가 있습니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

- 각 코디네이터 또는 작업자 노드가 사용하는 vCore의 수 스케일 업 또는 스케일 다운
- 각 코디네이터 또는 작업자 노드가 사용하는 메모리 스케일 업 또는 스케일 다운

이 가이드에서는 vCore 및/또는 메모리의 크기를 조정하는 방법을 설명합니다.

서버 그룹의 vCore 또는 메모리 설정을 스케일 업하거나 스케일 다운하면 각 vCore 및 메모리 설정에 대해 최소 및/또는 최대를 설정할 수 있습니다. 특정 수의 vCore 또는 특정 양의 메모리를 사용하도록 서버 그룹을 구성하려면 최대 설정에 해당하는 최소 설정을 설정합니다. VCores 및 메모리에 대해 설정 된 값을 늘리려면 다음을 확인 해야 합니다. 
- 배포를 호스트 하는 물리적 인프라에서 충분 한 리소스를 사용할 수 있습니다. 
- 동일한 시스템에서 배치 된 작업은 동일한 vCores 또는 메모리에 대해 경쟁 하지 않습니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>서버 그룹의 현재 정의 표시

서버 그룹 현재 정의를 표시하고 현재 vCore 및 메모리 설정을 확인하려면 다음 명령 중 하나를 실행합니다.

### <a name="with-azure-cli-az"></a>Azure CLI (az)

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```
### <a name="cli-with-kubectl"></a>kubectl을 사용하는 CLI

```console
kubectl describe postgresql/<server group name> -n <namespace name>
```

서버 그룹의 구성을 반환합니다. 기본 설정을 사용하여 서버 그룹을 만든 경우 다음과 같은 정의가 표시되어야 합니다.

```json
Spec:
  Dev:  false
  Engine:
    Extensions:
      Name:   citus
    Version:  12
  Scale:
    Replicas:       1
    Sync Replicas:  0
    Workers:        4
  Scheduling:
    Default:
      Resources:
        Requests:
          Memory:  256Mi
...
```

## <a name="interpret-the-definition-of-the-server-group"></a>서버 그룹의 정의 해석

서버 그룹의 정의에서 노드당 최소 또는 최대 vCore 및 노드당 최소 또는 최대 메모리 설정을 전달하는 섹션은 **"일정"** 섹션입니다. 해당 섹션에서 최대 설정은 **"제한"** 이라는 하위 섹션에서 유지되고 최소 설정은 **"요청"** 이라는 하위 섹션에서 유지됩니다.

최대 설정과 다른 최소 설정을 설정하는 경우 구성에서 서버 그룹에 요청된 리소스를 할당하는 것을 보장합니다(필요한 경우). 설정한 한도를 초과하지 않습니다.

실제로 서버 그룹에서 사용되는 리소스(vCore 및 메모리)는 최대 설정에 따르며 클러스터에서 사용할 수 있는 워크로드 및 리소스에 따라 달라집니다. 설정을 최댓값으로 제한하지 않으면 서버 그룹은 Kubernetes 클러스터가 서버 그룹이 예약된 Kubernetes 노드에 할당하는 모든 리소스를 사용할 수 있습니다.

이러한 vCore 및 메모리 설정은 PostgreSQL 하이퍼스케일 서버 그룹인 코디네이터 및 작업자를 구성하는 Postgres 인스턴스의 각 역할에 적용됩니다. 역할별로 요청 및 제한을 정의할 수 있습니다. 각 역할에 대해 서로 다른 요청 및 제한 설정을 정의할 수 있습니다. 요구 사항에 따라 유사할 수도 있습니다.

기본 구성에서는 최소 메모리만 PostgreSQL 하이퍼스케일을 실행하는 데 권장되는 최소 메모리 크기이므로 256Mi로 설정됩니다.

> [!NOTE]
> 최솟값을 설정하는 것은 서버 그룹이 반드시 해당 최소를 사용하는 것을 의미하지는 않습니다. 즉, 서버 그룹에 필요한 경우 최소한 이 최소로 할당되도록 보장합니다. 예를 들어 `--minCpu 2`를 설정하는 것을 가정해 보겠습니다. 서버 그룹은 항상 최소 2개의 vCore를 사용하는 것을 의미하지는 않습니다. 대신 서버 그룹이 필요한 양이 많지 않은 경우 2개 미만의 vCore를 사용하여 시작할 수 있으며 이는 나중에 필요할 경우 최소 2개의 vCore를 할당하는 것을 보장합니다. 이는 Kubernetes 클러스터가 필요한 경우 서버 그룹에 2개의 vCore를 할당할 수 있도록 이러한 방법으로 다른 워크로드에 리소스를 할당하는 것을 의미합니다. 또한 스케일 업 및 스케일 다운은 kubernetes Pod를 다시 시작해야 하므로 온라인 작업이 아닙니다.

>[!NOTE]
>시스템 구성을 수정하기 전에 [여기](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)에서 Kubernetes 리소스 모델을 숙지해야 합니다.

## <a name="scale-up-and-down-the-server-group"></a>서버 그룹 스케일 업 및 스케일 다운

스케일 업은 서버 그룹의 vCore 및/또는 메모리 설정 값을 늘리는 것을 의미합니다.
스케일 다운은 서버 그룹의 vCore 및/또는 메모리 설정 값을 줄이는 것을 의미합니다.

설정하려는 설정은 Kubernetes 클러스터에 대해 설정한 구성 내에서 고려해야 합니다. Kubernetes 클러스터가 충족시킬 수 없는 값을 설정하지 않았는지 확인합니다. 이로 인해 오류가 발생하거나 예측할 수 없는 동작(예: 데이터베이스 인스턴스 사용 불가)이 발생할 수 있습니다. 예를 들어 구성 변경 후 서버 그룹의 상태가 오랫동안 _업데이트_ 상태로 유지되는 경우 아래 매개 변수를 Kubernetes 클러스터가 충족하지 못하는 값으로 설정하는 것을 나타낼 수 있습니다. 이 경우 변경 내용을 되돌리거나 _troubleshooting_section을 읽습니다.

어떤 설정을 설정해야 하나요?
- 최소 vCore를 설정하려면 `--cores-request`를 설정합니다.
- 최대 vCore를 설정하려면 `--cores-limit`를 설정합니다.
- 최소 메모리를 설정하려면 `--memory-request`를 설정합니다.
- 최대 메모리를 설정하려면 `--memory-limit`를 설정합니다.

설정이 적용되는 역할을 어떻게 나타내나요?
- 코디네이터 역할에 대한 설정을 구성하려면 `coordinator=<value>`를 지정합니다.
- 작업자 역할에 대한 설정을 구성하려면(지정된 설정이 모든 작업자에서 동일한 값으로 설정) `worker=<value>`를 지정합니다.


> [!CAUTION]
> Kubernetes를 통해 해당 요청 설정을 구성하지 않고 제한 설정을 구성하면 요청 값이 제한과 동일한 값이 됩니다. 이로 인해 충분한 리소스와 함께 사용할 수 있는 Kubernetes 노드가 없는 경우 해당 Pod가 다시 예약되지 않을 수 있기 때문에 서버 그룹을 사용하지 못할 수 있습니다. 따라서 이러한 상황을 방지하기 위해 아래 예제에서는 요청 및 제한 설정을 모두 설정하는 방법을 보여줍니다.


**일반 구문은 다음과 같습니다.**

```azurecli
az postgres arc-server edit -n <servergroup name> --memory-limit/memory-request/cores-request/cores-limit <coordinator=val1,worker=val2> --k8s-namespace <namespace> --use-k8s
```

메모리 설정에 대해 나타내는 값은 숫자 뒤에 볼륨 단위가 나옵니다. 예를 들어 1Gb를 나타내려면 1024Mi 또는 1Gi를 나타냅니다.
코어 수를 나타내려면 단위 없이 숫자를 전달하면 됩니다. 

### <a name="examples-using-the-azure-cli"></a>Azure CLI를 사용하는 예제

**코디네이터 역할이 2개 코어를 초과하지 않도록 구성하고 작업자 역할이 4개 코어를 초과하지 않도록 구성합니다.**

```azurecli
 az postgres arc-server edit -n postgres01 --cores-request coordinator=1, --cores-limit coordinator=2  --k8s-namespace arc --use-k8s
 az postgres arc-server edit -n postgres01 --cores-request worker=1, --cores-limit worker=4 --k8s-namespace arc --use-k8s
```

또는
```azurecli
az postgres arc-server edit -n postgres01 --cores-request coordinator=1,worker=1 --cores-limit coordinator=4,worker=4 --k8s-namespace arc --use-k8s
```

> [!NOTE]
> 이러한 매개 변수에 대한 자세한 내용은 `az postgres arc-server edit --help`를 실행합니다.

### <a name="example-using-kubernetes-native-tools-like-kubectl"></a>`kubectl`과 같은 Kubernetes 네이티브 도구를 사용하는 예제

명령 실행: 
```console
kubectl edit postgresql/<servergroup name> -n <namespace name>
```

그러면 `vi` 편집기에서 구성을 탐색하고 변경할 수 있습니다. 다음을 사용하여 원하는 설정을 사양의 필드 이름에 매핑합니다.

> [!CAUTION]
> 다음은 구성을 편집하는 방법을 보여 주도록 제공된 예제입니다. 구성을 업데이트하기 전에 매개 변수를 Kubernetes 클러스터에서 사용할 수 있는 값으로 설정해야 합니다.

예를 들어 코디네이터와 작업자 역할 모두에 대해 다음 설정을 다음 값으로 설정하려는 경우입니다.
- 최소 vCore = `2` 
- 최대 vCore = `4`
- 최소 메모리 = `512Mb`
- 최대 메모리 = `1Gb` 

아래 구성과 일치하도록 서버 그룹의 정의를 설정합니다.

```json
...
  spec:
  dev: false
  engine:
    extensions:
    - name: citus
    version: 12
  scale:
    replicas: 1
    syncReplicas: "0"
    workers: 4
  scheduling:
    default:
      resources:
        requests:
          memory: 256Mi
    roles:
      coordinator:
        resources:
          limits:
            cpu: "4"
            memory: 1Gi
          requests:
            cpu: "2"
            memory: 256Mi
      worker:
        resources:
          limits:
            cpu: "4"
            memory: 1Gi
          requests:
            cpu: "2"
            memory: 256Mi
...
```

`vi` 편집기에 익숙하지 않은 경우 [여기](https://www.computerhope.com/unix/uvi.htm)에서 필요할 수 있는 명령에 대한 설명을 참조하세요.
- 편집 모드: `i`
- 화살표를 사용하여 이동
- 편집 중지: `esc`
- 저장하지 않고 종료: `:qa!`
- 저장 후 종료: `:qw!`


## <a name="reset-to-default-values"></a>기본 값으로 초기화
코어/메모리 제한/요청 매개 변수를 기본값으로 다시 설정하려면 해당 매개 변수를 편집하고 실제 값 대신 빈 문자열을 전달합니다. 예를 들어 코어 제한 매개 변수를 다시 설정하려는 경우 다음 명령을 실행합니다.

```azurecli
az postgres arc-server edit -n postgres01 --cores-request coordinator='',worker='' --k8s-namespace arc --use-k8s
az postgres arc-server edit -n postgres01 --cores-limit coordinator='',worker='' --k8s-namespace arc --use-k8s
```

또는 
```azurecli
az postgres arc-server edit -n postgres01 --cores-request coordinator='',worker='' --cores-limit coordinator='',worker='' --k8s-namespace arc --use-k8s
```

## <a name="next-steps"></a>다음 단계

- [Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 스케일 아웃](scale-out-in-postgresql-hyperscale-server-group.md)
- [스토리지 구성 및 Kubernetes 스토리지 개념](storage-configuration.md)
- [Kubernetes 리소스 모델](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
