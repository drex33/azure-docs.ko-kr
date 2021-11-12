---
title: 보안, 거 버 넌 스 및 Azure 개발/테스트 구독
description: 조직의 개발/테스트 구독 내에서 보안 및 거 버 넌 스를 관리 합니다.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: 6f3d9bae37e49c23dacc9a44ed4e0006df95ec29
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305886"
---
# <a name="security-within-azure-devtest-subscription"></a>Azure 개발/테스트 구독 내 보안

리소스를 안전 하 게 유지 하는 것은 클라우드 공급자와 Azure 간의 공동 작업입니다. Azure 개발/테스트 구독 및 [클라우드 용 Microsoft Defender](../../security-center/security-center-introduction.md) 는 네트워크를 강화 하 고, 서비스를 보호 하 고, 보안 상태를 최우선으로 하는 데 필요한 도구를 제공 합니다.  

Azure 개발/테스트 구독 내에서 중요 한 도구를 사용 하 여 리소스에 대 한 보안 액세스를 만들 수 있습니다.  

- Azure 관리 그룹  
- Azure Lighthouse  
- 크레딧 모니터링  
- Azure Active Directory  

## <a name="azure-management-groups"></a>Azure 관리 그룹  

azure 개발/테스트 구독을 설정 하 고 설정 하는 경우 azure는 단일 Azure Active Directory 도메인의 리소스에 대 한 id 및 액세스를 관리 하는 기본 리소스 계층 구조를 배포 합니다. 조직에서는 리소스 계층 구조를 사용 하 여 리소스 및 사용자에 대 한 강력한 보안 경계을 설정할 수 있습니다.  

![Azure 관리 그룹의 스크린샷](media/concepts-security-governance-devtest/access-management-groups.png "Azure 기본 리소스 계층 구조.")  

리소스, 리소스 그룹, 구독, 관리 그룹 및 테넌트가 리소스 계층 구조를 집합적으로 구성합니다. Azure 사용자 지정 역할 또는 Azure 정책 할당에서 이러한 설정을 업데이트 하 고 변경 하면 리소스 계층의 모든 리소스에 영향을 미칠 수 있습니다. 모든 리소스에 부정적인 영향을 줄 수 있는 변경으로부터 리소스 계층 구조를 보호하는 것이 중요합니다.  

[Azure 관리 그룹](../../governance/management-groups/overview.md) 는 단일 테 넌 트에서 리소스를 보호 하 고 액세스를 관리 하는 중요 한 측면입니다. Azure 관리 그룹를 사용 하 여 다양 한 유형의 구독에 할당량, Azure 정책 및 보안을 설정할 수 있습니다. 이러한 그룹은 조직의 개발/테스트 구독에 대 한 보안을 개발 하는 데 중요 한 구성 요소입니다.  

![Azure 조직 및 거 버 넌 스 그룹화의 스크린샷](media/concepts-security-governance-devtest/orgs-and-governance.png "Azure 관리 그룹 전체 거 버 넌 스에 맞추는 방법")

위에서 볼 수 있듯이, 관리 그룹을 사용 하 여 기본 계층을 변경 하 고 관리 그룹의 수준을 추가 합니다. [리소스 계층을 보호 하기 위해 적절 한 프로세스](../../governance/management-groups/how-to/protect-resource-hierarchy.md) 를 따르지 않는 경우이 동작으로 인해 예기치 않은 상황이 발생할 수 있으며 보안에 허점이 발생할 수 있습니다.  

## <a name="why-are-azure-management-groups-useful"></a>Azure 관리 그룹이 유용한 이유는 무엇 인가요?  

조직의 개발/테스트 구독에 대 한 보안 정책을 개발할 때 조직 구성 단위나 기간 업무에 따라 여러 개발/테스트 구독을 선택할 수 있습니다. 아래에서 해당 관리 그룹의 시각적 개체를 볼 수 있습니다.  

![조직 내의 여러 구독에 대 한 구독 관리 그룹의 다이어그램입니다.](media/concepts-security-governance-devtest/access-management-groups.png "조직 내의 여러 구독에 대 한 관리 그룹 다이어그램입니다.")  

다른 모든 단위에 대해 하나의 개발/테스트 구독을 갖도록 선택할 수도 있습니다.  

Azure 관리 그룹 및 개발/테스트 구독은 조직 구조 내에서 보안 장벽 역할을 합니다.  

