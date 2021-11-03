---
title: 보안, 거버넌스 및 Azure 개발/테스트 구독
description: 조직의 개발/테스트 구독 내에서 보안 및 거버넌스를 관리합니다.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: 70f4ea05d0fffe1046ac45246933db9ae34b8a9a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053651"
---
# <a name="security-within-azure-devtest-subscription"></a>Azure 개발/테스트 구독 내 보안

리소스를 안전하게 유지하는 것은 클라우드 공급자, Azure 및 사용자 간의 공동 노력입니다. Azure 개발/테스트 구독 및 [Azure Security Center](../../security-center/security-center-introduction.md) 네트워크를 강화하고, 서비스를 보호하고, 보안 태세를 기반으로 하는 데 필요한 도구를 제공합니다.  

Azure 개발/테스트 구독 내의 중요한 도구는 리소스에 대한 보안 액세스를 만드는 데 도움이 됩니다.  

- Azure 관리 그룹  
- Azure Lighthouse  
- 크레딧 모니터링  
- Azure Active Directory  

## <a name="azure-management-groups"></a>Azure 관리 그룹  

Azure 개발/테스트 구독을 사용하도록 설정하고 설정하는 경우 Azure는 단일 Azure Active Directory 도메인에서 ID 및 리소스에 대한 액세스를 관리하는 기본 리소스 계층 구조를 배포합니다. 리소스 계층 구조를 사용하면 조직에서 리소스 및 사용자에 대한 강력한 보안 경계를 설정할 수 있습니다.  

![Azure 관리 그룹 스크린샷](media/concepts-security-governance-devtest/access-management-groups.png "Azure 기본 리소스 계층 구조.")  

리소스, 리소스 그룹, 구독, 관리 그룹 및 테넌트가 리소스 계층 구조를 집합적으로 구성합니다. Azure 사용자 지정 역할 또는 Azure 정책 할당에서 이러한 설정을 업데이트하고 변경하면 리소스 계층의 모든 리소스에 영향을 줄 수 있습니다. 모든 리소스에 부정적인 영향을 줄 수 있는 변경으로부터 리소스 계층 구조를 보호하는 것이 중요합니다.  

[Azure 관리 그룹](../../governance/management-groups/overview.md) 액세스를 제어하고 단일 테넌트에서 리소스를 보호하는 중요한 측면입니다. Azure 관리 그룹 사용하면 할당량, Azure 정책 및 보안을 다양한 유형의 구독으로 설정할 수 있습니다. 이러한 그룹은 조직의 개발/테스트 구독에 대한 보안 개발의 중요한 구성 요소입니다.  

![Azure 조직 및 거버넌스 그룹화 스크린샷](media/concepts-security-governance-devtest/orgs-and-governance.png "Azure 관리 그룹 전반적인 거버넌스에 적합한 방식")

위에서 볼 수 있듯이 관리 그룹을 사용하면 기본 계층 구조가 변경되고 관리 그룹에 대한 수준이 추가됩니다. 이 동작은 리소스 계층 구조를 보호하기 위한 적절한 프로세스를 따르지 않는 경우 예기치 않은 상황과 보안 허점을 일으킬 [수 있습니다.](../../governance/management-groups/how-to/protect-resource-hierarchy.md)  

## <a name="why-are-azure-management-groups-useful"></a>Azure 관리 그룹 유용한 이유는 무엇인가요?  

조직의 개발/테스트 구독에 대한 보안 정책을 개발할 때 조직 구성 단위 또는 사업장당 여러 개발/테스트 구독을 선택할 수 있습니다. 아래에서 해당 관리 그룹화의 시각적 개체를 볼 수 있습니다.  

![조직 내의 여러 구독에 대한 구독 관리 그룹화 다이어그램](media/concepts-security-governance-devtest/access-management-groups.png "조직 내의 여러 구독에 대한 관리 그룹화 다이어그램")  

다른 모든 단위에 대해 하나의 개발/테스트 구독을 선택할 수도 있습니다.  

Azure 관리 그룹 및 개발/테스트 구독은 조직 구조 내에서 보안 장벽 역할을 합니다.  

