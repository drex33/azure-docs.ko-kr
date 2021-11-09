---
title: Bicep 파일을 만들 때 모범 사례 알아보기
description: Bicep 파일이 잘 작동하고 유지 관리하기 쉽도록 Bicep 파일을 만들 때 따라야 하는 모범 사례를 설명합니다.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 65f55208f0a2e09db39cedc8e5074b622b232834
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057673"
---
# <a name="best-practices-for-bicep"></a>Bicep에 대한 모범 사례

이 문서에서는 Bicep 파일을 만들 때 따라야 하는 모범 사례를 권장합니다. 이러한 모범 사례를 통해 Bicep 파일을 더 쉽게 이해하고 사용할 수 있습니다.

### <a name="microsoft-learn"></a>Microsoft Learn

Bicep 모범 사례 및 실습 지침에 대한 자세한 내용은 **Microsoft Learn** [협업을 위한 Bicep 코드 구조를 참조하세요.](/learn/modules/structure-bicep-code-collaboration/)

## <a name="parameters"></a>매개 변수

* 매개 변수 선언에 적합한 이름을 사용합니다. 적절한 이름을 사용할 때 템플릿을 쉽게 읽고 이해할 수 있습니다. 명확하고 설명적인 이름을 사용하고, 이름 지정에서 일관성이 있어야 합니다.

* 템플릿에서 사용하는 매개 변수에 대해 신중하게 생각합니다. 배포 사이에 변경되는 설정에 매개 변수를 사용해 봅니다. 배포 사이에 변경되지 않는 설정에는 변수와 하드 코드된 값을 사용할 수 있습니다.

* 사용하는 기본값에 유의해야 합니다. 모든 사용자가 기본값을 안전하게 배포할 수 있는지 확인합니다. 예를 들어, 테스트 환경에 템플릿을 배포할 때 불필요하게 많은 비용이 발생하지 않도록 저렴한 가격 책정 계층과 SKU를 사용하는 것이 좋습니다.

* `@allowed` 데코레이터는 신중하게 사용해야 합니다. 이 데코레이터를 너무 광범위하게 사용하면 유효한 배포가 차단될 수 있습니다. Azure 서비스에서 SKU 및 크기를 추가하면 허용 목록이 최신이 아닐 수 있습니다. 예를 들어 Premium v3 SKU만 허용하면 프로덕션에 적합할 수 있지만 비프로덕션 환경에서는 동일한 템플릿을 사용할 수 없습니다.

* 매개 변수에 대한 설명을 제공하는 것이 좋습니다. 설명을 유용하게 만들고 템플릿에 필요한 매개 변수 값에 대한 중요한 정보를 제공해봅니다.

  일부 정보에는 `//` 주석을 사용할 수도 있습니다.

* 매개 변수 선언을 템플릿 파일 어디에나 배치할 수 있습니다. 단, 파일의 맨 위에 배치하여 Bicep 코드를 쉽게 읽을 수 있도록 하는 것이 좋습니다.

* 이름 지정을 제어하는 매개 변수의 최소 및 최대 문자 길이를 지정하는 것이 좋습니다. 이러한 제한은 나중에 배포하는 동안 오류를 방지하는 데 도움이 됩니다.

Bicep 매개 변수에 대한 자세한 내용은 [Bicep의 매개 변수](parameters.md)를 참조하세요.

## <a name="variables"></a>변수

* 변수를 정의할 때 [데이터 형식](data-types.md)은 필요하지 않습니다. 변수는 확인 값에서 형식을 유추합니다.

* Bicep 함수를 사용하여 변수를 만들 수 있습니다.

* Bicep 파일에 변수가 정의되면 변수 이름을 사용하여 값을 참조합니다.

Bicep 변수에 대한 자세한 내용은 [Bicep의 변수](variables.md)를 참조하세요.

## <a name="names"></a>이름

* 이름에 대해 소문자(예: 또는 )를 `myVariableName` `myResource` 사용합니다.

