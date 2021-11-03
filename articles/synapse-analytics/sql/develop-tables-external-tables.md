---
title: Synapse SQL에서 외부 테이블 사용
description: Synapse SQL에서 외부 테이블로 데이터 파일 읽거나 쓰기
services: synapse-analytics
author: ma77b
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 07/23/2021
ms.author: maburd
ms.reviewer: wiassaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 14341d2c623ab465e054c83b7b47a100a52f8ece
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054631"
---
# <a name="use-external-tables-with-synapse-sql"></a>Synapse SQL에서 외부 테이블 사용

외부 테이블은 Hadoop, Azure Storage Blob 또는 Azure Data Lake Storage에 있는 데이터를 가리킵니다. 외부 테이블은 Azure Storage의 파일에서 데이터를 읽거나 쓰는 데 사용됩니다. Synapse SQL에서는 전용 SQL 풀 또는 서버리스 SQL 풀을 사용하여 외부 테이블로 데이터를 읽고 쓸 수 있습니다.

외부 데이터 원본의 유형에 따라 다음과 같은 두 가지 유형의 외부 테이블을 사용할 수 있습니다.
- CSV, Parquet 및 ORC와 같은 다양한 데이터 형식의 데이터를 읽고 내보낼 수 있는 Hadoop 외부 테이블. Hadoop 외부 테이블은 전용 SQL 풀에서 사용할 수 있지만 서버리스 SQL 풀에서는 사용할 수 없습니다.
- CSV 및 Parquet과 같은 다양한 데이터 형식의 데이터를 읽고 내보낼 수 있는 네이티브 외부 테이블. 네이티브 외부 테이블은 서버리스 SQL 풀에서 사용할 수 있으며 전용 SQL 풀에서 **공개 미리 보기** 로 제공됩니다.

Hadoop과 네이티브 외부 테이블의 주요 차이점은 다음 표에 정리되어 있습니다.

| 외부 테이블 유형 | Hadoop은 | 기본 |
| --- | --- | --- |
| 전용 SQL 풀 | 사용 가능 | Parquet 테이블은 **공개 미리 보기** 에서 사용할 수 있습니다. |
| 서버리스 SQL 풀 | 사용할 수 없음 | 사용 가능 |
| 지원되는 형식 | 구분 기호로 분리됨/CSV, Parquet, ORC, Hive RC 및 RC | 서버리스 SQL 풀: 구분됨/CSV, Parquet 및 [Delta Lake(미리 보기)](query-delta-lake-format.md)<br/>전용 SQL 풀: Parquet |
| 폴더 파티션 제거 | No | Synapse 작업 영역의 Apache Spark 풀에서 서버리스 SQL 풀과 동기화된 분할된 테이블에만 해당 |
| 위치의 사용자 지정 형식 | 예 | 예. `/year=*/month=*/day=*`와 같은 와일드카드 사용 |
| 재귀 폴더 검색 | No | 위치 경로의 끝에 `/**`가 지정된 경우 서버리스 SQL 풀에만 해당 |
| 스토리지 필터 푸시다운 | No | 서버리스 SQL 풀에만 해당. 문자열 푸시다운의 경우 `VARCHAR` 열에서 `Latin1_General_100_BIN2_UTF8` 데이터 정렬을 사용해야 합니다. |
| 스토리지 인증 | SAK(스토리지 액세스 키), AAD 통과, 관리 ID, 사용자 지정 애플리케이션 Azure AD ID | SAS(공유 액세스 서명), AAD 통과, 관리 ID |

> [!NOTE]
> Delta Lake 형식의 네이티브 외부 테이블은 공개 미리 보기로 제공됩니다. 자세한 내용은 [Delta Lake 파일 쿼리(미리 보기)](query-delta-lake-format.md)를 참조하세요. [CETAS](develop-tables-cetas.md)는 Delta Lake 형식의 콘텐츠 내보내기를 지원하지 않습니다.

