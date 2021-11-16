---
title: Spring Boot 앱에서 동적 구성 사용
titleSuffix: Azure App Configuration
description: Spring Boot 앱에 대한 구성 데이터를 동적으로 업데이트하는 방법을 알아봅니다.
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 34225576d435f9ff275f74372afa2cc9f7d50b9f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287251"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>자습서: Java Spring 앱에서 동적 구성 사용

앱 구성에는 Spring을 위한 두 개의 라이브러리가 있습니다. `azure-spring-cloud-appconfiguration-config`에는 Spring Boot가 필요하며 `spring-cloud-context`에 종속되어 있습니다. `azure-spring-cloud-appconfiguration-config-web`에는 Spring Boot.와 함께 Spring 웹이 필요합니다. 두 라이브러리는 새로 고침된 구성 값을 확인하는 수동 트리거를 지원합니다. `azure-spring-cloud-appconfiguration-config-web`은 구성 새로 고침의 자동 확인을 위한 지원도 추가합니다.

새로 고침을 사용하면 애플리케이션을 다시 시작하지 않고도 구성 값을 새로 고칠 수 있지만 `@RefreshScope`의 모든 빈이 다시 생성될 수 있습니다. 클라이언트 라이브러리는 현재 로드된 구성의 해시 ID를 캐시하여 구성 저장소에 대해 너무 많은 호출을 방지합니다. 캐시된 값이 만료될 때까지 새로 고침 작업은 구성 저장소에서 값이 변경된 경우에도 값을 업데이트하지 않습니다. 각 요청에 대한 기본 만료 시간은 30초입니다. 이는 필요한 경우 재정의할 수 있습니다.

`azure-spring-cloud-appconfiguration-config-web`의 자동 새로 고침은 작업(특히 스프링 웹의 `ServletRequestHandledEvent`)에 따라 트리거됩니다. `ServletRequestHandledEvent`가 트리거되지 않은 경우 `azure-spring-cloud-appconfiguration-config-web`의 자동 새로 고침은 캐시 만료 시간이 만료된 경우에도 새로 고침을 트리거하지 않습니다.

## <a name="use-manual-refresh"></a>수동 새로 고침 사용

앱 구성은 캐시가 만료되었는지 여부를 확인하는 데 사용할 수 있는 `AppConfigurationRefresh`가 노출되어 새로 고침을 트리거합니다.

```java
import com.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

`AppConfigurationRefresh`의 `refreshConfigurations()`는 새로 고침이 트리거된 경우 true이고, 그렇지 않은 경우 false인 `Future`를 반환합니다. False는 캐시 만료 시간이 만료되지 않았거나, 변경 내용이 없거나, 다른 스레드가 현재 새로 고침을 확인하고 있음을 의미합니다.

## <a name="use-automated-refresh"></a>자동화된 새로 고침 사용

자동화된 새로 고침을 사용하려면 [App Configuration에 대한 Spring Boot 빠른 시작](quickstart-java-spring-app.md)을 따라 만든 앱과 같은 App Configuration을 사용하는 Spring Boot 앱부터 시작합니다.

그런 다음, 텍스트 편집기에서 *pom.xml* 파일을 열고 다음 코드를 사용하여 `azure-spring-cloud-appconfiguration-config-web`에 대해 `<dependency>`를 추가합니다.

**Spring Boot**

```xml
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
    <version>2.0.0</version>
</dependency>
```

> [!NOTE]
> 이전 종속성에 대한 지원이 필요한 경우 [이전 라이브러리](https://github.com/Azure/azure-sdk-for-java/blob/spring-cloud-starter-azure-appconfiguration-config_1.2.9/sdk/appconfiguration/spring-cloud-starter-azure-appconfiguration-config/README.md)를 참조하세요.

1. 새로 고침을 사용하도록 `bootstrap.properties` 업데이트

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled=true
    spring.cloud.azure.appconfiguration.stores[0].monitoring.triggers[0].key=sentinel
    ```

1. **Azure Portal** 을 열고 애플리케이션과 연결된 앱 구성 리소스로 이동합니다. **작업** 아래에서 **구성 탐색기** 를 선택하고 + 키-값 **+ 만들기** > **키-값** 을 선택한 후 다음 매개 변수를 추가하여 새 키-값 쌍을 만듭니다.

    | 키 | 값 |
    |---|---|
    | Sentinel | 1 |

    지금은 **레이블** 과 **콘텐츠 형식** 을 비워 두세요.

1. **적용** 을 선택합니다.

1. Maven을 사용하여 Spring Boot 애플리케이션을 빌드하고 실행합니다.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. 새 브라우저 창을 열고 URL(`http://localhost:8080`)로 이동합니다.  키와 연결된 메시지가 표시됩니다.

    *curl* 을 사용하여 애플리케이션을 테스트할 수도 있습니다. 예를 들면 다음과 같습니다.

    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. 동적 구성을 테스트하려면 애플리케이션과 연결된 Azure App Configuration 포털을 엽니다. **구성 탐색기** 를 선택하고 표시되는 키의 값을 업데이트합니다. 예를 들면 다음과 같습니다.

    | 키 | 값 |
    |---|---|
    | /application/config.message | Hello - 업데이트됨 |

1. 이전에 만든 sentinel 키를 새 값으로 업데이트합니다. 이 변경으로 인해 새로 고침 간격이 지나면 애플리케이션에서 모든 구성 키를 새로 고치도록 트리거합니다.

    | 키 | 값 |
    |---|---|
    | Sentinel | 2 |

1. 표시되는 새 메시지를 확인하려면 브라우저 페이지를 새로 고칩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Spring Boot 앱을 사용하도록 설정하여 App Configuration에서 구성 설정을 동적으로 새로 고칩니다. Azure 관리 ID를 사용하여 App Configuration에 대한 액세스를 간소화하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./howto-integrate-azure-managed-service-identity.md)
