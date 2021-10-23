---
title: Windows 컨테이너를 사용하는 Azure IoT Edge용 스크립트 | Microsoft Docs
description: Windows 디바이스에서 설치, 제거 또는 업데이트할 IoT Edge PowerShell 스크립트의 참조 정보
author: kgremban
ms.author: kgremban
ms.date: 10/06/2020
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: bd450569298dfe8782f1a37b6c565daff0ae6175
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259170"
---
# <a name="powershell-scripts-for-iot-edge-with-windows-containers"></a>Windows 컨테이너를 사용하는 IoT Edge용 PowerShell 스크립트

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Windows 디바이스에서 IoT Edge를 설치, 업데이트 또는 제거하는 PowerShell 스크립트를 이해합니다.

이 문서에 설명된 명령은 모든 [IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)와 함께 릴리스되는 `IoTEdgeSecurityDaemon.ps1` 파일에서 가져옵니다. 최신 버전의 스크립트는 항상 aka.ms/iotedge-win에서 사용할 수 있습니다.

`Invoke-WebRequest` cmdlet을 사용하여 명령을 실행하면 최신 스크립트 버전에 액세스할 수 있습니다. 예:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge
```

이 스크립트 또는 특정 릴리스의 스크립트 버전을 다운로드하여 명령을 실행할 수도 있습니다. 예:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Deploy-IoTEdge
```

제공된 스크립트는 보안을 강화하기 위해 서명되어 있습니다. 디바이스에 스크립트를 다운로드하고 다음 PowerShell 명령을 실행하여 서명을 확인할 수 있습니다.

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

서명이 확인되면 출력 상태는 **유효** 입니다.

## <a name="deploy-iotedge"></a>Deploy-IoTEdge

Deploy-IoTEdge 명령은 IoT Edge 보안 디먼 및 해당 종속성을 다운로드하고 배포합니다. 배포 명령은 이러한 공통 매개 변수를 허용합니다. 전체 목록을 보려면 `Get-Help Deploy-IoTEdge -full` 명령을 사용합니다.  

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 또는 **Linux** | 컨테이너 운영 체제를 지정하지 않을 경우 Windows가 기본값입니다.<br><br>Windows 컨테이너의 경우 IoT Edge는 설치에 포함된 Moby 컨테이너 엔진을 사용합니다. Linux 컨테이너의 경우 설치를 시작하기 전에 컨테이너 엔진을 설치해야 합니다. |
| **프록시** | 프록시 URL | 디바이스가 프록시 서버를 통해 인터넷에 연결해야 하는 경우 이 매개 변수를 포함합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **OfflineInstallationPath** | 디렉터리 경로 | 이 매개 변수가 포함된 경우 설치 프로그램은 나열된 디렉터리에서 설치에 필요한 IoT Edge cab 및 VC 런타임 MSI 파일을 확인합니다. 디렉터리에서 찾을 수 없는 모든 파일이 다운로드됩니다. 두 파일이 모두 디렉터리에 있으면 인터넷에 연결하지 않고 IoT Edge를 설치할 수 있습니다. 이 매개 변수를 사용하여 특정 버전을 사용할 수도 있습니다. |
| **InvokeWebRequestParameters** | 매개 변수 및 값의 해시 테이블입니다. | 설치 중에 여러 개의 웹 요청이 생성됩니다. 이 필드를 사용하여 해당 웹 요청에 대한 매개 변수를 설정합니다. 이 매개 변수는 프록시 서버에 대한 자격 증명을 구성하는 데 유용합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **RestartIfNeeded** | 없음 | 이 플래그를 사용하면 필요한 경우 프롬프트 없이도 배포 스크립트가 머신을 다시 시작할 수 있습니다. |

## <a name="initialize-iotedge"></a>Initialize-IoTEdge

