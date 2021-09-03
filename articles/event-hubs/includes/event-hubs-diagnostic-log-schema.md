---
title: 포함 파일
description: 포함 파일
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 06/11/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ad25ce992dec7165e2b936e5642e8c3a209ce6a5
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122516325"
---
Event Hubs는 다음 범주에 대한 진단 로그를 캡처합니다.

| 범주 | Description | 
| -------- | ----------- | 
| 보관 로그 | [Event Hubs 캡처](../event-hubs-capture-overview.md) 작업에 대한 정보, 특히 캡처 오류와 관련된 로그를 캡처합니다. |
| 작업 로그 | Azure Event Hubs 네임스페이스에서 수행되는 모든 관리 작업을 캡처합니다. 데이터 작업은 Azure Event Hubs에서 수행되는 대량의 데이터 작업으로 인해 캡처되지 않습니다. |
| 자동 크기 조정 로그 | Event Hubs 네임스페이스에서 수행된 자동 확장 작업을 캡처합니다. |
| Kafka 코디네이터 로그 | Event Hubs와 관련된 Kafka 코디네이터 작업을 캡처합니다. |
| Kafka 사용자 오류 로그 | Event Hubs에서 호출되는 Kafka API에 대한 정보를 캡처합니다. |
| Event Hubs VNet(가상 네트워크) 연결 이벤트 | Event Hubs로 트래픽을 전송하는 IP 주소 및 가상 네트워크에 대한 정보를 캡처합니다. |
| 고객 관리형 키 사용자 로그 | 고객 관리형 키와 관련된 작업을 캡처합니다. |


모든 로그는 JSON(JavaScript Object Notation) 형식으로 저장됩니다. 각 항목에는 다음 섹션에 설명된 형식을 사용하는 문자열 필드가 있습니다.

### <a name="archive-logs-schema"></a>보관 로그 스키마

보관 로그 JSON 문자열에는 다음 표에 나열된 요소가 포함되어 있습니다.

Name | 설명
------- | -------
`TaskName` | 실패한 작업에 대한 설명
`ActivityId` | 추적에 사용되는 내부 ID
`trackingId` | 추적에 사용되는 내부 ID
`resourceId` | Azure Resource Manager 리소스 ID
`eventHub` | 이벤트 허브 전체 이름(네임스페이스 이름 포함)
`partitionId` | 데이터가 기록되는 이벤트 허브 파티션
`archiveStep` | 가능한 값은 다음과 같습니다. ArchiveFlushWriter, DestinationInit
`startTime` | 실패 시작 시간
`failures` | 실패가 발생한 횟수
`durationInSeconds` | 실패 기간
`message` | 오류 메시지
`category` | ArchiveLogs

다음 코드는 보관 로그 JSON 문자열에 대한 예입니다.

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>작업 로그 스키마

작업 로그 JSON 문자열에는 다음 표에 나열된 요소가 포함되어 있습니다.

속성 | 설명
------- | -------
`ActivityId` | 추적 목적으로 사용되는 내부 ID |
`EventName` | 작업 이름입니다. 이 요소에 대한 값 목록은 [이벤트 이름](#event-names)을 참조하세요. |
`resourceId` | Azure Resource Manager 리소스 ID |
`SubscriptionId` | 구독 ID |
`EventTimeString` | 작업 시간 |
`EventProperties` |작업에 대한 속성입니다. 이 요소는 다음 예제와 같이 이벤트에 대한 자세한 정보를 제공합니다. |
`Status` | 작업 상태입니다. 이 값은 **Succeeded** 또는 **Failed** 일 수 있습니다.  |
`Caller` | 작업 호출자(Azure Portal 또는 관리 클라이언트) |
`Category` | OperationalLogs |

다음 코드는 작업 로그 JSON 문자열에 대한 예입니다.

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

#### <a name="event-names"></a>이벤트 이름
이벤트 이름은 다음 열거형에서 작업 유형 + 리소스 종류로 채워집니다. `Create Queue`, `Retrieve Event Hu` 또는 `Delete Rule`). 

