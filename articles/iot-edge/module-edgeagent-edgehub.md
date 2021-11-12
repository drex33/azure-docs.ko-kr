---
title: 에이전트 및 허브 모듈 쌍의 속성 - Azure IoT Edge
description: Edge 에이전트 및 Edge 허브 모듈 트윈스에 대한 특정 속성 및 해당 값 검토
author: kgremban
ms.author: kgremban
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 71f7402fbd593d2fc97c2ec9574a48a2c28d53cb
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402469"
---
# <a name="properties-of-the-iot-edge-agent-and-iot-edge-hub-module-twins"></a>IoT Edge 에이전트 및 IoT Edge 허브 모듈 쌍의 속성

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge 에이전트 및 IoT Edge 허브는 IoT Edge 런타임을 구성하는 두 가지 모듈입니다. 각 런타임 모듈의 책임에 대한 자세한 내용은 [Azure IoT Edge 런타임 및 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

이 문서에서는 런타임 모듈 트윈스의 desired 속성 및 reported 속성을 제공합니다. IoT Edge 디바이스에 모듈을 배포하는 방법에 대한 자세한 내용은 [IoT Edge에서 모듈을 배포하고 경로를 설정하는 방법 알아보기](module-composition.md)를 참조하세요.

모듈 쌍은 다음을 포함합니다.

* **desired 속성**. 솔루션 백 엔드에서 desired 속성을 설정하고, 모듈에서 해당 속성을 읽을 수 있습니다. 모듈에서 desired 속성의 변경 내용 알림도 받을 수 있습니다. 원하는 속성은 reported 속성과 함께 모듈 구성 또는 조건을 동기화하는 데 사용됩니다.

* **reported 속성**. 모듈에서 reported 속성을 설정하고, 솔루션 백 엔드에서 해당 속성을 읽고 쿼리할 수 있습니다. reported 속성은 desired 속성과 함께 모듈 구성 또는 조건을 동기화하는 데 사용됩니다.

## <a name="edgeagent-desired-properties"></a>Edge 에이전트 desired 속성

IoT Edge 에이전트에 대한 모듈 쌍은 `$edgeAgent`라고 하며, 디바이스에서 실행 중인 IoT Edge 에이전트와 IoT Hub 간의 통신을 조정합니다. 단일 디바이스 또는 대규모 배포의 일부로 특정 디바이스에 배포 매니페스트를 적용할 때 desired 속성이 설정됩니다.

| 속성 | Description | 필수 |
| -------- | ----------- | -------- |
| schemaVersion | “1.0” 또는 “1.1”. IoT Edge 버전 1.0.10에서 도입된 버전 1.1을 사용하는 것이 좋습니다. | 예 |
| runtime.type | "docker"여야 합니다. | 예 |
| runtime.settings.minDockerVersion | 이 배포 매니페스트에 필요한 최소 Docker 버전으로 설정합니다. | 예 |
| runtime.settings.loggingOptions | IoT Edge 에이전트 컨테이너에 대한 로깅 옵션을 포함하는 문자열 형식 JSON입니다. [Docker 로깅 옵션](https://docs.docker.com/engine/admin/logging/overview/) | No |
| runtime.settings.registryCredentials<br>.{registryId}.username | 컨테이너 레지스트리의 사용자 이름입니다. Azure Container Registry의 경우 사용자 이름은 일반적으로 레지스트리 이름입니다.<br><br> 모든 프라이빗 모듈 이미지에는 레지스트리 자격 증명이 필요합니다. | No |
| runtime.settings.registryCredentials<br>.{registryId}.password | 컨테이너 레지스트리에 대한 암호입니다. | No |
| runtime.settings.registryCredentials<br>.{registryId}.address | 컨테이너 레지스트리의 주소입니다. Azure Container Registry의 경우 주소는 일반적으로 *{registryname}.azurecr.io* 입니다. | No |  
| systemModules.edgeAgent.type | "docker"여야 합니다. | 예 |
| systemModules.edgeAgent.settings.image | IoT Edge 에이전트 이미지의 URI입니다. 현재 IoT Edge 에이전트는 자체적으로 업데이트할 수 없습니다. | 예 |
| systemModules.edgeAgent.settings<br>.createOptions | IoT Edge 에이전트 컨테이너에 대한 만들기 옵션을 포함하는 문자열 형식 JSON입니다. [Docker 만들기 옵션](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeAgent.configuration.id | 이 모듈을 배포한 배포의 ID입니다. | IoT Hub는 배포를 사용하여 매니페스트를 적용할 때 이 속성을 설정합니다. 배포 매니페스트의 일부가 아닙니다. |
| systemModules.edgeHub.type | "docker"여야 합니다. | 예 |
| systemModules.edgeHub.status | "running"이어야 합니다. | 예 |
| systemModules.edgeHub.restartPolicy | "always"여야 합니다. | 예 |
| systemModules.edgeHub.startupOrder | 모듈의 시작 순서에 해당하는 정수 값입니다. 0이 첫 번째이고 최대 정수(4294967295)가 마지막입니다. 값을 제공하지 않으면 기본값은 최대 정수입니다.  | No |
| systemModules.edgeHub.settings.image | IoT Edge 허브 이미지의 URI입니다. | 예 |
| systemModules.edgeHub.settings<br>.createOptions | IoT Edge 허브 컨테이너에 대한 만들기 옵션을 포함하는 문자열 형식 JSON입니다. [Docker 만들기 옵션](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| systemModules.edgeHub.configuration.id | 이 모듈을 배포한 배포의 ID입니다. | IoT Hub는 배포를 사용하여 매니페스트를 적용할 때 이 속성을 설정합니다. 배포 매니페스트의 일부가 아닙니다. |
| modules.{moduleId}.version | 이 모듈의 버전을 나타내는 사용자 정의 문자열입니다. | 예 |
| modules.{moduleId}.type | "docker"여야 합니다. | 예 |
| modules.{moduleId}.status | {“running” \| “stopped”} | 예 |
| modules.{moduleId}.restartPolicy | {“never” \| “on-failure” \| “on-unhealthy” \| “always”} | 예 |
| modules.{moduleId}.startupOrder | 모듈의 시작 순서에 해당하는 정수 값입니다. 0이 첫 번째이고 최대 정수(4294967295)가 마지막입니다. 값을 제공하지 않으면 기본값은 최대 정수입니다.  | No |
| modules.{moduleId}.imagePullPolicy | {“on-create” \| “never”} | No |
| modules.{moduleId}.env | 모듈에 전달할 환경 변수 목록입니다. `"<name>": {"value": "<value>"}` 형식을 사용합니다. | No |
| modules.{moduleId}.settings.image | 모듈 이미지에 대한 URI입니다. | 예 |
| modules.{moduleId}.settings.createOptions | 모듈 컨테이너에 대한 만들기 옵션을 포함하는 문자열 형식 JSON입니다. [Docker 만들기 옵션](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) | No |
| modules.{moduleId}.configuration.id | 이 모듈을 배포한 배포의 ID입니다. | IoT Hub는 배포를 사용하여 매니페스트를 적용할 때 이 속성을 설정합니다. 배포 매니페스트의 일부가 아닙니다. |

## <a name="edgeagent-reported-properties"></a>Edge 에이전트 reported 속성

IoT Edge 에이전트 reported 속성에는 다음 세 가지 주요 정보가 포함됩니다.

1. 마지막으로 desired 속성이 표시된 애플리케이션의 상태
2. IoT Edge 에이전트에서 보고한 현재 디바이스에서 실행 중인 모듈의 상태
3. 현재 디바이스에서 실행 중인 desired 속성의 복사본

현재 desired 속성의 복사본은 디바이스에서 최신 배포를 적용했는지 아니면 이전 배포 매니페스트를 계속 실행하고 있는지를 확인하는 데 유용합니다.

> [!NOTE]
> 보고된 IoT Edge 에이전트의 속성은 [IoT Hub 쿼리 언어](../iot-hub/iot-hub-devguide-query-language.md)로 쿼리하여 대규모 배포의 상태를 조사할 수 있으므로 유용합니다. 상태에 대해 IoT Edge 에이전트 속성을 사용하는 방법에 대한 자세한 내용은 [단일 디바이스 또는 대규모 IoT Edge 배포에 대한 이해](module-deployment-monitoring.md)를 참조하세요.

다음 표에는 desired 속성에서 복사한 정보가 포함되어 있지 않습니다.

| 속성 | Description |
| -------- | ----------- |
| lastDesiredVersion | 이 정수는 IoT Edge 에이전트에서 처리하는 desired 속성의 마지막 버전을 나타냅니다. |
| lastDesiredStatus.code | 해당 상태 코드는 IoT Edge 에이전트에서 마지막으로 표시된 desired 속성을 나타내는 상태 코드입니다. 허용되는 값: `200` 성공, `400` 잘못된 구성, `412` 잘못된 스키마 버전, `417` 비어 있는 desired 속성, `500` 실패 |
| lastDesiredStatus.description | 상태에 대한 텍스트 설명입니다. |
| runtime.platform.OS | 디바이스에서 실행 중인 OS를 보고합니다. |
| runtime.platform.architecture | 디바이스의 CPU 아키텍처를 보고합니다. |
| systemModules.edgeAgent.runtimeStatus | IoT Edge 에이전트의 보고된 상태: {“running” \| “unhealthy”} |
| systemModules.edgeAgent.statusDescription | IoT Edge 에이전트의 reported 상태에 대한 텍스트 설명입니다. |
| systemModules.edgeHub.runtimeStatus | IoT Edge 허브의 상태: { “running” \| “stopped” \| “failed” \| “backoff” \| “unhealthy” } |
| systemModules.edgeHub.statusDescription | 비정상인 경우 IoT Edge 허브의 상태에 대한 텍스트 설명입니다. |
| systemModules.edgeHub.exitCode | 컨테이너가 종료되는 경우 IoT Edge 허브 컨테이너에서 보고하는 종료 코드입니다. |
| systemModules.edgeHub.startTimeUtc | IoT Edge 허브가 마지막으로 시작된 시간입니다. |
| systemModules.edgeHub.lastExitTimeUtc | IoT Edge 허브가 마지막으로 종료된 시간입니다. |
| systemModules.edgeHub.lastRestartTimeUtc | IoT Edge 허브가 마지막으로 다시 시작된 시간입니다. |
| systemModules.edgeHub.restartCount | 이 모듈이 다시 시작 정책의 일부로 다시 시작된 횟수입니다. |
| modules.{moduleId}.runtimeStatus | 모듈의 상태: { “running” \| “stopped” \| “failed” \| “backoff” \| “unhealthy” } |
| modules.{moduleId}.statusDescription | 비정상인 경우 모듈의 상태에 대한 텍스트 설명입니다. |
| modules.{moduleId}.exitCode | 컨테이너가 종료되는 경우 모듈 컨테이너가 보고하는 종료 코드입니다. |
| modules.{moduleId}.startTimeUtc | 모듈이 마지막으로 시작된 시간입니다. |
| modules.{moduleId}.lastExitTimeUtc | 모듈이 마지막으로 종료된 시간입니다. |
| modules.{moduleId}.lastRestartTimeUtc | 모듈이 마지막으로 다시 시작된 시간입니다. |
| modules.{moduleId}.restartCount | 이 모듈이 다시 시작 정책의 일부로 다시 시작된 횟수입니다. |

## <a name="edgehub-desired-properties"></a>Edge 허브 desired 속성

IoT Edge 허브에 대한 모듈 쌍은 `$edgeHub`라고 하며, 디바이스에서 실행 중인 IoT Edge 허브와 IoT Hub 간의 통신을 조정합니다. 단일 디바이스 또는 대규모 배포의 일부로 특정 디바이스에 배포 매니페스트를 적용할 때 desired 속성이 설정됩니다.

| 속성 | Description | 배포 매니페스트에 필요합니다. |
| -------- | ----------- | -------- |
| schemaVersion | “1.0” 또는 “1.1”. IoT Edge 버전 1.0.10에서 도입된 버전 1.1을 사용하는 것이 좋습니다. | 예 |
| routes.{routeName} | IoT Edge 허브 경로를 나타내는 문자열입니다. 자세한 내용은 [경로 선언](module-composition.md#declare-routes)을 참조하세요. | `routes` 요소는 존재하지만 비어 있을 수 있습니다. |
| storeAndForwardConfiguration.timeToLiveSecs | IoT Hub 또는 로컬 모듈과 관계없이 라우팅 엔드포인트에서 연결이 끊어진 경우 IoT Edge 허브에서 메시지를 보관하는 시간(초)입니다. 값은 임의의 양의 정수일 수 있습니다. | 예 |

## <a name="edgehub-reported-properties"></a>Edge 허브 reported 속성

| 속성 | Description |
| -------- | ----------- |
| lastDesiredVersion | 이 정수는 IoT Edge 허브에서 처리하는 desired 속성의 마지막 버전을 나타냅니다. |
| lastDesiredStatus.code | IoT Edge 허브에서 마지막으로 표시된 desired 속성을 나타내는 상태 코드입니다. 허용되는 값: `200` 성공, `400` 잘못된 구성, `500` 실패 |
| lastDesiredStatus.description | 상태에 대한 텍스트 설명입니다. |
| clients.{device or moduleId}.status | 이 디바이스 또는 모듈의 연결 상태입니다. 가능한 값 {“connected” \| “disconnected”}. 모듈 ID만 연결이 끊긴 상태가 될 수 있습니다. 연결된 경우에만 IoT Edge 허브에 연결된 다운스트림 디바이스가 표시됩니다. |
| clients.{device or moduleId}.lastConnectTime | 디바이스 또는 모듈이 마지막으로 연결된 시간입니다. |
| clients.{device or moduleId}.lastDisconnectTime | 디바이스 또는 모듈의 연결이 마지막으로 끊긴 시간입니다. |

## <a name="next-steps"></a>다음 단계

이러한 속성을 사용하여 배포 매니페스트를 빌드하는 방법을 알아보려면 [IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법에 대한 이해](module-composition.md)를 참조하세요.
