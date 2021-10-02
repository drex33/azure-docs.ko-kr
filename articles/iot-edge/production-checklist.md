---
title: 프로덕션 단계에서 솔루션 배포 준비 - Azure IoT Edge
description: 적절한 인증서를 사용하여 디바이스 설정, 향후 코드 업데이트를 위한 배포 계획 수립 등 Azure IoT Edge 솔루션을 개발에서 프로덕션으로 전환하는 방법을 알아봅니다.
author: kgremban
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: b131d20122ca2440698fed301768d1fe961ac286
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390352"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>IoT Edge 솔루션을 프로덕션 단계에서 배포하도록 준비

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge 솔루션을 개발에서 프로덕션으로 전환할 준비가 되었으면 지속적인 성능 유지가 가능하도록 구성되었는지 확인합니다.

이 문서에서 다루는 정보의 중요도가 모두 같지는 않습니다. 우선 순위를 정하는 데 도움이 되도록 각 섹션의 시작 부분에 작업이 두 가지 범주로 구분되어 나열되어 있습니다. **중요** 에 해당하는 작업은 프로덕션으로 전환하기 전에 완료해야 하며, **유용함** 에 해당하는 작업은 참조용으로 알아두면 좋습니다.

## <a name="device-configuration"></a>디바이스 구성

IoT Edge 디바이스는 Raspberry Pi부터 노트북, 서버에서 실행되는 가상 머신에 이르기까지 모든 것을 망라할 수 있습니다. 물리적으로 또는 가상 연결을 통해 디바이스에 액세스할 수 있는 경우도 있고, 디바이스가 오랜 기간 동안 격리되었을 수도 있습니다. 어느 쪽이든 간에, 제대로 작동하도록 구성되었는지 확인하는 것이 좋습니다.

* **중요**
  * 프로덕션 인증서 설치
  * 디바이스 관리 계획 준비
  * Moby를 컨테이너 엔진으로 사용

* **유용함**
  * 업스트림 프로토콜 선택

### <a name="install-production-certificates"></a>프로덕션 인증서 설치

프로덕션 단계의 모든 IoT Edge 디바이스에 디바이스 CA(인증 기관) 인증서가 설치되어 있어야 합니다. 설치된 CA 인증서는 구성 파일에서 IoT Edge 런타임으로 선언됩니다. 개발 및 테스트 시나리오의 경우, 구성 파일에서 선언된 인증서가 없는 경우 IoT Edge 런타임에서 임시 인증서를 만듭니다. 그러나 이러한 임시 인증서는 3개월 후에 만료되어 프로덕션 시나리오가 원활하게 진행될 수 없습니다. 프로덕션 시나리오의 경우 자체 서명된 인증 기관에서 또는 상용 인증 기관에서 구매한 사용자 고유의 디바이스 CA 인증서를 제공해야 합니다.

<!--1.1-->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> 현재 libiothsm의 제한으로 인해 2038년 1월 1일 이후에 만료되는 인증서는 사용할 수 없습니다.

:::moniker-end

디바이스 CA 인증서의 역할을 이해하려면 [Azure IoT Edge에서 인증서를 사용하는 방법](iot-edge-certs.md)을 참조하세요.

IoT Edge 디바이스에 인증서를 설치하고 구성 파일에서 해당 인증서를 참조하는 방법에 대한 자세한 내용은 [IoT Edge 디바이스에서 인증서 관리](how-to-manage-device-certificates.md)를 참조하세요.

### <a name="have-a-device-management-plan"></a>디바이스 관리 계획 준비

디바이스를 프로덕션으로 전환하기 전에 향후 업데이트를 관리할 방법을 준비해 두어야 합니다. IoT Edge 디바이스의 경우 업데이트할 구성 요소 목록에 다음이 포함될 수 있습니다.

* 디바이스 펌웨어
* 운영 체제 라이브러리
* Moby와 같은 컨테이너 엔진
* IoT Edge
* CA 인증서

