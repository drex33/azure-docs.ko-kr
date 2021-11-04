---
title: Windows의 음성 도우미 - 개요
titleSuffix: Azure Cognitive Services
description: 사용 가능한 기능 및 개발 리소스 등 Windows의 음성 도우미에 대한 개요입니다.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 4d8013bd8eccf990ad98f24ab720fb5ecb40af9c
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508668"
---
# <a name="voice-assistants-on-windows"></a>Windows의 음성 도우미

Windows 10 버전 2004 이상에서는 음성 도우미 애플리케이션이 Windows ConversationalAgent API를 활용하여 완벽한 음성 지원 도우미 환경을 제공할 수 있습니다.

## <a name="voice-assistant-features"></a>음성 도우미 기능

음성 에이전트 애플리케이션은 음성 키워드로 활성화되어 핸즈프리 음성 기반 환경을 구현할 수 있습니다. 음성 활성화는 애플리케이션이 닫히거나 화면이 잠겨 있을 때 작동합니다.

또한 Windows서는 사용자가 앱별로 음성 활성화 및 잠금 화면 위 활성화를 제어할 수 있는 일련의 음성 활성화 개인 정보 보호 설정을 제공합니다.

음성 활성화 후 Windows에서는 여러 활성 에이전트를 올바르게 관리하고 에이전트가 중단되거나 비활성화된 경우 각 음성 도우미에게 알립니다. 이를 통해 애플리케이션은 중단 및 기타 에이전트 간 이벤트를 적절히 관리할 수 있습니다.

## <a name="how-does-voice-activation-work"></a>음성 활성화 작동 방식

AAR(Agent Activation Runtime)은 Windows에서 진행되는 프로세스로서 음성 키워드 또는 단추 누르기에 대한 애플리케이션 활성화를 관리합니다. 시스템에 등록된 애플리케이션이 시스템에 하나 이상 있는 경우 Windows에서 시작합니다. 애플리케이션은 Windows SDK의 ConversationalAgent API를 통해 AAR과 상호 작용합니다.

사용자가 키워드를 말하면 시스템의 소프트웨어나 하드웨어 키워드 스포터는 AAR에 키워드가 감지되었음을 알리고 키워드 ID를 제공합니다. AAR은 다시 BackgroundService에 해당 애플리케이션 ID로 애플리케이션을 시작하라는 요청을 전송합니다.

### <a name="registration"></a>등록

음성이 활성화된 애플리케이션이 처음 실행되면 ConversionalAgent API를 통해 앱 ID와 키워드 정보를 등록합니다. AAR은 글로벌 매핑의 모든 구성을 시스템의 하드웨어 또는 소프트웨어 키워드 스포터에 등록하여 애플리케이션의 키워드를 감지할 수 있게 합니다. 애플리케이션은 [백그라운드 서비스에도 등록](/windows/uwp/launch-resume/register-a-background-task)됩니다.

이는 애플리케이션이 한 번 실행되고 등록이 완료될 때까지 음성으로 활성화할 수 없음을 의미합니다.

### <a name="receiving-an-activation"></a>활성화 수신

AAR에서 요청을 받으면 백그라운드 서비스가 애플리케이션을 시작합니다. 애플리케이션은 고유한 이벤트 인수와 함께 `App.xaml.cs`의 OnBackgroundActivated 수명 주기 메서드를 통해 신호를 수신합니다. 이 인수는 AAR에 의해 활성화되었으며 키워드 확인을 시작해야 한다는 것을 애플리케이션에 알립니다.

애플리케이션이 키워드를 성공적으로 확인하면 전경에 나타나는 요청을 만들 수 있습니다. 이 요청이 성공하면 애플리케이션이 UI를 표시하고 사용자와의 상호 작용을 계속합니다.

키워드를 말하면 AAR에서 활성 애플리케이션에 신호를 보냅니다. 그러나 `App.xaml.cs`의 수명 주기 메서드를 통해 신호를 보내는 대신 ConversationalAgent API의 이벤트를 통해 신호를 보냅니다.

### <a name="keyword-verification"></a>키워드 확인

애플리케이션 시작을 트리거하는 키워드 스포터는 키워드 모델을 단순화하여 저전력 소비를 달성했습니다. 이는 고기능의 영향 없이 키워드 스포터가 "항상 작동"할 수 있지만 키워드를 말하지 않았는데도 키워드를 감지하는 '허위 수락'이 발생할 가능성이 크다는 것을 의미하기도 합니다. 이것이 바로 음성 활성화 시스템이 백그라운드에서 애플리케이션을 시작하는 이유입니다. 즉, 사용자의 현재 세션을 중단하기 전에 해당 키워드가 사용되었는지 확인할 수 있는 기회를 애플리케이션에 부여하기 위해서입니다. AAR은 키워드가 스폿되기 몇 초 전부터 오디오를 저장하여 애플리케이션에서 액세스할 수 있도록 합니다. 애플리케이션은 이를 사용하여 동일한 오디오에서 보다 신뢰할 수 있는 키워드 스포터를 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- **디자인 지침 검토:** 당사의 [디자인 지침](windows-voice-assistants-best-practices.md)에서는 Windows 10에서 음성 활성화를 위한 최상의 환경을 제공하는 데 필요한 주요 작업에 대해 설명합니다.
- **시작 페이지 방문**: 개발 환경 설정부터 구현 가이드 소개까지 Windows에서 음성 도우미 구현을 시작하는 단계는 [여기](how-to-windows-voice-assistants-get-started.md)에서 시작합니다.
- **샘플 앱 사용**: 이러한 기능을 직접 경험하려면 [UWP 음성 도우미 샘플](windows-voice-assistants-faq.yml#the-uwp-voice-assistant-sample) 페이지를 방문하여 단계에 따라 샘플 클라이언트를 실행합니다.
