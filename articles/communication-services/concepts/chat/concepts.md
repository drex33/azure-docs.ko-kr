---
title: Azure Communication Services의 채팅 개념
titleSuffix: An Azure Communication Services concept document
description: Communication Services 채팅 개념에 대해 알아봅니다.
author: knvsl
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: chat
ms.openlocfilehash: 49fb9db94ceeaef94dc6c255002f392232ca820c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434164"
---
# <a name="chat-concepts"></a>채팅 개념 

Azure Communication Services 채팅 SDK를 사용하여 실시간 텍스트 채팅을 애플리케이션에 추가할 수 있습니다. 이 페이지에는 주요 채팅 개념 및 기능이 요약되어 있습니다.    

특정 SDK 언어 및 기능에 대해 자세히 알아보려면 [Communication Services 채팅 SDK 개요](./sdk-features.md)를 참조하세요.  

## <a name="chat-overview"></a>채팅 개요    

채팅 대화는 **채팅 스레드** 내에서 발생합니다. 채팅 스레드는 다음 속성을 가집니다.

- 채팅 스레드는 `ChatThreadId`에 의해 고유하게 식별됩니다. 
- 채팅 스레드에는 메시지를 보낼 수 있는 참가자로 한 명 이상의 사용자가 있을 수 있습니다. 
- 사용자는 하나 이상의 채팅 스레드에 속할 수 있습니다. 
- 스레드 참가자만 지정된 채팅 스레드에 액세스할 수 있으며, 채팅 스레드 작업을 수행할 수 있습니다. 이러한 작업에는 메시지 송수신, 참가자 추가 및 참가자 제거가 포함됩니다. 
- 사용자는 자신이 만든 모든 채팅 스레드에 자동으로 참가자로 추가됩니다.

### <a name="user-access"></a>사용자 액세스
일반적으로 스레드 작성자와 참여자는 스레드 접근 권한이 동일하며, 스레드 삭제를 포함하여 SDK에서 사용 가능한 모든 관련 작업을 실행할 수 있습니다. 참가자는 다른 참가자가 보낸 메시지에 대한 쓰기 권한이 없으므로, 메시지를 보낸 사람만 보낸 메시지를 업데이트하거나 삭제할 수 있습니다. 다른 참가자가 이 작업을 시도하면 오류가 발생합니다. 

### <a name="chat-data"></a>채팅 데이터 
Communication Services는 명시적으로 삭제될 때까지 채팅 기록을 저장합니다. 채팅 스레드 참가자는 `ListMessages`를 사용하여 특정 스레드에 대한 메시지 기록을 볼 수 있습니다. 채팅 스레드에서 제거된 사용자는 이전 메시지 기록을 볼 수 있지만, 해당 채팅 스레드의 일부로 새 메시지를 보내거나 받을 수는 없습니다. 참가자가 없는 완전히 유휴 상태인 스레드는 30일 후에 자동으로 삭제됩니다. Communication Services에 의해 저장되는 데이터에 대한 자세한 내용은 [개인 정보](../privacy.md)에 대한 문서를 참조하세요.  

### <a name="service-limits"></a>서비스 제한  
- 채팅 스레드에 허용되는 최대 참가자 수는 250명입니다.   
- 허용되는 최대 메시지 크기는 약 28KB입니다.  
- 참가자가 20명이 넘는 채팅 스레드의 경우 수신 확인 및 입력 표시기 기능이 지원되지 않습니다.    

## <a name="chat-architecture"></a>채팅 아키텍처    

채팅 아키텍처에는 다음과 같은 두 가지 핵심 부분이 있습니다. 1) 신뢰할 수 있는 서비스 및 2) 클라이언트 애플리케이션.    

:::image type="content" source="../../media/chat-architecture.png" alt-text="Communication Services의 채팅 아키텍처를 보여주는 다이어그램"::: 

 - **신뢰할 수 있는 서비스:** 채팅 세션을 제대로 관리하려면 리소스 연결 문자열을 사용하여 Communication Services에 연결하는 데 도움이 되는 서비스가 필요합니다. 이 서비스는 채팅 스레드를 만들고, 참가자를 추가 및 제거하고, 사용자에게 액세스 토큰 발급을 담당합니다. 액세스 토큰에 대한 자세한 내용은 [액세스 토큰](../../quickstarts/access-tokens.md) 빠른 시작을 참조하세요.  
 - **클라이언트 앱:** 클라이언트 애플리케이션은 신뢰할 수 있는 서비스에 연결하고 사용자가 Communication Services에 직접 연결하는 데 사용되는 액세스 토큰을 받습니다. 신뢰할 수 있는 서비스에서 채팅 스레드를 만들고 사용자를 참가자로 추가하면 클라이언트 앱을 사용하여 채팅 스레드에 연결하고 메시지를 보낼 수 있습니다. 클라이언트 앱에서 아래에서 설명할 실시간 알림 기능을 사용하여 다른 참가자의 메시지 및 스레드 업데이트를 구독합니다.
    
        
## <a name="message-types"></a>메시지 유형    

메시지 기록의 일부로 채팅은 사용자 생성 메시지와 시스템 생성 메시지를 공유합니다. 시스템 메시지는 채팅 스레드가 업데이트될 때 생성되며 참가자가 추가 또는 제거된 시기 또는 채팅 스레드 토픽이 업데이트된 시기를 식별하는 데 도움이 될 수 있습니다. 채팅 스레드에서 `List Messages` 또는 `Get Messages`를 호출하면 결과에 두 종류의 메시지가 모두 시간순으로 포함됩니다.

