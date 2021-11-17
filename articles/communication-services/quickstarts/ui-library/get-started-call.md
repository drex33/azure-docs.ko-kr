---
title: 빠른 시작 - 앱에 복합 호출 추가
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 UI 라이브러리에서 복합 호출을 시작하는 방법을 알아봅니다.
author: jorgegarc
ms.author: jorgegarc
ms.date: 10/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: a8bd62d55dfe4cf85b4af01bb93d9a4ea44a0a40
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134029"
---
# <a name="quickstart-get-started-with-calling-composite"></a>빠른 시작: 복합 호출 시작 

UI 라이브러리를 사용하여 Azure Communication Services를 시작하면 통신 환경을 애플리케이션에 빠르게 통합할 수 있습니다. 이 빠른 시작에서는 복합 호출을 Android 또는 iOS 애플리케이션에 통합하는 방법에 대해 알아봅니다.

UI 라이브러리는 전체 통신 환경을 애플리케이션에 바로 렌더링합니다. 원하는 호출에 연결하고 백그라운드에서 설정하는 것을 처리합니다. 개발자는 사용자 환경에서 통신 환경을 시작할 위치에 대해 고민할 필요가 있습니다. 복합은 사용자가 디바이스를 설정하고, 통화에 참가하고, 다른 참가자를 렌더링하는 것을 안내합니다.

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

::: zone pivot="platform-android"
[!INCLUDE [UI Library with Android](./includes/get-started-call/android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [UI Library with iOS](./includes/get-started-call/ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다.

리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.
[리소스 정리](../create-communication-resource.md#clean-up-resources)에 대해 자세히 알아보세요.
