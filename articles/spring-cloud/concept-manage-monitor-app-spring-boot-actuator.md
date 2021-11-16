---
title: Azure Spring Boot Actuator를 사용하여 앱 관리 및 모니터링
description: Spring Boot Actuator를 사용하여 앱을 관리하고 모니터링하는 방법을 알아봅니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 001b88e5abe0fc8710d7c87d792de23db4135e16
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486292"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>Azure Spring Boot Actuator를 사용하여 앱 관리 및 모니터링

**이 문서는 다음에 적용됩니다.** ✔️ Java

앱에 새 이진을 배포한 후 기능을 확인하고 실행 중인 애플리케이션에 대한 정보를 볼 수 있습니다. 이 문서에서는 Azure Spring Cloud가 제공하는 테스트 엔드포인트에서 API에 액세스하고 앱의 프로덕션 준비 기능을 노출하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 Azure Spring Cloud 서비스에서 성공적으로 배포, 부팅할 수 있는 Spring Boot 2.x 애플리케이션을 보유하고 있는 것으로 가정합니다.  [빠른 시작: Azure Portal 사용하여 Azure Spring Cloud 기존 애플리케이션 시작](./quickstart.md)

## <a name="verify-app-through-test-endpoint"></a>테스트 엔드포인트를 통해 앱 확인

1. **애플리케이션 대시보드** 로 이동한 후 앱을 선택하여 앱 개요 페이지를 시작합니다.

1. **개요** 창에 **테스트 엔드포인트** 가 표시됩니다.  명령줄 또는 브라우저에서 이 엔드포인트에 액세스하고 API 응답을 관찰합니다.

1. 들어오는 섹션에서 사용할 **테스트 엔드포인트** URI를 적어 둡니다.

>[!TIP]
> * 앱이 프런트 엔드 페이지를 반환하고 상대 경로를 통해 다른 파일을 참조하는 경우, 테스트 엔드포인트가 슬래시(/)로 끝나는지 확인합니다. 이렇게 하면 CSS 파일이 올바르게 로드됩니다.
> * 브라우저를 통해 API를 확인하고 브라우저에서 페이지를 보기 위해서는 로그인 자격 증명을 입력해야 하는 경우, [URL 디코드](https://www.urldecoder.org/)를 사용하여 테스트 엔드포인트를 디코드합니다. URL 디코드는 URL을 "https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/\<app-name>/\<deployment-name>" 형식으로 반환합니다.  이 형식을 사용하여 엔드포인트에 액세스합니다.

## <a name="add-actuator-dependency"></a>작동기 종속성 추가

Maven 기반 프로젝트에 작동기를 추가하려면 'Starter' 종속성을 추가합니다.

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

새 이진을 컴파일하여 앱에 배포합니다.

## <a name="enable-production-ready-features"></a>프로덕션 준비 기능 사용

작동기 엔드포인트를 사용하면 애플리케이션을 모니터링하고 이와 상호 작용할 수 있습니다. 기본적으로, Spring Boot 애플리케이션은 임의의 애플리케이션 정보와 상태 정보를 표시하기 위해 `health` 및 `info` 엔드포인트를 노출합니다.

구성 및 구성 가능한 환경을 관찰하려면 `env` 및 `configgrops` 엔드포인트도 사용하도록 설정해야 합니다.

1. 앱 **개요** 창으로 이동하여 설정 메뉴에서 **구성** 을 선택하고 **환경 변수** 구성 페이지로 이동합니다.
1. "key:value" 양식에서와 같이 다음 속성을 추가합니다. 이 환경은 Spring Actuator 엔드포인트 "env", "health", "info"를 엽니다.

   ```properties
   management.endpoints.web.exposure.include: env,health,info
   ```

1. **저장** 단추를 선택하면 애플리케이션이 자동으로 다시 시작되고 새 환경 변수가 로드됩니다.

이제 앱 개요 창으로 돌아가 프로비전 상태가 "성공"으로 변경될 때까지 기다릴 수 있습니다.  실행 중인 인스턴스가 두 개 이상 있습니다.

> [!Note]
> 앱을 공용으로 노출하면 이러한 작동기 엔드포인트도 공용으로 노출됩니다. `management.endpoints.web.exposure.include` 환경 변수를 삭제하여 모든 엔드포인트를 숨기고 `management.endpoints.web.exposure.exclude=*`을(를) 설정할 수 있습니다.

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>작동기 엔드포인트를 확인하여 애플리케이션 정보 확인

1. 이제 `"<test-endpoint>/actuator/"` URL에 액세스하여 Spring Boot Actuator에서 노출하는 모든 엔드포인트를 볼 수 있습니다.
1. `"<test-endpoint>/actuator/env"` URL에 액세스하면 앱에서 사용하는 활성 프로필과 로드된 모든 환경 변수를 볼 수 있습니다.
1. 특정 환경을 검색하려는 경우 `"<test-endpoint>/actuator/env/{toMatch}"` URL에 액세스하면 볼 수 있습니다.

기본 제공되는 엔드포인트를 모두 [엔드포인트 노출](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Spring Cloud의 메트릭 이해](./concept-metrics.md)
* [Azure Spring Cloud의 앱 상태 이해](./concept-app-status.md)