## <a name="external-tables-in-dedicated-sql-pool-and-serverless-sql-pool"></a>전용 SQL 풀 및 서버리스 SQL 풀의 외부 테이블

외부 테이블을 사용하면 다음 작업을 수행할 수 있습니다.

- Transact-SQL 문을 사용하여 Azure Blob Storage 및 Azure Data Lake Gen2를 쿼리합니다.
- [CETAS](develop-tables-cetas.md)를 사용하여 쿼리 결과를 Azure Blob Storage 또는 Azure Data Lake Storage의 파일에 저장합니다.
- Azure Blob Storage 및 Azure Data Lake Storage에서 데이터를 가져와서 전용 SQL 풀(전용 풀의 Hadoop 테이블에만)에 저장합니다.

> [!NOTE]
> [CREATE TABLE AS SELECT](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?context=/azure/synapse-analytics/context/context) 문과 함께 사용할 경우 외부 테이블에서 선택하면 데이터를 **전용** SQL 풀 내의 테이블로 가져옵니다. 외부 테이블은 [COPY 문](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) 외에도 데이터를 로드하는 데 유용합니다. 
> 
> 로드 자습서는 [PolyBase를 사용하여 Azure Blob Storage에서 데이터 로드](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)를 참조하세요.

다음 단계를 통해 Synapse SQL 풀에 외부 테이블을 만들 수 있습니다.

