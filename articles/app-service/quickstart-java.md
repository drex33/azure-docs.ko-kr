---
title: '빠른 시작: Azure App Service에서 Java 앱 만들기'
description: 몇 분 안에 첫 번째 Java Hello World를 Azure App Service에 배포합니다. Azure Web App Plugin for Maven을 사용하면 Java 앱을 편리하게 배포할 수 있습니다.
keywords: azure, 앱 서비스, 웹앱, windows, linux, java, maven, 빠른 시작
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-java-uiex
ms.openlocfilehash: 5be4133f23764ab87e8c1fca3b176d680e1eeae1
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129855615"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>빠른 시작: Azure App Service에서 Java 앱 만들기

[Azure App Service](overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다.  이 빠른 시작에서는 [Azure Web App Plugin for Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)과 함께 [Azure CLI](/cli/azure/get-started-with-azure-cli)를 사용하여 .jar 파일 또는 .war 파일을 배포하는 방법을 보여줍니다. 탭을 사용하여 Java SE와 Tomcat 지침 사이를 전환합니다.


> [!NOTE]
> IntelliJ 및 Eclipse와 같은 인기 있는 IDE를 사용하여 동일한 작업을 수행할 수도 있습니다. [Azure Toolkit for IntelliJ 빠른 시작](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) 또는 [Azure Toolkit for Eclipse 빠른 시작](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)에서 유사한 문서를 확인하세요.


![Azure App Service에서 실행 중인 샘플 앱](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java 앱 만들기

# <a name="java-se"></a>[Java SE](#tab/javase)

[Spring Boot 시작하기](https://github.com/spring-guides/gs-spring-boot) 샘플 프로젝트를 복제합니다.

```azurecli-interactive
git clone https://github.com/spring-guides/gs-spring-boot
```

디렉터리를 완료된 프로젝트로 변경합니다.

```azurecli-interactive
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Cloud Shell 프롬프트에서 다음 Maven 명령을 실행하여 `helloworld`라는 새 앱을 만듭니다.

```azurecli-interactive
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

그런 다음, 작업 디렉터리를 프로젝트 폴더로 변경합니다.

```azurecli-interactive
cd helloworld
```

# <a name="jboss-eap"></a>[JBoss EAP](#tab/jbosseap)

::: zone pivot="platform-windows"

JBoss EAP는 Linux 버전의 App Service에서만 사용할 수 있습니다. 이 문서의 맨 위에 있는 **Linux** 단추를 선택하여 JBoss EAP에 대한 빠른 시작 지침을 확인하세요.

::: zone-end
::: zone pivot="platform-linux"

애완 동물 스토어 데모 애플리케이션을 복제합니다.

```azurecli-interactive
git clone https://github.com/agoncal/agoncal-application-petstore-ee7.git
```

디렉터리를 복제된 프로젝트로 변경합니다.

```azurecli-interactive
cd agoncal-application-petstore-ee7
```

::: zone-end

---

## <a name="configure-the-maven-plugin"></a>Maven 플러그 인 구성

Azure App Service에 대한 배포 프로세스는 Azure CLI의 Azure 자격 증명을 자동으로 사용합니다. Azure CLI가 로컬로 설치되어 있지 않으면 Maven 플러그 인은 Oauth 또는 디바이스 로그인으로 인증됩니다. 자세한 내용은 [Maven 플러그 인으로 인증](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)을 참조하세요.

아래의 maven 명령을 실행하여 배포를 구성합니다. 이 명령은 App Service 운영 체제, Java 버전 및 Tomcat 버전을 설정하는 데 도움이 됩니다.

```azurecli-interactive
mvn com.microsoft.azure:azure-webapp-maven-plugin:2.2.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. **Subscription** 옵션을 사용하라는 메시지가 표시되면 줄 시작에 인쇄된 번호를 입력하여 적절한 `Subscription`을 선택합니다.
2. **Web App** 옵션을 사용하라는 메시지가 표시되면 Enter를 눌러 기본 옵션 `<create>`를 선택합니다.
3. **OS** 옵션을 사용하라는 메시지가 표시되면 `1`를 입력하여 **Windows** 를 선택합니다.
4. **javaVersion** 옵션을 사용하라는 메시지가 표시되면 `1`을 입력하여 **Java 8** 을 선택합니다.
5. **가격 책정 계층** 옵션을 사용하라는 메시지가 표시되면 `10`을 입력하여 **P1v2** 를 선택합니다.
6. 마지막으로, 마지막 프롬프트에서 Enter를 눌러 선택 내용을 확인합니다.

    요약 출력은 아래에 표시된 코드 조각과 유사하게 표시됩니다.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : centralus
    PricingTier : P1v2
    OS : Windows
    Java : Java 8
    Web server stack : Java SE
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. **Subscription** 옵션을 사용하라는 메시지가 표시되면 줄 시작에 인쇄된 번호를 입력하여 적절한 `Subscription`을 선택합니다.
2. **Web App** 옵션을 사용하라는 메시지가 표시되면 Enter를 눌러 기본 옵션 `<create>`를 선택합니다.
3. **OS** 옵션을 사용하라는 메시지가 표시되면 `1`를 입력하여 **Windows** 를 선택합니다.
4. **javaVersion** 옵션을 사용하라는 메시지가 표시되면 `1`을 입력하여 **Java 8** 을 선택합니다.
5. **webContainer** 옵션을 사용하라는 메시지가 표시되면 `1`을 입력하여 **Tomcat 8.5** 를 선택합니다.
6. **가격 책정 계층** 옵션을 사용하라는 메시지가 표시되면 `10`을 입력하여 **P1v2** 를 선택합니다.
7. 마지막으로, 마지막 프롬프트에서 Enter를 눌러 선택 내용을 확인합니다.

    요약 출력은 아래에 표시된 코드 조각과 유사하게 표시됩니다.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : centralus
    PricingTier : P1v2
    OS : Windows
    Java : Java 8
    Web server stack : Tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="jboss-eap"></a>[JBoss EAP](#tab/jbosseap)

JBoss EAP는 Linux 버전의 App Service에서만 사용할 수 있습니다. 이 문서의 맨 위에 있는 **Linux** 단추를 선택하여 JBoss EAP에 대한 빠른 시작 지침을 확인하세요.

---

::: zone-end
::: zone pivot="platform-linux"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. **Subscription** 옵션을 사용하라는 메시지가 표시되면 줄 시작에 인쇄된 번호를 입력하여 적절한 `Subscription`을 선택합니다.
1. **Web App** 옵션을 사용하라는 메시지가 표시되면 Enter를 눌러 기본 옵션 `<create>`를 선택합니다.
1. **OS** 옵션을 사용하라는 메시지가 표시되면 Enter를 눌러 **Linux** 를 선택합니다.
2. **javaVersion** 옵션을 사용하라는 메시지가 표시되면 `1`을 입력하여 **Java 8** 을 선택합니다.
3. **가격 책정 계층** 옵션을 사용하라는 메시지가 표시되면 `9`을 입력하여 **P1v2** 를 선택합니다.
4. 마지막으로, 마지막 프롬프트에서 Enter를 눌러 선택 내용을 확인합니다.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : centralus
    PricingTier : P1v2
    OS : Linux
    Web server stack : Java SE
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. **Subscription** 옵션을 사용하라는 메시지가 표시되면 줄 시작에 인쇄된 번호를 입력하여 적절한 `Subscription`을 선택합니다.
1. **Web App** 옵션을 사용하라는 메시지가 표시되면 Enter를 눌러 기본 옵션 `<create>`를 선택합니다.
1. **OS** 옵션을 사용하라는 메시지가 표시되면 Enter를 눌러 **Linux** 를 선택합니다.
1. **javaVersion** 옵션을 사용하라는 메시지가 표시되면 `1`을 입력하여 **Java 8** 을 선택합니다.
1. **webcontainer** 옵션을 사용하라는 메시지가 표시되면 `3`을 입력하여 **Tomcat 8.5** 를 선택합니다.
1. **가격 책정 계층** 옵션을 사용하라는 메시지가 표시되면 `9`을 입력하여 **P1v2** 를 선택합니다.
1. 마지막으로, 마지막 프롬프트에서 Enter를 눌러 선택 내용을 확인합니다.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : centralus
    PricingTier : P1v2
    OS : Linux
    Web server stack : Tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="jboss-eap"></a>[JBoss EAP](#tab/jbosseap)

1. **Subscription** 옵션을 사용하라는 메시지가 표시되면 줄 시작에 인쇄된 번호를 입력하여 적절한 `Subscription`을 선택합니다.
1. **웹 앱** 옵션을 선택하라는 메시지가 표시되면 enter 키를 눌러 기본 옵션`<create>`을 적용합니다.
1. **OS** 옵션을 사용하라는 메시지가 표시되면 Enter를 눌러 **Linux** 를 선택합니다.
1. **javaVersion** 옵션을 사용하라는 메시지가 표시되면 `1`을 입력하여 **Java 8** 을 선택합니다.
1. **webContainer** 옵션을 사용하라는 메시지가 표시되면 `1`을 입력하여 **Jbosseap 7** 을 선택합니다.
1. **pricingTier** 옵션을 사용하라는 메시지가 표시되면 `1`을 입력하여 **P1v3** 를 선택합니다.
1. 마지막으로, 마지막 프롬프트에서 Enter를 눌러 선택 내용을 확인합니다.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : petstoreee7-1623451825408
    ResourceGroup : petstoreee7-1623451825408-rg
    Region : centralus
    PricingTier : P1v3
    OS : Linux
    Java : Java 8
    Web server stack: Jbosseap 7
    Deploy to slot : false
    Confirm (Y/N) [Y]: y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 01:01 min
    [INFO] Finished at: 2021-06-11T15:52:25-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

필요한 경우 `pom.xml`에서 직접 App Service에 대한 구성을 수정할 수 있습니다. 몇 가지 일반적인 사항은 다음과 같습니다.

속성 | 필수 | 설명 | 버전
---|---|---|---
`<schemaVersion>` | false | 구성 스키마의 버전을 지정합니다. 지원되는 값은 `v1`, `v2`입니다. | 1.5.2
`<subscriptionId>` | false | 구독 ID를 지정합니다. | 0.1.0+
`<resourceGroup>` | true | 웹앱에 대한 Azure 리소스 그룹입니다. | 0.1.0+
`<appName>` | true | 웹앱의 이름입니다. | 0.1.0+
`<region>` | false | 웹앱이 호스트되는 지역을 지정합니다(기본값: **centralus**). [지원되는 지역](https://azure.microsoft.com/global-infrastructure/services/?products=app-service) 섹션에 있는 모든 유효한 지역입니다. | 0.1.0+
`<pricingTier>` | false | 웹앱에 대한 가격 책정 계층입니다. 기본값은 프로덕션 작업의 경우 **P1v2** 이고, **B2** 는 Java 개발/테스트에 권장되는 최솟값입니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0+
`<runtime>` | false | 런타임 환경 구성이며, [여기](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)에서 세부 정보를 볼 수 있습니다. | 0.1.0+
`<deployment>` | false | 배포 구성이며, [여기](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)에서 세부 정보를 볼 수 있습니다. | 0.1.0+

`<appName>` 및 `<resourceGroup>` 값(이 데모에서는 `helloworld-1590394316693` 및 `helloworld-1590394316693-rg`에 따라 다름)에 유의해야 합니다. 나중에 사용됩니다.

> [!div class="nextstepaction"]
> [문제가 발생했습니다.](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>앱 배포

pom 파일에서 모든 구성이 준비되면 단일 명령으로 Java 앱을 Azure에 배포할 수 있습니다.

```azurecli-interactive
mvn package azure-webapp:deploy
```

::: zone pivot="platform-linux"

> [!NOTE]
> JBoss EAP의 경우 Wildfly를 로컬로 설치해야 하므로 `mvn package azure-webapp:deploy -DskipTests`를 실행하여 테스트를 사용하지 않도록 설정합니다. 

::: zone-end

배포가 완료되면 애플리케이션이 `http://<appName>.azurewebsites.net/`(이 데모에서는 `http://helloworld-1590394316693.azurewebsites.net`)에 준비됩니다. 로컬 웹 브라우저에서 url을 열면 다음이 표시됩니다.

![Azure App Service에서 실행 중인 샘플 앱](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**축하합니다.** App Service에 첫 번째 Java 앱을 배포했습니다.

> [!div class="nextstepaction"]
> [문제가 발생했습니다.](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 Azure 리소스를 리소스 그룹에 만들었습니다. 나중에 이러한 리소스가 필요하지 않을 것으로 예상되는 경우 포털에서 리소스 그룹을 삭제하거나 Cloud Shell에서 다음 명령을 실행하여 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

이 명령을 실행하는 데 1분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Java를 사용하여 Azure DB for PostgreSQL에 연결](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [CI/CD를 설정합니다](deploy-continuous-deployment.md).

> [!div class="nextstepaction"]
> [가격 정보](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [로그 및 메트릭 집계](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [강화](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Java 개발자 리소스용 Azure](/java/azure/)

> [!div class="nextstepaction"]
> [Java 앱 구성](configure-language-java.md)