* [uniqueString() 함수](bicep-functions-string.md#uniquestring)는 전역적으로 고유한 리소스 이름을 만들 때 유용합니다. 동일한 매개 변수를 제공하면 매번 동일한 문자열이 반환됩니다. 리소스 그룹 ID를 전달하면 동일한 리소스 그룹에 대한 모든 배포에서 문자열이 동일하지만 다른 리소스 그룹 또는 구독에 배포할 때는 다릅니다.

* 경우에 따라 `uniqueString()` 함수는 숫자로 시작하는 문자열을 만듭니다. 일부 Azure 리소스(예: 스토리지 계정)는 숫자로 시작하는 이름을 허용하지 않습니다. 이 요구 사항은 문자열 보간을 사용하여 리소스 이름을 만드는 것이 좋습니다. 고유 문자열에 접두사를 추가할 수 있습니다.

* 템플릿 식을 사용하여 리소스 이름을 만드는 것이 좋은 경우가 자주 있습니다. 많은 Azure 리소스 종류에는 허용되는 문자 및 이름 길이에 대한 규칙이 있습니다. 템플릿에 리소스 이름 생성을 포함하면 템플릿을 사용하는 모든 사용자가 이러한 규칙을 따르기 위해 기억할 필요가 없다는 것을 의미합니다.

* 기호 `name` 이름에 를 사용하지 않습니다. 기호 이름은 리소스 이름이 아니라 리소스를 나타냅니다. 예를 들어 다음은 사용하지 마세요.

  ```bicep
  resource cosmosDBAccountName 'Microsoft.DocumentDB/databaseAccounts@2021-04-15' = {
  ```

  다음을 사용합니다.

  ```bicep
  resource cosmosDBAccount 'Microsoft.DocumentDB/databaseAccounts@2021-04-15' = {
  ```

* 접미사를 사용하여 변수와 매개 변수를 구분하지 않습니다.

## <a name="resource-definitions"></a>리소스 정의

* 복잡한 식을 리소스 속성에 직접 포함하는 대신 변수를 사용하여 식을 포함합니다. 이 접근 방식을 사용하면 Bicep 파일을 더 쉽게 읽고 이해할 수 있습니다. 논리를 사용하면 리소스 정의가 복잡해지는 것을 방지할 수 있습니다.

* 리소스 동작 방식에 대한 가정을 하는 대신 리소스 속성을 출력으로 사용합니다. 예를 들어 App Service 앱에 URL을 출력해야 하는 경우 URL의 문자열을 직접 만드는 대신 앱의 defaultHostname 속성을 사용합니다. 경우에 따라 이러한 가정이 다른 환경에서는 올바르지 않거나 리소스가 작동 방식을 변경합니다. 따라서 리소스가 고유한 속성을 알려주는 것이 더 안전합니다.

* 각 리소스에 최신 API 버전을 사용하는 것이 좋습니다. Azure 서비스의 새로운 기능은 경우에 따라 최신 API 버전에서만 사용할 수 있습니다.

* 가능하면 Bicep 파일에 [참조](./bicep-functions-resource.md#reference) 및 [resourceId](./bicep-functions-resource.md#resourceid) 함수를 사용하지 마십시오. 기호화된 이름을 사용하여 Bicep에서 모든 리소스에 액세스할 수 있습니다. 예를 들어 기호화된 이름이 toyDesignDocumentsStorageAccount인 스토리지 계정을 정의하는 경우 `toyDesignDocumentsStorageAccount.id` 식을 사용하여 해당 리소스 ID에 액세스할 수 있습니다. 기호화된 이름을 사용하여 리소스 간에 암시적 종속성을 만듭니다.

* 명시적 의존성보다 암시적 의존성 사용을 선호합니다. 리소스 `dependsOn` 속성을 사용하면 리소스 간에 명시적 종속성을 선언할 수 있지만 일반적으로 해당 기호 이름을 사용하여 다른 리소스의 속성을 사용할 수 있습니다. 이렇게 하면 두 리소스 간에 암시적 종속성이 만들어지고 Bicep에서 관계 자체를 관리할 수 있습니다.

* 리소스가 Bicep 파일에 배포되지 않은 경우, `existing` 키워드를 사용하여 리소스에 대한 기호화된 참조를 가져올 수 있습니다.

## <a name="child-resources"></a>자식 리소스

* 너무 많은 계층을 깊게 중첩하지 않도록 합니다. 중첩이 너무 많으면 Bicep 코드를 읽고 작업하기가 더 어려워집니다.

* 자식 리소스에 대한 리소스 이름을 생성하지 않습니다. Bicep이 리소스 간의 관계를 이해할 때 제공하는 이점을 잃게 됩니다. 대신 `parent` 속성 또는 중첩을 사용합니다.

## <a name="outputs"></a>출력

* 중요한 데이터는 출력을 만들면 안 됩니다. 배포 기록에 액세스할 수 있는 사람은 누구나 출력 값에 액세스할 수 있습니다. 비밀을 처리하는 데 적합하지 않습니다.

* 출력을 통해 속성 값을 전달하는 대신 `[existing` 키워드](resource-declaration.md#existing-resources)를 사용하여 이미 존재하는 리소스의 속성을 조회합니다. 출력을 통해 전달하는 대신 다른 리소스에서 이러한 방식으로 키를 조회하는 것이 가장 좋습니다. 항상 최신 데이터를 얻을 수 있을 뿐 아니라

Bicep 출력에 대한 자세한 내용은 [Bicep의 출력](outputs.md)을 참조하세요.

## <a name="tenant-scopes"></a>테넌트 범위

[테넌트 범위](deploy-to-tenant.md)에서 정책 또는 역할 할당을 만들 수 없습니다. 그러나 전체 조직에서 액세스 권한을 부여하거나 정책을 적용해야 하는 경우 이러한 리소스를 루트 관리 그룹에 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Bicep에 대한 소개는 [Bicep 빠른 시작](quickstart-create-bicep-use-visual-studio-code.md)을 참조하세요.
* Bicep 파일의 부분에 대한 자세한 내용은 [Bicep 파일의 구조 및 구문 이해](file.md)를 참조하세요.
