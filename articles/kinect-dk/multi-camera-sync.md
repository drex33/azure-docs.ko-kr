---
title: 여러 Azure Kinect DK 디바이스 동기화
description: 이 문서에서는 다중 디바이스 동기화의 이점과 동기화할 디바이스를 설정하는 방법을 살펴봅니다.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: Azure, Kinect, 사양, 하드웨어, DK, 기능, 깊이, 색, RGB, IMU, 배열, 깊이, 다중, 동기화
ms.openlocfilehash: 5e4936e5c7b795745b7f1b131efbf99a562d8ed0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254687"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>여러 Azure Kinect DK 디바이스 동기화

각 Azure Kinect DK 디바이스에는 여러 디바이스를 함께 연결하는 데 사용할 수 있는 3.5mm 동기화 포트(**동기화 입력** 및 **동기화 출력**)가 포함되어 있습니다. 디바이스가 연결되면 소프트웨어에서 디바이스 간의 트리거 타이밍을 조정할 수 있습니다. 

이 문서에서는 디바이스를 연결하고 동기화하는 방법에 대해 설명합니다.

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>여러 Azure Kinect DK 디바이스를 사용하는 경우의 이점

다음을 포함하여 여러 Azure Kinect DK 디바이스를 사용하는 데는 여러 가지 이유가 있습니다.

- 폐색을 채웁니다. Azure Kinect DK 데이터 변환은 단일 이미지를 생성하지만 두 카메라(깊이 및 RGB)는 실제로 약간 떨어져 있습니다. 오프셋을 사용하면 폐색이 가능합니다. 전경 개체가 디바이스의 두 카메라 중 하나에 대한 배경 개체의 일부 보기를 차단할 때 폐색이 발생합니다. 결과 색 이미지에서 전경 개체는 배경 개체의 그림자를 캐스트하는 것처럼 보입니다.  
   예를 들어 다음 다이어그램에서 왼쪽 카메라는 회색 픽셀 "P2"를 주시하지만, 흰색 전경 개체는 오른쪽 카메라 IR 빔을 차단합니다. 오른쪽 카메라에는 "P2"에 대한 데이터가 없습니다.  
   ![두 대의 카메라가 동일한 지점을 향하고, 그 중 하나가 차단된 상태를 보여 주는 다이어그램](./media/occlusion.png)  
   동기화된 추가 디바이스에서 폐색된 데이터를 제공할 수 있습니다.
- 개체를 3차원으로 스캔합니다.
- 유효 프레임 속도를 30FPS(초당 프레임 수)보다 큰 값으로 높입니다.
- 동일한 장면에서 여러 개의 4K 색 이미지를 캡처합니다. 모든 색 이미지는 노출 중심에서 100&mu;(마이크로초) 이내에 정렬되어 있습니다.
- 공간 내의 카메라 적용 범위를 늘립니다.

## <a name="plan-your-multi-device-configuration"></a>다중 디바이스 구성 계획

시작하기 전에 [Azure Kinect DK 하드웨어 사양](hardware-specification.md) 및 [Azure Kinect DK 깊이 카메라](depth-camera.md)를 검토해야 합니다.

> [!NOTE]  
> 외부 플라스틱 덮개를 제거하여 동기화 입력 및 동기화 출력 잭을 노출시킵니다.

### <a name="select-a-device-configuration"></a>디바이스 구성 선택

다음 방법 중 하나를 디바이스 구성에 사용할 수 있습니다.

- **데이지 체인 구성**. 하나의 마스터 디바이스와 최대 8개의 하위 디바이스를 동기화합니다.  
   ![Azure Kinect DK 디바이스를 데이지 체인 구성으로 연결하는 방법을 보여 주는 다이어그램](./media/multicam-sync-daisychain.png)
