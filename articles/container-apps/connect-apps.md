---
title: Azure 컨테이너 앱 미리 보기의 커넥트 응용 프로그램
description: Azure Container Apps에서 서로 통신 하는 여러 응용 프로그램을 배포 하는 방법을 알아봅니다.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 10fe890ecd4bb2bc89cea71d1a70f7df9eb38e1e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052979"
---
# <a name="connect-applications-in-azure-container-apps-preview"></a>Azure 컨테이너 앱 미리 보기의 커넥트 응용 프로그램

[수신](ingress.md) 이 사용 하도록 설정 된 경우 Azure container Apps는 도메인 이름을 통해 각 컨테이너 앱을 노출 합니다. 수신 끝점은 전 세계에 공개적으로 또는 내부적으로 노출 될 수 있으며 동일한 [환경의](environment.md)다른 컨테이너 앱 에서만 사용할 수 있습니다.

컨테이너 앱의 도메인 이름을 알고 있으면 응용 프로그램 코드 내에서 위치를 호출 하 여 여러 컨테이너 앱을 함께 연결할 수 있습니다.

## <a name="location"></a>위치

컨테이너 앱의 위치는 해당 환경, 이름 및 지역과 연결 된 값으로 구성 됩니다. 최상위 도메인을 통해 사용할 수 있는 정규화 `azurecontainerapps.io` 된 도메인 이름은 다음을 사용 합니다.

- 컨테이너 앱 이름
- 환경 고유 식별자
- 영역 이름

다음 다이어그램에서는 이러한 값을 사용 하 여 컨테이너 앱의 정규화 된 도메인 이름을 구성 하는 방법을 보여 줍니다.

:::image type="content" source="media/connect-apps/azure-container-apps-location.png" alt-text="Azure Container Apps 컨테이너 앱 정규화 된 도메인 이름입니다.":::

[!INCLUDE [container-apps-get-fully-qualified-domain-name](../../includes/container-apps-get-fully-qualified-domain-name.md)]

## <a name="dapr-location"></a>4 월 위치

마이크로 서비스를 개발 하려면 종종 분산 아키텍처에 공통적인 패턴을 구현 해야 합니다. tapr를 사용 하면 상호 TLS로 마이크로 서비스를 보호 하 고, 오류가 발생할 때 다시 시도를 트리거하고, Azure 애플리케이션 Insights 사용 하도록 설정 된 경우 분산 추적을 활용할 수 있습니다.

마이크로 서비스를 사용 하는 다음 URL 패턴을 통해 사용할 수 있습니다.

:::image type="content" source="media/connect-apps/azure-container-apps-location-dapr.png" alt-text="Azure 컨테이너 앱 컨테이너 앱 위치 (4, 4)":::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [시작](get-started.md)
