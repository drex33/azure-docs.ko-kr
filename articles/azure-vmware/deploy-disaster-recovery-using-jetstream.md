---
title: JetStream DR (미리 보기)을 사용 하 여 재해 복구 배포
description: Azure VMware 솔루션 사설 클라우드 및 온-프레미스 VMware 워크 로드에 대해 JetStream DR을 구현 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: references_regions
ms.openlocfilehash: f43d2a8db4ef29588ad5260c583faab31bd01228
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129279073"
---
# <a name="deploy-disaster-recovery-using-jetstream-dr-preview"></a>JetStream DR (미리 보기)을 사용 하 여 재해 복구 배포

>[!IMPORTANT]
>Azure VMware 솔루션 (미리 보기)의 JetStream DR은 현재 공개 미리 보기로 제공 됩니다.
>이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
>자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[JETSTREAM DR](https://www.jetstreamsoft.com/product-portfolio/jetstream-dr/) 은 재해가 발생 한 경우 vm (가상 머신)의 가동 중지 시간을 최소화 하도록 설계 된 클라우드 기본 재해 복구 솔루션입니다. JetStream DR의 인스턴스는 보호 된 사이트와 복구 사이트 모두에 배포 됩니다. 

JetStream는 [VMWARE VAIO framework](https://core.vmware.com/resource/vmware-vsphere-apis-io-filtering-vaio)를 사용 하 여 CDP (연속 데이터 보호)를 기반으로 하며,이를 통해 데이터 손실 없이 최소 또는 근접 하 게 사용할 수 있습니다. JetStream DR은 비즈니스 및 중요 업무용 응용 프로그램에 필요한 보호 수준을 제공 합니다. 또한 DR 사이트의 최소 리소스를 사용 하 고 비용 효율적인 클라우드 저장소 (예: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))를 사용 하 여 비용 효율적인 dr을 사용 하도록 설정 합니다.

이 문서에서는 Azure VMware 솔루션 사설 클라우드 및 온-프레미스 VMware 워크 로드에 대해 JetStream DR을 구현 합니다.

JetStream DR에 대 한 자세한 내용은 다음을 참조 하세요.

- [JetStream 솔루션 brief](https://www.jetstreamsoft.com/2020/09/28/solution-brief-disaster-recovery-for-avs/)

- [JetStream DR on Azure Marketplace](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020)

- [JetStream 기술 자료 문서](https://www.jetstreamsoft.com/resources/knowledge-base/)


## <a name="core-components-of-the-jetstream-dr-solution"></a>JetStream DR 솔루션의 핵심 구성 요소

| 항목 | 설명 |
| --- | --- |
| **JetStream 관리 서버 MSA (가상 어플라이언스)**  | MSA를 사용 하면 기본 사이트, 보호 도메인, Vm 복구 등의 0 일 및 2 일 구성을 모두 사용할 수 있습니다.  MSA는 클라우드 관리자가 vSphere 노드에 설치 합니다.  MSA는 vcenter에서 기본적으로 JetStream DR을 관리 하는 데 사용할 수 있는 vCenter 플러그 인을 구현 합니다. MSA는 보호 된 Vm의 복제 데이터를 처리 하지 않습니다.  | 
| **JetStream DR 가상 어플라이언스 (DRVA)**  | Linux 기반 가상 컴퓨터 어플라이언스는 원본 ESXi 호스트에서 보호 된 Vm 복제 데이터를 수신 합니다. DR 사이트에서 일반적으로 Azure Blob Storage와 같은 개체 저장소에 복제 데이터를 저장 하는 일을 담당 합니다. 보호 된 Vm의 수와 복제할 저장소의 양에 따라 개인 cloudadmin에서 하나 이상의 DRVA 인스턴스를 만들 수 있습니다.  | 
| **JetStream ESXi host 구성 요소 (IO 필터 패키지)**  | JetStream DR에 대해 구성 된 각 ESXi 호스트에 JetStream 소프트웨어를 설치 했습니다. 호스트 드라이버는 vSphere Vm IO를 가로채 고 복제 데이터를 DRVA로 보냅니다.   | 
| **JetStream protection 도메인**  | 동일한 정책 및 실행 설명서를 사용 하 여 함께 보호 될 Vm의 논리 그룹입니다. 보호 도메인의 모든 Vm에 대 한 데이터는 동일한 Azure Blob 컨테이너 인스턴스에 저장 됩니다. 동일한 DRVA 인스턴스는 보호 도메인의 모든 Vm에 대 한 원격 DR 저장소에 대 한 복제를 처리 합니다.   | 
| **Azure Blob Storage 컨테이너**  | 보호 된 Vm 복제 된 데이터는 Azure Blob에 저장 됩니다. JetStream software는 각 JetStream protection 도메인에 대해 하나의 Azure Blob 컨테이너 인스턴스를 만듭니다.    | 



## <a name="jetstream-scenarios-on-azure-vmware-solution"></a>Azure VMware 솔루션의 JetStream 시나리오
다음 두 가지 시나리오에 대해 Azure VMware 솔루션과 함께 JetStream DR을 사용할 수 있습니다.  

- 온-프레미스 VMware에서 Azure VMware 솔루션 DR

- Azure vmware 솔루션에 대 한 azure VMware 솔루션 DR

### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>시나리오 1: 온-프레미스 VMware에서 Azure VMware 솔루션 DR으로

이 시나리오에서 기본 사이트는 온-프레미스 VMware 환경 이며 DR 사이트는 Azure VMware 솔루션 사설 클라우드입니다.

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png" alt-text="Azure VMware Solution 프라이빗 클라우드 JetStream 배포에 대한 온-프레미스 배포를 보여 주는 다이어그램." border="false" lightbox="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png":::

### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-dr"></a>시나리오 2: azure vmware 솔루션 DR에 대 한 Azure VMware 솔루션

이 시나리오에서 기본 사이트는 한 Azure 지역에 있는 Azure VMware 솔루션 사설 클라우드입니다. 재해 복구 사이트는 다른 Azure 지역에 있는 Azure VMware 솔루션 사설 클라우드입니다. 

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png" alt-text="Azure VMware Solution 프라이빗 클라우드에서 프라이빗 클라우드 JetStream으로의 배포를 보여 주는 다이어그램" border="false" lightbox="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png":::


## <a name="prerequisites"></a>필수 조건


### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>시나리오 1: 온-프레미스 VMware에서 Azure VMware 솔루션 DR으로 

- 대상 DR 지역에 최소 세 개의 노드를 사용 하 여 Azure VMware 솔루션 사설 클라우드를 배포 했습니다.  


   :::image type="content" source="media/jetstream-disaster-recovery/disaster-recovery-scenario-prerequisite.png" alt-text="Azure VMware 솔루션에 대 한 재해 복구 솔루션의 첫 번째 필수 구성 요소를 보여 주는 다이어그램" lightbox="media/jetstream-disaster-recovery/disaster-recovery-scenario-prerequisite.png":::


- 기본 사이트 JetStream 어플라이언스와 Azure Storage blob 인스턴스 간에 구성 된 네트워크 연결입니다. 

- Azure Marketplace에서 [JETSTREAM dr을 설정 하 고 구독](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020) 하 여 JetStream dr 소프트웨어를 다운로드 합니다.

- 표준 또는 Premium 성능 계층 중 하나를 사용 하 여 만든 [Azure Blob Storage 계정](../storage/common/storage-account-create.md) . [액세스 계층에서 **핫** 을 선택](../storage/blobs/access-tiers-overview.md)합니다. 

   >[!NOTE]
   >Blob에서 **계층 구조 네임 스페이스 사용** 옵션이 지원 되지 않습니다.   

- Azure VMware 솔루션 사설 클라우드에 구성 된 NSX-T 네트워크 세그먼트 이며, 필요에 따라 JetStream 가상 어플라이언스에 대 한 세그먼트에서 DHCP를 사용 하도록 설정 합니다.  

- azure vmware solution vCenter의 IP 주소, azure vmware solution ESXi 호스트, Azure Storage 계정 및 JetStream 가상 어플라이언스에 대 한 JetStream Marketplace 서비스를 확인 하도록 구성 된 DNS 서버입니다. 



### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-dr"></a>시나리오 2: azure vmware 솔루션 DR에 대 한 Azure VMware 솔루션

- 주 지역과 보조 지역 모두에서 최소 3 개의 노드를 사용 하 여 Azure VMware 솔루션 사설 클라우드를 배포 했습니다.  
 
- 기본 사이트 JetStream 어플라이언스와 Azure Storage blob 인스턴스 간에 구성 된 네트워크 연결입니다. 

- Azure Marketplace에서 [JETSTREAM dr을 설정 하 고 구독](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020) 하 여 JetStream dr 소프트웨어를 다운로드 합니다.

- 표준 또는 Premium 성능 계층 중 하나를 사용 하 여 만든 [Azure Blob Storage 계정](../storage/common/storage-account-create.md) . [액세스 계층에서 **핫** 을 선택](../storage/blobs/access-tiers-overview.md)합니다. 

   >[!NOTE]
   >Blob에서 **계층 구조 네임 스페이스 사용** 옵션이 지원 되지 않습니다.   

- Azure VMware 솔루션 사설 클라우드에 구성 된 NSX-T 네트워크 세그먼트 이며, 필요에 따라 JetStream 가상 어플라이언스에 대 한 세그먼트에서 DHCP를 사용 하도록 설정 합니다.  

- azure vmware solution vCenter, azure vmware solution ESXi 호스트, Azure Storage 계정 및 JetStream 가상 어플라이언스에 대 한 JetStream Marketplace 서비스의 IP 주소를 확인 하기 위해 기본 및 DR 사이트 모두에 구성 된 DNS 서버입니다. 


온-프레미스 JetStream DR 필수 구성 요소에 대 한 자세한 내용은 [JetStream 사전 설치 가이드](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/pre-installation-guidelines/)를 참조 하세요.





## <a name="install-jetstream-dr-on-azure-vmware-solution"></a>Azure VMware 솔루션에 JetStream DR 설치  
 
지원 되는 두 시나리오 모두에 대해 다음 단계를 수행할 수 있습니다. 
 
1. 온-프레미스 데이터 센터에서 [JetStream 설명서](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/installing-jetstream-dr-software/)에 따라 JetStream DR을 설치 합니다.  

1. Azure VMware 솔루션 사설 클라우드에서 Run 명령을 사용 하 여 JetStream DR을 설치 합니다. [Azure Portal](https://portal.azure.com)에서 **명령**  >  **패키지** 실행  >  **jsdr을 선택 합니다. 구성**.  

   :::image type="content" source="media/run-command/run-command-overview-jetstream.png" alt-text="사용 가능한 JetStream 실행 명령에 액세스하는 방법을 보여 주는 스크린샷" lightbox="media/run-command/run-command-overview-jetstream.png":::
 
   >[!NOTE]
   >Azure VMware 솔루션의 기본 CloudAdmin 사용자에 게는 JetStream DR을 설치할 수 있는 충분 한 권한이 없습니다.  Azure VMware 솔루션을 사용 하면 JetStream DR에 대 한 Azure VMware Solution Run 명령을 호출 하 여 JetStream DR을 간편 하 게 자동으로 설치할 수 있습니다.  
 

1. JetStream DR을 설치 하기 위한 필수 구성 요소가 충족 되었는지 확인 하는 **PreflightJetDRInstall** cmdlet을 실행 합니다. 예를 들어 필요한 호스트 수, 클러스터 이름 및 고유한 VM 이름에 대 한 유효성을 검사 합니다. 

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **Network**  | JetStream MSA를 배포 해야 하는 NSX 네트워크 세그먼트의 이름입니다.  |
   | **Datastore**  | MSA를 배포할 데이터 저장소의 이름입니다.  |
   | **ProtectedCluster** | 보호할 Azure VMware 솔루션 사설 클라우드 클러스터의 이름 (예: **cluster-1**)입니다.  클러스터 이름은 하나만 제공할 수 있습니다. |
   | **Cluster** | JetStream MSA가 배포 되는 Azure VMware 솔루션 개인 클러스터의 이름 (예: **cluster-1**)입니다. |
   | **VMName** | JetStream MSA VM의 이름입니다 (예: **jetstreamServer**). |
   | **실행할 이름 지정**  | 실행의 영숫자 이름입니다 (예: **PreflightJetDRInstall-Exec1)**. Cmdlet이 성공적으로 실행 되었는지 확인 하는 데 사용 됩니다. |
   | **Timeout**  | 완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. [실행 상태를 확인](concepts-run-command.md#view-the-status-of-an-execution)합니다.
   

## <a name="install-the-jetstream-dr-msa"></a>JetStream DR MSA 설치

Azure VMware Solution 고정 IP 주소 또는 DHCP 기반 IP 주소를 사용하여 JetStream을 설치할 수 있습니다.  

### <a name="static-ip-address"></a>고정 IP 주소

1. **실행 명령**  >  **패키지**  >  **Install-JetDRWithStaticIP를** 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.


   | **필드** | **값** |
   | --- | --- |
   | **ProtectedCluster** | 보호할 Azure VMware Solution 프라이빗 클라우드 클러스터의 이름입니다(예: **Cluster-1).**  설치하는 동안에는 하나의 클러스터 이름만 제공할 수 있습니다. |
   | **Datastore**  | JetStream MSA를 배포할 데이터 저장소의 이름입니다.  |
   | **VMName** | JetStream MSA VM의 이름입니다(예: **jetstreamServer).** |
   | **Cluster** | JetStream MSA가 배포된 Azure VMware Solution 프라이빗 클러스터의 이름입니다(예: **Cluster-1).** |
   | **네트워크 마스크** | 배포할 MSA의 Netmask(예: **22** 또는 **24).** |
   | **MSIp** | JetStream MSA VM의 IP 주소입니다.   |
   | **Dns** | JetStream MSA VM에서 사용해야 하는 DNS IP입니다.   |
   | **게이트웨이** | JetStream MSA VM에 대한 네트워크 게이트웨이의 IP 주소입니다.  |
   | **자격 증명**  |  JetStream MSA VM의 루트 사용자 자격 증명입니다.   |
   | **HostName** | JetStream MSA VM의 호스트 이름(FQDN)입니다.  |
   | **Network**  | JetStream MSA를 배포해야 하는 NSX-T 네트워크 세그먼트의 이름입니다. |
   | **실행할 이름 지정**  | 실행의 영숫자 이름(예: **Install-JetDRWithStaticIP-Exec1)입니다.**   cmdlet이 성공적으로 실행되었는지 확인하는 데 사용됩니다.  |


1. [실행 상태를 확인합니다.](concepts-run-command.md#view-the-status-of-an-execution)


### <a name="dhcp-based-ip-address"></a>DHCP 기반 IP 주소

이 단계에서는 DR 보호가 필요한 클러스터에 JetStream vSphere 설치 번들(VIB)도 설치합니다. 

1. **실행 명령**  >  **패키지**  >  **Install-JetDRWithDHCP를** 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **ProtectedCluster** | 보호할 Azure VMware Solution 프라이빗 클라우드 클러스터의 이름입니다(예: **Cluster-1).**  설치하는 동안에는 하나의 클러스터 이름만 제공할 수 있습니다. |
   | **Datastore**  | JetStream MSA를 배포할 데이터 저장소의 이름입니다.  |
   | **VMName** | JetStream MSA VM의 이름입니다(예: **jetstreamServer).** |
   | **Cluster** | JetStream MSA가 배포된 Azure VMware Solution 프라이빗 클러스터의 이름입니다(예: **Cluster-1).** |
   | **자격 증명**  |  JetStream MSA VM의 루트 사용자 자격 증명입니다.   |
   | **HostName** | JetStream MSA VM의 호스트 이름(FQDN)입니다.  |
   | **Network**  | JetStream MSA를 배포해야 하는 NSX-T 네트워크 세그먼트의 이름입니다. |
   | **실행할 이름 지정**  | 실행의 영숫자 이름(예: **Install-JetDRWithDHCP-Exec1)입니다.**   cmdlet이 성공적으로 실행되었는지 확인하는 데 사용됩니다.  |
 
 
1. [실행 상태를 확인합니다.](concepts-run-command.md#view-the-status-of-an-execution)
 
 
## <a name="add-jetstream-dr-to-new-azure-vmware-solution-clusters"></a>새 Azure VMware Solution 클러스터에 JetStream DR 추가  


1. **실행 명령**  >  **패키지**  >  **Enable-JetDRForCluster를** 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **ProtectedCluster** | 보호할 Azure VMware Solution 프라이빗 클라우드 클러스터의 이름입니다(예: **Cluster-1).**  설치하는 동안에는 하나의 클러스터 이름만 제공할 수 있습니다. |
   | **자격 증명**  |  JetStream MSA VM의 루트 사용자 자격 증명입니다.   |
   | **MSIp** | JetStream MSA VM의 IP 주소입니다.   |
   | **실행할 이름 지정**  | 실행의 영숫자 이름(예: **Enable-JetDRForCluster-Exec1)입니다.**   cmdlet이 성공적으로 실행되었는지 확인하는 데 사용됩니다.  |
  
1. [실행 상태를 확인합니다.](concepts-run-command.md#view-the-status-of-an-execution)
 
 
 

## <a name="configure-jetstream-dr"></a>JetStream DR 구성 
 
이 섹션에서는 JetStream DR을 구성하는 데 필요한 단계에 대한 개요만 다룹니다.  자세한 설명 및 단계는 [JetStream DR 구성](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/configuring-jetstream-dr/) 설명서를 참조하세요. 
 
JetStream DR MSA 및 JetStream VIB가 Azure VMware Solution 클러스터에 설치되면 JetStream 포털을 사용하여 나머지 구성 단계를 완료합니다. 



1. vCenter 어플라이언스에서 JetStream 포털에 액세스합니다.

1. [외부 스토리지 사이트 를 추가합니다.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/add-a-storage-site/)  

1. [JetStream DRVA 어플라이언스 를 배포합니다.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/deploy-a-dr-virtual-appliance/) 

1. Azure VMware Solution 클러스터에서 사용할 수 있는 데이터 저장소 중 하나를 사용하여 [JetStream 복제 로그 저장소 볼륨을 만듭니다.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/create-a-replication-log-store-volume/) 

   >[!TIP]
   >vSAN 데이터 저장소와 같은 빠른 로컬 스토리지는 복제 로그에 선호됩니다. 
 
1. [JetStream 보호된 도메인 을 만듭니다.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/create-a-protected-domain/) Azure Blob Storage 사이트, JetStream DRVA 인스턴스 및 이전 단계에서 만든 복제 로그를 제공합니다. 

1. 보호하려는 [VM을 선택한](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/select-vms-for-protection/) [다음, VM 보호를 시작합니다.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/start-vm-protection/)

 
장애 조치(failover) Runbook 만들기, DR 사이트로 장애 조치(failover) 호출 및 기본 사이트로 장애 복구 호출과 같은 JetStream DR에 대한 나머지 구성 단계는 [JetStream 관리 가이드 설명서를 참조하세요.](https://www.jetstreamsoft.com/portal/jetstream-article-categories/product-manual/)  
 
## <a name="disable-jetstream-dr-on-an-azure-vmware-solution-cluster"></a>Azure VMware Solution 클러스터에서 JetStream DR 사용 안 함  
 
이 cmdlet은 클러스터 중 하나에서만 JetStream DR을 사용하지 않도록 설정하고 JetStream DR을 완전히 제거하지 않습니다. 

1. **실행 명령**  >  **패키지**  >  **Disable-JetDRForCluster를** 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **ProtectedCluster** | 보호할 Azure VMware Solution 프라이빗 클라우드 클러스터의 이름입니다(예: **Cluster-1).**  설치하는 동안에는 하나의 클러스터 이름만 제공할 수 있습니다. |
   | **자격 증명**  |  JetStream MSA VM의 루트 사용자 자격 증명입니다.   |
   | **MSIp** | JetStream MSA VM의 IP 주소입니다.   |
   | **실행할 이름 지정**  | 실행의 영숫자 이름(예: **Disable-JetDRForCluster-Exec1)입니다.**   cmdlet이 성공적으로 실행되었는지 확인하는 데 사용됩니다.  |

1. [실행 상태를 확인합니다.](concepts-run-command.md#view-the-status-of-an-execution)


 
## <a name="uninstall-jetstream-dr"></a>JetStream DR 제거  

1. **실행 명령** > **패키지** > **Invoke-PreflightJetDRUninstall** 을 선택합니다. 이 cmdlet은 클러스터에 호스트가 4개 이상 있는지 확인합니다(최소 필요). 

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **ProtectedCluster** | 보호할 Azure VMware Solution 프라이빗 클라우드 클러스터의 이름입니다(예: **Cluster-1).**  설치하는 동안에는 하나의 클러스터 이름만 제공할 수 있습니다. |
   | **자격 증명**  |  JetStream MSA VM의 루트 사용자 자격 증명입니다.   |
   | **MSIp** | JetStream MSA VM의 IP 주소입니다.   |
   | **실행할 이름 지정**  | 실행의 영숫자 이름(예: **Invoke-Preflight 표시DRUninstall-Exec1)입니다.**   cmdlet이 성공적으로 실행되었는지 확인하는 데 사용됩니다.  |

1. [실행 상태를 확인합니다.](concepts-run-command.md#view-the-status-of-an-execution)

1. 실행 전 cmdlet이 성공적으로 완료되면 **Uninstall-JetDR을** 선택하고, 필요한 값을 제공하거나, 기본값을 변경하고, **실행을** 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **ProtectedCluster** | 보호할 Azure VMware Solution 프라이빗 클라우드 클러스터의 이름입니다(예: **Cluster-1).**  설치하는 동안에는 하나의 클러스터 이름만 제공할 수 있습니다. |
   | **자격 증명**  |  JetStream MSA VM의 루트 사용자 자격 증명입니다.   |
   | **MSIp** | JetStream MSA VM의 IP 주소입니다.   |
   | **실행할 이름 지정**  | 실행의 영숫자 이름(예: **Uninstall-JetDR-Exec1)입니다.**   cmdlet이 성공적으로 실행되었는지 확인하는 데 사용됩니다.  |

 1. [실행 상태를 확인합니다.](concepts-run-command.md#view-the-status-of-an-execution)


## <a name="region-availability"></a>지역 가용성

Azure VMware Solutions용 JetStream DR은 다음 지역에서 사용할 수 있습니다. 

- 미국 동부 

- 북유럽 

- 미국 남부 

- 서유럽 

- 미국 중부 

- 미국 서부 

- 동아시아 

- 일본 동부 

- 브라질 남부 

- 캐나다 동부 

- 영국 서부 


 
## <a name="support"></a>Support(지원)  
 
JetStream DR은 [JetStream Software에서](https://www.jetstreamsoft.com/) 지원하는 솔루션입니다. JetStream과 관련된 제품 또는 지원 문제는 에 support-avs@jetstreamsoft.com 문의합니다.  
 
Azure VMware Solution 실행 명령(미리 보기)을 사용하여 JetStream DR의 설치 및 제거를 모두 자동화합니다. 실행 명령과 관련된 문제는 Microsoft 지원에 문의하십시오. JetStream 설치 및 제거 cmdlet과 관련된 문제는 JetStream에 지원을 요청합니다. 



## <a name="next-steps"></a>다음 단계

- [인프라 설정: Azure VMware Solution 위한 JetStream DR](https://vimeo.com/480574312/b5386a871c) 

- [Azure VMware Solution JetStream DR(전체 데모)](https://vimeo.com/475620858/2ce9413248)
   
   - [Azure VMware Solution JetStream DR 시작](https://vimeo.com/491880696/ec509ff8e3)

   - [VM 구성 및 보호](https://vimeo.com/491881616/d887590fb2)

   - [Azure VMware Solution으로 장애 조치(failover)](https://vimeo.com/491883564/ca9fc57092)

   - [온-프레미스로 장애 조치(failback)](https://vimeo.com/491884402/65ee817b60)

