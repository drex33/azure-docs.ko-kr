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
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019, mode-other
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 593ca7b9cdf503d8dc69f243403353f224c7297b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133042762"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>빠른 시작: Azure App Service에서 Java 앱 만들기

[Azure App Service](overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다.  이 빠른 시작에서는 [Azure Web App Plugin for Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)과 함께 [Azure CLI](/cli/azure/get-started-with-azure-cli)를 사용하여 .jar 파일 또는 .war 파일을 배포하는 방법을 보여줍니다. 

이 문서의 IDE 버전도 있습니다. [Azure Toolkit for IntelliJ 빠른 시작](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) 또는 [Azure Toolkit for Eclipse 빠른 시작](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)을 확인하세요.

## <a name="1-prepare-your-environment"></a>1. 환경 준비

시작하기 전에 다음이 있어야 합니다.

+ 입니다. <abbr title="Azure 사용량에 대한 청구 정보를 유지 관리하는 프로필입니다.">Azure 계정</abbr> 활성 <abbr title="Azure에서 리소스를 관리하는 기본 조직 구조이며 일반적으로 조직 내의 개인 또는 부서와 연결됩니다.">subscription</abbr>. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure CLI](/cli/azure/install-azure-cli)

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-support-on-azure) 버전 8 또는 11.

+ [Apache Maven](https://maven.apache.org) 버전 3.0 이상

Maven 플러그 인은 Azure CLI의 계정 자격 증명을 사용하여 App Services에 배포합니다. 계속하려면 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli)합니다. 자세한 내용은 [Maven 플러그 인으로 인증](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)을 참조하세요.

```azurecli
az login
```

<br>
<hr/>

## <a name="2-create-a-java-app"></a>2. Java 앱 만들기

# <a name="java-se"></a>[Java SE](#tab/javase)

