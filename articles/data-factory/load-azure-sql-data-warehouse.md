---
title: Azure Synapse Analytics에 데이터 로드
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Synapse 파이프라인을 사용하여 Azure Synapse Analytics로 데이터를 복사합니다.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 08/24/2021
ms.openlocfilehash: b297e47e3b1cb890312b6f7a566eb4f033e6612a
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824122"
---
# <a name="load-data-into-azure-synapse-analytics-using-azure-data-factory-or-a-synapse-pipeline"></a>Azure Data Factory 또는 Synapse 파이프라인을 사용하여 Azure Synapse Analytics로 데이터 로드

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)는 대용량 관계형 및 비관계형 데이터를 처리할 수 있는 클라우드 기반 스케일 아웃 데이터베이스입니다. Azure Synapse Analytics는 엔터프라이즈 데이터 웨어하우스 워크로드에 최적화된 MPP(Massively Parallel Processing) 아키텍처를 기반으로 합니다. 스토리지를 확장하고 개별적으로 계산할 수 있는 클라우드 탄력성을 유연하게 제공합니다.

이제 Azure Synapse Analytics로 시작하는 것이 그 어느 때보다 쉬워졌습니다. Azure Synapse 자체 내에 있는 Azure Data Factory와 해당 파이프라인 기능은 완전 관리형 클라우드 기반 데이터 통합 서비스를 제공합니다. 분석 솔루션을 빌드할 때 서비스를 사용하여 Azure Synapse Analytics를 기존 시스템의 데이터로 채우면 시간을 절약할 수 있습니다.

Azure Data Factory 및 Synapse 파이프라인은 Azure Synapse Analytics로 데이터를 로드할 경우 다음과 같은 혜택을 제공합니다.

* **간편한 설정**: 스크립팅이 필요 없는 직관적인 5단계 마법사.
* **다양한 데이터 저장소 지원**: 다양한 온-프레미스 및 클라우드 기반 데이터 저장소 집합에 대한 기본 제공 지원. 자세한 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.
* **보안 및 규정 준수**: 데이터가 HTTPS 또는 Express 경로를 통해 전송됩니다. 글로벌 서비스가 제공되므로 데이터가 지리적 경계를 벗어나지 않습니다.
* **PolyBase를 사용하여 제공되는 뛰어난 성능**: Polybase는 Azure Synapse Analytics로 데이터를 이동시키는 가장 효율적인 방법입니다. 스테이징 Blob 기능을 사용하여 Azure Blob Storage 및 Data Lake Store를 포함하여 모든 유형의 데이터 스토리지에서 높은 로드 속도를 얻습니다. (Polybase는 기본적으로 Azure Blob Storage 및 Data Lake Store를 지원합니다.) 자세한 내용은 [복사 작업 성능](copy-activity-performance.md)을 참조하세요.

이 문서에서는 데이터 복사 도구를 사용하여 _Azure SQL Database의 데이터를 Azure Synapse Analytics로 로드_ 하는 방법을 설명합니다. 다른 데이터 저장소 유형에서 데이터를 복사할 때도 이와 유사한 단계를 따를 수 있습니다.

