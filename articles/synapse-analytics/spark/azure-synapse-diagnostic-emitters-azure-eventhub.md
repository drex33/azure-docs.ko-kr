---
title: Azure Event Hubs를 사용하여 Apache Spark 애플리케이션 로그 및 메트릭 수집(미리 보기)
description: 이 자습서에서는 Synapse Apache Spark 진단 내보내기 확장을 사용하여 Apache Spark 애플리케이션의 로그, 이벤트 로그 및 메트릭을 Azure Event Hubs으로 내보내는 방법에 대해 알아봅니다.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 08/31/2021
ms.openlocfilehash: c3b8fbda0eaaa932f784540cc55d0a4e583927f0
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544715"
---
# <a name="collect-your-apache-spark-applications-logs-and-metrics-using-azure-event-hubs-preview"></a>Azure Event Hubs를 사용하여 Apache Spark 애플리케이션 로그 및 메트릭 수집(미리 보기)

Synapse Apache Spark 진단 내보내기 확장은 Apache Spark 애플리케이션에서 로그, 이벤트 로그 및 메트릭을 Azure Log Analytics, Azure Storage 및 Azure Event Hubs를 포함한 하나 이상의 대상으로 내보낼 수 있도록 하는 라이브러리입니다. 

이 자습서에서는 Synapse Apache Spark 진단 내보내기 확장을 사용하여 Apache Spark 애플리케이션의 로그, 이벤트 로그 및 메트릭을 Azure Event Hubs으로 내보내는 방법에 대해 알아봅니다.

## <a name="collect-logs-and-metrics-to-azure-event-hubs"></a>Azure Event Hubs로 로그 및 메트릭 수집

### <a name="step-1-create-an-azure-event-hub-instance"></a>1단계: Azure Event Hub 인스턴스 만들기

Azure Event Hubs로 진단 로그 및 메트릭을 수집하기 위해 기존 Azure Event Hubs 인스턴스를 사용할 수 있습니다.
또는 이벤트 허브가 없는 경우 [이벤트 허브를 만들](../../event-hubs/event-hubs-create.md) 수 있습니다.

### <a name="step-2-create-a-apache-spark-configuration-file"></a>2단계: Apache Spark 구성 파일 만들기