Initialize-IoTEdge 명령은 디바이스 연결 문자열 및 작업 세부 정보를 사용하여 IoT Edge를 구성합니다. 이 명령에서 생성된 대부분의 정보는 iotedge\config.yaml 파일에 저장됩니다. 초기화 명령은 무엇보다 이러한 공통 매개 변수를 허용합니다. 전체 목록을 보려면 `Get-Help Initialize-IoTEdge -full` 명령을 사용합니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| **ManualConnectionString** | 없음 | **매개 변수를 전환** 합니다. **기본값**. 프로비저닝 유형을 지정하지 않으면 연결 문자열을 사용하는 수동 프로비저닝이 기본값입니다.<br><br>디바이스 연결 문자열을 제공하여 디바이스를 수동으로 프로비저닝하도록 선언합니다. |
| **ManualX509** | 없음 | **매개 변수를 전환** 합니다. 프로비저닝 유형을 지정하지 않으면 연결 문자열을 사용하는 수동 프로비저닝이 기본값입니다.<br><br>ID 인증서와 프라이빗 키를 제공하여 디바이스를 수동으로 프로비저닝하도록 선언합니다.
| **DpsTpm** | 없음 | **매개 변수를 전환** 합니다. 프로비저닝 유형을 지정하지 않으면 연결 문자열을 사용하는 수동 프로비저닝이 기본값입니다.<br><br>DPS(Device Provisioning Service) 범위 ID 및 디바이스의 등록 ID를 제공하여 DPS를 통해 프로비전할 것을 선언합니다.  |
| **DpsSymmetricKey** | 없음 | **매개 변수를 전환** 합니다. 프로비저닝 유형을 지정하지 않으면 연결 문자열을 사용하는 수동 프로비저닝이 기본값입니다.<br><br>DPS(Device Provisioning Service) 범위 ID 및 디바이스의 등록 ID를 제공하여 증명을 위해 대칭 키와 함께 DPS를 통해 프로비저닝하도록 선언합니다. |
| **DpsX509** | 없음 | **매개 변수를 전환** 합니다. 프로비저닝 유형을 지정하지 않으면 연결 문자열을 사용하는 수동 프로비저닝이 기본값입니다.<br><br>DPS(Device Provisioning Service) 범위 ID와 디바이스의 등록 ID를 제공하여 증명을 위해 X.509 ID 인증서 및 프라이빗 키와 함께 DPS를 통해 프로비저닝하도록 선언합니다.  |
| **DeviceConnectionString** | IoT Hub에 등록된 IoT Edge 디바이스의 연결 문자열을 작은따옴표 안에 표시합니다. | 연결 문자열을 통해 수동으로 프로비저닝하는 데 **필요** 합니다. 스크립트 매개 변수에 연결 문자열을 제공하지 않을 경우 확인 메시지가 표시됩니다. |
| **IotHubHostName** | 디바이스가 연결하는 IoT 허브의 호스트 이름입니다. | X.509 인증서를 사용하여 수동으로 프로비저닝하는 데 **필요** 합니다. *{hub name}.azure-devices.net* 형식을 사용합니다. |
| **DeviceId** | IoT Hub에 등록된 디바이스 ID의 디바이스 ID입니다. | X.509 인증서를 사용하여 수동으로 프로비저닝하는 데 **필요** 합니다. |
| **ScopeId** | IoT Hub와 연결된 Device Provisioning Service 인스턴스의 범위 ID입니다. | DPS 프로비저닝에 **필요** 합니다. 스크립트 매개 변수에 범위 ID를 제공하지 않을 경우 확인 메시지가 표시됩니다. |
| **RegistrationId** | 디바이스에서 생성된 등록 ID입니다. | TPM 또는 대칭 키 증명을 사용하는 경우 DPS 프로비저닝에 **필요** 합니다. X.509 인증서 증명을 사용하는 경우 **선택 사항** 입니다. |
| **X509IdentityCertificate** | 디바이스에서 X.509 디바이스 ID 인증서의 URI 경로입니다. | X.509 인증서 증명을 사용하는 경우 수동 또는 DPS 프로비저닝에 **필요** 합니다. |
| **X509IdentityPrivateKey** | 디바이스에서 X.509 디바이스 ID 인증서 키의 URI 경로입니다. | X.509 인증서 증명을 사용하는 경우 수동 또는 DPS 프로비저닝에 **필요** 합니다. |
| **SymmetricKey** | DPS를 사용할 때 IoT Edge 디바이스 ID를 프로비저닝하는 데 사용되는 대칭 키 | 대칭 키 증명을 사용하는 경우 DPS 프로비저닝에 **필요** 합니다. |
| **ContainerOs** | **Windows** 또는 **Linux** | 컨테이너 운영 체제를 지정하지 않을 경우 Windows가 기본값입니다.<br><br>Windows 컨테이너의 경우 IoT Edge는 설치에 포함된 Moby 컨테이너 엔진을 사용합니다. Linux 컨테이너의 경우 설치를 시작하기 전에 컨테이너 엔진을 설치해야 합니다. |
| **DeviceCACertificate** | 디바이스에서 X.509 디바이스 CA 인증서의 URI 경로입니다. | `C:\ProgramData\iotedge\config.yaml` 파일에서 구성할 수도 있습니다. 자세한 내용은 [IoT Edge 디바이스에서 인증서 관리](how-to-manage-device-certificates.md)를 참조하세요. |
| **DeviceCAPrivateKey** | 디바이스에서 X.509 디바이스 CA 프라이빗 키의 URI 경로입니다. | `C:\ProgramData\iotedge\config.yaml` 파일에서 구성할 수도 있습니다. 자세한 내용은 [IoT Edge 디바이스에서 인증서 관리](how-to-manage-device-certificates.md)를 참조하세요. |
| **InvokeWebRequestParameters** | 매개 변수 및 값의 해시 테이블입니다. | 설치 중에 여러 개의 웹 요청이 생성됩니다. 이 필드를 사용하여 해당 웹 요청에 대한 매개 변수를 설정합니다. 이 매개 변수는 프록시 서버에 대한 자격 증명을 구성하는 데 유용합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **AgentImage** | IoT Edge 에이전트 이미지 URI | 기본적으로 새 IoT Edge 설치는 IoT Edge 에이전트 이미지에 대한 최신 롤링 태그를 사용합니다. 이 매개 변수를 사용하여 이미지 버전에 대한 특정 태그를 설정하거나 사용자 고유의 에이전트 이미지를 제공합니다. 자세한 내용은 [IoT Edge 태그 이해](how-to-update-iot-edge.md#understand-iot-edge-tags)를 참조하세요. |
| **사용자 이름** | 컨테이너 레지스트리 사용자 이름입니다. | -AgentImage 매개 변수를 프라이빗 레지스트리의 컨테이너로 설정한 경우에만 이 매개 변수를 사용합니다. 레지스트리에 대한 액세스 권한이 있는 사용자 이름을 제공합니다. |
| **암호** | 보안 암호 문자열입니다. | -AgentImage 매개 변수를 프라이빗 레지스트리의 컨테이너로 설정한 경우에만 이 매개 변수를 사용합니다. 레지스트리에 액세스하기 위한 암호를 제공합니다. |

## <a name="update-iotedge"></a>Update-IoTEdge

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 또는 **Linux** | 컨테이너 OS를 지정하지 않을 경우 Windows가 기본값입니다. Windows 컨테이너의 경우 컨테이너 엔진이 설치에 포함됩니다. Linux 컨테이너의 경우 설치를 시작하기 전에 컨테이너 엔진을 설치해야 합니다. |
| **프록시** | 프록시 URL | 디바이스가 프록시 서버를 통해 인터넷에 연결해야 하는 경우 이 매개 변수를 포함합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **InvokeWebRequestParameters** | 매개 변수 및 값의 해시 테이블입니다. | 설치 중에 여러 개의 웹 요청이 생성됩니다. 이 필드를 사용하여 해당 웹 요청에 대한 매개 변수를 설정합니다. 이 매개 변수는 프록시 서버에 대한 자격 증명을 구성하는 데 유용합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **OfflineInstallationPath** | 디렉터리 경로 | 이 매개 변수가 포함된 경우 설치 프로그램은 나열된 디렉터리에서 설치에 필요한 IoT Edge cab 및 VC 런타임 MSI 파일을 확인합니다. 디렉터리에서 찾을 수 없는 모든 파일이 다운로드됩니다. 두 파일이 모두 디렉터리에 있으면 인터넷에 연결하지 않고 IoT Edge를 설치할 수 있습니다. 이 매개 변수를 사용하여 특정 버전을 사용할 수도 있습니다. |
| **RestartIfNeeded** | 없음 | 이 플래그를 사용하면 필요한 경우 프롬프트 없이도 배포 스크립트가 머신을 다시 시작할 수 있습니다. |

## <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| **Force** | 없음 | 이 플래그는 이전 제거 시도가 실패한 경우 강제로 제거합니다.
| **RestartIfNeeded** | 없음 | 이 플래그를 사용하면 필요한 경우 프롬프트 없이도 제거 스크립트가 머신을 다시 시작할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

다음 문서에서 이러한 명령을 사용하는 방법을 알아봅니다.

* 대칭 키를 사용 하거나 [x.509 인증서를 사용](how-to-provision-single-device-windows-x509.md) [하 여 Windows에 대 한 Azure IoT Edge 설치 또는 제거](how-to-provision-single-device-windows-symmetric.md)
