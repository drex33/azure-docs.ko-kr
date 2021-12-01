---
title: 짧은 코드 프로그램 Brief 채우기 지침
titleSuffix: An Azure Communication Services concept document
description: 짧은 코드에 적용 하는 방법을 알아봅니다.
author: prakulka
manager: shahen
services: azure-communication-services
ms.author: prakulka
ms.date: 11/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: sms
ms.openlocfilehash: e33e9c12c0bccc7b493d906a2d47ac89314b3d77
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386907"
---
# <a name="short-code-program-brief-filling-guidelines"></a>짧은 코드 프로그램 Brief 채우기 지침
[!INCLUDE [Short code eligibility notice](../../includes/public-preview-include.md)]

[!INCLUDE [Short code eligibility notice](../../includes/public-preview-include-short-code-eligibility.md)]

Azure 통신 서비스를 사용 하면 SMS 프로그램에 대 한 간단한 코드를 적용할 수 있습니다. 이 문서에서는 간단한 코드 등록을 위해 프로그램을 간단 하 게 작성 하는 방법에 대 한 지침을 검토 합니다. 프로그램 brief 응용 프로그램은 다음과 같은 4 개의 섹션으로 구성 됩니다.
- 프로그램 세부 정보
- 연락처 세부 정보
- 볼륨
- 템플릿

## <a name="program-details"></a>프로그램 세부 정보
### <a name="program-name"></a>프로그램 이름
모든 짧은 코드 프로그램은 메시지, 동작에 대 한 호출 및 조건에 따라 프로그램 이름, 제품 설명 또는 둘 다를 노출 해야 합니다. 프로그램 이름은 일반적으로 짧은 코드 프로그램의 스폰서입니다. 일반적으로 짧은 코드와 관련 된 브랜드 이름 또는 회사 이름입니다. 

프로그램 이름이 두 부분으로 구성 되는 것이 가장 좋습니다. 
- 스폰서 이름 (회사 또는 브랜드 이름) 예: Contoso
- 제품 설명 예: 프로 모션 경고, SMS 뱅킹, 약속 미리 알림

**프로그램 이름 예:**
-   Contoso 프로 모션 경고
-   Contoso SMS 뱅킹
-   Contoso Appt. 미리 알림

### <a name="type-of-short-code"></a>짧은 코드의 형식
통신 서비스는 두 가지 유형의 짧은 코드 (random 및 베 니 티)를 제공 합니다.

