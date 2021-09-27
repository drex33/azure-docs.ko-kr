---
title: Bicep 함수-파일
description: Bicep 파일에서 파일의 콘텐츠를 로드 하는 데 사용 하는 함수에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 09/13/2021
ms.openlocfilehash: 10c4bb501945bdcd5b502797e8383dbea1d0f63c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552679"
---
# <a name="file-functions-for-bicep"></a>Bicep에 대 한 파일 함수

이 문서에서는 외부 파일에서 콘텐츠를 로드 하는 Bicep 함수에 대해 설명 합니다.

## <a name="loadfileasbase64"></a>loadFileAsBase64

`loadFileAsBase64(filePath)`

Base64 문자열로 파일을 로드 합니다. 

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| filePath | 예 | 문자열 | 로드할 파일의 경로입니다. 경로는 배포 된 Bicep 파일을 기준으로 합니다. |

### <a name="remarks"></a>설명

배포에 포함할 이진 콘텐츠가 있는 경우이 함수를 사용 합니다. 파일을 수동으로 base64 문자열로 인코딩하고 Bicep 파일에 추가 하는 대신이 함수를 사용 하 여 파일을 로드 합니다. Bicep 파일이 JSON 템플릿으로 컴파일될 때 파일이 로드 됩니다. 배포 하는 동안 JSON 템플릿에는 하드 코드 된 문자열로 파일의 내용이 포함 됩니다.

이 함수 **에는 Bicep 버전 0.4.412** 이상이 필요 합니다. 

파일에 허용 되는 최대 크기는 **96** 입니다.

### <a name="return-value"></a>반환 값

Base64 문자열로 된 파일입니다.

## <a name="loadtextcontent"></a>loadTextContent

`loadTextContent(filePath, [encoding])`

지정 된 파일의 내용을 문자열로 로드 합니다. 

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| filePath | 예 | 문자열 | 로드할 파일의 경로입니다. 경로는 배포 된 Bicep 파일을 기준으로 합니다. |
| encoding | 예 | 문자열 | 파일 인코딩입니다. 기본값은 `utf-8`입니다. 사용할 수 있는 옵션은 `iso-8859-1` , `us-ascii` , `utf-16` , `utf-16BE` 또는 `utf-8` 입니다.  |

### <a name="remarks"></a>설명

콘텐츠가 별도의 파일에 저장 되어 있는 경우이 함수를 사용 합니다. Bicep 파일에서 콘텐츠를 복제 하는 대신이 함수를 사용 하 여 콘텐츠를 로드 합니다. 예를 들어 파일에서 배포 스크립트를 로드할 수 있습니다. Bicep 파일이 JSON 템플릿으로 컴파일될 때 파일이 로드 됩니다. 배포 하는 동안 JSON 템플릿에는 하드 코드 된 문자열로 파일의 내용이 포함 됩니다.

Json 파일을 로드할 때 loadTextContent 함수와 함께 [json](bicep-functions-object.md#json) 함수를 사용 하 여 json 개체를 만들 수 있습니다. VS Code에서 로드 된 개체의 속성을 사용할 수 있는 intellisense입니다. 예를 들어 여러 Bicep 파일에서 공유 하는 값으로 파일을 만들 수 있습니다. 이 문서에는 예제가 나와 있습니다.

이 함수 **에는 Bicep 버전 0.4.412** 이상이 필요 합니다.

파일의 최대 허용 크기는 줄 끝을 포함 하 여 **131072 자** 입니다.

### <a name="return-value"></a>반환 값

파일의 내용 (문자열)입니다.

### <a name="examples"></a>예제

다음 예에서는 파일에서 스크립트를 로드 하 고 배포 스크립트에 사용 합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/loaddeploymentscript.bicep" highlight="13" :::

다음 예제에서는 네트워크 보안 그룹에 사용 하려는 값이 포함 된 JSON 파일을 만듭니다.

::: code language="json" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/nsg-security-rules.json" :::

해당 파일을 로드 하 고 JSON 개체로 변환 합니다. 개체를 사용 하 여 리소스에 값을 할당 합니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/loadsharedrules.bicep" highlight="3,13-21" :::

네트워크 보안 그룹을 배포 하는 다른 Bicep 파일의 값 파일을 다시 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Bicep 파일의 섹션에 대한 설명은 [Bicep 파일의 구조 및 구문 이해](./file.md)를 참조하세요.
