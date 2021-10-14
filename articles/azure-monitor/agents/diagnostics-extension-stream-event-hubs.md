---
title: Microsoft Azure 진단 확장에서 Azure Event Hubs로 데이터 전송
description: 데이터를 Azure Event Hub에 전송할 수 있도록 Azure Monitor에서 진단 확장을 구성하여 Azure 외부 위치로 데이터를 전달합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 6c8353064f96868c99a1e4f1feb270e6cd984bce
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994255"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Microsoft Azure 진단 확장에서 Azure Event Hubs로 데이터 전송
Azure Diagnostics 확장은 게스트 운영 체제에서 모니터링 데이터를 수집하고 Azure 가상 머신 및 기타 계산 리소스의 워크로드를 수집하는 Azure Monitor의 에이전트입니다. 이 문서는 Azure 외부 위치로 전달할 수 있도록 WAD(Windows Azure Diagnostic) 확장에서 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)로 데이터를 전송하는 방법을 설명합니다.

## <a name="supported-data"></a>지원되는 데이터

Event Hubs로 전송할 수 있는 게스트 운영 체제에서 수집된 데이터에는 다음이 포함됩니다. IIS 로그 및 크래시 덤프를 포함해 WAD가 수집하는 기타 데이터 원본은 Event Hubs로 보낼 수 없습니다.

* Windows (ETW) 이벤트에 대한 이벤트 추적
* 성능 카운터
* Windows 이벤트 로그(Windows 이벤트 로그의 애플리케이션 로그 포함)
* Azure Diagnostics 인프라 로그

## <a name="prerequisites"></a>필수 구성 요소

* Windows 진단 확장 1.6 이상 버전 기록은 [Azure Diagnostics 확장 구성 스키마 버전 및 기록](diagnostics-extension-versions.md)을 참조하고, 지원되는 리소스는 [Azure Diagnostics 확장 개요](diagnostics-extension-overview.md)를 참조하세요.
* Event Hubs 네임스페이스는 항상 프로비전되어야 합니다. 자세한 내용은 [Event Hubs 시작](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)을 참조하세요.


## <a name="configuration-schema"></a>구성 스키마
진단 확장을 구성하고 사용하도록 설정하는 다양한 옵션은 [Windows Azure 진단 확장(WAD) 설치 및 구성](diagnostics-extension-windows-install.md)을 참조하고, 구성 스키마 참조는 [Azure Diagnostics 구성 스키마](diagnostics-extension-schema-windows.md)를 참조하세요. 이 문서의 나머지 부분에서는 이 구성을 사용하여 이벤트 허브로 데이터를 전송하는 방법을 설명합니다. 

Azure Diagnostics는 항상 Azure Storage 계정에 로그 및 메트릭을 전송합니다. 데이터를 추가 위치로 전송하는 데이터 싱크를 하나 이상 구성할 수 있습니다. 각 싱크는 프라이빗 구성에서 중요한 정보를 사용하여 공용 구성의 [SinksConfig 요소](diagnostics-extension-schema-windows.md#sinksconfig-element)에 정의되어 있습니다. 이 이벤트 허브 구성에서는 다음 표에 나오는 값을 사용합니다.

| 속성 | Description |
|:---|:---|
| Name | 싱크에 대한 설명이 포함된 이름 구성에서 싱크로 보낼 데이터 원본을 지정하는 데 사용됩니다. |
| Url  | \<event-hubs-namespace\>.servicebus.windows.net/\<event-hub-name\> 형식의 이벤트 허브 URL입니다.          |
| SharedAccessKeyName | 적어도 **전송** 권한은 있는 이벤트 허브에 대한 공유 액세스 정책의 이름입니다. |
| SharedAccessKey     | 이벤트 허브에 대한 공유 액세스 정책의 기본 또는 보조 키입니다. |

공용 및 프라이빗 구성 예제는 다음과 같습니다. 이것은 이벤트 허브 데이터 싱크를 구성하고 사용하는 방법을 보여주기 위한 단일 성능 카운터와 이벤트 로그가 있는 최소 구성입니다. 더 복잡한 예는 [Azure Diagnostics 구성 스키마](diagnostics-extension-schema-windows.md)를 참조하세요.

### <a name="public-configuration"></a>공용 구성

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120,
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "myEventHub",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>프라이빗 구성

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>구성 옵션
데이터 싱크로 데이터를 보내려면 데이터 원본의 노드에 **싱크** 특성을 지정합니다. **싱크** 특성이 있는 위치에 따라 할당 범위가 결정됩니다. 다음 예제에서는 **싱크** 특성이 **PerformanceCounters** 노드에 정의되어 모든 자식 성능 카운터가 이벤트 허브로 전송됩니다.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


다음 예제에서는 **싱크** 특성이 3개 카운터에 직접 적용되어 해당하는 성능 카운터만 이벤트 허브에 전송됩니다. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>구성 유효성 검사
다양한 방법을 사용하여 데이터가 이벤트 허브로 전송되는지 확인할 수 있습니다. 한 가지 간단한 방법은 Azure Blob Storage [또는 Azure Data Lake Storage Azure Event Hubs 통해 이벤트 캡처에](../../event-hubs/event-hubs-capture-overview.md)설명된 대로 Event Hubs 캡처를 사용하는 것입니다. 


## <a name="troubleshoot-event-hubs-sinks"></a>Event Hubs 싱크 문제 해결

- Azure Diagnostics 자체의 로그와 오류가 있는 Azure Storage 표 **WADDiagnosticInfrastructureLogsTable** 를 확인합니다. 한 가지 옵션은 [Azure Storage Explorer](https://www.storageexplorer.com) 등의 도구를 사용하여 이 Storage 계정에 연결하고 이 테이블을 본 후 지난 24시간 동안의 타임스탬프에 대한 쿼리를 추가하는 것입니다. 이 도구를 사용하여 .csv 파일을 내보내고 Microsoft Excel과 같은 애플리케이션에서 열 수 있습니다. Excel를 통해 어떤 오류가 보고되는지 확인하는 **EventHubs** 와 같은 전화 카드 문자열을 쉽게 검색할 수 있습니다.  

- 이벤트 허브가 성공적으로 프로비저닝되었는지 확인합니다. 구성의 **PrivateConfig** 섹션에 있는 모든 연결 정보가 포털에 있는 리소스 값과 일치해야 합니다. 포털에 SAS 정책이 정의되어 있고(예제의 *SendRule*), *Send* 권한이 부여되어 있어야 합니다.  

## <a name="next-steps"></a>다음 단계

* [Event Hubs 개요](../../event-hubs/event-hubs-about.md)
* [이벤트 허브 만들기](../../event-hubs/event-hubs-create.md)
* [Event Hubs FAQ](../../event-hubs/event-hubs-faq.yml)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



