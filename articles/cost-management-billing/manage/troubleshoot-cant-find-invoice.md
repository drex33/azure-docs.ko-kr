---
title: Azure Portal에서 청구서를 볼 때 문제 해결
description: Azure Portal에서 송장을 보려고 할 때 발생 하는 문제 해결
services: cost-management-billing
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 06/25/2021
ms.author: banders
ms.openlocfilehash: 6f90d077b9cb7e1836170084c683e47cdafc480e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464683"
---
# <a name="troubleshoot-issues-while-trying-to-view-invoice-in-the-azure-portal"></a>Azure Portal에서 송장을 보려고 할 때 발생 하는 문제 해결

Azure Portal에서 송장을 보려고 하면 문제가 발생할 수 있습니다. 이 짧은 가이드에서는 몇 가지 일반적인 문제에 대해 설명합니다.
 
## <a name="common-issues-and-solutions"></a>일반적인 문제 및 솔루션

#### <a name="you-see-the-message-we-cant-display-the-invoices-for-your-subscription-this-typically-happens-when-you-sign-in-with-an-email-which-doesnt-have-access-to-view-invoices-check-youve-signed-in-with-the-correct-email-address-if-you-are-still-seeing-the-error-see-why-you-might-not-see-an-invoice"></a><a name="subnotfound"></a> '구독에 대한 인보이스를 표시할 수 없습니다. 라는 메세지를 볼 수 있을 것 입니다. 이는 일반적으로 인보이스를 볼 수 있는 액세스 권한이 없는 이메일로 로그인할 때 발생합니다. 올바른 이메일 주소를 사용하여 로그인 했는지 확인합니다. 여전히 오류가 표시되면 송장이 표시되지 않는 이유를 참조하세요."

이는 로그인에 사용한 ID에 구독에 대한 액세스 권한이 없는 경우에 발생합니다.

이 문제를 해결하려면 다음 방법 중 하나를 사용하십시오. 

**올바른 이메일 주소로 로그인했는지 확인합니다**

구독에 대한 계정 관리자 역할이 있는 이메일만 청구서를 볼 수 있습니다. 올바른 이메일 주소로 로그인했는지 확인합니다. 이메일 주소는 인보이스가 생성될 때 받은 이메일에 표시됩니다.  

  ![청구서 이메일을 보여 주는 스크린샷](./media/troubleshoot-cant-find-invoice/invoice-email.png)

**올바른 계정으로 로그인했는지 확인합니다**

일부 고객은 동일한 이메일 주소를 가진 두 개의 계정(직장 또는 학교 계정과 개인 계정)을 가지고 있습니다. 일반적으로 계정 중 하나만 청구서를 볼 수 있는 권한이 있습니다. 이메일 주소로 두 개의 계정이 있을 수 있습니다. 권한이 없는 계정으로 로그인하면 청구서가 표시되지 않습니다. 여러 계정이 있고 다른 계정을 사용하는지 확인하려면 다음 단계를 따르세요.

1. InPrivate/Incognito 창에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 동일한 이메일을 사용하는 계정이 여러 개인 경우 **직장 또는 학교 계정** 또는 **개인 계정** 을 선택하라는 메시지가 표시됩니다. 계정 중 하나를 선택한 다음 [청구서를 보려면 여기의 지침](../understand/download-azure-invoice.md#download-your-mosp-azure-subscription-invoice)을 따르십시오.  

    ![계정 선택을 보여 주는 스크린샷](./media/troubleshoot-cant-find-invoice/two-accounts.png)

1. 아직 Azure Portal에서 청구서를 볼 수 없는 경우 다른 계정으로 시도합니다.

**올바른 AAD(Azure Active Directory) 테넌트에 로그인했는지 확인합니다.**

청구 계정은 AAD 테넌트와 연결되어 있습니다. 잘못된 테넌트에 로그인하면 청구 계정에 구독에 대한 청구서가 표시되지 않습니다. Azure Portal에서 테넌트를 전환하려면 다음 단계를 시도하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 페이지 오른쪽 상단에서 이메일 주소를 선택합니다.
1. 디렉터리 전환을 선택합니다.  

    ![스위치 디렉토리 선택을 보여주는 스크린샷](./media/troubleshoot-cant-find-invoice/select-switch-tenant.png)

1. 모든 디렉터리 섹션에서 테넌트를 선택합니다. 모든 디렉터리 섹션이 표시되지 않으면 여러 테넌트에 대한 액세스 권한이 없습니다.  

    ![다른 디렉토리 선택을 보여주는 스크린샷](./media/troubleshoot-cant-find-invoice/select-another-tenant.png)

#### <a name="you-couldnt-find-the-invoice-that-you-see-on-your-credit-card-statement"></a><a name="cantsearchinvoice"></a>신용 카드 명세서에 표시된 인보이스를 찾을 수 없습니다.

신용 카드 **Microsoft Gxxxxxxxxx** 에서 청구 내역을 찾았습니다. 포털에서 다른 모든 청구서를 찾을 수 있지만 Gxxxxxxxxx는 찾을 수 없습니다. 이는 청구서가 다른 구독 또는 청구 프로필에 속하는 경우에 발생합니다. 청구서를 보려면 아래 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Portal 검색 창에서 청구서 번호를 검색합니다.
1. **청구서 보기** 를 선택합니다.  

    ![청구서 검색을 보여주는 스크린샷](./media/troubleshoot-cant-find-invoice/search-invoice.png)

## <a name="contact-us-for-help"></a>도움이 필요한 경우 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- [Azure 청구서 보기 및 다운로드](../understand/download-azure-invoice.md)
- [Azure 사용량 및 요금 보기 및 다운로드](../understand/download-azure-daily-usage.md)
- [Azure Portal의 구독을 찾을 수 없음 로그인 오류](no-subscriptions-found.md)