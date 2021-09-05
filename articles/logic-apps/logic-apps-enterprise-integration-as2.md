---
title: B2B용 AS2 메시지 송신 및 수신
description: 엔터프라이즈 통합 팩이 포함된 Azure Logic Apps를 사용하여 B2B 엔터프라이즈 통합용 AS2 메시지 교환
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 699bc3b15c47e1dc80dbb8a4defd27adad298ba0
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122539573"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>엔터프라이즈 통합 팩이 포함된 Azure Logic Apps에서 B2B 엔터프라이즈 통합용 AS2 메시지 교환

> [!IMPORTANT]
> 원래 AS2 커넥터는 사용되지 않으므로 추적 기능이 필요하지 않는 한 **AS2(v2)** 커넥터를 대신 사용합니다. 추적을 제외하고 v2 커넥터는 원래 버전과 동일한 기능 및 더 나은 성능을 제공하고 Azure Logic Apps 런타임의 기본 기능을 제공하며, 메시지 크기, 처리량 및 대기 시간이 크게 향상되었습니다. 또한, v2 커넥터는 통합 계정에 대한 연결을 만들 필요가 없습니다. 대신, 사전 요구 사항에 설명된 대로 커넥터를 사용하려는 논리 앱에 통합 계정을 연결해야 합니다.

Azure Logic Apps에서 AS2 메시지를 작업하려면 AS2(버전 1.2) 통신을 지원하고 관리하는 트리거 및 작업을 제공하는 AS2 커넥터를 사용하면 됩니다. 예를 들어, 메시지를 전송할 때 보안 및 안정성을 설정하려면 다음 작업을 사용할 수 있습니다.

