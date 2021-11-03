---
title: 마이크 배열 구성 방법 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK에서 사용할 수 있도록 마이크 배열을 구성하는 방법을 알아봅니다.
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6fe1739006f3f5b9943076c917cc2e3ef5dc2efa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024565"
---
# <a name="how-to-configure-a-microphone-array"></a>마이크 배열을 구성하는 방법

이 문서에서는 [마이크 배열](./speech-sdk-microphone.md)을 구성하는 방법에 대해 알아봅니다. 여기에는 작동 각도 설정과 Speech Devices SDK에 사용되는 마이크를 선택하는 방법이 포함됩니다.

Speech Devices SDK는 [지침](./speech-sdk-microphone.md)에 따라 디자인된 마이크 배열에서 가장 잘 작동합니다. 마이크 배열 구성은 운영 체제에서 제공하거나 다음 방법 중 하나를 통해 제공할 수 있습니다.

Speech Devices SDK는 처음에 고정된 구성 집합에서 선택하여 마이크 배열을 지원했습니다.

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

Windows에서 마이크 배열 구성은 오디오 드라이버에서 제공됩니다.

v1.11.0부터 Speech Devices SDK는 [JSON 파일](https://aka.ms/sdsdk-micarray-json)의 구성도 지원합니다.


## <a name="windows"></a>Windows
Windows에서 마이크 배열 기하 구조 정보는 오디오 드라이버에서 자동으로 가져옵니다. 따라서 `DeviceGeometry`, `SelectedGeometry` 및 `MicArrayGeometryConfigFile` 속성은 선택 사항입니다. 빔 포밍 범위를 얻기 위한 용도로만 `MicArrayGeometryConfigFile`을 사용하여 제공된 [JSON 파일](https://aka.ms/sdsdk-micarray-json)을 사용합니다.

`AudioConfig::FromMicrophoneInput`을 사용하여 마이크 배열을 지정하면 지정된 마이크가 사용됩니다. 마이크가 지정되지 않았거나 `AudioConfig::FromDefaultMicrophoneInput`이 호출되면 Windows의 사운드 설정에 지정된 기본 마이크를 사용합니다.
Speech Devices SDK의 Microsoft Audio Stack은 16KHz의 정수 배수인 샘플 속도에 대한 다운 샘플링만 지원합니다.

## <a name="linux"></a>Linux
Linux에서는 마이크 기하 구조 정보를 제공해야 합니다. `DeviceGeometry` 및 `SelectedGeometry`의 사용은 계속 지원됩니다. `MicArrayGeometryConfigFile` 속성을 사용하여 JSON 파일을 통해 제공할 수도 있습니다. Windows와 마찬가지로 JSON 파일에서 빔 포밍 범위를 제공할 수 있습니다.

`AudioConfig::FromMicrophoneInput`을 사용하여 마이크 배열을 지정하면 지정된 마이크가 사용됩니다. 마이크가 지정되지 않았거나 `AudioConfig::FromDefaultMicrophoneInput`이 호출되면 *default* 라는 이름의 ALSA 디바이스에서 녹음됩니다. 기본적으로 *default* 는 항상 카드 0 디바이스 0을 가리키지만 사용자는 `asound.conf` 파일에서 변경할 수 있습니다. 

Speech Devices SDK의 Microsoft Audio Stack은 16KHz의 정수 배수인 샘플 속도에 대한 다운 샘플링만 지원합니다. 또한 32비트 IEEE little endian float, 32비트 little endian signed int, 24비트 little endian signed int, 16비트 little endian signed int 및 8비트 signed int 형식이 지원됩니다.

## <a name="android"></a>Android
현재 Speech Devices SDK는 [Roobo v1](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)만 지원합니다. 동작은 이전 릴리스와 동일하지만 이제 `MicArrayGeometryConfigFile` 속성을 사용하여 빔 포밍 범위가 포함된 JSON 파일을 지정할 수 있습니다.

## <a name="microphone-array-configuration-json"></a>마이크 배열 구성 JSON

마이크 배열 기하 구조 구성을 위한 JSON 파일은 [JSON 스키마](https://aka.ms/sdsdk-micarray-json)를 따릅니다. 다음은 스키마를 따르는 몇 가지 예입니다.


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


또는


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

또는

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 디바이스 선택](get-speech-devices-sdk.md)
