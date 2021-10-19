---
title: Linter 규칙 - 사용되지 않는 변수 없음
description: Linter 규칙 - 사용되지 않는 변수 없음
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 3b59b8d72d12c365952dd8eef2c65a0f851ea561
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161871"
---
# <a name="linter-rule---no-unused-variables"></a>Linter 규칙 - 사용되지 않는 변수 없음

이 규칙은 Bicep 파일에서 참조되지 않는 변수를 찾습니다.

## <a name="returned-code"></a>반환된 코드

`no-unused-vars`

## <a name="solution"></a>해결 방법

템플릿에서 혼동을 줄이려면 정의되었지만 사용되지 않는 모든 변수를 삭제합니다. 이 테스트는 템플릿의 모든 위치에서 사용되지 않는 변수를 찾습니다.

## <a name="next-steps"></a>다음 단계

linter에 대한 자세한 내용은 [Bicep linter 사용을 참조하세요.](./linter.md)
