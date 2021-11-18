---
title: 직접 Enterprise 계약에 대한 Azure Portal 관리
description: 이 문서에서는 직접 엔터프라이즈 관리자가 Azure Portal 수행하는 일반적인 작업을 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 11/16/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: sapnakeshari
ms.openlocfilehash: 0cd07afa3b5bb63d017443ee4bc9de5e21f652ff
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718567"
---
# <a name="azure-portal-administration-for-direct-enterprise-agreements"></a>직접 Enterprise 계약에 대한 Azure Portal 관리

이 문서에서는 [AZURE PORTAL](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)EA(직접 기업계약) 관리자가 수행하는 일반적인 작업에 대해 설명합니다. 직접 기업계약은 Microsoft와 기업계약 고객 간에 서명됩니다.

반대로 간접 EA는 고객이 Microsoft 파트너와 계약에 서명하는 EA입니다. 간접 EA는 Azure Enterprise 포털 을 사용하여 [관리됩니다.](https://ea.azure.com/) 간접 EA 관리에 대한 자세한 내용은 Azure EA Portal 관리 를 [참조하세요.](ea-portal-administration.md)

## <a name="manage-your-enrollment"></a>등록 관리

서비스를 관리하기 위해 초기 엔터프라이즈 관리자는 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)열고   초대 전자 메일의 이메일 주소를 사용하여 로그인합니다.

엔터프라이즈 관리자로 설정된 경우 Azure Portal 이동하여 회사, 학교 또는 Microsoft 계정 이메일 주소 및 암호를 사용하여 로그인합니다.

청구 계정이 두 개 이상 있는 경우 청구 범위 메뉴에서 청구 계정을 선택합니다. 왼쪽 메뉴에서 청구 계정 속성 및 정책을 볼 수 있습니다.

