---
title: Azure Active Directory B2C 청구 모델
description: Azure AD B2C의 MAU(월간 활성 사용자) 청구 모델, Azure AD B2C 테넌트를 Azure 구독에 연결하는 방법 및 적절한 프리미엄 계층 가격을 선택하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 08/26/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 7b0b5a35a85a8b0564112d62401c722e65505f2e
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123186230"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Azure Active Directory B2C 청구 모델

Azure AD B2C(Azure Active Directory B2C) 가격 책정은 MAU(월간 활성 사용자)를 기반으로 하며, 이는 한 달 내에 인증 활동이 있는 고유한 사용자의 수입니다. 이 청구 모델은 Azure AD B2C 테넌트 및 [Azure AD 게스트 사용자 협업(B2B)](../active-directory/external-identities/external-identities-pricing.md)에 모두 적용됩니다. MAU 청구는 무료 계층 및 유연하고 예측 가능한 가격을 제공하므로 비용을 절감할 수 있습니다. 이 문서에서는 MAU 청구, Azure AD B2C 테넌트와 구독 연결, 가격 책정 계층 변경에 대해 알아봅니다.

## <a name="mau-overview"></a>MAU 개요

MAU(월간 활성 사용자)는 지정 된 월 내에 인증을 수행하는 고유 사용자입니다. 지정된 월 내에 여러 번 인증하는 사용자는 하나의 MAU로 계산 됩니다. 고객은 해당 월 중에 MAU의 후속 인증에 대한 요금이나 비활성 사용자에 대한 요금이 부과되지 않습니다. 인증은 다음을 포함할 수 있습니다.

- [등록 또는 로그인](add-sign-up-and-sign-in-policy.md), [셀프 서비스 암호 재설정](add-password-reset-policy.md), [프로필 편집](add-profile-editing-policy.md), [사용자 흐름](user-flow-overview.md) 또는 [사용자 지정 정책](custom-policy-overview.md) 등을 통해 사용자가 활성화한 대화형 로그인.
- [SSO(Single Sign-On)](session-behavior.md)와 같은 수동, 비 대화형 로그인, 또는 인증 코드 흐름, 토큰 새로 고침 등 모든 유형의 토큰 획득 또는 [ROPC(리소스 소유자 암호 자격 증명)](add-ropc-policy.md).

음성 및 SMS에 대해 MFA(다단계 인증)를 사용하여 더 높은 수준의 보증을 제공하는 것을 선택하는 경우 로그인 성공 또는 실패 여부와 관계 없이 해당 월의 각 MFA 시도에 대해 전 세계 고정 요금이 부과됩니다. 

