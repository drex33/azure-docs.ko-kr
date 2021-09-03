---
title: Azure Data Factory에서 매개 변수와 식을 사용하는 방법
description: 이 방법 문서에서는 Data Factory 엔터티 만들기에 사용할 수 있는 식과 함수에 대한 정보를 제공합니다.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.topic: conceptual
ms.date: 03/08/2020
ms.openlocfilehash: 41689a842bff067fb36dd97f980c10b71e533f8d
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539403"
---
# <a name="how-to-use-parameters-expressions-and-functions-in-azure-data-factory"></a>Azure Data Factory에서 매개 변수와 식, 함수를 사용하는 방법

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-functions-variables.md)
> * [현재 버전](how-to-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 주로 Azure Data Factory 내에서 매개 변수가 있는 데이터 파이프라인을 만드는 기능을 탐색할 수 있는 다양한 예제로 기본적인 개념을 학습하는 데 중점을 둡니다. 매개 변수화 및 동적 식은 상당한 시간을 절약하고 훨씬 더 유연한 ETL(추출, 변환, 로드) 또는 ELT(추출, 로드, 변환) 솔루션을 허용하여 솔루션 유지 관리 비용을 대폭 줄이고 기존 파이프라인으로 새 기능을 구현하는 속도를 높일 수 있기 때문에 주목할 만한 ADF 추가 기능입니다. 매개 변수화는 하드 코딩의 양을 최소화하고 솔루션에서 재사용 가능한 개체 및 프로세스의 수를 증가시키기 때문에 이러한 이점을 얻게 됩니다.

## <a name="azure-data-factory-ui-and-parameters"></a>Azure Data Factory UI 및 매개 변수

ADF 사용자 인터페이스에서 Azure Data Factory 매개 변수를 처음 사용하는 경우, 시각적으로 설명해 주는 [매개 변수와 연결된 서비스에 대한 Data Factory UI](./parameterize-linked-services.md#ui-experience) 및 [매개 변수가 있는 메타 데이터 기반 파이프라인에 대한 Data Factory UI](./how-to-use-trigger-parameterization.md#data-factory-ui)를 검토하세요.

## <a name="parameter-and-expression-concepts"></a>매개 변수 및 식 개념 

매개 변수를 사용하여 외부 값을 파이프라인, 데이터 세트, 연결된 서비스 및 데이터 흐름에 전달할 수 있습니다. 매개 변수가 리소스로 전달된 후에는 변경할 수 없습니다. 리소스를 매개 변수화하면 매번 다른 값을 사용하여 리소스를 재사용할 수 있습니다. 매개 변수는 개별적으로 또는 식의 일부로 사용될 수 있습니다. 정의의 JSON 값은 리터럴일 수도 있고 정의가 런타임에 평가되는 식일 수도 있습니다.

다음은 그 예입니다.  
  
```json
"name": "value"
```

 또는  
  
```json
"name": "@pipeline().parameters.password"
```

식은 JSON 문자열 값에서 어느 위치에나 나타날 수 있으며 그 결과 항상 다른 JSON 값이 발생합니다. 여기서 *password* 는 식의 파이프라인 매개 변수입니다. JSON 값이 식이면 at 기호(\@)를 제거하여 식의 본문을 추출합니다. \@으로 시작되는 리터럴 문자열이 필요한 경우 해당 문자열은 \@\@을 사용하여 이스케이프 처리해야 합니다. 다음 예제는 식의 작동 방식을 보여 줍니다.  
  
|JSON 값|결과|  
|----------------|------------|  
|"parameters"|'parameters' 문자가 반환됩니다.|  
|"parameters[1]"|'parameters[1]' 문자가 반환됩니다.|  
|"\@\@"|'\@'를 포함하는 1개 문자열이 반환됩니다.|  
|" \@"|'\@'를 포함하는 2개 문자열이 반환됩니다.|  
  
 *문자열 보간* 이라는 기능을 사용하면 식이 `@{ ... }`로 묶인 문자열 내부에 나타날 수도 있습니다. 예: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 문자열 보간을 사용하면 결과는 항상 문자열입니다. `myNumber`를 `42`로 정의하고 `myString`을 `foo`로 정의했다고 가정합니다.  
  
|JSON 값|결과|  
|----------------|------------|  
|“\@pipeline().parameters.myString”| `foo`을 문자열로 반환합니다.|  
|\@“@{pipeline().parameters.myString}”| `foo`을 문자열로 반환합니다.|  
|“\@pipeline().parameters.myNumber”| `42`를 *숫자* 로 반환합니다.|  
|“\@{pipeline().parameters.myNumber}”| `42`를 *문자열* 로 반환합니다.|  
|"Answer is: @{pipeline().parameters.myNumber}"| `Answer is: 42` 문자열을 반환합니다.|  
|“\@concat(‘Answer is: ’, string(pipeline().parameters.myNumber))”| `Answer is: 42` 문자열을 반환합니다.|  
|"대답은 다음과 같습니다. \@\@{pipeline().parameters.myNumber}"| `Answer is: @{pipeline().parameters.myNumber}` 문자열을 반환합니다.|  

## <a name="examples-of-using-parameters-in-expressions"></a>식에서의 매개 변수 사용 예제 

### <a name="complex-expression-example"></a>복합 식 예
아래 예에서 작업 출력의 심층 하위 필드를 참조하는 복잡한 예를 보여 줍니다. 하위 필드로 계산되는 파이프라인 매개 변수를 참조하려면 subfield1 및 subfield2의 경우와 같이 점(.) 연산자 대신 [] 구문을 사용합니다.

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

### <a name="dynamic-content-editor"></a>동적 콘텐츠 편집기

편집을 마치면 동적 콘텐츠 편집기가 콘텐츠에서 문자를 자동으로 이스케이프합니다. 예를 들어 콘텐츠 편집기의 다음 콘텐츠는 두 개의 식 함수를 사용하는 문자열 보간입니다. 

```json
{ 
  "type": "@{if(equals(1, 2), 'Blob', 'Table' )}",
  "name": "@{toUpper('myData')}"
}
```

동적 콘텐츠 편집기는 `"{ \n  \"type\": \"@{if(equals(1, 2), 'Blob', 'Table' )}\",\n  \"name\": \"@{toUpper('myData')}\"\n}"` 식으로 위의 콘텐츠를 변환합니다. 이 식의 결과는 아래에 표시된 JSON 형식 문자열입니다.

```json
{
  "type": "Table",
  "name": "MYDATA"
}
```

### <a name="a-dataset-with--parameters"></a>매개 변수가 있는 데이터 세트

다음 예제에서는 BlobDataset은 **경로** 라는 매개 변수를 사용합니다. `dataset().path` 식을 사용하여 **folderPath** 속성에 대한 값을 설정하도록 해당 값을 사용합니다. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with--parameters"></a>매개 변수가 있는 파이프라인

다음 예에서 파이프라인은 **inputPath** 및 **outputPath** 매개 변수를 사용합니다. 매개 변수가 있는 Blob 데이터 세트의 **경로** 는 이러한 매개 변수의 값을 사용하여 설정됩니다. 여기에 사용된 구문은 `pipeline().parameters.parametername`입니다. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```

  
## <a name="calling-functions-within-expressions"></a>식 내에서 함수 호출 

식 내에서 함수를 호출할 수 있습니다. 다음 섹션에서는 식에서 사용할 수 있는 함수에 대한 정보를 제공합니다.  

### <a name="string-functions"></a>문자열 함수  

문자열로 작업하려면 이러한 문자열 함수 및 일부 [컬렉션 함수](#collection-functions)도 사용할 수 있습니다.
문자열 함수는 문자열에서만 작동합니다.

| 문자열 함수 | Task |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | 둘 이상의 문자열을 결합하고 결합된 문자열을 반환합니다. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | 문자열이 지정된 하위 문자열로 끝나는지 여부를 확인합니다. |
| [guid](control-flow-expression-language-functions.md#guid) | 전역적으로 고유한 식별자(GUID)를 문자열로 생성합니다. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | 하위 문자열에 대한 시작 위치를 반환합니다. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | 마지막으로 나오는 부분 문자열의 시작 위치를 반환합니다. |
| [replace](control-flow-expression-language-functions.md#replace) | 하위 문자열을 지정된 문자열로 바꾸고 업데이트된 문자열을 반환합니다. |
| [split](control-flow-expression-language-functions.md#split) | 원래 문자열에 지정된 구분 기호 문자를 기준으로 하여 더 큰 문자열의 하위 문자열을 포함하는 배열(쉼표로 구분됨)을 반환합니다. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | 문자열이 특정 하위 문자열로 시작하는지 검사합니다. |
| [substring](control-flow-expression-language-functions.md#substring) | 지정된 위치에서 시작하여 문자열에서 문자를 반환합니다. |
| [toLower](control-flow-expression-language-functions.md#toLower) | 문자열을 소문자 형식으로 반환합니다. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | 문자열을 대문자 형식으로 반환합니다. |
| [trim](control-flow-expression-language-functions.md#trim) | 문자열에서 선행 및 후행 공백을 제거하고 업데이트된 문자열을 반환합니다. |

### <a name="collection-functions"></a>컬렉션 함수

컬렉션, 일반적으로 배열, 문자열 및 경우에 따라 사전으로 작업하려면 이러한 컬렉션 함수를 사용할 수 있습니다.

| 컬렉션 함수 | Task |
| ------------------- | ---- |
| [contains](control-flow-expression-language-functions.md#contains) | 컬렉션에 특정 항목이 있는지 검사합니다. |
| [empty](control-flow-expression-language-functions.md#empty) | 컬렉션이 비어 있는지 검사합니다. |
| [first](control-flow-expression-language-functions.md#first) | 컬렉션에서 첫 번째 항목을 반환합니다. |
| [intersection](control-flow-expression-language-functions.md#intersection) | 지정한 컬렉션에서 공통 항목 *만* 있는 컬렉션을 반환합니다. |
| [join](control-flow-expression-language-functions.md#join) | 지정된 문자로 구분되는 배열에서 *모든* 항목이 들어 있는 문자열을 반환합니다. |
| [last](control-flow-expression-language-functions.md#last) | 컬렉션에서 마지막 항목을 반환합니다. |
| [length](control-flow-expression-language-functions.md#length) | 문자열 또는 배열에서 항목 수를 반환합니다. |
| [skip](control-flow-expression-language-functions.md#skip) | 컬렉션 앞의 항목을 제거하고 *다른 모든* 항목을 반환합니다. |
| [take](control-flow-expression-language-functions.md#take) | 컬렉션 앞에서 항목을 반환합니다. |
| [union](control-flow-expression-language-functions.md#union) | 지정한 컬렉션에서 *모든* 항목이 있는 컬렉션을 반환합니다. | 

### <a name="logical-functions"></a>논리 함수  

이 함수는 조건 내에서 유용하며 논리의 형식을 평가하는 데 사용할 수 있습니다.  
  
| 논리 비교 함수 | Task |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | 모든 식이 true인지 확인합니다. |
| [equals](control-flow-expression-language-functions.md#equals) | 두 값이 동일한지 확인합니다. |
| [greater](control-flow-expression-language-functions.md#greater) | 첫 번째 값이 두 번째 값보다 큰지 검사합니다. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | 첫 번째 값이 두 번째 값보다 크거나 같은지 검사합니다. |
| [if](control-flow-expression-language-functions.md#if) | 식이 true인지 또는 false인지 검사합니다. 결과에 기반해 지정한 값을 반환합니다. |
| [less](control-flow-expression-language-functions.md#less) | 첫 번째 값이 두 번째 값보다 작은지 검사합니다. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | 첫 번째 값이 두 번째 값보다 작거나 같은지 검사합니다. |
| [not](control-flow-expression-language-functions.md#not) | 식이 false인지 검사합니다. |
| [or](control-flow-expression-language-functions.md#or) | 최소 하나의 식이 true인지 검사합니다. |
  
### <a name="conversion-functions"></a>변환 함수  

 이 함수는 언어의 각 기본 형식 간에 변환하는 데 사용됩니다.  
-   문자열
-   integer
-   float
-   boolean
-   arrays
-   dictionaries

| 변환 함수 | Task |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | 단일 지정 입력에서 배열을 반환합니다. 여러 입력의 경우 [createArray](control-flow-expression-language-functions.md#createArray)를 참조합니다. |
| [base64](control-flow-expression-language-functions.md#base64) | 문자열에 대한 base64로 인코딩된 버전을 반환합니다. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Base64로 인코딩된 문자열에 대한 이진 버전을 반환합니다. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Base64로 인코딩된 문자열에 대한 문자열 버전을 반환합니다. |
| [binary](control-flow-expression-language-functions.md#binary) | 입력 값에 대한 이진 버전을 반환합니다. |
| [bool](control-flow-expression-language-functions.md#bool) | 입력 값에 대한 부울 버전을 반환합니다. |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | 하나 이상의 매개 변수에서 Null이 아닌 첫 번째 값을 반환합니다. |
| [createArray](control-flow-expression-language-functions.md#createArray) | 여러 입력에서 배열을 반환합니다. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | 입력 값에 대한 데이터 URI를 반환합니다. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | 데이터 URI에 대한 이진 버전을 반환합니다. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | 데이터 URI에 대한 문자열 버전을 반환합니다. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Base64로 인코딩된 문자열에 대한 문자열 버전을 반환합니다. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | 데이터 URI에 대한 이진 버전을 반환합니다. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | 디코딩된 버전으로 이스케이프 문자를 바꾸는 문자열을 반환합니다. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | URL 안전하지 않은 문자를 이스케이프 문자로 대체하는 문자열을 반환합니다. |
| [float](control-flow-expression-language-functions.md#float) | 입력 값에 대해 부동 소수점 숫자를 반환합니다. |
| [int](control-flow-expression-language-functions.md#int) | 문자열에 대한 정수 버전을 반환합니다. |
| [json](control-flow-expression-language-functions.md#json) | JSON(JavaScript Object Notation) 형식 값 또는 문자열이나 XML에 대한 개체를 반환합니다. |
| [string](control-flow-expression-language-functions.md#string) | 입력 값에 대한 문자열 버전을 반환합니다. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | 이스케이프 문자로 URL 안전하지 않은 문자를 대체하여 입력 값에 대한 URI로 인코딩된 버전을 반환합니다. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | URI로 인코딩된 문자열에 대한 이진 버전을 반환합니다. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | URI로 인코딩된 문자열에 대한 문자열 버전을 반환합니다. |
| [xml](control-flow-expression-language-functions.md#xml) | 문자열에 대한 XML 버전을 반환합니다. |
| [xpath](control-flow-expression-language-functions.md#xpath) | XPath(XML 경로 언어) 식과 일치하는 노드 또는 값에 대한 XML을 확인하고 일치하는 노드 또는 값을 반환합니다. |

### <a name="math-functions"></a>수치 연산 함수  
 이 함수는 **integers** 및 **floats** 의 숫자 형식에 사용할 수 있습니다.  

| 수식 함수 | Task |
| ------------- | ---- |
| [추가](control-flow-expression-language-functions.md#add) | 두 숫자를 더한 결과를 반환합니다. |
| [div](control-flow-expression-language-functions.md#div) | 두 숫자를 나눈 결과를 반환합니다. |
| [max](control-flow-expression-language-functions.md#max) | 숫자 또는 배열 집합에서 가장 높은 값을 반환합니다. |
| [min](control-flow-expression-language-functions.md#min) | 숫자 또는 배열 집합에서 가장 낮은 값을 반환합니다. |
| [mod](control-flow-expression-language-functions.md#mod) | 두 숫자를 나눈 나머지를 반환합니다. |
| [mul](control-flow-expression-language-functions.md#mul) | 두 숫자를 곱한 결과를 반환합니다. |
| [rand](control-flow-expression-language-functions.md#rand) | 지정된 범위에서 임의의 정수를 반환합니다. |
| [range](control-flow-expression-language-functions.md#range) | 지정한 정수에서 시작하는 정수 배열을 반환합니다. |
| [sub](control-flow-expression-language-functions.md#sub) | 첫 번째 숫자에서 두 번째 숫자를 뺀 결과를 반환합니다. |
  
### <a name="date-functions"></a>날짜 함수  

| 날짜 또는 시간 함수 | Task |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | 타임스탬프에 일 수를 더합니다. |
| [addHours](control-flow-expression-language-functions.md#addHours) | 타임스탬프에 시간 수를 더합니다. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | 타임스탬프에 분 수를 더합니다. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | 타임스탬프에 초 수를 더합니다. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | 타임스탬프에 시간 단위 수를 더합니다. [getFutureTime](control-flow-expression-language-functions.md#getFutureTime)도 참조합니다. |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | UTC(Universal Time Coordinated)의 타임스탬프를 대상 표준 시간대로 변환합니다. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | 원본 표준 시간대의 타임스탬프를 대상 표준 시간대로 변환합니다. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | 원본 표준 시간대의 타임스탬프를 UTC(Universal Time Coordinated)로 변환합니다. |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | 타임 스탬프에서 월 구성 요소인 날짜를 반환합니다. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | 타임 스탬프에서 주 구성 요소인 날짜를 반환합니다. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | 타임 스탬프에서 연 구성 요소인 날짜를 반환합니다. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | 타임스탬프를 선택적 형식의 문자열로 반환합니다. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | 지정한 시간 단위를 더한 현재 타임스탬프를 반환합니다. [addToTime](control-flow-expression-language-functions.md#addToTime)도 참조합니다. |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | 지정한 시간 단위를 뺀 현재 타임스탬프를 반환합니다. [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime)도 참조합니다. |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | 타임스탬프에 대한 날의 시작을 반환합니다. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | 타임스탬프에 대한 시간의 시작을 반환합니다. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | 타임스탬프에 대한 월의 시작을 반환합니다. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | 타임스탬프에서 시간 단위 수를 뺍니다. [getPastTime](control-flow-expression-language-functions.md#getPastTime)도 함께 참조하세요. |
| [ticks](control-flow-expression-language-functions.md#ticks) | 지정한 타임스탬프에 대한 `ticks` 속성 값을 반환합니다. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | 현재 타임스탬프를 문자열로 반환합니다. |

## <a name="detailed-examples-for-practice"></a>자세한 모범 사례 예제

### <a name="detailed-azure-data-factory-copy-pipeline-with-parameters"></a>매개 변수가 있는 자세한 Azure Data Factory 복사 파이프라인 

이 [Azure Data Factory 복사 파이프라인 매개 변수 전달 자습서](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf)에서는 매개 변수를 작업 간에 전달하는 방법뿐만 아니라 파이프라인과 작업 간에 전달하는 방법을 안내합니다.

### <a name="detailed--mapping-data-flow-pipeline-with-parameters"></a>매개 변수가 있는 자세한 매핑 데이터 흐름 파이프라인 

데이터 흐름에서 매개 변수를 사용하는 방법에 대한 포괄적인 예는 [매개 변수로 데이터 흐름 매핑](./parameters-data-flow.md)을 수행하세요.

### <a name="detailed-metadata-driven-pipeline-with-parameters"></a>매개 변수가 있는 자세한 메타데이터 기반 파이프라인

매개 변수를 사용하여 메타데이터 기반 파이프라인을 디자인하는 방법에 대한 자세한 내용은 [매개 변수가 있는 메타데이터 기반 파이프라인](./how-to-use-trigger-parameterization.md)을 참조하세요. 다음은 일반적인 매개 변수 사용 사례입니다.


## <a name="next-steps"></a>다음 단계
식에 사용할 수 있는 시스템 변수 목록은 [시스템 변수](control-flow-system-variables.md)를 참조하세요.
