---
title: AKS (Azure Kubernetes Service)에서 webassembly () 노드 풀을 만들어 WebAssembly (WASM) 워크 로드 (미리 보기)를 실행 합니다.
description: AKS (Azure Kubernetes Service)에서 webassembly () 노드 풀을 만들어 Kubernetes에서 WebAssembly (WASM) 워크 로드를 실행 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 10/12/2021
ms.openlocfilehash: 91649a6c7893970eee5685554385eed1493c0094
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265966"
---
# <a name="create-webassembly-system-interface-wasi-node-pools-in-azure-kubernetes-service-aks-to-run-your-webassembly-wasm-workload-preview"></a>AKS (Azure Kubernetes Service)에서 webassembly () 노드 풀을 만들어 WebAssembly (WASM) 워크 로드 (미리 보기)를 실행 합니다.

[Webassembly ()][wasm] 는 빠른 다운로드와 WASM 런타임의 최대 실행 속도에 최적화 된 이진 형식입니다. WASM 런타임은 대상 아키텍처에서 실행 되며, 호스트 컴퓨터에서 격리 된 샌드박스에서 WebAssemblies를 거의 기본 성능으로 실행 하도록 설계 되었습니다. 기본적으로 WebAssemblies는 명시적으로 허용 되지 않는 한 샌드박스 외부의 호스트에 있는 리소스에 액세스할 수 없으며, 환경 변수 또는 HTTP 트래픽에 액세스 하기 위해 소켓을 통해 통신할 수 없습니다. [WEASEMBMBWASI (Mbambmbomsystem Interface)][wasi] 표준은 기능 모델을 사용 하 여 호스트 외부의 환경 및 리소스에 대 한 액세스를 제공 하는 WASM runtime API를 정의 합니다. [Krustlet][krustlet] 는 WASM 모듈을 Kubernetes에서 실행할 수 있도록 하는 오픈 소스 프로젝트입니다. Krustlet는 WASM/WASI runtime을 사용 하 여 노드에서 실행 되는 kubelet를 만듭니다. AKS를 사용 하면 WASM/WASI runtime 및 Krustlets가 포함 된 노드를 사용 하 여 WASM 어셈블리를 실행 하는 노드 풀을 만들 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

WASM/WASI 노드 풀은 현재 미리 보기로 제공 됩니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

이 문서에서는 [투구 3][helm] 을 사용 하 여 지원 되는 버전의 Kubernetes에 *nginx* 차트를 설치 합니다. 최신 버전의 투구를 사용 하 고 *bitnami* 투구 리포지토리에 액세스할 수 있는지 확인 합니다. 이 문서에 설명 된 단계는 이전 버전의 투구 차트 또는 Kubernetes와 호환 되지 않을 수 있습니다.

또한 다음 리소스를 설치 해야 합니다.

* 최신 버전의 Azure CLI
* `aks-preview`확장 버전 0.5.34 이상

### <a name="register-the-wasmnodepoolpreview-preview-feature"></a>`WasmNodePoolPreview` 미리 보기 기능 등록

기능을 사용하려면 구독에서도 `WasmNodePoolPreview` 기능 플래그를 사용하도록 설정해야 합니다.

`WasmNodePoolPreview`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "WasmNodePoolPreview"
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WasmNodePoolPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>`aks-preview` Azure CLI 설치

*Aks-preview* Azure CLI 확장 버전 0.5.34 이상이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>제한 사항

* 동일한 노드 풀에서 Weasemboma 및 컨테이너를 실행할 수 없습니다.
* Wasmtime 공급자를 사용 하 여 WebAssembly () runtime만 사용할 수 있습니다.
* WASM/WASI 노드 풀은 시스템 노드 풀에 사용할 수 없습니다.
* WASM/WASI 노드 풀의 *os 유형은* Linux 여야 합니다.
* Krustlet는 현재 Azure CNI에서 작동 하지 않습니다. 자세한 내용은 [Kruslet GitHub 문제에 대 한 cni 지원][krustlet-cni-support]을 참조 하세요.
* Krustlet는 WebAssemblies에 대 한 네트워킹 구성을 제공 하지 않습니다. Weasembommanifest는 IP 주소와 같은 네트워킹 구성을 제공 해야 합니다.

## <a name="add-a-wasmwasi-node-pool-to-an-existing-aks-cluster"></a>기존 AKS 클러스터에 WASM/WASI 노드 풀 추가

WASM/WASI 노드 풀을 추가 하려면 [az aks nodepool add][az-aks-nodepool-add] 명령을 사용 합니다. 다음 예에서는 하나의 노드가 있는 *mywasipool* 라는 WASI 노드 풀을 만듭니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mywasipool \
    --node-count 1 \
    --workload-runtime wasmwasi 
