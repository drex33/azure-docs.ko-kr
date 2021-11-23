---
title: Azure Arc 사용 데이터 서비스에 대 한 AKS 클러스터 만들기
description: 데이터 컨트롤러를 배포 하기 위해 Azure Kubernetes Service에서 Kubernetes 클러스터를 만드는 방법을 보여 줍니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/19/2021
ms.topic: how-to
ms.openlocfilehash: a9d9ac6b2e988b564a6c782710876cdc48443779
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132958780"
---
# <a name="create-cluster-on-aks"></a>AKS에서 클러스터 만들기

이 문서에서는 AKS (Azure Kubernetes Service)에서 3 노드 Kubernetes 클러스터를 만드는 방법을 보여 줍니다. 

이 클러스터를 사용 하 여 Azure Arc 사용 데이터 서비스를 시연 하 고 테스트 하 고 경험을 얻을 수 있습니다.

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

클러스터용 리소스 그룹을 만듭니다. 위치에 대해 지원 되는 지역을 지정 합니다. Azure Arc 사용 데이터 서비스의 경우 지원 되는 지역은 [개요](overview.md#supported-regions)에 나열 됩니다.

```azurecli
az group create --name <resource_group_name> --location <location>
```

리소스 그룹에 대 한 자세한 내용은 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)을 참조 하세요.

## <a name="create-kubernetes-cluster"></a>Kubernetes 클러스터 만들기

이전에 만든 리소스 그룹에 클러스터를 만듭니다.

다음 예에서는 모니터링을 사용 하도록 설정 된 3 노드 클러스터를 만들고 누락 된 경우 공개 및 개인 키 파일을 생성 합니다.

```azurecli
az aks create --resource-group <resource_group_name> --name <cluster_name> --node-count 3 --enable-addons monitoring --generate-ssh-keys --node-vm-size Standard_D8s_v3
```

명령 세부 정보는 [az aks create](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_create)를 참조 하세요.

단일 노드 Kubernetes 클러스터의 응용 프로그램을 포함 한 전체 데모를 보려면 [빠른 시작: Azure CLI을 사용 하 여 Azure Kubernetes Service 클러스터 배포](../../aks/kubernetes-walkthrough.md)로 이동 하세요.

## <a name="get-credentials"></a>자격 증명 가져오기

클러스터에 연결 하려면 자격 증명을 가져와야 합니다.

다음 명령을 실행 하 여 자격 증명을 가져옵니다.

   ```azurecli
   az aks get-credentials --resource-group <resource_group_name> --name <cluster_name>
   ```

## <a name="verify-cluster"></a>클러스터 확인

클러스터가 실행 중이 고 현재 연결 컨텍스트가 있는지 확인 하려면 다음을 실행 합니다.

```console
kubectl get nodes
```

이 명령은 노드 목록을 반환 합니다. 다음은 그 예입니다.

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-34164736-vmss000000   Ready    agent   4h28m   v1.20.9
aks-nodepool1-34164736-vmss000001   Ready    agent   4h28m   v1.20.9
aks-nodepool1-34164736-vmss000002   Ready    agent   4h28m   v1.20.9
aks-nodepool1-34164736-vmss000003   Ready    agent   4h28m   v1.20.9
```

## <a name="next-steps"></a>다음 단계

* 데이터 컨트롤러를 만듭니다. 다음 단계는 직접 연결 모드 또는 간접 연결 모드에서 데이터 컨트롤러를 만드는 경우에 따라 달라 집니다.
   * [직접 연결 모드에서 데이터 컨트롤러를 배포 하기 위한 필수 구성 요소](create-data-controller-direct-prerequisites.md) 완료
   * [CLI를 사용하여 Azure Arc 데이터 컨트롤러 만들기](create-data-controller-indirect-cli.md)