| 작업 유형 | 리소스 종류 | 
| -------------- | ------------- | 
| <ul><li>생성</li><li>업데이트</li><li>DELETE</li><li>장치</li><li>알 수 없음</li></ul> | <ul><li>네임스페이스</li><li>큐</li><li>항목</li><li>Subscription</li><li>EventHub</li><li>EventHubSubscription</li><li>NotificationHub</li><li>NotificationHubTier</li><li>SharedAccessPolicy</li><li>UsageCredit</li><li>NamespacePnsCredentials</li>규칙</li>ConsumerGroup</li> |

### <a name="autoscale-logs-schema"></a>자동 크기 조정 로그 스키마
자동 크기 조정 로그 JSON에는 다음 표에 나열된 요소가 포함되어 있습니다.

| Name | 설명 |
| ---- | ----------- | 
| `TrackingId` | 추적 목적으로 사용되는 내부 ID |
| `ResourceId` | Azure Resource Manager 리소스 ID입니다. |
| `Message` | 자동 확장 작업에 대한 세부 정보를 제공하는 정보 메시지입니다. 이 메시지에는 지정된 네임스페이스에 대한 이전 및 현재 처리량 단위 값과 TU의 확장을 트리거한 항목이 포함되어 있습니다. |

다음은 자동 크기 조정 이벤트의 예입니다. 

```json
{
    "TrackingId": "fb1b3676-bb2d-4b17-85b7-be1c7aa1967e",
    "Message": "Scaled-up EventHub TUs (UpdateStartTimeUTC: 5/13/2020 7:48:36 AM, PreviousValue: 1, UpdatedThroughputUnitValue: 2, AutoScaleReason: 'IncomingMessagesPerSecond reached 2170')",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name"
}
```

### <a name="kafka-coordinator-logs-schema"></a>Kafka 코디네이터 로그 스키마
Kafka 코디네이터 로그 JSON에는 다음 표에 나열된 요소가 포함되어 있습니다.

| 속성 | 설명 |
| ---- | ----------- | 
| `RequestId` | 추적 목적으로 사용되는 요청 ID |
| `ResourceId` | Azure Resource Manager 리소스 ID |
| `Operation` | 그룹 조정 중에 수행되는 작업의 이름 |
| `ClientId` | 클라이언트 ID |
| `NamespaceName` | 네임스페이스 이름 | 
| `SubscriptionId` | Azure 구독 ID |
| `Message` | 그룹 조정 중 수행된 작업에 대한 세부 정보를 제공하는 정보 또는 경고 메시지 |

#### <a name="example"></a>예제

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

### <a name="kafka-user-error-logs-schema"></a>Kafka 사용자 오류 로그 스키마
Kafka 사용자 오류 로그 JSON에는 다음 표에 나열된 요소가 포함되어 있습니다.

| Name | 설명 |
| ---- | ----------- |
| `TrackingId` | 추적 목적으로 사용되는 추적 ID입니다. |
| `NamespaceName` | 네임스페이스 이름 |
| `Eventhub` | 이벤트 허브 이름 |
| `PartitionId` | Partition ID |
| `GroupId` | 그룹 ID |
| `ClientId` | 클라이언트 ID |
| `ResourceId` | Azure Resource Manager 리소스 ID입니다. |
| `Message` | 오류에 대한 세부 정보를 제공하는 정보 메시지 |

### <a name="event-hubs-virtual-network-connection-event-schema"></a>Event Hubs 가상 네트워크 연결 이벤트 스키마
Event Hubs VNet(가상 네트워크) 연결 이벤트 JSON에는 다음 표에 나열된 요소가 포함되어 있습니다.

| 속성 | 설명 |
| ---  | ----------- | 
| `SubscriptionId` | Azure 구독 ID |
| `NamespaceName` | 네임스페이스 이름 |
| `IPAddress` | Event Hubs 서비스에 연결하는 클라이언트의 IP 주소 |
| `Action` | 연결 요청을 평가할 때 Event Hubs 서비스에서 수행된 작업입니다. 지원되는 작업은 **연결 허용** 및 **연결 거부** 입니다. |
| `Reason` | 작업이 완료된 이유를 제공합니다. |
| `Count` | 지정된 작업의 발생 수 |
| `ResourceId` | Azure Resource Manager 리소스 ID입니다. |

