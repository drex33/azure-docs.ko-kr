---
title: Azure ExpressRoute와 Oracle Cloud Infrastructure 연결 | Microsoft Docs
description: Azure ExpressRoute와 OCI(Oracle Cloud Infrastructure) FastConnect를 연결하여 클라우드 간 Oracle 애플리케이션 솔루션을 사용하도록 설정합니다.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 03/16/2020
ms.author: rogardle
ms.openlocfilehash: 9c785be73e424d4669b24600c353a14127e9279a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696026"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>Azure 및 Oracle Cloud Infrastructure 간 직접 상호 연결 설정  

**적용 대상:** :heavy_check_mark: Linux VM 

[통합된 다중 클라우드 환경](oracle-oci-overview.md)을 만들기 위해 Microsoft와 Oracle은 [ExpressRoute](../../../expressroute/expressroute-introduction.md) 및 [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm)를 통해 Azure 및 OCI(Oracle Cloud Infrastructure) 간에 직접 상호 연결을 제공합니다. ExpressRoute 및 FastConnect 상호 연결을 통해 고객은 두 클라우드 간에 짧은 대기 시간, 높은 처리량, 프라이빗 직접 연결을 갖춘 환경을 경험할 수 있습니다.

> [!IMPORTANT]
> 2020년 5월까지 Oracle은 Azure/Oracle Cloud 상호 연결 솔루션을 사용하여 이러한 애플리케이션을 Azure에서 실행하도록 인증할 것입니다.
> * E-Business Suite
> * JD Edwards EnterpriseOne
> * PeopleSoft
> * Oracle Retail 애플리케이션
> * Oracle Hyperion Financial Management

다음 이미지는 상호 연결에 대한 간략한 개요를 보여줍니다.

