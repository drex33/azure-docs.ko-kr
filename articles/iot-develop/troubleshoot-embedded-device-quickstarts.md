---
title: Azure RTOS 포함된 디바이스 빠른 시작 문제 해결
description: Azure RTOS 포함된 디바이스 빠른 시작을 사용할 때 일반적인 문제를 해결하는 데 도움이 되는 단계
author: JimacoMS4
ms.author: v-jbrannian
ms.service: iot-develop
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.openlocfilehash: 26353064f929c382559216c28651757c7b4ee498
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112124863"
---
# <a name="troubleshooting-the-azure-rtos-embedded-device-quickstarts"></a>Azure RTOS 포함된 디바이스 빠른 시작 문제 해결

[포함된 디바이스 개발 빠른 시작](quickstart-devkit-mxchip-az3166.md)을 수행할 때 몇 가지 일반적인 문제가 발생할 수 있습니다. 일반적으로 문제는 다음 출처에서 발생할 수 있습니다.

* **환경**. 머신, 소프트웨어 또는 네트워크 설정과 연결.
* **Azure IoT 리소스**. Azure IoT에 연결하기 위해 만든 IoT 허브 및 디바이스.
* **디바이스**. 물리적 보드 및 해당 구성.

이 문서에서는 빠른 시작 완료 시 발생할 수 있는 가장 일반적인 문제의 권장 해결 방법을 제공합니다.

## <a name="prerequisites"></a>필수 구성 요소

모든 문제 해결 단계를 수행하려면 작업 중인 빠른 시작에 대해 다음 필수 조건을 완료해야 합니다.

* 빠른 시작에 대한 모든 필수 조건과 소프트웨어 도구를 설치하거나 다운로드했습니다.
* 빠른 시작의 지침에 따라 Azure IoT 허브 또는 Azure IoT Central 애플리케이션을 만들고 디바이스를 등록했습니다.
* 빠른 시작의 지침에 따라 디바이스의 이미지를 빌드했습니다.

## <a name="issue-the-source-directory-doesnt-contain-cmakeliststxt-file"></a>문제: 원본 디렉터리에 CMakeLists.txt 파일이 없음
### <a name="description"></a>설명
이 문제는 프로젝트를 빌드할 때 발생할 수 있습니다. 프로젝트가 GitHub에서 잘못 복제된 결과입니다. 프로젝트에는 **--recursive** 플래그를 사용하지 않는 한 기본적으로 복제되지 않는 여러 하위 모듈이 포함됩니다.

### <a name="resolution"></a>해결 방법
* Git을 사용하여 리포지토리를 복제할 때 **--recursive** 옵션이 있는지 확인합니다.

## <a name="issue-the-build-fails"></a>문제: 빌드 실패

### <a name="description"></a>설명

이 문제는 개체 파일의 경로가 Windows의 기본 최대 경로 길이를 초과하기 때문에 발생할 수 있습니다. 다음과 유사한 메시지에 대한 빌드 출력을 검토합니다.

```output
-- Configuring done
CMake Warning in C:/embedded quickstarts/areallyreallyreallylongpath/getting-started/core/lib/netxduo/addons/azure_iot/azure_iot_security_module/iot-security-module-core/CMakeLists.txt:
  The object file directory

    C:/embedded quickstarts/areallyreallyreallylongpath/getting-started/NXP/MIMXRT1060-EVK/build/lib/netxduo/addons/azure_iot/azure_iot_security_module/iot-security-module-core/CMakeFiles/asc_security_core.dir/./

  has 208 characters.  The maximum full path to an object file is 250
  characters (see CMAKE_OBJECT_PATH_MAX).  Object file

    src/serializer/extensions/custom_builder_allocator.c.obj

  cannot be safely placed under this directory.  The build may not work
  correctly.


-- Generating done
```

### <a name="resolution"></a>해결 방법

다음 옵션 중 하나를 시도하여 이 오류를 해결할 수 있습니다.
* 더 짧은 경로를 사용하여 리포지토리를 디렉터리에 복제하고 다시 시도합니다.
* [최대 경로 길이 제한 사항](/windows/win32/fileio/maximum-file-path-limitation)의 지침에 따라 Windows 10, 버전 1607 이상에서 긴 경로를 사용하도록 설정합니다.

## <a name="issue-device-cant-connect-to-iot-hub"></a>문제: 디바이스가 IoT 허브에 연결할 수 없음

### <a name="description"></a>설명

이 문제는 Azure 리소스를 만들고 디바이스를 플래시한 후에 발생할 수 있습니다. 새로 플래시된 디바이스를 Azure IoT에 연결할 때 다음과 같은 콘솔 메시지가 표시됩니다.

```output
Unable to resolve DNS for MQTT Server
```

### <a name="resolution"></a>해결 방법

* *azure_config.h* 파일에서 IoT 구성에 대해 입력한 구성 값의 철자와 대/소문자를 확인합니다. `deviceID` 및 `primaryKey`와 같은 일부 IoT 리소스 특성의 값은 대/소문자를 구분합니다.

## <a name="issue--wi-fi-is-unable-to-connect"></a>문제: Wi-Fi에 연결할 수 없음

### <a name="description"></a>설명

