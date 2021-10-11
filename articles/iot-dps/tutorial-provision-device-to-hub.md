---
title: 자습서 - Azure IoT Hub Device Provisioning Service를 사용하여 디바이스 프로비전
description: 이 자습서에서는 Azure IoT Hub DPS(Device Provisioning Service)를 사용하여 단일 IoT 허브에 디바이스를 프로비저닝하는 방법을 보여줍니다.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: eed3ff374850d4861c94fe79263c26a8f33760ac
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271691"
---
# <a name="tutorial-provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>자습서: Azure IoT Hub Device Provisioning Service를 사용하여 IoT Hub에 디바이스를 프로비전

이전 자습서에서 Device Provisioning Service에 연결하기 위해 디바이스를 설정하는 방법을 알아보았습니다. 이 자습서에서는 이 서비스를 사용하여 자동 프로비전 및 **_등록 목록_** 을 사용하는 단일 IoT Hub에 디바이스를 프로비전하는 방법을 배웁니다. 이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 디바이스 등록
> * 디바이스 시작
> * 디바이스가 등록되어 있는지 확인

## <a name="prerequisites"></a>사전 요구 사항

계속 진행하려면 [Azure IoT Hub Device Provisioning Service를 사용하여 프로비전하도록 디바이스를 설정](./tutorial-set-up-device.md) 자습서의 설명에 따라 디바이스를 구성해야 합니다.

