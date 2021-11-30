---
title: Azure Database for PostgreSQL - 유연한 서버에서 SCRAM을 사용한 연결
description: Azure Database for PostgreSQL - 유연한 서버에서 SCRAM을 사용하여 구성하고 연결하는 방법에 대한 지침 및 정보입니다.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/30/2021
ms.openlocfilehash: 1a05db53891e33d08ba9d89bc5c349d453c4cb99
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133268874"
---
# <a name="scram-authentication-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - 유연한 서버의 SCRAM 인증



SCRAM(솔트된 Challenge Response Authentication Mechanism)은 암호 기반 상호 인증 프로토콜입니다. 이는 여러 수준의 보안을 추가하고 신뢰할 수 없는 연결에서 암호 검색을 방지하는 챌린지-응답 체계입니다. SCRAM에서는 고급 보안을 제공하는 암호로 해시된 형식으로 서버에 암호를 저장할 수 있습니다. 

SCRAM 인증 방법을 사용하여 PostgreSQL 데이터베이스 서버에 액세스하려면 클라이언트 라이브러리가 SCRAM을 지원해야 합니다.  SCRAM을 지원하는 [드라이버 목록](https://wiki.postgresql.org/wiki/List_of_drivers)을 참조하세요.

## <a name="configuring-scram-authentication"></a>SCRAM 인증 구성

1. password_encryption을 SCRAM-SHA-256으로 변경합니다. 현재 PostgreSQL은 SHA-256을 사용하는 SCRAM만 지원합니다.
        :::image type="content" source="./media/how-to-configure-scram/1-password-encryption.png" alt-text="SCRAM 암호 암호화 사용"::: 
2. 인증 방법으로 SCRAM-SHA-256을 허용합니다.
        :::image type="content" source="./media/how-to-configure-scram/2-auth-method.png" alt-text="인증 방법 선택"::: 
    >[!Important]
    > SCRAM-SHA-256 메서드만 선택하여 SCRAM 전용 인증을 적용하도록 선택할 수 있습니다. 이렇게 하면 MD5 인증을 사용하는 사용자가 더 이상 서버에 연결할 수 없습니다. 따라서 SCRAM을 적용하기 전에 모든 사용자 암호를 SCRAM-SHA-256으로 업데이트할 때까지 MD5와 SCRAM-SHA-256을 인증 방법으로 사용하는 것이 좋습니다. #7단계에서 설명한 쿼리를 사용하여 사용자에 대한 인증 유형을 확인할 수 있습니다.
3. 변경 내용을 저장합니다. 이러한 속성은 동적 속성이며 서버를 다시 시작하지 않아도 됩니다.
4. Postgres 클라이언트에서 Postgres 서버에 연결합니다. 예제:
   
    ```bash
    psql "host=myPGServer.postgres.database.azure.com port=5432 dbname=postgres user=myDemoUser password=MyPassword sslmode=require"

    psql (12.3 (Ubuntu 12.3-1.pgdg18.04+1), server 12.6)
    SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
    Type "help" for help.
    ```

5. 암호 암호화를 확인합니다.
   
    ```SQL
    postgres=> show password_encryption;
     password_encryption
    ---------------------
    scram-sha-256
    (1 row)
    ```

6. 그런 다음, 사용자에 대한 암호를 업데이트할 수 있습니다.

    ```SQL
    postgres=> \password myDemoUser
    Enter new password:
    Enter it again:
    postgres=>
    ```

7. `azure_roles_authtype()` 함수를 사용하여 사용자 인증 유형을 확인할 수 있습니다. 

    ``` SQL
    postgres=> SELECT * from azure_roles_authtype();
            rolename          | authtype
    ---------------------------+-----------
    azuresu                   | NOLOGIN
    pg_monitor                | NOLOGIN
    pg_read_all_settings      | NOLOGIN
    pg_read_all_stats         | NOLOGIN
    pg_stat_scan_tables       | NOLOGIN
    pg_read_server_files      | NOLOGIN
    pg_write_server_files     | NOLOGIN
    pg_execute_server_program | NOLOGIN
    pg_signal_backend         | NOLOGIN
    replication               | NOLOGIN
    myDemoUser                | SCRAM-256
    azure_pg_admin            | NOLOGIN
    srtest                    | SCRAM-256
    sr_md5                    | MD5
    (14 rows)
    ```

8. 그런 다음, SCRAM 인증을 지원하는 클라이언트에서 서버에 연결할 수 있습니다.

> [!Note] 
> SCRAM 인증은 기본 제공 [관리되는 PgBouncer](concepts-pgbouncer.md)에 연결된 경우 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계
- [Azure CLI를 사용하여 Azure Database for PostgreSQL - 유연한 서버 가상 네트워크를 만들고 관리](./how-to-manage-virtual-network-cli.md)합니다.
- [Azure Database for PostgreSQL - 유연한 서버의 네트워킹](./concepts-networking.md)에 대해 자세히 알아보기
- [Azure Database for PostgreSQL - 유연한 서버 방화벽 규칙](./concepts-networking.md#public-access-allowed-ip-addresses)에 대해 자세히 알아보기
