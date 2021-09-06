---
title: AKS(Azure Kubernetes Service)에서 GPU 사용
description: AKS(Azure Kubernetes Service)에서 고성능 컴퓨팅 또는 그래픽 집약적 워크로드에 GPU를 사용하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 08/06/2021
ms.openlocfilehash: fa7415f015ad17cc2e8a5ff4822c8ff53578f054
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529420"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 계산 집약적 워크로드에 GPU 사용

GPU(그래픽 처리 장치)는 그래픽 및 시각화 워크로드 같은 계산 집약적 워크로드에 자주 사용됩니다. AKS는 Kubernetes에서 이러한 계산 집약적 워크로드를 실행할 수 있도록 GPU 지원 노드 풀 만들기를 지원합니다. 사용 가능한 GPU 지원 VM에 대한 자세한 내용은 [Azure의 GPU 최적화 VM 크기][gpu-skus]를 참조하세요. AKS 노드 풀의 경우 권장하는 최소 크기는 *Standard_NC6* 입니다.

> [!NOTE]
> GPU 지원 VM에는 더 높은 가격 및 지역 가용성에 맞는 특별한 하드웨어가 포함되어 있습니다. 자세한 내용은 [가격 책정][azure-pricing] 도구 및 [지역 가용성][azure-availability]을 참조하세요.

현재 Linux 노드 풀에서만 GPU 사용 노드 풀을 사용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요할 경우 [빠른 시작: Azure CLI를 사용하여 Azure Kubernetes Service 클러스터 배포][aks-quickstart]를 참조하세요.

또한 Azure CLI 버전 2.0.64 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="get-the-credentials-for-your-cluster"></a>클러스터의 자격 증명 가져오기