자세한 내용은 [IoT Edge 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요. 현재는 IoT Edge 디바이스에 물리적으로 또는 SSH를 통해 액세스해야만 IoT Edge를 업데이트할 수 있습니다. 업데이트할 디바이스가 많을 경우 업데이트 단계를 스크립트에 추가하거나 Ansible 등의 자동화 도구를 사용하는 것이 좋습니다.

### <a name="use-moby-as-the-container-engine"></a>Moby를 컨테이너 엔진으로 사용

컨테이너 엔진은 모든 IoT Edge 디바이스의 사전 요구 사항입니다. 프로덕션 단계에서는 Moby 엔진만 지원됩니다. Docker 등 IoT Edge와 호환되는 다른 컨테이너 엔진은 개발 단계에서 사용할 수 있습니다. Moby 엔진은 Azure IoT Edge와 함께 사용할 때 재배포할 수 있으며, Microsoft에서 이 엔진에 대한 서비스를 제공합니다.

### <a name="choose-upstream-protocol"></a>업스트림 프로토콜 선택

IoT Edge 에이전트 및 IoT Edge 허브 모두에 대해 IoT Hub 업스트림 통신에 사용되는 포트를 결정하는 프로토콜을 구성할 수 있습니다. 기본 프로토콜은 AMQP이지만 네트워크 설정에 따라 변경할 수 있습니다.

두 개의 런타임 모듈에 모두 **UpstreamProtocol** 환경 변수가 있습니다. 변수에 유효한 값은 다음과 같습니다.

* MQTT
* AMQP
* MQTTWS
* AMQPWS

디바이스 자체의 구성 파일에서 IoT Edge 에이전트에 대한 UpstreamProtocol 변수를 구성합니다. 예를 들어 IoT Edge 디바이스가 AMQP 포트를 차단하는 프록시 서버 뒤에 있는 경우 AMQPWS(WebSocket을 통한 AMQP)를 사용하여 IoT Hub에 대한 초기 연결을 설정하도록 IoT Edge 에이전트를 구성해야 할 수 있습니다.

IoT Edge 디바이스가 연결된 다음, 향후 배포에서 두 런타임 모듈에 대한 UpstreamProtocol 변수를 계속 구성해야 합니다. 이 프로세스의 예제는 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)에 나와 있습니다.

## <a name="deployment"></a>배포

* **유용함**
  * 업스트림 프로토콜의 일관성 유지
  * 시스템 모듈의 호스트 스토리지 설정
  * IoT Edge 허브에서 사용하는 메모리 공간 축소
  * 모듈 이미지의 디버그 버전 사용 안 함

### <a name="be-consistent-with-upstream-protocol"></a>업스트림 프로토콜의 일관성 유지

IoT Edge 디바이스에서 기본 AMQP가 아닌 다른 프로토콜을 사용하도록 IoT Edge 에이전트를 구성한 경우 모든 후속 배포에서 동일한 프로토콜을 선언해야 합니다. 예를 들어 IoT Edge 디바이스가 AMQP 포트를 차단하는 프록시 서버 뒤에 있는 경우 AMQPWS(WebSocket을 통한 AMQP)를 통해 연결하도록 디바이스를 구성했을 수 있습니다. 디바이스에 모듈을 배포할 때 IoT Edge 에이전트 및 IoT Edge 허브에 대해 동일한 APQPWS 프로토콜을 구성하지 않으면 기본 AMQP가 설정을 재정의하여 다시 연결할 수 없게 됩니다.

IoT Edge 에이전트 및 IoT Edge 허브 모듈에 대한 UpstreamProtocol 환경 변수만 구성하면 됩니다. 모든 추가 모듈은 런타임 모듈에 설정된 프로토콜을 채택합니다.

이 프로세스의 예제는 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)에 나와 있습니다.

### <a name="set-up-host-storage-for-system-modules"></a>시스템 모듈의 호스트 스토리지 설정

