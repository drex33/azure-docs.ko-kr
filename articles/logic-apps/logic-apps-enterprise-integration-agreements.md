---
title: 워크플로의 파트너 간 규약 정의
description: 엔터프라이즈 통합 팩를 사용 하 여 Azure Logic Apps 워크플로에 대 한 통합 계정의 파트너 간에 규약을 추가 합니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 7b634da15248513ee782967eb1c86092e940ec9b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361305"
---
# <a name="add-agreements-between-partners-in-integration-accounts-for-workflows-in-azure-logic-apps"></a>Azure Logic Apps의 워크플로에 대 한 통합 계정에서 파트너 간의 규약 추가

통합 계정에 파트너를 추가한 후 통합 계정에서 [*규약*](logic-apps-enterprise-integration-agreements.md) 을 정의 하 여 파트너가 메시지를 교환 하는 방법을 지정 합니다. 규약은 메시지 교환을 위한 특정 산업 표준 프로토콜을 정의 하 고 다음과 같은 공유 이점을 제공 하 여 서로 원활 하 게 통신할 수 있도록 지원 합니다.

* 조직이 잘 알려진 형식을 사용하여 정보를 교환할 수 있게 합니다.

* B2B (기업 간) 트랜잭션을 수행할 때 효율성을 향상 시킵니다.

* 엔터프라이즈 통합 솔루션을 빌드하기 위해 규약을 만들고, 관리 하 고, 사용 하는 것이 쉽습니다.

규약에는 항상 조직의 *호스트 파트너* 와 메시지를 조직과 교환 하는 조직인 *게스트 파트너가* 필요 합니다. 게스트 파트너는 다른 회사 또는 조직 내 다른 부서일 수 있습니다. 이 규약을 사용 하 여 호스트 파트너의 관점에서 인바운드 및 아웃 바운드 메시지를 처리 하는 방법을 지정 합니다.

이 문서에서는 규약을 만들고 관리 하는 방법을 보여 줍니다 .이 규약은 AS2, X12, EDIFACT 또는 RosettaNet 작업을 사용 하 여 B2B 메시지를 다른 파트너와 교환 하는 데 사용할 수 있습니다.

