---
title: IoT Edge 로그 검색 - Azure IoT Edge
description: IoT Edge 모듈 로그 검색을 Azure Blob Storage에 업로드합니다.
author: kgremban
ms.author: kgremban
ms.date: 11/12/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 0b5c912f6d7a79af8ed8abb49929427a6a9c3b29
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132935069"
---
# <a name="retrieve-logs-from-iot-edge-deployments"></a>IoT Edge 배포에서 로그 검색

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge 에이전트 모듈에 포함된 직접 메서드를 사용하여 디바이스에 대한 물리적 또는 SSH 액세스 없이 IoT Edge 배포에서 로그를 검색합니다. 직접 메서드는 디바이스에서 구현된 다음 클라우드에서 호출할 수 있습니다. IoT Edge 에이전트에는 IoT Edge 디바이스를 원격으로 모니터링하고 관리하는 데 도움이 되는 직접 메서드가 포함되어 있습니다. 이 문서에서 설명하는 직접 메서드는 일반적으로 1.0.10 릴리스와 함께 사용할 수 있습니다.

직접 메서드, 그 사용 방법 및 사용자 고유의 모듈에서 이를 구현하는 방법에 대한 자세한 내용은 [IoT Hub에서의 직접 메서드 인식 및 호출](../iot-hub/iot-hub-devguide-direct-methods.md)을 참조하세요.

직접 메서드의 이름은 대/소문자를 구분하여 처리됩니다.

## <a name="recommended-logging-format"></a>권장 로깅 형식

필수는 아니지만 이 기능과의 최상의 호환을 위해 권장되는 로깅 형식은 다음과 같습니다.

```
<{Log Level}> {Timestamp} {Message Text}
```

