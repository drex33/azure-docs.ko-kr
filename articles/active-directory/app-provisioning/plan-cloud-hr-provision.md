---
title: Azure Active Directory 사용자 프로비저닝을 위한 클라우드 HR 애플리케이션 계획
description: 이 문서에서는 Workday 또는 SuccessFactors와 같은 클라우드 HR 시스템을 Azure Active Directory와 통합하는 배포 프로세스를 설명합니다. Azure AD를 클라우드 HR 시스템과 통합하면 완전한 ID 수명 주기 관리 시스템이 구현됩니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 07/13/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 77a80ced74bfd841001952dbf962babedece0978
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991169"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Azure Active Directory 사용자 프로비저닝을 위한 클라우드 HR 애플리케이션 계획

지금까지 IT 직원은 직원을 수동으로 만들고 업데이트하고 삭제하는 방법을 사용해 왔습니다. CSV 파일 업로드 또는 사용자 지정 스크립트와 같은 방법을 사용하여 직원 데이터를 동기화했습니다. 이러한 프로비저닝 프로세스는 오류가 발생하기 쉽고, 안전하지 않으며, 관리하기가 어렵습니다.

직원, 공급업체 또는 불확정 작업자의 ID 수명 주기를 관리하기 위해 [Azure AD(Azure Active Directory) 사용자 프로비저닝 서비스](../app-provisioning/user-provisioning.md)는 클라우드 기반 HR(인적 자원) 애플리케이션과의 통합을 제공합니다. 애플리케이션의 예로 Workday 또는 SuccessFactors가 있습니다.

Azure AD는 이 통합을 사용하여 다음과 같은 클라우드 HR 애플리케이션(앱) 워크플로를 사용하도록 설정합니다.

- **Active Directory에 사용자 프로비저닝:** 클라우드 HR 앱에서 선택한 사용자 집합을 하나 이상의 Active Directory 도메인으로 프로비저닝합니다.
- **Azure AD에 클라우드 전용 사용자 프로비저닝:** Active Directory가 사용되지 않는 시나리오에서는 사용자를 클라우드 HR 앱에서 Azure AD로 직접 프로비저닝합니다.
- **클라우드 HR 앱에 다시 쓰기:** Azure AD의 이메일 주소와 사용자 이름 특성을 클라우드 HR 앱에 다시 씁니다.

> [!NOTE]
> 이 배포 계획에서는 Azure AD 사용자 프로비저닝을 사용하여 클라우드 HR 앱 워크플로를 배포하는 방법을 보여 줍니다. SaaS(Software as a Service) 앱에 자동 사용자 프로비저닝을 배포하는 방법에 대한 자세한 내용은 [자동 사용자 프로비저닝 배포 계획](./plan-auto-user-provisioning.md)을 참조하세요.

## <a name="enabled-hr-scenarios"></a>사용되는 HR 시나리오

Azure AD 사용자 프로비저닝 서비스를 사용하면 다음과 같은 HR 기반 ID 수명 주기 관리 시나리오를 자동화할 수 있습니다.

- **새 직원 고용:** 새 직원이 클라우드 HR 앱에 추가되면 이메일 주소와 사용자 이름 특성을 클라우드 HR 앱에 다시 쓰는 옵션을 사용하여 사용자 계정이 Active Directory 및 Azure AD에서 자동으로 만들어집니다.
- **직원 특성 및 프로필 업데이트:** 이름, 직위 또는 관리자와 같은 직원 레코드가 클라우드 HR 앱에서 업데이트되면 사용자 계정이 Active Directory 및 Azure AD에서 자동으로 업데이트됩니다.
- **직원 해고:** 직원이 클라우드 HR 앱에서 해고되면 해당 사용자 계정이 Active Directory 및 Azure AD에서 자동으로 사용하지 않도록 설정됩니다.
- **직원 재고용:** 직원이 클라우드 HR 앱에서 다시 고용되면 이전 계정이 자동으로 다시 활성화되거나 Active Directory 및 Azure AD로 다시 프로비저닝될 수 있습니다.

## <a name="who-is-this-integration-best-suited-for"></a>이 통합에 가장 적합한 사용자

Azure AD 사용자 프로비저닝과 클라우드 HR 앱의 통합은 다음과 같은 조직에 가장 적합합니다.

- 클라우드 HR 사용자 프로비저닝에 맞게 미리 빌드된 클라우드 기반 솔루션이 필요합니다.
- 사용자를 클라우드 HR 앱에서 Active Directory 또는 Azure AD로 직접 프로비저닝해야 합니다.
- 클라우드 HR 앱에서 얻은 데이터를 사용하여 사용자를 프로비저닝해야 합니다.
- 클라우드 HR 앱에서 감지된 변경 정보만을 기반으로 하여 참가, 이동 및 탈퇴하는 사용자를 하나 이상의 Active Directory 포리스트, 도메인 및 OU에 동기화해야 합니다.
- Microsoft 365를 이메일로 사용합니다.

## <a name="learn"></a>배우기

사용자 프로비저닝은 지속적인 ID 거버넌스를 위한 토대를 만듭니다. 이를 통해 신뢰할 수 있는 ID 데이터를 사용하는 비즈니스 프로세스의 품질을 향상시킬 수 있습니다.

### <a name="terms"></a>사용 약관

이 문서에서 사용하는 용어는 다음과 같습니다.

- **원본 시스템** - Azure AD가 프로비저닝하는 사용자의 원본 리포지토리입니다. 예를 들어 Workday 또는 SuccessFactors와 같은 클라우드 HR 앱이 있습니다.
- **대상 시스템** - Azure AD가 프로비저닝하는 사용자의 대상 리포지토리입니다. 예를 들면 Active Directory, Azure AD, Microsoft 365 또는 기타 SaaS 앱이 있습니다.
- **Joiners-Movers-Leavers 프로세스**: 클라우드 HR 앱을 레코드 시스템으로 사용하여 신규 채용, 전보 및 해지에 사용되는 용어입니다. 이 프로세스는 서비스가 필요한 특성을 대상 시스템에 성공적으로 프로비저닝할 때 완료됩니다.

### <a name="key-benefits"></a>주요 이점

HR 기반 IT 프로비저닝의 이 기능은 다음과 같은 중요한 비즈니스 이점을 제공합니다.

- **생산성 향상:** 이제 사용자 계정 및 Microsoft 365 라이선스의 할당을 자동화하고 키 그룹에 대한 액세스를 제공할 수 있습니다. 할당을 자동화하면 새로 고용된 직원이 작업 도구에 즉시 액세스할 수 있고 생산성이 향상됩니다.
- **위험 관리:** 클라우드 HR 앱에서 들어오는 데이터를 사용하여 직원 상태 또는 그룹 멤버 자격에 따라 변경을 자동화하여 보안을 강화할 수 있습니다. 변경을 자동화하면 사용자가 조직에서 이동하거나 탈퇴할 때 사용자 ID와 주요 앱에 대한 액세스가 자동으로 업데이트됩니다.
- **주소 규정 준수 및 거버넌스:** Azure AD는 원본 및 대상 시스템 모두의 앱에서 수행하는 사용자 프로비저닝 요청에 대한 기본 감사 로그를 지원합니다. 감사를 사용하면 단일 화면에서 앱에 액세스할 수 있는 사용자를 추적할 수 있습니다.
- **비용 관리:** 자동 프로비저닝은 수동 프로비저닝과 관련된 비효율성 및 인간 오류를 방지하여 비용을 절감합니다. 레거시 플랫폼 및 오래된 플랫폼을 사용하여 시간이 지남에 따라 빌드되는 사용자 지정 개발 사용자 프로비저닝 솔루션에 대한 필요성을 줄입니다.

### <a name="licensing"></a>라이선싱

