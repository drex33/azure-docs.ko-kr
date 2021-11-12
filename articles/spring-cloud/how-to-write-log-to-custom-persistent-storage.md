---
title: Logback을 사용 하 여 Azure 스프링 클라우드의 사용자 지정 영구 저장소에 로그를 기록 하는 방법 | Microsoft Docs
description: Logback을 사용 하 여 Azure 스프링 클라우드의 사용자 지정 영구 저장소에 로그를 기록 하는 방법입니다.
author: karlerickson
ms.author: xuycao
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: devx-track-java
ms.openlocfilehash: 558ca29f8faa03857d4debe760c4859ab43b17e5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404683"
---
# <a name="how-to-use-logback-to-write-logs-to-custom-persistent-storage"></a>Logback을 사용 하 여 사용자 지정 영구 저장소에 로그를 기록 하는 방법

**이 문서는 다음에 적용됩니다.** ✔️ Java

이 문서에서는 Logback을 로드 하 고 Azure 스프링 클라우드의 사용자 지정 영구 저장소에 로그를 기록 하는 방법을 보여 줍니다.

> [!NOTE]
> 응용 프로그램의 클래스 경로에 있는 파일에 다음 이름 중 하나가 있는 경우 스프링 부팅은 Logback의 기본 구성을 통해 자동으로 로드 합니다.
> - *logback-spring.xml*
> - *logback.xml*
> - *logback-groovy*
> - *logback. groovy*

## <a name="prerequisites"></a>필수 구성 요소

* Azure 스프링 클라우드 인스턴스에 바인딩된 기존 저장소 리소스입니다. 저장소 리소스를 바인딩해야 하 [는 경우 Azure 스프링 클라우드에서 고유한 영구 저장소를 사용 하도록 설정 하는 방법](./how-to-custom-persistent-storage.md)을 참조 하세요.
* 응용 프로그램에 포함 된 Logback 종속성입니다. Logback에 대 한 자세한 내용은 [Logback 가이드](https://www.baeldung.com/logback)를 참조 하세요.
* Azure CLI에 대 한 [Azure 스프링 클라우드 확장](/cli/azure/azure-cli-extensions-overview)

## <a name="edit-the-logback-configuration-to-write-logs-into-a-specific-path"></a>로그를 특정 경로에 쓰도록 Logback 구성 편집

logback-spring.xml 예제 파일을 사용 하 여 로그를 쓸 경로를 설정할 수 있습니다.

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

앞의 예제에는 `{LOGS}` 응용 프로그램의 로그를 쓸 경로에 라는 두 개의 자리 표시 자가 있습니다. `LOGS`로그를 콘솔과 영구 저장소에 모두 쓰려면 환경 변수에 값을 할당 해야 합니다. 

## <a name="use-the-azure-cli-to-create-and-deploy-logback-to-write-logs-to-persistent-storage"></a>Azure CLI를 사용 하 여 영구 저장소에 로그를 쓰도록 Logback을 만들고 배포 합니다.

1. 다음 명령을 사용 하 여 영구 저장소를 사용 하 고 환경 변수 집합을 사용 하 여 Azure 스프링 클라우드에서 응용 프로그램을 만듭니다.

   ```azurecli
   az spring-cloud app create \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name> \
        --persistent-storage <path-to-json-file> \
        --env LOGS=/byos/logs
   ```

    영구 저장소는 응용 프로그램에서 사용 되는 것과 동일한 경로에 탑재 해야 합니다. Create 명령의 매개 변수에 전달 되는 JSON 파일의 예는 다음과 같습니다 `--persistent-storage` . 위의 CLI 명령 및 아래 속성에서 환경 변수에 대해 동일한 값을 전달 해야 합니다 `mountPath` . 

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
  
1. 다음 명령을 사용 하 여 응용 프로그램을 배포 합니다.

   ```azurecli
   az spring-cloud app deploy \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name> \
        --jar-path <path-to-jar-file>
   ```

1. 다음 명령을 사용 하 여 응용 프로그램의 콘솔 로그를 확인 합니다.

   ```azurecli
   az spring-cloud app logs \
        --resource-group <resource-group-name> \
        --name <app-name> \
        --service <spring-instance-name>
   ```

    바인딩된 Azure Storage 계정 리소스로 이동 하 고 영구 저장소로 연결 된 Azure 파일 공유를 찾습니다. 이 예제에서 로그는 Azure 파일 공유의 홈 경로에 있는 *spring-boot-logger* 파일에 기록 됩니다. 모든 회전 된 로그 파일은 Azure 파일 공유의 */보관* 폴더에 저장 됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Spring Cloud의 구조적 애플리케이션 로그](./structured-app-log.md)
* [진단 설정을 사용 하 여 로그 및 메트릭 분석](./diagnostic-services.md)