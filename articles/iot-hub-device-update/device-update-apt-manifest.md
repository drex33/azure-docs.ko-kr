---
title: Azure IoT Hub APT 매니페스트에 대한 디바이스 업데이트 이해 | Microsoft Docs
description: IoT Hub에 대한 디바이스 업데이트가 패키지 기반 업데이트에 APT 매니페스트를 사용하는 방식을 이해합니다.
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 24c0d04b6a29b813dc23604594ca4b1b1235dc37
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373216"
---
# <a name="device-update-apt-manifest"></a>디바이스 업데이트 APT 매니페스트

APT 매니페스트는 APT 업데이트 처리기에 필요한 업데이트 상세 정보를 설명하는 JSON 파일입니다. 이 파일을 다른 업데이트와 마찬가지로 Device Update for IoT Hub로 가져올 수 있습니다.

디바이스 업데이트로 업데이트를 가져오는 방법에 대한 [자세한 정보](import-update.md)를 알아봅니다.

## <a name="overview"></a>개요

APT 매니페스트가 디바이스 업데이트 에이전트에 업데이트로 배달되는 경우 에이전트는 매니페스트를 처리하고 필요한 작업을 수행합니다. 이러한 작업에는 APT 매니페스트 파일에 지정 된 패키지를 다운로드 하 고 설치 하는 작업과 지정 된 리포지토리에서의 종속성이 포함 됩니다.

디바이스 업데이트는 APT UpdateType와 APT 업데이트 처리기를 지원합니다. 이 지원을 통해 디바이스 업데이트 에이전트는 설치된 Debian 패키지를 평가하고 필요한 패키지를 업데이트할 수 있습니다. 

## <a name="schema"></a>스키마

APT 매니페스트 파일은 버전이 지정된 스키마가 있는 JSON 파일입니다.

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

예:

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>name

이 APT 매니페스트의 이름입니다. 시나리오에 의미 있는 이름 또는 ID 등 무엇이든 될 수 있습니다. 예들 들어 `contoso-iot-edge`입니다.

### <a name="version"></a>버전

이 APT 매니페스트에 대한 버전 번호입니다. 예들 들어 `1.0.0.0`입니다.


### <a name="packages"></a>패키지

패키지 관련 속성을 포함하는 개체의 목록입니다.

#### <a name="name"></a>name

패키지의 이름 또는 ID입니다. 예들 들어 `iotedge`입니다.

#### <a name="version"></a>버전

패키지에 원하는 버전의 기준입니다. 예들 들어 `1.0.8-2`입니다.

현재는 정확한 버전 번호만이 지원됩니다. 버전 번호는 [epoch:]upstream_version[-debian_revision] 형식의 원하는 Debian 패키지 버전입니다.

**epoch** 는 부호 없는 정수입니다.

**upstream_version** 에는 “.”, “+”, “-” 및 “~”와 같은 영숫자 및 문자를 포함할 수 있습니다. 숫자로 시작해야 합니다.
> [!NOTE]
> ‘1.0.8’은 ‘1.0.8-0’과 같습니다

예를 들어, **`"name":"iotedge" and "version":"1.0.8-2"`** 은 `apt-get install iotedge=1.0.8-2` 명령을 사용하여 패키지를 설치하는 것과 같습니다.

> [!NOTE]
> 버전 값에 등호가 없습니다

버전을 생략하면 지정된 패키지의 사용 가능한 최신 버전이 설치됩니다.

Debian 패키지의 버전을 지정하는 방법에 대한 [자세한 정보](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version)를 알아봅니다.

