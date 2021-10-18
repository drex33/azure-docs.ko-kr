---
title: 서버리스 SQL 풀에서 OPENROWSET를 사용하는 방법
description: 이 문서에서는 서버리스 SQL 풀의 OPENROWSET 구문을 설명하고 인수를 사용하는 방법을 설명합니다.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/07/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 392d457ead16d0bcfc057282886669a01e24ff3e
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730378"
---
# <a name="how-to-use-openrowset-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 서버리스 SQL 풀을 사용하여 OPENROWSET를 사용하는 방법

`OPENROWSET(BULK...)` 함수를 사용하여 Azure Storage의 파일에 액세스할 수 있습니다. `OPENROWSET` 함수는 원격 데이터 소스의 콘텐츠(예: 파일)를 읽고 이 콘텐츠를 일련의 행으로 반환합니다. 서버리스 SQL 풀 리소스 내에서 OPENROWSET 함수를 호출하고 BULK 옵션을 지정하여 OPENROWSET 대량 행 집합 공급자에 액세스합니다.  

`OPENROWSET` 함수는 쿼리의 `FROM` 절에서 테이블 이름 `OPENROWSET`인 것처럼 참조될 수 있습니다. 이 함수는 파일의 데이터를 읽어서 행 세트로 반환할 수 있는 기본 제공 BULK 공급자를 통해 대량 작업을 지원합니다.

## <a name="data-source"></a>데이터 원본

Synapse SQL의 OPENROWSET 함수는 데이터 소스에서 파일 콘텐츠를 읽습니다. 데이터 소스는 Azure 스토리지 계정이며 `OPENROWSET` 함수에서 명시적으로 참조되거나 읽으려는 파일의 URL에서 동적으로 유추될 수 있습니다.
`OPENROWSET` 함수는 파일을 포함하는 데이터 소스를 지정하는 `DATA_SOURCE` 매개 변수를 선택적으로 포함할 수 있습니다.
- `DATA_SOURCE`가 없는 `OPENROWSET`는 `BULK` 옵션으로 지정된 URL 위치에서 파일의 콘텐츠를 직접 읽는 데 사용할 수 있습니다.

    ```sql
    SELECT *
    FROM OPENROWSET(BULK 'http://<storage account>.dfs.core.windows.net/container/folder/*.parquet',
                    FORMAT = 'PARQUET') AS file
    ```

이 방법은 사전 구성 없이 파일 콘텐츠를 쉽고 빠르게 읽을 수 있는 방법입니다. 이 옵션을 사용하면 기본 인증 옵션을 사용하여 스토리지에 액세스할 수 있습니다(Azure AD 로그인의 경우 Azure AD 통과, SQL 로그인의 경우 SAS 토큰). 

- `DATA_SOURCE`가 있는 `OPENROWSET`는 지정된 스토리지 계정의 파일에 액세스하는 데 사용할 수 있습니다.

    ```sql
    SELECT *
    FROM OPENROWSET(BULK '/folder/*.parquet',
                    DATA_SOURCE='storage', --> Root URL is in LOCATION of DATA SOURCE
                    FORMAT = 'PARQUET') AS file
    ```


    이 옵션을 사용하면 데이터 소스에서 스토리지 계정의 위치를 구성하고 스토리지에 액세스하는 데 사용해야 하는 인증 방법을 지정할 수 있습니다. 
    
    > [!IMPORTANT]
    > `DATA_SOURCE`가 있는 `OPENROWSET`는 스토리지 파일에 쉽고 빠르게 액세스하는 방법을 제공하지만 제한된 인증 옵션을 제공합니다. 예를 들어 Azure AD 보안 주체는 [Azure AD ID](develop-storage-files-storage-access-control.md?tabs=user-identity) 또는 공개적으로 사용 가능한 파일을 통해서만 파일에 액세스할 수 있습니다. 더 강력한 인증 옵션이 필요하면 `DATA_SOURCE` 옵션을 사용하고 스토리지에 액세스하는 데 사용할 자격 증명을 정의하십시오.


## <a name="security"></a>보안

데이터베이스 사용자가 `OPENROWSET` 함수를 사용하려면 `ADMINISTER BULK OPERATIONS` 권한이 있어야 합니다.

