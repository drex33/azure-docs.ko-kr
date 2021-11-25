---
title: '빠른 시작: 서버 만들기 - Azure Portal - Azure Database for PostgreSQL - 유연한 서버'
description: Azure Portal 사용자 인터페이스를 사용하여 Azure Database for PostgreSQL - 유연한 서버를 만들고 관리하기 위한 빠른 시작 가이드입니다.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc, mode-portal
ms.topic: quickstart
ms.date: 06/02/2021
ms.openlocfilehash: d2a9bb93b080cfba1a0e74c3ecf106d23f5d51f5
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133066114"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---flexible-server-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 Azure Database for PostgreSQL - 유연한 서버 만들기

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

PostgreSQL용 Azure Database는 클라우드에서 항상 사용 가능한 PostgreSQL 데이터베이스를 실행, 관리 및 크기 조정하는 데 사용하는 관리 서비스입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 약 5분 안에 Azure Database for PostgreSQL - 유연한 서버를 만드는 방법을 보여 줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

웹 브라우저를 열고 [포털](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.

## <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure Database 서버 만들기

Azure Database for PostgreSQL 서버는 구성된 [컴퓨팅 및 스토리지 리소스](./concepts-compute-storage.md) 세트로 만들어집니다. 서버는 [Azure 리소스 그룹](../../azure-resource-manager/management/overview.md) 내에 만들어집니다.

PostgreSQL 서버용 Azure Database를 만들려면 다음 단계를 따릅니다.

1. 포털의 왼쪽 위 모서리에서 **리소스 만들기**(+)를 선택합니다.

2. **데이터베이스** > **PostgreSQL용 Azure Database** 를 차례로 선택합니다.

    :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="메뉴의 Azure Database for PostgreSQL":::

3. **유연한 서버** 배포 옵션을 선택합니다.

   :::image type="content" source="./media/quickstart-create-database-portal/2-select-deployment-option.png" alt-text="Azure Database for PostgreSQL - 유연한 서버 배포 옵션 선택":::

4. 다음 정보로 **기본 사항** 양식을 입력합니다.

    :::image type="content" source="./media/quickstart-create-database-portal/3-create-basics.png" alt-text="서버 만들기":::

    설정|제안 값|Description
    ---|---|---
    Subscription|구독 이름|서버에 사용할 Azure 구독입니다. 구독이 여러 개인 경우 해당 리소스에 대해 요금이 청구되는 구독을 선택합니다.
    Resource group|*myresourcegroup*| 새 리소스 그룹 이름 또는 구독의 기존 이름입니다.
    워크로드 유형|기본 SKU 선택|개발(버스트 가능 SKU), 프로덕션 소규모/중간 규모(범용 SKU) 또는 프로덕션 대규모(메모리 최적화 SKU) 중에서 선택할 수 있습니다. ‘서버 구성’ 링크를 클릭하여 SKU 및 스토리지를 추가로 사용자 지정할 수 있습니다.
    가용성 영역|기본 설정 AZ|서버를 배포하려는 가용성 영역을 선택할 수 있습니다. 애플리케이션과 함께 배치하는 것이 유용합니다. ‘기본 설정 없음’을 선택하면 기본 AZ가 선택됩니다.
    고가용성|영역 중복 배포 사용| 이 옵션을 선택하면 기본 서버와 동일한 구성의 대기 서버는 같은 지역의 다른 가용성 영역에서 자동으로 프로비저닝됩니다. 참고: 고가용성 게시 서버 만들기도 사용하거나 사용하지 않도록 설정할 수 있습니다.
    서버 이름 |*mydemoserver-pg*|PostgreSQL 서버용 Azure Database를 식별하는 고유한 이름입니다. 사용자가 제공한 서버 이름에 *postgres.database.azure.com* 도메인 이름이 추가됩니다. 서버는 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-63자 이상이어야 합니다.
    관리자 사용자 이름 |*myadmin*| 서버에 연결할 경우 사용할 사용자 고유의 로그인 계정입니다. 관리자 로그인 이름은 **azure_superuser**, **azure_pg_admin**, **admin**, **administrator**, **root**, **guest** 또는 **public** 이 될 수 없습니다. **pg_** 로 시작할 수 없습니다.
    암호 |사용자 암호| 서버 관리자 계정의 새 암호입니다. 8-128자여야 합니다. 사용자 암호는 다음 범주 중 세 개의 문자를 포함해야 합니다. 영문 대문자, 영문 소문자, 숫자(0-9) 및 영숫자가 아닌 문자(!, $, #, % 등).
    위치|사용자와 가장 가까운 지역| 사용자에게 가장 가까운 위치입니다.
    버전|최신 주 버전| 다른 특정 요구 사항이 없는 한 최신 PostgreSQL 주 버전입니다.
    컴퓨팅 + 스토리지 | **범용**, **4 vCore**, **512GB**, **7일** | 새 서버에 대한 컴퓨팅, 스토리지 및 백업 구성입니다. **서버 구성** 을 선택합니다. *범용*, *4 vCore*, *512GB* 및 *7일* 은 **컴퓨팅 계층**, **vCore**, **스토리지** 및 **백업 보존 기간** 에 대한 기본값입니다. 해당 슬라이더를 그대로 두거나 조정할 수 있습니다. 이 가격 책정 계층 선택을 저장하려면 **확인** 을 선택합니다. 다음 스크린샷은 이러한 선택을 캡처한 것입니다.

    :::image type="content" source="./media/quickstart-create-database-portal/4-pricing-tier.png" alt-text="가격 책정 계층 창":::
    
5. 네트워킹 구성 옵션

    네트워크 탭에서 서버에 연결하는 방법을 선택할 수 있습니다. PostgreSQL용 Azure Database는 서버 수준에서 방화벽을 만듭니다. 특정 IP 주소에 대한 방화벽을 열기 위한 규칙을 만들지 않는 한, 이 방화벽은 외부 애플리케이션과 도구에서 서버 및 서버의 모든 데이터베이스에 연결하는 것을 방지합니다. 서버를 공개적으로 액세스할 수 있도록 설정 하는 것이 좋습니다.

    :::image type="content" source="./media/quickstart-create-database-portal/5-networking.png" alt-text="네트워킹 창":::

    그런 다음, 사용자 고유의 클라이언트 IP 주소로 제한합니다.

    :::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="현재 클라이언트 IP 주소 추가 선택":::

    프라이빗 액세스를 선택하는 경우 
6. **검토 + 만들기** 를 선택하여 선택 사항을 검토합니다. **만들기** 를 선택하여 서버를 프로비전합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.

7. 배포 프로세스를 모니터링하려면 도구 모음에서 **알림** 아이콘(벨)을 선택합니다. 배포가 완료되면 Azure Portal 대시보드에서 이 서버에 대한 타일을 서버의 **개요** 페이지에 대한 바로 가기로 만드는 **대시보드에 고정** 을 선택할 수 있습니다. **리소스로 이동** 옵션을 선택하면 서버의 **개요** 페이지가 열립니다.

    :::image type="content" source="./media/quickstart-create-database-portal/7-notifications.png" alt-text="알림 창":::

   기본적으로 **postgres** 데이터베이스가 서버 아래에 만들어집니다. [postgres](https://www.postgresql.org/docs/12/static/app-initdb.html) 데이터베이스는 사용자, 유틸리티 및 타사 애플리케이션에서 사용하는 기본 데이터베이스입니다. (다른 기본 데이터베이스는 **azure_maintenance** 입니다. 해당 기능은 사용자 작업으로부터 관리되는 서비스 프로세스를 구분하는 것입니다. 이 데이터베이스에 액세스할 수 없습니다.)

    > [!NOTE]
    > Azure Database for PostgreSQL 서버에 대한 연결은 포트 5432를 통해 통신합니다. 회사 네트워크 내에서 연결하려는 경우 5432 포트를 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 5432 포트를 열지 않으면 서버에 연결할 수 없습니다.
    >

## <a name="get-the-connection-information"></a>연결 정보 가져오기

PostgreSQL 서버용 Azure Database를 만들 때 **postgres** 라는 기본 데이터베이스도 만들어집니다. 데이터베이스 서버에 연결하려면 전체 서버 이름 및 관리자 로그인 자격 증명이 필요합니다. 빠른 시작 문서의 앞부분에서 이러한 값을 기록했을 수도 있습니다. 기록하지 않은 경우에는 포털의 서버 **개요** 페이지에서 서버 이름과 로그인 정보를 쉽게 찾을 수 있습니다.

서버의 **개요** 페이지를 엽니다. **서버 이름** 및 **서버 관리자 로그인 이름** 을 기록해 둡니다. 각 필드 위에 커서를 올려 놓으면 텍스트 오른쪽에 복사 기호가 나타납니다. 필요에 따라 복사 기호를 선택하여 값을 복사합니다.

 :::image type="content" source="./media/quickstart-create-database-portal/8-server-name.png" alt-text="서버 개요 페이지":::

## <a name="connect-to-the-postgresql-database-using-psql"></a>psql을 사용하여 PostgreSQL 데이터베이스에 연결

Azure Database for PostgreSQL 서버 연결하기 위해 사용할 수 있는 여러 애플리케이션이 있습니다. 클라이언트 컴퓨터에 PostgreSQL이 설치되어 있는 경우 [psql](https://www.postgresql.org/docs/current/static/app-psql.html) 로컬 인스턴스를 사용하여 Azure PostgreSQL 서버에 연결할 수 있습니다. 이제 psql 명령줄 유틸리티를 사용하여 Azure PostgreSQL 서버에 연결해 보겠습니다.

1. 다음 psql 명령을 실행하여 Azure Database for PostgreSQL 서버에 연결합니다.

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   예를 들어, 다음 명령은 액세스 자격 증명을 사용하여 **mydemoserver.postgres.database.azure.com** PostgreSQL 서버의 **postgres** 라는 기본 데이터베이스에 연결합니다. 암호를 묻는 메시지가 표시되면 선택한 `<server_admin_password>`를 입력합니다.
  
   ```bash
   psql --host=mydemoserver-pg.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   연결되면 psql 유틸리티에 sql 명령을 입력할 수 있는 postgres 프롬프트가 표시됩니다. 초기 연결 출력에서는 사용하는 psql이 Azure Database for PostgreSQL 서버와 다른 버전일 수 있으므로 경고가 표시될 수 있습니다.

   psql 출력의 예:

   ```bash
   psql (12.3 (Ubuntu 12.3-1.pgdg18.04+1), server 13.2)
   WARNING: psql major version 12, server major version 13.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>

   ```

   > [!TIP]
   > 방화벽이 클라이언트의 IP 주소를 허용하도록 구성되지 않은 경우 다음 오류가 발생합니다.
   >
   > "psql: 오류: 호스트 `<IP address>`, 사용자 "myadmin", 데이터베이스 "postgres"에 대한 pg_hba.conf 항목이 없습니다. SSL 설정 오류: SSL 연결이 필요합니다. SSL 옵션을 지정하고 다시 시도하세요.
   >
   > 클라이언트의 IP가 위의 방화벽 규칙 단계에서 허용되는지 확인합니다.

2. 프롬프트에서 다음 명령을 입력하여 "mypgsqldb"라는 빈 데이터베이스를 만듭니다.

    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. 프롬프트에서 다음 명령을 실행하여 새로 만든 **mypgsqldb** 데이터베이스에 대한 연결로 전환합니다.

    ```bash
    \c mypgsqldb
    ```

4. `\q`를 입력한 다음, Enter 키를 선택하여 psql을 종료합니다.

psql을 통해 Azure Database for PostgreSQL 서버에 연결하고, 빈 사용자 데이터베이스를 만들었습니다.

## <a name="clean-up-resources"></a>리소스 정리

빠른 시작에서 만든 리소스는 두 가지 방법 중 하나로 정리할 수 있습니다. 리소스 그룹의 모든 리소스를 포함하고 있는 Azure 리소스 그룹을 삭제할 수 있습니다. 다른 리소스를 그대로 유지하려면 해당 서버 리소스만 삭제합니다.

> [!TIP]
> 이 컬렉션의 다른 빠른 시작은 이 빠른 시작을 기반으로 하여 빌드됩니다. 빠른 시작을 계속 사용하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요. 계속하지 않으려는 경우 다음 단계에 따라 이 빠른 시작에서 만든 리소스를 포털에서 삭제합니다.

새로 만든 서버를 비롯하여 전체 리소스 그룹을 삭제하려면

1. 포털에서 리소스 그룹을 찾습니다. 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다. 그런 다음 리소스 그룹의 이름을 선택합니다(예: **myresourcegroup**).

2. 리소스 그룹 페이지에서 **삭제** 를 선택합니다. 텍스트 상자에 리소스 그룹의 이름(예: **myresourcegroup**)을 입력하여 삭제를 확인합니다. **삭제** 를 선택합니다.

새로 만든 서버를 삭제하려면

1. 포털에서 서버를 찾습니다(열려 있지 않은 경우). 왼쪽 메뉴에서 **모든 리소스** 를 선택합니다. 그런 다음, 만든 서버를 검색합니다.

2. **개요** 페이지에서 **삭제** 를 선택합니다.

    :::image type="content" source="./media/quickstart-create-database-portal/9-delete.png" alt-text="삭제 단추":::

3. 삭제하려는 서버 이름을 확인하고, 영향을 받는 데이터베이스를 봅니다. 텍스트 상자에서 서버 이름(예: **mydemoserver**)을 입력합니다. **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [App Service 및 PostgreSQL를 사용하여 Django 앱 배포](tutorial-django-app-service-postgres.md)