이 보안 장벽에는 다음과 같은 두 가지 구성 요소가 있습니다.  

- Id 및 액세스: 특정 리소스에 대 한 액세스를 분할 해야 할 수 있습니다.  
- 데이터: 개인 정보에 액세스 하는 리소스에 대 한 다른 구독  

## <a name="using-azure-active-directory-tenants"></a>Azure Active Directory 테 넌 트 사용  

[테 넌 트는](../../active-directory/develop/quickstart-create-new-tenant.md) 조직이 나 응용 프로그램 개발자가 azure, Microsoft Intune 또는 Microsoft 365에 등록 하는 것과 같은 Microsoft와의 관계를 만들 때 받는 azure AD의 전용 인스턴스입니다.  

각 Azure AD 테 넌 트는 다른 Azure AD 테 넌 트와 별개입니다. 각 Azure AD 테 넌 트에는 회사 및 학교 id, 소비자 id (Azure AD B2C 테 넌 트 인 경우) 및 앱 등록에 대 한 고유한 표현이 있습니다. 테넌트 내의 앱 등록은 오직 테넌트 또는 모든 테넌트 내의 계정의 인증을 허용할 수 있습니다.  

조직의 id 인프라를 단일 테 넌 트 내의 관리 그룹 보다 더 분리 해야 하는 경우 자체 리소스 계층 구조를 사용 하 여 다른 테 넌 트를 만들 수도 있습니다.  

별도의 리소스 및 사용자를 수행 하는 쉬운 방법은 새 Azure AD 테 넌 트를 만드는 것입니다.  

### <a name="create-a-new-azure-ad-tenant"></a>새 Azure AD 테넌트 만들기  

Azure AD 테 넌 트가 없거나 개발용으로 새 테 넌 트가 없는 경우 [빠른 시작 가이드](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)를 참조   하거나 [디렉터리 만들기 환경을](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)따르세요. 새 테넌트를 만들려면 다음 정보를 제공해야 합니다.  

- **조직 이름**  
- **초기 도메인**   -/*. onmicrosoft.com의 일부가 됩니다. 나중에 도메인을 사용자 지정할 수 있습니다.  
- **국가 또는 지역**  

 [Azure AD 테 넌 트 만들기 및 설정에 대해 자세히 알아보세요.](../../active-directory/develop/quickstart-create-new-tenant.md)  

### <a name="using-azure-lighthouse-to-manage-multiple-tenants"></a>Azure Lighthouse를 사용 하 여 여러 테 넌 트 관리  

Azure Lighthouse는 교차 및 다중 테넌트 관리를 지원하여 리소스 및 테넌트 간에 더 높은 자동화, 확장성 및 향상된 거버넌스를 허용합니다. 서비스 공급자는 Azure 플랫폼에 기본 제공 되는 포괄적이 고 강력한 관리 도구를 사용 하 여 관리 서비스를 제공할 수 있습니다. 고객은 테 넌 트에 액세스 하는 사용자, 액세스 하는 리소스 및 수행할 수 있는 작업에 대 한 제어를 유지 합니다.  

Azure Lighthouse에 대 한 일반적인 시나리오는 고객의 테 넌 트 Azure Active Directory 리소스를 관리 하는 것입니다. 그러나 Azure Lighthouse의 기능을 사용 하 여 여러 Azure AD 테 넌 트를 사용 하는 엔터프라이즈 내에서 교차 테 넌 트 관리를 단순화할 수도 있습니다.  

대부분의 조직에서는 단일 Azure AD 테넌트를 사용하여 더 쉽게 관리할 수 있습니다. 모든 리소스가 한 테넌트 내에 있으면 해당 테넌트 내의 지정된 사용자, 사용자 그룹 또는 서비스 주체가 관리 작업을 중앙 집중식으로 수행할 수 있습니다.  

다중 테 넌 트 아키텍처가 필요한 경우 Azure Lighthouse는 관리 작업을 중앙 집중화 하 고 간소화 하는 데 도움이 됩니다. Azure 위임 된 리소스 관리를 사용 하 여 하나의 테 넌 트에 있는 사용자는 중앙 집중화 되 고 확장 가능한 방식으로 교차 테 넌 트 관리 기능을 수행할 수 있습니다.  

[추가 보안 리소스](../../security-center/security-center-introduction.md)
