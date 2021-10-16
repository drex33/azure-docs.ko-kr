---
title: Bicep 함수
description: Bicep 파일에서 값을 검색하고, 문자열과 숫자로 작업하고, 배포 정보를 검색하는 데 사용하는 함수를 설명합니다.
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 429dca8bd4544e1dd8b22406811e53d0ed2ea623
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070568"
---
# <a name="bicep-functions"></a>Bicep 함수

이 문서에서는 Bicep 파일에서 사용할 수 있는 모든 함수에 대해 설명합니다. Bicep 파일의 섹션에 대한 설명은 [Bicep 파일의 구조 및 구문 이해](./file.md)를 참조하세요.

대부분의 함수는 리소스 그룹, 구독, 관리 그룹 또는 테넌트에 배포될 때 동일하게 작동합니다. 일부 함수는 일부 범위에서 사용할 수 없습니다. 아래 목록에 나와 있습니다.

## <a name="namespaces-for-functions"></a>함수의 네임스페이스

모든 Bicep 함수는 및 네임스페이스 내에 `az` `sys` 포함됩니다. 일반적으로 함수를 사용할 때 네임스페이스를 지정할 필요가 없습니다. 함수 이름이 Bicep 파일에서 정의한 다른 항목과 동일한 경우에만 네임스페이스를 지정합니다. 예를 들어 라는 매개 변수를 만드는 경우 `range` `range` 네임스페이스를 추가하여 함수를 구분해야 `sys` 합니다.

```bicep
// Parameter contains the same name as a function
param range int

// Must use sys namespace to call the function. 
// The second use of range refers to the parameter.
output result array = sys.range(1, range)
```

`az`네임스페이스에는 Azure 배포와 관련이 있는 함수가 포함되어 있습니다. `sys`네임스페이스에는 값을 구성하는 데 사용되는 함수가 포함되어 있습니다. `sys`네임스페이스에는 매개 변수 및 리소스 루프에 대한 데코레이터도 포함됩니다. 네임스페이스는 이 문서에 언급되어 있습니다.

## <a name="any-function"></a>모든 함수

[any 함수](./bicep-functions-any.md)는 Bicep에서 데이터 형식 경고와 관련된 문제를 해결하는 데 사용할 수 있습니다. 이 함수는 `sys` 네임스페이스에 있습니다.

## <a name="array-functions"></a>배열 함수

다음 함수는 배열 작업에 사용할 수 있습니다. 이러한 모든 함수는 `sys` 네임스페이스에 있습니다.