`{Timestamp}`는 `yyyy-MM-dd HH:mm:ss.fff zzz`로 형식이 지정되어야 하며, `{Log Level}`은 아래 표를 따라야 합니다. 이 표는 [Syslog 표준의 심각도 코드](https://wikipedia.org/wiki/Syslog#Severity_level)에서 해당 심각도 수준을 파생시킵니다.

| 값 | Severity |
|-|-|
| 0 | 응급 |
| 1 | 경고 |
| 2 | 위험 |
| 3 | Error |
| 4 | Warning |
| 5 | 알림 |
| 6 | 정보 제공 |
| 7 | 디버그 |

[IoT Edge의 로거 클래스](https://github.com/Azure/iotedge/blob/master/edge-util/src/Microsoft.Azure.Devices.Edge.Util/Logger.cs)는 정식 구현으로 사용됩니다.

## <a name="retrieve-module-logs"></a>모듈 로그 검색

**GetModuleLogs** 직접 메서드를 사용하여 IoT Edge 모듈의 로그를 검색합니다.

>[!TIP]
>Azure Portal의 IoT Edge 문제 해결 페이지는 모듈 로그를 보기 위한 간소화된 환경을 제공합니다. 자세한 내용은 [Azure Portal에서 IoT Edge 디바이스 모니터링 및 문제 해결](troubleshoot-in-portal.md)을 참조하세요.

이 메서드는 다음 스키마를 사용하여 JSON 페이로드를 허용합니다.

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Name | Type | Description |
|-|-|-|
| schemaVersion | 문자열 | `1.0` |
| items | JSON 배열 | `id` 및 `filter` 튜플이 포함된 배열입니다. |
| ID | 문자열 | 모듈 이름을 제공하는 정규식입니다. Edge 디바이스에서 여러 모듈을 일치시킬 수 있습니다. [.NET 정규식](/dotnet/standard/base-types/regular-expressions) 형식이 필요합니다. |
| filter | JSON 섹션 | 튜플의 `id` 정규식과 일치하는 모듈에 적용할 로그 필터입니다. |
| tail | 정수 | 최근부터 검색하는 과거의 로그 줄 수입니다. 옵션. |
| since | 문자열 | 이 시간 이후에는 로그를 기간(1일, 90분, 2일 3시간 2분), rfc3339 타임스탬프 또는 UNIX 타임스탬프로만 반환합니다.  `tail` 및 `since`를 모두 지정하는 경우에는 먼저 `since` 값을 사용하여 로그를 검색합니다. 그런 다음, `tail` 값이 결과에 적용되고 최종 결과가 반환됩니다. 옵션. |
| until | 문자열 | 지정된 시간 이전에는 로그를 rfc3339 타임스탬프, UNIX 타임스탬프 또는 기간(1일, 90분, 2일 3시간 2분)으로만 반환합니다. 옵션. |
| log level | 정수 | 지정된 로그 수준보다 작거나 같은 로그 줄을 필터링합니다. 로그 줄은 권장되는 로깅 형식을 따르고 [Syslog 심각도 수준](https://en.wikipedia.org/wiki/Syslog#Severity_level) 표준을 사용해야 합니다. 옵션. |
| regex | 문자열 | [.NET 정규식](/dotnet/standard/base-types/regular-expressions) 형식을 사용하여 지정된 정규식과 일치하는 내용이 있는 로그 줄을 필터링합니다. 옵션. |
| encoding | 문자열 | `gzip` 또는 `none`입니다. 기본값은 `none`입니다. |
| contentType | 문자열 | `json` 또는 `text`입니다. 기본값은 `text`입니다. |

> [!NOTE]
> 로그 내용이 직접 메서드의 응답 크기 제한(현재 128 KB)을 초과하는 경우 응답에서 오류를 반환합니다.

로그를 성공적으로 검색하면 **"status": 200** 다음에 모듈에서 검색된 로그가 포함된 페이로드가 반환되며 요청에 지정하는 설정에 따라 필터링됩니다.

예를 들면 다음과 같습니다.

```azurecli
az iot hub invoke-module-method --method-name 'GetModuleLogs' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
'
```

Azure Portal에서 메서드 이름이 `GetModuleLogs`이며 다음과 같은 JSON 페이로드가 있는 메서드를 호출합니다.

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Azure Portal에서 직접 메서드 'GetModuleLogs' 호출](./media/how-to-retrieve-iot-edge-logs/invoke-get-module-logs.png)

CLI 출력을 [gzip](https://en.wikipedia.org/wiki/Gzip)과 같은 Linux 유틸리티로 파이프하여 압축된 응답을 처리할 수도 있습니다. 예를 들면 다음과 같습니다.

```azurecli
az iot hub invoke-module-method \
  --method-name 'GetModuleLogs' \
  -n <hub name> \
  -d <device id> \
  -m '$edgeAgent' \
  --method-payload '{"contentType": "text","schemaVersion": "1.0","encoding": "gzip","items": [{"id": "edgeHub","filter": {"since": "2d","tail": 1000}}],}' \
  -o tsv --query 'payload[0].payloadBytes' \
  | base64 --decode \
  | gzip -d
```

## <a name="upload-module-logs"></a>모듈 로그 업로드

**UploadModuleLogs** 직접 메서드를 사용하여 요청된 로그를 지정된 Azure Blob Storage 컨테이너로 보냅니다.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> 게이트웨이 디바이스 뒤의 디바이스에서 로그를 업로드하려는 경우에는 최상위 계층 디바이스에서 [API 프록시 및 Blob Storage 모듈](how-to-configure-api-proxy-module.md)을 구성해야 합니다. 이러한 모듈은 게이트웨이 디바이스를 통해 하위 계층 디바이스의 로그를 클라우드의 스토리지로 라우팅합니다.

::: moniker-end

이 메서드는 "sasUrl" 키를 추가하여 **GetModuleLogs** 와 유사한 JSON 페이로드를 허용합니다.

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "Full path to SAS URL",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Name | Type | Description |
|-|-|-|
| sasURL | 문자열(URI) | [Azure Blob Storage 컨테이너에 대한 쓰기 권한이 있는 공유 액세스 서명 URL](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer)입니다. |

로그 업로드 요청이 성공하면 **"status": 200** 뒤에 다음 스키마가 포함된 페이로드가 반환됩니다.

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Name | Type | Description |
|-|-|-|
| 상태 | 문자열 | `NotStarted`, `Running`, `Completed`, `Failed`, `Unknown` 중 하나입니다. |
| message | 문자열 | 오류가 발생하면 메시지이고, 그러지 않으면 빈 문자열입니다. |
| correlationId | 문자열   | 업로드 요청의 상태를 쿼리할 ID입니다. |

예를 들면 다음과 같습니다.

다음 호출은 모든 모듈의 마지막 100개 로그 줄을 압축된 JSON 형식으로 업로드합니다.

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": ".*",
                "filter": {
                    "tail": 100
                }
            }
        ],
        "encoding": "gzip",
        "contentType": "json"
    }
'
```

다음 호출에서는 edgeAgent 및 edgeHub의 마지막 100개 로그 줄과 tempSensor 모듈의 마지막 1000개 로그 줄을 압축되지 않은 텍스트 형식으로 업로드합니다.

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": "edge",
                "filter": {
                    "tail": 100
                }
            },
            {
                "id": "tempSensor",
                "filter": {
                    "tail": 1000
                }
            }
        ],
        "encoding": "none",
        "contentType": "text"
    }
'
```

Azure Portal에서 sasURL을 사용자의 정보로 채운 후 메서드 이름이 `UploadModuleLogs`이며 다음 JSON 페이로드가 있는 메서드를 호출합니다.

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "<sasUrl>",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Azure Portal에서 직접 메서드 'UploadModuleLogs'를 호출합니다.](./media/how-to-retrieve-iot-edge-logs/invoke-upload-module-logs.png)

## <a name="upload-support-bundle-diagnostics"></a>지원 번들 진단 업로드

