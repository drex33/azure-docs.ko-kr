---
title: 동적 그룹 멤버 자격에 대한 규칙 유효성 검사(미리 보기) - Azure AD | Microsoft Docs
description: Azure Active Directory에서 동적 그룹에 대한 멤버 자격 규칙에 대해 멤버를 테스트하는 방법입니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 09/02/2021
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee84f80110975f6a12a6371ae204ca85401a2adf
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129986514"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Azure Active Directory에서 동적 그룹 멤버 자격 규칙의 유효성 검사(미리 보기)

이제 Azure AD(Azure Active Directory)에서 동적 그룹 규칙의 유효성을 검사하는 방법을 제공합니다(공개 미리 보기). **규칙 유효성 검사** 탭에서 샘플 그룹 멤버에 대해 동적 규칙의 유효성을 검사하여 규칙이 예상대로 작동하는지 확인할 수 있습니다. 관리자는 동적 그룹 규칙을 만들거나 업데이트할 때 사용자 또는 디바이스가 그룹의 멤버인지 여부를 확인하려고 합니다. 이를 통해 사용자 또는 디바이스가 규칙 조건을 충족하는지 여부를 평가하고, 멤버 자격이 예상되지 않을 때 문제를 해결하는 데 도움이 됩니다.

## <a name="step-by-step-walk-through"></a>단계별 안내

시작하려면 **Azure Active Directory** > **그룹** 으로 이동합니다. 기존 동적 그룹을 선택하거나 새 동적 그룹을 만들고 동적 멤버 자격 규칙을 클릭합니다. 그러면 **규칙 유효성 검사** 탭을 볼 수 있습니다.

![규칙 유효성 검사 탭을 찾고 기존 규칙을 사용하여 시작](./media/groups-dynamic-rule-validation/validate-tab.png)

**규칙 유효성 검사** 탭에서 사용자를 선택하여 해당 멤버 자격의 유효성을 검사할 수 있습니다. 한 번에 20명의 사용자 또는 디바이스를 선택할 수 있습니다.

![사용자를 추가하여 기존 규칙의 유효성 검사](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

선택기에서 사용자 또는 디바이스를 선택한 후 **선택** 하면 유효성 검사가 자동으로 시작되고 유효성 검사 결과가 나타납니다.

![규칙 유효성 검사의 결과 보기](./media/groups-dynamic-rule-validation/validate-tab-results.png)

결과는 사용자가 그룹의 멤버인지 아닌지를 알려줍니다. 규칙이 유효하지 않거나 네트워크 문제가 있는 경우 결과는 **알 수 없음** 으로 표시됩니다. **알 수 없음** 의 경우 자세한 오류 메시지에는 필요한 문제 및 작업이 설명되어 있습니다.

![규칙 유효성 검사의 결과 세부 정보 보기](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

규칙을 수정할 수 있으며 멤버 자격의 유효성 검사가 트리거됩니다. 사용자가 그룹의 멤버가 아닌 이유를 확인하려면 "세부 정보 보기"를 클릭하고 확인 세부 정보에는 규칙을 작성하는 각 식의 결과가 표시됩니다. **확인** 을 클릭하여 종료합니다.

## <a name="next-steps"></a>다음 단계

- [그룹에 대한 동적 멤버 자격 규칙](groups-dynamic-membership.md)