> [!NOTE]
> 자세한 내용은 [Azure Synapse Analytics 간에 데이터 복사](connector-azure-sql-data-warehouse.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Azure Synapse Analytics: 데이터 웨어하우스에는 SQL 데이터베이스에서 복사된 데이터를 보관합니다. Azure Synapse Analytics가 없는 경우 [Azure Synapse Analytics 만들기](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md)의 지침을 참조하세요.
* Azure SQL Database: 이 자습서에서는 Azure SQL Database의 Adventure Works LT 샘플 데이터 세트에서 데이터를 복사합니다. [Azure SQL Database에서 샘플 데이터베이스 만들기](../azure-sql/database/single-database-create-quickstart.md)의 지침에 따라 SQL Database에서 이 샘플 데이터베이스를 만들 수 있습니다.
* Azure 스토리지 계정: Azure Storage는 대량 복사 작업에서 _스테이징_ Blob으로 사용됩니다. Azure Storage 계정이 없는 경우 [스토리지 계정 만들기](../storage/common/storage-account-create.md)의 지침을 참조하세요.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

> [!NOTE]
> 기존 Synapse 작업 영역 내에서 파이프라인 기능을 사용하여 데이터를 로드하려면 새 데이터 팩터리 만들기를 건너뛰면 됩니다.  Azure Synapse는 해당 파이프라인 기능 내에 Azure Data Factory 기능을 포함합니다.

1. 왼쪽 메뉴에서 **리소스 만들기** > **데이터 + 분석** > **Data Factory** 를 차례로 선택합니다.

2. **새 데이터 팩터리** 페이지에서 다음 항목에 대한 값을 제공합니다.

    * **이름**: 이름에 *LoadSQLDWDemo* 를 입력합니다. 데이터 팩터리 이름은 *전역으로 고유해야 합니다. "데이터 팩터리 이름 'LoadSQLDWDemo'은 사용할 수 없습니다" 오류가 표시되는 경우 데이터 팩터리에 다른 이름을 입력합니다. 예를 들어 _**yourname**_**ADFTutorialDataFactory** 라는 이름을 사용할 수 있습니다. 데이터 팩터리를 다시 만들어 봅니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
    * **구독**: 데이터 팩터리를 만들 Azure 구독을 선택합니다. 
    * **리소스 그룹**: 드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 옵션을 선택하고 리소스 그룹의 이름을 입력합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.  
    * **버전**: **V2** 를 선택합니다.
    * **위치**: 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소가 다른 위치 및 지역에 있어도 됩니다. 이러한 데이터 저장소는 Azure Data Lake Store, Azure Storage, Azure SQL Database 등을 포함합니다.

3. **만들기** 를 선택합니다.
4. 만들기가 완료되면 데이터 팩터리로 이동합니다. 다음 그림과 같이 **데이터 팩터리** 홈페이지가 표시됩니다.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Azure Data Factory Studio 열기 타일이 있는 Azure Data Factory 홈페이지":::

   **Azure Data Factory Studio 열기** 타일에서 **열기** 를 선택하여 별도의 탭에서 데이터 통합 애플리케이션을 시작합니다.

## <a name="load-data-into-azure-synapse-analytics"></a>Azure Synapse Analytics에 데이터 로드

1. Azure Data Factory 또는 Azure Synapse 작업 영역의 홈페이지에서 **수집** 타일을 선택하여 데이터 복사 도구를 시작합니다.  그런 다음 **기본 제공 복사 작업** 을 선택합니다.

2. **속성** 페이지의 **작업 종류** 에서 **기본 제공 복사 작업** 을 선택한 후 **다음** 을 선택합니다.

    ![속성 페이지](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

3. **원본 데이터 저장소** 페이지에서 다음 단계를 완료합니다.
    >[!TIP]
    >이 자습서에서는 *SQL 인증* 을 원본 데이터 저장소의 인증 형식으로 사용하지만 지원되는 다른 인증 방법을 선택할 수 있습니다. 필요한 경우 *서비스 주체* 및 *관리 ID* 를 선택합니다. 자세한 내용은 [이 문서](./connector-azure-sql-database.md#linked-service-properties)의 해당 섹션을 참조하세요.
    >데이터 저장소에 대한 비밀을 안전하게 저장하려면 Azure Key Vault를 사용하는 것도 좋습니다. 자세한 그림은 [이 문서](./store-credentials-in-key-vault.md)를 참조하세요.

    1. **+ 새 연결** 을 선택합니다.

    1. 갤러리에서 **Azure SQL Database** 를 선택하고 **계속** 을 선택합니다. 검색 상자에 "SQL"을 입력하여 커넥터를 필터링할 수 있습니다.

        ![Azure SQL DB 선택](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)
    
    1. **새 연결(Azure SQL Database)** 페이지의 드롭다운 목록에서 서버 이름과 DB 이름을 선택하고 사용자 이름 및 암호를 지정합니다. **연결 테스트** 를 선택하여 설정의 유효성을 검사한 후 **만들기** 를 선택합니다.

        ![Azure SQL DB 구성](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)
    
    1. **원본 데이터 저장소** 페이지에서 새로 만든 연결을 **연결** 섹션의 원본으로 선택합니다.

    1. **원본 테이블** 섹션에서 **SalesLT** 를 입력하여 테이블을 필터링합니다. 복사할 모든 테이블을 사용하려면 **(모두 선택)** 상자를 선택하고 **다음** 을 선택합니다.

    !['원본 데이터 저장소' 페이지의 구성을 보여 주는 스크린샷.](./media/load-azure-sql-data-warehouse/source-data-store-page.png)

4. **필터 적용** 페이지에서 설정을 지정하거나 **다음** 을 선택합니다. 이 페이지의 **데이터 미리 보기** 단추를 선택하여 데이터를 미리 보고 입력 데이터의 스키마를 볼 수 있습니다. 

    :::image type="content" source="./media/load-azure-sql-data-warehouse/apply-filter.png" alt-text=" '필터 적용' 페이지를 보여 주는 스크린샷.":::

5. **대상 데이터 저장소** 페이지에서 다음 단계를 완료합니다.
    >[!TIP]
    >이 자습서에서는 *SQL 인증* 을 대상 데이터 저장소의 인증 형식으로 사용하지만 필요한 경우 *서비스 주체* 및 *관리 ID* 등의 지원되는 다른 인증 방법을 선택할 수 있습니다. 자세한 내용은 [이 문서](./connector-azure-sql-data-warehouse.md#linked-service-properties)의 해당 섹션을 참조하세요.
    >데이터 저장소에 대한 비밀을 안전하게 저장하려면 Azure Key Vault를 사용하는 것도 좋습니다. 자세한 그림은 [이 문서](./store-credentials-in-key-vault.md)를 참조하세요.

    1. **+ 새 연결** 을 선택하여 연결을 추가합니다.

    1. 갤러리에서 **Azure Synapse Analytics** 를 선택하고 **계속** 을 선택합니다.

        ![Azure Synapse Analytics 선택](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    1. **새 연결(Azure Synapse Analytics)** 페이지의 드롭다운 목록에서 서버 이름과 DB 이름을 선택하고 사용자 이름 및 암호를 지정합니다. **연결 테스트** 를 선택하여 설정의 유효성을 검사한 후 **만들기** 를 선택합니다.

        ![Azure Synapse Analytics 구성](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    1. **대상 데이터 저장소** 페이지에서 새로 만든 연결을 **연결** 섹션의 싱크로 선택합니다.

6. **테이블 매핑** 섹션에서 콘텐츠를 검토하고 **다음** 을 선택합니다. 지능형 테이블 매핑이 표시됩니다. 원본 테이블은 테이블 이름에 따라 대상 테이블에 매핑됩니다. 원본 테이블이 대상에 없으면 기본적으로 서비스에서 같은 이름으로 대상 테이블을 만듭니다. 기존 대상 테이블에 원본 테이블을 매핑할 수도 있습니다.

   ![‘대상 데이터 저장소’ 페이지의 구성을 보여 주는 스크린샷.](./media/load-azure-sql-data-warehouse/destination-data-store-page.png)

1. **열 매핑** 페이지에서 콘텐츠를 검토하고 **다음** 을 선택합니다. 지능형 테이블 매핑은 열 이름을 기반으로 합니다. 서비스에서 테이블을 자동으로 만들도록 하면 원본과 대상 저장소가 서로 호환되지 않을 때 데이터 형식이 변환될 수 있습니다. 원본 및 대상 열 간에 지원되지 않는 데이터 형식 변환이 있는 경우 해당 테이블 옆에 오류 메시지가 표시됩니다.

    ![열 매핑 페이지](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. **설정** 페이지에서 다음 단계를 완료합니다.

    1. **작업 이름** 필드에 **CopyFromSQLToSQLDW** 를 지정합니다.
    1. **준비 설정** 섹션에서 **+ 새로 만들기** 를 선택하여 준비 스토리지를 새로 만듭니다. 스토리지는 PolyBase를 사용하여 Azure Synapse Analytics에 로드하기 전에 데이터를 준비하는 데 사용됩니다. 복사가 완료되면 Azure Blob Storage의 임시 데이터가 자동으로 정리됩니다.

    1. **새로 연결된 서비스** 페이지에서 스토리지 계정과 **만들기** 를 차례로 선택하여 연결된 서비스를 배포합니다.

    1. **형식 기본값 사용** 옵션 선택을 취소하고 **다음** 을 선택합니다.

    ![PolyBase 구성](./media/load-azure-sql-data-warehouse/configure-polybase.png)

8. **요약** 페이지에서 설정을 검토하고 **다음** 을 선택합니다.

9. **배포 페이지** 에서 **모니터** 를 선택하여 파이프라인(작업)을 모니터링합니다. 

    :::image type="content" source="./media/load-azure-sql-data-warehouse/deployment-complete-page.png" alt-text="배포 페이지를 보여 주는 스크린샷.":::
 
10. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. 파이프라인 실행이 완료되면 **파이프라인 이름** 열에서 **CopyFromSQLToSQLDW** 링크를 선택하여 활동 실행 세부 사항을 보거나 파이프라인을 다시 실행합니다.

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)
    :::image type="content" source="./media/load-azure-sql-data-warehouse/pipeline-monitoring.png" alt-text="파이프라인 실행 모니터링":::    

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)
    :::image type="content" source="./media/load-azure-sql-data-warehouse/pipeline-monitoring-synapse.png" alt-text="파이프라인 실행 모니터링":::   

--- 

12. 파이프라인 실행 보기로 다시 전환하려면 위쪽에서 **모든 파이프라인 실행** 링크를 선택합니다. **새로 고침** 을 선택하여 목록을 새로 고칩니다.

    ![작업 실행 모니터링](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. 복사 작업별로 실행 세부 사항을 모니터링하려면 활동 실행 보기의 **작업 이름 **에서** 세부 정보** 링크(안경 아이콘)를 선택합니다. 원본에서 싱크로 복사된 데이터 양, 데이터 처리량, 해당 기간의 실행 단계 및 사용된 구성과 같은 세부 정보를 모니터링할 수 있습니다.

    ![활동 실행 세부 정보 모니터링 첫 번째](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![활동 실행 세부 정보 모니터링 두 번째](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>다음 단계

Azure Synapse Analytics 지원에 대해 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
>[Azure Synapse Analytics 커넥터](connector-azure-sql-data-warehouse.md)