논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](logic-apps-overview.md)을 검토하세요. b2b 엔터프라이즈 통합에 대 한 자세한 내용은 [Azure Logic Apps 및 엔터프라이즈 통합 팩를 사용 하 여 b2b 엔터프라이즈 통합 워크플로](logic-apps-enterprise-integration-overview.md)를 검토 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 아직 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 엔터프라이즈 통합 및 B2B 워크플로에서 사용 하기 위해 거래 업체, 규약, 인증서 등의 아티팩트를 정의 하 고 저장 하는 [통합 계정 리소스](logic-apps-enterprise-integration-create-integration-account.md) 입니다. 이 리소스는 다음 요구 사항을 충족해야 합니다.

  * 논리 앱 리소스와 동일한 Azure 구독과 연결되어야 합니다.

  * 는 논리 앱 리소스와 동일한 위치 또는 Azure 지역에 있습니다.

  * [ **논리 앱 (소비)** 리소스 유형을](logic-apps-overview.md#resource-type-and-host-environment-differences)사용 하는 경우 워크플로에 아티팩트를 사용 하려면 먼저 통합 계정에 [논리 앱 리소스에 대 한 링크가](logic-apps-enterprise-integration-create-integration-account.md#link-account) 필요 합니다.

  * [ **논리 앱 (표준)** 리소스 종류](logic-apps-overview.md#resource-type-and-host-environment-differences)를 사용 하는 경우 통합 계정에 논리 앱 리소스에 대 한 링크가 필요 하지는 않지만, [AS2](logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md)및 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 작업을 사용 하는 것과 함께 파트너, 규약 및 인증서와 같은 다른 아티팩트를 저장 하는 데에도 필요 합니다. 통합 계정은 동일한 Azure 구독을 사용 하 고 논리 앱 리소스와 동일한 위치에 있는 다른 요구 사항을 충족 해야 합니다.

  > [!NOTE]
  > 현재 **논리 앱 (소비)** 리소스 유형만 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업을 지원 합니다. **논리 앱 (표준)** 리소스 유형은 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업을 포함 하지 않습니다.

* 통합 계정에 두 개 이상의 [거래 파트너가](logic-apps-enterprise-integration-partners.md) 있습니다. 규약에는 호스트 파트너와 게스트 파트너가 필요 합니다. 또한 규약은 두 파트너가 모두 AS2, X12, EDIFACT 또는 RosettaNet 규약에 적절 한 동일한 또는 호환 *비즈니스 id* 한정자를 사용 하도록 요구 합니다.

* 필요에 따라 규약을 사용 하 여 메시지를 교환 하려는 논리 앱 리소스 및 워크플로입니다. 워크플로에는 논리 앱의 워크플로를 시작 하는 트리거가 필요 합니다.

논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)를 검토하세요.

## <a name="add-an-agreement"></a>규약 추가

1. [Azure Portal](https://portal.azure.com) 검색 상자에를 입력 하 `integration accounts` 고 **통합 계정** 을 선택 합니다.

1. **통합 계정** 에서 파트너를 추가 하려는 통합 계정을 선택 합니다.

1. 통합 계정 메뉴의 **설정** 에서 **규약** 을 선택합니다.

1. **규약** 창에서 **추가** 를 선택 합니다.

1. **추가** 창에서 규약에 대 한 다음 정보를 제공 합니다.

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **이름** | 예 | <*agreement-name*> | 규약의 이름 |
   | **규약 유형** | 예 | **AS2**, **X12**, **EDIFACT** 또는 **RosettaNet** | 규약에 대한 프로토콜 형식입니다. 규약 파일을 만들 때 해당 파일의 콘텐츠가 규약 형식과 일치해야 합니다. |
   | **호스트 파트너** | 예 | <*host-partner-name*> | 호스트 파트너가 조직을 나타냅니다. |
   | **호스트 ID** | 예 | <*host-partner-identifier*> | 호스트 파트너의 식별자입니다. |
   | **게스트 파트너** | 예 | <*guest-partner-name*> | 게스트 파트너는 조직과 통신 하는 조직을 나타냅니다. |
   | **게스트 ID** | 예 | <*guest-partner-identifier*> | 게스트 파트너의 식별자입니다. |
   | **수신 설정** | 상황에 따라 다름 | 상황에 따라 다름 | 이러한 속성은 호스트 파트너가 규약의 게스트 파트너 로부터 인바운드 메시지를 수신 하는 방법을 지정 합니다. 자세한 내용은 해당 계약 유형을 참조 하세요. <p>- [AS2 메시지 설정](logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 메시지 설정](logic-apps-enterprise-integration-edifact.md) <br>- [X12 메시지 설정](logic-apps-enterprise-integration-x12.md) |
   | **송신 설정** | 상황에 따라 다름 | 상황에 따라 다름 | 이러한 속성은 호스트 파트너가 규약의 게스트 파트너에 게 아웃 바운드 메시지를 보내는 방법을 지정 합니다. 자세한 내용은 해당 계약 유형을 참조 하세요. <p>- [AS2 메시지 설정](logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT 메시지 설정](logic-apps-enterprise-integration-edifact.md) <br>- [X12 메시지 설정](logic-apps-enterprise-integration-x12.md) |
   | **RosettaNet PIP 참조** | 상황에 따라 다름 | 상황에 따라 다름 | 이 창에서는 RosettaNet 메시지를 사용할 하나 이상의 PIP (Partner Interface process)에 대 한 정보를 지정 합니다. 자세한 내용은 [Exchange RosettaNet 메시지](logic-apps-enterprise-integration-rosettanet.md)를 검토 하세요. |
   |||||

   > [!IMPORTANT]
   > 규약에 대 한 해결 방법은 파트너 및 인바운드 메시지에 정의 된 다음 항목과 일치 하는지에 따라 달라 집니다.
   >
   > * 보낸 사람의 한정자 및 식별자입니다.
   > * 수신자의 한정자 및 식별자입니다.
   >
   > 해당 값이 파트너에 따라 변경되면 반드시 규약도 업데이트합니다.

1. 완료되면 **확인** 을 선택합니다.

   이제 **규약이 계약 목록에** 표시 됩니다.

## <a name="edit-an-agreement"></a>규약 편집

1. [Azure Portal](https://portal.azure.com) 검색 상자에를 입력 하 `integration accounts` 고 **통합 계정** 을 선택 합니다.

1. **통합 계정** 에서 파트너를 추가 하려는 통합 계정을 선택 합니다.

1. 통합 계정 메뉴의 **설정** 에서 **규약** 을 선택합니다.

1. **규약 창에서** 규약을 선택 하 고 **편집** 을 선택 하 고 변경 합니다.

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
