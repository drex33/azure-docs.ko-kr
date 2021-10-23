---
title: Azure VMware Solution과 Azure NetApp Files 통합
description: Azure NetApp Files를 Azure VMware Solution VM과 함께 사용하여 온-프레미스 서버, Azure VMware Solution VM, 클라우드 인프라 간에 데이터를 마이그레이션하고 동기화합니다.
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: 7335434c30b88e54fbfd15b18f3604954cbdef0f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226776"
---
# <a name="integrate-azure-netapp-files-with-azure-vmware-solution"></a>Azure VMware Solution과 Azure NetApp Files 통합

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md)는 클라우드에서 가장 까다로운 엔터프라이즈 파일 워크로드(데이터베이스, SAP 및 고성능 컴퓨팅 애플리케이션)을 코드 변경 없이 마이그레이션 및 실행하는 Azure 서비스입니다. 이 문서에서는 NFS(네트워크 파일 시스템) 프로토콜을 사용하여 Azure VMware Solution 워크로드에 대한 파일 공유로 Azure NetApp Files 볼륨을 설정, 테스트 및 확인합니다. 게스트 운영 체제는 Azure NetApp Files 볼륨에 액세스하는 VM(가상 머신) 내에서 실행됩니다. 

Azure NetApp Files와 Azure VMware Solution은 동일한 Azure 지역에 생성됩니다. Azure NetApp Files는 많은 [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all)에서 사용할 수 있으며 지역 간 복제를 지원합니다. Azure NetApp Files 구성 방법에 대한 자세한 내용은 [Azure NetApp Files의 스토리지 계층 구조](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md)를 참조하세요.

Azure NetApp Files가 사용되는 서비스입니다.

