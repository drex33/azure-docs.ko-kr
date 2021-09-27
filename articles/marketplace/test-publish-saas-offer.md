---
title: SaaS 제품을 테스트하고 Microsoft 상용 Marketplace에 게시하는 방법
description: 파트너 센터를 사용하여 미리 보기용 SaaS 제품을 제출하고, 제품을 미리 보고, 테스트한 다음, Microsoft 상용 Marketplace에 게시합니다.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 09/27/2021
ms.openlocfilehash: 7836792245a9618e2ac8168af697c0151f8e123b
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080772"
---
# <a name="how-to-test-and-publish-a-saas-offer-to-the-commercial-marketplace"></a>SaaS 제품을 테스트하고 상용 Marketplace에 게시하는 방법

이 문서에서는 파트너 센터를 사용하여 게시하려는 SaaS 제품을 제출하고, 제품 미리 보고, 테스트한 후 상용 Marketplace에 실시간으로 게시하는 방법을 설명합니다. 게시하려는 제품이 이미 만들어져 있어야 합니다.

> [!NOTE]
> 프로덕션(PROD) 제품을 게시하기 전에 테스트하기 위해 별도의 테스트 및 개발(DEV) 제품을 낮은 위험 방식으로 만드는 것이 좋습니다. (PROD) 제품을 게시하기 전에 다음 단계에 따라 DEV 제품을 만들고 테스트 합니다.

## <a name="submit-your-offer-for-publishing"></a>게시를 위해 제품 제출

[!INCLUDE [Workspaces view note](./includes/preview-interface.md)]

#### <a name="workspaces-view"></a>[작업 영역 보기](#tab/workspaces-view)