#### <a name="random-short-code"></a>임의 짧은 코드
임의의 짧은 코드는 미국 일반적인 CSCA (Short code Association)에서 무작위로 선택 하 고 할당 하는 5-6 자리 전화 번호입니다.
#### <a name="vanity-short-code"></a>베 니 티 short 코드
베 니 티 short 코드는 프로그램에 대해 사용자가 선택 하는 5-6 자리 전화 번호입니다. [미국 짧은 코드 디렉터리](https://usshortcodedirectory.com/)에서 사용 가능한 짧은 코드 목록을 조회할 수 있습니다.
또한 고객이 전화 접속 패드에 동일한 영숫자로 입력할 수 있는 숫자를 선택할 수 있습니다. 예를 들어 Contoso는 제품 (633377)을 사용할 수 있습니다. 

베 니 티 short 코드를 선택 하는 경우 프로그램에 사용 하려는 베 니 티 short 코드의 우선 순위가 지정 된 목록을 입력 해야 합니다. 목록의 첫 번째 짧은 코드를 임대할 수 없는 경우 목록의 대안이 사용 됩니다. 예: 234567, 234578, 234589.

> [!Note]
> 미국의 짧은 코드는 1로 시작할 수 없습니다.

### <a name="directionality"></a>방향 
이 필드는 SMS 프로그램의 방향 (단방향 또는 양방향 SMS)을 캡처합니다. 미국에서 양방향 SMS는 고객의 옵트아웃 요청을 수용 하도록 요구 됩니다.

### <a name="recurrence"></a>되풀이
프로그램은 트랜잭션 프로그램 또는 구독 프로그램으로 분류 됩니다. 
- **트랜잭션** 프로그램은 고객의 옵트인 요청에 대 한 응답으로 일회성 메시지를 제공 합니다. 예를 들면 일회성 암호, 정보 알림 및 배달 알림이 포함 됩니다. 
- **구독** 프로그램은 계속 해 서 고객에 게 메시지를 보냅니다. 예를 들면 내용/정보 경고 구독 (news, 날씨, horoscopes 등), 마케팅 및 충성도 승격이 있습니다.

### <a name="estimated-ramp-up-time"></a>예상 램프 시간
이 필드는 전체 프로덕션 볼륨에 도달할 때까지 기간 (일)을 캡처합니다.

### <a name="political-campaign"></a>정치 캠페인
정치적 기부를 요청 하는 짧은 코드 프로그램에는 [추가 모범 사례가](https://www.ctia.org/the-wireless-industry/industry-commitments/guidelines-for-federal-political-campaign-contributions-via-wireless-carriers-bill)적용 됩니다. 

### <a name="privacy-policy-and-terms-and-conditions-url"></a>개인 정보 취급 방침 및 조건 URL
메시지 발신자는 모든 짧은 코드 프로그램에 대 한 개인 정보 취급 방침 및 조건을 유지 하 고 초기 조치를 통해 고객에 게 액세스할 수 있도록 하는 데 필요 합니다. 

이 필드에는 고객이 액세스할 수 있는 개인 정보 취급 방침의 URL과 사용 약관을 제공할 수 있습니다. 개인 정보 취급 방침 URL이 아직 없는 경우 정책에 표시 되는 내용의 스크린샷 URL을 제공할 수 있습니다 (캠페인이 시작 된 후에도 사용할 수 있는 웹 사이트의 디자인 mockup).

> [!Note]
> 웹 사이트, 모형을 또는 디자인의 URL이 없으면에 스크린샷을 보내세요 phone@microsoft.com .

### <a name="program-sign-up-type-and-url"></a>프로그램 등록 유형 및 URL 
이 필드는 고객이 문자 메시지를 받고 동의 프로그램의 특성을 이해할 수 있도록 고객에 게 조치를 취할 수 있는 명령인 CTA (작업 호출)를 캡처합니다. 작업에 대 한 호출은 SMS, IVR (대화형 음성 응답), 웹 사이트 또는 판매 지점을 초과할 수 있습니다. 매개체를 사용 하려면 작업 호출에 대 한 모든 짧은 코드 프로그램 brief 응용 프로그램이 mock ups를 사용 하 여 제출 되어야 합니다.

이러한 필드에서 고객이 프로그램을 검색 하는 웹 사이트의 URL, 웹 사이트의 스크린샷 URL, 웹 사이트의 mockup URL 또는 디자인을 사용 하는 URL을 제공 해야 합니다.

> [!Note]
> 웹 사이트, 모형을 또는 디자인의 URL이 없으면에 스크린샷을 보내세요 phone@microsoft.com .

#### <a name="guidelines-for-designing-the-call-to-action-cta"></a>CTA (작업 호출) 디자인에 대 한 지침:
1. CTA는 고객이 참여 하거나 동의한 프로그램에 대해 명확 해야 합니다.
   - 호출 작업은 명확 하 고 정확 해야 합니다. 동의는 거짓 수단을 통해 획득 해서는 안 됩니다.
   - 모든 프로그램이 동일한 짧은 코드에서 작동 하는 경우에도 단일 옵트인에 따라 여러 프로그램에서 사용자를 등록 하는 것은 금지 됩니다. 모범 사례는 [CTIA monitoring 안내서](https://www.wmcglobal.com/hubfs/CTIA%20Short%20Code%20Monitoring%20Handbook%20-%20v1.8.pdf) 를 참조 하세요.
2. CTA는 다음과 같은 축약 된 사용 약관을 포함 해야 합니다.
   -    프로그램 이름 – 위에 설명 된 대로
   -    메시지 빈도 (되풀이 메시지/구독)
   -    메시지 및 데이터 요금이 적용 될 수 있습니다.
   -    포괄적인 사용 약관 (정적 웹 사이트 또는 전체 텍스트)에 연결
   -    개인 정보 취급 방침에 대 한 링크 (또는 전체 텍스트)

> [!Note]
> 수상 또는 기타 특수 한 프로그램에 대 한 추가 정보가 필요할 수 있습니다. [CTIA monitoring 안내서](https://www.wmcglobal.com/hubfs/CTIA%20Short%20Code%20Monitoring%20Handbook%20-%20v1.8.pdf)를 확인 하세요.

##### <a name="examples"></a>예:
**SMS**

Contoso.com: 명절 판매를 발표 합니다. 다음 Contoso 구매에서 5%를 저장 하려면 예를 회신 합니다. Txt OFF to stop, 사용 약관에 대 한 도움말입니다.

**웹 옵트인**

:::image type="content" source= "../media/short-code-web-optin.png" alt-text="웹 옵트인 (opt in) mock를 보여 주는 스크린샷":::

**판매 지점 (하드 카피 leaflet)**

:::image type="content" source= "../media/print-opt-in-mock.png" alt-text="인쇄 옵트인 (opt in) mock를 보여 주는 스크린샷":::

**IVR**

최근 분의 여행 거래에 등록 하려면 1을 누릅니다.  개인 정보 보호 및 사용 약관에 대 한 margiestravel.com 방문에 메시지 및 데이터 요금이 적용 될 수 있습니다.

## <a name="contact-details"></a>연락처 세부 정보

### <a name="point-of-contact-email-address"></a>연락처 전자 메일 주소 지점
회사 및 연락처에 대 한 정보를 제공 해야 합니다. 짧은 코드 응용 프로그램의 상태 업데이트가 연락 지점 전자 메일 주소로 전송 됩니다.

### <a name="customer-care"></a>고객 관리
고객 과실 연락처 정보는 명확 하 게 사용할 수 있어야 하며, 고객이 프로그램 정보 뿐만 아니라 프로그램의 상태를 이해 하는 데 도움을 받을 수 있어야 합니다. 고객 과실 정보는 고객이 도움을 받을 수 있어야 합니다.

이러한 필드에는 고객이 도움을 받을 수 있도록 고객 관리 전자 메일 주소와 고객 담당 전화 번호를 제공 해야 합니다.

## <a name="volume"></a>볼륨 
### <a name="messages-sent-per-user"></a>사용자 당 보낸 메시지 수
이 필드에는 월별 고객 당 전송 될 대략적인 메시지 수를 제공 해야 합니다.

### <a name="replies-per-user"></a>사용자 당 회신 
이 필드에는 고객 당 받아야 하는 대략적인 회신 수를 제공 해야 합니다.

### <a name="expected-total-messages-sent"></a>예상 총 전송 메시지
이 필드에는 월간 총 전송 메시지 수를 제공 해야 합니다.

### <a name="traffic-spikes"></a>트래픽 급증
이 필드에는 트래픽 급증 및 예상 타이밍에 대 한 정보를 제공 해야 합니다.
예: 크리스마스와 같은 명절의 배달 알림 프로그램에 대 한 트래픽 급증이 예상 됩니다.

## <a name="templates"></a>템플릿
### <a name="opt-in-confirmation-message"></a>옵트인 확인 메시지
CTIA를 사용 하려면 고객이 모바일 장치에서 짧은 코드로 키워드를 전송 하 여 웹 사이트, IVR 등에 대 한 동의를 제공 함으로써 짧은 코드 프로그램을 적극적으로 옵트인 (opt in) 해야 합니다.

이 필드에는 동의를 받을 때 고객에 게 전송 되는 확인 메시지의 샘플을 제공 해야 합니다. 

**예:** Contoso 프로 모션 경고: 3 개 메시지/주 메시지&데이터 요금이 적용 될 수 있습니다. 도움말에 대 한 회신 도움말입니다. 옵트아웃 (opt out) 하려면 회신 중지를 선택 합니다.

### <a name="help-message-response"></a>도움말 메시지 응답
메시지 발신자는 프로그램 이름과 함께 도움말 키워드를 포함 하는 메시지에 응답 하 고 메시지 보낸 사람에 게 연락 하는 방법에 대 한 추가 정보를 요청 해야 합니다.

이 필드에는 도움말 키워드를 받을 때 고객에 게 전송 되는 응답 메시지의 샘플을 제공 해야 합니다. 

**예:** Texting Contoso! 지원에 대해 1-800-800-8000를 호출 합니다.

### <a name="opt-out-message"></a>옵트아웃 메시지
메시지 발신자는 프로그램에서 고객을 옵트아웃 (opt out) 하 고 프로그램 이름으로 STOP 키워드를 포함 하는 메시지에 응답 하 고 추가 메시지가 전송 되지 않도록 하는 메커니즘이 필요 합니다. 

이 필드에는 STOP 키워드를 받을 때 고객에 게 보내는 응답 메시지의 샘플을 제공 해야 합니다. 

**예:** Contoso 경고: 옵트아웃 (opt out) 하 고 더 이상 메시지를 받지 않습니다.

Azure Communication Services에서 옵트아웃을 처리 하는 방법에 대 한 자세한 내용은 [옵트아웃 (opt out) 가이드](./sms-faq.md#how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers) 를 참조 하세요.

### <a name="example-messages"></a>예제 메시지
메시지 발신자는 짧은 코드를 통해 전송 되는 샘플을 사용 하 여 메시지의 모든 형식/범주를 공개 해야 합니다.

이 필드에는에 대 한 간단한 코드를 사용 하 여 사용 하려는 각 콘텐츠 형식에 대 한 샘플 메시지를 제공 해야 합니다. 

#### <a name="example-flow"></a>예 흐름:
- **Contoso**: contoso: 2022 년 2 월 30 일에 대 한 예약이 확인 되었습니다. 다시 예약할 Txt R. Txt 도움말 또는 중지 메시지&데이터 요금이 적용 될 수 있습니다.
  - **사용자**: R
- **Contoso**: 다시 예약 하려는 경우 날짜 (MMDDYY)와 회신 합니다.
  - **사용자**: 030322
- **Contoso**: 030322에서 일정을 다시 예약 하려는 경우 시간 (HHMM)으로 회신 합니다.
  - **사용자**: 1200
- **Contoso**: 2022 년 3 월 3 일 오후 12:00 시에 예약이 확인 되었습니다. 다시 예약할 Txt R. Txt 도움말 또는 중지 메시지&데이터 요금이 적용 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [짧은 코드에 대해 적용](../../quickstarts/sms/apply-for-short-code.md)

다음 문서는 사용자에게 유용할 수 있습니다.

- [SMS SDK](../sms/sdk-features.md) 숙지