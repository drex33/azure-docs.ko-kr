---
title: Azure Kinect DK 하드웨어 사양
description: Azure Kinect DK의 구성 요소, 사양 및 기능을 살펴봅니다.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/18/2021
ms.topic: article
keywords: Azure, Kinect, 사양, 하드웨어, DK, 기능, 깊이, 색, RGB, IMU, 마이크, 어레이, 깊이
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: afbc0ab2680e2cde4c3b36f0a02407f7d90bba8e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130246721"
---
# <a name="azure-kinect-dk-hardware-specifications"></a>Azure Kinect DK 하드웨어 사양

이 문서에서는 Azure Kinect 하드웨어에서 Microsoft의 최신 센서 기술을 USB로 연결된 단일 액세서리에 통합하는 방법에 대해 자세히 설명합니다.

![Azure Kinect DK](./media/resources/hardware-specs-media/device-wire.png)

## <a name="terms"></a>항

이 문서 전체에서 사용되는 약어는 다음과 같습니다.

- NFOV(좁은 보기 필드 깊이 모드)
- WFOV(넓은 보기 필드 깊이 모드)
- FOV(보기 필드)
- FPS(초당 프레임 수)
- IMU(관성 측정 장치)
- FoI(관심 있는 필드)

## <a name="product-dimensions-and-weight"></a>제품 크기 및 무게

Azure Kinect 디바이스에 구성되는 크기와 무게 치수는 다음과 같습니다.

- **차원**: 103 x 39 x 126mm
- **무게**: 440g

![Azure Kinect DK 치수](./media/resources/hardware-specs-media/dimensions.png)

Azure Kinect 디바이스에 대한 STEP 파일은 [여기](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/assets)에서 사용할 수 있습니다. 

## <a name="operating-environment"></a>운영 환경

Azure Kinect DK는 다음과 같은 주변 조건에서 운영하는 개발자 및 상용 비즈니스를 위한 것입니다.

- **온도**: 10-25 <sup>0</sup>C
- **습도**: 8-90%(비응축) 상대 습도

> [!NOTE]
> 주변 조건 이외의 환경에서 사용하면 디바이스가 고장나거나 제대로 작동하지 않을 수 있습니다. 이러한 주변 조건은 모든 작동 조건에서 디바이스 주변의 환경에 즉시 적용할 수 있습니다. 외함과 함께 사용하는 경우 디바이스를 이러한 범위 내에서 유지하기 위해 활성 온도 제어 및/또는 다른 냉각 솔루션을 사용 하는 것이 좋습니다. 디바이스 설계에는 냉각 채널이 전면부와 후면 슬리브 사이에 있습니다. 디바이스를 구현할 때 이 냉각 채널을 방해하지 않도록 하세요.