```

> [!NOTE]
> *작업 런타임* 매개 변수의 기본값은 *ocicontainer* 입니다. 컨테이너 워크 로드를 실행 하는 노드 풀을 만들려면 *워크 로드 런타임* 매개 변수를 생략 하거나 값을 *ocicontainer* 로 설정 합니다.

을 사용 하 여 *workloadRuntime* 값을 확인 합니다 `az aks nodepool show` . 예를 들면 다음과 같습니다.

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mywasipool
```

다음 예제 출력에서는 *mywasipool* 의 *workloadRuntime* 형식이 *WasmWasi* 을 보여 줍니다.

```output
{
  ...
  "name": "mywasipool",
  ..
  "workloadRuntime": "WasmWasi"
}
```

WASM/WASI 노드 풀의 경우 taint가 and로 설정 되었는지 확인 `kubernetes.io/arch=wasm32-wagi:NoSchedule` 합니다 `kubernetes.io/arch=wasm32-wagi:NoExecute` . 그러면이 노드 풀에서 컨테이너 pod가 예약 되지 않습니다. 또한 `kubernetes.io/arch: wasm32-wasi` WASM pod가 OCI (일반 컨테이너) 노드 풀에서 예약 되지 않도록 하는 nodeLabels가 표시 되어야 합니다.

> [!NOTE]
> WASI 노드 풀의 taints는를 사용 하 여 볼 수 없습니다 `az aks nodepool list` . `kubectl`를 사용 하 여 WASI 노드 풀의 노드에 taints가 설정 되어 있는지 확인 합니다.

