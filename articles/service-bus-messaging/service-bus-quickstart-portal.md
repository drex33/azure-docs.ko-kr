---
title: Azure Portal을 사용하여 Service Bus 큐 만들기
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Service Bus 네임스페이스 및 네임스페이스에 큐를 만드는 방법에 대해 알아봅니다.
author: spelluru
ms.author: spelluru
ms.date: 09/10/2021
ms.topic: quickstart
ms.custom: mode-portal
ms.openlocfilehash: c6726e3717029b427c2cf15a974edf4d4871d588
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083672"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>Azure Portal을 사용하여 Service Bus 네임스페이스 및 큐 만들기
이 빠른 시작에서는 [Azure Portal][Azure portal]을 사용하여 Service Bus 네임스페이스 및 큐를 만드는 방법을 보여줍니다. 또한 클라이언트 애플리케이션이 큐에 메시지를 보내고 받는 데 사용할 수 있는 권한 부여 자격 증명을 가져오는 방법을 보여줍니다. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정][]을 만들 수 있습니다.

[!INCLUDE [service-bus-create-namespace-portal](./includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](./includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>다음 단계
이 문서에서는 Service Bus 네임스페이스와 네임스페이스에 큐를 만들었습니다. 큐에 메시지를 보내거나 받는 방법에 대한 자세한 내용은 **메시지 보내기 및 받기** 섹션에서 다음 빠른 시작 중 하나를 참조하세요. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)

[무료 계정]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
