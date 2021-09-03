---
title: Azure EA Portal 액세스 문제 해결
description: 이 문서에서는 Azure EA Portal에서 Azure 기업계약(EA)에 발생할 수 있는 몇 가지 일반적인 문제에 대해 설명합니다.
author: bandersmsft
ms.author: banders
ms.date: 07/26/2021
ms.topic: troubleshooting
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: e18c4707a0de97c696aa4ca93ce4c3ca818eb929
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707534"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Azure EA Portal 액세스 문제 해결

이 문서에서는 Azure 기업계약(EA)에 발생할 수 있는 몇 가지 일반적인 문제에 대해 설명합니다. Azure EA Portal은 기업계약 사용자 및 비용을 관리하는 데 사용됩니다. Azure EA Portal 액세스를 구성하거나 업데이트하는 경우 이러한 문제가 발생할 수 있습니다.

## <a name="issues-adding-a-user-to-an-enrollment"></a>등록에 사용자 추가 문제

기업 등록에 대한 다양한 유형의 인증 수준이 있습니다. 인증 수준이 잘못 적용되면 Azure EA Portal에 로그인하려고 할 때 문제가 발생할 수 있습니다.

Azure EA Portal을 사용하여 인증 수준이 서로 다른 사용자에게 액세스 권한을 부여할 수 있습니다. 엔터프라이즈 관리자는 조직의 보안 요구 사항에 맞게 인증 수준을 업데이트할 수 있습니다.

### <a name="authentication-level-types"></a>인증 수준 유형

- Microsoft 계정만 - Microsoft 계정을 통해 사용하고, 사용자를 만들고 관리하려는 조직에 적합합니다.
- 회사 또는 학교 계정 - 클라우드에 대한 페더레이션을 사용하여 Active Directory를 설정하고 모든 계정이 단일 테넌트에 있는 조직에 적합합니다.
- 회사 또는 학교 계정 교차 테넌트 - 클라우드에 대한 페더레이션을 사용하여 Active Directory를 설정하고 여러 테넌트에 있는 여러 계정을 갖는 조직에 적합합니다.
- 혼합 계정 - Microsoft 계정과 회사 또는 학교 계정을 사용하여 사용자를 추가할 수 있습니다.

등록에 추가된 첫 번째 회사 또는 학교 계정은 _기본_ 도메인을 결정합니다. 다른 테넌트를 사용하여 회사 또는 학교 계정을 추가하려면 등록에서 인증 수준을 교차 테넌트로 변경해야 합니다.

인증 수준을 업데이트하려면

1. Azure EA Portal에 엔터프라이즈 관리자로 로그인합니다.
2. 왼쪽 탐색 패널에서 **관리** 를 클릭합니다.
3. **등록** 탭을 클릭합니다.
4. **등록 세부 정보** 에서 **인증 수준** 을 선택합니다.
5. 연필 기호를 클릭합니다.
6. **저장** 을 클릭합니다.

![인증 수준을 보여 주는 예 ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Microsoft 계정에는 [https://signup.live.com](https://signup.live.com/)에서 만든 연결된 ID가 있어야 합니다.

회사 또는 학교 계정은 페더레이션을 사용하여 Active Directory를 설정하고 모든 계정이 단일 테넌트에 있는 조직에서 사용할 수 있습니다. 회사의 내부 Active Directory가 페더레이션된 경우 회사 또는 학교 페더레이션 사용자 인증을 사용하여 사용자를 추가할 수 있습니다.

조직에서 Active Directory 페더레이션을 사용하지 않는 경우에는 회사 또는 학교 이메일 주소를 사용할 수 없습니다. 대신, 새 이메일 주소를 등록하거나 만들고 Microsoft 계정으로 등록하세요.

## <a name="unable-to-access-the-azure-ea-portal"></a>Azure EA Portal에 액세스할 수 없음

Azure EA Portal에 로그인하려고 시도할 때 오류 메시지가 표시되면 다음 문제 해결 단계를 사용합니다.

- 올바른 Azure EA Portal URL(https://ea.azure.com )을 사용하고 있는지 확인합니다.
- Azure EA Portal에 대한 액세스 권한이 회사 또는 학교 계정 또는 Microsoft 계정으로 추가되었는지 확인합니다.
  - 회사 계정을 사용하는 경우 업무용 이메일 및 암호를 입력합니다. 업무용 암호는 조직에서 제공됩니다. 문제가 있는 경우 IT 부서에서 암호를 다시 설정하는 방법에 대한 정보를 확인할 수 있습니다.
  - Microsoft 계정을 사용하는 경우 Microsoft 계정 이메일 주소와 암호를 입력합니다. Microsoft 계정 암호를 잊은 경우에는 [https://account.live.com/password/reset](https://account.live.com/password/reset)에서 다시 설정할 수 있습니다.
- 이전 또는 기존 세션에서 쿠키 또는 캐시된 정보가 유지되지 않도록 프라이빗 또는 incognito 브라우저 세션을 사용하여 로그인합니다. 브라우저의 캐시를 지우고 프라이빗 또는 incognito 창을 사용하여 https://ea.azure.com 을 엽니다.
- Microsoft 계정을 사용할 때 _잘못된 사용자_ 오류가 발생하는 경우 Microsoft 계정이 여러 개 있기 때문일 수 있습니다. 로그인하기 위해 사용하려고 하는 계정이 기본 이메일 주소가 아닙니다.
또는 사용자를 등록에 추가할 때 잘못된 계정 유형을 사용했기 때문에 _잘못된 사용자_ 오류가 발생할 수 있습니다. Microsoft 계정 대신 회사 또는 학교 계정을 사용하는 경우를 예로 들 수 있습니다. 이 예에서는 다른 EA 관리자가 올바른 계정을 추가하거나 [지원](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c) 담당자에게 문의해야 합니다.
  - 기본 별칭을 확인해야 하는 경우 [https://account.live.com](https://account.live.com)으로 이동합니다. 그런 다음, **사용자 정보**, **Microsoft에 로그인하는 방법 관리** 를 차례로 클릭합니다. 프롬프트에 따라 암호 확인용 메일을 확인하고 중요한 정보에 액세스하기 위한 코드를 얻습니다. 보안 코드를 입력합니다. 2단계 인증을 설정하지 않으려면 **나중에 설정** 을 선택합니다.
  - 계정 별칭을 볼 수 있는 **Microsoft에 로그인하는 방법 관리** 페이지가 표시됩니다. Azure EA Portal에 로그인하는 데 기본 별칭을 사용하고 있는지 확인합니다. 그렇지 않으면 기본 별칭으로 만들 수 있습니다. 또는 Azure EA Portal에 대한 기본 별칭을 대신 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure EA Portal 관리자는 [Azure EA Portal 관리](ea-portal-administration.md)를 읽고 일반적인 관리 작업에 대해 알아보아야 합니다.
- Azure EA 활성화의 일반적인 문제에 대한 질문과 답변은 [Cost Management + Billing FAQ](../cost-management-billing-faq.yml)를 참조하세요.
