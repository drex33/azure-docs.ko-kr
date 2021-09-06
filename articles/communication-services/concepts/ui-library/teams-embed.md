---
title: Teams Embed SDK
titleSuffix: An Azure Communication Services - Teams Embed SDK description
description: 이 문서에서는 Teams 포함 기능과, 애플리케이션에서의 작동 방법을 검토합니다.
author: tophpalmer
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 49fca98d6bd8bc45cb7306dbc6ae9fae5047651b
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123256824"
---
# <a name="teams-embed"></a>Teams 포함

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

Teams 포함은 일반적인 B2C(business-to-customer) 및 B2B(business-to-business) 통화 상호 작용에 초점을 맞춘 Azure Communication Services 기능입니다. Teams 포함 시스템의 핵심은 [화상 및 음성 통화](../voice-video-calling/calling-sdk-features.md)이며, Microsoft Teams 모임 기반의 전체 사용자 환경을 제공하기 위해 Azure의 통화 기본 요소를 기반으로 하여 빌드됩니다.

Teams Embed SDK는 폐쇄형 소스이며, 이러한 기능을 턴키 방식의 복합 형식으로 사용할 수 있습니다. Teams Embed를 앱의 캔버스에 놓으면 SDK에서 전체 사용자 환경을 생성합니다. 이 사용자 환경은 Microsoft Teams 모임과 매우 비슷하므로 다음을 활용할 수 있습니다.

- 개발 시간 및 엔지니어링 복잡성 감소
- 최종 사용자의 Teams 환경에 대한 지식
- [Teams 최종 사용자 학습 콘텐츠](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67)를 다시 사용하는 기능

Teams Embed SDK는 다음을 포함하여 Teams 모임에서 지원되는 대부분의 기능을 제공합니다.

## <a name="joining-a-meeting"></a>모임 참가

사용자는 Teams 애플리케이션과 마찬가지로 Teams 모임 URL을 사용하여 모임에 쉽게 참가할 수 있습니다. Teams 애플리케이션의 간결한 환경을 그대로 유지하면서 사용자에게 광범위한 라이브 모임에 참여할 수 있는 기능을 추가합니다.

## <a name="pre-meeting-experience"></a>모임 전 환경

모임의 참가자는 오디오 및 비디오 디바이스에 대한 기본 구성을 설정할 수 있습니다. 이름을 추가하고 고유한 이미지 아바타를 가져옵니다.

## <a name="meeting-experience"></a>모임 환경

사용자 환경을 사용자 지정하고 필요에 따라 기능을 조정합니다. 모임 중에 전체 환경을 제어합니다.