1. [외부 데이터 원본 만들기](#create-external-data-source) - 외부 Azure Storage를 참조하고, 스토리지에 액세스할 때 사용해야 하는 자격 증명을 지정합니다.
2. [외부 파일 형식 만들기](#create-external-file-format) - CSV 또는 Parquet 파일의 형식을 설명합니다.
3. [외부 테이블 만들기](#create-external-table) - 데이터 원본에 배치된 파일을 기반으로 동일한 파일 형식을 사용하여 만듭니다.

### <a name="security"></a>보안

사용자가 데이터를 읽으려면 외부 테이블에 대해 `SELECT` 권한이 있어야 합니다.
다음 규칙을 사용하여 데이터 원본에 정의된 데이터베이스 범위의 자격 증명을 사용하는 기본 Azure Storage에 대한 외부 테이블 액세스:
- 자격 증명이 없는 데이터 원본의 경우 외부 테이블이 Azure Storage에서 공개적으로 사용 가능한 파일에 액세스할 수 있습니다.
- 데이터 원본에는 외부 테이블이 SAS 토큰 또는 작업 영역 관리 ID를 사용해서 Azure Storage에 있는 파일만 액세스하도록 설정하는 자격 증명이 포함되어 있습니다. 예를 들어 [스토리지 파일 개발 스토리지 액세스 제어](develop-storage-files-storage-access-control.md#examples) 문서를 참조하세요.

## <a name="create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE

외부 데이터 원본은 스토리지 계정에 연결하는 데 사용됩니다. 전체 설명서는 [여기](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true)에 요약되어 있습니다.

### <a name="syntax-for-create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE 구문

#### <a name="hadoop"></a>[Hadoop](#tab/hadoop)

`TYPE=HADOOP`인 외부 데이터 원본은 전용 SQL 풀에서만 사용할 수 있습니다.

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
     , TYPE = HADOOP
)
[;]
```

#### <a name="native"></a>[기본](#tab/native)

`TYPE=HADOOP`를 사용하지 않는 외부 데이터 원본은 서버리스 SQL 풀에 일반 공급되며 전용 풀에 공개 미리 보기로 제공됩니다.

```syntaxsql
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(    LOCATION         = '<prefix>://<path>'
     [, CREDENTIAL = <database scoped credential> ]
)
[;]
```

---

### <a name="arguments-for-create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE 인수

#### <a name="data_source_name"></a>data_source_name

데이터 원본에 대한 사용자 정의 이름을 지정합니다. 이름은 반드시 데이터베이스 내에서 고유해야 합니다.

#### <a name="location"></a>위치
LOCATION = `'<prefix>://<path>'` - 외부 데이터 원본에 대한 연결 프로토콜 및 경로를 제공합니다. 위치에서 다음과 같은 패턴을 사용할 수 있습니다.

| 외부 데이터 원본        | 위치 접두사 | 위치 경로                                         |
| --------------------------- | --------------- | ----------------------------------------------------- |
| Azure Blob Storage          | `wasb[s]`       | `<container>@<storage_account>.blob.core.windows.net` |
| Azure Blob Storage          | `http[s]`       | `<storage_account>.blob.core.windows.net/<container>/subfolders` |
| Azure Data Lake Store Gen 1 | `http[s]`       | `<storage_account>.azuredatalakestore.net/webhdfs/v1` |
| Azure Data Lake Store Gen 2 | `http[s]`       | `<storage_account>.dfs.core.windows.net/<container>/subfolders`  |

`https:` 접두사를 통해 경로에 하위 폴더를 사용할 수 있습니다.

#### <a name="credential"></a>자격 증명
CREDENTIAL = `<database scoped credential>`은 Azure Storage에서 인증을 수행하기 위해 사용되는 선택적인 자격 증명입니다. 자격 증명이 없는 외부 데이터 원본은 공용 스토리지 계정에 액세스하거나 호출자의 Azure AD ID를 사용하여 스토리지의 파일에 액세스할 수 있습니다. 
- 전용 SQL 풀에서 데이터베이스 범위의 자격 증명은 사용자 지정 애플리케이션 ID, 작업 영역 관리 ID 또는 SAK 키를 지정할 수 있습니다. 
- 서버리스 SQL 풀에서 데이터베이스 범위의 자격 증명은 작업 영역 관리 ID 또는 SAS 키를 지정할 수 있습니다. 

#### <a name="type"></a>TYPE
TYPE = `HADOOP`은 Java 기반 기술을 사용해서 기본 파일에 액세스하도록 지정하는 옵션입니다. 기본 제공되는 네이티브 리더를 사용하는 서버리스 SQL 풀에서는 이 매개 변수를 사용할 수 없습니다.

### <a name="example-for-create-external-data-source"></a>CREATE EXTERNAL DATA SOURCE 예제

#### <a name="hadoop"></a>[Hadoop](#tab/hadoop)

다음 예제에서는 뉴욕 데이터 세트를 가리키는 Azure Data Lake Gen2에 대한 Hadoop 외부 데이터 원본을 전용 SQL 풀에 만듭니다.

```sql
CREATE DATABASE SCOPED CREDENTIAL [ADLS_credential]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH
  -- Please note the abfss endpoint when your account has secure transfer enabled
  ( LOCATION = 'abfss://data@newyorktaxidataset.dfs.core.windows.net' ,
    CREDENTIAL = ADLS_credential ,
    TYPE = HADOOP
  ) ;
```

다음 예제에서는 공개적으로 사용할 수 있는 뉴욕 데이터 세트를 가리키는 Azure Data Lake Gen2에 대한 외부 데이터 원본을 만듭니다.

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/',
       TYPE = HADOOP)
```

#### <a name="native"></a>[기본](#tab/native)

다음 예제에서는 SAS 자격 증명을 사용하여 액세스 가능한 Azure Data Lake Gen2에 대한 외부 데이터 원본을 서버리스 또는 전용 SQL 풀에 만듭니다.

```sql
CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO

CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

다음 예제에서는 공개적으로 사용할 수 있는 뉴욕 데이터 세트를 가리키는 Azure Data Lake Gen2에 대한 외부 데이터 원본을 만듭니다.

```sql
CREATE EXTERNAL DATA SOURCE YellowTaxi
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/')
```
---

## <a name="create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT

Azure Blob Storage 또는 Azure Data Lake Storage에 저장된 외부 데이터를 정의하는 외부 파일 형식 개체를 만듭니다. 외부 파일 형식을 만드는 것은 외부 테이블을 만들기 위한 필수 구성 요소입니다. 전체 설명서는 [여기](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true)에 있습니다.

외부 파일 형식을 만드는 경우 외부 테이블에서 참조하는 데이터의 실제 레이아웃을 지정합니다.

### <a name="syntax-for-create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT 구문

```syntaxsql
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' ]
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter
    | First_Row = integer
    | USE_TYPE_DEFAULT = { TRUE | FALSE }
    | Encoding = {'UTF8' | 'UTF16'}
    | PARSER_VERSION = {'parser_version'}
}
```

### <a name="arguments-for-create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT 인수

file_format_name - 외부 파일 형식의 이름을 지정합니다.

FORMAT_TYPE = [ PARQUET | DELIMITEDTEXT] - 외부 데이터의 형식을 지정합니다.

- PARQUET - Parquet 형식을 지정합니다.
- DELIMITEDTEXT - 필드 종결자라고도 하는 열 구분 기호가 있는 텍스트 형식을 만듭니다.

FIELD_TERMINATOR = *field_terminator* - 분리된 텍스트 파일에만 적용됩니다. 필드 종결자는 구분 기호로 분리된 텍스트 파일의 각 필드(열)의 끝을 표시하는 하나 이상의 문자를 지정합니다. 기본값은 파이프 문자(|)입니다.

예제:

- FIELD_TERMINATOR = '|'
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR = \t

STRING_DELIMITER = *string_delimiter* - 텍스트 구분 파일에서 문자열 형식의 데이터에 대한 필드 종결자를 지정합니다. 문자열 구분 기호는 길이가 한 자 이상이며 작은따옴표로 묶입니다. 기본값은 빈 문자열("")입니다.

예제:

- STRING_DELIMITER = '"'
- STRING_DELIMITER = '*'
- STRING_DELIMITER = ,

FIRST_ROW = *First_row_int* - 먼저 읽고 모든 파일에 적용되는 행 번호를 지정합니다. 값이 2로 설정되면 데이터를 로드할 때 모든 파일의 첫 번째 행(머리글 행)을 건너뜁니다. 행은 행 종결자(/r/n, /r, /n)의 존재를 기준으로 건너뜁니다.

USE_TYPE_DEFAULT = { TRUE | **FALSE** } - 텍스트 파일에서 데이터를 검색하는 경우 분리된 텍스트 파일에서 누락된 값을 처리하는 방법을 지정합니다.

TRUE - 텍스트 파일에서 데이터를 검색하는 경우 외부 테이블 정의의 해당 열에 대한 기본값의 데이터 형식을 사용하여 누락된 각 값을 저장합니다. 예를 들어, 누락된 값을 다음으로 바꿉니다.

- 열이 숫자 열로 정의되었으면 0 10진수 열은 지원되지 않으며 오류가 발생합니다.
- 열이 문자열이면 빈 문자열("")입니다.
- 열이 날짜 열이면 1900-01-01

FALSE - 모든 누락된 값을 NULL로 저장합니다. 구분 기호로 분리된 텍스트 파일에서 NULL이라는 단어를 사용하여 저장된 모든 NULL 값은 문자열 'NULL'로 가져옵니다.

Encoding = {'UTF8' | 'UTF16'} - 서버리스 SQL 풀은 분리된 UTF8 및 UTF16으로 인코딩된 구분 텍스트 파일을 읽을 수 있습니다.

DATA_COMPRESSION = *data_compression_method* - 이 인수는 외부 데이터에 대한 데이터 압축 방법을 지정합니다. 

PARQUET 파일 형식 유형은 다음 압축 메서드를 지원합니다.

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

PARQUET 외부 테이블에서 읽을 때는 이 인수가 무시되지만, [CETAS](develop-tables-cetas.md)를 사용하여 외부 테이블에 쓸 때 사용됩니다.

DELIMITEDTEXT 파일 형식 유형은 다음 압축 메서드를 지원합니다.

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'

PARSER_VERSION = 'parser_version'은 CSV 파일을 읽을 때 사용할 파서 버전을 지정합니다. 사용 가능한 파서 버전은 `1.0` 및 `2.0`입니다. 이 옵션은 서버리스 SQL 풀에서만 사용할 수 있습니다.

### <a name="example-for-create-external-file-format"></a>CREATE EXTERNAL FILE FORMAT 예제

다음 예제에서는 인구 조사 파일에 대한 외부 파일 형식을 만듭니다.

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```

## <a name="create-external-table"></a>CREATE EXTERNAL TABLE

CREATE EXTERNAL TABLE 명령은 Synapse SQL에서 Azure Blob Storage 또는 Azure Data Lake Storage에 저장된 데이터에 액세스하기 위한 외부 테이블을 만듭니다. 

### <a name="syntax-for-create-external-table"></a>CREATE EXTERNAL TABLE 구문

```syntaxsql
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name
        [, TABLE_OPTIONS = N'{"READ_OPTIONS":["ALLOW_INCONSISTENT_READS"]}' ]
        [, <reject_options> [ ,...n ] ] 
    )
[;] 

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]

<reject_options> ::=  
{  
    | REJECT_TYPE = value,  
    | REJECT_VALUE = reject_value,  
    | REJECT_SAMPLE_VALUE = reject_sample_value,
    | REJECTED_ROW_LOCATION = '/REJECT_Directory'
}   
```

### <a name="arguments-create-external-table"></a>CREATE EXTERNAL TABLE 인수

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }*

만들려는 테이블의 한 부분에서 세 부분으로 이루어진 이름입니다. 외부 테이블의 경우 서버리스 SQL 풀은 테이블 메타데이터만 저장합니다. 실제 데이터가 Synapse SQL 데이터베이스로 이동하거나 저장되지 않습니다.

<column_definition>, ...*n* ]

