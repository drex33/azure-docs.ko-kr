---
title: Azure IoT Edge for Linux on Windows용 PowerShell 함수 | Microsoft Docs
description: IoT Edge for Linux on Windows 가상 머신을 배포, 프로비전하고 상태를 확인하는 Azure IoT Edge for Linux on Windows PowerShell 함수에 대한 참조 정보입니다.
author: v-tcassi
ms.author: fcabrera
ms.date: 06/18/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 8429ed368e8465c45137e555444a18b8a66769dd
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123271956"
---
# <a name="powershell-functions-for-iot-edge-for-linux-on-windows"></a>IoT Edge for Linux on Windows용 PowerShell 함수

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

IoT Edge for Linux on Windows(EFLOW) 가상 머신을 배포, 프로비저닝하고 상태를 확인하는 PowerShell 함수를 이해합니다.

## <a name="prerequisites"></a>필수 조건

이 문서에 설명된 명령은 `AzureEFLOW.psm1` 파일에서 가져온 것입니다. 이 파일은 `C:\Program Files\WindowsPowerShell\Modules\AzureEFLOW` 아래의 `WindowsPowerShell` 디렉터리에 있는 시스템에서 찾을 수 있습니다.

PowerShell 디렉터리에 **AzureEflow** 폴더가 없는 경우 다음 단계를 사용하여 Azure IoT Edge for Linux on Windows를 다운로드하고 설치합니다. 

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

1. 대상 디바이스의 실행 정책이 아직 설정되지 않은 경우 `AllSigned`로 설정합니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

## <a name="connect-eflowvm"></a>Connect-EflowVM

**Connect-EflowVM** 명령은 SSH를 사용하여 가상 머신에 연결합니다. SSH를 가상 머신에 허용하는 유일한 계정은 가상 머신을 만든 사용자입니다.

이 명령은 호스트 디바이스에서 실행되는 PowerShell 세션에서만 작동합니다. Windows Admin Center 또는 PowerShell ISE를 사용할 때는 작동하지 않습니다.

자세한 내용을 보려면 `Get-Help Connect-EflowVM -full` 명령을 사용하세요.

## <a name="copy-eflowvmfile"></a>Copy-EflowVmFile

**Copy-EflowVmFile** 명령은 SCP를 사용하여 가상 머신 간에 파일을 복사합니다. 선택적 매개 변수를 사용하여 원본 및 대상 파일 경로와 복사 방향을 지정합니다.

사용자 **iotedge-user** 는 모든 원본 디렉터리에 대한 읽기 권한이나 가상 머신의 모든 대상 디렉터리에 대한 쓰기 권한이 있어야 합니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| fromFile | 파일 경로를 나타내는 문자열 | 읽을 파일을 정의합니다. |
| toFile | 파일 경로를 나타내는 문자열 |  기록할 파일을 정의합니다. |
| pushFile | 없음 | 이 플래그는 복사 방향을 나타냅니다. 이 플래그가 있으면 명령은 파일을 가상 머신으로 푸시합니다. 이 플래그가 없으면 명령이 가상 머신에서 파일을 가져옵니다. |

자세한 내용을 보려면 `Get-Help Copy-EflowVMFile -full` 명령을 사용하세요.

## <a name="deploy-eflow"></a>Deploy-Eflow

