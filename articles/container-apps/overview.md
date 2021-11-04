---
title: Azure Container Apps 미리 보기 개요
description: Azure Container Apps의 일반적인 시나리오 및 용도에 대해 알아보기
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: overview
ms.date: 10/19/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7b7f0cfb02b5aa2b7b37efc306213915a855a7bf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101713"
---
# <a name="azure-container-apps-preview-overview"></a>Azure Container Apps 미리 보기 개요

Azure Container Apps를 사용하면 서버리스 플랫폼에서 마이크로서비스 및 컨테이너화된 애플리케이션을 실행할 수 있습니다. Azure Container Apps의 일반적인 용도는 다음과 같습니다.

- API 엔드포인트 배포
- 백그라운드 처리 애플리케이션 호스팅
- 이벤트 기반 처리 수행
- 마이크로서비스 실행

Azure Container Apps에 빌드된 애플리케이션은 다음 특성에 따라 동적으로 확장할 수 있습니다.

- HTTP 트래픽
- 이벤트 기반 처리
- CPU 또는 메모리 로드
- 모든 [KEDA 지원 스케일러](https://keda.sh/docs/scalers/)

:::image type="content" source="media/overview/azure-container-apps-example-scenarios.png" alt-text="Azure Container Apps의 예시 시나리오.":::

Azure Container Apps를 사용하면 모든 컨테이너에 패키징된 애플리케이션 코드를 실행할 수 있으며 런타임 또는 프로그래밍 모델에 대해 의견이 없습니다. Container Apps를 사용하면 클라우드 인프라 및 복잡한 컨테이너 오케스트레이터 관리에 대한 걱정을 하지 않고 컨테이너 실행의 이점을 누릴 수 있습니다.

Azure Container Apps를 사용하여 다음을 수행할 수 있습니다.

- [여러 컨테이너 버전을 실행](application-lifecycle-management.md)하고 컨테이너 앱의 애플리케이션 수명 주기를 관리합니다.

- KEDA 지원 확장 트리거를 기반으로 앱을 [자동 확장](scale-app.md)합니다. 대부분의 애플리케이션은 0<sup>1</sup>까지 확장할 수 있습니다.

- 다른 Azure 인프라를 관리할 필요 없이 [HTTPS 수신을 사용하도록 설정](ingress.md)합니다.

- Blue/Green 배포 및 A/B 테스트 시나리오를 위해 애플리케이션의 여러 버전에 걸쳐 [트래픽을 분할](revisions.md)합니다.

- DNS 기반 서비스 검색이 내장된 안전한 내부 전용 엔드포인트를 위해 [내부 수신 및 서비스 검색을 사용](connect-apps.md)합니다.

- [Dapr로 마이크로서비스를 구축](microservices.md)하고 풍부한 API 세트에 액세스합니다.

- Docker Hub 및 ACR(Azure Container Registry)을 포함하여 공개 또는 프라이빗 [모든 레지스트리에서 컨테이너를 실행](containers.md)합니다.

- [Azure CLI 확장 프로그램 또는 ARM 템플릿을 사용](get-started.md)하여 애플리케이션을 관리합니다.

- 애플리케이션에서 직접 [비밀을 안전하게 관리](secure-app.md)합니다.

- Azure Log Analytics를 사용하여 [애플리케이션 로그를 봅니다](monitor.md).

<sup>1</sup> [CPU 또는 메모리 부하에 따라 확장](scale-app.md)되는 애플리케이션은 0으로 확장할 수 없습니다.

### <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [첫 번째 컨테이너 앱 배포](get-started.md)
