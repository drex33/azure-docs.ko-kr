---
title: Azure SQL Database 및 Azure Synapse Analytics에 대한 Azure SQL 감사
description: Azure SQL Database 감사를 사용하여 데이터베이스 이벤트를 감사 로그로 추적합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: Rupp29
ms.author: arupp
ms.reviewer: vanto
ms.date: 08/25/2021
ms.custom: azure-synapse, sqldbrb=1
ms.openlocfilehash: 512f43c102aa40707170021ec1b1fd6372437d64
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793547"
---
# <a name="auditing-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL Database 및 Azure Synapse Analytics에 대한 감사
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

[Azure SQL Database](sql-database-paas-overview.md) 및 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 감사는 데이터베이스 이벤트를 추적하고 Azure Storage 계정, Log Analytics 작업 영역 또는 Event Hubs의 감사 로그에 이벤트를 기록합니다.

감사를 사용하면 다음과 같은 이점도 있습니다.

- 규정 준수를 유지 관리하고, 데이터베이스 활동을 이해하고, 비즈니스 문제나 보안 침해로 의심되는 사안을 나타낼 수 있는 불일치 및 이상을 파악하는 데 도움이 됩니다.

- 감사를 사용해도 규정 준수가 보장되는 것은 아니지만 규정 준수 표준을 쉽게 준수할 수 있습니다. 표준 규정 준수를 지원하는 Azure 프로그램에 대한 자세한 내용은 Azure SQL 규정 준수 인증의 최신 목록을 찾을 수 있는 [Azure 보안 센터](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)를 참조하세요.

> [!NOTE]
> Azure SQL Managed Instance 감사에 대한 자세한 내용은 [SQL Managed Instance 감사 시작하기](../managed-instance/auditing-configure.md) 문서를 참조하세요.

## <a name="overview"></a><a id="overview"></a>개요

SQL Database 감사를 사용하여 다음을 수행할 수 있습니다.

- **유지** 합니다. 감사할 데이터베이스 동작의 범주를 정의할 수 있습니다.
- **보고** 합니다. 미리 구성된 보고서 및 대시보드를 사용하여 활동 및 이벤트 보고를 빠르게 시작할 수 있습니다.
- **분석** 합니다. 의심스러운 이벤트, 특별한 활동 및 추세를 찾을 수 있습니다.

> [!IMPORTANT]
> Azure SQL Database, Azure Synapse 및 Azure SQL Managed Instance에 대한 감사는 가용성과 성능에 맞게 최적화되어 있습니다. 활동이 많거나 네트워크 부하가 많을 때는 Azure SQL Database, Azure Synapse 및 Azure SQL Managed Instance에서 작업을 계속 진행하도록 허용하지만, 일부 감사 이벤트를 기록하지 않을 수도 있습니다.

### <a name="auditing-limitations"></a>감사 제한 사항

- **프리미엄 스토리지** 는 현재 **지원되지 않습니다**.
- **Azure Data Lake Storage Gen2 스토리지 계정** 에 대한 **계층적 네임스페이스** 는 현재 **지원되지 않습니다**.
- 일시 중지된 **Azure Synapse** 에서 감사를 사용하도록 설정하는 것은 지원되지 않습니다. 감사를 사용하도록 설정하려면 Azure Synapse를 다시 시작합니다.
- **Azure Synapse SQL 풀** 에 대한 감사는 **기본 감사 작업 그룹만** 지원합니다.


#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>서버 수준 및 데이터베이스 수준 감사 정책 정의

감사 정책은 특정 데이터베이스에 대해 정의하거나 SQL Database 또는 Azure Synapse를 호스트하는 Azure의 기본 [서버](logical-servers.md) 정책으로 정의할 수 있습니다.

- 서버에 있는 모든 기존 데이터베이스 및 새로 만든 데이터베이스에 서버 정책이 적용됩니다.

- 서버 감사를 사용하는 경우 항상 데이터베이스에 적용됩니다. 데이터베이스 감사 설정에 관계없이 데이터베이스를 감사합니다.

- 감사 정책이 데이터베이스 수준에서 Log Analytics 작업 영역 또는 Event Hub 대상으로 정의된 경우 다음 작업은 원본 데이터베이스 수준 감사 정책을 유지하지 않습니다.
    - [데이터베이스 복사](database-copy.md)
    - [지정 시간 복원](recovery-using-backups.md)
    - [지역 복제](active-geo-replication-overview.md)(보조 데이터베이스에 데이터베이스 수준 감사가 포함되지 않음)

