---
title: 리소스 집합 패턴 규칙을 만드는 방법
description: 리소스 집합 패턴 규칙을 만들어 자산이 리소스 집합으로 그룹화되는 방식을 덮어쓰는 방법 알아보기
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: 8e1ec11d3c3a22d192939dc08807abe606599d57
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128708031"
---
# <a name="create-resource-set-pattern-rules"></a>리소스 집합 패턴 규칙 만들기

대규모 데이터 처리 시스템은 일반적으로 스토리지에 단일 테이블을 여러 파일로 저장합니다. 이 개념은 리소스 집합을 사용하는 Azure Purview에서 등장합니다. 리소스 집합은 스토리지의 많은 자산을 나타내는 데이터 카탈로그의 단일 개체입니다. 더 자세히 알아보려면 [리소스 집합 이해](concept-resource-sets.md)를 참조하세요.

스토리지 계정을 스캔하는 경우 Azure Purview는 정의된 패턴 집합을 사용하여 자산 그룹이 리소스 집합인지 여부를 확인합니다. 경우에 따라 Azure Purview의 리소스 집합 그룹화에서 데이터 자산을 정확하게 반영하지 못할 수도 있습니다. 리소스 집합 패턴 규칙을 사용하면 Azure Purview에서 리소스 집합으로 그룹화된 자산을 검색하는 방법과 카탈로그 내에 표시되는 방법을 사용자 지정하거나 재정의할 수 있습니다.

패턴 규칙은 현재 다음 원본 유형에서 지원됩니다.
- Azure Data Lake Storage Gen2
- Azure Blob Storage
- Azure 파일
- Amazon S3

