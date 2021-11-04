---
title: Windows의 음성 도우미 - 디자인 지침
titleSuffix: Azure Cognitive Services
description: 음성 에이전트 환경을 디자인할 때의 모범 사례에 대한 지침입니다.
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: 01f6e066ea5e6bca2f0160ac16ba0a6d94506c26
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509948"
---
# <a name="design-assistant-experiences-for-windows-10"></a>Windows 10용 디자인 도우미 환경

Windows 10에서 개발된 음성 도우미는 Windows 10에서 음성 활성화를 위한 최상의 환경을 제공하기 위해 아래의 사용자 환경 지침을 구현해야 합니다. 이 문서에서는 개발자가 음성 도우미를 Windows 10 셸과 통합하는 데 필요한 주요 작업을 이해할 수 있도록 안내합니다.

## <a name="contents"></a>콘텐츠

- [Windows 10에서 지원되는 음성 활성화 보기 요약](#summary-of-voice-activation-views-supported-in-windows-10)
- [요구 사항 요약](#requirements-summary)
- [바람직한 수신 환경에 대한 모범 사례](#best-practices-for-good-listening-experiences)
- [앱 내 음성 활성화를 위한 디자인 지침](#design-guidance-for-in-app-voice-activation)
- [잠금 화면 위 음성 활성화를 위한 디자인 지침](#design-guidance-for-voice-activation-above-lock)
- [음성 활성화 미리 보기를 위한 디자인 지침](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Windows 10에서 지원되는 음성 활성화 보기 요약

Windows 10은 디바이스 컨텍스트를 기준으로 고객 컨텍스트의 활성화 환경을 유추합니다. 다음 요약 표는 화면이 켜져 있을 때 사용할 수 있는 다양한 보기에 대한 개략적인 개요입니다.

| 보기(가용성) | 디바이스 컨텍스트 | 고객 목표 | 표시되는 경우 | 디자인 요구 사항 |
| --- | --- | --- | --- | --- |
| **앱 내(19H1)** | 잠금 화면 아래, 도우미에 포커스 | 도우미 앱과 상호 작용 | 도우미가 앱 내 요청 처리 | 기본 앱 내 보기 수신 환경 |
| **잠금 화면 위(19H2)** | 잠금 화면 위, 인증되지 않음 | 약간 거리를 두고 도우미와 상호 작용 | 시스템이 잠겨 있고 도우미가 활성화 요청 | 원거리 UI에 대한 전체 화면 시각적 개체. 잠금 해제를 차단하지 않도록 해제 정책 구현 |
| **음성 활성화 미리 보기(20H1)** | 잠금 화면 아래, 도우미에 포커스가 없음 | 도우미와 상호 작용하지만 방해가 적음 | 시스템이 잠금 화면 아래에 있고 도우미가 백그라운드 활성화 요청 | 최소 캔버스. 필요에 따라 기본 앱 보기로 크기를 조정하거나 이전 |

## <a name="requirements-summary"></a>요구 사항 요약

다양한 환경에 액세스하려면 약간의 노력이 필요합니다. 그러나 도우미는 각 보기에 적합한 디자인 지침을 구현해야 합니다. 다음 표에서는 따라야 하는 요구 사항에 대한 검사 목록을 제공합니다.

| **음성 활성화 보기** | **도우미 요구 사항 요약** |
| --- | --- |
| **앱 내** | <ul><li>앱 내 요청 처리</li><li>수신 대기 상태에 대한 UI 표시기 제공</li><li>창 크기가 변경될 때 UI 조정</li></ul> |
| **잠금 화면 위** | <ul><li>잠금 상태 감지 및 활성화 요청</li><li>Windows 잠금 화면에 대한 액세스를 차단하는 영구 UX 제공 금지</li><li>전체 화면 시각적 개체 및 음성 우선 환경 제공</li><li>아래 해제 지침 준수</li><li>아래의 개인 정보 보호 및 보안 고려 사항 준수</li></ul> |
| **음성 활성화 미리 보기** | <ul><li>잠금 해제 상태 감지 및 백그라운드 활성화 요청</li><li>미리 보기 창에서 최소 수신 UX 그리기</li><li>오른쪽 상단에 닫기 X를 그리고 누르면 자동 해제 및 오디오 스트리밍 중지</li><li>답변을 제공하는 데 필요한 경우 기본 도우미 앱 보기로 크기 조정 또는 이전</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>바람직한 수신 환경에 대한 모범 사례

도우미는 고객이 도우미의 상태를 이해할 수 있도록 중요한 피드백을 제공하는 듣기 환경을 구축해야 합니다. 다음은 도우미 환경을 구축할 때 고려할 수 있는 몇 가지 상태입니다. 이러한 항목은 필수 지침이 아닌 제안 사항에 불과합니다.

- 도우미를 음성 입력에 사용할 수 있습니다.
- 도우미를 활성화 중입니다(키워드 또는 마이크 단추 누름).
- 도우미가 현재 도우미 클라우드에 오디오를 스트리밍하는 중입니다.
- 이제 도우미에 음성 명령을 시작해도 됩니다.
- 도우미가 단어를 듣고 있습니다.
- 도우미가 고객이 말하는 것을 이해했습니다.
- 도우미가 응답을 처리 및 준비 중입니다.
- 도우미가 응답 중입니다.

상태가 빠르게 변경되더라도 Windows 에코시스템에 따라 기간이 가변적이므로 상태에 대한 UX를 제공하는 것을 고려할 수 있습니다. 시각적 피드백은 물론, &quot;이어콘&quot;이라고 하는 간단한 벨 소리나 짹짹 소리도 솔루션의 일부가 될 수 있습니다. 마찬가지로 오디오 설명과 결합된 시각적 카드도 좋은 응답 옵션을 제공합니다.

## <a name="design-guidance-for-in-app-voice-activation"></a>앱 내 음성 활성화를 위한 디자인 지침

도우미 앱에 포커스가 있는 경우 고객이 앱과 상호 작용하려는 의도가 분명하므로 모든 음성 활성화 환경은 기본 앱 보기에서 처리해야 합니다. 이 보기는 고객이 크기를 조정할 수 있습니다. 이 문서의 나머지 부분에서는 도우미 셸 상호 작용을 설명하기 위해 Contoso라는 금융 서비스 도우미의 구체적인 예를 사용합니다. 이 다이어그램과 후속 다이어그램에서는 왼쪽의 만화 말 풍선에 고객이 말하는 내용이 나타나고 오른쪽의 만화 말 풍선에는 도우미 응답이 표시되어 있습니다.

**앱 내 보기. 음성 활성화가 시작되는 초기 상태:** 
![Contoso 금융 도우미 앱이 기본 캔버스에 열려 있는 것을 보여 주는 스크린샷. 오른쪽의 만화 말 풍선에는 "Contoso"라고 쓰여 있음](media/voice-assistants/windows_voice_assistant/initial_state.png)

**앱 내 보기. 음성 활성화가 성공적으로 완료되고 듣기 환경 시작:** ![음성 도우미가 듣고 있는 동안의 Windows 음성 도우미 스크린샷](media/voice-assistants/windows_voice_assistant/listening.png)

**앱 내 보기. 모든 응답이 앱 환경에 유지됨.** ![도우미가 응답하는 Windows의 음성 도우미 스크린샷](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>잠금 화면 위 음성 활성화를 위한 디자인 지침

19H2에서 사용할 수 있습니다. Windows 음성 활성화 플랫폼을 기반으로 하는 도우미는 잠금 화면 위에서 대답할 수 있습니다.

### <a name="customer-opt-in"></a>고객 옵트인

잠금 화면 위 음성 활성화는 기본적으로 항상 사용하지 않도록 설정됩니다. 고객은 Windows 설정 > 개인 정보 보호 > 음성 활성화를 통해 옵트인합니다. 이 설정의 모니터링 및 프롬프트에 대한 자세한 내용은 [잠금 화면 위 구현 가이드](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference)를 참조하세요.

### <a name="not-a-lock-screen-replacement"></a>잠금 화면 대체 안 함

도우미가 알림 또는 기타 표준 앱 잠금 화면 통합 지점을 사용할 수 있지만 Windows 잠금 화면은 항상 음성 활성화가 발생할 때까지 초기 고객 환경을 정의합니다. 음성 활성화가 감지되면 도우미 앱이 일시적으로 잠금 화면 위에 표시됩니다. 고객 혼동을 방지하기 위해 잠금 화면 위 활성화 시 도우미 앱은 모든 종류의 자격 증명을 요청하거나 로그인하는 UI를 표시해서는 안 됩니다.

![Windows 잠금 화면 스크린샷](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>음성 활성화 후의 잠금 화면 위 환경

화면을 켜면 도우미 앱이 잠금 화면 위에 제목 표시줄 없이 전체 화면에 표시됩니다. 강력한 음성 기본 인터페이스를 갖춘 강력한 음성 설명과 더 큰 시각적 개체는 고객이 너무 멀리 떨어져 UI를 읽을 수 없거나 다른(PC 이외) 작업으로 손을 사용할 수 없을 때 유용합니다.

화면이 꺼진 상태에서는 도우미 앱이 도우미가 활성화 중임을 알리는 이어콘을 재생하고 음성 전용 환경을 제공할 수 있습니다.

![잠금 화면 위의 음성 도우미 스크린샷](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>해제 정책

도우미는 고객이 다음에 Windows PC를 사용할 때 쉽게 로그인할 수 있도록 이 섹션의 해제 지침을 구현해야 합니다. 다음은 도우미가 구현해야 하는 특정 요구 사항입니다.

- **잠금 화면 위에 표시된 모든 도우미 캔버스에는 오른쪽 상단에 도우미를 해제하는 X** 가 있어야 합니다.
- **아무 키나 눌러도 도우미 앱이 해제되어야 합니다**. 키보드 입력은 고객이 로그인하기를 원한다는 전통적인 잠금 앱 신호입니다. 따라서 키보드/텍스트 입력이 앱으로 전달되지 않아야 합니다. 대신 키보드 입력이 감지되면 고객이 디바이스에 쉽게 로그인할 수 있도록 앱이 자동으로 해제되어야 합니다.
- **화면이 꺼지면 앱이 자동으로 해제되어야 합니다.** 이렇게 하면 다음에 고객이 PC를 사용할 때 로그인 화면이 준비되고 대기하게 됩니다.
- 앱이 &quot;사용 중&quot;인 경우 잠금 화면 위에서 계속 실행될 수 있습니다. &quot;사용 중&quot;은 모든 입력 또는 출력을 구성합니다. 예를 들어 음악 또는 비디오를 스트리밍하는 경우 앱이 잠긴 화면 위에서도 계속 실행될 수 있습니다. &quot;팔로우&quot; 및 기타 멀티 턴 대화 단계는 앱 잠금 화면 위에 유지될 수 있습니다.
- **애플리케이션 해제에 대한 구현 세부 정보** 는 [잠금 화면 위 구현 가이드](windows-voice-assistants-implementation-guide.md#closing-the-application)에서 확인할 수 있습니다.

![Contoso 금융 도우미 앱의 잠금 화면 위 보기를 보여 주는 스크린샷](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![Windows 잠금 화면이 표시된 바탕 화면의 스크린샷](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>잠금 화면 위의 개인 정보 보호 &amp; 보안 고려 사항

휴대할 수 있는 PC도 많이 있지만 항상 고객 옆에 있는 것은 아닙니다. 다른 사람들이 물리적으로 접근할 수 있는 호텔 방, 비행기 좌석 또는 작업 공간에 잠시 두고 올 수 있습니다. 잠금 화면 위 기능이 설정된 도우미가 준비되지 않으면 소위 &quot;[악성](https://en.wikipedia.org/wiki/Evil_maid_attack)&quot; 공격의 대상이 될 수 있습니다.

따라서 도우미 환경을 안전하게 유지하려면 이 섹션의 지침을 따라야 합니다. 잠금 화면 위에서의 상호 작용은 Windows 사용자가 인증되지 않은 경우 발생합니다. 이는 일반적으로 **도우미에 대한 입력도 인증되지 않은 것으로 처리되어야 함** 을 의미합니다.

- 도우미는 **기술 허용 목록을 구현하여 잠금 화면 위에서 접근하기에 안전하다고 확인된 기술을 식별** 해야 합니다.
- 스피커 ID 기술은 일부 위험을 완화하는 데 중요한 역할을 할 수 있지만 스피커 ID는 Windows 인증을 대체하기에 적합하지 않습니다.
- 허용 기술 목록은 다음과 같은 세 가지 클래스의 작업 또는 기술을 고려해야 합니다.

| **작업 클래스** | **설명** | **예시** |
| --- | --- | --- |
| 인증 없이도 안전 | 범용 정보 또는 기본 앱 명령 및 제어 | &quot;What time is it?&quot;, &quot;Play the next track&quot; |
| 스피커 ID 사용 시 안전 | 가장 위험, 개인 정보 노출. | &quot;What&#39;s my next appointment?&quot;, &quot;Review my shopping list&quot;, &quot;Answer the call&quot; |
| Windows 인증 후에만 안전 | 공격자가 고객에게 해를 끼치는 데 사용할 수 있는 높은 위험 수준의 작업 | &quot;Buy more groceries&quot;, &quot;Delete my (important) appointment&quot;, &quot;Send a (mean) text message&quot;, &quot;Launch a (nefarious) webpage&quot; |

Contoso의 경우 공개 주식 정보에 대한 일반 정보는 인증 없이도 안전합니다. 보유한 주식 수와 같은 고객별 정보는 스피커 ID 사용 시 안전할 수 있습니다. 그러나 Windows 인증 없이 주식 구매 또는 판매를 허용해서는 안 됩니다.

환경을 더욱 안전하게 보호하기 위해 **WebLink 또는 기타 앱대앱 실행은 고객이 로그인할 때까지 항상 Windows에서 차단됩니다.** Microsoft는 심각한 보안 문제를 적시에 해결하지 못할 경우 최후의 완화 수단으로 도우미 허용 목록에서 애플리케이션을 제거할 수 있는 권한을 보유합니다.

## <a name="design-guidance-for-voice-activation-preview"></a>음성 활성화 미리 보기를 위한 디자인 지침

잠금 화면 아래에서 도우미 앱에 포커스가 _없는_ 경우 Windows에서는 고객의 흐름을 유지할 수 있도록 덜 방해가 되는 음성 활성화 UI를 제공합니다. 이는 전체 앱을 시작할 경우 매우 방해가 될 수 있는 잘못된 활성화의 경우 특히 그렇습니다. 핵심 아이디어는 셸에 각 도우미에 대한 별도의 홈, 즉 도우미 작업 표시줄 아이콘이 있다는 것입니다. 백그라운드 활성화 요청이 발생하면 도우미 작업 표시줄 아이콘 위에 작은 보기가 나타납니다. 도우미는 이 캔버스에서 작은 수신 환경을 제공해야 합니다. 요청을 처리한 후 도우미는 이 보기의 크기를 조정하여 상황에 맞는 대답을 표시하거나 기본 앱 보기를 넘겨 더 크고 상세한 시각적 개체를 표시하도록 선택할 수 있습니다.

- 최소화하기 위해 미리 보기에는 제목 표시줄이 없으므로, **도우미는 고객이 보기를 해제할 수 있도록 오른쪽 상단에 X를 그려야 합니다.** 닫기 단추를 누를 때 호출할 특정 API에 대해서는 [애플리케이션 닫기](windows-voice-assistants-implementation-guide.md#closing-the-application)를 참조하세요.
- 음성 활성화 미리 보기를 지원하기 위해 도우미는 첫 번째 실행 중에 도우미를 작업 표시줄에 고정하도록 고객을 초대할 수 있습니다.

**음성 활성화 미리 보기: 초기 상태**

Contoso 도우미에는 작업 표시줄에 홈이 있습니다(소용돌이 모양의 원형 아이콘).

![Windows에서 작업 표시줄 아이콘 사전 활성화로 표시된 음성 도우미 스크린샷](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

**활성화가 진행되면** 도우미가 백그라운드 활성화를 요청합니다. 도우미에게는 작은 미리 보기 창(기본 너비: 408, 높이: 248)이 제공됩니다. 서버 측 음성 활성화에서 신호가 가양성으로 판단되는 경우 중단 시간을 최소화하기 위해 이 보기를 해제할 수 있습니다.

![활성화를 확인하는 동안 간단히 보기로 표시된 Windows의 음성 도우미 스크린샷](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

**최종 활성화가 확인되면** 도우미가 수신 UX를 제공합니다. 도우미는 항상 음성 활성화 미리 보기의 오른쪽 상단에 해제 X를 그려야 합니다.

![간단히 보기에서 수신 중인 Windows의 음성 도우미 스크린샷](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

음성 활성화 미리 보기에 **빠른 답변** 이 표시될 수 있습니다. TryResizeView를 사용하면 도우미가 다른 크기를 요청할 수 있습니다.

![간단히 보기에서 응답 중인 Windows의 음성 도우미 스크린샷](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**이전**. 도우미는 언제든지 더 넓은 화면 공간이 필요한 더 많은 정보, 대화 또는 대답을 제공하기 위해 기본 앱 보기로 이전할 수 있습니다. 구현에 대한 자세한 내용은 [간단히 보기에서 전체 보기로 전환](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view) 섹션을 참조하세요.

![간단히 보기를 확장하기 전/후의 Windows 음성 도우미 스크린샷](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 도우미 개발 시작](how-to-windows-voice-assistants-get-started.md)
