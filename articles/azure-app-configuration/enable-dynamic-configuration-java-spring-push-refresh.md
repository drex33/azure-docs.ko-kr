---
title: '자습서: 단일 인스턴스 Java Spring 앱에서 푸시 새로 고침을 사용하여 동적 구성 사용'
titleSuffix: Azure App Configuration
description: 이 자습서에서는 푸시 새로 고침을 사용하여 Java Spring 앱의 구성 데이터를 동적으로 업데이트하는 방법을 알아봅니다.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: mametcal
ms.openlocfilehash: 340638bd5fba981525ac3ff148ed4076f093e178
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020923"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-java-spring-app"></a>자습서: Java Spring 앱에서 푸시 새로 고침을 사용하여 동적 구성 사용

App Configuration Java Spring 클라이언트 라이브러리는 요청 시 애플리케이션을 다시 시작하지 않고도 구성을 업데이트할 수 있습니다. 다음 두 가지 방법 중 하나 또는 둘 모두를 사용하여 App Configuration의 변경 내용을 검색하도록 애플리케이션을 구성할 수 있습니다.

- 폴링 모델: 폴링을 사용하여 구성 변경 내용을 검색하는 기본 동작입니다. 설정의 캐시된 값이 만료되면 `AppConfigurationRefresh`의 `refreshConfigurations`에 대한 다음 호출이 서버에 요청을 보내서 구성이 변경되었는지 확인하고, 필요한 경우 업데이트된 구성을 끌어옵니다.

- 푸시 모델: [App Configuration 이벤트](./concept-app-configuration-event.md)를 사용하여 구성 변경 내용을 검색합니다. [웹후크](../event-grid/handler-event-hubs.md)를 사용하여 Event Grid와 함께 키 값 변경 이벤트를 보내도록 App Configuration이 설정되면 애플리케이션은 이러한 이벤트를 사용하여 구성을 업데이트된 상태로 유지하는 데 필요한 총 요청 수를 최적화할 수 있습니다.

이 자습서에서는 푸시 새로 고침을 사용하여 코드에서 동적 구성 업데이트를 구현하는 방법을 보여줍니다. 빠른 시작에 소개된 앱을 기반으로 합니다. 계속 진행하기 전에 먼저 [App Configuration을 사용하여 Java Spring 앱 만들기](./quickstart-java-spring-app.md)를 완료하세요.

