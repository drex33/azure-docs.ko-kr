---
title: Azure Video Analyzer에서 직접 메서드 사용 - Azure
description: Azure Video Analyzer edge 모듈은이 항목에서 설명 하는 몇 가지 직접 메서드를 제공 합니다.
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 39a1ddaeca42240e3f37f8e70679d5bd3d24957b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556467"
---
# <a name="azure-video-analyzer-direct-methods"></a>Azure Video Analyzer 직접 메서드

[!INCLUDE [header](includes/edge-env.md)]

Azure Video Analyzer edge 모듈은 `avaedge` IoT Hub에서 호출할 수 있는 몇 가지 직접 메서드를 제공 합니다. 직접 메서드는 사용자가 지정한 시간 제한을 초과하는 즉시 성공하거나 실패한다는 점에서 HTTP 호출과 비슷한 디바이스와의 요청-응답 상호 작용을 나타냅니다. 이 방법은 즉각적인 조치 과정이 디바이스의 응답 여부에 따라 달라지는 시나리오에서 유용합니다. 자세한 내용은 [IoT Hub의 직접 메서드 이해 및 호출](../../../iot-hub/iot-hub-devguide-direct-methods.md)을 참조하세요.

이 항목에서는 이러한 메서드, 규칙 및 메서드의 스키마에 대해 설명합니다.

## <a name="conventions"></a>규칙

직접 메서드는 다음 규칙을 기준으로 합니다.

1. 직접 메서드에는 MAJOR.MINOR 형식으로 지정된 버전이 있습니다(다음 예제에 표시). 이 예제에서는 "@apiVersion" 숫자입니다. 여기서 "1"은 MAJOR이고 "0"은 MINOR입니다.

    ```
    {
      "methodName": "pipelineTopologySet",
      "payload": {
          "@apiVersion": "1.1",
          "name": "{TopologyName}",
          "properties": {
              // Desired Topology properties
          }
      }
    }
    ```
