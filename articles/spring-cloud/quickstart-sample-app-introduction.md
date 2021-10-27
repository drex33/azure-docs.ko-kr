---
title: 빠른 시작 - 샘플 앱 소개 - Azure Spring Cloud
description: Azure Spring Cloud에 배포에 대한 이 빠른 시작 시리즈에 사용되는 샘플 앱을 설명합니다.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/12/2021
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 2966f77bb070269e11a01303204362d3b6950207
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005865"
---
# <a name="introduction-to-the-sample-app"></a>샘플 앱 소개

::: zone pivot="programming-language-csharp"
이 빠른 시작의 시리즈는 두 개의 마이크로서비스로 구성된 샘플 앱을 사용하여 Azure Spring Cloud 서비스에 .NET Core Steeltoe 앱을 배포하는 방법을 보여 줍니다. 서비스 검색, 구성 서버, 로그, 메트릭 및 분산 추적과 같은 Azure Spring Cloud 기능을 사용합니다.

## <a name="functional-services"></a>기능 서비스

샘플 앱은 다음과 같은 두 개의 마이크로서비스로 구성됩니다.

* `planet-weather-provider` 서비스는 행성 이름을 지정하는 HTTP 요청에 대한 응답으로 날씨 텍스트를 반환합니다. 예를 들어 수성 행성에 대해 "매우 따뜻함"을 반환할 수 있습니다. 구성 서버에서 날씨 데이터를 가져옵니다. 구성 서버는 Git 리포지토리의 YAML 파일에서 날씨 데이터를 가져옵니다. 예를 들면 다음과 같습니다.

   ```yaml
   MercuryWeather: very warm
   VenusWeather: quite unpleasant
   MarsWeather: very cool
   SaturnWeather: a little bit sandy
   ```

* `solar-system-weather` 서비스는 HTTP 요청에 대한 응답으로 4개의 행성에 대한 데이터를 반환합니다. `planet-weather-provider`에 대한 4 개의 HTTP 요청을 만들어 데이터를 가져옵니다. Eureka 서버 검색 서비스를 사용하여 `planet-weather-provider`를 호출합니다. JSON을 반환합니다. 예를 들면 다음과 같습니다.

   ```json
   [{
       "Key": "Mercury",
       "Value": "very warm"
   }, {
       "Key": "Venus",
       "Value": "quite unpleasant"
   }, {
       "Key": "Mars",
       "Value": "very cool"
   }, {
       "Key": "Saturn",
       "Value": "a little bit sandy"
   }]
   ```

다음 다이어그램은 샘플 앱 아키텍처를 보여 줍니다.

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="샘플 앱 다이어그램":::

## <a name="code-repository"></a>코드 리포지토리

샘플 앱은 GitHub의 Azure-Samples/Azure-Spring-Cloud-Samples 리포지토리의 [steeltoe-sample](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) 폴더에 있습니다.

다음 빠른 시작의 지침은 필요한 경우 소스 코드를 참조합니다.

::: zone-end

::: zone pivot="programming-language-java"
이 빠른 시작에서는 Azure Spring Cloud 서비스에 앱을 배포하는 방법을 보여 주는 잘 알려진 샘플 앱 [PetClinic](https://github.com/spring-petclinic/spring-petclinic-microservices)의 마이크로 서비스 버전을 사용합니다. **Pet Clinic** 샘플은 마이크로 서비스 아키텍처 패턴을 보여 주고 서비스 내역을 요약합니다. 서비스 검색, 구성 서버, 로그, 메트릭 및 분산 추적 및 개발자 친화적인 지원을 비롯한 Azure Spring Cloud 기능을 제공하는 서비스를 Azure에 배포하는 방법을 확인할 수 있습니다.

Azure Spring Cloud 배포 예제를 따르려면 필요에 따라 제공되는 소스 코드의 위치만 필요합니다.

![PetClinic의 아키텍처](media/build-and-deploy/microservices-architecture-diagram.jpg)

## <a name="functional-services-to-be-deployed"></a>배포할 기능 서비스

PetClinic은 4개의 핵심 마이크로 서비스로 구성되어 있습니다. 이러한 모든 항목은 비즈니스 도메인별로 구성된 독립적으로 배포 가능한 애플리케이션입니다.

* **고객 서비스**: 애완 동물 및 소유자 정보(이름, 주소, 구/군/시, 전화)를 비롯한 일반적인 사용자 입력 논리 및 유효성 검사를 포함합니다.
* **방문 서비스**: 각 애완 동물에 대한 소견이 포함된 방문 정보를 저장하고 표시합니다.
* **수의사 서비스**: 수의사의 이름 및 전문 분야를 비롯한 정보를 저장하고 표시합니다.
* **API 게이트웨이**: API 게이트웨이는 요청을 처리하고 적절한 서비스로 라우트하거나 여러 서비스를 호출하여 결과를 집계하는 데 사용되는 시스템의 단일 진입점입니다.  세 가지 핵심 서비스는 외부 API를 클라이언트에 노출합니다. 실제 시스템에서 함수 수는 시스템 복잡성으로 인해 매우 빠르게 증가할 수 있습니다. 복잡한 웹 페이지 하나를 렌더링하는 데 수 백개의 서비스가 필요할 수 있습니다.

## <a name="infrastructure-services-hosted-by-azure-spring-cloud"></a>Azure Spring Cloud에서 호스트되는 인프라 서비스

분산 시스템에는 핵심 서비스를 지원하는 몇 가지 일반적인 패턴이 있습니다. Azure Spring Cloud는 다음과 같은 패턴을 구현하는 Spring Boot 애플리케이션을 향상시키는 도구를 제공합니다.

* **구성 서비스**: Azure Spring Cloud 구성은 분산 시스템에 대한 수평 확장 가능한 중앙 집중식 구성 서비스입니다. 현재 로컬 스토리지, Git 및 Subversion을 지원하는 플러그형 리포지토리를 사용합니다.
* **서비스 검색**: 자동 크기 조정, 오류 및 업그레이드로 인해 주소가 동적으로 할당될 수 있는 서비스 인스턴스에 대한 네트워크 위치의 자동 검색을 허용합니다.

## <a name="database-configuration"></a>데이터베이스 구성

기본 구성의 **Pet Clinic** 은 시작 시 데이터로 채워지는 메모리 내 데이터베이스(HSQLDB)를 사용합니다. 영구 데이터베이스 구성이 필요한 경우 MySql에 유사한 설정이 제공됩니다. MySQL JDBC 드라이버인 Connector/J에 대한 종속성은 이미 pom.xml 파일에 포함되어 있습니다.

## <a name="sample-usage-of-petclinic"></a>PetClinic의 샘플 사용법

전체 구현에 대한 자세한 내용은 [PetClinic](https://github.com/Azure-Samples/spring-petclinic-microservices)의 포크를 참조하세요. 샘플은 필요한 경우 소스 코드를 참조합니다.

::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Spring Cloud 인스턴스 프로비저닝](./quickstart-provision-service-instance.md)