- [**콘텐츠 공유**](https://support.microsoft.com/office/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8): 사용자는 비디오, 사진 또는 전체 화면을 공유할 수 있고 공유 콘텐츠를 볼 수 있습니다.

- [**비디오 갤러리에 대한 여러 레이아웃 옵션**](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae): 대규모 갤러리, 함께 모드, 포커스, 고정 및 스포트라이트 등, 회의 중에 레이아웃 기본 옵션을 선택하는 기능을 제공합니다. 그리고 디바이스 해상도에 따라 레이아웃을 조정합니다.

- [**비디오 켜기/끄기**](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae#bkmk_turnvideoonoff): 사용자가 모임 중에 비디오를 관리하는 기능을 제공합니다.

- **참석자 동작**: 사용자는 ["발언권을 요청하고"](https://support.microsoft.com/en-us/office/raise-your-hand-in-a-teams-meeting-bb2dd8e1-e6bd-43a6-85cf-30822667b372), 자신의 마이크 음소거를 적용하거나 해제하고, 카메라 또는 오디오 구성을 변경하며 종료하는 등 다양한 동작을 수행할 수 있습니다.

- [**다국어 지원**](https://support.microsoft.com/topic/languages-supported-in-microsoft-teams-for-education-293792c3-352e-4b24-9fc2-4c28b5de2db8): 전체 팀 환경에서 56개 언어를 지원합니다.

## <a name="quality-and-security"></a>품질 및 보안

Teams Embed SDK는 Teams 품질 표준에 따라 빌드되므로 비디오 품질은 [대역폭 요구 사항](/microsoftteams/prepare-network#bandwidth-requirements)에서 확인할 수 있습니다.

Azure Communication Service 액세스 토큰을 사용할 수 있습니다. 자세한 정보는 [액세스 토큰 생성 및 관리 방법](../../quickstarts/access-tokens.md)에서 제공합니다.

## <a name="capabilities"></a>기능

| SDK 기능                                                        | 가용성 |
|---------------------------------------------------------------------|--------------|
| *모임 동작*                                                   |              |
| 호출 참가                                                         | Yes          |
| *호출 상태*(연결 상태, 참가자 수, 마이크/카메라 상태 등의 형식 포함)를 처리합니다.                                           | Yes          |
| 사용자 이벤트(발언권 요청, 음소거 및 전송 비디오 보내기 이벤트) 발생                                                                 | Yes          |
| Flaky Network Handling 지원                                      | Yes          |
| 모임에서 참가자 제거                                    | Yes          |
| 56개 언어 지원                                               | Yes          |
| 모임 중 채팅 및 1n1 채팅                                    | No           |
| PSTN 호출                                                        | No           |
| 녹음/녹화 및 대본                                            | No           |
| 화이트보드 공유                                                  | No           |
| 방 나누기                                                 | No           |
| *모임 참가 환경*                                        |              |
| GUID 및 ACS 토큰을 사용하여 그룹 호출 참가                             | Yes          |
| 라이브 모임 URL 및 ACS 토큰을 사용하여 모임 참가                    | Yes          |
| 모임 URL 및 ACS 토큰을 사용하여 모임 참가                         | Yes          |
| 대기실 대기를 통해 참가                                           | Yes          |

| 사용자 환경 사용자 지정                                       | 가용성 |
|---------------------------------------------------------------------|--------------|
| *모임 동작*                                                   |              |
| 호출 명단 표시                                             | Yes          |
| 레이아웃 사용자 지정: 색, 아이콘, 단추                        | 부분적으로    |
| 호출 화면 아이콘 사용자 지정                                     | Yes          |
| 모임 보기 레이아웃 변경                                         | Yes          |
| 동적 호출 NxN 레이아웃 변경                                    | Yes          |
| 발언권 요청/취소                                                     | Yes          |
| 음소거/음소거 해제                                                        | Yes          |
| 대기 중으로 전환                                                         | Yes          |
| 오디오 라우팅 선택                                                | Yes          |
| 카메라 선택                                                       | Yes          |
| 사진, 화면 및 비디오 공유                                       | Yes          |
| 비디오 시작/중지                                                    | Yes          |
| 사용자 타일 누르기 이벤트                                               | Yes          |
| 이름판 누르기 이벤트                                              | Yes          |
| 배경색 흐리게                                                     | Yes          |
| 화면 배경색 사용자 지정                               | No           |
| 위/아래 표시줄 색 사용자 지정                                  | No           |
| 화이트보드 공유                                                  | No           |
| *모임 전 환경*                                            |              |
| 참가하면서 표시 이름을 구성하고 사진 공유를 사용할 수 있음            | Yes          |
| 참가하면서 호출 준비 보기(호출 전 화면)를 표시하도록 구성할 수 있습니다.   | Yes          |
| 참가하면서 호출 화면에 이름판을 표시하도록 구성할 수 있습니다.             | Yes          |
| 대기실 화면 사용자 지정                                          | 부분적으로    |

Teams Embed SDK 시작 방법에 대한 자세한 정보는 [시작 가이드](../../quickstarts/meeting/getting-started-with-teams-embed.md)를 참조하세요. SDK 기능에 대해 자세히 알아보려면 [샘플 가이드](../../quickstarts/meeting/samples-for-teams-embed.md)를 참조하세요.