IoT Edge 허브 및 에이전트 모듈은 로컬 스토리지를 사용하여 상태를 유지하고 모듈, 디바이스 및 클라우드 간에 메시징을 사용하도록 설정합니다. 안정성과 성능을 향상하려면 호스트 파일 시스템의 스토리지를 사용하도록 시스템 모듈을 구성합니다.

자세한 내용은 [시스템 모듈의 호스트 스토리지](how-to-access-host-storage-from-module.md)를 참조하세요.

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>IoT Edge 허브에서 사용하는 메모리 공간 축소

사용 가능한 메모리가 제한된 디바이스를 배포하는 경우, 실행되는 메모리 사용량을 줄이고 디스크 공간을 덜 사용하도록 IoT Edge 허브를 구성할 수 있습니다. 그러나 이러한 구성은 IoT Edge 허브의 성능을 제한하므로, 사용자 솔루션에 적합하게 균형을 맞추세요.

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>리소스가 제한된 디바이스에서 성능에 최적화하지 않음

IoT Edge 허브는 기본적으로 성능에 최적화되어 있으므로 큰 메모리 청크를 할당하려고 합니다. 이 구성으로 인해 Raspberry Pi와 같은 작은 디바이스에서 안정성 문제가 발생할 수 있습니다. 리소스가 제한된 디바이스를 배포하는 경우 IoT Edge 허브에서 **OptimizeForPerformance** 환경 변수를 **false** 로 설정하는 것이 좋습니다.

**OptimizeForPerformance** 가 **true** 로 설정되면 MQTT 프로토콜 헤드는 더 나은 성능을 갖지만 더 많은 메모리를 할당하는 PooledByteBufferAllocator를 사용합니다. 할당자는 32비트 운영 체제 또는 메모리가 부족한 디바이스에서 제대로 작동하지 않습니다. 또한 성능을 최적화하는 경우 RocksDb는 해당 역할에 대해 로컬 스토리지 공급자로 더 많은 메모리를 할당합니다.