[az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 Kubernetes 클러스터에 연결하도록 `kubectl`을 구성합니다. 다음 명령은 아래와 같은 작업을 수행합니다.  

```azurecli
az aks get-credentials -n myakscluster -g myresourcegroup
```

`kubectl get nodes`를 사용 하 여 클러스터의 노드를 표시 합니다.

```output
$ kubectl get nodes -o wide
NAME                                 STATUS   ROLES   AGE   VERSION         INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
aks-mywasipool-12456878-vmss000000   Ready    agent    9m   1.0.0-alpha.1   WASINODE_IP   <none>        <unknown>            <unknown>          mvp
aks-nodepool1-12456878-vmss000000    Ready    agent   13m   v1.20.9         NODE1_IP      <none>        Ubuntu 18.04.6 LTS   5.4.0-1059-azure   containerd://1.4.9+azure
```

이후 단계에서 사용 되는 *WASINODE_IP* 값을 저장 합니다.

`kubectl describe node`WASI 노드 풀의 노드에 레이블 및 taints을 표시 하는 데 사용 합니다. 다음 예제에서는 aks-mywasipool- *12456878-vmss000000* 의 세부 정보를 보여 줍니다.

```output
$ kubectl describe node aks-mywasipool-12456878-vmss000000

Name:               aks-mywasipool-12456878-vmss000000
Roles:              agent
Labels:             agentpool=mywasipool
...
                    kubernetes.io/arch=wasm32-wagi
...
Taints:             kubernetes.io/arch=wasm32-wagi:NoExecute
                    kubernetes.io/arch=wasm32-wagi:NoSchedule
```


## <a name="running-wasmwasi-workload"></a>WASM/WASI 워크 로드 실행

WASM/WASI 노드 풀에서 작업을 실행 하려면 노드 선택기와 tolerations를 배포에 추가 합니다. 예를 들면 다음과 같습니다.

```yml
...
spec:
  nodeSelector:
    kubernetes.io/arch: "wasm32-wagi"
  tolerations:
    - key: "node.kubernetes.io/network-unavailable"
      operator: "Exists"
      effect: "NoSchedule"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoExecute"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoSchedule"
...
```

샘플 배포를 실행 하려면 `wasi-example.yaml` 다음 YAML 정의를 사용 하 여 파일을 만듭니다.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: krustlet-wagi-demo
  labels:
    app: krustlet-wagi-demo
  annotations:
    alpha.wagi.krustlet.dev/default-host: "0.0.0.0:3001"
    alpha.wagi.krustlet.dev/modules: |
      {
        "krustlet-wagi-demo-http-example": {"route": "/http-example", "allowed_hosts": ["https://api.brigade.sh"]},
        "krustlet-wagi-demo-hello": {"route": "/hello/..."},
        "krustlet-wagi-demo-error": {"route": "/error"},
        "krustlet-wagi-demo-log": {"route": "/log"},
        "krustlet-wagi-demo-index": {"route": "/"}
      }
spec:
  hostNetwork: true
  nodeSelector:
    kubernetes.io/arch: wasm32-wagi
  containers:
    - image: webassembly.azurecr.io/krustlet-wagi-demo-http-example:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-http-example
    - image: webassembly.azurecr.io/krustlet-wagi-demo-hello:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-hello
    - image: webassembly.azurecr.io/krustlet-wagi-demo-index:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-index
    - image: webassembly.azurecr.io/krustlet-wagi-demo-error:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-error
    - image: webassembly.azurecr.io/krustlet-wagi-demo-log:v1.0.0
      imagePullPolicy: Always
      name: krustlet-wagi-demo-log
  tolerations:
    - key: "node.kubernetes.io/network-unavailable"
      operator: "Exists"
      effect: "NoSchedule"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoExecute"
    - key: "kubernetes.io/arch"
      operator: "Equal"
      value: "wasm32-wagi"
      effect: "NoSchedule"
```

`kubectl`을 사용 하 여 예제 배포를 실행 합니다.

```azurecli-interactive
kubectl apply -f wasi-example.yaml
```

> [!NOTE]
> 예제 배포의 pod는 *등록* 된 상태를 유지할 수 있습니다. 이 동작은 예상 된 것 이며 다음 단계를 진행 합니다.

`values.yaml`아래 예제 yaml을 사용 하 여를 만들고 *WASINODE_IP* 을 이전 단계의 값으로 바꿉니다.

```yml
serverBlock: |-
  server {
    listen 0.0.0.0:8080;
    location / {
            proxy_pass http://WASINODE_IP:3001;
    }
  }
```

[투구][helm]를 사용 하 여 *bitnami* 리포지토리를 추가 하  고 `values.yaml` 이전 단계에서 만든 파일을 사용 하 여 nginx 차트를 설치 합니다. 위의를 사용 하 여 NGINX를 설치 하면 `values.yaml` 예제 배포에 대 한 역방향 프록시가 생성 되므로 외부 IP 주소를 사용 하 여 액세스할 수 있습니다.

>[!NOTE]
> 다음 예제에서는 Docker 허브에서 공용 컨테이너 이미지를 가져옵니다. 익명의 끌어오기 요청을 하는 대신 Docker Hub 계정을 사용하여 인증하도록 끌어오기 비밀을 설정하는 것이 좋습니다. 공용 콘텐츠를 사용할 때 신뢰성을 향상시키려면 개인 Azure Container Registry에서 이미지를 가져오고 관리하세요. [공용 이미지 사용에 대해 자세히 알아봅니다][dockerhub-callout].

```console
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm install hello-wasi bitnami/nginx -f values.yaml
```

`kubectl get service`을 사용 하 여 *wasi-n워 ix* 서비스의 외부 IP 주소를 표시 합니다.

```output
$ kubectl get service
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
hello-wasi-nginx   LoadBalancer   10.0.58.239   EXTERNAL_IP      80:32379/TCP   112s
kubernetes         ClusterIP      10.0.0.1      <none>           443/TCP        145m
```

`curl` `/hello` *EXTERNAL_IP* 의 경로에 대해 명령이 배포를 실행 하 고 있는지 확인 합니다.

```azurecli-interactive
curl EXTERNAL_IP/hello
```

다음 예제 출력에서는 배포가 실행 되 고 있음을 확인 합니다.

```output
$ curl EXTERNAL_IP/hello
hello world
```

> [!NOTE] 
> 사용자의 도메인에 서비스를 게시하려면, [Azure DNS][azure-dns-zone] 및 [외부-DNS][external-dns] 프로젝트를 참조하세요.

## <a name="clean-up"></a>정리

NGINX을 제거 하려면를 사용 `helm delete` 합니다.

```console
helm delete hello-wasi
```

배포 예제를 제거 하려면를 사용 `kubectl delete` 합니다.

```azurecli-interactive
kubectl delete -f wasi-example.yaml
```

WASM/WASI 노드 풀을 제거 하려면를 사용 `az aks nodepool delete` 합니다.

```azurecli-interactive
az aks nodepool delete --name mywasipool -g myresourcegroup --cluster-name myakscluster
```


<!-- EXTERNAL LINKS -->
[helm]: https://helm.sh/
[krustlet]: https://krustlet.dev/
[krustlet-cni-support]: https://github.com/krustlet/krustlet/issues/533
[wasm]: https://webassembly.org/
[wasi]: https://wasi.dev/
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- INTERNAL LINKS -->

[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[dockerhub-callout]: ../container-registry/buffer-gate-public-content.md
[install-azure-cli]: /cli/azure/install-azure-cli
[use-multiple-node-pools]: use-multiple-node-pools.md
[use-system-pool]: use-system-pools.md
