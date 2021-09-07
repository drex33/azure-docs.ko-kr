---
title: AS2 MDN 승인
description: Azure Logic Apps의 AS2 메시지에 대한 MDN(메시지 처리 알림) 승인에 대해 알아봅니다.
services: logic-apps
ms.suite: integration
author: S-Jindal
ms.author: shivamjindal
ms.reviewer: estfan, azla
ms.topic: reference
ms.date: 08/12/2021
ms.openlocfilehash: 510be5ca1343f115bcdf13c2d67b598b4d410564
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567460"
---
# <a name="mdn-acknowledgments-for-as2-messages-in-azure-logic-apps"></a>Azure Logic Apps의 AS2 메시지에 대한 MDN 승인

Azure Logic Apps에서는 **AS2** 작업을 사용하는 경우 EDI(전자 데이터 교환) 통신용 AS2 메시지를 처리하는 워크플로를 만들 수 있습니다. EDI 메시징에서 승인은 EDI 교환 처리의 상태를 제공합니다. 교환을 받을 때 [**AS2 디코드** 작업](logic-apps-enterprise-integration-as2.md#decode)은 MDN(메시지 처리 알림) 또는 승인을 발신자에게 반환할 수 있습니다. MDN은 다음 항목을 확인합니다.

* 받는 파트너가 원래 메시지를 성공적으로 받았습니다.

  보내는 파트너가 원래 보낸 메시지의 `MessageID`를 수신자가 MDN에 포함하는 `original-message-id` 필드와 비교합니다.

* 받는 파트너가 교환된 데이터의 무결성을 확인했습니다.

  원래 보낸 메시지의 페이로드에서 MIC(메시지 무결성 검사) 또는 MIC 다이제스트가 계산됩니다. 보내는 파트너는 이 MIC를 수신자가 수신된 메시지의 페이로드에서 계산하여 MDN의 `Received-content-MIC` 필드에 포함한(서명된 경우) MIC와 비교합니다.

  > [!NOTE]
  > MDN은 서명될 수 있지만 암호화하거나 압축할 수는 없습니다.

* 수신 부인 방지

  보내는 파트너는 받는 파트너의 퍼블릭 키와 서명된 MDN을 비교하고 MDN의 반환된 MIC 값이 부인 방지 데이터베이스에 저장된 원본 메시지 페이로드의 MIC와 동일한지 확인합니다.

> [!NOTE]
> 응답에 MDN 보내기를 사용하도록 설정한 경우 처리 중에 오류가 발생하더라도 논리 앱에서 MDN을 반환하여 AS2 메시지 처리 상태를 보고하려고 합니다. 발신자가 MDN을 수신하고 확인할 때까지 AS2 전송이 완료되지 않습니다.
> 동기 MDN은 HTTP 응답(예: `200 OK` 상태) 역할을 합니다.

이 항목에서는 승인을 생성하는 데 사용되는 속성, 사용할 MDN 헤더 및 MIC를 비롯하여 AS2 MDN ACK에 대한 간략한 개요를 제공합니다. 기타 관련 정보에 대해서는 다음 설명서를 검토하세요.

* [Azure Logic Apps에서 B2B 엔터프라이즈 통합용 AS2 메시지 교환](logic-apps-enterprise-integration-as2.md)
* [AS2 메시지 설정](logic-apps-enterprise-integration-as2-message-settings.md)
* [Azure Logic Apps란?](logic-apps-overview.md)

## <a name="mdn-generation"></a>MDN 생성

AS2 디코드 작업은 규약의 **수신 설정** 에서 **MDN 보내기** 옵션을 선택한 경우 거래 업체의 AS2 규약 속성을 기준으로 MDN을 생성합니다. 이 경우 메시지 헤더의 **AS2-From** 속성이 MDN을 생성하는 데 사용되지만 다른 속성 및 해당 값은 파트너의 AS2 규약 설정에서 가져옵니다.

기본적으로 들어오는 AS2 메시지 헤더는 유효성 검사를 수행하고 MDN을 생성하는 데 사용됩니다. 규약의 유효성 검사 및 MDN 설정을 대신 사용하려면 규약의 **수신 설정** 에서 **메시지 속성 재정의** 를 선택합니다. 그렇지 않고 이 옵션을 선택하지 않은 상태로 유지하거나 규약을 사용할 수 없는 경우 AS2 디코드 작업은 들어오는 메시지 헤더를 대신 사용합니다.

## <a name="mdn-headers"></a>MDN 헤더

MDN을 응답으로 AS2 메시지에 상호 연결하려면 `AS2-From` 헤더, `AS2-To` 헤더 및 `MessageID` 컨텍스트 속성을 사용합니다. MDN에서 `Original-Message-ID` 헤더는 MDN이 응답인 AS2 메시지의 `Message-ID` 헤더에서 제공됩니다. MDN에는 다음 헤더가 포함됩니다.

| headers | Description |
|---------|-------------|
| HTTP 및 AS2 | 자세한 내용은 [AS2 메시지 설정](logic-apps-enterprise-integration-as2-message-settings.md)을 검토하세요.
| 전송 계층 | 이 헤더에는 서명된 다중 파트 메시지, MIC 알고리즘, 서명 형식 프로토콜 및 가장 바깥쪽 다중 파트 경계 하위 헤더가 있는 `Content-Type` 헤더가 포함됩니다. |
| 첫 번째 파트 | 서명된 다중 파트 메시지의 첫 번째 파트는 포함 MDN입니다. 이 파트는 사람이 읽을 수 있습니다. |
| 두 번째 파트 | 서명된 다중 파트 메시지의 두 번째 파트에는 디지털 서명, 원본 메시지에 대한 참조, 처리 유형 및 상태, MIC 값이 포함되며, 이 파트는 머신이 읽을 수 있습니다. |
|||

## <a name="mic-digest"></a>MIC 다이제스트

MIC 다이제스트 또는 MIC는 MDN이 원래 보낸 메시지의 페이로드와 상호 연결되어 있음을 확인합니다. 이 MIC는 `Received-Content-MIC` 확장 필드에서 서명된 다중 파트 MDN 메시지의 두 번째 파트에 포함됩니다.

MIC는 base64로 인코딩되고 AS2 규약의 **수신 설정** 페이지에서 **MDN 보내기** 및 **서명된 MDN 보내기** 속성을 선택할 때 사용하도록 설정된 **MIC 알고리즘** 속성에서 결정됩니다. MIC 생성의 경우 지원되는 다음 해시 알고리즘 중에서 선택할 수 있습니다.

* SHA1
* MD5
* SHA2-256
* SHA2-384
* SHA2-512

예를 들어, 다음 스크린샷은 AS2 규약의 **수신 설정** 페이지에 있는 MDN 속성을 보여 줍니다.

![MDN 승인 설정을 포함하는 AS2 규약의 "수신 설정"이 있는 Azure Portal을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-as2-mdn-acknowledgment/mdn-ack-settings.png)

## <a name="next-steps"></a>다음 단계

* [AS2 메시지 설정](logic-apps-enterprise-integration-as2-message-settings.md)
