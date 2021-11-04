---
title: Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버를 복원합니다.
description: 이 문서에서는 Azure Portal을 통해 Azure Database for MySQL 유연한 서버에서 복원 작업을 수행하는 방법을 설명합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: cce31cc3d83be03da462c9345c1b8d3d86ef6228
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467930"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Azure Portal 사용하여 Azure Database for MySQL 유연한 서버의 시점 복원

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

이 문서에서는 백업을 사용하여 유연한 서버에서 지정 시간 복구를 수행하는 단계별 절차를 제공합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 완료하려면 다음이 필요합니다.

- Azure Database for MySQL 유연한 서버가 있어야 합니다.

## <a name="restore-to-the-latest-restore-point"></a>최신 복원 지점으로 복원

가장 빠른 기존 백업을 사용하여 유연한 서버를 복원하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 백업을 복원할 유연한 서버를 선택합니다.

2. 왼쪽 패널에서 **개요** 를 클릭합니다.

3. 개요 페이지에서 **복원** 을 클릭합니다.

4. **최신 복원 지점** 과 사용자 지정 복원 지점 중에서 선택할 수 있는 옵션이 있는 복원 페이지가 표시됩니다.

5. **최신 복원 지점** 을 선택합니다.

6. **새 서버에 복원** 필드에 새 서버 이름을 제공합니다.

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-latest.png" alt-text="가장 빠른 복원 시간":::

7. **확인** 을 클릭합니다.

8. 복원 작업이 시작되었다는 알림이 표시됩니다.


## <a name="restore-to-a-fastest-restore-point"></a>가장 빠른 복원 지점으로 복원

기존 전체 백업을 가장 빠른 복원 지점으로 사용하여 유연한 서버를 복원하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com/)에서 백업을 복원할 유연한 서버를 선택합니다. 

2. 왼쪽 패널에서 **개요** 를 클릭합니다. 

3. 개요 페이지에서 **복원** 을 클릭합니다. 

4. 복원 페이지에는 최신 복원 지점, 사용자 지정 복원 지점 및 가장 빠른 복원 지점 중에서 선택하는 옵션이 표시됩니다. 

5. 가장 **빠른 복원 지점 선택(전체 백업을 사용하여 복원)** 옵션을 선택합니다. 

6. **가장 빠른 복원 지점(UTC)** 드롭다운 목록에서 원하는 전체 백업을 선택합니다. 
 
    :::image type="content" source="./media/how-to-restore-server-portal/fastest-restore-point.png" alt-text="가장 빠른 복원 지점":::

7. **새 서버에 복원** 필드에 새 서버 이름을 제공합니다.

8. **검토 + 만들기** 를 클릭합니다. 

9. **만들기를** 클릭하면 복원 작업이 시작되었다는 알림이 표시됩니다.  

## <a name="restore-from-a-full-backup-through-the-backup-and-restore-blade"></a>백업 및 복원 블레이드를 통해 전체 백업에서 복원

기존 전체 백업을 사용하여 유연한 서버를 복원하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com/)에서 백업을 복원할 유연한 서버를 선택합니다. 

2. 왼쪽 패널에서 **백업 및 복원을** 클릭합니다. 

3. 사용 가능한 백업 보기 페이지에는 보존 기간 내에 서버에 대해 수행된 모든 자동화된 백업에서 복원하는 옵션이 표시됩니다.  

4. 해당 **복원** 작업을 클릭하여 목록에서 원하는 전체 백업을 선택합니다. 
 
    :::image type="content" source="./media/how-to-restore-server-portal/view-available-backups.png" alt-text="사용 가능한 백업 보기":::

5. 복원 페이지에는 기본적으로 가장 빠른 복원 지점 옵션이 선택되고 사용 가능한 백업 보기 페이지에서 원하는 전체 백업 타임스탬프가 선택되어 표시됩니다. 

6. **새 서버에 복원** 필드에 새 서버 이름을 제공합니다.

7. **검토 + 만들기** 를 클릭합니다. 

8. **만들기를** 클릭하면 복원 작업이 시작되었다는 알림이 표시됩니다.  


## <a name="geo-restore-to-latest-restore-point"></a>최신 복원 지점으로 지역 복원

