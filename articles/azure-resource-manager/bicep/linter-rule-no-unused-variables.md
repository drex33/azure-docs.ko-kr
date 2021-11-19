---
title: Lecrule-사용 되지 않는 변수 없음
description: Lecrule-사용 되지 않는 변수 없음
ms.topic: conceptual
ms.date: 11/18/2021
ms.openlocfilehash: 71163196bc5b047be6ce572d5d3a364e364988a1
ms.sourcegitcommit: 11ca7ba5a017429c22a6b0bc02acb70b83a2984a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132813642"
---
# <a name="linter-rule---no-unused-variables"></a>Lecrule-사용 되지 않는 변수 없음

이 규칙은 Bicep 파일의 어디에서 나 참조 되지 않는 변수를 찾습니다.

## <a name="linter-rule-code"></a>Lecrule 코드

[Bicep 구성 파일](bicep-config-linter.md) 에서 다음 값을 사용 하 여 규칙 설정을 사용자 지정 합니다.

`no-unused-vars`

## <a name="solution"></a>해결 방법

템플릿에서 혼동을 줄이려면 정의되었지만 사용되지 않는 모든 변수를 삭제합니다. 이 테스트는 템플릿의 모든 위치에서 사용되지 않는 변수를 찾습니다.

## <a name="next-steps"></a>다음 단계

Linter에 대 한 자세한 내용은 [Bicep Linter 사용](./linter.md)을 참조 하세요.
