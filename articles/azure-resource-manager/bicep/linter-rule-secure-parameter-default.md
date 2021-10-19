---
title: Lecrule-보안 매개 변수 기본값
description: Lecrule-보안 매개 변수 기본값
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 139b19124f22d5cb42be71d6a6042830ad7809f7
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166491"
---
# <a name="linter-rule---secure-parameter-default"></a>Lecrule-보안 매개 변수 기본값

이 규칙은 보안 매개 변수에 대 한 하드 코드 된 기본값을 찾습니다.

## <a name="returned-code"></a>반환 된 코드

`secure-parameter-default`

## <a name="solution"></a>해결 방법

빈 문자열이 나 [Newguid ()](./bicep-functions-string.md#newguid) 함수를 호출 하는 식인 경우를 제외 하 고 Bicep 파일의 [보안 매개 변수에](./parameters.md#secure-parameters) 하드 코드 된 기본값을 제공 하지 마세요.

`@secure()`암호와 같은 중요 한 값을 포함 하는 매개 변수에 데코레이터를 사용 합니다. 매개 변수가 보안 데코레이터를 사용 하는 경우 매개 변수 값이 기록 되지 않거나 배포 기록에 저장 되지 않습니다. 이 작업을 수행하면 악의적인 사용자가 중요한 값을 검색하지 못합니다.

그러나 보안 매개 변수에 대 한 기본값을 제공 하는 경우 해당 값은 템플릿이나 배포 기록에 액세스할 수 있는 모든 사용자가 검색할 수 있습니다.

다음 예에서는 매개 변수가 하드 코드 된 기본값을 포함 하기 때문에이 테스트에 실패 합니다.

```bicep
@secure()
param adminPassword string = 'HardcodedPassword'
```

기본값을 제거 하 여 문제를 해결할 수 있습니다.

```bicep
@secure()
param adminPassword string
```

또는 기본값에 대해 빈 문자열을 제공 합니다.

```bicep
@secure()
param adminPassword string = ''
```

또는를 사용 하 여 `newGuid()` 기본값을 생성 합니다.

```bicep
@secure()
param adminPassword string = newGuid()
```

## <a name="next-steps"></a>다음 단계

Linter에 대 한 자세한 내용은 [Bicep Linter 사용](./linter.md)을 참조 하세요.