- **Active Directory 연결**: Azure NetApp Files는 [Active Directory Domain Services 및 Azure Active Directory Domain Services](../azure-netapp-files/create-active-directory-connections.md#decide-which-domain-services-to-use)를 지원합니다.

- **공유 프로토콜**: Azure NetApp Files는 SMB(서버 메시지 블록) 및 NFS(네트워크 파일 시스템) 프로토콜을 지원합니다. 이 지원은 볼륨을 Linux 클라이언트에 탑재할 수 있고 Windows 클라이언트에 매핑할 수 있음을 의미합니다.

- **Azure VMware Solution**: Azure NetApp Files 공유를 Azure VMware Solution 환경에서 만든 VM에서 탑재할 수 있습니다.


다이어그램은 Azure ExpressRoute를 통해 Azure VMware Solution 프라이빗 클라우드로 연결하는 방법을 보여줍니다. Azure VMware Solution 환경은 Azure VMware Solution VM에 탑재된 Azure NetApp Files 공유에 액세스합니다.

:::image type="content" source="media/netapp-files/netapp-files-topology.png" alt-text="Azure VMware Solution용 NetApp Files 아키텍처를 보여 주는 다이어그램" border="false":::


## <a name="prerequisites"></a>사전 요구 사항 

> [!div class="checklist"]
> * Azure NetApp Files를 사용하는 Azure 구독
> * Azure NetApp Files용 서브넷
> * Azure VMware Solution의 Linux VM
> * Azure VMware Solution의 Windows VM


## <a name="create-and-mount-azure-netapp-files-volumes"></a>Azure NetApp Files 볼륨 만들기 및 탑재

Azure NetApp Files 볼륨을 만들고 Azure VMware Solution VM에 탑재합니다.

1. [NetApp 계정을 만듭니다](../azure-netapp-files/azure-netapp-files-create-netapp-account.md).

1. [용량 풀을 설정](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)합니다.

1. [Azure NetApp Files에 대한 SMB 볼륨을 만듭니다](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md).

1. [Azure NetApp Files에 대한 NFS 볼륨을 만듭니다](../azure-netapp-files/azure-netapp-files-create-volumes.md).

1. [Azure NetApp Files에 서브넷을 위임](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)합니다.


## <a name="verify-pre-configured-azure-netapp-files"></a>미리 구성된 Azure NetApp Files 확인 

Azure NetApp Files 프리미엄 서비스 수준에서 Azure에 만들어진 미리 구성된 Azure NetApp Files를 확인합니다.

1. Azure Portal의 **스토리지** 에서 **Azure NetApp Files** 를 선택합니다. 구성된 Azure NetApp Files의 목록이 표시됩니다. 

   :::image type="content" source="media/netapp-files/azure-netapp-files-list.png" alt-text="미리 구성된 Azure NetApp Files의 목록을 보여 주는 스크린샷"::: 

2. 구성된 NetApp Files 계정을 선택하여 설정을 확인합니다. 예를 들어 **Contoso-anf2** 를 선택합니다. 

3. **용량 풀** 을 선택하여 구성된 풀을 확인합니다. 

   :::image type="content" source="media/netapp-files/netapp-settings.png" alt-text="구성된 NetApp Files 계정의 용량 풀 및 볼륨을 볼 수 있는 옵션을 보여 주는 스크린샷":::

   용량 및 서비스 수준을 보여 주는 용량 풀 페이지가 열립니다. 이 예제에서 스토리지 풀은 프리미엄 서비스 수준을 사용하여 4TiB로 구성됩니다.

4. **볼륨** 을 선택하여 용량 풀에 만들어진 볼륨을 확인합니다. (위의 스크린샷을 참조하세요.)

5. 볼륨을 선택하여 해당 구성을 확인합니다.  

   :::image type="content" source="media/netapp-files/azure-netapp-volumes.png" alt-text="용량 풀에 만들어진 볼륨을 보여 주는 스크린샷":::

   볼륨의 구성 세부 정보를 표시하는 창이 열립니다.

   :::image type="content" source="media/netapp-files/configuration-of-volume.png" alt-text="볼륨의 구성 세부 정보를 보여 주는 스크린샷":::

   200GiB 크기의 anfvolume이 용량 풀 anfpool1에 포함되어 있는 것을 볼 수 있습니다. 이 볼륨은 10.22.3.4:/ANFVOLUME을 통해 NFS 파일 공유로 내보내집니다. VM에 탑재할 Azure NetApp Files 및 NFS 경로를 위해 Azure VNet(Virtual Network)에서 개인 IP 하나가 만들어졌습니다.

   크기 또는 '할당량'에 따른 Azure NetApp Files 볼륨 성능에 대한 자세한 내용은 [Azure NetApp Files의 성능 고려 사항](../azure-netapp-files/azure-netapp-files-performance-considerations.md)을 참조하세요. 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>미리 구성된 Azure VMware Solution VM 공유 매핑 확인

Azure NetApp Files 공유가 Azure VMware Solution VM에 액세스할 수 있도록 하려면 SMB 및 NFS 공유 매핑을 이해해야 합니다. SMB 또는 NFS 볼륨을 구성한 후에만 여기에 설명된 대로 탑재할 수 있습니다.

- **SMB 공유:** SMB 볼륨을 배포하기 전에 Active Directory 연결을 만듭니다. 연결이 성공하려면 Azure NetApp Files의 위임된 서브넷이 지정된 도메인 컨트롤러에 액세스할 수 있어야 합니다. Active Directory가 Azure NetApp Files 계정 내에서 구성되면 SMB 볼륨을 만드는 동안 선택 가능한 항목으로 표시됩니다.

- **NFS 공유:** Azure NetApp Files는 NFS 또는 이중 프로토콜(NFS 및 SMB)을 사용하는 볼륨을 만드는 데 기여합니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다. NFS는 명령줄 또는/etc/fstab 항목을 사용하여 Linux 서버에 탑재할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure NetApp Files를 Azure VMware Solution 워크로드와 통합하는 방법을 설명했으므로 다음에 대해 알아볼 수 있습니다.

- [Azure NetApp Files에 대한 리소스 제한](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)
- [Azure NetApp Files 네트워크 계획 지침](../azure-netapp-files/azure-netapp-files-network-topologies.md)
- [Azure NetApp Files 볼륨의 지역 간 복제](../azure-netapp-files/cross-region-replication-introduction.md) 
- [NFS FAQ Azure NetApp Files](../azure-netapp-files/faq-nfs.md)
- [Azure NetApp Files SMB FAQ](../azure-netapp-files/faq-smb.md)
