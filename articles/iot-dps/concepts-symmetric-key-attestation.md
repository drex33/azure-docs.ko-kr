---
title: Azure IoT Hub Device Provisioning Service - 대칭 키 증명
description: 이 문서에서는 IoT DPS(Device Provisioning Service)를 사용하여 대칭 키 증명의 개념을 개괄적으로 설명합니다.
author: wesmc7777
ms.author: wesmc
ms.date: 04/23/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.custom: devx-track-csharp
ms.openlocfilehash: 2d7b5505092e2dfe32624545128be4ce4dbb3459
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278940"
---
# <a name="symmetric-key-attestation"></a>대칭 키 증명

이 문서에서는 Device Provisioning Service에서 대칭 키를 사용하는 경우 ID 증명 프로세스에 대해 설명합니다. 

대칭 키 증명은 Device Provisioning Service 인스턴스로 디바이스를 인증하는 간단한 방법입니다. 이 증명 방법은 디바이스 프로비저닝을 처음 사용하는 개발자나 엄격한 보안 요구 사항이 없는 개발자를 위한 "Hello World" 환경을 나타냅니다. [TPM](concepts-tpm-attestation.md) 또는 [X.509 인증서](concepts-x509-attestation.md)를 사용하는 디바이스 증명은 더욱 안전하며, 보다 엄격한 보안 요구 사항에 사용해야 합니다.

대칭 키 등록은 레거시 디바이스에 대한 유용한 방법을 제공하며 Azure IoT를 통해 클라우드로 부트스트랩할 수 있는 보안 기능이 제한됩니다. 레거시 디바이스에서 대칭 키 증명에 대한 자세한 내용은 [레거시 디바이스에서 대칭 키를 사용하는 방법](how-to-legacy-device-symm-key.md)을 참조하세요.


## <a name="symmetric-key-creation"></a>대칭 키 만들기

기본적으로 Device Provisioning Service는 **키 자동 생성** 옵션을 사용하도록 설정하여 새 등록이 저장된 경우 기본 길이인 64바이트로 새 대칭 키를 만듭니다.

![대칭 키 자동 생성](./media/concepts-symmetric-key-attestation/auto-generate-keys.png)

이 옵션을 사용하지 않도록 설정하면 등록을 위한 자체 대칭 키를 제공할 수도 있습니다. 자체 대칭 키를 지정하는 경우 키 길이는 16바이트에서 64바이트 사이여야 합니다. 또한 대칭 키는 유효한 Base64 형식으로 제공해야 합니다.



## <a name="detailed-attestation-process"></a>자세한 증명 프로세스