1. [Azure Portal](https://portal.azure.com/)에서 백업을 복원할 유연한 서버를 선택합니다.

2. 왼쪽 패널에서 **개요** 를 클릭합니다.

3. 개요 페이지에서 **복원** 을 클릭합니다.

4. 복원 페이지에는 **지역 중복 복원** 을 선택하는 옵션이 표시됩니다. 지리적 중복 백업을 위해 서버를 구성한 경우 해당 Azure 쌍을 이루는 지역으로 서버를 복원하고 지역 중복 복원 옵션을 사용하도록 설정할 수 있습니다. 지역 중복 복원 옵션은 서버를 최신 UTC Now 타임스탬프로 복원하므로 지역 중복 복원을 선택한 후에는 지정 시간 복원 옵션을 동시에 선택할 수 없습니다.

   :::image type="content" source="./media/how-to-restore-server-portal/georestore-flex.png" alt-text="지역 복원 옵션":::

   :::image type="content" source="./media/how-to-restore-server-portal/georestore-enabled-flex.png" alt-text="지역 복원 사용":::

5. 서버 세부 정보 섹션의 **이름** 필드에 새 서버 이름을 제공합니다.

6. **검토 + 만들기를** 선택하여 선택 항목을 검토합니다. 

7. 복원 작업이 시작되었다는 알림이 표시됩니다. 이 작업은 몇 분 정도 걸릴 수 있습니다. 

지역 복원을 통해 만든 새 서버에는 복원이 시작될 때 기존 서버에 유효했던 것과 동일한 서버 관리자 로그인 이름과 암호가 있습니다. 암호는 새 서버의 개요 페이지에서 변경할 수 있습니다. 또한 지역 복원 중에 가상 네트워크 설정 및 방화벽 규칙과 같은 **네트워킹** 설정을 아래 섹션에 설명된 대로 구성할 수 있습니다.

## <a name="using-restore-to-move-a-server-from-public-access-to-private-access"></a>복원을 사용하여 서버를 공용 액세스에서 프라이빗 액세스로 이동

가장 빠른 기존 백업을 사용하여 유연한 서버를 복원하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 백업을 복원할 유연한 서버를 선택합니다.

2. 개요 페이지에서 **복원** 을 클릭합니다.

3. 복원 페이지에는 지역 복원 또는 지정 시간 복원 옵션 중에서 선택할 수 있는 옵션이 표시됩니다.

4. 지역 **복원** 또는 **지정 시간 복원** 옵션을 선택합니다.

5. **새 서버에 복원** 필드에 새 서버 이름을 제공합니다.

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-private-dns-zone.png" alt-text="개요 보기":::

6. **네트워킹** 탭으로 이동하여 네트워킹 설정을 구성합니다.

7. **연결 방법** 에서 **개인 액세스(VNet 통합)** 를 선택합니다. **Virtual Network** 섹션으로 이동하여 이미 존재하는 *가상 네트워크* 및 *Microsoft.DBforMySQL/flexibleServers* 에 위임된 *서브넷* 을 선택하거나 *가상 네트워크 만들기* 링크를 클릭하여 새 가상 네트워크를 만듭니다.
    > [!Note]
    > 동일한 지역 및 구독의 가상 네트워크 및 서브넷만 드롭다운에 나열됩니다. </br>
    > 선택한 서브넷은 *Microsoft.DBforMySQL/flexibleServers* 에 위임됩니다. 이는 Azure Database for MySQL 유연한 서버에서만 해당 서브넷을 사용할 수 있음을 의미합니다.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="Vnet 구성":::

8. 새로 만들거나 기존 **프라이빗 DNS 영역** 을 선택합니다.
    > [!NOTE]
    > 프라이빗 DNS 영역 이름은 `mysql.database.azure.com`으로 끝나야 합니다. </br>
    > 새 프라이빗 DNS 영역을 만드는 옵션이 표시되지 않으면 **기본 사항** 탭에 서버 이름을 입력하세요.</br>
    > 유연한 서버를 가상 네트워크 및 서브넷에 배포한 후에는 공용 액세스(허용된 IP 주소)로 이동할 수 없습니다.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="dns 구성":::
9. **검토 + 만들기** 를 선택하여 유연한 서버 구성을 검토합니다.
10. **만들기** 를 선택하여 서버를 프로비전합니다. 프로비저닝에는 몇 분 정도 걸릴 수 있습니다.

11. 복원 작업이 시작되었다는 알림이 표시됩니다.


## <a name="perform-post-restore-tasks"></a>복원 후 작업 수행

복원이 완료된 후 다음 작업을 수행하여 사용자와 애플리케이션을 다시 실행되도록 해야 합니다.

- 새 서버가 원래 서버를 교체하기 위한 것이라면 클라이언트와 클라이언트 애플리케이션을 새 서버로 리디렉션합니다.
- 사용자가 연결할 수 있도록 적합한 VNet 규칙이 설정되었는지 확인합니다. 이러한 규칙은 원래 서버에서 복사되지 않습니다.
- 적절한 로그인 및 데이터베이스 수준 권한이 있는지 확인합니다.
- 새로운 복원 서버에 대해 적절한 경고를 구성합니다.

## <a name="next-steps"></a>다음 단계

[비즈니스 연속성](concepts-business-continuity.md) 알아보기
