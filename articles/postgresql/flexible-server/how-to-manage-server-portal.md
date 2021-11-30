---
title: 서버 관리 - Azure Portal - Azure Database for PostgreSQL - 유연한 서버
description: Azure Portal에서 Azure Database for PostgreSQL - 유연한 서버를 관리하는 방법을 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 11/30/2021
ms.custom: mvc
ms.openlocfilehash: 1ad66b8d79e1460d7ece42669b85bea93fa5a936
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133269468"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for PostgreSQL - 유연한 서버 관리



이 문서에서는 Azure Database for PostgreSQL - 유연한 서버를 관리하는 방법을 보여 줍니다. 관리 작업에는 컴퓨팅 및 스토리지 스케일링, 관리자 암호 재설정, 서버 정보 보기 등이 포함됩니다.

## <a name="sign-in"></a>로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다. Azure Portal에서 유연한 서버 리소스로 이동합니다.

## <a name="scale-compute-and-storage"></a>컴퓨팅 및 스토리지 스케일링

서버를 만든 후 요구 사항 변경에 따라 다양한 [가격 책정 계층](https://azure.microsoft.com/pricing/details/postgresql/) 간에 스케일링할 수 있습니다. vCore를 늘리거나 줄여서 컴퓨팅 및 메모리를 확장하거나 축소할 수도 있습니다.

> [!NOTE]
> 스토리지를 낮은 값으로 스케일 다운할 수는 없습니다.

1. Azure Portal에서 서버를 선택합니다. **설정** 섹션에 있는 **컴퓨팅 + 스토리지** 를 선택합니다.
2. **컴퓨팅 계층**, **vCore**, **스토리지** 를 변경하여 상위 컴퓨팅 계층으로 서버를 스케일 업하거나 스토리지 또는 vCore를 원하는 값으로 늘려 동일한 계층 내에서 스케일 업할 수 있습니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/scale-server.png" alt-text="스토리지 유연한 서버 스케일링":::

> [!Important]
> - 스토리지를 스케일 다운할 수는 없습니다.
> - vCore를 스케일링하면 서버가 다시 시작됩니다.

3. **확인** 을 선택하여 변경 내용을 저장합니다.

## <a name="reset-admin-password"></a>관리자 암호 재설정

Azure Portal을 사용하여 관리자 역할의 암호를 변경할 수 있습니다.

1. Azure Portal에서 서버를 선택합니다. **개요** 창에서 **암호 재설정** 을 선택합니다.
2. 새 암호를 입력하고 암호를 확인합니다. 텍스트 상자에 암호 복잡성 요구 사항에 대한 메시지가 표시됩니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-manage-server-portal/reset-password.png" alt-text="유연한 서버에 대한 암호 재설정":::

3. **저장** 을 선택하여 새 암호를 저장합니다.

## <a name="delete-a-server"></a>서버 삭제

더 이상 필요하지 않은 경우 서버를 삭제할 수 있습니다.

1. Azure Portal에서 서버를 선택합니다. **개요** 창에서 **삭제** 를 선택합니다.
2. 입력 상자에 서버 이름을 입력하여 서버 삭제를 확인합니다.

   :::image type="content" source="./media/howto-manage-server-portal/delete-server.png" alt-text="유연한 서버 삭제":::

   > [!IMPORTANT]
   > 서버를 삭제한 후에는 되돌릴 수 없습니다.

  > [!div class="mx-imgBorder"]
  > ![유연한 서버 삭제](./media/howto-manage-server-portal/delete-server.png)  

3. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [백업 및 복원 개념 이해](concepts-backup-restore.md)
- [서버 튜닝 및 모니터링](concepts-monitoring.md)
