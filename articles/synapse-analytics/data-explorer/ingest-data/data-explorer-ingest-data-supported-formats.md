---
title: 수집을 위해 Azure Synapse 데이터 탐색기에서 지 원하는 데이터 형식입니다.
description: 수집을 위해 Azure Synapse 데이터 탐색기에서 지 원하는 다양 한 데이터 및 압축 형식에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: f38325e37b548c7f8a99d38eacae26aa24756250
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482884"
---
# <a name="data-formats-supported-by-azure-synapse-data-explorer-for-ingestion-preview"></a>수집을 위해 Azure Synapse 데이터 탐색기에서 지 원하는 데이터 형식 (미리 보기)

데이터 수집은 테이블에 데이터를 추가 하 고 데이터 탐색기에서 쿼리에 사용할 수 있도록 하는 프로세스입니다. 쿼리에서 수집 이외의 모든 수집 메서드의 경우 데이터는 지원되는 형식 중 하나로 지정해야 합니다. 다음 표에서는 데이터 탐색기 데이터 수집에 대해 지원 되는 형식을 나열 하 고 설명 합니다.

> [!NOTE]
> 데이터를 수집하기 전에 데이터의 형식이 올바르게 지정되었는지 확인하고 필요한 필드를 정의합니다. 기본 유효성 검사기를 사용하여 형식이 유효한지 확인하는 것이 좋습니다. 예를 들어 CSV 또는 JSON 파일을 확인하는 데 유용한 다음과 같은 유효성 검사기를 찾을 수 있습니다.
>
> * CSV: http://csvlint.io/
> * JSON: https://jsonlint.com/
>
> 수집이 실패할 수 있는 이유에 대 한 자세한 내용은 데이터 탐색기 수집 오류 및 수집 [오류 코드](/azure/data-explorer/error-codes?context=/azure/synapse-analytics/context/context) [를 참조 하세요](/azure/data-explorer/kusto/management/ingestionfailures?context=/azure/synapse-analytics/context/context) .

|서식   |내선 번호   |설명|
|---------|------------|-----------|
|ApacheAvro|`.avro`    |[논리적 형식](https://avro.apache.org/docs/current/spec.html#Logical+Types)에 대한 지원을 포함하는 [AVRO](https://avro.apache.org/docs/current/) 형식 다음 압축 코덱이 지원됩니다. `null`, `deflate` 및 `snappy` `apacheavro` 형식의 판독기 구현은 공식 [Apache Avro 라이브러리](https://github.com/apache/avro)를 기반으로 합니다.|
|Avro     |`.avro`     |[.NET 라이브러리](https://www.nuget.org/packages/Microsoft.Hadoop.Avro)를 기반으로 하는 [AVRO](https://avro.apache.org/docs/current/) 형식에 대한 레거시 구현입니다. 다음 압축 코덱이 지원됩니다. `null`, `deflate`(`snappy`의 경우 - `ApacheAvro` 데이터 형식 사용).|
|CSV      |`.csv`      |쉼표(`,`)로 구분된 값을 사용하는 텍스트 파일입니다. [RFC 4180: _CSV(쉼표로 구분된 값) 파일의 일반 형식 및 MIME 형식_](https://www.ietf.org/rfc/rfc4180.txt)을 참조하세요.|
|JSON     |`.json`     |JSON 개체가 `\n` 또는 `\r\n`으로 구분된 텍스트 파일입니다. [JSON Lines(JSONL)](http://jsonlines.org/)를 참조하세요.|
|MultiJSON|`.multijson`|JSON 속성 모음 배열(각각 레코드를 나타냄) 또는 공백으로 구분된 여러 속성 모음(`\n` 또는 `\r\n`)이 있는 텍스트 파일입니다. 각 속성 모음을 여러 줄에 분배할 수 있습니다. 데이터가 속성 모음이 아닌 한 이 형식이 `JSON`보다 선호됩니다.|
|ORC      |`.orc`      |[ORC 파일](https://en.wikipedia.org/wiki/Apache_ORC)|
|Parquet  |`.parquet`  |[Parquet 파일](https://en.wikipedia.org/wiki/Apache_Parquet). |
|PSV      |`.psv`      |파이프(<code>&#124;</code>)로 구분된 값을 사용하는 텍스트 파일입니다.|
|RAW      |`.raw`      |전체 내용이 단일 문자열 값인 텍스트 파일입니다.|
|SCsv     |`.scsv`     |세미콜론(`;`)으로 구분된 값을 사용하는 텍스트 파일입니다.|
|SOHsv    |`.sohsv`    |SOH로 구분된 값을 사용하는 텍스트 파일입니다. (SOH는 ASCII 코드 포인트 1이며, 이 형식은 Hive on HDInsight에서 사용됩니다.)|
|TSV      |`.tsv`      |탭(`\t`)으로 구분된 값을 사용하는 텍스트 파일입니다.|
|TSVE     |`.tsv`      |탭(`\t`)으로 구분된 값을 사용하는 텍스트 파일입니다. 백슬래시(`\`) 문자가 이스케이프에 사용됩니다.|
|TXT      |`.txt`      |`\n`으로 구분된 줄을 사용하는 텍스트 파일입니다. 빈 줄은 건너뜁니다.|
|W3CLOGFILE |`.log`    |W3C에서 표준화된 [웹 로그 파일](https://www.w3.org/TR/WD-logfile.html) 형식 |

## <a name="supported-data-compression-formats"></a>지원되는 데이터 압축 형식

Blob과 파일은 다음 압축 알고리즘 중 하나를 통해 압축할 수 있습니다.

|압축|내선 번호|
|-----------|---------|
|GZip       |.gz      |
|Zip        |.zip     |

Blob 또는 파일 이름에 확장을 추가하여 압축을 표시합니다.

예를 들어 다음과 같습니다.

* `MyData.csv.zip`은 CSV로 포맷되고 ZIP으로 압축된 Blob 또는 파일을 나타냅니다(보관 또는 단일 파일).
* `MyData.json.gz`는 JSON으로 포맷되고, GZip으로 압축된 Blob 또는 파일을 나타냅니다.

형식 확장명을 포함하지 않고 압축(예: `MyData.zip`)만 포함한 블로그 또는 파일 이름도 지원됩니다. 이 경우 파일 형식을 유추할 수 없으므로 수집 속성으로 지정해야 합니다.

> [!NOTE]
> * 일부 압축 형식은 압축된 스트림의 일부로 원래 파일 확장명을 추적합니다. 이 확장명은 일반적으로 파일 형식을 확인하는 경우에는 무시됩니다. 압축된 Blob 또는 파일의 이름에서 파일 형식을 확인할 수 없는 경우 `format` 수집 속성을 통해 이를 지정해야 합니다.
> * `Parquet`, `AVRO` 및 `ORC` 형식에서 사용하는 내부(청크 수준) 압축 코덱과 혼동해서는 안 됩니다. 일반적으로 내부 압축 이름은 파일 형식 확장 이전에 파일 이름에 추가됩니다(예: `file1.gz.parquet`, `file1.snappy.avro` 등).

## <a name="next-steps"></a>다음 단계

- [데이터 수집](data-explorer-ingest-data-overview.md)에 대해 자세히 알아보세요.
- [데이터 수집 속성](data-explorer-ingest-data-properties.md) 에 대 한 자세한 정보
