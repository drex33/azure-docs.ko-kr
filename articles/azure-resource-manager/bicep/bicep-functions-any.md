---
title: Bicep 함수 - 모든
description: Bicep에서 형식을 변환하는 데 사용할 수 있는 함수에 대해 설명합니다.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 2826f6180bc8ba4c476fd067a19db25b52f5d65b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353702"
---
# <a name="any-function-for-bicep"></a>Bicep의 Any 함수

Bicep은 Bicep 형식 시스템의 형식 오류를 해결하기 위해 `any()`라는 함수를 지원합니다. 제공하는 값의 형식이 시스템에서 예상하는 형식과 일치하지 않는 경우 해당 함수를 사용합니다. 예를 들어 속성에 숫자가 필요하지만, `'0.5'`와 같이 문자열로 제공해야 하는 경우, `any()` 함수를 사용하여 형식 시스템에서 보고하는 오류를 표시하지 않습니다.

해당 함수는 Azure Resource Manager 템플릿 런타임에는 존재하지 않습니다. Bicep에서만 사용되며, 빌드된 템플릿에 대한 JSON으로 내보내지 않습니다.

> [!NOTE]
> 형식 오류를 해결 하려면 함수를 사용 하는 데 필요한 형식이 누락 되거나 잘못 된 경우 알려주세요 `any()` . [누락 된 형식 유효성 검사/](https://github.com/Azure/bicep/issues/784) 잘못 GitHub 문제에 대 한 세부 정보를 추가 합니다.

## <a name="any"></a>any

`any(value)`

모든 데이터 형식과 호환되는 값을 반환합니다.

네임 스페이스: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| 값 | 예 | 모든 형식 | 호환되는 형식으로 변환할 값입니다. |

### <a name="return-value"></a>반환 값

모든 데이터 형식과 호환되는 형태의 값입니다.

### <a name="examples"></a>예제

다음 예제에서는 `any()` 함수를 사용하여 숫자 값을 문자열로 제공하는 방법을 보여 줍니다.

```bicep
resource wpAci 'microsoft.containerInstance/containerGroups@2019-12-01' = {
  name: 'wordpress-containerinstance'
  location: location
  properties: {
    containers: [
      {
        name: 'wordpress'
        properties: {
          ...
          resources: {
            requests: {
              cpu: any('0.5')
              memoryInGB: any('0.7')
            }
          }
        }
      }
    ]
  }
}
```

함수는 Bicep에서 할당된 값에 대해 작동합니다. 다음 예에서는 `any()`를 3개로 구성된 식에 인수로 사용합니다.

```bicep
publicIPAddress: any((pipId == '') ? null : {
  id: pipId
})
```

## <a name="next-steps"></a>다음 단계

`any()` 함수의 더 복잡한 사용 방법은 다음 예제를 참조하세요.

* [특정 이름이 필요한 자식 리소스](https://github.com/Azure/bicep/blob/62eb8109ae51d4ee4a509d8697ef9c0848f36fe4/docs/examples/201/api-management-create-all-resources/main.bicep#L247)
* [리소스 종류가 있음에도 정의되지 않은 리소스 속성](https://github.com/Azure/bicep/blob/main/docs/examples/201/log-analytics-with-solutions-and-diagnostics/main.bicep#L26)

