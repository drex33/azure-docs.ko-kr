---
title: Azure EA Portal 관리
description: 이 문서에서는 관리자가 Azure EA Portal에서 수행하는 일반적인 작업에 대해 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 08/25/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperf-fy21q1
ms.openlocfilehash: bf887ad515f8c4349fcff54d25be1c70a30d018f
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123098322"
---
# <a name="azure-ea-portal-administration"></a>Azure EA Portal 관리

이 문서에서는 관리자가 Azure EA Portal(https://ea.azure.com) 에서 수행하는 일반적인 작업에 대해 설명합니다. Azure EA Portal은 고객이 Azure EA 서비스의 비용을 관리할 수 있는 온라인 관리 포털입니다. Azure EA Portal을 소개하는 내용은 [Azure EA Portal 시작](ea-portal-get-started.md) 문서를 참조하세요.

## <a name="activate-your-enrollment"></a>등록 활성화

서비스를 활성화하려면 초기 엔터프라이즈 관리자는 [Azure Enterprise Portal](https://ea.azure.com)을 열고 초대 이메일의 이메일 주소를 사용하여 로그인합니다.

엔터프라이즈 관리자로 설정된 경우에는 활성화 이메일을 받을 필요가 없습니다. [Azure Enterprise Portal](https://ea.azure.com)로 이동하여 회사, 학교 또는 Microsoft 계정 이메일 주소와 암호로 로그인합니다.

등록이 여러 개 있는 경우 하나를 선택하여 활성화합니다. 기본적으로 활성 등록만 표시됩니다. 등록 기록을 보려면 Azure Enterprise Portal의 오른쪽 위에서 **활성** 옵션을 선택 취소합니다.

**등록** 아래에 상태가 **활성** 으로 표시됩니다.

![활성 등록을 보여주는 예제](./media/ea-portal-administration/ea-enrollment-status.png)

기존 Azure 엔터프라이즈 관리자만이 다른 엔터프라이즈 관리자를 만들 수 있습니다.

### <a name="create-another-enterprise-administrator"></a>다른 엔터프라이즈 관리자 만들기

시나리오에 따라 다음 옵션 중 하나를 선택합니다.

#### <a name="if-youre-already-an-enterprise-administrator"></a>엔터프라이즈 관리자인 경우

1. [Azure Enterprise Portal](https://ea.azure.com)에 로그인합니다.
1. **관리** > **등록 세부 정보** 로 이동합니다.
1. 오른쪽 위에서 **+ 관리자 추가** 를 선택합니다.

회사, 학교 또는 Microsoft 계정처럼 사용자의 이메일 주소와 기본 인증 방법이 있는지 확인합니다.

#### <a name="if-youre-not-an-enterprise-administrator"></a>엔터프라이즈 관리자가 아닌 경우

엔터프라이즈 관리자가 아니면 등록에 추가해 달라고 엔터프라이즈 관리자에게 요청하세요. 엔터프라이즈 관리자는 이전 단계를 수행하여 엔터프라이즈 관리자로 추가합니다. 등록에 추가되면 활성화 이메일을 받게 됩니다.

#### <a name="if-your-enterprise-administrator-cant-help-you"></a>엔터프라이즈 관리자가 도와줄 수 없는 경우

엔터프라이즈 관리자의 도움을 받을 수 없는 경우 [Azure 지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 작성합니다. 다음 정보를 지정합니다.

- 등록 번호
- 추가할 이메일 주소 및 인증 유형(회사, 학교 또는 Microsoft 계정)
- 기존 엔터프라이즈 관리자의 이메일 승인
  - 기존 엔터프라이즈 관리자에게 연락할 수 없는 경우 파트너 또는 소프트웨어 관리자에게 연락하여 VLSC(Volume Licensing Service Center) 도구를 통해 연락처 세부 정보를 변경해 달라고 요청합니다.

## <a name="create-an-azure-enterprise-department"></a>Azure 엔터프라이즈 부서 만들기

엔터프라이즈 관리자와 부서 관리자는 부서를 사용하여 부서 및 비용 센터별로 엔터프라이즈 Azure 서비스와 사용량을 구성하고 보고합니다. 엔터프라이즈 관리자는 다음과 같은 작업을 할 수 있습니다.

- 부서 추가 또는 제거
- 부서에 계정 연결
- 부서 관리자 만들기
- 부서 관리자가 가격 및 비용을 볼 수 있도록 허용

부서 관리자는 부서에 새 계정을 추가할 수 있습니다. 부서 관리자는 부서에서 계정을 제거할 수 있지만 등록에서 제거할 수는 없습니다.

부서를 추가하려면 다음을 수행합니다.

1. Azure Enterprise Portal에 로그인합니다.
1. 왼쪽 창에서 **관리** 를 선택합니다.
1. **부서** 탭을 선택한 다음, **+ 부서 추가** 를 선택합니다.
1. 정보를 입력합니다.
   부서 이름은 유일한 필수 필드입니다. 부서 이름은 3자 이상이어야 합니다.
1. 마쳤으면 **추가** 를 선택합니다.

## <a name="add-a-department-administrator"></a>부서 관리자 추가

부서를 만든 후 엔터프라이즈 관리자는 부서 관리자를 추가하고 각 부서 관리자를 부서에 연결할 수 있습니다. 부서 관리자는 해당 부서에 대해 다음과 같은 작업을 수행할 수 있습니다.

- 다른 부서 관리자 만들기
- 이름 또는 비용 센터와 같은 부서 속성을 살펴보고 편집
- 계정 추가
- 계정 제거
- 사용량 세부 정보 다운로드
- 월간 사용량 및 요금 보기<sup>1</sup>

> <sup>1</sup> 엔터프라이즈 관리자는 이러한 권한을 부여해야 합니다. 부서 월간 사용량 및 요금을 볼 수 있는 권한이 부여되었지만 볼 수 없는 경우 파트너에게 문의하세요.

### <a name="to-add-a-department-administrator"></a>부서 관리자 추가

엔터프라이즈 관리자로 다음을 수행합니다.

1. Azure Enterprise Portal에 로그인합니다.
1. 왼쪽 창에서 **관리** 를 선택합니다.
1. **부서** 탭을 선택한 다음, 부서를 선택합니다.
1. **+ 관리자 추가** 를 선택하고 필요한 정보를 추가합니다.
1. 읽기 전용 액세스의 경우 **읽기 전용** 옵션을 **예** 로 설정하고 **추가** 를 선택합니다.

![[부서 관리자 추가] 대화 상자를 보여주는 예제](./media/ea-portal-administration/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>읽기 전용 액세스 설정 방법

부서 관리자에게 읽기 전용 액세스 권한을 부여할 수 있습니다.

- 새 부서 관리자를 만들 때 읽기 전용 옵션을 **예** 로 설정합니다.

- 기존 부서 관리자를 편집하려면 다음을 수행합니다.
   1. 부서를 선택한 다음, 편집하려는 **부서 관리자** 옆에 있는 연필 기호를 선택합니다.
   1. 읽기 전용 열기를 **예** 로 설정하고 **저장** 을 선택합니다.

엔터프라이즈 관리자에게 자동으로 부서 관리자 권한이 할당됩니다.

## <a name="add-an-account"></a>계정 추가

계정 및 구독의 구조는 관리 방법과 청구서 및 보고서에 표시되는 방식에 영향을 줍니다. 비즈니스 부문, 직능 팀 및 지리적 위치에 따라 조직 구조를 만드는 것이 대표적인 예입니다.

계정을 추가하려면 다음을 수행합니다.

1. navigation Enterprise Portal의 왼쪽 탐색 영역에서 **관리** 를 선택합니다.
1. **계정** 탭을 선택합니다. **계정** 페이지에서 **+ 계정 추가** 를 선택합니다.
1. 부서를 선택하거나 할당되지 않은 상태로 두고 원하는 인증 유형을 선택합니다.
1. 보고서에서 계정을 식별하는 데 사용할 식별 이름을 입력합니다.
1. 새 계정과 연결할 **계정 소유자 이메일** 주소를 입력합니다.
1. 이메일 주소를 확인하고 **추가** 를 선택합니다.

![계정 목록과 [+계정 추가] 옵션을 보여주는 예제](./media/ea-portal-administration/create-ea-add-an-account.png)

다른 계정을 추가하려면 **다른 계정 추가** 를 선택하거나, 왼쪽에 있는 도구 모음의 오른쪽 아래 모서리에서 **추가** 를 선택합니다.

계정 소유권을 확인하려면 다음을 수행합니다.

1. Azure Enterprise Portal에 로그인합니다.
1. 상태를 확인합니다.

   상태가 **보류 중** 에서 **시작/종료 날짜** 로 변경될 것입니다. 시작/종료 날짜는 사용자가 처음 로그인한 날짜와 계약 종료 날짜입니다.
1. **경고** 메시지가 표시되면 계정 소유자는 Azure Enterprise Portal에 처음 로그인할 때 **계속** 을 클릭하여 계정을 활성화해야 합니다.

## <a name="change-azure-subscription-or-account-ownership"></a>Azure 구독 또는 계정 소유권 변경

이 섹션은 구독 소유자가 변경되는 경우에만 적용됩니다. 구독 소유권을 변경하는 데 Azure 지원 티켓이 필요하지 않습니다. 엔터프라이즈 관리자는 Azure Enterprise Portal을 사용하여 등록 시 선택한 구독 또는 모든 구독의 계정 소유권을 양도할 수 있습니다. 또한 구독 디렉터리(테넌트)를 변경하는 옵션도 있습니다. 

그러나 EA 관리자는 한 등록에서 다른 등록으로 계정을 양도할 수 없습니다. 한 등록에서 다른 등록으로 계정을 양도하려면 지원 요청이 필요합니다. 한 등록에서 다른 등록으로 계정을 양도하는 방법에 대한 내용은 [엔터프라이즈 계정을 새 등록으로 양도](ea-transfers.md#transfer-an-enterprise-account-to-a-new-enrollment)를 참조하세요.

종량제 구독 관리자는 이 동일한 프로세스를 사용하여 구독의 계정 소유권을 EA 등록으로 이전할 수도 있습니다.

구독 또는 계정 소유권 양도가 완료되면 Microsoft에서 계정 소유자를 업데이트합니다.

소유권 양도를 수행하기 전에 다음과 같은 Azure RBAC(Azure 역할 기반 액세스 제어) 정책을 이해합니다.

- 동일한 테넌트 내에 있는 두 조직 ID 간에 구독 또는 계정 소유권 양도를 수행할 때 Azure RBAC 정책, 기존 서비스 관리자 및 공동 관리자 역할이 유지됩니다.
- 테넌트 간에 구독 또는 계정 소유권 양도로 인해 Azure RBAC 정책과 역할 할당이 손실됩니다.
- 정책 및 관리자 역할은 디렉터리 간에 양도되지 않습니다. 서비스 관리자는 대상 계정의 소유자로 업데이트됩니다.
- 테넌트 간에 구독을 양도할 때 Azure RBAC 정책 및 역할 할당이 손실되지 않도록 하려면 **구독을 받는 사람의 Azure AD 테넌트로 이동** 확인란이 **선택 취소** 상태가 유지되도록 합니다. 그러면 현재 Azure AD 테넌트의 서비스, Azure 역할 및 정책이 유지되고 해당 계정에 대한 청구 소유권만 이전됩니다.  
    :::image type="content" source="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Azure AD 테넌트로 구독을 이동하기 위한 선택 취소 확인란을 보여주는 이미지" lightbox="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" :::


계정 소유자를 변경하기 전에 다음을 수행합니다.

1. Azure Enterprise Portal에서 **계정** 탭을 보고 원본 계정을 찾습니다. 원본 계정이 활성 상태여야 합니다.
1. 대상 계정을 찾아서 활성 상태인지 확인합니다.

모든 구독의 계정 소유권을 양도하려면 다음을 수행합니다.

1. Azure Enterprise Portal에 로그인합니다.
1. 왼쪽 탐색 영역에서 **관리** 를 선택합니다.
1. **계정** 탭을 선택하고 계정 위로 마우스를 가져갑니다.
1. 오른쪽에서 계정 소유자 변경 아이콘을 선택합니다. 사람처럼 생긴 아이콘입니다.  
    ![계정 소유자 변경 기호를 보여주는 이미지](./media/ea-portal-administration/create-ea-create-sub-transfer-account-ownership-of-sub.png)
1. 이전할 대상 계정을 선택한 후, **다음** 을 선택합니다.
1. Azure AD 테넌트 간에 계정 소유권을 양도하려면 **구독을 받는 사람의 Azure AD 테넌트로 이동** 확인란을 선택합니다.  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Azure AD 테넌트로 구독을 이동하기 위해 선택한 확인란을 보여주는 이미지" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. 양도를 확인하고 **제출** 을 선택합니다.

단일 구독의 계정 소유권을 양도하려면 다음을 수행합니다.

1. Azure Enterprise Portal에 로그인합니다.
1. 왼쪽 탐색 영역에서 **관리** 를 선택합니다.
1. **계정** 탭을 선택하고 계정 위로 마우스를 가져갑니다.
1. 오른쪽에서 구독 양도 아이콘을 선택합니다. 페이지처럼 생긴 아이콘입니다.  
    ![양도 구독 기호를 보여주는 이미지](./media/ea-portal-administration/ea-transfer-subscriptions.png)
1. 구독을 이전할 대상 계정을 선택한 후, **다음** 을 선택합니다.
1. Azure AD 테넌트 간에 구독 소유권을 양도하려면 **구독을 받는 사람의 Azure AD 테넌트로 이동** 확인란을 선택합니다.  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Azure AD 테넌트로 구독을 이동하기 위해 선택한 확인란을 보여주는 이미지" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. 양도를 확인하고 **제출** 을 선택합니다.


## <a name="associate-an-account-to-a-department"></a>부서에 계정 연결

엔터프라이즈 관리자는 기존 계정을 등록된 [부서]에 연결할 수 있습니다.

### <a name="to-associate-an-account-to-a-department"></a>계정을 부서에 연결하려면

1. Azure EA Portal에 엔터프라이즈 관리자로 로그인합니다.
1. 왼쪽 탐색 영역에서 **관리** 를 선택합니다.
1. **부서** 를 선택합니다.
1. 마우스로 계정이 있는 행 위를 가리키고, 오른쪽에 있는 연필 아이콘을 선택합니다.
1. 드롭다운 메뉴에서 부서를 선택합니다.
1. **저장** 을 선택합니다.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>종량제 구독에 기존 계정 연결

Azure Portal에 기존 Microsoft Azure 계정이 이미 있는 경우 연결된 회사, 학교 또는 Microsoft 계정을 입력하여 기업계약 등록과 연결합니다.

### <a name="associate-an-existing-account"></a>기존 계정 연결

1. Azure Enterprise Portal에서 **관리** 를 선택합니다.
1. **계정** 탭을 선택합니다.
1. **+계정 추가** 를 선택합니다.
1. 기존 Azure 계정과 연결된 회사, 학교 또는 Microsoft 계정을 입력합니다.
1. 기존 Azure 계정과 연결된 계정을 확인합니다.
1. 보고서에서 이 계정을 식별하는 데 사용할 이름을 입력합니다.
1. **추가** 를 선택합니다.
1. 계정을 추가하려면 **+계정 추가** 옵션을 다시 선택하거나, **관리** 단추를 선택하여 홈페이지로 돌아갑니다.
1. **계정** 페이지를 열면 새로 추가된 계정이 **보류 중** 상태로 표시됩니다.

### <a name="confirm-account-ownership"></a>계정 소유권 확인

1. 입력한 회사, 학교 또는 Microsoft 계정과 연결된 이메일 계정에 로그인합니다.
1. _"Microsoft 볼륨 라이선스의 Microsoft Azure 서비스에서 계정 활성화를 위한 초대"_ 라는 제목의 이메일 알림을 엽니다.
1. 초대 이메일에서 **Microsoft Azure Enterprise Portal에 로그인** 링크를 클릭합니다.
1. **로그인** 을 선택합니다.
1. 회사, 학교 또는 Microsoft 계정과 암호를 입력하여 로그인하고 계정 소유권을 확인합니다.

### <a name="azure-marketplace"></a>Azure Marketplace

대부분의 구독은 종량제 환경에서 Azure 기업계약으로 변환할 수 있지만, Azure Marketplace 서비스는 그렇지 않습니다. 모든 구독과 요금을 단일 보기에서 보려면 다음과 같이 Azure Marketplace 서비스를 Azure Enterprise Portal에 추가하는 것이 좋습니다.

1. Azure Enterprise Portal에 로그인합니다.
1. 왼쪽 탐색 영역에서 **관리** 를 선택합니다.
1. **등록 탭** 을 선택합니다.
1. **등록 세부 정보** 섹션을 봅니다.
1. Azure Marketplace 필드 오른쪽에서 연필 아이콘을 선택하여 사용하도록 설정합니다. **저장** 을 선택합니다.

이제 계정 소유자는 이전에 종량제 구독에서 소유했던 Azure Marketplace 서비스를 구매할 수 있습니다.

Azure EA 등록에서 새 Azure Marketplace 구독이 활성화되면 종량제 환경에서 만든 Azure Marketplace 서비스를 취소합니다. 종량제 결제 방법이 만료될 때 Azure Marketplace 구독이 잘못된 상태로 전환되지 않도록 이 단계를 수행해야 합니다.

### <a name="msdn"></a>MSDN

MSDN 구독은 MSDN 개발/테스트로 자동 변환되며 Azure EA 제품은 기존 금액 크레딧을 잃게 됩니다.

### <a name="azure-in-open"></a>Azure in Open

Azure in Open 구독을 기업계약과 연결하면 사용하지 않은 Azure in Open 크레딧을 돌려받지 못하게 됩니다. 따라서 기업계약에 계정을 추가하기 전에 Azure in Open 구독의 모든 크레딧을 사용하는 것이 좋습니다.  

### <a name="accounts-with-support-subscriptions"></a>지원 구독이 있는 계정

기업계약에 지원 구독이 없는 상태에서 지원 구독이 있는 기존 계정을 Azure Enterprise Portal에 추가하면 MOSA 지원 구독이 자동으로 양도되지 않습니다. 유예 기간 동안 다음 달 말일까지 Azure EA에서 지원 구독을 다시 구매해야 합니다.

## <a name="department-spending-quotas"></a>부서 지출 할당액

EA 고객은 등록계약에 따라 각 부서의 지출 할당액을 설정하거나 변경할 수 있습니다. 지출 할당 금액은 현재 선불 기간에 대해 설정됩니다. 현재 선불 기간이 종료되면 값이 업데이트되지 않는 한 시스템에서 기존 지출 할당액을 다음 선불 기간으로 연장합니다.

부서 관리자는 지출 할당액을 볼 수 있으며, 엔터프라이즈 관리자만 할당액을 업데이트할 수 있습니다. 할당액이 50%, 75%, 90%, 100%에 도달하면 엔터프라이즈 관리자와 부서 관리자는 알림을 받게 됩니다.

### <a name="enterprise-administrator-to-set-the-quota"></a>엔터프라이즈 관리자가 할당액을 설정하려면,

 1. Azure EA Portal을 엽니다.
 1. 왼쪽 탐색 영역에서 **관리** 를 선택합니다.
 1. **부서** 탭을 선택합니다.
 1. [부서]를 선택합니다.
 1. [부서 세부 정보] 섹션에서 연필 기호를 선택하거나 **+ 부서 추가** 기호를 선택하여 새 부서와 함께 지출 할당액을 추가합니다.
 1. [부서 세부 정보] 아래의 [지출 할당액 $] 상자에서 지출 할당 금액을 등록계약의 통화 단위로 입력합니다(0보다 커야 함).
    - 이 시점에서 부서 이름과 비용 센터도 편집할 수 있습니다.
 1. **저장** 을 선택합니다.

이제 부서 지출 할당액이 [부서] 탭 아래의 [부서 목록] 보기에 표시됩니다. 현재 선불이 종료되면 Azure EA Portal은 다음 선불 기간에 대한 지출 할당액을 유지합니다.

부서 할당 금액은 현재 Azure 선불과 독립적이며, 할당 금액과 경고는 자사의 사용에만 적용됩니다. 부서 지출 할당액은 정보 제공용으로만 제공되며 지출 한도를 적용하지 않습니다.

### <a name="department-administrator-to-view-the-quota"></a>부서 관리자가 할당액을 보려면,

1. Azure EA Portal을 엽니다.
1. 왼쪽 탐색 영역에서 **관리** 를 선택합니다.
1. **부서** 탭을 선택하고, 지출 할당액이 있는 [부서 목록] 보기를 살펴봅니다.

간접 고객인 경우 채널 파트너가 비용 기능을 사용하도록 설정해야 합니다.

## <a name="enterprise-user-roles"></a>엔터프라이즈 사용자 역할

Azure EA Portal에서 Azure EA 비용 및 사용량을 관리할 수 있습니다. Azure EA Portal에는 다음과 같은 세 가지 주요 역할이 있습니다.

- EA 관리자
- 부서 관리자
- 계정 소유자

각 역할의 액세스 및 권한 수준은 서로 다릅니다.

사용자 역할에 대한 자세한 내용은 [엔터프라이즈 사용자 역할](./understand-ea-roles.md#enterprise-user-roles)을 참조하세요.

## <a name="add-an-azure-ea-account"></a>Azure EA 계정 추가

Azure EA 계정은 Azure EA Portal의 조직 구성 단위입니다. 구독을 관리하는 데 사용되며, 보고하는 데에도 사용됩니다. Azure 서비스에 액세스하여 사용하려면 계정을 만들거나 다른 사람이 대신 계정을 만들어주어야 합니다.

Azure 계정에 대한 자세한 내용은 [계정 추가](#add-an-account)를 참조하세요.

## <a name="enterprise-devtest-offer"></a>Enterprise 개발/테스트 제안

Azure 엔터프라이즈 관리자는 조직의 계정 소유자가 EA 개발/테스트 제안을 기반으로 하여 구독을 만들 수 있도록 설정할 수 있습니다. 이렇게 하려면 Azure EA Portal에서 계정 소유자에 대한 개발/테스트 상자를 선택합니다.

[개발/테스트] 확인란이 선택되면 개발/테스트 구독자 팀에 필요한 EA 개발/테스트 구독을 설정할 수 있도록 계정 소유자에게 알려줍니다.

이 제안을 통해 활성 Visual Studio 구독자는 Azure에서 개발 및 테스트 워크로드를 특별한 개발/테스트 요율로 실행할 수 있습니다. Windows 8.1 및 Windows 10을 포함하여 개발/테스트 이미지의 전체 갤러리에 액세스할 수 있습니다.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Enterprise 개발/테스트 제안을 설정하려면,

1. 엔터프라이즈 관리자 권한으로 로그인합니다.
1. 왼쪽 탐색 영역에서 **관리** 를 선택합니다.
1. **계정** 탭을 선택합니다.
1. 개발/테스트 액세스를 사용하도록 설정하려는 계정의 행을 선택합니다.
1. 행의 오른쪽에 있는 연필 기호를 선택합니다.
1. [개발/테스트] 확인란을 선택합니다.
1. **저장** 을 선택합니다.

Azure EA Portal을 통해 사용자가 계정 소유자로 추가되면 PAYG 개발/테스트 제안 또는 Visual Studio 구독자를 위한 월별 크레딧 제안을 기반으로 하는 계정 소유자와 연결된 모든 Azure 구독이 EA 개발/테스트 제안으로 변환됩니다. 계정 소유자와 관련된 다른 제안 유형(예: PAYG)을 기반으로 하는 구독은 Microsoft Azure 엔터프라이즈 제안으로 변환됩니다.

개발/테스트 제안은 현재 Azure Gov 고객에게 적용되지 않습니다.

## <a name="create-a-subscription"></a>구독 만들기

계정 소유자는 구독을 보고 관리할 수 있습니다. 구독을 사용하여 조직 내 팀에게 개발 환경 및 프로젝트에 대한 액세스 권한을 부여할 수 있습니다. 테스트, 프로덕션, 개발 및 준비를 예로 들 수 있습니다.

각 애플리케이션 환경에 대해 여러 구독을 만들 때 각 환경의 보안을 유지하는 데 도움이 됩니다.

- 또한 각 구독에 서로 다른 서비스 관리자 계정을 할당할 수 있습니다.
- 원하는 만큼 구독을 서비스에 연결할 수 있습니다.
- 계정 소유자는 구독을 만들고 계정의 각 구독에 서비스 관리자 계정을 할당합니다.

### <a name="add-a-subscription"></a>구독 추가

다음 정보를 사용하여 구독을 추가합니다.

계정에 구독을 처음으로 추가할 때 MOSA(Microsoft 온라인 정기가입 계약) 및 요금제를 수락하라는 메시지가 표시됩니다. MOSA 및 요금제는 기업계약 고객에게는 적용되지 않지만 구독을 만드는 데 필요합니다. Microsoft Azure 기업계약 등록 수정 사항이 위의 항목을 대체하며 기업계약 고객의 계약 관계는 변경되지 않습니다. 메시지가 표시되면 약관에 동의하는 상자를 선택합니다.

_Microsoft Azure 엔터프라이즈_ 는 구독을 만들 때의 기본 이름입니다. 등록 내의 다른 구독과 구분하고 엔터프라이즈 수준의 보고서에서 구독을 식별할 수 있도록 이름을 변경할 수 있습니다.

구독을 추가하려면 다음을 수행합니다.

1. Azure Enterprise Portal에서 계정에 로그인합니다.
1. **관리** 탭을 선택한 다음, 페이지 위쪽에서 **구독** 을 선택합니다.
1. 계정 소유자로 로그인했는지 확인합니다.
1. **+ 구독 추가** 를 선택하고 **구매** 를 선택합니다.

   계정에 구독을 처음으로 추가할 때 연락처 정보를 입력해야 합니다. 구독을 추가하면 연락처 정보가 자동으로 추가됩니다.

1. **구독** 을 선택한 다음, 앞에서 만든 구독을 선택합니다.
1. **구독 세부 사항 편집** 을 선택합니다.
1. **구독 이름** 및 **서비스 관리자** 를 편집한 다음, 확인 표시를 선택합니다.

   구독 이름이 보고서에 표시됩니다. 이 이름은 개발 포털의 구독과 연결된 프로젝트 이름입니다.

새 구독이 구독 목록에 표시될 때까지 최대 24시간이 걸릴 수 있습니다. 구독을 만든 후에는 다음을 수행할 수 있습니다.

- [구독 세부 정보 편집](https://portal.azure.com)
- [구독 서비스 관리](https://portal.azure.com/#home)

## <a name="delete-subscription"></a>구독 삭제

계정 소유자인 구독을 삭제하려면,

1. 계정에 연결된 자격 증명을 사용하여 Azure Portal에 로그인합니다.
1. 허브 메뉴에서 **구독** 을 선택합니다.
1. 페이지의 왼쪽 위 모서리에 있는 구독 탭에서 취소하려는 구독을 선택하고, **구독 취소** 를 선택하여 취소 탭을 시작합니다.
1. 구독 이름을 입력하고, 취소 이유를 선택하고, **구독 취소** 단추를 선택합니다.

계정 관리자만 구독을 취소할 수 있습니다.

자세한 내용은 [구독이 취소되면 어떻게 되나요?](cancel-azure-subscription.md#what-happens-after-subscription-cancellation)를 참조하세요.

## <a name="delete-an-account"></a>계정 삭제

활성 구독이 없는 활성 계정만 제거할 수 있습니다.

1. Enterprise Portal의 왼쪽 탐색 영역에서 **관리** 를 선택합니다.
1. **계정** 탭을 선택합니다.
1. [계정] 테이블에서 삭제하려는 계정을 선택합니다.
1. [계정] 행의 오른쪽에 있는 X 기호를 선택합니다.
1. 계정에 활성 구독이 없으면 [계정] 행 아래에서 **예** 를 선택하여 계정 제거를 확인합니다.

## <a name="update-notification-settings"></a>알림 설정 업데이트

엔터프라이즈 관리자는 등록과 연결된 사용 알림을 받도록 자동으로 등록됩니다. 각 엔터프라이즈 관리자는 개별 알림의 간격을 변경하거나 알림을 완전히 해제할 수 있습니다.

알림 연락처는 Azure EA Portal의 **알림 연락처** 섹션에 표시됩니다. 조직 내 적절한 사용자가 Azure EA 알림을 받도록 알림 연락처를 관리할 수 있습니다.

현재 알림 설정을 보려면 다음을 수행합니다.

1. Azure EA Portal에서 **관리** > **알림 연락처** 로 이동합니다.
2. 이메일 주소 – 알림을 받는 엔터프라이즈 관리자의 Microsoft 계정, 회사 또는 학교 계정과 연결된 이메일 주소입니다.
3. 미청구 잔액 알림 빈도 – 각 개별 엔터프라이즈 관리자에게 알림으로 보내도록 설정되는 빈도입니다.

연락처를 추가하려면 다음을 수행합니다.

1. **+ 연락처 추가** 를 선택합니다.
2. 이메일 주소를 입력하고 확인합니다.
3. **저장** 을 선택합니다.

새 알림 연락처가 **알림 연락처** 섹션에 표시됩니다. 알림 빈도를 변경하려면 알림 연락처를 선택하고, 선택한 행의 오른쪽에 있는 연필 기호를 선택합니다. 빈도를 **매일**, **매주**, **매월** 또는 **없음** 으로 설정합니다.

_적용 기간 종료 날짜 임박_ 및 _비활성화 및 프로비전 해제 날짜 임박_ 수명 주기 알림을 사용하지 않을 수 있습니다. 수명 주기 알림을 사용하지 않도록 설정하면 적용 기간 및 계약 종료 날짜에 대한 알림이 표시되지 않습니다.

## <a name="azure-sponsorship-offer"></a>Azure 스폰서쉽 제안

Azure 스폰서쉽 제안은 제한된 후원을 받는 Microsoft Azure 계정입니다. 이 제안은 이메일 초대를 통해 Microsoft에서 선택한 제한된 고객에게만 제공됩니다. Microsoft Azure 스폰서쉽 제안을 받을 자격이 있는 경우 계정 ID에 대한 이메일 초대장을 받게 됩니다.

자세한 내용을 알아보려면 [스폰서쉽 활성화 지원 요청](https://aka.ms/azrsponsorship)을 작성하여 제출하세요.

## <a name="conversion-to-work-or-school-account-authentication"></a>회사 또는 학교 계정 인증으로 변환

Azure Enterprise 사용자는 Microsoft 계정(MSA 또는 Live ID)에서 회사 또는 학교 계정(Azure Active Directory 사용) 인증 유형으로 변환할 수 있습니다.

시작하려면

1. 필요한 역할의 작업 또는 학교 계정을 Azure EA Portal에 추가합니다.
1. 오류가 발생하면 계정이 활성 디렉터리에서 유효하지 않을 수 있습니다.  Azure는 UPN(사용자 계정 이름)을 사용하며, 항상 이메일 주소와 동일하지는 않습니다.
1. 회사 또는 학교 계정을 사용하여 Azure EA Portal에 인증합니다.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>구독을 Microsoft 계정에서 회사 또는 학교 계정으로 변환하려면

1. 구독을 소유한 Microsoft 계정을 사용하여 관리 포털에 로그인합니다.
1. 계정 소유권 이전을 사용하여 새 계정으로 이전합니다.
1. 이제 Microsoft 계정은 활성 구독에서 구속되지 않으므로 삭제할 수 있습니다.
1. 삭제된 계정은 과거의 청구 이유로 인해 포털에서 비활성 상태로 유지됩니다.  활성 계정만 표시하는 확인란을 선택하여 보기에서 필터링할 수 있습니다.

## <a name="azure-ea-term-glossary"></a>Azure EA 사용 약관 용어집

- **계정**: Azure Enterprise Portal의 조직 구성 단위입니다. 구독을 관리하고 보고하는 데 사용됩니다.
- **계정 소유자**: Azure에서 구독 및 서비스 관리자를 관리하는 사람입니다. 이 계정 및 계정과 연결된 구독의 사용량 데이터를 볼 수 있습니다.
- **수정 구독**: 등록 수정계약에 따라 체결된 1년 또는 그와 동일한 구독입니다.
- **선불**: Azure 서비스 요금을 선결제하는 대신 할인된 선불 금액으로 사용할 수 있는 연간 선불 금액입니다.
- **부서 관리자**: 부서를 관리하고, 새 계정 및 계정 소유자를 만들고, 관리하는 부서의 사용량 세부 정보를 살펴보고, 권한이 부여되면 비용을 살펴볼 수 있는 사람입니다.
- **등록 번호**: 기업계약과 관련된 특정 등록을 식별하기 위해 Microsoft에서 제공하는 고유 식별자입니다.
- **엔터프라이즈 관리자**: Azure에서 부서, 부서 소유자, 계정 및 계정 소유자를 관리하는 사람입니다. 엔터프라이즈 관리자를 관리할 수 있을 뿐 아니라 엔터프라이즈 등록과 관련된 모든 계정과 구독의 사용 데이터, 청구된 수량 및 청구되지 않은 요금을 볼 수 있습니다.
- **기업계약**: Microsoft 기술을 기반으로 조직 전체를 표준화하고 Microsoft 소프트웨어 표준에 따라 정보 기술 인프라를 유지하려는 중앙 집중식 구매 방식을 사용하는 고객을 위한 Microsoft 라이선스 계약입니다.
- **기업계약 등록**: Microsoft 제품을 할인된 가격으로 대량 제공하는 기업계약 프로그램의 등록입니다.
- **Microsoft 계정**: 참여하는 사이트에서 단일 자격 증명 세트로 사용자를 인증할 수 있게 해주는 웹 기반 서비스입니다.
- **Microsoft Azure 엔터프라이즈 등록 수정계약(등록 수정계약)** : 기업에서 서명한 수정계약으로, 엔터프라이즈 등록의 일부로 Azure에 대한 액세스 권한을 제공합니다.
- **Azure Enterprise Portal**: 엔터프라이즈 고객이 Azure 계정과 관련 구독을 관리하는 데 사용하는 포털입니다.
- **사용된 리소스 수량**: 한 달 동안 사용된 개별 Azure 서비스의 수량입니다.
- **서비스 관리자**: Azure Enterprise Portal에서 구독 및 개발 프로젝트에 액세스하고 관리할 수 있는 사람입니다.
- **구독**: Azure Enterprise Portal 구독을 나타내며, 동일한 서비스 관리자가 관리하는 Azure 서비스의 컨테이너입니다.
- **회사 또는 학교 계정**: 클라우드에 대한 페더레이션을 사용하여 Azure Active Directory를 설정하고 모든 계정이 단일 테넌트에 있는 조직에 적합합니다.

### <a name="enrollment-statuses"></a>등록 상태

- **새로 만들기**: 이 상태는 24시간 이내에 만들어진 등록에 할당되며 24시간 이내에 보류 중 상태로 업데이트됩니다.
- **Pending**: 등록 관리자가 Azure Enterprise Portal에 로그인해야 합니다. 로그인하면 등록이 활성 상태로 전환됩니다.
- **활성**: 등록이 활성 상태이면 Azure Enterprise Portal에서 계정 및 구독을 만들 수 있습니다. 등록은 기업계약 종료 날짜가 될 때까지 활성 상태로 유지됩니다.
- **무한정 확장된 기간**: 무한정 확장된 기간은 기업계약 종료 날짜 후에도 발생합니다. 확장된 기간에 옵트인한 Azure EA 고객은 기업계약이 종료된 후에도 무기한으로 Azure 서비스를 계속 사용할 수 있습니다.

   Azure EA 등록이 기업계약 종료 날짜에 도달하기 전에 등록 관리자는 다음 옵션 중에 선택해야 합니다.

  - Azure 선불을 추가하여 등록 갱신
  - 새 등록으로 양도
  - MOSP(Microsoft Online Subscription 프로그램)로 마이그레이션
  - 등록과 연결된 모든 서비스의 비활성화 확인
- **만료됨**: Azure EA 고객이 연장된 기간에서 옵트아웃되었으며, Azure EA 등록이 기업계약 종료 날짜에 도달했습니다. 등록이 만료되고 연결된 모든 서비스를 사용할 수 없게 됩니다.
- **양도됨**: 연결된 모든 계정과 서비스가 새 등록으로 양도된 등록은 양도됨 상태로 표시됩니다.
  >[!NOTE]
  > 갱신할 때 새 등록 번호가 생성되면 등록이 자동으로 양도되지 않습니다. 이전 등록 번호가 갱신 서류에 포함되어 있어야만 자동으로 양도됩니다.

## <a name="next-steps"></a>다음 단계

- [가상 머신 예약](ea-portal-vm-reservations.md)을 통해 비용을 절감하는 방법에 대해 알아보세요.
- Azure EA Portal 이슈를 해결하는 데 도움이 필요한 경우 [Azure EA Portal 액세스 문제 해결](ea-portal-troubleshoot.md)을 참조하세요.
- EA 구독 소유권에 대한 질문은 [Cost Management + Billing FAQ](../cost-management-billing-faq.yml)를 참조하세요.