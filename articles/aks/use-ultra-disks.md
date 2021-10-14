---
title: AKS(Azure Kubernetes Service)에서 Ultra Disk 지원 사용 설정
description: AKS(Azure Kubernetes Service) 클러스터에서 Ultra Disks를 사용하도록 설정하고 구성하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 10/12/2021
ms.openlocfilehash: 07d43fc72d10f1739fb67124feadacc9632206be
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129984861"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service"></a>Azure Kubernetes Service Azure Ultra Disks 사용

[Azure Ultra Disks](../virtual-machines/disks-enable-ultra-ssd.md)는 상태 저장 애플리케이션에 대해 높은 처리량, 높은 IOPS, 일관성 있는 짧은 대기 시간 디스크 스토리지를 제공합니다. Ultra Disks의 주요 혜택 중 하나는 에이전트 노드를 다시 시작하지 않고도 워크로드에 따라 SSD의 성능을 동적으로 변경하는 기능입니다. Ultra Disks는 데이터를 많이 사용하는 워크로드에 적합합니다.

## <a name="before-you-begin"></a>시작하기 전에

해당 기능은 클러스터를 만들거나 노드 풀을 만들 때만 설정할 수 있습니다.

> [!IMPORTANT]
> Azure Ultra Disks에는 특정 VM 시리즈뿐만 아니라 해당 디스크를 지원하는 가용성 영역 및 지역에 배포된 노드 풀이 필요합니다. [**Ultra Disks GA 범위 및 제한 사항**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)을 참조하세요.

### <a name="limitations"></a>제한 사항
- [**Ultra Disks GA 범위 및 제한 사항**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)을 참조하세요.
- Ultra Disks에 지원되는 크기 범위는 100에서 1500 사이입니다.

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Ultra Disks를 사용할 수 있는 새 클러스터 만들기

다음 CLI 명령을 사용하여 Ultra Disks를 활용할 수 있는 AKS 클러스터를 만듭니다. `--enable-ultra-ssd` 플래그를 사용하여 `EnableUltraSSD` 기능을 설정합니다.

Azure 리소스 그룹 만들기:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Ultra Disks를 지원하는 AKS 클러스터를 만듭니다.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_D2s_v3 --zones 1 2 --node-count 2 --enable-ultra-ssd
```

Ultra Disk를 지원하지 않는 클러스터를 만들려는 경우 `--enable-ultra-ssd` 매개 변수를 생략하면 됩니다.

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>기존 클러스터에서 Ultra Disks 사용 설정

Ultra Disks를 지원하는 클러스터에 새 노드 풀을 추가하여 기존 클러스터에서 Ultra Disks를 사용하도록 설정할 수 있습니다. `--enable-ultra-ssd` 플래그를 사용하여 Ultra Disks를 사용하도록 새 노드 풀을 구성합니다.

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_D2s_v3 --zones 1 2 --node-count 2 --enable-ultra-ssd
```

Ultra Disks를 지원하지 않는 새 노드 풀을 만들려는 경우 `--enable-ultra-ssd` 매개 변수를 생략하면 됩니다.

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>스토리지 클래스를 사용하여 동적으로 Ultra Disks 사용

배포 또는 상태 저장 세트에서 Ultra Disks를 사용하려면 [동적 프로비저닝에 스토리지 클래스](azure-disks-dynamic-pv.md)를 사용하면 됩니다.

### <a name="create-the-storage-class"></a>스토리지 클래스 만들기

스토리지 클래스를 사용하여 영구적 볼륨에서 스토리지 단위를 동적으로 생성되는 방법을 정의합니다. Kubernetes 스토리지 클래스에 대한 자세한 내용은 [Kubernetes 스토리지 클래스][kubernetes-storage-classes]를 참조하세요.

이 경우에는 Ultra Disks를 참조하는 스토리지 클래스를 만듭니다. 파일 `azure-ultra-disk-sc.yaml`을 만들고 다음 매니페스트에 복사합니다.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

[kubectl apply][kubectl-apply] 명령을 사용하여 스토리지 클래스를 만들고 *azure-ultra-disk-sc.yaml* 파일을 지정합니다.

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>영구적 볼륨 클레임 만들기

PVC(영구적 볼륨 클레임)을 사용하여 스토리지 클래스를 기반으로 하는 스토리지를 자동으로 프로비전합니다. 이 경우 PVC는 이전에 만든 스토리지 클래스를 사용하여 Ultra Disk를 만들 수 있습니다.

파일 `azure-ultra-disk-pvc.yaml`을 만들고 다음 매니페스트에 복사합니다. 클레임은 *ReadWriteOnce* 액세스 권한이 *1,000GB* 크기의 `ultra-disk`이라는 디스크를 요구합니다. *ultra-disk-sc* 스토리지 클래스를 스토리지 클래스로 지정합니다.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

[kubectl apply][kubectl-apply] 명령을 사용하여 영구 볼륨 클레임을 만들고 *azure-ultra-disk-pvc.yaml* 파일을 지정합니다.

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>영구적 볼륨 사용

영구적 볼륨 클레임이 생성되고 디스크가 성공적으로 프로비전되면 디스크에 액세스하여 Pod를 만들 수 있습니다. 다음 매니페스트는 *ultra-disk* 라는 영구적 볼륨 클레임을 사용하여 `/mnt/azure` 경로에 Azure 디스크를 탑재하는 기본 NGINX Pod를 만듭니다.

파일 `nginx-ultra.yaml`을 만들고 다음 매니페스트에 복사합니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: ultra-disk
```

다음 예제와 같이 [kubectl apply][kubectl-apply] 명령을 사용하여 Pod를 만듭니다.

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

이제 Azure 디스크가 `/mnt/azure` 디렉터리에 탑재된 Pod가 실행되고 있습니다. 다음 압축된 예제에 표시된 것처럼 `kubectl describe pod nginx-ultra`를 통해 Pod를 검사할 때 이 구성을 볼 수 있습니다.

```console
$ kubectl describe pod nginx-ultra

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```


## <a name="next-steps"></a>다음 단계

- Ultra Disks에 대한 자세한 내용은 [Azure Ultra Disks 사용](../virtual-machines/disks-enable-ultra-ssd.md)을 참조하세요.
- 스토리지 모범 사례에 대한 자세한 내용은 [Azure Kubernetes Service(AKS)의 스토리지 및 백업 모범 사례][operator-best-practices-storage]를 참조하세요.

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-snapshot-create]: /cli/azure/snapshot#az_snapshot_create
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-disk-show]: /cli/azure/disk#az_disk_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
