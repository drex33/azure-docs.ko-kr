---
title: 서버 관리 - Azure Portal - Azure Database for MySQL
description: Azure Portal에서 Azure Database for MySQL 서버를 관리하는 방법을 알아봅니다.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/26/2021
ms.openlocfilehash: 5e6f21b2ae49539a8728a307eb33bfdba33f9688
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633054"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MySQL 서버 관리

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

이 문서에서는 Azure Database for MySQL 서버를 관리하는 방법을 보여 줍니다. 관리 작업에는 컴퓨팅 및 스토리지 스케일링, 관리자 암호 재설정, 서버 세부 정보 보기 등이 포함됩니다.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *slave* 에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.
>

## <a name="sign-in"></a>로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-a-server"></a>서버 만들기

Azure Database for MySQL 서버를 만들고 시작하는 방법을 알아보려면 [빠른 시작](quickstart-create-mysql-server-database-using-azure-portal.md)을 참조하세요.

## <a name="scale-compute-and-storage"></a>컴퓨팅 및 스토리지 스케일링

서버를 만든 후에는 요구 사항이 변경됨에 따라 범용과 메모리 최적화 계층 간에 스케일링할 수 있습니다. vCore를 늘리거나 줄여서 컴퓨팅과 메모리를 스케일링할 수도 있습니다. 스토리지는 스케일 업할 수 있습니다(그러나 스토리지 스케일 다운은 불가능함).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>범용 및 메모리 최적화 계층 간 크기 조정

범용에서 메모리 최적화로, 또는 그 반대로 스케일링할 수 있습니다. 서버 만들기가 지원되지 않게 되면 기본 계층을 변경하세요.

1. Azure Portal에서 서버를 선택합니다. **설정** 섹션에 있는 **가격 책정 계층** 을 선택합니다.

2. 스케일링하는 대상에 따라 **범용** 또는 **메모리 최적화** 를 선택합니다.

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="Azure Database for MySQL에서 기본, 범용 또는 메모리 최적화 계층을 선택하는 Azure Portal의 스크린샷":::

   > [!NOTE]
   > 계층을 변경하면 서버가 다시 시작됩니다.

3. **확인** 을 선택하여 변경 사항을 저장합니다.

### <a name="scale-vcores-up-or-down"></a>VCores 확장 또는 축소

1. Azure Portal에서 서버를 선택합니다. **설정** 섹션에 있는 **가격 책정 계층** 을 선택합니다.

2. 슬라이더를 원하는 값으로 이동하여 **vCore** 설정을 변경합니다.

    :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="Azure Database for MySQL에서 vCore 옵션을 선택하는 Azure Portal의 스크린샷":::

    > [!NOTE]
    > vCore를 스케일링하면 서버가 다시 시작됩니다.

3. **확인** 을 선택하여 변경 사항을 저장합니다.

### <a name="scale-storage-up"></a>스토리지 확장

1. Azure Portal에서 서버를 선택합니다. **설정** 섹션에 있는 **가격 책정 계층** 을 선택합니다.

2. 슬라이더를 원하는 값으로 이동하여 **스토리지** 설정을 변경합니다.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="Azure Database for MySQL에서 스토리지 스케일링을 선택하는 Azure Portal의 스크린샷":::

   > [!NOTE]
   > 스토리지를 축소할 수는 없습니다.

3. **확인** 을 선택하여 변경 사항을 저장합니다.

## <a name="update-admin-password"></a>관리자 암호 업데이트

Azure Portal을 사용하여 관리자 역할의 암호를 변경할 수 있습니다.

1. Azure Portal에서 서버를 선택합니다. **개요** 창에서 **암호 재설정** 을 선택합니다.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="Azure Database for MySQL에서 암호를 초기화하는 Azure Portal의 스크린샷":::

2. 새 암호를 입력하고 암호를 확인합니다. 텍스트 상자에 암호 복잡성 요구 사항에 대한 메시지가 표시됩니다.

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Azure Database for MySQL에서 암호를 초기화하고 저장하는 Azure Portal의 스크린샷":::

3. **확인** 을 클릭하여 새 암호를 저장합니다.
 

> [!IMPORTANT]
> 서버 관리자 암호를 다시 설정하면 자동으로 서버 관리자 권한이 기본값으로 다시 설정됩니다. 하나 이상의 서버 관리자 권한을 실수로 취소한 경우 서버 관리자 암호를 다시 설정하는 것이 좋습니다.
   
> [!NOTE]
> 서버 관리 사용자는 기본적으로 SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, RELOAD, PROCESS, REFERENCES, INDEX, ALTER, SHOW DATABASES, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER 권한을 가집니다.

## <a name="delete-a-server"></a>서버 삭제

더 이상 필요하지 않은 경우 서버를 삭제할 수 있습니다.

1. Azure Portal에서 서버를 선택합니다. **개요** 창에서 **삭제** 를 선택합니다.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="Azure Database for MySQL에서 서버를 삭제하는 Azure Portal의 스크린샷":::

2. 입력 상자에 서버 이름을 입력하여 서버 삭제를 확인합니다.

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="Azure Database for MySQL에서 서버 삭제를 확인하는 Azure Portal의 스크린샷":::

   > [!NOTE]
   > 서버를 삭제한 후에는 되돌릴 수 없습니다.

3. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [백업 및 서버 복원](howto-restore-server-portal.md)에 대한 자세한 정보
- [Azure Database for MySQL의 튜닝 및 모니터링 옵션](concepts-monitoring.md)에 관한 자세한 정보
