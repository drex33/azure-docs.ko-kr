---
title: Azure Red Hat OpenShift (ARO) 클러스터에서 Azure 스팟 Virtual Machines 사용
description: Azure Red Hat OpenShift (ARO)에서 Azure 스팟 Virtual Machines를 활용 하는 방법을 알아봅니다.
author: nilsanderselde
ms.author: suvetriv
ms.service: azure-redhat-openshift
keywords: 스폿, 노드, aro, 배포, openshift, red hat
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 941db40b96d9e7a00c32b42817a3f2a93e17643a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084429"
---
# <a name="use-azure-spot-virtual-machines-in-an-azure-red-hat-openshift-aro-cluster"></a>Azure Red Hat OpenShift (ARO) 클러스터에서 Azure 스팟 Virtual Machines 사용

이 문서에서는 azure 스폿 Virtual Machines를 사용 하도록 Azure Red Hat OpenShift 클러스터 (ARO)를 구성 하는 데 필요한 세부 정보를 제공 합니다.

Azure 스폿 가상 머신을 사용하면 대폭 절감된 비용으로 사용되지 않은 용량을 활용할 수 있습니다. Azure에 용량이 다시 필요한 경우 언제든지 인프라에서 Azure 스폿 가상 머신을 제거합니다. 스폿 인스턴스에 대 한 자세한 내용은 [스팟 Virtual Machines](../virtual-machines/spot-vms.md)를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

시작 하기 전에 Azure Red Hat Openshift 클러스터를 배포 했는지 확인 합니다. ARO 클러스터가 필요한 경우 퍼블릭 클러스터에 대해서는 [ARO 빠른 시작](tutorial-create-cluster.md)을 참조하고, 프라이빗 클러스터에 대해서는 [프라이빗 클러스터 자습서](howto-create-private-cluster-4x.md)를 참조하세요. 클러스터에서 스폿 Vm을 사용 하도록 구성 하는 단계는 개인 및 공용 클러스터 모두에 대해 동일 합니다.

## <a name="add-spot-vms"></a>스폿 Vm 추가