이 보안 장벽에는 두 가지 구성 요소가 있습니다.  

- ID 및 액세스: 특정 리소스에 대한 액세스를 분할해야 할 수 있습니다.  
- 데이터: 개인 정보에 액세스하는 리소스에 대한 다양한 구독  

## <a name="using-azure-active-directory-tenants"></a>Azure Active Directory 테넌트 사용  

[테넌트는](../../active-directory/develop/quickstart-create-new-tenant.md) 조직 또는 앱 개발자가 Azure, Microsoft Intune 또는 Microsoft 365 등록과 같이 Microsoft와 관계를 만들 때 조직 또는 앱 개발자가 받는 Azure AD의 전용 인스턴스입니다.  

각 Azure AD 테넌트는 다른 Azure AD 테넌트에서 분리됩니다. 각 Azure AD 테넌트는 고유한 업무 및 학교 ID, 소비자 ID(Azure AD B2C 테넌트인 경우) 및 앱 등록을 표시합니다. 테넌트 내의 앱 등록은 오직 테넌트 또는 모든 테넌트 내의 계정의 인증을 허용할 수 있습니다.  

단일 테넌트 내에서 관리 그룹 외에 조직의 ID 인프라를 추가로 분리해야 하는 경우 자체 리소스 계층 구조를 사용하여 다른 테넌트도 만들 수 있습니다.  

별도의 리소스 및 사용자를 수행하는 쉬운 방법은 새 Azure AD 테넌트 만들기입니다.  

### <a name="create-a-new-azure-ad-tenant"></a>새 Azure AD 테넌트 만들기  

Azure AD 테넌트 또는 개발을 위한 새 테넌트 만들기를 원하는 경우 [빠른 시작 가이드를](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)   참조하거나 [디렉터리 만들기 환경을](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)따르세요. 새 테넌트를 만들려면 다음 정보를 제공해야 합니다.  

- **조직 이름**  
- **초기 도메인**   - /*.onmicrosoft.com 일부가 됩니다. 나중에 도메인을 사용자 지정할 수 있습니다.  
- **국가 또는 지역**  

 [Azure AD 테넌트 만들기 및 설정에 대해 자세히 알아보기](../../active-directory/develop/quickstart-create-new-tenant.md)  

### <a name="using-azure-lighthouse-to-manage-multiple-tenants"></a>Azure Lighthouse 사용하여 여러 테넌트 관리  

Azure Lighthouse는 교차 및 다중 테넌트 관리를 지원하여 리소스 및 테넌트 간에 더 높은 자동화, 확장성 및 향상된 거버넌스를 허용합니다. 서비스 공급자는 Azure 플랫폼에 기본 제공되는 포괄적이고 강력한 관리 도구를 사용하여 관리형 서비스를 제공할 수 있습니다. 고객은 테넌트 액세스하는 사람, 액세스하는 리소스 및 수행할 수 있는 작업에 대한 제어를 유지합니다.  

Azure Lighthouse 일반적인 시나리오는 고객의 Azure Active Directory 테넌트에서 리소스를 관리하는 것입니다. 그러나 Azure Lighthouse 기능을 사용하여 여러 Azure AD 테넌트에서 사용하는 기업 내에서 테넌트 간 관리를 간소화할 수도 있습니다.  

대부분의 조직에서는 단일 Azure AD 테넌트를 사용하여 더 쉽게 관리할 수 있습니다. 모든 리소스가 한 테넌트 내에 있으면 해당 테넌트 내의 지정된 사용자, 사용자 그룹 또는 서비스 주체가 관리 작업을 중앙 집중식으로 수행할 수 있습니다.  

다중 테넌트 아키텍처가 필요한 경우 Azure Lighthouse 관리 작업을 중앙 집중화하고 간소화하는 데 도움이 됩니다. Azure 위임 리소스 관리를 사용하면 하나의 관리 테넌트 사용자가 중앙 집중식으로 확장 가능한 방식으로 테넌트 간 관리 기능을 수행할 수 있습니다.  

[추가 보안 리소스](../../security-center/security-center-introduction.md)