---
title: Azure 직접 라우팅 프로비저닝 및 구성 - Azure Communication Services
description: Session Border Controller를 추가하고 Azure Communication Services 직접 라우팅을 위한 음성 라우팅을 구성하는 방법을 알아봅니다.
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: pstn
ms.openlocfilehash: b6fa8523a347f9191c607ce3ba50b32f2d088886
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636024"
---
# <a name="session-border-controllers-and-voice-routing"></a>Session Border Controller 및 음성 라우팅
Azure Communication Services 직접 라우팅을 사용하면 기존 전화 통신 인프라를 Azure에 연결할 수 있습니다. 이 문서에는 지원되는 SBC(Session Border Controller)를 직접 라우팅에 연결하는 데 필요한 상위 수준 단계와 사용하도록 설정된 통신 리소스에 음성 라우팅을 사용하는 방법이 나와 있습니다. 

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]
 
Azure Communication Services 직접 라우팅이 조직에 적합한 솔루션인지 여부에 관한 내용은 [Azure 전화 통신 개념](./telephony-concept.md)을 참조하세요. 필수 조건과 배포 계획에 관한 내용은 [Communication Services 직접 라우팅 인프라 요구 사항](./direct-routing-infrastructure.md)을 참조하세요.

## <a name="connect-the-sbc-with-azure-communication-services"></a>SBC를 Azure Communication Services와 연결

### <a name="configure-using-azure-portal"></a>Azure Portal을 사용하여 구성 
1. 왼쪽 탐색의 음성 통화 - PSTN에서 직접 라우팅을 선택한 다음, Session Border Controller 탭에서 구성을 선택합니다.
1. SBC의 정규화된 도메인 이름과 신호 포트를 입력합니다.
 
Office 365를 사용하는 경우 SBC FQDN의 도메인 부분이 도메인에 속한 Office 365 관리 포털에 등록한 도메인과 다른지 확인합니다. 
- 예를 들어, `contoso.com`이 O365에 등록된 도메인인 경우에는 Communication Services에 `sbc.contoso.com`을 사용할 수 없습니다. 그러나 O365에 도메인이 없는 경우 상위 수준 도메인을 사용할 수 있습니다. `acs.contoso.com` 도메인을 만들고 FQDN `sbc.acs.contoso.com`을 SBC 이름으로 사용할 수 있습니다.
- SBC 인증서는 이름과 일치해야 합니다. 와일드카드 인증서가 지원됩니다.
- *.onmicrosoft.com 도메인은 SBC의 FQDN에 사용할 수 없습니다.
요구 사항의 전체 목록은 [Azure 직접 라우팅 인프라 요구 사항](./direct-routing-infrastructure.md)을 참조하세요.

   :::image type="content" source="../media/direct-routing-provisioning/add-session-border-controller.png" alt-text="Session Border Controller 추가":::
- 작업이 완료되면 다음을 클릭합니다.
모든 것이 올바르게 설정된 경우 Microsoft와 Session Border Controller 간에 OPTIONS 메시지 교환이 표시되어야 합니다. SBC 모니터링/로그를 사용하여 연결의 유효성을 검사합니다.

## <a name="voice-routing-considerations"></a>음성 라우팅 고려 사항

Azure Communication Services 직접 라우팅에는 전화를 건 번호 패턴에 따라 특정 SBC(Session Border Controller)로 통화를 보낼 수 있는 라우팅 메커니즘이 있습니다.
직접 라우팅 구성을 리소스에 추가하면 이 리소스의 인스턴스(ID)에서 만들어진 모든 통화가 먼저 직접 라우팅 트렁크를 시도합니다. 라우팅은 전화를 건 번호와 리소스에 대해 구성된 음성 경로의 일치 항목을 기반으로 합니다. 일치 항목이 있는 경우 통화는 직접 라우팅 트렁크를 통과합니다. 일치 항목이 없는 경우 다음 단계는 callAgent.startCall 메서드의 alternateCallerId 매개 변수를 처리하는 것입니다. 리소스가 음성 통화(PSTN)에 대해 사용되고 Microsoft에서 구매한 하나 이상의 번호를 포함하는 경우와 alternateCallerId가 리소스에 대해 구매한 번호 중 하나와 일치하는 경우 통화는 Microsoft 인프라를 사용하여 음성 통화(PSTN)를 통해 라우팅됩니다. alternateCallerId 매개 변수가 구매한 번호와 일치하지 않으면 통화에 실패합니다. 아래 다이어그램은 Azure Communication Services 음성 라우팅 논리를 보여 줍니다.

