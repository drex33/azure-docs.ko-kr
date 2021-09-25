---
title: 포털에서 Azure Functions 프리미엄 플랜 만들기
description: Azure Portal을 사용하여 프리미엄 플랜에서 실행되는 함수 앱을 만드는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: e510eb85cb0e30cd6cd0fcfa1a1979dd421dd266
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128573254"
---
# <a name="create-a-premium-plan-function-app-in-the-azure-portal"></a>Azure Portal에서 프리미엄 플랜 함수 앱 만들기

Azure Functions는 가상 네트워크 연결, 콜드 시작 방지, 프리미엄 하드웨어를 제공하는 스케일링 가능한 프리미엄 플랜을 제공합니다. 자세한 정보는 [Azure Functions 프리미엄 플랜](functions-premium-plan.md)을 참조하세요. 

이 문서에서는 Azure Portal을 사용하여 프리미엄 플랜에서 함수 앱을 만드는 방법을 알아봅니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

## <a name="create-a-function-app"></a>함수 앱 만들기

함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱을 사용하면 함수를 논리 단위로 그룹화하여 더 쉽게 리소스를 관리, 배포, 크기 조정 및 공유할 수 있습니다.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

이제 새 함수 앱에서 함수를 만들 수 있습니다. 해당 함수는 [프리미엄 플랜](functions-premium-plan.md)의 이점을 활용할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [HTTP 트리거 함수 추가](./functions-create-function-app-portal.md#create-function)
