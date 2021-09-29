---
title: SQL Managed Instance 감사
description: T-SQL을 사용하여 Azure SQL Managed Instance 감사를 시작하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 06/21/2021
ms.openlocfilehash: 5505350cffbda3b84e800b34be7094818fdb125c
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129230592"
---
# <a name="get-started-with-azure-sql-managed-instance-auditing"></a>Azure SQL Managed Instance 감사 시작하기
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) 감사는 데이터베이스 이벤트를 추적하고 Azure Storage 계정의 감사 로그에 기록합니다. 감사를 사용하면 다음과 같은 이점도 있습니다.

- 규정 준수를 유지 관리하고, 데이터베이스 활동을 이해하고, 비즈니스 문제나 보안 침해로 의심되는 사안을 나타낼 수 있는 불일치 및 이상을 파악하는 데 도움이 됩니다.
- 감사를 사용해도 규정 준수가 보장되는 것은 아니지만 규정 준수 표준을 쉽게 준수할 수 있습니다. 표준 규정 준수를 지원하는 Azure 프로그램에 대한 자세한 내용은 규정 준수 인증의 최신 목록을 찾을 수 있는 [Azure 보안 센터](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)를 참조하세요.

> [!IMPORTANT]
> Azure SQL Database, Azure Synapse 및 Azure SQL Managed Instance에 대한 감사는 가용성과 성능에 맞게 최적화되어 있습니다. 활동이 많거나 네트워크 부하가 많을 때는 Azure SQL Database, Azure Synapse 및 Azure SQL Managed Instance에서 작업을 계속 진행하도록 허용하지만, 일부 감사 이벤트를 기록하지 않을 수도 있습니다.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Azure Storage로 서버에 대한 감사 설정

다음 섹션에서는 관리되는 인스턴스에 대한 감사 구성을 설명합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 감사 로그가 저장되는 Azure Storage **컨테이너** 를 만듭니다.

   1. 감사 로그를 저장할 Azure Storage 계정으로 이동합니다.

      > [!IMPORTANT]
      > - 동일한 지역의 스토리지 계정을 관리되는 인스턴스로 사용하여 지역 간 읽기/쓰기를 방지합니다. 
      > - 스토리지 계정이 Virtual Network 또는 방화벽으로 보호되는 경우 [가상 네트워크의 액세스 허가](../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network)를 참조하세요.
      > - 보존 기간을 0(무제한 보존)에서 다른 값으로 변경하는 경우 보존 값이 변경된 후에 작성된 로그에만 보존이 적용됩니다(보존이 무제한으로 설정된 기간 동안 작성된 로그는 보존이 활성화된 후에도 보존됨).

   1. 스토리지 계정에서 **개요** 로 이동한 다음, **Blob** 을 클릭합니다.

      ![Azure Blob 위젯](./media/auditing-configure/1_blobs_widget.png)

   1. 최상위 메뉴에서 **+ 컨테이너** 를 클릭하여 새 컨테이너를 만듭니다.

      ![Blob 컨테이너 만들기 아이콘](./media/auditing-configure/2_create_container_button.png)

   1. 컨테이너 **이름** 을 지정하고 **공용 액세스 수준** 을 **프라이빗** 으로 설정한 다음, **확인** 을 클릭합니다.

      ![Blob 컨테이너 구성 만들기](./media/auditing-configure/3_create_container_config.png)

    > [!IMPORTANT]
    > 서버 또는 데이터베이스 수준 감사 이벤트에 대해 변경이 불가능한 로그 저장소를 구성하려는 고객은 [Azure Storage에서 제공하는 지침](../../storage/blobs/immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes)을 따라야 합니다. (변경이 불가능한 Blob 스토리지를 구성할 때 **추가 허용** 을 선택했는지 확인하세요.)
  
