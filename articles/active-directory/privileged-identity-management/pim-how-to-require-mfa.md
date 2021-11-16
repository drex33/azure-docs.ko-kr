---
title: MFA 또는 2FA 및 Privileged Identity Management - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 MFA(다단계 인증)의 유효성을 검사하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e263de51c234c0572584911b3bd8f7d4eb7c487c
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667215"
---
# <a name="multifactor-authentication-and-privileged-identity-management"></a>다단계 인증 및 Privileged Identity Management

모든 관리자에 대해 다단계 인증(MFA 또는 2FA)을 요구하는 것이 좋습니다. 다단계 인증은 손상된 암호를 사용하는 공격의 위험을 줄여줍니다.

사용자가 로그인할 때 다단계 인증 챌린지를 완료하도록 요구할 수 있습니다. 사용자가 Azure AD(Active Directory) PIM(Privileged Identity Management)의 역할을 활성화하는 경우 다단계 인증 챌린지를 완료하도록 요구할 수도 있습니다. 이렇게 하면 사용자가 로그인할 때 다단계 인증을 완료하지 않은 경우에도 Privileged Identity Management에서 해당 인증을 수행하라는 메시지가 표시됩니다.

> [!IMPORTANT]
> 현재, Azure AD Multi-Factor Authentication은 회사 또는 학교 계정에서만 작동하며 Microsoft 개인 계정(일반적으로 Skype, Xbox 또는 Outlook.com과 같은 Microsoft 서비스에 로그인하는 데 사용되는 개인 계정)에서는 작동하지 않습니다. 이 때문에 개인 계정을 사용하는 사용자는 다단계 인증을 사용하여 해당 역할을 활성화할 수 없기 때문에 적격 관리자가 될 수 없습니다. 이 사용자들이 Microsoft 계정을 사용하여 계속 워크로드를 관리해야 하는 경우 영구 관리자로 승격하세요.

## <a name="how-pim-validates-mfa"></a>PIM이 MFA의 유효성을 검사하는 방법

사용자가 역할을 활성화할 때 다단계 인증의 유효성을 검사하는 두 가지 옵션이 있습니다.

가장 간단한 옵션은 권한 있는 역할을 활성화하는 사용자에 대해 Azure AD Multi-Factor Authentication을 사용하는 것입니다. 이렇게 하려면 먼저 해당 사용자에게 사용이 허가되었는지와 Azure AD Multi-Factor Authentication에 등록되었는지 확인합니다. Azure AD Multi-Factor Authentication을 배포하는 방법에 대한 자세한 내용은 [클라우드 기반 Azure AD Multi-Factor Authentication 배포](../authentication/howto-mfa-getstarted.md)를 참조하세요. 이러한 사용자가 로그인할 때 다단계 인증을 적용하도록 Azure AD를 구성하는 것이 좋지만 필수 사항은 아닙니다. 이는 Privileged Identity Management 자체에서 다단계 인증 확인을 수행하기 때문입니다.

또는 사용자가 온-프레미스를 인증하는 경우 다단계 인증에 대해 책임지는 ID 공급자를 사용할 수 있습니다. 예를 들어 Azure AD에 액세스하기 전에 스마트 카드 기반 인증을 요구하도록 AD Federation Services를 구성한 경우 [Azure Multi-Factor Authentication 및 AD FS를 사용하여 클라우드 리소스 보안](../authentication/howto-mfa-adfs.md)에 포함된 지침을 참조하여 Azure AD로 클레임을 보내도록 AD FS를 구성합니다. 사용자가 역할을 활성화하려고 하면 Privileged Identity Management는 적절한 클레임을 받으면 사용자에 대해 다단계 인증의 유효성이 이미 검사되었다는 것을 수락합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 역할 설정 구성](pim-how-to-change-default-settings.md)
- [Privileged Identity Management에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