**Deploy-Eflow** 명령은 기본 배포 방법입니다. 이 배포 명령은 가상 머신을 만들고, 파일을 프로비전하고, IoT Edge 에이전트 모듈을 배포합니다. 다음 매개 변수는 필수는 아니지만 배포 중에 IoT Edge 디바이스를 프로비전하고 생성 중에 가상 머신의 설정을 수정하는 데 사용할 수 있습니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| acceptEula | **예** 또는 **아니요** | EULA를 수락/거부하고 EULA 프롬프트를 바이패스하는 바로 가기입니다. |
| acceptOptionalTelemetry | **예** 또는 **아니요** |  선택적 원격 분석을 수락/거부하고 원격 분석 프롬프트를 바이패스하는 바로 가기입니다. |
| cpuCount | 1과 디바이스의 CPU 코어 사이의 정수 값 |  VM의 CPU 코어 수입니다.<br><br>**기본값**: 1 vCore. |
| memoryInMB | 1024와 디바이스의 최대 여유 메모리 양 사이의 정수 값 |VM에 할당된 메모리입니다.<br><br>**기본값**: 1024MB. |
| vmDiskSize | 8GB에서 256GB 사이 | 동적으로 확장되는 가상 하드 디스크의 최대 디스크 크기입니다.<br><br>**기본값**: 10GB. |
| vswitchName | 가상 스위치의 이름 |  EFLOW VM에 할당된 가상 스위치의 이름입니다. |
| vswitchType | **내부** 또는 **외부** | EFLOW VM에 할당된 가상 스위치의 유형입니다. |
| ip4Address | DHCP 서버 범위의 IPv4 주소 | EFLOW VM의 고정 IPv4 주소입니다. _참고: ICS 기본 스위치에서만 지원됩니다_. |
| ip4PrefixLength | 서브넷의 IPv4 접두사 길이 | IPv4 서브넷 접두사 길이, 고정 IPv4 주소가 지정된 경우에만 유효합니다.  _참고: ICS 기본 스위치에서만 지원됩니다_. |
| ip4GatewayAddress | 서브넷 게이트웨이의 IPv4 주소 | 게이트웨이 IPv4 주소, 고정 IPv4 주소가 지정된 경우에만 유효합니다.  _참고: ICS 기본 스위치에서만 지원됩니다_. |
| gpuName | GPU 디바이스 이름 |  통과에 사용할 GPU 디바이스의 이름입니다. |
| gpuPassthroughType | **DirectDeviceAssignment**, **ParaVirtualization** 또는 없음(CPU만 해당) |  GPU 통과 유형 |
| gpuCount | 1과 디바이스의 GPU 코어 수 사이의 정수 값 | VM의 GPU 디바이스 수입니다. <br><br>**참고**: ParaVirtualization을 사용하는 경우 gpuCount = 1로 설정해야 합니다. |

자세한 내용을 보려면 `Get-Help Deploy-Eflow -full` 명령을 사용하세요.  

## <a name="get-eflowhostconfiguration"></a>Get-EflowHostConfiguration

**Get-EflowHostConfiguration** 명령은 호스트 구성을 반환합니다. 이 명령은 매개 변수를 사용하지 않습니다. 네 가지 속성이 포함된 개체를 반환합니다.

* FreePhysicalMemoryInMB
* NumberOfLogicalProcessors
* DiskInfo
* GpuInfo

자세한 내용을 보려면 `Get-Help Get-EflowHostConfiguration -full` 명령을 사용하세요.


## <a name="get-eflowlogs"></a>Get-EflowLogs

**Get-EflowLogs** 명령은 Windows 배포 및 설치의 Linux용 IoT Edge에서 로그를 수집하고 묶습니다. `.zip` 폴더 형식으로 번들로 묶인 로그를 출력합니다.

자세한 내용을 보려면 `Get-Help Get-EflowLogs -full` 명령을 사용하세요.

## <a name="get-eflowvm"></a>Get-EflowVm

**Get-EflowVm** 명령은 가상 머신의 현재 구성을 반환합니다. 이 명령은 매개 변수를 사용하지 않습니다. 네 가지 속성이 포함된 개체를 반환합니다.

* VmConfiguration
* EdgeRuntimeVersion
* EdgeRuntimeStatus
* SystemStatistics

읽을 수 있는 목록에서 특정 속성을 보려면 속성을 확장한 상태에서 `Get-EflowVM` 명령을 실행합니다. 예를 들면 다음과 같습니다.

```powershell
Get-EflowVM | Select -ExpandProperty VmConfiguration | Format-List
```

자세한 내용을 보려면 `Get-Help Get-EflowVm -full` 명령을 사용하세요.

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

**Get-EflowVmAddr** 명령은 가상 머신의 현재 IP 및 MAC 주소를 쿼리하는 데 사용됩니다. 이 명령은 IP 및 MAC 주소가 시간이 지남에 따라 변경될 수 있다는 사실을 고려하기 위해 존재합니다.

추가 정보를 보려면 `Get-Help Get-EflowVmAddr -full` 명령을 사용하세요.

## <a name="get-eflowvmfeature"></a>Get-EflowVmFeature