3. 감사 로그용 컨테이너를 만든 후 이 컨테이너를 감사 로그의 대상으로 구성하려면 [T-SQL을 사용](#blobtsql)하거나 [SSMS(SQL Server Management Studio) UI를 사용](#blobssms)할 수 있습니다.

   - <a id="blobtsql"></a>**T-SQL을 사용하여 감사 로그용 Blob Storage 구성:**

     1. 컨테이너 목록에서 새로 만든 컨테이너를 클릭한 다음, **컨테이너 속성** 을 클릭합니다.

        ![Blob 컨테이너 속성 단추](./media/auditing-configure/4_container_properties_button.png)

     1. 복사 아이콘을 클릭하여 컨테이너 URL을 복사하고 나중에 사용할 수 있도록 메모장 등에서 URL을 저장합니다. 컨테이너 URL 형식은 `https://<StorageName>.blob.core.windows.net/<ContainerName>`이어야 합니다.

        ![Blob 컨테이너 URL 복사](./media/auditing-configure/5_container_copy_name.png)

     1. 스토리지 계정에 관리되는 인스턴스 감사 액세스 권한을 부여하기 위한 Azure Storage **SAS 토큰** 을 생성합니다.

        - 이전 단계에서 컨테이너를 만든 Azure Storage 계정으로 이동합니다.

        - **스토리지 설정** 메뉴에서 **공유 액세스 서명** 을 클릭합니다.

          ![스토리지 설정 메뉴의 공유 액세스 서명 아이콘](./media/auditing-configure/6_storage_settings_menu.png)

        - 다음과 같이 SAS를 구성합니다.

          - **허용된 서비스**: Blob

          - **시작 날짜**: 표준 시간대 관련 문제를 방지하기 위해 어제 날짜를 사용합니다.

          - **종료 날짜**: 이 SAS 토큰이 만료되는 날짜를 선택합니다.

            > [!NOTE]
            > 감사 오류를 방지하려면 만료 시 토큰을 갱신합니다.

          - **SAS 생성** 을 클릭합니다.

            ![SAS 구성](./media/auditing-configure/7_sas_configure.png)

        - SAS 토큰은 아래쪽에 표시됩니다. 복사 아이콘을 클릭하여 토큰을 복사하고 나중에 사용할 수 있도록 메모장 등에서 토큰을 저장합니다.

          ![SAS 토큰 복사](./media/auditing-configure/8_sas_copy.png)

          > [!IMPORTANT]
          > 토큰 시작 부분에서 물음표(“?”) 문자를 제거합니다.

     1. SQL Server Management Studio 또는 기타 지원되는 도구를 통해 관리되는 인스턴스에 연결합니다.

     1. 다음 T-SQL 문을 실행하여 이전 단계에서 만든 컨테이너 URL 및 SAS 토큰으로 **새 자격 증명을 만듭니다**.

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. 다음 T-SQL 문을 실행하여 새 서버 감사를 만듭니다(고유한 감사 이름을 선택하고 이전 단계에서 만든 컨테이너 URL을 사용). 지정하지 않으면 `RETENTION_DAYS` 기본값은 0(무제한 보존)입니다.

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' , RETENTION_DAYS =  integer )
        GO
        ```

     1. [서버 감사 사양 또는 데이터베이스 감사 사양](#createspec)을 만들어 계속 진행합니다.

   - <a id="blobssms"></a>**SQL Server Management Studio 18을 사용하여 감사 로그용 Blob Storage를 구성합니다.**

     1. SQL Server Management Studio UI를 사용하여 관리되는 인스턴스에 연결합니다.

     1. 개체 탐색기의 루트 노드를 확장합니다.

     1. **보안** 노드를 확장하고 마우스 오른쪽 단추로 **감사** 노드를 클릭한 후 **새 감사** 를 클릭합니다.

        ![보안 및 감사 노드 확장](./media/auditing-configure/10_mi_SSMS_new_audit.png)

     1. **감사 대상** 에서 **URL** 이 선택되어 있는지 확인하고 **찾아보기** 를 클릭합니다.

        ![Azure Storage 찾아보기](./media/auditing-configure/11_mi_SSMS_audit_browse.png)

     1. (선택 사항) Azure 계정에 로그인합니다.

        ![Azure에 로그인](./media/auditing-configure/12_mi_SSMS_sign_in_to_azure.png)

     1. 드롭다운에서 구독, 스토리지 계정 및 Blob 컨테이너를 선택하거나 **만들기** 를 클릭하여 고유한 컨테이너를 만듭니다. 완료되면 **확인** 을 클릭합니다.

        ![Azure 구독, 스토리지 계정 및 BLOB 컨테이너 선택](./media/auditing-configure/13_mi_SSMS_select_subscription_account_container.png)

     1. **감사 만들기** 대화 상자에서 **확인** 을 클릭합니다.
     
        > [!NOTE]
        > SQL Server Management Studio UI를 사용하여 감사를 만드는 경우 SAS 키가 있는 컨테이너에 대한 자격 증명이 자동으로 만들어집니다. 
     
     1.  <a id="createspec"></a>Blob 컨테이너를 감사 로그의 대상으로 구성한 후에는 SQL Server와 마찬가지로 서버 감사 사양 또는 데이터베이스 감사 사양을 만들고 사용하도록 설정합니다.

   - [서버 감사 사양 만들기 T-SQL 가이드](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [데이터베이스 감사 사양 만들기 T-SQL 가이드](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. 다음과 같이 3단계에서 만든 서버 감사를 사용하도록 설정합니다.

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

추가 정보는 다음을 참조하세요.

- [Azure SQL Managed Instance와 SQL Server 간의 데이터베이스 감사 차이점](#auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server)
- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hubs-or-azure-monitor-logs"></a>서버 감사를 Event Hubs 또는 Azure Monitor 로그로 설정

관리되는 인스턴스의 감사 로그를 Azure Event Hubs 또는 Azure Monitor 로그로 보낼 수 있습니다. 이 섹션에서는 이렇게 구성하는 방법을 설명합니다.

1. [Azure Portal](https://portal.azure.com/)에서 관리되는 인스턴스로 이동합니다.

2. **진단 설정** 을 클릭합니다.

3. **진단 켜기** 를 클릭합니다. 진단을 이미 사용하도록 설정한 경우 **+ 진단 설정 추가** 가 대신 표시됩니다.

4. 로그 목록에서 **SQLSecurityAuditEvents** 를 선택합니다.

5. 감사 이벤트의 대상으로 Event Hubs, Azure Monitor 로그 또는 둘 다 선택합니다. 각 대상에 대해 필수 매개 변수(예: Log Analytics 작업 영역)를 구성합니다.

6. **저장** 을 클릭합니다.

    ![진단 설정 구성](./media/auditing-configure/9_mi_configure_diagnostics.png)

7. **SSMS(SQL Server Management Studio)** 또는 기타 지원되는 클라이언트를 사용하여 관리되는 인스턴스에 연결합니다.

8. 다음 T-SQL 문을 실행하여 서버 감사를 만듭니다.

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. SQL Server와 마찬가지로 서버 감사 사양 또는 데이터베이스 감사 사양을 만들고 사용하도록 설정합니다.

   - [서버 감사 사양 만들기 T-SQL 가이드](/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [데이터베이스 감사 사양 만들기 T-SQL 가이드](/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. 다음과 같이 8단계에서 만든 서버 감사를 사용하도록 설정합니다.

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>감사 로그 사용

### <a name="consume-logs-stored-in-azure-storage"></a>Azure Storage에 저장된 로그 사용

여러 방법으로 Blob 감사 로그를 볼 수 있습니다.

- 시스템 함수 `sys.fn_get_audit_file`(T-SQL)을 사용하여 테이블 형식의 감사 로그 데이터를 반환할 수 있습니다. 이 함수 사용에 대한 자세한 내용은 [sys.fn_get_audit_file 설명서](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)를 참조하세요.

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) 등의 도구를 사용하여 감사 로그를 살펴볼 수 있습니다. Azure Storage에서 감사 로그는 감사 로그를 저장하도록 정의된 컨테이너 내부에 Blob 파일 컬렉션으로 저장됩니다. 스토리지 폴더의 계층 구조, 명명 규칙 및 로그 형식에 대한 자세한 내용은 [BLOB 감사 로그 형식 참조](../database/audit-log-format.md)를 참조하세요.

- 감사 로그 사용 방법의 전체 목록은 [Azure SQL Database 감사 시작하기](../../azure-sql/database/auditing-overview.md)를 참조하세요.

### <a name="consume-logs-stored-in-event-hubs"></a>Event Hubs에 저장된 로그 사용

Event Hubs에서 감사 로그 데이터를 사용하려면 이벤트를 사용하고 대상에 작성하도록 스트림을 설정해야 합니다. 자세한 내용은 Azure Event Hubs 설명서를 참조하세요.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Azure Monitor 로그에 저장된 로그 사용 및 분석

감사 로그가 Azure Monitor 로그에 기록되는 경우 Log Analytics 작업 영역에서 사용할 수 있으며 감사 데이터에 대한 고급 검색을 실행할 수 있습니다. 시작점으로 Log Analytics 작업 영역으로 이동합니다. **일반** 섹션 아래에서 **로그** 를 클릭하고 간단한 쿼리(예: `search "SQLSecurityAuditEvents"`)를 입력하여 감사 로그를 봅니다.  

Azure Monitor 로그는 통합 검색 및 사용자 지정 대시보드를 사용하여 모든 워크로드 및 서버에서 수백만 개의 레코드를 쉽게 분석할 수 있는 실시간 운영 인사이트를 제공합니다. Azure Monitor 로그 검색 언어 및 명령에 대한 자세한 내용은 [Azure Monitor 로그 검색 참조](../../azure-monitor/logs/log-query-overview.md)에서 확인할 수 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server"></a>Azure SQL Managed Instance 내 데이터베이스와 SQL Server 내 데이터베이스 간의 감사 차이점

Azure SQL Managed Instance 내 데이터베이스와 SQL Server 내 데이터베이스에서 감사 간의 주요 차이점은 다음과 같습니다.

- Azure SQL Managed Instance에서는 감사가 서버 수준에서 작동하며 Azure Blob Storage에 `.xel` 로그 파일을 저장합니다.
- SQL Server에서 감사는 서버 수준에서 작동 하지만, 파일 시스템 및 Windows 이벤트 로그에 이벤트를 저장 합니다.

관리되는 인스턴스의 XEvent 감사는 Azure Blob Storage 대상을 지원합니다. 파일 및 Windows 로그는 **지원 되지 않습니다**.

Azure Blob Storage에 대한 감사에서 `CREATE AUDIT` 구문의 주요 차이점은 다음과 같습니다.

- 새 구문 `TO URL`을 제공하고 `.xel` 파일이 배치되는 Azure Blob Storage 컨테이너의 URL을 지정할 수 있습니다.
- Event Hubs 및 Azure Monitor 로그 대상을 사용하도록 설정할 수 있는 새 구문 `TO EXTERNAL MONITOR`를 제공합니다.
- Azure SQL Managed Instance는 Windows 파일 공유에 액세스할 수 없으므로 `TO FILE` 구문은 지원되지 **않습니다**.
- 종료 옵션은 **지원되지 않습니다**.
- `queue_delay` 0은 **지원되지 않습니다**.

## <a name="next-steps"></a>다음 단계

- 감사 로그 사용 방법의 전체 목록은 [Azure SQL Database 감사 시작하기](../../azure-sql/database/auditing-overview.md)를 참조하세요.
- 표준 규정 준수를 지원하는 Azure 프로그램에 대한 자세한 내용은 규정 준수 인증의 최신 목록을 찾을 수 있는 [Azure 보안 센터](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)를 참조하세요.

<!--Image references-->
