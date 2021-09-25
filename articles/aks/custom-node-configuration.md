---
title: AKS(Azure Kubernetes Service) 노드 풀의 노드 구성 사용자 지정(미리 보기)
description: AKS(Azure Kubernetes Service) 클러스터 노드 및 노드 풀에서 구성을 사용자 지정하는 방법에 대해 알아봅니다.
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c872848791e2de3591c8e3e94d4aa5744c7bbcdc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656382"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>AKS(Azure Kubernetes Service) 노드 풀의 노드 구성 사용자 지정(미리 보기)

노드 구성을 사용자 지정하면 워크로드의 요구에 맞게 OS(운영 체제) 설정 또는 kubelet 매개 변수를 구성하거나 튜닝할 수 있습니다. AKS 클러스터를 만들거나 클러스터에 노드 풀을 추가할 때는 일반적으로 사용되는 OS 및 kubelet 설정의 하위 집합을 사용자 지정할 수 있습니다. 이 하위 집합 이외의 설정을 구성하려면 [디먼 집합을 사용하여 노드에 대한 AKS 지원을 그대로 유지하면서 필요한 구성을 사용자 지정](support-policies.md#shared-responsibility)합니다.

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>`CustomNodeConfigPreview` 미리 보기 기능 등록

kubelet 매개 변수 또는 OS 설정을 사용자 지정할 수 있는 AKS 클러스터 또는 노드 풀을 만들려면 구독에서 `CustomNodeConfigPreview` 기능 플래그를 사용하도록 설정해야 합니다.

`CustomNodeConfigPreview`다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용하여 기능 플래그를 등록 합니다.

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인 합니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

Kubelet 매개 변수 또는 OS 설정을 사용자 지정할 수 있는 AKS 클러스터 또는 노드 풀을 만들려면 최신 *aks-preview* Azure CLI 확장이 필요합니다. [Az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치 합니다. 또는 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 설치 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>사용자 지정 노드 구성 사용

### <a name="kubelet-custom-configuration"></a>Kubelet 사용자 지정 구성

지원되는 Kubelet 매개 변수와 허용되는 값은 다음과 같습니다.

| 매개 변수 | 허용되는 값/간격 | 기본값 | Description |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | 없음, 고정 | 없음 | 고정 정책에서는 노드의 배타적 CPU에 대한 정수 CPU 요청 액세스를 통해 컨테이너가 [보장된 Pod](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/)에 있게 됩니다. |
| `cpuCfsQuota` | true, false | true |  CPU 제한을 지정하는 컨테이너에 대해 CPU CFS 할당량 적용을 사용하거나 사용하지 않도록 설정합니다. | 
| `cpuCfsQuotaPeriod` | 간격(밀리초) | `100ms` | CPU CFS 할당량 기간 값을 설정합니다. | 
| `imageGcHighThreshold` | 0-100 | 85 | 이미지 가비지 수집이 항시 실행된 후 디스크 사용 백분율입니다. 가비지 수집이 **트리거되는** 최소 디스크 사용량입니다. 이미지 가비지 수집을 사용하지 않으려면 100으로 설정합니다. | 
| `imageGcLowThreshold` | 0-100, `imageGcHighThreshold`보다 높지 않음 | 80 | 이미지 가비지 수집이 실행되기 이전의 디스크 사용 백분율입니다. 가비지 수집이 **트리거될 수 있는** 최소 디스크 사용량입니다. |
| `topologyManagerPolicy` | none, best-effort, restricted, single-numa-node | 없음 | NUMA 노드 맞춤 최적화에 대한 자세한 내용은 [여기](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/)를 참조하세요. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | 없음 | 안전하지 않은 sysctl 또는 sysctls 패턴의 허용된 목록입니다. | 
| `containerLogMaxSizeMB` | 크기(MB) | 10MB | 회전되기 전의 컨테이너 로그 파일의 최대 크기입니다(예: 10MB). | 
| `containerLogMaxFiles` | ≥ 2 | 5 | 컨테이너에 제공할 수 있는 컨테이너 로그 파일의 최대 수입니다. | 
| `podMaxPids` | -1에서 커널 PID 제한까지 | -1(∞)| Pod에서 실행할 수 있는 최대 프로세스 ID 양 |

### <a name="linux-os-custom-configuration"></a>Linux OS 사용자 지정 구성

지원되는 OS 설정 및 허용되는 값은 다음과 같습니다.

#### <a name="file-handle-limits"></a>파일 핸들 제한

대규모 트래픽을 처리하는 경우, 처리할 트래픽이 많은 수의 로컬 파일에서 제공되는 것이 일반적입니다. 시스템 메모리를 어느 정도 사용하여 처리량을 높일 수 있도록 아래의 커널 설정과 기본 제공 제한을 조정할 수 있습니다.

| 설정 | 허용되는 값/간격 | 기본값 | Description |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192 - 12000500 | 709620 | Linux 커널이 할당할 최대 파일 핸들 수입니다. 이 값을 높이면 허용되는 열린 파일의 최대 개수를 늘릴 수 있습니다. |
| `fs.inotify.max_user_watches` | 781250 - 2097152 | 1048576 | 시스템에서 허용하는 최대 파일 조사식 수입니다. 각 *조사식* 은 32비트 커널에서 약 90바이트, 64비트 커널에서 약 160바이트입니다. | 
| `fs.aio-max-nr` | 65536 - 6553500 | 65536 | aio-nr은 현재 시스템 전체의 비동기 IO 요청 수를 표시합니다. aio-max-nr을 사용하면 aio-nr이 증가할 수 있는 최댓값을 변경할 수 있습니다. |
| `fs.nr_open` | 8192 - 20000500 | 1048576 | 프로세스에서 할당할 수 있는 최대 파일 핸들 개수입니다. |


#### <a name="socket-and-network-tuning"></a>소켓 및 네트워크 튜닝

대규모의 동시 세션을 처리할 것으로 예상되는 에이전트 노드의 경우 TCP의 서브넷과, 노드 풀별로 조정 가능한 아래의 네트워크 옵션을 사용할 수 있습니다. 

| 설정 | 허용되는 값/간격 | 기본값 | Description |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096 - 3240000 | 16384 | 주어진 수신 대기 소켓에 대해 대기할 수 있는 최대 연결 요청 수입니다. [listen(2)](http://man7.org/linux/man-pages/man2/listen.2.html) 함수에 전달된 백로그 매개 변수 값의 상한입니다. 백로그 인수가 `somaxconn`보다 크면 자동으로 이 제한으로 잘립니다.
| `net.core.netdev_max_backlog` | 1000 - 3240000 | 1000 | 인터페이스가 커널이 처리할 수 있는 것보다 빠르게 패킷을 수신할 때 INPUT 쪽에서 대기 중인 패킷의 최대 개수입니다. |
| `net.core.rmem_max` | 212992 - 134217728 | 212992 | 최대 수신 소켓 버퍼 크기입니다(바이트). |
| `net.core.wmem_max` | 212992 - 134217728 | 212992 | 최대 송신 소켓 버퍼 크기입니다(바이트). | 
| `net.core.optmem_max` | 20480 - 4194304 | 20480 | 소켓당 허용되는 최대 보조 버퍼 크기(옵션 메모리 버퍼)입니다. 소켓 옵션 메모리는 소켓 사용량과 관련한 추가 구조를 저장하기 위해 몇 가지 사례에서 사용됩니다. | 
| `net.ipv4.tcp_max_syn_backlog` | 128 - 3240000 | 16384 | 연결하는 클라이언트로부터 아직 확인을 받지 못한 대기 중인 연결 요청의 최대 개수입니다. 이 숫자를 초과하면 커널이 요청을 삭제하기 시작합니다. |
| `net.ipv4.tcp_max_tw_buckets` | 8000 - 1440000 | 32768 | 시스템이 동시에 보유하는 최대 `timewait` 소켓 수입니다. 이 숫자를 초과하면 시간-대기 소켓이 즉시 파기되고 경고가 출력됩니다. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | 분리된(더 이상 애플리케이션에서 참조하지 않음) 연결이 로컬 쪽에서 중단되기 전까지 FIN_WAIT_2 상태로 유지되는 기간입니다. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | `keepalive`를 사용할 경우 TCP가 `keepalive` 메시지를 보내는 간격입니다. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | 연결이 끊어졌다고 판단할 때까지 TCP가 보내는 `keepalive` 프로브 수입니다. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | 프로브를 보내는 빈도입니다. `tcp_keepalive_probes`를 곱하면 프로브 시작 후 응답하지 않는 연결을 종료하는 시간이 됩니다. | 
| `net.ipv4.tcp_tw_reuse` | 0 또는 1 | 0 | 프로토콜 관점에서 안전할 때 새 연결에 대해 `TIME-WAIT` 소켓을 다시 사용하도록 허용합니다. | 
| `net.ipv4.ip_local_port_range` | 첫 번째: 1024 - 60999, 마지막: 32768 - 65000] | 첫 번째: 32768, 마지막: 60999 | 로컬 포트를 선택하기 위해 TCP 및 UDP 트래픽이 사용하는 로컬 포트 범위입니다. 두 숫자로 구성되는데, 첫 번째 숫자는 에이전트 노드에서 TCP 및 UDP 트래픽에 대해 허용되는 첫 번째 로컬 포트이고, 두 번째 숫자는 마지막 로컬 포트 번호입니다. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128 - 80000 | 4096 | ARP 캐시에 있을 수 있는 최소 항목 수입니다. 항목 수가 이 설정보다 작으면 가비지 수집이 트리거되지 않습니다. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512 - 90000 | 8192 | ARP 캐시에 있을 수 있는 임시 최대 항목 수입니다. 이 임시 최댓값에 도달하고 5초 후에 ARP 가비지 수집이 트리거되므로 이 설정이 가장 중요합니다. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024 - 100000 | 16384 | ARP 캐시의 고정 최대 항목 수입니다. |
| `net.netfilter.nf_conntrack_max` | 131072 - 589824 | 131072 | `nf_conntrack`은 Linux 안에서 NAT에 대한 연결을 추적하는 모듈입니다. `nf_conntrack` 모듈은 해시 테이블을 사용하여 TCP 프로토콜의 *설정된 연결* 레코드를 기록합니다. `nf_conntrack_max`는 해시 테이블에 있는 최대 노드 수, 즉 `nf_conntrack` 모듈에서 지원하는 최대 연결 수 또는 연결 추적 테이블 크기입니다. | 
| `net.netfilter.nf_conntrack_buckets` | 65536 - 147456 | 65536 | `nf_conntrack`은 Linux 안에서 NAT에 대한 연결을 추적하는 모듈입니다. `nf_conntrack` 모듈은 해시 테이블을 사용하여 TCP 프로토콜의 *설정된 연결* 레코드를 기록합니다. `nf_conntrack_buckets`는 해시 테이블의 크기입니다. | 

#### <a name="worker-limits"></a>작업자 제한

파일 설명자 제한과 마찬가지로, 프로세스가 만들 수 있는 작업자 또는 스레드 수는 커널 설정과 사용자 제한 모두에 따라 제한됩니다. AKS에 대한 사용자 제한은 무제한입니다. 

| 설정 | 허용되는 값/간격 | 기본값 | Description |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | 프로세스가 작업자 스레드를 진행할 수 있습니다. 만들 수 있는 모든 스레드의 최대 개수는 커널 설정 `kernel.threads-max`로 설정합니다. | 

#### <a name="virtual-memory"></a>가상 메모리

아래 설정을 사용하여 Linux 커널 VM(가상 메모리) 하위 시스템의 작업과, 더티 데이터의 디스크 `writeout` 작업을 튜닝할 수 있습니다.

| 설정 | 허용되는 값/간격 | 기본값 | Description |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530 - 262144 | 65530 | 이 파일은 프로세스가 보유할 수 있는 최대 메모리 맵 영역 수를 포함합니다. 메모리 맵 영역은 `mmap`, `mprotect` 및 `madvise`를 직접 사용한 `malloc` 호출의 파생 작업으로 사용되며 공유 라이브러리를 로드할 때도 사용됩니다. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | 이 백분율 값은 디렉터리 및 inode 개체의 캐싱에 사용되는 메모리를 확보하기 위한 커널의 추세를 제어합니다. |
| `vm.swappiness` | 0 - 100 | 60 | 이 컨트롤은 커널이 메모리 페이지를 교체하는 수준을 정의하는 데 사용됩니다. 값이 높을수록 강도가 증가하고, 값이 작을수록 교체 규모가 감소합니다. 값이 0이면 사용 가능한 파일 지원 페이지의 규모가 영역의 상위 워터마크보다 작을 때까지 커널에 교체를 시작하지 않도록 지시합니다. | 
| `swapFileSizeMB` | 1MB - [임시 디스크](../virtual-machines/managed-disks-overview.md#temporary-disk) 크기(/dev/sdb) | 없음 | SwapFileSizeMB는 이 노드 풀을 통해 에이전트 노드에 생성되는 스왑 파일의 크기를 MB 단위로 지정합니다. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [Transparent Hugepages](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge)는 프로세서의 메모리 매핑 하드웨어를 더 효율적으로 사용하여 성능을 개선하는 Linux 커널 기능입니다. 사용할 경우 커널은 가능한 모든 상황에서 `hugepages` 할당을 시도하며, `mmap` 지역이 기본적으로 2MB로 맞춰진 경우 모든 Linux 프로세스는 2MB 페이지를 받습니다. `hugepages`가 시스템 전체에서 사용하도록 설정된 경우 애플리케이션은 더 많은 메모리 리소스를 할당하게 될 수 있습니다. 애플리케이션은 넓은 지역을 `mmap`했으나 이 중 1바이트만 터치할 수 있습니다. 이 경우 적절한 이유 없이 4k 페이지 대신 2MB 페이지를 할당할 수 있습니다. 이런 시나리오 때문에 `hugepages`를 시스템 전체에서 사용하지 않도록 설정하거나, `MADV_HUGEPAGE madvise` 지역 안으로 한정할 수 있는 기능이 있는 것입니다. | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | 이 값은 `hugepages`의 가용성을 높이기 위해 커널에서 더 적극적으로 메모리 압축을 사용할지 여부를 제어합니다. | 

> [!IMPORTANT]
> 검색 편의와 가독성을 위해 이 문서에서는 OS 설정이 이름으로 표시되나, [camelCase 대/소문자 표기 규칙](/dotnet/standard/design-guidelines/capitalization-conventions)을 통해 구성 json 파일 또는 AKS API에 추가되어야 합니다.

다음 콘텐츠가 포함된 `kubeletconfig.json` 파일을 만듭니다.

```json
{
 "cpuManagerPolicy": "static",
 "cpuCfsQuota": true,
 "cpuCfsQuotaPeriod": "200ms",
 "imageGcHighThreshold": 90,
 "imageGcLowThreshold": 70,
 "topologyManagerPolicy": "best-effort",
 "allowedUnsafeSysctls": [
  "kernel.msg*",
  "net.*"
],
 "failSwapOn": false
}
```
다음 콘텐츠가 포함된 `linuxosconfig.json` 파일을 만듭니다.

```json
{
 "transparentHugePageEnabled": "madvise",
 "transparentHugePageDefrag": "defer+madvise",
 "swapFileSizeMB": 1500,
 "sysctls": {
  "netCoreSomaxconn": 163849,
  "netIpv4TcpTwReuse": true,
  "netIpv4IpLocalPortRange": "32000 60000"
 }
}
```

이전 단계에서 만든 JSON 파일을 사용하여 kubelet 및 OS 구성을 지정하는 새 클러스터를 만듭니다. 

> [!NOTE]
> 클러스터를 만들 때는 kubelet 구성, OS 구성 또는 둘 다를 지정할 수 있습니다. 클러스터를 만들 때 구성을 지정할 경우 최초 노드 풀의 노드에만 해당 구성이 적용됩니다. JSON 파일에서 구성되지 않은 모든 설정은 기본값을 유지합니다.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

만든 JSON 파일을 사용하여 Kubelet 매개 변수를 지정하는 새 노드 풀을 추가합니다.

> [!NOTE]
> 기존 클러스터에 노드 풀을 추가하는 경우 kubelet 구성, OS 구성 또는 둘 다를 지정할 수 있습니다. 노드 풀을 추가할 때 구성을 지정할 경우 새 노드 풀의 노드에만 해당 구성이 적용됩니다. JSON 파일에서 구성되지 않은 모든 설정은 기본값을 유지합니다.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>다음 단계

- [AKS 클러스터를 구성하는 방법](cluster-configuration.md)을 알아봅니다.
- 클러스터에서 [노드 이미지를 업그레이드](node-image-upgrade.md)하는 방법을 알아봅니다.
- 클러스터를 최신 버전의 Kubernetes로 업그레이드하는 방법을 알아보려면 [AKS(Azure Kubernetes Service) 클러스터 업그레이드](upgrade-cluster.md)를 참조하세요.
- 몇 가지 일반적인 AKS 질문에 대한 답변을 확인하려면 [AKS에 대한 질문과 대답](faq.md) 목록을 참조하세요.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ../azure-monitor/containers/container-insights-log-query.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why
