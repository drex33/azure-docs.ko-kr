---
title: 하이브리드 조직에 대한 B2B Collaboration - Azure AD
description: Azure AD B2B 협업을 사용하여 온-프레미스 및 클라우드 리소스에 파트너 액세스 권한을 부여합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12f41bc6369ab399ccce83d6c694003175a85fa7
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129229566"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>하이브리드 조직에 대한 Azure Active Directory B2B 협업

Azure AD(Azure Active Directory) B2B 협업을 사용하면 외부 파트너에게 조직의 앱과 리소스에 액세스할 수 있는 권한을 쉽게 부여할 수 있습니다. 이는 온-프레미스와 클라우드 기반 리소스가 모두 있는 하이브리드 구성에도 적용됩니다. Azure AD B2B 사용자로 클라우드의 외부 계정을 관리하는 경우이든 아니면 온-프레미스 ID 시스템에서 로컬로 외부 파트너 계정을 관리하는 경우든 상관 없습니다. 이제 두 환경 모두에 동일한 자격 증명을 사용하여 이러한 사용자가 모든 위치의 리소스에 액세스할 수 있도록 권한을 부여할 수 있습니다.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>Azure AD의 B2B 사용자에게 온-프레미스 앱에 대한 액세스 권한 부여

조직에서 Azure AD B2B 협업 기능을 사용하여 파트너 조직의 게스트 사용자를 Azure AD로 초대하는 경우 이제 이러한 B2B 사용자에게 온-프레미스 앱에 대한 액세스를 제공할 수 있습니다.

SAML 기반 인증을 사용하는 앱의 경우 인증에 Azure AD 애플리케이션 프록시를 사용하여 Azure Portal을 통해 B2B 사용자들이 이러한 앱을 사용하도록 만들 수 있습니다.

KCD(Kerberos 제한 위임)과 함께 IWA(Windows 통합 인증)를 사용하는 앱의 경우 인증에 Azure AD 프록시를 사용합니다. 단, 작업할 권한 부여의 경우 사용자 개체가 온-프레미스 Windows Server Active Directory에 있어야 합니다. B2B 게스트 사용자를 나타내는 로컬 사용자 개체를 만드는 데 사용할 수 있는 두 가지 방법이 있습니다.

- Microsoft Graph용 MIM 관리 에이전트 및 MIM(Microsoft Identity Manager) 2016 SP1을 사용할 수 있습니다.
- PowerShell 스크립트를 사용할 수 있습니다. (이 솔루션에는 MIM이 필요하지 않습니다.)

이러한 솔루션을 구현하는 방법에 대한 자세한 내용은 [Azure AD의 B2B 사용자에게 온-프레미스 애플리케이션에 대한 액세스 권한 부여](hybrid-cloud-to-on-premises.md)를 참조하세요.

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>로컬로 관리되는 파트너 계정에 클라우드 리소스에 대한 액세스 권한 부여

Azure AD 이전에는 온-프레미스 ID 시스템이 있는 조직은 일반적으로 온-프레미스 디렉터리에서 파트너 계정을 관리해왔습니다. 그러한 조직이라면 앱 및 다른 리소스를 클라우드로 이동해도 파트너가 계속 액세스 권한을 유지하길 원합니다. 이상적으로는 이러한 사용자가 동일한 자격 증명 집합을 사용하여 클라우드와 온-프레미스 리소스 모두에 액세스하도록 하려는 것입니다. 

이제 사용자가 Azure AD Connect를 사용하여 “게스트 사용자”로서 클라우드에 이러한 로컬 계정을 동기화할 수 있는 방법을 제공합니다. 여기서 계정은 Azure AD B2B 사용자와 동일한 동작을 수행합니다.

회사 데이터 보호를 용이하게 하기 위해 올바른 리소스에 대한 액세스를 제어하고, 이러한 게스트 사용자를 직원과는 다르게 처리하는 권한 부여 정책을 구성할 수 있습니다.

구현 세부 정보는 [Azure AD B2B 협업을 사용하여 로컬로 관리되는 파트너 계정에게 클라우드 리소스에 대한 액세스 권한 부여](hybrid-on-premises-to-cloud.md)를 참조하세요.
 
## <a name="next-steps"></a>다음 단계

- [Azure AD의 B2B 사용자에게 온-프레미스 애플리케이션에 대한 액세스 권한 부여](hybrid-cloud-to-on-premises.md)
- [Azure AD B2B 협업을 사용하여 로컬로 관리되는 파트너 계정에게 클라우드 리소스에 대한 액세스 권한 부여](hybrid-on-premises-to-cloud.md)