**Get-EflowVmFeature** 명령은 Windows 기능의 Linux용 IoT Edge 사용 함 상태를 반환합니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| 기능 | **DpsTpm** | 토글할 기능 이름입니다. |

자세한 내용을 보려면 `Get-Help Get-EflowVmFeature -full` 명령을 사용하세요.

## <a name="get-eflowvmname"></a>Get-EflowVmName

**Get-EflowVmName** 명령은 가상 머신의 현재 호스트 이름을 반환합니다. 이 명령은 Windows 호스트 이름이 시간이 지남에 따라 변경될 수 있다는 사실을 고려하기 위해 존재합니다.

자세한 내용을 보려면 `Get-Help Get-EflowVmName -full` 명령을 사용하세요.

## <a name="get-eflowvmtelemetryoption"></a>Get-EflowVmTelemetryOption

**Get-EflowVmTelemetryOption** 명령은 가상 머신 내부의 원격 분석 상태(**선택사항** 또는 **필수**)를 표시합니다.

자세한 내용을 보려면 `Get-Help Get-EflowVmTelemetryOption -full` 명령을 사용하세요.


## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

**Get-EflowVmTpmProvisioningInfo** 명령은 TPM 프로비저닝 정보를 반환합니다. 이 명령은 매개 변수를 사용하지 않습니다. 두 가지 속성이 포함된 개체를 반환합니다.

* 인증 키
* 등록 ID 

자세한 내용을 보려면 `Get-Help Get-EflowVmTpmProvisioningInfo -full` 명령을 사용하세요.



## <a name="invoke-eflowvmcommand"></a>Invoke-EflowVmCommand

**Invoke-EflowVMCommand** 명령은 가상 머신 내에서 Linux 명령을 실행하고 출력을 반환합니다. 이 명령은 유한 출력을 반환하는 Linux 명령에 대해서만 작동합니다. 사용자 상호 작용이 필요하거나 무기한 실행되는 Linux 명령에는 사용할 수 없습니다.

다음 선택적 매개 변수를 사용하여 사전에 명령을 지정할 수 있습니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| 명령을 사용합니다. | 문자열 | VM에서 실행할 명령입니다. |
| ignoreError | 없음 |  이 플래그가 있으면 명령의 오류를 무시합니다. |

자세한 내용을 보려면 `Get-Help Invoke-EflowVmCommand -full` 명령을 사용하세요.

## <a name="provision-eflowvm"></a>Provision-EflowVm

**Provision-EflowVm** 명령은 IoT Edge 디바이스에 대한 프로비저닝 정보를 가상 머신의 IoT Edge `config.yaml` 파일에 추가합니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| provisioningType | **ManualConnectionString**, **ManualX509**, **DpsTPM**, **DpsX509** 또는 **DpsSymmetricKey** |  IoT Edge 디바이스에 사용하려는 프로비저닝의 유형을 정의합니다. |
| devConnString | 기존 IoT Edge 디바이스의 디바이스 연결 문자열 | 수동으로 IoT Edge 디바이스를 프로비저닝하기 위한 디바이스 연결 문자열입니다(**ManualConnectionString**). |
| iotHubHostname | 기존 IoT Hub의 호스트 이름 | IoT Edge 디바이스 프로비저닝을 위한 Azure IoT Hub 호스트 이름(**ManualX509**) |
| deviceId | 기존 IoT Edge 디바이스의 디바이스 ID | IoT Edge 디바이스 프로비저닝을 위한 디바이스 ID입니다(**ManualX509**). |
| scopeId | 기존 DPS 인스턴스의 범위 ID입니다. | IoT Edge 디바이스 프로비저닝을 위한 범위 ID(**DpsTPM**, **DpsX509** 또는 **DpsSymmetricKey**) |
| symmKey | 기존 DPS 등록의 기본 키 또는 대칭 키를 사용하여 등록된 기존 IoT Edge 디바이스의 기본 키 | IoT Edge 디바이스를 프로비저닝하기 위한 대칭 키입니다(**DpsSymmetricKey**). |
| registrationId | 기존 IoT Edge 디바이스의 등록 ID | IoT Edge 디바이스를 프로비저닝하기 위한 등록 ID입니다(**DpsSymmetricKey**). |
| identityCertPath | 디렉터리 경로 | Windows 호스트 시스템에 있는 ID 인증서의 절대 대상 경로(**ManualX509**, **DpsX509**). |
| identityPrivKeyPath | 디렉터리 경로 | Windows 호스트 시스템에 있는 ID 프라이빗 키의 절대 원본 경로(**ManualX509**, **DpsX509**). |

