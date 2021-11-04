---
title: Windows의 음성 도우미에 대한 개인 정보 보호 지침
titleSuffix: Azure Cognitive Services
description: 기본적으로 음성 에이전트에 대한 음성 활성화를 설정하는 지침입니다.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 074335f9ccf9030a83354355a483ef5cbe0b58d3
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500187"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Windows의 음성 도우미에 대한 개인 정보 보호 지침

사용자에게 음성 데이터가 수집되고 사용되는 방법에 대한 명확한 정보가 제공되어야 하며 이러한 수집의 발생 여부와 방법을 제어할 수 있어야 합니다. 이러한 개인 정보 보호의 핵심 측면인 *공개* 및 *동의* 는 항상 수신되는 사용 특성을 고려할 때 Windows에 통합된 음성 도우미에 특히 중요합니다.

Windows에서 음성 도우미를 만드는 개발자는 도우미의 수신 기능을 반영하는 명확한 사용자 인터페이스 요소를 애플리케이션에 포함해야 합니다.

> [!NOTE]
> 애플리케이션 업데이트 이후를 포함하여 도우미 애플리케이션에 대해 적절한 공개 및 동의를 제공하지 않으면 개인 정보 보호 문제가 해결될 때까지 도우미를 음성 활성화에 사용할 수 없게 될 수 있습니다.

## <a name="minimum-requirements-for-feature-inclusion"></a>기능 포함을 위한 최소 요구 사항

Windows 사용자는 **`Settings > Privacy > Voice activation`** 에서 도우미 애플리케이션의 가용성을 확인하고 제어할 수 있습니다.

 > [!div class="mx-imgBorder"]
 > [![Cortana의 가용성을 제어하는 옵션이 표시된 스크린샷](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "도우미 애플리케이션에 대한 Windows 음성 활성화 개인 정보 보호 설정 항목")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

이 목록에 포함될 수 있게 하려면 Microsoft(winvoiceassistants@microsoft.com)에 문의하여 시작하세요. 기본적으로 사용자는 **`Settings > Privacy > Voice Activation`** 에서 새 도우미에 대한 음성 활성화를 명시적으로 활성화해야 합니다. 그래야 애플리케이션이 프로토콜 방식으로 `ms-settings:privacy-voiceactivation`에 연결할 수 있습니다. `Windows.ApplicationModel.ConversationalAgent` API를 실행하고 사용하면 허용된 애플리케이션이 목록에 표시됩니다. 애플리케이션이 사용자로부터 마이크 동의를 얻으면 음성 활성화 설정을 수정할 수 있습니다.

Windows 개인 정보 보호 설정에는 음성 활성화의 작동 방식에 대한 정보와 사용 권한을 제어하기 위한 표준 UI가 포함되어 있기 때문에 공개와 동의가 모두 충족됩니다. 다음과 같은 경우에는 도우미가 이 허용 목록에서 제외됩니다.

* 도우미가 음성 활성화 또는 음성 데이터 처리에 대해 사용자에게 잘못 알리거나 잘못된 정보를 제공하는 경우
* 다른 도우미와 지나치게 충돌하는 경우
* 기타 관련 Microsoft 정책을 위반하는 경우

위의 항목 중 하나라도 발견되면 Microsoft는 문제가 해결될 때까지 허용 목록에서 도우미를 제거할 수 있습니다.

> [!NOTE]
> 모든 경우 음성 활성화 권한에는 마이크 권한이 필요합니다. 도우미 애플리케이션이 마이크에 액세스할 수 없는 경우 음성 활성화를 설정할 수 없으며 음성 활성화 개인 정보 보호 설정에 사용 안 함 상태로 표시됩니다.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>마이크 동의에 포함하기 위한 추가 요구 사항

도우미 작성자는 사용자가 추가 요구 사항을 충족하는 경우 설정 페이지로 이동하지 않고도 공개와 동의를 적절하게 수행할 수 있도록 하여 음성 활성화를 더 쉽고 원활하게 옵트인하도록 할 수 있습니다. 승인된 경우 사용자가 도우미 애플리케이션에 마이크 권한을 부여하면 즉시 음성 활성화를 사용할 수 있습니다. 이 조건을 충족하려면 마이크 동의를 요청(예: `AppCapability.RequestAccessAsync` API 사용)하기 **전** 에 도우미 애플리케이션에서 다음을 수행해야 합니다.

1. *애플리케이션이 실행되지 않는 경우에도* 애플리케이션이 키워드에 대한 사용자의 음성을 듣고 싶어하며 동의를 구한다는 것을 사용자에게 명확하고 분명하게 표시합니다.
1. 공식 개인 정보 보호 정책에 대한 링크와 같은 데이터 사용 및 개인 정보 보호 정책에 대한 관련 정보를 포함합니다.
1. 오디오 캡처 동작을 공개하는 환경 흐름에서 지시문 또는 선행 문구(예: "다음 프롬프트에서 예 클릭")를 사용하지 않아야 합니다.

애플리케이션이 위의 사항을 모두 수행할 경우 마이크 동의와 함께 음성 활성화 기능을 사용할 수 있습니다. 자세한 내용을 확인하고 첫 사용 환경을 검토하려면 winvoiceassistants@microsoft.com에 문의하세요.

> [!NOTE]
> 잠금 화면 위 음성 활성화는 마이크 액세스를 통한 자동 활성화가 적용되지 않으며, 사용자가 음성 활성화 개인 정보 보호 페이지를 방문하여 도우미의 잠금 화면 위 액세스를 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Windows의 음성 도우미 모범 사례에 대한 자세한 정보](windows-voice-assistants-best-practices.md)
