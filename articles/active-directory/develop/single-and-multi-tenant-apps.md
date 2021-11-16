---
title: Azure AD의 단일 및 다중 테넌트 앱
titleSuffix: Microsoft identity platform
description: Azure AD에서 단일 테넌트와 다중 테넌트 앱의 기능 및 차이점에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/13/2021
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 9a21d6f4a59709c74a02dff2334402510e156918
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129987730"
---
# <a name="tenancy-in-azure-active-directory"></a>Azure Active Directory의 테넌시

Azure AD(Azure Active Directory)에서는 사용자 및 앱과 같은 개체를 _테넌트_ 라는 그룹으로 구성합니다. 테넌트를 사용하면 관리자는 조직 및 조직이 소유한 앱 내에서 해당 보안 및 운영 정책을 충족하도록 사용자의 정책을 설정할 수 있습니다.

## <a name="who-can-sign-in-to-your-app"></a>앱에 로그인할 수 있는 사용자

앱 개발에 있어서 개발자는 [Azure Portal](https://portal.azure.com)에서 앱을 등록하는 동안 해당 앱을 단일 테넌트 또는 다중 테넌트로 구성할 수 있습니다.

- 단일 테넌트 앱은 홈 테넌트라고도 하는 등록된 테넌트에서만 사용할 수 있습니다.
- 다중 테넌트 앱은 해당 홈 테넌트와 기타 테넌트의 사용자에게 지원됩니다.

Azure Portal에서 대상 그룹을 다음과 같이 설정하여 앱을 단일 테넌트 또는 다중 테넌트로 구성할 수 있습니다.

| 사용자                                                                                              | 단일/다중 테넌트 | 로그인할 수 있는 사용자                                                                                                                                                                                                                                                                                                       |
| ----------------------------------------------------------------------------------------------------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 이 디렉터리의 계정에만 해당                                                                       | 단일 테넌트       | 디렉터리의 모든 사용자 및 게스트 계정은 사용자의 애플리케이션 또는 API를 사용할 수 있습니다.<br>_대상 그룹이 조직 내부자인 경우 이 옵션을 사용합니다._                                                                                                                                                         |
| Azure AD 디렉터리의 계정                                                                    | 다중 테넌트        | Microsoft에서 회사 또는 학교 계정을 사용하는 모든 사용자 및 게스트는 사용자의 애플리케이션 또는 API를 사용할 수 있습니다. 여기에는 Microsoft 365를 사용하는 학교 및 기업이 포함됩니다.<br>_대상 그룹이 비즈니스 또는 교육용 고객인 경우 이 옵션을 사용합니다._                                                                    |
| Azure AD 디렉터리의 계정 및 개인 Microsoft 계정(예: Skype, Xbox, Outlook.com) | 다중 테넌트        | 회사나 학교 계정 또는 개인 Microsoft 계정을 사용하는 모든 사용자는 사용자의 애플리케이션 또는 API를 사용할 수 있습니다. Skype 및 Xbox와 같은 서비스에 로그인하는 데 사용되는 개인 계정뿐만 아니라 Microsoft 365를 사용하는 학교 및 회사가 포함됩니다.<br>_다양한 Microsoft 계정을 대상으로 지정하려면 이 옵션을 사용합니다._ |

## <a name="best-practices-for-multi-tenant-apps"></a>다중 테넌트 앱에 대한 모범 사례

IT 관리자가 해당 테넌트에서 설정할 수 있는 다양한 정책 수로 인해 우수한 다중 테넌트 앱을 빌드하는 작업은 어려울 수 있습니다. 다중 테넌트 앱을 빌드하려는 경우 다음과 같은 모범 사례를 따르세요.

- 구성된 [조건부 액세스 정책](../azuread-dev/conditional-access-dev-guide.md)이 있는 테넌트에서 앱을 테스트합니다.
- 앱이 실제로 필요한 사용 권한만 요청하도록 하기 위해 최소 사용자 액세스 원칙을 따릅니다.
- 앱의 일부로 노출한 사용 권한에 대해 적절한 이름 및 설명을 입력합니다. 이렇게 하면 사용자 및 관리자가 앱의 API를 사용하려고 할 때 동의하는 내용을 알 수 있습니다. 자세한 내용은 [사용 권한 가이드](v2-permissions-and-consent.md)에서 모범 사례 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure AD의 태넌시에 대한 자세한 내용은 다음을 참조하세요.

- [앱을 다중 테넌트로 변환하는 방법](howto-convert-app-to-be-multi-tenant.md)
- [다중 테넌트 로그인 사용](howto-convert-app-to-be-multi-tenant.md)
