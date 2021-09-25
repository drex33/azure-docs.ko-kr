---
title: 복사 활동을 사용하여 데이터 이동
description: Data Factory 파이프라인에서 데이터를 이동하는 방법(클라우드 저장소 간/온-프레미스 저장소와 클라우드 저장소 간에 데이터 마이그레이션)에 대해 알아봅니다. 또한 복사 활동을 사용하는 방법을 살펴봅니다.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d138cab93d9cd9b1c4c44e92e6fcfb583103fb70
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629411"
---
# <a name="move-data-by-using-copy-activity"></a>복사 활동을 사용하여 데이터 이동
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](data-factory-data-movement-activities.md)
> * [버전 2(현재 버전)](../copy-activity-overview.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2에서 복사 작업](../copy-activity-overview.md)을 참조하세요.

## <a name="overview"></a>개요
Azure Data Factory에서는 복사 작업을 사용해 온-프레미스 및 클라우드 데이터 저장소 간에 데이터를 복사할 수 있습니다. 복사한 데이터는 추가로 변환하고 분석할 수 있습니다. 복사 활동을 통해 BI(비즈니스 인텔리전스) 및 애플리케이션에서 사용할 수 있도록 변환 및 분석 결과를 게시할 수도 있습니다.

:::image type="content" source="media/data-factory-data-movement-activities/copy-activity.png" alt-text="복사 활동의 역할":::

