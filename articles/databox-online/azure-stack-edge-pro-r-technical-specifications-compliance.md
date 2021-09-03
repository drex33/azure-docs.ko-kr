---
title: Microsoft Azure Stack Edge Pro R 기술 사양 및 규정 준수 | Microsoft Docs
description: Azure Stack Edge Pro R 디바이스에 대한 기술 사양 및 규정 준수에 대해 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: alkohli
ms.openlocfilehash: cb0a7ede9ee4b4a5f6fbb2312026211784fa486c
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2021
ms.locfileid: "113301567"
---
# <a name="azure-stack-edge-pro-r-technical-specifications"></a>Azure Stack Edge Pro R 기술 사양

Azure Stack Edge Pro R 디바이스의 하드웨어 구성 요소는 이 문서에 설명된 기술 사양을 준수합니다. 기술 사양은 PSU(전원 공급 장치), 스토리지 용량, 엔클로저 및 환경 표준을 설명합니다.


## <a name="compute-memory-specifications"></a>컴퓨팅, 메모리 사양

Azure Stack Edge Pro R 디바이스에는 계산 및 메모리에 대한 다음과 같은 사양이 있습니다.

| 사양  | 값                                             |
|----------------|---------------------------------------------------|
| CPU 종류       | 이중 Intel Xeon Silver 4114 CPU                   |
| CPU: 원시       | 총 20개 코어, 총 40개 vCPU                    |
| CPU: 사용 가능    | 32개 vCPU                                          |
| 메모리 유형    | Dell 호환 16GB RDIMM, 2666MT/s, 이중 랭크 |
| 메모리: 원시    | 256GB RAM(16 x 16GB)                           |
| 메모리: 사용 가능 | 217GB RAM                                        |

## <a name="compute-acceleration-specifications"></a>컴퓨팅 가속 사양

GPU(그래픽 처리 장치)는 Kubernetes, 딥 러닝 및 기계 학습 시나리오를 지원하는 모든 디바이스에 포함되어 있습니다.

| 사양           | 값                      |
|-------------------------|----------------------------|
| GPU   | nVidia T4 GPU 1개 <br> 자세한 내용은 [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/)를 참조하세요. | 

## <a name="power-supply-unit-specifications"></a>전원 공급 장치 사양

Azure Stack Edge Pro R 디바이스에는 고성능 팬이 탑재된 2대의 100-240V PSU(전원 공급 디바이스)가 있습니다. 두 대의 PSU는 중복 전원 구성을 제공합니다. PSU가 실패하면, 디바이스는 실패한 모듈이 교체될 때까지 다른 PSU에서 정상적으로 작동합니다. 다음 표에는 PSU의 기술 사양이 나열되어 있습니다.

| 사양              | 550W PSU                  |
|----------------------------|----------------------------|
| 최대 출력 전원       | 550W                      |
| 열 분산(최대) | 2891BTU/hr                |
| 빈도                  | 50/60Hz                   |
| 전압 범위 선택    | 자동 범위 지정: 115-230V AC |
| 핫 플러그형              | 예                        |

## <a name="network-specifications"></a>네트워크 사양

Azure Stack Edge Pro R 디바이스에는 4개의 네트워크 인터페이스인 PORT1-PORT4가 있습니다.


|사양         |Description                       |
|----------------------|----------------------------------|
|네트워크 인터페이스    |**1GbE RJ45 2개** <br> 포트 1은 초기 설치를 위한 관리 인터페이스로 사용되며 기본적으로 정적입니다. 초기 설치가 완료된 후에는 모든 IP 주소를 사용하여 데이터에 대한 인터페이스를 사용할 수 있습니다. 그러나 다시 설정 시 인터페이스는 정적 IP로 돌아갑니다. <br>다른 인터페이스 포트 2는 사용자가 구성 가능하고 데이터 전송에 사용할 수 있으며, 기본적으로 DHCP입니다. |
|네트워크 인터페이스    |**25GbE SFP28 2개** <br> 이러한 데이터 인터페이스 포트 3 및 포트 4를 DHCP(기본값) 또는 정적으로 구성할 수 있습니다. |