또한 스토리지 관리자는 유효한 SAS 토큰을 제공하거나 Azure AD 보안 주체가 스토리지 파일에 액세스하도록 설정하여 사용자가 파일에 액세스할 수 있도록 해야 합니다. 스토리지 액세스 제어에 대한 자세한 내용은 [이 문서](develop-storage-files-storage-access-control.md)를 참조하세요.

`OPENROWSET`는 다음 규칙을 사용하여 스토리지 인증 방법을 결정합니다.
- `DATA_SOURCE`가 없는 `OPENROWSET`에서 인증 메커니즘은 호출자 유형에 따라 달라집니다.
  - 모든 사용자는 `DATA_SOURCE` 없이 `OPENROWSET`를 사용하여 Azure 스토리지에서 공개적으로 사용 가능한 파일을 읽을 수 있습니다.
  - Azure 스토리지에서 Azure AD 사용자가 기본 파일에 액세스할 수 있도록 허용된 경우(예: 호출자에게 Azure 스토리지에 대한 `Storage Reader` 권한이 있는 경우) Azure AD 로그인은 자체 [Azure AD ID](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types)를 사용하여 보호된 파일에 액세스할 수 있습니다.
  - SQL 로그인도 `DATA_SOURCE` 없이 `OPENROWSET`를 사용하여 공개적으로 사용 가능한 파일, SAS 토큰으로 보호된 파일 또는 Synapse 작업 영역의 관리 ID에 액세스 할 수 있습니다. 스토리지 파일에 대한 액세스를 허용하려면 [서버 범위 자격 증명을 만들어야](develop-storage-files-storage-access-control.md#examples) 합니다. 
- `DATA_SOURCE`가 있는 `OPENROWSET`에서 인증 메커니즘은 참조된 데이터 원본에 할당된 데이터베이스 범위 자격 증명에 정의됩니다. 이 옵션을 사용하면 공개적으로 사용 가능한 스토리지에 액세스하거나 SAS 토큰, 작업 영역의 관리 ID 또는 [호출자의 Azure AD ID](develop-storage-files-storage-access-control.md?tabs=user-identity#supported-storage-authorization-types)(호출자가 Azure AD 보안 주체인 경우)를 사용하여 스토리지에 액세스할 수 있습니다. `DATA_SOURCE`가 공용이 아닌 Azure 스토리지를 참조하는 경우에는 [데이터베이스 범위 자격 증명](develop-storage-files-storage-access-control.md#examples)을 만들어서 `DATA SOURCE`에서 참조하여 스토리지 파일에 대한 액세스를 허용해야 합니다.

호출자에게 자격 증명에 대한 `REFERENCES` 권한이 있어야 이 권한을 사용하여 스토리지를 인증할 수 있습니다.

## <a name="syntax"></a>구문

```syntaxsql
--OPENROWSET syntax for reading Parquet or Delta Lake (preview) files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ]
    FORMAT= ['PARQUET' | 'DELTA'] }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , [DATA_SOURCE = <data source name>, ] 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal' | 'json_path'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPECHAR = 'char' ] 
[ , FIRSTROW = 'first_row' ]     
[ , FIELDQUOTE = 'quote_characters' ]
[ , DATA_COMPRESSION = 'data_compression_method' ]
[ , PARSER_VERSION = 'parser_version' ]
[ , HEADER_ROW = { TRUE | FALSE } ]
[ , DATAFILETYPE = { 'char' | 'widechar' } ]
[ , CODEPAGE = { 'ACP' | 'OEM' | 'RAW' | 'code_page' } ]
[ , ROWSET_OPTIONS = '{"READ_OPTIONS":["ALLOW_INCONSISTENT_READS"]}' ]
```

## <a name="arguments"></a>인수

쿼리할 대상 데이터를 포함하고 있는 입력 파일에 대해 두 가지 옵션을 선택할 수 있습니다. 유효한 값은 다음과 같습니다.

- 'CSV' - 행/열 구분 기호로 구분된 텍스트 파일을 포함합니다. 모든 문자를 필드 구분 기호로 사용할 수 있습니다. TSV: FIELDTERMINATOR = tab을 예로 들 수 있습니다.

- 'PARQUET' - Parquet 형식의 이진 파일 

- 'DELTA' - Delta Lake(미리 보기) 형식으로 구성된 Parquet 파일 세트 

**'unstructured_data_path'**

데이터에 대한 경로를 설정하는 unstructured_data_path는 절대 또는 상대 경로일 수 있습니다.
- `\<prefix>://\<storage_account_path>/\<storage_path>` 형식의 절대 경로를 사용하면 사용자가 파일을 직접 읽을 수 있습니다.
- `<storage_path>` 형식의 상대 경로는 `DATA_SOURCE` 매개 변수와 함께 사용해야 하며 `EXTERNAL DATA SOURCE`에 정의된 <storage_account_path> 위치 내의 파일 패턴을 설명합니다. 

아래에는 특정 외부 데이터 소스에 연결되는 관련 \<storage account path> 값이 있습니다. 

| 외부 데이터 원본       | 접두사 | 스토리지 계정 경로                                 |
| -------------------------- | ------ | ---------------------------------------------------- |
| Azure Blob Storage         | http[s]  | \<storage_account>.blob.core.windows.net/path/file   |
| Azure Blob Storage         | wasb[s]  | \<container>@\<storage_account>.blob.core.windows.net/path/file |
| Azure Data Lake Store Gen1 | http[s]  | \<storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | http[s]  | \<storage_account>.dfs.core.windows.net /path/file   |
| Azure Data Lake Store Gen2 | abfs[s]  | [\<file_system>@\<account_name>.dfs.core.windows.net/path/file](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md#uri-syntax)              |
||||

'\<storage_path>'

읽으려는 폴더 또는 파일을 가리키는 스토리지 내의 경로입니다. 경로가 컨테이너 또는 폴더를 가리키는 경우 해당 컨테이너 또는 폴더에서 모든 파일을 읽습니다. 하위 폴더의 파일은 포함되지 않습니다. 

와일드카드 문자를 사용하여 여러 파일 또는 폴더를 대상으로 지정할 수 있습니다. 여러 비연속 와일드카드 문자를 사용할 수 있습니다.
다음은 */csv/population* 으로 시작하는 모든 폴더에서 *population* 으로 시작하는 모든 *csv* 파일을 읽는 예제입니다.  
`https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv`

unstructured_data_path를 폴더로 지정하면 서버리스 SQL 풀 쿼리가 해당 폴더에서 파일을 검색합니다. 

다음 예제와 같이 /*를 경로 끝에 지정하여 서버리스 SQL 풀에서 폴더를 트래버스하도록 지시할 수 있습니다. `https://sqlondemandstorage.blob.core.windows.net/csv/population/**`

> [!NOTE]
> Hadoop 및 PolyBase와 달리 /**를 경로 끝에 지정하지 않으면 서버리스 SQL 풀에서 하위 폴더를 반환하지 않습니다. Hadoop 및 PolyBase와 마찬가지로 파일 이름이 밑줄(_) 또는 마침표(.)로 시작하는 파일을 반환하지 않습니다.

아래 예제에서 unstructured_data_path=`https://mystorageaccount.dfs.core.windows.net/webdata/`인 경우 서버리스 SQL 풀 쿼리는 mydata.txt에서 행을 반환합니다. mydata2.txt 및 mydata3.txt는 하위 폴더에 있으므로 반환되지 않습니다.

![외부 테이블에 대한 재귀적 데이터](./media/develop-openrowset/folder-traversal.png)

`[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]`

WITH 절을 사용하여 파일에서 읽을 열을 지정할 수 있습니다.

- CSV 데이터 파일의 경우 모든 열을 읽으려면 열 이름과 해당 데이터 형식을 입력합니다. 열의 하위 세트를 원하는 경우 서수를 사용하여 원본 데이터 파일에서 서수를 기준으로 열을 선택합니다. 열은 서수 지정을 기준으로 바인딩됩니다. HEADER_ROW = TRUE를 사용하는 경우 열 바인딩은 서수 위치 대신 열 이름으로 수행됩니다.
    > [!TIP]
    > CSV 파일에서도 WITH 절을 생략할 수 있습니다. 데이터 형식은 파일 콘텐츠에서 자동으로 유추됩니다. HEADER_ROW 인수를 사용하여 헤더 행에서 열 이름을 읽을 때 헤더 행의 존재 여부를 지정할 수 있습니다. 자세한 내용은 [자동 스키마 검색](#automatic-schema-discovery)을 참조하세요.
    
- Parquet 또는 Delta Lake 파일의 경우 원본 데이터 파일의 열 이름과 일치하는 열 이름을 입력합니다. 열은 이름으로 바인딩되고 대/소문자를 구분합니다. WITH 절을 생략하면 Parquet 파일의 모든 열이 반환됩니다.
    > [!IMPORTANT]
    > Parquet 및 Delta Lake 파일의 열 이름은 대/소문자를 구분합니다. 파일에서 열 이름 대/소문자 구분과 다른 대/소문자의 열 이름을 지정하면 해당 열에 대해 `NULL` 값이 반환됩니다.


column_name은 출력 열의 이름입니다. 제공되는 경우 이 이름은 원본 파일의 열 이름과 JSON 경로에 제공된 열 이름(있는 경우)을 재정의합니다. json_path가 제공되지 않으면 '$.column_name'으로 자동 추가됩니다. 동작에 대한 json_path 인수를 확인합니다.

column_type은 출력 열의 데이터 형식입니다. 여기서 암시적 데이터 형식 변환이 수행됩니다.

column_ordinal은 원본 파일에 있는 열의 서수입니다. Parquet 파일은 이름을 기준으로 바인딩되므로 이 인수가 무시됩니다. 다음 예제에서는 CSV 파일의 두 번째 열만 반환합니다.

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```

json_path = 열 또는 중첩 속성에 대한 [JSON 경로 식](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true). 기본 [경로 모드](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true#PATHMODE)는 lax입니다.

> [!NOTE]
> strict 모드에서는 제공된 경로가 존재하지 않으면 쿼리가 실패하고 오류가 발생합니다. lax 모드에서는 쿼리가 성공하고 JSON 경로 식이 NULL로 계산됩니다.

**\<bulk_options>**

FIELDTERMINATOR ='field_terminator'

사용할 필드 종결자를 지정합니다. 기본 필드 종결자는 쉼표(" **,** ")입니다.

ROWTERMINATOR ='row_terminator'`

사용할 행 종결자를 지정합니다. 행 종결자를 지정하지 않으면 기본 종결자 중 하나가 사용됩니다. PARSER_VERSION = '1.0'의 기본 종결자는 \r\n, \n 및 \r입니다. PARSER_VERSION = '2.0'의 기본 종결자는 \r\n 및 \n입니다.

> [!NOTE]
> PARSER_VERSION=‘1.0’을 사용하고 \n(줄 바꿈)을 행 종결자로 지정하면 \r(캐리지 리턴) 문자가 접두사로 자동 지정되므로 \r\n이 행 종결자가 됩니다.

ESCAPE_CHAR = 'char'

자체 및 파일의 모든 구분 기호 값을 이스케이프하는 데 사용되는 파일의 문자를 지정합니다. 이스케이프 문자 뒤에 자체 또는 구분 기호 값 이외의 값이 있으면 값을 읽을 때 이스케이프 문자가 삭제됩니다. 

ESCAPECHAR 매개 변수는 FIELDQUOTE가 사용하도록 설정되었는지 여부에 관계없이 적용됩니다. 따옴표로 묶은 문자를 이스케이프하는 데 사용되지 않습니다. 따옴표 문자는 다른 따옴표 문자로 이스케이프해야 합니다. 따옴표로 묶은 문자는 값이 따옴표 문자로 캡슐화된 경우에만 열 값 내에 나타날 수 있습니다.

FIRSTROW = 'first_row' 

로드할 첫 번째 행의 번호를 지정합니다. 기본값은 1이며, 지정한 데이터 파일의 첫 번째 행을 가리킵니다. 행 번호는 행 종결자를 계산하여 결정됩니다. FIRSTROW는 1부터 시작합니다.

FIELDQUOTE = 'field_quote' 

CSV 파일에 따옴표 문자로 사용될 문자를 지정합니다. 지정하지 않으면 따옴표 문자(")가 사용됩니다. 

DATA_COMPRESSION = 'data_compression_method'

압축 방법을 지정합니다. PARSER_VERSION='1.0'에서만 지원됩니다. 다음 압축 방법이 지원됩니다.

- GZIP

PARSER_VERSION = 'parser_version'

파일을 읽을 때 사용할 파서 버전을 지정합니다. 현재 지원되는 CSV 파서 버전은 1.0 및 2.0입니다.

- PARSER_VERSION = '1.0'
- PARSER_VERSION = '2.0'

CSV 파서 버전 1.0이 기본값이며 기능이 풍부합니다. 버전 2.0은 성능을 위해 빌드되었으며 일부 옵션과 인코딩은 지원하지 않습니다. 

CSV 파서 버전 1.0 세부 정보:

- 다음 옵션은 지원되지 않습니다. HEADER_ROW
- 기본 종결자는 \r\n, \n, \r입니다. 
- \n(줄 바꿈)을 행 종결자로 지정하면 \r(캐리지 리턴) 문자가 접두사로 자동 지정되므로 \r\n이 행 종결자가 됩니다.

CSV 파서 버전 2.0 세부 정보:

- 일부 데이터 유형은 지원되지 않습니다.
- 최대 문자 열 길이는 8000입니다.
- 최대 행 크기 제한은 8MB입니다.
- 다음 옵션은 지원되지 않습니다. DATA_COMPRESSION.
- 따옴표로 묶인 빈 문자열("")은 빈 문자열로 해석됩니다.
- DATEFORMAT SET 옵션은 적용되지 않습니다.
- DATE 데이터 형식에 지원되는 형식: YYYY-MM-DD
- TIME 데이터 형식에 지원되는 형식: HH:MM:SS[.fractional seconds]
- DATETIME2 데이터 형식에 지원되는 형식: YYYY-MM-DD HH:MM:SS[.fractional seconds]
- 기본 종결자는 \r\n, \n입니다.

HEADER_ROW = { TRUE | FALSE }

CSV 파일에 헤더 행이 포함되는지 여부를 지정합니다. 기본값은 PARSER_VERSION='2.0'에서 지원되는 `FALSE.`입니다. TRUE이면 FIRSTROW 인수에 따라 첫 번째 행에서 열 이름을 읽습니다. WITH를 사용하여 TRUE와 스키마를 지정한 경우 열 이름 바인딩은 서수 위치가 아닌 열 이름으로 수행됩니다.

DATAFILETYPE = { 'char' | 'widechar' }

인코딩 지정: `char`는 UTF8에 사용되고, `widechar`는 UTF16 파일에 사용됩니다.

CODEPAGE = { 'ACP' | 'OEM' | 'RAW' | 'code_page' }

데이터 파일에서 데이터의 코드 페이지를 지정합니다. 기본값은 65001(UTF-8 인코딩)입니다. 이 옵션에 대한 자세한 내용을 [여기](/sql/t-sql/functions/openrowset-transact-sql?view=sql-server-ver15&preserve-view=true#codepage)를 클릭하세요.

ROWSET_OPTIONS = '{"READ_OPTIONS":["ALLOW_INCONSISTENT_READS"]}'

이 옵션은 쿼리를 실행하는 동안 파일 수정 검사를 사용하지 않도록 설정하고 쿼리가 실행되는 동안 업데이트되는 파일을 읽습니다. 이 옵션은 쿼리가 실행되는 동안 추가되는 추가 전용 파일을 읽어야 하는 경우에 유용합니다. 추가 가능한 파일에서 기존 내용은 업데이트되지 않고 새 행만 추가됩니다. 따라서 업데이트 가능한 파일에 비해 결과가 잘못될 가능성이 최소화됩니다. 이 옵션을 사용하면 오류를 처리하지 않고 자주 추가되는 파일을 읽을 수 있습니다. [추가 가능한 CSV 파일 쿼리](query-single-csv-file.md#querying-appendable-files) 섹션에서 자세한 내용을 확인하세요.

## <a name="fast-delimited-text-parsing"></a>분리된 텍스트에 대한 빠른 구문 분석

사용할 수 있는 두 가지 분리된 텍스트 구문 파서 버전이 있습니다. CSV 파서 버전 1.0은 기본값이고 기능이 풍부하지만, 파서 버전 2.0은 성능을 위해 빌드되었습니다. 향상된 파서 2.0 성능은 고급 구문 분석 기술 및 다중 스레딩에서 제공됩니다. 파일 크기가 커질수록 속도 차이도 커집니다.

## <a name="automatic-schema-discovery"></a>자동 스키마 검색

WITH 절을 생략하여 스키마를 인식하거나 지정하지 않고도 CSV 및 Parquet 파일을 모두 쉽게 쿼리할 수 있습니다. 열 이름 및 데이터 형식은 파일에서 유추됩니다.

Parquet 파일에는 읽을 열 메타데이터가 포함되어 있으며, 형식 매핑은 [Parquet에 대한 형식 매핑](#type-mapping-for-parquet)에서 확인할 수 있습니다. 샘플은 [스키마를 지정하지 않고 Parquet 파일 읽기](#read-parquet-files-without-specifying-schema)를 확인하세요.

CSV 파일의 경우 열 이름은 헤더 행에서 읽을 수 있습니다. HEADER_ROW 인수를 사용하여 헤더 행이 있는지 여부를 지정할 수 있습니다. HEADER_ROW = FALSE이면 C1, C2, ... Cn의 제네릭 열 이름이 사용됩니다. 여기서 n은 파일의 열 수입니다. 데이터 형식은 처음 100개의 데이터 행에서 유추됩니다. 샘플은 [스키마를 지정하지 않고 CSV 파일 읽기](#read-csv-files-without-specifying-schema)를 확인하세요.

> [!IMPORTANT]
> 정보가 부족하여 적절한 데이터 형식을 유추할 수 없고 더 큰 데이터 형식이 대신 사용되는 경우가 있습니다. 이는 성능 오버헤드를 발생시키며, 특히 varchar(8000)로 유추되는 문자 열에 중요합니다. 최적의 성능을 위해 [유추된 데이터 형식을 확인](./best-practices-serverless-sql-pool.md#check-inferred-data-types)하고 [적절한 데이터 형식을 사용](./best-practices-serverless-sql-pool.md#use-appropriate-data-types)하세요.

### <a name="type-mapping-for-parquet"></a>Parquet에 대한 형식 매핑

Parquet 및 Delta Lake 파일에는 모든 열에 대한 형식 설명이 포함되어 있습니다. 다음 표에서는 Parquet 형식이 SQL 네이티브 형식에 매핑되는 방법을 설명합니다.

| Parquet 형식 | Parquet 논리 형식(주석) | SQL 데이터 형식 |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY / BYTE_ARRAY | | varbinary |
| DOUBLE | | float |
| FLOAT | | real |
| INT32 | | int |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*(UTF8 데이터 정렬) |
| BINARY |STRING |varchar \*(UTF8 데이터 정렬) |
| BINARY |ENUM|varchar \*(UTF8 데이터 정렬) |
| FIXED_LEN_BYTE_ARRAY |UUID |uniqueidentifier |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar(8000) \*(UTF8 데이터 정렬) |
| BINARY |BSON | 지원되지 않음 |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL | 지원되지 않음 |
| INT32 |INT(8, true) |smallint |
| INT32 |INT(16, true) |smallint |
| INT32 |INT(32, true) |int |
| INT32 |INT(8, false) |tinyint |
| INT32 |INT(16, false) |int |
| INT32 |INT(32, false) |bigint |
| INT32 |DATE |date |
| INT32 |DECIMAL |decimal |
| INT32 |TIME(MILLIS)|time |
| INT64 |INT(64, true) |bigint |
| INT64 |INT(64, false) |decimal(20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |TIME(MICROS) |시간 - TIME(NANOS)은 지원되지 않습니다. |
|INT64 |TIMESTAMP(MILLIS / MICROS) |datetime2 - TIMESTAMP(NANOS)는 지원되지 않습니다. |
|[복합 형식](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |명단 등록 |varchar(8000), JSON으로 직렬화됨 |
|[복합 형식](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar(8000), JSON으로 직렬화됨 |

## <a name="examples"></a>예

### <a name="read-csv-files-without-specifying-schema"></a>스키마를 지정하지 않고 CSV 파일 읽기

다음 예제에서는 열 이름 및 데이터 형식을 지정하지 않고 헤더 행이 포함된 CSV 파일을 읽습니다. 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0',
    HEADER_ROW = TRUE) as [r]
```

다음 예제에서는 열 이름 및 데이터 형식을 지정하지 않고 헤더 행이 포함되지 않은 CSV 파일을 읽습니다. 

```sql
SELECT 
    *
FROM OPENROWSET(
    BULK 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.csv',
    FORMAT = 'CSV',
    PARSER_VERSION = '2.0') as [r]
```

### <a name="read-parquet-files-without-specifying-schema"></a>스키마를 지정하지 않고 Parquet 파일 읽기

다음 예제는 열 이름 및 데이터 형식을 지정하지 않고 Parquet 형식의 인구 조사 데이터 세트에서 첫 번째 행의 모든 열을 반환합니다. 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```

### <a name="read-delta-lake-files-without-specifying-schema"></a>스키마를 지정하지 않고 Delta Lake 파일 읽기

다음 예제는 열 이름 및 데이터 형식을 지정하지 않고 Delta Lake 형식의 인구 조사 데이터 세트에서 첫 번째 행의 모든 열을 반환합니다. 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='DELTA'
    ) AS [r]
```

### <a name="read-specific-columns-from-csv-file"></a>CSV 파일에서 특정 열 읽기

다음 예제는 population*.csv 파일에서 서수가 1과 4인 열 두 개만 반환합니다. 다음과 같이 파일에 헤더 행이 없기 때문에 첫 번째 줄에서 읽기를 시작합니다.

```sql
SELECT 
    * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```

### <a name="read-specific-columns-from-parquet-file"></a>Parquet 파일에서 특정 열 읽기

다음 예제에서는 인구 조사 데이터 세트에서 첫 번째 행의 두 열만 Parquet 형식으로 반환합니다. 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    [stateName] VARCHAR (50),
    [population] bigint
) AS [r]
```

### <a name="specify-columns-using-json-paths"></a>JSON 경로를 사용하여 열 지정

다음 예제에서는 WITH 절에서 [JSON 경로 식](/sql/relational-databases/json/json-path-expressions-sql-server?view=azure-sqldw-latest&preserve-view=true)을 사용하는 방법을 보여주고 strict 및 lax 경로 모드의 차이점을 보여줍니다. 

```sql
SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    )
WITH (
    --lax path mode samples
    [stateName] VARCHAR (50), -- this one works as column name casing is valid - it targets the same column as the next one
    [stateName_explicit_path] VARCHAR (50) '$.stateName', -- this one works as column name casing is valid
    [COUNTYNAME] VARCHAR (50), -- STATEname column will contain NULLs only because of wrong casing - it targets the same column as the next one
    [countyName_explicit_path] VARCHAR (50) '$.COUNTYNAME', -- STATEname column will contain NULLS only because of wrong casing and default path mode being lax

    --strict path mode samples
    [population] bigint 'strict $.population' -- this one works as column name casing is valid
    --,[population2] bigint 'strict $.POPULATION' -- this one fails because of wrong casing and strict path mode
)
AS [r]
```

## <a name="next-steps"></a>다음 단계

더 많은 샘플을 보려면 [쿼리 데이터 스토리지 빠른 시작](query-data-storage.md)을 참조하여 `OPENROWSET`를 사용해 [CSV](query-single-csv-file.md), [PARQUET](query-parquet-files.md), [DELTA LAKE](query-delta-lake-format.md) 및 [JSON](query-json-files.md) 파일 형식을 읽는 방법을 알아보세요. 최적의 성능을 얻으려면 [모범 사례](./best-practices-serverless-sql-pool.md)를 확인하세요. [CETAS](develop-tables-cetas.md)를 사용하여 쿼리 결과를 Azure Storage에 저장하는 방법도 알아볼 수 있습니다.