[Spring Boot 시작하기](https://github.com/spring-guides/gs-spring-boot) 샘플 프로젝트를 복제합니다.

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

디렉터리를 완료된 프로젝트로 변경합니다.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Cloud Shell 프롬프트에서 다음 Maven 명령을 실행하여 `helloworld`라는 새 앱을 만듭니다.

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

그런 다음, 작업 디렉터리를 프로젝트 폴더로 변경합니다.

```bash
cd helloworld
```

---

<br>
<hr/>

## <a name="3-configure-the-maven-plugin"></a>3. Maven 플러그 인 구성

아래의 maven 명령을 실행하여 배포를 구성합니다. 이 명령은 App Service 운영 체제, Java 버전 및 Tomcat 버전을 설정하는 데 도움이 됩니다.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. **Subscription** 옵션을 사용하라는 메시지가 표시되면 줄 시작에 인쇄 번호를 입력하여 적절한 `Subscription`을 선택합니다.
1. **Web App** 옵션을 묻는 메시지가 표시되면 Enter를 눌러 기본 옵션 `<create>`를 수락하거나 기존 앱을 선택합니다.
1. **OS** 옵션을 사용하라는 메시지가 표시되면 `3`를 입력하여 **Windows** 를 선택합니다.
1. **가격 책정 계층** 옵션을 사용하라는 메시지가 표시되면 `2`를 입력하여 **B2** 를 선택합니다.
1. Enter를 눌러 기본 Java 버전인 **Java 8** 을 사용합니다.
1. 마지막으로, 마지막 프롬프트에서 Enter를 눌러 선택 내용을 확인합니다.

    요약 출력은 아래에 표시된 코드 조각과 유사하게 표시됩니다.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. **Subscription** 옵션을 사용하라는 메시지가 표시되면 줄 시작에 인쇄 번호를 입력하여 적절한 `Subscription`을 선택합니다.
1. **Web App** 옵션을 묻는 메시지가 표시되면 Enter를 눌러 기본 옵션 `<create>`를 수락하거나 기존 앱을 선택합니다.
1. **OS** 옵션을 사용하라는 메시지가 표시되면 `3`를 입력하여 **Windows** 를 선택합니다.
1. **가격 책정 계층** 옵션을 사용하라는 메시지가 표시되면 `2`를 입력하여 **B2** 를 선택합니다.
1. Enter를 눌러 기본 Java 버전인 **Java 8** 을 사용합니다.
1. Enter를 눌러 기본 웹 컨테이너인 **Tomcat 8.5** 를 사용합니다.
1. 마지막으로, 마지막 프롬프트에서 Enter를 눌러 선택 내용을 확인합니다.

    요약 출력은 아래에 표시된 코드 조각과 유사하게 표시됩니다.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. **Subscription** 옵션을 사용하라는 메시지가 표시되면 줄 시작에 인쇄 번호를 입력하여 적절한 `Subscription`을 선택합니다.
1. **Web App** 옵션을 묻는 메시지가 표시되면 Enter를 눌러 기본 옵션 `<create>`를 수락하거나 기존 앱을 선택합니다.
1. **OS** 옵션을 사용하라는 메시지가 표시되면 Enter를 눌러 **Linux** 를 선택합니다.
1. **가격 책정 계층** 옵션을 사용하라는 메시지가 표시되면 `2`를 입력하여 **B2** 를 선택합니다.
1. Enter를 눌러 기본 Java 버전인 **Java 8** 을 사용합니다.
1. 마지막으로, 마지막 프롬프트에서 Enter를 눌러 선택 내용을 확인합니다.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. **Subscription** 옵션을 사용하라는 메시지가 표시되면 줄 시작에 인쇄 번호를 입력하여 적절한 `Subscription`을 선택합니다.
1. **Web App** 옵션을 묻는 메시지가 표시되면 Enter를 눌러 기본 옵션 `<create>`를 수락하거나 기존 앱을 선택합니다.
1. **OS** 옵션을 사용하라는 메시지가 표시되면 Enter를 눌러 **Linux** 를 선택합니다.
1. **가격 책정 계층** 옵션을 사용하라는 메시지가 표시되면 `2`를 입력하여 **B2** 를 선택합니다.
1. Enter를 눌러 기본 Java 버전인 **Java 8** 을 사용합니다.
1. Enter를 눌러 기본 웹 컨테이너인 **Tomcat 8.5** 를 사용합니다.
1. 마지막으로, 마지막 프롬프트에서 Enter를 눌러 선택 내용을 확인합니다.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    </pre>

---

::: zone-end

필요한 경우 `pom.xml`에서 직접 App Service에 대한 구성을 수정할 수 있습니다. 몇 가지 일반적인 사항은 다음과 같습니다.

속성 | 필수 | Description | 버전
---|---|---|---
`<schemaVersion>` | false | 구성 스키마의 버전을 지정합니다. 지원되는 값은 `v1`, `v2`입니다. | 1.5.2
`<subscriptionId>` | false | 구독 ID를 지정합니다. | 0.1.0+
`<resourceGroup>` | true | Azure <abbr title="한 단위로 관리할 수 있는 관련 Azure 리소스에 대한 논리적 컨테이너입니다.">리소스 그룹</abbr> 웹앱용 | 0.1.0+
`<appName>` | true | 웹앱의 이름입니다. | 0.1.0+
`<region>` | true | 웹앱이 호스트되는 지역을 지정합니다(기본값: **westeurope**). [지원되는 지역](https://azure.microsoft.com/global-infrastructure/services/?products=app-service) 섹션에 있는 모든 유효한 지역입니다. | 0.1.0+
`<pricingTier>` | false | 웹앱에 대한 가격 책정 계층입니다. 기본값은 프로덕션 작업의 경우 **P1V2** 이고, **B2** 는 Java 개발/테스트에 권장되는 최솟값입니다. [자세히 알아보기](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0+
`<runtime>` | true | 런타임 환경 구성이며, [여기](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)에서 세부 정보를 볼 수 있습니다. | 0.1.0+
`<deployment>` | true | 배포 구성이며, [여기](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)에서 세부 정보를 볼 수 있습니다. | 0.1.0+

`<appName>` 및 `<resourceGroup>`의 값에 유의해야 합니다(예제 출력의 `helloworld-1590394316693` 및 `helloworld-1590394316693-rg`). 나중에 사용됩니다.

[문제 보고](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

<br>
<hr/>

## <a name="4-deploy-the-app"></a>4. 앱 배포

다음 명령을 사용하여 Java 앱을 Azure에 배포합니다.

```bash
mvn package azure-webapp:deploy
```

배포가 완료되면 애플리케이션이 `http://<appName>.azurewebsites.net/`에 준비됩니다. 로컬 웹 브라우저에서 URL을 엽니다. 다음이 표시되어야 합니다.

![Azure App Service에서 실행 중인 샘플 앱](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**축하합니다.** App Service에 첫 번째 Java 앱을 배포했습니다.

[문제 보고](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. 리소스 정리

추가 비용이 발생하지 않도록 Java 앱과 관련 리소스를 삭제합니다.

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

이 명령을 실행하는 데 1분 정도 걸릴 수 있습니다.

<br>
<hr/>

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
