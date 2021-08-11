---
title: SQL 데이터 동기화 문제 해결
description: Azure에서 SQL 데이터 동기화의 일반적인 문제를 식별하고 해결하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: sql-data-sync
ms.custom: data sync, sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: MaraSteiu
ms.author: masteiu
ms.reviewer: mathoma
ms.date: 12/20/2018
ms.openlocfilehash: dfd6ec28769898c596746685b3a154f17f03bd02
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110707720"
---
# <a name="troubleshoot-issues-with-sql-data-sync"></a>SQL 데이터 동기화 문제 해결
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 문서에서는 Azure에서 SQL 데이터 동기화의 알려진 문제를 해결하는 방법을 설명합니다. 문제에 대한 해결 방법이 있으면 여기에 제공됩니다.

SQL 데이터 동기화에 대한 개요는 [Azure의 SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스에서 데이터 동기화](sql-data-sync-data-sql-server-sql-database.md)를 참조하세요.

> [!IMPORTANT]
> SQL 데이터 동기화는 현재 Azure SQL Managed Instance를 지원하지 **않습니다**.

## <a name="sync-issues"></a>동기화 문제

- [클라이언트 에이전트와 연결된 온-프레미스 데이터베이스에 대한 포털 UI에서 동기화에 실패함](#sync-fails)

- [내 동기화 그룹이 처리 중 상태에서 중단됨](#sync-stuck)

- [테이블에 잘못된 데이터가 표시됨](#sync-baddata)

- [동기화에 성공한 후 일관성 없는 기본 키 데이터가 표시됨](#sync-pkdata)

- [성능이 크게 저하됨](#sync-perf)

- [“열에 NULL 값을 열에 삽입할 수 없습니다\<column>. 열에는 Null을 사용할 수 없습니다.”라는 오류 메시지를 받았습니다. 이 오류는 어떤 의미이며 오류를 수정할 수 있는 방법은 무엇인가요?](#sync-nulls)

- [데이터 동기화에서는 어떻게 순환 참조가 처리되나요? 즉, 여러 동기화 그룹에서 동일한 데이터가 동기화되어 결과적으로 계속 변경되는 경우 어떻게 처리되나요?](#sync-circ)

### <a name="sync-fails-in-the-portal-ui-for-on-premises-databases-that-are-associated-with-the-client-agent"></a><a name="sync-fails"></a> 클라이언트 에이전트와 연결된 온-프레미스 데이터베이스에 대한 포털 UI에서 동기화에 실패함

클라이언트 에이전트와 연결된 온-프레미스 데이터베이스에 대한 SQL 데이터 동기화 포털 UI에서 동기화에 실패합니다. 에이전트를 실행하는 로컬 컴퓨터에서 System.IO.IOException 오류가 이벤트 로그에 표시됩니다. 이 오류는 디스크 공간이 부족함을 나타냅니다.

- **원인**. 드라이브에 공간이 부족합니다.

- **해결 방법**. %TEMP% 디렉터리가 있는 드라이브에 공간을 만듭니다.

### <a name="my-sync-group-is-stuck-in-the-processing-state"></a><a name="sync-stuck"></a> 내 동기화 그룹이 처리 중 상태에서 중단됨

SQL 데이터 동기화의 동기화 그룹이 오랫동안 처리 중 상태입니다. 또한 **중지** 명령에 응답하지 않으며 로그에 새 항목이 없다고 표시됩니다.

다음 조건 중 하나로 인해 동기화 그룹이 처리 중 상태에서 중단될 수 있습니다.

- **원인**. 클라이언트 에이전트가 오프라인 상태입니다.

- **해결 방법**. 클라이언트 에이전트가 온라인 상태인지 확인한 다음 다시 시도합니다.

- **원인**. 클라이언트 에이전트가 설치되지 않았거나 누락되었습니다.

- **해결 방법**. 클라이언트 에이전트가 설치되지 않았거나 누락된 경우:

    1. SQL 데이터 동기화 설치 폴더에서 에이전트 XML 파일(있는 경우)을 제거합니다.
    1. 온-프레미스 컴퓨터(같거나 다른 컴퓨터일 수 있음)에 에이전트를 설치합니다. 그런 다음 오프라인으로 표시된 에이전트에 대해 포털에서 생성된 에이전트 키를 제출합니다.

- **원인**. SQL 데이터 동기화 서비스가 중지되었습니다.

- **해결 방법**. SQL 데이터 동기화 서비스를 다시 시작합니다.

    1. **시작** 메뉴에서 **서비스** 를 검색합니다.
    1. 검색 결과에서 **서비스** 선택합니다.
    1. **SQL 데이터 동기화** 서비스를 찾습니다.
    1. 서비스 상태가 **중지됨** 인 경우 서비스 이름을 마우스 오른쪽 단추로 클릭하고 **시작** 을 선택합니다.

> [!NOTE]
> 위의 정보대로 진행해도 동기화 그룹이 처리 중 상태에서 벗어나지 않을 경우 Microsoft 지원 서비스에서 동기화 그룹의 상태를 재설정해드릴 수 있습니다. 동기화 그룹 상태를 다시 설정하려면 [Azure SQL Database에 대한 Microsoft Q&A 질문 페이지](/answers/topics/azure-sql-database.html)에서 게시물을 만듭니다. 게시물에는 구독 ID 및 재설정해야 하는 그룹의 동기화 그룹 ID를 포함합니다. Microsoft 지원 엔지니어가 게시물에 응답하여 상태가 다시 설정되면 알려드립니다.

### <a name="i-see-erroneous-data-in-my-tables"></a><a name="sync-baddata"></a> 테이블에 잘못된 데이터가 표시됨

이름은 같지만 다른 데이터베이스 스키마에서 가져온 테이블을 동기화에 포함할 경우 동기화 후에 테이블에 잘못된 데이터가 표시됩니다.

- **원인**. SQL 데이터 동기화 프로비전 프로세스는 이름이 같지만 스키마가 서로 다른 테이블에 동일한 추적 테이블을 사용합니다. 이로 인해 두 테이블의 변경 내용이 동일한 추적 테이블에 반영됩니다. 이렇게 하면 동기화 중에 데이터가 이상하게 변경됩니다.

- **해결 방법**. 테이블이 데이터베이스의 서로 다른 스키마에 속해 있는 경우에도 동기화에 포함된 테이블의 이름이 서로 다르게 되어 있는지 확인합니다.

### <a name="i-see-inconsistent-primary-key-data-after-a-successful-sync"></a><a name="sync-pkdata"></a> 동기화에 성공한 후 일관성 없는 기본 키 데이터가 표시됨

동기화가 성공한 것으로 보고된 후 로그에 실패했거나 건너뛴 행이 없다고 표시되었는데, 기본 키 데이터가 동기화 그룹의 데이터베이스 간에 일치하지 않는 것으로 표시됩니다.

- **원인**. 이 결과는 의도적입니다. 기본 키 열의 변경 내용은 기본 키가 변경된 행의 데이터와 일치하지 않습니다.

- **해결 방법**. 이 문제를 방지하려면 기본 키에 있는 데이터가 변경되지 않도록 해야 합니다. 이 문제가 발생한 후 해결하려면 동기화 그룹의 모든 엔드포인트에서 일관되지 않은 데이터가 포함된 행을 삭제합니다. 그런 다음 행을 다시 삽입합니다.

### <a name="i-see-a-significant-degradation-in-performance"></a><a name="sync-perf"></a> 성능이 크게 저하됨

데이터 동기화 UI도 열 수 없을 정도로 성능이 크게 저하되었습니다.

- **원인**. 가장 가능성이 높은 원인은 동기화 루프입니다. 동기화 그룹 A의 동기화가 동기화 그룹 B의 동기화를 트리거한 다음, 동기화 그룹 A의 동기화를 다시 트리거하게 되면 동기화 루프가 발생합니다. 실제 상황은 더 복잡해질 수 있으며 루프에 3개 이상의 동기화 그룹이 포함될 수 있습니다. 이 문제는 서로 중첩된 동기화 그룹으로 인한 동기화의 순환 트리거가 있다는 것입니다.

- **해결 방법**. 최선의 해결책은 예방입니다. 동기화 그룹에 순환 참조가 포함되지 않은지 확인합니다. 하나의 동기화 그룹에 의해 동기화되는 행은 다른 동기화 그룹에서 동기화할 수 없습니다.

### <a name="i-see-this-message-cannot-insert-the-value-null-into-the-column-column-column-does-not-allow-nulls-what-does-this-mean-and-how-can-i-fix-it"></a><a name="sync-nulls"></a> “열에 NULL 값을 삽입할 수 없습니다\<column>. 열에는 Null을 사용할 수 없습니다."라는 오류 메시지를 받았습니다. 이 오류는 어떤 의미이며 오류를 수정할 수 있는 방법은 무엇인가요? 
이 오류 메시지는 다음 두 가지 문제 중 하나가 발생했음을 나타냅니다.
-  테이블에 기본 키가 없습니다. 이 문제를 해결하려면 동기화하는 모든 테이블에 기본 키를 추가합니다.
-  CREATE INDEX 문에 WHERE 절이 있습니다. 데이터 동기화는 이러한 상황을 처리하지 않습니다. 이 문제를 해결하려면 WHERE 절을 제거하거나 수동으로 모든 데이터베이스를 변경합니다. 
 
### <a name="how-does-data-sync-handle-circular-references-that-is-when-the-same-data-is-synced-in-multiple-sync-groups-and-keeps-changing-as-a-result"></a><a name="sync-circ"></a> 데이터 동기화에서는 어떻게 순환 참조가 처리되나요? 즉, 여러 동기화 그룹에서 동일한 데이터가 동기화되어 결과적으로 계속 변경되는 경우 어떻게 처리되나요?
데이터 동기화에서는 순환 참조가 처리되지 않습니다. 순환 참조가 발생하지 않도록 해야 합니다. 

## <a name="client-agent-issues"></a>클라이언트 에이전트 문제

클라이언트 에이전트에서 발생한 문제를 해결하려면 [데이터 동기화 에이전트 문제 해결](sql-data-sync-agent-overview.md#agent-tshoot)을 참조하세요.

## <a name="setup-and-maintenance-issues"></a>설치 및 유지 관리 문제

- [“디스크 공간 부족” 메시지가 표시됨](#setup-space)

- [동기화 그룹을 삭제할 수 없음](#setup-delete)

- [SQL Server 데이터베이스의 등록을 취소할 수 없음](#setup-unreg)

- [시스템 서비스를 시작할 수 있는 권한이 없음](#setup-perms)

- [데이터베이스가 “만료” 상태임](#setup-date)

- [동기화 그룹이 “만료” 상태임](#setup-date2)

- [에이전트 제거/중지 후 3분 이내에 동기화 그룹을 삭제할 수 없음](#setup-delete2)

- [손실되거나 손상된 데이터베이스를 복원할 경우 어떻게 되나요?](#setup-restore)

### <a name="i-get-a-disk-out-of-space-message"></a><a name="setup-space"></a> “디스크 공간 부족” 메시지가 표시됨

- **원인**. "디스크 공간 부족" 메시지는 남은 파일을 삭제해야 하는 경우에 나타날 수 있습니다. 이 문제는 바이러스 백신 소프트웨어에 의해 발생할 수도 있고, 파일이 열려 있는 상태에서 삭제 작업을 시도할 때도 발생할 수 있습니다.

- **해결 방법**. %temp% 폴더에 있는 동기화 파일을 수동으로 삭제합니다(`del \*sync\* /s`). 그런 다음 %temp% 폴더에 있는 하위 디렉터리를 삭제합니다.

> [!IMPORTANT]
> 동기화가 진행 중인 동안에는 어떤 파일도 삭제하지 않도록 합니다.

### <a name="i-cant-delete-my-sync-group"></a><a name="setup-delete"></a> 동기화 그룹을 삭제할 수 없음

동기화 그룹을 삭제하려고 했으나 실패했습니다. 다음 시나리오 중 하나가 동기화 그룹을 삭제하지 못한 원인일 수 있습니다.

- **원인**. 클라이언트 에이전트가 오프라인 상태입니다.

- **해결 방법**. 클라이언트 에이전트가 온라인 상태인지 확인한 다음 다시 시도합니다.

- **원인**. 클라이언트 에이전트가 설치되지 않았거나 누락되었습니다.

- **해결 방법**. 클라이언트 에이전트가 설치되지 않았거나 누락된 경우:  
    a. SQL 데이터 동기화 설치 폴더에서 에이전트 XML 파일(있는 경우)을 제거합니다.  
    b. 온-프레미스 컴퓨터(같거나 다른 컴퓨터일 수 있음)에 에이전트를 설치합니다. 그런 다음 오프라인으로 표시된 에이전트에 대해 포털에서 생성된 에이전트 키를 제출합니다.

- **원인**. 데이터베이스가 오프라인 상태입니다.

- **해결 방법**. 데이터베이스가 모두 온라인 상태인지 확인합니다.

- **원인**. 동기화 그룹이 프로비전 또는 동기화되는 중입니다.

- **해결 방법**. 프로비전 또는 동기화 프로세스가 완료될 때까지 기다린 후 동기화 그룹 삭제를 다시 시도합니다.

### <a name="i-cant-unregister-a-sql-server-database"></a><a name="setup-unreg"></a>SQL Server 데이터베이스의 등록을 취소할 수 없음

- **원인**. 대개 이미 삭제된 데이터베이스를 등록 취소하려고 시도하는 경우입니다.

- **해결 방법**. SQL Server 데이터베이스의 등록을 취소하려면 데이터베이스를 선택한 다음, **강제 삭제** 를 선택합니다.

  이 작업으로 동기화 그룹에서 데이터베이스를 제거하지 못하면 다음 작업을 수행합니다.

  1. 클라이언트 에이전트 호스트 서비스를 중지한 다음 다시 시작합니다.  
    a. **시작** 메뉴를 선택합니다.  
    b. 검색 상자에 **services.msc** 를 입력합니다.  
    다. 검색 결과 창의 **프로그램** 섹션에서 **서비스** 를 두 번 클릭합니다.  
    d. **SQL 데이터 동기화** 서비스를 마우스 오른쪽 단추로 클릭합니다.  
    e. 서비스가 실행 중인 경우 중지합니다.  
    f. 서비스를 마우스 오른쪽 단추로 클릭한 다음 **시작** 을 선택합니다.  
    g. 데이터베이스가 여전히 등록된 상태인지 여부를 확인합니다. 더 이상 등록되지 않은 경우 완료된 것입니다. 그렇지 않은 경우 다음 단계를 진행합니다.
  1. 클라이언트 에이전트 앱(SqlAzureDataSyncAgent)을 엽니다.
  1. **자격 증명 편집** 을 선택한 다음 데이터베이스에 대한 자격 증명을 입력합니다.
  1. 등록 취소를 진행합니다.

### <a name="i-dont-have-sufficient-privileges-to-start-system-services"></a><a name="setup-perms"></a> 시스템 서비스를 시작할 수 있는 권한이 없음

- **원인**. 이 오류는 두 가지 상황에서 발생합니다.
  -   사용자 이름 및/또는 암호가 올바르지 않는 경우
  -   지정된 사용자 계정에 서비스로 로그온할 수 있는 충분한 권한이 없는 경우

- **해결 방법**. 사용자 계정에 서비스로 로그온할 수 있는 자격 증명을 부여합니다.

  1. **시작** > **제어판** > **관리 도구** > **로컬 보안 정책** > **로컬 정책** > **사용자 권한 관리** 로 이동합니다.
  1. **서비스로 로그온** 을 선택합니다.
  1. **속성** 대화 상자에서 사용자 계정을 추가합니다.
  1. **적용** 을 선택한 다음 **확인** 을 선택합니다.
  1. 모든 창을 닫습니다.

### <a name="a-database-has-an-out-of-date-status"></a><a name="setup-date"></a> 데이터베이스가 “만료” 상태임

- **원인**. SQL 데이터 동기화는 45일 이상(데이터베이스가 오프라인 상태가 된 시점부터 계산) 오프라인 상태인 데이터베이스를 서비스에서 제거합니다. 데이터베이스가 45일 이상 오프라인 상태였다가 다시 온라인 상태가 된 경우 상태는 **만료** 입니다.

- **해결 방법**. 데이터베이스가 45일 이상 오프라인 상태가 되지 않도록 확인하여 **만료** 상태를 방지할 수 있습니다.

  데이터베이스 상태 **만료** 인 경우

  1. 동기화 그룹에서 **만료** 상태의 데이터베이스를 제거합니다.
  1. 데이터베이스를 다시 동기화 그룹에 추가합니다.

  > [!WARNING]
  > 이 데이터베이스가 오프라인 상태일 때의 모든 변경 내용은 손실 됩니다.

### <a name="a-sync-group-has-an-out-of-date-status"></a><a name="setup-date2"></a> 동기화 그룹이 “만료” 상태임

- **원인**. 하나 이상의 변경 내용을 45일의 전체 유지 기간 동안 적용하지 못한 경우 동기화 그룹이 만료될 수 있습니다.

- **해결 방법**. 동기화 그룹에 대해 **만료** 상태를 방지하려면 정기적으로 기록 뷰어에서 동기화 작업의 결과 확인합니다. 또한 적용되지 않은 모든 변경 내용을 조사하여 해결합니다.

  동기화 그룹의 상태가 **만료** 인 경우 동기화 그룹을 삭제하고 다시 만들어야 합니다.

### <a name="a-sync-group-cant-be-deleted-within-three-minutes-of-uninstalling-or-stopping-the-agent"></a><a name="setup-delete2"></a> 에이전트 제거/중지 후 3분 이내에 동기화 그룹을 삭제할 수 없음

동기화 그룹을 연결된 SQL 데이터 동기화 클라이언트 에이전트를 제거/중지한 후 3분 이내에 삭제할 수 없습니다.

- **해결 방법**.

  1. 연결된 동기화 에이전트가 온라인 상태일 때 동기화 그룹을 제거합니다(권장).
  1. 에이전트가 오프라인이지만 설치된 경우 온-프레미스 컴퓨터에서 온라인 상태로 전환합니다. 에이전트의 상태가 SQL 데이터 동기화 포털에서 **온라인** 으로 표시되기를 기다립니다. 그런 다음 동기화 그룹을 제거합니다.
  1. 에이전트가 제거되어서 오프라인 상태인 경우  
    a.  SQL 데이터 동기화 설치 폴더에서 에이전트 XML 파일(있는 경우)을 제거합니다.  
    b.  온-프레미스 컴퓨터(같거나 다른 컴퓨터일 수 있음)에 에이전트를 설치합니다. 그런 다음 오프라인으로 표시된 에이전트에 대해 포털에서 생성된 에이전트 키를 제출합니다.  
    다. 동기화 그룹을 삭제합니다.

### <a name="what-happens-when-i-restore-a-lost-or-corrupted-database"></a><a name="setup-restore"></a> 손실되거나 손상된 데이터베이스를 복원할 경우 어떻게 되나요?

손실되거나 손상된 데이터베이스를 백업에서 복원하면 데이터베이스가 속한 동기화 그룹에서 데이터의 일치성이 유지되지 않을 수 있습니다.

## <a name="next-steps"></a>다음 단계
SQL 데이터 동기화에 대한 자세한 내용은 다음 항목을 참조하세요.

-   개요 - [Azure의 SQL 데이터 동기화를 사용하여 여러 클라우드 및 온-프레미스 데이터베이스에서 데이터 동기화](sql-data-sync-data-sql-server-sql-database.md)
-   데이터 동기화 설정
    - 포털 - [자습서: Azure SQL Database와 SQL Server 간에 데이터를 동기화하도록 SQL 데이터 동기화 설정](sql-data-sync-sql-server-configure.md)
    - PowerShell 사용
        -  [PowerShell을 사용하여 Azure SQL Database의 여러 데이터베이스 간에 동기화](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [PowerShell을 사용하여 Azure SQL Database의 데이터베이스와 SQL Server 인스턴스의 데이터베이스 간에 동기화](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   데이터 동기화 에이전트 - [Azure의 SQL 데이터 동기화용 데이터 동기화 에이전트](sql-data-sync-agent-overview.md)
-   모범 사례 - [Azure의 SQL 데이터 동기화에 대한 모범 사례](sql-data-sync-best-practices.md)
-   모니터 - [Azure Monitor 로그를 사용하여 SQL 데이터 동기화 모니터링](./monitor-tune-overview.md)
-   동기화 스키마 업데이트
    -   Transact-SQL 사용 - [Azure의 SQL 데이터 동기화에서 스키마 변경 내용 복제 자동화](sql-data-sync-update-sync-schema.md)
    -   PowerShell 사용 - [PowerShell을 사용하여 기존 동기화 그룹의 동기화 스키마 업데이트](scripts/update-sync-schema-in-sync-group.md)

SQL Database에 대한 자세한 내용은 다음 항목을 참조하세요.

-   [SQL Database 개요](sql-database-paas-overview.md)
-   [데이터베이스 수명 주기 관리](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))