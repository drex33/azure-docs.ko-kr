---
title: IoT Edge 버전 탐색 및 기록 - Azure IoT Edge
description: 최신 릴리스의 새로운 특징 및 기능에 대한 정보를 제공하는 IoT Edge의 새로운 기능을 알아봅니다.
author: kgremban
ms.author: kgremban
ms.date: 04/07/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9cc40014c1cb5cef195ea90fb0d0a7b41e726466
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241939"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Azure IoT Edge 버전 및 릴리스 정보

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge는 GitHub에 호스트된 오픈 소스 IoT Edge 프로젝트에서 빌드된 제품입니다. 모든 새 릴리스는 [Azure IoT Edge 프로젝트](https://github.com/Azure/azure-iotedge)에서 사용할 수 있습니다. 기여 및 버그 보고서는 [오픈 소스 IoT Edge 프로젝트](https://github.com/Azure/iotedge)에서 수행할 수 있습니다.

## <a name="documented-versions"></a>문서화된 버전

이 사이트에 대한 IoT Edge 설명서는 서로 다른 두 버전의 제품에 사용할 수 있으므로 IoT Edge 환경에 적용되는 콘텐츠를 선택할 수 있습니다. 현재 지원되는 두 가지 버전은 다음과 같습니다.

* **IoT Edge 1.2에는** 안정적인 최신 릴리스의 새로운 기능과 기능에 대한 콘텐츠가 포함되어 있습니다.
* **IoT Edge 1.1(LTS)** 은 IoT Edge의 첫 번째 LTS(장기 지원) 버전입니다. 이 버전의 설명서에서는 이전 버전부터 1.1까지의 모든 특징과 기능을 다루고 있습니다. 이 설명서 버전은 버전 1.1의 지원되는 수명 동안 안정적이며 이후 버전에서 릴리스된 새로운 기능을 반영하지 않습니다. IoT Edge 1.1 LTS는 [.NET Core 3.1 릴리스 수명 주기와](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)일치하도록 2022년 12월 3일까지 지원됩니다.

IoT Edge 릴리스에 대한 자세한 내용은 [Azure IoT Edge 지원 시스템](support.md)을 참조하세요.

## <a name="version-history"></a>버전 기록

다음 표에서는 IoT Edge 패키지 릴리스에 대한 최신 버전 기록을 제공하고 각 버전에 대해 수행된 문서 업데이트를 강조 표시합니다.

| 릴리스 정보 및 자산 | 유형 | Date | 주요 항목 |
| ------------------------ | ---- | ---- | ---------- |
| [1.2](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0) | Stable | 2021년 4월 | [게이트웨이 뒤에 있는 IoT Edge 디바이스](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[IoT Edge MQTT broker(미리 보기)](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true)<br>새로운 설치 및 구성 단계를 포함하는 새로운 IoT Edge 패키지가 도입되었습니다. 자세한 내용은 [1.0 또는 1.1에서 1.2로 업데이트](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)를 참조하세요.
| [1.1](https://github.com/Azure/azure-iotedge/releases/tag/1.1.0) | LTS(장기 지원) | 2021년 2월 | [장기 지원 계획 및 지원되는 시스템 업데이트](support.md) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Stable | 2020년 10월 | [UploadSupportBundle 직접 메서드](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[런타임 메트릭 업로드](how-to-access-built-in-metrics.md)<br>[경로 우선 순위 및 TTL(Time to Live)](module-composition.md#priority-and-time-to-live)<br>[모듈 시작 순서](module-composition.md#configure-modules)<br>[X.509 수동 프로비저닝](how-to-provision-single-device-linux-x509.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Stable | 2020년 3월 | DPS를 통한 X.509 자동 프로비저닝<br>[RestartModule 직접 메서드](how-to-edgeagent-direct-method.md#restart-module)<br>[support-bundle 명령](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>다음 단계

* [모든 Azure IoT Edge 릴리스 보기](https://github.com/Azure/azure-iotedge/releases)
* [피드백 포럼에서 기능 요청 만들기 또는 검토](https://feedback.azure.com/forums/907045-azure-iot-edge)