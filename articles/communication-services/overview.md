---
title: Azure Communication Services란?
description: Azure Communication Services를 사용하여 실시간 통신을 통해 풍부한 사용자 환경을 개발하는 방법을 알아봅니다.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5e6716980ef8f813be49a7cd6d269d432114aeae
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671947"
---
# <a name="what-is-azure-communication-services"></a>Azure Communication Services란?

Azure Communication Services는 애플리케이션에 통신을 통합하는 데 도움이 되는 REST API 및 클라이언트 라이브러리 SDK가 포함된 클라우드 기반 서비스입니다. 미디어 인코딩 및 실시간 네트워킹과 같은 통신 기술 전문가가 아니어도 애플리케이션에 통신 기능을 추가할 수 있습니다. 이 기능은 정부용 Azure에서도 지원됩니다.

Azure Communication Services는 다양한 통신 형식을 지원합니다.

1. 음성 및 비디오 통화
1. 서식 있는 텍스트 채팅
1. sms

사용자 지정 클라이언트 엔드포인트, 사용자 지정 서비스, PSTN(Publicly Switched Telephony Network) 등을 통신 애플리케이션에 연결할 수 있습니다. Azure Communication Services REST API, SDK 또는 Azure Portal을 통해 직접 전화 번호를 획득하고 SMS 또는 통화 애플리케이션에 해당 번호를 사용합니다. Azure Communication Services 직접 라우팅을 사용하면 SIP 및 세션 테두리 컨트롤러를 사용하여 사용자 고유의 PSTN 통신 사업자를 연결하고 사용자 고유의 전화 번호를 가져올 수 있습니다.

