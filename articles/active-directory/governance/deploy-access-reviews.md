---
title: Azure Active Directory 액세스 검토 배포 계획
description: 성공적인 액세스 검토 배포에 대한 계획 가이드
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 04/16/2021
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f754d527a1b469ae420ca1af53e2c3b73692a5ef
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051841"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Azure Active Directory 액세스 검토 배포 계획

[Azure AD(Azure Active Directory) 액세스 검토](access-reviews-overview.md)는 조직의 [리소스 액세스 수명 주기](identity-governance-overview.md)를 관리하여 네트워크를 보다 안전하게 유지하는 데 도움이 됩니다. 액세스 검토를 통해 다음을 수행할 수 있습니다.

* 정기적으로 검토를 예약하거나 임시 검토를 수행하여 애플리케이션 및 그룹과 같은 특정 리소스에 대한 액세스 권한이 있는 사용자 확인

* 정보, 규정 준수 또는 정책 이유에 대한 검토 추적

* 지속적인 액세스의 필요성을 자체 증명할 수 있는 특정 관리자, 비즈니스 소유자 또는 최종 사용자에게 검토 위임

* 인사이트를 사용하여 사용자가 계속 액세스 권한을 가져야 하는지 여부를 효율적으로 결정

* 리소스에 대한 사용자 액세스를 제거하는 등의 검토 결과 자동화

  ![액세스 검토 흐름을 보여 주는 다이어그램입니다.](./media/deploy-access-review/1-planning-review.png)

