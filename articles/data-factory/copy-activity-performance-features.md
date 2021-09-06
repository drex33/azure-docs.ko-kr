---
title: 복사 작업 성능 최적화 기능
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics 파이프라인에서 복사 작업 성능을 최적화하는 데 도움이 되는 주요 기능에 대해 알아봅니다.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 08/24/2021
ms.openlocfilehash: 9be8ef1772da6259441a8de4c85fa44d54945c7d
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122821818"
---
# <a name="copy-activity-performance-optimization-features"></a>복사 작업 성능 최적화 기능

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Synapse 파이프라인에서 활용할 수 있는 복사 작업 성능 최적화 기능을 간략하게 설명합니다.

## <a name="data-integration-units"></a>데이터 통합 단위

데이터 통합 단위는 해당 서비스 내 단일 단위의 힘(CPU, 메모리 및 네트워크 리소스 할당의 조합)을 나타내는 측정값입니다. 데이터 통합 단위는 [Azure 통합 런타임](concepts-integration-runtime.md#azure-integration-runtime)에만 적용되고 [자체 호스팅 통합 런타임](concepts-integration-runtime.md#self-hosted-integration-runtime)에는 적용되지 않습니다.

복사 작업 실행을 강화하는 데 허용되는 DIUs는 **2~256 사이** 입니다. 지정하지 않거나 UI에서 ‘Auto’를 선택하면 서비스에서 소스 싱크 쌍 및 데이터 패턴에 따라 최적의 DIU 설정을 동적으로 적용합니다. 다음 표에서는 다양한 복사 시나리오의 지원되는 DIU 범위 및 기본 동작을 보여 줍니다.

| 복사 시나리오 | 지원되는 DIU 범위 | 서비스에 따라 결정되는 기본 DIU |
|:--- |:--- |---- |
| 파일 저장소 간 |- **단일 파일 간 복사**: 2~4 <br>- **여러 파일 간 복사**: 파일의 수와 크기에 따라 2~256 사이에서 변동 <br><br>예를 들어, 크기가 큰 파일 4개가 있는 폴더에서 데이터를 복사하고 계층을 보존하도록 선택하는 경우 최대 유효 DIU는 16입니다. 파일 병합을 선택할 경우에는 최대 유효 DIU는 4입니다. |파일 수와 크기에 따라 4~32 |
| 파일 저장소에서 파일이 아닌 저장소로 |- **단일 파일에서 복사**: 2~4 <br/>- **여러 파일에서 복사**: 파일의 수와 크기에 따라 2~256 사이에서 변동 <br/><br/>예를 들어, 크기가 큰 파일 4개가 있는 폴더에서 데이터를 복사하는 경우 최대 유효 DIU는 16입니다. |- **Azure SQL Database 또는 Azure Cosmos DB에 복사**: 싱크 계층(DTU/RU) 및 소스 파일 패턴에 따라 4~16 사이입니다.<br>- **PolyBase 또는 COPY 문을 사용하여 Azure Synapse Analytics에 복사**: 2<br>- 기타 시나리오: 4 |
| 파일이 아닌 저장소에서 파일 저장소로 |- **파티션 옵션 사용 데이터 저장소**([Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source), [Teradata](connector-teradata.md#teradata-as-source) 포함)에서 복사: 폴더에 쓸 때 2~256, 단일 파일에 쓸 때 2~4 참고로 원본 데이터 파티션 당 최대 4개의 DIUs를 사용할 수 있습니다.<br>- **기타 시나리오**: 2~4 |- **REST 또는 HTTP에서 복사**: 1<br/>- UNLOAD를 사용하여 **Amazon Redshift에서 복사** : 2<br>- **기타 시나리오**: 4 |
| 파일이 아닌 저장소 간 |- **파티션 옵션 사용 데이터 저장소**([Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source), [Teradata](connector-teradata.md#teradata-as-source) 포함)에서 복사: 폴더에 쓸 때 2~256, 단일 파일에 쓸 때 2~4 참고로 원본 데이터 파티션 당 최대 4개의 DIUs를 사용할 수 있습니다.<br/>- **기타 시나리오**: 2~4 |- **REST 또는 HTTP에서 복사**: 1<br>- **기타 시나리오**: 4 |

복사 작업 모니터링 보기 또는 작업 출력에서 각 복사 실행에 사용된 DIUs를 볼 수 있습니다. 자세한 내용은 [복사 작업 모니터링](copy-activity-monitoring.md)을 참조하세요. 이 기본값을 재정의하려면 다음과 같이 `dataIntegrationUnits` 속성값을 지정합니다. 런타임 시 복사 작업에서 사용하는 *실제 DIU 수* 는 데이터 패턴에 따라 구성된 값보다 작거나 같습니다.

**사용된 DIUs의 # \* 복사 기간 \* 단가/DIU 시간** 로 요금이 청구됩니다. [여기](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)에서 현재 가격을 참조하세요. 현지 통화 및 개별 할인은 구독 형식별로 적용될 수 있습니다.

**예:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>자체 호스팅 통합 런타임 확장성

처리량을 높이기 위해 자체 호스팅 IR을 스케일 업하거나 스케일 아웃할 수 있습니다.

- 자체 호스팅 IR 노드의 CPU와 사용 가능한 메모리가 완전히 활용되지 않는데 동시 작업 실행이 한도에 도달하는 경우에는 노드에서 실행할 수 있는 동시 작업 수를 늘려 스케일 업해야 합니다.  지침은 [여기](create-self-hosted-integration-runtime.md#scale-up)를 참조하세요.
- 반면, 자체 호스팅 IR 노드에서 CPU 사용률이 높거나 사용 가능한 메모리가 낮은 경우에는 새 노드를 추가하여 여러 노드에서 로드를 스케일 아웃할 수 있습니다.  지침은 [여기](create-self-hosted-integration-runtime.md#high-availability-and-scalability)를 참조하세요.

참고로 다음 시나리오에서 단일 복사 작업 실행은 자체 호스팅 IR 노드를 여러 개 활용할 수 있습니다.

- 파일 수와 크기에 따라 파일 기반 저장소에서 데이터를 복사합니다.
- 데이터 파티션 수에 따라 파티션 옵션 사용 데이터 저장소([Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source), [Teradata](connector-teradata.md#teradata-as-source) 포함)에서 데이터를 복사합니다.

## <a name="parallel-copy"></a>병렬 복사

복사 작업에서 병렬 복사(`parallelCopies` 속성)를 설정하여 복사 작업에서 사용할 병렬 처리를 나타낼 수 있습니다. 이 속성은 원본에서 읽거나 싱크 데이터 저장소에 병렬로 쓰는 복사 작업 내의 최대 스레드 수로 간주합니다.

병렬 복사는 [데이터 통합 단위](#data-integration-units)나 [자체 호스팅 IR 노드](#self-hosted-integration-runtime-scalability)와 직교합니다. 모든 DIUs 또는 자체 호스팅 IR 노드에 걸쳐 계산됩니다.

각 복사 작업 실행 시 서비스는 기본적으로 원본 싱크 쌍 및 데이터 패턴에 따라 최적의 병렬 복사 설정을 동적으로 적용합니다. 

> [!TIP]
> 병렬 복사의 기본 동작은 일반적으로 원본 싱크 쌍, 데이터 패턴, DIUs 수 또는 자체 호스팅 IR의 CPU/메모리/노드 수를 기반으로 서비스에서 자동으로 결정되는 최상의 처리량을 제공합니다. 병렬 복사를 튜닝할 때 [복사 작업 성능 문제 해결](copy-activity-performance-troubleshooting.md)을 참조하세요.

다음 표에서는 병렬 복사 동작을 나열합니다.

| 복사 시나리오 | 병렬 복사 동작 |
| --- | --- |
| 파일 저장소 간 | `parallelCopies`은 **파일 수준에서** 병렬 처리를 결정합니다. 각 파일 내 청크는 자동으로 투명하게 발생합니다. 지정된 데이터 저장소 형식에 가장 적합한 청크 크기를 사용하여 데이터를 병렬로 로드하도록 설계되었습니다. <br/><br/>런타임 시 복사 작업에 사용하는 병렬 복사의 실제 수는 사용자가 보유한 파일의 수를 넘지 않습니다. 복사 동작이 파일 싱크에 대한 **mergeFile** 인 경우 복사 작업은 파일 수준 병렬 처리를 사용할 수 없습니다. |
| 파일 저장소에서 파일이 아닌 저장소로 | - Azure SQL Database 또는 Azure Cosmos DB에 데이터를 복사하는 경우 기본 병렬 복사도 싱크 계층(DTU/RU 수)에 따라 달라집니다.<br>- Azure Table에 데이터를 복사하는 경우 기본 병렬 복사본은 4입니다. |
| 파일이 아닌 저장소에서 파일 저장소로 | - 파티션 옵션 사용 데이터 저장소([Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source), [Teradata](connector-teradata.md#teradata-as-source) 포함)에서 데이터를 복사하는 경우, 기본 병렬 복사본은 4입니다. 런타임 시 복사 작업에 사용하는 병렬 복사의 실제 수는 사용자가 보유한 데이터 파티션의 수를 넘지 않습니다. 자체 호스팅 통합 런타임 사용하여 Azure Blob/ADLS Gen2에 복사하는 경우, 최대 유효 병렬 복사는 IR 노드당 4 또는 5입니다.<br>- 다른 시나리오의 경우 병렬 복사는 적용되지 않습니다. 병렬 처리를 지정하더라도 이 경우에는 적용되지 않습니다. |
| 파일이 아닌 저장소 간 | - Azure SQL Database 또는 Azure Cosmos DB에 데이터를 복사하는 경우 기본 병렬 복사도 싱크 계층(DTU/RU 수)에 따라 달라집니다.<br/>- 파티션 옵션 사용 데이터 저장소([Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source), [Teradata](connector-teradata.md#teradata-as-source) 포함)에서 데이터를 복사하는 경우, 기본 병렬 복사본은 4입니다.<br>- Azure Table에 데이터를 복사하는 경우 기본 병렬 복사본은 4입니다. |

데이터 저장소를 호스트하는 머신에서 로드를 제어하거나 복사 성능을 조정하기 위해 기본값을 재정의하고 `parallelCopies` 속성값을 지정할 수 있습니다. 값은 1 이상의 정수여야 합니다. 런타임 시, 최고의 성능을 위해 복사 작업은 설정된 값 이하의 값을 사용합니다.

`parallelCopies` 속성값을 지정하는 경우 계정으로의 원본 및 싱크 데이터 저장소 로드 증가를 고려해야 합니다. 또한 자체 호스팅 통합 런타임에 의해 복사 작업에 권한이 부여되는 경우, 로드가 증가할 수 있음을 고려해야 합니다. 동일한 데이터 저장소에 대해 실행되는 활동이 여러 개거나 동일한 활동을 동시에 실행하는 경우 특히 로드 증가가 발생합니다. 데이터 저장소나 자체 호스팅 통합 런타임이 로드로 인해 과부하가 걸리면 로드 감소를 위해 `parallelCopies` 값을 낮춥니다.

**예:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>준비된 복사

원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 복사하는 경우 Azure Blob Storage 또는 Azure Data Lake Storage Gen2를 임시 준비 저장소로 사용할 수도 있습니다. 준비는 다음과 같은 경우에 특히 유용합니다.

- **PolyBase를 통해 다양한 데이터 저장소의 데이터를 Azure Synapse Analytics로 수집하거나, 데이터를 Snowflake 간 복사하거나, Amazon Redshift/HDFS에서 성능 기준에 맞게 데이터를 수집하려고 합니다.** 세부 정보:
  - [PolyBase를 사용하여 Azure Synapse Analytics로 데이터 로드](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics)
  - [Snowflake 커넥터](connector-snowflake.md)
  - [Amazon Redshift 커넥터](connector-amazon-redshift.md)
  - [HDFS 커넥터](connector-hdfs.md)
- **기업 IT 정책 때문에 방화벽에서 80과 443 포트 이외의 포트는 열지 않으려 합니다.** 예를 들어, 온-프레미스 데이터 저장소에서 Azure SQL Database 또는 Azure Synapse Analytics에 데이터를 복사할 경우, Windows 방화벽 및 회사 방화벽 모두에 대해 1433 포트에서 아웃바운드 TCP 통신을 활성화해야 합니다. 이 시나리오에서는 준비된 복사가 자체 호스팅 통합 런타임을 이용하여 먼저 443 포트에서 HTTP나 HTTPS를 통해 준비 스토리지에 데이터를 복사한 다음, 준비 프로세스에서 데이터를 SQL Database나 Azure Synapse Analytics로 로드할 수 있습니다. 이 흐름에서는 포트 1433을 사용하도록 설정하지 않아도 됩니다.
- **때로는 느린 네트워크 연결을 통해 하이브리드 데이터 이동(즉, 온-프레미스 데이터 저장소에서 클라우드 데이터 저장소로 복사하는 것)을 수행하는 데 오랜 시간이 걸립니다.** 성능을 개선하기 위해 준비 복사를 사용해서 온-프레미스 데이터를 압축하면 데이터를 클라우드의 준비 데이터 저장소로 이동하는 데 소요되는 시간이 단축됩니다. 그런 다음 준비 저장소에서 데이터의 압축을 해제한 후 대상 데이터 저장소에 로드할 수 있습니다.

### <a name="how-staged-copy-works"></a>준비 복사의 작동 방법

준비 기능을 활성화하면 먼저 데이터가 원본 데이터 저장소에서 준비 스토리지(자체 환경 사용 Azure Blob 또는 Azure Data Lake Storage Gen2)로 복사됩니다. 그런 다음 데이터가 준비 프로세스에서 싱크 데이터 저장소로 복사됩니다. 복사 작업은 2단계 흐름을 자동으로 관리하여, 데이터 이동이 완료되면 준비 스토리지에서 임시 데이터도 정리합니다.

![준비된 복사](media/copy-activity-performance/staged-copy.png)

준비 저장소를 사용한 데이터 이동을 활성화하면 원본 데이터 저장소에서 준비 데이터 저장소로 데이터를 이동하기 전에 데이터를 압축할지와 중간 또는 준비 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하기 전에 압축할지 여부를 지정할 수 있습니다.

현재 준비된 복사가 있든 없든, 다른 자체 호스팅 IRs를 통해 연결된 두 데이터 저장소 간에 데이터를 복사할 수 없습니다. 그런 시나리오에서는 원본으로부터 준비 프로세스로 복사하고, 준비 프로세스에서 다시 싱크로 복사하기 위해 명시적으로 연결된 두 개의 복사 활동을 구성할 수 있습니다.

### <a name="configuration"></a>구성

복사 작업에서 **enableStaging** 설정을 구성하여 데이터를 대상 데이터 저장소에 로드하기 전에 스토리지에 데이터를 스테이징할지 여부를 지정합니다. **enableStaging** 을 `TRUE`으로 설정한 경우, 다음 표에 나열된 추가 속성을 지정해야 합니다. 

| 속성 | Description | 기본값 | 필수 |
| --- | --- | --- | --- |
| enableStaging |중간 준비 저장소를 통해 데이터를 복사할지 여부를 지정합니다. |False |예 |
| linkedServiceName |중간 준비 저장소로 사용할 스토리지 인스턴스를 참조하여 이름을 [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) 또는 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties)에 연결된 서비스로 지정합니다. |해당 없음 |예, **enableStaging** 이 TRUE로 설정된 경우입니다. |
| path |준비 데이터를 포함할 경로를 지정합니다. 경로를 제공하지 않으면, 서비스는 임시 데이터를 저장하는 컨테이너를 만듭니다. |해당 없음 |예 |
| enableCompression |대상을 복사하기 전에 데이터 압축 여부를 지정합니다. 이 설정은 전송되는 데이터 양을 줄입니다. |False |예 |

>[!NOTE]
> 압축이 활성화된 준비된 복사를 사용하는 경우, Blob 연결된 준비 서비스에 대한 서비스 주체 또는 MSI 인증은 지원되지 않습니다.

위의 표에서 설명하는 속성을 사용하는 복사 작업의 샘플 정의는 다음과 같습니다.

```json
"activities":[
    {
        "name": "CopyActivityWithStaging",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
            },
            "sink": {
                "type": "SqlDWSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path"
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>준비된 복사 청구 요소

복사 기간 및 복사 형식의 두 단계에 따라 요금이 청구됩니다.

* 클라우드 복사 중에 준비 프로세스를 사용할 경우(클라우드 데이터 저장소에서 다른 클라우드 데이터 저장소에 데이터를 복사, 둘 다 Azure Integration Runtime 사용), [1단계 및 2단계 복사 기간의 합]x[클라우드 복사 단가]로 요금이 청구됩니다.
* 하이브리드 복사 중에 준비 프로세스를 사용하는 경우(온-프레미스 데이터 저장소에서 클라우드 데이터 저장소로 데이터를 복사, 한 준비 저장소가 자체 호스팅 통합 런타임 사용) [하이브리드 복사 기간]x[하이브리드 복사 단가]+[클라우드 복사 기간]x[클라우드 복사 단가]로 요금이 청구됩니다.

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 성능 및 확장성 가이드](copy-activity-performance.md)
- [복사 작업 성능 문제 해결](copy-activity-performance-troubleshooting.md)
- [Azure Data Factory를 사용하여 데이터 레이크에서 데이터 마이그레이션 또는 데이터 웨어하우스에서 Azure로 데이터 마이그레이션](data-migration-guidance-overview.md)
- [Amazon S3에서 Azure Storage로 데이터 마이그레이션](data-migration-guidance-s3-azure-storage.md)