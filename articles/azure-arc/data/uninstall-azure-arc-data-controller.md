---
title: 부분 배포에서 정리
description: 부분 배포에서 정리
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/30/2021
ms.topic: how-to
ms.openlocfilehash: a4e1ce940aaf1276b23c4fdd84ade836e9faf237
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133366964"
---
# <a name="uninstall-azure-arc-enabled-data-services"></a>Azure Arc 사용 데이터 서비스 제거

이 문서에서는 azure에서 Azure Arc 사용 데이터 서비스 리소스를 삭제 하는 방법을 설명 합니다.

> [!WARNING]
> 이 문서에 설명된 대로 리소스를 삭제하면 이러한 작업은 되돌릴 수 없습니다.

azure arc 사용 데이터 서비스를 배포 하려면 azure arc 데이터 컨트롤러 및 데이터 서비스의 인스턴스 azure arc 사용 SQL Managed Instance 또는 azure arc 지원 postgres의 인스턴스를 배포 해야 합니다. 배포는 다음과 같은 여러 아티팩트를 만듭니다. 
- 사용자 지정 리소스 정의 (CRDs)
- 클러스터 역할
- 클러스터 역할 바인딩
- API 서비스 
- 네임 스페이스 (이전에 존재 하지 않는 경우) 

직접 연결 모드에는 다음과 같은 추가 아티팩트가 있습니다. 
- 클러스터 확장 
- 사용자 지정 위치

## <a name="before"></a>이전

Azure Arc 사용 SQL Managed Instance 또는 데이터 컨트롤러와 같은 리소스를 삭제 하기 전에 먼저 다음 작업을 완료 해야 합니다.

