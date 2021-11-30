---
title: 가상 네트워크 관리 - Azure Portal - Azure Database for PostgreSQL - 유연한 서버
description: Azure Portal을 사용하여 Azure Database for PostgreSQL - 유연한 서버에 대한 가상 네트워크 만들기 및 관리
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 11/30/2021
ms.openlocfilehash: 5d18a27533213687d6648d639f7a630b2a51738d
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271197"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for PostgreSQL - 유연한 서버에 대한 가상 네트워크 만들기 및 관리



Azure Database for PostgreSQL - 유연한 서버는 유연한 서버에 연결하는 두 가지 유형의 상호 배타적인 네트워크 연결 방법을 지원합니다. 다음은 두 가지 옵션입니다.

* 퍼블릭 액세스(허용된 IP 주소)
* 프라이빗 액세스(VNet 통합)

이 문서에서는 Azure Portal을 사용하여 **프라이빗 액세스(VNet 통합)** 로 PostgreSQL 서버를 만드는 방법에 중점을 둡니다. 개인 액세스(VNet 통합)를 사용하면 고유한 [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)에 유연한 서버를 배포할 수 있습니다. Azure Virtual Network는 안전한 비공개 네트워크 통신을 제공합니다. 프라이빗 액세스를 사용하면 PostgreSQL 서버에 대한 연결이 가상 네트워크로 제한됩니다. 이에 대해 자세히 알아보려면 [개인 액세스(VNet 통합)](./concepts-networking.md#private-access-vnet-integration)를 참조하세요.

서버를 만드는 동안 유동 서버를 가상 네트워크 및 서브넷에 배포할 수 있습니다. 유동 서버를 배포한 후에는 다른 가상 네트워크, 서브넷 또는 *퍼블릭 액세스(허용된 IP 주소)* 로 이동할 수 없습니다.

## <a name="prerequisites"></a>필수 구성 요소
가상 네트워크에서 유연한 서버를 만들려면 다음이 필요 합니다.
- [가상 네트워크](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > 가상 네트워크 및 서브넷은 유연한 서버와 동일한 지역 및 구독에 있어야 합니다.

-  [서브넷](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service)을 **Microsoft.DBforPostgreSQL/flexibleServers** 에 위임합니다. 이렇게 위임하면 Azure Database for PostgreSQL 유연한 서버에서만 해당 서브넷을 사용할 수 있습니다. 다른 Azure 리소스 유형은 위임된 서브넷에 있을 수 없습니다.
-  유연한 서버에 위임된 서브넷의 서비스 엔드포인트에 `Microsoft.Storage`를 추가합니다. 이렇게 하려면 다음 단계를 수행하면 됩니다.
     1. 가상 네트워크 페이지로 이동합니다.
     2. 유연한 서버를 배포하려는 VNET을 선택합니다.
     3. 유연한 서버에 대해 위임된 서브넷을 선택합니다.
     4. 풀아웃 화면의 **서비스 엔드포인트** 아래에 드롭다운에서 `Microsoft.storage`를 선택합니다.
     5. 변경 내용을 저장합니다.

- 유연한 서버와 함께 사용할 자체 프라이빗 DNS 영역을 설정하려는 경우 자세한 내용은 [프라이빗 DNS 개요](../../dns/private-dns-overview.md) 문서를 참조하세요. 
  
## <a name="create-azure-database-for-postgresql---flexible-server-in-an-already-existing-virtual-network"></a>기존 가상 네트워크에 Azure Database for PostgreSQL - 유연한 서버 만들기

1. 포털의 왼쪽 위 모서리에서 **리소스 만들기**(+)를 선택합니다.
2. **데이터베이스** > **PostgreSQL용 Azure Database** 를 차례로 선택합니다. 검색 상자에서 **PostgreSQL** 을 입력하여 해당 서비스를 찾을 수도 있습니다.
3. **유연한 서버** 를 배포 옵션으로 선택합니다.
4. **기본** 양식을 작성합니다.
5. **네트워킹** 탭으로 이동하여 서버에 연결할 방법을 구성합니다.
6. **연결 방법** 에서 **개인 액세스(VNet 통합)** 를 선택합니다. **Virtual Network** 로 이동하고 위 필수 구성 요소의 일부로 만든 기존 *가상 네트워크* 및 *서브넷* 을 선택합니다.
7. **프라이빗 DNS 통합** 에서 기본적으로 서버 이름을 사용하여 새 프라이빗 DNS 영역이 만들어집니다. 선택적으로 드롭다운 목록에서 *구독* 및 *프라이빗 DNS 영역* 을 선택할 수 있습니다.
8. **검토 + 만들기** 를 선택하여 유연한 서버 구성을 검토합니다.
9. **만들기** 를 선택하여 서버를 프로비전합니다. 프로비저닝에는 몇 분 정도 걸릴 수 있습니다.
:::image type="content" source="./media/how-to-manage-virtual-network-portal/how-to-inject-flexible-server-vnet.png" alt-text="VNET에 유연한 서버 삽입":::

>[!Note]
> 유연한 서버를 가상 네트워크 및 서브넷에 배포한 후에는 공용 액세스(허용된 IP 주소)로 이동할 수 없습니다.

>[!Note]
> 다른 VNET에 프로비저닝된 클라이언트에서 유연한 서버에 연결하려는 경우에는 VNET과 프라이빗 DNS 영역을 연결해야 합니다. 연결 방법은 이 [가상 네트워크 연결](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계
- [Azure CLI를 사용하여 Azure Database for PostgreSQL - 유연한 서버 가상 네트워크를 만들고 관리](./how-to-manage-virtual-network-cli.md)합니다.
- [Azure Database for PostgreSQL - 유연한 서버의 네트워킹](./concepts-networking.md)에 대해 자세히 알아보기
- [Azure Database for PostgreSQL - 유연한 서버 가상 네트워크](./concepts-networking.md#private-access-vnet-integration)에 대해 자세히 알아봅니다.