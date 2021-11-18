---
title: Logback을 사용하여 Azure Spring Cloud | 사용자 지정 영구 스토리지에 로그를 쓰는 방법 Microsoft Docs
description: Logback을 사용하여 Azure Spring Cloud 사용자 지정 영구 스토리지에 로그를 쓰는 방법입니다.
author: karlerickson
ms.author: xuycao
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2021
ms.custom: devx-track-java
ms.openlocfilehash: 1ee2be50071c99123a6741102429d1fe28a58b47
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132761533"
---
# <a name="how-to-use-logback-to-write-logs-to-custom-persistent-storage"></a>Logback을 사용하여 사용자 지정 영구 스토리지에 로그를 쓰는 방법

**이 문서는 다음에 적용됩니다.** ✔️ Java

이 문서에서는 logback을 로드하고 Azure Spring Cloud 사용자 지정 영구 스토리지에 로그를 쓰는 방법을 보여줍니다.

> [!NOTE]
> 애플리케이션의 클래스 경로에 있는 파일에 다음 이름 중 하나가 있는 경우 Spring Boot Logback에 대한 기본 구성을 통해 자동으로 로드합니다.
> - *logback-spring.xml*
> - *logback.xml*
> - *logback-spring.groovy*
> - *logback.groovy*

## <a name="prerequisites"></a>사전 요구 사항

* Azure Spring Cloud 인스턴스에 바인딩된 기존 스토리지 리소스입니다. 스토리지 리소스를 바인딩해야 하는 경우 [Azure Spring Cloud 고유한 영구 스토리지를 사용하도록 설정하는 방법을 참조하세요.](./how-to-custom-persistent-storage.md)
* 애플리케이션에 포함된 Logback 종속성입니다. Logback에 대한 자세한 내용은 [로그백 가이드를](https://www.baeldung.com/logback)참조하세요.
* Azure CLI 대한 Azure Spring Cloud [확장](/cli/azure/azure-cli-extensions-overview)

## <a name="edit-the-logback-configuration-to-write-logs-into-a-specific-path"></a>로그백 구성을 편집하여 특정 경로에 로그 작성

logback-spring.xml 예제 파일을 사용하여 로그를 쓸 경로를 로 설정할 수 있습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <appender name="Console"
              class="ch.qos.logback.core.ConsoleAppender">
        <!-- please feel free to customize the log layout -->
        <layout class="ch.qos.logback.classic.PatternLayout">
            <Pattern>
                %black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %yellow(%C{1.}): %msg%n%throwable
            </Pattern>
        </layout>
    </appender>

    <appender name="RollingFile"
              class="ch.qos.logback.core.rolling.RollingFileAppender">
        <!-- 'LOGS' here is a value to be read from the application's environment variable -->
        <file>${LOGS}/spring-boot-logger.log</file>
        <!-- please feel free to customize the log layout pattern -->
        <encoder
                class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <Pattern>%d %p %C{1.} [%t] %m%n</Pattern>
        </encoder>

        <rollingPolicy
                class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!-- rollover daily and when the file reaches 10 MegaBytes -->
            <fileNamePattern>${LOGS}/archived/spring-boot-logger-%d{yyyy-MM-dd}.%i.log
            </fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy
                    class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
    </appender>

    <!-- LOG everything at the INFO level -->
    <root level="info">
        <appender-ref ref="RollingFile" />
        <appender-ref ref="Console" />
    </root>

    <!-- LOG "com.baeldung*" at the TRACE level -->
    <logger name="com.baeldung" level="trace" additivity="false">
        <appender-ref ref="RollingFile" />
        <appender-ref ref="Console" />
    </logger>

</configuration>
```

앞의 예제에서는 애플리케이션의 로그를 쓰기 위한 경로에 라는 두 자리 표시자가 `{LOGS}` 있습니다. `LOGS`콘솔과 영구 스토리지 모두에 로그를 쓰려면 환경 변수에 값을 할당해야 합니다. 

## <a name="use-the-azure-cli-to-create-and-deploy-a-new-app-with-logback-on-persistent-storage"></a>Azure CLI 사용하여 영구 스토리지에 Logback을 사용하여 새 앱 만들기 및 배포

1. 다음 명령을 사용하여 영구 스토리지를 사용하도록 설정하고 환경 변수를 설정한 Azure Spring Cloud 애플리케이션을 만듭니다.

   ```azurecli
   az spring-cloud app create \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name> \
        --persistent-storage <path-to-json-file> \
        --env LOGS=/byos/logs
   ```
   > [!NOTE]
   > 환경 변수의 값은 또는 `LOGS` 의 하위Directory와 같을 수 `mountPath` 있습니다.
    
    create 명령의 매개 변수에 전달되는 JSON 파일의 예는 다음과 `--persistent-storage` 같습니다. 이 예제에서는 위의 CLI 명령과 아래 속성의 환경 변수에 대해 동일한 값이 `mountPath` 전달됩니다. 

    ```json
    {
        "customPersistentDisks": [
            {
                "storageName": "<Storage-Resource-Name>",
                "customPersistentDiskProperties": {
                    "type": "AzureFileVolume",
                    "shareName": "<Azure-File-Share-Name>",
                    "mountPath": "/byos/logs",
                    "readOnly": false
                }
            }
        ]
    }
    ```
  
1. 다음 명령을 사용하여 애플리케이션을 배포합니다.

   ```azurecli
   az spring-cloud app deploy \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name> \
        --jar-path <path-to-jar-file>
   ```

1. 다음 명령을 사용하여 애플리케이션의 콘솔 로그를 확인합니다.

   ```azurecli
   az spring-cloud app logs \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name>
   ```

    바인딩한 Azure Storage 계정 리소스로 이동하여 영구 스토리지로 연결된 Azure 파일 공유를 찾습니다. 이 예제에서 로그는 Azure 파일 공유의 루트에 있는 *spring-boot-logger.log* 파일에 기록됩니다. 회전된 모든 로그 파일은 Azure 파일 공유의 */archived* 폴더에 저장됩니다.

1. 필요에 따라 다음 명령을 사용하여 기존 앱의 경로 또는 영구 스토리지를 업데이트합니다.

    로그가 저장되는 경로 또는 영구 스토리지는 언제든지 변경할 수 있습니다. 환경 변수 또는 영구 스토리지가 변경되면 애플리케이션이 다시 시작됩니다.

   ```azurecli
   az spring-cloud app update \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name> \
        --persistent-storage <path-to-new-json-file> \
        --env LOGS=<new-path>
   ```

## <a name="next-steps"></a>다음 단계

* [Azure Spring Cloud의 구조적 애플리케이션 로그](./structured-app-log.md)
* [진단 설정을 사용하여 로그 및 메트릭 분석](./diagnostic-services.md)
