---
title: Azure 프라이빗 DNS란?
description: 이 문서에서는 Microsoft Azure의 프라이빗 DNS 호스팅 서비스에 대한 개요를 시작합니다.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 819e3f911a769f870b792ed92b699f3356139565
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457700"
---
# <a name="what-is-azure-private-dns"></a>Azure 프라이빗 DNS란?

DNS(Domain Name System)는 서비스 이름을 해당 IP 주소로 변환(또는 확인)합니다.  Azure DNS는 도메인에 대한 호스팅 서비스이며 Microsoft Azure 인프라를 사용하여 이름을 확인합니다. Azure DNS는 인터넷 연결 DNS 도메인을 지원할 뿐만 아니라 프라이빗 DNS 영역도 지원합니다.

Azure Private DNS는 가상 네트워크에 안정적이고 안전한 DNS 서비스를 제공합니다. Azure Private DNS는 사용자 지정 DNS 솔루션을 구성할 필요 없이 가상 네트워크의 도메인 이름을 관리하고 확인합니다. 프라이빗 DNS 영역을 사용하면 배포 중에 Azure에서 제공한 이름 대신 고유한 사용자 지정 도메인 이름을 사용할 수 있습니다. 사용자 지정 도메인 이름을 사용하면 조직의 요구 사항에 가장 적합하도록 가상 네트워크 아키텍처를 조정할 수 있습니다. 가상 네트워크와 연결된 가상 네트워크 내에서 VM(가상 머신)에 대한 이름을 확인할 수 있게 해줍니다. 또한 분할-수평 보기를 사용해 영역을 구성할 수도 있습니다. 그러면 프라이빗 DNS 영역과 공용 DNS 영역이 이름을 공유할 수 있습니다.

가상 네트워크에서 프라이빗 DNS 영역의 레코드를 확인하려면 가상 네트워크를 해당 영역과 연결해야 합니다. 연결된 가상 네트워크는 전체 액세스 권한을 가지며 프라이빗 영역에 게시된 모든 DNS 레코드를 확인할 수 있습니다. 가상 네트워크 연결에 대한 자동 등록을 사용하도록 설정할 수 있습니다. 가상 네트워크 연결에 대한 자동 등록을 사용하도록 설정하면 해당 가상 네트워크의 가상 머신에 대한 DNS 레코드가 프라이빗 영역에 등록됩니다. 자동 등록을 사용할 수 있게 되면 Azure DNS는 가상 머신이 생성되거나, 해당 IP 주소를 변경하거나, 삭제될 때마다 영역 레코드를 업데이트합니다.

![DNS 개요](./media/private-dns-overview/scenario.png)

> [!NOTE]
> 프라이빗 DNS 영역에 *.local* 도메인을 사용하지 않는 것이 가장 좋습니다. 일부 운영 체제는 이를 지원하지 않습니다.

## <a name="benefits"></a>이점

Azure 프라이빗 DNS는 다음과 같은 이점을 누릴 수 있습니다.

* **사용자 지정 DNS 솔루션이 필요 없음**. 이전에는 많은 고객이 가상 네트워크에서 DNS 영역을 관리하기 위해 사용자 지정 DNS 솔루션을 만들었습니다. 이제 기본 Azure 인프라를 사용하여 DNS 영역을 관리할 수 있으므로 사용자 지정 DNS 솔루션을 만들고 관리해야 하는 부담이 사라졌습니다.

* **모든 공용 DNS 레코드 형식 사용**. Azure DNS는 A, AAAA, CNAME, MX, PTR, SOA, SRV 및 TXT 레코드를 지원합니다.

* **자동 호스트 레코드 관리**. Azure는 사용자 지정 DNS 레코드를 호스팅할 뿐 아니라 VM의 호스트 이름 레코드를 지정된 가상 네트워크에 자동으로 보존합니다. 이 시나리오에서는 사용자 지정 DNS 솔루션을 만들거나 애플리케이션을 수정할 필요 없이 사용하는 도메인 이름을 최적화할 수 있습니다.

* **가상 네트워크 간의 호스트 이름 확인**. Azure에서 제공하는 호스트 이름과는 달리, 프라이빗 DNS 영역은 가상 네트워크 간에 공유할 수 있습니다. 이 기능은 가상 네트워크 피어링 같은 교차 네트워크 및 서비스 검색 시나리오를 단순화합니다.

