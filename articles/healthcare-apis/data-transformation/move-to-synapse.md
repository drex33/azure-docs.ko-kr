---
title: Azure API for FHIR 데이터를 Azure Synapse Analytics 이동
description: 이 문서에서는 FHIR 데이터를 Synapse로 이동하는 것을 설명합니다.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/13/2021
ms.author: ginle
ms.openlocfilehash: e548361f6cf8d638609dd9daa83a175fa49f3679
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122435480"
---
# <a name="moving-data-from-azure-api-for-fhir-to-azure-synapse-analytics"></a>Azure API for FHIR Azure Synapse Analytics 데이터 이동

이 문서에서는 데이터 통합, 엔터프라이즈 데이터 웨어하우징 및 빅 데이터 분석을 결합하는 무한 분석 서비스인 Azure API for FHIR 데이터를 [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/)이동하는 몇 가지 방법을 알아봅니다. 

FHIR 서버에서 Synapse로 데이터를 이동하려면 FHIR 작업을 사용하여 데이터를 내보낸 `$export` 다음, 데이터를 변환하고 Synapse로 로드하는 일련의 단계가 포함됩니다. 이 문서에서는 몇 가지 방법 중 두 가지 방법을 안내합니다. 두 방법 모두 FHIR 리소스를 Synapse로 이동하는 동안 테이블 형식으로 변환하는 방법을 보여줍니다.

