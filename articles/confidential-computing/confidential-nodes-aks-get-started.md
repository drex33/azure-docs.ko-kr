---
title: '빠른 시작: Azure CLI를 사용하여 기밀 컴퓨팅 노드가 있는 AKS 클러스터 배포'
description: 기밀 노드가 있는 AKS(Azure Kubernetes Service) 클러스터를 만들고 Azure CLI를 사용하여 Hello World 앱을 배포하는 방법을 알아봅니다.
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: quickstart
ms.date: 04/08/2021
ms.author: amgowda
ms.custom: contentperf-fy21q3, devx-track-azurecli
ms.openlocfilehash: db2eb3bf906ffebe67489ab83cb7e13595b601d5
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112757"
---
# <a name="quickstart-deploy-an-aks-cluster-with-confidential-computing-nodes-by-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 기밀 컴퓨팅 노드가 있는 AKS 클러스터 배포

이 빠른 시작에서는 Azure CLI를 사용하여 기밀 컴퓨팅(DCsv2) 노드가 있는 AKS(Azure Kubernetes Service) 클러스터를 배포합니다. 그런 다음, enclave에서 간단한 Hello World 애플리케이션을 실행합니다. Azure Portal에서 클러스터를 프로비전하고 기밀 컴퓨팅 노드를 추가할 수도 있지만, 이 빠른 시작에서는 Azure CLI에 대해 집중적으로 설명합니다.

AKS는 개발자 또는 클러스터 운영자가 클러스터를 신속하게 배포 및 관리할 수 있게 해주는 관리되는 Kubernetes 서비스입니다. 자세히 알아보려면 [AKS 소개](../aks/intro-kubernetes.md) 및 [AKS 기밀 노드 개요](confidential-nodes-aks-overview.md)를 참조하세요.

기밀 컴퓨팅 노드의 기능은 다음과 같습니다.

- Linux 컨테이너를 지원하는 Linux 작업자 노드
- Ubuntu 18.04 VM 노드를 사용하는 2세대 VM(가상 머신)
- EPC(암호화된 페이지 캐시 메모리)를 활용하는 기밀성 보호 enclave에서 컨테이너를 실행하는 데 도움이 되는 Intel SGX 지원 CPU. 자세한 내용은 [Azure 기밀 컴퓨팅 FAQ](./faq.yml)를 참조하세요.
- 기밀 컴퓨팅 노드에 미리 설치된 Intel SGX DCAP 드라이버. 자세한 내용은 [Azure 기밀 컴퓨팅 FAQ](./faq.yml)를 참조하세요.

> [!NOTE]
> DCsv2 VM은 더 높은 가격 및 지역 가용성에 맞는 특별한 하드웨어를 사용합니다. 자세한 내용은 [사용 가능한 SKU 및 지원되는 지역](virtual-machine-solutions.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작에는 다음이 필요합니다.

- 활성화된 Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 계정을 만듭니다.
- Azure CLI 버전 2.0.64 이상이 배포 머신에 설치되고 구성되어 있어야 합니다. 

  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](../container-registry/container-registry-get-started-azure-cli.md)를 참조하세요.
- 구독에서 사용할 수 있는 DCsv2 코어가 최소 6개 이상이어야 합니다. 

  기본적으로 Azure 구독당 기밀 컴퓨팅 할당량은 8개 VM 코어입니다. 8개가 넘는 코어가 필요한 클러스터를 프로비전하려면 [이러한 지침](../azure-portal/supportability/per-vm-quota-requests.md)에 따라 할당량 증가 티켓을 제출합니다.

## <a name="create-an-aks-cluster-with-confidential-computing-nodes-and-add-on"></a>기밀 컴퓨팅 노드 및 추가 항목을 사용하여 AKS 클러스터 만들기

다음 지침에 따라 기밀 컴퓨팅 추가 항목이 사용하도록 설정된 AKS 클러스터를 만들고, 클러스터에 노드 풀을 추가하고, 자신이 만든 항목을 확인합니다.

### <a name="create-an-aks-cluster-with-a-system-node-pool"></a>시스템 노드 풀을 사용하여 AKS 클러스터 만들기

