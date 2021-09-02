---
title: Azure Percept Vision 데이터시트
description: 자세한 디바이스 사양은 Azure Percept Vision 데이터 시트를 확인하세요.
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: cc15e8986058e01116341e970bfcf90799a5d416
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222399"
---
# <a name="azure-percept-vision-datasheet"></a>Azure Percept Vision 데이터시트

아래 나열된 사양은 [Azure Percept DK](./azure-percept-dk-datasheet.md)에 포함된 Azure Percept Vision 디바이스에 대한 것입니다.

|제품 사양           |값     |
|--------------------------------|---------------------|
|대상 산업               |제조업 <br> 스마트 빌딩 <br> 자동 <br> Retail |
|주인공 시나리오                  |구매자 분석 <br> 선반 가용성 <br> 수축 감소 <br> 작업 영역 모니터링|
|차원                      |42mm x 42mm x 40mm(하우징이 있는 Azure Percept Vision SoM 어셈블리) <br> 42mm x 42mm x 6mm(Vision SoM 칩)|
|제어 평면 관리        |ADU(Azure Device Update)          |
|지원되는 소프트웨어 및 서비스 |[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX Runtime](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Azure Device Update |
|AI 가속                 |Intel 카메라 ISP가 통합된 Intel Movidius Myriad X(MA2085) VPU(Vision Processing Unit), 0.7 TOPS |
|센서 및 시각적 표시기   |6P 렌즈를 사용하는 Sony IMX219 카메라 센서<br>해결 방법: 30FPS에서 8MP, 거리: 50cm - 무한대<br>FoV: 120도 대각선, 색: 와이드 동적 범위, 고정된 포커스 롤링 셔터|
|카메라 지원                  |RGB |
|보안 암호화 컨트롤러      |ST-Micro STM32L462CE      |
|버전 관리/ID 구성 요소       |64kb EEPROM |
|메모리                          |LPDDR4 2GB     |
|고급                           |3.5W     |
|포트                           |1x USB 3.0 형식 C <br> 2x MIPI 4레인(레인당 최대 1.5Gbps)     |
|제어 인터페이스              |2x I2C <br> 2x SPI <br> 6x PWM(GPIOs: 2x 클록, 2x 프레임 동기화, 2배 사용 안 함) <br> 2x 예비 GPIO |
|인증                   |CE <br> ACMA <br> FCC <br> IC <br> VCCI <br> NRTL <br> CB  |
|작동 온도           |0도~27도 C(하우징이 있는 Azure Percept Vision SoM 어셈블리) <br> -10도 ~70도 C(Vision SoM 칩) |
|터치 온도               |<= 48도 C |
|상대 습도               |8%~90%    |
