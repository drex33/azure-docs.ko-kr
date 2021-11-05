---
title: ‘로그인 상태를 유지하시겠습니까?’ 구성 Azure Active Directory 계정 프롬프트
description: 로그인 상태를 유지하시겠습니까? 프롬프트를 표시하는 KMSI(로그인 상태 유지), Azure Active Directory 포털에서 구성하는 방법, 로그인 이슈를 해결하는 방법을 알아봅니다.
services: active-directory
author: CelesteDG
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: fundamentals
ms.date: 06/05/2020
ms.author: celested
ms.reviewer: asteen, jlu, hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: e89eb124b9cfb4100ddfca0d949331508fce398f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052392"
---
# <a name="configure-the-stay-signed-in-prompt-for-azure-ad-accounts"></a>‘로그인 상태를 유지하시겠습니까?’ 구성 Azure AD 계정 프롬프트

KMSI(로그인 상태 유지)는 사용자가 로그인에 성공한 후 **로그인 상태를 유지하시겠습니까?** 프롬프트를 표시합니다. 사용자가 이 프롬프트에 **예** 로 응답하면 로그인 상태 유지 서비스에서 영구적 [새로 고침 토큰](../develop/developer-glossary.md#refresh-token)을 제공합니다. 페더레이션된 테넌트의 경우 사용자가 페더레이션된 ID 서비스에 인증한 후 프롬프트가 표시됩니다.

다음 다이어그램은 관리형 테넌트 및 페더레이션된 테넌트의 사용자 로그인 흐름과 새로운 로그인 상태 유지 프롬프트를 보여 줍니다. 기계 학습 시스템이 높은 위험 수준의 로그인 또는 공유 디바이스에서의 로그인을 검색한 경우 **로그인 상태를 유지하시겠습니까?** 옵션이 표시되지 않도록 이 흐름에는 스마트 논리가 포함되어 있습니다.

:::image type="content" source="./media/keep-me-signed-in/kmsi-workflow.png" alt-text="관리형 테넌트 대 페더레이션된 테넌트의 사용자 로그인 흐름을 보여 주는 다이어그램":::

> [!NOTE]
> 로그인 상태 유지 옵션을 구성하려면 Azure AD(Azure Active Directory) Premium 1, Premium 2 또는 Basic 버전을 사용하거나 Microsoft 365 라이선스가 있어야 합니다. 라이선스 및 버전에 대한 자세한 내용은 [Azure AD Premium에 등록](active-directory-get-started-premium.md)을 참조하세요.<br><br>Azure AD Premium 및 Basic 버전은 Azure AD의 전 세계 인스턴스를 사용하여 중국의 고객에게 제공됩니다. Azure AD Premium 및 Basic 버전은 현재 중국 21Vianet이 운영하는 Azure 서비스에서 지원되지 않습니다. 자세한 내용은 [Azure AD 포럼](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789)을 사용하여 문의하세요.

## <a name="configure-kmsi"></a>KMSI 구성

1. 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure Active Directory**, **회사 브랜딩**, **구성** 을 차례로 선택합니다.
1. **고급 설정** 섹션에서 **로그인 상태 유지 옵션 표시** 설정을 찾습니다.

   이 설정을 통해 사용자가 명시적으로 로그아웃할 때까지 Azure AD에 로그인 상태를 유지할지 여부를 선택할 수 있습니다.
   * **아니요** 를 선택하면 사용자가 로그인에 성공한 후 **로그인 상태를 유지하시겠습니까?** 옵션이 숨겨지고, 브라우저를 닫았다가 다시 열 때마다 사용자가 로그인해야 합니다.
   * **예** 를 선택하면 **로그인 상태를 유지하시겠습니까?** 옵션이 사용자에게 표시됩니다.

    :::image type="content" source="./media/keep-me-signed-in/kmsi-company-branding-advanced-settings-kmsi-1.png" alt-text="로그인 상태 유지 옵션 표시 설정 스크린샷":::

## <a name="troubleshoot-sign-in-issues"></a>로그인 문제 해결

사용자가 **로그인 상태를 유지하시겠습니까?** 프롬프트에서 다음 다이어그램과 같이 작업하지 않고 로그인 시도를 중단하면 인터럽트를 나타내는 로그인 로그 항목이 표시됩니다.

:::image type="content" source="./media/keep-me-signed-in/kmsi-stay-signed-in-prompt.png" alt-text="로그인 상태를 유지하시겠습니까? 프롬프트 표시":::

로그인 오류에 대한 자세한 내용은 다음과 같으며 예제에서 강조 표시되어 있습니다.

* **로그인 오류 코드**: 50140
* **실패 이유**: 사용자가 로그인할 때 “로그인 상태 유지” 인터럽트로 인해 이 오류가 발생했습니다.

:::image type="content" source="./media/keep-me-signed-in/kmsi-sign-ins-log-entry.png" alt-text="로그인 상태 유지 인터럽트가 포함된 예제 로그인 로그 항목":::

고급 브랜딩 설정에서 **로그인 상태 유지 옵션 표시** 설정을 **아니요** 로 설정하여 사용자에게 인터럽트가 표시되지 않도록 할 수 있습니다. 이렇게 하면 Azure AD 디렉터리의 모든 사용자에 대해 KMSI 프롬프트가 사용되지 않습니다.

조건부 액세스에서 영구적 브라우저 세션 제어를 사용하여 사용자에게 KMSI 프롬프트가 표시되지 않도록 할 수도 있습니다. 이 옵션을 사용하면 디렉터리에 있는 나머지 사용자의 로그인 동작에 영향을 주지 않고 선택한 사용자 그룹(예: 전역 관리자)에 대해 KMSI 프롬프트를 사용하지 않도록 설정할 수 있습니다. 자세한 내용은 [사용자 로그인 빈도](../conditional-access/howto-conditional-access-session-lifetime.md)를 참조하세요. 

사용자에게 유용한 경우에만 KMSI 프롬프트가 표시되도록 하기 위해 다음 시나리오에서는 KMSI 프롬프트가 의도적으로 표시되지 않습니다.

* 사용자가 원활한 SSO 및 IWA(Windows 통합 인증)를 통해 로그인됨
* 사용자가 Active Directory Federation Services 및 IWA를 통해 로그인됨
* 사용자는 테넌트의 게스트임
* 사용자의 위험 점수가 높음
* 사용자 또는 관리자 동의 흐름 중에 로그인이 발생함
* 조건부 액세스 정책에서 영구적 브라우저 세션 제어가 구성됨

## <a name="next-steps"></a>다음 단계

로그인 세션 시간 제한에 영향을 주는 기타 설정에 대해 알아봅니다.

* Microsoft 365 - [유휴 세션 시간 제한](/sharepoint/sign-out-inactive-users)
* Azure AD 조건부 액세스 - [사용자 로그인 빈도](../conditional-access/howto-conditional-access-session-lifetime.md)
* Azure Portal – [디렉터리 수준 비활성 시간 제한](../../azure-portal/set-preferences.md#change-the-directory-timeout-setting-admin)