---
title: 워크플로에 대한 거래 업체 정의
description: Enterprise 통합 팩을 사용하여 Azure Logic Apps 워크플로에 대한 거래 파트너를 통합 계정에 추가합니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: f0e0fed5bf7e3354cffa8d6799c4d3e5d39595d9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128549838"
---
# <a name="add-trading-partners-to-integration-accounts-for-workflows-in-azure-logic-apps"></a>Azure Logic Apps 워크플로의 통합 계정에 거래 업체 추가

B2B(기업 간) 엔터프라이즈 통합 워크플로에서 조직 및 다른 사람을 나타내려면 [통합 계정에서](logic-apps-enterprise-integration-create-integration-account.md) *거래 업체를* 만들어 비즈니스 관계의 각 참가자를 나타냅니다. 파트너는 B2B 트랜잭션에 참여하고 서로 메시지를 교환하는 비즈니스 엔터티입니다.

> [!IMPORTANT]
> 이러한 파트너를 정의하기 전에 파트너와 대화하여 서로 보내는 메시지를 식별하고 유효성을 검사합니다. 계약에 참여하고 서로 메시지를 교환하려면 통합 계정의 파트너가 동일하거나 호환되는 *비즈니스 한정자를* 사용해야 합니다. 이러한 세부 정보에 동의하면 통합 계정에 파트너를 만들 준비가 된 것입니다.

이 문서에서는 파트너를 만들고 관리하는 방법을 보여 주며, 나중에 파트너 간에 메시지를 교환하기 위한 특정 산업 표준 프로토콜을 정의하는 규약을 만드는 데 사용할 수 있습니다.

논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](logic-apps-overview.md)을 검토하세요. B2B 엔터프라이즈 통합에 대한 자세한 내용은 [Azure Logic Apps B2B 엔터프라이즈 통합 워크플로를 검토하세요.](logic-apps-enterprise-integration-overview.md)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 엔터프라이즈 통합 및 B2B 워크플로에서 사용하기 위해 거래 업체, 계약, 인증서 등과 같은 아티팩트 정의 및 저장을 위한 [통합 계정 리소스입니다.](logic-apps-enterprise-integration-create-integration-account.md) 이 리소스는 다음 요구 사항을 충족해야 합니다.

  * 논리 앱 리소스와 동일한 Azure 구독과 연결되어야 합니다.

  * 논리 앱 리소스와 동일한 위치 또는 Azure 지역에 존재합니다.

  * [ **논리 앱(소비)** 리소스 종류를](logic-apps-overview.md#resource-type-and-host-environment-differences)사용하는 경우 워크플로에서 아티팩트를 사용하려면 통합 계정에 논리 앱 리소스에 대한 [링크가](logic-apps-enterprise-integration-create-integration-account.md#link-account) 필요합니다.

  * [ **논리 앱(표준)** 리소스 종류를](logic-apps-overview.md#resource-type-and-host-environment-differences)사용하는 경우 통합 계정에 논리 앱 리소스에 대한 링크가 필요하지 않지만 [AS2,](logic-apps-enterprise-integration-as2.md) [X12](logic-apps-enterprise-integration-x12.md)및 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 작업과 함께 파트너, 계약 및 인증서와 같은 다른 아티팩트를 저장해야 합니다. 통합 계정은 여전히 논리 앱 리소스와 동일한 위치에 있는 동일한 Azure 구독 및 기존 구독을 사용하는 것과 같은 다른 요구 사항을 충족해야 합니다.

  > [!NOTE]
  > 현재 **논리 앱(소비)** 리소스 유형만 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업을 지원합니다. **논리 앱(표준)** 리소스 종류에는 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업이 포함되지 않습니다.

<a name="add-partner"></a>

## <a name="add-a-partner"></a>파트너 추가

1. [Azure Portal](https://portal.azure.com) 검색 상자에 `integration accounts` 를 입력하고 통합 **계정 을 선택합니다.**

1. **통합 계정에서** 파트너를 추가하려는 통합 계정을 선택합니다.

1. 통합 계정 메뉴의 **설정** 아래에서 **파트너를** 선택합니다.

1. **파트너** 창에서 **추가를** 선택합니다.

1. 파트너 추가 창에서 **파트너에** 대한 다음 정보를 제공합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **이름** | 예 | 파트너 이름 |
   | **한정자** | 예 | 조직에 고유한 비즈니스 ID를 제공하는 인증 기관(예: **D-U-N-S(Dun &amp; Bradstreet))** 입니다. <p>파트너는 상호 정의된 비즈니스 ID를 선택할 수 있습니다. 이런 시나리오에서는 **상호 정의됨**(EDIFACT의 경우) 또는 **상호 정의됨(X12)** (X12의 경우)를 선택합니다. <p>RosettaNet의 경우에는 표준인 **DUNS** 만 선택합니다. <p>**중요:** 통합 계정의 파트너가 계약에 참여하고 서로 메시지를 교환하려면 동일하거나 호환되는 한정자를 사용해야 합니다. |
   | **값** | 예 | 논리 앱에서 수신하는 문서를 식별하는 값입니다. <p>RosettaNet을 사용하는 파트너의 경우 이 값은 관련이 있는 9자리 숫자여야 합니다. 먼저 파트너를 만든 다음 나중에 [정의를 편집하여](#edit-partner)분류 및 연락처 정보와 같은 RosettaNet 파트너에 대한 자세한 정보를 제공할 수 있습니다. |
   ||||

1. 완료되면 **확인** 을 선택합니다.

   이제 파트너가 **파트너** 목록에 표시됩니다.

<a name="edit-partner"></a>

## <a name="edit-a-partner"></a>파트너 편집

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 여세요.

1. 통합 계정 메뉴의 **설정** 아래에서 **파트너를** 선택합니다.

1. **파트너** 창에서 파트너를 선택하고, **편집을** 선택하고, 변경합니다.

   RosettaNet을 사용하는 파트너의 경우 **RosettaNet 파트너 속성** 에서 다음 표에 설명된 대로 자세한 정보를 제공할 수 있습니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **파트너 분류** | 예 | 파트너의 조직 유형 |
   | **공급망 코드** | 예 | 파트너의 공급망 코드(예: ‘정보 기술’ 또는 ‘전자 구성 요소’) |
   | **담당자 이름** | 예 | 파트너의 담당자 이름 |
   | **Email** | 예 | 파트너의 이메일 주소 |
   | **Fax** | 예 | 파트너의 팩스 번호 |
   | **Telephone** | 예 | 파트너의 전화 번호 |
   ||||

1. 완료되면 **확인** 을 선택합니다.

<a name="delete-partner"></a>

## <a name="delete-a-partner"></a>파트너 삭제

1. [Azure Portal](https://portal.azure.com)에서 통합 계정을 여세요.

1. 통합 계정 메뉴의 **설정** 아래에서 **파트너를** 선택합니다.

1. **파트너** 창에서 삭제할 파트너를 선택한 다음, **삭제를** 선택합니다.

1. 파트너를 삭제할 것인지 확인하려면 **예를** 선택합니다.

## <a name="next-steps"></a>다음 단계

* [파트너 간 계약 추가](logic-apps-enterprise-integration-agreements.md)