:::image type="content" source="../media/direct-routing-provisioning/voice-routing-diagram.png" alt-text="Communication Services 나가는 음성 라우팅":::

## <a name="voice-routing-examples"></a>음성 라우팅 예제
다음 예제에서는 통화 흐름의 음성 라우팅을 표시합니다.

> [!NOTE]
> 모든 예제에서 더 높은 음성 경로는 우선 순위가 더 높지만 경로의 SBC는 임의 순서로 시도됩니다.

### <a name="one-route-example"></a>단일 경로 예제:
패턴 `^\+1(425|206)(\d{7})$`를 사용하여 하나의 음성 경로를 만들고 여기에 `sbc1.contoso.biz` 및 `sbc2.contoso.biz`를 추가한 경우 사용자가 `+1 425 XXX XX XX` 또는 `+1 206 XXX XX XX`에 전화를 걸면 해당 통화는 먼저 SBC `sbc1.contoso.biz` 또는 `sbc2.contoso.biz`로 라우팅됩니다. SBC를 둘 다 사용할 수 없으면 통화가 끊어집니다.

### <a name="two-routes-example"></a>두 개 경로 예제:
예를 들어, 패턴 `^\+1(425|206)(\d{7})$`를 사용하여 하나의 음성 경로를 만들고 여기에 `sbc1.contoso.biz`와 `sbc2.contoso.biz`를 추가한 다음, `sbc3.contoso.biz` 및 `sbc4.contoso.biz`와 함께 동일한 패턴을 사용하여 두 번째 경로를 만들었습니다. 이 경우 사용자가 `+1 425 XXX XX XX` 또는 `+1 206 XXX XX XX`에 전화를 걸면 해당 통화는 먼저 SBC `sbc1.contoso.biz` 또는 `sbc2.contoso.biz`로 라우팅됩니다. sbc1과 sbc2를 둘 다 사용할 수 없는 경우에는 우선 순위가 낮은 경로가 시도됩니다(`sbc3.contoso.biz` 및 `sbc4.contoso.biz`). 두 번째 경로의 SBC를 둘 다 사용할 수 없으면 통화가 끊어집니다.

### <a name="three-routes-example"></a>세 개 경로 예제:
예를 들어, 패턴 `^\+1(425|206)(\d{7})$`를 사용하여 하나의 음성 경로를 만들고 여기에 `sbc1.contoso.biz`와 `sbc2.contoso.biz`를 추가한 다음, `sbc3.contoso.biz` 및 `sbc4.contoso.biz`와 함께 동일한 패턴을 사용하여 두 번째 경로를 만들고, `sbc5.contoso.biz`와 함께 `^+1(\d[10])$`를 사용하여 세 번째 경로를 만들었습니다. 이 경우 사용자가 `+1 425 XXX XX XX` 또는 `+1 206 XXX XX XX`에 전화를 걸면 해당 통화는 먼저 SBC `sbc1.contoso.biz` 또는 `sbc2.contoso.biz`로 라우팅됩니다. sbc1과 sbc2를 둘 다 사용할 수 없는 경우에는 우선 순위가 낮은 경로가 시도됩니다(`sbc3.contoso.biz` 및 `sbc4.contoso.biz`). 두 번째 경로의 SBC를 둘 다 사용할 수 없는 경우에는 세 번째 경로가 시도됩니다. sbc5도 사용할 수 없으면 통화가 끊어집니다. 또한 사용자가 `+1 321 XXX XX XX`에 전화를 걸면 해당 통화는 `sbc5.contoso.biz`로 이동하고, 이 경로를 사용할 수 없으면 통화가 끊어집니다.

