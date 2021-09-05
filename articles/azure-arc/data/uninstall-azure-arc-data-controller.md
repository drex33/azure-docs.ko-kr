---
title: Azure Arc 데이터 컨트롤러 삭제
description: Azure Arc 데이터 컨트롤러 삭제
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 26784222d49e6f48ed324ce345dcb1f2ba7d4cf1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528931"
---
# <a name="delete-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러 삭제

다음 문서에서는 Azure Arc 데이터 컨트롤러를 삭제하는 방법을 설명합니다.

계속 진행하기 전에 다음과 같이 데이터 컨트롤러에서 생성된 데이터 서비스가 모두 제거되었는지 확인합니다.

## <a name="list--delete-existing-data-services"></a>기존 데이터 서비스 나열 및 삭제

다음 명령을 실행하여 생성된 SQL 관리형 인스턴스가 있는지 확인합니다.

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

위의 목록에 있는 각 SQL 관리형 인스턴스에 대해 다음과 같이 delete 명령을 실행합니다.

```azurecli
az sql mi-arc delete -n <name> --k8s-namespace <namespace> --use-k8s
# for example: az sql mi-arc delete -n sqlinstance1 --k8s-namespace <namespace> --use-k8s
```

마찬가지로, PostgreSQL 하이퍼스케일 인스턴스를 확인하려면 다음을 실행합니다.

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

또한 각 PostgreSQL 하이퍼스케일 인스턴스에 대해 다음과 같이 delete 명령을 실행합니다.

```azurecli
az postgres arc-server delete -n <name> --k8s-namespace <namespace> --use-k8s
# for example: az postgres arc-server delete -n pg1 --k8s-namespace <namespace> --use-k8s
```

## <a name="delete-controller"></a>컨트롤러 삭제

모든 SQL 관리형 인스턴스 및 PostgreSQL 하이퍼스케일 인스턴스를 제거한 후에는 다음과 같이 데이터 컨트롤러를 삭제할 수 있습니다.

```azurecli
az arcdata dc delete -n <name> -ns <namespace>
# for example: az arcdata dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>SCC 제거(Red Hat OpenShift만 해당)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>클러스터 수준 개체 삭제

네임스페이스 범위 개체뿐 아니라 CRD, `clusterroles`, `clusterrolebindings` 등의 클러스터 수준 개체도 삭제하려는 경우 다음 명령을 실행합니다.

```console
# Cleanup azure arc data service artifacts
#Delete CRDs
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
#Delete clusterrole
kubectl delete clusterrole <namespace>:cr-arc-metricsdc-reader
#For example: kubectl delete clusterrole arc:cr-arc-metricsdc-reader
#Delete rolebinding
kubectl delete clusterrolebinding <namespace>:crb-arc-metricsdc-reader
#For example: kubectl delete clusterrolebinding arc:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>필요에 따라 Azure Arc 데이터 컨트롤러 네임스페이스 삭제


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example: kubectl delete ns arc
```

## <a name="next-steps"></a>다음 단계

[Azure Arc 지원 데이터 서비스란?](overview.md)
