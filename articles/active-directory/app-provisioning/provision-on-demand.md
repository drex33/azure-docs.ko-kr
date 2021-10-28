---
title: Azure Active Directory를 사용하는 주문형 사용자 프로비전
description: Azure Active Directory에서 주문형 사용자를 프로비전하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 021e582887ec2927fa1e3180c41c522bd0e09fbf
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990960"
---
# <a name="on-demand-provisioning-in-azure-active-directory"></a>Azure Active Directory의 주문형 프로비저닝
주문형 프로비저닝을 사용하여 사용자를 몇 초 내에 애플리케이션에 프로비전합니다. 이 기능을 사용하는 주요 목적은 다음과 같습니다.

* 구성 문제를 신속하게 해결합니다.
* 정의한 식의 유효성을 검사합니다.
* 범위 지정 필터를 테스트합니다.

## <a name="how-to-use-on-demand-provisioning"></a>주문형 프로비저닝 사용 방법

1. **Azure Portal** 에 로그인합니다.
1. **모든 서비스** > **엔터프라이즈 애플리케이션** 으로 이동합니다.
1. 애플리케이션을 선택한 후 프로비저닝 구성 페이지로 이동합니다.
1. 관리 자격 증명을 제공하여 프로비저닝을 구성합니다.
1. **주문형 프로비전** 을 선택합니다.
1. 이름, 성, 표시 이름, 사용자 주체 이름 또는 이메일 주소로 사용자를 검색합니다.
   > [!NOTE]
   > Cloud HR 프로비저닝 앱(Workday/SuccessFactors to AD/Azure AD)의 경우 입력 값이 다릅니다. Workday 시나리오의 경우 Workday에서 사용자의 “WID”를 제공하세요. SuccessFactors 시나리오의 경우 SuccessFactors에 있는 사용자의 "personIdExternal"을 제공하세요. 
 
1. 페이지 아래쪽에서 **프로비전** 을 선택합니다.

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="사용자를 주문형으로 프로비전하기 위한 Azure Portal UI를 보여주는 스크린샷입니다.":::

## <a name="understand-the-provisioning-steps"></a>프로비저닝 단계 이해

주문형 프로비저닝 프로세스는 사용자를 프로비전할 때 프로비저닝 서비스에서 수행되는 단계를 보여주려고 시도합니다. 일반적으로 사용자 프로비전을 위해서는 5개 단계가 있습니다. 다음 섹션에서 설명하는 이러한 단계 중 일부는 주문형 프로비저닝 경험 중에 표시됩니다.

### <a name="step-1-test-connection"></a>1단계: 연결 테스트

프로비저닝 서비스는 “테스트 사용자”에 대해 요청을 수행하여 대상 애플리케이션에 대해 액세스 권한을 부여하려고 시도합니다. 프로비저닝 서비스는 해당 서비스에서 프로비전 단계를 계속하도록 권한이 부여되었음을 나타내는 응답이 필요합니다. 이 단계는 실패한 경우에만 표시됩니다. 이 단계가 성공하면 주문형 프로비저닝 경험 중에 표시되지 않습니다.

#### <a name="troubleshooting-tips"></a>문제 해결 팁

