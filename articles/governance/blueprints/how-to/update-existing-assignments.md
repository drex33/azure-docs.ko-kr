---
title: 포털에서 기존 할당 업데이트
description: Azure Blueprints의 포털에서 기존 청사진 할당을 업데이트하는 메커니즘에 대해 알아봅니다.
ms.date: 05/01/2021
ms.topic: how-to
ms.openlocfilehash: b08862ba32dbdd52faff85115553e59ae856b453
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108761366"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>기존 청사진 할당을 업데이트하는 방법

청사진이 할당된 경우 할당을 업데이트할 수 있습니다. 다음을 포함하여 기존 할당을 업데이트하는 여러 가지 이유가 있습니다.

- [리소스 잠금](../concepts/resource-locking.md) 추가 또는 제거
- [동적 매개 변수](../concepts/parameters.md#dynamic-parameters) 값 변경
- 할당을 최근에 **게시된** 버전의 청사진으로 업그레이드

## <a name="updating-assignments"></a>할당 업데이트

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **할당된 청사진** 을 선택합니다.

1. 청사진 목록에서 청사진 할당을 선택합니다. 그런 다음, **할당 업데이트** 단추를 사용하거나, 청사진 할당을 길게 선택(또는 마우스 오른쪽 단추로 클릭)하고 **할당 업데이트** 를 선택합니다.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="'할당 업데이트' 단추가 강조 표시된 청사진 할당 페이지의 스크린샷" border="false":::

1. 원래 할당의 모든 값으로 미리 채워진 **청사진 할당** 페이지가 로드됩니다. **청사진 정의 버전**, **잠금 할당** 상태, 청사진 정의에 있는 동적 매개 변수를 변경합니다. 변경을 마쳤으면 **할당** 을 선택합니다.

1. 업데이트된 할당 세부 정보 페이지에서 새 상태를 확인합니다. 이 예제에서는 할당에 **잠금** 을 추가했습니다.

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="잠금 모드가 변경된 것을 보여 주는 업데이트된 청사진 할당의 스크린샷" border="false":::

1. 드롭다운 목록을 사용하여 다른 **할당 작업** 에 대한 세부 정보를 살펴봅니다. 선택한 할당 작업으로 업데이트된 **관리되는 리소스** 테이블.

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="할당 작업 및 해당 상태를 보여 주는 업데이트된 청사진 할당의 스크린샷" border="false":::

## <a name="rules-for-updating-assignments"></a>할당 업데이트에 대한 규칙

업데이트된 할당의 배포는 몇 가지 중요한 규칙을 따릅니다. 이러한 규칙은 이미 배포된 리소스를 확인합니다. 요청된 변경 및 배포되거나 업데이트되는 아티팩트 리소스의 유형에 따라 수행되는 작업이 결정됩니다.

- 역할 할당
  - 역할 또는 역할 담당자(사용자, 그룹 또는 앱)가 변경되면 새 역할 할당이 만들어집니다. 이전에 배포된 역할 할당은 그대로 남아 있습니다.
- 정책 할당
  - 정책 할당의 매개 변수가 변경되면 기존 할당이 업데이트됩니다.
  - 정책 할당의 정의가 변경되면 새 정책 할당이 만들어집니다.
    이전에 배포된 정책 할당은 그대로 남아 있습니다.
  - 정책 할당 아티팩트를 청사진에서 제거해도 배포된 정책 할당은 그대로 남아 있습니다.
- ARM 템플릿(Azure Resource Manager 템플릿)
  - 템플릿은 Resource Manager를 통해 **PUT** 으로 처리됩니다. 리소스 유형마다 이 작업을 처리하는 방법이 다르므로, 포함된 각 리소스의 설명서를 검토하여 Blueprints에서 실행할 때 이 작업이 미치는 영향을 확인해야 합니다.

## <a name="possible-errors-on-updating-assignments"></a>할당 업데이트 시 발생 가능한 오류

할당을 업데이트할 때 변경한 내용으로 인해 실행 시 오류가 발생할 수 있습니다. 이미 배포된 리소스 그룹의 위치를 변경하는 경우를 예로 들 수 있습니다. [Resource Manager](../../../azure-resource-manager/management/overview.md)에서 지원되는 모든 변경 작업을 수행할 수 있지만 Resource Manager에서 오류가 발생하는 경우에는 할당 오류도 발생하게 됩니다.

할당을 업데이트할 수 있는 횟수에는 제한이 없습니다. 오류가 발생할 경우 오류를 확인하고 할당에 대해 다른 업데이트를 수행합니다. 오류 시나리오 예제:

- 잘못된 매개 변수
- 기존 개체
- Resource Manager에서 지원하지 않는 변경 내용

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결