> [!NOTE]
> APT 패키지 관리자는 설치할 종속 패키지를 자동으로 확인하는 경우 패키지에서 제공하는 버전 관리 요구 사항을 무시합니다. 종속 패키지의 명시적 버전을 지정하지 않는 한 패키지 자체가 지정된 버전에서 엄격한 요구 사항(=)을 지정할 수 있는 경우에도 최신 버전을 사용합니다. 이 자동 해결은 충족되지 않는 종속성과 관련된 오류를 발생시킬 수 있습니다. [자세한 내용](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

Azure IoT Edge 보안 디먼의 특정 버전을 업데이트하는 경우 `iotedge` 패키지와 그에 종속된 `libiothsm-std` 패키지의 원하는 버전을 APT 매니페스트에 포함시켜야 합니다.
[자세한 내용](../iot-edge/how-to-update-iot-edge.md#update-the-security-daemon)

> [!NOTE]
> APT 매니페스트는 디바이스 업데이트 에이전트와 그에 종속된 항목을 업데이트하는 데 사용할 수 있습니다. 다른 패키지에 하는 것처럼 디바이스 업데이트 에이전트 이름과 원하는 버전을 APT 매니페스트에 나열합니다. 그런 다음 Device Update for IoT Hub 파이프라인을 통해 이 APT 매니페스트를 가져오고 배포할 수 있습니다. 

## <a name="removing-packages"></a>패키지 제거

APT 매니페스트를 사용하여 디바이스에 설치된 패키지를 제거할 수도 있습니다. 단일 APT 매니페스트를 사용하여 여러 패키지를 제거, 추가 및 업데이트할 수 있습니다. 패키지를 제거하려면 패키지 이름 뒤에 빼기 기호 “-”를 추가합니다. 제거할 패키지의 버전 번호를 포함하지 않아야 합니다. APT 매니페스트를 통해 패키지를 제거해도 패키지에 종속된 항목과 구성은 제거되지 않습니다.

예:

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
이 APT 매니페스트는 배포된 디바이스에서 패키지 “foo”를 제거합니다. 

## <a name="recommended-value-for-installed-criteria"></a>설치된 기준에 권장되는 값

APT 매니페스트에 설치된 기준은 `<name>-<version>`으로, 여기서 `<name>`는 APT 매니페스트의 이름이고 `<version>`는 APT 매니페스트의 버전입니다. 예들 들어 `contoso-iot-edge-1.0.0.0`입니다. 

## <a name="guidelines-on-creating-an-apt-manifest"></a>APT 매니페스트를 만드는 방법에 대한 지침

APT 매니페스트를 만드는 동안 다음과 같은 몇 가지 지침을 염두해 둬야 합니다.

- 항상 APT 매니페스트가 잘 구성된(Well-Formed) JSON 파일인지 확인합니다
- 각 APT 매니페스트에는 고유한 버전이 있어야 합니다. 표준화된 방법으로 APT 매니페스트 버전을 증가시켜 시나리오에 적합하고 시나리오를 쉽게 따를 수 있도록 합니다
- 각 개별 패키지가 원하는 상태가 되면 디바이스에 설치하려는 패키지의 정확한 이름과 버전을 지정합니다. 패키지에 대한 원본으로 사용하려는 패키지 리포지토리에 항상 값이 유효한지 검사합니다
- APT 매니페스트의 패키지가 설치/제거되어야 하는 순서대로 나열되는지 확인합니다
- 테스트 디바이스에서 패키지 설치의 유효성을 항상 검사하여 결과가 필요한지 확인합니다
- 특정 버전의 패키지를 설치하는 경우(예: `iotedge 1.0.9-1`) APT 매니페스트에 설치될 종속 패키지의 명시적 버전(예: `libiothsm 1.0.9-1`)도 포함하는 것이 좋습니다
- 반드시 필요한 것은 아니지만 디바이스가 알 수 없는 상태로 전환되는 것을 방지하기 위해 APT 매니페스트가 누적되어 있는지 확인합니다. 누적 업데이트를 사용하는 경우 디바이스에서 설치에 실패하거나 오프라인으로 전환하는 APT 업데이트 배포를 건너뛴 경우에도 관심 있는 모든 패키지의 원하는 버전이 있는지 확인할 수 있습니다

예를 들면 다음과 같습니다.

**기본 APT 매니페스트**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**잘못된 업데이트**

이 업데이트에는 bar 패키지가 포함되지만 foo 패키지는 포함되지 않습니다.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**양호한 업데이트**

이 업데이트는 foo 패키지를 포함하며 bar 패키지도 포함합니다.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [새 업데이트 가져오기](import-update.md)