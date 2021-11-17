---
title: Azure Active Directory ID 보호 알림
description: 알림에서 조사 활동을 지원하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 09/23/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fea2ff8f44cd017d901e94e7458d968d5c82e9f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619821"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory ID 보호 알림

Azure AD ID 보호는 두 가지 유형의 자동화된 알림 전자 메일을 보내어 사용자 위험과 위험 검색을 관리합니다.

- 위험에 노출된 사용자가 검색된 전자 메일
- 주간 다이제스트 전자 메일

이 문서에는 두 알림 전자 메일의 개요가 나와 있습니다.

그룹 할당 역할의 사용자에게 메일을 보내는 것은 지원되지 않습니다.

## <a name="users-at-risk-detected-email"></a>위험에 노출된 사용자가 검색된 전자 메일

위험에 노출된 계정 검색에 대한 응답으로 Azure AD ID 보호는 **위험에 노출된 사용자 검색됨** 이란 제목의 전자 메일 경고를 생성합니다. 전자 메일에는 **[위험 플래그가 지정된 사용자](./overview-identity-protection.md)** 보고서에 대한 링크가 포함됩니다. 모범 사례로, 위험에 노출된 사용자를 즉시 조사해야 합니다.

이 경고에 대한 구성을 사용하여 경고를 생성하려는 사용자 위험 수준을 지정할 수 있습니다. 사용자의 위험 수준이 지정한 내용에 도달하면 이메일이 생성됩니다. 예를 들어, 중간 사용자 위험에 대해 경고하도록 정책을 설정하고 실시간 로그인 위험으로 인해 사용자 John의 사용자 위험 점수가 중간 위험으로 이동할 경우 위험 검색 메일을 받게 됩니다. 사용자에게 사용자 위험 수준 계산이 지정된 위험 수준(이상)이 되도록 하는 후속 위험 검색이 있는 경우, 사용자 위험 점수가 다시 계산될 때 위험 검색 메일을 추가로 받게 됩니다. 예를 들어, 사용자가 1월 1일에 중간 위험으로 이동하는 경우, 설정이 중간 위험에 대해 경고하도록 설정된 경우 메일 알림을 받게 됩니다. 동일한 사용자가 1월 5일에 중간 위험인 또 다른 위험 검색을 했는데도 사용자 위험 점수가 다시 계산되어 여전히 중간이면 다른 메일 알림을 받게 됩니다. 

그러나 위험 검색이 발생한 시간(사용자 위험 수준을 변경한 시간)이 마지막 메일이 전송된 시간보다 최근인 경우에만 추가 메일 알림이 전송됩니다. 예를 들어, 사용자가 1월 1일 오전 5시에 로그인하고 실시간 위험이 없습니다. 즉, 해당 로그인으로 인해 메일이 생성되지 않습니다. 10분 후 오전 5시 10분에 동일한 사용자가 다시 로그인하고 실시간 위험이 높아 사용자 위험 수준이 높음으로 이동하고 메일이 전송됩니다. 그런 다음, 오전 5시 15분에는 오전 5시 원래 로그인에 대한 오프라인 위험 점수가 오프라인 위험 처리로 인해 높은 위험으로 변경됩니다. 첫 번째 로그인 시간이 이미 메일 알림을 트리거한 두 번째 로그인 이전이었기 때문에 위험 메일에 플래그가 표시된 추가 사용자가 전송되지 않습니다.

메일의 오버로드를 방지하기 위해 5초 이내에 메일을 하나만 받게 됩니다. 이 지연은 동일한 5초 동안 여러 사용자가 지정된 위험 수준으로 이동하는 경우 모든 사용자에 대한 위험 수준의 변경을 나타내는 하나의 메일을 집계하여 전송합니다.

이 [Azure AD ID 보호를 사용한 사용자 환경](concept-identity-protection-user-experience.md) 문서에 설명된 대로 조직에서 자동 수정 기능을 사용하도록 설정한 경우 조사하기 전에 사용자가 위험을 수정할 수 있습니다. **위험한 사용자** 또는 **위험한 로그인** 보고서에서 **위험 상태** 필터에 "수정됨"을 추가하여 수정된 위험한 사용자 및 위험한 로그인을 볼 수 있습니다.

![위험에 노출된 사용자가 검색된 전자 메일](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>위험에 노출된 사용자가 검색된 경고 구성

관리자로서 다음을 설정할 수 있습니다.

- **이 이메일의 생성을 트리거하는 사용자 위험 수준** - 기본적으로 위험 수준은 “높음” 위험으로 설정됩니다.
- **이 이메일의 수신자** - 전역 관리자, 보안 관리자 또는 보안 읽기 권한자 역할의 사용자가 이 목록에 자동으로 추가됩니다. 각 역할의 처음 20개 구성원에게 이메일을 보내려고 시도합니다. 사용자가 요청 시 이러한 역할 중 하나로 상승하기 위해 PIM에 등록된 경우 **이메일이 전송되는 시점에 상승될 경우에만 이메일이 수신됩니다**.
   - 선택적으로 **여기에 사용자 지정 이메일을 추가** 할 수 있습니다. 정의된 사용자에게 Azure Portal에서 연결된 보고서를 볼 수 있는 적절한 사용 권한이 있어야 합니다.

**Azure Active Directory** > **보안** > **ID 보호** > **위험에 노출된 사용자가 검색된 경고** 아래의 **Azure Portal** 에서 위험에 노출된 사용자 이메일을 구성합니다.

## <a name="weekly-digest-email"></a>주간 다이제스트 전자 메일

주 단위 요약 메일은 새로운 위험 검색의 요약을 포함합니다.  
다음을 포함합니다.

- 새로운 위험한 사용자가 탐지됨
- 실시간으로 검색된 위험한 새 로그인
- ID 보호에서 관련된 보고서에 대한 링크

![주간 다이제스트 전자 메일](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

전역 관리자, 보안 관리자 또는 보안 읽기 권한자 역할의 사용자가 이 목록에 자동으로 추가됩니다. 각 역할의 처음 20개 구성원에게 이메일을 보내려고 시도합니다. 사용자가 요청 시 이러한 역할 중 하나로 승격시키기 위해 PIM에 등록된 경우 **이메일이 전송되는 시점에 승격될 경우에만 이메일이 수신됩니다**.

### <a name="configure-weekly-digest-email"></a>주간 다이제스트 이메일 구성

관리자는 주간 다이제스트 이메일 보내기를 켜거나 끌 수 있으며 이메일을 받도록 할당된 사용자를 선택할 수 있습니다.

**Azure Active Directory** > **보안** > **ID 보호** > **주간 다이제스트** 아래의 **Azure Portal** 에서 주간 다이제스트 이메일을 구성합니다.

## <a name="see-also"></a>참고 항목

- [Azure Active Directory ID 보호](./overview-identity-protection.md)