- 서버에서 활성화하는 것 외에도 데이터베이스에서 감사를 활성화하는 것은 서버 감사 설정을 재정의 또는 변경하지 않습니다. 두 감사는 함께 존재합니다. 즉, 데이터베이스는 동시에 두 번 감사됩니다(서버 정책에서 한 번, 데이터베이스 정책에서 한번).

   > [!NOTE]
   > 다음과 같은 경우가 아니면 서버 감사 및 데이터베이스 Blob 감사를 함께 활성화하지 않아야 합니다.
    >
    > - 특정 데이터베이스에 대해 다른 스토리지 계정, 보존 기간 또는 Log Analytics 작업 영역를 사용하려고 합니다.
    > - 서버의 나머지 데이터베이스와는 다른 특정 데이터베이스에 대해 이벤트 형식이나 범주를 감사하려 합니다. 예를 들어 특정 데이터베이스에 대해서만 감사해야 하는 테이블 삽입이 있을 수 있습니다.
   >
   > 그렇지 않으면 서버 수준 감사만 활성화하고 모든 데이터베이스에 대해 데이터베이스 수준 감사를 비활성화로 유지하는 것이 좋습니다.

#### <a name="remarks"></a>설명

- 감사 로그는 Azure 구독의 Azure Blob Storage에 있는 **Blob 추가** 에 기록됩니다.
- 감사 로그는 .xel 형식이므로 [SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms)를 사용하여 열 수 있습니다.
- 서버 또는 데이터베이스 수준 감사 이벤트에 대해 변경이 불가능한 로그 저장소를 구성하려면 [Azure Storage에서 제공하는 지침](../../storage/blobs/immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes)을 따르세요. 변경이 불가능한 Blog Storage를 구성할 때 **추가 허용** 을 선택했는지 확인하세요.
- VNet 또는 방화벽 뒤의 Azure Storage 계정에 감사 로그를 작성할 수 있습니다. 구체적인 지침은 [VNet 및 방화벽 뒤의 스토리지 계정에 감사 작성](audit-write-storage-account-behind-vnet-firewall.md)을 참조하세요.
- 로그 형식, 스토리지 폴더의 계층 구조 및 명명 규칙에 대한 자세한 내용은 [Blob 감사 로그 형식 참조](./audit-log-format.md)를 참조하세요.
- [읽기 전용 복제본](read-scale-out.md)에 대한 감사는 자동으로 설정됩니다. 스토리지 폴더의 계층 구조, 명명 규칙 및 로그 형식에 대한 자세한 내용은 [SQL Database 감사 로그 형식](audit-log-format.md)을 참조하세요.
- Azure AD 인증을 사용하는 경우 실패한 로그인 레코드가 SQL 감사 로그에 나타나지 않습니다. 실패한 로그인 감사 레코드를 보려면 이러한 이벤트의 세부 정보를 로깅하는 [Azure Active Directory 포털](../../active-directory/reports-monitoring/concept-sign-ins.md)을 방문해야 합니다.
- 로그인이 게이트웨이에 의해 데이터베이스가 있는 특정 인스턴스로 라우팅됩니다.  AAD 로그인의 경우 자격 증명이 확인된 후 해당 사용자로 요청된 데이터베이스에 로그인을 시도합니다.  오류가 발생하는 경우 요청된 데이터베이스는 액세스되지 않으므로 감사가 수행되지 않습니다.  SQL 로그인의 경우 요청된 데이터에서 자격 증명을 확인하므로 해당 사례를 감사할 수 있습니다.  데이터베이스에 연결된 성공적인 로그인은 두 경우 모두 감사됩니다.
- 감사 설정을 구성했으면 새로운 위협 감지 기능을 켜고, 보안 경고를 받을 전자 메일을 구성할 수 있습니다. 위협 감지를 사용하면 잠재적인 보안 위협을 나타낼 수 있는 비정상적인 데이터베이스 활동에 대해 사전 경고를 받을 수 있습니다. 자세한 내용은 [위협 감지 시작](threat-detection-overview.md)을 참조하세요.
- 감사를 사용하도록 설정된 데이터베이스를 다른 Azure SQL 논리 서버에 복사한 후 감사가 실패했음을 알리는 이메일을 받을 수 있습니다. 이는 알려진 문제이며 감사는 새로 복사된 데이터베이스에서 예상대로 작동해야 합니다.

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>서버에 대한 감사 설정