> [!IMPORTANT]
> 이 문서에는 가격 책정 정보가 포함되어 있지 않습니다. 사용 요금 청구 및 가격 책정에 대한 최신 정보는 [Azure Active Directory B2C 가격 책정](https://azure.microsoft.com/pricing/details/active-directory-b2c/)을 참조하세요. Azure AD B2C 서비스를 사용할 수 있는 위치 및 사용자 데이터가 저장되는 위치에 대한 자세한 내용은 [Azure AD B2C 지역 가용성 및 데이터 보존](data-residency.md)을 참조하세요.

## <a name="what-do-i-need-to-do"></a>무엇을 해야 하나요?

MAU 청구 기능을 사용하려면 Azure AD B2C 테넌트가 Azure 구독에 연결되어 있어야 합니다. 위험 기반 조건부 액세스 정책과 같은 Azure AD B2C 프리미엄 P2 기능을 사용하려는 경우 Azure AD B2C 테넌트를 다른 가격 책정 계층으로 전환해야 할 수도 있습니다.

|테넌트 ID는 다음과 같습니다.  |그러려면 다음 작업을 수행해야 합니다.  |
|---------|---------|
| Azure AD B2C 테넌트에 이미 MAU 기준으로 청구됨     | 아무 작업도 하지 않습니다. 사용자가 Azure AD B2C 테넌트에 인증하면 MAU 기반 청구 모델을 사용하여 자동으로 청구됩니다.        |
| Azure AD B2C 테넌트가 아직 구독에 연결되지 않음     |  [구독에 Azure AD B2C 테넌트를 연결](#link-an-azure-ad-b2c-tenant-to-a-subscription)하여 MAU 청구를 활성화하세요.     |
| 2019년 11월 1일 이전에 구독에 연결된 Azure AD B2C 테넌트    | [MAU 청구(권장)로 전환](#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)하거나 인증별 청구 모델을 유지합니다.     |
| Azure AD B2C 테넌트이고 프리미엄 기능(예: 위험 기반 조건부 액세스 정책)을 사용하려는 경우    | 사용하려는 기능을 지원하는 [Azure AD 가격 책정 계층으로 변경](#change-your-azure-ad-pricing-tier)합니다.        |
|  |  |

## <a name="about-the-monthly-active-users-mau-billing-model"></a>MAU(월간 활성 사용자) 청구 모델 정보

MAU 청구는 **2019년 11월 1일** 에 Azure AD B2C 테넌트에 적용되었습니다. 이 날짜 또는 그 이후에 만들어 구독에 연결한 모든 Azure AD B2C 테넌트는 MAU 기준으로 청구되었습니다. 구독에 연결하지 않은 Azure AD B2C 테넌트가 있는 경우 지금 연결해야 합니다. 2019년 11월 1일 이전에 구독에 연결된 기존 Azure AD B2C 테넌트가 있는 경우 MAU(월간 활성 사용자) 청구 모델로 업그레이드하거나 인증별 청구 모델을 유지하는 것이 좋습니다.
  
Azure AD B2C 테넌트는 사용하려는 기능에 따라 적절한 Azure 가격 책정 계층에도 연결되어야 합니다. 프리미엄 기능을 사용하려면 Azure AD B2C [프리미엄 P1 또는 P2 가격 책정](https://azure.microsoft.com/pricing/details/active-directory-b2c/)이 필요합니다. 새 기능을 사용할 때 가격 책정 계층을 업그레이드해야 할 수도 있습니다. 예를 들어 위험 기반 조건부 액세스 정책의 경우 테넌트에 대해 Azure AD B2C 프리미엄 P2 가격 책정 계층을 선택해야 합니다.
> [!NOTE]
>  프리미엄 P1 및 프리미엄 P2 기능 모두에 대해 매월 처음 50,000개의 MAU가 무료입니다. MAU의 총 수를 확인하기 위해 동일한 구독에 연결된 모든 테넌트(Azure AD 및 Azure AD B2C)의 MAU를 결합합니다.
## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>구독에 Azure AD B2C 테넌트 연결

Azure AD B2C(Azure Active Directory B2C) 사용 요금은 Azure 구독에 청구됩니다. 대상 Azure 구독 내에서 Azure AD B2C 리소스를 만들어 Azure AD B2C 테넌트를 Azure 구독에 명시적으로 연결해야 합니다. 가상 머신, Storage 계정, Logic Apps 등의 다른 Azure 리소스와 함께 여러 Azure AD B2C 리소스를 단일 Azure 구독에서 만들 수 있습니다. 구독과 연결된 Azure AD(Azure Active Directory) 테넌트로 이동하여 구독 내의 모든 리소스를 볼 수 있습니다.

Azure AD B2C 테넌트에 연결된 구독은 Azure AD B2C 사용 또는 추가 Azure AD B2C 리소스를 포함한 기타 Azure 리소스에 대한 요금 청구에 사용할 수 있습니다. Azure AD B2C 테넌트 내의 다른 Azure 라이선스 기반 서비스 또는 Office 365 라이선스를 추가하는 데는 구독을 사용할 수 없습니다.

### <a name="prerequisites"></a>필수 구성 요소

* [Azure 구독](https://azure.microsoft.com/free/)
* 구독에 연결할 [Azure AD B2C 테넌트](tutorial-create-tenant.md).
  * 테넌트 관리자여야 합니다.
  * 테넌트가 구독에 이미 연결되어 있지 않아야 합니다.

### <a name="create-the-link"></a>링크 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음 사용하려는 Azure 구독을 포함하는 디렉터리(Azure AD B2C 테넌트를 포함하는 디렉터리 아님)를 선택합니다.
3. **리소스 만들기** 를 선택하고 **Marketplace 검색** 필드에 `Active Directory B2C`를 입력한 다음 **Azure Active Directory B2C** 를 선택합니다.
4. **만들기** 를 선택합니다.
5. **내 Azure 구독에 기존 Azure AD B2C 테넌트 연결** 을 선택합니다.
6. 드롭다운에서 **Azure AD B2C 테넌트** 를 선택합니다. 전역 관리자이고 구독에 아직 연결되지 않은 테넌트만 표시됩니다. **Azure AD B2C 리소스 이름** 필드는 선택한 Azure AD B2C 테넌트의 도메인 이름으로 채워집니다.
7. 관리자인 활성 Azure **구독** 을 선택합니다.
8. **리소스 그룹** 에서 **새로 만들기** 를 선택한 다음 **리소스 그룹 위치** 를 지정합니다. 여기에서 선택한 리소스 그룹 설정은 Azure AD B2C 테넌트 위치, 성능 또는 청구 상태에 영향을 주지 않습니다.
9. **만들기** 를 선택합니다.

    ![Azure Portal의 Azure AD B2C 리소스 생성 페이지](./media/billing/portal-01-create-b2c-resource-page.png)

Azure AD B2C 테넌트에 대해 이러한 단계를 완료하면 Azure Direct 또는 기업계약 세부 정보(해당하는 경우)를 기준으로 Azure 구독에 대한 요금이 청구됩니다.

## <a name="change-your-azure-ad-pricing-tier"></a>Azure AD 가격 책정 계층 변경

Azure AD B2C 테넌트에 사용하려는 기능에 따라 테넌트를 적절한 Azure 가격 책정 계층에 연결해야 합니다. 프리미엄 기능을 사용하려면 [Azure Active Directory B2C 가격 책정](https://azure.microsoft.com/pricing/details/active-directory-b2c/)에 설명된 대로 Azure AD B2C 프리미엄 P1 또는 P2가 필요합니다. 새 기능을 사용할 때 가격 책정 계층을 업그레이드해야 하는 경우도 있습니다. 예를 들어 ID 보호, 위험 기반 조건부 액세스 정책, 향후 Premium P2 기능을 Azure AD B2C에 사용하려면 테넌트에 대해 Azure AD B2C Premium P2 가격 책정 계층을 선택해야 합니다.

가격 책정 계층을 변경하려면 다음 단계를 수행합니다.

1. Azure Portal에 로그인합니다.

2. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음 Azure B2C 테넌트가 연결된 Azure 구독을 포함하는 디렉터리를 선택합니다(Azure AD B2C 테넌트 자체를 포함하는 디렉터리를 선택하지 마세요).

3. 포털 맨 위에 있는 검색 상자에 Azure AD B2C 테넌트의 이름을 입력합니다. 그런 다음 **리소스** 아래 검색 결과에서 테넌트를 선택합니다.

4. 리소스 **개요** 페이지의 **가격 책정 계층** 에서 **변경** 을 선택합니다.

   ![가격 책정 계층 변경](media/billing/change-pricing-tier.png)
 
5. 사용하도록 설정하려는 기능이 포함된 가격 책정 계층을 선택합니다.

   ![가격 책정 계층 선택](media/billing/select-tier.png)

## <a name="switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants"></a>MAU 청구로 전환(2019년 11월 이전 Azure AD B2C 테넌트)

**2019년 11월 1일** 이전에 Azure AD B2C 테넌트를 구독에 연결한 경우 이전의 인증별 청구 모델이 사용됩니다. MAU(월간 활성 사용자) 청구 모델로 업그레이드하는 것이 좋습니다. 청구 옵션은 Azure AD B2C 리소스에서 구성됩니다.

MAU(월간 활성 사용자) 청구로의 전환은 **취소할 수 없습니다**. Azure AD B2C 리소스를 MAU 기반 청구 모델로 전환한 후에는 해당 리소스를 인증별 청구 모델로 되돌릴 수 없습니다.

기존 Azure AD B2C 리소스를 MAU 청구로 전환하는 방법은 다음과 같습니다.

1. Azure AD B2C 리소스에 대한 관리 권한이 있는 구독 소유자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음 MAU 청구로 업그레이드하려는 Azure AD B2C 디렉터리를 선택합니다.<br/>

    ![Azure Portal의 디렉터리 및 구독 필터](./media/billing/portal-mau-01-select-b2c-directory.png)

3. 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.

4. Azure AD B2C 테넌트의 **개요** 페이지에 있는 **리소스 이름** 에서 링크를 선택합니다. Azure AD 테넌트의 Azure AD B2C 리소스로 리디렉션됩니다.<br/>

    ![Azure Portal에 강조 표시된 Azure AD B2C 리소스 링크](./media/billing/portal-mau-02-b2c-resource-link.png)

5. Azure AD B2C 리소스의 **개요** 페이지에 있는 **청구 단위** 에서 **인증별(MAU로 변경)** 링크를 선택합니다.<br/>

    ![Azure Portal에 강조 표시된 MAU로 변경 링크](./media/billing/portal-mau-03-change-to-mau-link.png)

6. **확인** 을 선택하여 MAU 청구로 업그레이드를 완료합니다.<br/>

    ![Azure Portal의 MAU 기반 청구 확인 대화 상자](./media/billing/portal-mau-04-confirm-change-to-mau.png)


### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>인증별 청구에서 MAU 청구로 전환하는 경우 발생할 수 있는 사항

MAU 기반 계량은 구독/리소스 소유자가 변경을 확인하는 즉시 사용하도록 설정됩니다. 월별 요금 청구에는 변경될 때까지 청구되는 인증 단위와 변경 때부터 시작하는 MAU의 새 단위가 반영됩니다.

전환하는 달에는 사용자가 두 번 계산되지 않습니다. 변경 전에 인증하는 고유 활성 사용자에게는 월 인증별 요금이 부과됩니다. 이러한 사용자는 구독 청구 주기의 남은 기간 동안 MAU 계산에 포함되지 않습니다. 예를 들면 다음과 같습니다.

* Contoso B2C 테넌트에 1,000 명의 사용자가 있습니다. 250명의 사용자는 어느 달에나 활성 사용자입니다. 구독 관리자는 해당 달 10일에 인증별에서 MAU(월간 활성 사용자)로 변경합니다.
* 1일부터 10일까지의 요금은 인증별 모델을 사용하여 청구됩니다.
  * 이 기간(1일~10일) 동안 100명의 사용자가 로그인하는 경우 이 사용자들은 해당 달 납부로 태그가 지정됩니다.
* 10일(전환 실행 시간)부터의 요금은 MAU 요율로 청구됩니다.
  * 이 기간(10일~30일) 동안 추가로 150명의 사용자가 로그인한 경우에는 추가 150명에 대해서만 요금이 청구됩니다.
  * 처음 사용자 100명의 지속적인 활동은 해당 월의 나머지 기간에 대한 청구에 영향을 주지 않습니다.

청구 전환 기간 동안 구독 소유자는 Azure 구독 청구 명세서에서 다음과 같이 두 가지 방법(인증별 및 MAU별) 모두의 항목을 볼 수 있습니다.

* 변경 날짜/시간까지의 사용에 대해 인증별 청구를 반영하는 항목.
* 변경 후 사용에 대해 MAU(월간 활성 사용자)를 반영하는 항목.

Azure AD B2C의 사용 요금 청구 및 가격 책정에 대한 최신 정보는 [Azure Active Directory B2C 가격 책정](https://azure.microsoft.com/pricing/details/active-directory-b2c/)을 참조하세요.

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Azure AD B2C 테넌트 리소스 관리

Azure 구독에서 Azure AD B2C 리소스를 만든 후에는 다른 Azure 리소스와 함께 ‘B2C 테넌트’ 유형의 새 리소스가 표시되어야 합니다.

이 리소스를 사용하여 다음을 수행할 수 있습니다.

* 구독으로 이동하여 청구 정보를 검토합니다.
* GUID 형식으로 Azure AD B2C 테넌트의 테넌트 ID를 가져옵니다.
* Azure AD B2C 테넌트로 이동합니다.
* 지원 요청을 제출합니다.
* Azure AD B2C 테넌트 리소스를 다른 Azure 구독 또는 리소스 그룹으로 이동합니다.

### <a name="regional-restrictions"></a>지역 제한

구독에서 Azure 리소스 생성에 대한 지역 제한을 설정한 경우 해당 제한으로 인해 Azure AD B2C 리소스를 만들지 못할 수 있습니다.

이 문제를 완화하려면 지역 제한을 완화하세요.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Azure CSP(클라우드 솔루션 공급자) 구독

Azure CSP(Cloud Solution Providers) 구독은 Azure AD B2C에서 지원됩니다. 이 기능은 Azure AD B2C 또는 모든 Azure 리소스에 대한 API 또는 Azure Portal을 통해 사용할 수 있습니다. CSP 구독 관리자는 다른 Azure 리소스처럼 Azure AD B2C와의 관계를 연결, 이동 및 삭제할 수 있습니다.

역할 기반 액세스 제어를 사용한 Azure AD B2C 관리는 Azure AD B2C 테넌트와 Azure CSP 구독 간의 연결에 의해 영향을 받지 않습니다. 역할 기반 액세스 제어는 구독 기반 역할이 아닌 테넌트 기반 역할을 사용하여 수행됩니다.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Azure AD B2C 테넌트 청구 구독 변경

### <a name="move-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 이동

원본 구독과 대상 구독이 동일한 Azure Active Directory 테넌트 내에 있는 경우 Azure Resource Manager를 사용하여 Azure AD B2C 테넌트를 다른 구독으로 이동할 수 있습니다.

Azure AD B2C 테넌트와 같은 Azure 리소스를 다른 구독으로 이동하는 방법에 대한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요.

이동을 시작하기 전에 문서 전체를 읽고 이러한 이동의 제한 사항과 요구 사항을 완전히 이해해야 합니다. 이 문서에는 리소스 이동에 대한 지침 외에도 이동 전 검사 목록 및 이동 작업 유효성 검사 방법과 같은 중요한 정보가 포함되어 있습니다.

### <a name="move-by-unlinking-and-relinking"></a>연결 해제 및 다시 연결을 통한 이동

원본 구독과 대상 구독이 서로 다른 Azure Active Directory 테넌트와 연결된 경우 위에 설명한 것처럼 Azure Resource Manager를 통해 이동을 수행할 수 없습니다. 하지만 원본 구독에서 Azure AD B2C 테넌트를 연결 해제하고 대상 구독에 다시 연결하여 동일한 결과를 얻을 수 있습니다. 이 방법은 삭제하는 유일한 개체가 Azure AD B2C 테넌트 자체가 아니라 청구 링크이기 때문에 안전합니다. 사용자, 앱, 사용자 흐름 등은 영향을 받지 않습니다.

1. Azure AD B2C 디렉터리 자체에서 대상 Azure AD 테넌트(대상 Azure 구독이 연결된 테넌트)에서 [게스트 사용자를 초대](user-overview.md#guest-user)하고 이 사용자에게 Azure AD B2C **전역 관리자** 역할이 있는지 확인합니다.
1. 위의 [Azure AD B2C 테넌트 리소스 관리](#manage-your-azure-ad-b2c-tenant-resources) 섹션에 설명된 대로 원본 Azure 구독에서 Azure AD B2C를 나타내는 Azure 리소스로 이동합니다. 실제 Azure AD B2C 테넌트로 전환하지 마세요.
1. **개요** 페이지에서 **삭제** 단추를 클릭합니다. 관련 Azure AD B2C 테넌트의 사용자 또는 애플리케이션은 삭제되지 않습니다. 원본 구독에서 청구 링크만 제거됩니다.
1. 1단계에서 Azure AD B2C에서 관리자로 추가된 사용자 계정으로 Azure Portal에 로그인합니다. 그런 다음 대상 Azure Active Directory 테넌트에 연결된 대상 Azure 구독으로 이동합니다. 
1. 위의 [링크 만들기](#create-the-link) 절차를 수행하여 대상 구독에서 청구 링크를 다시 설정합니다.
1. 이제 Azure AD B2C 리소스가 대상 Azure 구독(대상 Azure Active Directory에 연결됨)으로 이동되었으며, 지금부터 이 구독을 통해 요금이 청구됩니다.

## <a name="next-steps"></a>다음 단계

최신 가격 책정 정보는 [Azure Active Directory B2C 가격 책정](https://azure.microsoft.com/pricing/details/active-directory-b2c/)을 참조하세요.
