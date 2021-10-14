---
title: Azure 구독을 기업계약
description: 이 문서는 Microsoft 고객 계약 또는 MOSP 구독을 기업계약 전송하는 단계를 이해하는 데 도움이 됩니다.
author: bandersmsft
ms.reviewer: jatracey
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/12/2021
ms.author: banders
ms.custom: ''
ms.openlocfilehash: 4350a36c63e8ea6be4284baeaba3a60d1de9099b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008787"
---
# <a name="transfer-an-azure-subscription-to-an-enterprise-agreement-ea"></a>Azure 구독을 EA(기업계약)로 이전

이 문서는 MICROSOFT 고객 계약 또는 MOSP(종량제) 구독을 EA로 전송하는 데 필요한 단계를 이해하는 데 도움이 됩니다. 전송에는 가동 중지 시간이 없지만 전송을 사용하도록 설정하기 위해 수행해야 하는 여러 단계가 있습니다.

> [!NOTE]
> 전송 프로세스는 구독이 연결된 Azure AD 디렉터리 정보를 변경하지 않습니다. Azure AD 디렉터리를 변경하려면 [Azure 구독을 다른 Azure AD 디렉터리로 이전을 참조하세요.](../../role-based-access-control/transfer-subscription.md)

## <a name="prerequisites"></a>사전 요구 사항

다음 섹션에서는 구독 계정 관리자를 식별하고, 대상 EA에 액세스해야 하며, EA 인증 수준을 설정하는 데 도움이 됩니다.

### <a name="identify-the-subscription-account-admin"></a>구독 계정 관리자 식별

구독에 대한 Azure RBAC 읽기 프로그램 역할 이상을 가진 계정으로 Azure Portal 로그인하여 구독 계정 관리자를 식별합니다. 그런 다음 **구독** > 속성 > 구독을 **선택합니다.** 구독 **계정 관리자가** 표시됩니다. 사용자 정보를 기록해 둡다.

:::image type="content" source="./media/mosp-ea-transfer/subscription-account-admin.png" alt-text="계정 관리자를 볼 수 있는 구독 속성을 보여주는 이미지" lightbox="./media/mosp-ea-transfer/subscription-account-admin.png" :::

> [!NOTE]
> 이전 정보에 설명된 대로 나중에 동일한 계정 관리자 사용자 계정으로 EA(기업계약) 포털에 로그인해야 합니다. 계정에 대한 액세스 권한이 없는 경우 계속하기 전에 액세스해야 합니다. 자세한 내용은 [Azure 구독의 청구 소유권을 다른 계정으로 이전을 참조하세요.](billing-subscription-transfer.md)

### <a name="access-to-the-destination-ea"></a>대상 EA에 대한 액세스

EA 계정 소유자를 만들려면 대상 EA에 대한 액세스 권한이 있어야 합니다. 기존 또는 새 부서가 필요합니다. 부서에서는 구독이 양도될 때 구독이 배치됩니다. EA 계정 소유자는 부서에 배치된 구독을 소유합니다.

EA 계정 소유자를 만들려면 다음 역할 중 하나가 있어야 합니다. EA 역할에 대한 자세한 내용은 [Azure 기업계약 역할 관리를 참조하세요.](understand-ea-roles.md)

- 엔터프라이즈 관리자
- 부서 관리자

### <a name="set-ea-authentication-level"></a>EA 인증 수준 설정