* **T-SQL 사용하여 내보낸 데이터를 Synapse로 로드합니다.** `$export`작업을 사용하여 FHIR 리소스를 **ADL Gen 2(Azure Data Lake Gen 2) Blob Storage로** `NDJSON` 형식으로 이동합니다. T-SQL 사용하여 Synapse의 **서버리스 또는 전용 SQL 풀에** 스토리지의 데이터를 로드합니다. [Synapse](../../synapse-analytics/get-started-pipelines.md)파이프라인을 사용하여 이러한 단계를 강력한 데이터 이동 파이프라인으로 변환합니다.
* **FHIR 분석 Pipelines OSS 리포지션의 도구를 사용합니다.** [FHIR 분석 파이프라인](https://github.com/microsoft/FHIR-Analytics-Pipelines) 리포지터에는 **ADF(Azure Data Factory) 파이프라인을** 만들어 FHIR 데이터를 **CDM(Common Data Model) 폴더로 이동하고 CDM 폴더에서** Synapse로 이동할 수 있는 도구가 포함되어 있습니다.

## <a name="load-exported-data-to-synapse-using-t-sql"></a>T-SQL 사용하여 내보낸 데이터를 Synapse로 로드

### <a name="export-for-moving-fhir-data-into-azure-data-lake-gen-2-storage"></a>`$export` Azure Data Lake Gen 2 스토리지로 FHIR 데이터 이동

:::image type="content" source="media/export-data/export-azure-storage-option.png" alt-text="$export 사용하여 Azure Storage에서 Synapse로":::

#### <a name="configure-your-fhir-server-to-support-export"></a>지원하도록 FHIR 서버 구성 `$export`

Azure API for FHIR `$export` FHIR 데이터의 전체 또는 필터링된 하위 집합을 형식으로 내보내기 위해 FHIR 사양에 정의된 작업을 `NDJSON` 구현합니다. 또한 내보내는 동안 FHIR 데이터를 익명화하기 위해 [식별되지 않은](./de-identified-export.md) 내보내기를 지원합니다. 를 사용하는 경우 기본적으로 해당 `$export` 기능이 에 이미 통합되어 있는 식별 해제 기능을 얻게 `$export` 됩니다.

FHIR 데이터를 Azure Blob Storage로 내보내려면 먼저 데이터를 스토리지 계정으로 내보내도록 FHIR 서버를 구성해야 합니다. (1) 관리 ID를 사용하도록 설정해야 합니다. (2) 스토리지 계정의 Access Control 이동하여 역할 할당을 추가하고,(3) 에 대한 스토리지 계정을 선택해야 `$export` 합니다. 자세한 단계별 단계는 여기에서 찾을 수 [있습니다.](./configure-export-data.md)

모든 종류의 Azure Storage 계정으로 데이터를 내보내도록 서버를 구성할 수 있지만 Synapse와 가장 잘 맞도록 ADL Gen 2로 내보내는 것이 좋습니다.

#### <a name="using-export-command"></a>명령 사용 `$export`

FHIR 서버를 구성한 후 [설명서에](./export-data.md#using-export-command) 따라 시스템, 환자 또는 그룹 수준에서 FHIR 리소스를 내보낼 수 있습니다. 예를 들어 다음 명령을 사용하여 의 환자와 관련된 모든 FHIR 데이터를 내보낼 수 있습니다. 이 `Group` `$export` 명령은 필드에서 ADL Gen 2 Blob Storage 이름을 지정합니다. `{{BlobContainer}}`

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}  
```

`_type`위의 호출에서 매개 변수를 사용하여 `$export` 내보내려는 리소스를 제한할 수도 있습니다. 예를 들어 다음 호출은 `Patient` , `MedicationRequest` 및 `Observation` 리소스만 내보냅니다.

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}&
_type=Patient,MedicationRequest,Condition
```  

지원되는 다양한 매개 변수에 대한 자세한 내용은 `$export` [쿼리 매개 변수](./export-data.md#settings-and-parameters)에 대한 페이지 섹션을 확인하세요.

### <a name="create-a-synapse-workspace"></a>Synapse 작업 영역 만들기

Synapse를 사용하기 전에 Synapse 작업 영역이 필요합니다. Azure Portal Azure Synapse Analytics 서비스를 만듭니다. 추가 단계별 가이드는 여기에서 찾을 수 [있습니다.](../../synapse-analytics/get-started-create-workspace.md) `ADLSGEN2`작업 영역을 만들려면 계정이 필요합니다. Azure Synapse 작업 영역에서는 이 스토리지 계정을 사용하여 Synapse 작업 영역 데이터를 저장합니다.

작업 영역을 만든 후 에서 작업 영역에 로그인하거나 Azure Portal Synapse Studio 시작하여 Synapse Studio 작업 영역을 볼 수 https://web.azuresynapse.net 있습니다.

#### <a name="creating-a-linked-service-between-azure-storage-and-synapse"></a>Azure Storage와 Synapse 간에 연결된 서비스 만들기

데이터를 Synapse로 이동하려면 Azure Storage 계정을 Synapse와 연결하는 연결된 서비스를 만들어야 합니다. 자세한 단계별 단계는 여기에서 찾을 수 [있습니다.](../../synapse-analytics/data-integration/data-integration-sql-pool.md#create-linked-services)

1. Synapse Studio **관리** 탭으로 이동하고 **외부 연결에서** **연결된 서비스를 선택합니다.**
2. **새로** 추가를 선택하여 새 연결된 서비스를 추가합니다.
3. 목록에서 **Azure Data Lake Storage Gen2를** 선택하고 **계속을** 선택합니다.
4. 인증 자격 증명을 입력합니다. 작업을 완료하면 **만들기** 를 선택합니다.

이제 ADL Gen 2 스토리지와 Synapse 간에 연결된 서비스가 생겼으므로 Synapse SQL 풀을 사용하여 FHIR 데이터를 로드하고 분석할 준비가 되었습니다.

### <a name="decide-between-serverless-and-dedicated-sql-pool"></a>서버리스 풀과 전용 SQL 풀 중에서 결정

Azure Synapse Analytics 서버리스 SQL 풀과 전용 SQL 풀의 두 가지 SQL 풀을 제공합니다. 서버리스 SQL 풀은 리소스 프로비저닝 없이 서버리스 SQL 엔드포인트를 사용하여 Blob Storage에서 직접 데이터를 쿼리하는 유연성을 제공합니다. 전용 SQL 풀은 고성능 및 동시성을 위한 처리 능력을 갖추고 있으며 엔터프라이즈 규모 데이터 웨어하우징 기능에 권장됩니다. 두 SQL 풀에 대한 자세한 내용은 SQL 아키텍처의 [Synapse 설명서 페이지를](../../synapse-analytics/sql/overview-architecture.md) 확인하세요.

#### <a name="using-serverless-sql-pool"></a>서버리스 SQL 풀 사용

서버리스이므로 설정할 인프라 또는 유지 관리해야 하는 클러스터가 없습니다. 작업 영역을 만드는 즉시 Synapse Studio 데이터 쿼리를 시작할 수 있습니다.

예를 들어 다음 쿼리를 사용하여 선택한 필드를 에서 `Patient.ndjson` 테이블 형식 구조로 변환할 수 있습니다.

```sql
SELECT * FROM  
OPENROWSET(bulk 'https://{{youraccount}}.blob.core.windows.net/{{yourcontainer}}/Patient.ndjson', 
FORMAT = 'csv', 
FIELDTERMINATOR ='0x0b', 
FIELDQUOTE = '0x0b')  
WITH (doc NVARCHAR(MAX)) AS rows     
CROSS APPLY OPENJSON(doc)     
WITH ( 
    ResourceId VARCHAR(64) '$.id', 
    Active VARCHAR(10) '$.active', 
    FullName VARCHAR(100) '$.name[0].text', 
    Gender VARCHAR(20) '$.gender', 
       ...
) 
```

위의 쿼리에서 `OPENROWSET` 함수는 Azure Storage 파일에 액세스하고 `OPENJSON` JSON 텍스트를 구문 분석하고 JSON 입력 속성을 행 및 열로 반환합니다. 이 쿼리가 실행될 때마다 서버리스 SQL 풀은 Blob Storage에서 파일을 읽고, JSON을 구문 분석하고, 필드를 추출합니다.

아래와 같이 [외부 테이블에서](../../synapse-analytics/sql/develop-tables-external-tables.md) Parquet 형식으로 결과를 구체화하여 쿼리 성능을 향상할 수도 있습니다.

```sql
-- Create External data source where the parquet file will be written 
CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH ( 
    LOCATION = 'https://{{youraccount}}.blob.core.windows.net/{{exttblcontainer}}' 
); 
GO 

-- Create External File Format 
CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH ( 
    FORMAT_TYPE = PARQUET, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec' 
); 
GO 

CREATE EXTERNAL TABLE [dbo].[Patient] WITH ( 
        LOCATION = 'PatientParquet/', 
        DATA_SOURCE = [MyDataSource], 
        FILE_FORMAT = [ParquetFF] 
) AS 
SELECT * FROM  
OPENROWSET(bulk 'https://{{youraccount}}.blob.core.windows.net/{{yourcontainer}}/Patient.ndjson' 
-- Use rest of the SQL statement from the previous example --
```

#### <a name="using-dedicated-sql-pool"></a>전용 SQL 풀 사용

전용 SQL 풀은 메모리 내 성능을 위해 관리되는 테이블 및 계층적 캐시를 지원합니다. 간단한 T SQL 쿼리를 사용하여 빅 데이터를 가져온 다음 분산 쿼리 엔진의 기능을 사용하여 고성능 분석을 실행할 수 있습니다.

스토리지에서 전용 SQL 풀로 데이터를 로드하는 가장 간단하고 빠른 방법은 **`COPY`** CSV, Parquet 및 ORC 파일을 읽을 수 있는 T-SQL 명령을 사용하는 것입니다. 아래 예제 쿼리와 같이 `COPY` 명령을 사용하여 `NDJSON` 행을 테이블 형식 구조로 로드합니다. 

```sql
-- Create table with HEAP, which is not indexed and does not have a column width limitation of NVARCHAR(4000) 
CREATE TABLE StagingPatient ( 
Resource NVARCHAR(MAX) 
) WITH (HEAP) 
COPY INTO StagingPatient 
FROM 'https://{{yourblobaccount}}.blob.core.windows.net/{{yourcontainer}}/Patient.ndjson' 
WITH ( 
FILE_TYPE = 'CSV', 
ROWTERMINATOR='0x0a', 
FIELDQUOTE = '', 
FIELDTERMINATOR = '0x00' 
) 
GO
```

위의 표에 JSON 행이 있으면 `StagingPatient` 함수를 사용하여 데이터의 다른 테이블 형식을 만들고 결과를 테이블에 저장할 수 `OPENJSON` 있습니다. 다음은 리소스에서 몇 가지 필드를 추출하여 테이블을 만드는 샘플 SQL `Patient` 쿼리입니다. `Patient`

```sql
SELECT RES.* 
INTO Patient 
FROM StagingPatient
CROSS APPLY OPENJSON(Resource)   
WITH (
    ResourceId VARCHAR(64) '$.id',
    FullName VARCHAR(100) '$.name[0].text',
    FamilyName VARCHAR(50) '$.name[0].family',
    GivenName VARCHAR(50) '$.name[0].given[0]',
    Gender VARCHAR(20) '$.gender',
    DOB DATETIME2 '$.birthDate',
    MaritalStatus VARCHAR(20) '$.maritalStatus.coding[0].display',
    LanguageOfCommunication VARCHAR(20) '$.communication[0].language.text'
) AS RES 
GO

```

## <a name="use-fhir-analytics-pipelines-oss-tools"></a>FHIR 분석 Pipelines OSS 도구 사용

:::image type="content" source="media/export-data/analytics-pipeline-option.png" alt-text="ADF 파이프라인을 사용하여 데이터를 CDM 폴더로 이동한 다음, Synapse로 이동":::

> [!Note]
> [FHIR 분석 파이프라인은](https://github.com/microsoft/FHIR-Analytics-Pipelines) MIT 라이선스에 따라 릴리스된 오픈 소스 도구이며 Azure 서비스에 대한 Microsoft SLA에서 다루지 않습니다.

### <a name="adf-pipeline-for-moving-fhir-data-into-cdm-folder"></a>FHIR 데이터를 CDM 폴더로 이동하기 위한 ADF 파이프라인

CDM(Common Data Model) 폴더는 잘 정의되고 표준화된 메타데이터 구조와 자체 설명 데이터를 준수하는 데이터 레이크의 폴더입니다. 이러한 폴더는 데이터 생산자와 데이터 소비자 간의 메타데이터 상호 운용성을 용이하게 합니다. FHIR 데이터를 CDM 폴더로 이동하기 전에 데이터를 테이블 구성으로 변환할 수 있습니다.

### <a name="generating-table-configuration"></a>테이블 구성 생성

복제 te repo 모든 스크립트 및 소스 코드를 얻을 수 있습니다. 를 사용하여 `npm install` dependencies를 설치합니다. `Configuration-Generator`YAML 형식 지침을 사용하여 테이블 구성 폴더를 생성하려면 폴더에서 다음 명령을 실행합니다.

```bash
Configuration-Generator> node .\generate_from_yaml.js -r {resource configuration file} -p {properties group file} -o {output folder}
```

`YAML` `resourcesConfig.yml` 리포지전에 제공된 샘플 파일을 사용할 수 `propertiesGroupConfig.yml` 있습니다.

### <a name="generating-adf-pipeline"></a>ADF 파이프라인 생성

이제 생성된 테이블 구성의 내용과 몇 가지 다른 구성을 사용하여 ADF 파이프라인을 생성할 수 있습니다. 이 ADF 파이프라인은 트리거될 때 API를 사용하여 FHIR 서버에서 데이터를 `$export` 내보내고 연결된 CDM 메타데이터와 함께 CDM 폴더에 씁니다.

1. AD(Azure Active Directory) 애플리케이션 및 서비스 주체를 만듭니다. ADF 파이프라인은 Azure 배치 서비스를 사용하여 변환을 수행하며 일괄 처리 서비스에 대한 Azure AD 애플리케이션이 필요합니다. [Azure AD 설명서](../../active-directory/develop/howto-create-service-principal-portal.md)를 따릅니다.
2. 서비스 주체에 스토리지 위치 내보내기 액세스 권한을 부여합니다. `Access Control`내보내기 스토리지의 에서 `Storage Blob Data Contributor` Azure AD 애플리케이션에 역할을 부여합니다.
3. 송신 파이프라인을 배포합니다. `fhirServiceToCdm.json`Azure에서 사용자 지정 배포에 템플릿을 사용합니다. 이 단계에서는 다음 Azure 리소스를 만듭니다.
    - 이름이 인 ADF `{pipelinename}-df` 파이프라인입니다.
    - 클라이언트 비밀을 저장할 이름의 키 자격 증명 `{pipelinename}-kv` 모음입니다.
    - 변환을 실행할 이름이 있는 일괄 처리 `{pipelinename}batch` 계정입니다.
    - 이름이 인 스토리지 `{pipelinename}storage` 계정입니다.
4. Azure Data Factory 대한 액세스 권한을 부여합니다. FHIR 서비스의 액세스 제어판에서 데이터 팩터리 에 `FHIR data exporter` 및 역할을 부여합니다. `FHIR data reader` `{pipelinename}-df`
5. 테이블 구성 폴더의 내용을 구성 컨테이너에 업로드.
6. 로 이동하여 `{pipelinename}-df` 파이프라인을 트리거합니다. 내보낸 데이터가 스토리지 계정 의 CDM 폴더에 `{pipelinename}storage` 표시됩니다. CSV 파일이 있는 각 테이블에 대해 하나의 폴더가 표시됩니다.

### <a name="from-cdm-folder-to-synapse"></a>CDM 폴더에서 Synapse로

데이터를 CDM 형식으로 내보내고 ADL Gen 2 스토리지에 저장한 후에는 CDM 폴더의 데이터를 Synapse로 이동할 수 있습니다.

다음과 같은 구성 파일을 사용하여 Synapse 파이프라인에 CDM을 만들 수 있습니다.

```json
{
  "ResourceGroup": "",
  "TemplateFilePath": "../Templates/cdmToSynapse.json",
  "TemplateParameters": {
    "DataFactoryName": "",
    "SynapseWorkspace": "",
    "DedicatedSqlPool": "",
    "AdlsAccountForCdm": "",
    "CdmRootLocation": "cdm",
    "StagingContainer": "adfstaging",
    "Entities": ["LocalPatient", "LocalPatientAddress"]
  }
}
```

위의 구성 파일을 사용하여 이 스크립트를 실행합니다.

```bash
.\DeployCdmToSynapsePipeline.ps1 -Config: config.json
```

ADF 관리 ID를 SQL 데이터베이스에 SQL 사용자로 추가합니다. 다음은 사용자 및 할당 역할을 만드는 샘플 SQL 스크립트입니다.

```sql
CREATE USER [datafactory-name] FROM EXTERNAL PROVIDER
GO
EXEC sp_addrolemember db_owner, [datafactory-name]
GO
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 데이터를 Synapse로 이동하는 두 가지 방법을 배웠습니다. (1) 를 사용하여 `$export` ADL Gen 2 Blob Storage로 데이터를 이동한 다음, Synapse SQL 풀에 데이터를 로드하고 , (2) FHIR 데이터를 CDM 폴더로 이동한 다음 Synapse로 이동하기 위해 ADF 파이프라인을 사용하는 방법을 알아보세요.

다음으로, 데이터를 Synapse로 이동하여 의료 정보가 보호되도록 하는 동안 FHIR 데이터의 익명화에 대해 알아볼 수 있습니다.
 
>[!div class="nextstepaction"]
>[식별되지 않은 데이터 내보내기](./de-identified-export.md)