Azure Red Hat Openshift의 컴퓨터 관리는 MachineSet을 사용 하 여 수행 됩니다. MachineSet 리소스는 컴퓨터 그룹입니다. MachineSets은 ReplicaSets가 pod 하는 컴퓨터에 대 한 것입니다. 더 많은 컴퓨터가 필요 하거나 규모를 축소 해야 하는 경우 컴퓨터에 설정 된 *복제본* 필드를 계산 요구에 맞게 변경 합니다. 자세히 알아보려면 OpenShift [Machineset 설명서](https://docs.openshift.com/container-platform/4.8/machine_management/creating_machinesets/creating-machineset-azure.html) 를 확인 하세요.

지점 Vm의 사용은 `spotVMOptions` MachineSet의 템플릿 사양 내에 필드를 추가 하 여 지정 합니다.
이 MachineSet을 만들려면 다음을 수행 합니다.

1. 클러스터에서 실행 되는 MachineSet의 복사본을 가져옵니다.
2. 수정 된 MachineSet 구성을 만듭니다.
3. 이 MachineSet을 클러스터에 배포

먼저 [CLI를 사용 하 여 OpenShift 클러스터에 연결](tutorial-connect-cluster.md)합니다.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

다음으로, 클러스터에 대 한 MachineSets를 나열 합니다. 기본 클러스터에는 3 개의 MachineSets가 배포 됩니다. 
```azurecli-interactive
oc get machinesets -n openshift-machine-api
```

다음은이 명령의 샘플 출력을 보여 줍니다. 
```
NAME                                    DESIRED   CURRENT   READY   AVAILABLE   AGE
aro-cluster-5t2dj-worker-eastus1   1         1         1       1           2d22h
aro-cluster-5t2dj-worker-eastus2   1         1         1       1           2d22h
aro-cluster-5t2dj-worker-eastus3   1         1         1       1           2d22h
```

다음에는 배포 된 MachineSet을 설명 합니다. 을 \<machineset\> 위에 나열 된 MachineSets 중 하나로 바꾸고이를 파일에 출력 합니다.

```azurecli-interactive
oc get machineset <machineset> -n openshift-machine-api -o yaml > spotmachineset.yaml
```

MachineSet에서 다음 매개 변수를 변경 해야 합니다.
- `metadata.name`
- `spec.selector.matchLabels.machine.openshift.io/cluster-api-machineset`
- `spec.template.metadata.labels.machine.openshift.io/cluster-api-machineset`
- `spec.template.spec.providerSpec.value.spotVMOptions` 이 필드를 추가 하 고로 설정 `{}` 합니다.


요약 별색 MachineSet YAML의 예는 아래와 같습니다. 여기에는 컨텍스트에 대 한 추가 정보를 포함 하 여 기존 worker MachineSet에 새 스폿 MachineSet을 기반으로 하는 경우에 수행 해야 하는 주요 변경 사항이 강조 표시 되어 있습니다. (전체 기능 MachineSet을 나타내지 않으며, 많은 필드가 아래에 생략 되었습니다.)

```
apiVersion: machine.openshift.io/v1beta1
kind: MachineSet
metadata:
  name: aro-cluster-abcd1-spot-eastus
spec:
  replicas: 2
  selector:
    matchLabels:
      machine.openshift.io/cluster-api-cluster: aro-cluster-abcd1
      machine.openshift.io/cluster-api-machineset: aro-cluster-abcd1-spot-eastus
  template:
    metadata:
        machine.openshift.io/cluster-api-machineset: aro-cluster-abcd1-spot-eastus
    spec:
      providerSpec:
        value:
          spotVMOptions: {}
      taints:
        - effect: NoExecute
          key: spot
          value: 'true'
```

파일이 업데이트 되 면 적용 합니다.

```azurecli-interactive
oc create -f spotmachineset.yaml
```

MachineSet가 성공적으로 만들어졌는지 확인 하려면 다음 명령을 실행 합니다.
```azurecli-interactive
oc get machinesets -n openshift-machine-api
```

샘플 출력은 다음과 같습니다. 컴퓨터가 "준비" 상태인 경우 Machineset은 준비가 된 것입니다.
```
  NAME                                    DESIRED   CURRENT   READY   AVAILABLE   AGE
aro-cluster-5t2dj-worker-eastus1           1         1         1       1           3d1h
aro-cluster-5t2dj-worker-eastus2           1         1         1       1           3d1h
aro-cluster-5t2dj-worker-eastus3           1         1         1       1           3d1h
spot                                       1         1         1       1           2m47s
```

## <a name="schedule-interruptible-workloads"></a>인터럽트가 가능한 작업 예약

중단할 노드가 예약 되지 않도록 taint를 별색 노드에 추가 하 고 예약 하려는 모든 pod에이 taint의 tolerations를 추가 하는 것이 좋습니다. MachineSet 사양을 통해 노드를 taint 수 있습니다.

예를 들어 다음 YAML를에 추가할 수 있습니다 `spec.template.spec` .

```
     taints:
        - effect: NoExecute
          key: spot
          value: 'true'
```

이로 인해 taint에 대 한 toleration가 없는 경우 결과 노드에서 pod이 예약 되지 `spot='true'` 않으며 해당 toleration 없는 모든 pod 제거 됩니다.

Taints 및 tolerations 적용에 대해 자세히 알아보려면 [node taints를 사용 하 여 pod 배치 제어](https://docs.openshift.com/container-platform/4.7/nodes/scheduling/nodes-scheduler-taints-tolerations.html)를 참조 하세요.

## <a name="quota"></a>할당량

사용 중인 컴퓨터 종류에 대 한 할당량이 너무 적으면 (예: 한 노드가 다른 노드를 만들 때 삭제 되는 경우) 할당량 문제 때문에 컴퓨터가 실패 한 상태로 전환 될 수 있습니다. 따라서 필요한 것 보다 약간 더 높은 지점 인스턴스에 사용 하는 컴퓨터 유형의 할당량을 설정 하는 것이 좋습니다. 여기서 n은 컴퓨터에서 사용 하는 코어 수입니다. 이러한 오버 헤드로 인해 오류가 발생 한 컴퓨터를 해결할 필요가 없습니다 .이는 비교적 간단 하지만 수동 작업입니다.

## <a name="node-readiness"></a>노드 준비

위에서 설명한 지점 VM 설명서에 설명 된 것 처럼 Vm은 더 이상 사용할 수 없거나 지정 된 최대 가격으로 더 이상 사용할 수 없는 경우 할당 해제 프로 비전 상태로 전환 됩니다.

이는 OpenShift에서 **준비 되지 않은** 노드로 매니페스트 합니다. **노드로 프로 비전** 된 단계에서 컴퓨터가 정상 상태로 유지 됩니다.

Vm을 다시 사용할 수 있게 되 면 **준비** 되는 것으로 돌아갑니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="node-stuck-in-not-ready-state-underlying-vm-deallocated"></a>노드가 준비 되지 않음 상태에서 중단 되었습니다. 기본 VM 할당이 취소 되었습니다.

VM의 할당이 취소 된 후 오랫동안 노드가 준비 되지 않음 상태로 유지 되는 경우 해당 VM을 삭제 하거나 해당 OpenShift machine 개체를 삭제 해 볼 수 있습니다.

### <a name="spot-machine-stuck-in-failed-state"></a>지점 컴퓨터가 실패 상태에 걸려 있습니다.

스폿 VM을 사용 하는 컴퓨터 (OpenShift 개체)가 실패 상태에 멈춰 있으면 수동으로 삭제 해 보세요. 403 때문에 VM이 더 이상 존재 하지 않아 삭제할 수 없는 경우 컴퓨터를 편집 하 고 종료자를 제거 합니다.