자세한 내용을 보려면 `Get-Help Provision-EflowVm -full` 명령을 사용하세요.

## <a name="set-eflowvm"></a>Set-EflowVM

**Set-EflowVM** 명령은 요청된 속성으로 가상 머신 구성을 업데이트합니다. 선택적 매개 변수를 사용하여 가상 머신에 대한 특정 구성을 정의합니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| cpuCount | 1과 디바이스의 GPU 코어 사이의 정수 값 | VM의 CPU 코어 수입니다. |
| memoryInMB | 1024와 디바이스의 최대 여유 메모리 양 사이의 정수 값 | VM에 할당된 메모리입니다. |
| gpuName | GPU 디바이스 이름 |  통과에 사용할 GPU 디바이스의 이름입니다. |
| gpuPassthroughType | **DirectDeviceAssignment**, **ParaVirtualization** 또는 없음(통과 없음) |  GPU 통과 유형 |
| gpuCount | 1과 디바이스의 GPU 코어 사이의 정수 값 | VM의 GPU 디바이스 수 **참고**: DirectDeviceAssignment를 사용할 때만 유효합니다. |
| 헤드리스 | 없음 | 이 플래그가 있으면 보안 경고가 발행된 경우 사용자가 확인해야 하는지 여부를 결정합니다. |

자세한 내용을 보려면 `Get-Help Set-EflowVM -full` 명령을 사용하세요.

## <a name="set-eflowvmfeature"></a>Set-EflowVmFeature

**Set-EflowVmFeature** 명령은 Windows 기능의 Linux용 IoT Edge 상태를 사용하거나 사용하지 않도록 설정합니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| 기능 | **DpsTpm** | 토글할 기능 이름입니다. |
| enable | 없음 | 이 플래그가 있으면 명령이 기능을 사용하도록 설정합니다. |

자세한 내용을 보려면 `Get-Help Set-EflowVmFeature -full` 명령을 사용하세요.

## <a name="set-eflowvmtelemetryoption"></a>Set-EflowVmTelemetryOption

**Set-EflowVmTelemetryOption** 명령은 가상 머신 내부의 선택적 원격 분석을 사용하거나 사용하지 않도록 설정합니다.

| 매개 변수 | 허용되는 값 | 의견 |
| --------- | --------------- | -------- |
| optionalTelemetry | **True** 또는 **False** | 선택적 원격 분석이 선택되었는지 여부입니다. |

자세한 내용을 보려면 `Get-Help Set-EflowVmTelemetryOption -full` 명령을 사용하세요.

## <a name="start-eflowvm"></a>Start-EflowVm

**Start-EflowVm** 명령은 가상 머신을 시작합니다. 가상 머신이 이미 시작된 경우 아무 조치도 취하지 않습니다.

자세한 내용을 보려면 `Get-Help Start-EflowVm -full` 명령을 사용하세요.

## <a name="stop-eflowvm"></a>Stop-EflowVm

**Stop-EflowVm** 명령은 가상 머신을 중지합니다. 가상 머신이 이미 중지된 경우 아무 조치도 취하지 않습니다.

자세한 내용을 보려면 `Get-Help Stop-EflowVm -full` 명령을 사용하세요.

## <a name="verify-eflowvm"></a>Verify-EflowVm

**Verify-EflowVm** 명령은 IoT Edge for Linux on Windows 가상 머신이 생성되었는지 확인하는 노출되는 함수입니다. 일반 매개 변수만 사용하며, 가상 머신이 생성되었으면 **True** 를 반환하고, 그렇지 않으면 **False** 를 반환합니다.

자세한 내용을 보려면 `Get-Help Verify-EflowVm -full` 명령을 사용하세요.

## <a name="next-steps"></a>다음 단계

다음 문서에서 이러한 명령을 사용하여 IoT Edge for Linux on Windows를 설치 및 프로비저닝하는 방법을 알아봅니다.

* [Azure IoT Edge for Linux on Windows 설치](./how-to-install-iot-edge-windows-on-windows.md)