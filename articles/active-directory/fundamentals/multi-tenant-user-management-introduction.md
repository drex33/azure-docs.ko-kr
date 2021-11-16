---
title: Azure Active Directory에서 다중 테넌트 사용자 관리 구성
description: 게스트 계정을 사용하여 Azure Active Directory 테넌트에서 사용자 액세스를 구성하는 데 사용되는 다양한 패턴에 대해 알아봅니다.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/25/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 379135bc446dbef6a2145a6339b5ddd318a5f579
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130273590"
---
# <a name="multi-tenant-user-management"></a>다중 테넌트 사용자 관리 

사용자를 단일 Azure AD(Azure Active Directory) 테넌트에 프로비전하면 리소스에 대한 통합 보기와 단일 정책 및 제어 세트가 제공됩니다. 이 방법은 일관된 사용자 수명 주기 관리를 가능하게 합니다. 

**Microsoft는 가능한 경우 단일 테넌트를 권장합니다**. 그러나 단일 Azure AD 테넌트에 대한 즉각적인 통합이 항상 가능한 것은 아닙니다. 다중 테넌트 조직은 둘 이상의 Azure AD 테넌트에 걸쳐 있을 수 있습니다. 이로 인해 고유한 테넌트 간 협업 및 관리 요구 사항이 발생할 수 있습니다.

조직에는 다음과 같은 복잡한 IAM(ID 및 액세스 관리) 요구 사항이 있을 수 있습니다.

* 합병, 인수 및 매각

* 퍼블릭, 소버린 및/또는 지역 클라우드 전반의 협업

* 단일 Azure AD 테넌트로의 통합을 금지하는 정치적 또는 조직적 구조

또한 이 지침은 일관된 사용자 수명 주기 관리 상태를 달성하는 데 도움이 되는 지침을 제공합니다. 즉, Azure에서 사용할 수 있는 도구를 사용하여 사용자를 테넌트 간에 프로비전, 관리 및 프로비전 해제합니다. 특히 [Azure AD B2B 협업](../external-identities/what-is-b2b.md)을 사용합니다.

## <a name="azure-ad-b2b-collaboration"></a>Azure AD B2B 협업

Azure AD 협업을 통해 회사의 애플리케이션 및 서비스를 외부 게스트 사용자와 안전하게 공유할 수 있습니다. 사용자는 모든 조직에서 올 수 있습니다. Azure AD B2B 협업을 사용하면 IT 환경 및 데이터에 대한 액세스 제어를 유지하는 데 도움이 됩니다. 또한 Azure AD B2B 협업을 사용하여 내부 사용자에게 게스트 액세스를 제공할 수 있습니다. 일반적으로 B2B 게스트 사용자 액세스는 조직에서 관리하지 않는 외부 사용자에 대한 액세스 권한을 부여하는 데 사용됩니다. 그러나 게스트 사용자 액세스를 사용하여 조직에서 관리하는 여러 테넌트 간의 액세스를 관리할 수도 있습니다. 실제로 B2B 솔루션이 아니지만 Azure AD B2B 협업을 사용하여 다중 테넌트 시나리오에서 내부 사용자를 관리할 수 있습니다.

Azure AD B2B 협업에 대해 자세히 알아보기 위해 방문할 수 있는 다음 링크에서 추가 정보를 제공합니다.

