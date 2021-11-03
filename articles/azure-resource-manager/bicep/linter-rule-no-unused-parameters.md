---
title: Lecrule-사용 되지 않는 매개 변수 없음
description: Lecrule-사용 되지 않는 매개 변수 없음
ms.topic: conceptual
ms.date: 10/14/2021
ms.openlocfilehash: 2e9c602a28b2ba19d8c0a38b1bba3ecc521e6d77
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017847"
---
# <a name="linter-rule---no-unused-parameters"></a>Lecrule-사용 되지 않는 매개 변수 없음

이 규칙은 Bicep 파일의 어디에서 나 참조 되지 않는 매개 변수를 찾습니다.

## <a name="returned-code"></a>반환 된 코드

`no-unused-params`

## <a name="solution"></a>솔루션

템플릿의 혼동을 줄이려면 정의되었지만 사용되지 않는 매개 변수를 모두 삭제합니다. 이 테스트는 템플릿의 모든 위치에서 사용되지 않는 매개 변수를 찾습니다. 사용하지 않는 매개 변수를 제거하면 불필요한 값을 제공하지 않아도 되므로 템플릿을 더 쉽게 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Linter에 대 한 자세한 내용은 [Bicep Linter 사용](./linter.md)을 참조 하세요.
