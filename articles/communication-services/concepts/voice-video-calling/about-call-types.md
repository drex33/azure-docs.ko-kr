---
title: Azure Communication Services의 음성 및 비디오 개념
titleSuffix: An Azure Communication Services concept document
description: Communication Services 호출 형식에 대해 알아봅니다.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 9fd2de1bdcd89f027db9a3fd74ef40111f540ced
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635967"
---
# <a name="voice-and-video-concepts"></a>음성 및 비디오 개념

[!INCLUDE [SDP Plan B Deprecation Notice](../../includes/plan-b-sdp-deprecation.md)]

Azure Communication Services를 사용하여 일대일 또는 그룹 음성 및 영상 통화를 걸고 받을 수 있습니다. 인터넷에 연결된 다른 디바이스 및 일반 전화기에 대한 호출을 수행할 수 있습니다. Communication Services JavaScript, Android 또는 iOS SDK를 사용하여 사용자가 프라이빗 대화 또는 그룹 토론에서 서로 말할 수 있는 애플리케이션을 빌드할 수 있습니다. Azure Communication Services는 서비스나 봇에서의 호출을 지원합니다.

## <a name="call-types-in-azure-communication-services"></a>Azure Communication Services의 호출 유형

Azure Communication Services에서 수행할 수 있는 호출에는 여러 유형이 있습니다. 호출 유형에 따라 신호 스키마, 미디어 트래픽 흐름 및 가격 책정 모델이 결정됩니다.

### <a name="voice-over-ip-voip"></a>VoIP(Voice over IP)

애플리케이션의 사용자가 인터넷 또는 데이터 연결을 통해 애플리케이션의 다른 사용자를 호출하면 VoIP(Voice Over IP)를 통해 호출이 수행됩니다. 이 경우 신호와 미디어 모두 인터넷을 통해 전달됩니다.

### <a name="public-switched-telephone-network-pstn"></a>PSTN(공중 교환 전화망)

사용자가 기존 전화 번호와 상호 작용하는 경우 PSTN(공중 교환 전화망) 음성 통화를 통해 호출이 지원됩니다. PSTN 호출을 수행 및 수신하려면 Azure Communication Services 리소스에 전화 통신 기능을 추가해야 합니다. 이 경우 신호와 미디어는 IP 기반 및 PSTN 기반 기술의 조합을 사용하여 사용자를 연결합니다.

### <a name="one-to-one-call"></a>일 대 일 호출

사용자 중 한 명이 SDK 중 하나를 사용하여 다른 사용자에 연결하는 경우 Azure Communication Services에 대한 일 대 일 호출이 발생합니다. 호출은 VoIP 또는 PSTN 중 하나일 수 있습니다.

### <a name="group-call"></a>그룹 호출

Azure Communication Services에 대한 그룹 호출은 세 명 이상의 참가자가 서로 연결하는 경우에 발생합니다. VoIP와 PSTN으로 연결된 사용자 조합으로 이루어진 그룹 호출이 있을 수 있습니다. 일 대 일 호출은 호출에 참가자를 더 추가하면 그룹 호출로 전환될 수 있다. 그러한 참가자 중 봇이 있을 수도 있습니다.

### <a name="supported-video-standards"></a>지원되는 비디오 표준
H.264(MPEG-4) 지원

### <a name="video-quality"></a>비디오 품질
네이티브(iOS, Android) SDK에서 최대 Full HD 1080p를 지원합니다. Web(JS) SDK의 경우 Standard HD 720p를 지원합니다. 품질은 사용 가능한 대역폭에 따라 달라집니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [통화 시작](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

자세한 내용은 다음 항목을 참조하세요.
- 일반적인 [통화 흐름](../call-flows.md) 숙지
- [전화 번호 유형](../telephony-sms/plan-solution.md)
- [Calling SDK 기능](../voice-video-calling/calling-sdk-features.md)에 대해 알아보기
