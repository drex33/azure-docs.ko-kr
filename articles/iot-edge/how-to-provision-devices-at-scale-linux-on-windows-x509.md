---
title: Windows의 Linux에서 x.509 인증서를 사용 하 여 IoT Edge 장치 만들기 및 프로 비전-Azure IoT Edge | Microsoft Docs
description: x.509 인증서 증명을 사용 하 여 장치 프로 비전 서비스를 통해 Azure IoT Edge에 대 한 대규모로 프로 비전 장치 테스트
author: v-tcassi
ms.author: v-tcassi
ms.reviewer: kgremban
ms.date: 08/17/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 309ac9bf4810d245695ad92c316a23d4d8571ac5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634268"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-x509-certificates"></a>x.509 인증서를 사용 하 여 대규모로 Windows 장치에서 Linux 용 IoT Edge 만들기 및 프로 비전

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

이 문서에서는 x.509 인증서를 사용 하 여 [Windows 장치에서 Linux에 대 한](iot-edge-for-linux-on-windows.md) 하나 이상의 IoT Edge를 자동으로 프로 비전 하는 종단 간 지침을 제공 합니다. [Azure IoT 허브 장치 프로 비전 서비스](../iot-dps/index.yml) (DPS)를 사용 하 여 Azure IoT에 지 장치를 자동으로 프로 비전 할 수 있습니다. 자동 프로 비전 프로세스에 익숙하지 않은 경우 계속 하기 전에 [프로 비전 개요](../iot-dps/about-iot-dps.md#provisioning-process) 를 검토 합니다.

작업은 다음과 같습니다.

1. 인증서 및 키를 생성합니다.
1. 단일 장치에 대 한 **개별 등록** 또는 장치 집합에 대 한 **그룹 등록** 을 만듭니다.
1. IoT Edge 런타임을 설치한 Linux 가상 머신을 배포 하 고 IoT Hub에 연결 합니다.

X.509 인증서를 증명 메커니즘으로 사용하면 프로덕션의 크기를 조정하고 디바이스 프로비전을 간소화할 수 있습니다. 일반적으로 X.509 인증서는 신뢰할 수 있는 인증서 체인에 정렬됩니다. 자체 서명되었거나 신뢰할 수 있는 루트 인증서부터 시작하여, 체인에 존재하는 각 인증서는 그다음으로 낮은 인증서에 서명합니다. 이 패턴을 통해 루트 인증서부터 시작하여 각 중간 인증서를 거쳐 디바이스에 설치된 마지막 “리프” 인증서에서 완료되는 위임된 신뢰 체인이 만들어집니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 IoT Hub
* 최신 버전의 [Git](https://git-scm.com/download/)이 설치되어 있습니다.
* 다음과 같은 최소 시스템 요구 사항을 충족하는 Windows 디바이스:
  * Windows 10 버전 1809 이상, 빌드 17763 이상
  * Professional, Enterprise 또는 Server 버전
  * 사용 가능한 최소 메모리: 1GB
  * 사용 가능한 최소 디스크 공간: 10GB
  * 가상화 지원
    * Windows 10에서 Hyper-V를 사용하도록 설정합니다. 자세한 내용은 [Hyper-V를 Windows 10에 설치](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)를 참조하세요.
    * Windows Server에서 Hyper-V 역할을 설치하고 기본 네트워크 스위치를 만듭니다. 자세한 내용은 [Azure IoT Edge for Linux on Windows의 중첩된 가상화](nested-virtualization.md)를 참조하세요.
    * 가상 머신에서 중첩 가상화를 구성합니다. 자세한 내용은 [중첩된 가상화](nested-virtualization.md)를 참조하세요.
  * 네트워킹 지원
    * Windows Server에는 기본 스위치가 없습니다. EFLOW를 Windows Server 디바이스에 배포하려면 먼저 가상 스위치를 만들어야 합니다.  자세한 내용은 [Windows에서 Linux용 가상 스위치 만들기](how-to-create-virtual-switch.md)를 참조하세요.
    * Windows 데스크톱 버전에는 EFLOW 설치에 사용할 수 있는 기본 스위치가 있습니다. 필요한 경우 고유한 사용자 지정 가상 스위치를 만들 수 있습니다.
* Azure의 IoT Hub Device Provisioning Service의 새 인스턴스로 IoT 허브에 연결되어 있습니다.
  * 장치 프로 비전 서비스 인스턴스가 없는 경우 [새 IoT Hub Device Provisioning Service 만들기](../iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) 의 지침에 따라 IoT Hub Device Provisioning Service 빠른 시작의 [IoT Hub 및 장치 프로 비전 서비스를 연결할](../iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) 수 있습니다.
  * Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.
* Windows 배포의 Linux용 Azure IoT Edge에서 **GPU 가속 Linux 모듈** 을 사용하려는 경우 고려해야 할 몇 가지 구성 옵션이 있습니다. GPU 아키텍처에 따라 올바른 드라이버를 설치해야 하며 Windows 참가자 프로그램 빌드에 액세스해야 할 수도 있습니다. 구성 요구 사항을 확인하고 이러한 필수 조건을 충족하려면 [Windows의 Linux용 Azure IoT Edge에 대한 GPU 가속](gpu-acceleration.md)을 참조하세요.

**PowerShell** 또는 **Windows 관리 센터** 를 사용 하 여 IoT Edge 장치를 설치 하 고 관리할 수 있습니다. 각 유틸리티에는 고유한 필수 구성 요소가 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하려는 경우 다음 단계를 사용 하 여 Windows에서 Linux 용 Azure IoT Edge를 다운로드 하 고 설치 합니다.

1. 관리자 권한 PowerShell 세션에서 다음 명령을 각각 실행하여 IoT Edge for Linux on Windows를 다운로드합니다.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. IoT Edge for Linux on Windows를 디바이스에 설치합니다.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   설치 명령에 `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` 및 `VHDXDIR="<FULLY_QUALIFIED_PATH>"` 매개 변수를 추가하여 사용자 지정 설치 및 VHDX 디렉터리를 지정할 수 있습니다.

1. 대상 장치에 대 한 실행 정책을로 설정 `AllSigned` 합니다. 다음을 사용하여 관리자 권한 PowerShell 프롬프트에서 현재 실행 정책을 확인할 수 있습니다.

   ```powershell
   Get-ExecutionPolicy -List
   ```

   `local machine`의 실행 정책이 `AllSigned`가 아닌 경우 다음을 사용하여 실행 정책을 설정할 수 있습니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Windows PowerShell 모듈의 Linux에 대 한 Azure IoT Edge에 대 한 자세한 내용은 [PowerShell 함수 참조](reference-iot-edge-for-linux-on-windows-functions.md)를 참조 하세요.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows 관리 센터를 사용 하려는 경우 Windows 관리 센터에 대 한 액세스 권한이 있는지 확인 하 고 Azure IoT Edge 확장이 설치 되어 있는지 확인 합니다.

   1. [Windows Admin Center 설치 관리자](https://aka.ms/wacdownload)를 다운로드 및 실행합니다. 설치 마법사의 프롬프트에 따라 Windows Admin Center를 설치합니다.

   1. 설치되면 지원되는 브라우저를 사용하여 Windows Admin Center를 엽니다. 지원되는 브라우저에는 Microsoft Edge(Windows 10 버전 1709 이상), Google Chrome 및 Microsoft Edge Insider가 있습니다.

   1. Windows Admin Center를 처음 사용하는 경우 사용할 인증서를 선택하라는 메시지가 표시됩니다. 인증서로 **Windows Admin Center 클라이언트** 를 선택합니다.

   1. Azure IoT Edge 확장을 설치합니다. Windows Admin Center 대시보드의 오른쪽 위에서 기어 아이콘을 선택합니다.

      ![대시보드의 오른쪽 위에 있는 기어 아이콘을 선택 하 여 PNG 설정에 액세스 합니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. **설정** 메뉴의 **게이트웨이** 아래에서 **확장** 을 선택합니다.

   1. **사용 가능한 확장** 탭의 확장 목록에서 **Azure IoT Edge** 를 찾습니다. 이를 선택하고, 확장 목록 위의 **설치** 프롬프트를 선택합니다.

   1. 설치가 완료되면 **설치된 확장** 탭의 설치된 확장 목록에 Azure IoT Edge가 표시됩니다.

---

## <a name="generate-device-identity-certificates"></a>디바이스 ID 인증서 생성

디바이스 ID 인증서는 인증서 신뢰 체인을 통해 상위 X.509 인증 기관(CA) 인증서에 연결하는 리프 인증서입니다. 장치 id 인증서에는 장치를 IoT hub에 포함 하려는 장치 ID로 설정 된 CN (일반 이름)이 있어야 합니다.

디바이스 ID 인증서는 IoT Edge 디바이스를 프로비저닝하고 Azure IoT Hub 디바이스를 인증하는 데만 사용됩니다. 해당 인증서는 IoT Edge 디바이스가 모듈이나 리프 디바이스에 확인을 위해 제공하는 CA 인증서와 달리 서명 인증서가 아닙니다. 자세한 내용은 [Azure IoT Edge 인증서 사용 현황 세부 정보](iot-edge-certs.md)를 참조하세요.

디바이스 ID 인증서를 만든 후에는 2개의 파일이 있어야 합니다. 이는 인증서의 퍼블릭 부분을 포함하는 .cer 또는 .pem 파일 및 인증서의 프라이빗 키를 포함하는 .cer 또는 .pem 파일입니다. DPS에서 그룹 등록을 사용하려면 동일한 인증서 신뢰 체인에 중간 또는 루트 CA 인증서의 퍼블릭 부분도 있어야 합니다.

X.509를 사용하여 자동 프로비저닝을 설정하려면 다음 파일이 필요합니다.

* 디바이스 ID 인증서 및 해당 인증서의 프라이빗 키 인증서. 개별 등록을 만들면 디바이스 ID 인증서가 DPS에 업로드됩니다. 프라이빗 키가 IoT Edge 런타임에 전달됩니다.
* 최소한 디바이스 ID와 중간 인증서를 포함해야 하는 전체 체인 인증서. 전체 체인 인증서는 IoT Edge 런타임에 전달됩니다.
* 인증서 신뢰 체인의 중간 또는 루트 CA 인증서. 이 인증서는 그룹 등록을 만들면 DPS에 업로드됩니다.

> [!NOTE]
> 현재 libiothsm의 제한으로 인해 2038년 1월 1일 이후에 만료되는 인증서는 사용할 수 없습니다.

### <a name="use-test-certificates-optional"></a>테스트 인증서 사용(선택 사항)

새 ID 인증서를 만드는 데 사용할 수 있는 인증 기관이 없지만 이 시나리오를 사용해 보려는 경우, Azure IoT Edge Git 리포지토리에 테스트 인증서 생성에 사용할 수 있는 스크립트가 포함되어 있습니다. 해당 인증서는 개발 테스트 전용으로 설계되었으며 프로덕션 단계에서 사용해서는 안 됩니다.

테스트 인증서를 만들려면 [데모 인증서를 만들어 IoT Edge 디바이스 기능 테스트](how-to-create-test-certificates.md)의 단계를 따릅니다. 인증서 생성 스크립트를 설정하고 루트 CA 인증서를 만들기 위한 두 개의 필수 섹션을 완료합니다. 그런 다음 단계를 따라 디바이스 ID 인증서를 만듭니다. 단계를 모두 완료하면 다음 인증서 체인 및 키 쌍이 있어야 합니다.

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

IoT Edge 디바이스에는 해당 인증서가 모두 필요합니다. DPS에서 개별 등록을 사용하려면 .cert.pem 파일을 업로드합니다. DPS에서 그룹 등록을 사용하려면 업로드하려는 동일한 인증서 신뢰 체인에 중간 또는 루트 CA 인증서도 있어야 합니다. 데모 인증서를 사용한다면 그룹 등록에 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 인증서를 사용합니다.

## <a name="create-a-dps-enrollment"></a>DPS 등록 만들기

생성 된 인증서 및 키를 사용 하 여 하나 이상의 IoT Edge 장치에 대해 DPS에서 등록을 만듭니다.

단일 IoT Edge 장치를 프로 비전 하려는 경우 **개별 등록** 을 만듭니다. 여러 장치를 프로 비전 해야 하는 경우 DPS **그룹 등록** 을 만드는 단계를 수행 합니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태** 를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

Device Provisioning Service의 등록에 대한 자세한 내용은 [디바이스 등록을 관리하는 방법](../iot-dps/how-to-manage-enrollments.md) 항목을 참조하세요.

# <a name="individual-enrollment"></a>[개별 등록](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>DPS 개별 등록 만들기

개별 등록은 디바이스의 ID 인증서의 퍼블릭 부분을 가져와 디바이스의 인증서와 일치시킵니다.

> [!TIP]
> 이 문서의 단계는 Azure Portal 위한 것이지만 Azure CLI 사용하여 개별 등록을 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment](/cli/azure/iot/dps/enrollment)를 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동합니다.

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

이제 이 디바이스에 대한 등록이 존재하므로 IoT Edge 런타임은 설치 중에 디바이스를 자동으로 프로비저닝할 수 있습니다.

# <a name="group-enrollment"></a>[그룹 등록](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>DPS 그룹 등록 만들기

그룹 등록은 개별 디바이스 ID 인증서 생성에 사용되는 인증서 신뢰 체인의 중간 또는 루트 CA 인증서를 사용합니다.

#### <a name="verify-your-root-certificate"></a>루트 인증서 확인

등록 그룹을 만들 때 확인된 인증서를 사용할 수 있는 옵션이 존재합니다. 루트 인증서의 소유권이 있음을 증명하여 DPS로 인증서를 확인할 수 있습니다. 자세한 내용은 [X.509 CA 인증서에 대해 소유 증명을 수행하는 방법](../iot-dps/how-to-verify-certificates.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동합니다.

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

Device Provisioning Service의 등록에 대한 자세한 내용은 [디바이스 등록을 관리하는 방법](../iot-dps/how-to-manage-enrollments.md) 항목을 참조하세요.

> [!TIP]
> 이 문서의 단계는 Azure Portal 위한 것이지만 Azure CLI 사용하여 그룹 등록을 만들 수도 있습니다. 자세한 내용은 [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group)을 참조하세요. CLI 명령의 일부로 **에지 사용** 플래그를 사용하여 IoT Edge 장치에 대한 등록을 지정합니다. 그룹 등록의 경우 모든 디바이스가 IoT Edge 디바이스여야 하며, 그렇지 않은 경우 어떤 디바이스도 등록할 수 없습니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동합니다.

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

이제 이러한 디바이스에 대한 등록이 존재하기 때문에 IoT Edge 런타임은 설치하는 동안 디바이스를 자동으로 프로비전할 수 있습니다.

---

다음 섹션으로 진행할 준비가 완료되었습니다.

## <a name="create-a-new-iot-edge-for-linux-on-windows-deployment"></a>Windows 배포 시 Linux용 새 IoT Edge 만들기

대상 디바이스에 Windows의 Linux용 Azure IoT Edge를 배포합니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

아직 설치되지 않은 경우 IoT Edge for Linux on Windows를 대상 디바이스에 설치합니다.

> [!NOTE]
> 다음 PowerShell 프로세스에서는 Windows의 Linux용 IoT Edge를 로컬 디바이스에 배포하는 방법을 간략하게 설명합니다. PowerShell을 사용하여 원격 대상 디바이스에 배포하려면 [원격 PowerShell](/powershell/module/microsoft.powershell.core/about/about_remote)을 사용하여 원격 디바이스에 대한 연결을 설정하고 해당 디바이스에서 이러한 명령을 원격으로 실행합니다.

1. 관리자 권한 PowerShell 세션에서 다음 명령을 각각 실행하여 IoT Edge for Linux on Windows를 다운로드합니다.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. IoT Edge for Linux on Windows를 디바이스에 설치합니다.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   설치 명령에 `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` 및 `VHDXDIR="<FULLY_QUALIFIED_PATH>"` 매개 변수를 추가하여 Windows의 Linux 설치 및 VHDX 디렉터리용 사용자 지정 IoT Edge를 지정할 수 있습니다.

1. 대상 디바이스의 실행 정책이 아직 설정되지 않은 경우 `AllSigned`로 설정합니다. 현재 실행 정책을 확인하고 실행 정책을 로 설정하려면 명령에 대한 PowerShell 필수 구성요약을 `AllSigned` 참조하세요.

1. IoT Edge for Linux on Windows 배포를 만듭니다.

   ```powershell
   Deploy-Eflow
   ```

   >[!TIP]
   >기본적으로 `Deploy-Eflow` 명령은 RAM 1GB, vCPU 코어 1개, 디스크 공간 16GB를 사용하여 Linux 가상 머신을 만듭니다. 그러나 VM에 필요한 리소스는 배포하는 워크로드에 따라 달라집니다. 워크로드를 지원하기에 충분 한 메모리가 없는 VM은 시작되지 않습니다.
   >
   >`Deploy-Eflow` 명령의 선택적 매개 변수를 사용하여 가상 컴퓨터의 사용 가능한 리소스를 사용자 지정할 수 있습니다.
   >
   >예를 들어, 아래 명령은 vCPU 코어 4개, RAM 4GB, 디스크 공간 20GB의 가상 머신을 만듭니다.
   >
   >   ```powershell
   >   Deploy-Eflow -cpuCount 4 -memoryInMB 4096 -vmDiskSize 20
   >   ```
   >
   >사용 가능한 모든 선택적 매개 변수에 대한 자세한 내용은 [Windows의 Linux용 IoT Edge용 PowerShell 함수](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)를 참조하세요.

   배포에 GPU를 할당하여 GPU 가속 Linux 모듈을 사용하도록 설정할 수 있습니다. 이러한 기능에 액세스하려면 [Windows의 Linux용 Azure IoT Edge용 GPU 가속](gpu-acceleration.md)에 설명된 필수 구성 요소를 설치해야 합니다.

   GPU 통과를 사용하려면 `Deploy-Eflow` 명령에 **gpuName**, **gpuPassthroughType** 및 **gpuCount** 매개 변수를 추가해야 합니다. 사용 가능한 모든 선택적 매개 변수에 대한 자세한 내용은 [Windows의 Linux용 IoT Edge용 PowerShell 함수](reference-iot-edge-for-linux-on-windows-functions.md#deploy-eflow)를 참조하세요.

   >[!WARNING]
   >하드웨어 디바이스 통과를 사용하도록 설정하면 보안 위험이 증가할 수 있습니다. Microsoft는 해당되는 경우 GPU 공급업체의 디바이스 완화 드라이버를 권장합니다. 자세한 내용은 [개별 디바이스 할당을 사용하여 그래픽 디바이스 배포](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)를 참조하세요.

1. 'Y'를 입력하여 사용 조건에 동의합니다.

1. 기본 설정에 따라 'O' 또는 'R'을 입력하여 **선택적 진단 데이터** 를 설정하거나 해제합니다.

1. 배포가 완료되면 PowerShell 창에 **배포 성공** 이 보고됩니다.

   ![배포가 성공하면 메시지 끝에 '배포 성공'이 표시됩니다( PNG).](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/successful-powershell-deployment.png)

배포가 완료되면 디바이스를 프로비전할 준비가 되었습니다.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

>[!NOTE]
>Windows Admin Center용 Azure IoT Edge 확장은 현재 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)입니다. 설치 및 관리 프로세스는 일반적으로 사용할 수 있는 기능과 다를 수 있습니다.

Windows Admin Center 시작 페이지의 연결 목록 아래에는 Windows Admin Center를 실행하는 PC를 나타내는 로컬 호스트 연결이 표시됩니다. 관리하는 추가 서버, PC 또는 클러스터도 여기에 표시됩니다.

Windows Admin Center를 사용하여 Azure IoT Edge for Linux on Windows를 로컬 디바이스 또는 원격 관리 디바이스에 설치하고 관리할 수 있습니다. 이 가이드에서 로컬 호스트 연결은 Azure IoT Edge for Linux on Windows를 배포하기 위한 대상 디바이스 역할을 수행합니다.

로컬 디바이스 대신 원격 대상 디바이스에 배포하려고 하고 목록에 원하는 대상 디바이스가 표시되지 않으면 [지침에 따라 디바이스를 추가](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)합니다.

   ![대상 디바이스가 PNG로 나열된 초기 Windows 관리 센터 대시보드](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/windows-admin-center-initial-dashboard.png)

1. **추가** 를 선택합니다.

1. **리소스 추가 또는 만들기** 창에서 **Azure IoT Edge** 타일을 찾습니다. **새로 만들기** 를 선택하여 새 Azure IoT Edge for Linux on Windows 인스턴스를 디바이스에 설치합니다.

   디바이스에서 실행되는 IoT Edge for Linux on Windows가 이미 있는 경우 **추가** 를 선택하여 기존 IoT Edge 디바이스에 연결하고 Windows Admin Center에서 해당 디바이스를 관리할 수 있습니다.

   ![Windows Admin Center, PNG에서 Azure IoT Edge 타일에서 새로 만들기를 선택합니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/resource-creation-tiles.png)

1. **Azure IoT Edge for Linux on Windows 배포 만들기** 창이 열립니다. **1. 시작하기** 탭에서 최소 요구 사항을 검토하고 **다음** 을 선택합니다.

1. 사용 조건을 검토하고, **동의함** 을 선택하고, **다음** 을 선택합니다.

1. 기본 설정에 따라 **선택적 진단 데이터** 를 설정하거나 해제할 수 있습니다.

1. **다음: 배포** 를 선택합니다.

   ![선택적 진단 데이터를 원하는 PNG로 전환한 후 다음: 배포 단추를 선택합니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-next-deploy.png)

1. **2. 배포** 탭의 **대상 디바이스 선택** 아래에서 나열된 디바이스를 클릭하여 최소 요구 사항을 충족하는지 확인합니다. 상태가 '지원됨'으로 확인되면 **다음** 을 선택합니다.

   ![디바이스를 선택하여 PNG가 지원되는지 확인합니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/evaluate-supported-device.png)

1. **2.2 설정** 탭에서 배포의 구성 설정을 검토합니다.

   >[!NOTE]
   >Windows의 Linux용 IoT Edge는 Linux 가상 머신에 내부 IP 주소를 할당하는 기본 스위치를 사용합니다. 이 내부 IP 주소는 Windows 시스템 외부에서 연결할 수 없습니다. Windows 컴퓨터에 로그온한 상태에서 가상 컴퓨터에 로컬로 연결할 수 있습니다.
   >
   >Windows Server를 사용하는 경우 Windows에서 Linux용 IoT Edge를 배포하기 전에 기본 스위치를 설정합니다.

   배포에 GPU를 할당하여 GPU 가속 Linux 모듈을 사용하도록 설정할 수 있습니다. 이러한 기능에 액세스하려면 [Windows의 Linux용 Azure IoT Edge용 GPU 가속](gpu-acceleration.md)에 설명된 필수 구성 요소를 설치해야 합니다. 배포 프로세스의 이 시점에서 이러한 필수 구성 요소만 설치하는 경우 처음부터 다시 시작해야 합니다.

   배포에 할당하는 GPU 어댑터에 따라 사용 가능한 GPU 통과에는 **DDA(직접 디바이스 할당)** 및 **GPU-PV(GPU 반가상화)** 의 두 가지 옵션이 있습니다. 각 방법의 예는 다음과 같습니다.

   직접 디바이스 할당 방법의 경우 Linux 가상 머신에 할당할 GPU 프로세서 수를 선택합니다.

   ![직접 디바이스 할당 GPU를 사용하도록 설정된 구성 설정, PNG.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/gpu-passthrough-direct-device-assignment.png)

   반가상화 방법의 경우 추가 설정이 필요하지 않습니다.

   ![PNG를 사용하는 매개변환 GPU를 사용하는 구성 설정입니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/gpu-passthrough-paravirtualization.png)

   >[!WARNING]
   >하드웨어 디바이스 통과를 사용하도록 설정하면 보안 위험이 증가할 수 있습니다. Microsoft는 해당되는 경우 GPU 공급업체의 디바이스 완화 드라이버를 권장합니다. 자세한 내용은 [개별 디바이스 할당을 사용하여 그래픽 디바이스 배포](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)를 참조하세요.

   설정에 만족하면 **다음** 을 선택합니다.

1. **2.3 배포** 탭에서 배포 상황을 확인할 수 있습니다. 전체 프로세스에는 Azure IoT Edge for Linux on Windows 패키지 다운로드, 패키지 설치, 호스트 디바이스 구성 및 Linux 가상 머신 설정이 포함됩니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다. 성공적인 배포는 다음과 같습니다.

   ![배포가 성공하면 각 단계에 녹색 확인 표시와 '완료' 레이블 PNG가 표시됩니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/successful-deployment.png)

배포가 완료되면 디바이스를 프로비전할 준비가 되었습니다. **다음: 연결** 을 선택하여 Azure IoT Edge 디바이스 프로비전을 처리하는 **3. 연결** 탭으로 이동합니다.

---

## <a name="configure-the-device-with-provisioning-information"></a>프로비저닝 정보를 사용하여 디바이스 구성

런타임이 디바이스에 설치되면 디바이스에서 Device Provisioning Service 및 IoT Hub에 연결하는 데 사용하는 정보로 디바이스를 구성합니다.

다음 정보를 준비합니다.

* DPS **ID 범위** 값. Azure Portal에 있는 DPS 인스턴스의 개요 페이지에서 이 값을 검색할 수 있습니다.
* 디바이스에 있는 디바이스 ID 인증서 체인 파일.
* 디바이스에 있는 디바이스 ID 키 파일.

PowerShell 또는 Windows Admin Center를 사용하여 IoT Edge 디바이스를 프로비저닝할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell의 경우 자체 값으로 업데이트된 자리 표시자 값을 사용하여 다음 명령을 실행합니다.

```powershell
Provision-EflowVm -provisioningType DpsX509 -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -identityCertPath PASTE_ABSOLUTE_PATH_TO_IDENTITY_CERTIFICATE_HERE -identityPrivateKey PASTE_ABSOLUTE_PATH_TO_IDENTITY_PRIVATE_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows Admin Center의 경우 다음 단계를 사용합니다.

1. Azure IoT **Edge 디바이스 프로비저닝 창의 프로비저닝** 방법 드롭다운에서 **X509 DPS(인증서)를** 선택합니다.

1. Windows Admin Center 필드에 DPS 범위 ID, 장치 등록 ID, 등록 기본 키, 파생 키를 제공합니다.

1. **선택한 방법을 통한 프로비전** 을 선택합니다.

   ![대칭 키 프로비전에 필요한 필드 PNG를 입력한 후 선택한 방법으로 프로비저닝을 선택합니다.](./media/how-to-provision-devices-at-scale-linux-on-windows-x509/provisioning-with-selected-method-x509-certs.png)

1. 프로비전이 완료되면 **마침** 을 선택합니다. 기본 대시보드로 돌아갑니다. 이제 유형이 `IoT Edge Devices`인 새 디바이스가 표시됩니다. 연결할 IoT Edge 디바이스를 선택할 수 있습니다. **개요** 페이지에서 디바이스의 **IoT Edge 모듈 목록** 및 **IoT Edge 상태** 가 표시됩니다.

---

## <a name="verify-successful-configuration"></a>성공적인 구성 확인

IoT Edge for Linux on Windows가 IoT Edge 디바이스에 성공적으로 설치되고 구성되었는지 확인합니다.

# <a name="individual-enrollment"></a>[개별 등록](#tab/individual-enrollment)

Device Provisioning Service에서 만든 개별 등록이 사용되었는지 확인할 수 있습니다. Azure Portal에서 Device Provisioning Service 인스턴스로 이동합니다. 만든 개별 등록의 등록 세부 정보를 엽니다. 등록 상태가 **할당됨** 이며 디바이스 ID가 나열된 것을 확인할 수 있습니다.

# <a name="group-enrollment"></a>[그룹 등록](#tab/group-enrollment)

Device Provisioning Service에서 만든 그룹 등록이 사용되었는지 확인할 수 있습니다. Azure Portal에서 Device Provisioning Service 인스턴스로 이동합니다. 만든 그룹 등록에 대한 등록 세부 정보를 엽니다. **등록 레코드** 탭으로 이동하여 해당 그룹에 등록된 모든 디바이스를 봅니다.

---

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. PowerShell 세션에서 다음 명령을 사용하여 IoT Edge for Linux on Windows 가상 머신에 로그인합니다.

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >SSH를 가상 머신에 허용하는 유일한 계정은 가상 머신을 만든 사용자입니다.

1. 로그인하면 다음 Linux 명령을 사용하여 실행되는 IoT Edge 모듈의 목록을 확인할 수 있습니다.

   ```bash
   sudo iotedge list
   ```

1. IoT Edge 서비스 문제를 해결해야 하는 경우 다음 Linux 명령을 사용합니다.

    1. 서비스 문제를 해결해야 할 경우 서비스 로그를 검색합니다.

       ```bash
       sudo journalctl -u iotedge
       ```

    2. `check` 도구를 사용하여 디바이스의 구성 및 연결 상태를 확인합니다.

       ```bash
       sudo iotedge check
       ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Windows Admin Center의 연결된 디바이스 목록에서 IoT Edge 디바이스를 선택하여 연결합니다.

1. 디바이스 개요 페이지에는 디바이스에 대한 몇 가지 정보가 표시됩니다.

   * **IoT Edge 모듈 목록** 섹션에는 디바이스에서 실행되는 모듈이 표시됩니다. IoT Edge 서비스가 처음 시작되면 **edgeAgent** 모듈만 실행되는 것으로 표시됩니다. edgeAgent 모듈은 기본적으로 실행되며, 디바이스에 배포하는 추가 모듈을 설치하고 시작하는 데 도움이 됩니다.

   * **IoT Edge 상태** 섹션에는 서비스 상태가 표시되며 **활성(실행 중)** 이 보고됩니다.

---

새 IoT Edge 디바이스를 만들면 Azure Portal에 상태 코드 `417 -- The device's deployment configuration is not set`이 표시됩니다. 이 상태는 정상이며 디바이스가 모듈 배포를 수신할 준비가 되었음을 의미합니다.

## <a name="next-steps"></a>다음 단계

Device Provisioning Service 등록 프로세스를 사용하면 새 디바이스를 프로비전할 때 디바이스 ID 및 디바이스 쌍 태그를 동시에 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. [Azure CLI를 사용](how-to-deploy-cli-at-scale.md)하거나 [Azure Portal을 사용하여 대규모로 IoT Edge 모듈을 배포하고 모니터링](how-to-deploy-at-scale.md)하는 방법을 알아봅니다.

다음도 가능합니다.

* 디바이스에 모듈을 배포하는 방법을 알아보려면 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md)를 계속 진행하세요.
* [IoT Edge for Linux on Windows 가상 머신에서 인증서를 관리](how-to-manage-device-certificates.md)하고 파일을 호스트 OS에서 Linux 가상 머신으로 전송하는 방법을 알아봅니다.
* [프록시 서버를 통해 통신하도록 IoT Edge 디바이스를 구성](how-to-configure-proxy-support.md)하는 방법을 알아봅니다.
