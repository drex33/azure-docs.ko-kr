---
title: Bicep 함수
description: Bicep 파일에서 값을 검색하고, 문자열과 숫자로 작업하고, 배포 정보를 검색하는 데 사용하는 함수를 설명합니다.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: bd697d6c1a1eaac7877341ffbbc10b1027882dd6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124794029"
---
# <a name="bicep-functions"></a>Bicep 함수

이 문서에서는 Bicep 파일에서 사용할 수 있는 모든 함수에 대해 설명합니다. Bicep 파일의 섹션에 대한 설명은 [Bicep 파일의 구조 및 구문 이해](./file.md)를 참조하세요.

대부분의 함수는 리소스 그룹, 구독, 관리 그룹 또는 테넌트에 배포될 때 동일하게 작동합니다. 일부 함수는 일부 범위에서 사용할 수 없습니다. 아래 목록에 나와 있습니다.

## <a name="any-function"></a>모든 함수

[any 함수](./bicep-functions-any.md)는 Bicep에서 데이터 형식 경고와 관련된 문제를 해결하는 데 사용할 수 있습니다.

## <a name="array-functions"></a>배열 함수

다음 함수는 배열 작업에 사용할 수 있습니다.

* [array](./bicep-functions-array.md#array)
* [concat](./bicep-functions-array.md#concat)
* [contains](./bicep-functions-array.md#contains)
* [empty](./bicep-functions-array.md#empty)
* [first](./bicep-functions-array.md#first)
* [intersection](./bicep-functions-array.md#intersection)
* [last](./bicep-functions-array.md#last)
* [length](./bicep-functions-array.md#length)
* [min](./bicep-functions-array.md#min)
* [max](./bicep-functions-array.md#max)
* [range](./bicep-functions-array.md#range)
* [skip](./bicep-functions-array.md#skip)
* [take](./bicep-functions-array.md#take)
* [union](./bicep-functions-array.md#union)

## <a name="date-functions"></a>날짜 함수

다음 함수는 날짜 작업에 사용할 수 있습니다.

* [dateTimeAdd](./bicep-functions-date.md#datetimeadd)
* [utcNow](./bicep-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>배포 값 함수

다음 함수는 배포와 관련된 값을 가져오는 데 사용할 수 있습니다.

* [deployment](./bicep-functions-deployment.md#deployment)
* [environment](./bicep-functions-deployment.md#environment)(환경)

## <a name="file-functions"></a>파일 함수

다음 함수는 외부 파일의 콘텐츠를 Bicep 파일에 로드 하는 데 사용할 수 있습니다.

* [loadFileAsBase64](bicep-functions-files.md#loadfileasbase64)
* [loadTextContent](bicep-functions-files.md#loadtextcontent)

## <a name="logical-functions"></a>논리 함수

다음 함수는 논리 조건 작업에 사용할 수 있습니다.

* [bool](./bicep-functions-logical.md#bool)

## <a name="numeric-functions"></a>숫자 함수

다음 함수는 정수 작업에 사용할 수 있습니다.

* [int](./bicep-functions-numeric.md#int)
* [min](./bicep-functions-numeric.md#min)
* [max](./bicep-functions-numeric.md#max)

## <a name="object-functions"></a>개체 함수

다음 함수는 개체 작업에 사용할 수 있습니다.

* [contains](./bicep-functions-object.md#contains)
* [empty](./bicep-functions-object.md#empty)
* [intersection](./bicep-functions-object.md#intersection)
* [json](./bicep-functions-object.md#json)
* [length](./bicep-functions-object.md#length)
* [union](./bicep-functions-object.md#union)

## <a name="resource-functions"></a>리소스 함수

다음 함수는 리소스 값을 가져오는 데 사용할 수 있습니다.

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

다음 함수는 범위 값을 가져오는 데 사용할 수 있습니다.

* [managementGroup](./bicep-functions-scope.md#managementgroup)
* [resourceGroup](./bicep-functions-scope.md#resourcegroup) - 리소스 그룹에 대한 배포에서만 사용할 수 있습니다.
* [구독](./bicep-functions-scope.md#subscription) - 리소스 그룹 또는 구독에 대한 배포에서만 사용할 수 있습니다.
* [테넌트](./bicep-functions-scope.md#tenant)

## <a name="string-functions"></a>문자열 함수

Bicep은 문자열 작업을 위한 다음 함수를 제공합니다.

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
