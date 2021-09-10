---
title: 개념 - 스토리지
description: Azure VMware Solution 프라이빗 클라우드의 스토리지 용량, 스토리지 정책, 내결함성 및 스토리지 통합에 대해 알아봅니다.
ms.topic: conceptual
ms.custom: contperf-fy21q4
ms.date: 07/28/2021
ms.openlocfilehash: ae37e0147ea03f91c2af68b8733a1702a02f81f3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528400"
---
# <a name="azure-vmware-solution-storage-concepts"></a>Azure VMware 솔루션 스토리지 개념

Azure VMware 솔루션 프라이빗 클라우드는 VMware vSAN을 사용하는 클러스터 차원의 네이티브 스토리지를 제공합니다. 클러스터의 각 호스트에 있는 로컬 스토리지는 vSAN 데이터 저장소에서 사용되며, 미사용 데이터 암호화를 사용할 수 있으며 기본적으로 사용하도록 설정됩니다. Azure Storage 리소스를 사용하여 프라이빗 클라우드의 스토리지 기능을 확장할 수 있습니다.

## <a name="vsan-clusters"></a>vSAN 클러스터

각 클러스터 호스트의 로컬 스토리지는 vSAN 데이터 저장소의 일부로 사용됩니다. 모든 디스크 그룹은 호스트당 원시 SSD 기반 용량 15.4TB가 있는 1.6TB의 NVMe 캐시 계층을 사용합니다. 클러스터의 원시 용량 계층 크기는 호스트당 용량에 호스트 수를 곱한 값입니다. 예를 들어, 4개의 호스트 클러스터는 vSAN 용량 계층에서 61.6TB 원시 용량을 제공합니다.

클러스터 호스트의 로컬 스토리지는 클러스터 차원의 vSAN 데이터 저장소에 사용됩니다. 모든 데이터 저장소는 프라이빗 클라우드 배포의 일부로 생성되며 즉시 사용할 수 있습니다. **cloudadmin** 사용자와 CloudAdmin 역할에 할당된 모든 사용자는 다음과 같은 vSAN 권한으로 데이터 저장소를 관리할 수 있습니다.

- Datastore.AllocateSpace
- Datastore.Browse
- Datastore.Config
- Datastore.DeleteFile
- Datastore.FileManagement
- Datastore.UpdateVirtualMachineMetadata

>[!IMPORTANT]
>데이터스토어 또는 클러스터의 이름은 변경할 수 없습니다. 포털(AzureCLI 또는 PowerShell)이 아닌 다른 곳에서 프로비저닝할 때 n > 1인 "Cluster-n" 이외의 클러스터 이름을 선택할 수 있습니다.

## <a name="storage-policies-and-fault-tolerance"></a>스토리지 정책 및 내결함성

기본 스토리지 정책은 RAID-1(미러링), FTT-1 및 씩 프로비저닝으로 설정됩니다.  스토리지 정책을 조정하거나 새 정책을 적용하지 않는 한 클러스터는 이 구성으로 계속 커집니다. 호스트가 3개인 클러스터에서 FTT-1은 단일 호스트의 실패를 수용합니다. Microsoft는 실패를 정기적으로 관리하고 아키텍처 관점에서 이러한 이벤트가 검색될 때 하드웨어를 바꿉니다.

:::image type="content" source="media/concepts/vsphere-vm-storage-policies.png" alt-text="vSphere Client VM 스토리지 정책을 보여주는 스크린샷.":::


|프로비저닝 형식  |Description  |
|---------|---------|
|**씩**      | 예약된 스토리지 공간 또는 미리 할당된 스토리지 공간입니다. vSAN 데이터 저장소가 가득 찬 경우에도 공간이 이미 예약되어 있으므로 시스템이 작동될 수 있게 보호합니다. 예를 들어 씩 프로비저닝을 사용하여 10GB 가상 디스크를 만드는 경우 전체 가상 디스크 스토리지 용량이 가상 디스크의 물리적 스토리지에 사전 할당되고 데이터 저장소에서 할당된 모든 공간을 사용합니다. 다른 VM(가상 머신)은 데이터 저장소의 공간을 공유할 수 없습니다.         |
|**씬**      | 처음에 필요한 공간을 사용하고 데이터 저장소에 사용되는 데이터 공간 수요까지 증가합니다. 기본 정책(thick_provision) 외에도 FTT-1 씬 프로비저닝을 사용하여 VM을 만들 수 있습니다. 중복 제거 설정의 경우 VM 템플릿에 씬 프로비저닝을 사용합니다.         |