- **별 모양 구성**. 하나의 마스터 디바이스와 최대 두 개의 하위 디바이스를 동기화합니다.  
   ![여러 Azure DK 디바이스를 별 모양 구성으로 설정하는 방법을 보여 주는 다이어그램](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>외부 동기화 트리거 사용

두 구성 모두에서 마스터 디바이스는 하위 디바이스에 대한 트리거 신호를 제공합니다. 그러나 동기화 트리거에는 사용자 지정 외부 원본을 사용할 수 있습니다. 예를 들어 이 옵션을 사용하여 이미지 캡처를 다른 장비와 동기화할 수 있습니다. 데이지 체인 구성 또는 별 모양 구성에서 외부 트리거 원본은 마스터 디바이스에 연결됩니다.

외부 트리거 원본은 마스터 디바이스와 동일한 방식으로 작동해야 합니다. 다음과 같은 특성이 있는 동기화 신호를 전송해야 합니다.

- 활성 높음
- 펄스 너비: 8&mu; 초과
- 5V TTL/CMOS
- 최대 구동 용량: 8mA(밀리암페어) 미만
- 주파수 지원: 정확히 30FPS, 15FPS 및 5FPS(컬러 카메라 마스터 VSYNC 신호의 주파수)

트리거 원본은 3.5mm 오디오 케이블을 사용하여 신호를 마스터 디바이스 **동기화 입력** 포트로 전송해야 합니다. 스테레오 또는 모노 케이블을 사용할 수 있습니다. Azure Kinect DK는 오디오 케이블 커넥터의 모든 슬리브와 링을 함께 단락시키고 접지합니다. 다음 다이어그램에서와 같이 디바이스는 커넥터 팁에서만 동기화 신호를 받습니다.

![외부 트리거 신호에 대한 케이블 구성](./media/resources/camera-trigger-signal.jpg)

외부 장비를 사용하는 방법에 대한 자세한 내용은 [외부 동기화 디바이스에 Azure Kinect 레코더 사용](record-external-synchronized-units.md)을 참조하세요.

> [!NOTE]  
> 동기화 출력은 RGB 카메라용 VSync입니다. 모든 디바이스에 대한 타임스탬프는 0으로 설정되고 수를 계산합니다. Microsoft는 동기화 펄스의 최소 및 최대 너비의 특성을 지정하지 않으며, Azure Kinect DK의 동기화 출력에서 생성되는 펄스를 모방하는 것이 좋습니다.

### <a name="plan-your-camera-settings-and-software-configuration"></a>카메라 설정 및 소프트웨어 구성 계획

카메라를 제어하고 이미지 데이터를 사용하도록 소프트웨어를 설정하는 방법에 대한 자세한 내용은 [Azure Kinect 센서 SDK](about-sensor-sdk.md)를 참조하세요.

이 섹션에서는 동기화된 디바이스(단일 디바이스가 아님)에 영향을 주는 여러 요소에 대해 설명합니다. 소프트웨어에서 이러한 요소를 고려해야 합니다.

#### <a name="exposure-considerations"></a>노출 고려 사항
각 디바이스의 정확한 타이밍을 제어하려면 수동 노출 설정을 사용하는 것이 좋습니다. 자동 노출 설정에서 각 컬러 카메라는 실제 노출을 동적으로 변경할 수 있습니다. 노출이 타이밍에 영향을 주므로 이러한 변경으로 인해 카메라가 빠르게 동기화되지 않습니다.

이미지 캡처 반복에서 동일한 노출 설정을 반복적으로 설정하지 마세요. 필요한 경우 API를 한 번만 호출합니다.

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>여러 깊이 카메라 간의 간섭 방지

여러 개의 깊이 카메라가 겹치는 시야를 이미징하는 경우 각 카메라에서 자체 연결된 레이저를 이미징해야 합니다. 레이저가 서로 간섭하지 않도록 방지하려면 카메라 캡처를 서로 160µs 이상으로 오프셋해야 합니다.

각 깊이 카메라 캡처에 대해 레이저는 9번 켜지고, 매번 125&mu; 동안만 활성화됩니다. 그런 다음, 작업 모드에 따라 1450 &mu; s 또는 2390 s에 대해 공더레이션이 유휴 &mu; 상태가 됩니다. 이 동작은 오프셋 계산의 시작 지점이 125&mu;임을 의미합니다.

또한 카메라 클록과 디바이스 펌웨어 클록의 차이는 최소 오프셋을 160&mu;로 늘립니다. 구성에 대한 더 정확한 오프셋을 계산하려면 사용하는 깊이 모드를 확인하고 [깊이 센서 원시 타이밍 표](hardware-specification.md#depth-sensor-raw-timing)를 참조하세요. 이 표의 데이터를 사용하여 다음 수식을 통해 최소 오프셋(각 카메라의 노출 시간)을 계산할 수 있습니다.

> *노출 시간* = (*IR 펄스* &times; *펄스 너비*) + (*유휴 기간* &times; *유휴 시간*)

160&mu; 오프셋을 사용하는 경우 다른 레이저가 유휴 상태인 동안 각 레이저가 켜지도록 최대 9개의 추가 깊이 카메라를 구성할 수 있습니다.

소프트웨어에서 ```depth_delay_off_color_usec``` 또는 ```subordinate_delay_off_master_usec```를 사용하여 각 IR 레이저가 자체 160&mu; 시간 범위에서 실행되거나 다른 시야를 갖추도록 합니다.

> [!NOTE]  
> 실제 펄스 너비는 &mu; 125입니다. 그러나 약간의 여유를 제공하기 위해 160us를 명시합니다. NFOV UNBINNED을 예로 들어 각 125의 &mu; 펄스 뒤에 1450초 &mu; 유휴 상태가 붙습니다. 이(9 x 125) + (8 x 1450)를 합계하면 12.8ms의 노출 시간이 계산됩니다. 두 디바이스의 노출을 인터리브할 수 있는 밀실 장치는 두 번째 카메라의 첫 번째 펄스가 첫 번째 카메라의 첫 번째 유휴 기간에 포함되도록 하는 것입니다. 첫 번째 카메라와 두 번째 카메라 사이의 지연은 &mu; 125초(펄스의 너비)에 해당할 수 있으나 약간의 여유를 160초로 하는 것이 &mu; 좋습니다. 160이 지정된 &mu; 경우 최대 10대의 카메라 노출 기간을 인터리브할 수 있습니다.

## <a name="prepare-your-devices-and-other-hardware"></a>디바이스 및 기타 하드웨어 준비

여러 Azure Kinect DK 디바이스 외에도 빌드하려는 구성을 지원하려면 추가 호스트 컴퓨터 및 기타 하드웨어를 가져와야 할 수 있습니다. 이 섹션의 정보를 사용하여 설정을 시작하기 전에 모든 디바이스 및 하드웨어가 준비되었는지 확인합니다.

### <a name="azure-kinect-dk-devices"></a>Azure Kinect DK 디바이스

동기화하려는 각 Azure Kinect DK 디바이스에 대해 다음을 수행합니다.

- 최신 펌웨어가 디바이스에 설치되어 있는지 확인합니다. 디바이스를 업데이트하는 방법에 대한 자세한 내용은 [Azure Kinect DK 펌웨어 업데이트](update-device-firmware.md)를 참조하세요. 
- 디바이스 덮개를 제거하여 동기화 포트를 노출시킵니다.
- 각 디바이스의 일련 번호를 적어 둡니다. 이 번호는 나중에 설정 프로세스에서 사용합니다.

### <a name="host-computers"></a>호스트 컴퓨터

각 Azure Kinect DK는 일반적으로 자체 호스트 컴퓨터를 사용합니다. 디바이스를 사용하는 방법 및 USB 연결을 통해 전송하는 데이터 양에 따라 전용 호스트 컨트롤러를 사용할 수 있습니다. 

Azure Kinect 센서 SDK가 각 호스트 컴퓨터에 설치되어 있는지 확인합니다. 센서 SDK를 설치하는 방법에 대한 자세한 내용은 [빠른 시작: Azure Kinect DK 설정](set-up-azure-kinect-dk.md)을 참조하세요. 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Linux 컴퓨터: Ubuntu의 USB 메모리

Linux 기반 호스트 컴퓨터는 기본적으로 USB 전송을 처리하기 위해 16MB의 커널 메모리만 USB 컨트롤러에 할당합니다. 이 양은 일반적으로 단일 Azure Kinect DK를 지원하는 데 충분합니다. 그러나 여러 디바이스를 지원하려면 USB 컨트롤러에 더 많은 메모리가 있어야 합니다. 메모리를 늘리려면 다음 단계를 수행합니다.

1. /**etc/default/grub** 를 편집합니다.
1. 다음 줄을 찾습니다.
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   다음 줄을 사용하여 바꿉니다.
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > 이러한 명령은 USB 메모리를 32MB로 설정합니다. 이는 기본값의 두 배에 해당하는 설정 예입니다. 솔루션에 맞게 훨씬 더 큰 값을 설정할 수 있습니다.
1. **sudo update-grub** 를 실행합니다.
1. 컴퓨터를 다시 시작합니다.

### <a name="cables"></a>케이블

디바이스를 서로 연결하고 호스트 컴퓨터에 연결하려면 3.5mm 수-수 케이블(3.5mm 오디오 케이블이라고도 함)을 사용해야 합니다. 케이블 길이는 10미터 미만이어야 하며, 스테레오 또는 모노일 수 있습니다.

필요한 케이블 수는 사용하는 디바이스의 수와 특정 디바이스 구성에 따라 달라집니다. Azure Kinect DK 상자에는 케이블이 포함되지 않습니다. 별도로 구매해야 합니다.

디바이스를 별 모양 구성으로 연결하는 경우 하나의 헤드폰 분할기도 있어야 합니다.

## <a name="connect-your-devices"></a>사용자 디바이스 연결

**Azure Kinect DK 디바이스를 데이지 체인 구성으로 연결하려면**

1. 각 Azure Kinect DK를 전원 공급 장치에 연결합니다.
1. 각 디바이스를 호스트 PC에 연결합니다. 
1. 하나의 디바이스를 마스터 디바이스로 선택하고, 3.5mm 오디오 케이블을 **동기화 출력** 포트에 꽂습니다.
1. 케이블의 다른 쪽 끝을 첫 번째 하위 디바이스의 **동기화 입력** 포트에 꽂습니다.
1. 다른 디바이스를 연결하려면 다른 케이블을 첫 번째 하위 디바이스의 **동기화 출력** 포트와 다음 디바이스의 **동기화 입력** 포트에 꽂습니다.
1. 모든 디바이스가 연결될 때까지 이전 단계를 반복합니다. 마지막 디바이스에는 하나의 케이블만 연결해야 합니다. 해당 **동기화 출력** 포트는 비어 있습니다.

**Azure Kinect DK 디바이스를 별 모양 구성으로 연결하려면**

1. 각 Azure Kinect DK를 전원 공급 장치에 연결합니다.
1. 각 디바이스를 호스트 PC에 연결합니다. 
1. 하나의 디바이스를 마스터 디바이스로 선택하고, 헤드폰 분할기의 한쪽 끝을 **동기화 출력** 포트에 꽂습니다.
1. 3\.5mm 오디오 케이블을 헤드폰 분할기의 "갈라진" 끝에 꽂습니다.
1. 각 케이블의 다른 쪽 끝을 하위 디바이스 중 하나의 **동기화 입력** 포트에 꽂습니다.

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>디바이스가 연결되어 있고 통신하는지 확인

디바이스가 올바르게 연결되었는지 확인하려면 [Azure Kinect 뷰어](azure-kinect-viewer.md)를 사용합니다. 필요에 따라 다음 절차를 반복하여 마스터 디바이스와 함께 각 하위 디바이스를 테스트합니다.

> [!IMPORTANT]  
> 이 절차를 수행하려면 각 Azure Kinect DK의 일련 번호를 알고 있어야 합니다.

1. Azure Kinect 뷰어의 두 인스턴스를 엽니다.
1. **디바이스 열기** 에서 테스트하려는 하위 디바이스의 일련 번호를 선택합니다.  
   ![디바이스 열기](./media/open-devices.png)
   > [!IMPORTANT]  
   > 모든 디바이스 간에 정확한 이미지 캡처 맞춤을 얻으려면 마스터 디바이스를 마지막으로 시작해야 합니다.  
1. **외부 동기화** 아래에서 **하위** 를 선택합니다.  
   ![하위 카메라 시작](./media/sub-device-start.png)
1.  **시작** 을 선택합니다.  
    > [!NOTE]  
    > 이는 하위 디바이스이므로 디바이스가 시작되면 Azure Kinect 뷰어에서 이미지를 표시하지 않습니다. 하위 디바이스에서 마스터 디바이스로부터 동기화 신호를 받을 때까지 이미지가 표시되지 않습니다.
1. 하위 디바이스가 시작되면 Azure Kinect 뷰어의 다른 인스턴스를 사용하여 마스터 디바이스를 엽니다.
1. **외부 동기화** 아래에서 **마스터** 를 선택합니다.
1. **시작** 을 선택합니다.

마스터 Azure Kinect 디바이스가 시작되면 Azure Kinect 뷰어의 두 인스턴스 모두에서 이미지가 표시됩니다.

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>디바이스를 동기화된 세트로 보정

디바이스에서 올바르게 통신하고 있다고 확인되면 단일 도메인에서 이미지를 생성하도록 디바이스를 보정할 준비가 된 것입니다.

단일 디바이스에서 깊이 및 RGB 카메라는 함께 작동하도록 공장에서 보정됩니다. 그러나 여러 디바이스가 함께 작동해야 하는 경우 이미지를 캡처한 카메라의 영역에서 이미지를 처리하는 데 사용할 카메라의 영역으로 이미지를 변환하는 방법을 결정하기 위해 이러한 디바이스를 보정해야 합니다.

디바이스를 교차 보정하는 여러 가지 옵션이 있습니다. Microsoft는 OpenCV 방법을 사용하는 [GitHub 녹색 화면 코드 샘플](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen)을 제공합니다. 이 코드 샘플의 추가 정보 파일에서는 디바이스를 보정하는 방법에 대한 자세한 내용과 지침을 제공합니다.

보정에 대한 자세한 내용은 [Azure Kinect 보정 함수 사용](use-calibration-functions.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

동기화된 디바이스가 설정되면
> [!div class="nextstepaction"]
> [Azure Kinect 센서 SDK 기록 및 재생 API](record-playback-api.md)를 사용하는 방법도 알아볼 수 있습니다.

## <a name="related-topics"></a>관련 항목

- [Azure Kinect 센서 SDK 정보](about-sensor-sdk.md)
- [Azure Kinect DK 하드웨어 사양](hardware-specification.md) 
- [빠른 시작: Azure Kinect DK 설정](set-up-azure-kinect-dk.md) 
- [Azure Kinect DK 펌웨어 업데이트](update-device-firmware.md) 
- [Azure Kinect DK 다시 설정](reset-azure-kinect-dk.md) 
- [Azure Kinect 뷰어](azure-kinect-viewer.md) 