Device Provisioning Service의 대칭 키 증명은 IoT 허브에서 지원하는 것과 동일한 [보안 토큰](../iot-hub/iot-hub-dev-guide-sas.md#security-token-structure)을 사용하여 수행되어 디바이스를 식별합니다. 이러한 보안 토큰은 [SAS(공유 액세스 서명) 토큰](../service-bus-messaging/service-bus-sas.md)입니다. 

SAS 토큰에는 대칭 키를 사용하여 생성된 해시된 서명이 있습니다. 서명은 Device Provisioning Service에서 다시 생성되어 증명 중에 제시되는 보안 토큰의 진위 여부를 확인합니다.

SAS 토큰은 다음과 같은 형식입니다.

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

각 토큰의 구성 요소는 다음과 같습니다.

| 값 | Description |
| --- | --- |
| {signature} |HMAC-SHA256 서명 문자열입니다. 개별 등록의 경우 이 서명은 대칭 키(기본 또는 보조)를 사용하여 해시를 수행함으로써 생성됩니다. 등록 그룹의 경우, 등록 그룹 키에서 파생된 키가 해시를 수행하는 데 사용됩니다. 해시는 `URL-encoded-resourceURI + "\n" + expiry` 형식의 메시지에서 수행됩니다. **중요**: 키는 HMAC-SHA256 계산을 수행하는 데 사용되기 전에 base64에서 디코딩되어야 합니다. 또한 서명 결과는 URL로 인코딩되어야 합니다. |
| {resourceURI} |Device Provisioning Service 인스턴스의 범위 ID로 시작되는, 이 토큰으로 액세스할 수 있는 등록 엔드포인트의 URI입니다. 예를 들어 `{Scope ID}/registrations/{Registration ID}` |
| {expiry} |1970년 1월 1일 epoch 0시 UTC 이후의 초 수에 대한 UTF8 문자열입니다. |
| {URL-encoded-resourceURI} |소문자 URL-소문자 리소스 URI의 인코딩 |
| {policyName} |이 토큰을 참조하는 공유 액세스 정책의 이름입니다. 대칭 키 증명이 있는 프로비저닝이 **등록** 인 경우 사용되는 정책 이름입니다. |

디바이스가 개별 등록으로 증명하는 경우 디바이스는 개별 등록 항목에 정의된 대칭 키를 사용하여 SAS 토큰에 대해 해시된 서명을 만듭니다.

SAS 토큰을 만드는 코드 예제는 [보안 토큰](../iot-hub/iot-hub-dev-guide-sas.md#security-token-structure)을 참조하세요.

대칭 키 증명을 위한 보안 토큰 생성은 Azure IoT C SDK에서 지원됩니다. Azure IoT C SDK를 사용 하 여 개별 등록을 증명 하는 예제는 [시뮬레이션 된 대칭 키 장치 프로 비전](quick-create-simulated-device-symm-key.md)을 참조 하세요.


## <a name="group-enrollments"></a>그룹 등록

그룹 등록을 위한 대칭 키는 프로비전할 때 디바이스에서 직접 사용되지 않습니다. 대신 등록 그룹에 속하는 디바이스는 파생된 디바이스 키를 사용하여 프로비전합니다. 

먼저 등록 그룹으로 증명하는 각 디바이스에 대해 고유한 등록 ID가 정의됩니다. 등록 ID로 유효한 문자는 소문자 영숫자 및 대시('-')입니다. 이 등록 ID는 디바이스를 식별하는 고유한 ID여야 합니다. 예를 들어, 레거시 디바이스는 다양한 보안 기능을 지원하지 않을 수 있습니다. 레거시 디바이스에는 이 디바이스를 고유하게 식별할 수 있는 MAC 주소 또는 일련 번호만 있을 수 있습니다. 이런 경우 등록 ID는 다음과 비슷한 MAC 주소와 일련 번호로 구성될 수 있습니다.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

이와 일치하는 예제가 [대칭 키를 사용하여 레거시 디바이스를 프로비전하는 방법](how-to-legacy-device-symm-key.md) 문서에 사용되었습니다.

디바이스에 대한 등록 ID가 정의되면, 등록 그룹에 대한 대칭 키는 등록 ID의 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) 해시를 계산하여 파생 디바이스 키를 생성하는 데 사용됩니다. 파생된 디바이스 키를 계산하는 몇 가지 예는 아래 탭에 나와 있습니다.  


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에 대한 IoT 확장에서는 파생된 디바이스 키를 생성할 수 있는 [`compute-device-key`](/cli/azure/iot/dps?view=azure-cli-latest&preserve-view=true#az_iot_dps_compute_device_key) 명령을 제공합니다. 이 명령은 Windows 기반 시스템이나 Linux 시스템의 PowerShell 또는 Bash 셸에서 사용할 수 있습니다.

`--key` 인수 값을 등록 그룹의 **기본 키** 로 바꿉니다.

`--registration-id` 인수의 값을 등록 ID로 바꿉니다.

```azurecli
az iot dps compute-device-key --key 8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw== --registration-id sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

결과 예제:

```azurecli
"Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc="
```

# <a name="windows"></a>[Windows](#tab/windows)

Windows 기반 워크스테이션을 사용하는 경우 PowerShell을 사용하여 다음 예제에 표시된 대로 파생된 디바이스 키를 생성할 수 있습니다.

**KEY** 값을 등록 그룹의 **기본 키** 로 바꿉니다.

**REG_ID** 의 값을 등록 ID로 바꿉니다.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

# <a name="linux"></a>[Linux](#tab/linux)

Linux 워크스테이션을 사용하는 경우 openssl을 사용하여 다음 예제에 표시된 대로 파생된 디바이스 키를 생성할 수 있습니다.

**KEY** 값을 등록 그룹의 **기본 키** 로 바꿉니다.

**REG_ID** 의 값을 등록 ID로 바꿉니다.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

# <a name="csharp"></a>[CSharp](#tab/csharp)

등록 ID의 해시는 다음 C# 코드를 사용하여 수행할 수 있습니다.

```csharp
using System; 
using System.Security.Cryptography; 
using System.Text;  

public static class Utils 
{ 
    public static string ComputeDerivedSymmetricKey(byte[] masterKey, string registrationId) 
    { 
        using (var hmac = new HMACSHA256(masterKey)) 
        { 
            return Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(registrationId))); 
        } 
    } 
} 
```

```csharp
String deviceKey = Utils.ComputeDerivedSymmetricKey(Convert.FromBase64String(masterKey), registrationId);
```

---

생성된 디바이스 키는 증명에 사용될 SAS 토큰을 생성하는 데 사용됩니다. 등록 그룹의 각 디바이스는 고유한 파생 키에서 생성된 보안 토큰을 사용하여 증명하는 데 필요합니다. 등록 그룹 대칭 키는 증명에 직접 사용될 수 없습니다.

#### <a name="installation-of-the-derived-device-key"></a>파생된 디바이스 키 설치

디바이스 키는 파생되어 공장에 설치되는 것이 이상적입니다. 이 방법을 사용하면 디바이스에 배포된 모든 소프트웨어에 그룹 키가 절대 포함되지 않습니다. 디바이스에 MAC 주소 또는 일련 번호가 할당되면 키가 파생되어 디바이스에 삽입될 수 있지만 제조업체가 저장하도록 선택합니다.

각 디바이스 등록 ID를 그룹 등록 키(**K**)로 해싱하여 공장에서 생성된 디바이스 키 테이블을 보여주는 다음 다이어그램을 고려해보십시오. 

![공장에서 할당된 디바이스 키](./media/concepts-symmetric-key-attestation/key-diversification.png)

각 디바이스의 ID는 공장에서 설치된 등록 ID 및 파생 디바이스 키로 표시됩니다. 디바이스 키는 다른 위치로 절대 복사되지 않으며 그룹 키는 디바이스에 절대 저장되지 않습니다.

디바이스 키가 공장에서 설치되지 않은 경우, [하드웨어 보안 모듈 HSM](concepts-service.md#hardware-security-module)을 사용하여 디바이스 ID를 안전하게 저장해야 합니다.

## <a name="next-steps"></a>다음 단계

대칭 키 증명에 대해 이해했으면, 다음 문서에서 자세한 내용을 확인해보십시오.

* [빠른 시작: 시뮬레이션 된 대칭 키 장치 프로 비전](quick-create-simulated-device-symm-key.md)
* [프로비저닝 개념에 대한 자세한 정보](about-iot-dps.md#provisioning-process)
* [자동 프로비전 사용 시작](./quick-setup-auto-provision.md) 