| 아티클| 설명 |
| - |-|
| **개념 문서**|  |
| [B2B 모범 사례](../external-identities/b2b-fundamentals.md)| 사용자와 관리자를 위한 가장 원활한 환경에 대한 권장 사항입니다.|  
| [B2B 및 Office 365 외부 공유](../external-identities/o365-external-user.md)| B2B, Office 365 및 SharePoint/OneDrive를 통한 리소스 공유의 유사점과 차이점을 설명합니다.|  
| [Azure AD B2B 협업 사용자의 속성](../external-identities/user-properties.md)| Azure AD(Azure Active Directory)에서 B2B 게스트 사용자 개체의 속성 및 상태를 설명합니다. 설명은 초대 회수 전후에 대한 세부 정보를 제공합니다.|  
| [B2B 사용자 토큰](../external-identities/user-token.md)| B2B 및 B2B 게스트 사용자에 대한 전달자 토큰 예제를 제공합니다.|  
| [B2B에 대한 조건부 액세스](../external-identities/conditional-access.md)| 조건부 액세스 및 MFA가 게스트 사용자에 대해 작동하는 방법을 설명합니다.|  
| **방법 문서**|  |
| [PowerShell을 사용하여 Azure AD B2B 협업 사용자 일괄 초대](../external-identities/bulk-invite-powershell.md)| PowerShell을 사용하여 외부 사용자에게 대량 초대를 보내는 방법을 알아봅니다.|
| [B2B 게스트 사용자에 다단계 인증 적용](../external-identities/b2b-tutorial-require-mfa.md)|조건부 액세스 및 MFA 정책을 사용하여 테넌트, 앱 또는 개별 게스트 사용자 인증 수준을 적용합니다. |
| [이메일 일회용 암호 인증](../external-identities/one-time-passcode.md)| 메일 일회성 암호 기능은 B2B 게스트 사용자가 Azure AD, Microsoft 계정(MSA) 또는 Google 페더레이션과 같은 다른 수단을 통해 인증할 수 없을 때 해당 사용자를 인증합니다.|

## <a name="terminology"></a>용어

이 콘텐츠 전체에서 사용되는 용어는 다음과 같습니다.

* **리소스 테넌트**: 사용자가 다른 사용자와 공유하려는 리소스가 포함된 Azure AD 테넌트입니다.

* **홈 테넌트**: 리소스 테넌트의 리소스에 액세스해야 하는 사용자가 포함된 Azure AD 테넌트입니다.

* **사용자 수명 주기 관리**: 리소스에 대한 사용자 액세스를 프로비전, 관리 및 프로비전 해제하는 프로세스입니다.

* **통합 GAL**: 각 테넌트의 각 사용자에게 GAL(전체 주소 목록)에 있는 각 조직의 사용자가 표시됩니다.

## <a name="deciding-how-to-meet-your-requirements"></a>요구 사항을 충족하는 방법 결정

조직의 고유한 요구 사항에 따라 테넌트 간에 사용자를 관리하기 위한 전략이 결정됩니다. 효과적인 전략을 만들려면 다음을 고려해야 합니다.

* 테넌트 수

* 조직 유형

* 현재 토폴로지

* 특정 사용자 동기화 요구 사항 

### <a name="common-requirements"></a>일반적인 요구 사항

많은 조직에서는 초기에 즉각적인 협업을 위해 원하는 요구 사항에 중점을 둡니다. Day One 요구 사항이라고도 하는 이러한 요구 사항은 최종 사용자가 회사의 가치를 창출하는 기능을 중단하지 않고 원활하게 병합할 수 있도록 하는 데 집중합니다. Day One 및 관리 요구 사항을 정의할 때 다음 목표를 포함하는 것이 좋습니다. 