> [!NOTE]
> 음성 라우팅에서 다음 SBC로의 장애 조치(failover)는 응답 코드 408, 503 및 504에 대해서만 작동합니다.

> [!NOTE]
> 모든 예제에서 전화를 건 번호가 패턴과 일치하지 않으면 통신 리소스에 대해 구매한 번호가 있고 이 번호가 애플리케이션에서 `alternateCallerId`로 사용되지 않는 한 통화가 끊어집니다. 

## <a name="configure-voice-routing"></a>음성 라우팅 구성 

### <a name="configure-using-azure-portal"></a>Azure Portal을 사용하여 구성

:::image type="content" source="../media/direct-routing-provisioning/voice-routing-configuration.png" alt-text="Communication Services 나가는 음성 라우팅 구성":::

음성 경로에 이름을 지정하고, 정규식을 사용하여 숫자 패턴을 지정한 다음, 해당 패턴에 대해 SBC를 선택합니다. 기본 정규식의 몇 가지 예제는 다음과 같습니다.
- `^\+\d+$` - 전화 번호가 더하기로 시작하는 하나 이상의 숫자와 일치하는지 확인합니다.
- `^+1(\d[10])$` - 전화 번호가 `+1` 뒤의 10개 숫자와 일치하는지 확인합니다.
- `^\+1(425|206)(\d{7})$` - `+1425` 또는 `+1206`로 시작하고 뒤에 7개 숫자가 있는 전화 번호가 일치하는지 확인합니다.
- `^\+0?1234$` - `+01234` 및 `+1234` 전화 번호가 둘 다 일치하는지 확인합니다.

정규식에 관한 자세한 내용은 [.NET 정규식 개요](/dotnet/standard/base-types/regular-expressions)를 참조하세요.

단일 패턴에 대해 여러 SBC를 선택할 수 있습니다. 이 경우 라우팅 알고리즘은 SBC를 임의 순서로 선택합니다. 정확한 번호 패턴을 두 번 이상 지정할 수도 있습니다. 더 높은 행은 우선 순위가 더 높으며, 해당 행과 연결된 모든 SBC를 사용할 수 없는 경우 다음 행이 선택됩니다. 이 방식으로, 복잡한 라우팅 시나리오를 만듭니다.

## <a name="delete-direct-routing-configuration"></a>직접 라우팅 구성 삭제

### <a name="delete-using-azure-portal"></a>Azure Portal을 사용하여 삭제

#### <a name="to-delete-a-voice-route"></a>음성 경로를 삭제하려면:
1. 왼쪽 탐색의 음성 통화 - PSTN에서 직접 라우팅으로 이동한 다음, 음성 경로 탭을 선택합니다.
1. 확인란을 사용하여 삭제하려는 경로를 선택합니다.
1. [제거]를 선택합니다.

#### <a name="to-delete-an-sbc"></a>SBC를 삭제하려면:
1. 왼쪽 탐색의 음성 통화 - PSTN에서 직접 라우팅으로 이동합니다.
1. Session Border Controller 탭에서 구성을 선택합니다.
1. 제거하려는 SBC의 FQDN 및 포트 필드를 지우고 다음을 선택합니다.
1. 음성 경로 탭에서 음성 라우팅 구성을 검토하고 필요한 경우 변경합니다. 저장을 선택합니다.

> [!NOTE]
> 음성 경로와 연결된 SBC를 제거하면 음성 경로 탭에서 경로에 대해 다른 SBC를 선택할 수 있습니다. SBC가 없는 음성 경로가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

### <a name="conceptual-documentation"></a>개념 설명서

- [Azure Communication Services 직접 라우팅에 대해 인증된 Session Border Controller](./certified-session-border-controllers.md)
- [가격](../pricing.md)

### <a name="quickstarts"></a>빠른 시작

- [휴대폰에 전화 걸기](../../quickstarts/voice-video-calling/pstn-call.md)
