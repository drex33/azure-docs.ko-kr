---
title: 프라이빗 액세스(미리 보기) - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 이 문서에서는 Azure Database for PostgreSQL - 하이퍼스케일(Citus) 대한 프라이빗 액세스를 설명합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 53e2cc3c62cc04ef05ccfe22b4876616916b0284
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075858"
---
# <a name="private-access-preview-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL 프라이빗 액세스(미리 보기) - 하이퍼스케일(Citus)

[!INCLUDE [azure-postgresql-hyperscale-access](../../includes/azure-postgresql-hyperscale-access.md)]

이 페이지에서는 프라이빗 액세스 옵션에 대해 설명합니다. 공용 액세스는 [여기를 참조하세요.](concepts-hyperscale-firewall-rules.md)

> [!NOTE]
>
> 프라이빗 액세스는 [특정 지역에서만](concepts-hyperscale-limits.md#regions)미리 보기로 사용할 수 있습니다.
>
> 서버 그룹이 허용된 지역 내에 있더라도 서버 그룹에 대해 프라이빗 액세스 옵션을 선택할 수 없는 경우 Azure [지원 요청을](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)열고 Azure 구독 ID를 포함시켜 액세스 권한을 얻습니다.

## <a name="definitions"></a>정의

**가상 네트워크**. Azure VNet(Virtual Network)은 Azure의 프라이빗 네트워킹에 대한 기본 구성 블록입니다. 가상 네트워크를 사용하면 데이터베이스 서버 및 Azure VM(Virtual Machines)과 같은 다양한 유형의 Azure 리소스가 서로 안전하게 통신할 수 있습니다. 가상 네트워크는 온-프레임 연결을 지원하고, 여러 가상 네트워크의 호스트가 피어링을 통해 서로 상호 작용할 수 있도록 하고, 확장, 보안 옵션 및 격리의 추가 이점을 제공합니다. 하이퍼스케일(Citus) 서버 그룹의 각 프라이빗 엔드포인트에는 연결된 가상 네트워크가 필요합니다.

**서브넷**. 서브넷은 가상 네트워크를 하나 이상의 서브네트워크로 분할합니다.
각 서브네트워크는 주소 공간의 일부를 가져오고 주소 할당 효율성을 향상합니다.  네트워크 보안 그룹을 사용하여 서브넷 내의 리소스에 보안을 지정할 수 있습니다. 자세한 내용은 네트워크 보안 그룹을 참조하세요.

하이퍼스케일(Citus) 프라이빗 엔드포인트에 대한 서브넷을 선택하는 경우 현재 및 향후 요구 사항에 맞게 해당 서브넷에서 충분한 개인 IP 주소를 사용할 수 있는지 확인합니다.

**프라이빗 엔드포인트 입니다.** 프라이빗 엔드포인트는 가상 네트워크의 개인 IP 주소를 사용하는 네트워크 인터페이스입니다. 이 네트워크 인터페이스는 Azure Private Link 구동되는 서비스에 비공개적으로 안전하게 연결됩니다. 프라이빗 엔드포인트는 서비스를 가상 네트워크로 가져옵니다.

하이퍼스케일(Citus) 프라이빗 액세스를 사용하도록 설정하면 서버 그룹의 코디네이터 노드에 대한 프라이빗 엔드포인트가 만들어집니다. 엔드포인트를 사용하면 선택한 가상 네트워크의 호스트가 코디네이터에 액세스할 수 있습니다. 필요에 따라 작업자 노드에 대한 프라이빗 엔드포인트도 만들 수 있습니다.

**영역 프라이빗 DNS.** Azure 프라이빗 DNS 영역은 연결된 가상 네트워크 내 및 피어된 가상 네트워크 내에서 호스트 이름을 확인합니다. 하이퍼스케일(Citus) 노드에 대한 도메인 레코드는 해당 서버 그룹에 대해 선택된 프라이빗 DNS 영역에 만들어집니다.  노드의 PostgreSQL 연결 문자열에 FQDN(정규화된 도메인 이름)을 사용해야 합니다.

## <a name="private-link"></a>프라이빗 링크

하이퍼스케일(Citus) 서버 그룹에 [프라이빗 엔드포인트를](/azure/private-link/private-endpoint-overview) 사용하여 VNet(가상 네트워크)의 호스트가 [Private Link](/azure/private-link/private-link-overview)통해 데이터에 안전하게 액세스할 수 있도록 할 수 있습니다.

서버 그룹의 프라이빗 엔드포인트는 가상 네트워크의 주소 공간에서 IP 주소를 사용합니다. 가상 네트워크의 호스트와 하이퍼스케일(Citus) 노드 간의 트래픽은 Microsoft 백본 네트워크의 프라이빗 링크를 통해 이동하고 공용 인터넷에 대한 노출을 제거합니다.

가상 네트워크의 애플리케이션은 다른 방법으로 사용하는 것과 동일한 연결 문자열 및 권한 부여 메커니즘을 사용하여 프라이빗 엔드포인트를 통해 하이퍼스케일(Citus) 노드에 원활하게 연결할 수 있습니다.

하이퍼스케일(Citus) 서버 그룹을 만드는 동안 프라이빗 액세스를 선택할 수 있으며 언제든지 퍼블릭 액세스에서 프라이빗 액세스로 전환할 수 있습니다.

### <a name="using-a-private-dns-zone"></a>프라이빗 DNS 영역 사용

하이퍼스케일(Citus) 이전에 만든 프라이빗 DNS 영역 중 하나를 선택하지 않으면 새 프라이빗 DNS 영역이 각 프라이빗 엔드포인트에 대해 자동으로 프로비전됩니다. 자세한 내용은 [프라이빗 DNS 영역 개요](/azure/dns/private-dns-overview)를 참조하세요.

하이퍼스케일(Citus) 서비스는 `c.privatelink.mygroup01.postgres.database.azure.com`  프라이빗 엔드포인트가 있는 각 노드에 대해 선택한 프라이빗 DNS 영역에 와 같은 DNS 레코드를 만듭니다. 프라이빗 엔드포인트를 통해 Azure VM에서 하이퍼스케일(Citus) 노드에 연결하는 경우 Azure DNS 노드의 FQDN을 개인 IP 주소로 확인합니다.

프라이빗 DNS 영역 설정과 가상 네트워크 피어링은 서로 독립적입니다. 다른 가상 네트워크(동일한 지역 또는 다른 지역에서)에 프로비전된 클라이언트에서 서버 그룹의 노드에 연결하려면 프라이빗 DNS 영역을 가상 네트워크와 연결해야 합니다. 자세한 내용은 [가상 네트워크 연결](/azure/dns/private-dns-getstarted-portal#link-the-virtual-network)을 참조하세요.

> [!NOTE]
>
> 또한 서비스는 항상 모든 노드에 대해 와 같은 공용 CNAME `c.mygroup01.postgres.database.azure.com` 레코드를 만듭니다. 그러나 데이터베이스 관리자가 서버 그룹에 대한 공용 액세스를 사용하도록 설정한 경우에만 공용 인터넷에서 선택한 컴퓨터가 [공용](concepts-hyperscale-firewall-rules.md) 호스트 이름에 연결할 수 있습니다.

사용자 지정 DNS 서버를 사용하는 경우 DNS 전달자를 사용하여 하이퍼스케일(Citus) 노드의 FQDN을 해결해야 합니다. 전달자 IP 주소는 168.63.129.16이어야 합니다. 사용자 지정 DNS 서버는 가상 네트워크 내부에 있거나 가상 네트워크의 DNS 서버 설정을 통해 연결할 수 있어야 합니다. 자세한 내용은 [자체 DNS 서버를 사용하는 이름 확인](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)을 참조하세요.

### <a name="recommendations"></a>권장 사항

하이퍼스케일(Citus) 서버 그룹에 대한 프라이빗 액세스를 사용하도록 설정하는 경우 다음을 고려합니다.

* **서브넷 크기:** 하이퍼스케일(Citus) 서버 그룹에 대한 서브넷 크기를 선택할 때 코디네이터 또는 해당 서버 그룹의 모든 노드에 대한 IP 주소와 같은 현재 요구 사항과 해당 서버 그룹의 증가와 같은 향후 요구 사항을 고려합니다. 현재 및 미래의 요구 사항에 충분한 개인 IP 주소가 있는지 확인합니다. Azure는 각 서브넷에 5개의 IP 주소를 예약합니다.
  자세한 내용은 [이 FAQ를 참조하세요.](/azure/virtual-network/virtual-networks-faq#configuration)
* **프라이빗 DNS 영역:** 개인 IP 주소가 있는 DNS 레코드는 하이퍼스케일(Citus) 서비스에서 유지 관리됩니다. 하이퍼스케일(Citus) 서버 그룹에 사용되는 프라이빗 DNS 영역을 삭제하지 않도록 합니다.

## <a name="limits-and-limitations"></a>제한 및 제한 사항

하이퍼스케일(Citus) [제한 사항](concepts-hyperscale-limits.md) 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [프라이빗 액세스를 사용하도록 설정하고 관리하는](howto-hyperscale-private-access.md) 방법 알아보기(미리 보기)
* [자습서에](tutorial-hyperscale-private-access.md) 따라 프라이빗 액세스(미리 보기)가 작동하는 모습을 확인합니다.
* 프라이빗 [엔드포인트에](/azure/private-link/private-endpoint-overview) 대해 알아보기
* 가상 [네트워크에](/azure/virtual-network/concepts-and-best-practices) 대해 알아보기
* 프라이빗 DNS 영역에 대해 [알아보기](/azure/dns/private-dns-overview)
