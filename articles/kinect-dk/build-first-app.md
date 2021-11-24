---
title: 빠른 시작 - 첫 번째 Azure Kinect 애플리케이션 빌드
description: 이 빠른 시작에서는 Azure Kinect DK 사용자에게 새 애플리케이션을 만드는 과정을 안내합니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azure, 센서, sdk, 마이크, 마이크 액세스, 마이크 데이터
ms.custom: mode-other
ms.openlocfilehash: c280f8b06167888f8cd5795535c399436b1c6924
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133074618"
---
# <a name="quickstart-build-your-first-azure-kinect-application"></a>빠른 시작: 첫 번째 Azure Kinect 애플리케이션 빌드

Azure Kinect DK 시작 이 빠른 시작을 통해 디바이스를 가동할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

지원되는 함수는 다음과 같습니다.

- [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
- [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
- [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
- [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="prerequisites"></a>필수 구성 요소

1. [Azure Kinect DK 디바이스를 설정합니다](set-up-azure-kinect-dk.md).
2. Azure Kinect 센서 SDK를 [다운로드](sensor-sdk-download.md)하여 설치합니다.

## <a name="headers"></a>headers

필요한 헤더는 `k4a.h` 하나뿐입니다. 원하는 컴파일러가 SDK의 라이브러리를 사용하여 설정되었는지 확인하고 폴더를 포함시킵니다. 또한 `k4a.lib` 및 `k4a.dll` 파일을 연결해야 합니다. [Azure Kinect 라이브러리를 프로젝트에 추가](add-library-to-project.md)를 검토하면 더 좋습니다.

```C
#include <k4a/k4a.h>
```

## <a name="finding-an-azure-kinect-dk-device"></a>Azure Kinect DK 디바이스 찾기

여러 Azure Kinect DK 디바이스를 컴퓨터에 연결할 수 있습니다. 먼저 [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) 함수를 사용하여 디바이스가 몇 대나 연결되어 있는지 확인하는 것부터 시작하겠습니다. 이 함수는 추가 설정 없이 바로 작동합니다.

```C
uint32_t count = k4a_device_get_installed_count();
```

컴퓨터에 연결된 디바이스가 있는지 확인한 후에는 [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)를 사용하여 디바이스를 열 수 있습니다. 열고 싶은 디바이스 인덱스를 제공해도 되고, 첫 번째 디바이스에 `K4A_DEVICE_DEFAULT`를 사용해도 됩니다.

```C
// Open the first plugged in Kinect device
k4a_device_t device = NULL;
k4a_device_open(K4A_DEVICE_DEFAULT, &device);
```
Azure Kinect 라이브러리에 있는 대부분의 항목이 그렇듯이, 항목을 열고 사용을 마친 후 닫아야 합니다. 종료할 때 잊지 말고 [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)를 호출해야 합니다.

```C
k4a_device_close(device);
```

디바이스가 열리면 테스트를 통해 디바이스가 작동하는지 확인할 수 있습니다. 디바이스의 일련 번호를 읽어 보겠습니다.

```C
// Get the size of the serial number
size_t serial_size = 0;
k4a_device_get_serialnum(device, NULL, &serial_size);

// Allocate memory for the serial, then acquire it
char *serial = (char*)(malloc(serial_size));
k4a_device_get_serialnum(device, serial, &serial_size);
printf("Opened device: %s\n", serial);
free(serial);
```

## <a name="starting-the-cameras"></a>카메라 시작

디바이스를 연 후에는 [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) 개체를 사용하여 카메라를 구성해야 합니다. 카메라 구성에는 다양한 옵션이 있습니다. 각자의 시나리오에 가장 적합한 설정을 선택합니다.

```C
// Configure a stream of 4096x3072 BRGA color data at 15 frames per second
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps       = K4A_FRAMES_PER_SECOND_15;
config.color_format     = K4A_IMAGE_FORMAT_COLOR_BGRA32;
config.color_resolution = K4A_COLOR_RESOLUTION_3072P;

// Start the camera with the given configuration
k4a_device_start_cameras(device, &config);

// ...Camera capture and application specific code would go here...

// Shut down the camera when finished with application logic
k4a_device_stop_cameras(device);
```

## <a name="error-handling"></a>오류 처리

간단하고 명확한 연습을 위해, 일부 인라인 예제의 오류 처리 방법을 생략하겠습니다. 그러나 오류 처리는 항상 중요합니다. 많은 함수가 일반적인 성공/실패 유형 [`k4a_result_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga4b419a99aa2220b076a4520dc2afd1e5.html#ga4b419a99aa2220b076a4520dc2afd1e5) 또는 [`k4a_wait_result_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga44c7c0c1cfba7c879e9e2da1a869e4ee.html#ga44c7c0c1cfba7c879e9e2da1a869e4ee)처럼 구체적인 정보가 포함된 구체적인 변형을 반환합니다. 각 함수에 대한 문서 또는 IntelliSense를 확인하여 어떤 오류 메시지가 표시되는지 알아보세요.

[`K4A_SUCCEEDED`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___macros_ga8e5b48150bc243c6052793bd830c2fcd.html#ga8e5b48150bc243c6052793bd830c2fcd) 및 [`K4A_FAILED`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___macros_ga7c2e32349135d008b6f836c571d434b4.html#ga7c2e32349135d008b6f836c571d434b4) 매크로를 사용하여 함수의 결과를 확인할 수 있습니다. 따라서 Azure Kinect DK 디바이스를 그냥 열지 말고, 다음과 같이 함수 호출을 보호해야 합니다.

```C
// Open the first plugged in Kinect device
k4a_device_t device = NULL;
if ( K4A_FAILED( k4a_device_open(K4A_DEVICE_DEFAULT, &device) ) )
{
    printf("Failed to open k4a device!\n");
    return;
}
```

## <a name="full-source"></a>전체 소스

```C
#pragma comment(lib, "k4a.lib")
#include <k4a/k4a.h>

#include <stdio.h>
#include <stdlib.h>

int main()
{
    uint32_t count = k4a_device_get_installed_count();
    if (count == 0)
    {
        printf("No k4a devices attached!\n");
        return 1;
    }

    // Open the first plugged in Kinect device
    k4a_device_t device = NULL;
    if (K4A_FAILED(k4a_device_open(K4A_DEVICE_DEFAULT, &device)))
    {
        printf("Failed to open k4a device!\n");
        return 1;
    }

    // Get the size of the serial number
    size_t serial_size = 0;
    k4a_device_get_serialnum(device, NULL, &serial_size);

    // Allocate memory for the serial, then acquire it
    char *serial = (char*)(malloc(serial_size));
    k4a_device_get_serialnum(device, serial, &serial_size);
    printf("Opened device: %s\n", serial);
    free(serial);

    // Configure a stream of 4096x3072 BRGA color data at 15 frames per second
    k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    config.camera_fps       = K4A_FRAMES_PER_SECOND_15;
    config.color_format     = K4A_IMAGE_FORMAT_COLOR_BGRA32;
    config.color_resolution = K4A_COLOR_RESOLUTION_3072P;

    // Start the camera with the given configuration
    if (K4A_FAILED(k4a_device_start_cameras(device, &config)))
    {
        printf("Failed to start cameras!\n");
        k4a_device_close(device);
        return 1;
    }

    // Camera capture and application specific code would go here

    // Shut down the camera when finished with application logic
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}

```

## <a name="next-steps"></a>다음 단계

센서 SDK를 사용하여 Azure Kinect DK 디바이스를 찾고 여는 방법 알아보기
> [!div class="nextstepaction"]
>[디바이스를 찾아서 열기](find-then-open-device.md)
