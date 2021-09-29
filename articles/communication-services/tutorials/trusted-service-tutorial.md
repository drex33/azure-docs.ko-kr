---
title: Azure Communication Services에서 Azure Functions를 사용하여 신뢰할 수 있는 사용자 액세스 서비스 빌드
titleSuffix: An Azure Communication Services tutorial
description: Azure Functions를 사용하여 Communication Services를 위한 신뢰할 수 있는 사용자 액세스 서비스를 만드는 방법을 알아봅니다.
author: ddematheu2
manager: chpalm
services: azure-communication-services
ms.author: dademath
ms.date: 06/30/2021
ms.topic: tutorial
ms.service: azure-communication-services
ms.subservice: identity
ms.openlocfilehash: 1c6a5f215d583069884f7c7c0e23b9e4b553bf9c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671510"
---
# <a name="build-a-trusted-user-access-service-using-azure-functions"></a>Azure Functions를 사용하여 신뢰할 수 있는 사용자 액세스 서비스 빌드

이 문서에서는 Azure Functions를 사용하여 신뢰할 수 있는 사용자 액세스 서비스를 빌드하는 방법을 설명합니다.

> [!IMPORTANT]
> 이 자습서의 끝에서 만드는 엔드포인트는 안전하지 않습니다. [Azure 함수 보안](../../azure-functions/security-concepts.md) 문서에서 보안 세부 정보를 확인해야 합니다. 잘못된 작업자가 토큰을 프로비저닝할 수 없도록 엔드포인트에 보안을 추가해야 합니다.

[!INCLUDE [Trusted Service JavaScript](./includes/trusted-service-js.md)]

## <a name="securing-azure-function"></a>Azure 함수 보호

사용자의 액세스 토큰을 프로비저닝하도록 신뢰할 수 있는 서비스를 설정하는 과정에서 잘못된 작업자가 서비스의 토큰을 임의로 만들 수 없도록 해당 엔드포인트의 보안을 고려해야 합니다. Azure Functions는 다양한 유형의 인증 정책으로 엔드포인트를 보호하는 데 사용할 수 있는 기본 제공 보안 기능을 제공합니다. [Azure 함수 보안](../../azure-functions/security-concepts.md)에 대해 자세히 알아보세요.

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. [Azure Communication Service 리소스 정리](../quickstarts/create-communication-resource.md#clean-up-resources) 및 [Azure 함수 리소스 정리](../../azure-functions/create-first-function-vs-code-csharp.md#clean-up-resources)에 대해 자세히 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure 함수 보안에 대한 자세한 정보](../../azure-functions/security-concepts.md)

> [!div class="nextstepaction"]
> [앱에 음성 통화 추가](../quickstarts/voice-video-calling/getting-started-with-calling.md)

다음을 수행할 수도 있습니다.

- [앱에 채팅 추가](../quickstarts/chat/get-started.md)
- [사용자 액세스 토큰 만들기](../quickstarts/access-tokens.md)
- [클라이언트 및 서버 아키텍처에 대한 자세한 정보](../concepts/client-and-server-architecture.md)
- [인증에 대한 자세한 정보](../concepts/authentication.md)