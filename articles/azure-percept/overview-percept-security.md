---
title: Azure Percept 보안
description: Azure Percept 보안에 대해 자세히 알아봅니다.
author: mimcco
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: cfee4f14b5e7f135c7bbe609b5f3453b5fce8589
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221948"
---
# <a name="azure-percept-security"></a>Azure Percept 보안

Azure Percept 디바이스는 하드웨어 신뢰할 수 있는 루트를 사용하여 설계되었습니다. 이러한 기본 제공 보안을 통해 카메라 및 마이크와 같은 유추 데이터 및 개인 정보 보호 센서를 보호하고, Azure Percept Studio 서비스에 대한 디바이스 인증 및 권한 부여를 사용할 수 있습니다.

> [!NOTE]
> Azure Percept DK는 개발 및 테스트 환경에서만 사용이 허가됩니다.

## <a name="devices"></a>디바이스

### <a name="azure-percept-dk"></a>Azure Percept DK

Azure Percept DK에는 디바이스를 Azure DPS(Device Provisioning Service)에 추가 보안으로 연결하는 데 활용할 수 있는 TPM(신뢰할 수 있는 플랫폼 모듈) 버전 2.0이 포함되어 있습니다. TPM은 TCG(신뢰할 수 있는 컴퓨팅 그룹)의 업계 전체 ISO 표준입니다. 전체 TPM 2.0 사양 또는 ISO/IEC 11889 사양에 대한 자세한 내용은 [TCG(신뢰할 수 있는 컴퓨팅 그룹) 웹 사이트](https://trustedcomputinggroup.org/resource/tpm-library-specification/)를 확인하세요. DPS에서 안전하게 디바이스를 프로비저닝하는 방법에 대한 자세한 내용은 [Azure IoT Hub Device Provisioning Service -TPM 증명](../iot-dps/concepts-tpm-attestation.md)을 참조하세요.

### <a name="azure-percept-system-on-modules-soms"></a>Azure SoM(system-on-module)

Azure Percept Vision SoM(system-on-module)과 Azure Percept Audio SoM은 모두 포함된 AI 센서에 대한 액세스를 보호하기 위한 MCU(마이크로 컨트롤러 장치)를 포함합니다. 부팅할 때마다 MCU 펌웨어는 DICE(Device Identifier Composition Engine) 아키텍처를 사용하여 Azure Percept Studio 서비스에서 AI 가속기를 인증하고 권한을 부여합니다. DICE는 부팅을 계층으로 나누고 각 계층 및 구성에 대해 UDS(Unique Device Secret)를 만드는 방식으로 작동합니다. 체인의 어느 지점에서든 다른 코드 또는 구성이 부팅되면 비밀이 달라집니다. [DICE 작업 그룹 사양](https://trustedcomputinggroup.org/work-groups/dice-architectures/)에서 DICE에 대해 자세히 알아볼 수 있습니다. Azure Percept Studio 및 필수 서비스에 대한 액세스를 구성하려면 [Azure Percept DK 방화벽 구성](concept-security-configuration.md) 문서를 참조하세요.

Azure Percept 디바이스는 하드웨어 신뢰할 수 있는 루트를 사용하여 펌웨어를 보호합니다. 부팅 ROM은 ROM 및 OS(운영 체제) 로더 간에 펌웨어 무결성을 보장합니다. 그러면 이 로더는 다른 소프트웨어 구성 요소의 무결성을 보장하여 신뢰 체인을 만듭니다.

## <a name="services"></a>Services

### <a name="iot-edge"></a>IoT Edge

Azure Percept DK는 TLS(전송 계층 보안) 프로토콜을 활용하는 추가 보안 및 기타 Azure 서비스를 사용하여 Azure Percept Studio에 연결합니다. Azure Percept DK는 Azure IoT Edge 지원 디바이스입니다. IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꾸는 프로그램 컬렉션입니다. 전체적으로 IoT Edge 런타임 구성 요소는 IoT Edge 디바이스를 사용하여 에지에서 실행될 코드를 받고 결과를 전달합니다. Azure Percept DK는 호스트 운영 체제 및 에지 지원 애플리케이션에서 IoT Edge 워크로드를 격리하기 위해 Docker 컨테이너를 활용합니다. Azure IoT Edge 보안 프레임워크에 대한 자세한 내용은 [IoT Edge 보안 관리자](../iot-edge/iot-edge-security-manager.md)를 참조하세요.

### <a name="device-update-for-iot-hub"></a>IoT Hub용 디바이스 업데이트 문서

IoT Hub용 디바이스 업데이트를 통해 Azure Percept 디바이스에 갱신 가능한 보안을 제공하는 보다 안전하고 확장 가능하며 안정적인 무선 업데이트를 사용할 수 있습니다. 인사이트를 통해 풍부한 관리 제어 및 업데이트 규정 준수를 제공합니다. Azure Percept DK에는 펌웨어에서 OS 계층으로의 복원력 업데이트(A/B)를 제공하는 사전 통합된 디바이스 업데이트 솔루션이 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [방화벽 구성 및 보안 권장 사항에 대한 자세한 정보](concept-security-configuration.md)

> [!div class="nextstepaction"]
> [Microsoft 온라인 스토어에서 Azure Percept DK 구매](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
