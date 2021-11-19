---
title: Lecrule-관리자 사용자 이름은 리터럴이 아니어야 합니다.
description: Lecrule-관리자 사용자 이름은 리터럴이 아니어야 합니다.
ms.topic: conceptual
ms.date: 11/18/2021
ms.openlocfilehash: 1ed8707c3b5d4a207fbe84384d3d3232f85e51ba
ms.sourcegitcommit: 11ca7ba5a017429c22a6b0bc02acb70b83a2984a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132813901"
---
# <a name="linter-rule---admin-user-name-should-not-be-literal"></a>Lecrule-관리자 사용자 이름은 리터럴이 아니어야 합니다.

이 규칙은 관리자 사용자 이름이 리터럴 값으로 설정 된 경우를 찾습니다.

## <a name="linter-rule-code"></a>Lecrule 코드

[Bicep 구성 파일](bicep-config-linter.md) 에서 다음 값을 사용 하 여 규칙 설정을 사용자 지정 합니다.

`adminusername-should-not-be-literal`

## <a name="solution"></a>해결 방법

리터럴 값 이나 리터럴 값으로 계산 되는 식을 사용 하지 마십시오. 대신 사용자 이름에 대 한 매개 변수를 만들고 관리 사용자 이름에 할당 합니다.

다음 예에서는 사용자 이름이 리터럴 값 이므로이 테스트에 실패 합니다.

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-12-01' = {
  name: 'name'
  location: location
  properties: {
    osProfile: {
      adminUsername: 'adminUsername'
    }
  }
}
```

다음 예에서는 기본값이 사용 될 때 식이 리터럴 값으로 계산 되기 때문에이 테스트에 실패 합니다.

```bicep
var defaultAdmin = 'administrator'
resource vm 'Microsoft.Compute/virtualMachines@2020-12-01' = {
  name: 'name'
  location: location
  properties: {
    osProfile: {
      adminUsername: defaultAdmin
    }
  }
}
```

이 예제에서는이 테스트를 통과 합니다.

```bicep
@secure()
param adminUsername string
param location string
resource vm 'Microsoft.Compute/virtualMachines@2020-12-01' = {
  name: 'name'
  location: location
  properties: {
    osProfile: {
      adminUsername: adminUsername
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

Linter에 대 한 자세한 내용은 [Bicep Linter 사용](./linter.md)을 참조 하세요.
