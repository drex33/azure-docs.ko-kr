---
title: Azure Spring Cloud 소개
description: Azure에서 Java Spring 애플리케이션을 배포 및 관리하는 Azure Spring Cloud의 기능 및 이점을 알아봅니다.
author: karlerickson
ms.service: spring-cloud
ms.topic: overview
ms.date: 03/09/2021
ms.author: karler
ms.custom: devx-track-java, contperf-fy21q2
customer intent: As an Azure Cloud user, I want to deploy, run, and monitor Spring Boot microservices.
ms.openlocfilehash: 8720a24e975ce7a73ed49f92460de301b5c8dfd8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485682"
---
# <a name="what-is-azure-spring-cloud"></a>Azure Spring Cloud란?

Azure Spring Cloud를 사용하면 코드 변경 없이 Spring Boot 마이크로서비스 애플리케이션을 Azure에 쉽게 배포할 수 있습니다.  이 서비스는 개발자가 코드에 집중할 수 있도록 Spring Cloud 애플리케이션의 인프라를 관리합니다.  Azure Spring Cloud는 포괄적인 모니터링 및 진단, 구성 관리, 서비스 검색, CI/CD 통합, 청록색 배포 등을 사용하여 수명 주기 관리를 제공합니다.

다음 동영상은 Azure Spring Cloud를 사용하여 Azure에서 실행되는 Spring Boot 애플리케이션으로 구성된 앱을 보여 줍니다.

<br>

