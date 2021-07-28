---
title: Windows에서 Linux용 Azure IoT Edge란? | Microsoft Docs
description: Windows 10 디바이스에서 Linux IoT Edge 모듈을 실행할 수 있는 개요
author: kgremban
manager: philmea
ms.reviewer: twarwick
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 3c7fd6c842d465dd5af5257628044666f10f2ece
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538199"
---
# <a name="what-is-azure-iot-edge-for-linux-on-windows-preview"></a>Windows에서 Linux용 Azure IoT Edge란?(미리 보기)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Windows에서 Linux용 Azure IoT Edge를 사용하면 Windows IoT 배포에서 Windows 애플리케이션과 함께 컨테이너화된 Linux 워크 로드를 실행할 수 있습니다. Windows IoT를 사용하여 에지 디바이스를 구동하는 기업은 이제 Linux에 빌드된 클라우드 네이티브 분석 솔루션을 활용할 수 있습니다.

Windows에서 Linux용 IoT Edge은 Windows 디바이스에서 Linux 가상 머신을 실행하여 작동합니다. Linux 가상 머신은 IoT Edge 런타임과 함께 미리 설치되어 제공됩니다. 디바이스에 배포된 모든 IoT Edge 모듈은 가상 머신 내에서 실행됩니다. 한편 Windows 호스트 디바이스에서 실행되는 Windows 애플리케이션은 Linux 가상 머신에서 실행되는 모듈과 통신할 수 있습니다.

지금 미리 보기를 [시작](how-to-install-iot-edge-on-windows.md)하세요.

>[!NOTE]
>IoT Edge 환경 및 목표에 따라 Windows에서 Linux용 Azure IoT Edge 개선할 수 있도록 [제품 설문 조사](https://aka.ms/AzEFLOW-Registration)에 참여하세요. 또한 설문 조사에 참여하면 향후 Windows에서 Linux용 Azure IoT Edge 공지를 받아볼 수 있습니다.

## <a name="components"></a>구성 요소

Windows에서 Linux용 IoT Edge는 다음과 같은 구성 요소를 사용하여 Linux와 Windows 워크로드가 동시에 실행되고 원활하게 통신할 수 있도록 합니다.

* **Azure IoT Edge를 실행하는 Linux 가상 머신**: Microsoft의 퍼스트 파티 [Mariner](https://github.com/microsoft/CBL-Mariner) 운영 체제를 기반으로 하는 Linux 가상 머신은 IoT Edge 런타임으로 빌드되었으며 IoT Edge 워크로드에 대한 계층 1 지원 환경으로 확인됩니다.

* **Windows Admin Center**: Windows Admin Center용 IoT Edge 확장은 Linux 가상 머신에서 IoT Edge의 설치, 구성 및 진단을 용이하게 합니다. Windows Admin Center는 Windows에서 Linux용 IoT Edge를 로컬 디바이스에 배포하거나 대상 디바이스에 연결하여 원격으로 관리할 수 있습니다.

* **Microsoft 업데이트**: Microsoft 업데이트와 통합하여 Windows 런타임 구성 요소, Mariner Linux VM 및 IoT Edge 최신 상태로 유지합니다.

![Windows 및 Linux VM은 병렬로 실행되고 Windows Admin Center는 두 구성 요소를 모두 제어합니다.](./media/iot-edge-for-linux-on-windows/architecture-and-communication.png)

Windows 프로세스와 Linux 가상 머신 간의 양방향 통신은 Windows 프로세스가 Linux 컨테이너에서 실행되는 워크로드에 대한 사용자 인터페이스 또는 하드웨어 프록시를 제공할 수 있음을 의미합니다.

## <a name="samples"></a>샘플

Windows에서 Linux용 IoT Edge는 Linux와 Windows 구성 요소가 상호 운용될 수 있음을 강조합니다.

Windows 애플리케이션 및 IoT Edge 모듈 간의 통신을 보여주는 샘플은 [EFLOW & Windows 10 IoT 샘플](https://aka.ms/AzEFLOW-Samples)을 참조하세요.

## <a name="public-preview"></a>퍼블릭 미리 보기

Windows에서 Linux용 IoT Edge는 현재 [퍼블릭 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에서 확인할 수 있습니다. 설치 및 관리 프로세스는 일반적으로 사용할 수 있는 기능과 다를 수 있습니다.

## <a name="support"></a>지원

IoT Edge 지원 및 피드백 채널을 사용하여 Windows에서 Linux용 IoT Edge에 대한 지원을 받을 수 있습니다.

**버그 보고** - IoT Edge 오픈 소스 프로젝트의 [문제 페이지](https://github.com/azure/iotedge/issues)에서 버그를 보고할 수 있습니다. Windows에서 Linux용 Azure IoT Edge 관련 버그는 [iotedge-eflow 문제 페이지](https://aka.ms/AzEFLOW-Issues)에서 보고할 수 있습니다.

**Microsoft 고객 지원팀** – [지원 계획](https://azure.microsoft.com/support/plans/)이 있는 사용자는 [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)에서 직접 지원 티켓을 만들어서 Microsoft 고객 지원팀과 협력할 수 있습니다.

**기능 요청** – Azure IoT Edge 제품은 제품의 [사용자 의견 페이지](https://feedback.azure.com/forums/907045-azure-iot-edge)를 통해 기능 요청을 추적합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용 및 작동 샘플은 [Windows 10 IoT Enterprise에서 Linux용 IoT Edge](https://aka.ms/EFLOWPPC9)를 확인하세요.

[Windows 디바이스에서 Azure IoT Edge 설치 및 프로비저닝](how-to-install-iot-edge-on-windows.md) 단계에 따라 Windows에서 Linux용 IoT Edge를 사용하여 디바이스를 설정합니다.
