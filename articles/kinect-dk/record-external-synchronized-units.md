---
title: 동기화된 외부 디바이스가 있는 Azure Kinect 레코더 사용
description: Azure Kinect 레코더를 사용하여 외부 동기화를 위해 구성된 디바이스에서 데이터를 기록하는 방법에 대해 알아봅니다.
author: tesych
ms.author: tesych
ms.reviewer: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, 센서, 뷰어, 외부 동기화, 단계 지연, 깊이, RGB, 카메라, 오디오 케이블, 레코더
ms.openlocfilehash: fd751c9979cf2dc1fa2698deff0e2b207fe518c5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124771387"
---
# <a name="use-azure-kinect-recorder-with-external-synchronized-devices"></a>동기화된 외부 디바이스가 있는 Azure Kinect 레코더 사용

이 문서에서는 [Azure Kinect 레코더가](azure-kinect-recorder.md) 외부 동기화로 구성 된 장치에서 데이터를 기록 하는 방법에 대 한 지침을 제공 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- [외부 동기화를 위해 여러 개의 Azure Kinect DK 장치를 설정합니다](https://support.microsoft.com/help/4494429).

## <a name="external-synchronization-constraints"></a>외부 동기화 제약 조건

- 마스터 디바이스는 SYNC IN 케이블을 연결할 수 없습니다.
- 동기화를 사용하도록 설정하려면 마스터 디바이스가 RGB 카메라를 스트림해야 합니다.
- 모든 장치가 동일한 카메라 구성(프레임 속도 및 해상도)을 사용해야 합니다.
- 모든 장치가 동일한 디바이스 펌웨어([펌웨어 업데이트](update-device-firmware.md) 지침)를 실행해야 합니다.
- 종속 디바이스는 모두 마스터 디바이스보다 먼저 시작되어야 합니다.
- 모든 디바이스에 동일한 노출 값을 설정해야 합니다.
- 각 종속 디바이스의 *마스터 지연 끄기* 설정은 마스터 디바이스를 기준으로 합니다.

## <a name="record-when-each-unit-has-a-host-pc"></a>각 장치에 호스트 PC가 있을 때 기록

아래 예에서는 각 디바이스에 전용 호스트 PC가 자체적으로 있습니다.
USB 대역폭과 CPU/GPU 사용량 문제를 방지하려면 디바이스를 전용 PC에 연결하는 것이 좋습니다.

### <a name="subordinate-1"></a>종속-1

1. 첫 번째 장치에 대한 레코더 설정

      `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub1.mkv`

2. 디바이스가 대기 시작

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="subordinate-2"></a>종속-2

1. 두 번째 장치에 대한 레코더 설정

    `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub2.mkv`

2. 디바이스가 대기 시작

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="master"></a>마스터

1. 마스터에서 기록 시작

    `>k4arecorder.exe --external-sync master -e -8 -r 5 -l 10 master.mkv`

2. 기록이 완료될 때까지 대기

## <a name="recording-when-multiple-units-connected-to-single-host-pc"></a>여러 개의 장치가 단일 호스트 PC에 연결되어 있을 때 기록

단일 호스트 PC에 여러 개의 Azure Kinect DK를 연결할 수 있습니다. 그러나 USB 대역폭과 호스트 컴퓨팅에는 매우 부담이 클 수 있습니다. 부담을 줄이려면:

- 각 디바이스를 자체 USB 호스트 컨트롤러에 연결합니다.
- 각 디바이스에 대한 깊이 엔진을 처리할 수 있는 강력한 GPU를 보유합니다.
- 필요한 센서만 기록하고 더 낮은 프레임 속도를 사용합니다.

항상 종속 디바이스를 먼저 시작하고 마스터를 마지막으로 시작합니다.

## <a name="subordinate-1"></a>종속-1

1. 종속 디바이스에서 레코더 시작

    `>k4arecorder.exe --device 1 --external-sync subordinate --imu OFF -e -8 -r 5 -l 5 output-2.mkv`

2. 디바이스가 대기 상태로 전환

## <a name="master"></a>마스터

1. 마스터 디바이스 시작

    `>k4arecorder.exe --device 0 --external-sync master --imu OFF -e -8 -r 5 -l 5 output-1.mkv`

2. 기록이 완료될 때까지 대기

## <a name="playing-recording"></a>기록 재생

[Azure Kinect 뷰어](azure-kinect-viewer.md)를 사용하여 기록을 재생할 수 있습니다.



## <a name="tips"></a>팁

- 동기화된 카메라를 기록하는 데 수동 노출을 사용합니다. RGB 카메라 자동 노출은 시간 동기화에 영향을 줄 수 있습니다.
- 종속 디바이스를 다시 시작하면 동기화가 손실됩니다.
- 일부 [카메라 모드](hardware-specification.md#depth-camera-supported-operating-modes)는 최대 15fps를 지원합니다. 디바이스 간에 모드/프레임 속도를 혼합하지 않는 것이 좋습니다.
- 단일 PC에 여러 개의 장치를 연결하면 USB 대역폭이 쉽게 포화될 수 있으므로 디바이스별로 개별 호스트 PC를 사용하는 것이 좋습니다. CPU/GPU 컴퓨팅에도 주의를 기울입니다.
- 안정성을 개선하는 데 필요하지 않은 경우 마이크와 IMU를 사용하지 않도록 설정합니다.

문제에 대해서는 [문제 해결](troubleshooting.md)을 참조하세요.

## <a name="see-also"></a>참고 항목

- [외부 동기화 설정](https://support.microsoft.com/help/4494429/sync-multiple-devices)
- 레코더 설정 및 추가 정보의 경우 [Azure Kinect 레코더](azure-kinect-recorder.md)
- 레코더를 통해서 사용 가능하지 않은 기록 재생 또는 RGB 카메라 속성 설정의 경우 [Azure Kinect 뷰어](azure-kinect-viewer.md)
- 디바이스 펌웨어 업데이트의 경우 [Azure Kinect 펌웨어 도구](azure-kinect-firmware-tool.md)
