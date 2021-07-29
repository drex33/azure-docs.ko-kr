---
title: 'Oracle에서 Azure Virtual Machines의 SQL Server로: 마이그레이션 가이드'
description: 이 가이드에서는 Oracle용 SQL Server Migration Assistant를 사용하여 Oracle 스키마를 Azure Virtual Machines의 SQL Server로 마이그레이션하는 방법을 설명합니다.
ms.service: virtual-machines-sql
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: cawrites
ms.date: 11/06/2020
ms.openlocfilehash: d7c315694b5b25597abdacce74813b3578682f89
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111972056"
---
# <a name="migration-guide-oracle-to-sql-server-on-azure-virtual-machines"></a>마이그레이션 가이드: Oracle에서 Azure Virtual Machines의 SQL Server로
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqldb.md)]

이 가이드에서는 Oracle용 SQL Server Migration Assistant를 사용하여 Oracle 스키마를 Azure Virtual Machines의 SQL Server로 마이그레이션하는 방법을 설명합니다. 

다른 마이그레이션 가이드는 [데이터베이스 마이그레이션](/data-migration)을 참조하세요. 

## <a name="prerequisites"></a>필수 조건 

Oracle 스키마를 Azure Virtual Machines의 SQL Server로 마이그레이션하려면 다음이 필요합니다.

