---
title: Azure AD의 MSA(Microsoft 계정) ID 공급자
description: Azure AD를 사용하여 외부 사용자(게스트)가 자신의 MSA(Microsoft 계정)를 통하여 내 Azure AD 앱에 로그인할 수 있도록 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/09/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2333ba6aec10d124d960dfc746bc9df2c449f3bb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529791"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities"></a>외부 ID에 대한 MSA(Microsoft 계정) ID 공급자

B2B 게스트 사용자는 자신들의 B2B 협업용 개인 Microsoft 계정을 추가 구성 없이 사용할 수 있습니다. 게스트 사용자는 내 B2B 협업 초대를 사용하거나 개인 Microsoft 계정을 사용하여 등록 사용자 흐름을 완료할 수 있습니다.

Microsoft 계정은 Outlook이나 OneDrive, Xbox LIVE 또는 Microsoft 365와 같은 소비자 중심적 Microsoft 제품 및 클라우드 서비스에 액세스하기 위하여 사용자가 설정합니다. 이 계정은 Microsoft에서 실행하는 Microsoft 소비자 ID 계정 시스템에서 생성되어 저장됩니다.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Microsoft 계정을 사용한 게스트 로그인

Microsoft 계정은 기본적으로 외부 ID의 ID 공급자 목록에서 사용할 수 있습니다. 초대 흐름이나 셀프 서비스 등록 사용자 흐름을 사용하여 게스트 사용자가 Microsoft 계정에 로그인하기 위하여 추가로 구성할 필요가 없습니다.

![ID 공급자 목록의 Microsoft 계정](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>초대 흐름의 Microsoft 계정

B2B 협업을 위하여 [게스트 사용자를 초대](add-users-administrator.md)하는 경우, 이들의 Microsoft 계정을 이들이 로그인할 때 사용할 이메일 주소로 지정할 수 있습니다.

![Microsoft 계정을 통하여 초대하기](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>셀프 서비스 등록 사용자 흐름의 Microsoft 계정

Microsoft 계정은 셀프 서비스 등록 사용자 흐름에 대한 ID 공급자 옵션 중 하나입니다. 사용자는 자신의 Microsoft 계정을 사용하여 내 애플리케이션에 등록할 수 있습니다. 먼저 테넌트에 대하여 [셀프 서비스 등록을 사용하도록 설정](self-service-sign-up-user-flow.md)하여야 합니다. 그런 다음 해당 애플리케이션을 위한 사용자 흐름을 설정하고 Microsoft 계정을 로그인 옵션 가운데 하나로 선택합니다.

![셀프 서비스 등록 사용자 흐름의 Microsoft 계정](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="verifying-the-applications-publisher-domain"></a>애플리케이션의 게시자 도메인 확인
2020년 11월부터 [애플리케이션의 게시자 도메인이 확인](../develop/howto-configure-publisher-domain.md)되고 회사 ID가 Microsoft 파트너 네트워크로 확인되고 애플리케이션과 연결되지 않는 한 새 애플리케이션 등록이 사용자 동의 프롬프트에 확인되지 않은 것으로 표시됩니다. (이 변경 사항에 대해 [자세히 알아보기](../develop/publisher-verification-overview.md)) Azure AD 사용자 흐름의 경우 게시자 도메인은 [Microsoft 계정](azure-ad-account.md) 또는 기타 Azure AD 테넌트를 ID 공급자로 사용할 때만 나타납니다. 이러한 새로운 요구 사항을 충족하려면 다음을 수행합니다.

1. [MPN(Microsoft 파트너 네트워크) 계정을 사용하여 회사 ID를 확인](/partner-center/verification-responses)합니다. 이 프로세스는 회사 및 회사의 기본 연락처에 대한 정보를 확인합니다.
1. 다음 옵션 중 하나를 사용하여 MPN 계정을 앱 등록과 연결하려면 게시자 확인 프로세스를 완료합니다.
   - Microsoft 계정 ID 공급자에 대한 앱 등록이 Azure AD 테넌트에 있는 경우 [앱 등록 포털에서 앱을 확인](../develop/mark-app-as-publisher-verified.md)합니다.
   - Microsoft 계정 ID 공급자에 대한 앱 등록이 Azure AD B2C 테넌트에 있는 경우 [Microsoft Graph API를 사용하여 앱을 게시자 확인으로 표시](../develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)합니다(예: Graph Explorer 사용).

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory B2B 협업 사용자 추가](add-users-administrator.md)
- [앱에 셀프 서비스 등록 추가](self-service-sign-up-user-flow.md)