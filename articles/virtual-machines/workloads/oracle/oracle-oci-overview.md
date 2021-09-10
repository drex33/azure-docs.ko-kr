---
title: Microsoft Azure와 Oracle 클라우드 인프라 통합 | Microsoft Docs
description: Microsoft Azure에서 실행되는 Oracle 앱을 OCI(Oracle 클라우드 인프라)의 데이터베이스와 통합하는 솔루션에 대해 알아봅니다.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 06/01/2020
ms.author: kegorman
ms.openlocfilehash: 1dfeaf71d846dfe33f42df3e9931293a76f62209
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698629"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Microsoft Azure와 Oracle 클라우드 인프라를 통합하는 Oracle 애플리케이션 솔루션

**적용 대상:** :heavy_check_mark: Linux VM 

Microsoft와 Oracle은 협력을 통해 대기 시간은 낮으면서 처리량은 높은 클라우드 간 연결을 제공하므로 두 클라우드의 장점을 활용할 수 있습니다. 

이러한 클라우드 간 연결을 통해 다중 계층 애플리케이션을 분할하여 OCI(Oracle 클라우드 인프라)에서 데이터베이스 계층을, Microsoft Azure에서 애플리케이션과 다른 계층을 실행할 수 있습니다. 이 환경은 단일 클라우드에서 전체 솔루션 스택을 실행하는 것과 비슷합니다. 

Azure 인프라에서 WebLogic Server를 포함한 미들웨어를 실행하는 데 관심이 있지만 OCI 내에서 Oracle 데이터베이스를 실행하는 경우 [WebLogic Server Azure 애플리케이션](oracle-weblogic.md)을 참조하세요.

Oracle 솔루션을 완전히 Azure 인프라에 배포하는 데 관심이 있다면 [Microsoft Azure의 Oracle VM 이미지 및 배포](oracle-vm-solutions.md)를 참조하세요.

## <a name="scenario-overview"></a>시나리오 개요

클라우드 간 연결은 OCI에서 호스팅되는 데이터베이스 서비스의 이점을 누리는 동시에 Azure Virtual Machines에서 Oracle의 업계 최고 애플리케이션 및 사용자 지정 애플리케이션을 실행할 수 있는 솔루션을 제공합니다. 

2020년 5월부로 다음 애플리케이션은 클라우드 간 구성에서 인증됩니다.

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle Retail 애플리케이션
* Oracle Hyperion Financial Management

다음 다이어그램은 연결된 솔루션의 상위 수준 개요를 제공합니다. 여기서는 간략한 설명을 위해 애플리케이션 계층과 데이터 계층만 보여 줍니다. 애플리케이션 아키텍처에 따라 솔루션에 WebLogic Server 클러스터 또는 Azure의 웹 계층과 같은 추가 계층이 포함될 수 있습니다. 자세한 내용은 다음 섹션을 참조하세요.

