---
title: Azure Spring Cloud에 대한 자주 묻는 질문 | Microsoft Docs
description: 이 문서는 Azure Spring Cloud에 관해 자주 묻는 질문에 대한 답변입니다.
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 79ff649d9710d4f114b7d8de85d275896f26a729
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003564"
---
# <a name="azure-spring-cloud-faq"></a>Azure Spring Cloud FAQ

이 문서는 Azure Spring Cloud에 관해 자주 묻는 질문에 대한 답변입니다.

## <a name="general"></a>일반

### <a name="why-azure-spring-cloud"></a>Azure Spring Cloud가 적합한 이유는?

Azure Spring Cloud는 Spring Cloud 개발자를 위한 PaaS(Platform as a Service)를 제공합니다. Azure Spring Cloud는 애플리케이션 인프라를 관리하므로 애플리케이션 코드 및 비즈니스 논리에 집중할 수 있습니다. Azure Spring Cloud에 기본 제공되는 핵심 기능에는 Eureka, Config 서버, 서비스 레지스트리 서버, Pivotal 빌드 서비스, 파란색-녹색 배포 등이 있습니다. 또한 개발자는 이 서비스를 사용하여 자신의 애플리케이션을 다른 Azure 서비스(예: Azure Cosmos DB, Azure Database for MySQL 및 Azure Cache for Redis)에 바인딩할 수 있습니다.

Azure Spring Cloud는 Azure Monitor, Application Insights 및 로그 분석을 통합하여 개발자와 운영자의 애플리케이션 진단 환경을 향상시킵니다.

### <a name="how-secure-is-azure-spring-cloud"></a>Azure Spring Cloud는 얼마나 안전한가요?

보안 및 개인 정보 보호는 Azure 및 Azure Spring Cloud 고객에게 가장 높은 우선 순위 중 하나입니다. Azure는 이 모든 데이터를 안전하게 암호화하여 고객만 애플리케이션 데이터, 로그 또는 구성에 액세스할 수 있도록 합니다.

* Azure Spring Cloud의 서비스 인스턴스는 서로 격리되어 있습니다.
* Azure Spring Cloud는 완전한 TLS/SSL 및 인증서 관리를 제공합니다.
* OpenJDK 및 Spring Cloud 런타임에 대한 중요 보안 패치는 가능한 한 빨리 Azure Spring Cloud에 적용됩니다.

### <a name="how-does-azure-spring-cloud-host-my-applications"></a>내 애플리케이션을 호스트하는 Azure Spring Cloud 어떻게 할까요?

Azure Spring Cloud 각 서비스 인스턴스는 여러 작업자 노드가 있는 완전 전용 Kubernetes 클러스터에서 지원됩니다. Azure Spring Cloud 고가용성, 확장성, Kubernetes 버전 업그레이드 등을 포함하여 기본 Kubernetes 클러스터를 관리합니다.

Azure Spring Cloud 기본 Kubernetes 작업자 노드에서 애플리케이션을 지능적으로 예약합니다. 고가용성을 제공하기 위해 Azure Spring Cloud 서로 다른 노드에 2개 이상의 인스턴스가 있는 애플리케이션을 배포합니다.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>Azure Spring Cloud는 어떤 지역에서 사용할 수 있나요?

미국 동부, 미국 동부 2, 미국 중부, 미국 중남부, 미국 중북부, 미국 서부, 미국 서부 2, 서유럽, 북유럽, 영국 남부, 동남아시아, 오스트레일리아 동부, 캐나다 중부, 아랍에미리트 북부, 인도 중부, 한국 중부, 동아시아, 일본 동부, 남아프리카 공화국 북부 및 중국 동부 2(Mooncake). [자세한 내용](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud)

### <a name="is-any-customer-data-stored-outside-of-the-specified-region"></a>지정된 지역 외부에 저장된 고객 데이터가 있나요?

