---
title: AKS(Azure Kubernetes Service)에서 Azure 디스크용 CSI(Container Storage Interface) 드라이버 사용
description: AKS(Azure Kubernetes Service) 클러스터에서 Azure 디스크용 CSI(Container Storage Interface) 드라이버를 사용하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 19606a1b529fcd7e6140d81361c5434324836198
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535575"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Azure 디스크 CSI(Container Storage Interface) 드라이버 사용
Azure 디스크 CSI(Container Storage Interface) 드라이버는 Azure 디스크의 수명 주기를 관리하기 위해 AKS(Azure Kubernetes Service)에서 사용하는 [CSI 사양](https://github.com/container-storage-interface/spec/blob/master/spec.md) 규격 드라이버입니다.

CSI는 임의 블록과 파일 스토리지 시스템을 Kubernetes의 컨테이너화된 워크로드에 공개하는 표준입니다. CSI를 채택하고 사용하면 AKS는 플러그 인을 작성, 배포 및 반복하여 핵심 Kubernetes 코드를 조정하고 릴리스 주기를 기다릴 필요 없이 Kubernetes에서 새로운 스토리지 시스템을 공개하거나 기존 스토리지 시스템을 개선할 수 있습니다.

CSI 드라이버를 지원하는 AKS 클러스터를 만들려면 [AKS에서 Azure 디스크와 Azure Files에 CSI 드라이버 사용](csi-storage-drivers.md)을 참조하세요.

> [!NOTE]
> *인트리 드라이버* 는 플러그 인인 새로운 CSI 드라이버와 비교하여 핵심 Kubernetes 코드의 일부인 현재 스토리지 드라이버를 나타냅니다.

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>Azure 디스크가 포함된 CSI 영구 볼륨 사용

PV([영구 볼륨](concepts-storage.md#persistent-volumes))는 Kubernetes Pod와 함께 사용하기 위해 프로비저닝된 스토리지 부분을 나타냅니다. PV는 하나 이상의 Pod에서 사용할 수 있으며 동적 또는 정적으로 프로비저닝할 수 있습니다. 이 문서에서는 AKS 클러스터에서 한 Pod에 사용할 Azure 디스크가 포함된 PV를 동적으로 만드는 방법을 설명합니다. 고정 프로비저닝의 경우 [Azure 디스크를 사용하여 수동으로 볼륨 만들기 및 사용](azure-disk-volume.md)을 참조하세요.

Kubernetes 볼륨에 대한 자세한 내용은 [AKS의 애플리케이션에 대한 스토리지 옵션][concepts-storage]을 참조하세요.

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>기본 제공 스토리지 클래스를 사용하여 동적으로 Azure 디스크 PV 만들기

스토리지 클래스를 사용하여 영구적 볼륨에서 스토리지 단위를 동적으로 생성되는 방법을 정의합니다. Kubernetes 스토리지 클래스에 대한 자세한 내용은 [Kubernetes 스토리지 클래스][kubernetes-storage-classes]를 참조하세요. AKS에서 스토리지 CSI 드라이버를 사용하는 경우 Azure 디스크 CSI 스토리지 드라이버를 사용하는 두 개의 추가 기본 제공 `StorageClasses`가 있습니다. 추가 CSI 스토리지 클래스는 인트리 기본 스토리지 클래스와 함께 클러스터에서 생성됩니다.

- `managed-csi`: Azure Standard SSD LRS(로컬 중복 스토리지)를 사용하여 관리 디스크를 만듭니다.
- `managed-csi-premium`: Azure Premium LRS를 사용하여 관리 디스크를 만듭니다.

두 스토리지 클래스의 회수 정책 때문에 각 PV가 삭제될 때 기본 Azure 디스크가 삭제됩니다. 스토리지 클래스도 PV를 확장할 수 있도록 구성합니다. PVC(영구 볼륨 클레임)를 새 크기로 편집만 하면 됩니다.

스토리지 클래스를 활용하려면 해당 클래스를 참조하고 사용하는 [PVC](concepts-storage.md#persistent-volume-claims)와 해당 Pod를 만듭니다. PVC를 사용하여 스토리지 클래스를 기반으로 하는 스토리지를 자동으로 프로비저닝합니다. PVC는 미리 만든 스토리지 클래스 중 하나 또는 사용자 정의 스토리지 클래스를 사용하여 원하는 SKU와 크기에 맞는 Azure 관리 디스크를 만들 수 있습니다. Pod 정의를 만들 때 원하는 스토리지를 요청하는 PVC가 지정됩니다.

[kubectl apply][kubectl-apply] 명령을 사용하여 예제 Pod와 각 PVC를 만듭니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Pod가 실행 중 상태가 되면 `test.txt`라는 새 파일을 만듭니다.

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

이제 다음 명령을 실행하고 출력에 `test.txt` 파일이 표시되는지 확인하여 디스크가 올바르게 탑재되었는지 확인할 수 있습니다.

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>사용자 지정 스토리지 클래스 만들기

기본 스토리지 클래스는 가장 일반적인 시나리오에 적합하지만, 일부는 아닙니다. 경우에 따라 고유한 매개 변수를 사용하여 고유 스토리지 클래스를 사용자 지정해야 할 수 있습니다. 예를 들어 `volumeBindingMode` 클래스를 변경하려는 시나리오가 있습니다.

PVC를 만든 직후 발생하도록 `volumeBindingMode: Immediate` 클래스를 사용할 수 있습니다. 예를 들어 가용성 영역을 사용하는 경우와 같이 노드 풀의 토폴로지가 제약된 경우, PV는 Pod의 예약 요구 사항(이 경우 특정 영역에 있음)에 관한 지식 없이 바인딩되거나 프로비저닝됩니다.

이 시나리오를 해결하려면 PVC를 사용하는 Pod를 만들 때까지 PV의 바인딩과 프로비저닝을 연기하는 `volumeBindingMode: WaitForFirstConsumer`를 사용할 수 있습니다. 이 방식으로 PV는 Pod의 예약 제약 조건에 지정된 가용성 영역(또는 기타 토폴로지)을 준수하고 프로비저닝됩니다. 기본 스토리지 클래스는 `volumeBindingMode: WaitForFirstConsumer` 클래스를 사용합니다.

`sc-azuredisk-csi-waitforfirstconsumer.yaml`이라는 파일을 만들고 다음 매니페스트를 붙여넣습니다.
스토리지 클래스는 `managed-csi` 스토리지 클래스와 같지만 다른 `volumeBindingMode` 클래스가 포함되어 있습니다.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

[kubectl apply][kubectl-apply] 명령을 사용하여 스토리지 클래스를 만들고 `sc-azuredisk-csi-waitforfirstconsumer.yaml` 파일을 지정합니다.

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>볼륨 스냅샷

Azure 디스크 CSI 드라이버는 [영구 볼륨의 스냅샷](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) 만들기를 지원합니다. 이 기능의 일부로 드라이버는 `incremental` 매개 변수에 설정된 값(기본적으로 true)에 따라 *전체* 또는 [*증분* 스냅샷](../virtual-machines/disks-incremental-snapshots.md)을 수행할 수 있습니다.

모든 매개 변수에 관한 자세한 내용은 [볼륨 스냅샷 클래스 매개 변수](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass)를 참조하세요.

### <a name="create-a-volume-snapshot"></a>볼륨 스냅샷 만들기

이 기능에 관한 예제를 보려면 [kubectl apply][kubectl-apply] 명령을 사용하여 [볼륨 스냅샷 클래스](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml)를 만듭니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

이제 [이 자습서의 시작 부분에서 동적으로 만든](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `pvc-azuredisk` PVC에서 [볼륨 스냅샷](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml)을 만들어 보겠습니다.

```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

스냅샷을 올바르게 만들었는지 확인합니다.

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>볼륨 스냅샷을 기반으로 새 PVC 만들기

볼륨 스냅샷을 기반으로 새 PVC를 만들 수 있습니다. 이전 단계에서 만든 스냅샷을 사용하여 [새 PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml)와 [새 Pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml)를 만들어 사용합니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

마지막으로, 콘텐츠를 확인하여 이전에 만든 PVC인지 확인합니다.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

예상대로 이전에 만든 `test.txt` 파일을 계속 볼 수 있습니다.

## <a name="clone-volumes"></a>볼륨 복제

복제된 볼륨은 기존 Kubernetes 볼륨의 복제로 정의됩니다. Kubernetes의 볼륨 복제에 관한 자세한 내용은 [볼륨 복제](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)에 관한 개념 문서를 참조하세요.

Azure 디스크용 CSI 드라이버는 볼륨 복제를 지원합니다. 시연하려면 [이전에 만든](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `azuredisk-pvc`의 [복제된 볼륨](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)을 만들고 [해당 볼륨을 사용하는 새 Pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)를 만듭니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

이제 다음 명령을 실행하고 `test.txt` 생성된 파일이 계속 표시되는지 확인하여 복제된 볼륨의 콘텐츠를 확인할 수 있습니다.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>영구 볼륨 크기 조정

대신 더 큰 용량의 PVC를 요청할 수 있습니다. PVC 개체를 편집하고 더 큰 크기를 지정합니다. 이 변경때문에 PV를 지원하는 기본 볼륨의 확장이 트리거됩니다.

> [!NOTE]
> 클레임을 충족하기 위해 새 PV를 만들지 않습니다. 대신 기존 볼륨의 크기가 조정됩니다.

AKS에서 기본 제공 `managed-csi` 스토리지 클래스는 이미 확장을 허용하므로 [이 스토리지 클래스로 이전에 만든 PVC ](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes)를 사용합니다. PVC에서 10-Gi 영구 볼륨을 요청했습니다. 다음을 실행하여 확인할 수 있습니다.

```console
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> 현재 Azure 디스크 CSI 드라이버는 연결된 Pod가 없는 PVC(및 특정 노드에 탑재되지 않은 볼륨)의 크기 조정만 지원합니다.

따라서 앞에서 만든 Pod를 삭제해 보겠습니다.

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

`spec.resources.requests.storage` 필드를 늘려 PVC를 확장해 보겠습니다.

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

이제 볼륨이 더 큰지 확인해 보겠습니다.

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> PVC는 Pod가 다시 연결될 때까지 새 크기를 반영하지 않습니다.

새 Pod를 만들어 보겠습니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

마지막으로 PVC의 크기와 Pod 내부에서 다음을 확인합니다.

```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>공유 디스크

[Azure 공유 디스크](../virtual-machines/disks-shared.md)는 Azure 디스크를 에이전트 노드에 동시에 연결할 수 있는 Azure 관리 디스크 기능입니다. 관리 디스크를 여러 에이전트 노드에 연결하면 새 클러스터형 애플리케이션을 배포하거나 기존 클러스터형 애플리케이션을 Azure로 마이그레이션할 수 있습니다.

> [!IMPORTANT]
> 현재 Azure 디스크 CSI 드라이버에서는 원시 블록 디바이스(`volumeMode: Block`)만 지원합니다. 애플리케이션은 원시 블록 디바이스로 공개되는 공유 디스크에서 쓰기, 읽기, 잠금, 캐시, 탑재 및 펜싱의 조정과 컨트롤을 관리해야 합니다.

공유 디스크 스토리지 클래스와 PVC가 포함된 다음 명령을 복사하여 `shared-disk.yaml`이라는 파일을 만들어 보겠습니다.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

[kubectl apply][kubectl-apply] 명령을 사용하여 스토리지 클래스를 만들고 `shared-disk.yaml` 파일을 지정합니다.

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
```

이제 다음 명령을 복사하여 `deployment-shared.yml`이라는 파일을 만들어 보겠습니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

[kubectl apply][kubectl-apply] 명령을 사용하여 배포를 만들고 `deployment-shared.yml` 파일을 지정합니다.

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

마지막으로 Pod에서 블록 디바이스를 확인해 보겠습니다.

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Windows 컨테이너

Azure 디스크 CSI 드라이버는 Windows 노드와 컨테이너도 지원합니다. Windows 컨테이너를 사용하려면 [Windows 컨테이너 자습서](windows-container-cli.md)에 따라 Windows 노드 풀을 추가합니다.

Windows 노드 풀이 있으면 이제 `managed-csi`와 같은 기본 제공 스토리지 클래스를 사용할 수 있습니다. [kubectl apply][kubectl-apply] 명령과 함께 다음 명령을 배포하여 `data.txt` 파일에 타임스탬프를 저장하는 [Windows 기반 상태 저장 세트](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) 예제를 배포할 수 있습니다.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

이제 다음을 실행하여 볼륨 콘텐츠의 유효성을 검사할 수 있습니다.

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>다음 단계

- Azure Files의 CSI 드라이버를 사용하는 방법을 알아보려면 [CSI 드라이버로 Azure Files 사용](azure-files-csi.md)을 참조하세요.
- 스토리지 모범 사례에 관한 자세한 내용은 [Azure Kubernetes Service의 스토리지 및 백업 모범 사례][operator-best-practices-storage]를 참조하세요.

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
