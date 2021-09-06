---
title: 개념 - 허브 및 스포크 아키텍처에서 Azure VMware Solution 배포 통합
description: Azure의 허브 및 스포크 아키텍처에서 Azure VMware Solution 배포를 통합하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: 2ed815904b8bb15b9822fbc9603b65e20ccdce43
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529643"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>허브 및 스포크 아키텍처에서 Azure VMware Solution 통합

이 문서에서는 Azure에서 기존 또는 새 [허브 및 스포크 아키텍처](/azure/architecture/reference-architectures/hybrid-networking/#hub-spoke-network-topology)에 Azure VMware Solution 배포를 통합하기 위한 권장 사항을 제공합니다. 


허브 및 스포크 시나리오에서는 다음에 대한 워크로드가 있는 하이브리드 클라우드 환경을 가정합니다.

* IaaS 또는 PaaS 서비스를 사용하는 네이티브 Azure
* Azure VMware 솔루션 
* 온-프레미스의 vSphere

## <a name="architecture"></a>Architecture

*허브* 는 온-프레미스 및 Azure VMware Solution 프라이빗 클라우드에 대한 중앙 연결 지점으로 작동하는 Azure Virtual Network입니다. *스포크* 는 가상 네트워크 간 통신을 허용하기 위해 허브와 피어링되는 가상 네트워크입니다.

온-프레미스 데이터 센터, Azure VMware Solution 프라이빗 클라우드 및 허브 간의 트래픽은 Azure ExpressRoute 연결을 통과합니다. 스포크 가상 네트워크는 일반적으로 IaaS 기반 워크로드를 포함하지만 Virtual Network와 직접 통합된 [App Service Environment](../app-service/environment/intro.md)와 같은 PssS 서비스 또는 [Azure Private Link](../private-link/index.yml)가 사용하도록 설정된 다른 PssS 서비스를 포함할 수 있습니다.

>[!IMPORTANT]
>가상 네트워크당 4개의 ExpressRoute 회로 제한을 초과하지 않는 한 기존 ExpressRoute 게이트웨이를 사용하여 Azure VMware Solution에 연결할 수 있습니다.  그러나 ExpressRoute를 통해 온-프레미스에서 Azure VMware Solution에 액세스하려면 ExpressRoute 게이트웨이가 연결된 회로 간에 전이적 라우팅을 제공하지 않으므로 ExpressRoute Global Reach가 있어야 합니다.

이 다이어그램은 ExpressRoute Global Reach를 통해 온-프레미스 및 Azure VMware Solution에 연결된 Azure의 허브 및 스포크 배포 예제를 보여 줍니다.

:::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png" alt-text="Azure VMware Solution Hub 및 Spoke 통합 배포를 보여주는 다이어그램." border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-and-spoke-deployment.png":::

이 아키텍처의 주요 구성 요소는 다음과 같습니다.

- **온-프레미스 사이트:** ExpressRoute 연결을 통해 Azure에 연결된 고객 온-프레미스 데이터 센터

- **Azure VMware Solution 프라이빗 클라우드:** 각각이 최대 16개의 호스트를 포함하는 하나 이상의 vSphere 클러스터로 구성된 Azure VMware Solution SDDC

- **ExpressRoute 게이트웨이:** Azure VMware Solution 프라이빗 클라우드, 허브 가상 네트워크의 공유 서비스, 스포크 가상 네트워크에서 실행되는 워크로드 간에 통신을 허용합니다.

- **ExpressRoute Global Reach:** 온-프레미스 및 Azure VMware Solution 프라이빗 클라우드 간의 연결을 허용합니다. Azure VMware Solution과 Azure 패브릭 간의 연결은 ExpressRoute Global Reach를 통해서만 진행됩니다. ExpressRoute 빠른 경로 이외의 옵션은 선택할 수 없습니다.  ExpressRoute Direct는 지원되지 않습니다.


- **S2S VPN 고려 사항:** Azure VMware Solution 프로덕션 배포의 경우 VMware HCX에 대한 네트워크 요구 사항으로 인해 Azure S2S VPN은 지원되지 않습니다. 그러나 PoC 배포에 사용할 수 있습니다.


- **허브 가상 네트워크:** 온-프레미스 네트워크 및 Azure VMware Solution 프라이빗 클라우드에 대한 중앙 연결 지점으로 작동합니다.

- **스포크 가상 네트워크**

    - **IaaS Spoke:** VM 가용성 집합, 가상 머신 확장 집합 및 해당 네트워크 구성 요소를 비롯한 Azure IaaS 기반 워크로드를 호스트합니다.

    - **PaaS Spok:** [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md) 및 [Private Link](../private-link/private-link-overview.md) 덕분에 프라이빗 주소 지정을 사용하여 Azure PaaS 서비스를 호스트합니다.

- **Azure Firewall:** 스포크와 Azure VMware Solution 간에 트래픽을 분할하는 중앙 부분으로 작동합니다.

- **Application Gateway:** Azure IaaS/PaaS 또는 Azure VMware Solution VM(가상 머신)에서 실행되는 웹앱을 노출하고 보호합니다. API Management 등의 다른 서비스와 통합됩니다.

## <a name="network-and-security-considerations"></a>네트워크 및 보안 고려 사항

ExpressRoute 연결을 사용하면 온-프레미스, Azure VMware Solution 및 Azure 네트워크 패브릭 간에 트래픽이 이동할 수 있습니다. Azure VMware Solution은 [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md)를 사용하여 이 연결을 구현합니다.

ExpressRoute 게이트웨이는 연결된 회로 간에 전이적 라우팅을 제공하지 않으므로 온-프레미스 연결에서는 온-프레미스 vSphere 환경과 Azure VMware Solution 간 통신을 위해 ExpressRoute Global Reach도 사용해야 합니다. 

* **온-프레미스에서 Azure VMware Solution으로의 트래픽 흐름**

  :::image type="content" source="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png" alt-text="온-프레미스에서 Azure VMware Solution으로의 트래픽 흐름을 보여주는 다이어그램." border="false" lightbox="./media/hub-spoke/on-premises-azure-vmware-solution-traffic-flow.png":::


* **Azure VMware Solution에서 허브 VNET으로의 트래픽 흐름**

  :::image type="content" source="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png" alt-text="Azure VMware Solution에서 허브 가상 네트워크로의 트래픽 흐름을 보여주는 다이어그램." border="false" lightbox="./media/hub-spoke/azure-vmware-solution-hub-vnet-traffic-flow.png":::


Azure VMware Solution 네트워킹 및 연결 개념에 대한 자세한 내용은 [Azure VMware Solution 제품 설명서](./concepts-networking.md)를 참조하세요.

### <a name="traffic-segmentation"></a>트래픽 구분

[Azure Firewall](../firewall/index.yml)은 허브 가상 네트워크에 배포되는 허브 및 스포크 토폴로지의 중앙 부분입니다. Azure Firewall 또는 다른 Azure 지원 NVA(네트워크 가상 어플라이언스)를 사용하여 트래픽 규칙을 설정하고 여러 스포크 및 Azure VMware Solution 워크로드 간에 통신을 구분합니다.

경로 테이블을 만들어 트래픽을 Azure Firewall로 보냅니다.  스포크 가상 네트워크의 경우 기본 경로를 Azure Firewall의 내부 인터페이스로 설정하는 경로를 만듭니다. 이러한 방식으로 Virtual Network의 워크로드가 Azure VMware Solution 주소 공간에 도달해야 하는 경우 방화벽은 이를 평가하고 해당 트래픽 규칙을 적용하여 허용하거나 거부할 수 있습니다.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Azure Firewall로 트래픽을 보내기 위한 경로 테이블을 보여주는 스크린샷." lightbox="media/hub-spoke/create-route-table-to-direct-traffic.png":::


> [!IMPORTANT]
> **GatewaySubnet** 설정에서 주소 접두사가 0.0.0.0/0인 경로는 지원되지 않습니다.

해당 경로 테이블에 특정 네트워크의 경로를 설정합니다. 스포크 워크로드에서 Azure VMware Solution 관리 및 워크로드 IP 접두사에 도달하는 경로 및 반대 경로를 예로 들 수 있습니다.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="해당 경로 테이블의 특정 네트워크에 대해 설정된 경로를 보여주는 스크린샷." lightbox="media/hub-spoke/specify-gateway-subnet-for-route-table.png":::

스포크 및 허브 내의 네트워크 보안 그룹을 사용하여 두 번째 수준의 트래픽 구분을 통해 보다 세분화된 트래픽 정책을 만들 수 있습니다.

> [!NOTE]
> **온-프레미스에서 Azure VMware Solution으로의 트래픽:** 온-프레미스 워크로드 간의 트래픽(vSphere 기반 또는 기타)은 Global Reach를 통해 지원되지만 트래픽이 허브의 Azure Firewall을 통과하지 않습니다. 이 시나리오에서는 온-프레미스 또는 Azure VMware Solution에서 트래픽 구분 메커니즘을 구현해야 합니다.

### <a name="application-gateway"></a>Application Gateway

Azure Application Gateway V1 및 V2는 백 엔드 풀로 Azure VMware Solution VM에서 실행되는 웹앱을 사용하여 테스트했습니다. Application Gateway는 현재 Azure VMware Solution VM에서 실행되는 웹앱을 인터넷에 노출하는 데 지원되는 유일한 방법입니다. 또한 앱을 내부 사용자에게 안전하게 노출할 수 있습니다.

자세한 내용은 [Application Gateway](./protect-azure-vmware-solution-with-application-gateway.md)에 대한 Azure VMware Solution 관련 문서를 참조하세요.

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="네트워크 보안 그룹을 사용하는 트래픽 구분의 두 번째 수준을 보여주는 다이어그램." border="false":::


### <a name="jump-box-and-azure-bastion"></a>점프 상자 및 Azure Bastion

허브 가상 네트워크 내의 공유 서비스 서브넷에 배포된 Windows 10 또는 Windows Server VM에 해당하는 점프 상자를 사용하여 Azure VMware Solution 환경에 액세스합니다.

>[!IMPORTANT]
>Azure Bastion은 인터넷에 Azure VMware Solution을 노출하지 않도록 점프 상자에 연결하도록 권장되는 서비스입니다. Azure IaaS 개체가 아니므로 Azure Bastion을 사용하여 Azure VMware Solution VM에 연결할 수 없습니다.  

보안 모범 사례로 허브 가상 네트워크 내에 [Microsoft Azure Bastion](../bastion/index.yml) 서비스를 배포합니다. Azure Bastion을 사용하면 공용 IP 주소를 해당 리소스에 제공하지 않고도 Azure에 배포된 VM에 RDP 및 SSH를 통해 원활하게 액세스할 수 있습니다. Azure Bastion 서비스를 프로비저닝하면 Azure Portal에서 선택한 VM에 액세스할 수 있습니다. 연결을 설정하면 점프 상자 데스크톱을 표시하는 새 탭이 열리고, 해당 데스크톱에서 Azure VMware Solution 프라이빗 클라우드 관리 평면에 액세스할 수 있습니다.

> [!IMPORTANT]
> 점프 박스 VM에 공용 IP 주소를 제공하거나 퍼블릭 인터넷에 3389/TCP 포트를 노출하지 마세요. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Azure Bastion Hub 가상 네트워크를 보여주는 다이어그램." border="false":::


## <a name="azure-dns-resolution-considerations"></a>Azure DNS 확인 고려 사항

Azure DNS 확인을 위해 다음 두 가지 옵션을 사용할 수 있습니다.

-   허브에 배포된 도메인 컨트롤러([ID 고려 사항](#identity-considerations)에 설명됨)를 이름 서버로 사용합니다.

-   Azure DNS 프라이빗 영역을 배포 및 구성합니다.

가장 좋은 방법은 Azure VMware Solution, 온-프레미스 및 Azure에 대해 신뢰할 수 있는 이름 확인을 제공하기 위해 두 가지를 결합하는 것입니다.

일반적인 디자인 권장 사항으로 허브 가상 네트워크의 두 개 이상의 Azure VM에 배포되고 Spoke 가상 네트워크에서 구성된 기존 Active Directory 통합 DNS를 사용하여 DNS 설정에서 해당 Azure DNS 서버를 사용합니다.

Azure 프라이빗 DNS를 사용할 수 있습니다. 여기서 Azure 프라이빗 DNS 영역은 가상 네트워크에 연결됩니다.  DNS 서버는 온-프레미스에 대한 조건부 전달 또는 고객 Azure 프라이빗 DNS 인프라를 사용하여 DNS를 실행하는 Azure VMware Solution이 있는 하이브리드 확인자로 사용됩니다. 

스포크 가상 네트워크 내에 배포된 VM에 대한 DNS 레코드의 수명 주기를 자동으로 관리하려면 자동 등록을 사용하도록 설정합니다. 사용하도록 설정한 경우 최대 프라이빗 DNS 영역 수는 1개 뿐입니다. 사용하지 않도록 설정한 경우 최대 수는 1000입니다.

Azure 프라이빗 DNS 영역의 Azure에서 확인자 VM에 대한 조건부 전달자를 사용하여 온-프레미스 및 Azure VMware Solution 서버를 구성할 수 있습니다.

## <a name="identity-considerations"></a>ID 고려 사항

ID에 가장 좋은 방법은 허브에 하나 이상의 도메인 컨트롤러를 배포하는 것입니다. 영역 분산 방식 또는 VM 가용성 집합에서 두 개의 공유 서비스 서브넷을 사용합니다. 온-프레미스 AD(Active Directory) 도메인을 Azure로 확장하는 방법에 대한 자세한 내용은 [Azure 아키텍처 센터](/azure/architecture/reference-architectures/identity/adds-extend-domain)를 참조하세요.

또한 Azure VMware Solution 쪽에 vSphere 환경 내에서 ID 및 DNS 원본으로 작동할 다른 도메인 컨트롤러를 배포합니다.

권장되는 모범 사례로 [AD 도메인을 Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad)와 통합합니다.

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