1. [파트너 센터](https://go.microsoft.com/fwlink/?linkid=2166002)에 로그인합니다.

1. 홈 페이지에서 **Marketplace 제품** 타일을 선택 합니다.

    [![파트너 센터 홈 페이지의 Marketplace 제품 타일을 보여 줍니다. ](./media/workspaces/partner-center-home.png) ](./media/workspaces/partner-center-home.png#lightbox)

1. Marketplace 제품 페이지에서 게시 하려는 제품을 선택 합니다.
1. 포털의 오른쪽 위 모서리에서 **검토 및 게시** 를 선택합니다.
1. 각 페이지의 **상태** 열에 **완료** 가 표시되는지 확인합니다. 가능한 세 가지 상태는 다음과 같습니다.

   - **시작 안 됨** – 페이지가 불완전합니다.
   - **완료 안 됨** – 페이지에 필수 정보가 누락되었거나 수정해야 할 오류가 있습니다. 페이지로 돌아가서 업데이트해야 합니다.
   - **완료** – 페이지가 완료되었습니다. 모든 필수 데이터가 제공되었고 오류가 없습니다.

1. 페이지의 상태가 **완료** 가 아닌 경우 페이지 이름을 선택하고 문제를 수정한 다음, 페이지를 저장하고 **검토 및 게시** 를 다시 선택하여 이 페이지로 돌아갑니다.
1. 모든 페이지가 완료되면 **인증 참고 사항** 상자에서 인증 팀을 위한 테스트 지침을 제공하여 앱이 제대로 테스트되었는지 확인합니다. 앱을 이해하는 데 유용한 보충 정보를 제공합니다.
1. 제품에 대한 게시 프로세스를 시작하려면 **게시** 를 선택합니다. **제품 개요** 페이지가 표시되고 제품의 **게시 상태** 가 표시됩니다.

제품의 게시 상태는 게시 프로세스를 진행하면서 변경됩니다. 이 프로세스에 대한 자세한 내용은 [유효성 검사 및 게시 단계](review-publish-offer.md#validation-and-publishing-steps)를 참조하세요.

#### <a name="current-view"></a>[현재 보기](#tab/current-view)

1. [파트너 센터](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)에서 상용 Marketplace 대시보드에 로그인합니다.
1. **개요** 페이지에서 게시하려는 제품을 선택합니다.
1. 포털의 오른쪽 위 모서리에서 **검토 및 게시** 를 선택합니다.
1. 각 페이지의 **상태** 열에 **완료** 가 표시되는지 확인합니다. 가능한 세 가지 상태는 다음과 같습니다.

   - **시작 안 됨** – 페이지가 불완전합니다.
   - **완료 안 됨** – 페이지에 필수 정보가 누락되었거나 수정해야 할 오류가 있습니다. 페이지로 돌아가서 업데이트해야 합니다.
   - **완료** – 페이지가 완료되었습니다. 모든 필수 데이터가 제공되었고 오류가 없습니다.

1. 페이지의 상태가 **완료** 가 아닌 경우 페이지 이름을 선택하고 문제를 수정한 다음, 페이지를 저장하고 **검토 및 게시** 를 다시 선택하여 이 페이지로 돌아갑니다.
1. 모든 페이지가 완료되면 **인증 참고 사항** 상자에서 인증 팀을 위한 테스트 지침을 제공하여 앱이 제대로 테스트되었는지 확인합니다. 앱을 이해하는 데 유용한 보충 정보를 제공합니다.
1. 제품에 대한 게시 프로세스를 시작하려면 **게시** 를 선택합니다. **제품 개요** 페이지가 표시되고 제품의 **게시 상태** 가 표시됩니다.

제품의 게시 상태는 게시 프로세스를 진행하면서 변경됩니다. 이 프로세스에 대한 자세한 내용은 [유효성 검사 및 게시 단계](review-publish-offer.md#validation-and-publishing-steps)를 참조하세요.

---

## <a name="preview-and-test-your-offer"></a>제품 미리 보기 및 테스트

제품을 로그오프할 준비가 되면 제품 미리 보기를 검토하고 승인하도록 요청하는 이메일을 보내드립니다. 브라우저에서 **제품 개요** 페이지를 새로 고쳐서 제품이 게시자 승인 단계에 도달했는지 확인할 수도 있습니다. 도달했으면 **Go live** 단추와 미리 보기 링크를 사용할 수 있습니다. 제품을 만들 때 선택한 옵션에 따라 Azure AppSource 미리 보기, Azure Marketplace 미리 보기 또는 둘 다에 대한 링크가 표시됩니다. Microsoft를 통해 제품을 판매하기로 선택한 경우 미리 보기 대상에 추가된 모든 사용자가 제품 구매 및 배포를 테스트하여 이 단계에서 요구 사항을 충족하는지 확인할 수 있습니다.

다음 스크린샷은 SaaS 제품에 대한 **제품 개요** 페이지를 보여 주며 **시작하기** 단추 아래에 두 개의 미리 보기 링크가 있습니다. 이 페이지에 표시되는 유효성 검사 단계는 제품 작성 시 선택한 사항에 따라 다릅니다.

[![파트너 센터의 제품에 대 한 게시 상태를 보여 줍니다. 라이브 이동 단추가 표시 됩니다. 유효성 검사 보고서 보기 링크도 자동 유효성 검사 아래에도 표시 됩니다. ](./media/review-publish-offer/publish-status-saas.png) ](./media/review-publish-offer/publish-status-saas.png#lightbox)

제품을 미리 보려면 다음 단계를 수행합니다.

1. **제품 개요** 페이지에서 **시작하기** 단추 아래의 미리 보기 링크를 선택합니다.

1. 엔드투엔드 구매 및 설정 흐름의 유효성을 검사하려면 제품이 미리 보기 상태일 때 플랜을 구매하세요. 먼저 [지원 티켓](https://aka.ms/marketplacesupport)으로 Microsoft에 알려서 요금이 처리되지 않도록 합니다.

1. SaaS 제품이 [상용 Marketplace 계량 서비스를 사용하는 요금 청구](./partner-center-portal/saas-metered-billing.md)를 지원하는 경우 [Marketplace 요금 청구 API](marketplace-metering-service-apis.md#development-and-testing-best-practices)에 자세히 설명된 테스트 모범 사례를 검토하고 따릅니다.

1. 제품을 라이브로 게시하기 전에 [Microsoft 상용 Marketplace의 SaaS 처리 API 버전 2](./partner-center-portal/pc-saas-fulfillment-api-v2.md#development-and-testing)의 테스트 지침을 검토하고 수행하여 제품이 API와 성공적으로 연결되었는지 확인합니다.

1. 제공 유효성 검사 단계에서 경고가 발생한 경우에는 **제품 개요** 페이지에 **유효성 검사 보고서 보기** 링크가 표시됩니다. **시작하기** 단추를 선택하기 전에 보고서를 검토 하 고 문제를 해결해야 합니다. 그렇지 않으면 인증이 실패하고 제품 릴리스가 지연될 가능성이 높습니다.

1. 제품을 미리 보고 테스트한 후에 변경해야 하는 경우 편집하고 다시 제출하여 새 미리 보기를 게시합니다. 자세한 내용은 [상용 Marketplace에서 기존 제품 업데이트](update-existing-offer.md)를 참조하세요.

## <a name="publish-your-offer-live"></a>실시간으로 제품 게시

미리 보기에서 모든 테스트를 완료한 후 **Go live** 를 선택하여 상용 Marketplace에 제품을 라이브 상태로 게시합니다. 제품이 이미 상용 Marketplace에 게시된 경우에는 **시작하기** 를 선택할 때까지 업데이트한 내용이 라이브 상태로 전환되지 않습니다.

> [!IMPORTANT]
> [개발/테스트 제품](create-saas-dev-test-offer.md)에 대해서는 **시작하기** 를 선택하지 마세요.

이제 상용 Marketplace에서 제품을 사용할 수 있게 제공하기로 선택했으므로 일련의 최종 유효성 검사를 수행하여 라이브 상태의 제품이 미리 보기 버전의 제품처럼 구성되었는지 확인합니다. 이러한 유효성 검사에 대한 자세한 내용은 [게시 단계](review-publish-offer.md#publish-phase)를 참조하세요.

이러한 유효성 검사가 완료되면 제품이 Marketplace에서 라이브 상태가 됩니다.

## <a name="next-steps"></a>다음 단계

- [파트너 센터에서 상용 Marketplace에 대한 분석 보고서에 액세스](analytics.md)
