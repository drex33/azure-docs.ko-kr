---
title: Azure Virtual Desktop에서 관리 ID 설정 - Azure
description: Azure AD, Azure AD DS 또는 AD DS를 사용하여 Azure Virtual Desktop에서 고객에 대한 관리 ID를 설정하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 98dcd68e62f04eaf9ae439389cb31e281cdb2f69
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535591"
---
# <a name="set-up-managed-identities"></a>관리 ID 설정

Azure Virtual Desktop은 현재 외부 프로필 또는 "ID"를 지원하지 않으므로 사용자는 자신의 회사 자격 증명을 사용하여 호스트하는 앱에 액세스할 수 없습니다. 대신, Active Directory Domain에서 원격 앱 스트리밍에 사용할 ID를 만들고 사용자 개체를 연결된 Azure Active Directory(Azure AD) 테넌트와 동기화해야 합니다.

이 문서에서는 사용자 ID를 관리하여 고객에게 보안 환경을 제공하는 방법을 설명합니다. 또한 ID를 구성하는 여러 부분에 대해 설명합니다.

## <a name="requirements"></a>요구 사항

만든 ID는 다음 지침을 따라야 합니다.

- ID는 [Active Directory(AD)](/previous-versions/windows/it-pro/windows-server-2003/cc781408(v=ws.10))와 [Azure Active Directory(Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 모두에 존재하는 [하이브리드 ID](../../active-directory/hybrid/whatis-hybrid-identity.md)여야 합니다. [Active Directory Domain Services(AD DS)](/windows-server/identity/ad-ds/active-directory-domain-services) 또는 [Azure Active Directory Domain Services(Azure AD DS)](https://azure.microsoft.com/services/active-directory-ds)를 사용하여 이러한 ID를 만들 수 있습니다. 각 방법에 대해 자세히 알아보려면 [ID 솔루션 비교](../../active-directory-domain-services/compare-identity-solutions.md)를 참조하세요.
- 보안 위반을 방지하려면 서로 다른 조직의 사용자를 별도의 Azure AD 테넌트에 유지해야 합니다. 고객 조직당 하나의 Active Directory Domain 및 Azure Active Directory 테넌트를 만드는 것이 좋습니다. 해당 테넌트에는 해당 고객 전용으로 연결된 Azure AD DS 또는 AD DS 구독이 있어야 합니다.

다음 두 섹션에서는 AD DS 및 Azure AD DS를 사용하여 ID를 만드는 방법을 설명합니다. [조직 간 앱에 대한 보안 지침](security.md)을 따르려면 각 고객에 대해 이 프로세스를 반복해야 합니다.

## <a name="managing-users-with-active-directory-domain-services"></a>Active Directory Domain Services로 사용자 관리

이 방법에서는 Active Directory Domain Controller를 사용해 하이브리드 ID를 설정하여 사용자 ID를 관리하고 Azure AD와 동기화합니다.

이 방법에는 Active Directory Domain Controller를 설정하여 사용자 ID를 관리하고 사용자를 Azure AD와 동기화하여 하이브리드 ID를 만드는 작업이 포함됩니다. 그런 다음, 이러한 ID를 사용하여 Azure Virtual Desktop에서 호스트된 애플리케이션에 액세스할 수 있습니다. 이 구성에서는 사용자가 Active Directory에서 Azure AD로 동기화되고 세션 호스트 VM이 AD DS 도메인에 조인됩니다.

AD DS에서 ID를 설정하려면 다음을 수행합니다.

1. 고객을 위해 [Azure AD 테넌트](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)와 구독을 만듭니다.

2. 고객을 위해 사용 중인 Windows Server VM(가상 머신)에 [Active Directory Domain Services를 설치](/windows-server/identity/ad-ds/deploy/install-active-directory-domain-services--level-100-)합니다.

3. 별도의 도메인 조인 VM에 [Azure AD Connect](../../active-directory/hybrid/how-to-connect-install-roadmap.md)를 설치하고 구성하여 사용자 계정을 Active Directory에서 Azure Active Directory로 동기화합니다.

4. Intune을 사용하여 VM을 관리하려는 경우 Azure AD Connect를 사용하여 [하이브리드 Azure AD 조인 디바이스](../../active-directory/devices/hybrid-azuread-join-plan.md)를 사용하도록 설정합니다.

5. 환경을 구성했으면 Active Directory에서 [새 사용자를 만듭니다.](/previous-versions/windows/it-pro/windows-server-2003/cc755607(v=ws.10)) 이러한 사용자는 Azure AD와 자동으로 동기화되어야 합니다.

6. 호스트 풀에 세션 호스트를 배포할 때 Active Directory 도메인 이름을 사용하여 VM을 조인하고 세션 호스트가 도메인 컨트롤러를 볼 수 있어야 합니다.

이 구성에서는 환경을 보다 세부적으로 제어할 수 있지만 복잡성으로 인해 관리하기가 쉽지 않습니다. 그러나 이 옵션을 사용하면 사용자에게 Azure AD 기반 앱을 제공할 수 있습니다. Intune을 사용하여 사용자의 VM을 관리할 수도 있습니다.

## <a name="managing-users-with-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services로 사용자 관리

Azure AD DS ID는 Microsoft 관리형 Active Directory PaaS(platform as a service)에 저장됩니다. 여기서 Microsoft는 사용자가 Azure 구독 내에서 AD DS를 사용할 수 있도록 두 개의 AD 도메인 컨트롤러를 관리합니다. 이 구성에서는 사용자가 Azure AD에서 Azure AD DS로 동기화되고 세션 호스트가 Azure AD DS 도메인에 조인됩니다. Azure AD DS ID는 관리하기가 더 쉽지만 일반 AD DS ID 만큼 많은 제어를 제공하지는 않습니다. Azure Virtual Desktop VM을 Azure AD DS 도메인에만 조인할 수 있으며 Intune을 사용하여 관리할 수 없습니다.

Azure AD DS를 사용하여 ID를 만들려면 다음을 수행합니다.

1. 고객을 위해 [Azure AD 테넌트](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)와 구독을 만듭니다.

2. 사용자의 구독에 [Azure AD Directory Services를 배포](../../active-directory-domain-services/tutorial-create-instance.md)합니다.

3. 환경 구성을 마쳤으면 Azure Active Directory에서 [새 사용자를 만듭니다.](../../active-directory/fundamentals/add-users-azure-active-directory.md) 이러한 사용자 개체는 Azure AD DS와 자동으로 동기화됩니다.

4. 호스트 풀에 세션 호스트를 배포할 때 Azure AD DS 도메인 이름을 사용하여 VM을 조인합니다.

## <a name="next-steps"></a>다음 단계

ID 및 테넌트를 설정하기 위한 보안 고려 사항에 대해 자세히 알아보려면 [조직 간 앱에 대한 보안 지침](security.md)을 참조하세요.
