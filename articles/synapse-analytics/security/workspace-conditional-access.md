---
title: Azure Synapse Analytics 조건부 액세스
description: Azure Synapse Analytics 조건부 액세스를 설명하는 문서
author: meenalsri
ms.author: mesrivas
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/07/2021
ms.sub-service: security
ms.custom: template-concept
ms.openlocfilehash: 4461ac0874eef735b7d01a3fc9c2c4158ddcb62d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634159"
---
# <a name="conditional-access-in-azure-synapse-analytics"></a>Azure Synapse Analytics 조건부 액세스

이제 Azure Synapse 작업 영역에 대한 조건부 액세스 정책을 구성할 수 있습니다. 조건부 액세스는 액세스 권한을 부여하거나, 액세스를 차단하거나, 리소스에 대한 다단계 인증을 적용하는 결정을 내리기 위해 디바이스 유형 및 디바이스 IP 위치와 같은 여러 신호를 함께 가져오기 위해 Azure Active Directory 제공하는 도구입니다. 조건부 액세스 정책은 Azure Active Directory 구성됩니다. [조건부 액세스](../../active-directory/conditional-access/overview.md)에 대해 자세히 알아보세요.


## <a name="configure-conditional-access"></a>조건부 액세스 구성
다음 단계에서는 Azure Synapse 작업 영역에 대한 조건부 액세스 정책을 구성하는 방법을 보여줍니다.

1. 전역 관리자 권한이 있는 계정을 사용하여 Azure Portal *로그인하고,* **Azure Active Directory** 선택하고, 메뉴에서 **보안을** 선택합니다. 
2. **조건부 액세스를** 선택한 **다음, + 새 정책** 를 선택하고 정책의 이름을 제공합니다.
3. **할당에서** 사용자 **및 그룹을** **선택하고, 사용자 및 그룹 선택** 옵션을 선택한 다음, 조건부 액세스를 위해 Azure AD 사용자 또는 그룹을 선택합니다. 선택을 클릭한 다음 완료를 클릭합니다.
4. **클라우드 앱** 을 선택하고 **앱 선택** 을 클릭합니다. **Microsoft Azure Synapse Gateway를** 선택합니다. 그런 다음 선택, 완료를 순서대로 클릭합니다.
5. **액세스 제어에서** **권한 부여를** 선택한 다음, 적용할 정책을 확인하고 완료를 선택합니다.
6. 정책 **사용** 토글을 **켜기로** 설정한 다음, 만들기를 선택합니다.


## <a name="next-steps"></a>다음 단계
조건부 액세스 정책 및 해당 구성 요소에 대해 자세히 알아봅니다.
- [일반적인 조건부 액세스 정책](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)
- [조건부 액세스 정책 빌드](../../active-directory/conditional-access/concept-conditional-access-policies.md)