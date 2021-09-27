---
title: 워크플로에서 파트너 간의 계약 정의
description: Enterprise 통합 팩을 사용하여 Azure Logic Apps 워크플로에 대한 계약을 통합 계정에 추가합니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 3a1b714be1f6eb70a4780c7abf58f13a45eb3f3f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584166"
---
# <a name="add-agreements-between-partners-to-integration-accounts-for-workflows-in-azure-logic-apps"></a>Azure Logic Apps 워크플로에 대한 통합 계정에 파트너 간 계약 추가

통합 계정에 파트너를 추가한 후 통합 계정에서 계약을 정의하여 파트너가 메시지를 교환하는 방법을 [*지정합니다.*](logic-apps-enterprise-integration-agreements.md) 규약은 메시지를 교환하기 위한 특정 업계 표준 프로토콜을 정의하고 다음과 같은 공유 혜택을 제공하여 조직이 서로 원활하게 통신할 수 있도록 지원합니다.

* 조직이 잘 알려진 형식을 사용하여 정보를 교환할 수 있게 합니다.

* B2B(Business-to-Business) 트랜잭션을 수행할 때 효율성을 개선합니다.

* 기업 통합 솔루션을 빌드하기 위해 계약을 쉽게 만들고, 관리하고, 사용할 수 있도록 합니다.

계약에는 항상 조직인 *호스트 파트너* 와 조직과 메시지를 교환하는 조직인 *게스트 파트너가* 필요합니다. 게스트 파트너는 다른 회사 또는 조직 내 다른 부서일 수 있습니다. 이 계약을 사용하여 호스트 파트너의 관점에서 인바운드 및 아웃바운드 메시지를 처리하는 방법을 지정합니다.

이 문서에서는 AS2, X12, EDIFACT 또는 RosettaNet 작업을 사용하여 B2B 메시지를 다른 파트너와 교환하는 데 사용할 수 있는 계약을 만들고 관리하는 방법을 보여 주며,

