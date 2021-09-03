---
title: 'Oracle에서 Azure SQL Database로 마이그레이션: 마이그레이션 가이드'
description: 이 가이드에서는 Oracle용 SQL Server Migration Assistant를 사용하여 Oracle 스키마를 Azure SQL Database로 마이그레이션하는 방법을 알아봅니다.
ms.service: sql-database
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: cawrites
ms.author: chadam
ms.date: 08/25/2020
ms.openlocfilehash: 0a6f7bca2e6a3a1f0ac10f868ddeffe72d082c28
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567158"
---
# <a name="migration-guide-oracle-to-azure-sql-database"></a>마이그레이션 가이드: Oracle에서 Azure SQL Database로 마이그레이션

[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

이 가이드에서는 Oracle용 SSMA(Oracle용 [SQL Server Migration](https://azure.microsoft.com/migration/sql-server/) Assistant)를 사용하여 Oracle 스키마를 Azure SQL Database로 [마이그레이션하는 방법](https://azure.microsoft.com/migration/migration-journey)을 알아봅니다.

다른 마이그레이션 가이드는 [Azure 데이터베이스 마이그레이션 가이드](/data-migration)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

Oracle 스키마를 SQL Database로 마이그레이션하기 전에 다음을 수행해야 합니다.

- 원본 환경이 지원되는지 확인합니다.
- [Oracle용 SSMA](https://www.microsoft.com/download/details.aspx?id=54258)를 다운로드합니다.
- 대상 [SQL Database](../../database/single-database-create-quickstart.md) 인스턴스가 있어야 합니다.
- [Oracle용 SSMA에 필요한 권한](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) 및 [공급자](/sql/ssma/oracle/connect-to-oracle-oracletosql)를 가져옵니다.

## <a name="pre-migration"></a>사전 마이그레이션

필수 구성 요소를 충족하면 사용자 환경의 토폴로지를 검색하고 [Azure 클라우드 마이그레이션](https://azure.microsoft.com/migration)의 적합성을 평가할 준비가 된 것입니다. 프로세스의 이 부분에서는 마이그레이션해야 하는 데이터베이스에 대한 인벤토리를 수행하고, 해당 데이터베이스에 잠재적인 마이그레이션 문제나 방해 요소가 있는지 평가한 다음, 발견했을 수 있는 모든 항목을 확인합니다.

### <a name="assess"></a>평가

Oracle용 SSMA를 사용하여 데이터베이스 개체 및 데이터를 검토하고, 마이그레이션할 데이터베이스를 평가하고, 데이터베이스 개체를 SQL Database로 마이그레이션한 다음, 마지막으로 데이터를 데이터베이스로 마이그레이션할 수 있습니다.

평가를 만들려면:

1. [Oracle용 SSMA](https://www.microsoft.com/download/details.aspx?id=54258)를 엽니다.
1. **파일** 을 선택하고 **새 프로젝트** 를 선택합니다.
1. 프로젝트 이름과 위치를 입력하여 프로젝트를 저장합니다. 그런 다음, 드롭다운 목록에서 **Azure SQL Database** 를 마이그레이션 대상으로 선택하고 **확인** 을 선택합니다.

   ![Oracle에 연결을 보여 주는 스크린샷](./media/oracle-to-sql-database-guide/connect-to-oracle.png)

1. **Oracle에 연결** 을 선택합니다. **Oracle에 연결** 대화 상자에서 Oracle 연결 세부 정보에 대한 값을 입력합니다.

1. 마이그레이션하려는 Oracle 스키마를 선택합니다.

   ![Oracle 스키마 선택을 보여 주는 스크린샷](./media/oracle-to-sql-database-guide/select-schema.png)

1. **Oracle 메타데이터 탐색기** 에서 마이그레이션할 Oracle 스키마를 마우스 오른쪽 단추로 클릭한 다음, **보고서 만들기** 를 선택하여 HTML 보고서를 생성합니다. 대신 데이터베이스를 선택한 다음, **보고서 만들기** 를 선택할 수 있습니다.

   ![보고서 만들기를 보여 주는 스크린샷](./media/oracle-to-sql-database-guide/create-report.png)

1. HTML 보고서를 검토하여 변환 통계와 오류 또는 경고를 파악합니다. Excel에서 보고서를 열어 Oracle 개체의 인벤토리와 스키마 변환을 수행하는 데 필요한 작업을 가져올 수도 있습니다. 보고서의 기본 위치는 SSMAProjects 내의 보고서 폴더에 있습니다.

   예제는 `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2020_11_12T02_47_55\`을 참조하세요.

   ![평가 보고서를 보여 주는 스크린샷](./media/oracle-to-sql-database-guide/assessment-report.png)

### <a name="validate-the-data-types"></a>데이터 형식의 유효성 검사

기본 데이터 형식 매핑의 유효성을 검사하고 필요한 경우 요구 사항에 따라 해당 매핑을 변경합니다. 이렇게 하려면 다음 단계를 따르십시오.

1. Oracle용 SSMA의 경우 **도구** 를 선택한 다음, **프로젝트 설정** 을 선택합니다.
1. **형식 매핑** 탭을 선택합니다.

   ![형식 매핑을 보여 주는 스크린샷](./media/oracle-to-sql-database-guide/type-mappings.png)

1. **Oracle 메타데이터 탐색기** 에서 테이블을 선택하여 각 테이블의 형식 매핑을 변경할 수 있습니다.

### <a name="convert-the-schema"></a>스키마 변환

스키마를 변환하려면:

1. (선택 사항) 동적 또는 임시 쿼리를 문에 추가합니다. 노드를 마우스 오른쪽 단추로 클릭한 다음, **문 추가** 를 선택합니다.
1. **Azure SQL Database에 연결** 탭을 선택합니다.
    1. **SQL Database** 에서 연결 정보를 입력하여 데이터베이스를 연결합니다.
    1. 드롭다운 목록에서 대상 SQL Database 인스턴스를 선택하거나 새 이름을 입력합니다. 이 경우 대상 서버에 데이터베이스가 생성됩니다.
    1. 인증 세부 정보를 입력한 다음, **연결** 을 선택합니다.

    ![Azure SQL Database에 대한 연결을 보여 주는 스크린샷](./media/oracle-to-sql-database-guide/connect-to-sql-database.png)

1. **Oracle 메타데이터 탐색기** 에서 Oracle 스키마를 마우스 오른쪽 단추로 클릭한 다음, **스키마 변환** 을 선택합니다. 또는 스키마를 선택한 다음, **스키마 변환** 탭을 선택할 수 있습니다.

   ![스키마 변환을 보여 주는 스크린샷](./media/oracle-to-sql-database-guide/convert-schema.png)

1. 변환이 완료된 후 변환된 개체와 원래 개체를 비교하고 검토하여 잠재적인 문제를 파악하고 권장 사항에 따라 해결합니다.

   ![검토 권장 사항 스키마를 보여 주는 스크린샷](./media/oracle-to-sql-database-guide/table-mapping.png)

1. 변환된 Transact-SQL 텍스트와 원래 저장 프로시저를 비교하고 권장 사항을 검토합니다.

   ![검토 권장 사항을 보여 주는 스크린샷](./media/oracle-to-sql-database-guide/procedure-comparison.png)

1. 출력 창에서 **결과 검토** 를 선택하고 **오류 목록** 창에서 오류를 검토합니다.
1. 오프라인 스키마 수정 연습을 위해 프로젝트를 로컬로 저장합니다. **파일** 메뉴에서 **프로젝트 저장** 을 선택합니다. 이 단계를 수행하면 스키마를 SQL Database에 게시하기 전에 원본 및 대상 스키마를 오프라인으로 평가하고 원하는 부분을 수정할 수 있습니다.

## <a name="migrate"></a>마이그레이션

데이터베이스를 평가하고 불일치를 해결한 다음에 수행할 단계는 마이그레이션 프로세스를 실행하는 것입니다. 마이그레이션에는 스키마 게시 및 데이터 마이그레이션의 두 단계가 포함됩니다.

스키마를 게시하고 데이터를 마이그레이션하려면 다음을 수행합니다.

1. **Azure SQL Database 메타데이터 탐색기** 의 **데이터베이스** 노드에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **데이터베이스와 동기화** 를 선택하여 스키마를 게시합니다.

   ![데이터베이스와의 동기화를 보여 주는 스크린샷](./media/oracle-to-sql-database-guide/synchronize-with-database.png)

1. 원본 프로젝트와 대상 간의 매핑을 검토합니다.

   ![데이터베이스와의 동기화 검토를 보여 주는 스크린샷](./media/oracle-to-sql-database-guide/synchronize-with-database-review.png)

1. **Oracle 메타데이터 탐색기** 에서 마이그레이션할 데이터베이스 또는 개체를 마우스 오른쪽 단추로 클릭하고 **데이터 마이그레이션** 을 선택하여 데이터를 마이그레이션합니다. 또는 **데이터 마이그레이션** 탭을 선택할 수 있습니다. 전체 데이터베이스에 대한 데이터를 마이그레이션하려면 데이터베이스 이름 옆의 확인란을 선택합니다. 개별 테이블에서 데이터를 마이그레이션하려면 데이터베이스를 확장하고 **테이블** 을 확장한 다음, 테이블 옆의 확인란을 선택합니다. 개별 테이블에서 데이터를 생략하려면 확인란의 선택을 취소합니다.

   ![데이터 마이그레이션을 보여 주는 스크린샷](./media/oracle-to-sql-database-guide/migrate-data.png)

1. Oracle 및 SQL Database에 대한 연결 세부 정보를 입력합니다.
1. 마이그레이션이 완료되면 **데이터 마이그레이션 보고서** 를 확인합니다.

   ![데이터 마이그레이션 보고서를 보여 주는 스크린샷](./media/oracle-to-sql-database-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)를 사용하여 SQL Database 인스턴스에 연결하고, 데이터 및 스키마를 검토하여 마이그레이션의 유효성을 검사합니다.

   ![SQL Server Management Studio의 유효성 검사를 보여 주는 스크린샷](./media/oracle-to-sql-database-guide/validate-data.png)

또는 SQL Server Integration Services를 사용하여 마이그레이션을 수행할 수도 있습니다. 자세한 내용은 다음을 참조하세요.

- [SQL Server Integration Services 시작](/sql/integration-services/sql-server-integration-services)
- [Azure용 SQL Server Integration Services 및 하이브리드 데이터 이동](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx)

## <a name="post-migration"></a>마이그레이션 후 작업

*마이그레이션* 단계를 성공적으로 완료한 후 모든 것이 최대한 원활하고 효율적으로 작동하게 하려면 일련의 마이그레이션 후 작업을 완료해야 합니다.

### <a name="remediate-applications"></a>애플리케이션 수정

데이터가 대상 환경으로 마이그레이션된 후 이전에 원본을 사용한 모든 애플리케이션이 대상 사용을 시작해야 합니다. 이 작업을 수행하려면 경우에 따라 애플리케이션을 변경해야 합니다.

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)은 Visual Studio Code의 확장으로, 이를 통해 Java 소스 코드를 분석하고 데이터 액세스 API 호출 및 쿼리를 검색할 수 있습니다. 이 도구 키트에서는 새 데이터베이스 백 엔드를 지원하기 위해 처리해야 하는 항목의 단일 창 보기를 제공합니다. 자세히 알아보려면 [Oracle에서 Java 애플리케이션 마이그레이션](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727) 블로그 게시물을 참조하세요.

### <a name="perform-tests"></a>테스트 수행

데이터베이스 마이그레이션의 테스트 접근 방식은 다음 작업으로 구성됩니다.

1. **유효성 검사 테스트 개발**: 데이터베이스 마이그레이션을 테스트하려면 SQL 쿼리를 사용해야 합니다. 원본 데이터베이스와 대상 데이터베이스 둘 다에서 실행할 유효성 검사 쿼리를 만들어야 합니다. 유효성 검사 쿼리는 정의한 범위를 포함해야 합니다.
1. **테스트 환경 설정**: 테스트 환경에는 원본 데이터베이스와 대상 데이터베이스의 복사본이 포함되어야 합니다. 테스트 환경을 격리해야 합니다.
1. **유효성 검사 테스트 실행**: 원본 및 대상에서 유효성 검사 테스트를 실행한 다음, 결과를 분석합니다.
1. **성능 테스트 실행**: 원본 및 대상에서 성능 테스트를 실행한 다음, 결과를 분석하고 비교합니다.

### <a name="validate-migrated-objects"></a>마이그레이션된 개체의 유효성 검사

Microsoft SSMA Tester(SQL Server Migration Assistant for Oracle Tester)를 사용하면 마이그레이션된 데이터베이스 개체를 테스트할 수 있습니다. SSMA Tester는 변환된 개체가 동일한 방식으로 동작하는지 확인하는 데 사용됩니다.

#### <a name="create-test-case"></a>테스트 사례 만들기

1. SSMA for Oracle을 열고 **Tester** 를 선택한 다음 **새 테스트 사례** 를 선택합니다.
   ![새 테스트 사례를 만드는 방법을 보여 주는 스크린샷.](./media/oracle-to-sql-database-guide/ssma-tester-new.png)

1. 새 테스트 사례에 대해 다음 정보를 제공합니다.

   **이름:** 테스트 사례를 식별할 이름을 입력합니다.

   **만든 날짜:** 자동으로 정의되는 오늘의 현재 날짜입니다.

   **최종 수정 날짜:** 자동으로 입력되며 변경하면 안 됩니다.

   **설명:** 테스트 사례의 목적을 식별하기 위한 추가 정보를 입력합니다.

   ![테스트 사례를 초기화하는 단계를 보여 주는 스크린샷.](./media/oracle-to-sql-database-guide/tester-init-test-case.png)

1. 왼쪽에 있는 Oracle 개체 트리에서 테스트 사례의 일부인 개체를 선택합니다.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-select-configure-objects.png" alt-text="개체 선택 및 구성 단계를 보여 주는 스크린샷.":::

   이 예에서는 저장 프로시저 `ADD_REGION` 및 테이블 `REGION`이 선택됩니다.

     자세한 내용은 [테스트할 개체 선택 및 구성](/sql/ssma/oracle/selecting-and-configuring-objects-to-test-oracletosql)을 참조하세요.

1. 그런 다음 왼쪽 창의 Oracle 개체 트리에서 테이블, 외래 키 및 기타 종속 개체를 선택합니다.

   :::image type="content" source="./media//oracle-to-sql-database-guide/tester-select-configure-affected.png" alt-text="영향을 받는 개체를 선택하고 구성하는 단계를 보여 주는 스크린샷.":::

   자세한 내용은 [영향을 받는 개체 선택 및 구성](/sql/ssma/oracle/selecting-and-configuring-affected-objects-oracletosql)을 참조하세요.

1. 개체의 평가 순서를 검토합니다. 그리드의 단추를 클릭하여 순서를 변경합니다.

   :::image type="content" source="./media/oracle-to-sql-database-guide/test-call-ordering.png" alt-text="테스트 개체 실행 순서를 보여 주는 스크린샷.":::

1. 이전 단계에서 제공된 정보를 검토하여 테스트 사례를 완료합니다. 테스트 시나리오를 기반으로 테스트 실행 옵션을 구성합니다.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-finalize-case.png" alt-text="개체를 완성하는 단계를 보여 주는 스크린샷.":::

   테스트 사례 설정에 대한 자세한 내용은 [테스트 사례 준비 완료](/sql/ssma/oracle/finishing-test-case-preparation-oracletosql)를 참조하세요.

1. 마침을 클릭하여 테스트 사례를 만듭니다.

   :::image type="content" source="./media//oracle-to-sql-database-guide/tester-test-repo.png" alt-text="리포지토리 테스트 단계를 보여 주는 스크린샷.":::

#### <a name="run-test-case"></a>테스트 사례 실행

SSMA Tester가 테스트 사례를 실행할 때 테스트 엔진은 테스트를 위해 선택된 개체를 실행하고 확인 보고서를 생성합니다.

1. 테스트 리포지토리에서 테스트 사례를 선택하고 실행을 클릭합니다.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-repo-run.png" alt-text="테스트 리포지토리를 검토하기 위해 표시되는 스크린샷.":::

1. 시작 테스트 사례를 검토하고 실행을 클릭합니다.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-run-test-case.png" alt-text="테스트 사례를 실행하는 단계를 보여 주는 스크린샷.":::

1. 다음으로 Oracle 원본 자격 증명을 제공합니다. 자격 증명을 입력한 후 연결을 클릭합니다.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-oracle-connect.png" alt-text="Oracle 원본에 연결하는 단계를 보여 주는 스크린샷.":::

1. 대상 SQL Server 자격 증명을 제공하고 연결을 클릭합니다.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-sql-connect.png" alt-text="SQL 대상에 연결하는 단계를 보여 주는 스크린샷.":::

   성공하면 테스트 사례가 초기화 단계로 이동합니다.

1. 실시간 진행률 표시줄은 테스트 실행의 실행 상태를 보여 줍니다.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-run-status.png" alt-text="테스터 테스트 진행 상황을 보여 주는 스크린샷.":::

1. 테스트가 완료된 후 보고서를 검토합니다. 보고서는 통계, 테스트 실행 중 오류 및 세부 보고서를 제공합니다.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-test-result.png" alt-text="샘플 테스터 테스트 보고서를 보여 주는 스크린샷.":::

1. 자세한 정보를 보려면 세부 정보를 클릭합니다.

   긍정적인 데이터 유효성 검사의 예.
   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-test-success.png" alt-text="샘플 테스터 성공 보고서를 보여 주는 스크린샷.":::

   실패한 데이터 유효성 검사의 예.

   :::image type="content" source="./media/oracle-to-sql-database-guide/tester-test-failed.png" alt-text="테스터 실패 보고서를 보여 주는 스크린샷.":::

### <a name="optimize"></a>최적화

마이그레이션 후 단계는 데이터 정확도 문제를 조정하고 완성도를 확인할 뿐만 아니라 워크로드 관련 성능 문제를 해결하는 데 매우 중요합니다.

> [!NOTE]
> 해당 문제와 이 문제를 완화하는 구체적인 단계에 대한 자세한 내용은 [마이그레이션 후 유효성 검사 및 최적화 가이드](/sql/relational-databases/post-migration-validation-and-optimization-guide)를 참조하세요.

## <a name="migration-assets"></a>마이그레이션 자산

이 마이그레이션 시나리오를 완료하는 방법에 대한 추가 지원은 다음 리소스를 참조하세요. 실제 마이그레이션 프로젝트 참여를 지원하기 위해 개발되었습니다.

| **제목/링크**                                                                                                                                          | **설명**                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Data Workload Assessment Model and Tool](https://www.microsoft.com/download/details.aspx?id=103130)(데이터 워크로드 평가 모델 및 도구) | 이 도구는 지정된 워크로드에 권장되는 “최적의” 대상 플랫폼, 클라우드 준비 상태, 애플리케이션 또는 데이터베이스 수정 수준을 제공합니다. 또한 자동화되고 균일한 대상 플랫폼 결정 프로세스를 제공하여 대규모 자산 평가를 가속화하는 데 도움이 되는 간단한 원클릭 계산 및 보고서 생성 기능을 제공합니다.                                                          |
| [Oracle Inventory Script Artifacts](https://www.microsoft.com/download/details.aspx?id=103121)(Oracle 인벤토리 스크립트 아티팩트)                 | 이 자산에는 Oracle 시스템 테이블에 적용되고 스키마 유형, 개체 유형 및 상태별로 개체 수를 제공하는 PL/SQL 쿼리가 포함됩니다. 또한 각 스키마의 대략적인 ‘원시 데이터’ 예측 및 각 스키마의 테이블 크기 조정을 CSV 형식으로 저장된 결과와 함께 제공합니다.                                                                                                               |
| [Automate SSMA Oracle Assessment Collection & Consolidation](https://www.microsoft.com/download/details.aspx?id=103120)(SSMA Oracle 평가 컬렉션 및 통합 자동화)                                             | 이 리소스 집합은 .csv 파일(프로젝트 폴더의 sources.csv)을 항목으로 사용하여 콘솔 모드에서 SSMA 평가를 실행하는 데 필요한 xml 파일을 생성합니다. source.csv는 기존 Oracle 인스턴스의 인벤토리를 기반으로 고객이 제공합니다. 출력 파일은 AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml 및 VariableValueFile.xml입니다.|
| [Oracle-SQL DB - 데이터베이스 비교 유틸리티](https://www.microsoft.com/download/details.aspx?id=103016)|Oracle 테스터용 SSMA는 데이터베이스 개체 변환 및 데이터 마이그레이션의 유효성을 자동으로 검사하는 권장 도구이며 데이터베이스 비교 기능의 상위 집합입니다.<br /><br />대체 데이터 유효성 검사 옵션을 원하는 경우 데이터베이스 비교 유틸리티를 사용하여 모든 또는 선택한 테이블, 행, 열의 행 또는 열 수준까지 데이터를 비교할 수 있습니다.|

데이터 SQL 엔지니어링 팀이 이러한 리소스를 개발했습니다. 이 팀의 핵심 선언은 Microsoft의 Azure 데이터 플랫폼으로의 데이터 플랫폼 마이그레이션 프로젝트에 대한 복잡한 현대화의 장애물을 제거하고 속도를 높이는 것입니다.

## <a name="next-steps"></a>다음 단계

- 다양한 데이터베이스 및 데이터 마이그레이션 시나리오와 특수 작업을 지원하는 데 사용할 수 있는 Microsoft와 타사의 서비스 및 도구 표는 [데이터 마이그레이션 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조하세요.

- SQL Database에 대해 자세히 알아보려면 다음을 참조하세요.
  - [Azure SQL Database 개요](../../database/sql-database-paas-overview.md)
  - [Azure TCO(총 소유 비용) 계산기](https://azure.microsoft.com/pricing/tco/calculator/)

- 클라우드 마이그레이션의 프레임워크 및 채택 주기에 대해 자세히 알아보려면 다음을 참조하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure로 마이그레이션하기 위한 워크로드의 비용 계산 및 크기 조정 모범 사례](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
   -  [클라우드 마이그레이션 리소스](https://azure.microsoft.com/migration/resources)

- 비디오 콘텐츠는 다음을 참조하세요.
    - [평가 및 마이그레이션을 수행하는 데 권장되는 도구/서비스 및 마이그레이션 과정에 대한 개요](https://azure.microsoft.com/resources/videos/overview-of-migration-and-recommended-tools-services/)
