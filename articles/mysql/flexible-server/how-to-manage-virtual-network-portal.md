---
title: 가상 네트워크 관리 - Azure Portal - Azure Database for MySQL - 유연한 서버
description: Azure Portal을 사용하여 Azure Database for MySQL - 유연한 서버 가상 네트워크 만들기 및 관리
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: b317dc91f8d4768e767a93d618e9c768d4039659
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536065"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MySQL - 유연한 서버 가상 네트워크 만들기 및 관리

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

Azure Database for MySQL 유동 서버는 유동 서버에 연결하는 두 가지 유형의 상호 배타적인 네트워크 연결 방법을 지원합니다. 다음은 두 가지 옵션입니다.

- 퍼블릭 액세스(허용된 IP 주소)
- 프라이빗 액세스(VNet 통합)

이 문서에서는 Azure Portal을 사용하여 **개인 액세스(VNet 통합)** 를 사용하여 MySQL 서버를 만드는 방법에 중점을 둡니다. 개인 액세스(VNet 통합)를 사용하면 고유한 [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)에 유연한 서버를 배포할 수 있습니다. Azure Virtual Network는 안전한 비공개 네트워크 통신을 제공합니다. 개인 액세스를 사용하면 MySQL server에 대한 연결이 가상 네트워크로 제한됩니다. 이에 대해 자세히 알아보려면 [개인 액세스(VNet 통합)](./concepts-networking-vnet.md#private-access-vnet-integration)를 참조하세요.

>[!Note]
>서버를 만드는 동안 유동 서버를 가상 네트워크 및 서브넷에 배포할 수 있습니다. 유동 서버를 배포한 후에는 다른 가상 네트워크, 서브넷 또는 *퍼블릭 액세스(허용된 IP 주소)* 로 이동할 수 없습니다.

## <a name="prerequisites"></a>필수 구성 요소

가상 네트워크에서 유연한 서버를 만들려면 다음이 필요 합니다.

- [가상 네트워크](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > 가상 네트워크 및 서브넷은 유연한 서버와 동일한 지역 및 구독에 있어야 합니다.

- [서브넷](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service)을 **Microsoft DBforMySQL/flexibleServers** 에 위임. 이렇게 위임하면 Azure Database for MySQL 유동 서버에서만 해당 서브넷을 사용할 수 있습니다. 다른 Azure 리소스 유형은 위임된 서브넷에 있을 수 없습니다.

## <a name="create-azure-database-for-mysql-flexible-server-in-an-already-existing-virtual-network"></a>기존 가상 네트워크에 Azure Database for MySQL 유연한 서버 만들기

1. 포털의 왼쪽 위 모서리에서 **리소스 만들기**(+)를 선택합니다.
2. **데이터베이스** > **Azure Database for MySQL** 을 차례로 선택합니다. 검색 상자에서 **MySQL** 을 입력하여 해당 서비스를 찾을 수도 있습니다.
3. **유연한 서버** 를 배포 옵션으로 선택합니다.
4. **기본** 양식을 작성합니다.
5. **네트워킹** 탭으로 이동합니다.
6. **연결 방법** 에서 **개인 액세스(VNet 통합)** 를 선택합니다. **Virtual Network** 섹션으로 이동하여 이미 존재하는 *가상 네트워크* 및 *Microsoft.DBforMySQL/flexibleServers* 에 위임된 *서브넷* 을 선택할 수 있습니다. 또는 *가상 네트워크 만들기* 링크를 클릭하여 새 네트워크를 만듭니다.
    > [!Note]
    > 동일한 지역 및 구독의 가상 네트워크 및 서브넷만 드롭다운에 나열됩니다. </br>
    > 선택한 서브넷은 *Microsoft.DBforMySQL/flexibleServers* 에 위임됩니다. 이는 Azure Database for MySQL 유연한 서버에서만 해당 서브넷을 사용할 수 있음을 의미합니다.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="VNET 통합":::

7. 새로 만들거나 기존 **프라이빗 DNS 영역** 을 선택합니다.
    > [!NOTE]
    > 프라이빗 DNS 영역 이름은 `mysql.database.azure.com`으로 끝나야 합니다. </br>
    > 새 프라이빗 DNS 영역을 만드는 옵션이 표시되지 않으면 **기본** 탭에 서버 이름을 입력하세요.</br>
    > 유연한 서버를 가상 네트워크 및 서브넷에 배포한 후에는 공용 액세스(허용된 IP 주소)로 이동할 수 없습니다.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="dnsconfiguration":::
8. **검토 + 만들기** 를 선택하여 유연한 서버 구성을 검토합니다.
9. **만들기** 를 선택하여 서버를 프로비전합니다. 프로비저닝에는 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure CLI를 사용하여 Azure Database for MySQL 유연한 서버 가상 네트워크를 만들고 관리](./how-to-manage-virtual-network-cli.md)합니다.
- [Azure Database for MySQL 유연한 서버의 네트워킹](./concepts-networking.md)에 대해 자세히 알아봅니다.
- [Azure Database for MySQL 유연한 서버 가상 네트워크](./concepts-networking-vnet.md#private-access-vnet-integration)에 대해 자세히 알아봅니다.