* [**AS2 인코딩** 작업](#encode) - MDN(메시지 처리 알림)을 통해 암호화, 디지털 서명 및 승인을 제공하는 데 사용됩니다. 이를 통해 부인 방지를 지원할 수 있습니다. 예를 들어, 이 작업은 AS2/HTTP 헤더를 적용하고 구성될 때 이러한 작업을 수행합니다.

  * 발신 메시지에 서명합니다.
  * 발신 메시지를 암호화합니다.
  * 메시지를 압축합니다.
  * MIME 헤더의 파일 이름을 전송합니다.

* [**AS2 디코딩** 작업](#decode) - MDN(메시지 처리 알림)을 통해 암호 해독, 디지털 서명 및 승인을 제공하는 데 사용됩니다. 예를 들어, 이 작업은 다음 작업을 수행합니다.

  * AS2/HTTP 헤더를 처리합니다.
  * 원본 아웃바운드 메시지와 함께 수신된 MDN을 조정합니다.
  * 부인 방지 데이터베이스에서 레코드를 업데이트 및 연결합니다.
  * AS2 상태 보고에 대한 레코드를 작성합니다.
  * 페이로드 콘텐츠를 base64 인코딩으로 출력합니다.
  * MDN이 필요한지 여부를 결정합니다. AS2 규약을 기반으로 MDN이 동기인지 비동기인지 결정합니다.
  * AS2 규약에 따라 동기 또는 비동기 MDN을 생성합니다.
  * MDN에 대한 상관 관계 토큰과 속성을 설정합니다.

  이 작업은 구성될 때 다음 작업도 수행합니다.

  * 서명을 확인합니다.
  * 메시지 암호를 해독합니다.
  * 메시지를 압축 해제합니다.
  * 메시지 ID 중복을 확인하고 허용하지 않습니다.

이 문서에서는 기존 논리 앱에 AS2 인코딩 및 디코딩 작업을 추가하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 아직 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* AS2 커넥터를 사용하려는 논리 앱과 논리 앱의 워크플로를 시작하는 트리거. AS2 커넥터는 작업만 제공하고 트리거를 제공하지 않습니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

* Azure 구독과 연결되고 AS2 커넥터를 사용하려는 논리 앱에 연결된 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). 논리 앱과 통합 계정이 동일한 위치 또는 Azure 지역에 있어야 합니다.

* 통합 계정에서 AS2 ID 한정자를 사용하여 정의한 두 군데 이상의 [거래 파트너](../logic-apps/logic-apps-enterprise-integration-partners.md).

* AS2 커넥터를 사용하려면 거래 파트너 간에 AS2 [규약](../logic-apps/logic-apps-enterprise-integration-agreements.md)을 만들고 통합 계정에 해당 규약을 저장해야 합니다.

* 인증서 관리에 [Azure Key Vault](../key-vault/general/overview.md)를 사용하는 경우 자격 증명 모음 키에서 **암호화** 및 **암호 해독** 작업을 허용하는지 확인합니다. 그렇지 않으면 인코딩 및 디코딩 작업이 실패합니다.

  Azure Portal에서 키 자격 증명 모음의 키로 이동하고, 키의 **허용된 작업** 을 검토하고, **암호화** 및 **암호 해독** 작업이 선택되었는지 확인합니다. 예를 들어 다음과 같습니다.

  ![자격 증명 모음 키 작업 확인](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>AS2 메시지 인코딩

1. 아직 열지 않았다면 [Azure Portal](https://portal.azure.com)의 Logic App Designer에서 논리 앱을 엽니다.

1. Designer에서 논리 앱에 새 작업을 추가합니다.

1. **작업 선택** 및 검색 상자 아래에서 **모두** 를 선택합니다. 검색 상자에 "as2 인코딩"을 입력하고 AS2(v2) 작업(**As2 인코딩**)을 선택했는지 확인합니다.

   !["AS2 인코딩"을 선택합니다.](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. 이제 이러한 속성에 대한 정보를 제공합니다.

   | 속성 | 설명 |
   |----------|-------------|
   | **인코딩할 메시지** | 메시지 페이로드 |
   | **AS2 보낸 사람** | AS2 규약에서 지정한 메시지 보낸 사람의 식별자 |
   | **AS2 받는 사람** | AS2 규약에서 지정한 메시지 받는 사람의 식별자 |
   |||

   예를 들면 다음과 같습니다.

   ![메시지 인코딩 속성](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> 서명되거나 암호화된 메시지를 보낼 때 문제가 발생하면 다른 SHA256 알고리즘 형식을 사용해 보세요. AS2 사양은 SHA256 형식에 대한 정보를 제공하지 않으므로 각 공급자는 고유한 구현 또는 형식을 사용합니다.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>AS2 메시지 디코딩

1. 아직 열지 않았다면 [Azure Portal](https://portal.azure.com)의 Logic App Designer에서 논리 앱을 엽니다.

1. Designer에서 논리 앱에 새 작업을 추가합니다.

1. **작업 선택** 및 검색 상자 아래에서 **모두** 를 선택합니다. 검색 상자에 "as2 디코딩"을 입력하고 AS2(v2) 작업(**As2 디코딩**)을 선택했는지 확인합니다.

   !["AS2 디코딩"을 선택합니다.](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. **인코딩할 메시지** 및 **메시지 헤더** 속성에 대해 이전 트리거 또는 작업 출력에서 이러한 값을 선택합니다.

   예를 들어, 논리 앱에서 요청 트리거를 통해 메시지를 수신한다고 가정합니다. 해당 트리거에서 출력을 선택할 수 있습니다.

   ![요청 출력에서 본문 및 헤더를 선택합니다.](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>샘플

완벽하게 작동하는 논리 앱 및 샘플 AS2 시나리오를 배포하려면 [AS2 논리 앱 템플릿 및 시나리오](https://azure.microsoft.com/resources/templates/logic-app-as2-send-receive/)를 참조하세요.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에 설명된 작업 및 제한을 비롯하여 이 커넥터에 대한 추가 기술 정보는 [커넥터의 참조 페이지](/connectors/as2/)에서 확인할 수 있습니다. 

> [!NOTE]
> 이 커넥터의 ISE 레이블이 지정된 버전은 [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)의 논리 앱에는 [ISE에 대한 B2B 메시지 제한](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits)을 사용합니다.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
