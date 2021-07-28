---
title: 리소스 집합 이해
description: 이 문서에서는 리소스 집합이란 무엇이며 Azure Purview에서 해당 집합을 만드는 방법에 대해 설명합니다.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 330a6e54ee88781f71c4a861051aab94f8eef81f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587904"
---
# <a name="understanding-resource-sets"></a>리소스 집합 이해

이 문서는 Azure Purview에서 리소스 집합을 사용하여 데이터 자산을 논리적 리소스에 매핑하는 방법을 이해하는 데 도움이 됩니다.
## <a name="background-info"></a>배경 정보

대규모 데이터 처리 시스템은 일반적으로 디스크에 단일 테이블을 여러 파일로 저장합니다. 이 개념은 리소스 집합을 사용하는 Azure Purview에서 등장합니다. 리소스 집합은 스토리지의 많은 자산을 나타내는 카탈로그의 단일 개체입니다.

예를 들어 Spark 클러스터가 DataFrame을 ADLS(Azure Data Lake Storage) Gen2 데이터 원본에 유지했다고 가정해 보겠습니다. Spark에서 테이블은 단일 논리 리소스처럼 보이지만, 디스크에는 각기 전체 DataFrame 내용에 대한 파티션을 나타내는 수천 개의 Parquet 파일이 있습니다. IoT 데이터와 웹 로그 데이터의 과제는 동일합니다. 로그 파일을 초당 여러 번 출력하는 센서가 있다고 가정해 보겠습니다. 해당 단일 센서에서 수백 개의 로그 파일을 포함할 때까지 시간은 그다지 오래 걸리지 않습니다.

다수의 데이터 자산을 단일 논리적 리소스로 매핑하는 과제를 해결하기 위해 Azure Purview는 리소스 집합을 사용합니다.

## <a name="how-azure-purview-detects-resource-sets"></a>Azure Purview에서 리소스 집합을 검색하는 방법

Azure Purview는 Azure Blob Storage, ADLS Gen1, ADLS Gen2에서의 리소스 집합 검색을 지원합니다.

Azure Purview는 검사할 때 리소스 집합을 자동으로 검색합니다. 이 기능은 검사를 통해 수집하는 모든 데이터를 확인하고 정의된 패턴 집합과 비교합니다.

예를 들어 URL이 `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet`인 데이터 원본을 검사한다고 가정해 보겠습니다. Azure Purview는 경로 세그먼트를 확인하고 임의의 기본 제공 패턴과 일치하는지 확인합니다. 경로 세그먼트는 GUID, 숫자, 날짜 형식, 지역화 코드(예: en-us) 등의 기본 제공 패턴을 포함합니다. 이 경우 숫자 패턴은 *23* 과 일치합니다. Azure Purview는 이 파일이 `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet`이라는 리소스 집합에 포함되어 있다고 가정합니다.

또는 `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json`와 같은 URL의 경우, Azure Purview는 지역화 패턴과 숫자 패턴을 모두 일치시켜 `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json`이라는 리소스 집합을 생성합니다.

Azure Purview는 이 전략을 사용하여 다음 리소스를 동일한 리소스 집합인 `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json`에 매핑합니다.

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Azure Purview가 리소스 집합으로 검색하지 않는 파일 형식

Purview는 Word, Excel 또는 PDF와 같은 대부분의 문서 파일 형식을 리소스 집합으로 분류하려고 시도하지 않습니다. 예외는 일반적인 분할된 파일 형식인 CSV 형식입니다.

## <a name="how-azure-purview-scans-resource-sets"></a>Azure Purview에서 리소스 집합을 검사하는 방법

Azure Purview가 리소스 집합의 일부로 간주되는 리소스를 검색하면 전체 검사에서 샘플 검사로 전환됩니다. 샘플 검사에서는 리소스 집합에 있는 것으로 간주되는 파일의 하위 집합만 엽니다. 열리는 각 파일에 대해 해당 스키마를 사용하고 해당 분류자를 실행합니다. 그런 다음, Azure Purview는 열려 있는 리소스에서 최신 리소스를 찾고 카탈로그에 있는 전체 리소스 집합에 대한 항목에서 해당 리소스의 스키마와 분류를 사용합니다.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Azure Purview가 리소스 집합에 대해 저장하는 항목

Azure Purview는 단일 스키마 및 분류 외에도 리소스 집합에 대해 다음과 같은 정보를 저장합니다.

