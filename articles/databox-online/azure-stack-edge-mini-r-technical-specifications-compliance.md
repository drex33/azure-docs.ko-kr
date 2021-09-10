---
title: Microsoft Azure Stack Edge Mini R 기술 사양 및 규정 준수 | Microsoft Docs
description: Azure Stack Edge Mini R 디바이스에 대한 기술 사양 및 규정 준수에 대해 알아보기
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 06/29/2021
ms.author: alkohli
ms.openlocfilehash: 8b2da6b75ec495fe96b0c65e312a91d0dd30a2d2
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113091504"
---
# <a name="azure-stack-edge-mini-r-technical-specifications"></a>Azure Stack Edge Mini R 기술 사양

Microsoft Azure Stack Edge Mini R 디바이스의 하드웨어 구성 요소는 이 문서에 설명된 기술 사양 및 규제 표준을 준수합니다. 기술 사양은 CPU, 메모리, 전원 공급 장치(PSU), 스토리지 용량, 인클로저 크기, 무게를 설명합니다.


## <a name="compute-memory"></a>컴퓨팅, 메모리

Azure Stack Edge Mini R 디바이스의 컴퓨팅 및 메모리 사양은 다음과 같습니다.

| 사양           | 값                           |
|-------------------------|---------------------------------|
| CPU 종류                | Intel Xeon-D 1577               |
| CPU: 원시                | 총 16개 코어, 총 32개 vCPU  |
| CPU: 사용 가능             | 24개 vCPU                        |
| 메모리 유형             | 16GB 2400 MT/s SODIMM          |
| 메모리: 원시             | 48GB RAM(3 x 16GB)           |
| 메모리: 사용 가능          | 32GB RAM                       |


## <a name="compute-acceleration"></a>컴퓨팅 가속

Kubernetes, 심층 신경망, 컴퓨터 비전 기반 애플리케이션을 지원하는 모든 Azure Stack Edge Mini R 디바이스에 Vision Processing Unit(VPU)이 포함됩니다.

