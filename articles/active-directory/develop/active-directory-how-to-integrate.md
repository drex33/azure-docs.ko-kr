---
title: Microsoft ID 플랫폼과 통합하는 방법 | Azure
titleSuffix: Microsoft identity platform
description: 애플리케이션을 Microsoft ID 플랫폼과 통합하는 이점에 대해 알아보고 단순화된 로그인, ID 관리, 다단계 인증 및 액세스 제어와 같은 기능에 대한 리소스를 가져옵니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/01/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: 57aefd8cc8a246d485854f72b47c131f75dced0c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734819"
---
# <a name="integrating-with-the-microsoft-identity-platform"></a>Microsoft ID 플랫폼과 통합

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

이 문서에서는 애플리케이션을 Microsoft ID 플랫폼과 통합할 때의 이점에 대해 알아보고 통합을 위한 리소스를 가져옵니다. Microsoft ID 플랫폼 및 Azure AD(Active Directory)는 조직에 클라우드 애플리케이션을 위한 엔터프라이즈급 ID 관리 기능을 제공합니다. Microsoft ID 플랫폼을 통합하여 사용자에게 효율적인 로그인 환경을 제공하고 애플리케이션이 IT 정책을 준수할 수 있습니다.

## <a name="how-to-integrate"></a>통합 방법

여러 가지 방법으로 애플리케이션을 Microsoft ID 플랫폼과 통합할 수 있습니다. 애플리케이션에 맞게 이 시나리오를 활용하세요.

### <a name="support-the-microsoft-identity-platform-as-a-way-to-sign-in-to-your-application"></a>Microsoft ID 플랫폼을 통해 애플리케이션에 로그인할 수 있도록 지원

**로그인 충돌을 줄이고 지원 비용을 절감합니다.** Microsoft ID 플랫폼을 사용하여 애플리케이션에 로그인하면 사용자는 이름 및 암호를 하나 더 기억할 필요가 없습니다. 개발자는 저장하고 보호할 암호가 줄어듭니다. 잊어버린 암호 재설정을 처리하지 않으므로 상당히 효율적입니다. Microsoft ID 플랫폼에서는 Microsoft 365, Microsoft Azure 등과 같이 전 세계에서 인기가 많은 다양한 클라우드 애플리케이션에 로그인할 수 있도록 지원합니다. 약 500만 개의 조직에서 4억 3천만 명 이상이 사용 중이므로 사용자가 이미 Microsoft ID 플랫폼에 로그인되어 있을 가능성이 높습니다. [Microsoft ID 플랫폼 로그인에 대한 지원을 추가하는 방법](./authentication-vs-authorization.md)에 대해 자세히 알아봅니다.

**애플리케이션 등록을 단순화합니다.**  Microsoft ID 플랫폼은 애플리케이션을 등록하는 동안 등록 양식을 사전에 입력하거나 이 단계를 완전히 제거할 수 있도록 사용자에 대한 필수 정보를 보낼 수 있습니다. 사용자는 소셜 미디어 및 모바일 애플리케이션에서 본 것과 비슷한 친숙한 동의 환경을 통해 자신의 Azure AD 계정을 사용하여 애플리케이션을 등록할 수 있습니다. 모든 사용자는 IT 담당자의 도움 없이 Microsoft ID 플랫폼과 통합된 애플리케이션을 등록 및 로그인할 수 있습니다. [Azure AD 계정 로그인을 위해 애플리케이션 등록](../../app-service/configure-authentication-provider-aad.md)에 대해 자세히 알아보세요.

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>사용자 검색, 사용자 프로비저닝 관리, 애플리케이션에 대한 액세스 제어

**디렉터리에서 사용자를 찾습니다.**  다른 사람을 초대하거나 액세스 권한을 부여할 때 사용자가 메일 주소를 입력할 필요 없이 Microsoft Graph API를 사용하면 조직에서 다른 사람을 검색하고 찾는 데 도움이 됩니다. 사용자는 조직 계층의 세부 정보 보기 등 친숙한 주소록 유형의 인터페이스를 사용하여 찾아볼 수 있습니다. [Microsoft Graph API](/graph/overview)에 대해 알아보세요.

**Active Directory 그룹 및 고객이 이미 관리하는 메일 그룹을 다시 사용합니다.**   Azure AD에는 고객이 이미 메일 배포에 사용하고 액세스를 관리하는 그룹이 포함되어 있습니다. 고객이 애플리케이션에서 별도의 그룹 집합을 만들고 관리할 필요 없이 Microsoft Graph API를 사용하여 그룹을 다시 사용하세요. 로그인 토큰의 애플리케이션에 그룹 정보가 전송될 수도 있습니다. [Microsoft Graph API](/graph/overview)에 대해 알아보세요.

