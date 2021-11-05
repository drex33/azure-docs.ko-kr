---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 16338971721edfb68c93821ddf08c5cc36caf0b3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846003"
---
## <a name="create-a-dps-enrollment"></a>DPS 등록 만들기

생성된 인증서 및 키를 사용하여 하나 이상의 IoT Edge 디바이스에 대한 등록을 DPS에 만듭니다.

단일 IoT Edge 디바이스를 프로비전하려는 경우 **개별 등록을** 만듭니다. 여러 디바이스를 프로비전해야 하는 경우 DPS **그룹 등록을** 만드는 단계를 수행합니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태** 를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](../articles/iot-edge/how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

디바이스 프로비전 서비스의 등록에 대한 자세한 내용은 디바이스 [등록을 관리하는 방법을 참조하세요.](../articles/iot-dps/how-to-manage-enrollments.md)

# <a name="individual-enrollment"></a>[개별 등록](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>DPS 개별 등록 만들기

개별 등록은 디바이스의 ID 인증서의 퍼블릭 부분을 가져와 디바이스의 인증서와 일치시킵니다.

> [!TIP]
> 이 문서의 단계는 Azure Portal 대한 단계이지만 Azure CLI 사용하여 개별 등록을 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment](/cli/azure/iot/dps/enrollment)를 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub 디바이스 프로비저닝 서비스의 인스턴스로 이동합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. 등록을 구성하려면 **개별 등록 추가** 를 선택한 다음, 다음 단계를 완료합니다.  

   * **메커니즘**: **X.509** 를 선택합니다.

   * **기본 인증서 .pem 또는 .cer 파일**: 디바이스 ID 인증서에서 퍼블릭 파일을 업로드합니다. 스크립트를 사용하여 테스트 인증서를 생성한 경우 다음 파일을 선택합니다.

      `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`

   * **IoT Hub 디바이스 ID**: 원하는 경우 디바이스에 대한 ID를 제공합니다. 디바이스 ID를 사용하여 모듈 배포에 대한 개별 디바이스를 대상으로 할 수 있습니다. 디바이스 ID를 제공하지 않으면 X.509 인증서의 일반 이름(CN)이 사용됩니다.

   * **IoT Edge 디바이스**: 등록이 IoT Edge 디바이스에 대한 것이라고 선언하려면 **True** 를 선택합니다.

   * **이 디바이스를 할당할 수 있는 IoT 허브 선택**: 디바이스를 연결하려는 연결된 IoT 허브를 선택합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 허브 중 하나에 디바이스가 할당됩니다.

   * **초기 디바이스 쌍 상태**: 원하는 경우 디바이스 쌍에 태그 값을 추가합니다. 태그를 사용하여 자동 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 예를 들면 다음과 같습니다.

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. **저장** 을 선택합니다.

**등록 관리** 에서 방금 만든 등록에 대한 **등록 ID를** 볼 수 있습니다. 디바이스를 프로비전할 때 사용할 수 있기 때문에 기록해 둡다.

이제 이 디바이스에 대한 등록이 존재하므로 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비저닝할 수 있습니다.

# <a name="group-enrollment"></a>[그룹 등록](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>DPS 그룹 등록 만들기

그룹 등록은 개별 디바이스 ID 인증서 생성에 사용되는 인증서 신뢰 체인의 중간 또는 루트 CA 인증서를 사용합니다.

#### <a name="verify-your-root-certificate"></a>루트 인증서 확인

등록 그룹을 만들 때 확인된 인증서를 사용할 수 있는 옵션이 존재합니다. 루트 인증서의 소유권이 있음을 증명하여 DPS로 인증서를 확인할 수 있습니다. 자세한 내용은 [X.509 CA 인증서에 대해 소유 증명을 수행하는 방법](../articles/iot-dps/how-to-verify-certificates.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub 디바이스 프로비저닝 서비스의 인스턴스로 이동합니다.

1. 왼쪽 메뉴에서 **인증서** 를 선택합니다.

1. **추가** 를 선택하여 새 인증서를 추가합니다.

1. 인증서에 식별 이름을 입력하고 X.509 인증서의 퍼블릭 부분을 나타내는 .cer 또는 .pem 파일로 이동합니다.

   데모 인증서를 사용하는 경우 `<wrkdir>\certs\azure-iot-test-only.root.ca.cert.pem` 인증서를 업로드합니다.

1. **저장** 을 선택합니다.

1. 이제 인증서가 **인증서** 페이지에 나열됩니다. 인증서 세부 정보를 열려면 해당 페이지를 선택합니다.

1. **확인 코드 생성** 을 선택하고 생성된 코드를 복사합니다.

1. 사용자 고유의 CA 인증서를 사용하든 데모 인증서를 사용하든 관계없이 IoT Edge 리포지토리에 제공되는 확인 도구를 사용하여 소유 증명을 확인할 수 있습니다. 확인 도구는 CA 인증서를 사용하여 제공된 확인 코드를 주체 이름으로 포함하는 새 인증서에 서명합니다.

   ```powershell
   New-CACertsVerificationCert "<verification code>"
   ```

1. Azure Portal의 동일한 인증서 정보 페이지에서 새로 생성된 확인 인증서를 업로드합니다.

1. **확인** 을 선택합니다.

#### <a name="create-enrollment-group"></a>등록 그룹 만들기

디바이스 프로비전 서비스의 등록에 대한 자세한 내용은 디바이스 [등록을 관리하는 방법을 참조하세요.](../articles/iot-dps/how-to-manage-enrollments.md)

> [!TIP]
> 이 문서의 단계는 Azure Portal 대한 단계이지만 Azure CLI 사용하여 그룹 등록을 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group)을 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다. 그룹 등록의 경우 모든 디바이스가 IoT Edge 디바이스여야 하며, 그렇지 않은 경우 어떤 디바이스도 등록할 수 없습니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub 디바이스 프로비저닝 서비스의 인스턴스로 이동합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. 등록을 구성하려면 **등록 그룹 추가** 를 선택한 다음, 다음 단계를 완료합니다.

   * **그룹 이름**: 이 그룹 등록에 대해 기억하기 쉬운 이름을 입력합니다.

   * **증명 형식**: **인증서** 를 선택합니다.

   * **IoT Edge 디바이스**: **True** 를 선택합니다. 그룹 등록의 경우 모든 디바이스가 IoT Edge 디바이스여야 하며, 그렇지 않은 경우 어떤 디바이스도 등록할 수 없습니다.

   * **인증서 유형:** **CA 인증서** 를 선택합니다.

   * **기본 인증서:** 드롭다운 목록에서 인증서를 선택합니다.

   * **이 디바이스를 할당할 수 있는 IoT 허브 선택**: 디바이스를 연결하려는 연결된 IoT 허브를 선택합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 허브 중 하나에 디바이스가 할당됩니다.

   * **초기 디바이스 쌍 상태**: 원하는 경우 디바이스 쌍에 태그 값을 추가합니다. 태그를 사용하여 자동 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 예를 들면 다음과 같습니다.

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. **저장** 을 선택합니다.

**등록 관리** 에서 방금 만든 등록에 대한 **등록 ID를** 볼 수 있습니다. 디바이스를 프로비전할 때 사용할 수 있기 때문에 기록해 둡다.

이제 이러한 디바이스에 대한 등록이 존재하기 때문에 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비전할 수 있습니다.

---
