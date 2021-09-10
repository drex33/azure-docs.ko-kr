---
title: Azure Spring Cloud 문제 해결 가이드 | Microsoft Docs
description: Azure Spring Cloud 문제 해결 가이드
author: karlerickson
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 98f9a87825a2eb0bbae36255111ba4b019fb4750
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567514"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>일반적인 Azure Spring Cloud 문제 해결

이 문서에서는 Azure Spring Cloud 개발 문제를 해결하기 위한 지침을 제공합니다. 자세한 내용은 [Azure Spring Cloud FAQ](./faq.md)를 참조하세요.

## <a name="availability-performance-and-application-issues"></a>가용성, 성능 및 애플리케이션 문제

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>애플리케이션을 시작할 수 없음(예를 들어, 엔드포인트를 연결할 수 없거나 몇 번 다시 시도한 후에 502를 반환함)

로그를 Azure Log Analytics로 내보냅니다. Spring 애플리케이션 로그에 대한 테이블 이름은 *AppPlatformLogsforSpring* 입니다. 자세히 알아보려면 [진단 설정을 사용하여 로그 및 메트릭 분석](diagnostic-services.md)을 참조하세요.

로그에 다음과 같은 오류 메시지가 나타날 수 있습니다.

> "org.springframework.context.ApplicationContextException: 웹 서버를 시작할 수 없음"

이 메시지는 다음과 같은 두 가지 문제 중 하나를 나타냅니다.

* 빈 중 하나 또는 해당 종속성 중 하나가 누락되었습니다.
* 빈 속성 중 하나가 누락되거나 잘못되었습니다. 이 경우 "java.lang.IllegalArgumentException"이 표시될 가능성이 있습니다.

또한 서비스 바인딩으로 인해 애플리케이션 시작 오류가 발생할 수도 있습니다. 로그를 쿼리하려면 바인딩된 서비스와 관련된 키워드를 사용합니다. 예를 들어, 애플리케이션에 로컬 시스템 시간으로 설정된 MySQL 인스턴스에 대한 바인딩이 있다고 가정합니다. 애플리케이션이 시작되지 않으면 로그에서 다음 오류가 표시될 수 있습니다.

> "java.sql.SQLException: 서버 표준 시간대 값 '협정 세계시'를 인식할 수 없거나 둘 이상의 표준 시간대를 나타냅니다."

이 오류를 해결하려면 MySQL 인스턴스의 `server parameters`로 이동하여 *시스템* 에서 `time_zone` 값을 *+0:00* 으로 변경합니다.

### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>애플리케이션이 충돌하거나 예기치 않은 오류를 throw함

애플리케이션 충돌이 디버그되면 먼저 애플리케이션의 실행 상태와 검색 상태를 확인하여 시작합니다. 이를 수행하려면 Azure Portal에서 _앱 관리_ 로 이동하여 모든 애플리케이션의 상태가 _Running_ 및 _UP_ 인지 확인합니다.

