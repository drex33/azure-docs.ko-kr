---
title: IoT Central를 사용 하 여 Windows (eflow)에서 Linux에 대 한 Azure IoT Edge Microsoft Docs
description: IoT Central를 사용 하 여 Linux 용 Azure IoT Edge를 Windows (eflow)에 연결 하는 방법을 알아봅니다.
author: v-krishnag
ms.author: v-krishnag
ms.date: 11/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c954bc1f281acc47c0bd7490790f3d65abe0a05f
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218962"
---
# <a name="azure-iot-edge-for-linux-on-windows-eflow-with-iot-central"></a>IoT Central를 사용 하 여 Windows (eflow)에서 Linux에 대 한 Azure IoT Edge

[eflow (Windows)에 대 한 Azure IoT edge](https://docs.microsoft.com/windows/iot/iot-enterprise/azure-iot-edge-for-linux-on-windows) 를 사용 하면 Windows 장치의 linux 컨테이너에서 Azure IoT edge를 실행할 수 있습니다. 이 문서에서는 EFLOW 장치를 프로 비전 하 고 IoT Central 응용 프로그램에서 관리 하는 방법에 대해 알아봅니다.

이 방법 문서에서는 다음 방법에 대해 알아봅니다.

* IoT Edge 장치에 대 한 장치 템플릿 만들기
* IoT Central에서 IoT Edge 장치를 만듭니다.
* EFLOW 장치를 커넥트 하 고 프로 비전 합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음이 필요합니다.

* 활성화된 Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* **사용자 지정 애플리케이션** 템플릿에서 [생성된 IoT Central 애플리케이션](howto-create-iot-central-application.md). 자세한 내용은 [IoT Central 애플리케이션 만들기](howto-create-iot-central-application.md)를 참조하세요.

* 다음 최소 요구 사항을 충족 하는 Windows 장치:

  * Windows 10<sup>1</sup>/11 (Pro, Enterprise, IoT Enterprise) 또는 Windows 서버 2019<sup>1</sup>/2022
  * 사용 가능한 최소 메모리: 1gb
  * 사용 가능한 최소 디스크 공간: 10gb
  * <sup>1</sup> Windows 10 Windows 현재 누적 업데이트가 모두 설치 된 Server 2019 최소 빌드 17763

이 문서의 단계를 수행 하려면 [EnvironmentalSensorManifest](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json) 파일을 컴퓨터에 다운로드 합니다.

## <a name="add-device-template"></a>장치 템플릿 추가

이 섹션에서는 IoT Edge 디바이스에 대한 IoT Central 디바이스 템플릿을 만듭니다. IoT Edge 매니페스트를 가져와서 시작한 다음, 원격 분석 정의 및 보기를 추가하도록 템플릿을 수정합니다.

### <a name="create-the-device-template-and-import-the-manifest"></a>장치 템플릿 만들기 및 매니페스트 가져오기

1. 디바이스 템플릿을 만들고 템플릿 유형으로 **Azure IoT Edge** 를 선택합니다.

1. 마법사의 **사용자 지정** 페이지에서 장치 템플릿에 대 한 *환경 센서에 지 장치* 와 같은 이름을 입력 합니다.

1. **찾아보기** 를 선택 하 고 이전에 다운로드 한 *EnvironmentalSensorManifest* 매니페스트 파일을 업로드 합니다.

1. **검토** 페이지에서 **만들기** 를 선택합니다.

1. **SimulatedTemperatureSensor** 모듈에서 **관리** 인터페이스를 선택 하 여 매니페스트에 정의 된 두 가지 속성을 봅니다.

:::image type="content" source="media/howto-connect-eflow/imported-manifest.png" alt-text="IoT Edge 매니페스트에서 만든 장치 템플릿입니다.":::

### <a name="add-telemetry-to-the-device-template"></a>장치 템플릿에 원격 분석 추가

IoT Edge 매니페스트는 모듈에서 보내는 원격 분석을 정의하지 않습니다. 원격 분석 정의를 IoT Central의 디바이스 템플릿에 추가합니다. **SimulatedTemperatureSensor** 모듈은 다음 JSON과 비슷한 원격 분석 메시지를 보냅니다.

```json
{
  "machine": {
    "temperature": 75.0,
    "pressure": 40.2
  },
  "ambient": {
    "temperature": 23.0,
    "humidity": 30.0
  },
  "timeCreated": ""
}
```

원격 분석 정의를 디바이스 템플릿에 추가하려면 다음을 수행합니다.

1. **환경 센서에 지 장치** 템플릿에서 **관리** 인터페이스를 선택 합니다.

1. **+ 기능 추가** 를 선택합니다. **표시 이름** 으로 *컴퓨터* 를 입력 하 고 **기능 유형을** **원격 분석** 으로 선택 합니다.

1. 스키마 유형으로 **Object** 를 선택한 다음, **정의** 를 선택합니다. 개체 정의 페이지에서 *온도* 및 *압력* 을 **Double** 형식의 특성으로 추가한 다음, **적용** 을 선택합니다.

1. **+ 기능 추가** 를 선택합니다. **표시 이름** 으로 *앰비언트* 를 입력 하 고 **기능 유형을** **원격 분석** 으로 선택 합니다.

1. 스키마 유형으로 **Object** 를 선택한 다음, **정의** 를 선택합니다. 개체 정의 페이지에서 *온도* 및 *습도* 를 **Double** 형식의 특성으로 추가한 다음, **적용** 을 선택합니다.

1. **+ 기능 추가** 를 선택합니다. **표시 이름** 으로 *timeCreated* 를 입력하고, **기능 유형** 이 **원격 분석** 인지 확인합니다.

1. 스키마 유형으로 **DateTime** 을 선택합니다.

1. **저장** 을 선택하여 템플릿을 업데이트합니다.

이제 **관리** 인터페이스에는 **machine**, **앰비언트** 및 **timeCreated** 원격 분석 유형이 포함 됩니다.

:::image type="content" source="media/howto-connect-eflow/manage-interface.png" alt-text="컴퓨터 및 주변 원격 분석 형식이 포함 된 인터페이스입니다."::: 

### <a name="add-views-to-template"></a>템플릿에 보기 추가

운영자가 장치에서 원격 분석을 볼 수 있도록 하려면 장치 템플릿에서 보기를 정의 합니다.

1. **환경 센서 에지 디바이스** 템플릿에서 **보기** 를 선택합니다.

1. **새 보기를 추가하려면 선택** 페이지에서 **디바이스를 시각화하는 중** 타일을 선택합니다.

1. 보기 이름을 *IoT Edge 디바이스 원격 분석 보기* 로 변경합니다.

1. **장치 시작** 에서 **주변/온도**, **주변/습도**, **컴퓨터/습도** 및 **컴퓨터/온도** 원격 분석 유형을 선택 합니다. 그런 다음, **타일 추가** 를 선택합니다.

1. **저장** 을 선택하여 **IoT Edge 디바이스 원격 분석 보기** 보기를 저장합니다.

:::image type="content" source="media/howto-connect-eflow/template-telemetry-view.png" alt-text="원격 분석 보기를 사용 하는 장치 템플릿."::: 

### <a name="publish-the-template"></a>템플릿 게시

**환경 센서 에지 디바이스** 템플릿을 사용하는 디바이스를 추가하려면 먼저 해당 템플릿을 게시해야 합니다.

**환경 센서 에지 디바이스** 템플릿으로 이동하고, **게시** 를 선택합니다. **이 디바이스 템플릿을 애플리케이션에 게시** 패널에서 **게시** 를 선택하여 템플릿을 게시합니다.

:::image type="content" source="media/howto-connect-eflow/publish-template.png" alt-text="장치 템플릿을 게시 합니다."::: 

## <a name="add-an-iot-edge-device"></a>IoT Edge 디바이스 추가

장치를 IoT Central에 연결 하려면 먼저 응용 프로그램에 장치를 등록 해야 합니다.

1. IoT Central 애플리케이션에서 **디바이스** 페이지로 이동하고, 사용 가능한 템플릿 목록에서 **환경 센서 에지 디바이스** 를 선택합니다.

1. **+ 새로 만들기** 를 선택하여 템플릿에서 새 디바이스를 추가합니다. **새 디바이스 만들기** 페이지에서 **만들기** 를 선택합니다.

이제 **등록됨** 상태의 새 디바이스가 있습니다.

:::image type="content" source="media/howto-connect-eflow/new-device.png" alt-text="새 장치."::: 

### <a name="get-the-device-credentials"></a>디바이스 자격 증명 가져오기

이 방법 문서 뒷부분에 IoT Edge 장치를 배포 하는 경우 장치가 IoT Central 응용 프로그램에 연결할 수 있도록 하는 자격 증명이 필요 합니다. 장치 자격 증명을 가져오려면 다음을 수행 합니다.

1. **디바이스** 페이지에서 만든 디바이스를 선택합니다.

1. **연결** 을 선택합니다.

1. **디바이스 연결** 페이지에서 **ID 범위**, **디바이스 ID** 및 **기본 키** 를 적어 둡니다. 이러한 값은 나중에 사용합니다.

1. **닫기** 를 선택합니다.

이제 IoT Edge 디바이스를 연결할 수 있도록 IoT Central 애플리케이션을 구성하는 작업이 완료되었습니다.

## <a name="install-and-provision-an-eflow-device"></a>EFLOW 장치 설치 및 프로 비전

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

1. IoT Edge for Linux on Windows 배포를 만듭니다. 배포는 Linux VM을 만들고 IoT Edge 런타임을 설치 합니다.

   ```powershell
   Deploy-Eflow
   ```

1. 이전에 기록해 둔 **id 범위**, **장치 Id** 및 **기본 키** 를 사용 합니다.

 ```powershell
   Provision-EflowVm -provisioningType DpsSymmetricKey -scopeId <ID_SCOPE_HERE> -registrationId <DEVCIE_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

eflow 장치를 배포 하 고 프로 비전 할 수 있는 다른 방법에 대 한 자세한 내용은 [Windows 장치에서 Linux 용 Azure IoT Edge 설치 및 프로 비전](../../iot-edge/how-to-install-iot-edge-on-windows.md)을 참조 하세요.

IoT Central 응용 프로그램에서 **장치 세부 정보** 페이지로 이동 하 여 eflow 장치에서 흐르는 원격 분석을 볼 수 있습니다.

:::image type="content" source="media/howto-connect-eflow/telemetry.png" alt-text="장치에서 원격 분석.":::

## <a name="next-steps"></a>다음 단계

이제 IoT Central를 사용 하 여 eflow (Windows)에 대 한 Azure IoT Edge를 연결 하는 방법을 배웠으므로 제안 된 다음 단계는 [IoT Edge 투명 게이트웨이를 통해 장치를 커넥트](how-to-connect-iot-edge-transparent-gateway.md)하는 방법을 알아보는 것입니다.