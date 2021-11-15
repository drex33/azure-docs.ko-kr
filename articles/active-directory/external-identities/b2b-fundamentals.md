---
title: Azure Active Directory B2B 모범 사례 및 권장 사항
description: Azure Active Directory에서 B2B(Business to Business) 게스트 사용자 액세스에 대한 모범 사례 및 권장 사항을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5f819cbffc04d79ef053d89af3ea865d3dd244c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251113"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Azure Active Directory B2B 모범 사례
이 문서에는 Azure AD(Azure Active Directory)에서 B2B(Business to Business) 공동 작업을 위한 권장 사항 및 모범 사례가 포함되어 있습니다.

> [!IMPORTANT]
> **2021년 11월 1일부터** 모든 기존 테넌트용 이메일 일회용 암호 기능을 켜고 새 테넌트용 기능을 기본적으로 사용하도록 설정하는 변경 내용의 배포를 시작합니다. 이러한 변경의 일환으로 Microsoft는 B2B Collaboration 초대를 사용하는 동안에는 새로운, 관리되지 않는(“바이럴”) Azure AD 계정 및 테넌트 만들기를 중지합니다. 휴일 및 배포 잠금 동안 중단을 최소화하기 위해 대부분의 테넌트에는 2022년 1월에 롤아웃되는 변경 사항이 표시됩니다. 게스트 사용자에게 원활한 대체 인증 방법을 제공하므로 이메일 일회용 암호 기능을 사용하고 있습니다. 하지만, 이 기능을 원하지 않는다면 [사용하지 않도록 설정](one-time-passcode.md#disable-email-one-time-passcode)할 수 있습니다.


## <a name="b2b-recommendations"></a>B2B 권장 사항
| 권장 | 주석 |
| --- | --- |
| 최적의 로그인 환경을 위해 ID 공급자와 페더레이션 | 가능하다면 언제나 ID 공급자와 직접 페더레이션하여 초대된 사용자가 MSA(Microsoft 계정) 또는 Azure AD 계정을 만들 필요 없이 공유 앱 및 리소스에 로그인하도록 허용할 수 있습니다. [Google 페더레이션 기능](google-federation.md)을 사용하여 B2B 게스트 사용자가 Google 계정으로 로그인할 수 있습니다. 또는 [SAML/WS-Fed ID 공급자(미리 보기) 기능](direct-federation.md)을 사용하여 IdP(ID 공급자)가 SAML 2.0 또는 WS-Fed 프로토콜을 지원하는 모든 조직과의 페더레이션을 설정할 수 있습니다. |
| 다른 방법으로 인증할 수 없는 B2B 게스트에 대해 이메일 일회용 암호 기능 사용 | [이메일 일회용 암호](one-time-passcode.md) 기능은 B2B 게스트 사용자가 Azure AD, MSA(Microsoft 계정) 또는 Google 페더레이션과 같은 다른 수단을 통해 인증할 수 없을 때 해당 사용자를 인증합니다. 게스트 사용자는 초대를 사용하거나 공유 리소스에 액세스할 때 메일 주소로 전송되는 임시 코드를 요청할 수 있습니다. 그런 다음, 이 코드를 입력하여 로그인을 계속합니다. |
| 로그인 페이지에 회사 브랜딩 추가 | B2B 게스트 사용자를 위해 보다 직관적인 로그인 페이지를 사용자 지정할 수 있습니다. [로그인 및 액세스 패널 페이지에 회사 브랜딩을 추가](../fundamentals/customize-branding.md)하는 방법을 참조하세요. |
| B2B 게스트 사용자 상환 환경에 개인 정보 취급 방침 추가 | 초대된 사용자가 계속하려면 개인 정보 취급 방침에 동의해야만 하도록 조직의 개인 정보 취급 방침에 대한 URL을 첫 초대 상환 프로세스에 추가할 수 있습니다. [방법: Azure Active Directory에 조직의 개인 정보 추가](../fundamentals/active-directory-properties-area.md)를 참조하세요. |
| 대량 초대(미리 보기) 기능을 사용하여 여러 B2B 게스트 사용자를 동시에 초대 | Azure Portal에서 대량 초대 미리 보기 기능을 사용하여 동시에 여러 게스트 사용자를 조직에 초대합니다. 이 기능을 사용하여 CSV 파일을 업로드하여 B2B 게스트 사용자를 만들고 대량 초대를 보낼 수 있습니다. [B2B 사용자를 대량 초대하는 방법에 대한 자습서](tutorial-bulk-invite.md)를 참조하세요. |
| Azure Active Directory MFA(Multi-Factor Authentication)에 대한 조건부 액세스 정책 적용 | 파트너 B2B 사용자와 공유하려는 앱에 MFA 정책을 적용하는 것이 좋습니다. 이러한 방식으로 MFA는 파트너 조직에서 MFA를 사용하는지 여부에 관계없이 테넌트의 앱에 일관되게 적용됩니다. [B2B 협업 사용자에 대한 조건부 액세스](conditional-access.md)를 참조하세요. |
| 디바이스 기반 조건부 액세스 정책을 적용하는 경우에는 제외 목록을 사용하여 B2B 사용자에 대한 액세스를 허용합니다. | 조직에서 디바이스 기반 조건부 액세스 정책을 사용하도록 설정하는 경우 B2B 게스트 사용자 디바이스는 조직에서 관리되지 않으므로 차단됩니다. 디바이스 기반 조건부 액세스 정책에서 제외할 특정 파트너 사용자가 포함된 제외 목록을 만들 수 있습니다. [B2B 협업 사용자에 대한 조건부 액세스](conditional-access.md)를 참조하세요. |
| B2B 게스트 사용자에게 직접 링크를 제공하는 경우 테넌트별 URL 사용 | 초대 이메일 대신 게스트에게 앱 또는 포털의 직접 링크를 제공할 수도 있습니다. 직접 링크는 테넌트별로 달라야 하므로 공유 앱이 있는 테넌트에서 게스트를 인증할 수 있도록 테넌트 ID 또는 확인된 도메인이 포함되어야 합니다. [게스트 사용자를 위한 상환 환경](redemption-experience.md)을 참조하세요. |
| 앱을 개발할 때 UserType을 사용하여 게스트 사용자 환경을 확인합니다.  | 애플리케이션을 개발 중이고 테넌트 사용자와 게스트 사용자에게 다른 환경을 제공하려면 UserType 속성을 사용합니다. UserType 클레임은 현재 토큰에 포함되지 않습니다. 애플리케이션은 Microsoft Graph API를 사용하여 사용자가 자신의 UserType을 얻기 위해 디렉터리를 쿼리해야 합니다. |
| 조직과의 사용자 관계가 변경되는 *경우에만* UserType 속성을 변경합니다. | PowerShell을 사용하여 사용자의 UserType 속성을 Member에서 Guest로 변환하는 것이 가능하지만(그 반대의 경우도 해당) 사용자의 조직에 대한 사용자의 관계가 변경되는 경우에만 이 속성을 변경해야 합니다. [B2B 게스트 사용자의 속성](user-properties.md)을 참조하세요.|

## <a name="next-steps"></a>다음 단계

[B2B 공유 관리](delegate-invitations.md)