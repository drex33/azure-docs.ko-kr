---
title: ASIM(Azure Sentinel 정보 모델) 파서 | Microsoft Docs
description: 이 문서에서는 쿼리 시간 파서로 KQL 함수를 사용하여 ASIM(Azure Sentinel 정보 모델)을 구현하는 방법을 설명합니다.
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2021
ms.author: ofshezaf
ms.openlocfilehash: b3dc1575258c495a2be63eced6aa141c60bb6631
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659441"
---
# <a name="azure-sentinel-information-model-asim-parsers-public-preview"></a>ASIM(Azure Sentinel 정보 모델) 파서(퍼블릭 미리 보기)

Azure 센티널에서 구문 분석 및 [정규화](normalization.md) 는 쿼리 시에 발생 합니다. 파서는 **CommonSecurityLog**, 사용자 지정 로그 테이블 또는 Syslog와 같은 기존 테이블의 데이터를 정규화된 스키마로 변환하는 [KQL 사용자 정의 함수](/azure/data-explorer/kusto/query/functions/user-defined-functions)로 빌드됩니다. 파서를 작업 영역 함수로 저장 한 후에는 Azure 센티널 테이블 처럼 사용할 수 있습니다.

> [!TIP]
> 또한 [Azure 센티널 정규화 파서와 웹 세미나의 심층](https://www.youtube.com/watch?v=zaqblyjQW6k) 조사를 시청 하 고 [슬라이드](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM)를 검토 합니다. 자세한 내용은 [다음 단계](#next-steps)를 참조하세요.
>

> [!IMPORTANT]
> ASIM은 현재 미리 보기 상태입니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="source-agnostic-and-source-specific-parsers"></a>소스 독립적 및 소스 관련 파서

ASIM에는 두 가지 수준의 파서인 **원본 중립적** 및 **원본 관련** 파서가 포함됩니다.

### <a name="source-agnostic-parsers"></a>원본 중립적 파서

**원본 중립적 파서** 는 동일한 스키마로 정규화된 모든 원본을 결합하며 정규화된 필드를 사용하여 모든 원본을 쿼리하는 데 사용될 수 있습니다. 원본 중립적 파서 이름은 `im<schema>`입니다. 여기서 `<schema>`는 파서가 제공하는 특정 스키마를 나타냅니다.

예를 들어, 다음 쿼리는 원본 중립적 DNS 파서를 사용하여 `ResponseCodeName`, `SrcIpAddr`, `TimeGenerated` 정규화된 필드를 사용하여 DNS 이벤트를 쿼리합니다.

```kusto
imDns
  | where isnotempty(ResponseCodeName)
  | where ResponseCodeName =~ "NXDOMAIN"
  | summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
```

원본 중립적 파서는 `union` KQL 연산자를 사용하여 여러 원본 관련 정규화된 파서를 결합할 수 있습니다. 원본 관련 정규화된 파서의 이름은 `vim<schema><vendor><product>`입니다. 따라서 `imDns` 파서는 다음과 같이 표시됩니다.

```kusto
union isfuzzy=true
vimDnsEmpty,
vimDnsCiscoUmbrella,
vimDnsInfobloxNIOS,
vimDnsMicrosoftOMS
```

> [!NOTE]
> 로그 페이지에서로 시작 하는 ASIM 소스에 관계 없는 파서를 사용 하는 경우 `im` 시간 범위 선택기는로 설정 됩니다  `custom` . 시간 범위는 직접 설정할 수 있습니다. 또는 파서 매개 변수를 사용 하 여 시간 범위를 지정 합니다.
>
> 또는 `ASim` 매개 변수를 지원 하지 않는 파서를 사용 하 고 기본적으로 시간 범위 선택도로 설정 하지 않습니다 `custom` .
>

### <a name="source-specific-parsers"></a>원본 관련 파서

**원본 관련** 정규화된 파서를 원본 중립적 파서에 추가하면 원본 중립적 파서를 사용하는 기본 제공 쿼리에 사용자 지정 원본을 포함할 수 있습니다.

원본 관련 파서를 사용하면 분석, 통합 문서, 사용자 지정 데이터 인사이트 등 기본 제공 콘텐츠에서 즉치 값을 가져올 수 있습니다.

원본 관련 파서는 독립적으로 사용할 수도 있습니다. 예를 들어, Infoblox 관련 통합 문서에서 `vimDnsInfobloxNIOS` 파서를 사용합니다.

## <a name="optimizing-parsing-using-parameters"></a><a name="optimized-parsers"></a>매개 변수를 사용 하 여 구문 분석 최적화

파서를 사용 하면 쿼리 성능에 영향을 줄 수 있으며, 주로 구문을 분석 한 후에 결과를 필터링 하지 않아도 됩니다. 이러한 이유로 대부분의 파서는 선택적 필터링 매개 변수를 가지 며이를 통해 구문 분석 하 고 쿼리 성능을 향상 시킬 수 있습니다. ASIM 파서는 쿼리 최적화 및 미리 필터링 작업을 함께 사용 하는 경우 정규화를 사용 하지 않는 것과 비교할 때 더 나은 성능을 제공 하는 경우가 많습니다.

파서를 호출할 때 하나 이상의 명명 된 매개 변수를 추가 하 여 필터링 매개 변수를 사용 합니다. 예를 들어 다음 쿼리를 시작 하면 존재 하지 않는 도메인에 대 한 DNS 쿼리만 반환 됩니다.

```kusto
imDns(responsecodename='NXDOMAIN')
```

이전 예제는 다음 쿼리와 유사 하지만 훨씬 더 효율적입니다.

```kusto
imDns | where ResponseCodeName == 'NXDOMAIN'
```

각 스키마에는 스키마 문서에서 설명 하는 표준 필터링 매개 변수 집합이 있습니다. 필터링 매개 변수는 완전히 선택적 이며 현재 DNS 스키마에 대해서만 완전 하 게 지원 됩니다. 다른 스키마는 미리 필터링 최적화 없이 표준 필터링 매개 변수를 지원 합니다.

## <a name="writing-source-specific-parsers"></a>원본 관련 파서 작성

파서는 작업 영역 함수로 저장된 KQL 쿼리입니다. 저장된 후에는 기본 제공 테이블처럼 사용할 수 있습니다. 파서 쿼리에는 다음 부분이 포함됩니다.

**필터** > **구문 분석** > **필드 준비**

### <a name="filtering"></a>필터링

#### <a name="filtering-the-relevant-records"></a>관련 레코드 필터링

대부분의 경우 테이블에는 여러 유형의 이벤트가 포함됩니다. 예를 들면 다음과 같습니다.
* Syslog 테이블에는 여러 원본의 데이터가 있습니다.
* 사용자 지정 테이블은 둘 이상의 이벤트 유형을 제공하는 단일 원본의 데이터를 포함하고 다양한 스키마에 적합할 수 있습니다.

따라서 파서는 먼저 대상 스키마와 관련된 레코드만 필터링해야 합니다.

KQL의 필터링은 `where` 연산자를 사용하여 수행합니다. 예를 들어, **Sysmon event 1** 은 프로세스 생성을 보고하며 **ProcessEvent** 스키마로 정규화되어야 합니다. **Sysmon event 1** 이벤트는 `Event` 테이블의 일부이며 다음 필터를 사용해야 합니다.

```kusto
Event | where Source == "Microsoft-Windows-Sysmon" and EventID == 1
```

#### <a name="filtering-based-on-parser-parameters"></a>파서 매개 변수를 기준으로 필터링

[매개 변수가 있는 파서](#optimized-parsers)를 사용 하는 경우 해당 스키마에 대 한 참조 문서에 설명 된 대로 파서가 관련 스키마에 대 한 필터링 매개 변수를 허용 하는지 확인 합니다.

함수 문서는 각 스키마에 대해 동일 합니다. 예를 들어, DNS 쿼리 매개 변수가 있는 파서 서명의 경우 다음과 같습니다.

```kusto
let DNSQuery_MS=(
    starttime:datetime=datetime(null), 
    endtime:datetime=datetime(null), 
    srcipaddr:string='*', 
    domain_has_any:dynamic=dynamic([]),
    responsecodename:string='*', 
    dnsresponsename:string='*', 
    response_has_any_prefix:dynamic=dynamic([]),
    eventtype:string='lookup'
    )
```

매개 변수 값에 따라 필터를 추가 합니다. 필터링 할 때 다음을 확인 합니다.

- **실제 필드를 사용 하 여 구문 분석 하기 전에 필터링** 합니다. 필터링 된 결과가 정확 하지 않을 경우 구문 분석 후 테스트를 반복 하 여 결과를 미세 조정 합니다. 자세한 내용은  ["필터링 최적화"](#optimization)를 참조 하십시오.
 - **매개 변수가 정의 되어 있지 않고 여전히 기본값을 포함 하는 경우 필터링 하지 마십시오**. 다음 예에서는 문자열 매개 변수에 대 한 필터링을 구현 하는 방법을 보여 줍니다. 기본값은 일반적으로 ' \* '이 고, 목록 매개 변수의 경우 기본값은 일반적으로 빈 목록입니다.

``` kusto
srcipaddr=='*' or ClientIP==srcipaddr
array_length(domain_has_any) == 0 or Name has_any (domain_has_any)
```

> [!TIP]
> 동일한 형식의 기존 파서는 매개 변수 필터링을 구현 하는 데 적합 합니다.
>

#### <a name="filtering-optimization"></a><a name="optimization"></a>필터링 최적화


파서의 성능을 보장하려면 다음 필터링 권장 사항을 참고하세요.

-   항상 구문 분석된 필드가 아닌 기본 제공 필드를 기준으로 필터링합니다. 구문 분석된 필드를 사용하여 필터링하는 것이 더 쉬울 수도 있지만 성능에 큰 영향을 미칩니다.
-   최적화된 성능을 제공하는 연산자를 사용합니다. 특히 `==`, `has`, `startswith`를 사용합니다. `contains` 또는 `matches regex`와 같은 연산자를 사용해도 성능에 큰 영향을 미칩니다.

성능에 대한 필터링 권장 사항은 항상 따르기에 간단한 것은 아닐 수 있습니다. 예를 들어, `has` 사용은 `contains`보다 덜 정확합니다. 다른 경우에는 `SyslogMessage`와 같은 기본 제공 필드와 일치시키는 것은 `DvcAction`과 같은 추출된 필드와 비교하는 것보다 덜 정확합니다. 이러한 경우에는 기본 제공 필드에 대해 성능 최적화 연산자를 사용하여 미리 필터링하고 구문 분석 후에 더 정확한 조건을 사용하여 필터를 반복하는 것이 좋습니다.

예를 들어 다음 [Infoblox DNS](https://aka.ms/AzSentinelInfobloxParser) 파서 코드 조각을 참조 하세요. 파서는 먼저 SyslogMessage 필드에 `client` 단어가 `has`(있는지) 확인합니다. 그러나 이 용어는 메시지의 다른 위치에서 사용될 수 있습니다. 따라서 `Log_Type` 필드를 구문 분석한 후 파서는 `client` 단어가 실제로 필드의 값이었는지 다시 확인합니다.

```kusto
Syslog | where ProcessName == "named" and SyslogMessage has "client"
…
      | extend Log_Type = tostring(Parser[1]),
      | where Log_Type == "client"
```

> [!NOTE]
> 파서를 사용 하는 쿼리는 이미 시간을 필터링 하기 때문에 파서는 시간을 기준으로 필터링 해서는 안 됩니다.
>

### <a name="parsing"></a>구문 분석

쿼리에서 관련 레코드를 선택한 후에는 해당 레코드를 구문 분석해야 할 수 있습니다. 일반적으로 단일 텍스트 필드에서 많은 이벤트 정보를 전달하는 경우 구문 분석이 필요합니다.

구문 분석을 수행하는 KQL 연산자는 성능 최적화를 기준으로 정렬되어 아래에 나열됩니다. 첫 번째는 가장 많이 최적화된 성능을 제공하는 반면, 마지막은 가장 덜 최적화된 성능을 제공합니다.

|연산자  |설명  |
|---------|---------|
|[split](/azure/data-explorer/kusto/query/splitfunction)     |    구분 기호로 구분된 값의 문자열 구문 분석     |
|[parse_csv](/azure/data-explorer/kusto/query/parsecsvfunction)     |     CSV(쉼표로 구분된 값) 줄로 형식이 지정된 값의 문자열을 구문 분석합니다.    |
|[parse](/azure/data-explorer/kusto/query/parseoperator)     |    패턴을 사용하여 임의 문자열에서 여러 값을 구문 분석합니다. 이 패턴은 성능이 향상된 단순화된 패턴이거나 정규식일 수 있습니다.     |
|[extract_all](/azure/data-explorer/kusto/query/extractallfunction)     | 정규식을 사용하여 임의 문자열에서 단일 값을 구문 분석합니다. `extract_all`의 성능은 정규식을 사용하는 `parse`와 비슷합니다.        |
|[extract](/azure/data-explorer/kusto/query/extractfunction)     |    정규식을 사용하여 임의 문자열에서 단일 값을 추출합니다. <br><br>`extract`를 사용하면 단일 값이 필요한 경우 `parse` 또는 `extract_all`보다 더 나은 성능이 제공됩니다. 그러나 동일한 원본 문자열에서 `extract`의 여러 활성화를 사용하는 것은 단일 `parse` 또는 `extract_all`보다 덜 효율적이므로 피해야 합니다.      |
|[parse_json](/azure/data-explorer/kusto/query/parsejsonfunction)  | JSON으로 형식이 지정된 문자열의 값을 구문 분석합니다. JSON에서 몇 개의 값만 필요한 경우 `parse`, `extract` 또는 `extract_all`을 사용하면 더 나은 성능이 제공됩니다.        |
|[parse_xml](/azure/data-explorer/kusto/query/parse-xmlfunction)     |    XML로 형식이 지정된 문자열의 값을 구문 분석합니다. XML에서 몇 개의 값만 필요한 경우 `parse`, `extract` 또는 `extract_all`을 사용하면 더 나은 성능이 제공됩니다.     |
| | |

문자열 구문 분석 외에도 구문 분석 단계에서 다음을 포함하여 원래 값을 더 많이 처리해야 할 수 있습니다.

- **형식 지정 및 형식 변환**. 원본 필드는 추출된 후 대상 스키마 필드에 맞게 형식을 지정해야 할 수 있습니다. 예를 들어, 날짜와 시간을 나타내는 문자열을 날짜/시간 필드로 변환해야 할 수 있습니다.     이 경우 `todatetime`, `tohex` 등의 함수가 도움이 됩니다.

- **값 조회**. 원본 필드의 값은 추출된 후 대상 스키마 필드에 대해 지정된 값 세트에 매핑해야 할 수 있습니다. 예를 들어, 일부 원본은 숫자 DNS 응답 코드를 보고하지만 스키마는 더 일반적인 텍스트 응답 코드를 요구합니다. 및 함수 `iff` 는 `case` 몇 가지 값을 매핑하는 데 도움이 될 수 있습니다.

    예를 들어, Microsoft DNS 파서는 다음과 같이 `iff` 문을 사용하여 이벤트 ID와 응답 코드를 기반으로 `EventResult` 필드를 할당합니다.

    ```kusto
    extend EventResult = iff(EventId==257 and ResponseCode==0 ,'Success','Failure')
    ```

    여러 값의 경우 동일한 DNS 파서에서 설명된 대로 `datatable` 및 `lookup`을 사용합니다.

    ```kusto
    let RCodeTable = datatable(ResponseCode:int,ResponseCodeName:string) [ 0, 'NOERROR', 1, 'FORMERR'....];
    ...
     | lookup RCodeTable on ResponseCode
     | extend EventResultDetails = case (
         isnotempty(ResponseCodeName), ResponseCodeName,
         ResponseCode between (3841 .. 4095), 'Reserved for Private Use',
         'Unassigned')
    ```

> [!NOTE]
> 여러 값이 `Reserved for Private Use` 또는 `Unassigned`에 매핑될 때 변환에서 `lookup`만 사용할 수는 없으므로 쿼리는 lookup과 case를 둘 다 사용합니다.
> 이 경우에도 쿼리는 모든 값에 대해 `case`를 사용하는 것보다 훨씬 더 효율적입니다.
>

### <a name="prepare-fields-in-the-result-set"></a>결과 집합에서 필드 준비

파서는 정규화 된 필드가 사용 되는지 확인 하기 위해 결과 집합 필드를 준비 해야 합니다. 참고로, 정규화되지 않은 원래 필드는 혼동을 초래하는 경우와 같이 제거해야 할 납득할 만한 이유가 있는 경우가 아니면 결과 집합에서 제거하면 안 됩니다.

다음 KQL 연산자는 필드를 준비하는 데 사용됩니다.

|연산자  | 설명  | 파서에서 사용하는 경우  |
|---------|---------|---------|
|**extend**     | 계산 필드를 만들고 레코드에 추가합니다.        |  `Extend`는 정규화된 필드가 원래 데이터에서 구문 분석되거나 변환되는 경우에 사용됩니다. 자세한 내용은 위 [구문 분석](#parsing) 섹션의 예제를 참조하세요.     |
|**project-rename**     | 필드 이름을 바꿉니다.        |     실제 이벤트에 필드가 있으며 이름을 변경 해야 하는 경우에는를 사용 `project-rename` 합니다. <br><br>이름을 바꾼 필드는 계속 기본 제공 필드처럼 동작하고 필드에 대한 작업은 훨씬 더 나은 성능을 제공합니다.   |
|**project-away**     |      필드를 제거합니다.   |결과 집합에서 제거하려는 특정 필드에는 `project-away`를 사용합니다.         |
|**project**     |  이전에 존재 하거나 문의 일부로 생성 된 필드를 선택 합니다. 다른 모든 필드를 제거합니다.       | 파서가 정규화되지 않은 다른 필드를 제거하지 않아야 하므로 파서에서 사용하지 않는 것이 좋습니다. <br><br>구문 분석 중에 사용되는 임시 값과 같은 특정 필드를 제거해야 하는 경우에는 `project-away`를 사용하여 결과에서 제거합니다.      |
| | | |

### <a name="handle-parsing-variants"></a>구문 분석 변형 처리

대부분의 경우 이벤트 스트림의 이벤트는 다른 구문 분석 논리가 필요한 변형을 포함합니다.

서로 다른 구문 분석 논리를 필요로 하는 다른 이벤트 변형을 처리 하는 서로 다른 하위 파서에서 파서를 빌드하는 경우가 종종 있습니다. 각각 자체적으로 쿼리인 이 하위 파서는 `union` 연산자를 사용하여 통합됩니다. 이 접근 방식은 편리하지만 파서 성능에 크게 영향을 미치므로 권장되지 ‘않습니다’.

변형을 처리하는 경우 다음 지침을 사용합니다.

|시나리오  |처리  |
|---------|---------|
|서로 다른 변형은 일반적으로 서로 다른 스키마에 매핑되는 ‘서로 다른’ 이벤트 유형을 나타냅니다.     |  개별 파서 사용       |
|다른 변형은 *동일한* 이벤트 유형을 나타내지만 다르게 구성 됩니다.     |   구문 분석 전에 이벤트를 구별하는 메서드가 있는 경우와 같이 변형이 알려진 경우 [Infoblox DNS 파서](https://aka.ms/AzSentinelInfobloxParser)에서 설명된 대로 `case` 연산자를 사용하여 실행할 올바른 `extract_all`과 필드 매핑을 선택합니다.      |
|`union`이 불가피한 경우     |  `union` 사용이 불가피한 경우 다음 지침을 사용해야 합니다.<br><br>- 하위 쿼리 각각에서 기본 제공 필드를 사용하여 미리 필터링합니다. <br>- 필터가 함께 사용할 수 없는지 확인합니다. <br>- 덜 중요한 정보를 구문 분석하지 않아 하위 쿼리 수를 줄이는 것이 좋습니다.       |
| | |

## <a name="add-your-parser-to-the-schema-source-agnostic-parser"></a><a name="include"></a>스키마 원본 중립적 파서에 파서 추가

정규화를 사용하면 사용자 지정 데이터와 함께 고유한 콘텐츠와 기본 제공 콘텐츠를 사용할 수 있습니다.

예를 들어 DNS 쿼리 작업 로그를 수신 하는 사용자 지정 커넥터가 있는 경우 DNS 쿼리 작업 로그가 정규화 된 DNS 콘텐츠를 사용 하도록 할 수 있습니다.

이렇게 하려면 사용자가 만든 소스 관련 파서를 포함 하도록 관련 소스에 관계 없는 파서를 수정 합니다. 예를 들어 문의 파서 `imDns` 목록에 파서를 추가 하 여 파서를 포함 하도록 소스에 관계 없는 파서를 변경 `union` 합니다. 파서가 매개 변수를 지 원하는 경우 아래 vimDnsYyyXxx 파서와 같은 매개 변수 서명과 함께 매개 변수를 포함 합니다. 그렇지 않은 경우 아래에 vimDnsWwwZzz parer와 같이 서명 없이 포함 합니다.

```kusto
let DnsGeneric=(starttime:datetime=datetime(null), endtime:datetime=datetime(null)... ){
  union isfuzzy=true
    vimDnsEmpty, 
    vimDnsCiscoUmbrella (starttime, endtime, srcipaddr...),
    vimDnsInfobloxNIOS (starttime, endtime, srcipaddr...),
    vimDnsMicrosoftOMS (starttime, endtime, srcipaddr...),
    vimDnsYyyXxx (starttime, endtime, srcipaddr...),
    vimDnsWwwZzz
  };
  DnsGeneric( starttime, endtime, srcipaddr...)
```


## <a name="deploy-parsers"></a>파서 배포

파서를 Azure Monitor 로그 페이지에 복사하고 변경 내용을 저장하여 파서를 수동으로 배포합니다. 이 방법은 테스트에 유용합니다. 자세한 내용은 [함수 만들기](../azure-monitor/logs/functions.md)를 참조하세요.

그러나 많은 파서를 배포하려면 ARM 템플릿을 사용하는 것이 좋습니다. 예를 들어, 원본 중립적 파서와 여러 원본 관련 파서를 포함하는 완전한 정규화 솔루션을 배포할 때 또는 원본의 다양한 스키마에 대해 여러 파서를 배포할 때 ARM 템플릿을 사용할 수 있습니다.

자세한 내용은 [일반 파서 ARM 템플릿](https://github.com/Azure/Azure-Sentinel/tree/master/Tools/ARM-Templates/ParserQuery)을 참조하세요. 이 템플릿을 시작 지점으로 사용하고 템플릿 배포 프로세스 중에 관련 지점에 붙여넣어 파서를 배포합니다. 예제에 관해서는 [DNS 파서 ARM 템플릿](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM)을 참조하세요.

> [!TIP]
> ARM 템플릿은 다양한 리소스를 포함할 수 있으므로 커넥터, 분석 규칙 또는 관심 목록과 함께 파서를 배포하여 몇 가지 유용한 옵션의 이름을 지정할 수 있습니다. 예를 들어, 파서는 함께 배포될 관심 목록을 참조할 수 있습니다.
>


## <a name="next-steps"></a><a name="next-steps"></a>다음 단계

이 문서에서는 ASIM(Azure Sentinel 정보 모델) 파서를 설명합니다.

자세한 내용은 다음을 참조하세요.

- [Azure 센티널의 심층 조사 웹 세미나 및 표준화 된 콘텐츠](https://www.youtube.com/watch?v=zaqblyjQW6k) 를 시청 하거나 [슬라이드](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM) 를 검토 합니다.
- [Azure Sentinel 정보 모델 개요](normalization.md)
- [Azure Sentinel 정보 모델 스키마](normalization-about-schemas.md)
- [Azure Sentinel 정보 모델 콘텐츠](normalization-content.md)