**Microsoft ID 플랫폼을 사용하여 애플리케이션에 대한 액세스 권한이 있는 사용자를 제어합니다.**  Azure AD의 관리자 및 애플리케이션 소유자는 애플리케이션에 대한 액세스 권한을 특정 사용자 및 그룹에게 할당할 수 있습니다. Microsoft Graph API를 사용하여 이 목록을 읽고 리소스의 프로비저닝 및 프로비저닝 해제와 애플리케이션 내 액세스 제어에 사용할 수 있습니다.

**역할 기반 액세스 제어 Microsoft ID 플랫폼을 사용합니다.**  관리자 및 애플리케이션 소유자는 Microsoft ID 플랫폼에서 애플리케이션을 등록할 때 정의한 역할에 사용자 및 그룹을 할당할 수 있습니다. 역할 정보는 로그인 토큰의 애플리케이션에 전송되며 Microsoft Graph API를 사용해서도 읽을 수 있습니다. [권한 부여를 위해 Microsoft ID 플랫폼을 사용하는 방법](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/)에 대해 자세히 알아봅니다.

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>사용자의 프로필, 일정, 이메일, 연락처, 파일 등에 대한 액세스 권한 얻기

**Microsoft ID 플랫폼은 Microsoft 365 및 기타 Microsoft 비즈니스 서비스에 대한 권한 부여 서버입니다.**  애플리케이션 로그인에 Microsoft ID 플랫폼을 지원하거나 OAuth 2.0을 사용하는 Azure AD 사용자 계정에 현재 사용자 계정 연결을 지원하는 경우 사용자의 프로필, 일정, 이메일, 연락처, 파일 및 기타 정보에 대한 읽기 및 쓰기 권한을 요청할 수 있습니다. 사용자의 일정에 매끄럽게 이벤트를 쓸 수 있으며 사용자의 OneDrive에 파일을 읽거나 쓸 수 있습니다. [Microsoft 365 API](/graph/overview)에 대한 자세한 정보

### <a name="promote-your-application-in-the-azure-and-microsoft-365-marketplaces"></a>Azure 및 Microsoft 365 Marketplace에서 애플리케이션 홍보

**이미 Azure AD를 사용 중인 수백만 개의 조직에 애플리케이션을 홍보합니다.**   이러한 마켓플레이스를 검색하고 찾아보는 사용자는 자격 있는 클라우드 서비스 고객이 되어 이미 하나 이상의 클라우드 서비스를 사용하고 있습니다. [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/)에서 애플리케이션 홍보하기에 대해 자세히 알아보세요.

**사용자가 애플리케이션에 등록하면 사용자의 Azure AD 액세스 패널 및 Microsoft 365 앱 시작 관리자에 표시됩니다.**  사용자가 나중에 애플리케이션으로 쉽고 빠르게 돌아갈 수 있어 사용자 참여를 향상시킵니다. [Azure AD 액세스 패널](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)에 대해 자세히 알아보세요.

### <a name="secure-device-to-service-and-service-to-service-communication"></a>디바이스와 서비스 및 서비스와 서비스 간의 안전한 통신

**서비스 및 디바이스의 ID 관리에 Microsoft ID 플랫폼을 사용하면 작성할 코드를 줄일 수 있으며 IT 담당자가 액세스를 관리할 수 있습니다.**  서비스 및 디바이스는 OAuth를 사용하여 Microsoft ID 플랫폼에서 토큰을 가져올 수 있으며 이 토큰으로 웹 API에 액세스할 수 있습니다. Microsoft ID 플랫폼을 사용하면 복잡한 인증 코드를 작성하지 않아도 됩니다. 서비스 및 디바이스의 ID가 Azure AD에 저장되므로 IT 담당자는 키 및 해지를 애플리케이션에서 별도로 수행하지 않고도 한 곳에서 관리할 수 있습니다.

## <a name="benefits-of-integration"></a>통합의 이점

Microsoft ID 플랫폼과 통합하면 추가 코드를 작성할 필요가 없다는 이점이 있습니다.

### <a name="integration-with-enterprise-identity-management"></a>엔터프라이즈 ID 관리와의 통합