| 사양             | 값                  |
|---------------------------|------------------------|
| 컴퓨팅 가속 카드 | Intel Movidius Myriad X VPU <br> 자세한 내용은 [Intel Movidius Myriad X VPU](https://www.movidius.com/MyriadX)를 참조하세요. |


## <a name="storage"></a>스토리지

Azure Stack Edge Mini R 디바이스에는 데이터 디스크 1개와 부팅 디스크 1개가 있습니다(운영 체제 스토리지로 사용됨). 다음 표에는 디바이스의 스토리지 용량에 대한 세부 정보가 나와 있습니다.

|     사양                          |     값                                              |
|--------------------------------------------|--------------------------------------------------------|
|    SSD(반도체 드라이브)의 수     |    1TB 디스크 2개 <br> 데이터 디스크 1개, 부팅 디스크 1개 |
|    단일 SSD 용량                     |    1TB                                                |
|    총 용량(데이터만)              |    1TB                                                |
|    총 사용 가능한 용량 *                  |    ~ 750GB                                            |

*일부 공간은 내부용으로 예약되어 있습니다.*

## <a name="network"></a>네트워크

Azure Stack Edge Mini R 디바이스의 네트워크 사양은 다음과 같습니다.

|사양         |값                                                               |
|----------------------|--------------------------------------------------------------------|
|네트워크 인터페이스    |2 x 10 Gbps SFP+ <br> 로컬 UI에 포트 3 및 포트 4로 표시됩니다.    |
|네트워크 인터페이스    |2 x 1 Gbps RJ45 <br> 로컬 UI에 포트 1 및 포트 2로 표시됩니다.     |
|Wi-Fi                 |802.11ac                                                            |

## <a name="routers-and-switches"></a>라우터 및 스위치

다음 라우터와 스위치는 Azure Stack Edge Mini R 디바이스의 10Gbps SPF + 네트워크 인터페이스(포트 3 및 포트 4)와 호환됩니다.

|라우터/스위치     |메모                         |
|------------------|------------------------------|
|[VoyagerESR 2.0](https://klastelecom.com/products/voyageresr2-0/)    |Cisco ESS3300 스위치 구성 요소   |
|[VoyagerSW26G](https://klastelecom.com/products/voyagersw26g/)       |                                 |
|[VoyagerVM 3.0](https://klastelecom.com/products/voyager-vm-3-0/)    |                                 |
|[TDC 스위치](https://klastelecom.com/voyager-tdc/)                   |                                 |
|[TRX R2](https://klastelecom.com/products/trx-r2/)(8 코어)  <!--Better link: https://www.klasgroup.com/products/voyagersw12gg/? On current link target, an "R6" link opens this page.-->        |                              |
|[SW12GG](https://www.klasgroup.com/products/voyagersw12gg/)          |                                 |

## <a name="transceivers-cables"></a>송수신, 케이블

다음 구리 SFP +(10Gbps) 트랜시버 및 케이블은 Azure Stack Edge Mini R 디바이스와 함께 사용하는 것이 좋습니다. 호환되는 파이버 광 케이블은 SFP + 네트워크 인터페이스(포트 3 및 포트 4)와 함께 사용할 수 있지만 테스트되지 않았습니다.

|SFP+ 송수신 형식 |지원되는 케이블    | 메모 |
|----------------------|--------------------|-------|
|SFP+ Direct-Attach Copper(10GSFP+Cu)| <ul><li>[FS SFP-10G-DAC](https://www.fs.com/c/fs-10g-sfp-dac-1115)(산업용 온도 -40ºC~+85ºC에서 사용자 지정 주문 가능)</li><br><li>[10Gtek CAB-10GSFP-P0.5M](http://www.10gtek.com/10G-SFP+-182)</li><br><li>[Cisco SFP-H10GB-CU1M](https://www.cisco.com/c/en/us/products/collateral/interfaces-modules/transceiver-modules/data_sheet_c78-455693.html)</li></ul> |<ul><li>SFP+ Twinax DAC 케이블이라고도 합니다.</li><br><li>전원 사용량이 가장 낮고 가장 간단하기 때문에 권장되는 옵션입니다.</li><br><li>자동 협상은 지원되지 않습니다.</li><br><li>SFP+ 디바이스에 SFP 디바이스를 연결하는 것은 지원되지 않습니다.</li></ul>|

## <a name="power-supply-unit"></a>전원 공급 장치

Azure Stack Edge Mini R 디바이스에는 전원을 공급하고 온보드 배터리를 충전하는 외장 85W AC 어댑터가 포함됩니다.

전원 공급 장치 사양은 다음 표와 같습니다.

| 사양           | 값                      |
|-------------------------|----------------------------|
| 최대 출력 전원    | 85W                       |
| 빈도               | 50/60Hz                   |
| 전압 범위 선택 | 자동 범위 지정: 100-240V AC |

## <a name="included-battery"></a>배터리 포함

Azure Stack Edge Mini R 디바이스에는 전원 공급 장치가 충전하는 온보드 배터리도 포함됩니다.

온보드 배터리에 따라 추가적으로 [Type 2590 배터리](https://www.bren-tronics.com/bt-70791ck.html)를 사용하면 충전 빈도를 줄일 수 있습니다. 이 배터리는 사용 국가에서 적용되는 모든 안전, 교통, 환경 규정을 준수해야 합니다.

| 사양            | 값      |
|--------------------------|------------|
| 온보드 배터리 용량 | 73Wh      |

## <a name="enclosure-dimensions-and-weight"></a>인클로저 치수 및 무게

다음 표에서 크기 및 무게에 대해 다양한 엔클로저 사양을 보여줍니다.

### <a name="enclosure-dimensions"></a>엔클로저 크기

디바이스와 러기드 케이스가 장착된 USP의 크기를 밀리미터와 인치로 나타내면 다음과 같습니다.

|     인클로저     |     밀리미터     |     인치     |
|-------------------|---------------------|----------------|
|    높이         |    68               |    2.68        |
|    너비          |    208              |    8.19        |
|    길이         |    259              |    10.20       |


### <a name="enclosure-weight"></a>엔클로저 무게

배터리를 포함한 디바이스 무게는 다음 표와 같습니다.

|     인클로저                     |     무게          |
|-----------------------------------|---------------------|
|    디바이스의 총 무게     |     7lbs           |

## <a name="enclosure-environment"></a>엔클로저 환경

이 섹션에서는 온도, 습도 및 고도와 같은 엔클로저 환경과 관련된 사양을 나열합니다.

|     사양             |     Description                                                          |
|--------------------------------|--------------------------------------------------------------------------|
|     온도 범위          |     0 ~ 40°C(작동)                                              |
|     Vibration                  |     MIL-STD-810 방법 514.7*<br> 프로시저 I CAT 4, 20                  |
|     충격                      |     MIL-STD-810 방법 516.7*<br> 프로시저 IV, 로지스틱                 |
|     고도                   |     작동: 15,000피트<br> 비작동: 4만 피트          |

**모든 참조는 MIL-STD-810G Change 1(2014)을 따릅니다.*

## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge Mini R 배포](azure-stack-edge-placeholder.md)
