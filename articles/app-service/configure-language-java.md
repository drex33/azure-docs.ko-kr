---
title: Java 앱 구성
description: Azure App Service에서 실행되도록 Java 앱을 구성하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
keywords: azure 앱 서비스, 웹앱, windows, oss, java, tomcat, jboss
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: 47e9e221bd57453a0c799318f939de59b84feb86
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356077"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Azure App Service용 Java 앱 구성

Azure App Service는 Java 개발자가 완전 관리형 서비스에 Java SE, Tomcat 및 JBoss EAP 웹 애플리케이션을 신속하게 구축하고, 배포하고, 규모를 조정할 수 있게 도와줍니다. 명령줄에서 또는 IntelliJ, Eclipse 또는 Visual Studio Code와 같은 편집기에서 Maven 플러그 인을 사용하여 애플리케이션을 배포합니다.

이 가이드에서는 App Service를 사용하는 Java 개발자를 위해 핵심 개념 및 지침을 제공합니다. Azure App Service를 사용한 경험이 없는 분들은 먼저 [Java 빠른 시작](quickstart-java.md)을 정독해야 합니다. Java 개발에 국한되지 않는 App Service 사용에 대한 일반적인 질문의 답은 [App Service FAQ](faq-configuration-and-management.yml)에서 찾을 수 있습니다.

## <a name="show-java-version"></a>Java 버전 표시

::: zone pivot="platform-windows"  

현재 Java 버전을 표시하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config show --name <app-name> --resource-group <resource-group-name> --query "[javaVersion, javaContainer, javaContainerVersion]"
```

지원되는 Java 버전을 모두 표시하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp list-runtimes | grep java
```

::: zone-end

::: zone pivot="platform-linux"

현재 Java 버전을 표시하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

지원되는 Java 버전을 모두 표시하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행합니다.

```azurecli-interactive
az webapp list-runtimes --linux | grep "JAVA\|TOMCAT\|JBOSSEAP"
```

::: zone-end

## <a name="deploying-your-app"></a>앱 배포

### <a name="build-tools"></a>빌드 도구

#### <a name="maven"></a>Maven
Azure [Web Apps Maven 플러그 인을](https://github.com/microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)사용하면 프로젝트 루트에서 하나의 명령을 통해 Azure Web App용 Maven Java 프로젝트를 쉽게 준비할 수 있습니다.

```shell
mvn com.microsoft.azure:azure-webapp-maven-plugin:2.2.0:config
```

이 명령은 `azure-webapp-maven-plugin` 기존 Azure 웹앱을 선택하거나 새 웹앱을 만들라는 메시지를 표시하여 플러그 인 및 관련 구성을 추가합니다. 그런 후, 다음 명령을 사용하여 Java 앱을 Azure에 배포할 수 있습니다.
```shell
mvn package azure-webapp:deploy
```

의 샘플 구성은 다음과 `pom.xml` 같습니다.
```xml
<plugin> 
  <groupId>com.microsoft.azure</groupId>  
  <artifactId>azure-webapp-maven-plugin</artifactId>  
  <version>2.2.0</version>  
  <configuration>
    <subscriptionId>111111-11111-11111-1111111</subscriptionId>
    <resourceGroup>spring-boot-xxxxxxxxxx-rg</resourceGroup>
    <appName>spring-boot-xxxxxxxxxx</appName>
    <pricingTier>B2</pricingTier>
    <region>westus</region>
    <runtime>
      <os>Linux</os>      
      <webContainer>Java SE</webContainer>
      <javaVersion>Java 11</javaVersion>
    </runtime>
    <deployment>
      <resources>
        <resource>
          <type>jar</type>
          <directory>${project.basedir}/target</directory>
          <includes>
            <include>*.jar</include>
          </includes>
        </resource>
      </resources>
    </deployment>
  </configuration>
</plugin> 
```

#### <a name="gradle"></a>Gradle
1. 플러그 인을 에 추가하여 [Azure Web Apps Gradle 플러그](https://github.com/microsoft/azure-gradle-plugins/tree/master/azure-webapp-gradle-plugin) 인을 설치합니다. `build.gradle`
    ```groovy
    plugins {
      id "com.microsoft.azure.azurewebapp" version "1.2.0"
    }
    ```

1. 웹앱 세부 정보를 구성합니다. 해당 Azure 리소스가 없으면 생성됩니다.
다음은 샘플 구성입니다. 자세한 내용은 이 [문서를](https://github.com/microsoft/azure-gradle-plugins/wiki/Webapp-Configuration)참조하세요.
    ```groovy
    azurewebapp {
        subscription = '<your subscription id>'
        resourceGroup = '<your resource group>'
        appName = '<your app name>'
        pricingTier = '<price tier like 'P1v2'>'
        region = '<region like 'westus'>'
        runtime {
          os = 'Linux'
          webContainer = 'Tomcat 9.0' // or 'Java SE' if you want to run an executable jar
          javaVersion = 'Java 8'
        }
        appSettings {
            <key> = <value>
        }
        auth {
            type = 'azure_cli' // support azure_cli, oauth2, device_code and service_principal
        }
    }
    ```

1. 하나의 명령으로 배포합니다.
    ```shell
    gradle azureWebAppDeploy
    ```
    
### <a name="ides"></a>IDE
Azure는 다음을 포함하여 인기 있는 Java ID에서 원활한 Java App Service 개발 환경을 제공합니다.
- *VS Code:* [java Web Apps with Visual Studio Code](https://code.visualstudio.com/docs/java/java-webapp#_deploy-web-apps-to-the-cloud)
- *IntelliJ IDEA:*[IntelliJ를 사용하여 Azure App Service Hello World 웹앱 만들기](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app)
- *Eclipse: Eclipse를*[사용하여 Azure App Service 위한 Hello World 웹앱 만들기](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)

### <a name="kudu-api"></a>Kudu API
#### <a name="java-se"></a>Java SE

Java SE에 .jar 파일을 배포하려면 Kudu 사이트의 `/api/publish/` 엔드포인트를 사용합니다. 이 API에 대한 자세한 내용은 [이 설명서](./deploy-zip.md#deploy-warjarear-packages)를 참조하세요. 

> [!NOTE]
>  .jar 애플리케이션의 이름을 App Service에서 `app.jar`로 정해야 애플리케이션을 식별하고 실행할 수 있습니다. 위에서 언급한 Maven 플러그인은 배포 중에 애플리케이션의 이름을 자동으로 바꿉니다. JAR 이름을 *app.jar* 로 변경하고 싶지 않은 경우에는 .jar 앱을 실행하는 명령이 포함된 셸 스크립트를 업로드하면 됩니다. 그런 다음, Portal의 구성 섹션에 있는 [시작 파일](/azure/app-service/faq-app-service-linux#built-in-images) 텍스트 상자에 이 스크립트의 절대 경로를 붙여 넣습니다. 시작 스크립트는 배치된 디렉터리에서 실행되지 않습니다. 따라서 항상 절대 경로를 사용하여 시작 스크립트의 파일을 참조해야 합니다(예: `java -jar /home/myapp/myapp.jar`).

#### <a name="tomcat"></a>Tomcat

.war 파일을 Tomcat에 배포하려면 `/api/wardeploy/` 엔드포인트를 사용하여 보관 파일을 게시합니다. 이 API에 대한 자세한 내용은 [이 설명서](./deploy-zip.md#deploy-warjarear-packages)를 참조하세요.

::: zone pivot="platform-linux"

#### <a name="jboss-eap"></a>JBoss EAP

JBoss에 .war 파일을 배포하려면 `/api/wardeploy/` 엔드포인트를 사용하여 보관 파일을 게시합니다. 이 API에 대한 자세한 내용은 [이 설명서](./deploy-zip.md#deploy-warjarear-packages)를 참조하세요.

.ear 파일을 배포하려면 [FTP를 사용합니다](deploy-ftp.md). .ear 애플리케이션은 애플리케이션의 구성에 정의된 컨텍스트 루트에 배포됩니다. 예를 들어 앱의 컨텍스트 루트가 `<context-root>myapp</context-root>`인 경우, `/myapp` 경로에서 사이트를 찾아 볼 수 있습니다. `http://my-app-name.azurewebsites.net/myapp` 웹앱을 루트 경로에 제공하려면 앱이 컨텍스트 루트를 루트 경로로 설정했는지 확인합니다. `<context-root>/</context-root>` 자세한 정보는 [웹 애플리케이션의 컨텍스트 루트 설정](https://docs.jboss.org/jbossas/guides/webguide/r2/en/html/ch06.html)을 참조하세요.

::: zone-end

FTP를 사용하여 .war 또는.jar을 배포하지 마십시오. FTP 도구는 시작 스크립트, 종속성 또는 기타 런타임 파일을 업로드하기 위해 설계되었습니다. 웹앱을 배포하기 위한 최적의 선택은 아닙니다.

## <a name="logging-and-debugging-apps"></a>앱 로깅 및 디버깅

Azure Portal을 통해 각 앱에 대한 성능 보고서, 트래픽 시각화 및 상태 확인을 사용할 수 있습니다. 자세한 내용은 [Azure App Service 진단 개요](overview-diagnostics.md)를 참조하세요.

### <a name="stream-diagnostic-logs"></a>진단 로그 스트림

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

자세한 내용은 [Cloud Shell에서 로그 스트리밍](troubleshoot-diagnostic-logs.md#in-cloud-shell)을 참조하세요.

::: zone pivot="platform-linux"

### <a name="ssh-console-access"></a>SSH 콘솔 액세스

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="troubleshooting-tools"></a>문제 해결 도구

기본 제공 Java 이미지는 [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) 운영 체제를 기반으로 합니다. `apk` 패키지 관리자를 사용하여 문제 해결 도구 또는 명령을 설치합니다.

::: zone-end

### <a name="flight-recorder"></a>비행 레코더

Azul JVM을 사용하는 App Service에 대한 모든 Java 런타임은 Zulu 비행 레코더와 함께 제공됩니다. 이를 사용하여 JVM, 시스템 및 애플리케이션 이벤트를 기록하고 Java 애플리케이션의 문제를 해결할 수 있습니다.

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>시간 기록

시간 기록 애플리케이션을 수행하려면 Java 애플리케이션의 PID(프로세스 ID)가 필요합니다. PID를 찾으려면 `https://<your-site-name>.scm.azurewebsites.net/ProcessExplorer/`에서 웹앱의 SCM 사이트로 브라우저를 엽니다. 이 페이지는 웹앱에서 실행 중인 프로세스를 표시합니다. 테이블에서 "java"라는 프로세스를 찾고 해당 PID(프로세스 ID)를 복사합니다.

다음으로, SCM 사이트의 상단 도구 모음에서 **디버그 콘솔** 을 열고 다음 명령을 실행합니다. `<pid>`를 이전에 복사한 프로세스 ID로 바꿉니다. 이 명령은 Java 애플리케이션의 30초 프로파일러 기록을 시작하고 `D:\home` 디렉터리에 `timed_recording_example.jfr`이라는 파일을 생성합니다.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

App Service에 SSH를 사용하고 `jcmd` 명령을 실행하여 실행 중인 모든 Java 프로세스 목록을 확인합니다. Jcmd 자체 외에 PID(프로세스 ID 번호)로 Java 애플리케이션이 실행 중인 것을 볼 수 있습니다.

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

아래 명령을 실행하여 JVM의 30초 기록을 시작합니다. 그러면 JVM을 프로파일링하고 홈 디렉터리에 *jfr_example.jfr* 이라는 JFR 파일을 만듭니다. (116을 Java 앱의 PID로 대체합니다.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

30초 간격 동안 `jcmd 116 JFR.check`를 실행하여 기록이 발생하는지 확인할 수 있습니다. 그러면 지정된 Java 프로세스의 모든 기록이 표시됩니다.

#### <a name="continuous-recording"></a>연속 기록

Zulu 비행 레코더를 사용하여 런타임 성능에 대한 영향을 최소화하면서 Java 애플리케이션을 지속적으로 프로파일링할 수 있습니다. 이렇게 하려면 다음 Azure CLI 명령을 실행하여 필요한 구성으로 JAVA_OPTS 앱 설정을 만듭니다. 앱이 시작되면 JAVA_OPTS 앱 설정의 내용이 `java` 명령에 전달됩니다.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

기록이 시작되면 `JFR.dump` 명령을 사용하여 언제든지 현재 기록 데이터를 덤프할 수 있습니다.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

::: zone-end

#### <a name="analyze-jfr-files"></a>`.jfr` 파일 분석

[FTPS](deploy-ftp.md)를 사용하여 JFR 파일을 로컬 컴퓨터에 다운로드합니다. JFR 파일을 분석하려면 [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/)을 다운로드하여 설치합니다. Zulu Mission Control에 대한 지침은 [Azul 설명서](https://docs.azul.com/zmc/) 및 [설치 지침](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)을 참조하세요.

### <a name="app-logging"></a>앱 로깅

::: zone pivot="platform-windows"

Azure Portal 또는 [Azure CLI](/cli/azure/webapp/log#az_webapp_log_config)를 통해 [애플리케이션 로깅](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)을 사용하도록 설정하여 애플리케이션의 표준 콘솔 출력 및 표준 콘솔 오류 스트림을 로컬 파일 시스템 또는 Azure Blob Storage에 쓰도록 App Service를 구성할 수 있습니다. 로컬 App Service 파일 시스템 인스턴스에 로깅하는 동작은 구성된 지 12시간 후에 비활성화 됩니다. 더 긴 시간 동안 보존하기를 원하는 경우 Blob Storage 컨테이너에 출력을 쓰도록 애플리케이션을 구성합니다. Java 및 Tomcat 앱 로그는 */home/LogFiles/Application/* 디렉터리에서 찾을 수 있습니다.

::: zone-end
::: zone pivot="platform-linux"

Azure Portal 또는 [Azure CLI](/cli/azure/webapp/log#az_webapp_log_config)를 통해 [애플리케이션 로깅](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer)을 사용하도록 설정하여 애플리케이션의 표준 콘솔 출력 및 표준 콘솔 오류 스트림을 로컬 파일 시스템 또는 Azure Blob Storage에 쓰도록 App Service를 구성할 수 있습니다. 더 긴 시간 동안 보존하기를 원하는 경우 Blob Storage 컨테이너에 출력을 쓰도록 애플리케이션을 구성합니다. Java 및 Tomcat 앱 로그는 */home/LogFiles/Application/* 디렉터리에서 찾을 수 있습니다.

Linux 기반 App Services에 대한 Azure Blob Storage 로깅은 [Azure Monitor(미리 보기)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)를 사용해서만 구성할 수 있습니다. 

::: zone-end

애플리케이션에서 [Logback](https://logback.qos.ch/) 또는 [Log4j](https://logging.apache.org/log4j)를 추적에 사용하는 경우 [Application Insights에서 Java 추적 로그 탐색](../azure-monitor/app/java-2x-trace-logs.md)의 로깅 프레임워크 구성 지침에 따라 이러한 추적 로그를 Azure Application Insights로 전송하여 검토할 수 있습니다.

## <a name="customization-and-tuning"></a>사용자 지정 및 튜닝

Linux용 Azure App Service는 기본적으로 Azure Portal 및 CLI를 통해 튜닝 및 사용자 지정이 가능합니다. 비 Java 관련 웹앱 구성에 대한 다음 문서를 검토하세요.

- [앱 설정 구성](configure-common.md#configure-app-settings)
- [사용자 지정 도메인 설정](app-service-web-tutorial-custom-domain.md)
- [TLS/SSL 바인딩 구성](configure-ssl-bindings.md)
- [CDN 추가](../cdn/cdn-add-to-web-app.md)
- [Kudu 사이트 구성](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)


### <a name="set-java-runtime-options"></a>Java 런타임 옵션 설정

할당된 메모리 또는 기타 JVM 런타임 옵션을 설정하려면 `JAVA_OPTS`라는 [앱 설정](configure-common.md#configure-app-settings)을 옵션과 함께 만듭니다. App Service는 시작될 때 이 설정을 Java 런타임에 환경 변수로 전달합니다.

Azure Portal에서, 웹앱의 **애플리케이션 설정** 아래에서 `-Xms512m -Xmx1204m`처럼 추가 설정을 포함하는 Java SE용 `JAVA_OPTS` 또는 Tomcat용 `CATALINA_OPTS`라고하는 새 앱 설정을 생성합니다.

Maven 플러그 인에서 앱 설정을 구성하려면 Azure 플러그 인 섹션에서 설정/값 태그를 추가합니다. 다음 예에서는 특정 최소 및 최대 Java 힙 크기를 설정합니다.

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

App Service 계획에서 배포 슬롯 하나를 사용하여 단일 애플리케이션을 실행하는 개발자는 다음 옵션을 사용할 수 있습니다.

- B1 및 S1 인스턴스: `-Xms1024m -Xmx1024m`
- B2 및 S2 인스턴스: `-Xms3072m -Xmx3072m`
- B3 및 S3 인스턴스: `-Xms6144m -Xmx6144m`

애플리케이션 힙 설정을 튜닝할 때 App Service 계획 세부 정보를 검토하고 여러 애플리케이션 및 배포 슬롯 요구 사항을 고려하여 최적의 메모리 할당을 찾아보세요.

### <a name="turn-on-web-sockets"></a>웹 소켓 켜기

애플리케이션의 Azure Portal **애플리케이션 설정** 에서 웹 소켓을 켭니다. 설정을 적용하려면 애플리케이션을 다시 시작해야 합니다.

Azure CLI에서 다음 명령을 사용하여 웹 소켓 지원을 켭니다.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

그런 다음, 애플리케이션을 다시 시작합니다.

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>기본 문자 인코딩 설정

Azure Portal에서, 웹앱의 **애플리케이션 설정** 아래에 `-Dfile.encoding=UTF-8` 값을 사용하여 `JAVA_OPTS`이라고 하는 새 앱 설정을 만듭니다.

또는 App Service Maven 플러그 인을 사용하여 앱 설정을 구성할 수 있습니다. 플러그 인 구성에서 설정 이름 및 값 태그를 추가합니다.

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>JSP 파일 미리 컴파일

Tomcat 애플리케이션의 성능을 향상시키기 위해 App Service에 배포하기 전에 JSP 파일을 컴파일할 수 있습니다. Apache Sling에서 제공하는 [Maven 플러그 인](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) 또는 [Ant 빌드 파일](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)을 사용할 수 있습니다.

## <a name="secure-applications"></a>보안 애플리케이션

App Service에서 실행되는 Java 애플리케이션의 [보안 모범 사례](../security/fundamentals/paas-applications-using-app-services.md) 집합은 다른 애플리케이션과 동일합니다.

### <a name="authenticate-users-easy-auth"></a>사용자 인증(간편 인증)

Azure Portal에서 **인증 및 권한 부여** 옵션을 사용하여 앱 인증을 설정합니다. 여기서 Azure Active Directory 또는 Facebook, Google, GitHub 등의 소셜 로그인을 사용하여 인증을 사용하도록 설정할 수 있습니다. Azure Portal 구성은 단일 인증 공급자를 구성할 때만 작동합니다. 자세한 내용은 [Azure Active Directory 로그인을 사용하도록 App Service 앱 구성](configure-authentication-provider-aad.md) 및 기타 ID 공급자 관련 문서를 참조하세요. 여러 로그인 공급자를 사용하도록 설정해야 하는 경우 [로그인 및 로그아웃 사용자 지정](configure-authentication-customize-sign-in-out.md) 문서의 지침을 따르세요.

#### <a name="java-se"></a>Java SE

Spring Boot 개발자는 [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory)를 사용하여 친숙한 Spring Security 주석 및 API로 애플리케이션을 보호할 수 있습니다. *application.properties* 파일에서 최대 헤더 크기를 늘려야 합니다. 이 값은 `16384`로 설정하는 것이 좋습니다.

#### <a name="tomcat"></a>Tomcat

Tomcat 애플리케이션은 주체 개체를 Map 개체로 캐스팅하여 서블릿에서 사용자의 클레임에 직접 액세스할 수 있습니다. Map 개체는 각 클레임 유형을 해당 유형에 대한 클레임 컬렉션에 매핑합니다. 아래 코드에서 `request`는 `HttpServletRequest`의 인스턴스입니다.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

이제 특정 클레임에 대한 `Map` 개체를 검사할 수 있습니다. 예를 들어 다음 코드 조각은 모든 클레임 형식을 반복하고 각 컬렉션의 내용을 인쇄합니다.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

사용자를 로그아웃시키려면 `/.auth/ext/logout` 경로를 사용합니다. 다른 작업을 수행하려면, [로그인 및 로그아웃 사용자 지정](configure-authentication-customize-sign-in-out.md)에 대한 설명서를 참조하세요. Tomcat [HttpServletRequest 인터페이스](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) 및 해당 메서드에 대한 공식 설명서도 있습니다. 다음 서블릿 메서드도 App Service 구성에 따라 하이드레이션됩니다.

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

이 기능을 사용하지 않도록 설정하려면 값이 `1`인 `WEBSITE_AUTH_SKIP_PRINCIPAL` 애플리케이션 설정을 만듭니다. App Service에서 추가한 모든 서블릿 필터를 사용하지 않도록 설정하려면 값이 `1`인 `WEBSITE_SKIP_FILTERS` 설정을 만듭니다.

### <a name="configure-tlsssl"></a>TLS/SSL 구성

[Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md) 지침에 따라 기존 TLS/SSL 인증서를 업로드하고 애플리케이션의 도메인 이름에 바인딩합니다. 기본적으로 애플리케이션에서 HTTP 연결을 계속 허용합니다. 자습서의 단계에 따라 TLS/SSL을 적용하세요.

### <a name="use-keyvault-references"></a>KeyVault 참조 사용

[Azure KeyVault](../key-vault/general/overview.md)는 액세스 정책 및 감사 기록을 통해 중앙 집중식 비밀 관리를 제공합니다. 키 자격 증명 모음에 암호 또는 연결 문자열과 같은 비밀을 저장하고 환경 변수를 통해 애플리케이션에서 이러한 비밀에 액세스할 수 있습니다.

먼저 [Key Vault에 앱 액세스 권한을 부여](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)하고 [애플리케이션 설정에서 사용자 비밀을 KeyVault 참조로 만드는](app-service-key-vault-references.md#reference-syntax) 지침을 따릅니다. App Service 터미널에 원격으로 액세스하는 동안 환경 변수를 인쇄하여 참조가 비밀로 해결되는지 확인할 수 있습니다.

이러한 비밀을 Spring 또는 Tomcat 구성 파일에 삽입하려면 환경 변수 삽입 구문(`${MY_ENV_VAR}`)을 사용합니다. Spring 구성 파일은 [표면화된 구성](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)에 대한 이 설명서를 참조하세요.

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>Java 키 저장소 사용

기본적으로 [App Service Linux에 업로드된](configure-ssl-certificate.md) 공용 또는 프라이빗 인증서는 컨테이너가 시작될 때 해당 Java 키 저장소에 로드됩니다. 인증서를 업로드한 후에는 Java 키 저장소에 로드하기 위해 App Service를 다시 시작해야 합니다. 공용 인증서는 `$JAVA_HOME/jre/lib/security/cacerts`에서 키 저장소로 로드되고 프라이빗 인증서는 `$JAVA_HOME/lib/security/client.jks`에 저장됩니다.

Java Key Store의 인증서를 사용하여 JDBC 연결을 암호화하려면 추가 구성이 필요할 수 있습니다. 선택한 JDBC 드라이버에 대한 설명서를 참조하세요.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>Java 키 저장소 초기화

`import java.security.KeyStore` 개체를 초기화하려면 암호를 사용하여 키 저장소 파일을 로드합니다. 두 키 저장소의 기본 암호는 `changeit`입니다.

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>수동으로 키 저장소 로드

키 저장소에 인증서를 수동으로 로드할 수 있습니다. App Service가 인증서를 키 저장소에 자동으로 로드하지 못하도록 값이 `1`인 앱 설정 `SKIP_JAVA_KEYSTORE_LOAD`를 만듭니다. Azure Portal을 통해 App Service에 업로드된 모든 공용 인증서는 `/var/ssl/certs/`에 저장됩니다. 프라이빗 인증서는 `/var/ssl/private/`에 저장됩니다.

App Service에 [SSH 연결을 열고](configure-linux-open-ssh-session.md) `keytool` 명령을 실행하여 Java Key Tool을 상호 작용하거나 디버그할 수 있습니다. 명령 목록은 [핵심 도구 설명서](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)를 참조하세요. KeyStore API에 대한 자세한 내용은 [공식 설명서](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)를 참조하세요.

::: zone-end

## <a name="configure-apm-platforms"></a>APM 플랫폼 구성

이 섹션에서는 Azure Monitor Application Insights, NewRelic, AppDynamics 애플리케이션 성능 모니터링(APM) 플랫폼을 사용하여 Linux의 Azure App Service에 배포된 Java 애플리케이션을 연결하는 방법을 설명합니다.

### <a name="configure-application-insights"></a>Application Insights 구성

Azure Monitor Application Insights는 고객이 실패, 병목 현상 및 사용 패턴을 관찰하여 애플리케이션 성능을 개선하고 MTTR (평균 해결 시간)을 줄일 수 있는 클라우드 네이티브 애플리케이션 모니터링 서비스입니다. 몇 번의 클릭 또는 CLI 명령을 통해 Node.js 또는 Java 앱에 대한 모니터링을 사용하도록 설정하고 로그, 메트릭 및 분산 추적을 자동으로 수집할 수 있어 앱에 SDK를 포함할 필요가 없습니다.

#### <a name="azure-portal"></a>Azure portal

Azure Portal에서 Application Insights를 사용하도록 설정하려면 왼쪽 메뉴의 **Application Insights** 로 이동하여 **Application Insights 활성화** 를 선택합니다. 기본값으로 웹앱과 동일한 이름의 새로운 Application Insights 리소스가 사용됩니다. 기존 Application Insights 리소스를 사용하거나 이름을 변경할 수 있습니다. 아래쪽에서 **적용** 을 클릭합니다

#### <a name="azure-cli"></a>Azure CLI

Azure CLI 통해 사용하도록 설정하려면 Application Insights 리소스를 만들고 Azure 포털에서 몇 가지 앱 설정을 지정하여 웹앱에 Application Insights를 연결해야 합니다.

1. Application Insights 확장 사용

    ```bash
    az extension add -n application-insights
    ```

2. 아래 CLI 명령을 사용해 Application Insights 리소스를 만듭니다. 자리 표시자를 원하는 리소스 이름 및 그룹으로 바꿉니다.

    ```bash
    az monitor app-insights component create --app <resource-name> -g <resource-group> --location westus2  --kind web --application-type web
    ```

    다음 단계에서 `connectionString` 및 `instrumentationKey` 값이 필요하므로 이 값을 메모해두십시오.

    > 기타 위치의 목록을 검색하려면 `az account list-locations`을 실행하십시오.

::: zone pivot="platform-windows"
    
3. 계측 키, 연결 문자열 및 모니터링 에이전트 버전을 웹앱의 앱 설정으로 설정합니다. `<instrumentationKey>` 및 `<connectionString>`를 이전 단계의 값으로 바꿉니다.

    ```bash
    az webapp config appsettings set -n <webapp-name> -g <resource-group> --settings "APPINSIGHTS_INSTRUMENTATIONKEY=<instrumentationKey>" "APPLICATIONINSIGHTS_CONNECTION_STRING=<connectionString>" "ApplicationInsightsAgent_EXTENSION_VERSION=~3" "XDT_MicrosoftApplicationInsights_Mode=default" "XDT_MicrosoftApplicationInsights_Java=1"
    ```

::: zone-end
::: zone pivot="platform-linux"
    
3. 계측 키, 연결 문자열 및 모니터링 에이전트 버전을 웹앱의 앱 설정으로 설정합니다. `<instrumentationKey>` 및 `<connectionString>`를 이전 단계의 값으로 바꿉니다.

    ```bash
    az webapp config appsettings set -n <webapp-name> -g <resource-group> --settings "APPINSIGHTS_INSTRUMENTATIONKEY=<instrumentationKey>" "APPLICATIONINSIGHTS_CONNECTION_STRING=<connectionString>" "ApplicationInsightsAgent_EXTENSION_VERSION=~3" "XDT_MicrosoftApplicationInsights_Mode=default"
    ```

::: zone-end

### <a name="configure-new-relic"></a>New Relic 구성

::: zone pivot="platform-windows"

1. [NewRelic.com](https://newrelic.com/signup)에서 NewRelic 계정 만들기
2. NewRelic에서 Java 에이전트를 다운로드합니다. 파일 이름은 *newrelic-java-x.x.x.zip* 과 유사합니다.
3. 라이선스 키를 복사합니다. 이 키는 나중에 에이전트를 구성하는 데 필요합니다.
4. [App Service 인스턴스에 대해 SSH를 실행](configure-linux-open-ssh-session.md)하고 새 디렉터리 */home/site/wwwroot/apm* 을 만듭니다.
5. 압축이 풀린 NewRelic Java 에이전트 파일을 */home/site/wwwroot/apm* 아래 디렉터리에 업로드합니다. 에이전트의 파일은 */home/site/wwwroot/apm/newrelic* 에 있어야 합니다.
6. */home/site/wwwroot/apm/newrelic/newrelic.yml* 에서 YAML 파일을 수정하고 자리 표시자 라이선스 값을 사용자의 라이선스 키로 바꿉니다.
7. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.

    - **Java SE** 앱의 경우 값이 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`인 `JAVA_OPTS`라는 환경 변수를 만듭니다.
    - **Tomcat** 의 경우 값이 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`인 `CATALINA_OPTS`라는 환경 변수를 만듭니다.

::: zone-end
::: zone pivot="platform-linux"

1. [NewRelic.com](https://newrelic.com/signup)에서 NewRelic 계정 만들기
2. NewRelic에서 Java 에이전트를 다운로드합니다. 파일 이름은 *newrelic-java-x.x.x.zip* 과 유사합니다.
3. 라이선스 키를 복사합니다. 이 키는 나중에 에이전트를 구성하는 데 필요합니다.
4. [App Service 인스턴스에 대해 SSH를 실행](configure-linux-open-ssh-session.md)하고 새 디렉터리 */home/site/wwwroot/apm* 을 만듭니다.
5. 압축이 풀린 NewRelic Java 에이전트 파일을 */home/site/wwwroot/apm* 아래 디렉터리에 업로드합니다. 에이전트의 파일은 */home/site/wwwroot/apm/newrelic* 에 있어야 합니다.
6. */home/site/wwwroot/apm/newrelic/newrelic.yml* 에서 YAML 파일을 수정하고 자리 표시자 라이선스 값을 사용자의 라이선스 키로 바꿉니다.
7. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.
   
    - **Java SE** 앱의 경우 값이 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`인 `JAVA_OPTS`라는 환경 변수를 만듭니다.
    - **Tomcat** 의 경우 값이 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`인 `CATALINA_OPTS`라는 환경 변수를 만듭니다.

::: zone-end

>  `JAVA_OPTS` 또는 `CATALINA_OPTS`에 대한 환경 변수가 이미 있는 경우 현재 값의 끝에 `-javaagent:/...` 옵션을 추가합니다.

### <a name="configure-appdynamics"></a>AppDynamics 구성

::: zone pivot="platform-windows"

1. [AppDynamics.com](https://www.appdynamics.com/community/register/)에서 AppDynamics 계정 만들기
2. AppDynamics 웹 사이트에서 Java 에이전트를 다운로드하면 파일 이름은 *AppServerAgent-x.x.x.xxxxx.zip* 과 유사합니다.
3. [Kudu 콘솔](https://github.com/projectkudu/kudu/wiki/Kudu-console)을 사용하여 새 디렉터리 */home/site/wwwroot/apm* 을 만듭니다.
4. Java 에이전트 파일을 */home/site/wwwroot/apm* 아래의 디렉터리에 업로드합니다. 에이전트의 파일은 */home/site/wwwroot/apm/appdynamics* 에 있어야 합니다.
5. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.

   - **Java SE** 앱의 경우 값이 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`인 `JAVA_OPTS`라는 환경 변수를 만듭니다. 여기서 `<app-name>`은 App Service 이름입니다.
   - **Tomcat** 앱의 경우 값이 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`인 `CATALINA_OPTS`라는 환경 변수를 만듭니다. 여기서 `<app-name>`은 App Service 이름입니다.

::: zone-end
::: zone pivot="platform-linux"

1. [AppDynamics.com](https://www.appdynamics.com/community/register/)에서 AppDynamics 계정 만들기
2. AppDynamics 웹 사이트에서 Java 에이전트를 다운로드하면 파일 이름은 *AppServerAgent-x.x.x.xxxxx.zip* 과 유사합니다.
3. [App Service 인스턴스에 대해 SSH를 실행](configure-linux-open-ssh-session.md)하고 새 디렉터리 */home/site/wwwroot/apm* 을 만듭니다.
4. Java 에이전트 파일을 */home/site/wwwroot/apm* 아래의 디렉터리에 업로드합니다. 에이전트의 파일은 */home/site/wwwroot/apm/appdynamics* 에 있어야 합니다.
5. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.

   - **Java SE** 앱의 경우 값이 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`인 `JAVA_OPTS`라는 환경 변수를 만듭니다. 여기서 `<app-name>`은 App Service 이름입니다.
   - **Tomcat** 앱의 경우 값이 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`인 `CATALINA_OPTS`라는 환경 변수를 만듭니다. 여기서 `<app-name>`은 App Service 이름입니다.

::: zone-end

> [!NOTE]
>  `JAVA_OPTS` 또는 `CATALINA_OPTS`에 대한 환경 변수가 이미 있는 경우 현재 값의 끝에 `-javaagent:/...` 옵션을 추가합니다.

## <a name="configure-data-sources"></a>데이터 원본 구성

### <a name="java-se"></a>Java SE

Spring Boot 애플리케이션에서 데이터 원본에 연결하려면 연결 문자열을 만들어 *application.properties* 파일에 삽입하는 것이 좋습니다.

1. App Service 페이지의 "구성" 섹션에서 문자열의 이름을 설정하고, JDBC 연결 문자열을 값 필드에 붙여넣고, 유형을 "사용자 지정"으로 설정합니다. 필요에 따라 이 연결 문자열을 슬롯 설정으로 설정할 수 있습니다.

    이 연결 문자열은 `CUSTOMCONNSTR_<your-string-name>`라는 환경 변수로 애플리케이션에서 액세스할 수 있습니다. 예를 들어 위에서 만든 연결 문자열의 이름은 `CUSTOMCONNSTR_exampledb`로 지정됩니다.

2. *application.properties* 파일에서 환경 변수 이름을 사용하여 이 연결 문자열을 참조합니다. 이 예에서는 다음을 사용합니다.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

이 항목에 대한 자세한 내용은 [데이터 액세스에 대한 Spring Boot 설명서](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) 및 [표면화된 구성](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)을 참조하세요.

::: zone pivot="platform-windows"

### <a name="tomcat"></a>Tomcat

이러한 지침은 데이터베이스 연결에 적용됩니다. 선택한 데이터베이스의 드라이버 클래스 이름 및 JAR 파일로 자리 표시자를 채워야 합니다. 공통 데이터베이스에 대한 클래스 이름 및 드라이버 다운로드가 포함된 표가 제공됩니다.

| 데이터베이스   | 드라이버 클래스 이름                             | JDBC 드라이버                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [다운로드](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [다운로드](https://dev.mysql.com/downloads/connector/j/)(“플랫폼 독립적” 선택) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [다운로드](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

JDBC(Java Database Connectivity) 또는 JPA(Java Persistence API)를 사용하도록 Tomcat을 구성하려면 먼저 시작 시 Tomcat에서 읽은 `CATALINA_OPTS` 환경 변수를 사용자 지정합니다. [App Service Maven 플러그 인](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)에서 앱 설정을 통해 이러한 값을 설정합니다.

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

또는 Azure Portal의 **구성** > **애플리케이션 설정** 페이지에서 환경 변수를 설정합니다.

다음으로, 데이터 원본을 한 애플리케이션에만 제공할 것인지 또는 Tomcat 서블릿에서 실행 중인 모든 애플리케이션에 제공할 것인지 결정합니다.

#### <a name="application-level-data-sources"></a>애플리케이션 수준 데이터 원본

1. 프로젝트의 *META-INF/* 디렉터리에 *context.xml* 파일을 만듭니다. *META-INF/* 디렉터리가 없으면 디렉터리를 만듭니다.

2. *context.xml* 에서는 데이터 원본을 JNDI 주소에 연결하는 `Context` 요소를 추가합니다. `driverClassName` 자리 표시자를 위 테이블에 있는 드라이버의 클래스 이름으로 바꿉니다.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. 애플리케이션의 데이터 원본을 사용하도록 애플리케이션의 *web.xml* 을 업데이트합니다.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>공유 서버 수준 리소스

Windows의 App Service에 설치된 Tomcat은 App Service 플랜의 공유 공간에 존재합니다. 서버 차원의 구성에 대해서는 Tomcat 설치를 직접 수정할 수 없습니다. Tomcat 설치에 대한 서버 수준 구성을 변경하려면 Tomcat를 로컬 폴더에 복사해야 합니다. 여기에서 Tomcat의 구성을 수정할 수 있습니다. 

##### <a name="automate-creating-custom-tomcat-on-app-start"></a>앱 시작 시 사용자 지정 Tomcat 만들기 자동화

웹앱이 시작되기 전에 시작 스크립트를 사용하여 작업을 수행할 수 있습니다. Tomcat를 사용자 지정하기 위한 시작 스크립트는 다음 단계를 완료해야 합니다.

1. Tomcat이 이미 복사되고 로컬로 구성되었는지 확인합니다. 그런 경우 시작 스크립트가 여기에서 끝날 수 있습니다.
2. Tomcat을 로컬로 복사합니다.
3. 필요한 구성 변경을 수행합니다.
4. 구성이 성공적으로 완료되었음을 표시합니다.

Windows 사이트의 경우, `wwwroot` 디렉터리에 `startup.cmd` 또는 `startup.ps1`라는 파일을 생성합니다. Tomcat 서버가 시작되기 전에 자동으로 실행됩니다.

다음은 이러한 단계를 완료하는 PowerShell 스크립트입니다.

```powershell
    # Check for marker file indicating that config has already been done
    if(Test-Path "$Env:LOCAL_EXPANDED\tomcat\config_done_marker"){
        return 0
    }

    # Delete previous Tomcat directory if it exists
    # In case previous config could not be completed or a new config should be forcefully installed
    if(Test-Path "$Env:LOCAL_EXPANDED\tomcat"){
        Remove-Item "$Env:LOCAL_EXPANDED\tomcat" --recurse
    }

    # Copy Tomcat to local
    # Using the environment variable $AZURE_TOMCAT90_HOME uses the 'default' version of Tomcat
    Copy-Item -Path "$Env:AZURE_TOMCAT90_HOME\*" -Destination "$Env:LOCAL_EXPANDED\tomcat" -Recurse

    # Perform the required customization of Tomcat
    {... customization ...}

    # Mark that the operation was a success
    New-Item -Path "$Env:LOCAL_EXPANDED\tomcat\config_done_marker" -ItemType File
```

##### <a name="transforms"></a>변형

Tomcat 버전을 사용자 지정하는 일반적인 사용 사례는 `server.xml`, `context.xml`, 또는 `web.xml` Tomcat 구성 파일을 수정하는 경우입니다. App Service는 플랫폼 기능을 제공하기 위해 이러한 파일을 이미 수정합니다. 이러한 기능을 계속 사용하려면 해당 파일을 변경할 때 이러한 파일의 내용을 유지하는 것이 중요합니다. 이를 위해서는 [XSLT (XSL 변환)](https://www.w3schools.com/xml/xsl_intro.asp)를 사용하는 것을 권합니다. XSL 변환을 사용하여 파일의 기존 콘텐츠를 유지하면서 XML 파일을 변경할 수 있습니다.

###### <a name="example-xslt-file"></a>XSLT 파일 예제

이 예제 변환은 새 커넥터 노드를 `server.xml`에 추가합니다. 파일의 기존 콘텐츠를 유지하는 *ID 변환* 을 메모하십시오.

```xml
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:output method="xml" indent="yes"/>
  
    <!-- Identity transform: this ensures that the original contents of the file are included in the new file -->
    <!-- Ensure that your transform files include this block -->
    <xsl:template match="@* | node()" name="Copy">
      <xsl:copy>
        <xsl:apply-templates select="@* | node()"/>
      </xsl:copy>
    </xsl:template>
  
    <xsl:template match="@* | node()" mode="insertConnector">
      <xsl:call-template name="Copy" />
    </xsl:template>
  
    <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                   contains(., '&lt;Connector') and
                                   (contains(., 'scheme=&quot;https&quot;') or
                                    contains(., &quot;scheme='https'&quot;))]">
      <xsl:value-of select="." disable-output-escaping="yes" />
    </xsl:template>
  
    <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                     comment()[contains(., '&lt;Connector') and
                                               (contains(., 'scheme=&quot;https&quot;') or
                                                contains(., &quot;scheme='https'&quot;))]
                                    )]
                        ">
      <xsl:copy>
        <xsl:apply-templates select="@* | node()" mode="insertConnector" />
      </xsl:copy>
    </xsl:template>
  
    <!-- Add the new connector after the last existing Connnector if there is one -->
    <xsl:template match="Connector[last()]" mode="insertConnector">
      <xsl:call-template name="Copy" />
  
      <xsl:call-template name="AddConnector" />
    </xsl:template>
  
    <!-- ... or before the first Engine if there is no existing Connector -->
    <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                  mode="insertConnector">
      <xsl:call-template name="AddConnector" />
  
      <xsl:call-template name="Copy" />
    </xsl:template>
  
    <xsl:template name="AddConnector">
      <!-- Add new line -->
      <xsl:text>&#xa;</xsl:text>
      <!-- This is the new connector -->
      <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
                 maxThreads="150" scheme="https" secure="true" 
                 keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
                 clientAuth="false" sslProtocol="TLS" />
    </xsl:template>

    </xsl:stylesheet>
```

###### <a name="function-for-xsl-transform"></a>XSL 변환에 대한 함수

PowerShell에는 XSL 변환을 사용하여 XML 파일을 변환하는 기본 제공 도구가 있습니다. 다음 스크립트는 `startup.ps1`에서 변환을 수행하는 데 사용할 수 있는 예제 함수입니다.

```powershell
    function TransformXML{
        param ($xml, $xsl, $output)

        if (-not $xml -or -not $xsl -or -not $output)
        {
            return 0
        }

        Try
        {
            $xslt_settings = New-Object System.Xml.Xsl.XsltSettings;
            $XmlUrlResolver = New-Object System.Xml.XmlUrlResolver;
            $xslt_settings.EnableScript = 1;

            $xslt = New-Object System.Xml.Xsl.XslCompiledTransform;
            $xslt.Load($xsl,$xslt_settings,$XmlUrlResolver);
            $xslt.Transform($xml, $output);

        }

        Catch
        {
            $ErrorMessage = $_.Exception.Message
            $FailedItem = $_.Exception.ItemName
            Write-Host  'Error'$ErrorMessage':'$FailedItem':' $_.Exception;
            return 0
        }
        return 1
    }
```

##### <a name="app-settings"></a>앱 설정

플랫폼은 사용자 지정 버전의 Tomcat이 설치된 위치를 알고 있어야 합니다. 설치 위치를 `CATALINA_BASE` 앱 설정에서 지정할 수 있습니다.

이 설정 변경을 위해 Azure CLI를 사용할 수도 있습니다.

```powershell
    az webapp config appsettings set -g $MyResourceGroup -n $MyUniqueApp --settings CATALINA_BASE="%LOCAL_EXPANDED%\tomcat"
```

또는 Azure Portal에서 설정을 수동으로 변경할 수도 있습니다.

1. 메뉴에서 **설정** > **구성** > **애플리케이션 설정** 으로 이동합니다.
1. 메뉴에서 **새로운 애플리케이션 설정** 을 선택합니다.
1. 다음 값을 사용하여 설정을 만듭니다.
   1. **이름**: `CATALINA_BASE`
   1. **값**: `"%LOCAL_EXPANDED%\tomcat"`

##### <a name="example-startupps1"></a>예제 startup.ps1

다음 예제 스크립트는 사용자 지정 Tomcat을 로컬 폴더에 복사하고, XSL 변환을 수행하며, 변환이 성공했음을 표시합니다.

```powershell
    # Locations of xml and xsl files
    $target_xml="$Env:LOCAL_EXPANDED\tomcat\conf\server.xml"
    $target_xsl="$Env:HOME\site\server.xsl"
    
    # Define the transform function
    # Useful if transforming multiple files
    function TransformXML{
        param ($xml, $xsl, $output)
    
        if (-not $xml -or -not $xsl -or -not $output)
        {
            return 0
        }
    
        Try
        {
            $xslt_settings = New-Object System.Xml.Xsl.XsltSettings;
            $XmlUrlResolver = New-Object System.Xml.XmlUrlResolver;
            $xslt_settings.EnableScript = 1;
    
            $xslt = New-Object System.Xml.Xsl.XslCompiledTransform;
            $xslt.Load($xsl,$xslt_settings,$XmlUrlResolver);
            $xslt.Transform($xml, $output);
        }
    
        Catch
        {
            $ErrorMessage = $_.Exception.Message
            $FailedItem = $_.Exception.ItemName
            echo  'Error'$ErrorMessage':'$FailedItem':' $_.Exception;
            return 0
        }
        return 1
    }
    
    $success = TransformXML -xml $target_xml -xsl $target_xsl -output $target_xml
    
    # Check for marker file indicating that config has already been done
    if(Test-Path "$Env:LOCAL_EXPANDED\tomcat\config_done_marker"){
        return 0
    }
    
    # Delete previous Tomcat directory if it exists
    # In case previous config could not be completed or a new config should be forcefully installed
    if(Test-Path "$Env:LOCAL_EXPANDED\tomcat"){
        Remove-Item "$Env:LOCAL_EXPANDED\tomcat" --recurse
    }
    
    md -Path "$Env:LOCAL_EXPANDED\tomcat"
    
    # Copy Tomcat to local
    # Using the environment variable $AZURE_TOMCAT90_HOME uses the 'default' version of Tomcat
    Copy-Item -Path "$Env:AZURE_TOMCAT90_HOME\*" "$Env:LOCAL_EXPANDED\tomcat" -Recurse
    
    # Perform the required customization of Tomcat
    $success = TransformXML -xml $target_xml -xsl $target_xsl -output $target_xml
    
    # Mark that the operation was a success if successful
    if($success){
        New-Item -Path "$Env:LOCAL_EXPANDED\tomcat\config_done_marker" -ItemType File
    }
```

#### <a name="finalize-configuration"></a>구성 완료

마지막으로 Tomcat 클래스 경로에 드라이버 JAR을 배치하고 App Service를 다시 시작합니다. JDBC 드라이버 파일을 Tomcat classloader에 사용할 수 있도록 */home/tomcat/lib* 디렉터리에 배치합니다. (아직 존재하지 않는 경우 이 디렉터리를 만듭니다.) 이러한 파일을 App Service 인스턴스에 업로드하려면 다음 단계를 수행합니다.

1. [Cloud Shell](https://shell.azure.com)에서 웹앱 확장을 설치합니다.

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. 다음 CLI 명령을 실행하여 로컬 시스템에서 App Service로 SSH 터널을 만듭니다.

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. SFTP 클라이언트를 사용하여 로컬 터널링 포트에 연결하고 파일을 */home/tomcat/lib* 폴더에 업로드합니다.

또는 FTP 클라이언트를 사용하여 JDBC 드라이버를 업로드할 수 있습니다. [FTP 자격 증명을 가져오기 위한 이러한 지침](deploy-configure-credentials.md)을 따릅니다.

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="tomcat"></a>Tomcat

이러한 지침은 데이터베이스 연결에 적용됩니다. 선택한 데이터베이스의 드라이버 클래스 이름 및 JAR 파일로 자리 표시자를 채워야 합니다. 공통 데이터베이스에 대한 클래스 이름 및 드라이버 다운로드가 포함된 표가 제공됩니다.

| 데이터베이스   | 드라이버 클래스 이름                             | JDBC 드라이버                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [다운로드](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [다운로드](https://dev.mysql.com/downloads/connector/j/)(“플랫폼 독립적” 선택) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [다운로드](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

JDBC(Java Database Connectivity) 또는 JPA(Java Persistence API)를 사용하도록 Tomcat을 구성하려면 먼저 시작 시 Tomcat에서 읽은 `CATALINA_OPTS` 환경 변수를 사용자 지정합니다. [App Service Maven 플러그 인](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)에서 앱 설정을 통해 이러한 값을 설정합니다.

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

또는 Azure Portal의 **구성** > **애플리케이션 설정** 페이지에서 환경 변수를 설정합니다.

다음으로, 데이터 원본을 한 애플리케이션에만 제공할 것인지 또는 Tomcat 서블릿에서 실행 중인 모든 애플리케이션에 제공할 것인지 결정합니다.

#### <a name="application-level-data-sources"></a>애플리케이션 수준 데이터 원본

1. 프로젝트의 *META-INF/* 디렉터리에 *context.xml* 파일을 만듭니다. *META-INF/* 디렉터리가 없으면 디렉터리를 만듭니다.

2. *context.xml* 에서는 데이터 원본을 JNDI 주소에 연결하는 `Context` 요소를 추가합니다. `driverClassName` 자리 표시자를 위 테이블에 있는 드라이버의 클래스 이름으로 바꿉니다.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. 애플리케이션의 데이터 원본을 사용하도록 애플리케이션의 *web.xml* 을 업데이트합니다.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>공유 서버 수준 리소스

공유 서버 수준 데이터 원본을 추가하려면 Tomcat의 server.xml을 편집해야 합니다. 먼저 [시작 스크립트](/azure/app-service/faq-app-service-linux#built-in-images)를 업로드하고 **구성** > **시작 명령** 의 스크립트에 대한 경로를 설정합니다. [FTP](deploy-ftp.md)를 사용하여 시작 스크립트를 업로드할 수 있습니다.

시작 스크립트는 server.xml 파일의 [xsl 변환](https://www.w3schools.com/xml/xsl_intro.asp) 파일을 만들고 결과 xml 파일을 `/usr/local/tomcat/conf/server.xml`로 출력합니다. 시작 스크립트는 apk를 통해 libxslt를 설치해야 합니다. FTP를 통해 xsl 파일 및 시작 스크립트를 업로드할 수 있습니다. 다음은 예제 시작 스크립트입니다.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

예제 xsl 파일은 아래에 제공됩니다. 예제 xsl 파일은 Tomcat server.xml에 새 커넥터 노드를 추가합니다.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>구성 완료

마지막으로 Tomcat 클래스 경로에 드라이버 JAR을 배치하고 App Service를 다시 시작합니다.

1. JDBC 드라이버 파일을 Tomcat classloader에 사용할 수 있도록 */home/tomcat/lib* 디렉터리에 배치합니다. (아직 존재하지 않는 경우 이 디렉터리를 만듭니다.) 이러한 파일을 App Service 인스턴스에 업로드하려면 다음 단계를 수행합니다.

    1. [Cloud Shell](https://shell.azure.com)에서 웹앱 확장을 설치합니다.

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. 다음 CLI 명령을 실행하여 로컬 시스템에서 App Service로 SSH 터널을 만듭니다.

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. SFTP 클라이언트를 사용하여 로컬 터널링 포트에 연결하고 파일을 */home/tomcat/lib* 폴더에 업로드합니다.

    또는 FTP 클라이언트를 사용하여 JDBC 드라이버를 업로드할 수 있습니다. [FTP 자격 증명을 가져오기 위한 이러한 지침](deploy-configure-credentials.md)을 따릅니다.

2. 서버 수준 데이터 원본을 만든 경우 App Service Linux 애플리케이션을 다시 시작합니다. Tomcat이 `CATALINA_BASE`을 `/home/tomcat`로 다시 설정하고 업데이트된 구성을 사용합니다.

### <a name="jboss-eap"></a>JBoss EAP

[JBoss EAP에 데이터 원본을 등록할](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management) 때 세 가지 핵심 단계는 JDBC 드라이버 업로드, JDBC 드라이버를 모듈로 추가 및 모듈 등록입니다. App Service는 상태 비저장 호스팅 서비스이므로, 컨테이너가 시작될 때 데이터 원본 모듈을 추가하고 등록하는 구성 명령을 스크립팅하고 적용해야 합니다.

1. 데이터베이스의 JDBC 드라이버를 가져옵니다. 
2. JDBC 드라이버에 대한 XML 모듈 정의 파일을 만듭니다. 아래에 표시된 예제는 PostgreSQL에 대한 모듈 정의입니다.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.postgres">
        <resources>
        <!-- ***** IMPORTANT : REPLACE THIS PLACEHOLDER *******-->
        <resource-root path="/home/site/deployments/tools/postgresql-42.2.12.jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. JBoss CLI 명령을 `jboss-cli-commands.cli`라는 파일에 배치합니다. JBoss 명령은 모듈을 추가하고 이를 데이터 원본으로 등록해야 합니다. 아래 예제에서는 PostgreSQL에 대한 JBoss CLI 명령을 보여 줍니다.

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. JBoss CLI 명령을 호출하는 시작 스크립트, `startup_script.sh`를 만듭니다. 아래의 예제에서는 `jboss-cli-commands.cli`를 호출하는 방법을 보여 줍니다. 나중에 컨테이너가 시작될 때 이 스크립트를 실행하도록 App Service를 구성합니다. 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. 원하는 FTP 클라이언트를 사용하여 JDBC 드라이버, `jboss-cli-commands.cli`, `startup_script.sh` 및 모듈 정의를 `/site/deployments/tools/`에 업로드합니다.
2. 컨테이너가 시작될 때 `startup_script.sh`를 실행하도록 사이트를 구성합니다. Azure Portal에서 **구성** > **일반 설정** > **시작 명령** 으로 이동합니다. 시작 명령 필드를 `/home/site/deployments/tools/startup_script.sh`로 설정합니다. 변경 내용을 **저장** 합니다.

데이터 원본이 JBoss 서버에 추가되었는지 확인하려면 웹앱에 SSH를 사용하고 `$JBOSS_HOME/bin/jboss-cli.sh --connect`를 실행합니다. JBoss에 연결되면 `/subsystem=datasources:read-resource`를 실행하여 데이터 원본 목록을 인쇄합니다.

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>Java 런타임 버전 선택

App Service를 통해 사용자는 JVM의 주 버전(예: Java 8 또는 Java 11) 및 부 버전(예: 1.8.0_232 또는 11.0.5)을 선택할 수 있습니다. 새 부 버전을 사용할 수 있게 되면 부 버전이 자동으로 업데이트되도록 선택할 수도 있습니다. 대부분의 경우 프로덕션 사이트는 고정된 부 JVM 버전을 사용해야 합니다. 이렇게 하면 부 버전 자동 업데이트 중에 발생하는 중단이 발생하지 않습니다. 모든 Java Wep Apps는 64비트 JVM을 사용하며 이는 구성이 불가능합니다.

부 버전을 고정하도록 선택하는 경우 사이트에서 JVM 부 버전을 주기적으로 업데이트해야 합니다. 최신 부 버전에서 애플리케이션을 실행하려면 스테이징 슬롯을 만들고 준스테이징 사이트에서 부 버전을 증가시킵니다. 새 부 버전에서 애플리케이션이 올바르게 실행되었는지 확인한 후에는 스테이징 및 프로덕션 슬롯을 교환할 수 있습니다.

::: zone pivot="platform-linux"

## <a name="jboss-eap-app-service-plans"></a>JBoss EAP App Service 플랜
<a id="jboss-eap-hardware-options"></a>

JBoss EAP는 프리미엄 v3 및 격리 v2 App Service 플랜 유형에서만 사용할 수 있습니다. 공개 미리 보기 중 제공되는 무료, 공유, 기본 또는 표준 계층에서 JBoss EAP 사이트를 만든 고객은 예기치 않은 동작을 방지하기 위해 프리미엄 또는 격리된 하드웨어 계층으로 확장해야 합니다.

::: zone-end

## <a name="java-runtime-statement-of-support"></a>Java 런타임 문 지원

### <a name="jdk-versions-and-maintenance"></a>JDK 버전 및 유지 관리

Azure에서 지원되는 JDK(Java Development Kit)는 [Azul Systems](https://www.azul.com/)를 통해 제공하는 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)입니다. OpenJDK의 Azul Zulu Enterprise 빌드는 Microsoft와 Azul Systems가 후원하는 Azure 및 Azure Stack에 대한 OpenJDK의 무료 다중 플랫폼 프로덕션 준비 배포입니다. 여기에는 Java SE 애플리케이션을 빌드하고 실행하기 위한 모든 구성 요소가 포함됩니다. [Java JDK 설치](/azure/developer/java/fundamentals/java-support-on-azure)에서 JDK를 설치할 수 있습니다.

주 버전 업데이트는 Azure App Service에서 새 런타임 옵션을 통해 제공될 예정입니다. 고객은 App Service 배포를 구성하여 최신 버전의 Java로 업데이트해야 하며, 주 업데이트를 테스트하고 요구 사항을 충족하도록 관리할 책임이 있습니다.

지원되는 JDK는 매년 분기마다 1월, 4월, 7월, 10월에 자동으로 패치됩니다. Azure의 Java에 대한 자세한 내용은 [이 지원 문서](/azure/developer/java/fundamentals/java-support-on-azure)를 참조하세요.

### <a name="security-updates"></a>보안 업데이트

Azul Systems에서 주요 보안 취약점에 대한 패치 및 수정 사항을 출시하는 즉시 고객에게 제공됩니다. [NIST Common Vulnerability Scoring System 버전 2](https://nvd.nist.gov/vuln-metrics/cvss)에서 기본 점수 9.0 이상을 받으면 "주요" 취약점으로 정의됩니다.

Tomcat 8.0이 [2018년 9월 30일 현재 EOL(수명 종료)](https://tomcat.apache.org/tomcat-80-eol.html)에 도달했습니다. Azure App Service에서 런타임을 계속 사용할 수 있지만 Azure는 Tomcat 8.0에 보안 업데이트를 적용하지 않습니다. 가능하면 애플리케이션을 Tomcat 8.5 또는 9.0으로 마이그레이션합니다. Tomcat 8.5 및 9.0은 모두 Azure App Service에서 사용할 수 있습니다. 자세한 내용은 [공식 Tomcat 사이트](https://tomcat.apache.org/whichversion.html)를 참조하세요. 

### <a name="deprecation-and-retirement"></a>사용 중단 및 사용 중지

지원되는 Java 런타임이 폐기되는 경우 폐기 예정인 런타임을 사용하는 Azure 개발자에게는 적어도 런타임 폐기 6개월 전에 사용 중단 알림이 제공됩니다.


### <a name="local-development"></a>로컬 개발

개발자는 [Azul의 다운로드 사이트](https://www.azul.com/downloads/azure-only/zulu/)에서 로컬 개발용 Azul Zulu Enterprise JDK의 프로덕션 버전을 다운로드할 수 있습니다.

### <a name="development-support"></a>개발 지원

[정규화된 Azure 지원 계획](https://azure.microsoft.com/support/plans/)을 사용하여 Azure 또는 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/)용 제품을 개발하는 경우 Microsoft를 통해 [Azure에서 지원하는 Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/)에 대한 제품 지원을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Java 개발자용 Azure](/java/azure/) 센터를 방문하여 Azure 빠른 시작, 자습서 및 Java 참조 설명서를 찾아보세요.

- [App Service Linux FAQ](faq-app-service-linux.yml)
- [환경 변수 및 앱 설정 참조](reference-app-settings.md)