* **친숙한 도구 및 사용자 환경**. 이 서비스는 학습 곡선을 단축할 수 있도록 잘 설정된 Azure DNS 도구(Azure Portal, Azure PowerShell, Azure CLI, Azure Resource Manager 템플릿, REST API)를 사용합니다.

* **수평 분할 DNS 지원**. Azure DNS를 사용하면 이름이 같고 가상 네트워크 내 그리고 공용 인터넷의 다른 답변을 확인하는 영역을 만들 수 있습니다. 분할 수평 DNS에 대한 일반적인 시나리오는 가상 네트워크 내에서 사용할 수 있는 전용 서비스 버전을 제공하는 것입니다.

* **모든 Azure 지역에서 사용 가능**. Azure DNS 프라이빗 영역 기능은 Azure 퍼블릭 클라우드의 모든 Azure 지역에서 사용할 수 있습니다.

## <a name="capabilities"></a>기능

Azure 프라이빗 DNS는 다음과 같은 기능을 제공합니다.

* **자동 등록이 사용 설정된 프라이빗 영역에 연결된 가상 네트워크에서 가상 머신 자동 등록**. 가상 머신이 개인 IP 주소를 가리키는 A 레코드로 프라이빗 영역에 등록됩니다. 자동 등록이 사용 설정된 가상 네트워크 연결에서 가상 머신이 삭제되면 Azure DNS는 연결된 프라이빗 영역에서 해당 DNS 레코드도 자동으로 제거합니다.

* **프라이빗 영역에 연결된 가상 네트워크 전체에서 정방향 DNS 확인이 지원됩니다**. 가상 네트워크 간 DNS 확인의 경우 가상 네트워크가 서로 연결되는 명시적 종속성은 없습니다. 그러나 다른 시나리오(예: HTTP 트래픽)를 위해 가상 네트워크를 피어링할 수도 있습니다.

* **DNS 역방향 조회는 가상 네트워크 범위 내에서 지원됩니다**. 프라이빗 영역에 연결된 개인 IP에 대한 역방향 DNS 조회에서는 호스트/레코드 이름과 영역 이름이 접미사로 포함된 FQDN이 반환됩니다.

## <a name="other-considerations"></a>기타 고려 사항

Azure 프라이빗 DNS의 제한 사항은 다음과 같습니다.

* VM DNS 레코드의 자동 등록이 활성화된 경우 특정 가상 네트워크를 하나의 프라이빗 영역에만 연결할 수 있습니다. 그러나 단일 DNS 영역에 여러 가상 네트워크를 연결할 수 있습니다.
* 역방향 DNS는 연결된 가상 네트워크의 개인 IP 공간에 대해서만 작동합니다.
* 연결된 가상 네트워크의 개인 IP 주소에 대한 역방향 DNS는 가상 머신의 기본 접미사로 `internal.cloudapp.net`을 반환합니다. 자동 등록이 활성화된 프라이빗 영역에 연결된 가상 네트워크의 경우 개인 IP 주소의 역방향 DNS는 기본 접미사 `internal.cloudapp.net`과 프리이빗 영역 접미사인 두 개의 FQDN을 반환합니다.
* 조건부 전달은 현재 기본적으로 지원되지 않습니다. Azure와 온-프레미스 네트워크 간에 확인을 사용하려면 [VM 및 역할 인스턴스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.
 
## <a name="pricing"></a>가격 책정

가격 책정 정보는 [Azure DNS 가격 책정](https://azure.microsoft.com/pricing/details/dns/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure PowerShell](./private-dns-getstarted-powershell.md) 또는 [Azure CLI](./private-dns-getstarted-cli.md)를 사용하여 Azure DNS에서 프라이빗 영역을 만드는 방법을 알아봅니다.

* Azure DNS에서 프라이빗 영역으로 실현할 수 있는 몇 가지 일반 [프라이빗 영역 시나리오](./private-dns-scenarios.md)에 대해 읽어보세요.

* Azure DNS의 프라이빗 영역에 대한 일반적인 질문과 답변은 [프라이빗 DNS FAQ](./dns-faq-private.yml)를 참조하세요.

* [DNS 영역 및 레코드 개요](dns-zones-records.md)를 참조하여 DNS 영역 및 레코드에 대해 알아봅니다.

* Azure의 몇 가지 다른 주요 [네트워킹 기능](../networking/fundamentals/networking-overview.md)을 알아봅니다.
