---
title: 컨트롤 테이블을 사용하여 데이터베이스에서 델타 복사
description: 솔루션 템플릿을 사용하여 증분 방식으로 Azure Data Factory의 데이터베이스에서 새 행 또는 업데이트된 행만 복사하는 방법을 알아봅니다.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: b5bb939c787af16ca3affdf8ba131ea640f25ed4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040968"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>컨트롤 테이블을 사용하여 데이터베이스에서 델타 복사

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 상위 워터마크 값을 저장하는 외부 컨트롤 테이블을 사용하여 데이터베이스 테이블에서 Azure로 새로운 행 또는 업데이트된 행을 증분 로드 하는 데 사용할 수 있는 템플릿에 대해 설명합니다.

이 템플릿에는 원본 데이터베이스의 스키마가 새로운 행 또는 업데이트된 행을 식별하기 위한 타임스탬프 열 또는 증분 키가 포함됩니다.

>[!NOTE]
> 원본 데이터베이스에 새로운 행이나 업데이트된 행을 식별하기 위한 타임스탬프 열이 있고 델타 복사에 사용할 외부 컨트롤 테이블을 만들지 않으려는 경우, [Azure Data Factory 데이터 복사 도구](copy-data-tool.md)를 사용해 파이프라인을 가져올 수 있습니다. 해당 도구는 트리거 예약 시간을 변수로 사용하여 원본 데이터베이스에서 새로운 행을 읽습니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

해당 템플릿은 먼저 이전 워터마크 값을 검색한 후 현재 워터마크 값과 비교합니다. 그런 다음, 두 워터마크 값 사이의 비교 결과를 기준으로 원본 데이터베이스에서 변경된 내용만 복사합니다. 마지막으로, 다음 델타 데이터 로드를 위해 새로운 상위 워터마크 값을 외부 컨트롤 테이블에 저장합니다.

이 템플릿은 다음 4가지 작업을 포함합니다.
- **Lookup** 은 외부 컨트롤 테이블에 저장된 이전 상위 워터마크 값을 검색합니다.
- 다른 **Lookup** 작업은 원본 데이터베이스에서 현재 상위 워터마크 값을 검색합니다.
- **Copy** 는 원본 데이터베이스에서 변경된 내용만 대상 스토어로 복사합니다. 원본 데이터베이스의 변경된 내용을 식별하는 데 사용하는 쿼리는 SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > “last high-watermark” 및 TIMESTAMP_Column <= “current high-watermark”와 유사합니다.
- **SqlServerStoredProcedure** 는 다음 델타 복사를 위해 현재 상위 워터마크 값을 외부 컨트롤 테이블에 씁니다.

템플릿은 다음 매개 변수를 정의합니다.
- *Data_Source_Table_Name* 은 데이터를 로드하려는 원본 데이터베이스의 테이블입니다.
- *Data_Source_WaterMarkColumn* 은 새로운 행 또는 업데이트된 행을 식별하는 데 사용되는 원본 테이블의 열 이름입니다. 해당 열의 형식은 일반적으로 *Datetime*, *INT* 이거나, 또는 유사합니다.
- *Data_Destination_Container* 는 대상 스토어에서 데이터가 복사되는 장소의 루트 경로입니다.
- *Data_Destination_Directory* 는 대상 스토어에 데이터가 복사되는 장소의 루트에 있는 디렉터리 경로입니다.
- *Data_Destination_Table_Name* 은 대상 스토어의 데이터가 복사되는 위치입니다. ‘Azure Synapse Analytics’가 데이터 대상으로 선택된 경우에 해당합니다.
- *Data_Destination_Folder_Path* 는 대상 스토어의 데이터가 복사되는 위치입니다. ‘파일 시스템’ 또는 ‘Azure Data Lake Storage Gen1’을 데이터 대상으로 선택한 경우에 해당합니다.
- *Control_Table_Table_Name* 은 상위 워터마크 값을 저장할 외부 컨트롤 테이블입니다.
- *Control_Table_Column_Name* 은 상위 워터마크 값을 저장할 외부 컨트롤 테이블의 열입니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. 로드하려는 원본 테이블을 살펴보고 새로운 행 또는 업데이트된 행을 분할하는 데 사용할 수 있는 상위 워터마크 열을 정의합니다. 해당 열은 *datetime*, *INT*, 또는 이와 유사한 형식을 가질 수 있습니다. 새로운 행이 추가되면 해당 열의 값이 증가합니다. 다음 샘플 원본 테이블(data_source_table)에서 *LastModifytime* 열을 상위 워터마크 열로 사용할 수 있습니다.

    ```output
    PersonID    Name            LastModifytime
    1           aaaa            2017-09-01 00:56:00.000
    2           bbbb            2017-09-02 05:23:00.000
    3           cccc            2017-09-03 02:36:00.000
    4           dddd            2017-09-04 03:21:00.000
    5           eeee            2017-09-05 08:06:00.000
    6           fffffff         2017-09-06 02:23:00.000
    7           gggg            2017-09-07 09:01:00.000
    8           hhhh            2017-09-08 09:01:00.000
    9           iiiiiiiii       2017-09-09 09:01:00.000
    ```

