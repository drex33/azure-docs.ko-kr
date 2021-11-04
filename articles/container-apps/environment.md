---
title: Azure Container Apps 환경 미리 보기
description: Azure Container Apps에서 환경을 관리하는 방법을 알아봅니다.
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 60876683455d9f35dcfac01a5272e04d40d76e6a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443227"
---
# <a name="azure-container-apps-preview-environments"></a>Azure Container Apps 미리 보기 환경

개별 컨테이너 앱은 컨테이너 앱 그룹을 중심으로 보안 경계 역할을 하는 단일 Container Apps 환경에 배포됩니다. 동일한 환경의 Container Apps는 동일한 가상 네트워크에 배포되고 동일한 Log Analytics 작업 영역에 로그를 씁니다.

:::image type="content" source="media/environments/azure-container-apps-environments.png" alt-text="Azure Container Apps 환경.":::

컨테이너 앱을 동일한 환경에 배포하는 이유에는 다음이 필요한 상황이 포함됩니다.

- 관련 서비스 관리
- 동일한 가상 네트워크에 다른 애플리케이션 배포
- 애플리케이션이 Dapr을 사용하여 서로 통신하게 합니다.
- 애플리케이션이 동일한 Dapr 구성을 공유하도록 합니다.
- 애플리케이션이 동일한 로그 분석 작업 영역을 공유하게 합니다.

여러 환경에 컨테이너 앱을 배포하는 이유에는 다음을 확인하려는 상황이 포함됩니다.

- 두 애플리케이션이 동일한 컴퓨팅 리소스를 공유하지 않습니다.
- 두 애플리케이션은 Dapr을 통해 서로 통신할 수 없습니다.

## <a name="logs"></a>로그

설정 Azure Container Apps 환경 API 리소스와 관련이 있습니다.

| 속성 | Description |
|---|---|
| `properties.appLogsConfiguration` | 환경의 모든 앱에 대한 로그가 게시될 Log Analytics 작업 영역을 구성하는 데 사용됩니다. |
| `properties.containerAppsConfiguration.daprAIInstrumentationKey` | 추적을 위해 Dapr에 제공된 앱 Insights 계측 키 |

## <a name="billing"></a>결제

청구는 개별 컨테이너 앱 및 해당 리소스 사용량과만 관련이 있습니다. Container Apps 환경과 관련된 기본 요금은 없습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자동 조정 동작 관리](scale-app.md)