[az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 AKS 클러스터의 자격 증명을 가져옵니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에서 *myAKSCluster* 에 대한 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-the-nvidia-device-plugin"></a>NVIDIA 디바이스 플러그 인 추가

두 가지 옵션으로 NVIDIA 디바이스 플러그 인을 추가할 수 있습니다.

* AKS GPU 이미지 사용
* NVIDIA 디바이스 플러그 인 수동 설치

> [!WARNING]
> 위의 옵션 중 하나를 사용할 수 있지만, AKS GPU 이미지를 사용하는 클러스터가 있는 NVIDIA 디바이스 플러그 인 디먼 집합은 수동으로 설치하면 안 됩니다.

### <a name="update-your-cluster-to-use-the-aks-gpu-image-preview"></a>AKS GPU 이미지를 사용하도록 클러스터 업데이트(미리 보기)

AKS는 [Kubernetes용 NVIDIA 디바이스 플러그 인][nvidia-github]을 이미 포함하고 있는 완전히 구성된 AKS 이미지를 제공합니다.

`GPUDedicatedVHDPreview` 기능을 등록합니다.

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

상태가 **등록됨** 으로 표시되는 데 몇 분 정도 걸릴 수 있습니다. [az feature list](/cli/azure/feature#az_feature_list) 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

상태가 등록됨으로 표시되면 [az provider register](/cli/azure/provider#az_provider_register) 명령을 사용하여 `Microsoft.ContainerService` 리소스 공급자 등록 상태를 새로 고칩니다.

```azurecli
az provider register --namespace Microsoft.ContainerService
```

aks-preview CLI 확장을 설치하려면 다음 Azure CLI 명령을 사용합니다.

```azurecli
az extension add --name aks-preview
```

aks-preview CLI 확장을 업데이트하려면 다음 Azure CLI 명령을 사용합니다.

```azurecli
az extension update --name aks-preview
```

## <a name="add-a-node-pool-for-gpu-nodes"></a>GPU 노드에 대한 노드 풀 추가

노드 풀을 클러스터에 추가하려면 [az aks nodepool add][az-aks-nodepool-add]를 사용합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunp \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --node-taints sku=gpu:NoSchedule \
    --aks-custom-headers UseGPUDedicatedVHD=true \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3
```

위의 명령에서는 이름이 *gpunp* 인 노드 풀을 *myResourceGroup* 리소스 그룹의 *myAKSCluster* 에 추가합니다. 또한 이 명령은 노드 풀의 노드에 대한 VM 크기를 *Standard_NC6* 으로 설정하고, 클러스터 자동 크기 조정기를 사용하도록 설정하고, 노드 풀에서 최소 1개, 최대 3개의 노드를 유지 관리하도록 클러스터 자동 크기 조정기를 구성하고, 새 노드 풀에 특수화된 AKS GPU 이미지 노드를 지정하며, 노드 풀에 대해 *sku=gpu:NoSchedule* taint를 지정합니다.

> [!NOTE]
> taint와 VM 크기는 노드 풀을 만드는 동안 노드 풀에 대해서만 설정할 수 있지만 자동 크기 조정기 설정은 언제든 업데이트할 수 있습니다.

> [!NOTE]
> GPU SKU에 2세대 VM이 필요한 경우 *--aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true* 를 사용합니다. 예를 들면 다음과 같습니다.
> 
> ```azurecli
> az aks nodepool add \
>    --resource-group myResourceGroup \
>    --cluster-name myAKSCluster \
>    --name gpunp \
>    --node-count 1 \
>    --node-vm-size Standard_NC6 \
>    --node-taints sku=gpu:NoSchedule \
>    --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true \
>    --enable-cluster-autoscaler \
>    --min-count 1 \
>    --max-count 3
> ```

### <a name="manually-install-the-nvidia-device-plugin"></a>NVIDIA 디바이스 플러그 인 수동 설치

또는 NVIDIA 디바이스 플러그 인에 대한 DaemonSet을 배포할 수 있습니다. 이 DaemonSet는 각 노드에서 Pod를 실행하여 GPU에 필요한 드라이버를 제공합니다.

노드 풀을 클러스터에 추가하려면 [az aks nodepool add][az-aks-nodepool-add]를 사용합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunp \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --node-taints sku=gpu:NoSchedule \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3
```

위의 명령에서는 이름이 *gpunp* 인 노드 풀을 *myResourceGroup* 리소스 그룹의 *myAKSCluster* 에 추가합니다. 또한 이 명령은 노드 풀의 노드에 대한 VM 크기를 *Standard_NC6* 으로 설정하고, 클러스터 자동 크기 조정기를 사용하도록 설정하고, 노드 풀에서 최소 1개, 최대 3개의 노드를 유지 관리하도록 클러스터 자동 크기 조정기를 구성하며, 노드 풀에 대해 *sku=gpu:NoSchedule* taint를 지정합니다.

> [!NOTE]
> taint와 VM 크기는 노드 풀을 만드는 동안 노드 풀에 대해서만 설정할 수 있지만 자동 크기 조정기 설정은 언제든 업데이트할 수 있습니다.

*gpu-resources* 같은 [kubectl create namespace][kubectl-create] 명령을 사용하여 네임스페이스를 만듭니다.

```console
kubectl create namespace gpu-resources
```

*nvidia-device-plugin-ds.yaml* 이라는 파일을 만들고 다음 YAML 매니페스트를 붙여넣습니다. 이 매니페스트는 [Kubernetes 프로젝트에 대한 NVIDIA 장치 플러그 인][nvidia-github]의 일부로 제공 됩니다.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      - key: "sku"
        operator: "Equal"
        value: "gpu"
        effect: "NoSchedule"
      containers:
      - image: mcr.microsoft.com/oss/nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

다음 예제 출력과 같이 [kubectl apply][kubectl-apply]를 사용하여 DaemonSet를 만들고 NVIDIA 디바이스 플러그 인이 성공적으로 만들어졌는지 확인합니다.

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="confirm-that-gpus-are-schedulable"></a>GPU의 예약 가능 여부 확인

AKS 클러스터를 만들었으면, Kubernetes에서 GPU를 예약할 수 있는지 확인합니다. 먼저, [kubectl get nodes][kubectl-get] 명령을 사용하여 클러스터의 노드를 나열합니다.

```console
$ kubectl get nodes

NAME                   STATUS   ROLES   AGE   VERSION
aks-gpunp-28993262-0   Ready    agent   13m   v1.20.7
```

[kubectl describe node][kubectl-describe] 명령을 사용하여 GPU를 예약할 수 있는지 확인합니다. *용량* 섹션에 GPU가 `nvidia.com/gpu:  1`으로 나열됩니다

압축된 다음 예제는 *aks-nodepool1-18821093-0* 노드에서 GPU를 사용할 수 있음을 보여줍니다.

```console
$ kubectl describe node aks-gpunp-28993262-0

Name:               aks-gpunp-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
[...]
 nvidia.com/gpu:                 1
[...]
```

## <a name="run-a-gpu-enabled-workload"></a>GPU 지원 워크로드 실행

GPU가 실제로 작동하는 모습을 보려면 적절한 리소스 요청을 사용하여 GPU 사용 워크로드를 예약하세요. 이 예제에서는 [MNIST 데이터 세트](http://yann.lecun.com/exdb/mnist/)에 대해 [Tensorflow](https://www.tensorflow.org/) 작업을 실행하겠습니다.

*samples-tf-mnist-demo.yaml* 이라는 파일을 만들고 다음 YAML 매니페스트를 붙여넣습니다. 다음 작업 매니페스트에는 `nvidia.com/gpu: 1`의 리소스 제한이 포함되어 있습니다.

> [!NOTE]
> 드라이버를 호출할 때 CUDA 드라이버 버전이 CUDA 런타임 버전에 적합하지 않다는 버전 불일치 오류가 발생하는 경우, NVIDIA 드라이버 행렬 호환성 차트([https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html))를 검토합니다.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
      tolerations:
      - key: "sku"
        operator: "Equal"
        value: "gpu"
        effect: "NoSchedule"
```

작업을 실행하려면 [kubectl apply][kubectl-apply] 명령을 사용합니다. 이 명령은 매니페스트 파일을 구문 분석하고 정의된 Kubernetes 개체를 만듭니다.

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>GPU 지원 워크로드의 상태 및 출력 보기

[kubectl get jobs][kubectl-get] 명령과 `--watch` 인수를 사용하여 작업 진행 상태를 모니터링합니다. 처음으로 이미지를 끌어와서 데이터 세트를 처리하는 경우 몇 분 정도 걸릴 수 있습니다. *COMPLETIONS* 열에 *1/1* 이 표시되면 작업이 성공적으로 완료된 것입니다. *Ctrl+C* 를 눌러 명령`kubetctl --watch`을 종료합니다.

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

GPU 지원 워크로드의 출력을 보려면 먼저 [kubectl get pods][kubectl-get] 명령을 사용하여 Pod 이름을 가져와야 합니다.

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

이제 [kubectl logs][kubectl-logs] 명령을 사용하여 Pod 로그를 봅니다. 다음 Pod 로그 예제에서 적절한 GPU 디바이스 `Tesla K80`이 발견되었음을 확인합니다. 자체 Pod의 이름을 입력합니다.

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="use-container-insights-to-monitor-gpu-usage"></a>Container Insights를 사용하여 GPU 사용량 모니터링

다음 메트릭을 사용하여 [Container Insights with AKS][aks-container-insights]로 GPU 사용량을 모니터링할 수 있습니다.

| 메트릭 이름 | 메트릭 차원(태그) | Description |
|-------------|-------------------------|-------------|
| containerGpuDutyCycle | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName`, `gpuId`, `gpuModel`, `gpuVendor`   | 과거 샘플 기간(60초) 대비 GPU가 컨테이너를 위해 사용 중/적극적으로 처리 중인 시간의 백분율입니다. 업무 주기는 1에서 100 사이의 숫자입니다. |
| containerGpuLimits | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName` | 각 컨테이너가 하나 이상의 GPU로 한도를 지정할 수 있습니다. GPU의 일부를 요청하거나 제한할 수는 없습니다. |
| containerGpuRequests | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName` | 각 컨테이너가 하나 이상의 GPU를 요청할 수 있습니다. GPU의 일부를 요청하거나 제한할 수는 없습니다. |
| containerGpumemoryTotalBytes | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName`, `gpuId`, `gpuModel`, `gpuVendor` | 특정 컨테이너에 사용할 수 있는 GPU 메모리 크기(바이트)입니다. |
| containerGpumemoryUsedBytes | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `containerName`, `gpuId`, `gpuModel`, `gpuVendor` | 특정 컨테이너에 사용 중인 GPU 메모리 크기(바이트)입니다. |
| nodeGpuAllocatable | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `gpuVendor` | Kubernetes에서 사용할 수 있는 한 노드의 GPU 수입니다. |
| nodeGpuCapacity | `container.azm.ms/clusterId`, `container.azm.ms/clusterName`, `gpuVendor` | 한 노드에 있는 총 GPU 수입니다. |

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 관련 Kubernetes 개체를 제거하려면 다음과 같이 [kubectl delete job][kubectl delete] 명령을 사용합니다.

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>다음 단계

Apache Spark 작업을 실행하려면 [AKS에서 Apache Spark 작업 실행][aks-spark]을 참조하세요.

Kubernetes에서 ML(머신 러닝) 워크로드를 실행하는 방법에 대한 자세한 내용은 [Kubeflow 랩][kubeflow-labs]을 참조하세요.

Azure Machine Learning과 함께 Azure Kubernetes Service를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Kubernetes Service에 모델 배포][azureml-aks]
* [GPU를 사용하여 유추를 위한 딥 러닝 모델 배포][azureml-gpu]
* [Triton Inference Server를 사용한 고성능 서비스 제공][azureml-triton].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-quickstart]: kubernetes-walkthrough.md
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
[azureml-aks]: ../machine-learning/how-to-deploy-azure-kubernetes-service.md
[azureml-gpu]: ../machine-learning/how-to-deploy-inferencing-gpus.md
[azureml-triton]: ../machine-learning/how-to-deploy-with-triton.md
[aks-container-insights]: monitor-aks.md#container-insights