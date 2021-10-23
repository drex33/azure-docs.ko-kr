---
title: 서비스 레지스트리 및 검색 자동화
description: Spring Cloud 서비스 레지스트리를 사용하여 서비스 검색 및 등록을 자동화하는 방법 알아보기
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: e73fdd7c07104d791fc5259fbcd6a7edf3c9b0c5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241196"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Spring Cloud 서비스 검색 및 등록

서비스 검색은 마이크로 서비스 기반 아키텍처의 핵심 요구 사항입니다.  각 클라이언트를 수동으로 구성하면 시간이 걸리고 인적 오류가 발생할 가능성이 있습니다.  Azure Spring Cloud 서비스 레지스트리가 이 문제를 해결합니다.  구성된 서비스 레지스트리 서버는 애플리케이션의 마이크로 서비스에 대한 서비스 등록과 검색을 제어합니다. 서비스 레지스트리 서버는 배포된 마이크로 서비스의 레지스트리를 유지 관리하고 클라이언트 측 부하 분산을 지원하며 DNS에 의존하지 않고 서비스 공급자를 클라이언트로부터 분리합니다.

::: zone pivot="programming-language-csharp"
Steeltoe 앱에 대한 서비스 등록을 설정하는 방법에 대한 자세한 내용은 [Azure Spring Cloud에서 배포용 Java Spring 애플리케이션 준비](how-to-prepare-app-deployment.md)를 참조하세요.
::: zone-end
::: zone pivot="programming-language-java"

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Spring Cloud 서비스 레지스트리를 사용하여 애플리케이션 등록

애플리케이션에서 Spring Cloud 서비스 레지스트리를 사용하여 서비스 등록 및 검색을 관리하려면 여러 종속성이 애플리케이션의 *pom.xml* 파일에 포함되어야 합니다.
*spring-cloud-starter-netflix-eureka-client* 및 *spring-cloud-starter-azure-spring-cloud-client* 에 대한 종속성을 *pom.xml* 에 포함합니다.

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
```

## <a name="update-the-top-level-class"></a>최상위 클래스 업데이트

마지막으로 애플리케이션의 최상위 수준 클래스에 주석을 추가합니다.

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

    @SpringBootApplication
    @EnableEurekaClient
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Spring Cloud 서비스 레지스트리 서버 엔드포인트가 애플리케이션의 환경 변수로 삽입됩니다.  이제 마이크로 서비스가 서비스 레지스트리 서버에 자체 등록하고 다른 종속 마이크로 서비스를 검색할 수 있습니다.

변경 내용이 서버에서 모든 마이크로 서비스로 전파되는 데 몇 분 정도 걸릴 수 있습니다.
::: zone-end
