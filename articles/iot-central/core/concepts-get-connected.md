---
title: Azure IoT Central에서 디바이스 연결 | Microsoft Docs
description: 이 문서에서는 Azure IoT Central의 디바이스 연결과 관련된 주요 개념을 소개합니다.
author: dominicbetts
ms.author: dobett
ms.date: 09/07/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- amqp
- mqtt
- device-developer
ms.openlocfilehash: 68791ff58ec33ceb720b2edb068a8b0549cc1191
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132809935"
---
# <a name="get-connected-to-azure-iot-central"></a>Azure IoT Central에 연결

이 문서에서는 디바이스에서 Azure IoT Central 애플리케이션에 연결하는 방법에 대해 설명합니다. 디바이스에서 데이터를 IoT Central과 교환하려면 먼저 다음을 수행해야 합니다.

- *인증* 합니다. IoT Central 애플리케이션을 통한 인증은 _SAS(공유 액세스 서명) 토큰_ 또는 _X.509 인증서_ 를 사용합니다. X.509 인증서는 프로덕션 환경에서 사용하는 것이 좋습니다.
- *등록* 합니다. 디바이스는 IoT Central 애플리케이션에 등록해야 합니다. 애플리케이션의 **디바이스** 페이지에는 등록된 디바이스가 표시됩니다.
- *디바이스 템플릿과 연결* 합니다. IoT Central 애플리케이션에서 디바이스 템플릿은 운영자가 연결된 디바이스를 보고 관리하는 데 사용하는 UI를 정의합니다.

IoT Central에서 지원하는 다음 두 가지 디바이스 등록 시나리오는 다음과 같습니다.