1. 간접적으로 연결 된 데이터 컨트롤러의 경우 azure로의 [요금 청구 데이터 업로드](view-billing-data-in-azure.md#upload-billing-data-to-azure---indirectly-connected-mode)에 설명 된 지침에 따라 정확한 청구 계산을 위해 azure에 사용 정보를 내보내고 업로드 합니다.

2. 다음에 설명 된 대로 데이터 컨트롤러에 생성 된 모든 데이터 서비스가 제거 되었는지 확인 합니다.

   - [Azure Arc 지원 SQL Managed Instance 삭제](delete-managed-instance.md)
   - [Azure Arc 사용 PostgreSQL Hyperscale 서버 그룹을 삭제](delete-postgresql-hyperscale-server-group.md)합니다.


azure arc에서 SQL 사용 하도록 설정 된 기존 인스턴스를 삭제 한 후에는 관리 되는 인스턴스 및/또는 azure arc 지원 postgres의 서버 그룹을 사용 하 여 연결 모드의 적절 한 방법 중 하나를 사용 하 여 데이터 컨트롤러를 삭제 합니다.

> [!Note]
> 직접 연결 모드에서 데이터 컨트롤러를 배포한 경우 다음 단계를 수행 합니다.
> - Azure Portal 또는을 (를) [사용 하 여 직접 연결 모드에서 데이터 컨트롤러 삭제](#delete-data-controller-in-directly-connected-mode-using-azure-portal)
> - [Azure CLI를 사용 하 여 직접 연결 모드에서 데이터 컨트롤러를 삭제](#delete-data-controller-in-directly-connected-mode-using-azure-cli) 한 다음 AZURE PORTAL 또는 CLI에서 데이터 컨트롤러를 삭제 한 다음 (2) Kubernetes 클러스터 아티팩트 삭제를 클릭 합니다.
>
>데이터 컨트롤러를 간접적으로 연결 된 모드로 배포한 경우에는 [간접적으로 연결 된 모드에서 데이터 컨트롤러를 삭제](#delete-data-controller-in-indirectly-connected-mode)하는 단계를 수행 합니다. 

## <a name="delete-data-controller-in-directly-connected-mode-using-azure-portal"></a>Azure Portal를 사용 하 여 직접 연결 모드에서 데이터 컨트롤러 삭제

Azure Portal에서:
1. 리소스 그룹으로 이동 하 여 데이터 컨트롤러를 삭제 합니다.
2. Azure Arc 사용 Kubernetes 클러스터를 선택 하 고 개요 페이지로 이동 합니다.
    - 설정 아래에서 **확장** 을 선택 합니다.
    - 확장 페이지에서 Azure Arc data services 확장 (유형)을 선택 `microsoft.arcdataservices` 하 고 **제거** 시를 선택 합니다.
3. 필요에 따라 데이터 컨트롤러가 배포 되는 사용자 지정 위치를 삭제 합니다.
4. 필요에 따라 네임 스페이스에 다른 리소스가 생성 되지 않은 경우 Kubernetes 클러스터에서 네임 스페이스를 삭제할 수도 있습니다.

[Azure Portal을 사용하여 Azure 리소스 관리](../../azure-resource-manager/management/manage-resources-portal.md)를 참조하세요.


## <a name="delete-data-controller-in-directly-connected-mode-using-azure-cli"></a>Azure CLI를 사용 하 여 직접 연결 모드에서 데이터 컨트롤러 삭제

Azure CLI를 사용 하 여 직접 연결 된 모드에서 데이터 컨트롤러를 삭제 하려면 다음 세 가지 단계를 수행 합니다.

1. [데이터 컨트롤러 삭제](#delete-the-data-controller)
1. [데이터 컨트롤러 확장 삭제](#delete-the-data-controller-extension)
1. [사용자 지정 위치 삭제](#delete-the-custom-location)

### <a name="delete-the-data-controller"></a>데이터 컨트롤러 삭제
Kubernetes 클러스터에 연결한 후 다음 명령을 실행 하 여 데이터 컨트롤러를 삭제 합니다.

```azurecli
az arcdata dc delete --name <name of datacontroller> --resource-group <name of resource-group>

## Example
az arcdata dc delete --name arcdc --resource-group myrg
```

### <a name="delete-the-data-controller-extension"></a>데이터 컨트롤러 확장 삭제

데이터 컨트롤러를 삭제 한 후 아래에 설명 된 대로 데이터 컨트롤러 확장을 삭제 합니다. Arc data controller 확장의 이름을 가져오려면 Azure Portal에서 연결 된 클러스터의 개요 페이지로 이동 하 고 확장 탭 아래에서 다음 명령을 사용 하 여 클러스터의 모든 확장 목록을 가져올 수 있습니다.

```azurecli
az k8s-extension list --resource-group <name of resource-group> --cluster-name <name of connected cluster> --cluster-type connectedClusters

## Example
az k8s-extension list --resource-group myrg --cluster-name mycluster --cluster-type  connectedClusters
```
Arc data controller 확장의 이름을 찾았으면 다음을 실행 하 여 삭제 합니다.

```azurecli
az k8s-extension delete --resource-group <name of resource-group> --cluster-name <name of connected cluster> --cluster-type connectedClusters --name <name of your Arc data controller extension> 

## Example
az k8s-extension delete --resource-group myrg --cluster-name mycluster --cluster-type connectedClusters --name myadsextension 
```

위의 작업이 완료 될 때까지 몇 분 정도 기다립니다. 다음 명령을 실행 하 여 상태를 확인 하 여 데이터 컨트롤러를 삭제 했는지 확인 합니다.

```console
kubectl get datacontrollers -A
```

### <a name="delete-the-custom-location"></a>사용자 지정 위치 삭제

이 사용자 지정 위치와 연결 된 다른 확장이 없으면 다음과 같이 사용자 지정 위치를 삭제 합니다.

```azurecli
az customlocation delete --name <Name of customlocation> --resource-group <Name of resource group>

## Example
az customlocation delete --name myCL --resource-group myrg
```

## <a name="delete-data-controller-in-indirectly-connected-mode"></a>간접적으로 연결 된 모드에서 데이터 컨트롤러 삭제 

정의에 따라 간접적으로 연결 된 데이터 컨트롤러 배포를 사용 하 여 Azure Portal Kubernetes 클러스터를 인식할 수 없습니다. 따라서 데이터 컨트롤러를 삭제 하려면 Kubernetes 클러스터에서 해당 컨트롤러를 삭제 하 고 두 단계를 Azure Portal 해야 합니다.

1. [클러스터에서 간접적으로 연결 된 모드의 데이터 컨트롤러 삭제](#delete-data-controller-in-indirectly-connected-mode-from-cluster) 
1. [Azure Portal에서 간접적으로 연결 된 모드의 데이터 컨트롤러를 삭제 합니다.](#delete-data-controller-in-indirectly-connected-mode-from-azure-portal)

### <a name="delete-data-controller-in-indirectly-connected-mode-from-cluster"></a>클러스터에서 간접적으로 연결 된 모드의 데이터 컨트롤러 삭제

다음 명령을 실행 하 여 Kubernetes 클러스터의 데이터 컨트롤러를 삭제 합니다.

```azurecli
az arcdata dc delete --name <name of datacontroller> --k8s-namespace <namespace of data controller> --use-k8s

## Example
az arcdata dc delete --name arcdc --k8s-namespace arc --use-k8s
```

### <a name="delete-data-controller-in-indirectly-connected-mode-from-azure-portal"></a>Azure Portal에서 간접적으로 연결 된 모드의 데이터 컨트롤러를 삭제 합니다.

Azure Portal에서 데이터 컨트롤러를 포함 하는 리소스 그룹을 찾아 삭제 합니다. 

## <a name="delete-kubernetes-cluster-artifacts"></a>Kubernetes 클러스터 아티팩트 삭제

위에서 설명한 대로 데이터 컨트롤러를 삭제 한 후에는 아래 단계에 따라 Azure Arc 사용 데이터 서비스와 관련 된 모든 아티팩트를 완전히 제거 합니다. 부분 또는 실패 한 배포가 있거나 단순히 Azure Arc 사용 데이터 서비스를 다시 설치 하려는 경우에는 모든 아티팩트를 제거 해야 할 수 있습니다.

```console
## Substitute your namespace into the variable
export mynamespace="arc"


## Delete Custom Resource Definitions
kubectl delete crd datacontrollers.arcdata.microsoft.com
kubectl delete crd postgresqls.arcdata.microsoft.com
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com
kubectl delete crd sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com
kubectl delete crd dags.sql.arcdata.microsoft.com
kubectl delete crd exporttasks.tasks.arcdata.microsoft.com
kubectl delete crd monitors.arcdata.microsoft.com

## Delete Cluster roles and Cluster role bindings
kubectl delete clusterrole arcdataservices-extension
kubectl delete clusterrole $mynamespace:cr-arc-metricsdc-reader
kubectl delete clusterrole $mynamespace:cr-arc-dc-watch
kubectl delete clusterrole cr-arc-webhook-job
kubectl delete clusterrole $mynamespace:cr-upgrade-worker

kubectl delete clusterrolebinding $mynamespace:crb-arc-metricsdc-reader
kubectl delete clusterrolebinding $mynamespace:crb-arc-dc-watch
kubectl delete clusterrolebinding crb-arc-webhook-job
kubectl delete clusterrolebinding $mynamespace:crb-upgrade-worker

## API services Up to May 2021 release
kubectl delete apiservice v1alpha1.arcdata.microsoft.com
kubectl delete apiservice v1alpha1.sql.arcdata.microsoft.com

## June 2021 release
kubectl delete apiservice v1beta1.arcdata.microsoft.com
kubectl delete apiservice v1beta1.sql.arcdata.microsoft.com

## GA/July 2021 release
kubectl delete apiservice v1.arcdata.microsoft.com
kubectl delete apiservice v1.sql.arcdata.microsoft.com

## Delete mutatingwebhookconfiguration
kubectl delete mutatingwebhookconfiguration arcdata.microsoft.com-webhook-$mynamespace
```

필요에 따라 다음과 같이 네임 스페이스도 삭제 합니다.
```
kubectl delete --namespace <name of namespace>

## Example:
kubectl delete --namespace arc
```

## <a name="verify-all-objects-are-deleted"></a>모든 개체가 삭제 되었는지 확인

1. `kubectl get crd`을 실행 하 고를 포함 하는 결과가 없는지 확인 `*.arcdata.microsoft.com` 합니다.
2. `kubectl get clusterrole`을 실행 하 고 형식에 클러스터 역할이 없는지 확인 `<namespace>:cr-*` 합니다.
3. `kubectl get clusterrolebindings`을 실행 하 고 형식의 클러스터 역할 바인딩이 없는지 확인 `<namespace>:crb-*` 합니다.