**애플리케이션이 IT 정책을 준수하도록 합니다.**  조직에서는 Microsoft ID 플랫폼과 조직의 엔터프라이즈 ID 관리 시스템을 통합하므로 조직을 떠날 경우 IT 담당자가 추가 단계를 수행할 필요 없이 자동으로 애플리케이션에 대한 액세스 권한이 사라집니다. IT 담당자는 애플리케이션에 액세스할 수 있는 사람을 관리하고 필요한 액세스 정책(예: 다단계 인증)을 결정할 수 있어 복잡한 회사 정책을 준수하기 위해 작성해야 하는 코드가 줄어듭니다. Azure AD는 애플리케이션에 로그인한 사람의 세부적인 감사 로그를 관리자에게 제공하므로 IT 담당자가 사용 현황을 추적할 수 있습니다.

**Azure AD는 애플리케이션을 AD와 통합할 수 있도록 Active Directory를 클라우드로 확장합니다.**  전 세계 많은 조직에서 Active Directory를 기본 로그인 및 ID 관리 시스템으로 사용하며 애플리케이션에 AD와 함께 작업하도록 요구합니다. Azure AD와 통합하면 응용 프로그램이 Active Directory와 통합됩니다.

### <a name="advanced-security-features"></a>고급 보안 기능

**다단계 인증.**  Microsoft ID 플랫폼은 네이티브 다단계 인증을 제공합니다. 애플리케이션에 액세스하기 위해 IT 관리자가 다단계 인증을 요구할 수 있으므로 이 지원을 직접 코딩할 필요가 없습니다. [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)에 대해 자세히 알아보세요.

**비정상적인 로그인 감지.**  Microsoft ID 플랫폼은 의심스러운 활동을 감지하고 발생 가능한 문제를 IT 관리자에게 알리는 기계 학습 알고리즘을 사용하면서 하루 10억 개가 넘는 로그인을 처리합니다. Microsoft ID 플랫폼을 통한 로그인을 지원함으로써 애플리케이션은 이러한 보호를 받습니다. [Azure Active Directory 액세스 보고서 보기](../reports-monitoring/overview-reports.md)에 대해 자세히 알아보세요.

**조건부 액세스**  관리자는 다단계 인증 외에도 특정 조건을 충족해야 사용자가 애플리케이션에 로그인할 수 있도록 요구할 수 있습니다. 클라이언트 디바이스의 IP 주소 범위, 지정된 그룹의 멤버 자격, 액세스에 사용되는 디바이스의 상태 조건을 설정할 수 있습니다. [Azure Active Directory 조건부 액세스](../conditional-access/overview.md)에 대해 자세히 알아보세요.

### <a name="easy-development"></a>손쉬운 배포

**산업 표준 프로토콜.**   Microsoft는 산업 표준을 지원합니다. Microsoft ID 플랫폼은 업계 표준 OAuth 2.0 및 OpenID Connect 1.0 프로토콜을 지원합니다. [Microsoft ID 플랫폼 인증 프로토콜](active-directory-v2-protocols.md)에 대해 자세히 알아봅니다.

**오픈 소스 라이브러리.**   Microsoft는 인기 있는 언어 및 플랫폼에 대해 개발에 박차를 가하기 위해 완벽하게 지원되는 오픈 소스 라이브러리를 제공합니다. 소스 코드는 Apache 2.0에 따라 사용이 허가되며, 원하는 대로 소스 코드를 분기하여 프로젝트에 다시 제공할 수 있습니다. [MSAL(Microsoft 인증 라이브러리)](reference-v2-libraries.md)에 대해 자세히 알아보세요.

### <a name="worldwide-presence-and-high-availability"></a>전 세계 제공 및 고가용성

**Azure AD는 전 세계 데이터 센터에 배포되어 24시간 내내 관리 및 모니터링됩니다.**  Azure AD는 Microsoft Azure 및 Microsoft 365의 ID 관리 시스템이며 전 세계 28곳의 데이터 센터에 배포됩니다. 디렉터리 데이터는 3개 이상의 데이터 센터에 복제되어야 합니다. 전역 부하 분산 장치는 사용자가 자신의 데이터가 들어 있는 Azure AD의 가장 근접한 복사본에 액세스하도록 하며 문제가 감지되면 요청을 다른 데이터 센터로 자동으로 다시 라우팅합니다.

## <a name="next-steps"></a>다음 단계

[코드 작성 시작하기](v2-overview.md#getting-started).

[Microsoft ID 플랫폼을 사용하여 사용자 로그인](./authentication-vs-authorization.md)