추가 제품 [안전 정보](https://support.microsoft.com/help/4023454/safety-information)를 참조하세요.

## <a name="depth-camera-supported-operating-modes"></a>깊이 카메라 지원 작동 모드

Azure Kinect DK는 [ISSCC 2018에 제시된 이미지 센서](/windows/mixed-reality/ISSCC-2018)를 사용하여 Microsoft에서 설계한 1메가픽셀 ToF(Time-of-Flight) 깊이 카메라를 통합합니다. 깊이 카메라에서 지원하는 모드는 아래에 나와 있습니다.

 | 모드            | 해결 방법 | FoI       | FPS                | 작동 범위* | 노출 시간 |
|-----------------|------------|-----------|--------------------|------------------|---------------|
| NFOV Unbinned   | 640 x 576    | 75° x 65°   | 0, 5, 15, 30       | 0.5-3.86m       | 12.8ms        |
| NFOV 2x2 Binned(SW) | 320 x 288    | 75° x 65°   | 0, 5, 15, 30       | 0.5 - 5.46m       | 12.8ms        |
| WFOV 2x2 Binned | 512 x 512    | 120° x 120° | 0, 5, 15, 30       | 0.25-2.88m      | 12.8ms        |
| WFOV Unbinned   | 1024 x 1024  | 120° x 120° | 0, 5, 15           | 0.25-2.21m      | 20.3ms        |
| Passive IR      | 1024 x 1024  | 해당 없음       | 0, 5, 15, 30       | 해당 없음              | 1.6ms         |

\*850nm에서 15-95% 반사율, 2.2 μW/cm<sup>2</sup>/nm, 임의 오류 표준 편차 ≤ 17mm, 일반 체계적 오류 < 11mm + 다중 경로 간섭을 사용하지 않는 거리의 0.1% 기준입니다. 위에 표시된 작동 범위 이외의 깊이가 제공될 수 있습니다. 개체의 반사율에 따라 달라집니다.

## <a name="color-camera-supported-operating-modes"></a>컬러 카메라 지원 작동 모드

Azure Kinect DK에는 OV12A10 12MP CMOS 센서 롤링 셔터 센서가 포함되어 있습니다. 기본 작동 모드는 다음과 같습니다.

|             RGB 카메라 해상도(HxV)  |          가로 세로 비율  |          형식 옵션   |          프레임 속도(FPS)  |          명목 FOV(HxV)(후처리)  |
|------------------------------------------|------------------------|---------------------------|-----------------------------|---------------------------------------------|
|       3840 x 2160                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° x 59°                              |
|       2560 x 1440                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° x 59°                              |
|       1920 x 1080                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° x 59°                              |
|       1280 x 720                           |          16:9          |          MJPEG/YUY2/NV12  |          0, 5, 15, 30       |          90° x 59°                              |
|       4096 x 3072                          |          4:3           |          MJPEG             |          0, 5, 15           |          90° x 74.3°                            |
|       2048x1536                          |          4:3           |          MJPEG             |          0, 5, 15, 30       |          90° x 74.3°                            |

RGB 카메라는 USB 비디오 클래스와 호환되며 센서 SDK 없이 사용할 수 있습니다. RGB 카메라 색 공간: BT.601 전체 범위 [0..255]. MJPEG [색도 하위 샘플링](https://en.wikipedia.org/wiki/Chroma_subsampling)은 4:2:2입니다.

> [!NOTE]
> 센서 SDK는 BGRA 픽셀 형식의 컬러 이미지를 제공할 수 있습니다. 이는 디바이스에서 지원하는 기본 모드가 아니므로 사용 시 추가 CPU 로드가 발생합니다. 호스트 CPU는 디바이스에서 받는 MJPEG 이미지를 변환하는 데 사용됩니다.

## <a name="rgb-camera-exposure-time-values"></a>RGB 카메라 노출 시간 값

허용되는 RGB 카메라 수동 노출 값에 대한 매핑은 다음과 같습니다.

| exp| 2^exp | 50Hz   |60Hz    |
|----|-------|--------|--------|
| -11|     488|    500|    500 |
| -10|     977|   1250|   1250 |
|  -9|    1953|   2500|   2500 |
|  -8|    3906|  10000|   8330 |
|  -7|    7813|  20000|  16670 |
|  -6|   15625|  30000|  33330 |
|  -5|   31250|  40,000|  41670 |
|  -4|   62500|  50000|  50000 |
|  -3|  125000|  60000|  66670 |
|  -2|  250000|  80,000|  83330 |
|  -1|  500000| 100000| 100000 |
|   0| 1000000| 120000| 116670 |
|   1| 2000000| 130000| 133330 |

## <a name="depth-sensor-raw-timing"></a>깊이 센서 원시 타이밍

깊이 모드 | IR <br>펄스 | 펄스 <br>너비  | 유휴 상태 <br>마침표| 유휴 시간 | 노출 <br> 시간
-|-|-|-|-|-
NFOV Unbinned <br>  NFOV 2xx Binned <br> WFOV 2x2 Binned | 9 | 125us | 8 | 1450us | 12.8ms 
WFOV Unbinned                                            | 9 | 125us | 8 | 2390us | 20.3ms


## <a name="camera-field-of-view"></a>카메라 보기 필드

다음 이미지에서는 깊이 및 RGB 카메라 보기 필드 또는 센서의 "가시" 각도를 보여 줍니다. 이 다이어그램의 경우 4:3 모드의 RGB 카메라를 보여 줍니다.

![카메라 FOV](./media/resources/hardware-specs-media/camera-fov.png)

다음 이미지에서는 정면 방향 2,000mm 거리를 주시하는 카메라의 보기 필드를 보여 줍니다.

![카메라 FOV 전면](./media/resources/hardware-specs-media/fov-front.png)

> [!NOTE]
> 깊이가 NFOV 모드인 경우 RGB 카메라의 픽셀 겹침은 16:9 해상도보다 4:3 해상도에서 더 효율적입니다.

## <a name="motion-sensor-imu"></a>동작 센서(IMU)

내장형 IMU(관성 측정 장치)는 LSM6DSMUS이며, 가속도계와 자이로스코프를 모두 포함하고 있습니다. 가속도계와 자이로스코프는 1.6kHz로 동시에 샘플링됩니다. 샘플은 208Hz로 호스트에 보고됩니다.

## <a name="microphone-array"></a>마이크 배열

Azure Kinect DK에는 표준 USB 오디오 클래스 2.0 디바이스로 식별되는 7개의 고품질 마이크 순환 어레이가 포함되어 있습니다. 7개 채널 모두에 액세스할 수 있습니다. 성능 사양은 다음과 같습니다.

- 민감도: -22dBFS(94dB SPL, 1kHz)
- 신호 대 잡음 비율 > 65dB
- 음향 오버로드 지점: 116dB

![마이크 버블](./media/resources/hardware-specs-media/mic-bubble.png)

## <a name="usb"></a>USB

Azure Kinect DK는 다음 하드웨어 엔드포인트를 운영 체제에 공개하는 USB3 복합 디바이스입니다.

공급업체 ID는 0x045E(Microsoft)입니다. 아래는 제품 ID 표입니다.

|    USB 인터페이스        |    PNP IP    |     메모            |
|-------------------------|--------------|----------------------|
|    USB3.1 2세대 허브    |    0x097A    |    주 허브    |
|    USB 2.0 허브         |    0x097B    |    HS USB          |
|    깊이 카메라       |    0x097C    |    USB3.0            |
|    컬러 카메라       |    0x097D    |    USB3.0            |
|    마이크        |    0x097E    |    HS USB          |

## <a name="indicators"></a>표시기

디바이스 전면에는 센서 SDK를 사용하여 프로그래밍 방식으로 비활성화할 수 있는 카메라 스트리밍 표시기가 있습니다.

디바이스 후면에 있는 상태 LED에서 나타내는 디바이스 상태는 다음과 같습니다.

| 표시등 상태     | 의미                                                   |
|-----------------------|------------------------------------------------------------|
| 흰색 고정           | 디바이스가 켜져 있고 제대로 작동합니다.                         |
| 흰색 깜박임        | 디바이스가 켜져 있지만 USB 3.0 데이터 연결이 없습니다.   |
| 황색 깜박임        | 디바이스가 작동하는 데 필요한 전원이 부족합니다.               |
| 황색, 다음으로 흰색 깜박임  | 펌웨어 업데이트 또는 복구가 진행 중입니다.                    |

## <a name="power-device"></a>전원 장치

디바이스에 전원을 공급하는 두 가지 방법은 다음과 같습니다.

1. 기본 제공 전원 공급 장치를 사용합니다. 전원 커넥터는 3.0mm ID와 0.6mm의 핀 직경을 가진 4.5mm OD입니다.
2. 전원 및 데이터는 모두 C형-C형 케이블을 사용합니다.

C형-C형 케이블은 Azure Kinect DK에 포함되어 있지 않습니다.

> [!NOTE]
> - 기본 제공 전원 공급 케이블은 A형-단일 포스트 배럴 커넥터 USB입니다. 이 케이블과 함께 제공된 벽 거치형 전원 공급 장치를 사용합니다. 디바이스는 두 개의 표준 A형 USB 포트에서 제공할 수 있는 것보다 더 많은 전원을 끌 수 있습니다.
> - USB 케이블이 중요하므로 디바이스를 원격으로 배포하기 전에 고품질 케이블을 사용하고 기능을 확인하는 것이 좋습니다.

> [!TIP]
> 효율적인 C형-C형 케이블을 선택하는 경우 다음 사항을 고려하세요.
> - [USB 인증 케이블](https://www.usb.org/products)에서 전원과 데이터를 모두 지원해야 합니다.
> - 패시브 케이블의 길이는 1.5m 미만이어야 합니다. 더 긴 경우 액티브 케이블을 사용합니다. 
> - 케이블은 최소 1.5A를 지원해야 합니다. 그렇지 않으면 외부 전원 공급 장치를 연결해야 합니다.

다음과 같이 케이블을 확인합니다.

- 케이블을 통해 디바이스를 호스트 PC에 연결합니다.
- Windows 디바이스 관리자에서 모든 디바이스가 올바르게 열거되는지 확인합니다. 깊이 및 RGB 카메라는 아래 예와 같이 표시되어야 합니다.

  ![디바이스 관리자의 Azure Kinect DK](./media/resources/hardware-specs-media/device-manager.png)

- 다음 설정을 사용하여 Azure Kinect 뷰어의 모든 센서에 대한 케이블에서 안정적으로 스트림할 수 있는지 확인합니다.

  - 깊이 카메라: NFOV Unbinned
  - RGB 카메라: 2160p
  - 마이크 및 IMU 사용

## <a name="what-does-the-light-mean"></a>표시등이란?

전원 표시등은 Azure Kinect DK의 후면에 있는 LED입니다. 디바이스 상태에 따라 LED 색이 변경됩니다.

![Azure Kinect DK의 후면을 보여 주는 이미지입니다. 번호가 지정된 세 개의 설명선이 있습니다. 하나는 LED 표시기이고, 그 아래의 두 설명선은 케이블에 대한 것입니다.](./media/quickstarts/azure-kinect-dk-power-indicator.png)

이 그림에서 레이블이 지정된 구성 요소는 다음과 같습니다.

1. 전원 표시등
1. 전원 케이블(전원 공급 장치에 연결됨)
1. C형 USB 데이터 케이블(PC에 연결됨)

케이블이 그림과 같이 연결되어 있는지 확인합니다. 그런 다음, 다음 표를 확인하여 다양한 전원 표시등 상태를 확인합니다.

|표시등 상태 |의미 |수행해야 하는 작업 |
| ---| --- | --- |
|흰색 고정 |디바이스가 켜져 있고, 제대로 작동하고 있습니다. |디바이스를 사용합니다. |
|꺼져 있음 |디바이스가 PC에 연결되어 있지 않습니다. |원형 전원 커넥터 케이블이 디바이스 및 USB 전원 어댑터에 연결되어 있는지 확인합니다.<br /><br />C형 USB 케이블이 디바이스 및 PC에 연결되어 있는지 확인합니다. |
|흰색 깜박임 |디바이스가 켜져 있지만, USB 3.0 데이터 연결이 없습니다. |원형 전원 커넥터 케이블이 디바이스 및 USB 전원 어댑터에 연결되어 있는지 확인합니다.<br /><br />USB-C 케이블이 디바이스 및 PC의 USB 3.0 포트에 연결되어 있는지 확인합니다.<br /><br />디바이스를 PC의 다른 USB 3.0 포트에 연결합니다.<br /><br />PC에서 디바이스 관리자(**시작** > **제어판** > **디바이스 관리자**)를 열고, 지원되는 USB 3.0 호스트 컨트롤러가 PC에 있는지 확인합니다. |
|황색 깜박임 |디바이스가 작동하는 데 필요한 전원이 부족합니다. |원형 전원 커넥터 케이블이 디바이스 및 USB 전원 어댑터에 연결되어 있는지 확인합니다.<br /><br />C형 USB 케이블이 디바이스 및 PC에 연결되어 있는지 확인합니다. |
|황색, 다음으로 흰색 깜박임 |디바이스가 켜져 있고, 펌웨어 업데이트를 받고 있거나 디바이스에서 공장 설정을 복원하고 있습니다. |전원 표시등이 흰색으로 고정될 때까지 기다립니다. 자세한 내용은 [Azure Kinect DK 다시 설정](reset-azure-kinect-dk.md)을 참조하세요. |

## <a name="power-consumption"></a>전력 소비

Azure Kinect DK는 최대 5.9W의 전력을 소비합니다. 특정 전력 소비량은 사용 사례에 따라 달라집니다.

## <a name="calibration"></a>보정

Azure Kinect DK는 공장에서 보정됩니다. 시각 센서 및 관성 센서에 대한 보정 매개 변수는 센서 SDK를 통해 프로그래밍 방식으로 쿼리할 수 있습니다.

## <a name="device-recovery"></a>디바이스 복구

잠금 핀 아래에 있는 단추를 사용하여 디바이스 펌웨어를 원래 펌웨어로 다시 설정할 수 있습니다.

![Azure Kinect DK 복구 단추](./media/resources/hardware-specs-media/recovery.png)

디바이스를 복구하려면 [여기의 지침](reset-azure-kinect-dk.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Kinect 센서 SDK 사용](about-sensor-sdk.md)
- [하드웨어 설정](set-up-azure-kinect-dk.md)