액세스 검토는 [Azure AD Identity Governance](identity-governance-overview.md) 기능 중 하나입니다. 다른 기능은 [권한 관리](entitlement-management-overview.md), [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 및 [사용 약관](../conditional-access/terms-of-use.md)이 있습니다. 이를 통해 조직은 다음 네 가지 질문을 해결할 수 있습니다.

* 액세스할 수 있는 리소스 및 사용자는 무엇인가요?

* 해당 사용자가 해당 액세스를 사용하여 수행하는 작업은 무엇인가요?

* 액세스 관리를 위해 조직에서 효과적으로 제어할 수 있나요?

* 감사자는 컨트롤이 작동하는지 확인할 수 있나요?

조직의 사용자에 대해 원하는 거버넌스 전략을 달성하려면 액세스 검토 배포 계획을 세워야 합니다.

### <a name="key-benefits"></a>주요 이점

액세스 검토를 사용하는 경우의 주요 이점은 다음과 같습니다.

* **공동 작업 제어**. 액세스 검토를 통해 조직에서는 사용자가 필요로 하는 모든 리소스에 대한 액세스를 관리할 수 있습니다. 조직의 사용자가 공유하고 공동 작업하는 경우 정보가 인증된 사용자에게만 공유되도록 할 수 있습니다.

* **위험 관리**. 액세스 검토를 통해 조직에서는 데이터와 애플리케이션에 대한 액세스를 검토하여 데이터 누출 및 데이터 분산의 위험을 낮출 수 있습니다. 여기에는 회사 리소스에 대한 외부 파트너의 액세스를 정기적으로 검토하는 기능이 포함됩니다. 

* **주소 규정 준수 및 거버넌스**. 액세스 검토를 통해 그룹, 앱 및 사이트에 대한 액세스 수명 주기를 제어하고 다시 인증할 수 있습니다. 조직과 관련한 규정 준수 또는 위험에 민감한 애플리케이션에 대한 추적 검토를 제어할 수 있습니다. 

* **비용 절감**. 액세스 검토는 클라우드에서 빌드되고, 기본적으로 그룹, 애플리케이션 및 액세스 패키지와 같은 클라우드 리소스와 함께 작동합니다. 액세스 검토를 사용하면 사용자 고유의 도구를 빌드하거나 온-프레미스 도구 집합을 업그레이드하는 것보다 비용이 저렴합니다.

### <a name="training-resources"></a>학습 리소스

다음 비디오는 액세스 검토에 대해 알아볼 때 유용할 수 있습니다.

* [Azure AD에서 액세스 검토란?](https://youtu.be/kDRjQQ22Wkk)

* [Azure AD에서 액세스 검토를 만드는 방법](https://youtu.be/6KB3TZ8Wi40)

* [Azure AD에서 Microsoft 365 그룹에 대한 액세스 권한이 있는 모든 게스트 사용자에 대한 자동 액세스 검토를 만드는 방법](https://www.youtube.com/watch?v=3D2_YW2DwQ8)

* [Azure AD에서 액세스 검토를 사용하도록 설정하는 방법](https://youtu.be/X1SL2uubx9M)

* [내 액세스를 사용하여 액세스를 검토하는 방법](https://youtu.be/tIKdQhdHLXU)


### <a name="licenses"></a>라이선스

전역 관리자 또는 사용자 관리자 외에도 액세스 검토를 만들거나 수행할 수 있는 유효한 Azure AD Premium(P2) 라이선스가 필요합니다. 자세한 내용은 [액세스 검토 라이선스 요구 사항](access-reviews-overview.md)을 참조하세요.

또한 [자격 수명 주기 관리](entitlement-management-overview.md) 또는 특권 ID 관리 같은 다른 ID 거버넌스 기능이 필요할 수 있습니다. 이 경우 관련 라이선스가 필요할 수도 있습니다. 자세한 내용은 [Azure Active Directory 가격 책정](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)을 참조하세요.

## <a name="plan-the-access-reviews-deployment-project"></a>액세스 검토 배포 프로젝트 계획

사용자 환경에서 액세스 검토를 배포하기 위한 전략을 결정하려면 조직의 요구 사항을 고려해야 합니다.

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트가 실패하는 이유는 일반적으로 영향, 결과 및 책임에 대한 기대 수준이 일치하지 않기 때문입니다. 이러한 문제를 방지하려면 [올바른 관련자를 참여](../fundamentals/active-directory-deployment-plans.md)시키고 프로젝트 역할을 명확하게 해야 합니다.

액세스 검토의 경우 조직 내에서 다음과 같이 팀의 담당자를 포함할 가능성이 높습니다.

* **IT 관리** 는 IT 인프라와 함께 클라우드 투자 및 SaaS(Software As a Service) 앱을 관리합니다. 이 팀은 다음 작업을 수행합니다.

   * Microsoft 365 및 Azure AD를 포함하여 인프라 및 앱에 대한 권한 있는 액세스를 검토합니다.

   * 최신 액세스 목록을 유지하기 위해 예외 목록 또는 IT 파일럿 프로젝트를 유지 관리하는 데 사용되는 그룹에 대한 액세스 검토를 예약하고 실행합니다.

   * 서비스 주체를 통해 리소스에 대한 프로그래밍(스크립트) 방식의 액세스를 제어하고 검토해야 합니다.

* **개발 팀** 은 조직의 애플리케이션을 빌드 및 유지 관리합니다. 이 팀은 다음 작업을 수행합니다.

   * 개발된 솔루션을 구성하는 SaaS, PaaS 및 IaaS 리소스의 구성 요소에 액세스하고 관리할 수 있는 사용자를 제어합니다.

   * 내부 애플리케이션 개발을 위해 애플리케이션 및 도구에 액세스할 수 있는 그룹을 관리합니다.

   * 고객을 위해 호스트되는 프로덕션 소프트웨어 또는 솔루션에 대한 액세스 권한이 있는 ID 필요

* **비즈니스 팀** 은 프로젝트를 관리하고 애플리케이션을 소유합니다. 이 팀은 다음 작업을 수행합니다. 

   * 내부 및 외부 사용자의 그룹 및 애플리케이션에 대한 액세스 권한을 검토하고 승인하거나 거부합니다.

   * 비즈니스 파트너와 같은 직원 및 외부 ID의 액세스 권한을 지속적으로 증명하기 위해 검토를 예약하고 수행합니다.

* **기업 거버넌스** 는 조직이 내부 정책과 규정을 준수하는지 확인합니다. 이 팀은 다음 작업을 수행합니다.

   * 새 액세스 권한의 검토를 요청하거나 예약합니다.

   * 규정 준수에 대한 설명서 및 기록을 포함하여 액세스를 검토하기 위한 프로세스 및 절차를 평가합니다.

   * 가장 중요한 리소스에 대한 이전 검토의 결과를 확인합니다.

> [!NOTE]
> 수동으로 평가가 필요한 검토의 경우 적절한 검토자를 계획하고 정책 및 규정 준수 요구 사항을 충족하는 검토 주기를 확인해야 합니다. 검토 주기가 너무 잦거나 검토자가 너무 적은 경우 품질이 저하되고 액세스할 수 있는 사람이 너무 많거나 너무 적을 수 있습니다. 

### <a name="plan-communications"></a>통신 계획

통신은 새로운 비즈니스 프로세스의 성공에 매우 중요합니다. 사용자 환경이 언제 어떻게 변화할 것인지, 그리고 문제가 발생할 경우 어떻게 지원을 받을지에 대해 사용자와 능동적으로 소통합니다. 

#### <a name="communicate-changes-in-accountability"></a>책임 변경 내용에 대한 커뮤니케이션

액세스 검토는 지속적인 액세스 권한을 검토하고 조치하는 책임이 비즈니스 소유자로 이동하는 것을 지원합니다. IT에서 액세스 결정을 분리하면 더욱 정확한 액세스 결정을 내리는 데 도움이 됩니다. 이는 리소스 소유자의 책임에 대한 문화적인 변화입니다. 변경 사항을 능동적으로 전달하고 리소스 소유자가 교육을 받고 인사이트를 활용하여 적절한 결정을 내릴 수 있도록 합니다.

분명히 IT 담당자는 모든 인프라 관련 액세스 의사 결정 및 권한 있는 역할 할당을 계속 제어하기를 원할 것입니다. 

#### <a name="customize-email-communication"></a>이메일 통신 사용자 지정

검토를 예약할 때 이 검토를 수행하는 사용자를 지명합니다. 그러면 해당 검토자는 할당된 검토가 만료되기 전에 새 검토에 대한 미리 알림에 더해 메일 알림도 받습니다. 

검토자에게 보내는 메일은 검토자가 검토 작업을 수행하도록 독려하는 간단한 메시지를 포함하는 방식으로 사용자 지정할 수 있습니다. 추가 텍스트를 사용하여 다음을 수행하는 것이 좋습니다.

* 검토자에게 보내는 개인 메시지를 포함하여 메시지가 규정 준수 또는 IT 부서에서 전송되었다는 사실을 알릴 수 있습니다.

* 검토 기대 사항과 관련된 내부 정보에 대한 참조 자료, 추가 참조자료 또는 교육 자료를 포함합니다.

  ![검토자 이메일](./media/deploy-access-review/2-plan-reviewer-email.png)

검토 시작을 선택하면 검토자는 그룹 및 애플리케이션 액세스 검토를 위해 [myAccess 포털](https://myapplications.microsoft.com/)로 이동합니다. 포털에서는 마지막 로그인 및 액세스 정보를 기반으로 하여 검토 중인 리소스에 대한 액세스 권한이 있는 모든 사용자와 시스템 권장 사항에 대한 개요를 제공합니다.

### <a name="plan-a-pilot"></a>파일럿 계획

처음에는 작은 그룹으로 액세스 검토를 파일럿하고 중요하지 않은 리소스를 대상으로 하는 것이 좋습니다. 파일럿을 통해 필요에 따라 프로세스 및 통신을 조정하고 보안 및 규정 준수 요구 사항을 충족할 수 있도록 사용자와 검토자의 능력을 향상할 수 있습니다.

파일럿에서 다음을 수행하는 것이 좋습니다.

* 결과가 자동으로 적용되지 않는 검토부터 시작하여 영향을 제어할 수 있습니다.

* 모든 사용자가 Azure AD에 유효한 이메일 주소 목록을 가지고 있고 적절한 조치를 취할 수 있도록 이메일 통신을 받는 것을 확인합니다. 

* 신속하게 복원해야 하는 경우를 대비해 파일럿의 일부로 제거된 모든 액세스를 문서화합니다.

* 감사 로그를 모니터링하여 모든 이벤트가 제대로 감사되는지 확인합니다.

자세한 내용은 [파일럿에 대한 모범 사례](../fundamentals/active-directory-deployment-plans.md)를 참조하세요.

## <a name="introduction-to-access-reviews"></a>액세스 검토 소개

이 섹션에서는 검토를 계획하기 전에 알고 있어야 하는 액세스 검토 개념을 소개합니다.

### <a name="what-resource-types-can-be-reviewed"></a>검토할 수 있는 리소스 종류는 무엇인가요?

조직의 리소스를 Azure AD와 통합하면(예: 사용자, 애플리케이션, 그룹) 관리와 검토가 가능합니다. 

검토할 일반적인 목표는 다음과 같습니다.

* [Single Sign-On을 위해 Azure AD와 통합된 애플리케이션](../manage-apps/what-is-application-management.md)(예: SaaS, LOB(기간 업무))

* 그룹 [멤버 자격](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)(Azure AD에 동기화되었거나 Microsoft Teams를 포함하여 Azure AD 또는 Microsoft 365에서 만들어짐)

* 액세스를 관리하기 위해 리소스(그룹, 앱, 사이트)를 단일 패키지로 그룹화하는 [액세스 패키지](./entitlement-management-overview.md)

* PIM(Privileged Identity Management)에 정의된 [Azure AD 역할 및 Azure 리소스 역할](../privileged-identity-management/pim-resource-roles-assign-roles.md)

### <a name="who-will-create-and-manage-access-reviews"></a>액세스 검토를 만들고 관리하는 사람은 누구인가요?

액세스 검토를 만들거나, 관리하거나, 읽는 데 필요한 관리 역할은 검토 중인 리소스의 유형에 따라 달라집니다. 다음 표에서는 각 리소스 유형에 필요한 역할을 나타냅니다.

| 리소스 종류| 액세스 검토 생성 및 관리(작성자)| 액세스 검토 결과 읽기 |
| - | - | -|
| 그룹 또는 애플리케이션| 전역 관리자 <p>사용자 관리자<p>Identity Governance 관리자<p>권한 있는 역할 관리자(Azure AD 역할 할당 가능 그룹에 대한 검토만 해당)<p>그룹 소유자([관리자가 사용하도록 설정한 경우]( create-access-review.md#allow-group-owners-to-create-and-manage-access-reviews-of-their-groups-preview))| 전역 관리자<p>전역 Reader<p>사용자 관리자<p>Identity Governance 관리자<p>권한 있는 역할 관리자<p>보안 Reader<p>그룹 소유자([관리자가 사용하도록 설정한 경우]( create-access-review.md#allow-group-owners-to-create-and-manage-access-reviews-of-their-groups-preview)) |
|Azure AD 역할| 전역 관리자 <p>권한 있는 역할 관리자|  전역 관리자<p>전역 Reader<p>사용자 관리자<p>권한 있는 역할 관리자<p> <p>보안 Reader |
| Azure 리소스 역할| 전역 관리자<p>리소스 소유자| 전역 관리자<p>전역 Reader<p>사용자 관리자<p>권한 있는 역할 관리자<p> <p>보안 Reader  |
| 액세스 패키지| 전역 관리자<p>사용자 관리자<p>Identity Governance 관리자| 전역 관리자<p>전역 Reader<p>사용자 관리자<p>Identity Governance 관리자<p> <p>보안 Reader  |

자세한 내용은 [Azure Active Directory의 관리자 역할 권한](../roles/permissions-reference.md)을 참조하세요.

### <a name="who-will-review-the-access-to-the-resource"></a>리소스에 대한 액세스를 검토하는 사람은 누구인가요?

액세스 검토 작성자는 검토를 작성할 때 누가 검토할 것인지 결정합니다. 검토가 시작된 후에는 이 설정을 변경할 수 없습니다. 검토자는 세 가지 가상 사용자로 표시됩니다.

* 리소스 소유자는 리소스의 비즈니스 소유자입니다.

* 액세스 검토 관리자가 선택하는 대로 개별적으로 선택된 대리인 세트입니다.

* 최종 사용자는 각 사용자에게 지속적으로 액세스하는 데 필요한 사항을 자체적으로 증명합니다.

* 관리자는 리소스에 대한 직접 보고서의 액세스를 검토합니다. 

액세스 검토를 작성할 때 관리자는 하나 이상의 검토자를 선택할 수 있습니다. 모든 검토자는 사용자를 선택하여 리소스에 대한 액세스를 계속하거나 제거하기 위한 검토를 시작하고 수행할 수 있습니다. 

### <a name="components-of-an-access-review"></a>액세스 검토의 구성 요소

액세스 검토를 구현하기 전에 조직과 관련된 검토 유형을 계획해야 합니다. 이를 위해서는 검토할 항목과 해당 검토에 따라 취할 조치에 대한 비즈니스 의사 결정을 해야 합니다.

액세스 검토 정책을 만들려면 다음 정보가 필요합니다.

* 검토할 리소스는 무엇인가요?

* 누구의 액세스를 검토하는 중인가요?

* 얼마나 자주 검토하나요?

* 검토를 수행하는 사람은 누구인가요?

   * 검토에 대한 통지는 어떻게 되나요?

   * 검토를 위해 적용되는 타임라인은 무엇인가요?

* 검토에 따라 적용해야 하는 자동 작업은 무엇인가요?

   * 검토자가 시간 내에 응답하지 않으면 어떻게 되나요?

* 검토에 따라 수동으로 수행해야 하는 작업은 무엇인가요?

* 수행된 작업에 따라 어떤 통신이 전송되나요?

**액세스 검토 계획 예제**

| 구성 요소| 값 |
| - | - |
| **검토할 리소스**| Microsoft Dynamics에 대한 액세스 |
| **검토 주기**| 매월 |
| **검토를 수행하는 사람**| Dynamics 비즈니스 그룹 프로그램 관리자 |
| **알림**| 검토를 시작할 때 별칭 Dynamics-Pms로 보낸 이메일<p>사용자 지정 메시지를 포함하여 검토자에게 자신의 구매를 보호하게 하세요. |
| **타임라인**| 알림으로부터 48시간 |
|**자동 작업**| 보안 그룹 dynamics-access에서 사용자를 제거하여 90일 이내에 대화형 로그인이 없는 모든 계정에서 액세스 권한을 제거합니다. <p>*타임라인 내에서 검토되지 않은 경우 작업을 수행합니다.* |
| **수동 작업**| 검토자는 원하는 경우 자동화된 작업 전에 제거 승인을 수행할 수 있습니다. |

### <a name="automate-actions-based-on-access-reviews"></a>액세스 검토를 기반으로 작업 자동화

리소스에 결과 자동 적용 옵션을 사용으로 설정하여 액세스 제거를 자동화하도록 선택할 수 있습니다.

  ![액세스 검토 계획](./media/deploy-access-review/3-automate-actions-settings.png)

검토가 완료 및 종료되고 나서 검토자가 승인하지 않은 사용자는 자동으로 리소스에서 제거되거나 계속 액세스를 유지합니다. 이는 그룹 멤버 자격 또는 해당 애플리케이션 할당을 제거하거나 권한 있는 역할로 승격할 수 있는 권한을 취소하는 것을 의미할 수 있습니다.

권장 사항 사용

권장 사항은 검토자 경험의 일부로 검토자에게 표시되며 테넌트에 대한 사용자의 마지막 로그인 또는 애플리케이션에 대한 마지막 액세스를 표시합니다. 해당 정보는 검토자가 올바른 액세스 결정을 내리는 데 도움을 줍니다. 권장 사항 사용을 선택하면 액세스 검토의 권장 사항을 따릅니다. 액세스 검토가 끝나면 시스템은 검토자가 응답하지 않은 사용자에 대해 이러한 권장 사항을 자동으로 적용합니다.

권장 사항은 액세스 검토의 조건을 기반으로 합니다. 예를 들어, 90일 동안 대화형 로그인을 사용하지 않으면 액세스를 제거하도록 검토를 구성하는 경우 해당 조건에 맞는 모든 사용자를 제거하도록 권장할 것입니다. Microsoft는 계속해서 권장 사항을 개선하기 위해 노력하고 있습니다. 

### <a name="review-guest-user-access"></a>게스트 사용자 액세스 검토

액세스 검토를 사용하여 외부 조직의 협업 파트너 ID를 검토하고 정리합니다. 파트너당 검토의 구성은 규정 준수 요구 사항을 충족할 수 있습니다.

외부 ID는 다음 작업 중 하나를 통해 회사 리소스에 대한 액세스 권한을 부여받을 수 있습니다.

* 그룹에 추가합니다. 

* 팀에 초대합니다. 

* 엔터프라이즈 애플리케이션 또는 액세스 패키지를 할당합니다.

* Azure AD 또는 Azure 구독에 권한 있는 역할을 할당합니다.

[샘플 스크립트](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)를 참조하세요. 이 스크립트는 테넌트에 초대된 외부 ID가 어디에서 사용되는지 보여 줍니다. Azure AD에서 외부 사용자의 그룹 멤버 자격, 역할 할당, 애플리케이션 할당을 확인할 수 있습니다. 이 스크립트는 그룹을 사용하지 않고 Sharepoint 리소스에 대한 직접 권한 할당과 같은 Azure AD 외부의 할당을 표시하지 않습니다.

그룹이나 애플리케이션에 대한 액세스 검토를 만들 때 검토자가 액세스 권한이 있는 모든 사용자 또는 게스트 사용자에게만 집중하도록 선택할 수 있습니다. 게스트 사용자만을 선택하면 검토자에게는 리소스 액세스 권한이 있는 Azure AD B2B의 외부 ID에 집중된 목록이 제공됩니다.

 ![게스트 사용자 검토](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> 멤버의 userType을 가진 외부 멤버는 포함되지 않습니다. 또한 Azure AD B2B 공동 작업 외부에서 초대받은 사용자(예: SharePoint를 통해 직접 공유 콘텐츠에 액세스할 수 있는 사용자)는 포함되지 않습니다.

## <a name="plan-access-reviews-for-access-packages"></a>액세스 패키지에 대한 액세스 검토 계획

[액세스 패키지](entitlement-management-overview.md)는 거버넌스 및 액세스 검토 전략을 크게 간소화할 수 있습니다. 액세스 패키지는 사용자가 프로젝트에서 작업하거나 작업을 수행하는 데 필요한 액세스 권한이 있는 모든 리소스의 번들입니다. 예를 들어, 조직의 개발자가 필요로 하는 모든 애플리케이션이나 외부 사용자가 액세스할 수 있는 모든 애플리케이션을 포함하는 액세스 패키지를 생성한다고 해보겠습니다. 관리자 또는 위임된 액세스 패키지 관리자는 리소스(그룹 또는 앱)와 해당 리소스에 대해 사용자가 필요로 하는 역할을 그룹화합니다.

[액세스 패키지를 만들](entitlement-management-access-package-create.md) 때 사용자가 액세스 패키지를 요청할 수 있는 조건, 승인 프로세스의 형태 및 사용자가 액세스를 다시 요청해야 하는 빈도를 설정하는 액세스 정책을 하나 이상 만들 수 있습니다. 액세스 검토는 액세스 패키지 정책을 만들거나 편집하는 동안 구성됩니다.

수명 주기 탭을 열고 아래로 스크롤하여 검토에 액세스합니다.

 !["수명 주기" 탭의 "정책 편집"을 보여 주는 스크린샷](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>그룹에 대한 액세스 검토 계획

액세스 패키지 외에도 그룹 멤버 자격 검토는 액세스를 관리하는 가장 효과적인 방법입니다. 리소스에 대한 액세스를 [보안 그룹 또는 Microsoft 365 그룹](../fundamentals/active-directory-manage-groups.md)을 통해 할당하고 해당 그룹에 사용자를 추가하여 액세스 권한을 얻을 수 있도록 하는 것이 좋습니다.

단일 그룹에 해당하는 모든 리소스에 대한 액세스 권한을 부여할 수 있습니다. 개별 리소스 또는 애플리케이션 및 기타 리소스를 그룹화하는 액세스 패키지에 그룹 액세스 권한을 할당할 수 있습니다. 해당 방법을 사용하여 각 애플리케이션에 대한 개별 액세스 권한이 아닌 그룹에 대한 액세스를 검토할 수 있습니다. 

다음을 통해 그룹 멤버 자격을 검토할 수 있습니다. 

* 관리자

* 그룹 소유자

* 검토가 생성될 때 선택한 사용자, 위임된 검토 기능

* 자체 증명 그룹의 구성원

* 관리자는 직접 보고서의 액세스를 검토합니다. 

### <a name="group-ownership"></a>그룹 소유권

그룹 소유자는 액세스해야 하는 사용자를 파악하기 가장 적절한 위치에 있으므로 그룹 소유자가 멤버 자격을 검토하는 것을 추천합니다. 그룹의 소유권은 그룹의 유형과 다릅니다.

Microsoft 365 및 Azure AD에서 생성된 그룹에는 잘 정의된 소유자가 하나 이상 있습니다. 대부분의 경우 소유자는 누가 액세스 권한을 가져야 하는지 알기 때문에 자신의 그룹에 대한 완벽한 검토자이기도 합니다. 

예를 들어, Microsoft Teams는 기본 권한 부여 모델로 Microsoft 365 그룹을 사용하여 SharePoint, Exchange, OneNote 또는 기타 Microsoft 365 서비스에 있는 리소스에 대한 액세스 권한을 사용자에게 부여합니다. 팀의 작성자는 자동으로 소유자가 되며 해당 그룹의 멤버 자격을 증명해야 하는 책임이 있습니다. 

Azure AD 포털에서 수동으로 만들어지거나 Microsoft Graph에서의 스크립팅을 통해 생성된 그룹에 반드시 정의된 소유자가 있는 것은 아닙니다. Azure AD 포털에 있는 그룹의 “소유자” 섹션에서 또는 Graph를 통해 정의하는 것이 좋습니다.

온-프레미스 Active Directory에서 동기화된 그룹에는 Azure AD의 소유자가 있을 수 없습니다. 액세스 검토를 만드는 경우, 멤버 자격을 결정하는 데 가장 적합한 개인을 선택해야 합니다.

> [!NOTE]
> 멤버 자격에 대한 정기적인 검토를 위해 그룹 소유권과 책임이 명확하게 유지되도록 그룹을 만드는 방법을 나타내는 비즈니스 정책을 정의하는 것이 좋습니다. 

### <a name="review-membership-of-exclusion-groups-in-conditional-access-policies"></a>조건부 액세스 정책에서 제외 그룹의 멤버 자격 검토 

제외 그룹의 구성원을 검토하는 방법을 알아보려면 [Azure AD 액세스 검토를 사용하여 조건부 액세스 정책에서 제외된 사용자 관리](conditional-access-exclusion.md)로 이동하세요.

### <a name="review-guest-users-group-memberships"></a>게스트 사용자의 그룹 멤버 자격 검토

그룹 멤버 자격에 대한 게스트 사용자의 액세스를 검토하는 방법을 알아보려면 [Azure AD 액세스 검토로 게스트 액세스 관리](./manage-guest-access-with-access-reviews.md)로 이동하세요.

### <a name="review-access-to-on-premises-groups"></a>온-프레미스 그룹에 대한 액세스 검토

액세스 검토는 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)를 사용하여 온-프레미스에서 동기화하는 그룹의 멤버 자격을 변경할 수 없습니다. 이는 인증 소스가 온-프레미스이기 때문입니다.

여전히 액세스 검토를 사용하여 온-프레미스 그룹의 정기 검토를 예약하고 유지 관리할 수 있습니다. 이후 검토자가 온-프레미스 그룹에서 작업을 수행합니다. 해당 전략에서는 액세스 검토를 모든 검토에 대한 도구로 유지합니다.

온-프레미스 그룹에 대한 액세스 검토의 결과를 사용하고 다음을 수행하여 그 결과를 추가적으로 처리할 수 있습니다.

* 액세스 검토에서 CSV 보고서를 다운로드하고 수동으로 작업을 수행합니다.

* Microsoft Graph를 사용하여 완료된 액세스 검토에서 결과 및 결정 사항에 프로그래밍 방식으로 액세스합니다.

예를 들어 Windows AD 관리 그룹에 대한 결과에 액세스하려면 이 [PowerShell 샘플 스크립트](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)를 사용합니다. 이 스크립트는 필요한 그래프 호출을 간략하게 설명하고 변경 내용을 수행하는 Windows AD-PowerShell 명령을 내보냅니다.

## <a name="plan-access-reviews-for-applications"></a>애플리케이션에 대한 액세스 검토 계획 

애플리케이션에 대한 액세스를 검토하는 경우, 애플리케이션 내의 정보 및 데이터에 대한 직원과 외부의 ID 액세스를 검토하게 됩니다. 특정 애플리케이션에 대한 액세스 권한이 있는 사용자를 알아야 하는 경우 액세스 패키지 또는 그룹이 아니라 애플리케이션을 검토하세요. 

다음 시나리오에서 애플리케이션에 대한 검토를 계획하는 것이 좋습니다.

* 사용자에게는 (그룹 또는 액세스 패키지 외부의) 애플리케이션에 직접 액세스할 수 있는 권한이 부여됩니다.

* 애플리케이션은 중요하거나 민감한 정보를 노출합니다.

* 애플리케이션에는 증명해야 하는 특정 규정 준수 요구 사항이 있습니다.

* 부적합한 액세스에 주의합니다.

애플리케이션에 대한 액세스 검토를 만들려면 필요한 사용자 할당을 설정합니다. 속성을 예로 설정합니다. 아니요로 설정한 경우 외부 ID를 포함하여 디렉터리의 모든 사용자가 애플리케이션에 액세스할 수 있으며 애플리케이션에 대한 액세스를 검토할 수 없습니다. 

 ![앱 할당 계획](./media/deploy-access-review/6-plan-applications-assignment-required.png)

그런 다음 액세스 권한이 있는 [사용자 및 그룹을 할당](../manage-apps/assign-user-or-group-access-portal.md)해야 합니다. 

### <a name="reviewers-for-an-application"></a>애플리케이션 검토자

액세스 검토는 애플리케이션에 할당된 그룹 구성원이거나 사용자일 수 있습니다. Azure AD의 애플리케이션에 반드시 소유자가 있어야 하는 것은 아닙니다. 따라서 애플리케이션 소유자를 검토자로 선택하는 옵션을 사용할 수 없습니다. 모든 액세스 권한을 검토하는 대신 애플리케이션에 할당된 게스트 사용자만 검토하도록 범위를 지정할 수 있습니다.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Azure AD 및 Azure 리소스 역할의 계획 검토

[PIM(Privileged Identity Management)](../privileged-identity-management/pim-configure.md)은 기업이 Azure AD에서 리소스에 대해 권한 있는 액세스를 관리하는 방법을 간소화합니다. 이렇게 하면 [Azure AD](../roles/permissions-reference.md) 및 [Azure 리소스](../../role-based-access-control/built-in-roles.md) 모두에서 권한 있는 역할 목록이 훨씬 작아지고 디렉터리의 전체 보안이 강화됩니다.

액세스 검토를 통해 검토자는 사용자가 여전히 역할을 부여받아야 하는지 여부를 증명할 수 있습니다. 액세스 패키지에 대한 액세스 검토와 마찬가지로 Azure AD 역할 및 Azure 리소스에 대한 검토가 PIM 관리 사용자 환경에 통합되어 있습니다. 정기적으로 다음 역할 할당을 검토하는 것이 좋습니다.

* 전역 관리자

* 사용자 관리자

* 권한 있는 인증 관리자

* 조건부 액세스 관리자

* 보안 관리자

* 모든 Microsoft 365 및 Dynamics 서비스 관리 역할

검토되는 역할에는 영구적이고 적격 할당이 포함됩니다. 

검토자 섹션에서 모든 사용자를 검토할 한 명 이상의 사용자를 선택합니다. 또는 구성원이 자신의 액세스 권한을 검토하도록 할 수도 있습니다.

 ![정책 편집](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>액세스 검토 배포

Azure AD와 통합된 리소스에 대한 액세스를 검토하는 전략 및 계획을 준비한 후에는 아래 리소스를 사용하여 리뷰를 배포하고 관리합니다.

### <a name="review-access-packages"></a>액세스 패키지 검토

관리자는 오래된 액세스의 위험을 줄이기 위해 액세스 패키지에 대한 활성 할당을 가진 사용자를 정기적으로 검토할 수 있습니다. 아래 링크의 지침을 따르세요.

| 방법 문서| Description |
| - | - |
| [액세스 검토 만들기](entitlement-management-access-reviews-create.md)| 액세스 패키지의 검토를 사용하도록 설정합니다. |
| [액세스 검토 수행](entitlement-management-access-reviews-review-access.md)| 액세스 패키지에 할당된 다른 사용자에 대한 액세스 검토를 수행합니다. |
| [액세스 패키지에 할당된 자체 검토](entitlement-management-access-reviews-self-review.md)| 할당된 액세스 패키지의 자체 검토 |

> [!NOTE]
> 자체 검토하고 더 이상 액세스하지 않아도 된다는 최종 사용자는 액세스 패키지에서 즉시 제거되지 않고, 검토가 종료되거나 관리자가 검토를 중지한 경우 액세스 패키지에서 제거됩니다.

### <a name="review-groups-and-apps"></a>그룹 및 앱 검토

직원 및 게스트의 그룹 및 애플리케이션에 대한 액세스는 시간이 지남에 따라 변경될 수 있습니다. 오래된 액세스 할당과 관련된 위험을 줄이기 위해 관리자는 그룹 구성원 또는 애플리케이션 액세스에 대한 검토를 만들 수 있습니다. 아래 링크의 지침을 따르세요.

| 방법 문서| Description |
| - | - |
| [액세스 검토 만들기](create-access-review.md)| 그룹 구성원 또는 애플리케이션 액세스 권한에 대한 액세스 검토를 하나 이상 만듭니다. |
| [액세스 검토 수행](perform-access-review.md)| 그룹 구성원이나 애플리케이션에 대한 액세스 권한이 있는 사용자의 액세스 검토를 수행합니다. |
| [액세스 권한 자체 검토](review-your-access.md)| 멤버는 그룹 또는 애플리케이션에 대한 자신의 액세스 권한을 검토합니다. |
| [액세스 검토 완료](complete-access-review.md)| 액세스 검토 보기 및 결과 적용 |
| [온-프레미스 그룹에 대한 작업 수행](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| 온-프레미스 그룹에 대한 액세스 검토를 수행하는 샘플 PowerShell 스크립트입니다. |

### <a name="review-azure-ad-roles"></a>Azure AD 역할 검토

오래된 역할 할당과 관련된 위험을 줄이려면 권한 있는 Azure AD 역할에 대한 액세스를 정기적으로 검토해야 합니다.

![Azure AD 역할의 "멤버 자격 검토" 목록을 보여 주는 스크린샷](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

아래 링크의 지침을 따르세요.

| 방법 문서 | Description |
| - | - |
 [액세스 검토 만들기](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| PIM에서 권한 있는 Azure AD 역할에 대한 액세스 검토 만들기 |
| [액세스 권한 자체 검토](../privileged-identity-management/pim-perform-azure-ad-roles-and-resource-roles-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 관리 역할에 할당된 경우 역할에 대한 액세스를 승인하거나 거부합니다. |
| [액세스 검토 완료](../privileged-identity-management/pim-complete-azure-ad-roles-and-resource-roles-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 액세스 검토 보기 및 결과 적용 |


### <a name="review-azure-resource-roles"></a>Azure 리소스 역할 검토

오래된 역할 할당과 관련된 위험을 줄이려면 권한 있는 Azure 리소스 역할에 대한 액세스를 정기적으로 검토해야 합니다. 

![Azure AD 역할 검토](./media/deploy-access-review/9-review-azure-roles-picker.png)

아래 링크의 지침을 따르세요.

| 방법 문서| Description |
| - | -|
| [액세스 검토 만들기](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| PIM에서 권한 있는 Azure 리소스 역할에 대한 액세스 검토 만들기 |
| [액세스 권한 자체 검토](../privileged-identity-management/pim-perform-azure-ad-roles-and-resource-roles-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 관리 역할에 할당된 경우 역할에 대한 액세스를 승인하거나 거부합니다. |
| [액세스 검토 완료](../privileged-identity-management/pim-complete-azure-ad-roles-and-resource-roles-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| 액세스 검토 보기 및 결과 적용 |


## <a name="use-the-access-reviews-api"></a>액세스 검토 API 사용

검토할 수 있는 리소스와 상호 작용하고 이를 관리하려면 [그래프 API 메서드](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) 및 [역할 및 애플리케이션 권한 부여 검사](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)를 참조하세요. Microsoft Graph API의 액세스 검토 방법은 애플리케이션 및 사용자 컨텍스트 모두에 사용할 수 있습니다. 애플리케이션 컨텍스트에서 스크립트를 실행하는 경우 API(서비스 원칙)를 실행하는 데 사용되는 계정에 “AccessReview.Read.All”의 사용 권한을 부여해 액세스 검토 정보를 쿼리하게 합니다.

액세스 검토를 위해 Graph API를 사용하여 자동화하는 인기 액세스 검토 작업은 다음과 같습니다.

* 액세스 검토를 만들고 시작합니다.

* 예약된 종료가 실행되기 전에 액세스 검토를 수동으로 종료합니다.

* 실행 중인 모든 액세스 검토 및 해당 상태를 나열합니다.

* 검토 시리즈의 기록과 각 검토에서 수행된 결정 및 작업을 확인합니다.

* 액세스 검토에서 결정을 수집합니다.

* 완료된 검토 중 검토자가 시스템에서 권장하는 다르게 내린 결정을 수집합니다.

자동화를 위해 새 Graph API 쿼리를 만들 때 [Graph 탐색기](https://developer.microsoft.com/en-us/graph/graph-explorer)를 사용하는 것이 좋습니다. Graph 쿼리를 스크립트 및 코드에 배치하기 전에 빌드하고 탐색할 수 있습니다. 이를 통해 스크립트 코드를 변경하지 않고도 원하는 결과를 정확하게 얻을 수 있도록 쿼리를 신속하게 반복할 수 있습니다.

## <a name="monitor-access-reviews"></a>액세스 검토 모니터링

액세스 검토 작업은 [Azure AD의 감사 로그](../reports-monitoring/concept-audit-logs.md)에 사용할 수 있도록 기록됩니다. 범주, 활동 유형, 날짜 범위에서 감사 데이터를 필터링 할 수 있습니다. 쿼리 샘플은 다음과 같습니다.

| 범주| 정책 |
| - | - |
| 활동 유형| 액세스 검토 생성 |
| | 액세스 검토 업데이트 |
| | 액세스 검토 종료 |
| | 액세스 검토 삭제 |
| | 결정 승인 |
| | 결정 거부 |
| | 결정 다시 설정 |
| | 결정 적용 |
| 날짜 범위| 7일 |


액세스 검토에 대한 고급 쿼리 및 분석, 검토의 변경 및 완료를 추적하려면 Azure AD 감사 로그를 [Azure Log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) 또는 Azure 이벤트 허브로 내보내는 것이 좋습니다. Azure Log Analytics에 저장되면 [강력한 분석 언어](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)를 사용하고 고유의 대시보드를 빌드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

아래 관련 기술에 대해 알아봅니다.

* [Azure AD 권한 관리란?](entitlement-management-overview.md)

* [Azure AD Privileged Identity Management란?](../privileged-identity-management/pim-configure.md)