![클라우드 간 네트워크 연결](https://user-images.githubusercontent.com/37556655/115093592-bced0180-9ecf-11eb-976d-9d4c7a1be2a8.png)

## <a name="prerequisites"></a>사전 요구 사항

* Azure와 OCI 간에 연결을 설정하려면 활성 Azure 구독 및 활성 OCI 테넌시가 있어야 합니다.

* 연결은 Azure ExpressRoute 피어링 위치가 OCI FastConnect 피어링 위치와 근접하거나 동일한 경우에만 가능합니다. [지역 가용성](oracle-oci-overview.md#region-availability)을 참조하세요.

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>ExpressRoute 및 FastConnect 간 직접 연결 구성

1. 리소스 그룹에서 Azure 구독에 표준 ExpressRoute 회로를 만듭니다. 
    * ExpressRoute를 만드는 동안 서비스 공급자로 **Oracle Cloud FastConnect** 를 선택합니다. ExpressRoute 회로를 만들려면 [단계별 가이드](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)를 참조하세요.
    * Azure ExpressRoute 회로는 세분화된 대역폭 옵션을 제공하지만 FastConnect는 1, 2, 5, 10Gbps를 지원합니다. 따라서 ExpressRoute에서 일치하는 대역폭 옵션 중 하나를 선택하는 것이 좋습니다.

    ![ExpressRoute 회로 만들기](media/configure-azure-oci-networking/exr-create-new.png)
1. ExpressRoute **서비스 키** 를 적어둡니다. FastConnect 회로를 구성할 때 이 키를 제공해야 합니다.

    ![ExpressRoute 서비스 키](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > ExpressRoute 회로가 프로비저닝되는 즉시 ExpressRoute 요금이 청구됩니다(**공급자 상태** 가 **프로비저닝되지 않음** 인 경우에도 청구됨).

1. Azure 가상 네트워크 또는 OCI 가상 클라우드 네트워크 IP 주소 공간과 겹치지 않으며 각각 /30인 개인 IP 주소 공간 두 개를 만듭니다. 첫 번째 IP 주소 공간은 기본 주소 공간이라고 하고 두 번째 IP 주소 공간은 보조 주소 공간이라고 하겠습니다. 주소를 적어둡니다. 이 주소는 FastConnect 회로를 구성할 때 필요합니다.
1. DRG(동적 라우팅 게이트웨이)를 만듭니다. FastConnect 회로를 만들 때 필요합니다. 자세한 내용은 [동적 라우팅 게이트웨이](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm) 설명서를 참조하세요.
1. Oracle 테넌트에서 FastConnect 회로를 만듭니다. 자세한 내용은 [Oracle 설명서](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)를 참조하세요.
  
    * FastConnect 구성에서 공급자로 **Microsoft Azure: ExpressRoute** 를 선택합니다.
    * 이전 단계에서 프로비저닝한 동적 라우팅 게이트웨이를 선택합니다.
    * 프로비저닝할 대역폭을 선택합니다. 성능을 최적화하려면 대역폭이 ExpressRoute 회로를 만들 때 선택한 대역폭과 일치해야 합니다.
    * **공급자 서비스 키** 에 ExpressRoute 서비스 키를 붙여넣습니다.
    * **기본 BGP IP 주소** 에는 이전 단계에서 만든 첫 번째 /30 개인 IP 주소 공간을 사용하고 **보조 BGP IP** 주소에는 두 번째 /30 개인 IP 주소 공간을 사용합니다.
        * Oracle BGP IP 주소(기본 및 보조)에는 두 범위 중 첫 번째 사용 가능한 주소를 할당하고 고객 BGP IP 주소(FastConnect 관점)에는 두 번째 주소를 할당합니다. 첫 번째 사용 가능한 IP 주소는 /30 주소 공간의 두 번째 IP 주소입니다(첫 번째 IP 주소는 Microsoft에서 예약됨).
    * **만들기** 를 클릭합니다.
1. 경로 테이블을 사용하는 동적 라우팅 게이트웨이를 통해 Oracle 테넌트에서 가상 클라우드 네트워크에 대한 FastConnect 연결을 완료합니다.
1. Azure로 이동하여 ExpressRoute 회로의 **공급자 상태** 가 **프로비저닝됨** 으로 변경되었고 **Azure 개인** 유형의 피어링이 프로비저닝되었는지 확인합니다. 이 작업은 다음 단계에 필요한 필수 조건입니다.

    ![ExpressRoute 공급자 상태](media/configure-azure-oci-networking/exr-provider-status.png)
1. **Azure 개인** 피어링을 클릭합니다. FastConnect 회로를 설정할 때 입력한 정보를 기반으로 피어링 세부 정보가 자동으로 구성된 것을 볼 수 있습니다.

    ![개인 피어링 설정](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>ExpressRoute에 가상 네트워크 연결

1. 가상 네트워크 및 가상 네트워크 게이트웨이가 아직 없는 경우 만듭니다. 자세한 내용은 [단계별 가이드](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)를 참조하세요.
1. [Terraform 스크립트](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2)를 실행하거나 PowerShell 명령을 실행하여 [ExpressRoute FastPath를 구성](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)하는 방법으로 가상 네트워크 게이트웨이 및 ExpressRoute 회로 간에 연결을 설정합니다.

네트워크 구성을 완료하면 Azure Portal의 ExpressRoute 개인 피어링 블레이드에서 **ARP 레코드 가져오기** 및 **경로 테이블 가져오기** 를 클릭하여 구성의 유효성을 확인할 수 있습니다.

## <a name="automation"></a>Automation

Microsoft는 Terraform 스크립트를 만들어 네트워크 상호 연결 자동 배포를 사용할 수 있도록 했습니다. Terraform 스크립트를 실행하려면 먼저 Azure에서 인증해야 합니다. Azure 구독에 대한 적절한 권한이 필요하기 때문입니다. 인증은 [Azure Active Directory 서비스 주체](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 사용하거나 Azure CLI를 사용하여 수행할 수 있습니다. 자세한 내용은 [Terraform 설명서](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)를 참조하세요.

내부 연결을 배포하기 위한 Terraform 스크립트 및 관련 설명서는 이 [GitHub 리포지토리](https://aka.ms/azureociinterconnecttf)에서 찾을 수 있습니다.

## <a name="monitoring"></a>모니터링

클라우드 둘 다에 에이전트를 설치하면 Azure [NPM(네트워크 성능 모니터)](../../../expressroute/how-to-npm.md)을 활용하여 엔드투엔드 네트워크 성능을 모니터링할 수 있습니다. NPM은 네트워크 문제를 쉽게 식별하고 제거하는 데 도움이 됩니다.

## <a name="delete-the-interconnect-link"></a>상호 연결 링크 삭제

상호 연결을 삭제하려면 다음 단계를 지정된 순서대로 수행해야 합니다. 이렇게 하지 않으면 ExpressRoute 회로가 “실패 상태”가 됩니다.

1. ExpressRoute 연결을 삭제합니다. 연결 페이지에서 **삭제** 아이콘을 클릭하여 연결을 삭제합니다. 자세한 내용은 [ExpressRoute 설명서](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#clean-up-resources)를 참조하세요.
1. Oracle Cloud Console에서 Oracle FastConnect를 삭제합니다.
1. Oracle FastConnect 회로를 삭제하면 Azure ExpressRoute 회로를 삭제할 수 있습니다.

이 시점에 삭제 및 프로비전 해제 프로세스가 완료됩니다.

## <a name="next-steps"></a>다음 단계

* OCI와 Azure 사이의 클라우드 간 연결에 대한 자세한 내용은 [Oracle 설명서](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)를 참조하세요.
* [Terraform 스크립트](https://aka.ms/azureociinterconnecttf)를 사용하여 Azure의 대상 Oracle 애플리케이션에 대한 인프라를 배포하고 네트워크 상호 연결을 구성합니다. 