- 심층적으로 검사한 최신 파티션 리소스의 데이터
- 리소스 집합을 구성하는 파티션 리소스에 대한 정보 집계
- 검색된 파티션 리소스의 수를 표시하는 파티션 수
- 심층 검사를 수행한 샘플 세트에 있는 고유한 스키마 수를 표시하는 스키마 개수. 이 값은 1~5 사이의 숫자이거나 5보다 큰 값의 경우 5를 넘는 값입니다.
- 둘 이상의 파티션 유형이 리소스 집합에 포함된 경우 파티션 유형 목록. 예를 들어 IoT 센서는 XML 및 JSON 파일을 모두 출력할 수 있습니다. 물론 두 파일은 모두 논리적으로 동일한 리소스 집합에 포함됩니다.

## <a name="built-in-resource-set-patterns"></a>기본 제공 리소스 집합 패턴

Azure Purview는 다음과 같은 리소스 집합 패턴을 지원합니다. 이러한 패턴은 디렉터리의 이름 또는 파일 이름의 일부로 표시될 수 있습니다.
### <a name="regex-based-patterns"></a>Regex 기반 패턴

| 패턴 이름 | 표시 이름 | Description |
|--------------|--------------|-------------|
| GUID         | {GUID}       | [RFC 4122](https://tools.ietf.org/html/rfc4122)에 정의된 GUID(Globally Unique Identifier) |
| 숫자       | {N}          | 하나 이상의 숫자 |
| 날짜/시간 형식 | {Year}{Month}{Day}{N}     | 다양한 날짜/시간 형식을 지원하지만 모두 {Year}[delimiter]{Month}[delimiter]{Day} 또는 일련의 {N}으로 표시됩니다. |
| 4ByteHex     | {HEX}        | 4자리 16진수 숫자입니다. |
| 지역화 | {LOC}        | [BCP 47](https://tools.ietf.org/html/bcp47)에 정의된 언어 태그, - 및 _ 이름이 모두 지원됩니다(예: en_ca 및 en-ca). |

### <a name="complex-patterns"></a>복합 패턴

| 패턴 이름 | 표시 이름 | Description |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Spark 파티션 파일 식별자 |
| Date(yyyy/mm/dd)InPath  | {Year}/{Month}/{Day} | 여러 폴더에 걸쳐 있는 연도/월/일 패턴 |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Azure Purview 카탈로그에서 리소스 집합을 표시하는 방법

Azure Purview는 자산 그룹을 리소스 집합으로 일치시킬 때 카탈로그에서 표시 이름으로 사용하기에 가장 유용한 정보를 추출하려고 시도합니다. 적용되는 기본 명명 규칙의 몇 가지 예는 다음과 같습니다. 

### <a name="example-1"></a>예 1

정규화된 이름: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

표시 이름: "name of spark output"

### <a name="example-2"></a>예제 2

정규화된 이름: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

표시 이름: "my partitioned data"

### <a name="example-3"></a>예제 3

정규화된 이름: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

표시 이름: "data"

## <a name="customizing-resource-set-grouping-using-pattern-rules"></a>패턴 규칙을 사용하여 리소스 집합 그룹화 사용자 지정

스토리지 계정을 스캔하는 경우 Azure Purview는 정의된 패턴 세트를 사용하여 자산 그룹이 리소스 집합인지 여부를 확인합니다. 경우에 따라 Azure Purview의 리소스 집합 그룹화에서 데이터 자산을 정확하게 반영하지 못할 수도 있습니다. 이러한 문제에는 다음이 포함될 수 있습니다.

- 자산을 리소스 집합으로 잘못 표시
- 잘못된 리소스 집합에 자산 배치
- 자산을 리소스 집합이 아닌 것으로 잘못 표시

Azure Purview에서 리소스 집합으로 그룹화된 자산을 검색하는 방법과 카탈로그 내에 표시되는 방법을 사용자 지정하거나 재정의하려면 관리 센터에서 패턴 규칙을 정의할 수 있습니다. 단계별 지침 및 구문은 [리소스 집합 패턴 규칙](how-to-resource-set-pattern-rules.md)을 참조하세요.
## <a name="next-steps"></a>다음 단계

Azure Purview를 시작하려면 [빠른 시작: Azure Purview 계정 만들기](create-catalog-portal.md)를 참조하세요.
