---
title: Azure Container Apps 미리 보기에서 애플리케이션 커넥트
description: Azure Container Apps에서 함께 통신하는 여러 애플리케이션을 배포하는 방법을 알아봅니다.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: b39e13aa8cf5953349af14c80317b67f05047919
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576342"
---
# <a name="connect-applications-in-azure-container-apps-preview"></a>Azure Container Apps 미리 보기에서 애플리케이션 커넥트

[수신이](ingress.md) 사용되는 경우 Azure Container Apps는 도메인 이름을 통해 각 컨테이너 앱을 노출합니다. 수신 엔드포인트는 전 세계에 공개적으로 노출되거나 내부적으로 노출될 수 있으며 동일한 [환경의](environment.md)다른 컨테이너 앱에서만 사용할 수 있습니다.

컨테이너 앱의 도메인 이름을 알고 있으면 애플리케이션 코드 내의 위치를 호출하여 여러 컨테이너 앱을 함께 연결할 수 있습니다.

> [!NOTE]
> FQDN을 사용하여 동일한 환경에서 다른 컨테이너를 호출하는 경우 네트워크 트래픽이 환경을 벗어나지 않습니다.

FQDN 위치 또는 Dapr을 둘 다 사용하여 컨테이너 간에 호출하는 방법을 보여주는 샘플 솔루션은 [Azure 샘플에서](https://github.com/Azure-Samples/container-apps-connect-multiple-apps) 찾을 수 있습니다.

## <a name="location"></a>위치

컨테이너 앱의 위치는 환경, 이름 및 지역과 연결된 값으로 구성됩니다. 최상위 도메인을 통해 사용할 수 있는 `azurecontainerapps.io` FQDN(정규화된 도메인 이름)은 다음을 사용합니다.

- 컨테이너 앱 이름
- 환경 고유 식별자
- 지역 이름

다음 다이어그램에서는 이러한 값을 사용하여 컨테이너 앱의 정규화된 도메인 이름을 구성하는 방법을 보여 주는 다이어그램입니다.

:::image type="content" source="media/connect-apps/azure-container-apps-location.png" alt-text="Azure Container Apps 컨테이너 앱의 정규화된 도메인 이름입니다.":::

[!INCLUDE [container-apps-get-fully-qualified-domain-name](../../includes/container-apps-get-fully-qualified-domain-name.md)]

## <a name="dapr-location"></a>Dapr 위치

마이크로서비스를 개발하려면 분산 아키텍처에 공통된 패턴을 구현해야 하는 경우가 많습니다. Dapr을 사용하면 상호 TLS를 사용하여 마이크로서비스를 보호하고, 오류가 발생할 때 재시도를 트리거하고, Azure 애플리케이션 Insights 사용하도록 설정된 경우 분산 추적을 활용할 수 있습니다.

Dapr을 사용하는 마이크로 서비스는 다음 URL 패턴을 통해 사용할 수 있습니다.

:::image type="content" source="media/connect-apps/azure-container-apps-location-dapr.png" alt-text="Dapr을 사용한 Azure Container Apps 컨테이너 앱 위치.":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [시작](get-started.md)
