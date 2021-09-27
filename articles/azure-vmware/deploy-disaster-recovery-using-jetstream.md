---
title: JetStream DR(미리 보기)을 사용하여 재해 복구 배포
description: Azure VMware Solution 프라이빗 클라우드 및 온-프레미스 VMware 워크로드에 JetStream DR을 구현하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: references_regions
ms.openlocfilehash: 75bb77ad575f96e7605fe6a19e12d7129c28dac6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128554442"
---
# <a name="deploy-disaster-recovery-using-jetstream-dr-preview"></a>JetStream DR(미리 보기)을 사용하여 재해 복구 배포

>[!IMPORTANT]
>Azure VMware Solution JetStream DR(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
>이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
>자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[JetStream DR은](https://www.jetstreamsoft.com/product-portfolio/jetstream-dr/) 재해가 발생한 경우 VM(가상 머신)의 가동 중지 시간을 최소화하도록 설계된 클라우드 네이티브 재해 복구 솔루션입니다. JetStream DR의 인스턴스는 보호된 사이트와 복구 사이트 모두에 배포됩니다. 

JetStream은 데이터 손실을 최소화하거나 거의 사용하지 않는 [VMware VAIO 프레임워크를](https://core.vmware.com/resource/vmware-vsphere-apis-io-filtering-vaio)사용하여 CDP(지속적인 데이터 보호)를 기반으로 합니다. JetStream DR은 비즈니스 및 중요 업무용 애플리케이션에 대해 원하는 보호 수준을 제공합니다. 또한 DR 사이트에서 최소한의 리소스를 사용하고 [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)같은 비용 효율적인 클라우드 스토리지를 사용하여 비용 효율적인 DR을 가능하게 합니다.

이 문서에서는 Azure VMware Solution 프라이빗 클라우드 및 온-프레미스 VMware 워크로드에 대해 JetStream DR을 구현합니다.

JetStream DR에 대한 자세한 내용은 다음을 참조하세요.

- [JetStream 솔루션 간략한 설명](https://www.jetstreamsoft.com/2020/09/28/solution-brief-disaster-recovery-for-avs/)

- [JetStream DR on Azure Marketplace](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020)

- [JetStream 기술 자료 문서](https://www.jetstreamsoft.com/resources/knowledge-base/)


## <a name="core-components-of-the-jetstream-dr-solution"></a>JetStream DR 솔루션의 핵심 구성 요소

| 항목 | 설명 |
| --- | --- |
| **JetStream 관리 서버 MSA(가상 어플라이언스)**  | MSA는 기본 사이트, 보호 도메인 및 VM 복구와 같은 0일차 및 2일차 구성을 모두 사용하도록 설정합니다.  MSA는 클라우드 관리자가 vSphere 노드에 설치합니다.  MSA는 vCenter에서 기본적으로 JetStream DR을 관리할 수 있는 vCenter 플러그 인을 구현합니다. MSA는 보호된 VM의 복제 데이터를 처리하지 않습니다.  | 
| **JetStream DRVA(DRVA)**  | Linux 기반 Virtual Machine 어플라이언스는 원본 ESXi 호스트에서 보호된 VM 복제 데이터를 받습니다. 복제 데이터를 DR 사이트(일반적으로 Azure Blob Storage 같은 개체 저장소)에 저장하는 역할을 담당합니다. 보호된 VM 수와 복제할 스토리지 양에 따라 프라이빗 cloudadmin은 하나 이상의 DRVA 인스턴스를 만들 수 있습니다.  | 
| **JetStream ESXi 호스트 구성 요소(IO 필터 패키지)**  | JetStream DR에 대해 구성된 각 ESXi 호스트에 설치된 JetStream 소프트웨어입니다. 호스트 드라이버는 vSphere VM IO를 가로채서 복제 데이터를 DRVA로 보냅니다.   | 
| **JetStream 보호 도메인**  | 동일한 정책 및 실행 책을 사용하여 함께 보호될 VM의 논리적 그룹입니다. 보호 도메인의 모든 VM에 대한 데이터는 동일한 Azure Blob 컨테이너 인스턴스에 저장됩니다. 동일한 DRVA 인스턴스는 보호 도메인의 모든 VM에 대해 원격 DR 스토리지에 대한 복제를 처리합니다.   | 
| **Azure Blob Storage 컨테이너**  | 보호된 VM 복제 데이터는 Azure Blob에 저장됩니다. JetStream 소프트웨어는 각 JetStream 보호 도메인에 대해 하나의 Azure Blob 컨테이너 인스턴스를 만듭니다.    | 



## <a name="jetstream-scenarios-on-azure-vmware-solution"></a>Azure VMware Solution JetStream 시나리오
다음 두 가지 시나리오에서 Azure VMware Solution JetStream DR을 사용할 수 있습니다.  

- DR을 Azure VMware Solution 온-프레미스 VMware

- Azure VMware Solution DR에 대한 Azure VMware Solution

### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>시나리오 1: DR을 Azure VMware Solution 온-프레미스 VMware

이 시나리오에서 기본 사이트는 온-프레미스 VMware 환경이고 DR 사이트는 Azure VMware Solution 프라이빗 클라우드입니다.

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png" alt-text="Azure VMware Solution 프라이빗 클라우드 JetStream 배포에 대한 온-프레미스 배포를 보여 주는 다이어그램." border="false" lightbox="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png":::

### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-dr"></a>시나리오 2: Azure VMware Solution DR에 Azure VMware Solution

이 시나리오에서 기본 사이트는 하나의 Azure 지역에 Azure VMware Solution 프라이빗 클라우드입니다. 재해 복구 사이트는 다른 Azure 지역의 Azure VMware Solution 프라이빗 클라우드입니다. 

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png" alt-text="Azure VMware Solution 프라이빗 클라우드에서 프라이빗 클라우드 JetStream으로의 배포를 보여 주는 다이어그램" border="false" lightbox="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png":::


## <a name="prerequisites"></a>필수 조건


### <a name="scenario-1-on-premises-vmware-to-azure-vmware-solution-dr"></a>시나리오 1: DR을 Azure VMware Solution 온-프레미스 VMware 

- Azure VMware Solution 프라이빗 클라우드는 대상 DR 지역에 최소 3개의 노드로 배포됩니다.  


   :::image type="content" source="media/jetstream-disaster-recovery/disaster-recovery-scenario-prerequisite.png" alt-text="Azure VMware Solution 재해 복구 솔루션에 대한 첫 번째 필수 구성 요소 다이어그램" lightbox="media/jetstream-disaster-recovery/disaster-recovery-scenario-prerequisite.png":::


- 기본 사이트 JetStream 어플라이언스와 Azure Storage Blob 인스턴스 간에 구성된 네트워크 연결입니다. 

- Azure Marketplace [JetStream DR을 설치하고 구독하여](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020) JetStream DR 소프트웨어를 다운로드합니다.

- 표준 또는 Premium 성능 계층을 사용하여 만든 [Azure Blob](../storage/common/storage-account-create.md) Storage 계정입니다. [액세스 계층에 **대해 핫을** 선택합니다.](../storage/blobs/storage-blob-storage-tiers.md) 

   >[!NOTE]
   >Blob에서 **계층 구조 네임스페이스 사용** 옵션은 지원되지 않습니다.   

- Azure VMware Solution 프라이빗 클라우드에 구성된 NSX-T 네트워크 세그먼트이며, 필요에 따라 JetStream 가상 어플라이언스에 대한 세그먼트에서 DHCP를 사용하도록 설정합니다.  

- jetStream 가상 어플라이언스에 대한 Azure VMware Solution vCenter, Azure VMware Solution ESXi 호스트, Azure Storage 계정 및 JetStream Marketplace 서비스의 IP 주소를 확인하도록 구성된 DNS 서버입니다. 



### <a name="scenario-2-azure-vmware-solution-to-azure-vmware-solution-dr"></a>시나리오 2: Azure VMware Solution DR에 Azure VMware Solution

- Azure VMware Solution 프라이빗 클라우드는 주 지역과 보조 지역 모두에서 최소 3개의 노드로 배포됩니다.  
 
- 기본 사이트 JetStream 어플라이언스와 Azure Storage Blob 인스턴스 간에 구성된 네트워크 연결입니다. 

- Azure Marketplace [JetStream DR을 설치하고 구독하여](https://ms.portal.azure.com/#create/jetstreamsoftware1596597632545.jsdravs-093020) JetStream DR 소프트웨어를 다운로드합니다.

- 표준 또는 Premium 성능 계층을 사용하여 만든 [Azure Blob](../storage/common/storage-account-create.md) Storage 계정입니다. [액세스 계층에 **대해 핫을** 선택합니다.](../storage/blobs/storage-blob-storage-tiers.md) 

   >[!NOTE]
   >Blob에서 **계층 구조 네임스페이스 사용** 옵션은 지원되지 않습니다.   

- Azure VMware Solution 프라이빗 클라우드에 구성된 NSX-T 네트워크 세그먼트이며, 필요에 따라 JetStream 가상 어플라이언스에 대한 세그먼트에서 DHCP를 사용하도록 설정합니다.  

- 기본 사이트와 DR 사이트 모두에 구성된 DNS 서버는 Azure VMware Solution vCenter, Azure VMware Solution ESXi 호스트, Azure Storage 계정 및 JetStream 가상 어플라이언스에 대한 JetStream Marketplace 서비스의 IP 주소를 확인합니다. 


자세한 온-프레미스 JetStream DR 필수 구성 조건은 [JetStream 사전 설치 가이드를 참조하세요.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/pre-installation-guidelines/)





## <a name="install-jetstream-dr-on-azure-vmware-solution"></a>Azure VMware Solution JetStream DR 설치  
 
지원되는 두 시나리오에 대해 다음 단계를 따를 수 있습니다. 
 
1. 온-프레미스 데이터 센터에서 JetStream 설명서 에 따라 [JetStream DR을](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/installing-jetstream-dr-software/)설치합니다.  

1. Azure VMware Solution 프라이빗 클라우드에서 실행 명령을 사용하여 JetStream DR을 설치합니다. [Azure Portal](https://portal.azure.com)명령 **실행**  >  **JSDR을**  >  **선택합니다. 구성**.  

   :::image type="content" source="media/run-command/run-command-overview-jetstream.png" alt-text="사용 가능한 JetStream 실행 명령에 액세스하는 방법을 보여 주는 스크린샷" lightbox="media/run-command/run-command-overview-jetstream.png":::
 
   >[!NOTE]
   >Azure VMware Solution 기본 CloudAdmin 사용자에게 JetStream DR을 설치할 수 있는 충분한 권한이 없습니다.  Azure VMware Solution JetStream DR에 대한 Azure VMware Solution Run 명령을 호출하여 JetStream DR의 간소화되고 자동화된 설치를 지원합니다.  
 

1. **Invoke-Preflight 표시DRInstall** cmdlet을 실행하여 JetStream DR을 설치하기 위한 필수 조건이 충족되었는지 확인합니다. 예를 들어 필요한 수의 호스트, 클러스터 이름 및 고유한 VM 이름의 유효성을 검사합니다. 

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **Network**  | JetStream MSA를 배포해야 하는 NSX-T 네트워크 세그먼트의 이름입니다.  |
   | **Datastore**  | MSA를 배포할 데이터 저장소의 이름입니다.  |
   | **ProtectedCluster** | 보호할 Azure VMware Solution 프라이빗 클라우드 클러스터의 이름입니다(예: **Cluster-1).**  하나의 클러스터 이름만 제공할 수 있습니다. |
   | **Cluster** | JetStream MSA가 배포된 Azure VMware Solution 프라이빗 클러스터의 이름입니다(예: **Cluster-1).** |
   | **VMName** | JetStream MSA VM의 이름입니다(예: **jetstreamServer).** |
   | **실행할 이름 지정**  | 실행의 영숫자 이름(예: **Invoke-Preflight 표시DRInstall-Exec1)입니다.** cmdlet이 성공적으로 실행되었는지 확인하는 데 사용됩니다. |
   | **Timeout**  | 완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. [실행 상태를 확인합니다.](concepts-run-command.md#view-the-status-of-an-execution)
   

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
 
이 섹션에서는 JetStream DR을 구성하는 데 필요한 단계의 개요만 다룹니다.  자세한 설명 및 단계는 [JetStream DR 구성](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/configuring-jetstream-dr/) 설명서를 참조하세요. 
 
JetStream DR MSA 및 JetStream VIB가 Azure VMware Solution 클러스터에 설치되면 JetStream 포털을 사용하여 나머지 구성 단계를 완료합니다. 



1. vCenter 어플라이언스에서 JetStream 포털에 액세스합니다.

1. [외부 스토리지 사이트 를 추가합니다.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/add-a-storage-site/)  

1. [JetStream DRVA 어플라이언스 를 배포합니다.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/deploy-a-dr-virtual-appliance/) 

1. Azure VMware Solution 클러스터에서 사용할 수 있는 데이터 저장소 중 하나를 사용하여 [JetStream 복제 로그 저장소 볼륨을 만듭니다.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/create-a-replication-log-store-volume/) 

   >[!TIP]
   >복제 로그에는 vSAN 데이터 저장소와 같은 빠른 로컬 스토리지가 선호됩니다. 
 
1. [JetStream 보호된 도메인 을 만듭니다.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/create-a-protected-domain/) Azure Blob Storage 사이트, JetStream DRVA 인스턴스 및 이전 단계에서 만든 복제 로그를 제공합니다. 

1. 보호하려는 [VM을 선택한](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/select-vms-for-protection/) [다음, VM 보호를 시작합니다.](https://www.jetstreamsoft.com/portal/jetstream-knowledge-base/start-vm-protection/)

 
장애 조치(failover) Runbook 만들기, DR 사이트로 장애 조치(failover) 호출 및 기본 사이트에 대한 장애 복구(failback) 호출과 같은 JetStream DR에 대한 나머지 구성 단계는 [JetStream 관리자 가이드 설명서를 참조하세요.](https://www.jetstreamsoft.com/portal/jetstream-article-categories/product-manual/)  
 
## <a name="disable-jetstream-dr-on-an-azure-vmware-solution-cluster"></a>Azure VMware Solution 클러스터에서 JetStream DR 사용 안 함  
 
이 cmdlet은 클러스터 중 하나에서만 JetStream DR을 사용하지 않도록 설정하고 JetStream DR을 완전히 제거하지 않습니다. 

1. **실행 명령**  >  **패키지**  >  **Disable-JetDRForCluster를** 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **ProtectedCluster** | 보호할 Azure VMware Solution 프라이빗 클라우드 클러스터의 이름입니다(예: **Cluster-1).**  설치하는 동안에는 하나의 클러스터 이름만 제공할 수 있습니다. |
   | **자격 증명**  |  JetStream MSA VM의 루트 사용자 자격 증명입니다.   |
   | **MSIp** | JetStream MSA VM의 IP 주소입니다.   |
   | **실행할 이름 지정**  | 실행의 영숫자 이름입니다 (예: **JetDRForCluster-Exec1)**.   Cmdlet이 성공적으로 실행 되었는지 확인 하는 데 사용 됩니다.  |

1. [실행 상태를 확인](concepts-run-command.md#view-the-status-of-an-execution)합니다.


 
## <a name="uninstall-jetstream-dr"></a>JetStream DR 제거  

1. **실행 명령** > **패키지** > **Invoke-PreflightJetDRUninstall** 을 선택합니다. 이 cmdlet은 클러스터에 4 개 이상의 호스트가 있는지 확인 합니다 (최소 필수). 

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **ProtectedCluster** | 보호할 Azure VMware 솔루션 사설 클라우드 클러스터의 이름 (예: **cluster-1**)입니다.  설치 하는 동안 클러스터 이름을 하나만 제공할 수 있습니다. |
   | **자격 증명**  |  JetStream MSA VM의 루트 사용자에 대 한 자격 증명입니다.   |
   | **MSIp** | JetStream MSA VM의 IP 주소입니다.   |
   | **실행할 이름 지정**  | 실행의 영숫자 이름입니다 (예: **PreflightJetDRUninstall-Exec1)**.   Cmdlet이 성공적으로 실행 되었는지 확인 하는 데 사용 됩니다.  |

1. [실행 상태를 확인](concepts-run-command.md#view-the-status-of-an-execution)합니다.

1. 실행 전 cmdlet이 성공적으로 완료 되 면 **JetDR** 를 선택 하 고, 필요한 값을 입력 하거나 기본값을 변경 하 고, **실행** 을 선택 합니다.

   | **필드** | **값** |
   | --- | --- |
   | **ProtectedCluster** | 보호할 Azure VMware 솔루션 사설 클라우드 클러스터의 이름 (예: **cluster-1**)입니다.  설치 하는 동안 클러스터 이름을 하나만 제공할 수 있습니다. |
   | **자격 증명**  |  JetStream MSA VM의 루트 사용자에 대 한 자격 증명입니다.   |
   | **MSIp** | JetStream MSA VM의 IP 주소입니다.   |
   | **실행할 이름 지정**  | 실행의 영숫자 이름입니다 (예: **JetDR-Exec1)**.   Cmdlet이 성공적으로 실행 되었는지 확인 하는 데 사용 됩니다.  |

 1. [실행 상태를 확인](concepts-run-command.md#view-the-status-of-an-execution)합니다.


## <a name="region-availability"></a>지역 가용성

Azure VMware 솔루션에 대 한 JetStream DR은 다음 지역에서 제공 됩니다. 

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
 
JetStream DR은 [JetStream 소프트웨어](https://www.jetstreamsoft.com/) 에서 지 원하는 솔루션입니다. JetStream 관련 된 모든 제품 또는 지원 문제는에 문의 하세요 support-avs@jetstreamsoft.com .  
 
Azure VMware 솔루션은 실행 명령 (미리 보기)을 사용 하 여 JetStream DR의 설치 및 제거를 자동화 합니다. 실행 명령에 대 한 문제는 Microsoft 지원에 문의 하세요. JetStream 설치 및 제거 cmdlet에 대 한 문제는 JetStream에 지원을 문의 하세요. 



## <a name="next-steps"></a>다음 단계

- [인프라 설정: Azure VMware 솔루션에 대 한 JetStream DR](https://vimeo.com/480574312/b5386a871c) 

- [Azure VMware 솔루션에 대 한 JetStream DR (전체 데모)](https://vimeo.com/475620858/2ce9413248)
   
   - [Azure VMware 솔루션에 대 한 JetStream DR 시작](https://vimeo.com/491880696/ec509ff8e3)

   - [Vm 구성 및 보호](https://vimeo.com/491881616/d887590fb2)

   - [Azure VMware Solution으로 장애 조치(failover)](https://vimeo.com/491883564/ca9fc57092)

   - [온-프레미스로 장애 복구](https://vimeo.com/491884402/65ee817b60)

