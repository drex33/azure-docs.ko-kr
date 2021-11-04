---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 92c933cf2f504459359652124f72ca959241db92
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505951"
---
## <a name="create-a-dps-enrollment"></a>DPS 등록 만들기

DPS를 통해 하나 이상의 장치를 프로 비전 하는 등록을 만듭니다.

단일 IoT Edge 장치를 프로 비전 하려는 경우 **개별 등록** 을 만듭니다. 여러 장치를 프로 비전 해야 하는 경우 DPS **그룹 등록** 을 만드는 단계를 수행 합니다.

DPS에서 등록을 만들 때 **초기 장치 쌍 상태** 를 선언할 수 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](../articles/iot-edge/how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

장치 프로 비전 서비스의 등록에 대 한 자세한 내용은 [How to manage device 등록](../articles/iot-dps/how-to-manage-enrollments.md)항목을 참조 하세요.

# <a name="individual-enrollment"></a>[개별 등록](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>DPS 개별 등록 만들기

> [!TIP]
> 이 문서의 단계는 Azure Portal에 대 한 것 이지만 Azure CLI를 사용 하 여 개별 등록를 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment](/cli/azure/iot/dps/enrollment)를 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다.

1. [Azure Portal](https://portal.azure.com)에서 장치 프로 비전 서비스 IoT Hub 인스턴스로 이동 합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. 등록을 구성하려면 **개별 등록 추가** 를 선택한 다음, 다음 단계를 완료합니다.  

   1. **메커니즘** 에서 **대칭 키** 를 선택합니다.

   1. 디바이스에 대한 고유한 **등록 ID** 를 제공합니다.

   1. 원하는 경우, 디바이스에 대한 **IoT Hub 디바이스 ID** 를 제공합니다. 디바이스 ID를 사용하여 모듈 배포에 대한 개별 디바이스를 대상으로 할 수 있습니다. 디바이스 ID를 제공하지 않으면 등록 ID가 사용됩니다.

   1. 등록이 IoT Edge 디바이스에 대한 것이라고 선언하려면 **True** 를 선택합니다.

   1. 원하는 경우, **초기 디바이스 쌍 상태** 에 태그 값을 추가합니다. 태그를 사용하여 모듈 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 예를 들면 다음과 같습니다.

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

1. IoT Edge 런타임을 설치할 때 사용할 개별 등록의 **기본 키** 값을 복사합니다.

이제 이 디바이스에 대한 등록이 존재하므로 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비저닝할 수 있습니다.

# <a name="group-enrollment"></a>[그룹 등록](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>DPS 그룹 등록 만들기

> [!TIP]
> 이 문서의 단계는 Azure Portal에 대 한 것 이지만 Azure CLI를 사용 하 여 그룹 등록 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group)을 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다. 그룹 등록의 경우 모든 디바이스가 IoT Edge 디바이스여야 하며, 그렇지 않은 경우 어떤 디바이스도 등록할 수 없습니다.

1. [Azure Portal](https://portal.azure.com)에서 장치 프로 비전 서비스 IoT Hub 인스턴스로 이동 합니다.

1. **설정** 에서 **등록 관리** 를 선택합니다.

1. 등록을 구성하려면 **개별 등록 추가** 를 선택한 다음, 다음 단계를 완료합니다.  

   1. **그룹 이름** 을 제공합니다.

   1. 인증 유형으로 **대칭 키** 를 선택합니다.

   1. 등록이 IoT Edge 디바이스에 대한 것이라고 선언하려면 **True** 를 선택합니다. 그룹 등록의 경우 모든 디바이스가 IoT Edge 디바이스여야 하며, 그렇지 않은 경우 어떤 디바이스도 등록할 수 없습니다.

   1. 원하는 경우, **초기 디바이스 쌍 상태** 에 태그 값을 추가합니다. 태그를 사용하여 모듈 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 예를 들면 다음과 같습니다.

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

1. 그룹 등록에 사용할 장치 키를 만들 때 사용할 등록 그룹의 **기본 키** 값을 복사합니다.

이제 등록 그룹이 있으므로 IoT Edge 런타임은 설치 중에 장치를 자동으로 프로 비전 할 수 있습니다.

#### <a name="derive-a-device-key"></a>디바이스 키 파생

그룹 등록의 일부로 프로비전된 각 장치에는 프로비전 중에 등록으로 대칭 키 증명을 수행하기 위해 파생된 디바이스 키가 필요합니다.

디바이스 키를 생성하려면 DPS 등록 그룹에서 복사한 키를 사용하여 디바이스에 대한 고유한 등록 ID의 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)을 계산하고 결과를 Base64 형식으로 변환합니다.

> [!IMPORTANT]
> 디바이스 코드에 등록의 기본 또는 보조 키를 포함하지 마세요.

Windows의 경우 PowerShell을 사용하여 다음 예제에 표시된 대로 파생된 디바이스 키를 생성할 수 있습니다.

**KEY** 의 값을 이전에 적어 둔 **기본 키** 로 바꿉니다.

**REG_ID** 의 값을 디바이스의 등록 ID로 바꿉니다.

```powershell
$KEY='PASTE_YOUR_ENROLLMENT_KEY_HERE'
$REG_ID='PASTE_YOUR_REGISTRATION_ID_HERE'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

다음은 파생 된 장치 키의 샘플 출력입니다.

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---
