---
title: Dynamics 365 제품 검토 및 Microsoft AppSource(Azure Marketplace)에 게시
description: Microsoft AppSource(Azure Marketplace)에 Dynamics 365 제품을 검토하고 게시합니다.
ms.reviewer: emuench
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 05/20/2021
ms.openlocfilehash: dd2c33fc1c52c8365b712d986afc86ce35266610
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110703251"
---
# <a name="review-and-publish-a-dynamics-365-offer"></a>Dynamics 365 제품 검토 및 게시

이 문서에서는 파트너 센터를 사용하여 Dynamics 365 제품 초안을 미리 본 후 상업용 Marketplace에 게시하는 방법을 보여 줍니다. 게시 단계를 진행하면서 게시 상태를 확인하는 방법도 포함되어 있습니다.

## <a name="offer-status"></a>제안 상태

제안 상태는 [파트너 센터](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)에 있는 상업용 Marketplace 대시보드의 **개요** 탭에서 검토할 수 있습니다. 각 제안의 **상태** 는 다음 중 하나입니다.

| 상태 | 설명 |
| ------------ | ------------- |
| Draft | 제안이 만들어졌지만 게시되지 않았습니다. |
| 게시 진행 중 | 제안을 게시하기 위한 프로세스가 진행 중입니다. |
| 주의가 필요함 | 인증 또는 다른 게시 단계에서 중요한 문제가 발견되었습니다. |
| 미리 보기 | 제안을 인증했고 이제 게시자의 최종 확인을 기다리고 있습니다. 제안을 라이브 상태로 게시하려면 **라이브 상태로 전환** 을 선택하세요. |
| 라이브 | 제안이 마켓플레이스에서 라이브 상태이며 고객이 제안을 보고 구매할 수 있습니다. |
| 판매 중지 보류 중 | 게시자가 제안 또는 플랜에서 “판매 중지”를 선택했지만 해당 작업이 아직 완료되지 않았습니다. |
| 마켓플레이스에서 사용할 수 없음 | 마켓플레이스에 이전에 게시된 제품이 제거되었습니다. |
|||

## <a name="validation-and-publishing-steps"></a>유효성 검사 및 게시 단계

제품의 게시 상태는 게시 프로세스를 진행하면서 변경됩니다. 이 프로세스에 대한 자세한 내용은 [유효성 검사 및 게시 단계](review-publish-offer.md#validation-and-publishing-steps)를 참조하세요.

게시할 제안을 제출할 준비가 되면 포털의 오른쪽 위 모서리에서 **검토 및 게시** 를 선택합니다. 나열된 제안에 대한 각 페이지의 상태는 다음 중 하나로 표시됩니다.

   - **시작 안 됨** – 페이지가 불완전합니다.
   - **완료 안 됨** – 페이지에 필수 정보가 누락되었거나 수정해야 할 오류가 있습니다. 페이지로 돌아가서 업데이트해야 합니다.
   - **완료** – 페이지가 완료되었습니다. 모든 필수 데이터가 제공되었고 오류가 없습니다.

상태가 **완료** 가 아닌 페이지가 있는 경우 해당 페이지에서 문제를 해결한 후 **검토 및 게시** 페이지로 돌아가서 이제 상태가 **완료** 로 표시되는지 확인해야 합니다. 일부 제안 유형은 테스트가 필요합니다. 이 경우 **인증 참고 사항** 필드가 표시되며 이 필드를 통해 인증 팀에 테스트 지침을 제공하고 앱을 이해하는 데 도움이 되는 추가적인 참고 사항을 제공해야 합니다.

모든 페이지가 완료되고 해당하는 테스트 참고 사항을 입력한 후에는 **게시** 를 선택하여 제안을 제출합니다. 승인하는 데 제안의 미리 보기 버전을 사용할 수 있는 경우 메일이 제공됩니다. 이 경우 다음을 수행합니다.

1. 파트너 센터로 돌아갑니다.
1. 왼쪽 탐색 메뉴 모음에서 **개요** 탭을 선택합니다.
1. 제안을 선택합니다.
1. **검토 및 게시** 를 선택합니다.
1. **라이브 상태로 전환** 을 선택하여 제안을 공개적으로 사용할 수 있도록 합니다.

**검토 및 게시** 를 선택하시면, 제품이 AppSource에 게시되기 전에 인증 및 기타 확인 프로세스를 수행합니다. 제품을 미리 보기에서 사용할 수 있게 되면 알려드립니다. 문제가 있는 경우 세부 정보를 알리고 해결 방법에 대한 참고 자료를 제공합니다.

## <a name="next-steps"></a>다음 단계

- 제품에서 _Microsoft를 통해 타사 앱 라이선스 관리_ 를 사용하도록 설정한 경우, 제품을 판매한 후 파트너 센터에 거래를 등록해야 합니다. 자세한 내용은 [마켓플레이스 제품 라이선스 관리](/partner-center/csp-commercial-marketplace-licensing)를 참조하세요.
- [상업용 Marketplace에서 기존 제품 업데이트](update-existing-offer.md)
