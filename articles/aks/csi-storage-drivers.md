---
title: AKS(Azure Kubernetes Service)에서 CSI(Container Storage Interface) 드라이버 사용
description: AKS(Azure Kubernetes Service) 클러스터에서 Azure 디스크 및 Azure Files용 CSI(Container Storage Interface) 드라이버를 사용하도록 설정하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 10/15/2021
author: palma21
ms.openlocfilehash: 26de8065b5f96b9fc914a824018c7c7a2028b7b9
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065448"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Azure 디스크와 Azure Files용 CSI(Container Storage Interface) 드라이버 사용

CSI(Container Storage Interface)는 임의 블록과 파일 스토리지 시스템을 Kubernetes의 컨테이너화된 워크로드에 공개하는 표준입니다. CSI를 채택하고 사용하면 AKS(Azure Kubernetes Service)는 플러그 인을 작성, 배포, 반복하여 핵심 Kubernetes 코드를 조정하고 릴리스 주기를 기다릴 필요 없이 Kubernetes에서 새로운 스토리지 시스템을 공개하거나 기존 스토리지 시스템을 개선할 수 있습니다.

AKS의 CSI 스토리지 드라이버 지원을 통해 기본적으로 다음을 사용할 수 있습니다.
- [Azure 디스크](azure-disk-csi.md) - Kubernetes *DataDisk* 리소스를 만드는 데 사용할 수 있습니다. 디스크는 고성능 SSD를 통해 지원되는 Azure Premium Storage 또는 일반 HDD나 표준 SSD를 통해 지원되는 Azure Standard Storage를 사용할 수 있습니다. 대부분의 프로덕션 및 개발 워크로드에는 Premium Storage를 사용합니다. Azure 디스크는 *ReadWriteOnce* 로 탑재되므로 단일 Pod에서만 사용할 수 있습니다. 여러 Pod에서 동시에 액세스할 수 있는 스토리지 볼륨에는 Azure Files를 사용합니다.
- [*Azure Files*](azure-files-csi.md) - Azure Storage 계정을 통해 지원되는 SMB 3.0 공유를 Pod에 탑재하는 데 사용할 수 있습니다. Azure Files를 사용하면 여러 노드 및 Pod 간에 데이터를 공유할 수 있습니다. Azure Files는 일반 HDD를 통해 지원되는 Azure Standard Storage 또는 고성능 SSD를 통해 지원되는 Azure Premium Storage를 사용할 수 있습니다.

> [!IMPORTANT]
> Kubernetes 버전 1.21부터 Kubernetes는 기본적으로 CSI 드라이버만 사용합니다. 이러한 드라이버는 Kubernetes에서 스토리지 지원의 미래입니다.
> 
> AKS 1.21로 업그레이드하기 전에 수동으로 설치된 오픈 소스 Azure 디스크 및 Azure 파일 CSI 드라이버를 제거하세요.
> 
> ‘인트리 드라이버’는 플러그 인인 새로운 CSI 드라이버와 비교하여 핵심 Kubernetes 코드의 일부인 현재 스토리지 드라이버를 나타냅니다.

## <a name="limitations"></a>제한 사항

- 이 기능은 클러스터를 만들 때만 설정할 수 있습니다.
- CSI 드라이버를 지원하는 최소 Kubernetes 부 버전은 v1.17입니다.
- 기본 스토리지 클래스는 `managed-csi` 스토리지 클래스입니다.

## <a name="install-csi-storage-drivers-on-a-new-cluster-with-version--121"></a>버전이 < 1.21인 새 클러스터에 CSI 스토리지 드라이버 설치

다음 CLI 명령을 사용하여 Azure 디스크 및 Azure Files용 CSI 스토리지 드라이버를 사용할 수 있는 새 클러스터를 만듭니다. `--aks-custom-headers` 플래그를 사용하여 `EnableAzureDiskFileCSIDriver` 기능을 설정합니다.

Azure 리소스 그룹 만들기:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

CSI 스토리지 드라이버를 지원하는 AKS 클러스터를 만듭니다.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure  --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

CSI 스토리지 드라이버 대신 트리 스토리지 드라이버에 클러스터를 만들려면 사용자 지정 `--aks-custom-headers` 매개 변수를 생략하여 이 작업을 수행할 수 있습니다. Kubernetes 버전 1.21부터 Kubernetes는 기본적으로 CSI 드라이버만 사용합니다.


다음을 실행하여 이 노드에 연결할 수 있는 Azure 디스크 기반 볼륨의 수를 확인합니다.

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="install-csi-storage-drivers-on-an-existing-cluster-with-version--121"></a>버전이 < 1.21인 기존 클러스터에 CSI 스토리지 드라이버 설치
 - [AKS 클러스터에서 Azure Disk CSI 드라이버 설정](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/install-driver-on-aks.md)
 - [AKS 클러스터에서 Azure File CSI 드라이버 설정](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/install-driver-on-aks.md)

## <a name="migrating-custom-in-tree-storage-classes-to-csi"></a>사용자 지정 트리 내 스토리지 클래스를 CSI로 마이그레이션
트리 내 스토리지 드라이버를 기반으로 사용자 지정 스토리지 클래스를 만든 경우 클러스터를 1.21.x로 업그레이드할 때 마이그레이션해야 합니다.

스토리지 클래스가 여전히 유효하려면 CSI 공급자로의 명시적 마이그레이션이 필요하지 않지만 CSI 기능(스냅샷 등)을 사용하려면 마이그레이션을 수행해야 합니다.

이러한 스토리지 클래스의 마이그레이션에는 기존 스토리지 클래스를 삭제하고, Azure 디스크를 사용하는 경우 **disk.csi.azure.com** 로 설정된 프로비저닝기를 사용하여 다시 프로비전하고, Azure Files 사용하는 경우 **files.csi.azure.com** 포함됩니다.  Azure 디스크의 예:

### <a name="original-in-tree-storage-class-definition"></a>원래 트리 내 스토리지 클래스 정의

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

### <a name="csi-storage-class-definition"></a>CSI 스토리지 클래스 정의

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: disk.csi.azure.com
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

CSI 스토리지 시스템은 트리 내 드라이버와 동일한 기능을 지원하므로 프로비저닝기만 변경하면 됩니다.


## <a name="next-steps"></a>다음 단계

- Azure 디스크에 CSI 드라이브를 사용하려면 [CSI 드라이버에서 Azure 디스크 사용](azure-disk-csi.md)을 참조하세요.
- Azure Files에 CSI 드라이브를 사용하려면 [CSI 드라이버에서 Azure Files 사용](azure-files-csi.md)을 참조하세요.
- 스토리지 모범 사례에 대한 자세한 내용은 [Azure Kubernetes Service의 스토리지 및 백업 모범 사례][operator-best-practices-storage]를 참조하세요.
- CSI 마이그레이션에 대한 자세한 내용은 [Kubernetes In-Tree CSI 볼륨 마이그레이션을 참조하세요.][csi-migration-community]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[csi-migration-community]: https://kubernetes.io/blog/2019/12/09/kubernetes-1-17-feature-csi-migration-beta
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