* 상태가 _Running_ 이지만 검색 상태는 _UP_ 이 아닌 경우 ["애플리케이션을 등록할 수 없음"](#my-application-cant-be-registered) 섹션으로 이동합니다.

* 검색 상태가 _UP_ 인 경우 메트릭으로 이동하여 애플리케이션의 상태를 확인합니다. 다음 메트릭을 검사합니다.

   - `TomcatErrorCount`(_tomcat.global.error_):

      모든 Spring 애플리케이션 예외는 여기에서 계산됩니다. 이 수가 크면 Azure Log Analytics로 이동하여 애플리케이션 로그를 검사합니다.

   - `AppMemoryMax`(_jvm.memory.max_):

      애플리케이션에서 사용할 수 있는 최대 메모리 양입니다. 양은 정의되지 않을 수 있습니다. 또는 정의된 경우 시간이 지남에 따라 변경될 수 있습니다. 정의된 경우 사용된 양 및 커밋된 메모리는 언제나 최대보다 작거나 같습니다. 하지만 *used <= max* 가 여전히 true인 경우에도 할당에서 *used > committed* 와 같이 사용되는 메모리를 늘려야 하는 경우 `OutOfMemoryError` 메시지가 표시되며 메모리 할당이 실패할 수 있습니다. 이러한 상황에서 `-Xmx` 매개 변수를 사용하여 최대 힙 크기를 늘려봅니다.

   - `AppMemoryUsed`(_jvm.memory.used_):

      애플리케이션에서 현재 사용하고 있는 메모리의 양(바이트). 정상적인 Java 애플리케이션 로드의 경우 이 메트릭 계열은 *톱니* 패턴으로 구성됩니다. 메모리 사용이 꾸준히 증가하고 조금씩 감소하다가 급격하게 떨어지는 패턴이 반복됩니다. 이 메트릭 계열은 Java 가상 머신 내부의 가비지 수집 때문이며, 수집 작업에서 톱니 패턴의 하강을 나타냅니다.

    이 메트릭은 다음과 같은 메모리 문제를 식별하는 데 중요합니다.

    * 처음에 메모리 급증이 있습니다.
    * 특정 논리 경로에 대해 불안정한 메모리 할당.
    * 점진적인 메모리 누수.

   자세한 내용은 [메트릭](./concept-metrics.md)을 참조하세요.

* 애플리케이션이 시작되지 않으면 애플리케이션에 유효한 jvm 매개 변수가 있는지 확인합니다. jvm 메모리가 너무 높게 설정된 경우 로그에 다음과 같은 오류 메시지가 나타날 수 있습니다.

   > "필수 메모리 2728741K는 할당에 사용할 수 있는 2000M보다 큽니다."

Azure Log Analytics에 대한 자세한 내용은 [Azure Monitor에서 Log Analytics 시작](../azure-monitor/logs/log-analytics-tutorial.md)을 참조하세요.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>애플리케이션에서 높은 CPU 사용 또는 높은 메모리 사용 경험

애플리케이션에서 높은 CPU/메모리 사용량이 발생하는 경우 다음 두 가지 상황 중 하나에 해당합니다.

* 모든 앱 인스턴스에서 높은 CPU/메모리 사용량이 발생합니다.
* 일부 앱 인스턴스에서 높은 CPU/메모리 사용량이 발생합니다.

적용되는 상황을 확인하려면 다음을 수행합니다.

1. **메트릭** 으로 이동한 다음 **서비스 CPU 사용량 비율** 또는 **사용되는 서비스 메모리** 를 선택합니다.
2. **App=** 필터를 추가하여 모니터링할 애플리케이션을 지정합니다.
3. 메트릭을 **인스턴스** 로 분할합니다.

*모든 인스턴스* 에서 높은 CPU/메모리 사용량이 발생하는 경우 애플리케이션을 확장하거나 CPU/메모리 사용량을 확대해야 합니다. 자세한 내용은 [자습서: Azure Spring Cloud에서 애플리케이션 비율 크기 조정](./how-to-scale-manual.md)을 참조하세요.

*일부 인스턴스* 에서 높은 CPU/메모리 사용량이 발생하는 경우 인스턴스 상태 및 해당 검색 상태를 확인합니다.

자세한 내용은 [Azure Spring Cloud의 메트릭](./concept-metrics.md)을 참조하세요.

모든 인스턴스가 실행 중인 경우 Azure Log Analytics로 이동하여 애플리케이션 로그를 쿼리하고 코드 논리를 검토합니다. 이를 통해 비율 크기 조정 분할에 영향을 줄 수 있는지 여부를 확인할 수 있습니다. 자세한 내용은 [진단 설정을 사용하여 로그 및 메트릭 분석](diagnostic-services.md)을 참조하세요.

Azure Log Analytics에 대한 자세한 내용은 [Azure Monitor에서 Log Analytics 시작](../azure-monitor/logs/log-analytics-tutorial.md)을 참조하세요. [Kusto 쿼리 언어](/azure/kusto/query/)를 사용하여 로그를 쿼리합니다.

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Spring 애플리케이션을 Azure Spring Cloud에 배포하기 위한 검사 목록

애플리케이션을 온보드하기 전에 다음 조건을 충족하는지 확인합니다.

* 지정된 Java 런타임 버전을 사용하여 애플리케이션을 로컬로 실행할 수 있습니다.
* 환경 구성(CPU/RAM/인스턴스)에서 애플리케이션 공급자가 설정한 최소 요구 사항을 충족합니다.
* 구성 항목에 해당 예상 값이 있습니다. 자세한 내용은 [Config 서버](./how-to-config-server.md)를 참조하세요.
* 환경 변수에 해당 예상 값이 있습니다.
* JVM 매개 변수에 해당 예상 값이 있습니다.
* 애플리케이션 패키지에서 포함된 _Config Server_ 및 _Spring 서비스 레지스트리_ 서비스를 사용하지 않도록 설정하거나 제거하는 것이 좋습니다.
* Azure 리소스가 _서비스 바인딩_ 을 통해 바인딩될 경우 대상 리소스가 실행 중인지 확인합니다.

## <a name="configuration-and-management"></a>구성 및 관리

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Azure Spring Cloud 서비스 인스턴스를 만드는 동안 문제가 발생했습니다.

Azure Portal을 사용하여 Azure Spring Cloud 서비스 인스턴스를 설정할 때 Azure Spring Cloud에서 유효성 검사를 수행합니다.

하지만 [Azure CLI](/cli/azure/get-started-with-azure-cli) 또는 [Azure Resource Manager 템플릿](../azure-resource-manager/index.yml)을 사용하여 Azure Spring Cloud 서비스 인스턴스를 설정하려는 경우 다음을 확인하세요.

* 구독이 활성 상태입니다.
* Azure Spring Cloud에서 [지원](./faq.md)하는 위치입니다.
* 인스턴스에 대한 리소스 그룹이 이미 만들어졌습니다.
* 리소스 이름이 명명 규칙을 준수합니다. 소문자, 숫자 및 하이픈만 포함해야 합니다. 첫 글자는 문자여야 합니다. 마지막 글자는 문자 또는 숫자여야 합니다. 값은 2~32자를 포함해야 합니다.

Resource Manager 템플릿을 사용하여 Azure Spring Cloud 서비스 인스턴스를 설정하려는 경우 먼저 [Azure Resource Manager 템플릿의 구조 및 구문 이해](../azure-resource-manager/templates/syntax.md)를 참조하세요.

Azure Spring Cloud 서비스 인스턴스의 이름은 `azureapps.io`에 있는 하위 도메인 이름 요청에 사용되므로 이름이 기존 이름과 충돌하는 경우 설정에 실패합니다. 활동 로그에서 추가 정보를 확인할 수 있습니다.

### <a name="i-cant-deploy-a-net-core-app"></a>.NET Core 앱을 배포할 수 없음

Azure Portal 또는 Resource Manager 템플릿을 사용하여 .NET Core Steeltoe 앱에 대한 *.zip* 파일을 업로드할 수 없습니다.

[Azure CLI](/cli/azure/get-started-with-azure-cli)을 사용하여 애플리케이션 패키지를 배포할 때 Azure CLI가 배포 진행률을 주기적으로 진행률을 조사하고 마지막에는 배포 결과를 표시합니다.

애플리케이션이 올바른 *.zip* 파일 형식으로 패키지되어 있는지 확인합니다. 올바르게 패키지되어 있지 않은 경우 프로세스가 응답을 중단하거나 오류 메시지가 표시됩니다.

### <a name="i-cant-deploy-a-jar-package"></a>JAR 패키지를 배포할 수 없음

Azure Portal 또는 Resource Manager 템플릿을 사용하여 Java 아카이브 파일(JAR)/원본 패키지를 업로드할 수 없습니다.

[Azure CLI](/cli/azure/get-started-with-azure-cli)을 사용하여 애플리케이션 패키지를 배포할 때 Azure CLI가 배포 진행률을 주기적으로 진행률을 조사하고 마지막에는 배포 결과를 표시합니다.

폴링이 중단된 경우에도 다음 명령을 사용하여 배포 로그를 계속 가져올 수 있습니다.

`az spring-cloud app show-deploy-log -n <app-name>`

애플리케이션이 올바른 [실행 가능한 JAR 형식](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)으로 패키지되어 있는지 확인합니다. 올바르게 패키지되어 있지 않은 경우 다음과 유사한 오류 메시지가 표시됩니다.

> "오류: 잘못되거나 손상된 jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>원본 패키지를 배포할 수 없음

Azure Portal 또는 Resource Manager 템플릿을 사용하여 JAR/원본 패키지를 업로드할 수 없습니다.

[Azure CLI](/cli/azure/get-started-with-azure-cli)을 사용하여 애플리케이션 패키지를 배포할 때 Azure CLI가 배포 진행률을 주기적으로 진행률을 조사하고 마지막에는 배포 결과를 표시합니다.

폴링이 중단된 경우에도 다음 명령을 사용하여 빌드 및 배포 로그를 계속 가져올 수 있습니다.

`az spring-cloud app show-deploy-log -n <app-name>`

하지만 하나의 Azure Spring Cloud 서비스 인스턴스는 한 번에 하나의 원본 패키지에 대해 하나의 빌드 작업만 트리거할 수 있습니다. 자세한 내용은 [애플리케이션 배포](./quickstart.md) 및 [Azure Spring Cloud에서 스테이징 환경 설정](./how-to-staging-environment.md)을 참조하세요.

### <a name="my-application-cant-be-registered"></a>애플리케이션을 등록할 수 없음

대부분의 경우 이 문제는 *필요한 종속성* 및 *서비스 검색* 이 POM(프로젝트 개체 모델) 파일에 제대로 구성되지 않은 경우 발생합니다. 구성된 후에는 기본 제공 서비스 레지스트리 서버 엔드포인트가 애플리케이션에서 환경 변수로 삽입됩니다. 애플리케이션이 서비스 레지스트리 서버에 자체적으로 등록되고 기타 종속 마이크로 서비스를 검색할 수 있습니다.

새로 등록된 인스턴스가 트래픽을 받기 시작할 때까지 2분 이상 기다립니다.

기존의 Spring Cloud 기반 솔루션을 Azure로 마이그레이션하는 경우 Azure Spring Cloud를 통해 제공되는 관리되는 인스턴스와의 충돌을 피하려면 애드혹 _서비스 레지스트리_ 및 _Config Server_ 인스턴스가 제거(또는 사용하지 않도록 설정)되었는지 확인합니다.

Azure Log Analytics에서 _서비스 레지스트리_ 클라이언트 로그도 확인합니다. 자세한 내용은 [진단 설정을 사용하여 로그 및 메트릭 분석](diagnostic-services.md) 참조

Azure Log Analytics에 대한 자세한 내용은 [Azure Monitor에서 Log Analytics 시작](../azure-monitor/logs/log-analytics-tutorial.md)을 참조하세요. [Kusto 쿼리 언어](/azure/kusto/query/)를 사용하여 로그를 쿼리합니다.

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>내 애플리케이션의 환경 변수를 검사하려고 합니다.

환경 변수는 Azure에서 애플리케이션을 형성하는 서비스를 구성하는 위치와 방법을 이해할 수 있도록 Azure Spring Cloud 프레임워크에 정보를 알려 줍니다. 환경 변수가 올바른지 확인하는 것은 잠재적인 문제 해결에 필수적인 첫 번째 단계입니다.  Spring Boot Actuator 엔드포인트를 사용하여 환경 변수를 검토할 수 있습니다.

> [!WARNING]
> 이 프로시저는 테스트 엔드포인트를 사용하여 환경 변수를 노출합니다.  테스트 엔드포인트에 공개적으로 액세스할 수 있거나 애플리케이션에 도메인 이름을 할당한 경우에는 더이상 진행하지 마십시오.

1. `https://<your application test endpoint>/actuator/health` 으로 이동합니다.

    - `{"status":"UP"}`과 유사한 응답은 엔드포인트가 사용하도록 설정되었음을 나타냅니다.
    - 응답이 부정이면 *POM.xml* 파일에 다음 종속성을 포함합니다.

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Spring Boot Actuator 엔드포인트가 사용하도록 설정된 상태에서 Azure Portal로 이동하여 애플리케이션의 구성 페이지를 찾습니다.  이름이 `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`이고 값이 `*`인 환경 변수를 추가합니다.

1. 애플리케이션을 다시 시작합니다.

1. `https://<your application test endpoint>/actuator/env`로 이동하여 응답을 검사합니다.  다음과 같이 표시됩니다.

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

`systemEnvironment`라는 자식 노드를 찾습니다.  이 노드에는 애플리케이션의 환경 변수가 포함됩니다.

> [!IMPORTANT]
> 애플리케이션에 대한 공용 액세스를 가능하게 하려면 환경 변수의 노출을 역방향으로 바꾸어야 합니다.  Azure Portal로 이동하여 애플리케이션의 구성 페이지를 찾은 후, 환경 변수 `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`를 삭제합니다.

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>애플리케이션에 대한 메트릭 또는 로그를 찾을 수 없음

**앱 관리** 로 이동하여 애플리케이션 상태가 _Running_ 및 _UP_ 인지 확인합니다.

애플리케이션 패키지에서 _JMX_ 를 사용하도록 설정되어 있는지 확인합니다. 이 기능은 구성 속성 `spring.jmx.enabled=true`를 사용하여 설정할 수 있습니다.

애플리케이션 패키지에서 `spring-boot-actuator` 종속성을 사용하도록 설정하고 성공적으로 부팅되었는지 확인합니다.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

애플리케이션 로그를 스토리지 계정에 보관할 수 있지만 Azure Log Analytics에 보내지 않은 경우 [작업 영역을 올바르게 설정](../azure-monitor/logs/quick-create-workspace.md)했는지 확인합니다. Azure Log Analytics의 무료 계층을 사용하는 경우 [무료 계층은 SLA(서비스 수준 계약)를 제공하지 않습니다](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).

## <a name="next-steps"></a>다음 단계

* [Azure Spring Cloud의 자체 진단 및 문제 해결 방법](./how-to-self-diagnose-solve.md)
