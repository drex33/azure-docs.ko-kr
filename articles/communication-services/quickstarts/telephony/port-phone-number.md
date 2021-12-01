---
title: 빠른 시작 - Azure Communication Services로 전화 번호 포트
description: 전화 번호를 Communication Services 리소스로 포트하는 방법에 대해 알아봅니다.
author: probableprime
manager: mikben
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: pstn
ms.custom: references_regions, mode-other
ms.openlocfilehash: ff92d128a6dc270d443b6fa4686d2b39a5e203cd
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133385406"
---
# <a name="quickstart-port-a-phone-number"></a>빠른 시작: 전화 번호 포트

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

전화 번호를 Azure Communication Services 리소스로 포트하여 Azure Communication Services를 시작하세요. 수신자 부담 및 미국에서 제공하는 지역 번호가 포트에 적합합니다. 전화 번호 형식에 대한 자세한 내용은 [전화 번호 개념 설명서](../../concepts/telephony/plan-solution.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [활성 Communication Services 리소스](../create-communication-resource.md)

## <a name="gather-your-azure-resource-details"></a>Azure 리소스 세부 정보 모으기

포트 요청을 시작하기 전에 Azure Portal로 이동하여 Communication Services 리소스를 선택합니다. `Overview` 창이 선택된 상태에서 오른쪽 위 모퉁이에 있는 `JSON View` 링크를 클릭합니다.

:::image type="content" source="./media/number-port/json-view.png" alt-text="JSON 보기 선택을 보여 주는 스크린샷":::

리소스의 **Azure ID** 및 **변경 불가능 리소스 ID** 를 기록합니다.

:::image type="content" source="./media/number-port/json-properties.png" alt-text="JSON 속성 선택을 보여 주는 스크린샷":::

## <a name="initiate-the-port-request"></a>포트 요청 시작

수신자 부담 및 미국에서 제공하는 지역 번호가 포트에 적합합니다. 다음 형식 중 하나를 사용하여 포트 요청을 제출합니다.

- 수신자 부담 전화 번호의 경우: [수신자 부담 번호 포트 요청](https://aka.ms/acs-port-form-tollfree)
- 미국에서 제공하는 지역 번호: [지역 번호 포트 요청](https://aka.ms/acs-port-form-geographic)

완료되면 완료된 포트 요청 양식을 acsporting@microsoft.com으로 보냅니다. 이메일 제목 줄이 “ACS Port-In Request”로 시작해야 합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 수행하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * Communication Services 리소스 메타데이터 획득
> * 전화 번호 포트를 위한 요청 제출

> [!div class="nextstepaction"]
> [SMS 보내기](../sms/send.md)
> [전화 시작](../voice-video-calling/getting-started-with-calling.md)
