---
title: Azure의 BareMetal Infrastructure란?
description: Azure의 BareMetal Infrastructure에 대한 개요를 제공합니다.
ms.custom: references_regions
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: 135de43f26b969d3df79f6ec7348d295493e5134
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113765808"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Azure의 BareMetal Infrastructure란?

Microsoft Azure는 고객의 비즈니스 요구 사항을 충족하는 광범위한 통합 클라우드 서비스를 갖춘 클라우드 인프라를 제공합니다. 하지만 가상화 계층이 없는 운영 체제 미설치 서버에서 서비스를 실행해야 하는 경우가 있습니다. 루트 액세스 권한과 OS(운영 체제)에 대한 제어권이 필요할 수 있습니다. 이 요구 사항을 충족할 수 있도록 Azure는 여러 가지 중요 업무용 애플리케이션을 위한 BareMetal Infrastructure를 제공합니다.

BareMetal Infrastructure는 전용 BareMetal 인스턴스(컴퓨팅 인스턴스)로 구성됩니다. 기능은 다음과 같습니다.
- 애플리케이션(NFS, ISCSI 및 파이버 채널)에 적합한 고성능 스토리지입니다. BareMetal 인스턴스 간에 스토리지를 공유하여 스케일 아웃 클러스터 또는 STONITH를 사용하는 고가용성 쌍과 같은 기능을 사용할 수 있습니다. 
- 격리된 환경에서 함수별 VLAN(가상 LAN)의 집합입니다. 
 
또한 이 환경에는 Azure 구독에 있는 하나 이상의 Azure VNet(Virtual Network)에서 VM(가상 머신)을 실행하는 경우에 액세스할 수 있는 특별한 VLAN이 있습니다. 전체 환경은 Azure 구독에서 리소스 그룹으로 표시됩니다.

BareMetal Infrastructure는 2소켓~24소켓 서버와 1.5TB~24TB 메모리에 걸쳐 30개 이상의 SKU를 제공합니다. 대형 SKU 세트를 옥테인 메모리와 함께 사용할 수도 있습니다. Azure는 하이퍼스케일 클라우드에서 가장 광범위한 운영 체제 미설치 인스턴스를 제공합니다.

## <a name="why-baremetal-infrastructure"></a>BareMetal Infrastructure를 선택하는 이유  

기업의 워크로드 중 일부는 일반적인 가상화 클라우드 설정에서 실행되도록 설계되지 않은 기술로 이루어져 있습니다. 이러한 워크로드에는 특수 아키텍처, 인증된 하드웨어 또는 매우 큰 크기가 필요합니다. 이러한 기술은 가장 정교한 데이터 보호 및 비즈니스 연속성 기능을 제공하지만 해당 기능은 가상화 클라우드를 위해 빌드된 것이 아닙니다. 이러한 기능은 대기 시간 및 노이즈가 많은 주변 환경에 민감하며 변경 관리 및 유지 관리 활동에 대한 관리 부담이 더 많습니다.

BareMetal Infrastructure는 이러한 애플리케이션을 위해 빌드되고, 인증되고, 테스트를 거쳤습니다. Azure는 최초로 이러한 솔루션을 제공했으며, 가장 방대한 포트폴리오와 가장 정교한 시스템을 자랑합니다.

### <a name="baremetal-benefits"></a>BareMetal의 이점  

BareMetal Infrastructure는 기업 애플리케이션에서 실행하려면 인증이 필요한 중요 워크로드를 위한 제품입니다. BareMetal 인스턴스는 고객 전용이며, 고객은 OS(운영 체제)에 대한 전체 권한(루트 액세스)을 갖습니다. 고객이 필요한 대로 OS 및 애플리케이션 설치를 관리합니다. 보안을 위해 인스턴스는 인터넷에 연결되지 않은 Azure VNet(Virtual Network) 내에서 프로비저닝됩니다. VM(가상 머신)에서 실행되는 서비스 및 동일한 계층 2 네트워크의 다른 Azure 서비스만 BareMetal 인스턴스와 통신할 수 있습니다.  

BareMetal Infrastructure는 다음과 같은 이점을 제공합니다.