[EA 관리자 등록 관리](https://www.youtube.com/watch?v=NUlRrJFF1_U) 비디오를 확인하세요. Azure Portal 시리즈 비디오 시리즈에서 [직접 Enterprise 고객 청구 환경의](https://www.youtube.com/playlist?list=PLeZrVF6SXmsoHSnAgrDDzL0W5j8KevFIm) 일부입니다.

>[!VIDEO https://www.youtube.com/embed/NUlRrJFF1_U]

## <a name="select-a-billing-scope"></a>청구 범위 선택

Enterprise 계약 및 계약에 액세스하는 고객은 여러 등록을 가질 수 있습니다. 사용자는 여러 등록 범위(청구 계정 범위)에 액세스할 수 있습니다. Azure Portal 모든 정보 및 활동은 청구 계정 범위의 컨텍스트에 있습니다. 엔터프라이즈 관리자는 먼저 청구 범위를 선택한 다음 관리 작업을 수행하는 것이 중요합니다.

### <a name="to-select-a-billing-scope"></a>청구 범위를 선택하려면

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)에 로그인합니다.
1. **Cost Management + Billing** 검색하여 선택합니다.  
    :::image type="content" source="./media/direct-ea-administration/search-cost-management.png" alt-text="Cost Management + Billing 검색을 보여주는 스크린샷." lightbox="./media/direct-ea-administration/search-cost-management.png" :::
1. 탐색 메뉴에서 **청구 범위를** 선택한 다음, 작업할 청구 계정을 선택합니다.  
    :::image type="content" source="./media/direct-ea-administration/select-billing-scope.png" alt-text="청구 계정 선택을 보여주는 스크린샷." lightbox="./media/direct-ea-administration/select-billing-scope.png" :::

## <a name="view-enrollment-details"></a>등록 세부 정보 보기

Azure 엔터프라이즈 관리자(EA 관리자)는 등록 속성 및 정책을 보고 관리하여 등록 설정이 올바르게 구성되었는지 확인할 수 있습니다.

### <a name="to-view-enrollment-properties"></a>등록 속성을 보려면

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 왼쪽 메뉴에서 청구 **범위를** 선택한 다음, 청구 계정 범위를 선택합니다.
1. 탐색 메뉴의 **설정** 아래에서 **속성을** 선택합니다.
1. 청구 계정 정보를 봅니다.  
    :::image type="content" source="./media/direct-ea-administration/billing-account-properties.png" alt-text="청구 계정 속성을 보여주는 스크린샷." lightbox="./media/direct-ea-administration/billing-account-properties.png" :::

### <a name="view-and-manage-enrollment-policies"></a>등록 정책 보기 및 관리

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 왼쪽 메뉴에서 청구 **범위를** 선택한 다음, 청구 계정 범위를 선택합니다.
1. 탐색 메뉴에서 **정책** 을 선택합니다.
1. **켜기** 또는 **끄기** 를 선택하여 정책을 사용하거나 사용하지 않도록 설정합니다.  
    :::image type="content" source="./media/direct-ea-administration/enrollment-policies.png" alt-text="E A 등록 정책을 보여주는 스크린샷." lightbox="./media/direct-ea-administration/enrollment-policies.png" :::

DA(부서 관리자) 및 AO(계정 소유자) 보기 요금 정책 설정에 대한 자세한 내용은 [다른 사용자 역할에 대한 가격 책정을 참조하세요.](understand-ea-roles.md#see-pricing-for-different-user-roles)

## <a name="add-another-enterprise-administrator"></a>다른 엔터프라이즈 관리자 추가

기존 EA 관리자만 다른 엔터프라이즈 관리자를 만들 수 있습니다. 시나리오에 따라 다음 옵션 중 하나를 선택합니다.

### <a name="if-youre-already-an-enterprise-administrator"></a>엔터프라이즈 관리자인 경우

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 왼쪽 메뉴에서 청구 **범위를** 선택한 다음, 청구 계정 범위를 선택합니다.
1. 왼쪽 메뉴에서 **Access Control(IAM)** 를 선택합니다.
1. 상단 메뉴에서 + **추가를** 선택한 다음, **관리자 Enterprise** 선택합니다.  
    :::image type="content" source="./media/direct-ea-administration/add-enterprise-admin-navigate.png" alt-text="Enterprise 관리자에 대한 탐색을 보여주는 스크린샷" lightbox="./media/direct-ea-administration/add-enterprise-admin-navigate.png" :::
1. 역할 할당 추가 양식을 완료한 다음, **추가를** 선택합니다.

사용자의 이메일 주소와 기본 인증 방법(예: 직장, 학교 또는 Microsoft 계정)이 편리한지 확인합니다.

EA 관리자는 각 사용자의 오른쪽에 있는 줄임표(**...**) 기호를 선택하여 기존 엔터프라이즈 관리자에 대한 액세스를 관리할 수 있습니다. 기존 사용자를 **편집하고** **삭제할** 수 있습니다.

### <a name="if-youre-not-an-enterprise-administrator"></a>엔터프라이즈 관리자가 아닌 경우

EA 관리자가 아닌 경우 EA 관리자에게 문의하여 등록에 추가하도록 요청합니다. EA 관리자는 이전 단계를 사용하여 엔터프라이즈 관리자로 추가합니다. 등록에 추가되면 활성화 이메일을 받게 됩니다.

### <a name="if-your-enterprise-administrator-cant-help-you"></a>엔터프라이즈 관리자가 도와줄 수 없는 경우

엔터프라이즈 관리자가 도움을 줄 수 없는 경우 [Azure 지원 요청을](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)만듭니다. 다음 정보를 지정합니다.

- 등록 번호
- 추가할 이메일 주소 및 인증 유형(회사, 학교 또는 Microsoft 계정)
- 기존 엔터프라이즈 관리자의 이메일 승인

기존 엔터프라이즈 관리자에게 연락할 수 없는 경우 파트너 또는 소프트웨어 관리자에게 연락하여 VLSC(Volume Licensing Service Center) 도구를 통해 연락처 세부 정보를 변경해 달라고 요청합니다.

## <a name="create-an-azure-enterprise-department"></a>Azure 엔터프라이즈 부서 만들기

EA 관리자 및 부서 관리자는 부서를 사용하여 부서 및 비용 센터별로 엔터프라이즈 Azure 서비스 및 사용 현황을 구성하고 보고합니다. 엔터프라이즈 관리자는 다음과 같은 작업을 할 수 있습니다.

- 부서 추가 또는 제거
- 부서에 계정 연결
- 부서 관리자 만들기
- 부서 관리자가 가격 및 비용을 볼 수 있도록 허용

부서 관리자는 부서에 새 계정을 추가할 수 있습니다. 또한 해당 부서에서 계정을 제거할 수 있지만 등록에서는 제거할 수 없습니다.

EA 관리자 부서 관리 비디오를 [확인하세요.](https://www.youtube.com/watch?v=NUlRrJFF1_U) Azure Portal 시리즈 비디오 시리즈에서 [직접 Enterprise 고객 청구 환경의](https://www.youtube.com/playlist?list=PLeZrVF6SXmsoHSnAgrDDzL0W5j8KevFIm) 일부입니다.

>[!VIDEO https://www.youtube.com/embed/cxAtOSSE6UI]

### <a name="to-create-a-department"></a>부서를 만들려면

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 왼쪽 메뉴에서 청구 **범위를** 선택한 다음, 청구 계정 범위를 선택합니다.
1. 왼쪽 탐색 메뉴에서 **부서 를 선택합니다.**
1. **+추가** 를 선택합니다.  
    :::image type="content" source="./media/direct-ea-administration/add-department.png" alt-text="부서를 추가하는 탐색을 보여주는 스크린샷." lightbox="./media/direct-ea-administration/add-department.png" :::
1. 새 부서 추가에서 정보를 입력합니다. 부서 이름은 유일한 필수 필드입니다. 3자 이상이어야 합니다.
1. 완료되면 **저장** 을 선택합니다.

## <a name="add-a-department-administrator"></a>부서 관리자 추가

부서를 만든 후 EA 관리자는 부서 관리자를 추가하고 각 부서를 부서에 연결할 수 있습니다. 부서 관리자는 해당 부서에 대해 다음 작업을 수행할 수 있습니다.

- 다른 부서 관리자 만들기
- 이름 또는 비용 센터와 같은 부서 속성을 살펴보고 편집
- 계정 추가
- 계정 제거
- 사용량 세부 정보 다운로드
- 월간 사용량 및 요금 보기<sup>1</sup>

 <sup>1</sup> EA 관리자는 권한을 부여해야 합니다.

### <a name="to-add-a-department-administrator"></a>부서 관리자 추가

엔터프라이즈 관리자로 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 왼쪽 메뉴에서 청구 **범위를** 선택한 다음, 청구 계정 범위를 선택합니다.
1. 탐색 메뉴에서 **부서 를 선택합니다.**
1. 관리자를 추가할 부서를 선택합니다.
1. 부서 보기에서 **Access Control(IAM)** 를 선택합니다.
1. **+ 추가를** 선택한 **다음, 부서 관리자를** 선택합니다.
1. 이메일 주소 및 기타 필수 정보를 입력합니다.
1. 읽기 전용 액세스의 경우 **읽기 전용** 옵션을 **예** 로 설정한 다음, **추가를** 선택합니다.  
    :::image type="content" source="./media/direct-ea-administration/add-department-admin.png" alt-text="부서 관리자에 대한 탐색을 보여주는 스크린샷." lightbox="./media/direct-ea-administration/add-department-admin.png" :::

### <a name="to-set-read-only-access"></a>읽기 전용 액세스 설정 방법

EA 관리자는 부서 관리자에게 읽기 전용 액세스 권한을 부여할 수 있습니다. 새 부서 관리자를 만들 때 읽기 전용 옵션을 **예** 로 설정합니다.

기존 부서 관리자를 편집하려면 다음을 수행합니다.

1. 행의 오른쪽에 있는 줄임표 기호(**...**)를 선택하고 선택합니다.
1. 읽기 전용 열기를  **예로** 설정한 다음, **적용을** 선택합니다.

엔터프라이즈 관리자에게 자동으로 부서 관리자 권한이 할당됩니다.

## <a name="add-an-account-and-account-owner"></a>계정 및 계정 소유자 추가

계정 및 구독의 구조는 관리 방법 및 청구서 및 보고서에 표시되는 방식에 영향을 줍니다. 비즈니스 부문, 직능 팀 및 지리적 위치에 따라 조직 구조를 만드는 것이 대표적인 예입니다.

등록에 새 계정이 추가되면 계정 소유자는 소유권을 확인하는 데 사용되는 계정 소유권 전자 메일을 보냅니다.

EA 관리자 계정 관리 비디오를 [확인하세요.](https://www.youtube.com/watch?v=VKWAEx6qfPc) Azure Portal 시리즈 비디오 시리즈에서 [직접 Enterprise 고객 청구 환경의](https://www.youtube.com/playlist?list=PLeZrVF6SXmsoHSnAgrDDzL0W5j8KevFIm) 일부입니다.

>[!VIDEO https://www.youtube.com/embed/VKWAEx6qfPc]

### <a name="to-add-an-account-and-account-owner"></a>계정 및 계정 소유자를 추가하려면

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 왼쪽 메뉴에서 청구 **범위를** 선택한 다음, 청구 계정 범위를 선택합니다.
1. 왼쪽 메뉴에서 계정 **을 선택합니다.**
1. **+추가** 를 선택합니다.
1. 계정 추가 페이지에서 친숙한 이름을 입력하여 보고에 사용되는 계정을 식별합니다.
1. 계정 **소유자 전자 메일** 주소를 입력하여 새 계정과 연결합니다.
1. 부서를 선택하거나 할당되지 않은 상태로 둡니다.
1. 완료되면 **추가를** 선택합니다.

### <a name="to-confirm-account-ownership"></a>계정 소유권을 확인하려면

계정 소유자가 계정 소유권 이메일을 받은 후 소유권을 확인해야 합니다.

1. 계정 소유자로 설정된 직장, 학교 또는 Microsoft 계정 연결된 이메일 계정에 로그인합니다.
1. Microsoft Azure 서비스에서 계정 _활성화 초대라는_ 이메일 알림을 엽니다.
1. 초대에서 **계정 활성화** 링크를 선택합니다.
1. Azure Portal에 로그인합니다.
1. 계정 활성화 페이지에서 예, 계정 **소유권을 계속 확인하려고** 합니다를 선택합니다.  
    :::image type="content" source="./media/direct-ea-administration/activate-account.png" alt-text="계정 활성화 페이지를 보여주는 스크린샷." lightbox="./media/direct-ea-administration/activate-account.png" :::

계정 소유권이 확인되면 구독을 만들고 구독을 사용하여 리소스를 구매할 수 있습니다.

## <a name="change-azure-subscription-or-account-ownership"></a>Azure 구독 또는 계정 소유권 변경

EA 관리자는 Azure Portal 사용하여 등록에서 선택한 구독 또는 모든 구독의 계정 소유권을 양도할 수 있습니다. 구독 또는 계정 소유권 양도가 완료되면 Microsoft에서 계정 소유자를 업데이트합니다.

소유권 이전을 시작하기 전에 다음 Azure RBAC(Azure 역할 기반 액세스 제어) 정책을 숙지합니다.

- 동일한 테넌트 내의 두 조직 ID 간에 구독 또는 계정 소유권 이전을 수행하는 경우 다음 항목이 유지됩니다.
    - Azure RBAC 정책
    - 기존 서비스 관리자
    - 공동 배치자 역할
- 테넌트 간에 구독 또는 계정 소유권 양도로 인해 Azure RBAC 정책과 역할 할당이 손실됩니다.
- 정책 및 관리자 역할은 디렉터리 간에 양도되지 않습니다. 서비스 관리자는 대상 계정의 소유자로 업데이트됩니다.
- 테넌트 간에 구독을 양도할 때 Azure RBAC 정책 및 역할 할당이 손실되지 않도록 하려면 **구독을 받는 사람의 Azure AD 테넌트 선택 영역으로 이동이** 지워져 있는지 확인합니다. 이 선택 항목은 현재 Azure AD 테넌트에서 서비스, Azure 역할 및 정책을 유지하고 계정에 대한 청구 소유권만 이전합니다.

계정 소유자를 변경하기 전에 다음을 수행합니다.

1. **계정** 탭을 보고 원본 계정을 식별합니다. 원본 계정이 활성 상태여야 합니다.
1. 대상 계정을 찾아서 활성 상태인지 확인합니다.

모든 구독의 계정 소유권을 양도하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 왼쪽 메뉴에서 청구 **범위를** 선택한 다음, 청구 계정 범위를 선택합니다.
1. 왼쪽 메뉴에서 계정 **을 선택합니다.**
1. **계정을** 선택한 다음, 행 오른쪽에서 줄임표(**...**) 기호를 선택합니다.
1. **구독 양도를 선택합니다.**  
    :::image type="content" source="./media/direct-ea-administration/transfer-subscriptions-01.png" alt-text="구독을 양도할 위치를 보여주는 스크린샷." lightbox="./media/direct-ea-administration/transfer-subscriptions-01.png" :::
1. 구독 양도 페이지에서 양도할 대상 계정을 선택한 후 **다음을** 선택합니다.
1. Azure AD 테넌트 간에 계정 소유권을 양도하려면 **예, 구독을 새 계정의 Azure AD 테넌트 확인으로 이동하려고** 합니다.를 선택합니다.
1. 전송을 확인하고 **제출을** 선택합니다.  
    :::image type="content" source="./media/direct-ea-administration/transfer-account-confirmation.png" alt-text="구독 양도 확인을 보여 주는 스크린샷." lightbox="./media/direct-ea-administration/transfer-account-confirmation.png" :::

단일 구독의 계정 소유권을 양도하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 왼쪽 메뉴에서 청구 **범위를** 선택한 다음, 청구 계정 범위를 선택합니다.
1. 왼쪽 메뉴에서 Azure **구독을 선택합니다.**
1. Azure 구독 페이지의 구독 오른쪽에서 말임표(**...**) 기호를 선택합니다.
1. **구독 이전** 을 선택합니다.
1. 구독 양도 페이지에서 구독을 전송할 대상 계정을 선택한 후  **다음을** 선택합니다.
1. Azure AD 테넌트 간에 구독 소유권을 양도하려면 **예, 구독을 새 계정의 Azure AD 테넌트 옵션으로 이동하려고** 합니다.
1. 양도를 확인하고 **제출** 을 선택합니다.

## <a name="associate-an-account-to-a-department"></a>부서에 계정 연결

EA 관리자는 등록의 부서에 기존 계정을 연결할 수 있습니다.

### <a name="to-associate-an-account-to-a-department"></a>계정을 부서에 연결하려면

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 왼쪽 메뉴에서 청구 **범위를** 선택한 다음, 청구 계정 범위를 선택합니다.
1. 왼쪽 메뉴에서 계정 **을 선택합니다.**
1. 계정의 오른쪽에 있는 말임표(**...**) 기호를 선택한 **다음, 편집을** 선택합니다.
1. 계정 편집 페이지의 목록에서 **부서를** 선택합니다.
1.  **저장** 을 선택합니다.

## <a name="associate-an-account-with-a-pay-as-you-go-subscription"></a>종량제 구독에 계정 연결

Azure 종량제 구독에 대한 기존 학교, 직장 또는 Microsoft 계정 이미 있는 경우 기업계약 등록에 연결할 수 있습니다.

계정을 연결하려면 EA 관리자 또는 부서 관리자 역할이 있어야 합니다.

### <a name="to-associate-an-existing-account"></a>기존 계정을 연결하려면

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 왼쪽 메뉴에서 청구 **범위를** 선택한 다음, 청구 계정 범위를 선택합니다.
1. 왼쪽 메뉴에서 계정 **을 선택합니다.**
1. **+추가** 를 선택합니다.
1. 계정 추가 페이지에서 보고 목적으로 계정을 식별하는 데 사용할 이름을 입력합니다.
1. 기존 Azure **계정과** 연결된 **Microsoft** 또는 Work 또는 School 계정을 선택합니다.
1. 계정 소유자의 이메일 주소를 입력한 다음 확인합니다.
1. **추가** 를 선택합니다.

계정이 Azure Portal 표시되는 데 최대 8시간이 걸릴 수 있습니다.

### <a name="to-confirm-account-ownership"></a>계정 소유권을 확인하려면

1. 이전 절차에서 연결한 직장, 학교 또는 Microsoft 계정 연결된 이메일 계정에 로그인합니다.
1. Microsoft Azure 서비스에서 계정 _활성화 초대라는_ 이메일 알림을 엽니다.
1. 초대에서 **계정 활성화** 링크를 선택합니다.
1. Azure Portal에 로그인합니다.
1. 계정 활성화 페이지에서 예, 계정 **소유권을 계속 확인하려고** 합니다를 선택합니다.  
    :::image type="content" source="./media/direct-ea-administration/activate-account.png" alt-text="계정 활성화 페이지를 보여주는 스크린샷." lightbox="./media/direct-ea-administration/activate-account.png" :::

## <a name="enable-azure-marketplace-purchases"></a>Azure Marketplace 구매 사용

대부분의 종량제 _구독은_ Azure 기업계약 연결될 수 있지만 이전에 구매한 Azure Marketplace _서비스는_ 연결될 수 없습니다. 모든 구독 및 요금을 단일 보기로 보려면 Azure Marketplace 구매를 사용하도록 설정하는 것이 좋습니다.

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 왼쪽 메뉴에서 청구 **범위를** 선택한 다음, 청구 계정 범위를 선택합니다.
1. 왼쪽 메뉴에서 **정책** 을 선택합니다.
1. **Azure Marketplace** 아래에서 정책을 **켜기로** 설정합니다.  
   :::image type="content" source="./media/direct-ea-administration/azure-marketplace.png" alt-text="Azure Marketplace 정책 설정을 보여주는 스크린샷." lightbox="./media/direct-ea-administration/azure-marketplace.png" :::

그러면 계정 소유자는 종량제 구독에서 이전에 소유한 Azure Marketplace 서비스를 다시 구독할 수 있습니다.

이 설정은 등록의 모든 계정 소유자에게 적용됩니다. 이를 통해 Azure Marketplace 구매할 수 있습니다.

Azure EA 등록에서 구독이 활성화되면 종량제 구독으로 만든 Azure Marketplace 서비스를 취소합니다. 이 단계는 종량제 결제 수단이 만료되는 경우에 중요합니다.

## <a name="msdn-subscription-transfer"></a>MSDN 구독 전송

MSDN 구독을 EA 등록으로 전송하면 MSDN 개발/테스트 구독으로 자동 변환됩니다. 전환 후 구독은 기존 금액 크레딧을 잃게 됩니다. 따라서 기업계약 양도하기 전에 모든 크레딧을 사용하는 것이 좋습니다.

## <a name="azure-in-open-subscription-transfer"></a>Azure in Open 구독 이전

Azure in Open 구독을 기업계약 양도하면 사용되지 않은 Azure in Open 크레딧이 손실됩니다. 기업계약 양도하기 전에 Azure in Open 구독에 대한 모든 크레딧을 사용하는 것이 좋습니다.

## <a name="subscription-transfers-with-support-plans"></a>지원 계획이 있는 구독 양도

기업계약 지원 계획이 없고 지원 계획이 있는 기존 MOSA(Microsoft Online Support Agreement) 구독을 이전하려고 하면 구독이 자동으로 양도되지 않습니다. 유예 기간(다음 달 말)에 EA 등록에 대한 지원 플랜을 다시 예약해야 합니다.

## <a name="manage-department-and-account-spending-with-budgets"></a>예산을 통해 부서 및 계정 지출 관리

EA 고객은 등록에 따라 각 부서 및 계정에 대한 예산을 설정할 수 있습니다. Cost Management의 예산을 통해 조직 책임을 계획하고 주도할 수 있습니다. 비용을 적극적으로 관리하고 지출이 진행되는 방식을 모니터링하기 위해 지출에 대해 다른 사람들에게 알리는 데 도움이 됩니다. 지출이 조직 지출 한도 내에 있도록 실제 비용 또는 예상 비용을 기반으로 경고를 구성할 수 있습니다. 사용자가 만든 예산 임계값이 초과된 경우 알림만 트리거되고 리소스에는 영향을 미치지 않으며 소비도 중단되지 않습니다. 예산을 사용하여 비용 분석 시 지출을 비교하고 추적할 수 있습니다. 예산을 만드는 방법에 대한 자세한 내용은 [자습서: Azure 예산 만들기 및 관리를 참조하세요.](../costs/tutorial-acm-create-budgets.md)

## <a name="enterprise-agreement-user-roles"></a>사용자 역할 기업계약

Azure Portal Azure EA 비용 및 사용량을 관리하는 데 도움이 됩니다. 세 가지 주요 EA 역할이 있습니다.

- EA(기업계약) 관리자
- 부서 관리자
- 계정 소유자

각 역할의 액세스 및 권한 수준은 서로 다릅니다. 사용자 역할에 대한 자세한 내용은 사용자 역할 [Enterprise 참조하세요.](understand-ea-roles.md#enterprise-user-roles)

## <a name="add-an-azure-ea-account"></a>Azure EA 계정 추가

Azure EA 계정은 Azure Portal 조직 구성 단위입니다. Azure Portal _계정이라고_ 합니다. 구독을 관리하는 데 사용되며, 보고하는 데에도 사용됩니다. Azure 서비스에 액세스하여 사용하려면 계정을 만들거나 다른 사람이 대신 계정을 만들어주어야 합니다. 계정에 대한 자세한 내용은 계정 [추가를 참조하세요.](#add-an-account-and-account-owner)

## <a name="enable-the-enterprise-devtest-offer"></a>Enterprise 개발/테스트 제안 사용

EA 관리자는 조직의 계정 소유자가 EA 개발/테스트 제안을 기반으로 구독을 만들도록 허용할 수 있습니다. 이렇게 하려면 계정 속성에서 **개발/테스트** 옵션을 선택합니다. 개발/테스트 옵션을 선택한 후 개발/테스트 구독자 팀에 필요한 EA 개발/테스트 구독을 만들 수 있도록 계정 소유자에게 알릴 수 있습니다. 이 제안을 통해 활성 Visual Studio 구독자는 Azure에서 개발 및 테스트 워크로드를 특별한 개발/테스트 요율로 실행할 수 있습니다. Windows 8.1 및 Windows 10을 포함하여 개발/테스트 이미지의 전체 갤러리에 액세스할 수 있습니다.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Enterprise 개발/테스트 제안을 설정하려면

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 왼쪽 메뉴에서 청구 **범위를** 선택한 다음, 청구 계정 범위를 선택합니다.
1. 왼쪽 메뉴에서 계정 **을 선택합니다.**
1. 개발/테스트 액세스를 사용하도록 설정할 계정을 선택합니다.
1. 등록 계정 페이지에서 **편집을** 선택합니다.
1. 계정 편집 페이지에서 **개발/테스트를** 선택한 다음, **저장을** 선택합니다.

사용자가 계정 소유자로 추가되면 종량제 개발/테스트 제품 또는 Visual Studio 구독자에 대한 월간 크레딧 제안을 기반으로 하는 사용자와 연결된 모든 Azure 구독이 EA 개발/테스트 제안으로 변환됩니다. 계정 소유자와 연결된 종량제와 같은 다른 제품 유형을 기반으로 하는 구독은 Microsoft Azure Enterprise 제안으로 변환됩니다.

현재 개발/테스트 제안은 Azure Gov 고객에게 적용되지 않습니다.

## <a name="create-a-subscription"></a>구독 만들기

계정 소유자는 구독을 보고 관리할 수 있습니다. 구독을 사용하여 조직 내 팀에게 개발 환경 및 프로젝트에 대한 액세스 권한을 부여할 수 있습니다. 예를 들어 다음과 같습니다. 

- 테스트
- 프로덕션
- 개발
- 준비

각 애플리케이션 환경에 대해 여러 구독을 만들 때 각 환경의 보안을 유지하는 데 도움이 됩니다.

- 또한 각 구독에 서로 다른 서비스 관리자 계정을 할당할 수 있습니다.
- 원하는 만큼 구독을 서비스에 연결할 수 있습니다.
- 계정 소유자는 구독을 만들고 계정의 각 구독에 서비스 관리자 계정을 할당합니다.

EA 관리자 구독 관리 비디오를 [확인하세요.](https://www.youtube.com/watch?v=KFfcg2eqPo8) Azure Portal 시리즈 비디오 시리즈에서 [직접 Enterprise 고객 청구 환경의](https://www.youtube.com/playlist?list=PLeZrVF6SXmsoHSnAgrDDzL0W5j8KevFIm) 일부입니다.

>[!VIDEO https://www.youtube.com/embed/KFfcg2eqPo8]

## <a name="add-a-subscription"></a>구독 추가

계정 소유자는 등록 계정 내에서 구독을 만듭니다. 계정에 구독을 처음으로 추가할 때 MOSA(Microsoft 온라인 정기가입 계약) 및 요금제를 수락하라는 메시지가 표시됩니다. MOSA 및 요금제는 기업계약 고객에게는 적용되지 않지만 구독을 만드는 데 필요합니다. Microsoft Azure 기업계약 등록 수정계약은 이전 항목을 대체하며 계약 관계는 변경되지 않습니다. 메시지가 표시되면 약관에 동의함을 나타내는 옵션을 선택합니다.

_Microsoft Azure Enterprise_   구독을 만들 때의 기본 이름입니다. 등록 내의 다른 구독과 구분하고 엔터프라이즈 수준의 보고서에서 구독을 식별할 수 있도록 이름을 변경할 수 있습니다.

### <a name="to-add-a-subscription"></a>구독을 추가하려면

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 왼쪽 메뉴에서 **청구 범위** 를 선택한 후 등록에 대 한 청구 계정 범위를 선택 합니다.
1. 왼쪽 메뉴에서 **계정** 을 선택 합니다.
1. 등록 계정 목록에서 구독을 만들려는 구독을 선택 합니다.
1. 등록 계정 페이지에서 **+ 새 구독** 을 선택 합니다.  
    :::image type="content" source="./media/direct-ea-administration/new-subscription.png" alt-text="새 구독 옵션을 보여 주는 스크린샷" lightbox="./media/direct-ea-administration/new-subscription.png" :::
1. 구독 만들기 페이지에서 구독 이름을 입력 하 고 **만들기** 를 선택 합니다.

구독 이름이 보고서에 표시됩니다. 이 이름은 개발 포털의 구독과 연결된 프로젝트 이름입니다.

새 구독이 구독 목록에 표시될 때까지 최대 24시간이 걸릴 수 있습니다. 구독을 만든 후에는 다음을 수행할 수 있습니다.

- 구독 세부 정보 편집
- 구독 서비스 관리

Azure 구독 페이지로 이동 하 고 **+ 추가** 를 선택 하 여 구독을 만들 수도 있습니다.

## <a name="cancel-a-subscription"></a>구독 취소

계정 소유자만 자신의 구독을 취소할 수 있습니다.

계정 소유자인 구독을 삭제하려면,

1. 계정과 연결 된 자격 증명을 사용 하 여 Azure Portal에 로그인 합니다.
1. 탐색 메뉴에서 **구독** 을 선택 합니다.
1. 구독을 선택합니다.
1. 구독 정보 페이지의 왼쪽 위 모서리에 있는 **구독을 취소** 합니다.
1. 구독 이름을 입력 하 고 취소 이유를 선택한 다음 **취소** 를 선택 합니다.

자세한 내용은 [구독을 취소 하면 어떻게 되나요?](cancel-azure-subscription.md#what-happens-after-subscription-cancellation)를 참조 하세요.

## <a name="delete-an-enrollment-account"></a>등록 계정 삭제

활성 구독이 없는 경우에만 등록 계정을 삭제할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes)에 로그인합니다.
1. **Cost Management + 청구** 로 이동합니다.
1. 왼쪽 메뉴에서 **청구 범위** 를 선택한 다음 청구 계정 범위를 선택 합니다.
1. 왼쪽 메뉴에서 **계정** 을 선택 합니다.
1. 계정 목록에서 삭제 하려는 계정을 검색 합니다.
1. 삭제 하려는 계정 행에서 줄임표 (**...**) 기호를 선택한 다음 **삭제** 를 선택 합니다.
1. 계정 삭제 페이지에서 **예,이 계정** 확인을 삭제 합니다 .를 선택 하 고 **삭제** 를 선택 합니다.

## <a name="azure-sponsorship-offer"></a>Azure 스폰서쉽 제안

Azure 스폰서쉽 제안은 제한된 후원을 받는 Microsoft Azure 계정입니다. 이 제안은 이메일 초대를 통해 Microsoft에서 선택한 제한된 고객에게만 제공됩니다. Microsoft Azure 스폰서쉽 제안을 받을 자격이 있는 경우 계정 ID에 대한 이메일 초대장을 받게 됩니다.

자세한 내용은 [후원 활성화에 대 한 지원 요청](https://aka.ms/azrsponsorship)을 만듭니다.

## <a name="convert-to-work-or-school-account-authentication"></a>회사 또는 학교 계정 인증으로 변환

Azure Enterprise 사용자는 Microsoft 계정 (MSA 또는 Live ID)에서 회사 또는 학교 계정으로 변환할 수 있습니다. 회사 또는 학교 계정은 Azure Active Directory 인증 유형을 사용 합니다.

### <a name="to-begin"></a>시작 하려면

1. 필요한 역할의 Azure Portal에 회사 또는 학교 계정을 추가 합니다.
1. 오류가 발생 하면 Azure Active Directory에서 계정이 유효 하지 않을 수 있습니다. Azure는 UPN(사용자 계정 이름)을 사용하며, 항상 이메일 주소와 동일하지는 않습니다.
1. 회사 또는 학교 계정을 사용 하 여 Azure Portal에 인증 합니다.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Microsoft 계정에서 회사 또는 학교 계정으로 구독을 변환 하려면

1. 구독을 소유한 Microsoft 계정를 사용 하 여 Azure Portal에 로그인 합니다.
1. 계정 소유권 이전을 사용 하 여 새 계정으로 이동 합니다.
1. Microsoft 계정은 활성 구독에서 무료로 제공 되 고 삭제할 수 있습니다.
1. 삭제 된 계정은 기록 청구 이유로 Azure Portal 비활성 상태와 함께 볼 수 있습니다. **활성 계정만 표시** 를 선택 하 여 보기에서 필터링 할 수 있습니다.

## <a name="azure-ea-term-glossary"></a>Azure EA 사용 약관 용어집

**계정**<br>
조직 구성 단위입니다. 구독 및 보고를 관리 하는 데 사용 됩니다.

**계정 소유자**<br>
Azure에서 구독 및 서비스 관리자를 관리하는 사람입니다. 이 계정 및 계정과 연결된 구독의 사용량 데이터를 볼 수 있습니다.

**수정 구독**<br>
등록 수정계약에 따라 체결된 1년 또는 그와 동일한 구독입니다.

**선불**<br>
이 선불에 대 한 사용량에 대해 할인 된 선불 요금으로 Azure 서비스의 연간 금액 금액을 선불 합니다.

**부서 관리자**<br>
부서를 관리하고, 새 계정 및 계정 소유자를 만들고, 관리하는 부서의 사용량 세부 정보를 살펴보고, 권한이 부여되면 비용을 살펴볼 수 있는 사람입니다.

**등록 번호**<br>
기업계약과 관련된 특정 등록을 식별하기 위해 Microsoft에서 제공하는 고유 식별자입니다.

**엔터프라이즈 관리자**<br>
Azure에서 부서, 부서 소유자, 계정 및 계정 소유자를 관리하는 사람입니다. 엔터프라이즈 관리자를 관리 하 고 엔터프라이즈 등록과 관련 된 모든 계정 및 구독에서 사용 데이터, 청구 된 수량 및 청구 되지 않은 요금을 볼 수 있습니다.

**Enterprise 규약**<br>
Microsoft 기술을 기반으로 조직 전체를 표준화하고 Microsoft 소프트웨어 표준에 따라 정보 기술 인프라를 유지하려는 중앙 집중식 구매 방식을 사용하는 고객을 위한 Microsoft 라이선스 계약입니다.

**Enterprise 규약 등록**<br>
Microsoft 제품을 할인된 가격으로 대량 제공하는 기업계약 프로그램의 등록입니다.

**Microsoft 계정**<br>
참여하는 사이트에서 단일 자격 증명 세트로 사용자를 인증할 수 있게 해주는 웹 기반 서비스입니다.

**Microsoft Azure Enterprise 등록 수정 (등록 수정)**<br>
기업에서 서명한 수정계약으로, 엔터프라이즈 등록의 일부로 Azure에 대한 액세스 권한을 제공합니다.

**소비 된 리소스 수량**<br>
한 달 동안 사용된 개별 Azure 서비스의 수량입니다.

**서비스 관리자**<br>
구독 및 개발 프로젝트에 액세스 하 고 관리 하는 사람입니다.

**구독**<br>
Azure EA 구독을 나타내며 동일한 서비스 관리자가 관리 하는 Azure 서비스의 컨테이너입니다.

**회사 또는 학교 계정**<br>
클라우드의 페더레이션을 사용 하 여 Azure Active Directory 설정 하 고 모든 계정이 단일 테 넌 트에 있는 조직의 경우.

## <a name="enrollment-status"></a>등록 상태

**새로 만들기**<br>
이 상태는 24 시간 이내에 만들어진 등록에 할당 되며 24 시간 이내에 보류 중 상태로 업데이트 됩니다.

**보류 중**<br>
등록 관리자가 Azure Portal에 로그인 해야 합니다. 로그인하면 등록이 활성 상태로 전환됩니다.

**활성**<br>
등록이 활성화 되 고 Azure Portal에서 계정 및 구독을 만들 수 있습니다. 등록은 기업계약 종료 날짜가 될 때까지 활성 상태로 유지됩니다.

**무한 확장 된 용어**<br>
무한정 확장된 기간은 기업계약 종료 날짜 후에도 발생합니다. 확장된 기간에 옵트인한 Azure EA 고객은 기업계약이 종료된 후에도 무기한으로 Azure 서비스를 계속 사용할 수 있습니다.

Azure EA 등록이 기업계약 종료 날짜에 도달하기 전에 등록 관리자는 다음 옵션 중에 선택해야 합니다.

- Azure 선불을 더 추가하여 등록을 갱신합니다.
- 새 등록으로 양도
- MOSP(Microsoft Online Subscription 프로그램)로 마이그레이션
- 등록과 연결된 모든 서비스의 비활성화 확인

**만료됨**<br>
Azure EA 고객이 연장된 기간에서 옵트아웃되었으며, Azure EA 등록이 기업계약 종료 날짜에 도달했습니다. 등록이 만료되고 연결된 모든 서비스를 사용할 수 없게 됩니다.

**전송**<br>
연결된 모든 계정과 서비스가 새 등록으로 양도된 등록은 양도됨 상태로 표시됩니다.
 > [!NOTE]
 > 갱신할 때 새 등록 번호가 생성되면 등록이 자동으로 양도되지 않습니다. 이전 등록 번호가 갱신 서류에 포함되어 있어야만 자동으로 양도됩니다.

## <a name="next-steps"></a>다음 단계

- EA 등록에 대한 Azure 지원 요청을 만들어야 하는 경우 [기업계약 문제에 대한 Azure 지원 요청을 만드는 방법을](how-to-create-azure-support-request-ea.md)참조하세요.
- EA 구독 소유권에 대한 질문은 [Cost Management + Billing FAQ](../cost-management-billing-faq.yml)를 참조하세요.