---
title: Azure Static Web Apps 호스팅 계획
description: 서로 다른 Azure Static Web Apps 호스팅 계획을 비교하고 대조합니다.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: overview
ms.date: 10/05/2021
ms.author: cshoe
ms.openlocfilehash: a29a13e11416e533a27020a745daddf6e26ef919
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729637"
---
# <a name="azure-static-web-apps-hosting-plans"></a>Azure Static Web Apps 호스팅 계획

Azure Static Web Apps는 체험 및 표준의 두 가지 계획을 통해 제공됩니다. [표준 계획 비용은 가격 책정 페이지](https://azure.microsoft.com/pricing/details/app-service/static/)를 참조하세요.

## <a name="features"></a>기능

| 기능 | 무료 요금제 <br> (개인 프로젝트의 경우) | 표준 계획 <br> (프로덕션 앱의 경우) |
| --- | --- | --- |
| 웹 호스팅 | ✔ | ✔ |
| GitHub 통합 | ✔ | ✔ |
| Azure DevOps 통합 | ✔ | ✔ |
| 전역적으로 분산된 정적 콘텐츠 | ✔ | ✔ |
| 무료, 자동으로 SSL 인증서 갱신 | ✔ | ✔ |
| 스테이징 환경 | 앱당 3개 | 앱당 10개 |
| 최대 앱 크기 | 앱당 250MB | 앱당 500MB |
| 사용자 지정 도메인 | 앱당 2개 | 앱당 5개 |
| Azure Functions를 통한 API | 관리 | 관리 또는<br>[자체 Functions 앱 가져오기](functions-bring-your-own.md) |
| 인증 공급자 통합 | [사전 구성](authentication-authorization.md)<br>(서비스 정의) | [사용자 지정 등록](authentication-custom.md) |
| [함수를 사용하여 사용자 지정 역할 할당](authentication-authorization.md?tabs=function#role-management) | - | ✔ |
| 프라이빗 엔드포인트 | - | ✔ |
| [Service Level Agreement(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/app-service-static/v1_0/) | 없음  | ✔ |

## <a name="selecting-a-plan"></a>계획 선택

다음 시나리오는 표준 계획이 요구 사항에 가장 적합한지 여부를 결정하는 데 도움이 될 수 있습니다.

- 예상 트래픽 볼륨이 대역폭 최댓값을 초과합니다.
- 사용하려는 기존 Azure Functions 앱에는 HTTP 엔드포인트를 벗어나는 트리거와 바인딩이 있거나 관리되는 Functions 앱으로 변환할 수 없습니다.
- [사용자 지정 공급자 등록](authentication-custom.md)이 필요한 보안 요구 사항입니다.
- 사이트의 웹 자산 총 파일 크기가 스토리지 최대 크기를 초과합니다.
- 공식적인 고객 지원이 필요합니다.
- 3개를 초과하는 [스테이징 환경](review-publish-pull-requests.md)이 필요합니다.

제한 사항에 대한 자세한 내용은 [할당량 가이드](quotas.md)를 참조하세요.

## <a name="changing-plans"></a>계획 변경

Azure Portal을 통해 무료 계획 또는 표준 계획 간에 이동할 수 있습니다.

1. Azure Portal에서 Static Web Apps 리소스로 이동합니다.

1. _설정_ 메뉴에서 **호스팅 계획** 을 선택합니다.

1. 정적 웹앱에 대해 원하는 호스팅 계획을 선택합니다.

1. **저장** 을 선택합니다.