> [!NOTE]
> 앞서 나열된 필수 조건을 충족하는 AKS 클러스터가 이미 있는 경우 [다음 클러스터 섹션으로 건너뛰어](#add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster) 기밀 컴퓨팅 노드 풀을 추가합니다.

먼저, [az group create][az-group-create] 명령을 사용하여 클러스터에 대한 리소스 그룹을 만듭니다. 다음 예제에서는 *myResourceGroup* 이라는 리소스 그룹을 *westus2* 지역에 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

이제 [az aks create][az-aks-create] 명령을 사용하여 기밀 컴퓨팅 추가 항목이 사용하도록 설정된 AKS 클러스터를 만듭니다.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addons confcom
```

### <a name="add-a-user-node-pool-with-confidential-computing-capabilities-to-the-aks-cluster"></a>기밀 컴퓨팅 기능이 있는 사용자 노드 풀을 AKS 클러스터에 추가합니다. 

다음 명령을 실행하여 세 개의 노드가 있는 `Standard_DC2s_v2` 크기의 사용자 노드 풀을 AKS 클러스터에 추가합니다. [지원되는 DCsv2 SKU 및 지역 목록](../virtual-machines/dcv2-series.md)에서 다른 SKU를 선택할 수 있습니다.

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-vm-size Standard_DC2s_v2
```

명령을 실행하면 DCsv2가 있는 새 노드 풀이 기밀 컴퓨팅 추가 항목 DaemonSet([SGX 디바이스 플러그 인](confidential-nodes-aks-overview.md#confidential-computing-add-on-for-aks))와 함께 표시되어야 합니다.

### <a name="verify-the-node-pool-and-add-on"></a>노드 풀 및 추가 기능 확인

[az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 AKS 클러스터의 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

`kubectl get pods` 명령을 사용하여 노드가 제대로 생성되었고 SGX 관련 DaemonSet가 DCsv2 노드 풀에서 실행 중인지 확인합니다.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

출력이 이전 코드와 일치하면 이제 AKS 클러스터에서 기밀 애플리케이션을 실행할 준비가 된 것입니다.

이 빠른 시작의 [격리된 enclave 애플리케이션에서 Hello World 배포](#hello-world) 섹션으로 이동하여 enclave에서 앱을 테스트할 수 있습니다. 또는 다음 지침에 따라 AKS에 노드 풀을 추가합니다. (AKS는 SGX 노드 풀 및 비-SGX 노드 풀 혼합을 지원합니다.)

## <a name="add-a-confidential-computing-node-pool-to-an-existing-aks-cluster"></a>기존 AKS 클러스터에 기밀 컴퓨팅 노드 풀 추가<a id="existing-cluster"></a>

이 섹션에서는 이 빠른 시작의 앞부분에 나열된 필수 조건을 충족하는 AKS 클러스터를 이미 실행하고 있다고 가정합니다.

### <a name="enable-the-confidential-computing-aks-add-on-on-the-existing-cluster"></a>기존 클러스터에서 기밀 컴퓨팅 AKS 추가 기능 사용

다음 명령을 실행하여 기밀 컴퓨팅 추가 기능을 사용하도록 설정합니다.

```azurecli-interactive
az aks enable-addons --addons confcom --name MyManagedCluster --resource-group MyResourceGroup 
```

### <a name="add-a-dcsv2-user-node-pool-to-the-cluster"></a>DCsv2 사용자 노드 풀을 클러스터에 추가

> [!NOTE]
> 기밀 컴퓨팅 기능을 사용하려면 기존 AKS 클러스터에 하나 이상의 DCsv2 VM SKU 기반 노드 풀이 있어야 합니다. 기밀 컴퓨팅용 DCs-v2 VM SKU에 대한 자세한 내용은 [사용 가능한 SKU 및 지원되는 지역](virtual-machine-solutions.md)을 참조하세요.

다음 명령을 실행하여 노드 풀을 만듭니다.

```azurecli-interactive
az aks nodepool add --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup --node-count 1 --node-vm-size Standard_DC4s_v2
```

이름이 *confcompool1* 인 새 노드 풀이 생성되었는지 확인합니다.

```azurecli-interactive
az aks nodepool list --cluster-name myAKSCluster --resource-group myResourceGroup
```

### <a name="verify-that-daemonsets-are-running-on-confidential-node-pools"></a>DaemonSet가 기밀 노드 풀에서 실행되고 있는지 확인

기존 AKS 클러스터에 로그인하여 다음 확인을 수행합니다.

```console
kubectl get nodes
```

출력에 AKS 클러스터에 새로 추가된 *confcompool1* 풀이 표시됩니다. 다른 DaemonSet가 표시될 수도 있습니다.

```console
$ kubectl get pods --all-namespaces

kube-system     sgx-device-plugin-xxxx     1/1     Running
```

출력이 이전 코드와 일치하면 이제 AKS 클러스터에서 기밀 애플리케이션을 실행할 준비가 된 것입니다. 

## <a name="deploy-hello-world-from-an-isolated-enclave-application"></a>격리된 enclave 애플리케이션에서 Hello World 배포<a id="hello-world"></a>
이제 테스트 애플리케이션을 배포할 준비가 되었습니다. 

*hello-world-enclave.yaml* 이라는 파일을 만들고, 다음 YAML 매니페스트를 붙여넣습니다. [Open Enclave 프로젝트](https://github.com/openenclave/openenclave/tree/master/samples/helloworld)에서 이 애플리케이션 예제 코드를 찾을 수 있습니다. 이 배포에서는 사용자가 *confcom* 추가 항목을 배포했다고 가정합니다.

> [!NOTE]
> 다음 예제에서는 Docker 허브에서 공용 컨테이너 이미지를 가져옵니다. 익명의 끌어오기 요청을 하는 대신 Docker Hub 계정을 사용하여 인증하도록 끌어오기 비밀을 설정하는 것이 좋습니다. 공용 콘텐츠를 사용할 때 신뢰성을 향상시키려면 개인 Azure Container Registry에서 이미지를 가져오고 관리하세요. [공용 이미지 사용에 대해 자세히 알아봅니다](../container-registry/buffer-gate-public-content.md).

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            sgx.intel.com/epc: 5Mi # This limit will automatically place the job into a confidential computing node and mount the required driver volumes. Alternatively, you can target deployment to node pools with node selector.
      restartPolicy: Never
  backoffLimit: 0
  ```

이제 다음 예제 출력과 같이 `kubectl apply` 명령을 사용하여 보안 enclave에서 열 샘플 작업을 만듭니다.

```console
$ kubectl apply -f hello-world-enclave.yaml

job "sgx-test" created
```

다음 명령을 실행하여 워크로드에서 신뢰할 수 있는 실행 환경(enclave)을 성공적으로 만들었는지 확인할 수 있습니다.

```console
$ kubectl get jobs -l app=sgx-test

NAME       COMPLETIONS   DURATION   AGE
sgx-test   1/1           1s         23s
```

```console
$ kubectl get pods -l app=sgx-test

NAME             READY   STATUS      RESTARTS   AGE
sgx-test-rchvg   0/1     Completed   0          25s
```

```console
$ kubectl logs -l app=sgx-test

Hello world from the enclave
Enclave called into host to print: Hello World!
```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 기밀 컴퓨팅 노드 풀을 제거하려면 다음 명령을 사용합니다. 

```azurecli-interactive
az aks nodepool delete --cluster-name myAKSCluster --name confcompool1 --resource-group myResourceGroup
```

AKS 클러스터를 삭제하려면 다음 명령을 사용합니다. 

```azurecli-interactive
az aks delete --resource-group myResourceGroup --cluster-name myAKSCluster
```

## <a name="next-steps"></a>다음 단계

* [GitHub의 기밀 컨테이너 샘플](https://github.com/Azure-Samples/confidential-container-samples)을 사용하여 기밀 컨테이너를 통해 Python, Node 또는 기타 애플리케이션을 실행합니다.

* [GitHub의 enclave 인식 Azure 컨테이너 샘플](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/)을 사용하여 enclave 인식 애플리케이션을 실행합니다.

<!-- LINKS -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
