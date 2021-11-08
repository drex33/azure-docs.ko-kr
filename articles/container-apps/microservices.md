---
title: Azure 컨테이너 앱 미리 보기를 사용 하는 마이크로 서비스
description: Azure Container Apps에서 마이크로 서비스을 빌드합니다.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 39d095f28c9f642815e5463975b9836e228d9c3a
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028746"
---
# <a name="microservices-with-azure-containers-apps-preview"></a>Azure 컨테이너 앱 미리 보기를 사용 하는 마이크로 서비스

[마이크로 서비스 아키텍처](https://azure.microsoft.com/solutions/microservice-applications/#overview) 를 사용 하면 전체 시스템의 핵심 기능 영역을 독립적으로 개발, 업그레이드, 버전 및 확장할 수 있습니다. Azure Container Apps는 다음과 같은 마이크로 서비스 배포를 위한 토대를 제공 합니다.

- 독립적인 [크기 조정](scale-app.md), [버전 관리](application-lifecycle-management.md)및 [업그레이드](application-lifecycle-management.md)
- [서비스 검색](connect-apps.md)
- 기본 [6C4 통합](microservices-dapr.md)

:::image type="content" source="media/microservices/azure-container-services-microservices.png" alt-text="컨테이너 앱은 마이크로 서비스로 배포 됩니다.":::

컨테이너 앱 [환경은](environment.md) 컨테이너 앱 그룹 주위에 보안 경계를 제공 합니다. 단일 컨테이너 앱은 일반적으로 하나 이상의 [컨테이너로](containers.md)이루어진 pod로 구성 된 마이크로 서비스를 나타냅니다.

## <a name="dapr-integration"></a>4apr 통합

마이크로 서비스로 구성 된 시스템을 구현 하는 경우 함수 호출은 네트워크를 통해 분산 됩니다. 마이크로 서비스의 분산 특성을 지원 하려면 실패, 다시 시도 및 시간 제한을 고려해 야 합니다. 컨테이너 앱은 마이크로 서비스를 실행 하기 위한 빌딩 블록을 제공 하지만, d [4](https://docs.dapr.io/concepts/overview/) 의 사용은 더욱 풍부한 마이크로 서비스 프로그래밍 모델을 제공 합니다. 관찰성, pub/sub와 같은 기능과 상호 TLS, 다시 시도 등을 사용 하는 서비스 간 호출을 포함 합니다.

Eapr 사용에 대 한 자세한 내용은 d 4를 사용 하 [여 마이크로 서비스 빌드](microservices-dapr.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [크기 조정](scale-app.md)
