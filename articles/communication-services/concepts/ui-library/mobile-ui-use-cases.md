---
title: UI 모바일 라이브러리 사용 사례 시나리오
titleSuffix: An Azure Communication Services - UI Mobile Library use cases scenarios
description: 이 문서에서는 UI 모바일 라이브러리 기능 및 애플리케이션에서 작동하는 방법을 소개합니다.
author: jorgegarc
ms.author: jorgegarc
ms.date: 09/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: cc849bb98faea5cbf7f3ec7828cc8318061dc5a5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130182358"
---
# <a name="ui-library-ios-and-android-capabilities"></a>UI 라이브러리(iOS 및 Android) 기능

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

iOS 및 Android용 UI 라이브러리는 호출 **복합** 를 사용하여 호출 사용 사례를 지원합니다.
복합을 사용하면 개발자가 몇 줄의 코드만으로 전체 통화 환경을 애플리케이션에 쉽게 통합할 수 있습니다. 이러한 복합은 설정부터 호출 종료까지 호출의 전체 수명 주기를 처리합니다.

## <a name="calling"></a>호출

| Area                                                                                            | 사용 사례                                              |
| ----------------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| 호출 유형                                                                                      | Teams 모임 참가                                     |
|                                                                                                 | 그룹 ID를 사용하여 Azure Communication Services 통화에 참가   |
| [Teams 상호 운용성](../../concepts/teams-interop.md) | 통화 로비                                             |
|                                                                                                 | 전사 및 기록 경고 배너               |
| 참가자 갤러리                                                                             | 원격 참가자가 표에 표시됩니다.              |
|                                                                                                 | 로컬 사용자에 대한 통화에서 사용할 수 있는 비디오 미리 보기 |
|                                                                                                 | 비디오를 끌 때 사용할 수 있는 기본 아바타            |
|                                                                                                 | 참가자 갤러리에 표시되는 공유 화면 콘텐츠 |
|                                                                                     | 참가자 명단                                     |
| 통화 구성                                                                              | 마이크 디바이스 관리                           |
|                                                                                                 | 카메라 디바이스 관리                               |
|                                                                                                 | 스피커 디바이스 관리                              |
|                                                                                                 | 사용자가 비디오를 확인하는 데 사용할 수 있는 로컬 미리 보기        |
| 통화 제어                                                                                   | 통화 음소거/음소거 해제                                       |
|                                                                                                 | 통화 시 비디오 켜기/끄기                                   |
|                                                                                                 | 통화 종료                                               |

## <a name="supported-identities"></a>지원되는 ID

복합을 초기화하고 서비스에 인증하려면 Azure Communication Services ID가 필요합니다.
인증에 대한 자세한 내용은 [인증](../authentication.md) 및 [액세스 토큰](../../quickstarts/access-tokens.md)을 참조하세요.

## <a name="teams-interop"></a>Teams 상호 운용성

![통화 및 채팅을 위한 Team Interop 패턴](../media/mobile-ui/teams-interop-diagram.png)

[Teams Interop](../teams-interop.md) 시나리오의 경우 개발자는 UI 모바일 라이브러리 구성 요소를 사용하여 Azure Communication Services 통해 Teams 모임에 참가할 수 있습니다.
Teams Interop을 사용하도록 설정하기 위해 개발자는 Teams Interop 호출에 조인하는 수명 주기를 처리할 호출 복합을 사용할 수 있습니다.

:::image type="content" source="../media/mobile-ui/teams-meet.png" alt-text="모임 전 환경":::

## <a name="theming"></a>테마

iOS 및 Android용 UI 라이브러리 통화 복합은 개발자가 기본 색을 전달하여 환경의 테마를 변경하는 인터페이스를 제공합니다. 복합은 해당 기본 색을 사용하여 환경 전체에 적절한 테마를 제공합니다.

| Android                            | iOS                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| :::image type="content" source="../media/mobile-ui/android-color.png" alt-text="android 테마"::: | :::image type="content" source="../media/mobile-ui/ios-dark.png" alt-text="iOS 테마":::  |


## <a name="screen-size"></a>화면 크기

호출 복합은 5" 화면에서 태블릿으로 지원을 가져오고, 동적 참가자의 명단 레이아웃을 가져오고, 보기를 명확하게 제공하고, 대화에 집중할 수 있는 모든 화면 크기에 맞게 조정됩니다.

|분할 모드 | 태블릿 모드|
|---------|---------|
| :::image type="content" source="../media/mobile-ui/meet-splitscreen.png" alt-text="분할 화면"::: |  :::image type="content" source="../media/mobile-ui/tablet-landscape.png" alt-text="태블릿 모드"::: |

## <a name="recommended-architecture"></a>권장 아키텍처

복합은 Azure Communication Services 액세스 토큰을 사용하여 초기화됩니다. 액세스 토큰은 사용자가 관리하는 신뢰할 수 있는 서비스를 통해 Azure Communication Services에서 확보해야 합니다. 자세한 내용은 [빠른 시작: 액세스 토큰 만들기](../../quickstarts/access-tokens.md) 및 [신뢰할 수 있는 서비스 자습서](../../tutorials/trusted-service-tutorial.md)를 참조하세요.

:::image type="content" source="../media/mobile-ui/ui-library-architecture.png" alt-text="권장 아키텍처 다이어그램":::

이러한 클라이언트 라이브러리에는 조인할 호출에 대한 컨텍스트도 필요합니다. 사용자 액세스 토큰과 마찬가지로 이 컨텍스트는 사용자 고유의 신뢰할 수 있는 서비스를 통해 클라이언트에 배포해야 합니다. 아래 목록에는 운영하는 데 필요한 초기화 및 리소스 관리 기능이 요약되어 있습니다.

| Contoso 책임                                 | UI 라이브러리 책임                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| Azure에서 액세스 토큰 제공                          | 지정된 액세스 토큰을 통과하여 구성 요소 초기화        |
| 새로 고침 함수 제공                                 | 개발자 제공 함수를 사용하여 액세스 토큰 새로 고침          |
| 통화 또는 채팅을 위한 참가 정보 검색/전달          | 통화 및 채팅 정보를 전달하여 구성 요소 초기화 |
| 사용자 지정 데이터 모델에 대한 사용자 정보 검색/전달 | 렌더링할 구성 요소에 사용자 지정 데이터 모델 전달          |

## <a name="platform-support"></a>플랫폼 지원

|플랫폼 | 버전|
|---------|---------|
| iOS     | iOS 13 이상 |
| Android | v23+    |

## <a name="accessibility"></a>액세스 가능성

- 설계상의 접근성은 Microsoft 제품에 대한 원칙입니다.
- UI 라이브러리는 모든 UI 구성 요소가 완전히 액세스할 수 있도록 하기 위해 이 원칙을 따릅니다.
- 공개 미리 보기 중에 UI 라이브러리는 UI 구성 요소에 접근성 기능을 계속 개선하고 추가합니다.

## <a name="localization"></a>지역화

- 지역화는 전 세계와 다른 언어를 사용하는 사람들이 사용할 수 있는 제품을 만드는 핵심입니다.
- UI 라이브러리는 RTL과 같은 일부 언어 및 기능에 대한 상자 지원을 제공합니다.
- 개발자는 UI 라이브러리에 사용할 자체 지역화 파일을 제공할 수 있습니다.

> [!div class="nextstepaction"]

UI 모바일 라이브러리 복합으로 시작하는 방법에 대한 자세한 내용은 [빠른 시작 가이드를](../../quickstarts/ui-library/get-started-call.md)따르세요.