- 지원되는 원본 환경.
- [Oracle용 SSMA(SQL Server Migration Assistant)](https://www.microsoft.com/en-us/download/details.aspx?id=54258)
- 대상 [SQL Server VM](../../virtual-machines/windows/sql-vm-create-portal-quickstart.md).
- [Oracle용 SSMA에 필요한 권한](/sql/ssma/oracle/connecting-to-oracle-database-oracletosql) 및 [공급자](/sql/ssma/oracle/connect-to-oracle-oracletosql).
- 원본과 대상 모두에 액세스하려면 연결이 보장되어야 하며 충분한 권한이 필요합니다. 


## <a name="pre-migration"></a>사전 마이그레이션

클라우드로의 마이그레이션을 준비할 때는 원본 환경이 지원되는지와 필수 구성 요소를 모두 충족했는지 확인합니다. 이렇게 하면 효율적이고 성공적인 마이그레이션이 보장됩니다.

프로세스의 이 부분에는 다음이 포함됩니다. 
- 마이그레이션해야 하는 데이터베이스의 인벤토리를 수행합니다.
- 잠재적인 마이그레이션 문제나 차단 요인이 있는지 해당 데이터베이스 평가. 
- 발견한 문제 해결. 

### <a name="discover"></a>검색

[MAP 도구 키트](https://go.microsoft.com/fwlink/?LinkID=316883)를 사용하여 비즈니스에서 사용 중인 기능에 관한 세부 정보와 기존 데이터 원본을 식별합니다. 그러면 마이그레이션을 더 확실히 파악하고 계획하는 데 도움을 줄 수 있습니다. 이 프로세스에서는 네트워크를 스캔하여 조직의 Oracle 인스턴스와 사용 중인 버전과 기능을 파악합니다.

MAP 도구 키트를 사용하여 인벤토리 스캔을 수행하려면 다음 단계를 수행합니다. 


1. [MAP 도구 키트](https://go.microsoft.com/fwlink/?LinkID=316883)를 엽니다.


1. **데이터베이스 만들기/선택** 을 선택합니다.

   ![데이터베이스 만들기/선택 옵션을 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/select-database.png)

1. **인벤토리 데이터베이스 만들기** 를 선택합니다. 만들려는 새 인벤토리 데이터베이스의 이름을 입력하고 간략한 설명을 제공한 다음, **확인** 을 선택합니다. 

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/create-inventory-database.png" alt-text="인벤토리 데이터베이스를 만들기 위한 인터페이스를 보여 주는 스크린샷.":::

1. **인벤토리 데이터 수집** 을 선택하여 **Inventory and Assessment Wizard**(인벤토리 및 평가 마법사)를 엽니다.

   :::image type="content" source="media/oracle-to-sql-on-azure-vm-guide/collect-inventory-data.png" alt-text="인벤토리 데이터 수집 링크를 보여 주는 스크린샷.":::


1. **인벤토리 및 평가 마법사** 에서 **Oracle** 을 선택하고 **다음** 을 선택합니다.

   ![인벤토리 및 평가 마법사의 인벤토리 시나리오 페이지를 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/choose-oracle.png)

1. 비즈니스 요구 사항 및 환경에 가장 적합한 컴퓨터 검색 옵션을 선택하고 **다음** 을 선택합니다. 

   ![인벤토리 및 평가 마법사의 검색 방법 페이지를 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/choose-search-option.png)

1. 검색하려는 시스템에 대해 자격 증명을 입력하거나 새 자격 증명을 만들고 **다음** 을 선택합니다.

    ![인벤토리 및 평가 마법사의 모든 컴퓨터 자격 증명 페이지를 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/choose-credentials.png)


1. 자격 증명의 순서를 설정하고 **다음** 을 선택합니다. 

   ![인벤토리 및 평가 마법사의 자격 증명 순서 페이지를 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/set-credential-order.png)  


1. 검색하려는 각 컴퓨터의 자격 증명을 입력합니다. 모든 컴퓨터/머신에 고유한 자격 증명을 사용하거나 모든 컴퓨터 자격 증명 목록을 사용할 수 있습니다.  

   ![인벤토리 및 평가 마법사의 컴퓨터와 자격 증명 지정 페이지를 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/specify-credentials-for-each-computer.png)


1. 선택 내용을 확인한 다음 **완료** 를 선택합니다.

   ![인벤토리 및 평가 마법사의 요약 페이지를 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/review-summary.png)


1. 스캔이 완료되면 **데이터 컬렉션** 요약을 확인합니다. 검색은 몇 분 정도 걸릴 수 있으며, 데이터베이스 수에 따라 달라집니다. 완료되면 **닫기** 를 선택합니다. 

   ![데이터 수집 요약을 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/collection-summary-report.png)


1. Oracle 평가 및 데이터베이스 세부 정보에 대한 보고서를 생성하려면 **옵션** 을 선택합니다. 두 옵션을 모두(한 번에 하나씩) 선택하여 보고서를 생성합니다.


### <a name="assess"></a>평가

데이터 원본을 식별한 다음 [Oracle용 SQL Server Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=54258)를 사용하여 SQL Server VM으로 마이그레이션할 Oracle 인스턴스를 평가합니다. 도우미를 사용하면 원본과 대상 데이터베이스 간의 차이를 파악할 수 있습니다. 데이터베이스 개체와 데이터를 검토하고, 마이그레이션할 데이터베이스를 평가하며, 데이터베이스 개체를 SQL Server로 마이그레이션한 다음, 데이터를 SQL Server로 마이그레이션할 수 있습니다.

평가를 만들려면 다음 단계를 따릅니다. 


1. [Oracle용 SQL Server Migration Assistant](https://www.microsoft.com/en-us/download/details.aspx?id=54258)를 엽니다. 
1. **파일** 메뉴에서 **새 프로젝트** 를 선택합니다. 
1. 프로젝트 이름과 프로젝트의 위치를 제공한 다음, 목록에서 SQL Server 마이그레이션 대상을 선택합니다. **확인** 을 선택합니다. 

   ![새 프로젝트 대화 상자를 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/new-project.png)


1. **Oracle에 연결** 을 선택합니다. **Oracle에 연결** 대화 상자에서 Oracle 연결 값을 입력합니다.

   ![Oracle에 연결 대화 상자를 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-oracle.png)

   마이그레이션하려는 Oracle 스키마를 선택합니다. 

   ![마이그레이션할 수 있는 Oracle 스키마 목록을 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/select-schema.png)


1. **Oracle 메타데이터 탐색기** 에서 마이그레이션할 Oracle 스키마를 마우스 오른쪽 단추로 클릭한 다음, **보고서 만들기** 를 선택합니다. 그러면 HTML 보고서가 생성됩니다. 또는 데이터베이스를 선택한 다음, 맨 위 메뉴에서 **보고서 만들기** 를 선택할 수 있습니다.

   ![보고서를 만드는 방법을 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/create-report.png)

1. 변환 통계, 오류, 경고에 관한 HTML 보고서를 검토합니다. 보고서를 분석하여 변환 문제와 해결 방법을 파악합니다.

    Excel에서 보고서를 열어 Oracle 개체의 인벤토리와 스키마 변환을 완료하는 데 필요한 작업을 가져올 수도 있습니다. 보고서의 기본 위치는 SSMAProjects 내의 보고서 폴더입니다. 

   예: `drive:\<username>\Documents\SSMAProjects\MyOracleMigration\report\report_2016_11_12T02_47_55\`


   ![변환 보고서를 보여 주는 스크린샷](./media/oracle-to-sql-on-azure-vm-guide/conversion-report.png)


### <a name="validate-data-types"></a>데이터 형식의 유효성 검사

기본 데이터 형식 매핑의 유효성을 검사하고 필요한 경우 요구 사항에 따라 해당 매핑을 변경합니다. 이렇게 하려면 다음 단계를 따르십시오. 


1. **도구** 메뉴에서 **프로젝트 설정** 을 선택합니다. 
1. **형식 매핑** 탭을 선택합니다. 

   ![형식 매핑 탭을 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/type-mappings.png)

1. **Oracle 메타데이터 탐색기** 에서 테이블을 선택하여 각 테이블의 형식 매핑을 변경할 수 있습니다. 

### <a name="convert-the-schema"></a>스키마 변환

스키마를 변환하려면 다음 단계를 따릅니다. 

1. (선택 사항) 동적 쿼리나 임시 쿼리를 변환하려면 노드를 마우스 오른쪽 단추로 클릭하고 **문 추가** 를 선택합니다.

1. 맨 위 메뉴에서 **SQL Server에 연결** 을 선택합니다. 
     1. Azure VM에서 SQL Server의 연결 세부 정보를 입력합니다. 
     1. 목록에서 대상 데이터베이스를 선택하거나 새 이름을 입력합니다. 새 이름을 제공하면 대상 서버에 데이터베이스가 생성됩니다. 
     1. 인증 세부 정보를 제공합니다. 
     1. **연결** 을 선택합니다. 


   ![SQL Server에 연결하는 방법을 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/connect-to-sql-vm.png)

1. **Oracle 메타데이터 탐색기** 에서 Oracle 스키마를 마우스 오른쪽 단추로 클릭하고 **스키마 변환** 을 선택합니다. 또는 맨 위 메뉴에서 **스키마 변환** 을 선택할 수 있습니다.

   ![스키마를 변환하는 방법을 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/convert-schema.png)


1. 스키마 변환이 완료된 후 변환된 개체를 검토하고 원본 개체와 비교하여 잠재적인 문제를 식별합니다. 권장 사항을 사용하여 문제를 해결합니다.

   ![두 스키마를 비교한 결과를 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/table-mapping.png)

   변환된 Transact-SQL 텍스트와 원래 저장 프로시저를 비교하고 권장 사항을 검토합니다. 

   ![Transact-SQL, 저장 프로시저, 경고를 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/procedure-comparison.png)

   오프라인 스키마 수정 연습을 위해 프로젝트를 로컬에 저장할 수 있습니다. 이렇게 하려면 **파일** 메뉴에서 **프로젝트 저장** 을 선택합니다. 프로젝트를 로컬에 저장하면 스키마를 SQL Server에 게시하기 전에 원본과 대상 스키마를 오프라인으로 평가하고 원하는 부분을 수정할 수 있습니다.

1. **출력** 창에서 **결과 검토** 를 선택한 다음, **오류 목록** 창에서 오류를 검토합니다. 
1. 오프라인 스키마 수정 연습을 위해 프로젝트를 로컬로 저장합니다. **파일** 메뉴에서 **프로젝트 저장** 을 선택합니다. 이렇게 하면 스키마를 Azure Virtual Machines의 SQL Server에 게시하기 전에 원본 및 대상 스키마를 오프라인으로 평가하고 원하는 부분을 수정할 수 있습니다.


## <a name="migrate"></a>마이그레이션

필요한 필수 조건을 준비하고 ‘마이그레이션 전’ 단계와 관련된 작업을 완료했으면 스키마 및 데이터 마이그레이션을 시작할 준비가 된 것입니다. 마이그레이션에는 스키마 게시 및 데이터 마이그레이션의 두 단계가 포함됩니다. 


스키마를 게시하고 데이터를 마이그레이션하려면 다음 단계를 따릅니다. 

1. 스키마 게시: **SQL Server 메타데이터 탐색기** 에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **데이터베이스와 동기화** 를 선택합니다. 그러면 Azure Virtual Machines의 SQL Server에 Oracle 스키마를 게시합니다. 

   ![데이터베이스와 동기화 명령을 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database.png)

   원본 프로젝트와 대상 간의 매핑을 검토합니다.

   ![동기화 상태를 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/synchronize-database-review.png)



1. 데이터 마이그레이션: **Oracle 메타데이터 탐색기** 에서 마이그레이션할 데이터베이스 또는 개체를 마우스 오른쪽 단추로 클릭하고 **데이터 마이그레이션** 을 선택합니다. 또는 맨 위 메뉴에서 **데이터 마이그레이션** 을 선택할 수 있습니다.

   전체 데이터베이스의 데이터를 마이그레이션하려면 데이터베이스 이름 옆의 확인란을 선택합니다. 개별 테이블에서 데이터를 마이그레이션하려면 데이터베이스를 확장하고 **테이블** 을 확장한 다음, 테이블 옆의 확인란을 선택합니다. 개별 테이블에서 데이터를 생략하려면 확인란의 선택을 취소합니다.

   ![데이터 마이그레이션 명령을 보여 주는 스크린샷](./media/oracle-to-sql-on-azure-vm-guide/migrate-data.png)

1. 대화 상자에서 Azure Virtual Machines의 Oracle과 SQL Server에 대한 연결 세부 정보를 제공합니다.
1. 마이그레이션이 완료되면 **데이터 마이그레이션 보고서** 를 확인합니다.

    ![데이터 마이그레이션 보고서를 보여 주는 스크린샷](./media/oracle-to-sql-on-azure-vm-guide/data-migration-report.png)

1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)를 사용하여 Azure Virtual Machines 인스턴스에서 SQL Server에 연결합니다. 데이터 및 스키마를 검토하여 마이그레이션을 검증합니다.


   ![SSMA의 SQL Server 인스턴스를 보여 주는 스크린샷.](./media/oracle-to-sql-on-azure-vm-guide/validate-in-ssms.png)

SSMA를 사용하는 대신, SSIS(SQL Server Integration Services)를 사용하여 데이터를 마이그레이션할 수 있습니다. 자세한 내용은 다음을 참조하세요. 
- [SQL Server Integration Services](//sql/integration-services/sql-server-integration-services) 문서.
- [Azure용 SSIS 및 하이브리드 데이터 이동](https://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/SSIS%20Hybrid%20and%20Azure.docx) 백서.


## <a name="post-migration"></a>마이그레이션 후 작업 

마이그레이션 스테이지를 완료한 다음, 모든 것이 최대한 원활하고 효율적으로 실행되게 하려면 일련의 마이그레이션 후 작업을 완료해야 합니다.

### <a name="remediate-applications"></a>애플리케이션 수정

데이터가 대상 환경으로 마이그레이션된 후 이전에 원본을 사용한 모든 애플리케이션이 대상 사용을 시작해야 합니다. 이처럼 변경하려면 애플리케이션을 변경해야 할 수 있습니다.

[Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)은 Visual Studio Code의 확장입니다. Java 소스 코드를 분석하고 데이터 액세스 API 호출과 쿼리를 검색하는 데 사용할 수 있습니다. 이 도구 키트에서는 새 데이터베이스 백 엔드를 지원하기 위해 처리해야 하는 항목의 단일 창 보기를 제공합니다. 자세한 정보는 [Oracle에서 Java 애플리케이션 마이그레이션](https://techcommunity.microsoft.com/t5/microsoft-data-migration/migrate-your-java-applications-from-oracle-to-sql-server-with/ba-p/368727)을 참조하세요. 

### <a name="perform-tests"></a>테스트 수행

데이터베이스 마이그레이션을 테스트하려면 다음 작업을 완료합니다.

1. **유효성 검사 테스트 개발**. 데이터베이스 마이그레이션을 테스트하려면 SQL 쿼리를 사용해야 합니다. 원본 데이터베이스와 대상 데이터베이스 둘 다에서 실행할 유효성 검사 쿼리를 만듭니다. 유효성 검사 쿼리는 정의한 범위를 포함해야 합니다.

2. **테스트 환경 설정**. 테스트 환경에는 원본 데이터베이스와 대상 데이터베이스의 복사본이 포함되어야 합니다. 테스트 환경을 격리해야 합니다.

3. **유효성 검사 테스트 실행**. 원본 및 대상에서 유효성 검사 테스트를 실행한 다음, 결과를 분석합니다.

4. **성능 테스트 실행**. 원본 및 대상에서 성능 테스트를 실행한 다음, 결과를 분석하고 비교합니다.

### <a name="optimize"></a>최적화

마이그레이션 후 단계는 데이터 정확도 문제를 조정하고 완결성을 확인하는 데 중요합니다. 워크로드와 관련된 성능 이슈를 해결하는 데도 중요합니다.

> [!Note]
> 해당 문제와 문제를 완화하는 구체적인 단계에 관한 자세한 내용은 [마이그레이션 후 유효성 검사 및 최적화 가이드](/sql/relational-databases/post-migration-validation-and-optimization-guide)를 참조하세요.


## <a name="migration-resources"></a>마이그레이션 리소스 

이 마이그레이션 시나리오를 완료하는 데 추가 지원이 필요하면 실제 마이그레이션 프로젝트를 지원하도록 개발된 다음 리소스를 참조하세요.

| **제목/링크**                                                                                                                                          | **설명**                                                                                                                                                                                                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Data Workload Assessment Model and Tool](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)(데이터 워크로드 평가 모델 및 도구) | 이 도구는 지정된 워크로드에 권장되는 최적의 대상 플랫폼, 클라우드 준비 상태 및 애플리케이션/데이터베이스 수정 수준을 제공합니다. 또한 자동화되고 균일한 대상 플랫폼 결정 프로세스를 제공하여 대규모 자산 평가를 가속화하는 데 도움이 되는 간단한 원클릭 계산 및 보고서 생성 기능을 제공합니다.                                                          |
| [Oracle Inventory Script Artifacts](https://github.com/Microsoft/DataMigrationTeam/tree/master/Oracle%20Inventory%20Script%20Artifacts)(Oracle 인벤토리 스크립트 아티팩트)                 | 이 자산에는 Oracle 시스템 테이블을 대상으로 하고 스키마 형식, 개체 형식 및 상태별로 개체 수를 제공하는 PL/SQL 쿼리가 포함됩니다. 또한 각 스키마의 대략적인 ‘원시 데이터’ 예측 및 각 스키마의 테이블 크기 조정을 CSV 형식으로 저장된 결과와 함께 제공합니다.                                                                                                               |
| [Automate SSMA Oracle Assessment Collection & Consolidation](https://github.com/microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/Automate%20SSMA%20Oracle%20Assessment%20Collection%20%26%20Consolidation)(SSMA Oracle 평가 컬렉션 및 통합 자동화)                                             | 이 리소스 집합은 .csv 파일(프로젝트 폴더의 sources.csv)을 항목으로 사용하여 콘솔 모드에서 SSMA 평가를 실행하는 데 필요한 XML 파일을 생성합니다. 기존 Oracle 인스턴스의 인벤토리를 가져와 source.csv 파일을 제공합니다. 출력 파일은 AssessmentReportGeneration_source_1.xml, ServersConnectionFile.xml 및 VariableValueFile.xml입니다.|
| [Oracle 데이터베이스 마이그레이션 시 SSMA 문제와 가능한 해결 방법](https://aka.ms/dmj-wp-ssma-oracle-errors)                                                           | Oracle을 사용하면 WHERE 절에서 스칼라가 아닌 조건을 할당할 수 있습니다. SQL Server에서는 해당 형식의 조건을 지원하지 않습니다. 따라서 Oracle용 SSMA는 WHERE절에 스칼라가 아닌 조건이 있는 쿼리를 변환하지 않습니다. 대신 O2SS0001 오류를 생성합니다. 이 백서에서는 이 문제에 관한 자세한 정보와 해결 방법을 제공합니다.          |
| [Oracle to SQL Server Migration Handbook](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20SQL%20Server%20Migration%20Handbook.pdf)(Oracle에서 SQL Server로 마이그레이션 안내서)                | 이 문서에서는 Oracle 스키마를 최신 버전의 SQL Server로 마이그레이션하는 것과 관련된 작업을 집중적으로 설명합니다. 마이그레이션에서 기능을 변경해야 하면 각 변경 사항이 데이터베이스를 사용하는 애플리케이션에 미칠 수 있는 영향을 신중하게 고려해야 합니다.                                                     |
|[Oracle-SQL Server - 데이터베이스 비교 유틸리티](https://www.microsoft.com/download/details.aspx?id=103016)|Oracle 테스터용 SSMA는 데이터베이스 개체 변환 및 데이터 마이그레이션의 유효성을 자동으로 검사하는 권장 도구이며 데이터베이스 비교 기능의 상위 집합입니다.<br /><br />대체 데이터 유효성 검사 옵션을 원하는 경우 데이터베이스 비교 유틸리티를 사용하여 모든 또는 선택한 테이블, 행, 열의 행 또는 열 수준까지 데이터를 비교할 수 있습니다.|


데이터 SQL 엔지니어링 팀이 이러한 리소스를 개발했습니다. 이 팀의 핵심 선언은 Microsoft의 Azure 데이터 플랫폼으로의 데이터 플랫폼 마이그레이션 프로젝트에 대한 복잡한 현대화의 장애물을 제거하고 속도를 높이는 것입니다.


## <a name="next-steps"></a>다음 단계

- SQL Server에 적용할 수 있는 서비스의 가용성을 확인하려면 [Azure 글로벌 인프라 센터](https://azure.microsoft.com/global-infrastructure/services/?regions=all&amp;products=synapse-analytics,virtual-machines,sql-database)를 참조하세요.

- 다양한 데이터베이스 및 데이터 마이그레이션 시나리오와 특수 작업을 지원하는 데 사용할 수 있는 Microsoft와 타사의 서비스 및 도구 테이블은 [데이터 마이그레이션 서비스 및 도구](../../../dms/dms-tools-matrix.md)를 참조하세요.

- Azure SQL에 관한 자세한 내용은 다음을 참조하세요.
   - [배포 옵션](../../azure-sql-iaas-vs-paas-what-is-overview.md)
   - [Azure Virtual Machines의 SQL Server](../../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
   - [Azure 총 소유 비용 계산기](https://azure.microsoft.com/pricing/tco/calculator/)


- 클라우드 마이그레이션의 프레임워크 및 채택 주기에 관한 자세한 정보는 다음을 참조하세요.
   -  [Azure용 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Azure로 마이그레이션된 워크로드의 비용 및 크기 조정 모범 사례](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- 라이선스에 관한 자세한 내용은 다음을 참조하세요.
   - [Azure 하이브리드 혜택을 사용하는 사용자 라이선스 필요](../../virtual-machines/windows/licensing-model-azure-hybrid-benefit-ahb-change.md)
   - [SQL Server 2008 및 SQL Server 2008 R2에 관한 무료 지원 확장 받기](../../virtual-machines/windows/sql-server-2008-extend-end-of-support.md)

- 애플리케이션 액세스 레이어를 평가하려면 [Data Access Migration Toolkit 미리 보기](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit)를 사용하세요.
- 데이터 액세스 계층 A/B 테스트를 수행하는 방법에 관한 자세한 내용은 [데이터베이스 실험 도우미 개요](/sql/dea/database-experimentation-assistant-overview)를 참조하세요.
