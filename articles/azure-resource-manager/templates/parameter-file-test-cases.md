---
title: Azure Resource Manager 테스트 도구 키트에 대한 매개 변수 파일 테스트 사례
description: Azure Resource Manager 템플릿 테스트 도구 키트에서 실행되는 매개 변수 파일 테스트에 대해 설명합니다.
ms.topic: conceptual
ms.date: 07/16/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 37fefd84a6385e003a4bb501a789003e8c63d461
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114393627"
---
# <a name="test-cases-for-parameter-files"></a>매개 변수 파일에 대한 테스트 사례

이 문서에서는 [매개 변수 파일](parameter-files.md)에 대해 [템플릿 테스트 도구 키트](test-toolkit.md)로 실행되는 테스트에 대해 설명합니다. 예를 들어 이름이 _azuredeploy.parameters.json_ 인 파일이 있습니다. 이 예제에는 테스트 이름과 테스트를 **통과** 하거나 **실패** 하는 코드 샘플이 포함됩니다. 테스트를 실행하는 방법 또는 특정 테스트를 실행하는 방법에 대한 자세한 내용은 [테스트 매개 변수](test-toolkit.md#test-parameters)를 참조하세요.

이 도구 키트에는 ARM 템플릿(Azure Resource Manager 템플릿)에 대한 [테스트 사례](template-test-cases.md)와 _azuredeploy.json_ 또는 _maintemplate.json_ 이라는 기본 템플릿 파일이 포함되어 있습니다.

## <a name="use-valid-contentversion"></a>유효한 contentVersion 사용

테스트 이름: DeploymentParameters에 ContentVersion이 있어야 합니다.

`contentVersion`에는 `1.0.0.0` 형식의 문자열이 포함되어 있어야 하며 숫자만 사용해야 합니다.

다음 예제는 `contentVersion`이 없기 때문에 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

다음 예제는 `contentVersion`이 문자열이 아니기 때문에 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": {},
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

다음 예제는 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="file-must-include-parameters"></a>파일은 매개 변수를 포함해야 합니다.

테스트 이름: DeploymentParameters에 Parameters가 있어야 합니다.

매개 변수 파일은 `parameters` 섹션을 포함해야 합니다.

다음 예제는 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
}
```

다음 예제는 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="use-valid-schema-version"></a>유효한 스키마 버전 사용

테스트 이름: DeploymentParameters에 Schema가 있어야 합니다.

매개 변수 파일은 유효한 스키마 버전을 포함해야 합니다.

매개 변수 파일에는 다음과 같은 두 가지 유효한 스키마 버전이 있습니다.

- `https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#`
- `https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#`

다음 예제는 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2021-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

다음 예제는 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="parameters-must-contain-values"></a>매개 변수는 값을 포함해야 합니다.

테스트 이름: DeploymentParameters에 Value가 있어야 합니다.

매개 변수는 `value` 또는 `reference`를 포함해야 합니다. 암호와 같은 비밀의 경우 키 자격 증명 모음은 매개 변수 파일에서 `reference`를 사용합니다. 자세한 내용은 [Azure Key Vault를 사용하여 배포 중에 보안 매개 변수 값 전달](key-vault-parameter.md)을 참조하세요.

다음 예제는 `stgAcctName`에 `value`가 없기 때문에 **실패** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {}
  }
}
```

다음 예제는 **통과** 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stgAcctName": {
      "value": "demostorage01"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

- 테스트 도구 키트에 대한 자세한 내용은 [ARM 템플릿 테스트 도구 키트 사용](test-toolkit.md)을 참조하세요.
- ARM 템플릿 테스트는 [ARM 템플릿에 대한 테스트 사례](template-test-cases.md)를 참조하세요.
- createUiDefinition 테스트는 [createUiDefinition.json에 대한 테스트 사례](createUiDefinition-test-cases.md)를 참조하세요.
- 모든 파일에 대한 테스트에 대해 알아보려면 [모든 파일에 대한 테스트 사례](all-files-test-cases.md)를 참조하세요.
