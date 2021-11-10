---
title: AKS(Azure Kubernetes Service)에서 Azure Files용 CSI(Container Storage Interface) 드라이버 사용
description: AKS(Azure Kubernetes Service) 클러스터에서 Azure Files용 CSI(Container Storage Interface) 드라이버를 사용하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 11/09/2021
author: palma21
ms.openlocfilehash: f1a0abb0a89ba9e2c913b29ccc17b591c7fa0d39
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137280"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Azure Files CSI(Container Storage Interface) 드라이버 사용

Azure Files CSI(Container Storage Interface) 드라이버는 Azure Files 공유의 수명 주기를 관리하기 위해 AKS(Azure Kubernetes Service)에서 사용하는 [CSI 사양](https://github.com/container-storage-interface/spec/blob/master/spec.md) 호환 드라이버입니다.

CSI는 임의 블록과 파일 스토리지 시스템을 Kubernetes의 컨테이너화된 워크로드에 공개하는 표준입니다. CSI를 채택하고 사용하면 이제 AKS는 플러그인을 작성, 배포 및 반복하여 핵심 Kubernetes 코드를 조정하고 릴리스 주기를 기다릴 필요없이 Kubernetes에서 새로운 스토리지 시스템을 공개하거나 기존 스토리지 시스템을 개선할 수 있습니다.

CSI 드라이버를 지원하는 AKS 클러스터를 만들려면 [AKS에서 Azure 디스크와 Azure Files에 CSI 드라이버 사용](csi-storage-drivers.md)을 참조하세요.

> [!NOTE]
> *인트리 드라이버* 는 플러그 인인 새로운 CSI 드라이버와 비교하여 핵심 Kubernetes 코드의 일부인 현재 스토리지 드라이버를 나타냅니다.

## <a name="use-a-persistent-volume-with-azure-files"></a>Azure Files에서 영구적 볼륨 사용

[PV(영구 볼륨)](concepts-storage.md#persistent-volumes)는 Kubernetes Pod와 함께 사용하기 위해 프로비저닝된 스토리지 부분을 나타냅니다. PV는 하나 이상의 Pod에서 사용할 수 있으며 동적 또는 정적으로 프로비저닝할 수 있습니다. 여러 Pod에서 동일한 스토리지 볼륨에 동시에 액세스해야 하는 경우 Azure Files에서 [SMB(서버 메시지 블록) 프로토콜][smb-overview]을 사용하여 연결할 수 있습니다. 이 문서에서는 AKS 클러스터에서 여러 Pod에 사용할 Azure Files공유를 동적으로 만드는 방법을 설명합니다. 고정 프로비저닝의 경우 [Azure Files 공유를 사용하여 수동으로 볼륨 만들기 및 사용](azure-files-volume.md)을 참조하세요.

Kubernetes 볼륨에 대한 자세한 내용은 [AKS의 애플리케이션에 대한 스토리지 옵션][concepts-storage]을 참조하세요.

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>기본 제공 스토리지 클래스를 사용하여 동적으로 Azure Files PV 만들기

스토리지 클래스는 Azure Files 공유를 만드는 방법을 정의하는 데 사용됩니다. 스토리지 계정은 Azure Files 공유를 보관할 스토리지 클래스에서 사용할 수 있도록 자동으로 [노드 리소스 그룹][node-resource-group]에 생성됩니다. *skuName* 으로 다음 [Azure Storage 중복도 SKU][storage-skus] 중 하나를 선택합니다.

* **Standard_LRS**: 표준 로컬 중복 스토리지
* **Standard_GRS**: 표준 지역 중복 스토리지
* **Standard_ZRS**: 표준 영역 중복 스토리지
* **Standard_RAGRS**: 표준 읽기 액세스 지역 중복 스토리지
* **Premium_LRS**: 프리미엄 로컬 중복 스토리지

> [!NOTE]
> Azure Files는 Azure Premium Storage를 지원합니다. 최소 프리미엄 파일 공유는 100GB입니다.

AKS에서 스토리지 CSI 드라이버를 사용하는 경우 Azure Files CSI 스토리지 드라이버를 사용하는 두 개의 추가 기본 제공 `StorageClasses`가 있습니다. 추가 CSI 스토리지 클래스는 인트리 기본 스토리지 클래스와 함께 클러스터에서 생성됩니다.

- `azurefile-csi`: Azure Standard Storage를 사용하여 Azure Files 공유를 만듭니다.
- `azurefile-csi-premium`: Azure Premium Storage를 사용하여 Azure Files 공유를 만듭니다.

두 스토리지 클래스의 회수 정책 때문에 각 PV가 삭제될 때 기본 Azure Files 공유가 삭제됩니다. 또한 스토리지 클래스는 파일 공유를 확장 가능하도록 구성하므로 사용자는 영구적 볼륨 클레임(PVC)을 새 크기로 편집하기만 하면 됩니다.

스토리지 클래스를 사용하려면 해당 클래스를 참조하고 사용하는 [PVC](concepts-storage.md#persistent-volume-claims)와 해당 Pod를 만듭니다. PVC를 사용하여 스토리지 클래스를 기반으로 하는 스토리지를 자동으로 프로비저닝합니다. PVC는 미리 만든 스토리지 클래스 중 하나 또는 사용자 정의 스토리지 클래스를 사용하여 원하는 SKU와 크기에 맞는 Azure Files 공유를 만들 수 있습니다. Pod 정의를 만들 때 원하는 스토리지를 요청하는 PVC가 지정됩니다.

[kubectl apply][kubectl-apply] 명령을 사용하여 [현재 날짜를 `outfile`에 사용하는 예제 PVC 및 Pod](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml)를 만듭니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Pod가 실행 중 상태가 된 후에는 다음 명령을 실행하고 출력에 `outfile`이 포함되어 있는지 확인하여 파일 공유가 올바르게 탑재되었는지 확인할 수 있습니다.

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>사용자 지정 스토리지 클래스 만들기

기본 스토리지 클래스는 가장 일반적인 시나리오에 적합하지만, 일부는 아닙니다. 경우에 따라 고유한 매개 변수를 사용하여 고유 스토리지 클래스를 사용자 지정해야 할 수 있습니다. 예를 들어 다음 매니페스트를 사용하여 파일 공유의 `mountOptions`을 구성합니다.

*fileMode* 및 *dirMode* 의 기본값은 Kubernetes 탑재 파일 공유의 경우 *0777* 입니다. 스토리지 클래스 개체에 다른 탑재 옵션을 지정할 수 있습니다.

`azure-file-sc.yaml`이라는 파일을 만들고 다음 예제 매니페스트를 붙여넣습니다.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

[kubectl apply][kubectl-apply] 명령을 사용하여 스토리지 클래스를 만듭니다.

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Azure Files CSI 드라이버는 [영구적 볼륨의 스냅샷](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) 및 기본 파일 공유 의 스냅샷 생성을 지원합니다.

[kubectl apply][kubectl-apply] 명령을 사용하여 [볼륨 스냅샷 클래스](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml)를 만듭니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

[이 자습서의 시작부분에서 동적으로 만든](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes) PVC의 [볼륨 스냅샷](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) `pvc-azurefile`을 만듭니다.

```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml

volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

스냅샷을 올바르게 만들었는지 확인합니다.

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume"></a>영구 볼륨 크기 조정

더 큰 용량의 PVC를 요청할 수 있습니다. PVC 개체를 편집하고 더 큰 크기를 지정합니다. 이 변경때문에 PV를 지원하는 기본 볼륨의 확장이 트리거됩니다.

> [!NOTE]
> 클레임을 충족하기 위해 새 PV를 만들지 않습니다. 대신 기존 볼륨의 크기가 조정됩니다.

AKS에서 기본 제공 `azurefile-csi` 스토리지 클래스는 이미 확장을 지원하므로 [이 스토리지 클래스로 이전에 만든 PVC ](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes)를 사용합니다. PVC가 100Gi 파일 공유를 요청했습니다. 다음을 실행하여 확인할 수 있습니다.

```console
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

`spec.resources.requests.storage` 필드를 늘려 PVC를 확장합니다.

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Pod 내의 PVC와 파일 시스템이 모두 새 크기를 나타내는지 확인합니다.

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```

## <a name="use-a-persistent-volume-with-private-azure-files-storage-private-endpoint"></a>프라이빗 Azure Files 스토리지(프라이빗 엔드포인트)에서 영구 볼륨 사용

Azure Files 리소스가 프라이빗 엔드포인트로 보호되는 경우 다음 매개 변수로 사용자 지정된 고유한 스토리지 클래스를 만들어야 합니다.

* `resourceGroup`: 스토리지 계정이 배포되는 리소스 그룹입니다.
* `storageAccount`: 스토리지 계정 이름입니다.
* `server`: 스토리지 계정 프라이빗 엔드포인트의 FQDN(예: `<storage account name>.privatelink.file.core.windows.net`)입니다.

*private-azure-file-sc.yaml* 이라는 파일을 만들고 다음 예제 매니페스트를 파일에 붙여넣습니다. `<resourceGroup>` 및 `<storageAccountName>`에 대한 값을 대체합니다.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: private-azurefile-csi
provisioner: file.csi.azure.com
allowVolumeExpansion: true
parameters:
  resourceGroup: <resourceGroup>
  storageAccount: <storageAccountName>
  server: <storageAccountName>.privatelink.file.core.windows.net 
reclaimPolicy: Delete
volumeBindingMode: Immediate
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict  # https://linux.die.net/man/8/mount.cifs
  - nosharesock  # reduce probability of reconnect race
  - actimeo=30  # reduce latency for metadata-heavy workload
```

[kubectl apply][kubectl-apply] 명령을 사용하여 스토리지 클래스를 만듭니다.

```console
kubectl apply -f private-azure-file-sc.yaml

storageclass.storage.k8s.io/private-azurefile-csi created
```
  
*private-pvc.yaml* 이라는 파일을 만들고 다음 예제 매니페스트를 파일에 붙여넣습니다.
  
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: private-azurefile-pvc
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: private-azurefile-csi
  resources:
    requests:
      storage: 100Gi
```
  
[kubectl apply][kubectl-apply] 명령을 사용하여 PVC를 만듭니다.
  
```console
kubectl apply -f private-pvc.yaml
```

## <a name="nfs-file-shares"></a>NFS 파일 공유

[Azure Files는 NFS v4.1 프로토콜을 지원](../storage/files/storage-files-how-to-create-nfs-shares.md)합니다. Azure Files에 대한 NFS 4.1 지원은 완벽하게 관리되는 NFS 파일 시스템을 사용 가능하고 내구성이 뛰어난 배포 복원력 스토리지 플랫폼에 구축된 서비스로 제공합니다.

이 옵션은 내부 데이터 업데이트를 사용하는 임의 액세스 워크로드에 최적화되며 전체 POSIX 파일 시스템 지원을 제공합니다. 이 섹션에서는 AKS 클러스터에서 Azure 파일 CSI 드라이버를 사용하여 NFS 공유를 사용하는 방법을 보여 줍니다.

> [!NOTE]
> 클러스터 `Control plane` ID(이름 `AKS Cluster Name` )에 vnet 리소스 그룹에 대한 권한이 있는지 확인합니다. `Contributor`

### <a name="create-nfs-file-share-storage-class"></a>NFS 파일 공유 스토리지 클래스 만들기

각 자리 표시자 편집 아래에 매니페스트가 있는 `nfs-sc.yaml` 파일을 저장합니다.

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile-csi-nfs
provisioner: file.csi.azure.com
parameters:
  protocol: nfs
```

파일을 편집하고 저장한 후 [kubectl apply][kubectl-apply] 명령을 사용하여 스토리지 클래스를 만듭니다.

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi-nfs created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>NFS 지원 파일 공유를 사용하여 배포 만들기

[kubectl apply][kubectl-apply] 명령과 함께 다음 명령을 배포하여 파일 `data.txt`에 타임스탬프를 저장하는 [상태 저장 세트](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/nfs/statefulset.yaml) 예제를 배포할 수 있습니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nfs/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

다음을 실행하여 볼륨 콘텐츠의 유효성을 검사합니다.

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

> [!NOTE]
> NFS 파일 공유는 프리미엄 계정이므로 최소 파일 공유 크기는 100GB입니다. 스토리지 크기가 작은 PVC를 만들 경우 "파일 공유를 만들지 못했습니다. ... size (5)..." 오류가 발생할 수 있습니다.

## <a name="windows-containers"></a>Windows 컨테이너

Azure Files CSI 드라이버는 Windows 노드와 컨테이너도 지원합니다. Windows 컨테이너를 사용하려면 [Windows 컨테이너 자습서](windows-container-cli.md)에 따라 Windows 노드 풀을 추가합니다.

Windows 노드 풀이 있으면 이제 `azurefile-csi`와 같은 기본 제공 스토리지 클래스를 사용할 수 있습니다. [kubectl apply][kubectl-apply] 명령으로 다음 명령을 배포하여 `data.txt` 파일에 타임스탬프를 저장하는 [Windows 기반 상태 저장 세트](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) 예제를 배포할 수 있습니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

다음을 실행하여 볼륨 콘텐츠의 유효성을 검사합니다.

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>다음 단계

- Azure 디스크의 CSI 드라이버를 사용하는 방법을 알아보려면 [CSI 드라이버로 Azure 디스크 사용](azure-disk-csi.md)을 참조하세요.
- 스토리지 모범 사례에 대한 자세한 내용은 [Azure Kubernetes Service의 스토리지 및 백업 모범 사례][operator-best-practices-storage]를 참조하세요.

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md