2. SQL Server 또는 Azure SQL Database에 델타 데이터 로드를 위한 상위 워터마크 값을 저장하는 컨트롤 테이블을 만듭니다. 다음 예제에서 컨트롤 테이블의 이름은 *watermarktable* 입니다. 해당 테이블에서 *WatermarkValue* 는 상위 워터마크 값을 저장하는 열이고, 해당 형식은 *datetime* 입니다.

    ```sql
    create table watermarktable
    (
    WatermarkValue datetime,
    );
    INSERT INTO watermarktable
    VALUES ('1/1/2010 12:00:00 AM')
    ```

3. 컨트롤 테이블을 만드는 데 사용한 것과 동일한 SQL Server 또는 Azure SQL 데이터베이스 인스턴스에 저장 프로시저를 만듭니다. 해당 저장 프로시저는 다음 델타 데이터 로드를 위해 새로운 상위 워터마크 값을 외부 컨트롤 테이블에 쓰는 데 사용됩니다.

    ```sql
    CREATE PROCEDURE update_watermark @LastModifiedtime datetime
    AS

    BEGIN

        UPDATE watermarktable
        SET [WatermarkValue] = @LastModifiedtime 

    END
    ```

4. **데이터베이스에서 델타 복사** 템플릿으로 이동합니다. 데이터를 복사하려는 원본 데이터베이스에 대해 **새** 연결을 만듭니다.

    :::image type="content" source="media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png" alt-text="원본 테이블에 대한 새 연결 만들기":::

5. 데이터를 복사하려는 대상 데이터 저장소에 대해 **새** 연결을 만듭니다.

    :::image type="content" source="media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png" alt-text="대상 테이블에 대한 새 연결 만들기":::

6. 2단계와 3단계에서 만든 외부 컨트롤 테이블 및 저장 프로시저에 대한 **새** 연결을 만듭니다.

    :::image type="content" source="media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png" alt-text="제어 테이블 데이터 저장소에 대한 새 연결 만들기":::

7. **이 템플릿 사용** 을 선택합니다.
    
8. 다음 예제와 같이 이용 가능한 파이프라인이 표시됩니다.
  
    :::image type="content" source="media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png" alt-text="파이프라인 검토":::

9. **저장 프로시저** 를 선택합니다. **저장 프로시저 이름** 에 **[dbo].[update_watermark]** 를 선택합니다. **매개 변수 가져오기** 를 선택하고 **동적 콘텐츠 추가** 를 선택합니다.  

    :::image type="content" source="media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png" alt-text="저장 프로시저 작업 설정":::   

10. 콘텐츠 **\@@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** 를 작성하고 **마침** 을 클릭합니다.  

    :::image type="content" source="media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png" alt-text="저장 프로시저에 대한 매개 변수 콘텐츠 작성":::        
     
11. **디버그** 를 선택하고 **매개 변수** 를 입력한 다음, **마침** 을 선택합니다.

    :::image type="content" source="media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png" alt-text="**디버그** 선택":::

12. 다음 예제와 유사한 결과가 표시됩니다.

    :::image type="content" source="media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png" alt-text="결과 검토":::

13. 원본 테이블에 새 행을 만들 수 있습니다. 새로운 행을 만드는 샘플 SQL 언어는 다음과 같습니다.

    ```sql
    INSERT INTO data_source_table
    VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

    INSERT INTO data_source_table
    VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. 파이프라인을 다시 실행하려면 **디버그** 를 선택하고 **매개 변수** 를 입력한 다음 **마침** 을 선택합니다.

    대상으로 새로운 행만 복사된 것을 볼 수 있습니다.

15. (선택 사항:) Azure Synapse Analytics를 데이터 대상으로 선택하는 경우 Azure Synapse Analytics Polybase가 필요로 하는 Azure Blob Storage에 대한 연결도 제공해야 합니다. 해당 템플릿은 컨테이너 경로를 생성합니다. 파이프라인을 실행한 후 컨테이너가 Blob Storage에 만들어졌는지 확인합니다.
    
    :::image type="content" source="media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png" alt-text="PolyBase 구성":::
    
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory의 컨트롤 테이블을 사용하여 데이터베이스에서 대량 복사](solution-template-bulk-copy-with-control-table.md)
- [Azure Data Factory를 사용하여 여러 컨테이너에서 파일 복사](solution-template-copy-files-multiple-containers.md)
