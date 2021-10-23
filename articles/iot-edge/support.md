---
title: 지원되는 운영 체제, 컨테이너 엔진 - Azure IoT Edge
description: Azure IoT Edge 디먼 및 런타임을 실행할 수 있는 운영 체제와 프로덕션 디바이스의 지원되는 컨테이너 엔진에 대해 알아봅니다.
author: kgremban
ms.author: kgremban
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3c4b8c2578d0443077b29eb350ca53c3176e6dbf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234608"
---
# <a name="azure-iot-edge-supported-systems"></a>Azure IoT Edge 지원 시스템

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

이 문서에서는 공식적으로 또는 미리 보기에서 IoT Edge가 지원하는 시스템 및 구성 요소에 대한 세부 정보를 제공합니다.

## <a name="get-support"></a>지원 받기

Azure IoT Edge 서비스를 사용하는 동안 문제가 발생하는 경우 지원을 검색하는 몇 가지 방법이 있습니다. 지원을 받으려면 다음 채널 중 하나를 시도합니다.

**버그 보고** – Azure IoT Edge 제품에 대한 대다수의 개발은 IoT Edge 오픈 소스 제품에서 이루어집니다. 버그는 프로젝트의 [문제 페이지](https://github.com/azure/iotedge/issues)에서 보고됩니다. Azure IoT Edge for Linux on Windows 관련 버그는 [iotedge-eflow 문제 페이지](https://github.com/azure/iotedge-eflow/issues)에서 보고할 수 있습니다. 프로젝트에서 제품 업데이트로 신속하게 문제를 해결합니다.

**Microsoft 고객 지원팀** – [지원 계획](https://azure.microsoft.com/support/plans/)이 있는 사용자는 [Azure Portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac)에서 직접 지원 티켓을 만들어서 Microsoft 고객 지원팀과 협력할 수 있습니다.

**기능 요청** – Azure IoT Edge 제품은 제품의 [사용자 의견 페이지](https://feedback.azure.com/forums/907045-azure-iot-edge)를 통해 기능 요청을 추적합니다.

## <a name="container-engines"></a>컨테이너 엔진

Azure IoT Edge 모듈은 컨테이너로 구현되므로 모듈을 시작하려면 컨테이너 엔진이 필요합니다. Microsoft에서는 컨테이너 엔진인 Moby 엔진을 제공하여 이러한 요구 사항을 충족합니다. 이 컨테이너 엔진은 Moby 오픈 소스 프로젝트를 기준으로 합니다. Docker CE 및 Docker EE도 널리 사용되는 다른 컨테이너 엔진이며, Moby 오픈 소스 프로젝트를 기반으로 하고 Azure IoT Edge와 호환됩니다. Microsoft에서는 이러한 컨테이너 엔진을 사용하는 시스템에 대해 최고의 지원을 제공하지만 해당 시스템에서 발생한 문제를 수정할 수 없습니다. 이로 인해 Microsoft에서는 프로덕션 시스템에서 Moby 엔진을 사용하는 것이 좋습니다.

<br>
<center>

![컨테이너 런타임으로서의 Moby 엔진](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>운영 체제

Azure IoT Edge는 컨테이너를 실행할 수 있는 대부분의 운영 체제에서 실행되지만 이러한 모든 시스템이 동일하게 지원되지는 않습니다. 운영 체제는 사용자가 사용할 수 있는 지원 수준을 나타내는 계층으로 그룹화됩니다.

* 계층 1 시스템이 지원됩니다. 계층 1 시스템의 경우 Microsoft는 다음을 수행합니다.
  * 자동화된 테스트에서 이 운영 체제를 사용하고
  * 해당 운영 체제에 대한 설치 패키지를 제공합니다.
* 계층 2 시스템은 Azure IoT Edge와 호환되며 상대적으로 쉽게 사용할 수 있습니다. 계층 2 시스템:
  * Microsoft에서는 해당 플랫폼에 대한 비공식적 테스트를 완료하고 해당 플랫폼에서 성공적으로 Azure IoT Edge를 실행하는 파트너를 인식하고 있습니다.
  * 다른 플랫폼에 대한 설치 패키지는 다음 플랫폼에서 작동할 수 있습니다.

호스트 OS 제품군은 항상 모듈의 컨테이너 내부에서 사용되는 게스트 OS 제품군과 일치해야 합니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
즉, Linux에서는 Linux 컨테이너만 사용하고 Windows에서는 Windows 컨테이너만 사용할 수 있습니다. Windows 컨테이너를 사용하는 경우 프로세스 격리 컨테이너만 지원되고 Hyper-V 격리 컨테이너는 지원되지 않습니다.  

IoT Edge for Linux on Windows는 Windows 호스트에서 실행되는 Linux 가상 머신의 IoT Edge를 사용합니다. 이러한 방식으로 Windows 디바이스에서 Linux 모듈을 실행할 수 있습니다.
:::moniker-end
<!-- end 1.1 -->

### <a name="tier-1"></a>계층 1

다음 표에 나열된 시스템은 일반 공급되거나 퍼블릭 미리 보기 방식으로 Microsoft에서 지원되며 각 새 릴리스로 테스트되었습니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Azure IoT Edge는 Linux 또는 Windows 컨테이너로 빌드된 모듈을 지원합니다. Linux 컨테이너는 Linux 디바이스에 배포하거나 IoT Edge for Linux on Windows를 사용하여 Windows 디바이스에 배포할 수 있습니다. Windows 컨테이너는 Windows 디바이스에만 배포할 수 있습니다.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Azure IoT Edge 버전 1.2는 Linux 컨테이너로 빌드된 모듈만 지원합니다.

현재 Windows 디바이스에서 IoT Edge 버전 1.2를 실행하기 위해 지원되는 방법은 없습니다. [IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md)는 Windows 디바이스에서 IoT Edge를 실행하는 권장 방법이지만 현재는 IoT Edge 1.1만 실행합니다. 자세한 내용은 이 문서의 [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) 버전을 참조하세요.

:::moniker-end
<!-- end 1.2 -->

#### <a name="linux-containers"></a>Linux 컨테이너

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Linux 컨테이너로 빌드된 모듈은 Linux 또는 Windows 디바이스에 배포할 수 있습니다. Linux 디바이스의 경우 IoT Edge 런타임은 호스트 디바이스에 직접 설치됩니다. Windows 디바이스의 경우 IoT Edge 런타임으로 미리 빌드된 Linux 가상 머신이 호스트 디바이스에서 실행됩니다.

[Windows에 깔린 Linux를 위한 IoT Edge](iot-edge-for-linux-on-windows.md)는 Windows 디바이스에서 IoT Edge를 실행하는 권장 방법입니다.

| 운영 체제 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/support/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/support/green-check.png) |  | 퍼블릭 미리 보기 |
| Windows 10 Pro | ![Windows 10 Pro + AMD64](./media/support/green-check.png) |  |  |
| Windows 10 Enterprise | ![Windows 10 Enterprise + AMD64](./media/support/green-check.png) |  |  |
| Windows 10 IoT Enterprise | ![Windows 10 IoT Enterprise + AMD64](./media/support/green-check.png) |  |  |
| Windows Server 2019 | ![Windows Server 2019 + AMD64](./media/support/green-check.png) |  |  |

모든 Windows 운영 체제는 버전 1809(빌드 17763) 이상이어야 합니다.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

| 운영 체제 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspberry Pi OS Stretch |  | ![Raspberry Pi OS Stretch + ARM32v7](./media/support/green-check.png) |  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18.04 + AMD64](./media/support/green-check.png) |  | 퍼블릭 미리 보기 |

:::moniker-end
<!-- end 1.2 -->

>[!NOTE]
>Ubuntu Server 16.04 지원은 IoT Edge 버전 1.1 릴리스에서 종료되었습니다.

#### <a name="windows-containers"></a>Windows 컨테이너

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
>[!IMPORTANT]
>IoT Edge 1.1 LTS는 Windows 컨테이너를 지원하는 마지막 릴리스 채널입니다. 버전 1.2부터 Windows 컨테이너가 지원되지 않습니다. Windows 디바이스에서 IoT Edge를 실행하려면 [Windows에서 Linux용 IoT Edge](iot-edge-for-linux-on-windows.md)를 사용하거나 이동하는 것이 좋습니다.

Windows 컨테이너로 빌드된 모듈은 Windows 디바이스에만 배포할 수 있습니다.

| 운영 체제 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![check1](./media/support/green-check.png) |  |  |
| Windows Server 2019  | ![check1](./media/support/green-check.png) |  |  |
| Windows Server IoT 2019 | ![check1](./media/support/green-check.png) |  |  |

모든 Windows 운영 체제는 버전 1809(빌드 17763)여야 합니다. Windows의 특정 빌드는 Windows의 Windows 컨테이너 버전이 호스트 Windows 디바이스의 버전과 정확하게 일치해야 하기 때문에 IoT Edge on Windows에 필요합니다. Windows 컨테이너는 현재 빌드 17763만 사용합니다.

>[!NOTE]
>Windows 10 IoT Core 지원은 IoT Edge 버전 1.1 릴리스에 종료되었습니다.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
IoT Edge 1.1 LTS는 Windows 컨테이너를 지원하는 마지막 릴리스 채널입니다. 버전 1.2부터 Windows 컨테이너가 지원되지 않습니다.

Windows 컨테이너의 지원되는 운영 체제에 관한 자세한 내용은 이 문서의 [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) 버전을 참조하세요.

:::moniker-end
<!-- end 1.2 -->

### <a name="tier-2"></a>계층 2

다음 표에 나열된 시스템은 Azure IoT Edge 호환 는 것으로 간주되지만 Microsoft에서 적극적으로 테스트하거나 유지 관리하지 않습니다.

| 운영 체제 | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS-7](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7) | ![CentOS + AMD64](./media/support/green-check.png) | ![CentOS + ARM32v7](./media/support/green-check.png) | ![CentOS + ARM64](./media/support/green-check.png) |
| [Ubuntu 20.04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/support/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/support/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/support/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/support/green-check.png) | ![Debian 9 + ARM32v7](./media/support/green-check.png) | ![Debian 9 + ARM64](./media/support/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/support/green-check.png) | ![Debian 10 + ARM32v7](./media/support/green-check.png) | ![Debian 10 + ARM64](./media/support/green-check.png) |
| [Debian 11](https://www.debian.org/releases/bullseye/) | ![Debian 11 + AMD64](./media/support/green-check.png) | ![Debian 11 + ARM32v7](./media/support/green-check.png) | ![Debian 11 + ARM64](./media/support/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/support/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/support/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/support/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/support/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/support/green-check.png) |
| [RHEL 7](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7) | ![RHEL 7 + AMD64](./media/support/green-check.png) | ![RHEL 7 + ARM32v7](./media/support/green-check.png) | ![RHEL 7 + ARM64](./media/support/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/support/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/support/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/support/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Wind River 8 + AMD64](./media/support/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/support/green-check.png) | ![Yocto + ARM32v7](./media/support/green-check.png) | ![Yocto + ARM64](./media/support/green-check.png) |
| Raspberry Pi OS Buster |  | ![Raspberry Pi OS Buster + ARM32v7](./media/support/green-check.png) | ![Raspberry Pi OS Buster + ARM64](./media/support/green-check.png) |

<sup>1</sup> [Azure IoT Edge for Linux 설치 또는 제거](how-to-provision-single-device-linux-symmetric.md)의 Ubuntu Server 18.04 설치 단계는 Ubuntu 20.04에서 변경 없이 작동해야 합니다.

## <a name="releases"></a>릴리스

IoT Edge 릴리스 자산과 릴리스 정보는 [azure-iotedge 릴리스](https://github.com/Azure/azure-iotedge/releases) 페이지에서 사용할 수 있습니다. 이 섹션에서는 각 버전의 구성 요소를 보다 쉽게 시각화하는 데 유용한 릴리스 정보에 대한 정보를 반영합니다.

다음 표에서는 1.2.0부터 각 릴리스에 포함된 구성 요소를 나열합니다. 이 표에 나열된 구성 요소는 개별적으로 설치하거나 업데이트할 수 있으며 이전 버전과 호환됩니다.

| Release | aziot-edge | edgeHub<br>edgeAgent | aziot-identity-service |
| ------- | ---------- | -------------------- | ---------------------- |
| **1.2** | 1.2.0<br>1.2.1<br>1.2.2<br>1.2.3 | 1.2.0<br>1.2.1<br>1.2.2<br> 1.2.3  | 1.2.0<br>1.2.1<br>1.2.2<br><br> |

다음 표에서는 1.1 LTS 릴리스까지 각 릴리스에 포함된 구성 요소를 나열합니다. 이 표에 나열된 구성 요소는 개별적으로 설치하거나 업데이트할 수 있으며 이전 버전과 호환됩니다.

IoT Edge 1.1은 첫 번째 LTS (장기 지원) 릴리스 채널입니다. 이 버전은 새로운 기능을 도입하지 않았지만 보안 업데이트 및 재발 방지용 수정 사항을 수신합니다. IoT Edge 1.1 LTS는 .NET Core 3.1을 사용하며, [.Net Core 및 .NET 5 릴리스 수명 주기](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)에 따라 2022년 12월 3일까지 지원됩니다.

| Release | iotedge | edgeHub<br>edgeAgent | libiothsm | moby |
|--|--|--|--|--|
| **1.1 LTS** | 1.1.0<br>1.1.1<br><br>1.1.3<br>1.1.4<br>1.1.5<br>1.1.6 | 1.1.0<br>1.1.1<br>1.1.2<br>1.1.3<br>1.1.4<br>1.1.5<br><br> | 1.1.0<br>1.1.1<br><br>1.1.3<br>1.1.4<br>1.1.5<br>1.1.6 |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 |  |
| **1.0.8** | 1.0.8 | 1.0.8<br>1.0.8.1<br>1.0.8.2<br>1.0.8.3<br>1.0.8.4<br>1.0.8.5 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 3.0.4(ARMv7hl, CentOS)<br>3.0.5 |
| **1.0.6** | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

>[!IMPORTANT]
>장기 지원 채널의 릴리스를 통해 1.0.x를 실행하는 모든 고객은 디바이스를 1.1.x로 업그레이드하여 지속적인 지원을 받는 것이 좋습니다.

IoT Edge는 Microsoft.Azure.Devices.Client SDK를 사용합니다. 자세한 내용은 [Azure IoT C# SDK GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-csharp) 또는 [.NET용 Azure SDK 참조 콘텐츠](/dotnet/api/overview/azure/iot/client)를 참조하세요. 다음 목록에서는 각 릴리스가 테스트되는 클라이언트 SDK 버전을 보여 줍니다.

| IoT Edge 버전 | Microsoft.Azure.Devices.Client SDK 버전 |
|------------------|--------------------------------------------|
| 1.2.0            | 1.33.4-NestedEdge
| 1.1(LTS)        | 1.28.0                                     |
| 1.0.10           | 1.28.0                                     |
| 1.0.9            | 1.21.1                                     |
| 1.0.8            | 1.20.3                                     |
| 1.0.7            | 1.20.1                                     |
| 1.0.6            | 1.17.1                                     |
| 1.0.5            | 1.17.1                                     |

## <a name="virtual-machines"></a>Virtual Machines

가상 머신에서 Azure IoT Edge를 실행할 수 있습니다. 일반적으로 고객이 에지 인텔리전스를 사용하여 기존 인프라를 보강하려는 경우 가상 머신을 IoT Edge 디바이스로 사용하는 것이 일반적입니다. 호스트 VM OS 제품군은 모듈의 컨테이너 내부에서 사용되는 게스트 OS 제품군과 일치해야 합니다. 이 요구 사항은 Azure IoT Edge가 디바이스에서 직접 실행될 때와 동일합니다. Azure IoT Edge는 기본 가상화 기술에 중립적이며, Hyper-V 및 vSphere와 같은 플랫폼을 통해 구동되는 VM에서 작동합니다.

<br>

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

<center>

![VM의 Azure IoT Edge](./media/support/edge-on-vm-with-windows.png)

</center>

::: moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

<center>

![VM의 Azure IoT Edge](./media/support/edge-on-vm.png)

</center>

:::moniker-end

## <a name="minimum-system-requirements"></a>최소 시스템 요구 사항

Azure IoT Edge는 Raspberry Pi3만큼 작은 디바이스부터 서버 등급 하드웨어까지 잘 실행됩니다. 시나리오에 적합한 하드웨어 선택은 실행하려는 워크로드에 따라 다릅니다. 최종 디바이스 결정은 복잡할 수 있습니다. 그러나 기존 노트북이나 데스크톱에서 쉽게 솔루션 프로토타입 생성을 시작할 수 있습니다.

프로토타입 생성 경험은 최종 디바이스 선택에 도움이 됩니다. 고려해야 할 질문은 다음과 같습니다.

* 워크로드에 있는 모듈은 몇 개나 되나요?
* 모듈의 컨테이너는 몇 개의 계층을 공유하나요?
* 모듈을 작성할 때 사용한 언어는 무엇인가요?
* 모듈에서 처리할 데이터의 양은 얼마나 되나요?
* 워크로드를 가속화하기 위해 모듈에 전문화된 하드웨어가 필요한가요?
* 솔루션의 원하는 성능 특성은 무엇인가요?
* 하드웨어 예산은 얼마나 되나요?