클라우드 HR 앱을 Azure AD 사용자 프로비저닝 통합에 구성하려면 유효한 [Azure AD Premium 라이선스](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing) 및 Workday 또는 SuccessFactors와 같은 클라우드 HR 앱에 대한 라이선스가 필요합니다.

또한 클라우드 HR 앱에서 소싱되고 온-프레미스 Active Directory 또는 Azure AD에 프로비저닝되는 모든 사용자에 대해서도 유효한 Azure AD Premium P1 이상의 구독 라이선스가 필요합니다. 클라우드 HR 앱에서 보유한 라이선스 수가 잘못된 경우 사용자 프로비저닝 중에 오류가 발생할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

- Azure AD Connect 프로비저닝 에이전트를 구성하기 위한 Azure AD [하이브리드 ID 관리자](../roles/permissions-reference.md#hybrid-identity-administrator)
- Azure Portal에서 프로비저닝 앱을 구성하기 위한 Azure AD [애플리케이션 관리자](../roles/permissions-reference.md#application-administrator) 역할
- 클라우드 HR 앱의 테스트 및 프로덕션 인스턴스
- 테스트 목적으로 시스템 통합 사용자를 만들고 직원 데이터를 변경하기 위한 클라우드 HR 앱 관리자 권한
- Active Directory에 대한 사용자 프로비저닝의 경우 Azure AD Connect 프로비저닝 에이전트를 호스트하려면 Windows Server 2016 이상을 실행하는 서버 필요 이 서버는 Active Directory 관리 계층 모델을 기준으로 계층 0 서버여야 합니다.
- Active Directory와 Azure AD 간 사용자 동기화를 위한 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)

### <a name="training-resources"></a>학습 리소스

| **리소스** | **링크 및 설명** |
|:-|:-|
| 동영상 | [Active Azure Directory에서 사용자 프로비저닝이란?](https://youtu.be/_ZjARPpI6NI) |
| | [Active Azure Directory에서 사용자 프로비저닝을 배포하는 방법](https://youtu.be/pKzyts6kfrw) |
| 자습서 | [SaaS 앱을 Azure AD와 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md) |
| | [자습서: 자동 사용자 프로비저닝을 위한 Workday 구성](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| FAQ | [자동화된 사용자 프로비저닝](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Workday에서 Azure AD로 프로비저닝 구성](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>솔루션 아키텍처

다음 예제에서는 일반적인 하이브리드 환경을 위한 엔드투엔드 사용자 프로비저닝 솔루션 아키텍처에 대해 설명하며 다음이 포함되어 있습니다.

- **클라우드 HR 앱에서 Active Directory로의 신뢰할 수 있는 HR 데이터 흐름.** 이 흐름에서 HR 이벤트(Joiners-Movers-Leavers 프로세스)는 클라우드 HR 앱 테넌트에서 시작됩니다. Azure AD 프로비저닝 서비스 및 Azure AD Connect 프로비저닝 에이전트는 클라우드 HR 앱 테넌트의 사용자 데이터를 Active Directory로 프로비저닝합니다. 이벤트에 따라 Active Directory에서 만들기, 업데이트, 활성화 및 비활성화 작업을 수행할 수 있습니다.
- **Azure AD와 동기화하고 온-프레미스 Active Directory에서 클라우드 HR 앱으로 이메일 및 사용자 이름을 쓰기 저장합니다.** Active Directory에서 계정이 업데이트되면 Azure AD Connect를 통해 Azure AD와 동기화됩니다. 이메일 주소 및 사용자 이름 특성은 클라우드 HR 앱 테넌트에 쓰기 저장될 수 있습니다.

![워크플로 다이어그램](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>워크플로에 대한 설명

다이어그램에는 다음과 같은 주요 단계가 나와 있습니다.  

1. **HR 팀** 은 클라우드 HR 앱 테넌트에서 트랜잭션을 수행합니다.
2. **Azure AD 프로비저닝 서비스** 는 클라우드 HR 앱 테넌트에서 예약된 주기를 실행하고 Active Directory와 동기화하기 위해 처리해야 하는 변경 사항을 식별합니다.
3. **Azure AD 프로비저닝 서비스** 는 Active Directory 계정 만들기, 업데이트, 활성화 및 비활성화 작업이 포함된 요청 페이로드로 Azure AD Connect 프로비저닝 에이전트를 호출합니다.
4. **Azure AD Connect 프로비저닝 에이전트** 는 서비스 계정을 사용하여 Active Directory 계정 데이터를 관리합니다.
5. **Azure AD Connect** 는 델타 [동기화](../hybrid/how-to-connect-sync-whatis.md)를 실행하여 Active Directory에서 업데이트를 가져옵니다.
6. **Active Directory** 업데이트는 Azure AD와 동기화됩니다.
7. **Azure AD 프로비저닝 서비스** 는 Azure AD의 이메일 특성 및 사용자 이름을 클라우드 HR 앱 테넌트에 쓰기 저장합니다.

## <a name="plan-the-deployment-project"></a>배포 프로젝트 계획

사용자 환경에서 이 배포에 대한 전략을 결정하는 동안 조직의 요구 사항을 고려합니다.

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트가 실패하는 이유는 일반적으로 영향, 결과 및 책임에 대한 기대 수준이 일치하지 않기 때문입니다. 이러한 문제를 방지하려면 [올바른 관련자가 참여](../fundamentals/active-directory-deployment-plans.md)해야 합니다. 또한 프로젝트의 관련자 역할을 잘 이해하고 있어야 합니다. 관련자 및 각 관련자의 프로젝트 입력 및 책임을 문서화합니다.

기존 HR 비즈니스 프로세스 및 작업자 ID와 작업 데이터 처리 요구 사항에 대한 입력을 제공할 수 있는 HR 조직의 담당자를 포함합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자 경험이 언제 어떻게 변화하는지 적극적으로 사용자와 의견을 교환하세요. 문제가 발생하는 경우 지원을 얻는 방법을 알려주세요.

### <a name="plan-a-pilot"></a>파일럿 계획

HR 비즈니스 프로세스 및 ID 워크플로를 클라우드 HR 앱에서 대상 시스템으로 통합하려면 솔루션을 프로덕션 환경에 배포하기 전에 상당한 양의 데이터 유효성 검사, 데이터 변환, 데이터 정리 및 엔드투엔드 테스트가 필요합니다.

프로덕션 환경에서 모든 사용자로 확장하기 전에 [파일럿 환경](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)에서 초기 구성을 실행합니다.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>클라우드 HR 프로비저닝 커넥터 앱 선택

클라우드 HR 앱과 Active Directory 간에 Azure AD 프로비저닝 워크플로를 용이하게 하기 위해 Azure AD 앱 갤러리에서 여러 프로비저닝 커넥터 앱을 추가할 수 있습니다.

- **클라우드 HR 앱에서 Active Directory로 사용자 프로비저닝**: 이 프로비저닝 커넥터 앱은 클라우드 HR 앱에서 단일 Active Directory 도메인으로 사용자 계정 프로비저닝을 용이하게 합니다. 도메인이 여러 개 있는 경우 프로비전해야 하는 각 Active Directory 도메인에 대해 Azure AD 앱 갤러리에서 이 앱의 하나의 인스턴스를 추가할 수 있습니다.
- **클라우드 HR 앱에서 Azure AD로 사용자 프로비저닝**: Azure AD Connect는 Active Directory 사용자를 Azure AD와 동기화하는 데 사용되는 도구이지만, 이 프로비저닝 커넥터 앱을 사용하여 클라우드 HR 앱에서 단일 Azure AD 테넌트로 클라우드 전용 사용자를 프로비저닝할 수 있습니다.
- **클라우드 HR 앱 쓰기 저장**: 이 프로비저닝 커넥터 앱은 Azure AD에서 클라우드 HR 앱으로 사용자 이메일 주소 쓰기 저장을 용이하게 합니다.

예를 들어 다음 이미지에는 Azure AD 앱 갤러리에서 사용할 수 있는 Workday 커넥터 앱이 나열되어 있습니다.

![Azure Active Directory 포털 앱 갤러리](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>의사 결정 흐름 차트

다음 의사 결정 순서도를 사용하여 시나리오와 관련된 클라우드 HR 프로비저닝 앱을 확인하세요.

![의사 결정 흐름 차트](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Azure AD Connect 프로비저닝 에이전트 배포 토폴로지 디자인

클라우드 HR 앱과 Active Directory 간의 프로비저닝 통합에는 네 가지 구성 요소가 필요합니다.

- 클라우드 HR 앱 테넌트
- 프로비저닝 커넥터 앱
- Azure AD Connect 프로비저닝 에이전트
- Active Directory 도메인

Azure AD Connect 프로비저닝 에이전트 배포 토폴로지는 통합하려는 클라우드 HR 앱 테넌트 및 Active Directory 하위 도메인의 수에 따라 달라집니다. 여러 Active Directory 도메인이 있는 경우에는 Active Directory 도메인이 연속적인지 또는 [비연속적](/windows-server/identity/ad-ds/plan/disjoint-namespace)인지에 따라 달라집니다.

의사 결정에 따라 배포 시나리오 중 하나를 선택합니다.

- 단일 클라우드 HR 앱 테넌트 -> 신뢰할 수 있는 포리스트에 있는 단일 또는 여러 Active Directory 하위 도메인
- 단일 클라우드 HR 앱 테넌트 -> 비연속 Active Directory 포리스트의 여러 자식 도메인

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>단일 클라우드 HR 앱 테넌트 -> 신뢰할 수 있는 포리스트에 있는 단일 또는 여러 Active Directory 하위 도메인

다음 프로덕션 환경 구성을 권장합니다.

|요구 사항|권장|
|:-|:-|
|배포할 Azure AD Connect 프로비저닝 에이전트 수|2개(고가용성 및 장애 조치(failover))
|구성할 프로비저닝 커넥터 앱 수|자식 도메인당 앱 1개|
|Azure AD Connect 프로비저닝 에이전트용 서버 호스트|지리적으로 배치된 Active Directory 도메인 컨트롤러 찾을 수 있는 Windows Server 2016</br>Azure AD Connect 서비스와 함께 사용할 수 있음|

![온-프레미스 에이전트로의 흐름](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>단일 클라우드 HR 앱 테넌트 -> 비연속 Active Directory 포리스트의 여러 자식 도메인

이 시나리오에는 클라우드 HR 앱에서 비연속 Active Directory 포리스트의 도메인으로 사용자를 프로비저닝하는 작업이 포함됩니다.

다음 프로덕션 환경 구성을 권장합니다.

|요구 사항|권장|
|:-|:-|
|온-프레미스로 배포할 Azure AD Connect 프로비저닝 에이전트 수|비연속 Active Directory 포리스트당 2개|
|구성할 프로비저닝 커넥터 앱 수|자식 도메인당 앱 1개|
|Azure AD Connect 프로비저닝 에이전트용 서버 호스트|지리적으로 배치된 Active Directory 도메인 컨트롤러 찾을 수 있는 Windows Server 2016</br>Azure AD Connect 서비스와 함께 사용할 수 있음|

![단일 클라우드 HR 앱 테넌트 비연속 Active Directory 포리스트](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect 프로비저닝 에이전트 요구 사항

클라우드 HR 앱에서 Active Directory로 사용자 프로비저닝 솔루션을 사용하려면 Windows Server 2016 이상을 실행하는 서버에 하나 이상의 Azure AD Connect 프로비저닝 에이전트를 배포해야 합니다. 서버에는 최소 4GB RAM 및 .NET 4.7.1 이상 런타임이 있어야 합니다. 호스트 서버에 대상 Active Directory 도메인에 대한 네트워크 액세스 권한이 있어야 합니다.

온-프레미스 환경을 준비하기 위해, Azure AD Connect 프로비저닝 에이전트 구성 마법사는 Azure AD 테넌트에 에이전트를 등록하고, [포트를 열고](../app-proxy/application-proxy-add-on-premises-application.md#open-ports), [URL에 대한 액세스를 허용하고](../app-proxy/application-proxy-add-on-premises-application.md#allow-access-to-urls), [아웃바운드 HTTPS 프록시 구성](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)을 지원합니다.

프로비저닝 에이전트는 Active Directory 도메인과 통신하도록 [GMSA(전역 관리 서비스 계정)](../cloud-sync/how-to-prerequisites.md#group-managed-service-accounts)를 구성합니다. 비 GMSA 서비스 계정을 프로비저닝에 사용하려는 경우 [GMSA 구성을 건너뛰고](../cloud-sync/how-to-manage-registry-options.md#skip-gmsa-configuration) 구성 중에 서비스 계정을 지정할 수 있습니다. 

프로비저닝 요청을 처리할 도메인 컨트롤러를 선택할 수 있습니다. 지리적으로 분산된 도메인 컨트롤러가 여러 개 있는 경우 기본 설정된 도메인 컨트롤러와 동일한 사이트에 프로비저닝 에이전트를 설치합니다. 이렇게 위치를 지정하면 엔드투엔드 솔루션의 안정성 및 성능이 향상됩니다.

고가용성을 위해 둘 이상의 Azure AD Connect 프로비저닝 에이전트를 배포할 수 있습니다. 동일한 온-프레미스 Active Directory 도메인 집합을 처리하도록 에이전트를 등록합니다.

## <a name="design-hr-provisioning-app-deployment-topology"></a>HR 프로비저닝 앱 배포 토폴로지 디자인

인바운드 사용자 프로비저닝 구성과 관련된 Active Directory 도메인 수에 따라 다음 배포 토폴로지 중 하나를 고려할 수 있습니다. 각 토폴로지 다이어그램은 예제 배포 시나리오를 사용하여 구성 측면을 강조해서 보여 줍니다. 배포 요구 사항과 매우 유사한 예제를 사용하여 요구 사항을 충족하는 구성을 결정합니다. 

### <a name="deployment-topology-1-single-app-to-provision-all-users-from-cloud-hr-to-single-on-premises-active-directory-domain"></a>배포 토폴로지 1: 클라우드 HR의 모든 사용자를 단일 온-프레미스 Active Directory 도메인으로 프로비저닝하는 단일 앱

가장 일반적인 배포 토폴로지입니다. 클라우드 HR의 모든 사용자를 단일 AD 도메인으로 프로비저닝해야 하고 모든 사용자에게 동일한 프로비저닝 규칙이 적용되는 경우 이 토폴로지를 사용합니다. 

:::image type="content" source="media/plan-cloud-hr-provision/topology-1-single-app-with-single-ad-domain.png" alt-text="클라우드 HR에서 단일 AD 도메인으로 사용자를 프로비저닝하는 단일 앱 스크린샷" lightbox="media/plan-cloud-hr-provision/topology-1-single-app-with-single-ad-domain.png":::

**주요 구성 측면**
* 고가용성 및 장애 조치(failover)를 위해 두 개의 프로비저닝 에이전트 노드를 설정합니다. 
* [프로비저닝 에이전트 구성 마법사](../cloud-sync/how-to-install.md#install-the-agent)를 사용하여 Azure AD 테넌트에서 AD 도메인을 등록합니다. 
* 프로비저닝 앱을 구성할 때 등록된 도메인 드롭다운에서 AD 도메인을 선택합니다. 
* 범위 지정 필터를 사용하는 경우 실수로 계정을 비활성화하는 것을 방지하도록 [범위 외 삭제 건너뛰기 플래그](skip-out-of-scope-deletions.md)를 구성합니다. 

### <a name="deployment-topology-2-separate-apps-to-provision-distinct-user-sets-from-cloud-hr-to-single-on-premises-active-directory-domain"></a>배포 토폴로지 2: 클라우드 HR에서 단일 온-프레미스 Active Directory 도메인으로 고유한 사용자 세트를 프로비저닝하는 별도의 앱

이 토폴로지는 사용자 유형(직원/계약자), 사용자 위치 또는 사용자의 사업부에 따라 특성 매핑 및 프로비저닝 논리가 다른 비즈니스 요구 사항을 지원합니다. 또한 이 토폴로지로 부서 또는 국가에 따라 인바운드 사용자 프로비저닝의 관리 및 유지 관리를 위임할 수 있습니다.

:::image type="content" source="media/plan-cloud-hr-provision/topology-2-separate-apps-with-single-ad-domain.png" alt-text="클라우드 HR에서 단일 AD 도메인으로 사용자를 프로비저닝하는 별도의 앱 스크린샷" lightbox="media/plan-cloud-hr-provision/topology-2-separate-apps-with-single-ad-domain.png":::

**주요 구성 측면**
* 고가용성 및 장애 조치를 위해 두 개의 프로비저닝 에이전트 노드를 설정합니다. 
* 프로비저닝하려는 각 고유 사용자 집합에 대해 HR2AD 프로비저닝 앱을 만듭니다. 
* 프로비저닝 앱에서 [범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 각 앱에서 처리할 사용자를 정의합니다. 
* 개별 사용자 집합에서 관리자 참조를 확인해야 하는 시나리오(예: 직원인 관리자에게 보고하는 계약자)를 처리하려면 *관리자* 특성만 업데이트하기 위한 별도의 HR2AD 프로비저닝 앱을 만들 수 있습니다. 이 앱의 범위를 모든 사용자로 설정합니다. 
* 실수로 계정을 비활성화하는 것을 방지하도록 [범위 외 삭제 건너뛰기 플래그](skip-out-of-scope-deletions.md)를 구성합니다. 

> [!NOTE] 
> 테스트 AD 도메인이 없으며 AD에서 TEST OU 컨테이너를 사용하는 경우 이 토폴로지에서 두 개의 개별 앱 *HR2AD(프로덕션)* 및 *HR2AD(테스트)* 를 만들 수 있습니다. *HR2AD(테스트)* 앱을 사용하여 먼저 테스트를 거친 후 특성 매핑 변경 내용을 *HR2AD(프로덕션)* 앱으로 승격합니다.  

### <a name="deployment-topology-3-separate-apps-to-provision-distinct-user-sets-from-cloud-hr-to-multiple-on-premises-active-directory-domains-no-cross-domain-visibility"></a>배포 토폴로지 3: 클라우드 HR에서 여러 온-프레미스 Active Directory 도메인으로 고유한 사용자 집합을 프로비저닝하는 별도의 앱(도메인 간 가시성 없음)

관리자가 항상 사용자와 동일한 도메인에 있고 *userPrincipalName*, *samAccountName* 및 *mail* 과 같은 특성에 대한 고유 ID 생성 규칙에 포리스트 전체 조회가 필요하지 않은 경우 이 토폴로지에서 동일한 포리스트에 속하는 여러 독립적인 자식 AD 도메인을 관리할 수 있습니다. 또한 도메인 경계별로 각 프로비저닝 작업의 관리를 유연하게 위임할 수 있습니다. 

예를 들어 아래 다이어그램에서 프로비저닝 앱은 NA(북아메리카), 유럽, 중동 및 아프리카(EMEA) 및 APAC(아시아 태평양)와 같은 각 지리적 지역에 대해 설정됩니다. 위치에 따라 사용자는 해당 AD 도메인에 프로비저닝됩니다. *EMEA 관리자* 가 EMEA 지역에 속한 사용자의 프로비저닝 구성을 독립적으로 관리할 수 있도록 프로비저닝 앱의 위임된 관리가 가능합니다.  

:::image type="content" source="media/plan-cloud-hr-provision/topology-3-separate-apps-with-multiple-ad-domains-no-cross-domain.png" alt-text="클라우드 HR에서 여러 AD 도메인으로 사용자를 프로비저닝하는 별도의 앱 스크린샷" lightbox="media/plan-cloud-hr-provision/topology-3-separate-apps-with-multiple-ad-domains-no-cross-domain.png":::

**주요 구성 측면**
* 고가용성 및 장애 조치를 위해 두 개의 프로비저닝 에이전트 노드를 설정합니다. 
* [프로비저닝 에이전트 구성 마법사](../cloud-sync/how-to-install.md#install-the-agent)를 사용하여 Azure AD 테넌트에서 모든 자식 AD 도메인을 등록합니다. 
* 각 대상 도메인에 대해 별도의 HR2AD 프로비저닝 앱을 만듭니다. 
* 프로비저닝 앱을 구성할 때 사용 가능한 AD 도메인 드롭다운에서 해당 자식 AD 도메인을 선택합니다. 
* 프로비저닝 앱에서 [범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 각 앱에서 처리할 사용자를 정의합니다. 
* 실수로 계정을 비활성화하는 것을 방지하도록 [범위 외 삭제 건너뛰기 플래그](skip-out-of-scope-deletions.md)를 구성합니다. 


### <a name="deployment-topology-4-separate-apps-to-provision-distinct-user-sets-from-cloud-hr-to-multiple-on-premises-active-directory-domains-with-cross-domain-visibility"></a>배포 토폴로지 4: 클라우드 HR에서 여러 온-프레미스 Active Directory 도메인으로 고유한 사용자 집합을 프로비저닝하는 별도의 앱(도메인 간 가시성 있음)

사용자의 관리자가 다른 도메인에 있을 수 있고 *userPrincipalName*, *samAccountName* 및 *Mail* 과 같은 특성에 대한 고유 ID 생성 규칙에 포리스트 전체 조회가 필요한 경우 이 토폴로지에서 동일한 포리스트에 속하는 여러 독립적인 자식 AD 도메인을 관리할 수 있습니다. 

예를 들어 아래 다이어그램에서 프로비저닝 앱은 NA(북아메리카), 유럽, 중동 및 아프리카(EMEA) 및 APAC(아시아 태평양)와 같은 각 지리적 지역에 대해 설정됩니다. 위치에 따라 사용자는 해당 AD 도메인에 프로비저닝됩니다. 도메인 간 관리자 참조 및 포리스트 전체 조회는 프로비저닝 에이전트에서 조회 추적을 사용하도록 설정하여 처리됩니다. 

:::image type="content" source="media/plan-cloud-hr-provision/topology-4-separate-apps-with-multiple-ad-domains-cross-domain.png" alt-text="클라우드 HR에서 도메인 간 지원이 있는 여러 AD 도메인으로 사용자를 프로비저닝하는 별도의 앱 스크린샷" lightbox="media/plan-cloud-hr-provision/topology-4-separate-apps-with-multiple-ad-domains-cross-domain.png":::

**주요 구성 측면**
* 고가용성 및 장애 조치를 위해 두 개의 프로비저닝 에이전트 노드를 설정합니다. 
* 프로비저닝 에이전트에서 [조회 추적](../cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing)을 구성합니다. 
* [프로비저닝 에이전트 구성 마법사](../cloud-sync/how-to-install.md#install-the-agent)를 사용하여 부모 AD 도메인 및 모든 자식 AD 도메인을 Azure AD 테넌트에 등록합니다. 
* 각 대상 도메인에 대해 별도의 HR2AD 프로비저닝 앱을 만듭니다. 
* 각 프로비저닝 앱을 구성할 때 사용 가능한 AD 도메인 드롭다운에서 부모 AD 도메인을 선택합니다. 이렇게 하면 *userPrincipalName*, *samAccountName* 및 *mail* 과 같은 특성에 대한 고유한 값을 생성할 때 포리스트 전체 조회가 보장됩니다.
* 식 매핑과 함께 *parentDistinguishedName* 을 사용하여 올바른 자식 도메인 및 [OU 컨테이너](#configure-active-directory-ou-container-assignment)에서 사용자를 동적으로 만듭니다. 
* 프로비저닝 앱에서 [범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 각 앱에서 처리할 사용자를 정의합니다. 
* 도메인 간 관리자 참조를 확인하려면 *관리자* 특성만 업데이트하기 위한 별도의 HR2AD 프로비저닝 앱을 만듭니다. 이 앱의 범위를 모든 사용자로 설정합니다. 
* 실수로 계정을 비활성화하는 것을 방지하도록 [범위 외 삭제 건너뛰기 플래그](skip-out-of-scope-deletions.md)를 구성합니다. 

### <a name="deployment-topology-5-single-app-to-provision-all-users-from-cloud-hr-to-multiple-on-premises-active-directory-domains-with-cross-domain-visibility"></a>배포 토폴로지 5: 클라우드 HR의 모든 사용자를 여러 온-프레미스 Active Directory 도메인으로 프로비저닝하는 단일 앱(도메인 간 가시성 있음)

단일 프로비저닝 앱을 사용하여 모든 부모 및 자식 AD 도메인에 속한 사용자를 관리하려면 이 토폴로지를 사용합니다. 이 토폴로지는 프로비저닝 규칙이 모든 도메인에서 일관되고 프로비저닝 작업의 위임된 관리를 위한 요구 사항이 없는 경우에 권장됩니다. 이 토폴로지는 도메인 간 관리자 참조를 확인하도록 지원하며 포리스트 전체의 고유성 검사를 수행할 수 있습니다. 

예를 들어 아래 다이어그램에서 단일 프로비저닝 앱은 지역별, 즉 NA(북아메리카), 유럽, EMEA(중동 및 아프리카) 및 APAC(아시아 태평양)에 따라 그룹화되는 세 가지 자식 도메인에 있는 사용자를 관리합니다. *parentDistinguishedName* 에 대한 특성 매핑은 적절한 자식 도메인에서 사용자를 동적으로 만드는 데 사용됩니다. 도메인 간 관리자 참조 및 포리스트 전체 조회는 프로비저닝 에이전트에서 조회 추적을 사용하도록 설정하여 처리됩니다. 

:::image type="content" source="media/plan-cloud-hr-provision/topology-5-single-app-with-multiple-ad-domains-cross-domain.png" alt-text="클라우드 HR에서 도메인 간 지원이 있는 여러 AD 도메인으로 사용자를 프로비저닝하는 단일 앱 스크린샷" lightbox="media/plan-cloud-hr-provision/topology-5-single-app-with-multiple-ad-domains-cross-domain.png":::

**주요 구성 측면**
* 고가용성 및 장애 조치를 위해 두 개의 프로비저닝 에이전트 노드를 설정합니다. 
* 프로비저닝 에이전트에서 [조회 추적](../cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing)을 구성합니다. 
* [프로비저닝 에이전트 구성 마법사](../cloud-sync/how-to-install.md#install-the-agent)를 사용하여 부모 AD 도메인 및 모든 자식 AD 도메인을 Azure AD 테넌트에 등록합니다. 
* 전체 포리스트에 대한 단일 HR2AD 프로비저닝 앱을 만듭니다. 
* 프로비저닝 앱을 구성할 때 사용 가능한 AD 도메인 드롭다운에서 부모 AD 도메인을 선택합니다. 이렇게 하면 *userPrincipalName*, *samAccountName* 및 *mail* 과 같은 특성에 대한 고유한 값을 생성할 때 포리스트 전체 조회가 보장됩니다.
* 식 매핑과 함께 *parentDistinguishedName* 을 사용하여 올바른 자식 도메인 및 [OU 컨테이너](#configure-active-directory-ou-container-assignment)에서 사용자를 동적으로 만듭니다. 
* 범위 지정 필터를 사용하는 경우 실수로 계정을 비활성화하는 것을 방지하도록 [범위 외 삭제 건너뛰기 플래그](skip-out-of-scope-deletions.md)를 구성합니다. 

### <a name="deployment-topology-6-separate-apps-to-provision-distinct-users-from-cloud-hr-to-disconnected-on-premises-active-directory-forests"></a>배포 토폴로지 6: 클라우드 HR에서 연결이 끊긴 온-프레미스 Active Directory 포리스트로 고유한 사용자를 프로비저닝하는 별도의 앱

IT 인프라에서 AD 포리스트의 연결이 끊어지고/가입이 취소되고 비즈니스 소속에 따라 다른 포리스트에 사용자를 프로비저닝해야 하는 경우 이 토폴로지를 사용합니다. 예를 들어 자회사 Fabrikam에서 근무하는 사용자는 fabrikam.com 도메인으로 프로비저닝해야 하지만, 자회사 Contoso에 근무하는 사용자는 contoso.com에 프로비저닝해야 합니다.    

:::image type="content" source="media/plan-cloud-hr-provision/topology-6-separate-apps-with-disconnected-ad-forests.png" alt-text="클라우드 HR에서 연결이 끊긴 AD 포리스트로 사용자를 프로비저닝하는 별도의 앱 스크린샷" lightbox="media/plan-cloud-hr-provision/topology-6-separate-apps-with-disconnected-ad-forests.png":::

**주요 구성 측면**
* 고가용성 및 장애 조치를 위해 포리스트마다 하나씩 2개의 프로비저닝 에이전트 집합을 설정합니다. 
* 포리스트마다 하나씩 두 개의 서로 다른 프로비저닝 앱을 만듭니다. 
* 포리스트 내에서 도메인 간 참조를 확인해야 하는 경우 프로비저닝 에이전트에서 [조회 추적](../cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing)을 사용하도록 설정합니다. 
* 연결이 끊긴 각 포리스트에 대해 별도의 HR2AD 프로비저닝 앱을 만듭니다. 
* 각 프로비저닝 앱을 구성할 때 사용 가능한 AD 도메인 이름 드롭다운에서 해당 부모 AD 도메인을 선택합니다. 
* 실수로 계정을 비활성화하는 것을 방지하도록 [범위 외 삭제 건너뛰기 플래그](skip-out-of-scope-deletions.md)를 구성합니다. 

### <a name="deployment-topology-7-separate-apps-to-provision-distinct-users-from-multiple-cloud-hr-to-disconnected-on-premises-active-directory-forests"></a>배포 토폴로지 7: 여러 클라우드 HR에서 연결이 끊긴 온-프레미스 Active Directory 포리스트로 고유한 사용자를 프로비저닝하는 별도의 앱

대규모 조직에서는 여러 HR 시스템이 있는 것이 일반적입니다. 비즈니스 M&amp;A(합병 및 인수) 시나리오 중에 온-프레미스 Active Directory를 여러 HR 원본에 연결해야 할 수 있습니다. 여러 HR 원본이 있고 채널을 통해 이러한 HR 원본의 ID 데이터를 동일하거나 다른 온-프레미스 Active Directory 도메인에 연결하려는 경우 아래 토폴로지가 권장됩니다.  

:::image type="content" source="media/plan-cloud-hr-provision/topology-7-separate-apps-from-multiple-hr-to-disconnected-ad-forests.png" alt-text="여러 클라우드 HR에서 연결이 끊긴 AD 포리스트로 사용자를 프로비저닝하는 별도의 앱 스크린샷" lightbox="media/plan-cloud-hr-provision/topology-7-separate-apps-from-multiple-hr-to-disconnected-ad-forests.png":::

**주요 구성 측면**
* 고가용성 및 장애 조치를 위해 포리스트마다 하나씩 2개의 프로비저닝 에이전트 집합을 설정합니다. 
* 포리스트 내에서 도메인 간 참조를 확인해야 하는 경우 프로비저닝 에이전트에서 [조회 추적](../cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing)을 사용하도록 설정합니다. 
* 각 HR 시스템 및 온-프레미스 Active Directory 조합에 대해 별도의 HR2AD 프로비저닝 앱을 만듭니다.
* 각 프로비저닝 앱을 구성할 때 사용 가능한 AD 도메인 이름 드롭다운에서 해당 부모 AD 도메인을 선택합니다. 
* 실수로 계정을 비활성화하는 것을 방지하도록 [범위 외 삭제 건너뛰기 플래그](skip-out-of-scope-deletions.md)를 구성합니다. 

## <a name="plan-scoping-filters-and-attribute-mapping"></a>범위 지정 필터 및 특성 매핑 계획

클라우드 HR 앱에서 Active Directory 또는 Azure AD로 프로비저닝을 사용하도록 설정하면 Azure Portal은 특성 매핑을 통해 특성 값을 제어합니다.

### <a name="define-scoping-filters"></a>범위 지정 필터 정의

[범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 클라우드 HR 앱에서 Active Directory 또는 Azure AD로 프로비저닝할 사용자를 결정하는 특성 기반 규칙을 정의합니다.

Joiners 프로세스를 시작하는 경우 다음 요구 사항을 수집합니다.

- 클라우드 HR 앱이 직원과 비정규직 작업자를 모두 등록하는 데 사용되나요?
- 클라우드 HR 앱에서 Azure AD로 사용자 프로비저닝을 사용하여 직원과 비정규직 작업자를 모두 관리할 계획인가요?
- 클라우드 HR 앱에서 Azure AD로 사용자 프로비저닝을 클라우드 HR 앱 사용자 하위 집합에만 롤아웃할 계획인가요? 예를 들면 직원만입니다.

요구 사항에 따라 특성 매핑을 구성할 때 **원본 개체 범위** 필드를 설정하여 클라우드 HR 앱의 사용자 집합을 Active Directory로 프로비저닝 범위에 속해야 하는 사용자 집합을 선택할 수 있습니다. 자세한 내용은 일반적으로 사용되는 범위 지정 필터에 대한 클라우드 HR 앱 자습서를 참조하세요.

### <a name="determine-matching-attributes"></a>일치 특성 결정

프로비저닝을 사용하면 원본 시스템과 대상 시스템 간에 기존 계정을 일치시킬 수 있습니다. 클라우드 HR 앱을 Azure AD 프로비저닝 서비스와 통합하는 경우 클라우드 HR 앱에서 Active Directory 또는 Azure AD로 이동해야 하는 사용자 데이터를 결정하는 [특성 매핑을 구성](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings)할 수 있습니다.

Joiners 프로세스를 시작하는 경우 다음 요구 사항을 수집합니다.

- 이 클라우드 HR 앱에서 각 사용자를 식별하는 데 사용되는 고유 ID는 무엇인가요?
- ID 수명 주기 관점에서 재고용을 어떻게 처리하나요? 재고용 시 이전 직원 ID를 계속 유지하나요?
- 예정된 고용을 처리하고 사전에 Active Directory 계정을 만들어야 하나요?
- ID 수명 주기 관점에서 정규직과 비정규직 간 전환을 어떻게 처리하나요?
- 전환된 사용자가 이전 Active Directory 계정을 유지하나요 아니면 새 계정을 부여받나요?

요구 사항에 따라 Azure AD는 상수 값 제공 또는 [특성 매핑 식 작성](../app-provisioning/functions-for-customizing-application-data.md)을 통해 특성 간 직접 매핑을 지원합니다. 이러한 유연성을 통해 대상 앱 특성에 채워지는 내용을 궁극적으로 제어할 수 있습니다. [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) 및 Graph 탐색기를 사용하여 사용자 프로비저닝 특성 매핑 및 스키마를 JSON 파일로 내보내고 Azure AD로 다시 가져올 수 있습니다.

기본적으로 클라우드 HR 앱에서 고유한 직원 ID를 나타내는 특성은 *Active Directory의 고유한 특성에 매핑되는' 일치 특성으로 사용됩니다.* 예를 들어 Workday 앱 시나리오에서 **Workday** **WorkerID** 특성은 Active Directory **employeeID** 특성에 매핑됩니다.

일치 특성을 여러 개 설정하고 일치 우선 순위를 할당할 수 있습니다. 해당 특성이 일치 우선 순위에 따라 평가됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.

기존 특성 매핑을 변경하거나 삭제하는 등 [기본 특성 매핑을 사용자 지정](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)할 수도 있습니다. 비즈니스 요구 사항에 따라서도 기본 특성 매핑을 사용자 지정할 수 있습니다. 자세한 내용은 매핑할 사용자 지정 특성 목록에 대한 클라우드 HR 앱 자습서(예: [Workday](../saas-apps/workday-inbound-tutorial.md#managing-your-configuration))를 참조하세요.

### <a name="determine-user-account-status"></a>사용자 계정 상태 확인

기본적으로 프로비저닝 커넥터 앱은 HR 사용자 프로필 상태를 Active Directory 또는 Azure AD의 사용자 계정 상태에 매핑하여 사용자 계정을 사용하거나 사용하지 않도록 설정할지 여부를 결정합니다.

Joiners-Leavers 프로세스를 시작하는 경우 다음 요구 사항을 수집합니다.

| Process | 요구 사항 |
| - | - |
| **Joiners** | ID 수명 주기 관점에서 재고용을 어떻게 처리하나요? 재고용 시 이전 직원 ID를 계속 유지하나요? |
| | 예정된 고용을 처리하고 사전에 Active Directory 계정을 만들어야 하나요? 이러한 계정은 활성 상태로 생성되나요 아니면 비활성 상태로 생성되나요? |
| | ID 수명 주기 관점에서 정규직과 비정규직 간 전환을 어떻게 처리하나요? |
| | 전환된 사용자는 이전 Active Directory 계정을 유지하나요 아니면 새 계정을 부여받나요? |
| **Leavers** | Active Directory에서 해지가 직원과 비정규직 작업자에 대해 다르게 처리되나요? |
| | 사용자 해지를 처리하는 데 고려되는 유효 날짜를 무엇인가요? |
| | 정규직과 비정규직 간 전환이 기존 Active Directory 계정에 어떤 영향을 미치나요? |
| | Active Directory에서 취소 작업을 처리하려면 어떻게 해야 하나요? Active Directory에서 Joiner 프로세스의 일부로 예정된 고용이 생성되는 경우 취소 작업을 처리해야 합니다. |

요구 사항에 따라, Active Directory 계정이 데이터 요소의 조합을 기반으로 활성화 또는 비활성화되도록 [Azure AD 식](../app-provisioning/functions-for-customizing-application-data.md)을 사용하여 매핑 논리를 사용자 지정할 수 있습니다.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>클라우드 HR 앱에서 Active Directory로 사용자 특성 매핑

각 클라우드 HR 앱은 클라우드 HR과 Active Directory 간 기본 매핑을 제공합니다.

Joiners-Movers-Leavers 프로세스를 시작하는 경우 다음 요구 사항을 수집합니다.

| Process | 요구 사항 |
| - | - |
| **Joiners** | Active Directory 계정 생성 프로세스가 수동, 자동화, 부분적으로 자동화 중 무엇인가요? |
| | 클라우드 HR 앱에서 Active Directory로 사용자 지정 특성을 전파할 계획인가요? |
| **Movers** | 클라우드 HR 앱에서 Movers 작업이 발생하는 경우 어떤 특성을 처리하기를 원하나요? |
| | 사용자를 업데이트할 때 특정 특성 유효성 검사를 수행하나요? 그렇다면 세부 정보를 제공하세요. |
| **Leavers** | Active Directory에서 해지가 직원과 비정규직 작업자에 대해 다르게 처리되나요? |
| | 사용자 해지를 처리하는 데 고려되는 유효 날짜를 무엇인가요? |
| | 정규직과 비정규직 간 전환이 기존 Active Directory 계정에 어떤 영향을 미치나요? |

요구 사항에 따라 통합 목표를 충족하도록 매핑을 수정할 수 있습니다. 자세한 내용은 매핑할 사용자 지정 특성 목록에 대한 특정 클라우드 HR 앱 자습서(예: [Workday](../saas-apps/workday-inbound-tutorial.md#part-4-configure-attribute-mappings))를 참조하세요.

### <a name="generate-a-unique-attribute-value"></a>고유한 특성 값 생성

Joiners 프로세스를 시작할 때 CN, samAccountName, UPN 같은 특성을 설정하는 경우 고유한 특성 값을 생성해야 할 수 있습니다.

Azure AD 함수 [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)는 각 규칙을 평가한 다음 대상 시스템에서 고유성이 생성된 값을 확인합니다. 예제를 보려면 [userPrincipalName(UPN) 특성에 대한 고유 값 생성](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)을 참조하세요.

> [!NOTE]
> 이 함수는 현재 Active Directory에 대한 Workday 및 Active Directory 사용자 프로비저닝에 대한 SAP SuccessFactors에서만 지원됩니다. 다른 프로비저닝 앱에서는 사용할 수 없습니다.

### <a name="configure-active-directory-ou-container-assignment"></a>Active Directory OU 컨테이너 할당 구성

사업부, 위치 및 부서에 따라 Active Directory 사용자 계정을 컨테이너에 저장하는 것이 일반적인 요구 사항입니다. Movers 프로세스를 시작하고 감독 조직이 변경되는 경우 Active Directory에서 한 OU에서 다른 OU로 사용자를 이동해야 할 수 있습니다.

[Switch()](../app-provisioning/functions-for-customizing-application-data.md#switch) 함수를 사용하여 OU 할당에 대한 비즈니스 논리를 구성하고 Active Directory 특성 **parentDistinguishedName** 에 매핑합니다.

예를 들어 HR 특성 **지방자치제** 를 기반으로 OU에 사용자를 만들려는 경우 다음 식을 사용할 수 있습니다.

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

이 식을 사용하는 경우 지방자치제 값이 댈러스, 오스틴, 시애틀 또는 런던이면 해당 OU에 사용자 계정이 생성됩니다. 일치하는 항목이 없으면 기본 OU에 계정이 만들어집니다.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>새 사용자 계정의 암호 전달 계획

Joiners 프로세스를 시작하는 경우 새 사용자 계정의 임시 암호를 설정하고 전달해야 합니다. 클라우드 HR에서 Azure AD로 사용자 프로비저닝을 사용하면 첫 번째 날에 사용자에게 Azure AD [SSPR(셀프 서비스 암호 재설정)](../authentication/tutorial-enable-sspr.md) 기능을 롤아웃할 수 있습니다.

SSPR은 사용자가 암호를 재설정하거나 계정을 잠금 해제할 수 있도록 IT 관리자가 사용할 수 있는 간단한 수단입니다. 클라우드 HR 앱에서 Active Directory로 **휴대폰 번호** 특성을 프로비저닝하고 Azure AD와 동기화할 수 있습니다. **모바일 번호** 특성이 Azure AD에 프로비저닝된 후에는 사용자 계정에 대해 SSPR을 사용하도록 설정할 수 있습니다. 그러면 새 사용자가 첫 날부터 인증을 위해 휴대폰 번호를 등록하고 확인될 수 있습니다. 인증 연락처 정보를 미리 채우는 방법에 대한 자세한 내용은 [SSPR 설명서](../authentication/howto-sspr-authenticationdata.md)를 참조하세요. 

## <a name="plan-for-initial-cycle"></a>초기 주기 계획

Azure AD 프로비저닝 서비스를 처음 실행하는 경우 클라우드 HR 앱에 [초기 주기](../app-provisioning/how-provisioning-works.md#initial-cycle)를 수행하여 클라우드 HR 앱 내 모든 사용자 개체의 스냅샷을 만듭니다. 초기 주기에 소요되는 시간은 원본 시스템에 있는 사용자 수에 따라 결정됩니다. 사용자가 10만 명을 상회하는 일부 클라우드 HR 앱 테넌트는 초기 주기가 오랜 시간이 걸릴 수 있습니다.

**대규모 클라우드 HR 앱 테넌트(사용자 3만 이상)** 의 경우 점진적 단계로 초기 주기를 실행합니다. 다양한 사용자 프로비저닝 시나리오에서 Active Directory에 올바른 특성이 설정되는지 확인한 이후에만 증분 업데이트를 시작합니다. 다음 순서를 따르세요.

1. [범위 지정 필터](#plan-scoping-filters-and-attribute-mapping)를 설정하여 제한된 사용자 집합에 대해서만 초기 주기를 실행합니다.
2. 첫 번째 실행에 선택된 사용자에 대해 설정된 Active Directory 계정 프로비저닝 및 특성 값을 확인합니다. 결과가 예상과 일치하는 경우 범위 지정 필터를 확장하여 점차 더 많은 사용자를 포함하고 두 번째 실행의 결과를 확인합니다.

테스트 사용자에 대한 초기 주기 결과가 만족스러우면 [증분 업데이트](../app-provisioning/how-provisioning-works.md#incremental-cycles)를 시작합니다.

## <a name="plan-testing-and-security"></a>테스트 및 보안 계획

초기 파일럿에서 사용자 프로비저닝 사용까지 각 배포 단계에서 결과가 예상대로인지 테스트하고 프로비저닝 주기를 감사해야 합니다.

### <a name="plan-testing"></a>테스트 계획

클라우드 HR 앱에서 Azure AD로 사용자 프로비저닝을 구성한 후 테스트 사례를 실행하여 이 솔루션이 조직의 요구 사항을 충족하는지 확인합니다.

|시나리오|예상 결과|
|:-|:-|
|클라우드 HR 앱에서 새 직원을 채용합니다.| - 사용자 계정이 Active Directory 프로비저닝됩니다.</br>- 사용자가 Active Directory 도메인 앱에 로그인하여 원하는 작업을 수행할 수 있습니다.</br>- Azure AD Connect 동기화가 구성된 경우 사용자 계정이 Azure AD에서도 생성됩니다.
|클라우드 HR 앱에서 사용자가 해지됩니다.|- 사용자 계정이 Active Directory에서 비활성화됩니다.</br>- 사용자가 Active Directory로 보호되는 엔터프라이즈 앱에 로그인할 수 없습니다.
|Cloud HR 앱에서 사용자 감독 조직이 업데이트됩니다.|특성 매핑에 따라 사용자 계정이 Active Directory에서 한 OU에서 다른 OU로 이동합니다.|
|HR이 클라우드 HR 앱에서 사용자의 관리자를 업데이트합니다.|Active Directory의 관리자 필드가 새 관리자의 이름을 반영하여 업데이트됩니다.|
|HR이 직원을 새 역할로 다시 고용합니다.|동작은 클라우드 HR 앱이 직원 ID를 생성하도록 구성된 방식에 따라 달라집니다.</br>- 재고용 시 이전 직원 ID를 다시 사용하는 경우 커넥터는 사용자의 기존 Active Directory 계정을 사용하도록 설정합니다.</br>- 재고용 시 새 직원 ID를 가져오는 경우 커넥터는 사용자에 대한 새 Active Directory 계정을 만듭니다.|
|HR이 직원을 계약직 작업자로 전환하거나 그 반대로 전환합니다.|새 가상 사용자에 대한 새 Active Directory 계정이 생성되고 이전 계정이 전환 개시 날짜에 비활성화됩니다.|

위의 결과를 사용하여 설정된 타임라인에 따라 자동 사용자 프로비저닝 구현을 프로덕션으로 전환하는 방법을 결정합니다.

> [!TIP]
> 프로덕션 데이터를 사용하여 테스트 환경을 새로 고칠 때 개인정보보호 및 보안 표준을 준수하도록 데이터 감소 및 데이터 스크러빙과 같은 기술을 사용하여 중요한 개인 데이터를 제거하거나 마스킹하세요. 

### <a name="plan-security"></a>보안 계획

신규 서비스 배포의 일부로 보안 검토가 필요한 경우가 많습니다. 보안 검토가 필요한 경우 서비스로서의 ID를 개략적으로 설명하는 여러 Azure AD [백서](https://www.microsoft.com/download/details.aspx?id=36391)를 참조하세요.

### <a name="plan-rollback"></a>롤백 계획

클라우드 HR 사용자 프로비저닝 구현이 프로덕션 환경에서 원하는 대로 작동하지 않을 수 있습니다. 이 경우 다음 롤백 단계를 수행하면 양호한 것으로 알려진 이전 상태로 되돌리는 데 도움이 될 수 있습니다.

1. [ 요약 보고서](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) 및 [프로비저닝 로그](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview)를 검토하여 영향을 받는 사용자 또는 그룹에 수행된 잘못된 작업을 확인합니다. 프로비저닝 요약 보고서 및 로그에 대한 자세한 내용은 [클라우드 HR 앱 사용자 프로비저닝 관리](#manage-your-configuration)를 참조하세요.
2. 영향을 받는 사용자 또는 그룹의 양호한 것으로 알려진 마지막 상태는 프로비저닝 감사 로그를 통하거나 대상 시스템(Azure AD 또는 Active Directory)을 검토하여 확인할 수 있습니다.
3. 앱 소유자와 협력하여 양호한 것으로 알려진 마지막 상태 값을 사용하여 앱의 직접적인 영향을 받는 사용자 또는 그룹을 업데이트합니다.

## <a name="deploy-the-cloud-hr-app"></a>클라우드 HR 앱 배포

솔루션 요구 사항에 부합하는 클라우드 HR 앱을 선택합니다.

**Workday**: Workday에서 Active Directory 및 Azure AD로 작업자 프로필을 가져오려면 [자습서: 자동 사용자 프로비저닝을 위한 Workday 구성](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment)을 참조하세요. 필요에 따라 이메일 주소, 사용자 이름 및 전화 번호를 Workday에 쓰기 저장할 수 있습니다.

**SAP SuccessFactors**: SuccessFactors에서 Active Directory 및 Azure AD로 작업자 프로필을 가져오려면 [자습서: 자동 사용자 프로비저닝을 위한 SAP SuccessFactors 구성](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)을 참조하세요. 필요에 따라 이메일 주소 및 사용자 이름을 SuccessFactors에 쓰기 저장할 수 있습니다.

## <a name="manage-your-configuration"></a>구성 관리

Azure AD는 감사 로그 및 보고서를 통해 조직의 사용자 프로비저닝 사용량 및 작동 상태에 대한 추가 인사이트를 제공할 수 있습니다.

### <a name="gain-insights-from-reports-and-logs"></a>보고서 및 로그에서 인사이트 얻기

[초기 주기](../app-provisioning/how-provisioning-works.md#initial-cycle)가 성공적으로 완료되면 Azure AD 프로비저닝 서비스는 각 앱의 자습서에 정의된 간격으로 다음 이벤트 중 하나가 발생할 때까지 증분 업데이트를 무기한으로 실행합니다.

- 서비스를 수동으로 종료하는 경우. 새 초기 주기는 [Azure Portal](https://portal.azure.com/) 또는 적절한 [Microsoft Graph API](/graph/api/resources/synchronization-overview) 명령을 사용하여 트리거합니다.
- 특성 매핑 또는 범위 지정 필터가 변경되어 새 초기 주기가 트리거되는 경우
- 높은 오류 비율로 인해 프로비저닝 프로세스가 격리 상태로 전환되는 경우. 프로세스가 4주 이상 격리 상태를 유지하는 경우 자동으로 사용하지 않도록 설정됩니다.

이러한 이벤트 및 프로비저닝 서비스에서 수행하는 다른 모든 작업을 검토하려면 [프로비저닝 작업에 대한 로그를 검토하고 보고서를 확인하는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).

#### <a name="azure-monitor-logs"></a>Azure Monitor 로그

프로비저닝 서비스에서 실행하는 모든 활동은 Azure AD 감사 로그에 기록됩니다. 추가 분석을 위해 Azure AD 감사 로그를 Azure Monitor 로그로 라우팅할 수 있습니다. Azure Monitor 로그(Log Analytics 작업 영역이 라고도 함)를 사용하여 데이터를 쿼리하여 이벤트를 찾고, 추세를 분석하고, 다양한 데이터 원본 사이의 상관 관계를 분석할 수 있습니다. 실제 사용자 시나리오에서 Azure AD 로그에 대해 Azure Monitor 로그를 사용할 때의 이점을 알아보려면 [이 동영상](https://youtu.be/MP5IaCTwkQg)을 시청하세요.

[Azure AD 활동 로그용 Log Analytics 보기](../reports-monitoring/howto-install-use-log-analytics-views.md)를 설치하여 사용자 환경의 프로비저닝 이벤트에 대한 [미리 빌드된 보고서](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views)에 액세스합니다.

자세한 내용은 [Azure Monitor 로그를 사용하여 Azure AD 활동 로그를 분석](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)하는 방법을 참조하세요.

### <a name="manage-personal-data"></a>개인 데이터 관리

Windows Server에 설치된 Azure AD Connect 프로비저닝 에이전트는 클라우드 HR 앱에서 Active Directory로 특성 매핑에 따라 개인 데이터를 포함할 수 있는 Windows 이벤트 로그에 로그를 생성합니다. 사용자 개인 정보 보호 의무를 준수하려면 Windows 예약 작업을 설정하여 이벤트 로그를 삭제하고 데이터가 48시간 이상 유지되지 않도록 합니다.

Azure AD 프로비저닝 서비스는 30일 이상 데이터를 저장, 처리 또는 보관하지 않으므로 30일 이상 기간에 대해서는 보고서, 분석 또는 인사이트를 제공하지 않습니다.

### <a name="troubleshoot"></a>문제 해결

프로비저닝 중에 발생할 수 있는 문제를 해결하려면 다음 문서를 참조하세요.

- [Azure AD 갤러리 애플리케이션에 대해 사용자 프로비전 구성 문제](application-provisioning-config-problem.md)
- [애플리케이션에 프로비저닝하기 위해 온-프레미스 Active Directory의 특성을 Azure AD로 동기화](user-provisioning-sync-attributes-for-mapping.md)
- [사용자가 Azure Active Directory 갤러리 애플리케이션에 프로비전을 구성하는 동안 관리자 자격 증명을 저장하는 문제](./user-provisioning.md)
- [사용자가 Azure AD 갤러리 애플리케이션에 프로비전되지 않음](application-provisioning-config-problem-no-users-provisioned.md)
- [Azure AD 갤러리 애플리케이션에 잘못된 사용자 집합이 프로비전됨](../manage-apps/add-application-portal-assign-users.md)
- [에이전트 문제 해결을 위한 Windows 이벤트 뷰어 설정](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [서비스 문제 해결을 위한 Azure Portal 감사 로그 설정](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [AD 사용자 계정 생성 작업에 대한 로그 이해](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [관리자 업데이트 작업에 대한 로그 이해](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [일반적으로 발생하는 오류 해결](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>다음 단계

- [특성 매핑에 대한 식 작성](functions-for-customizing-application-data.md)
- [Azure AD 동기화 API 개요](/graph/api/resources/synchronization-overview)
- [범위를 벗어난 사용자 계정 삭제 건너뛰기](skip-out-of-scope-deletions.md)
- [Azure AD Connect 프로비저닝 에이전트: 버전 릴리스 기록](provisioning-agent-release-version-history.md)