* [array](./bicep-functions-array.md#array)
* [concat](./bicep-functions-array.md#concat)
* [contains](./bicep-functions-array.md#contains)
* [empty](./bicep-functions-array.md#empty)
* [first](./bicep-functions-array.md#first)
* [intersection](./bicep-functions-array.md#intersection)
* [items](./bicep-functions-array.md#items)
* [last](./bicep-functions-array.md#last)
* [length](./bicep-functions-array.md#length)
* [min](./bicep-functions-array.md#min)
* [max](./bicep-functions-array.md#max)
* [range](./bicep-functions-array.md#range)
* [skip](./bicep-functions-array.md#skip)
* [take](./bicep-functions-array.md#take)
* [union](./bicep-functions-array.md#union)

## <a name="date-functions"></a>날짜 함수

다음 함수는 날짜 작업에 사용할 수 있습니다. 이러한 모든 함수는 `sys` 네임스페이스에 있습니다.

* [dateTimeAdd](./bicep-functions-date.md#datetimeadd)
* [utcNow](./bicep-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>배포 값 함수

배포와 관련된 값을 얻는 데 사용할 수 있는 함수는 다음과 같습니다. 이러한 모든 함수는 `az` 네임스페이스에 있습니다.

* [deployment](./bicep-functions-deployment.md#deployment)
* [environment](./bicep-functions-deployment.md#environment)(환경)

## <a name="file-functions"></a>파일 함수

외부 파일의 콘텐츠를 Bicep 파일로 로드하는 데 사용할 수 있는 함수는 다음과 같습니다. 이러한 모든 함수는 `sys` 네임스페이스에 있습니다.

* [loadFileAsBase64](bicep-functions-files.md#loadfileasbase64)
* [loadTextContent](bicep-functions-files.md#loadtextcontent)

## <a name="logical-functions"></a>논리 함수

다음 함수는 논리 조건 작업에 사용할 수 있습니다. 이 함수는 `sys` 네임스페이스에 있습니다.

* [bool](./bicep-functions-logical.md#bool)

## <a name="numeric-functions"></a>숫자 함수

정수 작업에 사용할 수 있는 함수는 다음과 같습니다. 이러한 모든 함수는 `sys` 네임스페이스에 있습니다.

* [int](./bicep-functions-numeric.md#int)
* [min](./bicep-functions-numeric.md#min)
* [max](./bicep-functions-numeric.md#max)

## <a name="object-functions"></a>개체 함수

다음 함수는 개체 작업에 사용할 수 있습니다. 이러한 모든 함수는 `sys` 네임스페이스에 있습니다.

* [contains](./bicep-functions-object.md#contains)
* [empty](./bicep-functions-object.md#empty)
* [intersection](./bicep-functions-object.md#intersection)
* [json](./bicep-functions-object.md#json)
* [length](./bicep-functions-object.md#length)
* [union](./bicep-functions-object.md#union)

## <a name="resource-functions"></a>리소스 함수

리소스 값을 얻는 데 사용할 수 있는 함수는 다음과 같습니다. 이러한 함수의 대부분은 `az` 네임스페이스에 있습니다. list 함수 및 getSecret 함수는 리소스 종류에서 직접 호출되므로 네임스페이스 한정자를 갖지 않습니다.

* [extensionResourceId](./bicep-functions-resource.md#extensionresourceid)
* [getSecret](./bicep-functions-resource.md#getsecret)
* [listAccountSas](./bicep-functions-resource.md#list)
* [listKeys](./bicep-functions-resource.md#listkeys)
* [listSecrets](./bicep-functions-resource.md#list)
* [list*](./bicep-functions-resource.md#list)
* [pickZones](./bicep-functions-resource.md#pickzones)
* [공급자(사용되지 않음)](./bicep-functions-resource.md#providers)
* [reference](./bicep-functions-resource.md#reference)
* [resourceId](./bicep-functions-resource.md#resourceid) - 모든 범위에서 사용할 수 있지만 유효한 매개 변수는 범위에 따라 변경됩니다.
* [subscriptionResourceId](./bicep-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](./bicep-functions-resource.md#tenantresourceid)

## <a name="scope-functions"></a>범위 함수

다음 함수는 범위 값을 가져오는 데 사용할 수 있습니다. 이러한 모든 함수는 `az` 네임스페이스에 있습니다.

* [managementGroup](./bicep-functions-scope.md#managementgroup)
* [resourceGroup](./bicep-functions-scope.md#resourcegroup) - 리소스 그룹에 대한 배포에서만 사용할 수 있습니다.
* [구독](./bicep-functions-scope.md#subscription) - 리소스 그룹 또는 구독에 대한 배포에서만 사용할 수 있습니다.
* [테넌트](./bicep-functions-scope.md#tenant)

## <a name="string-functions"></a>문자열 함수

Bicep은 문자열 작업을 위해 다음과 같은 함수를 제공합니다. 이러한 모든 함수는 `sys` 네임스페이스에 있습니다.

* [base64](./bicep-functions-string.md#base64)
* [base64ToJson](./bicep-functions-string.md#base64tojson)
* [base64ToString](./bicep-functions-string.md#base64tostring)
* [concat](./bicep-functions-string.md#concat)
* [contains](./bicep-functions-string.md#contains)
* [dataUri](./bicep-functions-string.md#datauri)
* [dataUriToString](./bicep-functions-string.md#datauritostring)
* [empty](./bicep-functions-string.md#empty)
* [endsWith](./bicep-functions-string.md#endswith)
* [first](./bicep-functions-string.md#first)
* [format](./bicep-functions-string.md#format)
* [guid](./bicep-functions-string.md#guid)
* [indexOf](./bicep-functions-string.md#indexof)
* [last](./bicep-functions-string.md#last)
* [lastIndexOf](./bicep-functions-string.md#lastindexof)
* [length](./bicep-functions-string.md#length)
* [newGuid](./bicep-functions-string.md#newguid)
* [padLeft](./bicep-functions-string.md#padleft)
* [replace](./bicep-functions-string.md#replace)
* [skip](./bicep-functions-string.md#skip)
* [split](./bicep-functions-string.md#split)
* [startsWith](./bicep-functions-string.md#startswith)
* [string](./bicep-functions-string.md#string)
* [substring](./bicep-functions-string.md#substring)
* [take](./bicep-functions-string.md#take)
* [toLower](./bicep-functions-string.md#tolower)
* [toUpper](./bicep-functions-string.md#toupper)
* [trim](./bicep-functions-string.md#trim)
* [uniqueString](./bicep-functions-string.md#uniquestring)
* [uri](./bicep-functions-string.md#uri)
* [uriComponent](./bicep-functions-string.md#uricomponent)
* [uriComponentToString](./bicep-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>다음 단계

* Bicep 파일의 섹션에 대한 설명은 [Bicep 파일의 구조 및 구문 이해](./file.md)를 참조하세요.
* 리소스 종류를 생성할 때 지정된 횟수만큼 반복하려면 [Bicep에 여러 리소스 인스턴스 배포](./loop-resources.md)를 참조하세요.
* 생성한 Bicep 파일을 배포하는 방법을 알아보려면 [Bicep 및 Azure PowerShell을 사용하여 리소스 배포](./deploy-powershell.md)를 참조하세요.