- 특수 워크로드에 대해 인증된 하드웨어
    - SAP([SAP 노트 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 참조)
    - Oracle([Oracle 문서 ID #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52) 참조)
- 하이퍼바이저가 아닌 BareMetal 인스턴스, 단일 테넌트 소유권
- Azure 호스트 애플리케이션 VM과 BareMetal 인스턴스 간의 짧은 대기 시간(0.35밀리초)
- 올 플래시 SSD 및 NVMe
    - 테넌트당 최대 1PB 
    - 테넌트당 120만의 최대 IOPS 
    - 40/100GB 네트워크 대역폭
    - NFS, ISCSI 및 FC를 통해 액세스 가능
- 이중 전원, 전원 공급 장치, NIC, TOR, 포트, WAN, 스토리지 및 관리
- 장애 시 교체를 위한 핫 스페어(재구성 필요 없음)
- 고객이 유지 관리 기간 조정
- 애플리케이션 인식 스냅샷, 보관, 미러링 및 복제

## <a name="sku-availability-in-azure-regions"></a>Azure 지역의 SKU 가용성

BareMetal Infrastructure는 특수 워크로드에 대해 인증된 여러 SKU를 제공합니다. 워크로드별 SKU를 사용하여 고객의 요구 사항을 충족합니다.

- 대형 인스턴스 – 2소켓부터 4소켓 시스템까지 제공합니다.  
- 초대형 인스턴스 – 4소켓부터 20소켓 시스템까지 제공합니다. 

특수 워크로드를 위한 BareMetal Infrastructure는 다음과 같은 Azure 지역에서 사용할 수 있습니다.
- 서유럽
- 북유럽
- 독일 중서부 *영역 지원
- 미국 동부 2 *영역 지원
- 미국 동부 *영역 지원
- 미국 서부 *영역 지원
- 미국 서부 2 *영역 지원
- 미국 중남부

>[!NOTE]
>**영역 지원** 은 모든 영역에 BareMetal 인스턴스를 배포하여 복원력과 가용성을 높일 수 있는 영역 내의 가용성 영역을 나타냅니다. 이 기능을 사용하면 다중 사이트 활성-활성 스케일링을 지원할 수 있습니다.

## <a name="managing-baremetal-instances-in-azure"></a>Azure에서 BareMetal 인스턴스 관리 

고객의 요구 사항에 따라 BareMetal Infrastructure의 애플리케이션 토폴로지가 복잡할 수 있습니다. 하나 이상의 위치에 여러 인스턴스를 배포할 수 있습니다. 인스턴스에는 공유 또는 전용 스토리지와 특수 LAN 및 WAN 연결이 있을 수 있습니다. 따라서 BareMetal 인프라의 경우 Azure는 현장의 CSA/GBB에서 상담을 제공하여 함께 작업합니다. 

BareMetal Infrastructure가 프로비저닝되면 OS, 네트워크, 스토리지 볼륨, 영역 및 지역의 배치, 위치 간의 WAN 연결이 미리 구성되어 있습니다. OS 라이선스(BYOL)를 등록하고, OS를 구성하고, 애플리케이션 레이어를 설치하면 됩니다.

Azure Portal에서 모든 BareMetal 리소스와 리소스의 상태 및 특성이 표시됩니다. 또한 인스턴스를 작동하고, 인스턴스에서 서비스 요청 및 지원 티켓을 열 수 있습니다. 

## <a name="operational-model"></a>운영 모델

BareMetal Infrastructure는 ISO 27001, ISO 27017, SOC 1 및 SOC 2와 호환됩니다. 또한 BYOL(사용자 라이선스 필요) 모델, 즉 OS, 특수 작업 및 타사 애플리케이션을 사용합니다.  

루트 액세스 및 모든 권한을 받는 즉시 다음에 대한 책임이 있다고 가정합니다.
- 백업 및 복구 솔루션 디자인 및 구현, 고가용성, 재해 복구
- OS 및 타사 소프트웨어에 대한 라이선스, 보안 및 지원

Microsoft는 다음을 담당합니다.
- 특수 워크로드를 위한 하드웨어 제공 
- OS 프로비저닝

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="BareMetal Infrastructure 지원 모델 다이어그램" border="false":::

## <a name="baremetal-instance-stamp"></a>BareMetal 인스턴스 스탬프

BareMetal 인스턴스 스탬프 자체는 다음 구성 요소를 결합합니다.

- **컴퓨팅:** 필요한 컴퓨팅 기능을 제공하고 특수 워크로드에 대해 인증된 Intel Xeon 프로세서 기반의 서버입니다.

- **네트워크:** 컴퓨팅, 스토리지, LAN 구성 요소를 상호 연결하는 통합된 고속 네트워크 패브릭입니다.

- **스토리지:** 통합 네트워크 패브릭을 통해 액세스 되는 인프라입니다.

BareMetal 스탬프의 다중 테넌트 인프라 내에서 고객이 격리된 테넌트 내에서 배포됩니다. 테넌트 배포 시 Azure 등록내에서 Azure 구독 이름을 지정합니다. 이 Azure 구독은 BareMetal 인스턴스에 대한 요금이 청구됩니다.

>[!NOTE]
>BareMetal 인스턴스를 배포하는 고객은 테넌트에 격리됩니다. 테넌트는 네트워킹, 스토리지 및 컴퓨팅 계층에서 다른 테넌트로부터 격리됩니다. 따라서 서로 다른 테넌트에 할당된 스토리지 및 컴퓨팅 단위는 BareMetal 인스턴스에서 서로 보거나 통신할 수 없습니다.

## <a name="operating-system"></a>운영 체제

BareMetal 인스턴스를 프로비전하는 동안 컴퓨터에 설치하려는 OS를 선택할 수 있습니다. 

>[!NOTE]
>BareMetal Infrastructure는 BYOL 모델을 염두에 두어야 합니다.

사용 가능한 Linux OS 버전은 다음과 같습니다.
- RHEL(Red Hat Enterprise Linux)
- SLES(SUSE Linux Enterprise Server)

## <a name="storage"></a>Storage

BareMetal Infrastructure는 매우 중복적인 NFS 스토리지 및 파이버 채널 스토리지를 제공합니다. 이 인프라는 SAP, SQL 등과 같은 엔터프라이즈 워크로드를 긴밀하게 통합합니다. 또한 모든 애플리케이션에서 일관적인 데이터 보호 및 데이터 관리 기능을 제공합니다. 셀프 서비스 관리 도구는 단일 모니터링 창과 함께 공간 효율이 높은 스냅샷, 복제 및 세분화된 복제 기능을 제공합니다. 이 인프라는 데이터 가용성 및 비즈니스 연속성 요구 사항을 충족하는 제로 RPO 및 RTO 기능을 가능하게 합니다. 

스토리지 인프라는 다음을 제공합니다.
- 최대 4 x 100GB 업링크
- 최대 32GB 파이버 채널 업링크
- 올 플래시 SSD 및 NVMe 드라이브
- 매우 낮은 대기 시간과 높은 처리량
- 최대 4PB까지 스케일 업 가능한 원시 스토리지 
- 최대 1100만 IOPS

다음과 같은 데이터 액세스 프로토콜을 지원합니다. 
- iSCSI 
- NFS(v3 또는 v4) 
- 파이버 채널 
- FC를 통한 NVMe  

## <a name="networking"></a>네트워킹

Azure 네트워크 서비스의 아키텍처는 BareMetal 인스턴스에서 특수 워크로드를 성공적으로 배포하기 위한 핵심 구성 요소입니다. 일부 IT 시스템은 아직 Azure에 위치하지 가능성이 큽니다. Azure를 온-프레미스 소프트웨어 배포에 대한 가상 데이터 센터처럼 보이도록 하는 네트워크 기술을 제공합니다. BareMetal 인스턴스에 필요한 Azure 네트워크 기능은 다음과 같습니다.

- 온-프레미스 네트워크 자산에 연결하는 Azure ExpressRoute 회로에 연결된 Azure 가상 네트워크
- 온-프레미스를 Azure에 연결하며 대역폭이 1Gbps 이상인 ExpressRoute 회로
- Azure에서 확장되었거나 완전히 Azure에서 실행되는 Active Directory 및 DNS

ExpressRoute를 사용하면 연결 공급자의 도움을 받아 프라이빗 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. **ExpressRoute 로컬** 을 사용하여 온-프레미스 위치와 원하는 Azure 지역 간에 비용 효율적으로 데이터를 전송할 수 있습니다. **ExpressRoute 프리미엄** 을 사용하여 지정학적 경계를 넘어 연결을 확장할 수 있습니다. 

BareMetal 인스턴스는 Azure VNet 서버 IP 주소 범위 내에서 프로비저닝됩니다.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Azure BareMetal Infrastructure의 아키텍처 다이어그램" lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

표시된 아키텍처는 다음과 같은 세 가지 섹션으로 구분됩니다.
- **왼쪽:** 여러 애플리케이션을 실행하는 고객 온-프레미스 인프라를 보여줍니다. 파트너 또는 로컬 에지 라우터를 통해 연결합니다. 자세한 내용은 [연결 공급자 및 위치: Azure ExpressRoute](../expressroute/expressroute-locations.md)를 참조하세요.
- **중앙:** Azure 구독을 사용하여 프로비저닝된 [ExpressRoute](../expressroute/expressroute-introduction.md)를 Azure 경계 네트워크에 연결하여 표시합니다.
- **오른쪽:** Azure IaaS를 보여주며, 여기서는 Azure 가상 네트워크 내에서 프로비저닝되는 애플리케이션을 호스트하는 VM을 사용합니다.
- **아래:** 짧은 대기 시간을 제공하는 BareMetal 연결에 대해 [ExpressRoute FastPath](../expressroute/about-fastpath.md)를 사용하도록 설정된 ExpressRoute 게이트웨이 사용을 보여줍니다.   
   >[!TIP]
   >이를 지원하기 위해 ExpressRoute 게이트웨이를 UltraPerformance여야 합니다. 자세한 내용은 [ExpressRoute 가상 네트워크 게이트웨이 정보](../expressroute/expressroute-about-virtual-network-gateways.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Portal을 통해 BareMetal 인스턴스를 식별하고 상호 작용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Portal을 통해 BareMetal 인스턴스 관리](connect-baremetal-infrastructure.md)