CREATE EXTERNAL TABLE은 열 이름, 데이터 형식 및 데이터 정렬을 구성하는 기능을 지원합니다. 외부 테이블에 DEFAULT CONSTRAINT를 사용할 수 없습니다.

>[!IMPORTANT]
>데이터 형식 및 열 수를 포함한 열 정의는 외부 파일의 데이터와 일치해야 합니다. 불일치가 있는 경우 실제 데이터를 쿼리할 때 파일 행이 거부됩니다. 거부된 행 동작을 제어하려면 거부 옵션을 참조하세요.

Parquet 파일에서 읽는 경우 읽으려는 열만 지정하고 나머지는 건너뛸 수 있습니다.


LOCATION = '*folder_or_filepath*'

Azure Blob Storage의 실제 데이터에 대한 폴더 또는 파일 경로 및 파일 이름을 지정합니다. 위치는 루트 폴더에서 시작합니다. 루트 폴더는 외부 데이터 원본에 지정된 데이터 위치입니다.

![외부 테이블에 대한 재귀적 데이터](./media/develop-tables-external-tables/folder-traversal.png)

Hadoop 외부 테이블과 달리 네이티브 외부 테이블은 경로 끝에 /**를 지정하지 않는 한 하위 폴더를 반환하지 않습니다. 이 예제에서 서버리스 SQL 풀 쿼리인 LOCATION='/webdata/'인 경우 mydata.txt에서 행을 반환합니다. mydata2.txt 및 mydata3.txt는 하위 폴더에 있으므로 반환되지 않습니다. Hadoop 테이블은 모든 하위 폴더에 있는 모든 파일을 반환합니다.

Hadoop 및 네이티브 외부 테이블은 모두 밑줄(_) 또는 마침표(.)로 시작하는 이름의 파일을 건너뜁니다.


DATA_SOURCE = *external_data_source_name*

외부 데이터 위치가 포함된 외부 데이터 원본의 이름을 지정합니다. 외부 데이터 원본을 만들려면 [CREATE EXTERNAL DATA SOURCE](#create-external-data-source)를 사용합니다.


FILE_FORMAT = *external_file_format_name*

외부 데이터에 대한 파일 형식과 압축 방법을 저장하는 외부 파일 형식 개체의 이름을 지정합니다. 외부 파일 형식을 만들려면 [CREATE EXTERNAL FILE FORMAT](#create-external-file-format)을 사용합니다.

거부 옵션 

> [!NOTE]
> 거부된 행 기능은 공개 미리 보기로 제공됩니다.
> 거부된 행 기능은 분리된 텍스트 파일 및 PARSER_VERSION 1.0에서 작동합니다.

서비스가 외부 데이터 원본에서 검색하는 *더티* 레코드를 처리하는 방법을 결정하는 거부 매개 변수를 지정할 수 있습니다. 데이터 레코드는 실제 데이터 형식이 외부 테이블의 열 정의와 일치하지 않으면 '더티'로 간주됩니다.

거부 옵션을 지정하거나 변경하지 않으면 서비스는 기본값을 사용합니다. 거부 매개 변수에 관한 이 정보는 CREATE EXTERNAL TABLE 문을 사용하여 외부 테이블을 만들 때 추가 메타데이터로 저장됩니다. 후속 SELECT 문 또는 SELECT INTO SELECT 문이 외부 테이블에서 데이터를 선택하는 경우 서비스는 거부 옵션을 사용하여 실제 쿼리가 실패하기 전 거부할 수 있는 행의 수를 결정합니다. 이 쿼리는 거부된 임계값이 초과될 때까지 (부분) 결과를 반환합니다. 그런 다음, 적절한 오류 메시지와 함께 실패합니다.


REJECT_TYPE = **value** 

현재 유일하게 지원되는 값입니다. REJECT_VALUE 옵션이 리터럴 값으로 지정되었음을 명확히 합니다.

값 

REJECT_VALUE는 리터럴 값입니다. 쿼리는 거부된 행 수가 *reject_value* 를 초과하면 실패합니다.

예를 들어 REJECT_VALUE = 5이고 REJECT_TYPE = value인 경우, SELECT 쿼리는 5개 행이 거부된 후 실패합니다.


REJECT_VALUE = *reject_value* 

쿼리가 실패하기 전에 거부될 수 있는 행 수를 지정합니다.

REJECT_TYPE = value인 경우, *reject_value* 는 0에서 2,147,483,647 사이의 정수여야 합니다.


REJECTED_ROW_LOCATION = *디렉터리 위치*

거부된 행과 해당 오류 파일을 작성해야 하는 외부 데이터 원본 내 디렉터리를 지정합니다. 지정된 경로가 존재하지 않으면 서비스는 사용자를 대신하여 경로를 만듭니다. "_rejectedrows"라는 이름의 하위 디렉터리가 생성됩니다. "_ " 문자는 위치 매개 변수에 명시적으로 명명되지 않는 한, 다른 데이터 처리를 위해 디렉터리를 이스케이프합니다. 이 디렉터리 내에는 로드 제출 시간을 기준으로 YearMonthDay_HourMinuteSecond_StatementID 형식에 따라 생성된 폴더가 있습니다(예: 20180330-173205-559EE7D2-196D-400A-806D-3BF5D007F891). 명령문 ID를 사용하여 폴더를 생성한 쿼리와 폴더의 상관 관계를 지정할 수 있습니다. 이 폴더에는 error.json 파일 및 데이터 파일이라는 두 개의 파일이 기록됩니다. 

error.json 파일에는 거부된 행과 관련된 오류가 발생한 json 배열이 포함되어 있습니다. 오류를 나타내는 각 요소에는 다음 특성이 포함됩니다.

| attribute | 설명                                                  |
| --------- | ------------------------------------------------------------ |
| Error     | 행이 거부된 이유입니다.                                  |
| 행       | 파일의 행 서수를 거부했습니다.                         |
| 열    | 열 서수를 거부했습니다.                              |
| 값     | 열 값이 거부되었습니다. 값이 100자보다 크면 처음 100자만 표시됩니다. |
| 파일      | 행이 속한 파일의 경로입니다.                            |

#### <a name="table_options"></a>TABLE_OPTIONS

TABLE_OPTIONS = *json 옵션* - 기본 파일을 읽는 방법을 설명하는 옵션 집합을 지정합니다. 현재 사용할 수 있는 유일한 옵션은 일부 일치하지 않는 읽기 작업이 발생할 수 있는 경우에도 외부 테이블에 기본 파일에 대한 업데이트를 무시하도록 지시하는 `"READ_OPTIONS":["ALLOW_INCONSISTENT_READS"]`입니다. 자주 추가되는 파일이 있는 특수한 경우에만 이 옵션을 사용합니다. 이 옵션은 서버리스 SQL 풀에서 CSV 형식으로 사용할 수 있습니다.

### <a name="permissions-create-external-table"></a>CREATE EXTERNAL TABLE 권한

외부 테이블에서 선택하려면 목록 및 읽기 권한이 있는 적절한 자격 증명이 필요합니다.

### <a name="example-create-external-table"></a>CREATE EXTERNAL TABLE 예제

다음 예제에서는 외부 테이블을 만듭니다. 첫 번째 행을 반환합니다.

```sql
CREATE EXTERNAL TABLE census_external_table
(
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex    varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = '/parquet/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

SELECT TOP 1 * FROM census_external_table
```

## <a name="create-and-query-external-tables-from-a-file-in-azure-data-lake"></a>Azure Data Lake의 파일에서 외부 테이블 만들기 및 쿼리

이제 Synapse Studio의 Data Lake 검색 기능을 사용하여 마우스 오른쪽 단추로 파일을 간단히 클릭하면 Synapse SQL 풀에서 외부 테이블을 만들고 쿼리할 수 있습니다. ADLS Gen2 스토리지 계정에서 외부 테이블을 만드는 원클릭 제스처는 Parquet 파일에 대해서만 지원됩니다. 

### <a name="prerequisites"></a>필수 구성 요소

- ADLS Gen2 계정 또는 ACL(액세스 제어 목록)에서 파일을 쿼리할 수 있는 `Storage Blob Data Contributor` 이상의 액세스 역할을 사용하여 작업 영역에 액세스할 수 있어야 합니다.

- Synapse SQL 풀(전용 또는 서버리스)에서 외부 테이블을 [만들고 쿼리할 수 있는 권한](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#permissions-2&preserve-view=true)이 최소한 있어야 합니다.

[데이터] 패널에서 외부 테이블을 만드는 데 사용할 파일을 선택합니다.
> [!div class="mx-imgBorder"]
>![externaltable1](./media/develop-tables-external-tables/external-table-1.png)

대화 상자 창이 열립니다. 전용 SQL 풀 또는 서버리스 SQL 풀을 선택하고, 테이블에 이름을 지정하고, [스크립트 열기]를 선택합니다.

> [!div class="mx-imgBorder"]
>![externaltable2](./media/develop-tables-external-tables/external-table-2.png)

파일에서 스키마를 유추하여 SQL 스크립트가 자동으로 생성됩니다.
> [!div class="mx-imgBorder"]
>![externaltable3](./media/develop-tables-external-tables/external-table-3.png)

스크립트를 실행합니다. 스크립트에서 Select Top 100 *를 자동으로 실행합니다.
> [!div class="mx-imgBorder"]
>![externaltable4](./media/develop-tables-external-tables/external-table-4.png)

이제 외부 테이블이 만들어집니다. 사용자는 나중에 [데이터] 창에서 직접 쿼리하여 이 외부 테이블의 콘텐츠를 검색할 수 있습니다.
> [!div class="mx-imgBorder"]
>![externaltable5](./media/develop-tables-external-tables/external-table-5.png)

## <a name="next-steps"></a>다음 단계

쿼리 결과를 Azure Storage의 외부 테이블에 저장하는 방법은 [CETAS](develop-tables-cetas.md) 문서를 확인하세요. 또는 [Apache Spark for Azure Synapse 외부 테이블](develop-storage-files-spark-tables.md) 쿼리를 시작할 수 있습니다.