가상 네트워크 로그는 네임스페이스에서 **선택한 네트워크** 또는 **특정 IP 주소**(IP 필터 규칙)의 액세스를 허용하는 경우에만 생성됩니다. 해당 기능을 사용하여 네임스페이스에 대한 액세스를 제한하지 않고, Event Hubs 네임스페이스에 연결하는 클라이언트의 IP 주소를 추적하는 가상 네트워크 로그를 계속 가져오려는 경우 다음 해결 방법을 사용할 수 있습니다. [IP 필터링을 사용하도록 설정](../event-hubs-ip-filtering.md)하고 주소 지정 가능한 총 IPv4 범위(1.0.0.0/1 - 255.0.0.0/1)를 추가합니다. Event Hubs IP 필터링은 IPv6 범위를 지원하지 않습니다. 로그에 IPv6 형식의 프라이빗 엔드포인트 주소가 표시될 수도 있습니다. 

#### <a name="example"></a>예제

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

### <a name="customer-managed-key-user-logs-schema"></a>고객 관리형 키 사용자 로그 스키마
고객 관리형 키 사용자 로그 JSON에는 다음 표에 나열된 요소가 포함되어 있습니다.

| Name | 설명 |
| ---- | ----------- | 
| `Category` | 메시지의 범주 유형입니다. **오류** 및 **정보** 중 하나입니다. 예를 들어, 키 자격 증명 모음의 키를 사용하지 않도록 설정하는 경우에는 정보 범주이거나, 키를 래핑 해제할 수 없는 경우에는 오류가 발생할 수 있습니다.|
| `ResourceId` | Azure 구독 ID 및 네임스페이스 이름을 포함하는 내부 리소스 ID |
| `KeyVault` | Key Vault 리소스의 이름 |
| `Key` | Event Hubs 네임스페이스를 암호화하는 데 사용되는 Key Vault 키의 이름입니다. |
| `Version` | Key Vault 키의 버전입니다.|
| `Operation` | 키 자격 증명 모음의 키에 수행되는 작업입니다. 예를 들어 키를 사용/사용하지 않도록 설정, 래핑 또는 래핑 해제 등의 작업입니다. |
| `Code` | 작업과 연결된 코드입니다. 예: 오류 코드 404는 키를 찾을 수 없음을 의미합니다. |
| `Message` | 오류 또는 정보 메시지에 대한 세부 정보를 제공하는 메시지 |

고객 관리형 키에 대한 로그의 예시는 다음과 같습니다.

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

다음은 BYOK 암호화를 사용하는 경우 흔히 보는 일반적인 오류 코드입니다.

| 작업 | 오류 코드 | 데이터의 결과 상태 |
| ------ | ---------- | ----------------------- | 
| 키 자격 증명 모음에서 래핑/래핑 해제 권한을 제거하세요. | 403 |    액세스할 수 없음 |
| 래핑/래핑 해제 권한이 부여된 AAD 보안 주체에서 AAD 역할 멤버 자격을 제거하세요. | 403 |  액세스할 수 없음 |
| 키 자격 증명 모음에서 암호화 키를 삭제하세요. | 404 | 액세스할 수 없음 |
| 키 자격 증명 모음을 삭제하세요. | 404 | 액세스할 수 없음(필수 설정인 일시 삭제를 사용하는 것으로 가정함.) |
| 이미 만료된 경우 암호화 키의 만료 기간을 변경합니다. | 403 |   액세스할 수 없음  |
| 키 암호화 키가 활성화되지 않도록 NBF(not before)를 변경합니다. | 403 | 액세스할 수 없음  |
| 키 자격 증명 모음 방화벽에 대해 **MSFT 서비스 허용** 옵션을 선택하거나, 암호화 키가 있는 키 자격 증명 모음으로 연결되는 네트워크 액세스를 차단합니다. | 403 | 액세스할 수 없음 |
| 다른 테넌트로 키 자격 증명 모음을 이동합니다. | 404 | 액세스할 수 없음 |  
| 일시적인 네트워크 문제 또는 DNS/AAD/MSI 중단 |  | 캐시된 데이터 암호화 키를 사용하여 액세스 가능 |