>[!TIP]
>클러스터가 4개 이상으로 증가할지 확실하지 않은 경우 기본 정책을 사용하여 배포합니다.  클러스터가 증가하는 경우 초기 배포 후 클러스터를 확장하는 대신 배포 중에 추가 호스트를 배포하는 것이 좋습니다. VM이 클러스터에 배포되면 VM 설정에서 디스크의 스토리지 정책을 RAID-5 FTT-1 또는 RAID-6 FTT-2로 변경합니다. 
>
>:::image type="content" source="media/concepts/vsphere-vm-storage-policies-2.png" alt-text="RAID-5 FTT-1 및 RAID-6 Ftt-2 옵션을 강조 표시해서 보여 주는 스크린샷":::


## <a name="data-at-rest-encryption"></a>미사용 데이터 암호화

vSAN 데이터 저장소는 기본적으로 Azure Key Vault에 저장된 키를 사용하여 미사용 데이터 암호화를 사용합니다. 암호화 솔루션은 KMS 기반이며 키 관리를 위한 vCenter 작업을 지원합니다.  클러스터에서 호스트를 제거하면 SSD의 데이터가 즉시 무효화됩니다.

## <a name="azure-storage-integration"></a>Azure Storage 통합

프라이빗 클라우드에서 실행되는 워크로드에서 Azure Storage 서비스를 사용할 수 있습니다. Azure Storage 서비스에는 스토리지 계정, Table Storage 및 Blob Storage가 포함됩니다. Azure Storage 서비스에 대한 워크로드 연결은 인터넷을 트래버스하지 않습니다. 이 연결을 통해 보안을 강화하고 프라이빗 클라우드 워크로드에서 SLA 기반 Azure Storage 서비스를 사용할 수 있습니다.

## <a name="alerts-and-monitoring"></a>경고 및 모니터링

Microsoft는 용량 사용량이 75%를 초과하는 경우 경고를 제공합니다.  Azure Monitor 통합된 용량 소비량 메트릭을 모니터링할 수도 있습니다. 자세한 내용은 [Azure VMware Solution에서 Azure 경고 구성](configure-alerts-for-azure-vmware-solution.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 Azure VMware Solution 스토리지 개념을 살펴보았으므로 다음에 대해 알아볼 수 있습니다.

- [Azure VMware 솔루션 호스트에 디스크 풀 연결(미리 보기)](attach-disk-pools-to-azure-vmware-solution-hosts.md) - 디스크를 Azure VMware Solution에 대한 영구적 스토리지로 사용하여 최적의 비용 및 성능을 얻을 수 있습니다.
- [프라이빗 클라우드에서 클러스터 크기 조정][tutorial-scale-private-cloud] - 애플리케이션 워크로드에 필요한 만큼 프라이빗 클라우드의 클러스터 및 호스트를 확장할 수 있습니다. 특정 서비스의 성능 및 가용성 제한은 사례별로 다루어야 합니다.
- [Azure VMware Solution을 사용하는 Azure NetApp Files](netapp-files-with-azure-vmware-solution.md) - Azure NetApp을 사용하여 클라우드에서 가장 까다로운 엔터프라이즈 파일 워크로드(데이터베이스, SAP 및 고성능 컴퓨팅 애플리케이션)을 코드 변경 없이 마이그레이션하고 실행할 수 있습니다. 
- [Azure VMware Solution에 대한 vSphere 역할 기반 액세스 제어](concepts-identity.md) - vCenter를 사용하여 VM 워크로드를 관리하고 NSX-T Manager를 사용하여 프라이빗 클라우드를 관리 및 확장합니다. 액세스 및 ID 관리는 vCenter에서 CloudAdmin 역할을, NSX-T Manager에서 제한된 관리자 권한을 사용합니다.


<!-- LINKS - external-->

<!-- LINKS - internal -->
[tutorial-scale-private-cloud]: ./tutorial-scale-private-cloud.md
[concepts-identity]: ./concepts-identity.md
