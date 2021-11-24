---
title: '빠른 시작: 서버 만들기 - Azure Portal - Azure Database for MySQL'
description: 이 문서에서는 Azure Portal을 사용하여 약 5분 안에 Azure Database for MySQL 서버 샘플을 만드는 과정을 안내합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, mode-portal
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: 7a7714bb2c99720f7d4dfe843898083760959670
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133052693"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Azure Database for MySQL 서버 만들기

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL은 클라우드에서 고가용성 MySQL 데이터베이스를 실행, 관리 및 확장하는 데 사용하는 관리형 서비스입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 Azure Database for MySQL 단일 서버를 만드는 방법을 보여줍니다. 서버에 연결하는 방법도 보여줍니다.

## <a name="prerequisites"></a>필수 구성 요소
Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="create-an-azure-database-for-mysql-single-server"></a>Azure Database for MySQL 단일 서버 만들기
1. [Azure Portal](https://portal.azure.com/)로 이동하여 MySQL 단일 서버 데이터베이스를 만듭니다. **Azure Database for MySQL** 을 검색하여 선택합니다.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Azure Database for MySQL 찾기":::

1. **추가** 를 선택합니다.

2. **Azure Database for MySQL 배포 옵션 선택** 페이지에서 **단일 서버** 를 선택합니다.
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="단일 서버 옵션을 보여주는 스크린샷":::

3. 새 단일 서버의 기본 설정을 입력합니다.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="[MySQL 서버 만들기] 페이지를 보여주는 스크린샷":::

   **설정** | **제안 값** | **설명**
   ---|---|---
   Subscription | 사용자의 구독 | 원하는 Azure 구독을 선택합니다.
   Resource group | **myresourcegroup** | 새 리소스 그룹 또는 구독의 기존 리소스 그룹을 입력합니다.
   서버 이름 | **mydemoserver** | 고유한 이름을 입력합니다. 서버 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3~63자를 포함해야 합니다.
   데이터 원본 |**없음** | 새 서버를 처음부터 만들려면 **없음** 을 선택합니다. 기존 서버의 지역 백업에서 복원하는 경우에만 **백업** 을 선택합니다.
   위치 |원하는 위치 | 목록에서 위치를 선택합니다.
   버전 | 최신 주 버전| 최신 주 버전을 사용합니다. [지원되는 모든 버전](concepts-supported-versions.md)을 참조하세요.
   컴퓨팅 + 스토리지 | 기본값 사용| 기본 가격 책정 계층은 **4개 vCore** 및 **100GB** 스토리지를 사용하는 **범용** 입니다. 백업 보존은 **지리적 중복** 백업 옵션을 사용하여 **7일** 로 설정됩니다.<br/>[가격 책정](https://azure.microsoft.com/pricing/details/mysql/) 페이지를 검토하고, 필요한 경우 기본값을 업데이트합니다.
   관리자 사용자 이름 | **mydemoadmin** | 서버 관리 사용자 이름을 입력합니다. 관리 사용자 이름으로 **azure_superuser**, **admin**, **administrator**, **root**, **guest** 또는 **public** 을 사용할 수 없습니다.
   암호 | 암호 | 서버 관리 사용자의 새 암호입니다. 암호는 대문자 또는 소문자, 숫자 및 영숫자가 아닌 문자(!, $, #, % 등)를 조합하여 8~128자 길이여야 합니다.
  

   > [!NOTE]
   > 워크로드에 가벼운 컴퓨팅 및 I/O가 적합한 경우 기본 가격 책정 계층을 고려합니다. 기본 가격 책정 계층에서 만든 서버는 나중에 범용으로 또는 메모리 최적화되도록 확장할 수 없습니다.

4. **검토 + 만들기** 를 선택하여 서버를 프로비저닝합니다.

5. 포털 페이지에 **배포가 완료됨** 이 표시되기를 기다립니다. **리소스로 이동** 을 선택하여 새로 만든 서버 페이지로 이동합니다.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="[배포가 완료됨] 메시지를 보여주는 스크린샷":::

[문제가 있나요? 알려주세요.](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 구성

기본적으로 새 서버는 방화벽으로 보호됩니다. 연결하려면 다음 단계를 완료하여 IP에 대한 액세스 권한을 제공해야 합니다.

1. 서버 리소스의 왼쪽 창에서 **연결 보안** 으로 이동합니다. 리소스를 찾는 방법을 모르는 경우 [리소스를 여는 방법](../azure-resource-manager/management/manage-resources-portal.md#open-resources)을 참조하세요.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="[연결 보안] > [방화벽 규칙] 페이지를 보여주는 스크린샷":::

2. **현재 클라이언트 IP 주소 추가** 를 선택한 다음, **저장** 을 선택합니다.

   > [!NOTE]
   > 연결 문제를 방지하려면 현재 네트워크에서 Azure Database for MySQL이 사용하는 3306 포트를 통해 아웃바운드 트래픽을 허용하는지 확인합니다. 

IP를 더 추가하거나 IP 범위를 제공하여 해당 IP에서 서버에 연결할 수 있습니다. 자세한 내용은 [Azure Database for MySQL 서버에서 방화벽 규칙을 관리하는 방법](./concepts-firewall-rules.md)을 참조하세요.


[문제가 있나요? 알려주세요.](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-by-using-mysqlexe"></a>mysql.exe를 사용하여 서버에 연결
[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 또는 [MySQL 워크벤치](./connect-workbench.md)를 사용하여 로컬 환경에서 서버에 연결할 수 있습니다. 이 빠른 시작에서는 [Azure Cloud Shell](../cloud-shell/overview.md)에서 mysql.exe를 실행하여 서버에 연결합니다.


1. 다음 스크린샷처럼 포털의 도구 모음에서 첫 번째 단추를 선택하여 Azure Cloud Shell을 엽니다. 스크린샷처럼 **개요** 섹션에서 새 서버의 서버 이름, 서버 관리자 이름 및 구독을 기록해 둡니다.

    > [!NOTE]
    > Cloud Shell을 처음 여는 경우 리소스 그룹과 스토리지 계정을 생성하라는 메시지가 표시됩니다. 이 단계는 한 번만 수행하면 됩니다. 이후부터는 모든 세션에서 자동으로 연결됩니다.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Azure Portal의 Cloud Shell을 보여주는 스크린샷":::
2. Azure Cloud Shell 터미널에서 다음 명령을 실행합니다. 여기에 표시된 값을 실제 서버 이름 및 관리 사용자 이름으로 바꿉니다. Azure Database for MySQL의 경우 다음과 같이 관리 사용자 이름에 `@\<servername>`를 추가해야 합니다. 

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      Cloud Shell 터미널은 다음과 비슷합니다.

      ```
      Requesting a Cloud Shell.Succeeded.
      Connecting terminal...

      Welcome to Azure Cloud Shell

      Type "az" to use Azure CLI
      Type "help" to learn about Cloud Shell

      user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
      Enter password:
      Welcome to the MySQL monitor.  Commands end with ; or \g.
      Your MySQL connection id is 64796
      Server version: 5.6.42.0 Source distribution

      Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
      Oracle is a registered trademark of Oracle Corporation and/or its
      affiliates. Other names may be trademarks of their respective
      owners.

      Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
      mysql>
      ```
3. 동일한 Azure Cloud Shell 터미널에서 `guest`라는 데이터베이스를 만듭니다.
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. `guest` 데이터베이스로 전환합니다.
      ```
      mysql> USE guest;
      Database changed
      ```
5. `quit`를 입력한 다음, **Enter** 를 선택하여 mysql을 종료합니다.

[문제가 있나요? 알려주세요.](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>리소스 정리
리소스 그룹에서 Azure Database for MySQL 서버를 만들었습니다.  이러한 리소스가 나중에 필요하지 않을 경우에는 리소스 그룹을 삭제하거나 MySQL 서버만 삭제하여 리소스를 삭제할 수 있습니다. 리소스 그룹을 삭제하려면 다음 단계를 완료합니다.
1. Azure Portal에서 **리소스 그룹** 을 검색하고 선택합니다.
2. 리소스 그룹 목록에서 삭제하려는 리소스 그룹의 이름을 선택합니다.
3. 리소스 그룹의 **개요** 페이지에서 **리소스 그룹 삭제** 를 선택합니다.
4. 확인 대화 상자에서 리소스 그룹의 이름을 입력한 다음, **삭제** 를 선택합니다.

서버를 삭제하려면 다음과 같이 서버의 **개요** 페이지에서 **삭제** 단추를 선택하면 됩니다.
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="서버 개요 페이지의 [삭제] 단추를 보여주는 스크린샷":::

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
>[MySQL을 사용하여 Windows에서 PHP 앱 빌드](../app-service/tutorial-php-mysql-app.md) <br/>

> [!div class="nextstepaction"]
>[MySQL을 사용하여 Linux에서 PHP 앱 빌드](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%3fpivots%3dplatform-linux)<br/><br/>

[원하는 항목을 찾을 수 없나요? 알려주세요.](https://aka.ms/mysql-doc-feedback)
