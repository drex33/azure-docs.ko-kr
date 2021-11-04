---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 74dfc438f1d1da5716933184fe1cc446fcb86c41
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505874"
---
## <a name="install-iot-edge"></a>IoT Edge 설치

이 섹션에서는 IoT Edge Windows VM 또는 물리적 디바이스를 준비합니다. 그런 다음, IoT Edge 설치합니다.

Azure IoT Edge는 OCI 호환 컨테이너 런타임을 사용합니다. Moby 기반 엔진인 [Moby가](https://github.com/moby/moby)설치 스크립트에 포함되어 있습니다. 즉, 엔진을 설치하는 추가 단계가 없습니다.

IoT Edge 런타임을 설치하려면 다음을 수행합니다.

1. PowerShell을 관리자 권한으로 실행합니다.

   PowerShell(x86)이 아닌 AMD64 PowerShell 세션을 사용합니다. 사용 중인 세션 형식을 잘 모르는 경우 다음 명령을 실행합니다.

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. 다음 작업을 수행하는 [Deploy-IoTEdge](../articles/iot-edge/reference-windows-scripts.md#deploy-iotedge) 명령을 실행합니다.

   * Windows 머신이 지원되는 버전에 해당하는지 확인합니다.
   * 컨테이너 기능을 사용하도록 설정합니다.
   * Moby 엔진과 IoT Edge 런타임을 다운로드합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. 메시지가 표시되면 디바이스를 다시 시작합니다.

디바이스에 IoT Edge를 설치하는 경우 추가 매개 변수를 사용하여 다음과 같이 프로세스를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 오프라인 설치 시 설치 프로그램이 로컬 디렉터리를 가리키도록 합니다.

추가 매개 변수에 대한 자세한 내용은 [Windows 컨테이너를 포함한 IoT Edge용 PowerShell 스크립트](../articles/iot-edge/reference-windows-scripts.md)를 참조하세요.