자세한 내용은 [더 작은 디바이스의 안정성 문제](troubleshoot-common-errors.md#stability-issues-on-smaller-devices)를 참조하세요.

#### <a name="disable-unused-protocols"></a>사용하지 않는 프로토콜 비활성화

IoT Edge 허브의 성능을 최적화하고 메모리 사용량을 줄이는 또 다른 방법은 솔루션에서 사용하지 않는 프로토콜의 헤드를 끄는 것입니다.

프로토콜 헤드는 배포 매니페스트에서 IoT Edge 허브 모듈에 대한 부울 환경 변수를 설정하여 구성합니다. 세 가지 변수는 다음과 같습니다.

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

세 변수에는 모두 *두 개의 밑줄* 이 있으며 true 또는 false로 설정할 수 있습니다.

#### <a name="reduce-storage-time-for-messages"></a>메시지의 스토리지 시간 축소

IoT Edge 허브 모듈은 어떤 이유로든 IoT Hub에 배달할 수 없는 메시지를 임시로 저장합니다. IoT Edge 허브가 만료되도록 허용하기 전에 배달하지 못한 메시지를 유지하는 기간을 구성할 수 있습니다. 디바이스에 메모리 관련 문제가 있는 경우 IoT Edge 허브 모듈 쌍에서 **timeToLiveSecs** 값을 낮출 수 있습니다.

timeToLiveSecs 매개 변수의 기본값은 7200초로, 2시간입니다.

### <a name="do-not-use-debug-versions-of-module-images"></a>모듈 이미지의 디버그 버전 사용 안 함

테스트 시나리오에서 프로덕션 시나리오로 전환하는 경우 배포 매니페스트에서 디버그 구성을 제거해야 합니다. 배포 매니페스트에 **\.debug** 접미사가 있는 모듈 이미지가 없는지 확인합니다. 디버깅을 위해 모듈의 포트를 노출하는 만들기 옵션을 추가한 경우 해당 만들기 옵션도 제거합니다.

## <a name="container-management"></a>컨테이너 관리

* **중요**
  * 태그를 사용하여 버전 관리
* **유용함**
  * 프라이빗 레지스트리에 런타임 컨테이너 저장

### <a name="use-tags-to-manage-versions"></a>태그를 사용하여 버전 관리

태그는 Docker 컨테이너의 버전을 구별하는 데 사용할 수 있는 Docker 개념입니다. 태그는 컨테이너 리포지토리의 끝에 추가되는 **1.1** 등의 접미사입니다. 예를 들어 **mcr.microsoft.com/azureiotedge-agent:1.1** 입니다. 태그는 변경 가능하며, 언제든지 다른 컨테이너를 가리키도록 변경할 수 있으므로 앞으로 모듈 이미지를 업데이트할 때 따를 규칙에 대해 팀이 합의해야 합니다.

또한 태그는 IoT Edge 디바이스에 업데이트를 적용하는 데 도움이 됩니다. 모듈의 업데이트된 버전을 컨테이너 레지스트리에 푸시할 때 태그를 증분합니다. 그런 다음, 태그가 증분된 새 배포를 디바이스에 푸시합니다. 컨테이너 엔진이 증분된 태그를 새 버전으로 인식하고 최신 모듈 버전을 디바이스로 끌어옵니다.

#### <a name="tags-for-the-iot-edge-runtime"></a>IoT Edge 런타임에 대한 태그

IoT Edge 에이전트 및 IoT Edge 허브 이미지에는 연결된 IoT Edge 버전으로 태그가 지정됩니다. 런타임 이미지에 태그를 사용하는 방법에는 다음 두 가지가 있습니다.

* **롤링 태그** - 버전 번호의 처음 두 개 값만 사용하여 해당 숫자와 일치하는 최신 이미지를 가져옵니다. 예를 들어, 최신 1.1.x 버전을 가리키는 새 릴리스가 있을 때마다 1.1이 업데이트됩니다. IoT Edge 디바이스의 컨테이너 런타임이 이미지를 다시 끌어오면 런타임 모듈은 최신 버전으로 업데이트됩니다. Azure Portal에서 배포할 때는 기본적으로 롤링 태그가 사용됩니다. *이 접근 방법은 개발 목적으로 제안됩니다.*

* **특정 태그** - 버전 번호의 세 값을 모두 사용하여 이미지 버전을 명시적으로 설정합니다. 예를 들어, 1.1.0은 초기 릴리스 이후에 변경되지 않습니다. 업데이트할 준비가 되면 배포 매니페스트에서 새 버전 번호를 선언할 수 있습니다. *이 접근 방법은 프로덕션 목적으로 제안됩니다.*

### <a name="store-runtime-containers-in-your-private-registry"></a>프라이빗 레지스트리에 런타임 컨테이너 저장

프라이빗 Azure 레지스트리에 사용자 지정 코드 모듈에 대한 컨테이너 이미지를 저장하는 방법을 알고 있지만, 이를 사용하여 edgeAgent 및 edgHub 런타임 모듈과 같은 퍼블릭 컨테이너 이미지를 저장할 수도 있습니다. 이러한 런타임 컨테이너가 MCR(Microsoft Container Registry)에 저장되므로 매우 엄격한 방화벽 제한이 있는 경우 이 작업을 수행해야 할 수 있습니다.

Docker pull 명령을 사용하여 프라이빗 레지스트리에 배치할 이미지를 가져옵니다. 끌어오기 작업 중에 컨테이너 버전을 지정하고, 아래와 같이 컨테이너 설명 페이지에서 최신 컨테이너 버전을 찾고, 필요한 경우 끌어오기 명령에서 버전을 대체해야 합니다. IoT Edge 런타임의 각 새 릴리스로 이미지를 업데이트해야 합니다.

| IoT Edge 런타임 컨테이너 | Docker pull 명령 |
| --- | --- |
| [Azure IoT Edge 에이전트](https://hub.docker.com/_/microsoft-azureiotedge-agent) | `docker pull mcr.microsoft.com/azureiotedge-agent:<VERSION_TAG>` |
| [Azure IoT Edge Hub](https://hub.docker.com/_/microsoft-azureiotedge-hub) | `docker pull mcr.microsoft.com/azureiotedge-hub:<VERSION_TAG>` |

다음으로, edgeAgent 및 edgeHub 시스템 모듈에 대한 파일의 deployment.template.js 이미지 참조를 업데이트해야 합니다. `mcr.microsoft.com`을 두 모듈의 레지스트리 이름 및 서버로 대체합니다.

* edgeAgent:

    `"image": "<registry name and server>/azureiotedge-agent:1.1",`

* edgeHub:

    `"image": "<registry name and server>/azureiotedge-hub:1.1",`

## <a name="networking"></a>네트워킹

* **유용함**
  * 아웃바운드/인바운드 구성 검토
  * IoT Edge 디바이스의 연결 허용
  * 프록시를 통한 통신 구성

### <a name="review-outboundinbound-configuration"></a>아웃바운드/인바운드 구성 검토

Azure IoT Hub과 IoT Edge 간의 통신 채널은 항상 아웃바운드로 구성됩니다. 대부분의 IoT Edge 시나리오에서는 세 개의 연결만 있으면 됩니다. 컨테이너 엔진은 모듈 이미지를 포함하는 컨테이너 레지스트리(한 개 또는 여러 개)에 연결해야 합니다. IoT Edge 런타임은 디바이스 구성 정보를 검색하고 메시지 및 원격 분석을 보내기 위해 IoT Hub에 연결해야 합니다. 또한 자동 프로비전을 사용하는 경우, IoT Edge는 DPS에 연결해야 합니다. 자세한 내용은 [방화벽 및 포트 구성 규칙](troubleshoot.md#check-your-firewall-and-port-configuration-rules)을 참조하세요.

### <a name="allow-connections-from-iot-edge-devices"></a>IoT Edge 디바이스의 연결 허용

네트워킹 설정에 따라 IoT Edge 디바이스의 연결을 명시적으로 허용해야 하는 경우 다음 IoT Edge 구성 요소 목록을 검토합니다.

* **IoT Edge 에이전트** 는 WebSockets 등을 통해 IoT Hub에 대한 영구적 AMQP/MQTT 연결을 엽니다.
* **IoT Edge 허브** 는 WebSockets 등을 통해 IoT Hub에 대한 단일 영구적 AMQP 연결이나 여러 MQTT 연결을 엽니다.
* **IoT Edge 서비스** 는 IoT Hub에 대한 HTTPS 호출을 간헐적으로 수행합니다.

세 가지 경우 모두, DNS 이름이 \*.azure-devices.net 패턴과 일치합니다.

또한 **컨테이너 엔진** 은 HTTPS를 통해 컨테이너 레지스트리를 호출합니다. IoT Edge 런타임 컨테이너 이미지를 검색하려면 DNS 이름이 mcr.microsoft.com입니다. 컨테이너 엔진은 배포에 구성된 대로 다른 레지스트리에 연결합니다.

다음 검사 목록은 방화벽 규칙의 시작점입니다.

   | URL(\* = 와일드카드) | 아웃바운드 TCP 포트 | 사용량 |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft Container Registry |
   | global.azure-devices-provisioning.net  | 443 | DPS 액세스(선택 사항) |
   | \*.azurecr.io | 443 | 개인 및 타사 컨테이너 레지스트리 |
   | \*.blob.core.windows.net | 443 | Blob Storage에서 Azure Container Registry 이미지 델타 다운로드 |
   | \*.azure-devices.net | 5671, 8883, 443 | IoT Hub 액세스 |
   | \*.docker.io  | 443 | Docker Hub 액세스(선택 사항) |

이러한 방화벽 규칙 중 일부는 Azure Container Registry에서 상속됩니다. 자세한 내용은 [방화벽 뒤에서 Azure Container Registry에 액세스하기 위한 규칙 구성](../container-registry/container-registry-firewall-access-rules.md)을 참조하세요.

> [!NOTE]
> REST와 데이터 엔드포인트 간에 일관된 FQDN을 제공하기 위해 **2020년 6월 15일부터** Microsoft Container Registry 데이터 엔드포인트가 `*.cdn.mscr.io`에서 `*.data.mcr.microsoft.com`으로 변경됩니다.  
> 자세한 내용은 [Microsoft Container Registry 클라이언트 방화벽 규칙 구성](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)을 참조하세요.

퍼블릭 컨테이너 레지스트리에 대한 액세스를 허용하도록 방화벽을 구성하지 않으려는 경우 [프라이빗 레지스트리에 런타임 컨테이너 저장](#store-runtime-containers-in-your-private-registry)에 설명된 대로 프라이빗 컨테이너 레지스트리에 이미지를 저장할 수 있습니다.

### <a name="configure-communication-through-a-proxy"></a>프록시를 통한 통신 구성

프록시 서버를 사용하는 네트워크에 사용자 디바이스를 배포하려는 경우 디바이스가 IoT Hub 및 컨테이너 레지스트리에 접근하기 위해 프록시를 통해 통신할 수 있어야 합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요.

## <a name="solution-management"></a>솔루션 관리

* **유용함**
  * 로그 및 진단 설정
  * 로그 크기에 대한 제한
  * 테스트 및 CI/CD 파이프라인 고려

### <a name="set-up-logs-and-diagnostics"></a>로그 및 진단 설정

Linux에서 IoT Edge 디먼은 업무 일지를 기본 로깅 드라이버로 사용합니다. 명령줄 도구 `journalctl`을 사용하여 디먼 로그를 쿼리할 수 있습니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Windows에서 IoT Edge 디먼은 PowerShell 진단을 사용합니다. `Get-IoTEdgeLog`를 사용하여 디먼 로그를 쿼리합니다. IoT Edge 모듈은 로깅에 JSON 드라이버를 사용하며, 이것이 기본값입니다.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

:::moniker-end
<!-- end 1.1 -->

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

버전 1.2부터 IoT Edge는 여러 디먼에 의존합니다. 각 디먼의 로그는 `journalctl`를 사용하여 개별적으로 쿼리할 수 있지만 `iotedge system` 명령은 결합된 로그를 쿼리하는 편리한 방법을 제공합니다.

* 통합 `iotedge` 명령:

  ```bash
  sudo iotedge system logs
  ```

* 해당 `journalctl` 명령:

  ```bash
  journalctl -u aziot-edge -u aziot-identityd -u aziot-keyd -u aziot-certd -u aziot-tpmd
  ```

:::moniker-end

IoT Edge 배포를 테스트할 때는 일반적으로 사용자 디바이스에 액세스하여 로그를 검색하고 문제를 해결할 수 있습니다. 배포 시나리오에서는 해당 옵션이 없을 수 있습니다. 프로덕션에서 사용자 디바이스에 대한 정보를 수집하는 방법을 고려해 보세요. 한 가지 옵션은 다른 모듈에서 정보를 수집하여 클라우드로 보내는 로깅 모듈을 사용하는 것입니다. 로깅 모듈의 한 가지 예는 [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics)이며, 사용자가 직접 로깅 모듈을 설계할 수도 있습니다.

### <a name="place-limits-on-log-size"></a>로그 크기에 대한 제한

기본적으로 Moby 컨테이너 엔진은 컨테이너 로그 크기 제한을 설정하지 않습니다. 시간이 지남에 따라 로그를 채우고 디스크 공간이 부족해질 수 있습니다. 이를 방지하려면 다음 옵션을 고려하세요.

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>옵션: 모든 컨테이너 모듈에 적용되는 전체 제한 설정

컨테이너 엔진 로그 옵션에서 모든 컨테이너 logfiles의 크기를 제한할 수 있습니다. 다음 예에서는 파일의 크기 및 수에 대한 제한으로 로그 드라이버를 `json-file`(권장)로 설정합니다.

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

이 정보를 `daemon.json`이라는 파일에 추가(또는 첨부)하고 다음 위치에 배치합니다.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
| 플랫폼 | 위치 |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

* `/etc/docker/`

:::moniker-end
<!-- end 1.2 -->

변경 내용을 적용하려면 컨테이너 엔진을 다시 시작해야 합니다.

#### <a name="option-adjust-log-settings-for-each-container-module"></a>옵션: 각 컨테이너 모듈에 대해 로그 설정 조정

각 모듈의 **createOptions** 에서 이 작업을 수행할 수 있습니다. 예를 들면 다음과 같습니다.

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

#### <a name="additional-options-on-linux-systems"></a>Linux 시스템에 대한 추가 옵션

* `journald`를 기본 로깅 드라이버로 설정하여 `systemd` [업무 일지](https://docs.docker.com/config/containers/logging/journald/)에 로그를 보내도록 컨테이너 엔진을 구성합니다.

* logrotate 도구를 설치하여 사용자 디바이스에서 이전 로그를 정기적으로 제거합니다. 다음 파일 사양을 사용합니다.

   ```txt
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>테스트 및 CI/CD 파이프라인 고려

가장 효율적인 IoT Edge 배포 시나리오를 위해 프로덕션 배포를 테스트 및 CI/CD 파이프라인에 통합하는 것이 좋습니다. Azure IoT Edge는 Azure DevOps를 포함하여 여러 CI/CD 플랫폼을 지원합니다. 자세한 내용은 [Azure IoT Edge 연속 통합 및 지속적인 배포](how-to-continuous-integration-continuous-deployment.md)를 참조하세요.

## <a name="security-considerations"></a>보안 고려 사항

* **중요**
  * 컨테이너 레지스트리에 대한 액세스 관리
  * 호스트 리소스에 대한 컨테이너 액세스 제한

### <a name="manage-access-to-your-container-registry"></a>컨테이너 레지스트리에 대한 액세스 관리

프로덕션 IoT Edge 디바이스에 모듈을 배포하기 전에 외부 사용자가 컨테이너 이미지를 액세스하거나 변경할 수 없도록 컨테이너 레지스트리에 대한 액세스를 제어해야 합니다. 프라이빗 컨테이너 레지스트리를 사용하여 컨테이너 이미지를 관리합니다.

자습서 및 기타 설명서에서는 개발 머신에서 사용한 것과 동일한 컨테이너 레지스트리 자격 증명을 IoT Edge 디바이스에서 사용하도록 안내합니다. 이러한 지침은 테스트 및 개발 환경을 보다 쉽게 설정하도록 돕기 위한 것이며, 프로덕션 시나리오에서 따르면 안 됩니다.

레지스트리에 보다 안전하게 액세스할 수 있도록 [인증 옵션](../container-registry/container-registry-authentication.md)을 선택할 수 있습니다. 널리 사용되고 권장되는 인증은 애플리케이션이나 서비스에 적합한 Active Directory 서비스 주체를 사용하는 것이며, 이는 IoT Edge 디바이스가 수행하는 것처럼 자동 또는 기타 무인(헤드리스) 방식으로 컨테이너 이미지를 끌어오는 것입니다. 또 다른 옵션은 리포지토리 범위 토큰을 사용 하는 것입니다 .이 토큰을 사용 하 여 생성 된 Azure Container Registry에만 존재 하는 긴 또는 짧은 라이브 id를 만들고 리포지토리 수준에 대 한 액세스 범위를 지정할 수 있습니다.

서비스 주체를 만들려면 [서비스 주체 만들기](../container-registry/container-registry-auth-service-principal.md#create-a-service-principal)에 설명된 대로 두 스크립트를 실행합니다. 이러한 스크립트는 다음 작업을 수행합니다.

* 첫 번째 스크립트는 서비스 사용자를 만듭니다. 서비스 주체 ID 및 서비스 주체 암호를 출력합니다. 이러한 값을 레코드에 안전하게 저장합니다.

* 두 번째 스크립트는 필요한 경우 나중에 실행할 수 있는 서비스 주체에게 부여할 역할 할당을 만듭니다. `role` 매개 변수에 대한 **acrPull** 사용자 역할을 적용하는 것이 좋습니다. 역할 목록은 [Azure Container Registry 역할 및 권한](../container-registry/container-registry-roles.md)을 참조하세요.

서비스 주체를 사용하여 인증하려면 첫 번째 스크립트에서 얻은 서비스 주체 ID와 암호를 제공합니다. 배포 매니페스트에서 이러한 자격 증명을 지정합니다.

* 사용자 이름 또는 클라이언트 ID에 대해 서비스 주체 ID를 지정합니다.

* 암호 또는 클라이언트 암호의 경우 서비스 주체 암호를 지정합니다.

<br>

리포지토리 범위 토큰을 만들려면 [리포지토리 범위 토큰 만들기](../container-registry/container-registry-repository-scoped-permissions.md)를 따르세요.

리포지토리 범위 토큰을 사용 하 여 인증 하려면 리포지토리 범위 토큰을 만든 후에 얻은 토큰 이름과 암호를 제공 합니다. 배포 매니페스트에서 이러한 자격 증명을 지정합니다.

* 사용자 이름에는 토큰의 사용자 이름을 지정 합니다.

* 암호에 대해 토큰의 암호 중 하나를 지정 합니다.

> [!NOTE]
> 향상된 보안 인증을 구현한 후 기본 사용자 이름/암호 액세스를 더 이상 사용할 수 없도록 **관리 사용자** 설정을 사용하지 않도록 설정합니다. Azure Portal 컨테이너 레지스트리의 왼쪽 창 메뉴의 **설정** 에서 **액세스 키** 를 선택합니다.

### <a name="limit-container-access-to-host-resources"></a>호스트 리소스에 대한 컨테이너 액세스 제한

모듈 간에 공유 호스트 리소스의 균형을 유지하기 위해 모듈당 리소스 소비에 대해 제한을 설정하는 것이 좋습니다. 이러한 제한은 한 모듈이 너무 많은 메모리 또는 CPU 사용량을 소비할 수 없도록 하고 다른 프로세스가 디바이스에서 실행되는 것을 방지합니다. 지정된 모듈을 실행하는 데 필요한 리소스의 양을 파악하기 위해서는 테스트가 필요하기 때문에 IoT Edge 플랫폼은 기본적으로 모듈에 대한 리소스를 제한하지 않습니다.

Docker는 메모리 및 CPU 사용량과 같은 리소스를 제한하는 데 사용할 수 있는 몇 가지 제약 조건을 제공합니다. 자세한 내용은 [메모리, CPU 및 GPU를 사용하는 런타임 옵션](https://docs.docker.com/config/containers/resource_constraints/)을 참조하세요.

배포 매니페스트에서 만들기 옵션을 사용하여 개별 모듈에 이러한 제약 조건을 적용할 수 있습니다. 자세한 내용은 [IoT Edge 모듈에 대한 컨테이너 만들기 옵션 구성 방법](how-to-use-create-options.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [IoT Edge 자동 배포](module-deployment-monitoring.md)에 대해 자세히 알아보세요.
* IoT Edge에서 [연속 통합 및 지속적인 배포](how-to-continuous-integration-continuous-deployment.md)를 지원하는 방법을 확인합니다.