[Azure Communication Services 클라이언트 라이브러리](./concepts/sdk-options.md)는 REST API 외에도 웹 브라우저(JavaScript), iOS(Swift), Java(Android), Windows(.NET)를 비롯한 다양한 플랫폼 및 언어에 사용할 수 있습니다. [웹 브라우저의 UI 라이브러리](https://aka.ms/acsstorybook)를 사용하여 모바일 및 데스크톱 브라우저의 개발을 가속화할 수 있습니다. Azure Communication Services는 ID와 관련이 없으며, 최종 사용자를 식별하고 인증하는 방법을 제어합니다.

Azure Communication Services 시나리오는 다음과 같습니다.

- **B2C(Business to Consumer).** 기업의 직원과 서비스는 사용자 지정 브라우저 또는 모바일 애플리케이션에서 음성, 영상 및 서식 있는 텍스트 채팅을 사용하여 소비자와 상호 작용합니다. 조직은 SMS 메시지를 보내거나 받을 수 있으며, Azure를 통해 획득한 전화 번호를 사용하여 [IVR(대화형 음성 응답 시스템)을 운영](https://github.com/microsoft/botframework-telephony/blob/main/EnableTelephony.md)할 수 있습니다. [Microsoft Teams와의 통합](./quickstarts/voice-video-calling/get-started-teams-interop.md)을 통해 소비자가 직원들이 주최하는 Teams 모임에 연결할 수 있습니다. 이는 원격 의료, 뱅킹 및 제품 지원 시나리오에 적합하며, 직원들은 이미 Teams에 익숙할 수 있습니다.
- **C2C(Consumer to Consumer).** 음성, 영상 및 서식 있는 텍스트 채팅을 통해 소비자와 소비자 간의 상호 작용을 위한 유용한 소셜 공간을 빌드하세요. 모든 유형의 사용자 인터페이스는 Azure Communication Services SDK를 기반으로 빌드하거나 전체 애플리케이션 샘플 및 오픈 소스 UI 도구 키트를 사용하여 빠르게 시작할 수 있습니다.

자세히 알아보려면 [Microsoft Mechanics 영상](https://www.youtube.com/watch?v=apBX7ASurgM) 또는 아래 링크에 연결된 리소스를 확인하세요.

## <a name="common-scenarios"></a>일반적인 시나리오

<br>

| 리소스                               |설명                           |
|---                                    |---                                   |
|**[Communication Services 리소스 만들기](./quickstarts/create-communication-resource.md)**|Azure Portal 또는 Communication Services SDK를 통해 첫 번째 Communication Services 리소스를 프로비저닝하여 Azure Communication Services 사용을 시작합니다. Communication Services 리소스 연결 문자열이 있으면 첫 번째 사용자 액세스 토큰을 프로비저닝할 수 있습니다.|
|**[전화 번호 가져오기](./quickstarts/telephony-sms/get-phone-number.md)**|Azure Communication Services를 사용하여 전화 번호를 프로비전하고 해제합니다. 이러한 전화 번호는 전화 통화를 시작하거나 받고 SMS 솔루션을 구축하는 데 사용할 수 있습니다.|
|**[앱에서 SMS 보내기](./quickstarts/telephony-sms/send.md)**| Azure Communication Services SMS REST API 및 SDK를 사용하면 서비스 애플리케이션에서 SMS 메시지를 보내고 받을 수 있습니다.|

Communication Services 리소스를 만든 후 음성 및 화상 통화 또는 텍스트 채팅 등의 클라이언트 시나리오 빌드를 시작할 수 있습니다.

| 리소스                               |설명                           |
|---                                    |---                                   |
|**[첫 번째 사용자 액세스 토큰 만들기](./quickstarts/access-tokens.md)**|사용자 액세스 토큰은 Azure Communication Services 리소스에 대해 클라이언트를 인증합니다. 이러한 토큰은 Communication Services ID API 및 SDK를 사용하여 프로비전되고 다시 발급됩니다.|
|**[음성 및 화상 통화 시작](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Azure Communication Services를 사용하면 Calling SDK를 사용하여 음성 및 화상 통화를 브라우저 또는 네이티브 앱에 추가할 수 있습니다. |
|**[앱에 전화 통화 추가](./quickstarts/voice-video-calling/pstn-call.md)**|Azure Communication Services를 사용하면 애플리케이션에 전화 통신 기능을 추가할 수 있습니다.|
|**[통화 앱으로 Teams 미팅 참가](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Azure Communication Services는 Microsoft Teams와 상호 작용하는 사용자 지정 모임 환경을 구축하는 데 사용할 수 있습니다. Communication Services 솔루션의 사용자는 음성, 비디오, 채팅 및 화면 공유를 통해 Teams 참가자와 상호 작용할 수 있습니다.|
|**[채팅 시작](./quickstarts/chat/get-started.md)**|Azure Communication Services 채팅 SDK를 사용하여 서식 있는 실시간 텍스트 채팅을 애플리케이션에 추가할 수 있습니다.|
|**[Microsoft Bot을 전화 번호에 연결](https://github.com/microsoft/botframework-telephony)**|전화 통신 채널은 봇이 전화를 통해 사용자와 상호 작용할 수 있도록 하는 Microsoft Bot Framework의 채널입니다. Azure Communication Services 및 Azure Speech Services와 결합된 Microsoft Bot Framework의 강력한 성능을 활용합니다.  |


## <a name="samples"></a>샘플

다음 샘플에서는 Azure Communication Services의 엔드투엔드 사용법을 보여 줍니다. 이러한 샘플을 사용하여 사용자 고유의 Communication Services 솔루션을 부트스트랩합니다.
<br>

| 샘플 이름                               | 설명                           |
|---                                    |---                                   |
|**[그룹 통화 주인공 샘플](./samples/calling-hero-sample.md)**| 브라우저, iOS 및 Android 디바이스용으로 설계된 그룹 통화 애플리케이션 샘플을 다운로드합니다. |
|**[그룹 채팅 주인공 샘플](./samples/chat-hero-sample.md)**| 브라우저용으로 설계된 그룹 텍스트 채팅 애플리케이션 샘플을 다운로드합니다. |
|**[웹 통화 샘플](./samples/web-calling-sample.md)**| 오디오, 비디오 및 PSTN 통화를 위해 디자인된 웹 애플리케이션 샘플을 다운로드합니다. |


## <a name="platforms-and-sdk-libraries"></a>플랫폼 및 SDK 라이브러리

아래 리소스를 사용하여 Azure Communication Services SDK에 대해 자세히 알아보세요. 자체 클라이언트를 빌드하거나 인터넷을 통해 서비스에 액세스하려는 경우 REST API를 대부분의 기능에 사용할 수 있습니다.

| 리소스                               | 설명                           |
|---                                    |---                                   |
|**[SDK 라이브러리 및 REST API](./concepts/sdk-options.md)**|Azure Communication Services 기능은 개념적으로 각각 SDK로 표시되는 6가지 영역으로 구성됩니다. 실시간 통신 요구 사항에 따라 사용할 SDK 라이브러리를 결정할 수 있습니다.|
|**[Calling SDK 개요](./concepts/voice-video-calling/calling-sdk-features.md)**|Communication Services Calling SDK 개요를 검토합니다.|
|**[채팅 SDK 개요](./concepts/chat/sdk-features.md)**|Communication Services 채팅 SDK 개요를 검토합니다.|
|**[SMS SDK 개요](./concepts/telephony-sms/sdk-features.md)**|Communication Services SMS SDK 개요를 검토합니다.|

## <a name="other-microsoft-communication-services"></a>기타 Microsoft Communication Services

현재 Communication Services와 직접 상호 운용할 수 없는 두 가지 다른 Microsoft 통신 제품을 사용하는 것을 고려해 볼 수 있습니다.

 - [Microsoft Graph Cloud Communication API](/graph/cloud-communications-concept-overview)를 사용하면 조직에서 Microsoft 365 라이선스가 있는 Azure Active Directory 사용자와 연결된 통신 환경을 빌드할 수 있습니다. 이는 Azure Active Directory에 연결된 애플리케이션에 적합하거나 Microsoft Teams에서 생산성 환경을 확장하려는 경우에 적합합니다. 또한 [Teams 환경](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest) 내에서 애플리케이션 및 사용자 지정을 빌드하는 API도 있습니다.

 - [Azure PlayFab 파티](/gaming/playfab/features/multiplayer/networking/)는 대기 시간이 짧은 채팅 및 데이터 통신을 게임에 간편하게 추가할 수 있습니다. Communication Services를 사용하여 게임 채팅 및 네트워킹 시스템을 제공할 수 있지만, PlayFab은 맞춤형 옵션이며 Xbox에서 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계

 - [Communication Services 리소스 만들기](./quickstarts/create-communication-resource.md)
