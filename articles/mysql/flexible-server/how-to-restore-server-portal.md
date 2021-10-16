---
title: Azure Portal을 사용하여 Azure Database for MySQL 유연한 서버를 복원합니다.
description: 이 문서에서는 Azure Portal을 통해 Azure Database for MySQL 유연한 서버에서 복원 작업을 수행하는 방법을 설명합니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 20632b46067f7ec1db6add26d9bed4318f6a7fd5
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069618"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-preview-using-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MySQL - 유연한 서버(미리 보기)의 특정 시점 복원

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL - 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

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

## <a name="using-restore-to-move-a-server-from-public-access-to-private-access"></a>복원을 사용하여 서버를 공용 액세스에서 프라이빗 액세스로 이동

가장 빠른 기존 백업을 사용하여 유연한 서버를 복원하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 백업을 복원할 유연한 서버를 선택합니다.

2. 개요 페이지에서 **복원** 을 클릭합니다.

3. 가장 빠른 복원 지점과 사용자 지정 복원 지점 중에서 선택할 수 있는 옵션이 있는 복원 페이지가 표시됩니다.

4. **가장 빠른 복원 지점** 또는 **사용자 지정 복원 지점** 을 선택합니다.

5. **새 서버에 복원** 필드에 새 서버 이름을 제공합니다.

6. **새 서버에 복원** 필드에 새 서버 이름을 제공합니다.

    :::image type="content" source="./media/how-to-restore-server-portal/point-in-time-restore-private-dns-zone.png" alt-text="개요 보기":::

7. **네트워킹** 탭으로 이동하여 네트워킹 설정을 구성합니다.

8. **연결 방법** 에서 **개인 액세스(VNet 통합)** 를 선택합니다. **Virtual Network** 섹션으로 이동하여 이미 존재하는 *가상 네트워크* 및 *Microsoft.DBforMySQL/flexibleServers* 에 위임된 *서브넷* 을 선택하거나 *가상 네트워크 만들기* 링크를 클릭하여 새 가상 네트워크를 만듭니다.
    > [!Note]
    > 동일한 지역 및 구독의 가상 네트워크 및 서브넷만 드롭다운에 나열됩니다. </br>
    > 선택한 서브넷은 *Microsoft.DBforMySQL/flexibleServers* 에 위임됩니다. 이는 Azure Database for MySQL 유연한 서버에서만 해당 서브넷을 사용할 수 있음을 의미합니다.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/vnet-creation.png" alt-text="Vnet 구성":::

9. 새 사설 DNS 영역을 만들거나 기존 **영역** 을 선택 합니다.
    > [!NOTE]
    > 프라이빗 DNS 영역 이름은 `mysql.database.azure.com`으로 끝나야 합니다. </br>
    > 새 프라이빗 DNS 영역을 만드는 옵션이 표시되지 않으면 **기본 사항** 탭에 서버 이름을 입력하세요.</br>
    > 유연한 서버를 가상 네트워크 및 서브넷에 배포한 후에는 공용 액세스(허용된 IP 주소)로 이동할 수 없습니다.</br>

    :::image type="content" source="./media/how-to-manage-virtual-network-portal/private-dns-zone.png" alt-text="dns 구성":::
10. **검토 + 만들기** 를 선택하여 유연한 서버 구성을 검토합니다.
11. **만들기** 를 선택하여 서버를 프로비전합니다. 프로비저닝에는 몇 분 정도 걸릴 수 있습니다.

12. 복원 작업이 시작되었다는 알림이 표시됩니다.

## <a name="perform-post-restore-tasks"></a>복원 후 작업 수행

복원이 완료된 후 다음 작업을 수행하여 사용자와 애플리케이션을 다시 실행되도록 해야 합니다.

- 새 서버가 원래 서버를 교체하기 위한 것이라면 클라이언트와 클라이언트 애플리케이션을 새 서버로 리디렉션합니다.
- 사용자가 연결할 수 있도록 적합한 VNet 규칙이 설정되었는지 확인합니다. 이러한 규칙은 원래 서버에서 복사되지 않습니다.
- 적절한 로그인 및 데이터베이스 수준 권한이 있는지 확인합니다.
- 새로운 복원 서버에 대해 적절한 경고를 구성합니다.

## <a name="next-steps"></a>다음 단계

[비즈니스 연속성](concepts-business-continuity.md) 알아보기
