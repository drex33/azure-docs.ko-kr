---
title: IoT Edge 장치 만들기-Azure IoT Edge | Microsoft Docs
description: IoT Edge 장치를 만들기 위한 플랫폼 및 프로 비전 옵션에 대해 알아봅니다.
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/11/2021
ms.author: kgremban
ms.openlocfilehash: 6fdfbd937e767cde118ed80476e73d6207c657e6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495047"
---
# <a name="create-an-iot-edge-device"></a>IoT Edge 디바이스 만들기

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

이 문서에서는 장치에 IoT Edge를 설치 및 프로 비전 하는 데 사용할 수 있는 옵션의 개요를 제공 합니다.

이 문서에서는 IoT Edge 솔루션에 대 한 모든 옵션을 확인 하 고 다음과 같은 작업을 돕습니다.

* [플랫폼 선택](#choose-a-platform)
* [장치를 프로 비전 하는 방법 선택](#choose-how-to-provision-your-devices)
* [인증 방법 선택](#choose-an-authentication-method)

이 문서를 마치면 IoT Edge 솔루션에 사용할 플랫폼, 프로 비전 및 인증 옵션을 명확 하 게 파악할 수 있습니다.

## <a name="get-started"></a>시작

IoT Edge 장치를 만드는 데 사용 하려는 플랫폼, 프로 비전 및 인증 옵션의 유형을 알고 있는 경우 다음 표의 링크를 사용 하 여 시작 하세요.

적절 한 옵션을 선택 하는 방법에 대 한 자세한 내용을 보려면이 문서를 계속 진행 하 여 자세히 알아보세요.

<!--1.1-->
:::moniker range="iotedge-2018-06"

|    | Linux 호스트의 linux 컨테이너 | Windows 호스트의 Linux 컨테이너 | Windows 호스트의 컨테이너 Windows |
|--| ----- | ---------------- | ------- |
| **수동 프로 비전 (단일 장치)** | [X.509 인증서](how-to-provision-single-device-linux-x509.md)<br><br>[대칭 키](how-to-provision-single-device-linux-symmetric.md) | [X.509 인증서](how-to-provision-single-device-linux-on-windows-x509.md)<br><br>[대칭 키](how-to-provision-single-device-linux-on-windows-symmetric.md) | [X.509 인증서](how-to-provision-single-device-windows-x509.md)<br><br>[대칭 키](how-to-provision-single-device-windows-symmetric.md) |
| **Autoprovisioning (규모에 맞게 장치)** | [X.509 인증서](how-to-provision-devices-at-scale-linux-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-linux-tpm.md)<br><br>[대칭 키](how-to-provision-devices-at-scale-linux-symmetric.md) | [X.509 인증서](how-to-provision-devices-at-scale-linux-on-windows-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-linux-on-windows-tpm.md)<br><br>[대칭 키](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md) | [X.509 인증서](how-to-provision-devices-at-scale-windows-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-windows-tpm.md)<br><br>[대칭 키](how-to-provision-devices-at-scale-windows-symmetric.md) |

:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>다음 표에서는 IoT Edge 버전 1.2에 대해 지원 되는 시나리오를 반영 합니다. Windows 장치에 대 한 콘텐츠를 보려면이 문서의 [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) 버전으로 전환 하세요.

|    | Linux 호스트의 linux 컨테이너 |
|--| ----- |
| **수동 프로 비전 (단일 장치)** | [X.509 인증서](how-to-provision-single-device-linux-x509.md)<br><br>[대칭 키](how-to-provision-single-device-linux-symmetric.md) |
| **Autoprovisioning (규모에 맞게 장치)** | [X.509 인증서](how-to-provision-devices-at-scale-linux-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-linux-tpm.md)<br><br>[대칭 키](how-to-provision-devices-at-scale-linux-symmetric.md) |

:::moniker-end

## <a name="terms-and-concepts"></a>용어 및 개념

IoT Edge 용어를 아직 잘 모르는 경우 몇 가지 주요 개념을 검토 합니다.

**IoT Edge runtime**: [IoT Edge 런타임은](iot-edge-runtime.md) 장치를 IoT Edge 장치로 전환 하는 프로그램 컬렉션입니다. 전체적으로 IoT Edge 런타임 구성 요소를 통해 IoT Edge 장치에서 IoT Edge 모듈을 실행할 수 있습니다.

**프로 비전**: 각 IoT Edge 장치를 프로 비전 해야 합니다. 프로 비전은 2 단계 프로세스입니다. 첫 번째 단계는 장치에서 허브에 대 한 연결을 설정 하는 데 사용 하는 클라우드 id를 만드는 IoT hub에 장치를 등록 하는 것입니다. 두 번째 단계는 클라우드 id를 사용 하 여 장치를 구성 하는 것입니다. 프로 비전은 장치 단위로 수동으로 수행 하거나 [IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md)를 사용 하 여 규모에 따라 수행할 수 있습니다.

**인증**: IoT Edge 장치는 IoT Hub에 연결할 때 해당 id를 확인 해야 합니다. 대칭 키 암호, 인증서 지문 또는 Tpm (신뢰할 수 있는 플랫폼 모듈)과 같이 사용할 인증 방법을 선택할 수 있습니다.

## <a name="choose-a-platform"></a>플랫폼 선택

플랫폼 옵션은 컨테이너 운영 체제 및 호스트 운영 체제에서 참조 합니다. 컨테이너 운영 체제는 IoT Edge 런타임 및 모듈 컨테이너 내에서 사용 되는 운영 체제입니다. 호스트 운영 체제는 IoT Edge 런타임 컨테이너 및 모듈이 실행 되는 장치의 운영 체제입니다.

IoT Edge 장치에 대 한 세 가지 플랫폼 옵션이 있습니다.

* Linux 호스트 **의 linux 컨테이너**: linux 호스트에서 직접 linux 기반 IoT Edge 컨테이너를 실행 합니다. IoT Edge 문서 전체에서 간단 하 게 하기 위해 **linux** 및 **linux 컨테이너** 라고 하는이 옵션을 볼 수도 있습니다.

* **Windows 호스트의 linux 컨테이너**: Windows 호스트의 linux 가상 머신에서 linux 기반 IoT Edge 컨테이너를 실행 합니다. IoT Edge 문서 전체에서이 옵션은 **Windows에서 linux** 로, **Windows의 linux에 대 한 IoT Edge** 및 **eflow** 라고도 합니다.

* **Windows 호스트의 Windows 컨테이너**: Windows 호스트에서 직접 Windows 기반 IoT Edge 컨테이너를 실행 합니다. IoT Edge 문서 전체에서이 옵션을 **Windows** 하 고 **Windows 컨테이너** 를 간소화 하는 것도 볼 수 있습니다.

현재 프로덕션 시나리오에 대해 지원 되는 운영 체제에 대 한 최신 정보는 [Azure IoT Edge 지원 시스템](support.md#operating-systems)을 참조 하세요.

### <a name="linux-containers-on-linux"></a>Linux의 linux 컨테이너

Linux 디바이스의 경우 IoT Edge 런타임은 호스트 디바이스에 직접 설치됩니다.

IoT Edge은 X64, ARM32 및 ARM64 Linux 장치를 지원 합니다. Microsoft는 Ubuntu Server 18.04 및 Raspberry Pi OS Stretch 운영 체제용 설치 패키지를 제공합니다.

ARM64 디바이스 지원은 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)로 제공됩니다.

### <a name="linux-containers-on-windows"></a>Windows의 Linux 컨테이너

<!--1.1-->
:::moniker range="iotedge-2018-06"
Windows linux의 IoT Edge은 Windows 장치에서 linux 가상 머신을 호스팅합니다. 가상 머신은 IoT Edge 런타임으로 미리 빌드된 상태로 제공 되며 업데이트는 Microsoft 업데이트를 통해 관리 됩니다.

Windows에 깔린 Linux를 위한 IoT Edge는 Windows 디바이스에서 IoT Edge를 실행하는 권장 방법입니다. 자세한 내용은 [Windows에서 Linux 용 Azure IoT Edge 란?](iot-edge-for-linux-on-windows.md)을 참조 하세요.

현재 Windows에서 Linux에 대 한 IoT Edge Azure IoT Edge 버전 1.2을 지원 하지 않습니다.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
현재 IoT Edge 버전 1.2는 Windows에서 Linux 용 IoT Edge를 지원 하지 않습니다. Windows에서 Linux에 대 한 IoT Edge에 대해 자세히 알아보려면이 문서의 [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) 버전을 참조 하세요.
:::moniker-end

### <a name="windows-containers-on-windows"></a>Windows의 컨테이너 Windows

<!--1.1-->
:::moniker range="iotedge-2018-06"
Windows 장치의 경우 IoT Edge 런타임은 호스트 장치에 직접 설치 됩니다. 이 플랫폼을 사용 하 여 IoT Edge 모듈을 Windows 컨테이너로 빌드하고, 배포 하 고, 실행할 수 있습니다.

   > [!NOTE]
   > Windows 컨테이너는 Windows 장치에서 IoT Edge를 실행 하는 데 권장 되는 방법이 아닙니다. Azure IoT Edge 버전 1.1 이상에서는 지원 되지 않습니다.
   >
   > 이후 버전에서 지원 되는 Windows에서 Linux 용 IoT Edge를 사용 하는 것이 좋습니다.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
IoT Edge 버전 1.2은 Windows 컨테이너를 지원 하지 않습니다. Windows 컨테이너는 버전 1.1 이상에서 지원 되지 않습니다. Windows 컨테이너 IoT Edge에 대 한 자세한 내용은이 문서의 [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) 버전을 참조 하세요.
:::moniker-end

## <a name="choose-how-to-provision-your-devices"></a>장치를 프로 비전 하는 방법 선택

IoT Edge 솔루션의 요구 사항에 따라 단일 장치 또는 여러 장치를 대규모로 프로 비전 할 수 있습니다.

IoT Edge 장치와 IoT hub 간의 통신을 인증 하는 데 사용할 수 있는 옵션은 선택한 프로 비전 방법에 따라 달라 집니다. [인증 방법 선택 섹션](#choose-an-authentication-method)에서 이러한 옵션에 대해 자세히 알아볼 수 있습니다.

### <a name="single-device"></a>단일 장치

단일 장치 프로 비전은 DPS ( [IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) )를 지원 하지 않고 IoT Edge 장치를 프로 비전 하는 것을 말합니다. 단일 장치 프로비저닝을 **수동 프로 비전** 이 라고도 합니다.

단일 장치 프로 비전을 사용 하는 경우 장치에서 연결 문자열과 같은 프로 비전 정보를 수동으로 입력 해야 합니다. 수동 프로 비전은 몇 가지 장치에 대해서만 빠르고 쉽게 설정할 수 있지만, 작업은 장치 수로 증가 합니다. 솔루션의 확장성을 고려 하는 경우이 점을 염두에 두어야 합니다.

**대칭 키** 및 **x.509 자체 서명** 된 인증 방법을 수동 프로 비전에 사용할 수 있습니다. [인증 방법 선택 섹션](#choose-an-authentication-method)에서 이러한 옵션에 대해 자세히 알아볼 수 있습니다.

### <a name="devices-at-scale"></a>규모의 장치

규모에 맞게 장치를 프로 비전 하는 것은 [IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md)을 지원 하 여 하나 이상의 IoT Edge 장치를 프로 비전 하는 것을 말합니다. 프로 비전 확장을 **자동 프로 비전** 이 라고도 합니다.

IoT Edge 솔루션에 두 개 이상의 장치가 필요한 경우 DPS를 사용 하 여 자동으로 프로 비전 하면 사용 하려는 각 장치의 구성 파일에 프로 비전 정보를 수동으로 입력 하는 작업을 줄일 수 있습니다. 이 자동화 된 모델은 수백만 개의 IoT Edge 장치로 확장할 수 있습니다. [IOT HUB DPS 개요 페이지의 배경 화면](../iot-dps/about-iot-dps.md#behind-the-scenes)에서 자동화 된 프로 비전 흐름을 볼 수 있습니다.

선택한 인증 방법으로 IoT Edge 솔루션의 보안을 유지할 수 있습니다. **대칭 키**, **X.509 인증서** 및 **TPM (신뢰할 수 있는 플랫폼 모듈) 증명** 인증 방법은 장치를 대규모로 프로 비전 하는 데 사용할 수 있습니다. [인증 방법 선택 섹션](#choose-an-authentication-method)에서 이러한 옵션에 대해 자세히 알아볼 수 있습니다.

DPS의 기능에 대 한 자세한 내용은 [개요 페이지의 기능 섹션](../iot-dps/about-iot-dps.md#features-of-the-device-provisioning-service)을 참조 하세요.

## <a name="choose-an-authentication-method"></a>인증 방법 선택

### <a name="symmetric-keys-attestation"></a>대칭 키 증명

대칭 키 증명은 장치를 인증 하는 간단한 방법입니다. 이 증명 방법은 디바이스 프로비저닝을 처음 사용하는 개발자나 엄격한 보안 요구 사항이 없는 개발자를 위한 "Hello World" 환경을 나타냅니다.

IoT Hub에서 새 장치 id를 만들면 서비스에서 두 개의 키를 만듭니다. 디바이스에 키 중 하나를 저장하고 인증 시 IoT Hub 키를 제시합니다.

이 인증 방법을 이용 하면 안전 하지 않고 시작 하는 것이 더 빠릅니다. TPM 또는 x.509 인증서를 사용 하 여 장치를 프로 비전 하는 것이 더 안전 하며 보안 요구 사항이 더 엄격한 솔루션에 사용 되어야 합니다.

### <a name="x509-certificate-attestation"></a>X.509 인증서 증명

X.509 인증서를 증명 메커니즘으로 사용하면 프로덕션의 크기를 조정하고 디바이스 프로비전을 간소화할 수 있습니다. 일반적으로 X.509 인증서는 신뢰할 수 있는 인증서 체인에 정렬됩니다. 자체 서명되었거나 신뢰할 수 있는 루트 인증서부터 시작하여, 체인에 존재하는 각 인증서는 그다음으로 낮은 인증서에 서명합니다. 이 패턴을 통해 루트 인증서부터 시작하여 각 중간 인증서를 거쳐 디바이스에 설치된 마지막 “리프” 인증서에서 완료되는 위임된 신뢰 체인이 만들어집니다.

두 개의 x.509 id 인증서를 만들어 장치에 저장 합니다. IoT Hub에서 새 디바이스 ID를 만들 때 두 인증서의 지문을 모두 제공합니다. 디바이스는 IoT Hub에 인증 시 인증서 하나를 제시되고 IoT Hub는 인증서가 지문과 일치하는지 확인합니다.

이 인증 방법은 대칭 키 보다 더 안전 하며 프로덕션 시나리오에 권장 됩니다.

### <a name="trusted-platform-module-tpm-attestation"></a>TPM (신뢰할 수 있는 플랫폼 모듈) 증명

TPM 증명을 사용 하는 것은 소프트웨어 및 하드웨어의 인증 기능을 제공 하므로 장치 프로 비전을 위한 가장 안전한 방법입니다. 각 TPM 칩은 고유한 인증 키를 사용 하 여 정품 인증을 확인 합니다.

TPM 증명은 DPS를 사용 하 여 대규모로 프로 비전 하는 경우에만 사용할 수 있으며, 개별 등록 그룹 등록 지원 하지 않습니다. TPM의 장치별 특성 때문에 등록 그룹을 사용할 수 없습니다.

Tpm 2.0는 장치 프로 비전 서비스에 TPM 증명을 사용 하는 경우에 필요 합니다.

이 인증 방법은 대칭 키 보다 더 안전 하며 프로덕션 시나리오에 권장 됩니다.

## <a name="next-steps"></a>다음 단계

목차를 사용 하 여 IoT Edge 솔루션의 플랫폼, 프로 비전 및 인증 요구 사항에 대 한 IoT Edge 장치를 만들기 위한 적절 한 종단 간 가이드로 이동할 수 있습니다.

아래 링크를 사용 하 여 관련 문서로 이동할 수도 있습니다.

### <a name="linux-containers-on-linux-hosts"></a>Linux 호스트의 linux 컨테이너

**단일 장치 수동 프로 비전**:

* [X.509 인증서를 사용 하 여 단일 Linux 장치 프로 비전](how-to-provision-single-device-linux-x509.md)
* [대칭 키를 사용 하 여 단일 Linux 장치 프로 비전](how-to-provision-single-device-linux-symmetric.md)

**여러 장치를 대규모로 프로 비전**:

* [X.509 인증서를 사용 하 여 대규모 Linux 장치 프로 비전](how-to-provision-devices-at-scale-linux-x509.md)
* [TPM 증명을 사용 하 여 규모에 맞게 Linux 장치 프로 비전](how-to-provision-devices-at-scale-linux-tpm.md)
* [대칭 키를 사용 하 여 규모에 맞게 Linux 장치 프로 비전](how-to-provision-devices-at-scale-linux-symmetric.md)

<!--1.1-->
:::moniker range="iotedge-2018-06"
### <a name="linux-containers-on-windows-hosts"></a>Windows 호스트의 Linux 컨테이너

**단일 장치 수동 프로 비전**:

* [x.509 인증서를 사용 하 여 Windows 장치에 단일 Linux 프로 비전](how-to-provision-single-device-linux-on-windows-x509.md)
* [대칭 키를 사용 하 여 Windows 장치에서 단일 Linux 프로 비전](how-to-provision-single-device-linux-on-windows-symmetric.md)

**여러 장치를 대규모로 프로 비전**:

* [x.509 인증서를 사용 하 여 확장 Windows 장치에 Linux 프로 비전](how-to-provision-devices-at-scale-linux-on-windows-x509.md)
* [TPM 증명을 사용 하 여 규모에 따라 Windows 장치에 Linux 프로 비전](how-to-provision-devices-at-scale-linux-on-windows-tpm.md)
* [대칭 키를 사용 하 여 규모에 맞게 Linux Windows 장치 프로 비전](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md)

### <a name="windows-containers-on-windows-hosts"></a>Windows 호스트의 컨테이너 Windows

**단일 장치 수동 프로 비전**:

* [x.509 인증서를 사용 하 여 단일 Windows 장치 프로 비전](how-to-provision-single-device-windows-x509.md)
* [대칭 키를 사용 하 여 단일 Windows 장치 프로 비전](how-to-provision-single-device-windows-symmetric.md)

**여러 장치를 대규모로 프로 비전**:

* [x.509 인증서를 사용 하 여 규모에 맞게 Windows 장치 프로 비전](how-to-provision-devices-at-scale-windows-x509.md)
* [TPM 증명을 사용 하 여 규모에 Windows 장치 프로 비전](how-to-provision-devices-at-scale-windows-tpm.md)
* [대칭 키를 사용 하 여 규모에 Windows 장치 프로 비전](how-to-provision-devices-at-scale-windows-symmetric.md)
:::moniker-end