자동 프로비저닝 프로세스에 익숙하지 않은 경우 계속하기 전에 [프로비저닝](about-iot-dps.md#provisioning-process) 개요를 검토하세요.

<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>디바이스 등록

이 단계에서는 Device Provisioning Service에 디바이스의 고유 보안 아티팩트를 추가하는 것이 포함됩니다. 이러한 보안 아티팩트는 다음과 같은 디바이스의 [증명 메커니즘](concepts-service.md#attestation-mechanism)을 기반으로 합니다.

- TPM 기반 디바이스의 경우 다음과 같은 항목이 필요합니다.
    - TPM 칩 제조업체에서 얻은 각 TPM 칩 또는 시뮬레이션에 고유한 *인증 키*.  자세한 정보는 [TPM 인증 키 이해](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770443(v=ws.11))를 읽어보세요.
    - 네임스페이스/범위에서 디바이스를 고유하게 식별하는 데 사용되는 *등록 ID* 입니다. 이 ID는 디바이스 ID와 같을 수도 있고 다를 수도 있습니다. ID는 모든 디바이스에 필수입니다. TPM 기반 디바이스의 경우 등록 ID는 TPM 인증 키의 SHA-256 해시와 같이 TPM 자체에서 파생될 수도 있습니다.

      [![포털에서 TPM에 대한 등록 정보](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png#lightbox)  

- X.509 기반 디바이스의 경우 다음과 같은 항목이 필요합니다.
    - [X.509에 발급된 인증서](/windows/win32/seccertenroll/about-x-509-public-key-certificates) 칩 또는 시뮬레이션입니다. 형식은 *.pem* 또는 *.cer* 파일 중 하나입니다. 개별 등록에는 X.509 시스템에 대한 디바이스별 *서명자 인증서* 를 사용하고, 등록 그룹에는 *루트 인증서* 를 사용해야 합니다. 

      [![포털에서 X.509 증명에 대한 개별 등록 추가](./media/tutorial-provision-device-to-hub/individual-enrollment.png)](./media/tutorial-provision-device-to-hub/individual-enrollment.png#lightbox)

Device Provisioning Service에 디바이스를 등록하는 방법은 두 가지가 있습니다.

- **등록 그룹** 특정 증명 메커니즘을 공유하는 디바이스의 그룹을 나타냅니다. 원하는 초기 구성을 공유하는 디바이스 수가 많은 경우 또는 디바이스가 모두 동일한 테넌트로 이동하는 경우 등록 그룹을 사용하는 것이 좋습니다. 등록 그룹의 Id 증명에 대한 자세한 내용은 [보안](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates)을 참조하세요.

    [![포털에 X.509 증명에 대한 그룹 등록 추가](./media/tutorial-provision-device-to-hub/group-enrollment.png)](./media/tutorial-provision-device-to-hub/group-enrollment.png#lightbox)

- **개별 등록** Device Provisioning Service에 등록할 수도 있는 단일 디바이스에 대한 항목을 나타냅니다. 개별 등록은 증명 메커니즘으로 x509 인증서 또는 SAS 토큰(실제 또는 가상 TPM) 중 하나를 사용할 수 있습니다. 고유한 초기 구성이 필요한 디바이스 및 증명 메커니즘으로 TPM 또는 가상 TPM을 통해 SAS 토큰만을 사용할 수 있는 디바이스의 경우 개별 등록을 사용하는 것이 좋습니다. 개별 등록은 지정된 원하는 IoT Hub 디바이스 ID가 있을 수 있습니다.

이제 디바이스의 증명 메커니즘에 따라 필요한 보안 아티팩트를 사용하여 디바이스를 Device Provisioning Service 인스턴스에 등록합니다. 

1. Azure Portal에 로그인하고, 왼쪽 메뉴에서 **모든 리소스** 단추를 클릭하고, Device Provisioning Service를 엽니다.

2. Device Provisioning Service 요약 블레이드에서 **등록 관리** 를 선택합니다. **개별 등록** 탭 또는 **등록 그룹** 탭 중 하나를 디바이스 설정에 따라 탭합니다. 위쪽에 있는 **추가** 단추를 클릭합니다. **TPM** 또는 **X.509** 를 ID 증명 *메커니즘* 으로 선택하고, 이전에 설명된 대로 적절한 보안 아티팩트를 입력합니다. 새 **IoT Hub 디바이스 ID** 를 입력할 수도 있습니다. 완료되면 **저장** 단추를 클릭합니다. 

3. 디바이스를 성공적으로 등록하면 포털에 다음과 같은 화면이 표시됩니다.

    ![포털에서 성공적인 TPM 등록](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)

등록 후 프로비전 서비스는 향후 특정한 시점에 이러한 디바이스가 부팅 및 연결될 때까지 대기합니다. 디바이스를 처음으로 부팅하는 경우 클라이언트 SDK 라이브러리는 칩과 상호 작용하여 디바이스에서 보안 아티팩트를 추출하고, Device Provisioning Service에 등록을 확인합니다. 

## <a name="start-the-iot-device"></a>IoT 디바이스 시작

IoT 디바이스는 실제 디바이스일 수도 있고 시뮬레이션된 디바이스일 수도 있습니다. IoT 디바이스가 디바이스 프로비전 서비스 인스턴스에 등록되었으니, 이제 디바이스를 부팅하고, 증명 메커니즘을 사용하여 프로비전 서비스를 호출하고 인식할 수 있습니다. 프로비전 서비스에서 디바이스를 인식하면 디바이스가 IoT Hub에 할당됩니다.

TPM 및 X.509 증명을 사용하여 시뮬레이션된 디바이스 예제가 C, Java, C#, Node.js 및 Python용으로 포함되어 있습니다.  TPM 증명을 사용하는 디바이스의 예를 보려면 [빠른 시작: 시뮬레이션된 TPM 디바이스 프로비전](quick-create-simulated-device-tpm.md)을 참조하세요. X.509 증명을 사용하는 디바이스의 예는 [빠른 시작: 시뮬레이션된 대칭 키 디바이스 프로비전](quick-create-simulated-device-x509.md#prepare-and-run-the-device-provisioning-code)을 참조하세요.

디바이스를 시작하고 디바이스의 클라이언트 애플리케이션이 Device Provisioning Service에 등록할 수 있도록 허용하세요.  

## <a name="verify-the-device-is-registered"></a>디바이스가 등록되어 있는지 확인

디바이스가 부팅되면 다음 작업을 수행해야 합니다.

1. 디바이스에서 등록 요청을 Device Provisioning Service에 보냅니다.
2. TPM 디바이스의 경우 Device Provisioning Service에서 디바이스가 응답하는 등록 챌린지를 다시 보냅니다. 
3. 등록에 성공하면 Device Provisioning Service는 IoT Hub URI, 디바이스 ID 및 암호화된 키를 디바이스로 다시 보냅니다. 
4. 그러면 디바이스의 IoT Hub 클라이언트 애플리케이션이 사용자 허브에 연결됩니다. 
5. 허브에 성공적으로 연결되면 IoT Hub의 **IoT 디바이스** 탐색기에 디바이스가 표시됩니다. 

    ![포털에서 허브에 연결 성공](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

자세한 내용은 디바이스 클라이언트 프로비전 샘플 [prov_dev_client_sample.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c)를 참조하세요. 이 샘플은 TPM, X.509 인증서 및 대칭 키를 사용하여 시뮬레이션된 디바이스를 프로비전하는 방법을 보여줍니다. 다시 [TPM](./quick-create-simulated-device-tpm.md), [X.509](./quick-create-simulated-device-x509.md) 및 [대칭 키](./quick-create-simulated-device-symm-key.md) 증명 빠른 시작을 참조하여 샘플 사용 방법에 대한 단계별 지침을 확인하세요.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 디바이스 등록
> * 디바이스 시작
> * 디바이스가 등록되어 있는지 확인

부하가 분산된 허브 간 여러 디바이스를 프로비저닝하는 방법을 알아보려면 다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [부하가 분산된 IoT Hub 간 디바이스 프로비전](./tutorial-provision-multiple-hubs.md)