Azure Spring Cloud는 지역 서비스입니다. Azure Spring Cloud의 모든 고객 데이터는 중복도를 위해 지정된 지역의 동일한 지역 내의 여러 지역에 저장됩니다. 지리적 위치 및 지역에 대한 자세한 내용은 [Azure의 데이터 보존](https://azure.microsoft.com/global-infrastructure/data-residency/)을 참조하세요.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Azure Spring Cloud의 알려진 제한 사항은 어떻게 되나요?

Azure Spring Cloud에는 다음과 같이 알려진 제한 사항이 있습니다.

* `spring.application.name`은 각 애플리케이션을 만드는 데 사용되는 애플리케이션 이름으로 재정의됩니다.
* `server.port`의 기본값은 포트 1025입니다. 다른 값이 적용되면 재정의됩니다. 또한 이 설정을 준수하고 코드에서 서버 포트를 지정하지 마십시오.
* Azure Portal, Azure Resource Manager 템플릿 및 Terraform은 애플리케이션 패키지 업로드를 지원하지 않습니다. Azure CLI, Azure DevOps, Azure Spirng Cloud용 Maven 플러그 인, IntelliJ용 Azure Toolkit 및 Azure Spring Cloud Visual Studio Code 확장을 사용하여 애플리케이션 패키지를 업로드할 수 있습니다.

### <a name="what-pricing-tiers-are-available"></a>어떤 가격 책정 계층을 사용할 수 있나요?

어떤 것을 사용해야 하며 각 계층의 제한은 무엇인가요?

* Azure Spring Cloud는 기본 및 표준의 두 가지 가격 책정 계층을 제공합니다. 기본 계층은 개발/테스트 및 Azure Spring Cloud 사용을 대상으로 합니다. 표준 계층은 범용 프로덕션 트래픽을 실행하도록 최적화되어 있습니다. 제한 및 기능 수준 비교에 대한 자세한 내용은 [Azure Spring Cloud 가격 책정 정보](https://azure.microsoft.com/pricing/details/spring-cloud/)를 참조하세요.

### <a name="whats-the-difference-between-service-binding-and-service-connector"></a>서비스 바인딩과 서비스 커넥터의 차이점은 무엇인가요?
Service Connector라는 새로운 Azure 전체 솔루션을 위해 서비스 바인딩에 대한 추가 기능을 적극적으로 개발하지 않습니다. 한편, 새 솔루션은 azure의 앱 호스팅 서비스(예: App Service)에서 일관된 통합 환경을 제공합니다. 반면, MySQL, SQL DB, Cosmos DB, Postgres DB, Redis, Storage 등 가장 많이 사용되는 10개 이상의 대상 Azure 서비스를 지원하여 요구 사항을 더 잘 처리합니다. 서비스 커넥터는 현재 공개 미리 보기로 제공됩니다. 새로운 환경을 사용해 보시기 위해 초대합니다.

### <a name="how-can-i-provide-feedback-and-report-issues"></a>피드백 및 보고서 문제를 제출하려면 어떻게 해야 하나요?

Azure Spring Cloud에 문제가 발생하면 [Azure 지원 요청](../azure-portal/supportability/how-to-create-azure-support-request.md)을 만드세요. 기능 요청을 제출하거나 피드백을 제공하려면 [Azure 피드백](https://feedback.azure.com/forums/34192--general-feedback)으로 이동하세요.

## <a name="development"></a>개발

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>저는 Azure를 처음 접하는 Spring Cloud 개발자입니다. Azure Spring Cloud 애플리케이션을 개발하는 방법을 배우는 가장 빠른 방법은 무엇인가요?

Azure Spring Cloud를 시작하는 가장 빠른 방법은 [빠른 시작: Azure Portal을 사용하여 Azure Spring Cloud 애플리케이션 시작](./quickstart.md)의 지침을 따르세요.

::: zone pivot="programming-language-java"
### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Azure Spring Cloud에서 지원하는 Java 런타임은 어떻게 되나요?

Azure Spring Cloud는 Java 8 및 11을 지원합니다. [Java 런타임 및 OS 버전을](#java-runtime-and-os-versions) 참조

### <a name="is-spring-boot-24x-supported"></a>Spring Boot 2.4. x가 지원되나요?
Spring Boot 2.4의 문제를 확인했으며 현재 Spring 커뮤니티와 협력하여 문제를 해결하고 있습니다. 그동안에는 이러한 두 가지 종속성을 포함하여 앱과 Eureka 간에 TLS 인증을 사용하도록 설정하세요.

```xml
<dependency>
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-client</artifactId>
    <version>1.19.4</version>
</dependency>
<dependency>
    <groupId>com.sun.jersey.contribs</groupId>
    <artifactId>jersey-apache-client4</artifactId>
    <version>1.19.4</version>
</dependency>
```

::: zone-end

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Spring Cloud 애플리케이션 로그 및 메트릭은 어디에서 확인할 수 있나요?

메트릭은 [앱 개요] 탭 및 [Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md#metrics-explorer) 탭에서 찾습니다.

Azure Spring Cloud는 Spring Cloud 애플리케이션 로그 및 메트릭을 Azure Storage, EventHub 및 [로그 분석](../azure-monitor/logs/data-platform-logs.md)으로 내보내는 기능을 지원합니다. 로그 분석의 테이블 이름은 *AppPlatformLogsforSpring* 입니다. 사용하도록 설정하는 방법을 알아보려면 [진단 서비스](diagnostic-services.md)를 참조하세요.

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud는 분산 추적을 지원하나요?

예. 자세한 내용은 [자습서: Azure Spring Cloud에서 분산 추적 사용](./how-to-distributed-tracing.md)을 참조하세요.

::: zone pivot="programming-language-java"
### <a name="what-resource-types-does-service-binding-support"></a>서비스 바인딩에서 지원하는 리소스 종류는 무엇인가요?

현재 지원되는 세 가지 서비스는

* Azure Cosmos DB
* Azure Database for MySQL
* Azure Cache for Redis
::: zone-end

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>애플리케이션 내부에서 영구 볼륨을 보거나 추가하거나 이동할 수 있나요?

예.

### <a name="how-many-outbound-public-ip-addresses-does-an-azure-spring-cloud-instance-have"></a>Azure Spring Cloud 인스턴스에는 몇 개의 아웃바운드 공용 IP 주소가 있나요?

아웃바운드 공용 IP 주소의 수는 계층 및 기타 요인에 따라 다를 수 있습니다.

| Azure Spring Cloud 인스턴스 형식 | 기본 아웃바운드 공용 IP 주소 수 |
| -------------------------------- | ---------------------------------------------- |
| 기본 계층 인스턴스             | 1                                              |
| 표준 계층 인스턴스          | 2                                              |
| VNet 삽입 인스턴스         | 1                                              |

### <a name="can-i-increase-the-number-of-outbound-public-ip-addresses"></a>아웃바운드 공용 IP 주소의 수를 늘릴 수 있나요?

예, 추가 아웃바운드 공용 IP 주소를 요청하는 [지원 티켓](https://azure.microsoft.com/support/faq/)을 열 수 있습니다.

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Azure Spring Cloud 서비스 인스턴스를 삭제/이동하면 확장 리소스도 삭제/이동되나요?

확장 리소스를 소유하는 리소스 공급자의 논리에 따라 다릅니다. `Microsoft.AppPlatform` 인스턴스의 확장 리소스는 동일한 네임스페이스에 속하지 않으므로 동작은 리소스 제공자에 따라 다릅니다. 예를 들어, 삭제/이동 작업은 **진단 설정** 리소스에 적용되지 않습니다. 새 Azure Spring Cloud 인스턴스가 삭제된 것과 동일한 리소스 ID로 프로비전되거나 이전 Azure Spring Cloud 인스턴스가 다시 이동되는 경우 이전 **진단 설정** 리소스는 계속 확장합니다.

Azure CLI를 사용하여 Spring Cloud의 진단 설정을 삭제할 수 있습니다.

```azurecli
 az monitor diagnostic-settings delete --name $diagnosticSettingName --resource $azureSpringCloudResourceId
```

::: zone pivot="programming-language-java"
## <a name="java-runtime-and-os-versions"></a>Java 런타임 및 OS 버전

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>Azure Spring Cloud에서 지원되는 Java 런타임 버전은 무엇인가요?

Azure Spring Cloud는 최신 빌드의 Java LTS 버전이 지원됩니다. 2020년 6월 현재, Java 8 및 Java 11이 지원됩니다. [Azure 및 Azure Stack용 JDK 설치](/azure/developer/java/fundamentals/java-jdk-install) 참조

### <a name="who-built-these-java-runtimes"></a>이러한 Java 런타임은 누가 빌드했나요?

Azul 시스템 Azure용 Azul Zulu - 엔터프라이즈 버전 JDK 빌드는 Microsoft와 Azul Systems가 후원하는 Azure 및 Azure Stack에 대한 OpenJDK의 무료 다중 플랫폼 프로덕션 준비 배포입니다. 여기에는 Java SE 애플리케이션을 빌드하고 실행하기 위한 모든 구성 요소가 포함됩니다.

### <a name="how-often-will-java-runtimes-get-updated"></a>Java 런타임은 얼마나 자주 업데이트되나요?

LTS 및 MTS JDK 릴리스에는 필요에 따라 분기별 보안 업데이트, 버그 수정 및 중요한 대역 외 업데이트 및 패치가 포함됩니다. 이 지원에는 보안 업데이트의 Java 7 및 8에 대한 백포트 및 Java 11과 같은 최신 버전의 Java에서 보고된 버그 수정이 포함됩니다.

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>Java 8 및 Java 11 LTS 버전은 얼마 동안 지원되나요?

[Azure 및 Azure Stack에 대한 Java 장기 지원](/azure/developer/java/fundamentals/java-support-on-azure) 참조.

* Java 8 LTS는 2030년 12월까지 지원될 예정입니다.
* Java 11 LTS는 2027년 9월까지 지원될 예정입니다.

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>로컬 개발을 위해 지원되는 Java 런타임을 다운로드하려면 어떻게 해야 하나요?

[Azure 및 Azure Stack용 JDK 설치](/azure/developer/java/fundamentals/java-jdk-install) 참조.

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>이전 Java 런타임에 대한 사용 중지 정책은 무엇인가요?

공개 알림은 이전 런타임 버전이 사용 중지되기 12개월 후에 전송됩니다. 12개월 이내에 최신 버전으로 마이그레이션해야 합니다.

* 구독 관리자는 Java 버전이 사용 중지되는 경우 이메일 알림을 받습니다.
* 사용 중단 정보는 설명서에 게시됩니다.

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>Java 런타임 수준에서 문제에 대한 지원을 받으려면 어떻게 해야 하나요?

Azure 지원을 통해 지원 티켓을 열 수 있습니다.  [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md) 참조.

### <a name="what-is-the-operation-system-to-run-my-apps"></a>애플리케이션을 실행하는 운영 체제는 무엇인가요?

최신 Ubuntu LTS 버전이 사용되며, 현재 [Ubuntu 20.04 LTS (Focal Fossa)](https://releases.ubuntu.com/focal/)가 기본 OS입니다.

### <a name="how-often-are-os-security-patches-applied"></a>OS 보안 패치는 얼마나 자주 적용되나요?

Azure Spring Cloud에 적용되는 보안 패치는 매월 프로덕션에 배포됩니다.
Azure Spring Cloud에 적용 가능한 중요 보안 패치(CVE 점수> = 9)는 가능한 한 빨리 출시됩니다.
::: zone-end

## <a name="deployment"></a>배포

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring Cloud에서 파란색-녹색 배포를 지원하나요?

예. 자세한 정보는 [스테이징 환경 설정](./how-to-staging-environment.md)을 참조하세요.

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>내 애플리케이션 컨테이너를 조작하기 위해 Kubernetes에 액세스할 수 있나요?

아니요.  Azure Spring Cloud는 기본 아키텍처에서 개발자를 추상화하여 애플리케이션 코드와 비즈니스 논리에 집중할 수 있도록 합니다.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud는 원본에서 컨테이너를 빌드할 수 있도록 지원하나요?

예. 자세한 내용은 [소스 코드에서 Spring Cloud 애플리케이션 시작](./quickstart.md)을 참조하세요.

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud에서 앱 인스턴스의 자동 크기 조정을 지원하나요?

예. 자세한 내용은 [마이크로 서비스 애플리케이션에 대한 자동 크기 조정 설정을 참조하세요.](./how-to-setup-autoscale.md)

### <a name="how-does-azure-spring-cloud-monitor-the-health-status-of-my-application"></a>Azure Spring Cloud 애플리케이션의 상태를 모니터링하는 방법

Azure Spring Cloud 고객의 애플리케이션에 대한 포트 1025를 지속적으로 검색합니다. 이러한 프로브는 애플리케이션 컨테이너가 트래픽 허용을 시작할 준비가 되었는지 여부와 Azure Spring Cloud 애플리케이션 컨테이너를 다시 시작해야 하는지 여부를 결정합니다. 내부적으로 Azure Spring Cloud Kubernetes 사용량 및 준비 상태 프로브를 사용하여 상태 모니터링을 달성합니다.

>[!NOTE]
> 이러한 프로브로 인해 현재 포트 1025를 노출하지 않고는 Azure Spring Cloud 애플리케이션을 시작할 수 없습니다.

### <a name="whether-and-when-will-my-application-be-restarted"></a>내 애플리케이션을 다시 시작할지 여부와 시기는 언제인가요?

예. 자세한 내용은 [Azure 활동 로그를 사용하여 앱 수명 주기 이벤트 모니터링 및 Azure Service Health.](./monitor-app-lifecycle-events.md)

::: zone pivot="programming-language-java"
### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>기존 Spring Cloud 마이크로서비스를 Azure Spring Cloud로 마이그레이션하기 위한 모범 사례는 무엇인가요?

자세한 내용은 [Azure Spring Cloud Spring Cloud 애플리케이션 마이그레이션을](/azure/developer/java/migration/migrate-spring-cloud-to-azure-spring-cloud)참조하세요.
::: zone-end

::: zone pivot="programming-language-csharp"
## <a name="net-core-versions"></a>.NET Core 버전

### <a name="which-net-core-versions-are-supported"></a>지원되는 .NET Core 버전은 무엇인가요?

.NET Core 3.1 이상 버전

### <a name="how-long-will-net-core-31-be-supported"></a>.NET Core 3.1은 얼마나 오래 지원되나요?

2022년 12월 3일까지 지원됩니다. [.NET Core 지원 정책](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) 참조.
::: zone-end

## <a name="troubleshooting"></a>문제 해결

### <a name="what-are-the-impacts-of-service-registry-rarely-unavailable"></a>거의 사용할 수 없는 서비스 레지스트리의 영향은 무엇인가요?

드물게 발생하는 일부 시나리오에서는 애플리케이션 로그에서 다음과 같은 오류가 표시될 수 있습니다.

```output
RetryableEurekaHttpClient: Request execution failure with status code 401; retrying on another server if available
```

이 문제는 네트워크 불안정 또는 기타 네트워크 문제로 인한 매우 낮은 속도로 스프링 프레임워크에서 도입되었습니다.

사용자 환경에 영향을 주지 않아야 합니다. eureka 클라이언트에서는 하트비트 및 재시도 정책을 모두 포함하여 이 작업을 처리합니다. 일시적인 오류로 간주하고 안전하게 건너뛸 수 있습니다.

이 부분을 개선하고 곧 사용자의 애플리케이션에서 이 오류를 방지할 것입니다.

## <a name="next-steps"></a>다음 단계

추가 질문이 있는 경우 [Azure Spring Cloud 문제 해결 가이드](./troubleshoot.md)를 참조하세요.