* 비밀 토큰 및 테넌트 URL과 같은 유효한 자격 증명을 대상 애플리케이션에 제공했는지 확인합니다. 필요한 자격 증명은 애플리케이션마다 다릅니다. 자세한 구성 자습서는 [자습서 목록](../saas-apps/tutorial-list.md)을 참조하세요. 
* 대상 애플리케이션에서 **특성 매핑** 창에 정의된 일치하는 특성에 따른 필터링이 지원되는지 확인합니다. 지원되는 필터를 확인하려면 애플리케이션 개발자가 제공한 API 설명서를 확인해야 할 수 있습니다.
* SCIM(System for Cross-domain Identity Management) 애플리케이션의 경우 Postman과 같은 도구를 사용할 수 있습니다. 이러한 도구를 사용하여 애플리케이션이 Azure AD(Azure Active Directory) 프로비저닝 서비스에 예상되는 방식으로 권한 부여 요청에 응답하는지 확인할 수 있습니다. [예제 요청](./use-scim-to-provision-users-and-groups.md#request-3)을 확인하세요.

### <a name="step-2-import-user"></a>2단계: 사용자 가져오기

그런 후 프로비저닝 서비스가 소스 시스템에서 사용자를 검색합니다. 서비스가 검색하는 사용자 특성은 나중에 다음 목적으로 사용됩니다.

* 사용자가 프로비저닝 범위에 있는지 평가합니다.
* 기존 사용자의 대상 시스템을 확인합니다.
* 대상 시스템으로 내보낼 사용자 특성을 결정합니다.

#### <a name="view-details"></a>세부 정보 보기


**세부 정보 보기** 섹션에는 소스 시스템(예: Azure AD)에서 가져온 사용자의 속성이 표시됩니다.

#### <a name="troubleshooting-tips"></a>문제 해결 팁

* 소스 시스템의 사용자 객체에 일치하는 속성이 누락된 경우 사용자 가져오기가 실패할 수 있습니다. 이 오류를 해결하기 위해서는 다음 방법 중 하나를 시도하세요.

  * 일치하는 특성의 값으로 사용자 개체를 업데이트합니다.
  * 프로비저닝 구성에서 일치하는 특성을 변경합니다.

* 필요한 특성이 가져온 목록에서 누락된 경우 소스 시스템의 사용자 개체에 대한 값이 특성에 포함되었는지 확인합니다. 프로비저닝 서비스는 현재 null 특성의 프로비전을 지원하지 않습니다.
* 프로비저닝 구성의 **특성 매핑** 페이지에 필요한 특성이 포함되었는지 확인합니다.

### <a name="step-3-determine-if-user-is-in-scope"></a>3단계: 사용자가 범위 내에 있는지 확인

그런 후 프로비저닝 서비스는 사용자가 프로비저닝 [범위](./how-provisioning-works.md#scoping) 내에 있는지 확인합니다. 이 서비스는 다음과 같은 특성을 고려합니다.

* 사용자가 애플리케이션에 할당되었는지 여부.
* 범위가 **할당된 항목 동기화** 또는 **모두 동기화** 로 설정되었는지 여부.
* 프로비저닝 구성에 정의된 범위 필터.  

#### <a name="view-details"></a>세부 정보 보기

**세부 정보 보기** 섹션에는 평가된 범위 조건이 표시됩니다. 다음 속성 중 하나 이상이 표시될 수 있습니다.

* **원본 시스템에서 활성** 은 Azure AD에서 `IsActive` 속성이 **true** 로 설정되었음을 나타냅니다.
* **애플리케이션에 할당됨** 은 Azure AD에서 사용자가 애플리케이션에 할당되었음을 나타냅니다.
* **모두 동기화 범위** 는 범위 설정에 따라 테넌트에 있는 모든 사용자 및 그룹이 허용됨을 나타냅니다.
* **사용자에게 필요한 역할 포함** 은 애플리케이션에 프로비전하는 데 필요한 역할이 사용자에게 있음을 나타냅니다. 
* **범위 필터** 는 애플리케이션에 범위 필터를 정의한 경우에도 표시됩니다. 필터는 {scoping filter title} {scoping filter attribute} {scoping filter operator} {scoping filter value} 형식으로 표시됩니다.

#### <a name="troubleshooting-tips"></a>문제 해결 팁

* 유효한 범위 역할을 정의했는지 확인합니다. 예를 들어 [Greater_Than 연산자](./define-conditional-rules-for-provisioning-user-accounts.md#create-a-scoping-filter)에 정수가 아닌 값을 사용하지 마세요.
* 사용자에게 필요한 역할이 없으면 [기본 액세스 역할에 할당된 사용자 프로비전 팁](./application-provisioning-config-problem-no-users-provisioned.md#provisioning-users-assigned-to-the-default-access-role)을 검토하세요.

### <a name="step-4-match-user-between-source-and-target"></a>4단계: 원본 및 대상 간 사용자 일치

이 단계에서는 서비스가 가져오기 단계에서 검색된 사용자를 대상 시스템의 사용자와 일치시키려고 시도합니다.

#### <a name="view-details"></a>세부 정보 보기

**세부 정보 보기** 페이지에는 대상 시스템에서 일치된 사용자의 속성이 표시됩니다. 컨텍스트 창에 표시되는 속성은 다음과 같이 다양합니다.

* 대상 시스템에서 일치하는 사용자가 없으면 속성이 표시되지 않습니다.
* 대상 시스템에 일치하는 사용자가 한 명 있으면 대상 시스템에서 해당 일치하는 사용자의 속성이 표시됩니다.
* 여러 사용자가 일치한 경우 일치한 사용자 모두의 속성이 표시됩니다.
* 여러 일치 특성이 특성 매핑에 포함된 경우 각 일치 특성을 순차적으로 평가하고, 해당 특성의 일치 사용자가 표시됩니다.

#### <a name="troubleshooting-tips"></a>문제 해결 팁

* 프로비저닝 서비스가 대상 시스템의 사용자와 고유하게 일치하는 사용자를 원본 시스템에서 찾을 수 없습니다. 이 문제를 해결하려면 일치하는 특성이 고유한지 확인합니다.
* 대상 애플리케이션에서 일치 특성으로 정의된 특성에 대한 필터가 지원되는지 확인합니다.  

### <a name="step-5-perform-action"></a>5단계: 작업 수행

마지막으로 프로비저닝 서비스는 사용자 만들기, 업데이트, 삭제 또는 건너뛰기와 같은 작업을 수행합니다.

다음은 사용자에 대한 주문형 프로비저닝이 성공한 후 표시될 수 있는 결과에 대한 예시입니다.

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="사용자에 대해 성공한 주문형 프로비저닝을 보여주는 스크린샷입니다.":::

#### <a name="view-details"></a>세부 정보 보기

**세부 정보 보기** 섹션에는 대상 애플리케이션에서 수정된 특성이 표시됩니다. 이 표시는 프로비저닝 서비스 작업의 최종 결과와 내보낸 특성을 보여줍니다. 이 단계가 실패하면 표시된 특성에 프로비저닝 서비스가 수정하려고 시도한 특성이 나타납니다.

#### <a name="troubleshooting-tips"></a>문제 해결 팁

* 변경 내용 내보내기 오류는 매우 다양할 수 있습니다. 일반적인 오류는 [프로비저닝 로그 설명서](../reports-monitoring/concept-provisioning-logs.md#error-codes)를 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

* **주문형 프로비저닝을 사용하려면 프로비저닝을 해제해야 하나요?** 장기 실행 전달자 토큰 또는 권한 부여를 위해 사용자 이름과 암호를 사용하는 애플리케이션의 경우 추가 단계가 필요하지 않습니다. 현재까지 권한 부여를 위해 OAuth를 사용하는 애플리케이션의 경우 주문형 프로비저닝을 사용하기 전 프로비저닝 작업을 중지해야 합니다. G Suite, Box, Workplace by Facebook 및 Slack과 같은 애플리케이션이 이 범주에 포함됩니다. 프로비저닝 작업을 중지하지 않고도 모든 애플리케이션에 대해 주문형 프로비저닝을 지원하기 위한 작업이 진행되고 있습니다.

* **주문형 프로비저닝은 얼마나 오래 걸리나요?** 주문형 프로비저닝은 일반적으로 30초 이내로 수행됩니다.

## <a name="known-limitations"></a>알려진 제한 사항

현재까지 주문형 프로비저닝에 대해 몇 가지 알려진 제한 사항이 있습니다. 다음에 수행할 기능 개선을 선택하는 데 도움이 될 수 있도록 귀하의 [제안 및 피드백](https://aka.ms/appprovisioningfeaturerequest)을 게시해주시기 바랍니다.

> [!NOTE]
> 주문형 프로비저닝 기능과 관련된 제한 사항은 다음과 같습니다. 애플리케이션에서 프로비저닝 그룹, 삭제 또는 기타 기능이 지원되는지 여부를 알아보려면 해당 애플리케이션의 자습서를 확인하세요.

* AWS(Amazon Web Services) 애플리케이션은 주문형 프로비저닝을 지원하지 않습니다. 
* 그룹 및 역할의 주문형 프로비저닝은 지원되지 않습니다.
* 주문형 프로비저닝은 애플리케이션에서 할당 해제된 사용자의 해제를 지원합니다. 하지만 Azure AD에서 사용하지 않도록 설정되었거나 삭제된 사용자의 해제 또는 삭제를 지원하지 않습니다. 이러한 사용자는 사용자를 검색할 때 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [프로비저닝 문제 해결](./application-provisioning-config-problem.md)