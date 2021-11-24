---
title: '빠른 시작: Azure Kinect 신체 추적 애플리케이션'
description: 첫 번째 Azure Kinect 신체 추적 애플리케이션을 빌드하는 방법에 대한 단계별 지침
author: qm13
ms.author: quentinm
ms.reviewer: yijwan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azure, 센서, sdk, 신체, 추적, 관철, 애플리케이션, 첫 번째
ms.custom: mode-other
ms.openlocfilehash: bcc7d0781eae22e1daf9f04983c5fc05d8b3d6d5
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133074602"
---
# <a name="quickstart-build-an-azure-kinect-body-tracking-application"></a>빠른 시작: Azure Kinect 신체 추적 애플리케이션

신체 추적 SDK 시작 이 빠른 시작을 통해 신체 추적을 수행할 수 있습니다. [Azure-Kinect-Sample 리포지토리](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples)에서 더 많은 예제를 찾을 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Azure Kinect DK 설정](set-up-azure-kinect-dk.md)
- [신체 추적 SDK 설정](body-sdk-setup.md)
- [첫 번째 Azure Kinect 애플리케이션 빌드](build-first-app.md) 빠른 시작을 진행합니다.
- 다음 센서 SDK 함수를 숙지합니다.
  - [k4a_device_open()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
  - [k4a_device_start_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
  - [k4a_device_stop_cameras()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  - [k4a_device_close()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)
- 다음 신체 추적 SDK 함수에 대한 설명서를 검토합니다.
  - [k4abt_tracker_create()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga1aa71481b8441def94de11b29e0e3cbc.html#ga1aa71481b8441def94de11b29e0e3cbc)
  - [k4abt_tracker_enqueue_capture()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e)
  - [k4abt_tracker_pop_result()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b)
  - [k4abt_tracker_shutdown()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga94036969ef94cbc414c78b3f6d04bfa5.html#ga94036969ef94cbc414c78b3f6d04bfa5)
  - [k4abt_tracker_destroy()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gab2c9afca092130976cd66077c0557ed1.html#gab2c9afca092130976cd66077c0557ed1)

## <a name="headers"></a>headers

신체 추적에는 단일 헤더 `k4abt.h`가 사용됩니다. `k4a.h`와 함께 이 헤더를 포함해야 합니다. 원하는 컴파일러가 센서 SDK와 신체 추적 SDK `lib` 및 `include` 폴더 모두에 대해 설정되어 있는지 확인합니다. 또한 `k4a.lib` 및 `k4abt.lib` 파일에 연결해야 합니다. 애플리케이션을 실행하려면 `k4a.dll`, `k4abt.dll`, `onnxruntime.dll` 및 `dnn_model.onnx`가 애플리케이션 실행 경로에 있어야 합니다.

```C
#include <k4a/k4a.h>
#include <k4abt.h>
```

## <a name="open-device-and-start-the-camera"></a>디바이스를 열고 카메라를 시작합니다.

첫 번째 신체 추적 애플리케이션은 단일 Azure Kinect 디바이스가 PC에 연결된 것으로 가정합니다.

신체 추적은 센서 SDK를 기반으로 합니다. 신체 추적을 사용하려면 먼저 디바이스를 열고 구성해야 합니다. [k4a_device_open()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) 함수를 사용하여 디바이스를 연 다음, [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) 개체를 사용하여 디바이스를 구성합니다. 최상의 결과를 얻을 수 있도록 깊이 모드를 `K4A_DEPTH_MODE_NFOV_UNBINNED` 또는 `K4A_DEPTH_MODE_WFOV_2X2BINNED`로 설정합니다. 깊이 모드가 `K4A_DEPTH_MODE_OFF` 또는 `K4A_DEPTH_MODE_PASSIVE_IR`로 설정되면 신체 추적기가 실행되지 않습니다.

디바이스를 찾고 여는 방법에 대한 자세한 내용은 [이 페이지](find-then-open-device.md)에서 확인할 수 있습니다.

Azure Kinect 깊이 모드에 대한 자세한 내용은 [하드웨어 사양](hardware-specification.md) 및 [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) 열거형 페이지에서 확인할 수 있습니다.

```C
k4a_device_t device = NULL;
k4a_device_open(0, &device);

// Start camera. Make sure depth camera is enabled.
k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
k4a_device_start_cameras(device, &deviceConfig);
```

## <a name="create-the-tracker"></a>추적기 만들기

신체 추적 결과를 가져오기 위한 첫 번째 단계는 신체 추적기를 만드는 것입니다. 그러려면 센서 보정 [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) 구조체가 필요합니다. 센서 보정은 [k4a_device_get_calibration()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) 함수를 사용하여 쿼리할 수 있습니다.

```C
k4a_calibration_t sensor_calibration;
k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration);

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker);
```

## <a name="get-captures-from-the-azure-kinect-device"></a>Azure Kinect 디바이스에서 캡처 가져오기

이미지 데이터를 검색하는 방법에 대한 자세한 내용은 [이 페이지](retrieve-images.md)에서 확인할 수 있습니다.

```C
// Capture a depth frame
k4a_capture_t sensor_capture;
k4a_device_get_capture(device, &sensor_capture, TIMEOUT_IN_MS);
```

## <a name="enqueue-the-capture-and-pop-the-results"></a>캡처를 큐에 추가하고 결과를 팝업

추적기는 Azure Kinect DK 캡처를 보다 효율적으로 처리하기 위해 입력 큐와 출력 큐를 내부적으로 유지 관리합니다. 다음 단계는 `k4abt_tracker_enqueue_capture()` 함수를 사용하여 새 캡처를 입력 큐에 추가하는 것입니다. `k4abt_tracker_pop_result()` 함수를 사용하여 출력 큐의 결과를 팝업합니다. 시간 제한 값은 애플리케이션에 따라 다르며 큐 대기 시간을 제어합니다.

첫 번째 신체 추적 애플리케이션은 실시간 처리 패턴을 사용합니다. 다른 패턴에 대한 자세한 설명은 [신체 추적 결과 가져오기](get-body-tracking-results.md)를 참조하세요.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

## <a name="access-the-body-tracking-result-data"></a>신체 추적 결과 데이터에 액세스

각 센서 캡처에 대한 신체 추적 결과는 신체 프레임 [k4abt_frame_t](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/structk4abt__frame__t.html) 구조체에 저장됩니다. 각 신체 프레임에는 세 가지 주요 구성 요소인 신체 구조체 컬렉션, 2D 신체 인덱스 맵 및 입력 캡처가 포함되어 있습니다.

첫 번째 신체 추적 애플리케이션은 감지된 신체 수에만 액세스합니다. 신체 프레임의 데이터에 대한 자세한 내용은 [신체 프레임의 데이터에 액세스](access-data-body-frame.md)를 참조하세요.

```C
size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
printf("%zu bodies are detected!\n", num_bodies);
```

## <a name="clean-up"></a>정리

마지막 단계는 신체 추적기를 종료하고 신체 추적 개체를 해제하는 것입니다. 또한 디바이스를 중지하고 닫아야 합니다.

```C
k4abt_tracker_shutdown(tracker);
k4abt_tracker_destroy(tracker);
k4a_device_stop_cameras(device);
k4a_device_close(device);
```

## <a name="full-source"></a>전체 소스

```C
#include <stdio.h>
#include <stdlib.h>

#include <k4a/k4a.h>
#include <k4abt.h>

#define VERIFY(result, error)                                                                            \
    if(result != K4A_RESULT_SUCCEEDED)                                                                   \
    {                                                                                                    \
        printf("%s \n - (File: %s, Function: %s, Line: %d)\n", error, __FILE__, __FUNCTION__, __LINE__); \
        exit(1);                                                                                         \
    }                                                                                                    \

int main()
{
    k4a_device_t device = NULL;
    VERIFY(k4a_device_open(0, &device), "Open K4A Device failed!");

    // Start camera. Make sure depth camera is enabled.
    k4a_device_configuration_t deviceConfig = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
    deviceConfig.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;
    deviceConfig.color_resolution = K4A_COLOR_RESOLUTION_OFF;
    VERIFY(k4a_device_start_cameras(device, &deviceConfig), "Start K4A cameras failed!");

    k4a_calibration_t sensor_calibration;
    VERIFY(k4a_device_get_calibration(device, deviceConfig.depth_mode, deviceConfig.color_resolution, &sensor_calibration),
        "Get depth camera calibration failed!");

    k4abt_tracker_t tracker = NULL;
    k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
    VERIFY(k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker), "Body tracker initialization failed!");

    int frame_count = 0;
    do
    {
        k4a_capture_t sensor_capture;
        k4a_wait_result_t get_capture_result = k4a_device_get_capture(device, &sensor_capture, K4A_WAIT_INFINITE);
        if (get_capture_result == K4A_WAIT_RESULT_SUCCEEDED)
        {
            frame_count++;
            k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
            k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
            if (queue_capture_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                // It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Add capture to tracker process queue timeout!\n");
                break;
            }
            else if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
            {
                printf("Error! Add capture to tracker process queue failed!\n");
                break;
            }

            k4abt_frame_t body_frame = NULL;
            k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
            if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
            {
                // Successfully popped the body tracking result. Start your processing

                size_t num_bodies = k4abt_frame_get_num_bodies(body_frame);
                printf("%zu bodies are detected!\n", num_bodies);

                k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
            }
            else if (pop_frame_result == K4A_WAIT_RESULT_TIMEOUT)
            {
                //  It should never hit timeout when K4A_WAIT_INFINITE is set.
                printf("Error! Pop body frame result timeout!\n");
                break;
            }
            else
            {
                printf("Pop body frame result failed!\n");
                break;
            }
        }
        else if (get_capture_result == K4A_WAIT_RESULT_TIMEOUT)
        {
            // It should never hit time out when K4A_WAIT_INFINITE is set.
            printf("Error! Get depth frame time out!\n");
            break;
        }
        else
        {
            printf("Get depth capture returned error: %d\n", get_capture_result);
            break;
        }

    } while (frame_count < 100);

    printf("Finished body tracking processing!\n");

    k4abt_tracker_shutdown(tracker);
    k4abt_tracker_destroy(tracker);
    k4a_device_stop_cameras(device);
    k4a_device_close(device);

    return 0;
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>[신체 추적 결과 가져오기](get-body-tracking-results.md)
