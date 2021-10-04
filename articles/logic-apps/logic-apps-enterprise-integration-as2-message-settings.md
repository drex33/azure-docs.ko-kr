---
title: AS2 메시지 설정
description: 엔터프라이즈 통합 팩 Azure Logic Apps에 대 한 계약의 AS2 메시지 설정에 대 한 참조 가이드입니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: reference
ms.date: 07/20/2021
ms.openlocfilehash: 2bec9c598c0f08e9e114fcf5a83bfb0d238dd2b1
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400994"
---
# <a name="reference-for-as2-message-settings-in-agreements-for-azure-logic-apps"></a>Azure Logic Apps에 대 한 규약의 AS2 메시지 설정에 대 한 참조

이 참조는 [거래 업체](logic-apps-enterprise-integration-partners.md)간에 메시지를 처리 하는 방법을 지정 하기 위해 AS2 규약에서 설정할 수 있는 속성을 설명 합니다. 사용자와 메시지를 교환하는 파트너와의 규약에 따라 이러한 속성을 설정합니다.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>AS2 수신 설정

![“설정 받기”를 선택합니다.](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| 속성 | 필수 | Description |
|----------|----------|-------------|
| **메시지 속성 재정의** | 예 | 들어오는 메시지의 속성을 속성 설정으로 재정의합니다. |
| **메시지 서명 필요** | 예 | 들어오는 모든 메시지를 디지털 서명해야 하는지 여부를 지정합니다. 서명을 요청하는 경우 **인증서** 목록에서 기존 게스트 파트너 퍼블릭 인증서를 선택하여 메시지에 대한 서명의 유효성을 검사합니다. 인증서가 없는 경우 [인증서 추가](../logic-apps/logic-apps-enterprise-integration-certificates.md)에 대해 자세히 알아보세요. |
| **메시지 암호화 필요** | 예 | 들어오는 모든 메시지를 암호화해야 하는지 여부를 지정합니다. 암호화되지 않은 메시지는 거부됩니다. 암호화를 요청하는 경우 **인증서** 목록에서 기존 호스트 파트너 프라이빗 인증서를 선택하여 들어오는 메시지의 암호를 해독합니다. 인증서가 없는 경우 [인증서 추가](../logic-apps/logic-apps-enterprise-integration-certificates.md)에 대해 자세히 알아보세요. |
| **메시지 압축 필요** | 예 | 들어오는 모든 메시지를 압축해야 하는지 여부를 지정합니다. 압축되지 않은 메시지는 거부됩니다. |
| **메시지 ID 중복 허용 안 함** | 예 | 중복 ID를 사용한 메시지를 허용할지 여부를 지정합니다. 중복 ID를 허용하지 않는 경우 검사 사이의 일 수를 선택합니다. 중복을 일시 중지할지 여부를 선택할 수도 있습니다. |
| **MDN 텍스트** | 예 | 메시지 보낸 사람에게 보내려는 기본 MDN(메시지 처리 알림)을 지정합니다. |
| **MDN 전송** | 예 | 받은 메시지에 대한 동기 MDN을 보낼지 여부를 지정합니다.  |
| **서명된 MDN 전송** | 예 | 받은 메시지에 대해 서명된 MDN을 보낼지 여부를 지정합니다. 서명이 필요한 경우 **MIC 알고리즘** 목록에서 메시지 서명에 사용할 알고리즘을 선택합니다. |
| **비동기 MDN 전송** | 예 | MDN을 비동기적으로 보낼지 여부를 지정합니다. 비동기 MDN을 선택하는 경우 **URL** 상자에서 MDN을 보낼 위치에 대한 URL을 지정합니다. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>AS2 송신 설정

![“설정 보내기”를 선택합니다.](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| 속성 | 필수 | Description |
|----------|----------|-------------|
| **메시지 서명 사용** | 예 | 보내는 모든 메시지에 디지털 서명을 해야 하는지 여부를 지정합니다. 서명이 필요한 경우 다음 값을 선택합니다. <p>- **서명 알고리즘** 목록에서 메시지에 서명하는 데 사용할 알고리즘을 선택합니다. <br>- **인증서** 목록에서 기존 호스트 파트너 프라이빗 인증서를 선택합니다. 인증서가 없는 경우 [인증서 추가](../logic-apps/logic-apps-enterprise-integration-certificates.md)에 대해 자세히 알아보세요. |
| **메시지 암호화 사용** | 예 | 보내는 모든 메시지를 암호화해야 하는지 여부를 지정합니다. 암호화가 필요한 경우 다음 값을 선택합니다. <p>- **암호화 알고리즘** 목록에서 메시지를 암호화하는 데 사용할 게스트 파트너 퍼블릭 인증서 알고리즘을 선택합니다. <br>- **인증서** 목록에서 보내는 메시지를 암호화하는 데 사용할 기존 게스트 파트너 퍼블릭 인증서를 선택합니다. 인증서가 없는 경우 [인증서 추가](../logic-apps/logic-apps-enterprise-integration-certificates.md)에 대해 자세히 알아보세요. |
| **메시지 압축 사용** | 예 | 보내는 모든 메시지를 압축해야 하는지 여부를 지정합니다. |
| **HTTP 헤더 펼침** | 예 | HTTP `content-type` 헤더를 한 줄에 배치합니다. |
| **MIME 헤더로 파일 이름 전송** | 예 | MIME 헤더에 파일 이름을 포함할지 여부를 지정합니다. |
| **MDN 요청** | 예 | 보내는 모든 메시지에 대해 MDN(메시지 처리 알림)을 받을지 여부를 지정합니다. |
| **서명된 MDN 요청** | 예 | 보내는 모든 메시지에 대해 서명된 MDN을 받을지 여부를 지정합니다. 서명이 필요한 경우 **MIC 알고리즘** 목록에서 메시지 서명에 사용할 알고리즘을 선택합니다. |
| **비동기 MDN 요청** | 예 | 비동기식으로 MDN을 받을지 여부를 지정합니다. 비동기 MDN을 선택하는 경우 **URL** 상자에서 MDN을 보낼 위치에 대한 URL을 지정합니다. |
| **NRR 사용** | 예 | NRR(수신 거부 없음)이 필요한지 여부를 지정합니다. 이 통신 특성은 데이터가 주소대로 수신되었다는 증거를 제공합니다. |
| **SHA2 알고리즘 형식** | 예 | 보내는 AS2 메시지 또는 MDN에 대한 헤더에 서명하는 데 사용할 MIC 알고리즘 형식을 지정합니다. |
||||

## <a name="next-steps"></a>다음 단계

[AS2 메시지 교환](../logic-apps/logic-apps-enterprise-integration-as2.md)
