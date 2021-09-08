---
title: JetStream을 사용하여 재해 복구 배포
description: Azure VMware Solution 프라이빗 클라우드 및 온-프레미스 VMware 워크로드에 대해 JetStream DR(재해 복구)을 배포하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: a26cbaa4f8c37f3148a86005a637b006572685dc
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123315127"
---
# <a name="deploy-disaster-recovery-using-jetstream"></a>JetStream을 사용하여 재해 복구 배포

[JetStream DR(재해 복구)](https://www.jetstreamsoft.com/product-portfolio/jetstream-dr/)은 VMware vSphere 환경에 설치되고 vCenter 플러그 인 어플라이언스를 통해 관리됩니다. VM(가상 머신) I/O 작업을 지속적으로 복제하는 클라우드 네이티브 CDP(지속적 데이터 보호)를 제공합니다. 정기적으로 스냅샷을 캡처하는 대신 실행 중인 애플리케이션에 미치는 영향을 최소화하면서 기본 스토리지에 기록되는 데이터를 지속적으로 캡처하고 복제합니다.  결과적으로 VM과 해당 데이터를 신속하게 복구하여 더 낮은 RPO(복구 지점 목표)에 도달할 수 있습니다.  

Azure VMware Solution을 사용하면 vSAN의 복구 클러스터 또는 Azure NetApp Files와 같은 연결된 파일 시스템에 데이터를 직접 저장할 수 있습니다. 데이터는 vSphere 내에서 실행되는 I/O 필터를 통해 캡처됩니다. 기본 데이터 저장소는 VMFS, VSAN, vVol 또는 모든 HCI 플랫폼일 수 있습니다. 

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-disaster-recovery-diagram.png" alt-text="JetStream DR 및 Azure VMware Solution 통합을 보여 주는 다이어그램." border="false":::

JetStream DR 소프트웨어는 세 가지 주요 구성 요소로 구성됩니다. 

- DR 보호 전에 MSA(관리 서버 가상 어플라이언스)가 설치 및 구성됩니다. 

- DRVA(DR 가상 어플라이언스)는 JetStream DR MSA가 자동으로 배포하는 .ISO 이미지입니다.

- 호스트 구성 요소(IO 필터 패키지) 

MSA는 컴퓨팅 클러스터에 호스트 구성 요소를 설치 및 구성한 다음 JetStream DR 소프트웨어를 관리하는 데 사용됩니다. DRVA는 데이터 경로 DR 구성 요소를 실행합니다. 더 나은 확장성을 위해 여러 DRVA를 동시에 실행할 수 있습니다. 각 DRVA에는 iSCSI LUN 또는 짧은 대기 시간 VDISK로 연결된 하나 이상의 전용 파티션이 있습니다. 파티션은 영구 메타데이터에 대한 복제 로그 및 리포지토리를 유지 관리하는 데 사용됩니다.

이 문서에서는 Azure VMware Solution 프라이빗 클라우드 및 온-프레미스 VMware 워크로드에서 JetStream DR을 사용하는 방법을 배포하고 배웁니다.


## <a name="supported-scenarios"></a>지원되는 시나리오
필요한 보호 서비스와 보호되는 프라이빗 클라우드 유형에 따라 JetStream DR을 두 가지 방법으로 배포할 수 있습니다.

- 온-프레미스와 클라우드 간

- 클라우드 간

### <a name="on-premises-to-cloud-deployment"></a>온-프레미스에서 클라우드로
조직의 VMware 기반 데이터 센터에서 실행되는 VM은 Microsoft Azure에 지속적으로 복제됩니다. 결과적으로 VM은 온-프레미스 데이터 센터에 인시던트가 있는 경우 Azure VMware Solution에서 작업을 재개할 수 있습니다. VM이 복구 환경에서 실행되는 동안 지속적인 보호를 위해 데이터를 계속 복제합니다. 온-프레미스 데이터 센터가 복원된 후 VM 및 해당 데이터(복구 환경에서 VM에 의해 생성된 모든 새 데이터 포함)는 중단 없이 원래 데이터 센터로 반환될 수 있습니다.

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-on-premises-to-cloud-diagram.png" alt-text="Azure VMware Solution 프라이빗 클라우드 JetStream 배포에 대한 온-프레미스 배포를 보여 주는 다이어그램." border="false":::

### <a name="cloud-to-cloud-deployment"></a>클라우드 간 배포

이 구성에서 Azure VMware Solution은 하나의 데이터 센터에서 기본 환경을 호스트합니다. 다른 데이터 센터의 다른 프라이빗 클라우드에 지속적으로 복제하여 VM과 데이터를 보호합니다. 인시던트가 발생하면 두 번째 데이터 센터에서 VM 및 데이터가 복구됩니다. 이 보호는 데이터 센터 "A"가 "B"를 보호하는 경우일 수 있으며 그 반대의 경우도 마찬가지입니다.

:::image type="content" source="media/jetstream-disaster-recovery/jetstream-cloud-to-cloud-diagram.png" alt-text="Azure VMware Solution 프라이빗 클라우드에서 프라이빗 클라우드 JetStream으로의 배포를 보여 주는 다이어그램" border="false":::


## <a name="prerequisites"></a>필수 조건

- 보조 지역으로 배포된 Azure VMware Solution 프라이빗 클라우드. 

- Azure VMware Solution 프라이빗 클라우드에 대한 ExpressRoute 연결이 있는 Ubuntu Linux 점프 상자. 

- Linux 점프 상자에 설치된 최신 PowerShell.

- PowerShell 갤러리의 최신 타사 모듈이 설치되었습니다.

### <a name="protected-site"></a>보호된 사이트

보호된 사이트는 vCenter, DNS, Active Directory 및 보호된 기간 업무 애플리케이션이 실행되는 **컴퓨터 클러스터** 와 같은 관리 서비스를 위한 **서비스 클러스터** 를 호스트합니다. 보호된 사이트는 온-프레미스에 있거나 Azure VMware Solution에서 호스트될 수 있습니다.

다음 유형 중 하나를 사용할 수 있습니다.

- Azure Blob Storage

- Azure VMware Solution vSAN

- Azure NetApp Files와 같은 Azure VMware Solution 연결된 파일 시스템


| **Item**  | **설명**  |  
| --- | --- |  
| **vCenter Server**  | <ul><li>지원되는 버전: 6.7</li><li>HTTPS 포트: 방화벽을 사용하는 경우 HTTPS 포트 443이 열려 있어야 합니다.</li><li>연결: JetStream DR 관리 서버 어플라이언스 FQDN은 vCenter에서 연결할 수 있어야 합니다. 그렇지 않으면 플러그 인 설치가 실패합니다.</li><li>시간: vCenter 및 JetStream DR MSA 시계가 동기화되어야 합니다.</li></ul>  |
| **DRS(분산 리소스 스케줄러)**  | 리소스 분산을 위해 컴퓨팅 클러스터에서 권장됩니다.  |
| **클러스터**  | vSphere 호스트: JetStream DR로 보호할 VM은 클러스터의 일부여야 합니다.  |
| **vSphere 호스트**  | <ul><li>지원되는 버전: 6.7U1(빌드 #10302608) 이상</li><li>연결: 호스트에서 vCenter Server FQDN에 연결할 수 있어야 합니다. 그렇지 않으면 호스트 구성이 실패합니다.</li><li>시간: vSphere 호스트와 JetStream DR MSA 시계가 동기화되어야 합니다.</li><li>CIM 서비스: 기본 설정인 CIM 서버를 사용하도록 설정해야 합니다.</li></ul>  |
| **JetStream DR MSA**  | <ul><li>CPU: 64비트, vCPU 4개</li><li>메모리: 4GB</li><li>디스크 공간: 60GB</li><li>네트워크: 고정 또는 동적 할당(DHCP) IP 주소를 사용할 수 있습니다. FQDN은 DNS에 등록되어 있어야 합니다.</li><li>DNS: vSphere 호스트 및 vCenter Server에 대한 DNS 이름 확인</li></ul>  |
| **JetStream DRVA**  | <ul><li>CPU: 4 코어</li><li>메모리: 8GB</li><li>네트워크: 고정 또는 동적 할당(DHCP) IP 주소를 사용할 수 있습니다.</li></ul>  |
| **복제 로그 저장소**  | 보호된 사이트는 최적의 성능을 위해 호스트가 클러스터에서 공유하는 짧은 대기 시간의 플래시 스토리지 디바이스를 노출해야 합니다. 이 디바이스는 JetStream DR 소프트웨어로 제어하거나 타사에서 제공할 수 있습니다. 복제 로그의 리포지토리로 사용됩니다. DRVA 및 ESXi 호스트는 iSCSI를 통해 이 스토리지에 직접 액세스할 수 있어야 합니다.  |
| **Ports**  | JetStream DR 소프트웨어가 설치되면 원본 ESXi 호스트에서 다양한 포트가 자동으로 열립니다. 따라서 대부분의 사용자에게는 더 이상의 작업이 필요하지 않습니다. 그러나 온-프레미스/원본 설정에 이러한 포트를 차단하는 특수 방화벽 규칙이 있는 경우 이러한 포트를 수동으로 열어야 합니다.<br /><br />포트 범위: 32873-32878  |



### <a name="recovery-site"></a>복구 사이트

장애 조치(failover) 복구를 위해 Azure VMware Solution _표시등_ 클러스터가 설정됩니다. 복구 사이트는 설치 프로세스의 일부로 만들어지지만 복구 사이트 클러스터는 정상 작동 중에 완전히 채워지지 않거나 사용되지 않습니다. 장애 조치(failover)된 컴퓨팅 클러스터는 재해 이벤트에 대한 응답으로 요청 시 복구 사이트에 추가됩니다.

### <a name="network"></a>네트워크

보호 사이트와 복구 사이트 사이에는 다음과 같은 특성을 가진 네트워크가 설정되어야 합니다.

| **항목** | **설명** |
| --- | --- |
| **JetStream DR MSA**  | MSA에는 관리 네트워크가 필요합니다. 이 네트워크는 JetStream DR RESTful API에 액세스하고 다른 데이터 경로를 호출하는 데 사용됩니다. 개체 저장소에 연결하는 데 프라이빗 네트워크를 사용할 수 있는 경우 이 프라이빗 네트워크를 MSA VM에 별도의 네트워크로 추가해야 합니다. 프라이빗 네트워크를 사용할 수 없는 경우 관리 네트워크를 사용하여 개체 저장소에 연결할 수 있는지 확인합니다. <br /><br />전용 외부 네트워크를 개체 저장소 액세스에 사용할 수 있습니다. 그렇지 않으면 데이터 트래픽이 관리 네트워크를 통해 전송됩니다.  | 
| **JetStream DRVA**  | 사용되는 유일한 네트워크가 관리 네트워크인 경우 IO 필터와 개체 저장소에 모두 액세스할 수 있는지 확인합니다. 클러스터 내에 여러 네트워크가 있는 경우 모두 DRVA VM에 연결되어 있어야 합니다.  | 
| **RocVA(개체 클라우드 가상 어플라이언스로부터 복구)**  | 사용되는 유일한 네트워크가 관리 네트워크인 경우 ESXi 호스트와 개체 저장소에 모두 액세스할 수 있는지 확인합니다. 클러스터 내에 여러 네트워크가 있는 경우 모두 RocVA VM에 연결되어 있어야 합니다. RocVA는 VM 복구에 필요할 때 자동으로 만들어지고 더 이상 필요하지 않을 때 제거되는 임시 VM입니다.  | 
| **개체 스토리지/Blob Storage**  | 개체 스토리지/Blob Storage는 보호된 사이트와 복구 사이트 모두에서 액세스할 수 있어야 합니다.  | 
| **복제 로그 저장소**  | DRVA 및 ESXi 호스트는 iSCSI를 통해 이 스토리지에 직접 액세스할 수 있어야 합니다.  | 


## <a name="install-jetstream-dr"></a>JetStream DR 설치

JetStream DR 설치는 Azure VMware Solution 포털의 실행 명령 기능을 통해 사용할 수 있습니다. 3단계로 설치를 완료합니다.

:::image type="content" source="media/run-command/run-command-overview-jetstream.png" alt-text="사용 가능한 JetStream 실행 명령에 액세스하는 방법을 보여 주는 스크린샷" lightbox="media/run-command/run-command-overview-jetstream.png":::


>[!NOTE]
>실행 명령은 제출된 순서대로 한 번에 하나씩 실행됩니다.

### <a name="check-the-current-state-of-the-system"></a>시스템의 현재 상태 확인

`Invoke-PreflightJetDRSystemCheck` cmdlet을 실행하여 시스템 상태와 스크립트에 대한 최소 요구 사항이 충족되는지 여부를 확인합니다. 또한 다른 cmdlet을 실행하는 데 필요한 vCenter 구성을 확인합니다.  

cmdlet은 다음을 확인합니다.

- PowerShell
- vCenter FQDN
- CloudAdmin 역할
- VMware 모듈

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **실행 명령** > **패키지** > **Invoke-PreflightJetDRSystemCheck** 를 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **다음까지 유지**  | cmdlet 출력의 보존 기간입니다. 기본값은 60입니다.  |
   | **실행할 이름 지정**  | 영숫자 이름(예: **checkDRsystem**)입니다.  |
   | **Timeout**  | 완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. 진행 상황을 보려면 **알림** 을 확인합니다. 



### <a name="check-cluster-configuration"></a>클러스터 구성 확인

`Invoke-PreflightJetDRInstall` cmdlet을 실행하여 다음 클러스터 구성을 확인합니다.

- 클러스터 세부 정보가 올바른 경우 
- 최소 4개의 호스트가 있음(최소)
- MSA 설치를 위해 제공된 동일한 이름의 VM이 있는 경우
- vCenter에 **jetdr** 플러그 인이 있는 경우


1. **실행 명령** > **패키지** > **Invoke-PreflightJetDRInstall** 을 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **VMName** | MSA VM의 이름입니다. 예: **jetstreamServer**. |
   | **클러스터** | MSA가 배포될 클러스터 이름입니다. 예: **Cluster-1**. |
   | **ProtectedCluster** | 보호할 클러스터입니다. 예: **Cluster-1**. |
   | **다음까지 유지**  | cmdlet 출력의 보존 기간입니다. 기본값은 60입니다.  |
   | **실행할 이름 지정**  | 영숫자 이름(예: **check_jetserverdetails**)입니다.  |
   | **Timeout**  | 완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. 진행 상황을 보려면 **알림** 을 확인합니다.  

1. 오류가 보고되면 다음 단계로 이동하여 JetDR MSA를 배포할 수 있습니다.


### <a name="deploy-jetdr-msa"></a>JetDR MSA 배포

`Install-JetDR` cmdlet을 실행하여 JetDR MSA를 배포하고 vCenter를 JetDR MSA에 등록하고 클러스터를 구성합니다. 배포는 MMS(Microsoft Server Media)에서 JetDR 번들을 다운로드하고 할당된 상승된 권한을 가진 새 사용자를 만듭니다. 

1. **실행 명령** > **패키지** > **Install-JetDR** 을 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **Network** | 배포할 MSA에 대한 네트워크 매핑입니다. 예: **VM 네트워크**.  |
   | **HostName** | 배포할 MSA의 호스트 이름(FQDN)입니다.  |
   | **자격 증명** | 배포할 MSA의 루트 사용자 자격 증명입니다.   |
   | **게이트웨이** | 배포할 MSA의 게이트웨이입니다. DHCP의 경우 비워 둡니다.  |
   | **Dns** | MSA가 사용해야 하는 DNS IP입니다. DHCP의 경우 비워 둡니다.  |
   | **MSIp** | 배포할 MSA의 IP 주소입니다. DHCP의 경우 비워 둡니다.  |
   | **네트워크 마스크** | 배포할 MSA의 넷마스크입니다. DHCP의 경우 비워 둡니다.  |
   | **클러스터** | MSA가 배포될 클러스터 이름입니다. 예: **Cluster-1**. |
   | **VMName** | MSA VM의 이름입니다. 예: **jetstreamServer**. |
   | **Datastore** | MSA가 배포될 데이터 저장소입니다.  |
   | **ProtectedCluster** | 보호할 클러스터입니다. 예: **Cluster-1**. |
   | **RegisterWithIp** | 호스트 이름 대신 IP로 MSA를 등록합니다. <ul><li>MSA의 호스트 이름이 DNS에 등록되지 않은 경우 True입니다.</li><li>MSA의 호스트 이름이 DNS에 등록된 경우 False입니다. </li></ul> |
   | **다음까지 유지**  | cmdlet 출력의 보존 기간입니다. 기본값은 60입니다.  |
   | **실행할 이름 지정**  | 영숫자 이름(예: **check_jetserverdetails**)입니다.  |
   | **Timeout**  |  완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. 진행 상황을 보려면 **알림** 을 확인합니다.  


## <a name="uninstall-jetstream-dr"></a>JetStream DR 제거

두 단계로 JetStream DR을 제거합니다. 


### <a name="check-current-state-of-the-jetstream-appliance"></a>JetStream 어플라이언스의 현재 상태 확인

`Invoke-PreflightJetDRUninstall` cmdlet을 실행하여 기존 MSA VM 및 클러스터 구성을 진단합니다. JetStream DR 어플라이언스의 현재 상태와 스크립트에 대한 최소 요구 사항이 충족되는지 확인합니다.

- 클러스터 세부 정보가 올바른 경우 
- 최소 4개의 호스트가 있음(최소)
- vCenter가 MSA에 등록된 경우

1. **실행 명령** > **패키지** > **Invoke-PreflightJetDRUninstall** 을 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **MSIp** | MSA VM의 IP 주소입니다.  |
   | **자격 증명** | MSA VM의 루트 사용자 자격 증명입니다. 설치 시 제공되는 것과 동일해야 합니다.  |
   | **ProtectedCluster** | 보호된 클러스터의 이름(예: **Cluster-1**)입니다.  설치 시 제공된 클러스터여야 합니다. |
   | **다음까지 유지**  | cmdlet 출력의 보존 기간입니다. 기본값은 60입니다.  |
   | **실행할 이름 지정**  | 영숫자 이름(예: **uninstallcheck_jetserverdetails**)입니다.  |
   | **Timeout**  |  완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. 진행 상황을 보려면 **알림** 을 확인합니다.  




### <a name="uninstall-jetdr"></a>JetDR 제거

`Uninstall-JetDR` cmdlet을 실행하여 JetStream DR 및 해당 구성 요소를 제거합니다.  클러스터를 구성 해제하고 MSA에서 vCenter를 등록 해제한 다음 사용자를 제거합니다.

1. **실행 명령** > **패키지** > **Uninstall-JetDR** 을 선택합니다.

1. 필요한 값을 제공하거나 기본값을 변경한 다음 **실행** 을 선택합니다.

   | **필드** | **값** |
   | --- | --- |
   | **MSIp** | MSA VM의 IP 주소입니다.  |
   | **자격 증명** | MSA VM의 루트 사용자 자격 증명입니다. 설치 시 제공되는 것과 동일해야 합니다.   |
   | **ProtectedCluster** | 보호된 클러스터의 이름(예: **Cluster-1**)입니다.  설치 시 제공된 클러스터여야 합니다. |
   | **다음까지 유지**  | cmdlet 출력의 보존 기간입니다. 기본값은 60입니다.  |
   | **실행할 이름 지정**  | 영숫자 이름(예: **uninstallcheck_jetserverdetails**)입니다.  |
   | **Timeout**  |  완료하는 데 너무 오래 걸릴 경우 cmdlet이 종료되는 기간입니다.  |

1. 진행 상황을 보려면 **알림** 을 확인합니다.  


## <a name="next-steps"></a>다음 단계

- [Azure VMware Solution에 대한 JetStream DR - 전체 데모](https://vimeo.com/475620858/2ce9413248)
   
   - [Azure VMware Solution에 대한 JetStream DR 시작](https://vimeo.com/491880696/ec509ff8e3)

   - [VM 구성 및 보호](https://vimeo.com/491881616/d887590fb2)

   - [Azure VMware Solution으로 장애 조치(failover)](https://vimeo.com/491883564/ca9fc57092)

   - [온-프레미스로 장애 복구(failback)](https://vimeo.com/491884402/65ee817b60)

- [Azure VMware Solution에 대한 JetStream DR 인프라 설정](https://vimeo.com/480574312/b5386a871c)(*기술 세부 정보, 음성 트랙 없음*)