Azure Stack Edge Pro R 디바이스에는 다음과 같은 네트워크 하드웨어가 있습니다.

* **Mellanox 이중 포트 25G ConnectX-4 채널 네트워크 어댑터** - 포트 3 및 포트 4 

<!--Here are the details for the Mellanox card: MCX4421A-ACAN

| Parameter           | Description                 |
|-------------------------|----------------------------|
| Model    | ConnectX®-4 Lx EN network interface card                      |
| Model Description               | 25 GbE dual-port SFP28; PCIe3.0 x8; ROHS R6                    |
| Device Part Number (XR2) | MCX4421A-ACAN  |
| PSID (R640)           | MT_2420110034                         |-->
<!-- confirm w/ Ravi what is this-->

이러한 네트워크 카드에 대해 지원되는 케이블, 스위치 및 트랜시버의 전체 목록은 [Mellanox 이중 포트 25G ConnectX-4 채널 네트워크 어댑터 호환 제품](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)으로 이동하세요.

## <a name="storage-specifications"></a>스토리지 사양

Azure Stack Edge Pro R 디바이스에는 데이터 디스크 8개, 운영 체제 디스크 역할을 하는 2개의 M.2 SATA 디스크가 있습니다. 자세한 내용은 [M.2 SATA 디스크](https://en.wikipedia.org/wiki/M.2)로 이동합니다.

#### <a name="storage-for-1-node-device"></a>1개 노드 디바이스용 스토리지

다음 표에는 1개 노드 디바이스의 스토리지 용량에 대한 세부 정보가 나와 있습니다.

|     사양                          |     값             |
|--------------------------------------------|-----------------------|
|    SSD(반도체 드라이브)의 수     |    8                  |
|    단일 SSD 용량                     |    8 TB               |
|    총 용량                          |    64TB              |
|    총 사용 가능한 용량 *                  |    ~42TB            |

**일부 공간은 내부용으로 예약되어 있습니다.*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## <a name="enclosure-dimensions-and-weight-specifications"></a>엔클로저 차원과 가중치 사양

다음 표에서 크기 및 무게에 대해 다양한 엔클로저 사양을 보여줍니다.

### <a name="enclosure-dimensions"></a>엔클로저 크기 

디바이스와 러기드 케이스가 장착된 UPS의 크기를 밀리미터와 인치로 나타내면 다음과 같습니다.

|     인클로저     |     밀리미터     |     인치     |
|-------------------|---------------------|----------------|
|    높이         |    301.2            |    11.86       |
|    너비          |    604.5            |    23.80       |
|    길이         |    740.4            |    35.50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### <a name="enclosure-weight"></a>엔클로저 무게 

디바이스의 무게는 엔클로저 구성에 따라 달라집니다.

|     인클로저                                 |     무게          |
|-----------------------------------------------|---------------------|
|    1개 노드 디바이스 + 끝 단면이 있는 견고한 케이스의 총 무게     |    ~114lbs          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

## <a name="enclosure-environment-specifications"></a>엔클로저 환경 사양

이 섹션에서는 온도, 진동, 충격 및 고도와 같은 엔클로저 환경과 관련된 사양을 나열합니다.


|     사양              |     값    |
|--------------------------------|-------------------------------------------------------------------|
|     온도 범위          |     0 ~ 43°C(작동)    |
|     Vibration                  |     MIL-STD-810 방법 514.7*<br>프로시저 I CAT 4, 20                  |
|     충격                      |     MIL-STD-810 방법 516.7*<br>프로시저 IV, 로지스틱                 |
|     고도                   |     작동: 1만 피트<br>비작동: 4만 피트          |

**모든 참조는 MIL-STD-810G Change 1(2014)을 따릅니다.*

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge 배포](azure-stack-edge-placeholder.md)