이 자습서의 단계는 임의의 코드 편집기를 사용하여 수행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 훌륭한 옵션입니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * App Configuration에서 웹후크로 구성 변경 이벤트를 보내도록 구독 설정
> * App Service에 Spring Boot 애플리케이션 배포
> * App Configuration의 변경에 대한 응답으로 구성을 업데이트하도록 Java Spring 앱 설정
> * 애플리케이션의 최신 구성을 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- 버전 8 이상이 설치된 지원되는 [JDK(Java Development Kit)](/java/azure/jdk)
- [Apache Maven](https://maven.apache.org/download.cgi) 버전 3.0 이상
- 기존 Azure App Configuration 저장소.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="setup-push-refresh"></a>푸시 새로 고침 설정

1. *pom.xml* 을 열고 다음 종속성을 사용하여 파일을 업데이트합니다.

   ```xml
           <dependency>
               <groupId>com.azure.spring</groupId>
               <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
               <version>2.0.0</version>
           </dependency>
   
           <!-- Adds the Ability to Push Refresh -->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-actuator</artifactId>
           </dependency>
   ```

1. Maven을 통해 Azure App Service에 애플리케이션을 배포할 수 있도록 [Maven App Service 배포](../app-service/quickstart-java.md?tabs=javase)를 설정합니다.

   ```console
   mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
   ```

1. bootstrap.properties를 열고 Azure App Configuration 푸시 새로 고침 및 Azure Service Bus를 구성

   ```properties
   # Azure App Configuration Properties
   spring.cloud.azure.appconfiguration.stores[0].connection-string= ${AppConfigurationConnectionString}
   spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled= true
   spring.cloud.azure.appconfiguration.stores[0].monitoring.cacheExpiration= 30d
   spring.cloud.azure.appconfiguration.stores[0].monitoring.triggers[0].key= sentinel
   spring.cloud.azure.appconfiguration.stores[0].monitoring.push-notification.primary-token.name= myToken
   spring.cloud.azure.appconfiguration.stores[0].monitoring.push-notification.primary-token.secret= myTokenSecret
   
   management.endpoints.web.exposure.include= "appconfiguration-refresh"
   ```

잠재적인 제한을 줄이기 위해 캐시된 값이 더티로 표시되기 전에 임의 지연이 추가됩니다. 캐시된 값이 더티로 표시되기 전의 기본 최대 지연 시간은 30초입니다.

> [!NOTE]
> 기본 토큰 이름은 App Configuration 키로 저장한 다음, 기본 토큰 암호를 보안 강화를 위한 App Configuration Key Vault 참조로 저장해야 합니다.

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

Event Grid 웹후크를 만들려면 유효성 검사가 필요합니다. 이 [가이드](../event-grid/webhook-event-delivery.md)를 따르거나 이미 구성된 Azure App Configuration Spring 웹 라이브러리로 애플리케이션을 시작하여 유효성을 검사할 수 있습니다. 그러면 애플리케이션이 등록됩니다. 이벤트 구독을 사용하려면 다음 두 섹션의 단계를 수행합니다.

1. 환경 변수를 App Configuration 인스턴스의 연결 문자열로 설정합니다.

    #### <a name="windows-command-prompt"></a>[Windows 명령 프롬프트](#tab/cmd)

    ```cmd
    setx AppConfigurationConnectionString <connection-string-of-your-app-configuration-store>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AppConfigurationConnectionString = <connection-string-of-your-app-configuration-store>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AppConfigurationConnectionString = <connection-string-of-your-app-configuration-store>
    ```

1. 다음 명령을 실행하여 콘솔 앱을 빌드합니다.

   ```shell
    mvn package
   ```

1. 빌드가 성공적으로 완료되면 다음 명령을 실행하여 앱을 로컬로 실행합니다.

    ```shell
    mvn spring-boot:deploy
    ```

## <a name="set-up-an-event-subscription"></a>이벤트 구독을 설정

1. Azure Portal에서 App Configuration 리소스를 열고 `Events` 창에서 `+ Event Subscription`을 클릭합니다.

    :::image type="content" source="./media/events-pane.png" alt-text="이벤트 창에는 새 구독을 만드는 옵션이 있습니다." :::

1. `Event Subscription` 및 `System Topic`에 대한 이름을 입력합니다. 기본적으로 이벤트 유형 Key-Value 수정 및 Key-Value 삭제가 설정되며 이를 필터 탭 사용과 함께 변경하면 푸시 이벤트가 전송되는 정확한 이유를 선택할 수 있습니다.

    :::image type="content" source="./media/create-event-subscription.png" alt-text="이벤트에는 이름, 토픽 및 필터가 필요합니다." :::

1. `Endpoint Type`을 `Web Hook`로 선택하고 `Select an endpoint`를 선택합니다.

    :::image type="content" source="./media/event-subscription-webhook-endpoint.png" alt-text="엔드포인트를 선택하면 엔드포인트 URI를 입력할 새 블레이드가 만들어집니다." :::

1. 엔드포인트는 애플리케이션의 URI + "/actuator/appconfiguration-refresh?{your-token-name}={your-token-secret}"입니다. 예: `https://my-azure-webapp.azurewebsites.net/actuator/appconfiguration-refresh?myToken=myTokenSecret`

1. `Create`를 클릭하여 구독을 만듭니다. `Create`가 선택되면 웹후크에 대한 등록 요청이 애플리케이션으로 전송됩니다. 이는 Azure App Configuration 클라이언트 라이브러리에서 수신되고 확인되며 유효한 응답을 반환합니다.

1. `Events` 창에서 `Event Subscriptions`를 클릭하여 구독이 성공적으로 만들어졌는지 확인합니다.

    :::image type="content" source="./media/event-subscription-view-webhook.png" alt-text="웹후크는 페이지 아래쪽의 테이블에 표시됩니다." :::

> [!NOTE]
> 구성 변경을 구독할 때는 하나 이상의 필터를 사용하여 애플리케이션으로 전송되는 이벤트 수를 줄일 수 있습니다. 이러한 필터는 [Event Grid 구독 필터](../event-grid/event-filtering.md) 또는 [Service Bus 구독 필터](../service-bus-messaging/topic-filters.md)로 구성할 수 있습니다. 예를 들어 구독 필터는 특정 문자열로 시작하는 키에 대한 변경의 이벤트만 구독하는 데 사용할 수 있습니다.

## <a name="verify-and-test-application"></a>애플리케이션 확인 및 테스트

1. 애플리케이션이 실행되면 *curl* 을 사용하여 애플리케이션을 테스트합니다. 예를 들어 다음과 같습니다.

   ```cmd
   curl -X GET http://localhost:8080
   ```

1. **Azure Portal** 을 열고 애플리케이션과 연결된 앱 구성 리소스로 이동합니다. **작업** 아래에서 **구성 탐색기** 를 선택하고, 다음 키의 값을 업데이트합니다.

    | 키 | 값 |
    |---|---|
    | application/config.message | Hello - 업데이트됨 |

1. 표시되는 새 메시지를 확인하려면 브라우저 페이지를 새로 고칩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Java 앱을 사용하도록 설정하여 App Configuration에서 구성 설정을 동적으로 새로 고칩니다. Azure 관리 ID를 사용하여 App Configuration에 대한 액세스를 간소화하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./howto-integrate-azure-managed-service-identity.md)