![Azure OCI 솔루션 개요](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>지역 가용성 

클라우드 간 연결은 다음 지역으로 제한됩니다.
* Azure 미국 동부(EastUS) 및 OCI 애슈번, VA(미국 동부)
* Azure 영국 남부(UKSouth) 및 OCI 런던(영국 남부)
* Azure 캐나다 중부(CanadaCentral) 및 OCI 토론토(캐나다 남동쪽)
* Azure 서유럽(WestEurope) 및 OCI 암스테르담(네덜란드 북서부)
* Azure 일본 동부(JapanEast) 및 OCI 도쿄(일본 동부)
* Azure 미국 서부(WestUS) 및 OCI 산호세(미국 서부)
* 독일 중서부(프랑크푸르트) 및 OCI 독일 중부(프랑크푸르트)


## <a name="networking"></a>네트워킹

Enterprise 고객은 다양한 비즈니스 및 운영상의 이유로 여러 클라우드에 워크로드를 다각화하여 배포하는 경우가 종종 있습니다. 다각화를 위해 고객은 인터넷, IPSec VPN 또는 온-프레미스 네트워크를 경유하는 클라우드 공급자의 직접 연결 솔루션을 통해 클라우드 네트워크를 상호 연결합니다. 클라우드 네트워크를 상호 연결하려면 시간, 비용, 설계, 조달, 설치, 테스트 및 운영에 상당한 투자가 필요할 수 있습니다. 

이러한 고객 문제를 해결하기 위해 Oracle과 Microsoft는 통합 다중 클라우드 환경을 사용합니다. 클라우드 간 네트워킹은 Microsoft Azure에서 [ExpressRoute](../../../expressroute/expressroute-introduction.md) 서킷을 OCI의 [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) 서킷과 연결하여 설정합니다. 이 연결은 Azure ExpressRoute 피어링 위치가 OCI FastConnect와 근접하거나, 동일한 피어링 위치에 있는 경우에 가능합니다. 이 설정을 통해 중간 서비스 공급자 없이도 두 클라우드 간에 안전하고 빠른 연결이 가능합니다.

ExpressRoute 및 FastConnect를 사용하여 개인 IP 주소 공간이 겹치지 않는다면 Azure의 가상 네트워크를 OCI의 가상 클라우드 네트워크와 피어링할 수 있습니다. 두 네트워크를 피어링하면 두 리소스가 동일한 네트워크에 있는 것처럼 가상 네트워크의 리소스가 OCI 가상 네트워크의 리소스에 통신할 수 있습니다.

## <a name="network-security"></a>네트워크 보안

네트워크 보안은 모든 엔터프라이즈 애플리케이션의 중요한 구성 요소로, 이 다중 클라우드 솔루션의 핵심입니다. ExpressRoute 및 FastConnect를 거치는 트래픽은 개인 네트워크를 통해 전달됩니다. 이 구성을 통해 Azure 가상 네트워크와 Oracle 가상 클라우드 네트워크 간의 안전한 통신이 가능합니다. Azure의 가상 머신에 공용 IP 주소를 제공하지 않아도 됩니다. 마찬가지로, OCI에는 인터넷 게이트웨이가 필요하지 않습니다. 모든 통신은 머신의 개인 IP 주소를 통해 일어납니다.

또한 OCI 가상 클라우드 네트워크 및 보안 규칙(Azure [네트워크 보안 그룹](../../../virtual-network/network-security-groups-overview.md)에 연결됨)에서 [보안 목록](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm)을 설정할 수 있습니다. 이러한 규칙을 사용하여 가상 네트워크에서 머신 간의 트래픽 흐름을 제어할 수 있습니다. 네트워크 보안 규칙은 가상 네트워크 수준뿐만 아니라 머신 및 서브넷 수준에서 추가할 수 있습니다.

[WebLogic Server Azure 애플리케이션](oracle-weblogic.md)은 각각 WebLogic Server의 포트 구성에 작동하도록 미리 구성된 네트워크 보안 그룹을 만듭니다.
 
## <a name="identity"></a>ID

ID는 Microsoft와 Oracle 간 파트너 관계의 핵심 요소 중 하나입니다. [Oracle IDCS(Identity Cloud Service)](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html)를 [Azure AD(Azure Active Directory)](../../../active-directory/index.yml)에 통합하기 위해 상당한 작업이 수행되었습니다. Azure AD는 Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스입니다. 사용자는 Azure AD를 통해 다양한 리소스에 로그인하고 액세스할 수 있습니다. Azure AD를 사용하여 사용자와 해당 권한을 관리할 수도 있습니다.

현재 이 통합을 통해 하나의 중앙 위치, 즉, Azure Active Directory에서 관리할 수 있습니다. Azure AD는 디렉터리의 모든 변경 내용을 해당하는 Oracle 디렉터리와 동기화하며, 클라우드 간 Oracle 솔루션의 Single Sign-On에 사용됩니다.

## <a name="next-steps"></a>다음 단계

Azure와 OCI 사이의 [클라우드 간 네트워크](configure-azure-oci-networking.md)를 시작해 보세요. 

OCI에 대한 자세한 내용 및 백서는 [Oracle 클라우드](https://docs.cloud.oracle.com/iaas/Content/home.htm) 설명서를 참조하세요.