사용자가 생성한 메시지의 경우 메시지를 채팅 스레드로 보낼 때 메시지 유형을 `SendMessageOptions`에서 설정할 수 있습니다. 값이 제공되지 않으면 Communication Services는 기본적으로 `text` 유형으로 설정됩니다. HTML을 보낼 때 이 값을 설정하는 것이 중요합니다. `html`이 지정되면 Communication Services는 클라이언트 디바이스에서 안전하게 렌더링되도록 콘텐츠를 삭제합니다.
 - `text`: 채팅 스레드의 일부로 사용자가 작성하여 보낸 일반 문자 메시지입니다. 
 - `html`: 채팅 스레드의 일부로 사용자가 작성하여 보낸 html을 사용하는 형식화된 메시지입니다. 

시스템 메시지 형식: 
 - `participantAdded`: 한 명 이상의 참가자가 채팅 스레드에 추가되었음을 나타내는 시스템 메시지입니다.
 - `participantRemoved`: 참가자가 채팅 스레드에서 제거되었음을 나타내는 시스템 메시지입니다.
 - `topicUpdated`: 스레드 토픽이 업데이트되었음을 나타내는 시스템 메시지입니다.

## <a name="real-time-notifications"></a>실시간 알림  

일부 SDK(예: JavaScript 채팅 SDK)는 실시간 알림을 지원합니다. 이 기능을 사용하면 클라이언트가 API를 폴링하지 않고도 실시간 업데이트 및 채팅 스레드에 들어오는 메시지를 위해 Communication Services를 수신 대기할 수 있습니다. 클라이언트 앱은 다음 이벤트를 구독할 수 있습니다.
 - `chatMessageReceived` - 참가자가 새 메시지를 채팅 스레드로 전송할 때.
 - `chatMessageEdited` - 채팅 스레드에서 메시지가 편집될 때. 
 - `chatMessageDeleted` - 채팅 스레드에서 메시지가 삭제될 때.   
 - `typingIndicatorReceived` - 다른 참가자가 입력 표시기를 채팅 스레드로 보낼 때.    
 - `readReceiptReceived` - 다른 참가자가 읽은 메시지에 대해 읽음 확인을 보낼 때.  
 - `chatThreadCreated` - Communication Services 사용자가 채팅 스레드를 만들 때.    
 - `chatThreadDeleted` - Communication Services 사용자가 채팅 스레드를 삭제할 때.    
 - `chatThreadPropertiesUpdated` - 채팅 스레드 속성이 업데이트될 때, 현재 스레드에 대한 토픽 업데이트만 지원됩니다. 
 - `participantsAdded` - 사용자가 채팅 스레드 참가자로 추가될 때.     
 - `participantsRemoved` - 기존 참가자가 채팅 스레드에서 제거될 때.

실시간 알림을 사용하여 사용자에게 실시간 채팅 경험을 제공할 수 있습니다. 사용자가 자리를 비운 동안 놓친 메시지에 대한 푸시 알림을 보내기 위해 Communication Services는 Azure Event Grid와 통합하여 사용자 지정 앱 알림 서비스에 연결할 수 있는 채팅 관련 이벤트(사후 작업)를 게시합니다. 자세한 내용은 [서버 이벤트](../../../event-grid/event-schema-communication-services.md?bc=https%3a%2f%2fdocs.microsoft.com%2fen-us%2fazure%2fbread%2ftoc.json&toc=https%3a%2f%2fdocs.microsoft.com%2fen-us%2fazure%2fcommunication-services%2ftoc.json)를 참조하세요.


## <a name="build-intelligent-ai-powered-chat-experiences"></a>지능형 AI 기반 채팅 환경 빌드   

채팅 SDK와 함께 [Azure Cognitive API](../../../cognitive-services/index.yml)를 사용하여 다음과 같은 사용 사례를 빌드할 수 있습니다.

- 사용자가 서로 다른 언어로 채팅할 수 있도록 합니다.  
- 고객에게서 들어오는 메시지에서 부정적인 감정을 감지하여 지원 상담원이 티켓의 우선 순위를 지정하도록 돕습니다. 
- 들어오는 메시지에서 키 감지 및 엔터티 인식을 분석하여 메시지 내용을 기반으로 앱의 사용자에게 관련 정보를 표시합니다.

이러한 기능을 구현하는 한 가지 방법은 신뢰할 수 있는 서비스를 채팅 스레드에서 참가자로 사용하는 것입니다. 언어 번역을 활성화하려는 경우를 가정해 보겠습니다. 이 서비스는 다른 참가자가 서로 주고 받는 메시지를 듣고[1], Cognitive API를 호출하여 콘텐츠를 원하는 언어로 번역하고[2,3] 번역된 결과를 채팅 스레드에 메시지로 보내는[4] 작업을 담당합니다.

이렇게 하면 메시지 기록에 원본 메시지와 번역된 메시지가 모두 포함됩니다. 클라이언트 애플리케이션에서 원래 메시지 또는 번역된 메시지를 표시하는 논리를 추가할 수 있습니다. Cognitive API를 사용하여 텍스트를 다른 언어로 번역하는 방법을 이해하려면 [이 빠른 시작](../../../cognitive-services/translator/quickstart-translator.md)을 참조하세요. 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Communication Services와 상호 작용하는 Cognitive Services를 보여주는 다이어그램"::: 

## <a name="next-steps"></a>다음 단계   

> [!div class="nextstepaction"] 
> [채팅 시작](../../quickstarts/chat/get-started.md)    

다음 문서는 사용자에게 유용할 수 있습니다.  
- [채팅 SDK](sdk-features.md)를 숙지하세요.
