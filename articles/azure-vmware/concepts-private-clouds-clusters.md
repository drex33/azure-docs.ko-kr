---
title: 개념 - 프라이빗 클라우드와 클러스터
description: Azure VMware Solution 소프트웨어 정의 데이터 센터 및 vSphere 클러스터의 주요 기능에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 08/25/2021
ms.openlocfilehash: 6e068462cada0a324d646766574e05414ad92772
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122968512"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware Solution 프라이빗 클라우드 및 클러스터 개념

Azure VMware Solution은 Azure에서 VMware 기반 프라이빗 클라우드를 제공합니다. Azure에서 프라이빗 클라우드 하드웨어 및 소프트웨어 배포를 완벽하게 통합하고 자동화할 수 있습니다. Azure Portal, CLI 또는 PowerShell을 통해 프라이빗 클라우드를 배포하고 관리합니다.  

프라이빗 클라우드에는 다음을 사용하는 클러스터가 포함되어 있습니다.

- VMware ESXi 하이퍼바이저로 프로비저닝된 전용 베어메탈 서버 호스트 
- ESXi 및 vSAN을 관리하기 위한 vCenter Server 
- vSphere 워크로드 VM을 위한 VMware NSX-T 소프트웨어 방식 네트워킹  
- vSphere 워크로드 VM용 VMware vSAN 데이터 저장소  
- 워크로드 이동성을 위한 VMware HCX  
- Azure 언더레이의 리소스(연결하고 프라이빗 클라우드를 운영하는 데 필요함)

다른 리소스와 마찬가지로 프라이빗 클라우드는 Azure 구독 내에서 설치되고 관리됩니다. 구독 내의 프라이빗 클라우드 수는 스케일링 가능합니다. 처음에는 구독 당 하나의 프라이빗 클라우드로 제한됩니다.  Azure 구독, Azure VMware Solution 프라이빗 클라우드, vSAN 클러스터 및 호스트 간에는 논리적 관계가 있습니다. 

이 다이어그램은 개발 및 프로덕션 환경을 나타내는 두 프라이빗 클라우드를 포함하는 단일 Azure 구독에 대해 보여 줍니다. 각 프라이빗 클라우드에서는 두 개의 클러스터가 있습니다. 

:::image type="content" source="media/concepts/hosts-clusters-private-clouds-final.png" alt-text="개발 및 프로덕션 환경을 나타내는 두 프라이빗 클라우드를 포함하는 단일 Azure 구독을 보여주는 다이어그램." border="false":::

## <a name="hosts"></a>호스트

[!INCLUDE [disk-capabilities-of-the-host](includes/disk-capabilities-of-the-host.md)]

## <a name="clusters"></a>클러스터

[!INCLUDE [hosts-minimum-initial-deployment-statement](includes/hosts-minimum-initial-deployment-statement.md)]

[!INCLUDE [azure-vmware-solutions-limits](includes/azure-vmware-solutions-limits.md)]

## <a name="vmware-software-versions"></a>VMware 소프트웨어 버전

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]

## <a name="host-maintenance-and-lifecycle-management"></a>호스트 유지 관리 및 수명 주기 관리



[!INCLUDE [vmware-software-update-frequency](includes/vmware-software-update-frequency.md)]

## <a name="host-monitoring-and-remediation"></a>호스트 모니터링 및 수정

Azure VMware 솔루션은 언더레이 및 VMware 구성 요소의 상태를 지속적으로 모니터링합니다. Azure VMware 솔루션이 실패를 탐지하면 실패한 구성 요소를 복구하는 작업을 수행합니다. Azure VMware Solution이 Azure VMware Solution 노드에서 성능 저하 또는 장애를 탐지하면 호스트 수정 프로세스를 트리거합니다. 

호스트 수정에는 장애가 발생한 노드를 클러스터에서 새로운 정상 노드로 대체하는 작업이 포함됩니다. 그런 다음 가능한 경우 장애가 발생한 호스트를 VMware vSphere 유지 관리 모드로 전환합니다. VMware vMotion은 VM을 장애가 발생한 호스트에서 클러스터의 다른 사용 가능한 서버로 이동하여 워크로드의 실시간 마이그레이션으로 인한 가동 중지 시간을 최소화할 수 있습니다. 장애가 발생한 호스트를 유지 관리 모드로 전환할 수 없는 경우 호스트는 클러스터에서 제거됩니다.

Azure VMware Solution은 호스트에서 다음 상태를 모니터링합니다.  

- 프로세서 상태 
- 메모리 상태 
- 연결 및 전원 상태 
- 하드웨어 팬 상태 
- 네트워크 연결 손실 
- 하드웨어 시스템 보드 상태 
- vSAN 호스트의 디스크에서 발생한 오류 
- 하드웨어 전압 
- 하드웨어 온도 상태 
- 하드웨어 전원 상태 
- 스토리지 상태 
- 연결 실패 

> [!NOTE]
> Azure VMware Solution 테넌트 관리자는 위의 정의된 VMware vCenter 경보를 편집하거나 삭제해서는 안 됩니다. 이들은 vCenter의 Azure VMware Solution 컨트롤 플레인에서 관리되기 때문입니다. 이러한 경보는 Azure VMware Solution 모니터링에서 Azure VMware Solution 호스트 수정 프로세스를 트리거하는 데 사용됩니다.

## <a name="backup-and-restoration"></a>백업 및 복원

프라이빗 클라우드 vCenter 및 NSX-T 구성은 Microsoft가 시간별 일정에 따라 백업합니다.  백업은 3일 동안 보관됩니다. 백업에서 복원해야 하는 경우 Azure Portal에서 [지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support)을 열어 복원을 요청합니다.

Azure VMware 솔루션은 언더레이 및 VMware 구성 요소의 상태를 지속적으로 모니터링합니다. Azure VMware 솔루션이 실패를 탐지하면 실패한 구성 요소를 복구하는 작업을 수행합니다.

## <a name="next-steps"></a>다음 단계

이제 Azure VMware Solution 프라이빗 클라우드의 개념을 살펴 보았습니다. 다음에 대해 알아볼 수 있습니다. 

- [Azure VMware Solution 네트워킹 및 상호 연결 개념](concepts-networking.md)
- [Azure VMware Solution 스토리지 개념](concepts-storage.md)
- [Azure VMware Solution 리소스를 사용하는 방법](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider)

<!-- LINKS - internal -->
[concepts-networking]: ./concepts-networking.md

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

