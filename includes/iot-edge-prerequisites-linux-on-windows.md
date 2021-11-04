---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3fae7fc37b010a754b7fe1dd9b6e7b092ae534e8
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576856"
---
### <a name="iot-edge-for-linux-on-windows-installation"></a>Windows 설치에서 Linux에 대 한 IoT Edge

다음 최소 요구 사항을 충족 하는 Windows 장치:

* 시스템 요구 사항
   * Windows 10 ¹/11 (Pro, Enterprise, IoT Enterprise)
   * Windows 서버 2019 ¹/2022  
   <sub>¹ Windows 10 및 Windows 현재 누적 업데이트가 모두 설치 된 Server 2019 최소 빌드 17763</sub>

* 하드웨어 요구 사항
  * 사용 가능한 최소 메모리: 1GB
  * 사용 가능한 최소 디스크 공간: 10GB

* 가상화 지원
  * Windows 10에서 Hyper-V를 사용하도록 설정합니다. 자세한 내용은 [Hyper-V를 Windows 10에 설치](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)를 참조하세요.
  * Windows Server에서 Hyper-V 역할을 설치하고 기본 네트워크 스위치를 만듭니다. 자세한 내용은 [Azure IoT Edge for Linux on Windows의 중첩된 가상화](../articles/iot-edge/nested-virtualization.md)를 참조하세요.
  * 가상 머신에서 중첩 가상화를 구성합니다. 자세한 내용은 [중첩된 가상화](../articles/iot-edge/nested-virtualization.md)를 참조하세요.

* 네트워킹 지원
  * Windows Server에는 기본 스위치가 없습니다. EFLOW를 Windows Server 디바이스에 배포하려면 먼저 가상 스위치를 만들어야 합니다.  자세한 내용은 [Windows에서 Linux용 가상 스위치 만들기](../articles/iot-edge/how-to-create-virtual-switch.md)를 참조하세요.
  * Windows 데스크톱 버전에는 EFLOW 설치에 사용할 수 있는 기본 스위치가 있습니다. 필요한 경우 고유한 사용자 지정 가상 스위치를 만들 수 있습니다.

> [!TIP]
> Windows 배포의 Linux용 Azure IoT Edge에서 **GPU 가속 Linux 모듈** 을 사용하려는 경우 고려해야 할 몇 가지 구성 옵션이 있습니다.
>
> GPU 아키텍처에 따라 올바른 드라이버를 설치해야 하며 Windows 참가자 프로그램 빌드에 액세스해야 할 수도 있습니다. 구성 요구 사항을 확인하고 이러한 필수 조건을 충족하려면 [Windows의 Linux용 Azure IoT Edge에 대한 GPU 가속](../articles/iot-edge/gpu-acceleration.md)을 참조하세요.
>
> 이제 GPU 가속에 대 한 사전 요구 사항을 충족 해야 합니다. 설치 하는 동안 GPU 가속을 원하는 경우 설치 프로세스를 다시 시작 해야 합니다.

**PowerShell** 또는 **Windows 관리 센터** 를 사용 하 여 IoT Edge 장치를 관리할 수 있습니다. 각 유틸리티에는 고유한 필수 구성 요소가 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell을 사용 하려는 경우 다음 단계를 사용 하 여 linux 가상 컴퓨터의 배포 및 Windows에 linux 용 Azure IoT Edge 설치를 위한 대상 장치를 준비 합니다.

1. 대상 장치에 대 한 실행 정책을로 설정 `AllSigned` 합니다. 다음 명령을 사용 하 여 관리자 권한 PowerShell 프롬프트에서 현재 실행 정책을 확인할 수 있습니다.

   ```powershell
   Get-ExecutionPolicy -List
   ```

   `local machine`의 실행 정책이 `AllSigned`가 아닌 경우 다음을 사용하여 실행 정책을 설정할 수 있습니다.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

Windows PowerShell 모듈의 Linux에 대 한 Azure IoT Edge에 대 한 자세한 내용은 [PowerShell 함수 참조](../articles/iot-edge/reference-iot-edge-for-linux-on-windows-functions.md)를 참조 하세요.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows 관리 센터를 사용 하려는 경우 다음 단계를 사용 하 여 Windows 관리 센터를 다운로드 및 설치 하 고 Windows 관리 센터 Azure IoT Edge 확장을 설치 합니다.

   1. [Windows Admin Center 설치 관리자](https://aka.ms/wacdownload)를 다운로드 및 실행합니다. 설치 마법사의 프롬프트에 따라 Windows Admin Center를 설치합니다.

   1. 설치되면 지원되는 브라우저를 사용하여 Windows Admin Center를 엽니다. 지원되는 브라우저에는 Microsoft Edge(Windows 10 버전 1709 이상), Google Chrome 및 Microsoft Edge Insider가 있습니다.

   1. Windows Admin Center를 처음 사용하는 경우 사용할 인증서를 선택하라는 메시지가 표시됩니다. 인증서로 **Windows Admin Center 클라이언트** 를 선택합니다.

   1. Azure IoT Edge 확장을 설치합니다. Windows Admin Center 대시보드의 오른쪽 위에서 기어 아이콘을 선택합니다.

      ![대시보드의 오른쪽 위에 있는 기어 아이콘을 선택 하 여 PNG 설정에 액세스 합니다.](../articles/iot-edge/media/how-to-provision-devices-at-scale-linux-on-windows-x509/select-gear-icon.png)

   1. **설정** 메뉴의 **게이트웨이** 아래에서 **확장** 을 선택합니다.

   1. **사용 가능한 확장** 탭의 확장 목록에서 **Azure IoT Edge** 를 찾습니다. 이를 선택하고, 확장 목록 위의 **설치** 프롬프트를 선택합니다.

   1. 설치가 완료되면 **설치된 확장** 탭의 설치된 확장 목록에 Azure IoT Edge가 표시됩니다.

---