Wi-Fi 연결을 사용하는 디바이스를 플래시하고 Wi-Fi 네트워크에 연결하려고 시도한 후 Wi-Fi에 연결할 수 없다는 오류 메시지가 표시됩니다.

### <a name="resolution"></a>해결 방법

* Wi-Fi 네트워크 주파수와 설정을 확인합니다. 포함된 디바이스 빠른 시작에 사용되는 디바이스는 모두 2.4GHz를 사용합니다. Wi-Fi 라우터가 2.4GHz 네트워크를 지원하도록 구성되어 있는지 확인합니다.
* Wi-Fi 모드를 확인합니다. *azure_config.h* 파일에서 WIFI_MODE 상수에 사용한 설정을 확인합니다. Wi-Fi 네트워크 보안 또는 인증 설정을 확인하여 Wi-Fi 보안 모드가 구성 파일에 있는 것과 일치하는지 확인합니다.

## <a name="issue-flashing-the-board-fails"></a>문제: 보드 플래시 실패

### <a name="description"></a>설명

디바이스를 플래시하는 프로세스를 완료할 수 없습니다. 다음 증상이 발생하는 경우 이를 알 수 있습니다.

* 빌드한 * *.bin* 이미지 파일이 디바이스에 복사되지 않습니다.
* 디바이스를 플래시하는 데 사용하는 유틸리티가 경고나 오류를 제공합니다.
* 디바이스를 플래시하는 데 사용하는 유틸리티가 프로그래밍이 성공적으로 완료되었음을 나타내지 않습니다.

### <a name="resolution"></a>해결 방법

* 디바이스의 올바른 USB 포트에 연결되었는지 확인합니다. 일부 디바이스에 포트가 두 개 이상 있습니다.
* 다른 마이크로 USB 케이블을 사용해 보세요. 일부 디바이스와 케이블이 호환되지 않습니다.
* 컴퓨터의 다른 USB 포트에 연결해 보세요. USB 포트가 내부적으로 연결이 끊어지거나, 소프트웨어에서 사용하지 않도록 설정되거나, 일시적으로 사용할 수 없는 상태일 수 있습니다.
* 컴퓨터를 다시 시작합니다.

## <a name="issue-device-fails-to-connect-to-port"></a>문제: 디바이스가 포트에 연결하지 못함

### <a name="description"></a>설명

디바이스를 플래시하고 컴퓨터에 연결한 후 터미널 소프트웨어에 다음과 같은 메시지가 표시됩니다.

```output
Failed to initialize the port.
Please verify the COM port settings.
```

### <a name="resolution"></a>해결 방법

* 터미널 소프트웨어에 대한 설정에서 **포트** 설정을 확인하여 올바른 포트가 선택되었는지 확인합니다. 여러 포트가 표시되는 경우 Windows 디바이스 관리자를 열고 **포트** 노드를 선택하여 연결된 디바이스의 올바른 포트를 찾을 수 있습니다.

## <a name="issue-terminal-output-shows-garbled-text"></a>문제: 터미널 출력에 왜곡된 텍스트가 표시됨

### <a name="description"></a>설명

디바이스를 성공적으로 플래시하고 컴퓨터에 연결한 후 터미널 소프트웨어에 왜곡된 텍스트 출력이 표시됩니다.

### <a name="resolution"></a>해결 방법

* 터미널 소프트웨어에 대한 설정에서 **전송 속도** 설정이 *115,200* 인지 확인합니다.

## <a name="issue-terminal-output-shows-no-text"></a>문제: 터미널 출력에 텍스트가 표시되지 않음

### <a name="description"></a>설명

디바이스를 성공적으로 플래시하고 컴퓨터에 연결한 후 터미널 소프트웨어에 출력이 표시되지 않습니다.

### <a name="resolution"></a>해결 방법

* 터미널 소프트웨어의 설정이 빠른 시작의 설정과 일치하는지 확인합니다.
* 터미널 소프트웨어를 다시 시작합니다.
* 디바이스의 **재설정** 단추를 누릅니다.
* USB 케이블이 제대로 연결되어 있는지 확인합니다.

## <a name="issue-communication-between-device-and-iot-hub-fails"></a>문제: 디바이스와 IoT Hub 간 통신이 실패함

### <a name="description"></a>설명

디바이스를 플래시하고 컴퓨터에 연결한 후 터미널 창에 다음과 같은 반복된 메시지가 표시됩니다.

```output
Failed to publish temperature
```

### <a name="resolution"></a>해결 방법

* ‘가격 책정 및 스케일링 계층’이 ‘무료’ 또는 ‘표준’ 중 하나인지 확인합니다.   **기본** 은 클라우드-디바이스 간 및 디바이스 쌍 통신을 지원하지 않으므로 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 문제를 검토한 후에도 터미널에서 디바이스를 모니터링하거나 Azure IoT에 연결할 수 없는 경우에는 디바이스의 하드웨어나 물리적 구성에 문제가 있을 수 있습니다. 설명서와 지원 옵션을 찾으려면 디바이스의 제조업체 페이지를 참조하세요.

* [STMicroelectronics B-L475E-IOT01](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-mpu-eval-tools/stm32-mcu-mpu-eval-tools/stm32-discovery-kits/b-l475e-iot01a.html)
* [NXP MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK)
* [Microchip ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro)