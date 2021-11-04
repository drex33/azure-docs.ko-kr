---
title: JVM 인수 추가 - Java용 Azure Monitor Application Insights
description: Java용 Azure Monitor Application Insights를 활성화하는 JVM 인수를 추가하는 방법
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 88721d4545625d11614075db77ee61df46cbe9ef
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470379"
---
# <a name="tips-for-updating-your-jvm-args---azure-monitor-application-insights-for-java"></a>JVM args 업데이트에 대한 팁 - Java용 애플리케이션 Insights Azure Monitor

## <a name="azure-environments"></a>Azure 환경

[App Services](../../app-service/configure-language-java.md#set-java-runtime-options)를 구성합니다.

## <a name="spring-boot"></a>Spring Boot

`-jar` 앞 어딘가에 JVM 인수 `-javaagent:path/to/applicationinsights-agent-3.2.2.jar`을 추가합니다. 예를 들면 다음과 같습니다.

```
java -javaagent:path/to/applicationinsights-agent-3.2.2.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Docker 진입점을 통한 Spring Boot

*exec* 양식을 사용하는 경우 `"-jar"` 매개 변수 앞의 매개 변수 목록에 매개 변수 `"-javaagent:path/to/applicationinsights-agent-3.2.2.jar"`을 추가합니다. 예를 들면 다음과 같습니다.

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.2.2.jar", "-jar", "<myapp.jar>"]
```

*shell* 양식을 사용하는 경우 `-jar` 앞 어딘가에 JVM 인수 `-javaagent:path/to/applicationinsights-agent-3.2.2.jar`을 추가합니다. 예를 들면 다음과 같습니다.

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.2.2.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8(Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>`apt-get` 또는 `yum`을 통해 설치된 Tomcat

`apt-get` 또는 `yum`을 통해 Tomcat를 설치한 경우에는 `/etc/tomcat8/tomcat8.conf` 파일이 있어야 합니다.  해당 파일 끝에 다음 줄을 추가합니다.

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.2.2.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>다운로드 및 압축 해제를 통해 설치된 Tomcat

[https://tomcat.apache.org](https://tomcat.apache.org)에서 다운로드 및 압축 해제를 통해 Tomcat을 설치한 경우에는 `<tomcat>/bin/catalina.sh` 파일이 있어야 합니다.  다음 내용이 포함된 `<tomcat>/bin/setenv.sh`라는 동일한 디렉터리에 새 파일을 만듭니다.

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.2.2.jar"
```

파일 `<tomcat>/bin/setenv.sh`가 이미 있으면 해당 파일을 수정하고 `-javaagent:path/to/applicationinsights-agent-3.2.2.jar`을 `CATALINA_OPTS`에 추가합니다.


## <a name="tomcat-8-windows"></a>Tomcat 8(Windows)

### <a name="running-tomcat-from-the-command-line"></a>명령줄에서 Tomcat 실행

파일 `<tomcat>/bin/catalina.bat`를 찾습니다.  다음 내용이 포함된 `<tomcat>/bin/setenv.bat`라는 동일한 디렉터리에 새 파일을 만듭니다.

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.2.2.jar
```

따옴표는 필요하지는 않지만 이를 포함하려는 경우 적절한 배치는 다음과 같습니다.

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.2.2.jar"
```

파일 `<tomcat>/bin/setenv.bat`가 이미 있으면 해당 파일을 수정하고 `-javaagent:path/to/applicationinsights-agent-3.2.2.jar`을 `CATALINA_OPTS`에 추가하기만 하면 됩니다.

### <a name="running-tomcat-as-a-windows-service"></a>Windows 서비스로 Tomcat 실행

파일 `<tomcat>/bin/tomcat8w.exe`를 찾습니다.  해당 실행 파일을 실행하고 `Java` 탭 아래의 `Java Options`에 `-javaagent:path/to/applicationinsights-agent-3.2.2.jar`을 추가합니다.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>독립 실행형 서버

파일 `JBOSS_HOME/bin/standalone.conf`(Linux) 또는 `JBOSS_HOME/bin/standalone.conf.bat`(Windows)의 기존 `JAVA_OPTS` 환경 변수에 `-javaagent:path/to/applicationinsights-agent-3.2.2.jar`을 추가합니다.

```java    ...
    JAVA_OPTS="-javaagent:path/to/applicationinsights-agent-3.2.2.jar -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>도메인 서버

`JBOSS_HOME/domain/configuration/host.xml`의 기존 `jvm-options`에 `-javaagent:path/to/applicationinsights-agent-3.2.2.jar`을 추가합니다.

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.2.2.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

단일 호스트에서 여러 관리형 서버를 실행하는 경우 각 `server`에 대해 `system-properties`에 `applicationinsights.agent.id`를 추가해야 합니다.

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

지정된 `applicationinsights.agent.id` 값은 고유해야 합니다. 각 JVM 프로세스에는 자체 로컬 applicationinsights 구성 및 로컬 applicationinsights 로그 파일이 필요하므로 applicationinsights 디렉터리 아래에 하위 디렉터리를 만드는 데 사용됩니다. 또한 중앙 수집기에 보고하는 경우 `applicationinsights.properties` 파일은 여러 관리형 서버에서 공유되므로 지정된 `applicationinsights.agent.id`가 해당 공유 파일의 `agent.id` 설정을 재정의하는 데 필요합니다. 관리형 서버별로 `agent.rollup.id` 설정을 재정의해야 하는 경우 `applicationinsights.agent.rollup.id`는 서버의 `system-properties`에 비슷한 방식으로 지정할 수 있습니다.


## <a name="jetty-9"></a>Jetty 9

이러한 줄을 `start.ini`에 추가

```
--exec
-javaagent:path/to/applicationinsights-agent-3.2.2.jar
```


## <a name="payara-5"></a>Payara 5

`glassfish/domains/domain1/config/domain.xml`의 기존 `jvm-options`에 `-javaagent:path/to/applicationinsights-agent-3.2.2.jar`을 추가합니다.

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.2.2.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

관리 콘솔을 열어 **서버 > WebSphere 애플리케이션 서버 > 애플리케이션 서버** 로 이동하여 적절한 애플리케이션 서버를 선택하고 다음을 클릭합니다. 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
"일반 JVM 인수"에서 다음을 추가합니다.
```
-javaagent:path/to/applicationinsights-agent-3.2.2.jar
```
그런 다음, 애플리케이션 서버를 저장하고 다시 시작합니다.


## <a name="openliberty-18"></a>OpenLiberty 18

서버 디렉터리(예: `<openliberty>/usr/servers/defaultServer`)에 새 파일 `jvm.options`를 만들고 다음 줄을 추가합니다.
```
-javaagent:path/to/applicationinsights-agent-3.2.2.jar
```

## <a name="others"></a>Others

JVM args를 추가하는 방법은 애플리케이션 서버 설명서를 참조하세요.