기본 감사 정책에는 모든 작업 및 다음과 같은 일련의 작업 그룹이 포함됩니다. 여기서는 성공 및 실패한 로그인뿐만 아니라 데이터베이스에 대해 실행된 모든 쿼리 및 저장 프로시저를 감사합니다.
  
- BATCH_COMPLETED_GROUP
- SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
- FAILED_DATABASE_AUTHENTICATION_GROUP
  
[Azure PowerShell을 사용하여 SQL Database 감사 관리](#manage-auditing) 섹션에 설명된 대로 PowerShell을 사용하여 다양한 형식의 작업 및 작업 그룹에 대한 감사를 구성할 수 있습니다.

Azure SQL Database 및 Azure Synapse 감사는 감사 레코드의 문자 필드에 대해 4,000자의 데이터를 저장합니다. 감사가 가능한 작업에서 반환된 **statement** 또는 **data_sensitivity_information** 값에 4000자가 넘게 포함되면 처음 4000자를 초과하는 문자는 **잘리고 감사되지 않습니다**.
다음 섹션에서는 Azure Portal을 사용하여 감사 구성을 설명합니다.

  > [!NOTE]
  > - 일시 중지된 전용 SQL 풀에서는 감사를 사용하도록 설정할 수 없습니다. 감사를 사용하도록 설정하려면 전용 SQL 풀의 일시 중지를 해제합니다. [전용 SQL 풀](../..//synapse-analytics/sql/best-practices-dedicated-sql-pool.md)에 대해 자세히 알아보세요.
  > - 감사가 Azure Portal 또는 PowerShell cmdlet을 통해 Log Analytics 작업 영역 또는 Event Hub 대상으로 구성되면 "SQLSecurityAuditEvents" 범주가 사용되는 [진단 설정](../../azure-monitor/essentials/diagnostic-settings.md)이 만들어집니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. **SQL 데이터베이스** 또는 **SQL 서버** 창의 보안 제목 아래에 있는 **감사** 로 이동합니다.
3. 서버 감사 정책을 설정하는 것을 선호하면 데이터베이스 감사 페이지에서 **서버 설정 보기** 링크를 선택할 수 있습니다. 그런 다음 서버 감사 설정을 보거나 수정할 수 있습니다. 서버 감사 정책은 이 서버의 모든 기존 및 새로 만든 데이터베이스에 적용됩니다.

    ![데이터베이스 감사 페이지에 강조 표시된 서버 설정 보기 링크를 보여 주는 스크린샷](./media/auditing-overview/2_auditing_get_started_server_inherit.png)

4. 데이터베이스 수준에서 감사를 사용하도록 설정하려면 **감사** 를 **켜짐** 으로 전환합니다. 서버 감사를 사용하는 경우 데이터베이스 구성 감사가 서버 감사와 나란히 존재합니다.

5. 감사 로그가 작성될 위치를 구성하기 위한 여러 가지 옵션이 있습니다. Azure Storage 계정, Azure Monitor 로그에서 사용하는 경우 Log Analytics 작업 영역 또는 이벤트 허브를 통해 사용하는 경우 이벤트 허브에 로그를 작성할 수 있습니다. 이러한 옵션을 조합하여 구성할 수 있으며, 감사 로그는 각각에 대해 작성됩니다.
  
   ![스토리지 옵션](./media/auditing-overview/auditing-select-destination.png)

### <a name="auditing-of-microsoft-support-operations"></a><a id="auditing-of-microsoft-support-operations"></a>Microsoft 지원 작업에 대한 감사

Azure SQL Server에 대한 Microsoft 지원 작업의 감사를 통해 지원 요청 중에 서버에 액세스해야 하는 경우 Microsoft 지원 엔지니어의 작업을 감사할 수 있습니다. 감사와 함께 이 기능을 사용하여 직원에게 더 많은 투명성을 제공하고 변칙 검색, 추세 시각화, 데이터 손실 방지를 지원합니다.

Microsoft 지원 작업의 감사를 사용하도록 설정하려면 Azure **SQL Server** 창의 보안 제목 아래에 있는 **감사** 로 이동하여 **Microsoft 지원 작업의 감사 이용** 을 **켜짐** 으로 전환합니다.

![Microsoft 지원 작업 스크린샷](./media/auditing-overview/support-operations.png)

Log Analytics 작업 영역에서 Microsoft 지원 작업의 감사 로그를 검토하려면 다음 쿼리를 사용합니다.

```kusto
AzureDiagnostics
| where Category == "DevOpsOperationsAudit"
```

이 감사 로그에 대해 다른 스토리지 대상을 선택하거나 서버에 대해 동일한 감사 구성을 사용할 수 있습니다.

:::image type="content" source="media/auditing-overview/auditing-support-operation-log-destination.png" alt-text="감사 지원 작업에 대한 감사 구성 스크린샷":::

### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>스토리지 대상에 감사 작성

스토리지 계정에 감사 로그를 작성하도록 구성하려면 **감사** 섹션으로 이동하여 **스토리지** 를 선택합니다. 로그를 저장할 Azure Storage 계정을 선택한 다음, **고급 속성** 을 열어서 보존 기간을 선택합니다. 그런 다음 **Save** 를 클릭합니다. 보존 기간 보다 오래된 로그는 삭제됩니다.

- 보존 기간의 기본값은 0(무제한 보존)입니다. 감사를 위해 스토리지 계정을 구성할 때 **고급 속성** 에서 **보존(일)** 슬라이더를 이동하여 이 값을 변경할 수 있습니다.
  - 보존 기간을 0(무제한 보존)에서 다른 값으로 변경하는 경우 보존 값이 변경된 후에 작성된 로그에만 보존이 적용됩니다(보존이 무제한으로 설정된 기간 동안 작성된 로그는 보존이 활성화된 후에도 보존됨).

  ![스토리지 계정 만들기](./media/auditing-overview/auditing_select_storage.png)

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>Log Analytics 대상에 감사 작성
  
Log Analytics 작업 영역에 감사 로그를 작성하도록 구성하려면 **Log Analytics** 를 선택하고 **Log Analytics 세부 정보** 를 엽니다. 로그를 작성할 Log Analytics 작업 영역을 선택한 다음, **확인** 을 클릭합니다. Log Analytics 작업 영역을 만들지 않았다면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../../azure-monitor/logs/quick-create-workspace.md)를 참조하세요.

   ![LogAnalyticsworkspace](./media/auditing-overview/auditing_select_oms.png)

Azure Monitor Log Analytics 작업 영역에 대한 자세한 내용은 [Azure Monitor 로그 배포 디자인](../../azure-monitor/logs/design-logs-deployment.md)을 참조하세요.
   
### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>이벤트 허브 대상에 감사 작성

이벤트 허브에 감사 로그 작성을 구성하려면 **Event Hub** 를 선택하세요. 로그가 작성될 이벤트 허브를 선택한 다음, **저장** 을 클릭합니다. 이벤트 허브는 데이터베이스 및 서버와 동일한 지역에 있어야 합니다.

   ![Eventhub](./media/auditing-overview/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>감사 로그 및 보고서 분석

Log Analytics에 감사 로그를 작성하도록 선택한 경우:

- [Azure Portal](https://portal.azure.com) 사용 관련 데이터베이스를 엽니다. 데이터베이스의 **감사** 페이지 맨 위에서 **감사 로그 보기** 를 선택합니다.

    ![감사 로그 보기](./media/auditing-overview/auditing-view-audit-logs.png)

- 그런 다음 두 가지 방법으로 로그를 볼 수 있습니다.

    **감사 레코드** 페이지의 맨 위에서 **Log Analytics** 를 클릭하면 시간 범위 및 검색 쿼리를 사용자 지정할 수 있는 Log Analytics 작업 영역에서 로그 보기가 열립니다.

    ![Log Analytics 작업 영역에서 열기](./media/auditing-overview/auditing-log-analytics.png)

    **감사 레코드** 페이지의 맨 위에 있는 **대시보드 보기** 를 클릭하면 감사 로그 정보를 표시하는 대시보드가 열립니다. 여기에서 보안 인사이트로 드릴다운하고 중요한 데이터에 액세스하는 등의 작업을 수행할 수 있습니다. 이 대시보드는 데이터에 대한 보안 인사이트를 얻는 데 도움이 되도록 설계되었습니다.
    시간 범위 및 검색 쿼리를 사용자 지정할 수도 있습니다.
    ![Log Analytics 대시보드 보기](media/auditing-overview/auditing-view-dashboard.png)

    ![Log Analytics 대시보드](media/auditing-overview/auditing-log-analytics-dashboard.png)

    ![Log Analytics 보안 인사이트](media/auditing-overview/auditing-log-analytics-dashboard-data.png)

- 또는 Log Analytics 블레이드에서 감사 로그에 액세스할 수도 있습니다. Log Analytics 작업 영역을 열고 **일반** 섹션 아래에서 **로그** 를 클릭합니다. *"SQLSecurityAuditEvents" 검색* 과 같은 간단한 쿼리를 시작하여 감사 로그를 볼 수 있습니다.
    여기서 [Azure Monitor 로그](../../azure-monitor/logs/log-query-overview.md)를 사용하여 감사 로그 데이터에서 고급 검색을 실행할 수도 있습니다. Azure Monitor 로그는 통합 검색 및 사용자 지정 대시보드를 사용하여 모든 워크로드 및 서버에서 수백만 개의 레코드를 쉽게 분석할 수 있는 실시간 운영 정보를 제공합니다. Azure Monitor 로그 검색 언어 및 명령에 대한 자세한 내용은 [Azure Monitor 로그 검색 참조](../../azure-monitor/logs/log-query-overview.md)를 참조하세요.

이벤트 허브에 감사 로그를 작성하도록 선택한 경우:

- 이벤트 허브에서 감사 로그 데이터를 사용하려면 이벤트를 사용하고 이벤트를 대상에 작성하도록 스트림을 설정해야 합니다. 자세한 내용은 [Azure Event Hubs 설명서](../index.yml)를 참조하세요.
- Event Hub의 감사 로그는 [Apache Avro](https://avro.apache.org/) 이벤트의 본문에서 캡처되고 UTF-8 인코딩을 통해 JSON 형식을 사용하여 저장됩니다. 감사 로그를 읽으려면 [Avro 도구](../../event-hubs/event-hubs-capture-overview.md#use-avro-tools) 또는 이 형식을 처리하는 유사한 도구를 사용할 수 있습니다.

Azure Storage 계정에 감사 로그를 작성하도록 선택한 경우 로그를 보는 데 사용할 수 있는 여러 가지 방법이 있습니다.

- 감사 로그는 설치 중에 선택한 계정에 집계됩니다. [Azure Storage Explorer](https://storageexplorer.com/) 등의 도구를 사용하여 감사 로그를 살펴볼 수 있습니다. Azure Storage에서 감사 로그는 **sqldbauditlogs** 라는 컨테이너 내부에 Blob 파일의 컬렉션으로 저장됩니다. 스토리지 폴더의 계층 구조, 명명 규칙 및 로그 형식에 대한 자세한 내용은 [SQL Database 감사 로그 형식](./audit-log-format.md)을 참조하세요.

- [Azure Portal](https://portal.azure.com) 사용  관련 데이터베이스를 엽니다. 데이터베이스의 **감사** 페이지 맨 위에서 **감사 로그 보기** 를 클릭합니다.

    ![감사 로그 보기](./media/auditing-overview/auditing-view-audit-logs.png)

    **감사 레코드** 가 열리고, 여기서 로그를 볼 수 있습니다.

  - **감사 레코드** 페이지의 맨 위에서 **필터** 를 클릭하여 특정 날짜를 볼 수 있습니다.
  - **감사 원본** 을 전환하여 *서버 감사 정책* 에서 생성된 감사 레코드와 *데이터베이스 감사 정책* 에서 생성된 감사 레코드 간을 전환할 수 있습니다.

       ![감사 레코드를 표시하는 옵션을 보여 주는 스크린샷]( ./media/auditing-overview/8_auditing_get_started_blob_audit_records.png)

- 시스템 함수 **sys.fn_get_audit_file**(T-SQL)을 사용하여 테이블 형식의 감사 로그 데이터를 반환할 수 있습니다. 이 함수 사용에 대한 자세한 내용은 [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)을 참조하세요.

- SQL Server Management Studio에서 **감사 파일 병합** 사용(SSMS 17부터 지원):
    1. SSMS 메뉴에서 **파일** > **열기** > **감사 파일 병합** 을 선택합니다.

        ![감사 파일 병합 메뉴 옵션을 보여 주는 스크린샷](./media/auditing-overview/9_auditing_get_started_ssms_1.png)
    2. **감사 파일 추가** 대화 상자가 열립니다. **추가** 옵션 중 하나를 선택하여 로컬 디스크에서 감사 파일을 병합할지 또는 Azure Storage에서 감사 파일을 가져올지 선택합니다 Azure Storage 세부 정보 및 계정 키를 제공해야 합니다.

    3. 병합할 모든 파일을 추가했으면 **확인** 을 클릭하여 병합 작업을 완료합니다.

    4. 병합된 파일이 SSMS에서 열립니다. 여기에서 파일을 보고 분석할 수 있을 뿐만 아니라 XEL 또는 CSV 파일이나 테이블로 내보낼 수 있습니다.

- Power BI를 사용합니다. Power BI에서 감사 로그 데이터를 보고 분석할 수 있습니다. 자세한 내용을 확인하고 다운로드할 수 있는 템플릿에 액세스하려면 [Power BI에서 감사 로그 데이터 분석](https://techcommunity.microsoft.com/t5/azure-database-support-blog/sql-azure-blob-auditing-basic-power-bi-dashboard/ba-p/368895)을 참조하세요.
- 포털을 통해 또는 [Azure Storage Explorer](https://storageexplorer.com/)와 같은 도구를 사용하여 Azure Storage Blob 컨테이너에서 로그 파일을 다운로드합니다.
  - 로그 파일을 로컬에 다운로드한 후에는 해당 파일을 두 번 클릭하여 열고, SSMS에서 로그를 살펴보고 분석합니다.
  - 또한 Azure Storage Explorer를 통해 동시에 여러 파일을 다운로드할 수 있습니다. 이렇게 하려면 특정 하위 폴더를 마우스 오른쪽 단추로 클릭하고 **다른 이름으로 저장** 을 선택하여 로컬 폴더에 저장합니다.

- 추가 방법:

  - 여러 파일 또는 로그 파일이 포함된 하위 폴더를 다운로드한 후 SSMS 감사 파일 병합 지침에 설명된 대로 파일을 로컬로 병합할 수 있습니다.
  - 프로그래밍 방식으로 Blob 감사 로그 보기: PowerShell을 사용하여 [확장 이벤트 파일을 쿼리](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)합니다.

## <a name="production-practices"></a><a id="production-practices"></a>프로덕션 사례


### <a name="auditing-geo-replicated-databases"></a>지역에서 복제된 데이터베이스 감사

지역 복제 데이터베이스에서 주 데이터베이스에 대해 감사를 활성화하면 보조 데이터베이스에도 동일한 감사 정책이 적용됩니다. 또한 주 데이터베이스와는 별도로 **보조 서버** 에서 감사를 활성화하여 보조 데이터베이스에 대한 감사를 설정할 수도 있습니다.

- 서버 수준(**권장**): **주 서버** 및 **보조 서버** 둘 다에서 감사를 켭니다. 주 데이터베이스 및 보조 데이터베이스가 해당하는 서버 수준 정책에 따라 독립적으로 감사됩니다.
- 데이터베이스 수준: 보조 데이터베이스에 대한 데이터베이스 수준 감사는 주 데이터베이스 감사 설정에서만 구성될 수 있습니다.
  - 감사는 서버가 아니라 *주 데이터베이스 자체* 에서 활성화해야 합니다.
  - 주 데이터베이스에서 감사가 활성화되면 보조 데이터베이스에서도 활성화됩니다.

    > [!IMPORTANT]
    > 데이터베이스 수준 감사에서 보조 데이터베이스의 스토리지 설정은 주 데이터베이스와 동일하기 때문에 지역 간 트래픽이 발생합니다. 서버 수준 감사만 활성화하고 모든 데이터베이스에 대해 데이터베이스 수준 감사를 비활성화로 유지하는 것이 좋습니다.

### <a name="storage-key-regeneration"></a>스토리지 키 다시 생성

프로덕션에서는 스토리지 키를 주기적으로 새로 고치게 됩니다. Azure Storage에 감사 로그를 작성하는 경우 키를 새로 고칠 때 감사 정책을 다시 저장해야 합니다. 프로세스는 다음과 같습니다.

1. **스토리지** 에서 **고급 속성** 을 엽니다. **스토리지 액세스 키** 상자에서 **보조** 를 선택합니다. 그런 다음, 감사 구성 페이지의 맨 위에서 **저장** 을 클릭합니다.

    ![보조 스토리지 액세스 키를 선택하는 프로세스를 보여 주는 스크린샷](./media/auditing-overview/5_auditing_get_started_storage_key_regeneration.png)
2. 스토리지 구성 패널로 이동하고 기본 액세스 키를 다시 생성합니다.

    ![탐색 창](./media/auditing-overview/6_auditing_get_started_regenerate_key.png)
3. 감사 구성 페이지로 다시 돌아가서 스토리지 액세스 키를 보조에서 기본으로 전환한 다음, **확인** 을 클릭합니다. 그런 다음, 감사 구성 페이지의 맨 위에서 **저장** 을 클릭합니다.
4. 스토리지 구성 페이지로 다시 돌아와서 보조 액세스 키를 다시 생성합니다(다음 키의 새로 고침 주기를 위한 준비).

## <a name="manage-azure-sql-database-auditing"></a><a id="manage-auditing"></a>Azure SQL Database 감사 관리

### <a name="using-azure-powershell"></a>Azure PowerShell 사용

**PowerShell cmdlet(추가 필터링을 위한 WHERE 절 지원 포함)**:

- [데이터베이스 감사 정책 만들기 또는 업데이트(Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [서버 감사 정책 만들기 또는 업데이트(Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [데이터베이스 감사 정책 가져오기(Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [서버 감사 정책 가져오기(Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [데이터베이스 감사 정책 제거(Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [서버 감사 정책 제거(Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

스크립트 예제는 [PowerShell을 사용하여 감사 및 위협 감지 구성](scripts/auditing-threat-detection-powershell-configure.md)을 참조하세요.

### <a name="using-rest-api"></a>REST API 사용

**REST API**:

- [데이터베이스 감사 정책 만들기 또는 업데이트](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [서버 감사 정책 만들기 또는 업데이트](/rest/api/sql/2017-03-01-preview/server-auditing-settings/create-or-update)
- [데이터베이스 감사 정책 가져오기](/rest/api/sql/database%20auditing%20settings/get)
- [서버 감사 정책 가져오기](/rest/api/sql/2017-03-01-preview/server-auditing-settings/get)

WHERE 절 지원을 사용하여 추가 필터링에 대해 확장된 정책입니다.

- [데이터베이스 *확장* 감사 정책 만들기 또는 업데이트](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [서버 *확장* 감사 정책 만들기 또는 업데이트](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [데이터베이스 확장 감사 정책 가져오기](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [서버 확장 감사 정책 가져오기](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-cli"></a>Azure CLI 사용

- [서버의 감사 정책 관리](/cli/azure/sql/server/audit-policy)
- [데이터베이스의 감사 정책 관리](/cli/azure/sql/db/audit-policy)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 사용

다음 예제에 나오는 대로 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 템플릿을 사용하여 Azure SQL Database 감사를 관리할 수 있습니다.

- [감사가 설정된 Azure SQL Database를 배포하여 Azure Blob Storage 계정에 감사 로그 쓰기](https://azure.microsoft.com/resources/templates/sql-auditing-server-policy-to-blob-storage/)
- [감사가 설정된 Azure SQL Database를 배포하여 Log Analytics에 감사 로그 쓰기](https://azure.microsoft.com/resources/templates/sql-auditing-server-policy-to-oms/)
- [감사가 설정된 Azure SQL Database를 배포하여 Event Hubs에 감사 로그 쓰기](https://azure.microsoft.com/resources/templates/sql-auditing-server-policy-to-eventhub/)

> [!NOTE]
> 연결된 샘플은 외부 공용 리포지토리에 있으며 보증 없이 '있는 그대로' 제공되며 Microsoft 지원 프로그램/서비스에서 지원되지 않습니다.

## <a name="see-also"></a>참고 항목

- 데이터 노출 에피소드 채널 9의 [Azure SQL 감사의 새로운 기능](https://channel9.msdn.com/Shows/Data-Exposed/Whats-New-in-Azure-SQL-Auditing).
- [SQL Managed Instance 감사](../managed-instance/auditing-configure.md)
- [SQL Server 감사](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)
