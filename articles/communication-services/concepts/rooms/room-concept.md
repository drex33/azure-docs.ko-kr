---
title: Azure Communication Services 대화방 개요
titleSuffix: An Azure Communication Services concept document
description: Azure 통신 서비스 방에 대해 알아보세요.
author: nmurav
manager: antval
services: azure-communication-services
ms.author: nmurav
ms.date: 11/24/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 2e3009d198f8feb406b75fb59ad31aaffe93dc89
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133519570"
---
# <a name="rooms-overview"></a>방 개요

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Azure 통신 서비스는 구조화 된 대화를 빌드하는 개발자를 위한 대화방 개념을 제공 합니다. 대화방은 비공개 미리 보기에서 음성 및 화상 통화만 지원 합니다.
대화방이 유용한 주요 시나리오는 다음과 같습니다.

- **서비스 관리 통신.** 대화방은 다양 한 업계 응용 프로그램에 적절 하 게 일반으로 제공 되는 한편 서비스 플랫폼에서 모임 스타일 환경을 제공 합니다. 서비스는 환자를 검색 하 고, 클라이언트와 작업 하는 금융 계획자, 법률 서비스를 제공 하는 법률에 대 한 방을 만들고 관리할 수 있습니다. 
- **초대 전용 환경을 제공 하는 기능.** 대화방을 사용 하면 서비스에서 대화방에 참여할 수 있는 사용자를 제어할 수 있습니다. 보드 구성원은 중요 한 토픽의 기밀을 논의할 수 있습니다.


## <a name="when-to-use-rooms"></a>방을 사용 하는 경우
일부 솔루션에는 공간이 필요 하지 않습니다. 기본 일 대 일 또는 일 대 일 임시 상호 작용 빌드와 같은 일부 시나리오는 회의실 없이 호출 또는 채팅 Sdk를 사용 하 여 만들 수 있습니다.

필요한 경우 회의실을 사용 합니다.
- 서버 쪽에서 호출 세션에 액세스할 수 있는 사용자 제어
- 특정 시간에 만료 될 수 있는 좌표가 필요 합니다.
- 초대 된 사용자만 조인할 수 있는 호출

 :::image type="content" source="../media/rooms/decision-tree.png" alt-text="방 선택을 위한 의사 결정 트리를 보여 주는 다이어그램":::

임시 좌표가 필요한 경우에는 그룹 CallID 또는 대화방을 사용할 수 있습니다. 빌드 중인 모든 새 솔루션에 대해 대화방 API를 사용 하는 것이 좋습니다.

| 기능  | 1: N 호출 | 1: N 호출 <br>임시 ID 사용</br> |  대화방 통화 | 
| ------ | :------: | :------: | :------: |
| 대화형 참가자  | 350 | 350 | 350 |
| 참가자에 게 배포할 임시 ID  | No  | 예 (그룹 ID) | 예 (대화방 ID) |
| 초대만 참여   | 예  | 예 | 예 <br>(비공개 미리 보기에서 필수)</br> |
| 만들 API입니다. 호출 제거, 업데이트, 삭제   | 예  | 예 | 대화방 API |
| 전화 통화에 대 한 유효 기간 설정   | 예  | 예 | 예 <br> 최대 6 개월 </br> |


## <a name="managing-the-rooms"></a>대화방 관리

대화방은 대화방 SDK 또는 대화방 API를 통해 관리 됩니다. 초기 릴리스에서 대화방은 대화방 내에서 음성 및 비디오 호출만 허용 합니다. 

서버 응용 프로그램에서 대화방 **API/SDK** 를 사용 합니다.
- 만들기 
- 수정
- 삭제
- 참가자 집합 정의 및 업데이트
- 대화방 유효성을 설정 하 고 수정 합니다 (최대 6 개월).

**JS 호출 sdk** (기타 호출 sdk 및 로드맵에서 채팅 지원 포함)를 사용 하 여 대화방에 참여 합니다. 


아래 그림은 대화방을 관리 하 고 조인 하는 개념을 보여 줍니다.

 :::image type="content" source="../media/rooms/rooms-management.png" alt-text="방 관리를 보여 주는 다이어그램.":::
 
 ## <a name="runtime-operations"></a>런타임 작업
 
 JS 호출 SDK를 호출 하는 일반 일대일 또는 그룹 호출에서 사용할 수 있는 대부분의 작업은 대화방 에서도 사용할 수 있습니다. 기존 일대일 또는 그룹 호출을 대화방 호출로 승격할 수 없으며 대화방에 참가 하기 위해 임시 사용자를 초대할 수 없습니다. (대화방 API를 사용 하 여 사용자를 추가 해야 하는 경우) JS SDK에서 사용 가능한 기능의 전체 목록이 [호출 SDK 개요](../voice-video-calling/calling-sdk-features.md#detailed-capabilities)에 나열 되어 있습니다.

| 기능 | JS 호출 SDK | 대화방 API/SDK |
|----------------------------------------------| :--------: | :---------: |
| 음성 및 비디오를 사용 하 여 대화방 통화 조인 | ✔️ | ❌ |
| 대화방 통화를 조인한 참가자 나열 | ✔️ | ❌ |
| 대화방 통화에 초대 된 모든 참가자를 나열 합니다. | ❌ | ✔️ |
| VoIP 참가자 추가 또는 제거  | ❌ | ✔️ |
| 새 PSTN 참가자 추가 또는 제거  | ❌ | ❌ |

## <a name="next-steps"></a>다음 단계:
-   빠른 시작을 사용 [하 여 대화방을 생성, 관리 및 조인 합니다.](../../quickstarts/rooms/get-started-rooms.md)
-   [미디어 및 신호에 대 한 네트워크 요구 사항](../voice-video-calling/network-requirements.md) 검토



