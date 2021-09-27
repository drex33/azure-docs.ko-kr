---
title: 제어 테이블을 사용하여 데이터베이스에서 대량 복사
description: 외부 제어 테이블을 사용하여 Azure Data Factory에서 원본 테이블의 파티션 목록을 저장하는 방식으로 솔루션 템플릿을 사용하여 데이터베이스에서 데이터를 대량으로 복사하는 방법을 알아봅니다.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: 94f7210c05056db2ead8b1aa54d9a0d16e3d8849
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743572"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>제어 테이블을 사용하여 데이터베이스에서 대량 복사

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Oracle Server, Netezza, Teradata 또는 SQL Server의 데이터 웨어하우스 데이터를 Azure Synapse Analytics에 복사하려면 여러 테이블에서 대량의 데이터를 로드해야 합니다. 일반적으로 단일 테이블에서 여러 스레드를 병렬 처리하여 행을 로드할 수 있도록 각 테이블에 데이터를 분할해야 합니다. 이 문서에서는 이러한 시나리오에서 사용할 템플릿을 설명합니다.

 >!참고 비교적 작은 데이터 볼륨이 포함된 적은 수의 테이블에서 Azure Synapse Analytics로 데이터를 복사하려는 경우 [Azure Data Factory 데이터 복사 도구](copy-data-tool.md)를 사용하는 것이 더 효율적입니다. 이 문서에서 설명하는 템플릿은 해당 시나리오에 필요한 것 이상의 기능을 제공합니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 외부 제어 테이블에서 복사할 원본 데이터베이스 파티션의 목록을 검색합니다. 그런 다음, 원본 데이터베이스의 각 파티션을 반복하고 대상에 데이터를 복사합니다.

이 템플릿은 다음 세 가지 작업을 포함합니다.
- **조회** 는 외부 제어 테이블에서 데이터베이스 파티션 목록을 검색합니다.
- **ForEach** 는 조회 작업에서 파티션 목록을 가져오고 각 파티션을 복사 작업으로 반복합니다.
- **복사** 는 원본 데이터베이스 저장소의 각 파티션을 대상 저장소로 복사합니다.

템플릿은 다음 매개 변수를 정의합니다.
- *Control_Table_Name* 은 원본 데이터베이스에 대한 파티션 목록을 저장하는 외부 제어 테이블입니다.
- *Control_Table_Schema_PartitionID* 는 각 파티션 ID를 저장하는 외부 제어 테이블의 열 이름입니다. 파티션 ID가 원본 데이터베이스의 각 파티션에서 고유한지 확인합니다.
- *Control_Table_Schema_SourceTableName* 은 원본 데이터베이스의 각 테이블 이름을 저장하는 외부 제어 테이블입니다.
- *Control_Table_Schema_FilterQuery* 는 원본 데이터베이스의 각 파티션에서 데이터를 가져오는 필터 쿼리를 저장하는 외부 제어 테이블의 열 이름입니다. 예를 들어 연도를 기준으로 데이터를 분할한 경우 각 행에 저장된 쿼리는 ‘select * from datasource where LastModifytime >= “2015-01-01 00:00:00” and LastModifytime <= “2015-12-31 23:59:59.999”’와 유사할 수 있습니다.
- *Data_Destination_Folder_Path* 는 데이터가 대상 저장소에 복사되는 경로입니다(선택한 대상이 "파일 시스템" 또는 "Azure Data Lake Storage Gen1"인 경우에 해당). 
- *Data_Destination_Container* 는 대상 저장소에서 데이터가 복사 되는 루트 폴더 경로입니다. 
- *Data_Destination_Directory* 는 대상 저장소에 데이터가 복사되는 루트 아래의 디렉터리 경로입니다. 

대상 저장소의 경로를 정의하는 마지막 세 매개 변수는 선택한 대상이 파일 기반 스토리지인 경우에만 표시됩니다. ‘Azure Synapse Analytics’를 대상 저장소로 선택하는 경우 이러한 매개 변수는 필요하지 않습니다. 단, Azure Synapse Analytics와 원본 데이터베이스의 테이블 이름과 스키마가 동일해야 합니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. 대량 복사를 위해 원본 데이터베이스 파티션 목록을 저장할 제어 테이블을 SQL Server 또는 Azure SQL Database에 만듭니다. 다음 예제에서는 원본 데이터베이스에 5개의 파티션이 있습니다. 3개의 파티션은 *datasource_table* 에 대한 것이고, 2개는 *project_table* 에 대한 것입니다. *LastModifytime* 열은 원본 데이터베이스에서 *datasource_table* 테이블의 데이터를 분할하는 데 사용됩니다. 첫 번째 파티션을 읽는 데 사용되는 쿼리는 ‘select * from datasource_table where LastModifytime >= “2015-01-01 00:00:00” and LastModifytime <= “2015-12-31 23:59:59.999”’입니다. 유사한 쿼리를 사용하여 다른 파티션에서 데이터를 읽을 수도 있습니다.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. **데이터베이스에서 대량 복사** 템플릿으로 이동합니다. 1단계에서 만든 외부 제어 테이블에 대한 **새** 연결을 만듭니다.

    :::image type="content" source="media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png" alt-text="제어 테이블에 대한 새 연결 만들기":::

3. 데이터를 복사하는 원본 데이터베이스에 대해 **새** 연결을 만듭니다.

    :::image type="content" source="media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png" alt-text="원본 데이터베이스에 대한 새 연결 만들기":::
    
4. 데이터를 복사하는 대상 데이터 저장소에 대해 **새** 연결을 만듭니다.

    :::image type="content" source="media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png" alt-text="대상 저장소에 대한 새 연결 만들기":::

5. **이 템플릿 사용** 을 선택합니다.

6. 다음 예제와 같이 파이프라인이 표시됩니다.

    :::image type="content" source="media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png" alt-text="파이프라인 검토":::

7. **디버그** 를 선택하고 **매개 변수** 를 입력한 다음, **마침** 을 선택합니다.

    :::image type="content" source="media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png" alt-text="**디버그** 클릭":::

8. 결과는 다음 예제와 유사합니다.

    :::image type="content" source="media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png" alt-text="결과 검토":::

9. (선택 사항) ‘Azure Synapse Analytics’를 데이터 대상으로 선택했다면 Azure Synapse Analytics Polybase에 필요한 경우 스테이징을 위해 Azure Blob 스토리지에 대한 연결을 입력해야 합니다. 이 템플릿은 Blob 스토리지에 대한 컨테이너 경로를 자동으로 생성합니다. 파이프라인이 실행된 후 컨테이너를 만들었는지 확인합니다.
    
    :::image type="content" source="media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png" alt-text="Polybase 설정":::
       
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)