복사 활동은 안전성, 안전성, 확장성이 뛰어나며 [전 세계에서 사용 가능한 서비스](#global)를 통해 제공됩니다. 이 문서는 Data Factory 및 복사 작업에서 데이터 이동에 대한 세부 정보를 제공합니다.

먼저 두 개의 클라우드 데이터 저장소 간, 그리고 온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간에 이루어지는 데이터 마이그레이션 방식에 대해 살펴보겠습니다.

> [!NOTE]
> 활동과 관련된 전반적인 정보를 살펴보려면 [파이프라인 및 활동 이해](data-factory-create-pipelines.md)를 참조하세요.
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>두 클라우드 데이터 저장소 간의 데이터 복사
소스 및 싱크 데이터 저장소가 모두 클라우드에 있는 경우 복사 활동은 다음 단계를 통해 소스에서 싱크로 데이터를 복사합니다. 복사 활동을 제공하는 서비스가 다음 작업을 수행합니다.

1. 소스 데이터 저장소에서 데이터를 읽습니다.
2. 직렬화/역직렬화, 압축/압축 해제, 열 매핑 및 형식 변환을 수행합니다. 이러한 작업은 입력 데이터 세트, 출력 데이터 세트 및 복사 활동의 구성에 따라 수행됩니다.
3. 대상 데이터 저장소에 데이터를 씁니다.

서비스는 데이터 이동을 수행할 최적의 지역을 자동으로 선택합니다. 이 지역은 대개 싱크 데이터 저장소와 가장 가까운 지역입니다.

:::image type="content" source="./media/data-factory-data-movement-activities/cloud-to-cloud.png" alt-text="클라우드 간 복사":::

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간 데이터 복사
온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간에 데이터를 안전하게 이동하려면 온-프레미스 컴퓨터에 데이터 관리 게이트웨이를 설치합니다. 데이터 관리 게이트웨이는 하이브리드 데이터 이동 및 처리를 수행할 수 있도록 하는 에이전트입니다. 게이트웨이는 데이터 저장소 자체와 같은 컴퓨터에 설치할 수도 있고, 데이터 저장소에 액세스할 수 있는 별도의 컴퓨터에 설치할 수도 있습니다.

이 시나리오에서는 데이터 관리 게이트웨이가 직렬화/역직렬화, 압축/압축 해제, 열 매핑 및 형식 변환을 수행합니다. 데이터는 Azure Data Factory 서비스를 통과하지 않습니다. 대신 데이터 관리 게이트웨이가 데이터를 대상 저장소에 직접 씁니다.

:::image type="content" source="./media/data-factory-data-movement-activities/onprem-to-cloud.png" alt-text="온-프레미스 및 클라우드 간 복사":::

데이터 이동에 대한 소개와 연습은 [온-프레미스 및 클라우드 데이터 저장소 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 을 참조하세요. 이 에이전트에 대한 자세한 내용은 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) 를 참조하세요.

데이터 관리 게이트웨이를 사용하여 Azure IaaS VM(Virtual Machines)에서 호스트되는 지원되는 데이터 저장소 간에 데이터를 이동할 수도 있습니다. 이 경우 데이터 관리 게이트웨이는 데이터 저장소 자체와 같은 VM에 설치할 수도 있고, 데이터 저장소에 액세스할 수 있는 별도의 VM에 설치할 수도 있습니다.

## <a name="supported-data-stores-and-formats"></a>지원되는 데이터 저장소 및 형식
데이터 팩터리의 복사 활동은 원본 데이터 저장소의 데이터를 싱크 데이터 저장소로 복사합니다. Data Factory는 다음과 같은 데이터 저장소를 지원합니다. 모든 소스의 데이터를 모든 싱크에 쓸 수 있습니다. 데이터 저장소를 클릭하면 해당 저장소에서/저장소로 데이터를 복사하는 방법을 확인할 수 있습니다.

> [!NOTE] 
> 복사 활동에서 지원되지 않는 데이터 저장소에서/데이터 저장소로 데이터를 이동해야 하는 경우 데이터 복사/이동을 위한 자체 논리가 포함된 **사용자 지정 활동** 을 Data Factory에서 사용합니다. 사용자 지정 활동을 만들고 사용하는 방법에 대한 자세한 내용은 [Azure Data Factory 파이프라인에서 사용자 지정 활동 사용](data-factory-use-custom-activities.md)을 참조하세요.

[!INCLUDE [data-factory-supported-data-stores](includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> *가 있는 데이터 저장소는 온-프레미스 또는 Azure IaaS에 있을 수 있으며 온-프레미스/Azure IaaS 컴퓨터에 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) 를 설치해야 합니다.

### <a name="supported-file-formats"></a>지원되는 파일 형식
복사 활동을 사용해서 파일 기반 데이터 저장소 간에 **파일을 있는 그대로 복사** 하고, 입력 및 출력 데이터 세트 정의 둘 다에서 [형식 섹션](data-factory-create-datasets.md)을 건너뛸 수 있습니다. 그러면 데이터가 직렬화/역직렬화되지 않고 효율적으로 복사됩니다.

복사 작업은 지정된 형식의 파일에서 읽고 씁니다. **텍스트, JSON, Avro, ORC 및 Parquet**, 압축 코덱 **GZip, Deflate, BZip2 및 ZipDeflate** 가 지원됩니다. 자세한 내용은 [지원되는 파일 및 압축 형식](data-factory-supported-file-and-compression-formats.md)을 참조하세요.

예를 들어 다음 복사 작업을 수행할 수 있습니다.

* SQL Server 데이터베이스에서 데이터를 복사하고 ORC 형식으로 Azure Data Lake Store에 씁니다.
* 온-프레미스 파일 시스템에서 텍스트(CSV) 형식의 파일을 복사하여 Avro 형식으로 Azure Blob에 씁니다.
* 온-프레미스 파일 시스템에서 압축된 파일을 복사하고 압축을 푼 다음 Azure Data Lake Store에 씁니다.
* Azure Blob에서 GZip 압축 텍스트(CSV) 형식의 데이터를 복사하여 Azure SQL Database에 씁니다.

## <a name="globally-available-data-movement"></a><a name="global"></a>전역적으로 사용 가능한 데이터 이동
Azure Data Factory는 미국 서부, 미국 동부 및 북유럽 지역에서만 사용할 수 있습니다. 그러나 복사 작업을 지원하는 서비스는 다음과 같은 지역 및 지리에서 전역적으로 사용할 수 있습니다. 전역적으로 사용 가능한 토폴로지에서는 대개 지역 간 홉이 없는 효율적인 데이터 이동이 가능합니다. 지역별 Data Factory 및 데이터 이동 기능 사용 가능 여부는 [지역별 서비스](https://azure.microsoft.com/regions/#services) 를 참조하세요.

### <a name="copy-data-between-cloud-data-stores"></a>클라우드 데이터 저장소 간의 데이터 복사
원본 및 싱크 데이터 저장소가 모두 클라우드에 있는 경우, Data Factory는 데이터 이동을 수행하기 위해 동일한 지역의 싱크 위치에 가장 가까운 지역에서 서비스 배포를 사용합니다. 매핑은 다음 표를 참조하세요.

| 대상 데이터 저장소의 지리적 위치 | 대상 데이터 저장소의 지역 | 데이터 이동에 사용되는 지역 |
|:--- |:--- |:--- |
| 미국 | 미국 동부 | 미국 동부 |
| &nbsp; | 미국 동부 2 | 미국 동부 2 |
| &nbsp; | 미국 중부 | 미국 중부 |
| &nbsp; | 미국 중북부 | 미국 중북부 |
| &nbsp; | 미국 중남부 | 미국 중남부 |
| &nbsp; | 미국 중서부 | 미국 중서부 |
| &nbsp; | 미국 서부 | 미국 서부 |
| &nbsp; | 미국 서부 2 | 미국 서부 2 |
| Canada | 캐나다 동부 | 캐나다 중부 |
| &nbsp; | 캐나다 중부 | 캐나다 중부 |
| 브라질 | 브라질 남부 | 브라질 남부 |
| 유럽 | 북유럽 | 북유럽 |
| &nbsp; | 서유럽 | 서유럽 |
| 영국 | 영국 서부 | 영국 남부 |
| &nbsp; | 영국 남부 | 영국 남부 |
| 아시아 태평양 | 동남아시아 | 동남아시아 |
| &nbsp; | 동아시아 | 동남아시아 |
| 오스트레일리아 | 오스트레일리아 동부 | 오스트레일리아 동부 |
| &nbsp; | 오스트레일리아 남동부 | 오스트레일리아 남동부 |
| 인도 | 인도 중부 | 인도 중부 |
| &nbsp; | 인도 서부 | 인도 중부 |
| &nbsp; | 인도 남부 | 인도 중부 |
| 일본 | 일본 동부 | 일본 동부 |
| &nbsp; | 일본 서부 | 일본 동부 |
| 한국 | 한국 중부 | 한국 중부 |
| &nbsp; | 한국 남부 | 한국 중부 |

또는 복사 작업 `typeProperties`에서 `executionLocation` 속성을 지정하여 복사를 수행하는 데 사용할 Data Factory 서비스의 지역을 명시적으로 지정할 수 있습니다. 이 속성에 대한 지원되는 값은 위의 **데이터 이동에 사용되는 지역** 열에 나열됩니다. 데이터는 복사 동안 유선을 통해 해당 하위 지역으로 이동됩니다. 예를 들어 영국의 Azure 저장소 간을 복사하려면 `"executionLocation": "Japan East"`를 지정하여 일본을 통해 라우팅되도록 할 수 있습니다([샘플 JSON](#by-using-json-scripts) 참조).

> [!NOTE]
> 대상 데이터 저장소의 지역이 위의 목록에 없거나 검색 가능하지 않을 경우 `executionLocation`을 지정하지 않을 경우 기본적으로 복사 작업이 대체 지역을 거치지 않고 실패합니다. 지원되는 지역 목록은 시간이 지남에 따라 확장됩니다.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간 데이터 복사
온-프레미스 또는 Azure Virtual Machines/IaaS와 클라우드 저장소 간에 데이터를 복사할 때 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) 는 온-프레미스 컴퓨터 또는 Virtual Machines에서 데이터 이동을 수행합니다. [준비된 복사](data-factory-copy-activity-performance.md#staged-copy) 기능을 사용하는 경우가 아니면 이 데이터는 클라우드의 서비스를 통과하지 않습니다. 암호화를 사용하는 동안 Blob 또는 File Storage에 기록된 내용이 암호화됩니다.

## <a name="create-a-pipeline-with-copy-activity"></a>복사 활동을 포함하는 파이프라인 만들기
두 가지 방법을 통해 복사 활동을 포함하는 파이프라인을 만들 수 있습니다.

### <a name="by-using-the-copy-wizard"></a>복사 마법사 사용
Data Factory 복사 마법사를 사용하면 복사 활동을 포함하는 파이프라인을 만들 수 있습니다. 이 파이프라인에서는 연결된 서비스, 데이터 세트 및 파이프라인에 대한 *JSON 정의를 작성하지 않고도* 지원되는 소스에서 대상으로 데이터를 복사할 수 있습니다. 마법사에 대한 자세한 내용은 [Data Factory 복사 마법사](data-factory-copy-wizard.md) 를 참조하세요.  

### <a name="by-using-json-scripts"></a>JSON 스크립트 사용
Visual Studio 또는 Azure PowerShell에서 Data Factory Editor를 사용하여 복사 작업을 통해 파이프라인에 대한 JSON 정의를 만들 수 있습니다. 그런 다음 해당 정의를 배포하여 Data Factory에서 파이프라인을 만들 수 있습니다. 단계별 지침이 포함된 자습서는 [자습서: Azure Data Factory 파이프라인에서 복사 활동 사용](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 을 참조하세요.    

이름, 설명, 입력/출력 테이블, 정책 등의 JSON 속성은 모든 형식의 활동에 사용할 수 있습니다. 활동의 `typeProperties` 섹션에서 사용 가능한 속성은 각 활동 형식에 따라 다릅니다.

복사 활동의 경우 `typeProperties` 섹션은 원본 및 싱크의 형식에 따라 달라집니다. 해당 데이터 저장소에 대한 복사 활동에서 지원하는 형식 속성에 대해 알아보려면 [지원되는 소스 및 싱크](#supported-data-stores-and-formats) 섹션에서 소스/싱크를 클릭하세요.

샘플 JSON 정의는 다음과 같습니다.

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "Japan East"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
출력 데이터 세트에 정의된 일정은 작업 실행 시기를 결정합니다(예: **매일**, 빈도는 **요일** 및 간격은 **1**). 복사 작업에서는 입력 데이터 세트(**source**)에서 출력 데이터 세트(**sink**)로 데이터를 복사합니다.

복사 활동에 대해 입력 데이터 세트를 둘 이상 지정할 수 있습니다. 이러한 데이터 집합은 활동 실행 전에 종속성을 확인하는 데 사용됩니다. 그러나 첫 번째 데이터 세트의 데이터만 대상 데이터 세트로 복사됩니다. 자세한 내용은 [예약 및 실행](data-factory-scheduling-and-execution.md)을 참조하세요.  

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 활동) 성능에 영향을 주는 주요 요인에 대해 설명하는 [복사 작업 성능 및 튜닝 가이드](data-factory-copy-activity-performance.md)를 참조하세요. 이 문서에서는 내부 테스트 중에 관찰되는 성능 관련 정보도 제공하며, 복사 활동의 성능을 최적화하는 여러 가지 방법에 대해서도 설명합니다.

## <a name="fault-tolerance"></a>내결함성
기본적으로 복사 작업은 원본과 싱크 간에 호환되지 않는 데이터가 발견되면 데이터 복사를 중지하고 오류를 반환합니다. 물론 호환되지 않는 행을 건너뛰고 로깅한 후 호환되는 데이터만 복사하여 복사가 성공적으로 수행되도록 명시적으로 구성할 수도 있습니다. 자세한 내용은 [복사 활동 내결함성](data-factory-copy-activity-fault-tolerance.md)을 참조하세요.

## <a name="security-considerations"></a>보안 고려 사항
Azure Data Factory의 데이터 이동 서비스가 데이터를 보호하는 데 사용하는 기본 보안 인프라에 대해 설명하는 [보안 고려 사항](data-factory-data-movement-security-considerations.md)을 참조하세요.

## <a name="scheduling-and-sequential-copy"></a>예약 및 순차 복사
Data Factory에서 예약 및 실행이 작동하는 방식에 대한 자세한 내용은 [예약 및 실행](data-factory-scheduling-and-execution.md) 을 참조하세요. 순차/순서가 지정된 방식으로 하나씩 여러 복사 작업을 실행하는 것이 가능합니다. [순차적으로 복사](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) 섹션을 참조하세요.

## <a name="type-conversions"></a>형식 변환
다른 데이터 저장소에는 서로 다른 네이티브 형식 시스템이 있습니다. 복사 활동에서는 다음과 같은 2단계 방식을 통해 소스 형식에서 싱크 형식으로의 자동 형식 변환을 수행합니다.

1. 네이티브 소스 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

네이티브 형식 시스템에서 데이터 저장소용 .NET 형식으로의 매핑은 각 데이터 저장소 문서에 있습니다. (지원되는 데이터 저장소 테이블에서 해당 링크를 클릭하세요.) 복사 활동에서 적절한 변환을 수행하도록 이러한 매핑을 사용하여 테이블을 만드는 동안 적절한 형식을 결정할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* 복사 작업에 대해 자세히 알아보려면 [Azure Blob 스토리지에서 Azure SQL Database로 데이터를 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
* 온-프레미스 데이터 저장소에서 클라우드 데이터 저장소로 데이터를 이동하는 방법에 대해 알아보려면 [온-프레미스에서 클라우드 데이터 저장소로 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md)을 참조하세요.