- *자동 등록*. 디바이스는 처음 연결할 때 자동으로 등록됩니다. 이 시나리오를 통해 OEM은 먼저 등록하지 않고도 연결할 수 있는 제조업체 디바이스를 대량으로 만들 수 있습니다. OEM은 적절한 디바이스 자격 증명을 생성하고, 공장에서 디바이스를 구성합니다. 필요에 따라 데이터 보내기를 시작하기 전에 운영자에게 디바이스를 승인하도록 요구할 수 있습니다. 이 시나리오에서는 애플리케이션에서 X.509 또는 SAS _그룹 등록_ 을 구성해야 합니다.
- *수동 등록*. 운영자는 **디바이스** 페이지에서 개별 디바이스를 등록하거나 [CSV 파일을 가져와서](howto-manage-devices-in-bulk.md#import-devices) 디바이스를 대량 등록합니다. 이 시나리오에서는 X.509 또는 SAS _그룹 등록_ 이나 X.509 또는 SAS _개별 등록_ 을 사용할 수 있습니다.

IoT Central에 연결하는 디바이스는 *IoT 플러그 앤 플레이 규칙* 을 따라야 합니다. 이러한 규칙 중 하나는 디바이스에서 연결할 때 구현하는 디바이스 모델의 _모델 ID_ 를 보내야 한다는 것입니다. 모델 ID를 사용하면 IoT Central 애플리케이션에서 디바이스를 올바른 디바이스 템플릿과 연결할 수 있습니다.

IoT Central은 [Azure IoT Hub DPS(Device Provisioning Service)](../../iot-dps/about-iot-dps.md)를 사용하여 연결 프로세스를 관리합니다. 디바이스는 먼저 DPS 엔드포인트에 연결하여 애플리케이션에 연결하는 데 필요한 정보를 검색합니다. 내부적으로 IoT Central 애플리케이션에서 IoT 허브를 사용하여 디바이스 연결을 처리합니다. DPS를 사용하면 다음과 같은 장점이 있습니다.

- IoT Central이 디바이스를 대규모로 온보딩하고 연결하는 것을 지원합니다.
- IoT Central UI를 통해 디바이스를 등록할 필요 없이 오프라인으로 디바이스 자격 증명을 생성하고 디바이스를 구성할 수 있습니다.
- 사용자의 고유한 디바이스 ID를 사용하여 IoT Central에서 디바이스를 등록할 수 있습니다. 사용자의 고유한 디바이스 ID를 사용하면 기존 백 오피스 시스템과 간편하게 통합할 수 있습니다.
- 디바이스를 IoT Central에 연결하는 한 가지 일관적인 방법이 있습니다.

이 문서에서는 다음 디바이스 연결 단계에 대해 설명합니다.

- [X.509 그룹 등록](#x509-group-enrollment)
- [SAS 그룹 등록](#sas-group-enrollment)
- [개별 등록](#individual-enrollment)
- [디바이스 등록](#device-registration)
- [디바이스를 디바이스 템플릿과 연결](#associate-a-device-with-a-device-template)

## <a name="x509-group-enrollment"></a>X.509 그룹 등록

프로덕션 환경에서 X.509 인증서를 사용하는 것이 IoT Central에 권장되는 디바이스 인증 메커니즘입니다. 자세한 내용은 [X.509 CA 인증서를 사용하여 디바이스 인증](../../iot-hub/iot-hub-x509ca-overview.md)을 참조하세요.

X.509 인증서가 있는 디바이스를 애플리케이션에 연결하려면 다음을 수행합니다.

1. **인증서(X.509)** 증명 유형을 사용하는 *등록 그룹* 을 만듭니다.
1. 중간 또는 루트 X.509 인증서를 등록 그룹에 추가하고 확인합니다.
1. 등록 그룹의 루트 또는 중간 인증서에서 리프 인증서를 생성합니다. 애플리케이션에 연결할 때 디바이스에서 리프 인증서를 보냅니다.

자세한 내용은 [X.509 인증서를 사용하여 디바이스를 연결하는 방법](how-to-connect-devices-x509.md)을 참조하세요.

### <a name="for-testing-purposes-only"></a>테스트 전용

다음 유틸리티를 테스트용으로만 사용하여 루트, 중간 및 디바이스 인증서를 생성할 수 있습니다.

- [Azure IoT 디바이스 프로비전 디바이스 SDK용 도구](https://github.com/Azure/azure-iot-sdk-node/blob/main/provisioning/tools/readme.md): X.509 인증서 및 키를 생성하고 확인하는 데 사용할 수 있는 Node.js 도구 모음입니다.
- [샘플 및 자습서에 대한 테스트 CA 인증서 관리](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md): 다음을 수행하는 PowerShell 및 Bash 스크립트의 모음입니다.
  - 인증서 체인을 만듭니다.
  - 인증서를 .cer 파일로 저장하여 IoT Central 애플리케이션에 업로드합니다.
  - IoT Central 애플리케이션의 확인 코드를 사용하여 확인 인증서를 생성합니다.
  - 디바이스 ID를 도구에 대한 매개 변수로 사용하여 디바이스에 대한 리프 인증서를 만듭니다.

## <a name="sas-group-enrollment"></a>SAS 그룹 등록

디바이스 SAS 키가 있는 디바이스를 애플리케이션에 연결하려면 다음을 수행합니다.

1. **SAS(공유 액세스 서명)** 증명 유형을 사용하는 *등록 그룹* 을 만듭니다. 
1. 등록 그룹에서 그룹 기본 또는 보조 키를 복사합니다.
1. Azure CLI를 사용하여 그룹 키에서 디바이스 키를 생성합니다.

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. 디바이스에서 IoT Central 애플리케이션에 연결할 때 생성된 디바이스 키를 사용합니다.

> [!NOTE]
> 등록 그룹에서 기존 SAS 키를 사용 하려면 SAS 키의 **키 자동 생성** 설정/해제 및 입력을 사용 하지 않도록 설정 합니다.

## <a name="individual-enrollment"></a>개별 등록

각각 고유한 인증 자격 증명이 있는 디바이스를 연결하는 고객은 개별 등록을 사용합니다. 개별 등록은 연결할 수 있도록 허용된 단일 디바이스에 대한 항목입니다. 개별 등록은 신뢰할 수 있는 물리적 또는 가상 플랫폼 모듈의 X.509 리프 인증서 또는 SAS 토큰을 증명 메커니즘으로 사용할 수 있습니다. 디바이스 ID는 문자, 숫자 및 `-` 문자를 포함할 수 있습니다. 자세한 내용은 [DPS 개별 등록](../../iot-dps/concepts-service.md#individual-enrollment)을 참조하세요.

> [!NOTE]
> 디바이스에 대한 개별 등록을 만들면 해당 디바이스가 IoT Central 애플리케이션의 기본 그룹 등록 옵션보다 우선 적용됩니다.

### <a name="create-individual-enrollments"></a>개별 등록 만들기

IoT Central에서 개별 등록에 지원하는 증명 메커니즘은 다음과 같습니다.

- **대칭 키 증명:** 대칭 키 증명은 디바이스를 DPS 인스턴스로 인증하는 간단한 방법입니다. 대칭 키를 사용하는 개별 등록을 만들려면 디바이스에 대한 **디바이스 연결** 페이지를 열고, 연결 방법으로 **개별 등록** 을 선택하고, 메커니즘으로 **SAS(공유 액세스 서명)** 를 선택합니다. base64로 인코딩된 기본 및 보조 키를 입력하고, 변경 내용을 저장합니다. **ID 범위**, **디바이스 ID** 및 기본 또는 보조 키를 사용하여 디바이스를 연결합니다.

    > [!TIP]
    > 테스트를 위해 **OpenSSL** 을 사용하여 base64로 인코딩된 키를 생성할 수 있습니다(`openssl rand -base64 64`).

- **X.509 인증서:** X.509 인증서를 사용하여 개별 등록을 만들려면 **디바이스 연결** 페이지를 열고, 연결 방법으로 **개별 등록** 을 선택하고, 메커니즘으로 **인증서(X.509)** 를 선택합니다. 개별 등록 항목에 사용되는 디바이스 인증서에는 발급자 및 주체 CN이 디바이스 ID로 설정되어야 합니다.

    > [!TIP]
    > 테스트를 위해 [Node.js에 대한 Azure IoT 디바이스 프로비전 디바이스 SDK용 도구](https://github.com/Azure/azure-iot-sdk-node/tree/main/provisioning/tools)를 사용하여 자체 서명된 인증서를 생성할 수 있습니다(`node create_test_cert.js device "mytestdevice"`).

- **TPM(신뢰할 수 있는 플랫폼 모듈) 증명:** [TPM](../../iot-dps/concepts-tpm-attestation.md)은 하드웨어 보안 모듈의 한 유형입니다. TPM을 사용하는 것은 디바이스를 연결하는 가장 안전한 방법 중 하나입니다. 이 문서에서는 사용자가 개별, 펌웨어 또는 통합 TPM을 사용한다고 가정합니다. 소프트웨어 에뮬레이트된 TPM은 프로토타입 또는 테스트에 적합하지만 개별, 펌웨어 또는 통합 TPM과 동일한 수준의 보안을 제공하지 않습니다. 소프트웨어 TPM은 프로덕션에서 사용하지 않습니다. TPM을 사용하는 개별 등록을 만들려면 **디바이스 연결** 페이지를 열고, 연결 방법으로 **개별 등록** 을 선택하고, 메커니즘으로 **TPM** 을 선택합니다. TPM 인증 키를 입력하고, 디바이스 연결 정보를 저장합니다.

## <a name="device-registration"></a>디바이스 등록

디바이스에서 IoT Central 애플리케이션에 연결하려면 먼저 해당 디바이스를 애플리케이션에 등록해야 합니다.

- 디바이스는 처음 연결할 때 자동으로 등록할 수 있습니다. 이 옵션을 사용하려면 [X.509 그룹 등록](#x509-group-enrollment) 또는 [SAS 그룹 등록](#sas-group-enrollment)을 사용해야 합니다.
- 운영자는 CSV 파일을 가져와서 애플리케이션에서 디바이스 목록을 대량으로 등록할 수 있습니다.
- 운영자는 애플리케이션의 **디바이스** 페이지에서 개별 디바이스를 수동으로 등록할 수 있습니다.

IoT Central을 통해 OEM은 자동으로 등록할 수 있는 제조업체 디바이스를 대량으로 만들 수 있습니다. OEM은 적절한 디바이스 자격 증명을 생성하고, 공장에서 디바이스를 구성합니다. 고객이 처음으로 디바이스를 켜면 DPS에 연결됩니다. 그러면 DPS에서 디바이스를 올바른 IoT Central 애플리케이션에 자동으로 연결합니다. 필요에 따라 데이터를 애플리케이션에 보내기 전에 운영자에게 디바이스를 승인하도록 요구할 수 있습니다.

> [!TIP]
> **관리 > 디바이스 연결** 페이지에서 **자동 승인** 옵션은 데이터 보내기를 시작하기 전에 운영자가 디바이스를 수동으로 승인해야 하는지 여부를 제어합니다.

### <a name="automatically-register-devices-that-use-x509-certificates"></a>X.509 인증서를 사용하는 디바이스를 자동으로 등록

1. [X.509 등록 그룹](#x509-group-enrollment)에 추가한 루트 또는 중간 인증서를 사용하여 디바이스에 대한 리프 인증서를 생성합니다. 디바이스 ID를 리프 인증서의 `CNAME`으로 사용합니다. 디바이스 ID는 문자, 숫자 및 `-` 문자를 포함할 수 있습니다.

1. OEM은 디바이스 ID, 생성된 X.509 리프 인증서 및 애플리케이션 **ID 범위** 값을 사용하여 각 디바이스를 플래시합니다. 디바이스 코드는 구현하는 디바이스 모델의 모델 ID도 보내야 합니다.

1. 디바이스를 켜면 먼저 DPS에 연결하여 IoT Central 연결 정보를 검색합니다.

1. 디바이스에서 DPS의 정보를 사용하여 IoT Central 애플리케이션에 연결하고 등록합니다.

IoT Central 애플리케이션은 디바이스에서 보낸 모델 ID를 사용하여 [등록된 디바이스를 디바이스 템플릿과 연결](#associate-a-device-with-a-device-template)합니다.

### <a name="automatically-register-devices-that-use-sas-tokens"></a>SAS 토큰을 사용하는 디바이스를 자동으로 등록

1. **SAS-IoT-Devices** 등록 그룹에서 그룹 기본 키를 복사합니다.

    :::image type="content" source="media/concepts-get-connected/group-primary-key.png" alt-text="SAS-IoT-Devices 등록 그룹의 그룹 기본 키":::

1. `az iot central device compute-device-key` 명령을 사용하여 디바이스 SAS 키를 생성합니다. 이전 단계의 그룹 기본 키를 사용합니다. 디바이스 ID에는 문자, 숫자 및 `-` 문자가 포함될 수 있습니다.

    ```azurecli
    az iot central device compute-device-key --primary-key <enrollment group primary key> --device-id <device ID>
    ```

1. OEM은 디바이스 ID, 생성된 디바이스 SAS 키 및 애플리케이션 **ID 범위** 값을 사용하여 각 디바이스를 플래시합니다. 디바이스 코드는 구현하는 디바이스 모델의 모델 ID도 보내야 합니다.

1. 디바이스를 켜면 먼저 DPS에 연결하여 IoT Central 등록 정보를 검색합니다.

1. 디바이스에서 DPS의 정보를 사용하여 IoT Central 애플리케이션에 연결하고 등록합니다.

IoT Central 애플리케이션은 디바이스에서 보낸 모델 ID를 사용하여 [등록된 디바이스를 디바이스 템플릿과 연결](#associate-a-device-with-a-device-template)합니다.

### <a name="bulk-register-devices-in-advance"></a>디바이스를 미리 대량으로 등록

많은 수의 디바이스를 IoT Central 애플리케이션에 등록하려면 CSV 파일을 사용하여 [디바이스 ID 및 디바이스 이름을 가져옵니다](howto-manage-devices-in-bulk.md#import-devices).

디바이스에서 SAS 토큰을 사용하여 인증하는 경우 [IoT Central 애플리케이션에서 CSV 파일을 내보냅니다](howto-manage-devices-in-bulk.md#export-devices). 내보낸 CSV 파일에는 디바이스 ID 및 SAS 키가 포함되어 있습니다.

디바이스에서 X.509 인증서를 사용하여 인증하는 경우 X.509 등록 그룹에 업로드한 루트 또는 중간 인증서를 사용하여 디바이스에 대한 X.509 리프 인증서를 생성합니다. 가져온 디바이스 ID를 리프 인증서의 `CNAME` 값으로 사용합니다.

디바이스에서 애플리케이션에 대한 **ID 범위** 값을 사용하고, 연결할 때 모델 ID를 보내야 합니다.

> [!TIP]
> **ID 범위** 값은 **관리 > 디바이스 연결** 에서 확인할 수 있습니다.

### <a name="register-a-single-device-in-advance"></a>단일 디바이스를 미리 등록

이 방법은 IoT Central을 실험하거나 디바이스를 테스트하는 경우에 유용합니다. **디바이스** 페이지에서 **+ 새로 만들기** 를 선택하여 개별 디바이스를 등록합니다. 디바이스 연결 SAS 키를 사용하여 디바이스를 IoT Central 애플리케이션에 연결할 수 있습니다. 등록된 디바이스에 대한 연결 정보에서 _디바이스 SAS 키_ 를 복사합니다.

![개별 디바이스에 대한 SAS 키](./media/concepts-get-connected/single-device-sas.png)

## <a name="associate-a-device-with-a-device-template"></a>디바이스를 디바이스 템플릿과 연결

디바이스가 연결되면 IoT Central에서 디바이스를 디바이스 템플릿과 자동으로 연결합니다. 디바이스는 연결할 때 [모델 ID](../../iot-fundamentals/iot-glossary.md?toc=/azure/iot-central/toc.json&bc=/azure/iot-central/breadcrumb/toc.json#model-id)를 보냅니다. IoT Central은 모델 ID를 사용하여 해당 특정 디바이스 모델에 대한 디바이스 템플릿을 식별합니다. 검색 프로세스는 다음과 같이 작동합니다.

1. 디바이스 템플릿이 IoT Central 애플리케이션에 이미 게시된 경우 디바이스가 디바이스 템플릿과 연결됩니다.
1. 디바이스 템플릿이 IoT Central 애플리케이션에 아직 게시되지 않은 경우 IoT Central에서 [퍼블릭 모델 리포지토리](https://github.com/Azure/iot-plugandplay-models)에 있는 디바이스 모델을 찾습니다. IoT Central에서 모델을 찾으면 이를 사용하여 기본 디바이스 템플릿을 생성합니다.
1. IoT Central에서 퍼블릭 모델 리포지토리에 있는 모델을 찾을 수 없는 경우 디바이스가 **연결 안 됨** 으로 표시됩니다. 운영자는 디바이스에 대한 디바이스 템플릿을 만든 다음, 연결을 끊은 디바이스를 새 디바이스 템플릿으로 마이그레이션하거나 디바이스에서 보내는 데이터를 기준으로 [디바이스 템플릿을 자동으로 생성](howto-set-up-template.md#autogenerate-a-device-template)할 수 있습니다.

다음 스크린샷에서는 IoT Central에서 디바이스 템플릿의 모델 ID를 표시하는 방법을 보여 줍니다. 디바이스 템플릿에서 구성 요소를 선택한 다음, **ID 편집** 을 선택합니다.

:::image type="content" source="media/concepts-get-connected/model-id.png" alt-text="자동 온도 조절기 디바이스 템플릿의 모델 ID를 보여 주는 스크린샷":::

퍼블릭 모델 리포지토리에서 [자동 온도 조절기 모델](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/thermostat-1.json)을 볼 수 있습니다. 모델 ID 정의는 다음과 같습니다.

```json
"@id": "dtmi:com:example:Thermostat;1"
```

다음 DPS 페이로드를 사용 하 여 장치를 장치 템플릿에 연결 합니다.

```json
{
  "modelId":"dtmi:com:example:TemperatureController;2"
}
```

DPS 페이로드에 대 한 자세한 내용은 [자습서: 클라이언트 응용 프로그램을 만들어 Azure IoT 중앙 응용 프로그램에 연결](tutorial-connect-device.md)에 사용 된 샘플 코드를 참조 하세요.

## <a name="device-status-values"></a>디바이스 상태 값

실제 디바이스가 IoT Central 애플리케이션에 연결되면 디바이스 상태가 다음과 같이 변경됩니다.

1. 디바이스 상태가 처음에는 **등록됨** 입니다. 이 상태는 IoT Central에서 디바이스가 만들어지고 디바이스 ID가 있음을 의미합니다. 다음과 같은 경우 디바이스가 등록됩니다.
    - **디바이스** 페이지에 새 실제 디바이스가 추가됩니다.
    - **디바이스** 페이지에서 **가져오기** 를 사용하여 디바이스 세트가 추가됩니다.

1. 유효한 자격 증명을 사용하여 IoT Central 애플리케이션에 연결된 디바이스에서 프로비전 단계를 완료하면 디바이스 상태가 **프로비전됨** 으로 변경됩니다. 이 단계에서 디바이스는 DPS를 사용하여 IoT Central 애플리케이션에서 사용하는 IoT Hub에서 연결 문자열을 자동으로 검색합니다. 이제 디바이스에서 IoT Central에 연결하고 데이터 보내기를 시작할 수 있습니다.

1. 운영자는 디바이스를 차단할 수 있습니다. 디바이스가 차단되면 데이터를 IoT Central 애플리케이션에 보낼 수 없습니다. 차단된 디바이스의 상태는 **차단됨** 입니다. 사용자가 데이터 보내기를 다시 시작하려면 먼저 디바이스를 다시 설정해야 합니다. 운영자가 디바이스의 차단을 해제하면 상태가 이전 값인 **등록됨** 또는 **프로비전됨** 으로 돌아갑니다.

1. 디바이스 상태가 **승인을 기다리는 중** 이면 **자동 승인** 옵션이 사용하지 않도록 설정된 것입니다. 운영자는 데이터 보내기를 시작하기 전에 디바이스를 명시적으로 승인해야 합니다. **디바이스** 페이지에서 수동으로 등록되지 않았지만 유효한 자격 증명을 사용하여 연결된 디바이스의 디바이스 상태는 **승인을 기다리는 중** 입니다. 사용자는 **승인** 단추를 사용하여 **디바이스** 페이지에서 이러한 디바이스를 승인할 수 있습니다.

1. 디바이스 상태가 **연결 안 됨** 이면 IoT Central에 연결하는 디바이스에 연결된 디바이스 템플릿이 없음을 의미입니다. 이 상황은 일반적으로 다음 시나리오에서 발생합니다.

    - 디바이스 템플릿을 지정하지 않고 **디바이스** 페이지에서 **가져오기** 를 사용하여 디바이스 세트가 추가됩니다.
    - 디바이스 템플릿을 지정하지 않고 **디바이스** 페이지에서 디바이스가 수동으로 등록되었습니다. 그런 다음, 디바이스가 유효한 자격 증명을 사용하여 연결되었습니다.  

    운영자는 **마이그레이션** 단추를 사용하여 **디바이스** 페이지에서 디바이스 템플릿에 디바이스를 연결할 수 있습니다.

## <a name="device-connection-status"></a>디바이스 연결 상태

디바이스 또는 에지 디바이스가 MQTT 프로토콜을 사용하여 연결되면 디바이스에 대한 _연결_ 및 _연결 끊김_ 이벤트가 생성됩니다. 이러한 이벤트는 디바이스에서 보내는 것이 아니라 IoT Central에서 내부적으로 생성됩니다.

다음 다이어그램은 디바이스가 연결될 때 기간 끝에 연결이 등록되는 방법을 보여 줍니다. 여러 연결 및 연결 끊김 이벤트가 발생하는 경우 IoT Central은 기간 끝에 가장 가까운 이벤트를 등록합니다. 예를 들어 디바이스가 기간 내에 연결을 끊었다가 다시 연결하는 경우 IoT Central은 연결 이벤트를 등록합니다. 현재 이 기간은 약 1분입니다.

:::image type="content" source="media/concepts-get-connected/device-connectivity-diagram.png" alt-text="연결 이벤트 및 연결 끊김 이벤트에 대한 이벤트 창을 보여 주는 다이어그램." border="false":::

장치 연결 상태를 모니터링 하는 방법에 대 한 자세한 내용을 보려면 다음 비디오를 시청 하세요.

> [!VIDEO https://www.youtube.com/embed/EUZH_6Ihtto]

[IoT Central에서 내보내기](howto-export-data.md#set-up-data-export)에 연결 및 연결 끊김 이벤트를 포함할 수 있습니다. 자세한 내용은 [IoT Hub 이벤트에 반응 > 디바이스 연결 및 디바이스 연결 끊김 이벤트에 대한 제한 사항](../../iot-hub/iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events).

## <a name="sdk-support"></a>SDK 지원

Azure 디바이스 SDK는 디바이스 코드를 구현하는 가장 쉬운 방법을 제공합니다. 현재 다음과 같은 디바이스 SDK를 사용할 수 있습니다.

- [C용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-c)
- [Python용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-python)
- [Node.js용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-node)
- [Java용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-java)
- [.NET용 Azure IoT SDK](https://github.com/azure/azure-iot-sdk-csharp)

### <a name="sdk-features-and-iot-hub-connectivity"></a>SDK 기능 및 IoT Hub 연결

IoT Hub와의 모든 디바이스 통신에 다음 IoT Hub 연결 옵션이 사용됩니다.

- [디바이스-클라우드 메시징](../../iot-hub/iot-hub-devguide-messages-d2c.md)
- [클라우드-디바이스 메시징](../../iot-hub/iot-hub-devguide-messages-c2d.md)
- [디바이스 쌍](../../iot-hub/iot-hub-devguide-device-twins.md)

다음 표에는 Azure IoT Central 디바이스 기능이 IoT Hub 기능에 매핑되는 방식이 요약되어 있습니다.

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| 원격 분석 | 디바이스-클라우드 메시징 |
| 오프라인 명령 | 클라우드-디바이스 메시징 |
| 속성 | 디바이스 쌍 reported 속성 |
| 속성(쓰기 가능) | 디바이스 쌍 desired 및 reported 속성 |
| 명령 | 직접 메서드 |

### <a name="protocols"></a>프로토콜

디바이스 SDK는 IoT Hub에 연결하기 위한 다음과 같은 네트워크 프로토콜을 지원합니다.

- MQTT
- AMQP
- HTTPS

프로토콜 간 차이점 및 프로토콜 선택 방법에 대한 지침은 [통신 프로토콜 선택](../../iot-hub/iot-hub-devguide-protocols.md)을 참조하세요.

디바이스에서 지원되는 프로토콜을 사용할 수 없는 경우 Azure IoT Edge를 사용하여 프로토콜 변환을 수행합니다. IoT Edge는 Azure IoT Central 애플리케이션에서 처리를 오프로드하는 다른 intelligence-on-the-edge(에지의 인텔리전스) 시나리오를 지원합니다.

## <a name="security"></a>보안

디바이스와 Azure IoT Central 간에 교환되는 모든 데이터는 암호화됩니다. IoT Hub는 디바이스 연결 IoT Hub 엔드포인트 중 하나에 연결하는 디바이스의 모든 요청을 인증합니다. 유선으로 자격 증명을 교환하지 못하도록 방지하기 위해 디바이스에서는 서명된 토큰을 사용하여 인증합니다. 자세한 내용은 [IoT Hub에 대한 액세스 제어](../../iot-hub/iot-hub-devguide-security.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

몇 가지 제안된 다음 단계는 다음과 같습니다.

- 디바이스 개발에 대한 [모범 사례](concepts-best-practices.md)를 검토합니다.
- [자습서: 클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결](tutorial-connect-device.md)에서 SAS 토큰을 사용하는 방법을 보여 주는 샘플 코드를 검토합니다.
- [IoT Central 애플리케이션용 Node.js 디바이스 SDK를 사용하여 X.509 인증서로 디바이스를 연결하는 방법](how-to-connect-devices-x509.md)을 알아봅니다.
- [Azure CLI를 사용하여 디바이스 연결을 모니터링](./howto-monitor-devices-azure-cli.md)하는 방법을 알아봅니다.
- [Azure IoT Edge 디바이스 및 Azure IoT Central](./concepts-iot-edge.md)을 참조합니다.