EA에는 등록을 위해 EA 계정 소유자로 추가할 수 있는 사용자 유형을 결정하는 인증 수준 집합이 있습니다. 인증 수준 형식 에 설명된 대로 네 가지 인증 수준을 사용할 수 [있습니다.](ea-portal-troubleshoot.md#authentication-level-types)

EA에 대해 설정된 인증 수준을 통해 이전에 언급한 구독 계정 관리자를 사용하여 새 EA 계정 소유자를 만들 수 있는지 확인합니다. 다음은 그 예입니다. 

- 구독 계정 관리자의 이메일 주소 도메인이 인 경우 `@outlook.com` EA는 인증 수준을 **Microsoft 계정만** 또는 **혼합 계정** 으로 설정해야 합니다.
- 구독 계정 관리자의 이메일 주소 도메인이 인 경우 `@<YourAzureADTenantPrimaryDomain.com>` EA는 인증 수준을 **직장 또는 학교 계정 또는 직장 또는 학교 계정** 교차 **테넌트** 로 설정해야 합니다. 새 EA 계정 소유자를 만드는 기능은 EA의 기본 도메인이 구독 계정 관리자의 이메일 주소 도메인과 같은지 여부에 따라 달라집니다.

> [!NOTE]
> 올바르게 설정하면 인증 수준을 변경해도 전송 프로세스에 영향을 미치지 않습니다. 자세한 내용은 [인증 수준 형식을 참조하세요.](ea-portal-troubleshoot.md#authentication-level-types)

## <a name="transfer-the-subscription-to-the-ea"></a>EA에 구독 이전

필요한 [필수 구성요소](#prerequisites)를 검토하고 완료한 후에는 구독을 EA로 이전하는 프로세스를 시작할 준비가 된 것입니다.

### <a name="create-an-ea-account-owner"></a>EA 계정 소유자 만들기

[필수 조건](#access-to-the-destination-ea) 섹션에 설명된 대로 기존 또는 새 EA 부서의 일부로 EA 계정 소유자를 만들어야 합니다.

1. 에서 EA 포털에 https://ea.azure.com 엔터프라이즈 또는 부서 관리자로 로그인합니다.
1. 새 부서를 만들려면 [Azure Enterprise 부서 만들기를 참조하세요.](ea-portal-administration.md#create-an-azure-enterprise-department)
1. EA 계정 소유자를 만들려면 [계정 추가를 참조하세요.](ea-portal-administration.md#add-an-account) **계정 소유자 전자 메일을** 묻는 메시지가 표시되면 이전에 식별한 구독 계정 **관리자의** 이메일 주소를 입력합니다.
    > [!NOTE]
    > EA 인증 수준이 잘못 설정된 경우 EA 계정 소유자 만들기가 실패하고 EA 포털에 오류가 표시됩니다.

EA 계정 소유자가 만들어지고 나면 구독 계정 관리자(현재 EA 계정 소유자이기도 한 관리자)가 Azure EA Portal에서 이메일을 받습니다. 이제 EA 계정 소유자임을 사용자에게 알리고 있습니다. 사용자가 지정된 계정과 연결된 메일 사서함에 액세스할 수 없는 경우 걱정할 필요가 없습니다. 이메일은 알림일 뿐입니다. 메일의 정보는 계속 진행할 필요가 없습니다. 그러나 메일 사서함은 구독에 대한 향후 알림을 받는 것이 좋습니다.

### <a name="complete-the-subscription-transfer"></a>구독 양도 완료

이제 구독 계정 관리자도 EA 계정 소유자이기 때문에 EA에서 구독을 만들 수 있습니다.

> [!IMPORTANT]
> 계속하기 전에 새 EA 계정 소유자가 처음으로 EA 포털에 가입할 때 발생하는 일을 이해해야 합니다. EA 포털에서 구독 계정 관리자로 로그인하기 전에 다음 정보를 읽고 이해합니다.

새 EA 계정 소유자가 EA 포털에 처음 가입하면 다음과 같은 경고가 표시됩니다.

```
WARNING

You are about to associate your account (email address) to the following enrollment:

Enrollment Name: <EnrollmentName>
Enrollment Number: <EnrollmentNumber>

All Enrollment Administrators can gain access to all of your subscriptions if you proceed.
Additionally, all Azure subscriptions for which you are the account owner will be converted to your Enterprise Agreement.
This includes subscriptions which include a monthly credit (e.g. Visual Studio, MPN, BizSpart, etc.) meaning you will lose the monthly credit by proceeding.
All subscriptions based on a Visual Studio subscriber offer (monthly credit for Visual Studio subscribers or Pay-As-You-Go Dev/Test) will be converted to use the Enterprise Dev/Test usage rates and be billed against this enrollment from today onwards.
If you wish to retain the monthly credits currently associated with any of your subscriptions, please cancel.
Please see additional details.

Cancel  Continue
```

경고는 [Azure EA Portal 관리](ea-portal-administration.md#enterprise-devtest-offer)에 설명된 대로 다음과 같이 표시됩니다.

***사용자가 Azure EA Portal 통해 계정 소유자로 추가되면 MOSP(PAYG) 개발/테스트 제품 또는 Visual Studio 구독자에 대한 월간 크레딧 제안을 기반으로 하는 계정 소유자와 연결된 모든 Azure 구독이 EA 개발/테스트 제안으로 변환됩니다. 계정 소유자와 연결된 MOSP(PAYG)와 같은 다른 제품 유형을 기반으로 하는 구독은 표준 EA 구독 제안으로 변환됩니다.***

사용자가 경고의 결과를 이해하면 **계속을** 선택하면 해당 계정과 연결된 구독이 EA로 전송됩니다.

## <a name="more-help"></a>자세한 도움말

위의 정보를 사용하여 구독을 EA로 이전해야 합니다. 더 많은 도움이 필요한 경우 [청구 지원 요청을](https://go.microsoft.com/fwlink/?linkid=2083458)만듭니다.

## <a name="next-step"></a>다음 단계

- [Azure Enterprise Portal 시작](ea-portal-get-started.md)