| 요구 사항 범주| 일반적인 요구 사항|
| ------------ | - |
| **통신 요구 사항**|  |
| 통합 전체 주소 목록| 각 사용자가 홈 테넌트에서 GAL에 있는 다른 모든 사용자를 볼 수 있습니다. |
| 약속 있음/없음 정보| 사용자가 서로의 가용성을 검색할 수 있도록 합니다. 이 작업은 [Exchange Online에서 조직 관계](/exchange/sharing/organization-relationships/create-an-organization-relationship)를 사용하여 수행할 수 있습니다.|
| 채팅 및 현재 상태| 사용자가 다른 사람의 현재 상태를 확인하고 인스턴트 메시징을 시작할 수 있도록 합니다. 이는 [Microsoft Teams에서 외부 액세스](/microsoftteams/manage-external-access)를 통해 구성할 수 있습니다.|
| 리소스(예: 회의실) 예약| 사용자가 조직 전체에서 회의실 또는 기타 리소스를 예약할 수 있도록 합니다. 현재는 테넌트 간 회의실 예약을 사용할 수 없습니다.|
‎단일 이메일 도메인| 모든 사용자가 단일 이메일 도메인(예: *users@contoso.com* )에서 메일을 보내고 받을 수 있도록 합니다. 현재는 타사 주소 재작성 솔루션이 메일을 보내는 데 필요합니다.|
| **액세스 요구 사항**|  |
| 문서 액세스| 사용자가 SharePoint, OneDrive 및 Teams에서 문서를 공유할 수 있도록 합니다. |
| 관리| 관리자가 여러 테넌트에 배포된 구독 및 서비스의 구성을 관리할 수 있도록 허용합니다. |
| 애플리케이션 액세스| 최종 사용자가 조직 전체의 애플리케이션에 액세스할 수 있도록 허용합니다. |
| Single Sign-On| 사용자가 더 많은 자격 증명을 입력할 필요 없이 조직 전체의 리소스에 액세스할 수 있도록 합니다.|

### <a name="patterns-for-account-creation"></a>계정 만들기 패턴 

게스트 사용자 계정의 수명 주기를 만들고 관리하는 데 사용할 수 있는 몇 가지 메커니즘이 있습니다. Microsoft는 세 가지 일반적인 패턴을 추출했습니다. 이러한 패턴을 사용하여 요구 사항을 정의하고 구현할 수 있습니다. 시나리오에 가장 적합한 패턴을 선택한 다음, 해당 패턴에 대한 세부 정보에 집중합니다.

| 메커니즘 |  설명 | 가장 적합한 경우 |
| - | - | - |
| [최종 사용자 시작](multi-tenant-user-management-scenarios.md#end-user-initiated-scenario) | 리소스 테넌트 관리자가 게스트 사용자를 테넌트, 앱 또는 리소스에 초대하는 기능을 테넌트 내의 사용자에게 위임합니다. 홈 테넌트의 사용자가 개별적으로 초대되거나 가입됩니다. |  <li>사용자에게 리소스에 대한 임시 액세스 권한이 필요합니다. <li>자동 사용자 특성 동기화가 필요하지 않습니다.<li>통합 GAL이 필요하지 않습니다. |
|[스크립팅됨](multi-tenant-user-management-scenarios.md#scripted-scenario) | 리소스 테넌트 관리자가 공유 시나리오를 지원하기 위해 스크립팅된 "끌어오기" 프로세스를 배포하여 게스트 사용자의 검색 및 프로비전을 자동화합니다. |  <li>테넌트가 2개 이하입니다.<li>자동 사용자 특성 동기화가 필요하지 않습니다.<li>사용자에게 리소스에 대해 미리 구성된(임시가 아님) 액세스 권한이 필요합니다.|
|[자동](multi-tenant-user-management-scenarios.md#automated-scenario)|리소스 테넌트 관리자가 ID 프로비전 시스템을 사용하여 프로비전 및 프로비전 해제 프로세스를 자동화합니다. |  <li>프로비전 및 프로비전 해제를 통한 전체 ID 수명 주기 관리를 자동화해야 합니다.<li>GAL 세부 정보를 채우고 동적 자격 시나리오를 지원하려면 특성 동기화가 필요합니다.<li>사용자에게 "Day One"의 리소스에 대해 미리 구성된(임시가 아님) 액세스 권한이 필요합니다.|

  
## <a name="next-steps"></a>다음 단계

[다중 테넌트 사용자 관리 시나리오](multi-tenant-user-management-scenarios.md)

[일반적인 다중 테넌트 고려 사항](multi-tenant-common-considerations.md)

[일반적인 다중 테넌트 솔루션](multi-tenant-common-solutions.md)