`diagnostic-emitter-azure-event-hub-conf.txt`를 만들고, 다음 내용을 이 파일에 복사합니다. 또는 Apache Spark 풀을 구성하기 위한 [샘플 템플릿 파일](https://go.microsoft.com/fwlink/?linkid=2169375)을 다운로드합니다.

```
spark.synapse.diagnostic.emitters MyDestination1
spark.synapse.diagnostic.emitter.MyDestination1.type AzureEventHub
spark.synapse.diagnostic.emitter.MyDestination1.categories Log,EventLog,Metrics
spark.synapse.diagnostic.emitter.MyDestination1.secret <connection-string>
```

구성 파일에서 매개 변수 `<connection-string>`을 입력합니다.
매개 변수에 대한 자세한 설명은 [Azure EventHub 구성](#available-configurations)을 참조하세요.

### <a name="step-3-upload-the-apache-spark-configuration-file-to-apache-spark-pool"></a>3단계: Apache Spark 풀에 Apache Spark 구성 파일 업로드

1. Synapse Studio에서 Apache Spark 풀로 이동합니다 **(관리 -> Apache Spark 풀)** .
2. Apache Spark 풀의 오른쪽에 있는 **"..."** 단추를 클릭하고, **Apache Spark 구성** 을 선택합니다.
3. **업로드** 를 클릭하고, ".txt" 구성 파일을 선택하고 **적용** 을 클릭합니다.

## <a name="available-configurations"></a>사용 가능한 구성

| 구성                                                               | 설명                                                                                                                                                                                          |
| --------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `spark.synapse.diagnostic.emitters`                                         | 필수 사항입니다. 쉼표로 구분된 진단 내보내기의 대상 이름입니다.                                                                                                                              |
| `spark.synapse.diagnostic.emitter.<destination>.type`                       | 필수 사항입니다. 기본 제공 대상 유형입니다. Azure Event Hubs 대상을 사용하려면 값은 `AzureEventHub`이어야 합니다.                                                                                    |
| `spark.synapse.diagnostic.emitter.<destination>.categories`                 | 선택 사항입니다. 쉼표로 구분된 선택한 로그 범주입니다. 사용 가능한 값은 `DriverLog`, `ExecutorLog`, `EventLog`, `Metrics`입니다. 설정되지 않으면 기본값은 **모두** 범주입니다.              |
| `spark.synapse.diagnostic.emitter.<destination>.secret`                     | 선택 사항입니다. Azure Eventhub 인스턴스 연결 문자열입니다. 이 필드는 이 패턴 `Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>;EntityPath=<PathName>`과 일치해야 합니다. |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault`            | `.secret`이 지정되지 않은 경우 필수 사항입니다. 비밀(연결 문자열)이 저장되는 [Azure Key Vault](../../key-vault/general/overview.md) 이름입니다.                                                                  |
| `spark.synapse.diagnostic.emitter.<destination>.secret.keyVault.secretName` | `.secret.keyVault`를 지정하는 경우 필요합니다. 비밀(연결 문자열)이 저장되는 Azure Key Vault 비밀 이름입니다.                                                                         |
| `spark.synapse.diagnostic.emitter.<destination>.filter.eventName.match`     | 선택 사항입니다. 쉼표로 구분된 Spark 이벤트 이름이며, 수집할 이벤트를 지정할 수 있습니다. 예: `SparkListenerApplicationStart,SparkListenerApplicationEnd` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.loggerName.match`    | 선택 사항입니다. 쉼표로 구분된 log4j 로거 이름이며, 수집할 이벤트를 지정할 수 있습니다. 예: `org.apache.spark.SparkContext,org.example.Logger` |
| `spark.synapse.diagnostic.emitter.<destination>.filter.metricName.match`    | 선택 사항입니다. 쉼표로 구분된 Spark 메트릭 이름 접미사이며, 수집할 메트릭을 지정할 수 있습니다. 예: `jvm.heap.used` |


> [!NOTE]
>
> Azure Eventhub 인스턴스 연결 문자열에는 항상 Azure Event Hubs 인스턴스의 이름인 `EntityPath`가 포함되어야 합니다.

## <a name="log-data-sample"></a>로그 데이터 샘플

JSON 형식의 샘플 로그 레코드는 다음과 같습니다.

```json
{
    "timestamp": "2021-01-02T12:34:56.789Z",
    "category": "Log|EventLog|Metrics",
    "workspaceName": "<my-workspace-name>",
    "sparkPool": "<spark-pool-name>",
    "livyId": "<livy-session-id>",
    "applicationId": "<application-id>",
    "applicationName": "<application-name>",
    "executorId": "<driver-or-executor-id>",
    "properties": {
        // The message properties of logs, events and metrics.
        "timestamp": "2021-01-02T12:34:56.789Z",
        "message": "Registering signal handler for TERM",
        "logger_name": "org.apache.spark.util.SignalUtils",
        "level": "INFO",
        "thread_name": "main"
        // ...
    }
}
```

## <a name="synapse-workspace-with-data-exfiltration-protection-enabled"></a>데이터 반출 보호가 사용하도록 설정된 Synapse 작업 영역

Azure Synapse Analytics 작업 영역에서는 작업 영역에 대한 데이터 반출 보호 사용을 지원합니다. 반출 보호를 사용하면 로그와 메트릭을 대상 엔드포인트로 직접 보낼 수 없습니다. 이 시나리오에서는 다른 대상 엔드포인트에 해당하는 [관리형 프라이빗 엔드포인트](../../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md)를 만들거나 [IP 방화벽 규칙을 만들](../../synapse-analytics/security/synapse-workspace-ip-firewall.md) 수 있습니다.