리소스 집합 패턴 규칙을 만들려면 고급 리소스 집합 기능 집합을 사용하도록 설정해야 합니다. 자세한 내용은 [고급 리소스 집합 이해를 참조하세요.](concept-resource-sets.md#advanced-resource-sets)

## <a name="how-to-create-a-resource-set-pattern-rule"></a>리소스 집합 패턴 규칙을 만드는 방법

아래 단계에 따라 새 리소스 집합 패턴 규칙을 만듭니다.

1. 데이터 맵으로 이동합니다. 원본 관리 제목 아래의 메뉴에서 **패턴 규칙을** 선택합니다. 새 규칙 세트를 만들려면 **+ 새로 만들기** 를 선택합니다.

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-rule.png" alt-text="새 리소스 집합 패턴 규칙 만들기" border="true":::

1. 리소스 집합 패턴 규칙의 범위를 입력합니다. 스토리지 계정 유형 및 규칙 집합을 만들려는 스토리지 계정 이름을 선택합니다. 각 규칙 집합은 **폴더 경로** 필드에 지정된 폴더 경로 범위를 기준으로 적용됩니다.

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-scope.png" alt-text="리소스 집합 패턴 규칙 구성 만들기" border="true":::

1. 구성 범위에 대한 규칙을 입력하려면 **+ 새 규칙** 을 선택합니다.

1. 다음 필드에 입력하여 규칙을 만듭니다.

   1. **규칙 이름:** 구성 규칙의 이름입니다. 이 필드는 규칙이 적용되는 자산에는 영향을 주지 않습니다.

   1. **정규화된 이름:** 텍스트, 동적 치환 및 정적 치환을 함께 사용하여 자산을 구성 규칙에 일치시키는 정규화된 경로입니다. 이 경로는 구성 규칙의 범위를 기준으로 합니다. 정규화된 이름을 지정하는 방법에 대한 자세한 지침은 아래 [구문](#syntax) 섹션을 참조하세요.

   1. **표시 이름:** 자산의 표시 이름입니다. 이 필드는 선택 사항입니다. 일반 텍스트 및 정적 치환을 사용하여 카탈로그에 자산이 표시되는 방식을 사용자 지정할 수 있습니다. 자세한 지침은 아래의 [구문](#syntax) 섹션을 참조하세요.

   1. **리소스 집합으로 그룹화 안 함:** 사용하도록 설정하면 일치하는 리소스가 리소스 집합으로 그룹화되지 않습니다.

      :::image type="content" source="media/how-to-resource-set-pattern-rules/scoped-resource-set-rule-example.png" alt-text="새 구성 규칙을 만듭니다." border="true":::

1. **추가** 를 클릭하여 규칙을 저장합니다.

> [!NOTE]
> 패턴 규칙을 만든 후에는 모든 새 검사가 수집 중에 규칙을 적용합니다. 데이터 카탈로그의 기존 자산은 몇 시간이 걸릴 수 있는 백그라운드 프로세스를 통해 업데이트됩니다. 

## <a name="pattern-rule-syntax"></a><a name="syntax"></a> 패턴 규칙 구문

리소스 집합 패턴 규칙을 만드는 경우 다음 구문을 사용하여 적용할 자산 규칙을 지정합니다.

### <a name="dynamic-replacers-single-brackets"></a>동적 치환(단일 대괄호)

단일 대괄호는 패턴 규칙에서 **동적 치환** 으로 사용됩니다. `{<replacerName:<replacerType>}` 형식을 사용하여 정규화된 이름에 동적 치환을 지정합니다. 일치하는 경우 동적 치환은 자산을 리소스 집합으로 표시해야 함을 나타내는 그룹화 조건으로 사용됩니다. 자산이 리소스 집합으로 그룹화되는 경우 정규화된 리소스 집합 경로에는 치환이 지정된 `{replacerName}`이 포함됩니다.

예를 들어 `folder1/file-1.csv` 및 `folder2/file-2.csv`의 두 자산이 `{folder:string}/file-{NUM:int}.csv` 규칙과 일치하는 경우 리소스 집합은 단일 엔터티 `{folder}/file-{NUM}.csv`가 됩니다.

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>특수한 경우: 리소스 집합으로 그룹화하지 않는 경우의 동적 치환

*리소스 집합으로 그룹화하지 않음* 이 사용하도록 설정된 경우 치환 이름은 선택적 필드가 됩니다. `{:<replacerType>}`은 유효한 구문입니다. 예를 들어, `file-1.csv` 및 `file-2.csv`에 대해 `file-{:int}.csv`가 성공적으로 일치하고 리소스 집합 대신 두 개의 다른 자산을 만듭니다.

### <a name="static-replacers-double-brackets"></a>정적 치환(이중 대괄호)

이중 대괄호는 패턴 규칙의 정규화된 이름에서 **정적 치환** 으로 사용됩니다. `{{<replacerName>:<replacerType>}}` 형식을 사용하여 정규화된 이름에 정적 치환을 지정합니다. 일치하는 경우 고유한 정적 치환 값의 각 집합은 다른 리소스 집합 그룹화를 만듭니다.

예를 들어 두 자산 `folder1/file-1.csv` 및 `folder2/file-2.csv`가 `{{folder:string}}/file-{NUM:int}.csv` 규칙에 일치하는 경우 두 개의 리소스 집합 `folder1/file-{NUM}.csv` 및 `folder2/file-{NUM}.csv`가 생성됩니다.

정적 치환은 패턴 규칙과 일치하는 자산의 표시 이름을 지정하는 데 사용할 수 있습니다. 규칙의 표시 이름에 `{{<replacerName>}}`을 사용하면 자산 이름에서 일치하는 값이 사용됩니다.

### <a name="available-replacement-types"></a>사용 가능한 대체 형식

다음은 정적 및 동적 치환에 사용할 수 있는 형식입니다.

| 유형 | 구조체 |
| ---- | --------- |
| 문자열 | 공백과 같은 구분 기호를 포함하는 1개 이상의 유니코드 문자입니다. |
| int | 1개 이상의 0-9 ASCII 문자로, 접두어는 0일 수 있습니다(예: 0001). |
| guid | [RFC 4122](https://tools.ietf.org/html/rfc4122)의 defineddefa로 UUID의 32자 또는 8-4-4-4-12자로 된 문자열 표현입니다. |
| date | [RFC 3339](https://tools.ietf.org/html/rfc3339)에 지정된 선택적 구분 기호(yyyymmdd, yyyy-mm-dd, yymmdd, yy-mm-dd)를 사용하는 6자 또는 8자로 된 0-9의 ASCII 문자입니다. |
| time | [RFC 3339](https://tools.ietf.org/html/rfc3339)에 지정된 선택적 구분 기호(HHmm, HH:mm, HHmmss, HH:mm:ss)를 사용하는 4자 또는 6자로 된 0-9의 ASCII 문자입니다. |
| timestamp | [RFC 3339](https://tools.ietf.org/html/rfc3339)에 지정된 선택적 구분 기호(yyyy-mm-ddTHH:mm, yyyymmddhhmm, yyyy-mm-ddTHH:mm:ss, yyyymmddHHmmss)를 사용하는 12자 또는 14자로 된 0-9의 ASCII 문자입니다. |
| boolean | 대/소문자를 구분하지 않고 ‘true’ 또는 ‘false’를 포함할 수 있습니다. |
| number | 0부터 시작하는 0-9의 ASCII 문자로, 접두어는 0일 수 있고(예: 0001), 선택적으로 앞에 점(‘.’)이 나올 수 있으며, 1부터 시작하는 0-9의 ASCII 문자로 접미어는 0일 수 있습니다(예: .100). |
| hex | 집합 0-1, A-F에서 1부터 시작하는 ASCII 문자로 값의 접두어는 0일 수 있습니다. |
| locale | [RFC 5646](https://tools.ietf.org/html/rfc5646)에 지정된 구문과 일치하는 문자열입니다. |

## <a name="order-of-resource-set-pattern-rules-getting-applied"></a>적용되는 리소스 집합 패턴 규칙의 순서

패턴 규칙을 적용하는 작업의 순서는 다음과 같습니다.

1. 자산이 두 규칙에 일치하는 경우 더 구체적인 범위가 우선적으로 적용됩니다. 예를 들어 `container/folder` 범위의 규칙은 `container` 범위의 규칙보다 먼저 적용됩니다.

1. 특정 범위 내에 있는 규칙의 순서입니다. 이는 UX에서 편집할 수 있습니다.

1. 자산이 지정된 규칙과 일치하지 않으면 기본 리소스 집합 추론을 적용합니다.

## <a name="examples"></a>예

### <a name="example-1"></a>예 1

전체 로드 및 델타 로드로 SAP 데이터 추출

#### <a name="inputs"></a>입력

Files:

- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`

#### <a name="pattern-rule"></a>패턴 규칙

**범위:** `https://myazureblob.blob.core.windows.net/bar/`

**표시 이름:** ’외부 고객’

**정규화된 이름:** `customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**리소스 집합:** true

#### <a name="output"></a>출력

하나의 리소스 집합 자산

**표시 이름:** 외부 고객

**정규화된 이름:** `https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>예제 2

avro 형식의 IoT 데이터

#### <a name="inputs"></a>입력

Files:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rules"></a>패턴 규칙

**범위:** `https://myazureblob.blob.core.windows.net/bar/`

규칙 1

**표시 이름:** ‘machine-89’

**정규화된 이름:** `raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**리소스 집합:** true

규칙 2

**표시 이름:** ‘machine-90’

**정규화된 이름:** `raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

**리소스 집합:** true

#### <a name="outputs"></a>출력

리소스 집합 2개

리소스 집합 1

**표시 이름:** machine-89

**정규화된 이름:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

리소스 집합 2

**표시 이름:** machine-90

**정규화된 이름:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>예제 3

avro 형식의 IoT 데이터

#### <a name="inputs"></a>입력

Files:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>패턴 규칙

**범위:** `https://myazureblob.blob.core.windows.net/bar/`

**표시 이름:** ‘machine-{{machineid}}’

**정규화된 이름:** `raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**리소스 집합:** true

#### <a name="outputs"></a>출력

리소스 집합 1

**표시 이름:** machine-89

**정규화된 이름:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

리소스 집합 2

**표시 이름:** machine-90

**정규화된 이름:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>예제 4

리소스 집합으로 그룹화 안 함

#### <a name="inputs"></a>입력

Files:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>패턴 규칙

**범위:** `https://myazureblob.blob.core.windows.net/bar/`

**표시 이름:** `Machine-{{machineid}}`

**정규화된 이름:** `raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**리소스 집합:** false

#### <a name="outputs"></a>출력

개별 자산 4개

자산 1

**표시 이름:** machine-89

**정규화된 이름:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

자산 2

**표시 이름:** machine-89

**정규화된 이름:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

자산 3

**표시 이름:** machine-89

**정규화된 이름:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

자산 4

**표시 이름:** machine-90

**정규화된 이름:** `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>다음 단계

[Azure Data Lake Gen2 스토리지 계정을 등록하고 스캔](register-scan-adls-gen2.md)하여 시작하세요.