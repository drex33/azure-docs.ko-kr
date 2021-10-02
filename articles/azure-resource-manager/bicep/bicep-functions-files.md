---
title: Bicep 함수 - 파일
description: Bicep 파일에서 파일의 콘텐츠를 로드하는 데 사용할 함수에 대해 설명합니다.
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 28b28086986eb4e871cc6ed8c315c3802e721840
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359840"
---
# <a name="file-functions-for-bicep"></a>Bicep에 대한 파일 함수

이 문서에서는 외부 파일에서 콘텐츠를 로드하기 위한 Bicep 함수에 대해 설명합니다.

## <a name="loadfileasbase64"></a>loadFileAsBase64

`loadFileAsBase64(filePath)`

파일을 base64 문자열로 로드합니다.

네임스페이스: [sys .](bicep-functions.md#namespaces-for-functions)

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| filePath | 예 | 문자열 | 로드할 파일의 경로입니다. 경로는 배포된 Bicep 파일을 기준으로 합니다. |

### <a name="remarks"></a>설명

배포에 포함하려는 이진 콘텐츠가 있는 경우 이 함수를 사용합니다. 파일을 base64 문자열로 수동으로 인코딩하고 Bicep 파일에 추가하는 대신 이 함수를 사용하여 파일을 로드합니다. Bicep 파일이 JSON 템플릿으로 컴파일되면 파일이 로드됩니다. 배포하는 동안 JSON 템플릿에는 하드 코드된 문자열로 파일의 내용이 포함됩니다.

이 함수에는 **Bicep 버전 0.4.412 이상** 이 필요합니다. 

파일의 최대 허용 크기는 **96Kb입니다.**

### <a name="return-value"></a>반환 값

base64 문자열인 파일입니다.

## <a name="loadtextcontent"></a>loadTextContent

`loadTextContent(filePath, [encoding])`

지정된 파일의 내용을 문자열로 로드합니다. 

네임스페이스: [sys .](bicep-functions.md#namespaces-for-functions)

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| filePath | 예 | 문자열 | 로드할 파일의 경로입니다. 경로는 배포된 Bicep 파일을 기준으로 합니다. |
| encoding | 예 | 문자열 | 파일 인코딩입니다. 기본값은 `utf-8`입니다. 사용할 수 있는 옵션은 `iso-8859-1` , , , 또는 `us-ascii` `utf-16` `utf-16BE` `utf-8` 입니다.  |

### <a name="remarks"></a>설명

별도의 파일에 더 많이 저장된 콘텐츠가 있는 경우 이 함수를 사용합니다. Bicep 파일의 콘텐츠를 복제하는 대신 이 함수를 통해 콘텐츠를 로드합니다. 예를 들어 파일에서 배포 스크립트를 로드할 수 있습니다. Bicep 파일이 JSON 템플릿으로 컴파일되면 파일이 로드됩니다. 배포하는 동안 JSON 템플릿에는 하드 코드된 문자열로 파일의 내용이 포함됩니다.

JSON 파일을 로드할 때 loadTextContent 함수와 함께 [json](bicep-functions-object.md#json) 함수를 사용하여 JSON 개체를 만들 수 있습니다. VS Code 로드된 개체의 속성은 intellisense를 사용할 수 있습니다. 예를 들어 여러 Bicep 파일에서 공유할 값이 있는 파일을 만들 수 있습니다. 예제는 이 문서에 표시됩니다.

이 함수에는 **Bicep 버전 0.4.412 이상** 이 필요합니다.

파일의 최대 허용 크기는 줄 끝을 포함하여 **131,072자입니다.**

### <a name="return-value"></a>반환 값

파일의 내용을 문자열로 표시합니다.

### <a name="examples"></a>예제

다음 예제에서는 파일에서 스크립트를 로드하고 배포 스크립트에 사용합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/loaddeploymentscript.bicep" highlight="13" :::

다음 예제에서는 네트워크 보안 그룹에 사용할 값이 포함된 JSON 파일을 만듭니다.

::: code language="json" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/nsg-security-rules.json" :::

해당 파일을 로드하고 JSON 개체로 변환합니다. 개체를 사용하여 리소스에 값을 할당합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/loadsharedrules.bicep" highlight="3,13-21" :::

네트워크 보안 그룹을 배포하는 다른 Bicep 파일에서 값의 파일을 다시 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Bicep 파일의 섹션에 대한 설명은 [Bicep 파일의 구조 및 구문 이해](./file.md)를 참조하세요.
