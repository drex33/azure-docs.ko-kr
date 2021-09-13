---
title: Azure VMware Solution 호스트(미리 보기)에 디스크 풀 연결
description: iSCSI 대상을 통해 Azure VMware Solution 프라이빗 클라우드의 VMware 데이터 저장소로 표시되는 디스크 풀을 연결하는 방법을 알아봅니다. 데이터 저장소가 구성되면 데이터 저장소에서 볼륨을 만들어 VMware 인스턴스에 연결할 수 있습니다.
ms.topic: how-to
ms.date: 08/20/2021
ms.openlocfilehash: 2487e26d887935f0d66f13d51ce7894edb2b2b6e
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769303"
---
# <a name="attach-disk-pools-to-azure-vmware-solution-hosts-preview"></a>Azure VMware Solution 호스트(미리 보기)에 디스크 풀 연결

[Azure 디스크 풀](../virtual-machines/disks-pools.md)은 Azure 디스크에서 지원하는 애플리케이션과 워크로드에 영구 블록 스토리지를 제공합니다. 최적 비용과 성능을 위해 디스크를 Azure VMware Solution의 영구 스토리지로 사용할 수 있습니다. 예를 들어 스토리지를 많이 사용하는 워크로드를 호스트하는 경우 클러스터를 스케일링하는 대신 디스크 풀을 사용하여 스케일 업할 수 있습니다. 디스크를 사용하여 온-프레미스 또는 기본 VMware 환경에서 보조 사이트의 디스크 스토리지로 데이터를 복제할 수도 있습니다. Azure VMware Solution 호스트와 독립적으로 스토리지를 스케일링하기 위해 [울트라 디스크](../virtual-machines/disks-types.md#ultra-disk)와 [프리미엄 SSD](../virtual-machines/disks-types.md#premium-ssd)를 데이터 저장소로 표시할 수 있습니다.  

>[!IMPORTANT]
>Azure VMware Solution(미리 보기)의 Azure 디스크 풀은 현재 퍼블릭 미리 보기로 제공됩니다.
>이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
>자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 관리 디스크는 Azure VMware Solution 리소스 그룹 아래에 배포된 하나의 iSCSI 컨트롤러 가상 머신에 연결됩니다. 디스크는 디스크 풀에 스토리지 대상으로 배포되고, 각 스토리지 대상은 iSCSI 대상 아래에 iSCSI LUN으로 표시됩니다. Azure VMware Solution 호스트에 데이터 저장소로 연결된 iSCSI 대상으로 디스크 풀을 노출할 수 있습니다. 디스크 풀은 스토리지 대상으로 추가된 모든 기본 디스크의 단일 엔드포인트로 표시됩니다. 각 디스크 풀에는 하나의 iSCSI 컨트롤러만 포함될 수 있습니다.

다이어그램은 디스크 풀이 Azure VMware Solution 호스트에서 작동하는 방식을 보여 줍니다. 각 iSCSI 컨트롤러는 표준 Azure 프로토콜을 사용하여 관리 디스크에 액세스하고, Azure VMware Solution 호스트는 iSCSI를 통해 iSCSI 컨트롤러에 액세스할 수 있습니다.


:::image type="content" source="media/disk-pools/azure-disks-attached-to-managed-iscsi-controllers.png" alt-text="디스크 풀이 Azure VMware Solution 호스트에서 작동하는 방식을 보여 주는 다이어그램" border="false":::


## <a name="supported-regions"></a>지원되는 지역

디스크 풀은 동일한 지역의 Azure VMware Solution 프라이빗 클라우드에만 연결할 수 있습니다. 지원되는 지역 목록은 [지역별 가용성](../virtual-machines/disks-pools.md#regional-availability)을 참조하세요.  프라이빗 클라우드가 지원되지 않는 지역에 배포된 경우 지원되는 지역에서 다시 배포할 수 있습니다. Azure VMware Solution 프라이빗 클라우드와 디스크 풀을 공동 배치하면 네트워크 대기 시간을 최소화하면서 최상의 성능을 얻을 수 있습니다.


## <a name="prerequisites"></a>전제 조건

- 워크로드의 스케일링 수준 및 성능 요구 사항 확인. 자세한 내용은 [Azure 디스크 풀 계획](../virtual-machines/disks-pools-planning.md)을 참조하세요.

- [가상 네트워크를 구성](deploy-azure-vmware-solution.md#connect-to-azure-virtual-network-with-expressroute)하여 배포된 [Azure VMware Solution 프라이빗 클라우드](deploy-azure-vmware-solution.md). 자세한 내용은 [네트워크 계획 검사 목록](tutorial-network-checklist.md)과 [VMware 프라이빗 클라우드에 대한 네트워킹 구성](tutorial-configure-networking.md)을 참조하세요. 

   - 울트라 디스크를 선택한 경우 Azure VMware Solution 프라이빗 클라우드에 초고성능을 사용한 다음, [ExpressRoute FastPath를 사용하도록 설정](../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)합니다.

   - 프리미엄 SSD를 선택한 경우 Azure VMware Solution 프라이빗 클라우드에 표준(1Gbps)을 사용합니다.  iSCSI를 호스트하려면 Standard\_DS##\_v3을 사용해야 합니다.  할당량 문제가 발생하는 경우 Dsv3 시리즈에 대해 Azure VM 시리즈당 [vCPU 할당량 한도](../azure-portal/supportability/per-vm-quota-requests.md) 증가를 요청합니다.

- 각 디스크를 개별 LUN으로 사용하여 iSCSI 대상으로 배포 및 노출되는 지원 스토리지인 디스크 풀. 자세한 내용은 [Azure 디스크 풀 배포](../virtual-machines/disks-pools-deploy.md)를 참조하세요.

   >[!IMPORTANT]
   > 디스크 풀은 VMware 클러스터와 동일한 구독에 배포해야 하며, VMware 클러스터와 동일한 VNET에 연결해야 합니다.

## <a name="attach-a-disk-pool-to-your-private-cloud"></a>프라이빗 클라우드에 디스크 풀 연결
iSCSI 대상을 통해 Azure VMware Solution 프라이빗 클라우드의 VMware 데이터 저장소로 표시되는 디스크 풀에 연결합니다.

>[!IMPORTANT]
>**퍼블릭 미리 보기** 에서는 디스크 풀을 테스트 또는 비프로덕션 클러스터에만 연결합니다.

1. 구독이 `Microsoft.AVS`에 등록되었는지 확인합니다.

   ```azurecli
   az provider show -n "Microsoft.AVS" --query registrationState
   ```

   아직 등록되지 않은 경우 지금 등록합니다.

   ```azurecli
   az provider register -n "Microsoft.AVS"
   ```

2. 구독이 Microsoft.AVS의 `CloudSanExperience` AFEC에 등록되었는지 확인합니다.

   ```azurecli
   az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS"
   ```

   - 아직 등록되지 않은 경우 지금 등록합니다.

      ```azurecli
      az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
      ```

      등록을 완료하는 데 약 15분이 걸릴 수 있으며, 현재 상태를 확인할 수 있습니다.
      
      ```azurecli
      az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS" --query properties.state
      ```

      >[!TIP]
      >등록이 완료되는 데 15분 넘게 중간 상태로 고정된 경우 등록을 취소한 다음, 플래그를 다시 등록합니다.
      >
      >```azurecli
      >az feature unregister --name "CloudSanExperience" --namespace "Microsoft.AVS"
      >az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
      >```

3. `vmware `확장이 설치되었는지 확인합니다. 

   ```azurecli
   az extension show --name vmware
   ```

   - 확장이 이미 설치된 경우 버전이 **3.0.0** 인지 확인합니다. 이전 버전이 설치된 경우 확장을 업데이트합니다.

      ```azurecli
      az extension update --name vmware
      ```

   - 아직 설치되지 않은 경우 지금 설치합니다.

      ```azurecli
      az extension add --name vmware
      ```

4. `Microsoft.StoragePool`이 제공한 iSCSI 대상을 사용하여 Azure VMware Solution 프라이빗 클라우드 클러스터에서 iSCSI 데이터 저장소를 만들고 연결합니다. 이 디스크 풀은 Microsoft.StoragePool/diskPools 리소스 공급자를 통해 수행되는 위임된 서브넷을 통해 vNet에 연결됩니다.  서브넷이 위임되지 않으면 배포가 실패합니다.

   ```bash
   az vmware datastore disk-pool-volume create --name iSCSIDatastore1 --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud --target-id /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/ResourceGroup1/providers/Microsoft.StoragePool/diskPools/mpio-diskpool/iscsiTargets/mpio-iscsi-target --lun-name lun0
   ```

   >[!TIP]
   >데이터 저장소 관련 도움말을 표시할 수 있습니다.
   >
   >   ```azurecli
   >   az vmware datastore -h
   >   ```
   

5. 프라이빗 클라우드 클러스터에 있는 iSCSI 데이터 저장소의 세부 정보를 표시합니다.
   
   ```azurecli
   az vmware datastore show --name MyCloudSANDatastore1 --resource-group MyResourceGroup --cluster -Cluster-1 --private-cloud MyPrivateCloud
   ```

6. 프라이빗 클라우드 클러스터에 있는 모든 데이터 저장소를 나열합니다.

   ```azurecli
   az vmware datastore list --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud
   ```

## <a name="delete-an-iscsi-datastore-from-your-private-cloud"></a>프라이빗 클라우드에서 iSCSI 데이터 저장소 삭제

프라이빗 클라우드 데이터 저장소를 삭제할 때 디스크 풀 리소스는 삭제되지 않습니다. 이 작업에는 유지 관리 기간이 필요하지 않습니다.

1. VM의 전원을 끄고, 다음을 포함하여 iSCSI 데이터 저장소와 연결된 모든 개체를 제거합니다.

   - VM(인벤토리에서 제거)

   - 템플릿

   - 스냅샷

2. 프라이빗 클라우드 데이터 저장소를 삭제합니다.

   ```azurecli
   az vmware datastore delete --name MyCloudSANDatastore1 --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud
   ```

## <a name="next-steps"></a>다음 단계

이제 디스크 풀을 Azure VMware Solution 호스트에 연결했으므로 다음 사항에 대해 알아보는 것이 좋습니다.

- [Azure 디스크 풀 관리](../virtual-machines/disks-pools-manage.md).  디스크 풀을 배포한 후에는 다양한 관리 작업을 사용할 수 있습니다. 디스크 풀에서 디스크를 추가 또는 제거하거나, iSCSI LUN 매핑을 업데이트하거나, ACL을 추가할 수 있습니다.

- [디스크 풀 삭제](../virtual-machines/disks-pools-deprovision.md#delete-a-disk-pool). 디스크 풀을 삭제하면 관리되는 리소스 그룹의 모든 리소스도 삭제됩니다.

- [디스크에서 iSCSI 지원 사용 안 함](../virtual-machines/disks-pools-deprovision.md#disable-iscsi-support). 디스크 풀에서 iSCSI 지원을 사용하지 않도록 설정하면 더 이상 디스크 풀을 사용할 수 없습니다.

- [디스크 풀을 다른 구독으로 이동](../virtual-machines/disks-pools-move-resource.md). Azure 디스크 풀을 다른 구독으로 이동합니다. 여기에는 디스크 풀 자체, 포함된 디스크, 관리되는 리소스 그룹, 모든 리소스 이동이 포함됩니다. 

- [디스크 풀 문제 해결](../virtual-machines/disks-pools-troubleshoot.md) Azure 디스크 풀(미리 보기)과 관련된 일반적인 오류 코드를 검토합니다. 가능한 해결 방법과 디스크 풀 상태에 대한 설명도 제공됩니다.
