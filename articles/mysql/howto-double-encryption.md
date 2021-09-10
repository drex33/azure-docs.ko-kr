---
title: 인프라 이중 암호화 - Azure Portal - Azure Database for MySQL
description: Azure Database for MySQL용 인프라 이중 암호화를 설정하고 관리하는 방법을 알아봅니다.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 3f9c22a690859b459b6bb748c3b1001c4aa7660d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93241755"
---
# <a name="infrastructure-double-encryption-for-azure-database-for-mysql"></a>Azure Database for MySQL용 인프라 이중 암호화

Azure Database for MySQL용 인프라 이중 암호화를 설정하고 관리하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---portal"></a>인프라 이중 암호화를 사용하여 Azure Database for MySQL 서버 만들기-포털

Azure Portal에서 인프라 이중 암호화를 사용하여 Azure Database for MySQL 서버를 만들려면 다음 단계를 따르세요.

1. 포털의 왼쪽 위 모서리에서 **리소스 만들기**(+)를 선택합니다.

2. **데이터베이스** > **Azure Database for MySQL** 을 차례로 선택합니다. 검색 상자에서 **MySQL** 을 입력하여 해당 서비스를 찾을 수도 있습니다.

   :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Azure Database for MySQL 옵션":::

3. 서버에 대한 기본 정보를 제공합니다. **추가 설정** 을 선택하고 **인프라 이중 암호화** 확인란을 사용하도록 설정하여 매개 변수를 설정합니다.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-selected.png" alt-text="Azure Database for MySQL 서버":::

4. **검토 + 만들기** 를 선택하여 서버를 프로비저닝합니다.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-summary.png" alt-text="Azure Database for MySQL 요약":::

5. 서버가 만들어지면 **데이터 암호화** 서버 블레이드에서 상태를 확인하여 인프라 이중 암호화가 유효한지 검사할 수 있습니다.

    :::image type="content" source="./media/howto-double-encryption/infrastructure-encryption-validation.png" alt-text="Azure Database for MySQL 유효성 검사":::

## <a name="create-an-azure-database-for-mysql-server-with-infrastructure-double-encryption---cli"></a>인프라 이중 암호화를 사용하여 Azure Database for MySQL 서버 만들기 - CLI

CLI에서 인프라 이중 암호화를 사용하여 Azure Database for MySQL 서버를 만들려면 다음 단계를 따르세요.

다음 예제에서는 `westus` 위치에 `myresourcegroup`이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```
다음 예제에서는 미국 서부에 `myadmin` 서버 관리자 로그인을 사용하여 `myresourcegroup` 리소스 그룹에 `mydemoserver`라는 MySQL 5.7 서버를 만듭니다. 이 서버는 **2개 vCore** 가 있는 **4세대** **범용** 서버입니다. 이렇게 하면 생성된 서버에 대한 인프라 이중 암호화도 사용하도록 설정됩니다. `<server_admin_password>`를 자신의 고유한 값으로 직접 바꿉니다.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7 --infrastructure-encryption <Enabled/Disabled>
```

## <a name="next-steps"></a>다음 단계

 데이터 암호화에 대해 자세히 알아보려면 [Azure Database for MySQL 데이터 인프라 이중 암호화](concepts-Infrastructure-double-encryption.md)를 참조하세요.