> [!VIDEO https://www.youtube.com/embed/1jOXMFc1oRg]

## <a name="why-use-azure-spring-cloud"></a>Azure Spring Cloud를 사용하는 이유는?

Azure Spring Cloud에 애플리케이션을 배포하면 많은 이점이 있습니다. 다음과 같습니다.

* 기존 Spring 앱을 효율적으로 마이그레이션하고 클라우드 크기 조정 및 비용을 관리합니다.
* Spring Cloud 패턴으로 애플리케이션을 현대화하여 민첩성 및 전달 속도를 개선합니다.
* 클라우드 규모로 Java를 실행하고 복잡한 인프라 없이 더 높은 사용량을 구동합니다.
* 컨테이너화 종속성 없이 빠르게 개발하고 배포합니다.
* 프로덕션 워크로드를 효율적이고 손쉽게 모니터링합니다.

Azure Spring Cloud는 Java [Spring Boot](https://spring.io/projects/spring-boot) 및 ASP.NET Core [Steeltoe](https://steeltoe.io/) 앱을 모두 지원합니다. Steeltoe 지원은 현재 공개 미리 보기로 제공됩니다. 공개 미리 보기 제품을 통해 공식 릴리스 전에 새로운 기능을 시험해 볼 수 있습니다.

## <a name="service-overview"></a>서비스 개요

Azure 에코시스템의 일부인 Azure Spring Cloud를 사용하면 스토리지, 데이터베이스, 모니터링 등을 비롯한 다른 Azure 서비스에 쉽게 바인딩할 수 있습니다.

![Azure Spring Cloud 개요](media/spring-cloud-principles/azure-spring-cloud-overview.png)

* Azure Spring Cloud는 인프라를 관리할 필요 없이 앱을 빌드 및 실행하는 데 집중할 수 있도록 하는 Spring Boot 앱을 위한 완전 관리형 서비스입니다.

* Spring Boot 앱을 위한 JAR 또는 코드나 Steeltoe 앱을 위한 Zip을 배포하기만 하면 Azure Spring Cloud가 자동으로 앱을 Spring 서비스 런타임 및 기본 제공 앱 수명 주기와 연결합니다.

* 모니터링은 간단합니다. 배포 후 애플리케이션 성능을 모니터링하고, 오류를 수정하고, 애플리케이션을 신속하게 개선할 수 있습니다.

* Azure의 에코시스템 및 서비스에 완벽하게 통합됩니다.

* Azure Spring Cloud는 완전 관리형 인프라, 기본 제공 수명 주기 관리 및 간편한 모니터링을 통해 엔터프라이즈로 준비됩니다.

## <a name="documentation-overview"></a>설명서 개요

이 설명서에는 Azure Spring Cloud 서비스를 시작하고 활용하는 방법을 설명하는 섹션이 포함되어 있습니다.

* 시작하기

    * [첫 번째 앱 시작](./quickstart.md)
    * [Azure Spring Cloud 서비스 프로비저닝](./quickstart-provision-service-instance.md)
    * [구성 서버 설정]()
    * [앱 빌드 및 배포](./quickstart-deploy-apps.md)
    * [로그 메트릭 및 추적 사용](./quickstart-logs-metrics-tracing.md)

* 방법

    * [개발](how-to-prepare-app-deployment.md): Azure Spring Cloud에 배포할 기존 Java Spring 애플리케이션을 준비합니다. 제대로 구성되면 Azure Spring Cloud는 Java Spring Cloud 애플리케이션을 모니터링하고, 크기를 조정하고, 업데이트할 수 있는 강력한 서비스를 제공합니다.
    * [배포](./how-to-staging-environment.md): Azure Spring Cloud에서 청록색 배포 패턴을 사용하여 스테이징 배포를 설정하는 방법을 보여줍니다. 파란색/녹색 배포는 새로운(녹색) 버전이 배포되는 동안 기존(파란색) 버전을 유지하도록 하는 Azure DevOps 지속적인 업데이트 패턴입니다.
    * [앱 구성](./how-to-start-stop-delete.md): Azure Spring Cloud의 애플리케이션을 시작, 중지 및 삭제합니다. Azure Portal 또는 Azure CLI를 사용하여 Azure Spring Cloud에서 애플리케이션의 상태를 변경합니다.
    * [크기 조정](./how-to-scale-manual.md): Azure Portal에서 Azure Spring Cloud 대시보드를 사용하거나 자동 크기 조정 설정을 사용하여 모든 마이크로서비스 애플리케이션의 크기를 조정하는 방법을 보여줍니다. 공용 IP는 데이터베이스, 스토리지 및 키 자격 증명 모음과 같은 외부 리소스와 통신하는 데 사용할 수 있습니다.
    * [앱 모니터링](./how-to-distributed-tracing.md): 복잡한 문제를 쉽게 디버깅하고 모니터링할 수 있는 분산 추적 도구입니다. Azure Spring Cloud는 Spring Cloud Sleuth를 Azure의 Application Insights와 통합합니다. 이러한 통합은 Azure Portal의 강력한 분산 추적 기능을 제공합니다.
    * [보안 앱](./how-to-enable-system-assigned-managed-identity.md): Azure 리소스는 Azure Active Directory에서 자동으로 관리 ID를 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다.
    * [다른 Azure 서비스와 통합](./how-to-bind-cosmos.md): Spring Boot 애플리케이션을 수동으로 구성하는 대신 선택한 Azure 서비스를 애플리케이션에 자동으로 바인딩할 수 있습니다(예: 애플리케이션을 Azure Cosmos DB에 바인딩).
    * [자동화](./how-to-cicd.md): 연속 통합 및 지속적인 업데이트 도구를 사용하면 최소한의 노력과 위험으로 기존 애플리케이션에 업데이트를 신속하게 배포할 수 있습니다. Azure DevOps는 이러한 주요 작업을 구성하고 제어하는 데 도움이 됩니다.
    * [문제 해결](./how-to-self-diagnose-solve.md): Azure Spring Cloud 진단은 앱 문제를 해결하는 데 유용한 대화형 환경을 제공합니다. 구성이 필요하지 않습니다. 문제를 발견하면 Azure Spring Cloud 진단에서 문제를 식별하고 문제를 해결하는 데 도움이 되는 정보로 안내합니다.
    * [마이그레이션](/azure/developer/java/migration/migrate-spring-boot-to-azure-spring-cloud): Azure Spring Cloud에서 실행되도록 기존 Spring Cloud 애플리케이션 또는 Spring Boot 애플리케이션을 마이그레이션하는 방법입니다.

시작하려면 다음을 참조하십시오.

> [!div class="nextstepaction"]
> [Spring Cloud 빠른 시작](./quickstart.md)

샘플은 GitHub에서 사용할 수 있습니다. [Azure Spring Cloud 샘플](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).
