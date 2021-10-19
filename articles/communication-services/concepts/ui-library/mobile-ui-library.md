---
title: UI 모바일 라이브러리
titleSuffix: An Azure Communication Services - UI Mobile Library
description: 이 문서에서는 UI 모바일 라이브러리를 소개합니다.
author: jorgegarc
ms.author: jorgegarc
ms.date: 09/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: fdf84a2eb2cc57081a315bbf9dc41c14ee96ae8e
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130182352"
---
# <a name="ui-mobile-library"></a>UI 모바일 라이브러리

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

UI 모바일 라이브러리는 일반적인 기업 간 통화 상호 작용 및 기업 간 통화 상호 작용을 위한 UI 구성 요소를 제공하는 데 중점을 둔 Azure Communication Services 기능입니다. 모바일 UI 라이브러리의 핵심 초점은 비디오 [및 음성 호출을](../voice-video-calling/calling-sdk-features.md)위한 구성 요소입니다. 모바일에서 통화 및 모임 환경을 위한 완벽한 사용자 환경을 제공하기 위해 Azure의 통화 기본형을 기반으로 합니다.

UI 모바일 라이브러리의 목표는 이러한 기능을 턴키 복합 형식으로 제공하는 것입니다. SDK를 즐겨찾는 모바일 개발 앱의 캔버스에 놓으면 SDK가 완전한 사용자 환경을 생성합니다. 이 사용자 환경은 가까우므로 개발 시간 단축 및 엔지니어링 복잡성을 활용할 수 있습니다.

## <a name="composites"></a>복합

복합은 Azure Communication Services 사용하여 일반적인 통신 시나리오에 대한 턴키 솔루션을 제공하는 더 작은 구성 요소로 구성된 상위 수준 구성 요소입니다.

개발자는 Azure Communication Services 액세스 토큰 및 호출에 필요한 구성 특성을 사용하여 복합을 쉽게 인스턴스화할 수 있습니다.

| 복합                                                                   | 사용 사례                                                                                                                                                                                                                                                                                                  |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [CallComposite](../../quickstarts/ui-library/get-started-call.md)  | 사용자가 통화를 시작하거나 참가할 수 있는 통화 환경입니다. 환경 내에서 사용자는 디바이스를 구성하고, 비디오로 통화에 참여하고, 비디오가 켜져 있는 참가자를 비롯한 다른 참가자를 볼 수 있습니다. Teams Interop의 경우 사용자가 잠깐 대기할 수 있도록 의 기능이 포함되어 있습니다. |

## <a name="scenarios"></a>시나리오

### <a name="joining-a-videoaudio-call"></a>비디오/오디오 통화 조인

사용자는 *Teams 모임 URL을* 사용하여 통화를 통해 쉽게 조인하거나 Teams 애플리케이션과 마찬가지로 Azure Communication Services 호출을 간단하고 뛰어난 환경으로 설정할 수 있습니다. 단순성 환경을 손실하고 실제로 중요한 사항에 집중하지 않고 사용자가 광범위한 라이브 비디오 또는 오디오 통화에 참여할 수 있는 기능을 추가합니다.

### <a name="pre-call-experience"></a>호출 전 환경

통화 참가자는 이름을 제공하고 오디오 및 비디오 디바이스에 대한 기본 구성을 설정할 수 있으며, 통화로 이동할 준비가 된 것입니다.

:::image type="content" source="../media/mobile-ui/teams-meet.png" alt-text="사전 회의 환경.":::

### <a name="call-experience"></a>통화 환경

호출 복합은 개발 시간을 최적화하고 클린 레이아웃에 초점을 맞춘 끝 2단 환경을 제공합니다.  

:::image type="content" source="../media/mobile-ui/calling-composite.png" alt-text="모임 환경.":::

**호출 환경은 단일 복합 구성 요소에서 이러한 모든 기능을 제공하며 복잡한 코드 없이 명확한 경로를 제공하여 개발 시간을 단축합니다.**

### <a name="quality-and-security"></a>품질 및 보안

모바일 복합은 Azure Communication Services [액세스 토큰을](../../quickstarts/access-tokens.md)사용하여 초기화됩니다.

### <a name="more-details"></a>자세한 내용

모바일 복합에 대한 자세한 내용이 필요한 경우 [사용 사례 사이트를](mobile-ui-use-cases.md) 방문하여 자세한 내용을 확인하세요.

## <a name="what-ui-artifact-is-best-for-my-project"></a>내 Project 가장 적합한 UI 아티팩트

다음 요구 사항을 이해하면 올바른 클라이언트 라이브러리를 선택하는 데 도움이 됩니다.

- **원하는 사용자 지정이 얼마나 많은가요?** Azure Communication Services 핵심 클라이언트 라이브러리에는 UX가 없으며, 원하는 UX를 구축할 수 있도록 설계되었습니다. UI 라이브러리 구성 요소는 축소된 사용자 지정 비용으로 UI 자산을 제공합니다.

- **대상 플랫폼은 무엇인가요?** 플랫폼마다 기능이 다릅니다.

[UI 라이브러리의](mobile-ui-use-cases.md)기능 가용성에 대한 자세한 내용은 에서 확인할 수 있지만 주요 장단 부분은 아래에 요약되어 있습니다.

| 클라이언트 라이브러리/SDK  | 구현 복잡성 | 사용자 지정 기능 | 호출 |  [Teams 상호 운용성](../../concepts/teams-interop.md) |
| --------------------- | ------------------------- | --------------------- |  ---- | ----------------------------------------------------------------------------------------------- |
| 복합 구성 요소  | 낮음                       | 낮음                   |         ✔    | ✔                                                                                               |
| 핵심 클라이언트 라이브러리 | 높음                      | 높음                  |         ✔    | ✔                                                                                               |

> [!div class="nextstepaction"]

UI 모바일 라이브러리 복합으로 시작하는 방법에 대한 자세한 내용은 [빠른 시작 가이드를](../../quickstarts/ui-library/get-started-call.md)따르세요.