논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](logic-apps-overview.md)을 검토하세요. B2B 엔터프라이즈 통합에 대한 자세한 내용은 [Azure Logic Apps 및 Enterprise 통합 팩을 통해 B2B 엔터프라이즈 통합 워크플로를](logic-apps-enterprise-integration-overview.md)검토하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 엔터프라이즈 통합 및 B2B 워크플로에서 사용하기 위해 거래 업체, 계약, 인증서 등과 같은 아티팩트 정의 및 저장을 위한 [통합 계정 리소스입니다.](logic-apps-enterprise-integration-create-integration-account.md) 이 리소스는 다음 요구 사항을 충족해야 합니다.

  * 논리 앱 리소스와 동일한 Azure 구독과 연결되어야 합니다.

  * 논리 앱 리소스와 동일한 위치 또는 Azure 지역에 존재합니다.

  * [ **논리 앱(소비)** 리소스 종류를](logic-apps-overview.md#resource-type-and-host-environment-differences)사용하는 경우 워크플로에서 아티팩트를 사용하려면 통합 계정에 논리 앱 리소스에 대한 [링크가](logic-apps-enterprise-integration-create-integration-account.md#link-account) 필요합니다.

  * [ **논리 앱(표준)** 리소스 종류를](logic-apps-overview.md#resource-type-and-host-environment-differences)사용하는 경우 통합 계정에 논리 앱 리소스에 대한 링크가 필요하지 않지만 [AS2,](logic-apps-enterprise-integration-as2.md) [X12](logic-apps-enterprise-integration-x12.md)및 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 작업과 함께 파트너, 계약 및 인증서와 같은 다른 아티팩트를 저장해야 합니다. 통합 계정은 여전히 논리 앱 리소스와 동일한 위치에 있는 동일한 Azure 구독 및 기존 구독을 사용하는 것과 같은 다른 요구 사항을 충족해야 합니다.

  > [!NOTE]
  > 현재 **논리 앱(소비)** 리소스 유형만 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업을 지원합니다. **논리 앱(표준)** 리소스 종류에는 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업이 포함되지 않습니다.

* 통합 계정에 두 개 이상의 [거래 업체가](logic-apps-enterprise-integration-partners.md) 있습니다. 계약에는 호스트 파트너 및 게스트 파트너가 필요합니다. 또한 계약에 따라 두 파트너 모두 AS2, X12, EDIFACT 또는 RosettaNet 계약에 적합한 동일하거나 호환되는 *비즈니스 ID* 한정자를 사용해야 합니다.

* 필요에 따라 계약을 사용하여 메시지를 교환하려는 논리 앱 리소스 및 워크플로입니다. 워크플로에는 논리 앱의 워크플로를 시작하는 트리거가 필요합니다.

논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)를 검토하세요.

## <a name="add-an-agreement"></a>계약 추가

1. [Azure Portal](https://portal.azure.com) 검색 상자에 `integration accounts` 를 입력하고 통합 **계정 을 선택합니다.**

1. **통합 계정에서** 파트너를 추가하려는 통합 계정을 선택합니다.

1. 통합 계정 메뉴의 **설정** 에서 **규약** 을 선택합니다.

1. **계약** 창에서 **추가를** 선택합니다.

1. **추가** 창에서 계약에 대한 다음 정보를 제공합니다.

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **이름** | 예 | <*agreement-name*> | 규약의 이름 |
   | **규약 유형** | 예 | **AS2,** **X12,** **EDIFACT** 또는 **RosettaNet** | 규약에 대한 프로토콜 형식입니다. 규약 파일을 만들 때 해당 파일의 콘텐츠가 규약 형식과 일치해야 합니다. |
   | **호스트 파트너** | 예 | <*host-partner-name*> | 호스트 파트너가 조직을 나타냅니다. |
   | **호스트 ID** | 예 | <*host-partner-identifier*> | 호스트 파트너의 식별자입니다. |
   | **게스트 파트너** | 예 | <*guest-partner-name*> | 게스트 파트너는 조직과 통신하는 조직을 나타냅니다. |
   | **게스트 ID** | 예 | <*guest-partner-identifier*> | 게스트 파트너의 식별자입니다. |
   | **수신 설정** | 상황에 따라 다름 | 상황에 따라 다름 | 이러한 속성은 호스트 파트너가 계약의 게스트 파트너로부터 인바운드 메시지를 받는 방법을 지정합니다. 자세한 내용은 각 계약 유형을 검토하세요. <p>- [AS2 메시지 설정](logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 메시지 설정](logic-apps-enterprise-integration-edifact.md) <br>- [X12 메시지 설정](logic-apps-enterprise-integration-x12.md) |
   | **송신 설정** | 상황에 따라 다름 | 상황에 따라 다름 | 이러한 속성은 호스트 파트너가 계약에서 게스트 파트너에게 아웃바운드 메시지를 보내는 방법을 지정합니다. 자세한 내용은 각 계약 유형을 검토하세요. <p>- [AS2 메시지 설정](logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 메시지 설정](logic-apps-enterprise-integration-edifact.md) <br>- [X12 메시지 설정](logic-apps-enterprise-integration-x12.md) |
   | **RosettaNet PIP 참조** | 상황에 따라 다름 | 상황에 따라 다름 | 이 창에서는 RosettaNet 메시지를 사용하기 위해 하나 이상의 PIP(파트너 인터페이스 프로세스)에 대한 정보를 지정합니다. 자세한 내용은 [Exchange RosettaNet 메시지를 검토하세요.](logic-apps-enterprise-integration-rosettanet.md) |
   |||||

   > [!IMPORTANT]
   > 계약에 대한 해결은 파트너 및 인바운드 메시지에 정의된 다음 항목의 일치에 따라 달라집니다.
   >
   > * 보낸 사람의 한정자 및 식별자입니다.
   > * 수신자의 한정자 및 식별자입니다.
   >
   > 해당 값이 파트너에 따라 변경되면 반드시 규약도 업데이트합니다.

1. 완료되면 **확인** 을 선택합니다.

   이제 계약 목록에 **계약이** 표시됩니다.

## <a name="edit-an-agreement"></a>계약 편집

1. [Azure Portal](https://portal.azure.com) 검색 상자에 `integration accounts` 를 입력하고 통합 **계정 을 선택합니다.**

1. **통합 계정에서** 파트너를 추가하려는 통합 계정을 선택합니다.

1. 통합 계정 메뉴의 **설정** 에서 **규약** 을 선택합니다.

1. **계약** 창에서 계약을 **선택하고, 편집을** 선택하고, 변경합니다.

1. 완료되면 **확인** 을 선택합니다.

## <a name="delete-an-agreement"></a>규약 삭제

1. [Azure Portal](https://portal.azure.com) 검색 상자에를 입력 하 `integration accounts` 고 **통합 계정** 을 선택 합니다.

1. **통합 계정** 에서 파트너를 추가 하려는 통합 계정을 선택 합니다.

1. 통합 계정 메뉴의 **설정** 에서 **규약** 을 선택합니다.

1. **규약** 창에서 삭제할 규약을 선택 하 고 **삭제** 를 선택 합니다.

1. 규약을 삭제할 것인지 확인 하려면 **예** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계

* [AS2 메시지 교환](logic-apps-enterprise-integration-as2.md)
* [EDIFACT 메시지 교환](logic-apps-enterprise-integration-edifact.md)
* [X12 메시지 교환](logic-apps-enterprise-integration-x12.md)
* [Exchange RosettaNet 메시지](logic-apps-enterprise-integration-rosettanet.md)