1. 지정된 버전의 Video Analyzer 모듈은 직접 메서드 호출의 모든 부 버전을 현재 버전까지 지원합니다. 주 버전에 대한 지원은 보장되지 않습니다.
1. 모든 직접 메서드는 동기적입니다.
1. 오류 결과는 [OData 오류 스키마](http://docs.oasis-open.org/odata/odata-json-format/v4.01/odata-json-format-v4.01.html#sec_ErrorResponse)를 기반으로 합니다.
1. 이름은 다음 제약 조건을 따라야 합니다.
    * 영숫자 문자와 대시만 포함(단, 대시로 시작하고 끝나면 안 됨)
    * 공백 없음
    * 최대 32 문자

### <a name="example-of-response-from-a-direct-method"></a>직접 메서드의 응답 예제

```
-----------------------  Request: livePipelineList  --------------------------------------------------

{
  "@apiVersion": "1.1"
}

---------------  Response: livePipelineList - Status: 200  ---------------

{
  "value": []
}

--------------------------------------------------------------------------
```    
### <a name="error-codes"></a>오류 코드
아래 예제와 같이 직접 메서드에서 오류 응답을 받으면 최상위 오류 코드가 있으며 `details` 아래에 추가 정보가 제공됩니다.


```json
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "The request is invalid",
      "details": [
        {
          "code": "ApiVersionNotSupported",
          "message": "The API version '1.4' is not supported. Supported version(s): 1.0, 1.1"
        }
      ]
    }
  }
}
```

다음은 최상위 수준에서 사용되는 오류 코드입니다.

|상태 |코드   |메시지|
|---|---|---|
|400|   BadRequest| 요청이 잘못되었습니다.|
|400|   InvalidResource|    리소스가 잘못되었습니다.|
|400|   InvalidVersion| API 버전이 잘못되었습니다.|
|402|   ConnectivityRequired    |에지 모듈을 제대로 작동하려면 클라우드 연결이 필요합니다.|
|404|   NotFound    |리소스를 찾을 수 없습니다.|
|409|   충돌|   작업 충돌|
|500|   InternalServerError|    내부 서버 오류|
|503|   ServerBusy| 서버 작업 중|

다음은 세부 정보 수준에서 사용되는 몇 가지 오류 코드입니다.

|상태|    자세한 코드   |Description|
|---|---|---|
|400|   PipelineValidationError|    주기 또는 분할과 같은 일반적인 파이프라인 오류 등|
|400|   ModuleValidationError|  모듈 특정 유효성 검사 오류입니다.|
|409|   PipelineTopologyInUse|  파이프라인 토폴로지는 여전히 하나 이상의 라이브 파이프라인에서 참조됩니다.|
|409|   OperationNotAllowedInState| 요청한 작업은 현재 상태에서 수행할 수 없습니다.|
|409|   ResourceValidationError|    참조된 리소스(예: 비디오 리소스)가 올바른 상태가 아닙니다.|

## <a name="supported-direct-methods"></a>지원되는 직접 메서드  
다음은 Video Analyzer 에지 모듈에서 노출하는 직접 메서드입니다. 직접 메서드에 대한 스키마는 [여기](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.1.0/AzureVideoAnalyzerSdkDefinitions.json)에서 찾을 수 있습니다.

### <a name="pipelinetopologylist"></a>pipelineTopologyList

이 직접 메서드는 생성된 모든 파이프라인 토폴로지를 나열합니다.

#### <a name="request"></a>요청

```
{
  "@apiVersion": "1.1"
}
```
#### <a name="response"></a>응답

```
{
  "status": 200,
  "value": [
    {
      "systemData": {
        "createdAt": "2021-05-05T14:19:22.16Z",
        "lastModifiedAt": "2021-05-05T16:20:41.505Z"
      },
      
      // first pipeline topology payload
      
    },
      "systemData": {
        "createdAt": "2021-05-06T14:19:22.16Z",
        "lastModifiedAt": "2021-05-06T16:20:41.505Z"
      },
      
      // next pipeline topology payload
      
    }    
  ]
}
```

#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 |
|--|--|
| 엔터티를 찾음 | 200 |
| 일반 사용자 오류 | 400 범위 |
| 엔터티를 찾을 수 없음 | 404 |
| 일반 서버 오류 | 500 범위 |

### <a name="pipelinetopologyset"></a>pipelineTopologySet

지정된 이름의 파이프라인 토폴로지가 없는 경우 이러한 토폴로지를 만들고, 해당 이름을 가진 기존 토폴로지가 있으면 업데이트합니다.

주요 측면:

* 파이프라인 토폴로지를 참조하는 라이브 파이프라인이 없으면 파이프라인 토폴로지를 자유롭게 업데이트할 수 있습니다.
* 다음과 같은 경우 모든 참조 라이브 파이프라인이 비활성화되면 파이프라인 토폴로지를 자유롭게 업데이트할 수 있습니다.

    * 새로 추가된 매개 변수에 기본값이 있는 경우
    * 제거된 매개 변수가 파이프라인에서 참조되지 않는 경우
* 라이브 파이프라인이 활성 상태인 동안에는 일부 파이프라인 토폴로지 업데이트만 허용됩니다.

#### <a name="request"></a>요청

```
  {
    "methodName": "pipelineTopologySet",
    "payload": {
        "@apiVersion": "1.1",
        "name": "{TopologyName}",
        "properties": {
            // Desired pipeline topology properties
        }
    }
  }
```

#### <a name="response"></a>응답

```
  {
    "status": 201,
    "payload": {
        "systemData": {
           "createdAt": "2021-05-11T18:16:46.491Z",
           "lastModifiedAt": "2021-05-11T18:16:46.491Z"
        },
        "name": "{TopologyName}",
        "properties": {
            // Complete pipeline topology
        }
    }
  }
```
#### <a name="status-codes"></a>상태 코드

|조건  |상태 코드    |오류 코드 세부 정보|
|---|---|---|
기존 엔터티가 업데이트됨 |200|   해당 없음|
새 엔터티가 만들어짐  |201|   해당 없음|
일반 사용자 오류 |400 범위  ||
파이프라인 유효성 검사 오류  |400    |PipelineValidationError|
모듈 유효성 검사 오류|   400 |ModuleValidationError|
일반 서버 오류   |500 범위  ||

### <a name="pipelinetopologyget"></a>pipelineTopologyGet

지정된 이름을 가진 파이프라인 토폴로지를 검색합니다(있는 경우).

#### <a name="request"></a>요청

```
  {
        "@apiVersion": "1.1",
        "name": "{TopologyName}"       
  }
```
#### <a name="response"></a>응답

```
{
  "status": 200,
  "payload": {
    "value": [
      {
        "systemData": {
          "createdAt": "2021-05-06T10:28:04.560Z",
          "lastModifiedAt": "2021-05-06T10:28:04.560Z"
        },
        "name": "{TopologyName}",
        // Complete pipeline topology
      }
    ]
  }
}
```

#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 |
|--|--|
| Success | 200 |
| 일반 사용자 오류 | 400 범위 |
| 일반 서버 오류 | 500 범위 |

### <a name="pipelinetopologydelete"></a>pipelineTopologyDelete

단일 파이프라인 토폴로지를 삭제합니다.

* [삭제할 파이프라인 토폴로지를 참조](../pipeline.md#pipeline-states)하는 라이브 파이프라인이 있으면 안 됩니다. 이러한 라이브 파이프라인이 있으면 `TopologyInUse` 오류가 표시됩니다.

#### <a name="request"></a>요청

```
  {
    "methodName": "pipelineTopologyDelete",
    "payload": {
        "@apiVersion": "1.1",
        "name": "{TopologyName}"
    }
  }
```
#### <a name="response"></a>응답

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| 엔터티가 삭제됨 | 200 | 해당 없음 |
| 엔터티를 찾을 수 없음 | 204 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 |  |
| 하나 이상의 파이프라인이 파이프라인 토폴로지를 참조하고 있습니다. | 409 | PipelineTopologyInUse |
| 일반 서버 오류 | 500 범위 |  |

### <a name="livepipelinelist"></a>livePipelineList

모든 라이브 파이프라인을 나열합니다.

#### <a name="request"></a>요청

```
  {
        "@apiVersion": "1.1"
  }
```
#### <a name="response"></a>응답

```
{
  "status": 200,
  "value": [
    {
      "systemData": {
        "createdAt": "2021-05-05T14:19:22.16Z",
        "lastModifiedAt": "2021-05-05T16:20:41.505Z"
      },      
      // first live pipeline payload  
    },
      "systemData": {
        "createdAt": "2021-05-06T14:19:22.16Z",
        "lastModifiedAt": "2021-05-06T16:20:41.505Z"
      },
      // next live pipeline payload
    }    
  ]
}
```
#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 |
|--|--|
| 엔터티를 찾음 | 200 |
| 일반 사용자 오류 | 400 범위 |
| 엔터티를 찾을 수 없음 | 404 |
| 일반 서버 오류 | 500 범위 |

### <a name="livepipelineset"></a>livePipelineSet

지정된 이름을 가진 라이브 파이프라인이 없으면 새로 만들고, 해당 이름을 가진 기존 라이브 파이프라인이 있으면 업데이트합니다.

주요 측면:

* 라이브 파이프라인은 "비활성화" 상태에서 자유롭게 업데이트할 수 있습니다. 다른 상태에서는 일부 업데이트만 허용됩니다.
* 모든 업데이트에서 라이브 파이프라인의 유효성이 다시 검사됩니다.

#### <a name="request"></a>요청

```
  {
        "@apiVersion": "1.1",
        "name": "{livePipelineName}",
        "properties": {
            // Desired live pipeline properties
        }
  }
```
#### <a name="response"></a>응답

````
  {
    "status": 201,
    "payload": {
        "systemData": {
           "createdAt": "2021-05-11T18:16:46.491Z",
           "lastModifiedAt": "2021-05-11T18:16:46.491Z"
        },
        "name": "{livePipelineName}",
        "properties": {
            // Complete live pipeline
        }
    }
  }
````
#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| 기존 엔터티가 업데이트됨 | 200 | 해당 없음 |
| 새 엔터티가 만들어짐 | 201 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 | 해당 없음 |
| 라이브 파이프라인 유효성 검사 오류 | 400 | PipelineValidationError |
| 모듈 유효성 검사 오류 | 400 | ModuleValidationError |
| 리소스 유효성 검사 오류 | 409 | ResourceValidationError |
| 일반 서버 오류 | 500 범위 | 해당 없음 |

### <a name="livepipelinedelete"></a>livePipelineDelete

단일 라이브 파이프라인을 삭제합니다.

주요 측면:

* 비활성화된 라이브 파이프라인만 삭제할 수 있습니다.

#### <a name="request"></a>요청

```
  {
        "@apiVersion": "1.1",
        "name": "{livePipelineName}"
  }
```
#### <a name="response"></a>응답

```
  {
    "status": 200,
    "payload": null
  }
```
#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| 라이브 파이프라인이 성공적으로 삭제되었습니다. | 200 | 해당 없음 |
| 라이브 파이프라인을 찾을 수 없습니다. | 204 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 |  |
| 파이프라인이 "비활성화" 상태가 아닙니다. | 409 | OperationNotAllowedInState |
| 일반 서버 오류 | 500 범위 |  |

### <a name="livepipelineget"></a>livePipelineGet

이는 livePipelineTopologyGet와 유사 합니다. 지정된 이름을 가진 라이브 파이프라인을 검색합니다(있는 경우).

#### <a name="request"></a>요청

```
  {
        "@apiVersion": "1.1",
        "name": "{livePipelineName}"       
  }
```
#### <a name="response"></a>응답

```
{
  "status": 200,
  "payload": {
    "value": [
      {
        "systemData": {
          "createdAt": "2021-05-06T10:28:04.560Z",
          "lastModifiedAt": "2021-05-06T10:28:04.560Z"
        },
        "name": "{livePipelineName}",
        // Complete live pipeline
      }
    ]
  }
}
```

#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 |
|--|--|
| Success | 200 |
| 일반 사용자 오류 | 400 범위 |
| 일반 서버 오류 | 500 범위 |

### <a name="livepipelineactivate"></a>livePipelineActivate

라이브 파이프라인을 활성화합니다. 

주요 측면

* 라이브 파이프라인이 "활성화 중" 또는 "활성화" 상태이면 메서드가 반환됩니다. 
* 라이브 파이프라인에 대한 List/Set 작업은 현재 상태를 반환합니다.
* 라이브 파이프라인이 (일시적) "비활성화 중" 상태가 아닌 한 이 메서드를 호출할 수 있습니다.
* 멱등성:
    * 이미 "활성화 중" 상태인 라이브 파이프라인에 대한 `livePipelineActivate` 호출은 라이브 파이프라인이 비활성화된 것처럼 동작합니다.
    * "활성" 상태인 파이프라인을 활성화하면 성공 코드가 즉시 반환됩니다.

#### <a name="request"></a>요청

```
  {
        "@apiVersion": "1.1",
        "name": "{livePipelineName}"
  }
```
#### <a name="response"></a>응답

```
{
    "status": 200,
    "payload": null
}
```
#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| 파이프라인이 성공적으로 활성화되었습니다. | 200 | 해당 없음 |
| 새 엔터티가 만들어짐 | 201 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 |  |
| 모듈 유효성 검사 오류 | 400 | ModuleValidationError |
| 리소스 유효성 검사 오류 | 409 | ResourceValidationError |
| 라이브 파이프라인이 비활성화 중인 상태입니다. | 409 | OperationNotAllowedInState |
| 일반 서버 오류 | 500 범위 |  |

### <a name="livepipelinedeactivate"></a>livePipelineDeactivate

라이브 파이프라인을 비활성화합니다. 파이프라인을 비활성화하면 비디오 처리가 비활성화되고 해당하는 경우 에지에 캐시된 모든 이벤트 및 비디오가 클라우드로 커밋됩니다.

주요 측면:

* 파이프라인이 비활성화된 경우에만 메서드가 반환됩니다.
* 라이브 파이프라인이 (일시적) "활성화 중" 상태가 아닌 한 이 메서드를 호출할 수 있습니다.
* 파이프라인은 비활성화 중일 때 "비활성화 중" 상태로 간주됩니다.
    * 라이브 파이프라인에 대한 List/Set 작업은 현재 상태를 반환합니다.
    * 모든 미디어가 클라우드에 업로드되면 비활성화가 완료됩니다(파이프라인에 [비디오 싱크](../pipeline.md#video-sink) 노드가 있는 경우).
* 멱등성:
    * 이미 "비활성화 중" 상태인 라이브 파이프라인에 대한 `livePipelineDeactivate` 호출은 라이브 파이프라인이 "활성" 상태인 것처럼 동작합니다.
    * "비활성" 상태인 파이프라인을 비활성화하면 성공 코드가 즉시 반환됩니다.

#### <a name="request"></a>요청

```
  {
    "@apiVersion": "1.1",
    "name": "{livePipelineName}"
  }
```
#### <a name="response"></a>응답

```
{
 "status": 200,
 "payload": null
}
```
| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| 파이프라인이 성공적으로 활성화되었습니다. | 200 | 해당 없음 |
| 새 엔터티가 만들어짐 | 201 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 |  |
| 파이프라인이 활성화 중인 상태입니다. | 409 | OperationNotAllowedInState |
| 일반 서버 오류 | 500 범위 |  |

### <a name="remotedeviceadapterlist"></a>remoteDeviceAdapterList

모든 원격 디바이스 어댑터를 나열합니다. Video Analyzer 에지 모듈은 비디오의 투명한 게이트웨이 역할을 하여 IoT 디바이스가 방화벽 뒤에서 클라우드로 비디오를 보낼 수 있도록 합니다. 이러한 각 IoT 디바이스에 대해 원격 디바이스 어댑터를 만들어야 합니다. 클라우드와 IoT 디바이스 간의 통신은 Video Analyzer 에지 모듈을 통해 흐릅니다.

#### <a name="request"></a>요청

```
  {
        "@apiVersion": "1.1"
  }
```
#### <a name="response"></a>응답

```
{
  "status": 200,
  "value": [
    {
      "systemData": {
        "createdAt": "2021-10-05T14:19:22.16Z",
        "lastModifiedAt": "2021-10-05T16:20:41.505Z"
      },      
      // first remote device adapter payload
    },
      "systemData": {
        "createdAt": "2021-10-06T14:19:22.16Z",
        "lastModifiedAt": "2021-10-06T16:20:41.505Z"
      },
      // next remote device adapter  payload
    }    
  ]
}
```
#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 |
|--|--|
| 엔터티를 찾음 | 200 |
| 일반 사용자 오류 | 400 범위 |
| 엔터티를 찾을 수 없음 | 404 |
| 일반 서버 오류 | 500 범위 |

### <a name="remotedeviceadapterset"></a>remoteDeviceAdapterSet

해당 어댑터가 없는 경우 지정된 이름의 원격 디바이스 어댑터를 만들거나 해당 이름으로 기존 어댑터를 업데이트합니다. 응답 페이로드 및 `remoteDeviceAdapterList` 또는 `remoteDeviceAdapterGet` 호출에서는 자격 증명 또는 비밀이 생략됩니다.


#### <a name="request"></a>요청

```
  {
        "@apiVersion": "1.1",
        "name": "{remoteDeviceAdapterName}",
        "properties": {
            // Desired remote device adapter properties
        }
  }
```
#### <a name="response"></a>응답

````
  {
    "status": 201,
    "payload": {
        "systemData": {
           "createdAt": "2021-10-11T18:16:46.491Z",
           "lastModifiedAt": "2021-10-11T18:16:46.491Z"
        },
        "name": "{remoteDeviceAdapterName}",
        "properties": {
            // Remote device adapter properties, except the credentials
        }
    }
  }
````
#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| 기존 엔터티가 업데이트됨 | 200 | 해당 없음 |
| 새 엔터티가 만들어짐 | 201 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 | 해당 없음 |
| 원격 디바이스 어댑터 유효성 검사 오류 | 400 | RemoteDeviceAdapterValidationError |
| 모듈 유효성 검사 오류 | 400 | ModuleValidationError |
| 리소스 유효성 검사 오류 | 409 | ResourceValidationError |
| 일반 서버 오류 | 500 범위 | 해당 없음 |

### <a name="remotedeviceadapterget"></a>remoteDeviceAdapterGet

이는 `livePipelineTopologyGet`과 유사합니다. 지정된 이름의 원격 디바이스 어댑터가 있으면 검색합니다(자격 증명 또는 비밀은 생략).

#### <a name="request"></a>요청

```
  {
        "@apiVersion": "1.1",
        "name": "{remoteDeviceAdapterName}"       
  }
```
#### <a name="response"></a>응답

```
{
  "status": 200,
  "payload": {
    "value": [
      {
        "systemData": {
          "createdAt": "2021-10-06T10:28:04.560Z",
          "lastModifiedAt": "2021-10-06T10:28:04.560Z"
        },
        "name": "{remoteDeviceAdapterName}",
        // Remote device adapter properties, except the credentials
      }
    ]
  }
}
```

#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 |
|--|--|
| Success | 200 |
| 일반 사용자 오류 | 400 범위 |
| 일반 서버 오류 | 500 범위 |

### <a name="remotedeviceadapterdelete"></a>remoteDeviceAdapterDelete

단일 원격 디바이스 어댑터가 있는 경우 삭제합니다.

#### <a name="request"></a>요청

```
  {
        "@apiVersion": "1.1",
        "name": "{remoteDeviceAdapterName}"
  }
```
#### <a name="response"></a>응답

```
  {
    "status": 200,
    "payload": null
  }
```
#### <a name="status-codes"></a>상태 코드

| 조건 | 상태 코드 | 오류 코드 세부 정보 |
|--|--|--|
| 원격 디바이스 어댑터가 성공적으로 삭제되었습니다. | 200 | 해당 없음 |
| 원격 디바이스 어댑터를 찾을 수 없습니다. | 204 | 해당 없음 |
| 일반 사용자 오류 | 400 범위 |  |
| 일반 서버 오류 | 500 범위 |  |

### <a name="onvifdevicediscover-and-onvifdeviceget"></a>onvifDeviceDiscover 및 onvifDeviceGet
이러한 호출의 세부 정보는 [ONVIF 지원 디바이스 검색](camera-discovery.md)문서에 제공됩니다.

## <a name="next-steps"></a>다음 단계

[모듈 쌍 구성 스키마](module-twin-configuration-schema.md)