**UploadSupportBundle** 직접 메서드를 사용하여 IoT Edge 모듈 로그의 zip 파일을 번들화하고 사용 가능한 Azure Blob Storage 컨테이너에 업로드할 수 있습니다. 이 직접 메서드는 IoT Edge 디바이스에서 [`iotedge support-bundle`](./troubleshoot.md#gather-debug-information-with-support-bundle-command) 명령을 실행하여 로그를 가져옵니다.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> 게이트웨이 디바이스 뒤의 디바이스에서 로그를 업로드하려는 경우에는 최상위 계층 디바이스에서 [API 프록시 및 Blob Storage 모듈](how-to-configure-api-proxy-module.md)을 구성해야 합니다. 이러한 모듈은 게이트웨이 디바이스를 통해 하위 계층 디바이스의 로그를 클라우드의 스토리지로 라우팅합니다.

::: moniker-end

이 메서드는 다음 스키마를 사용하여 JSON 페이로드를 허용합니다.

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

| Name | Type | Description |
|-|-|-|
| schemaVersion | 문자열 | `1.0` |
| sasURL | 문자열(URI) | [Azure Blob Storage 컨테이너에 대한 쓰기 권한이 있는 공유 액세스 서명 URL](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer)입니다. |
| since | 문자열 | 이 시간 이후에는 로그를 기간(1일, 90분, 2일 3시간 2분), rfc3339 타임스탬프 또는 UNIX 타임스탬프로만 반환합니다. 옵션. |
| until | 문자열 | 지정된 시간 이전에는 로그를 rfc3339 타임스탬프, UNIX 타임스탬프 또는 기간(1일, 90분, 2일 3시간 2분)으로만 반환합니다. 옵션. |
| edgeRuntimeOnly | boolean | true이면 Edge 에이전트, Edge 허브, Edge 보안 디먼의 로그만 반환합니다. 기본값: false입니다.  옵션. |

> [!IMPORTANT]
> IoT Edge 지원 번들에는 개인 식별이 가능한 정보가 포함될 수 있습니다.

로그 업로드 요청이 성공하면 **"status": 200** 뒤에 **UploadModuleLogs** 응답과 동일한 스키마를 가진 페이로드가 반환됩니다.

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Name | Type | Description |
|-|-|-|
| 상태 | 문자열 | `NotStarted`, `Running`, `Completed`, `Failed`, `Unknown` 중 하나입니다. |
| message | 문자열 | 오류가 발생하면 메시지이고, 그러지 않으면 빈 문자열입니다. |
| correlationId | 문자열   | 업로드 요청의 상태를 쿼리할 ID입니다. |

예를 들면 다음과 같습니다.

```azurecli
az iot hub invoke-module-method --method-name 'UploadSupportBundle' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
'
```

Azure Portal에서 sasURL을 사용자의 정보로 채운 후 메서드 이름이 `UploadSupportBundle`이며 다음 JSON 페이로드가 있는 메서드를 호출합니다.

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

![Azure Portal에서 직접 메서드 'UploadSupportBundle'을 호출합니다.](./media/how-to-retrieve-iot-edge-logs/invoke-upload-support-bundle.png)

## <a name="get-upload-request-status"></a>업로드 요청 상태 가져오기

**GetTaskStatus** 직접 메서드를 사용하여 로그 업로드 요청의 상태를 쿼리합니다. **GetTaskStatus** 요청 페이로드는 로그 업로드 요청의`correlationId`를 사용하여 작업의 상태를 가져옵니다. `correlationId`는 **UploadModuleLogs** 직접 메서드 호출에 대한 응답으로 반환됩니다.

이 메서드는 다음 스키마를 사용하여 JSON 페이로드를 허용합니다.

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

로그 업로드 요청이 성공하면 **"status": 200** 뒤에 **UploadModuleLogs** 응답과 동일한 스키마를 가진 페이로드가 반환됩니다.

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Name | Type | Description |
|-|-|-|
| 상태 | 문자열 | , `NotStarted` , `Running` , , `Completed` `Failed` 'Cancelled' 또는 중 `Unknown` 하나입니다. |
| message | 문자열 | 오류가 발생하면 메시지이고, 그러지 않으면 빈 문자열입니다. |
| correlationId | 문자열   | 업로드 요청의 상태를 쿼리할 ID입니다. |

예를 들면 다음과 같습니다.

```azurecli
az iot hub invoke-module-method --method-name 'GetTaskStatus' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
'
```

Azure Portal에서 GUID를 사용자의 정보로 채운 후 메서드 이름이 `GetTaskStatus`이며 다음 JSON 페이로드가 있는 메서드를 호출합니다.

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

![Azure Portal에서 직접 메서드 'GetTaskStatus'를 호출합니다.](./media/how-to-retrieve-iot-edge-logs/invoke-get-task-status.png)

## <a name="next-steps"></a>다음 단계

[IoT Edge 에이전트 및 IoT Edge 허브 모듈 쌍의 속성](module-edgeagent-edgehub.md)
