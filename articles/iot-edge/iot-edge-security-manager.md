---
title: Azure IoT edge 보안 관리자/모듈 런타임-Azure IoT edge
description: IoT Edge 디바이스 보안 태세와 보안 서비스의 무결성을 관리합니다.
services: iot-edge
keywords: 보안, 보안 요소, enclave, TEE, IoT Edge
author: kgremban
ms.author: kgremban
ms.reviewer: eustacea
ms.date: 09/17/2021
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: fedb06a23a71dbe30954154daa6d314267268551
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659681"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge 보안 관리자

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge 보안 관리자는 보안 실리콘 하드웨어를 추상화하여 IoT Edge 디바이스 및 이 디바이스의 모든 구성 요소를 보호하기 위해 잘 정립된 보안 코어입니다. 보안 관리자는 보안 강화의 중심이며 OEM(original equipment manufacturers)에 기술 통합 지점을 제공합니다.

<!--1.1-->
:::moniker range="iotedge-2018-06"
보안 관리자는 IoT Edge 장치에서 보안 실리콘 하드웨어를 추상화 합니다.

![Azure IoT Edge 보안 관리자](media/edge-security-manager/iot-edge-security-manager.png)
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
보안 관리자는 IoT Edge 장치에서 보안 실리콘 하드웨어를 추상화 하 고 추가 보안 서비스를 위한 확장성 프레임 워크를 제공 합니다.

<!-- add new graphic here -->
:::moniker-end

IoT Edge security manager는 IoT Edge 장치의 무결성 및 모든 내재 된 소프트웨어 작업을 보호 하는 것을 목표로 합니다. 보안 관리자는 신뢰할 수 있는 하드웨어(사용 가능한 경우)의 기본 하드웨어 루트에서 트러스트를 전환하여 IoT Edge 런타임을 부트스트랩하고 진행 중인 작업을 모니터링합니다.  IoT Edge 보안 관리자는 보안 실리콘 하드웨어(사용 가능한 경우)와 연동되어 가능한 최고 수준의 보안을 보장하는 소프트웨어입니다.  

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
또한 IoT Edge 보안 관리자는 호스트 수준 모듈을 통해 보안 서비스 확장을 위한 안전한 프레임 워크를 제공 합니다. 이러한 서비스에는 장치의 일부 구성 요소에 대 한 특권 수준의 액세스 권한이 있는 장치 내의 에이전트가 필요한 보안 모니터링 및 업데이트가 포함 됩니다. 확장성 프레임 워크는 이러한 통합이 전반적인 시스템 보안을 일관성 있게 취했습니다 합니다.
:::moniker-end

IoT Edge 보안 관리자의 책임에는 다음을 비롯한 여러 사항이 포함됩니다.

<!--1.1-->
:::moniker range="iotedge-2018-06"

* Azure IoT Edge 장치를 부트스트랩 합니다.
* 공증 서비스를 통한 신뢰의 장치 하드웨어 루트에 대 한 액세스를 제어 합니다.
* 런타임 시 IoT Edge 작업의 무결성 모니터링.
* HSM (하드웨어 보안 모듈)에서 트러스트 위임을 수신 합니다.
* 장치 id를 프로 비전 하 고 해당 하는 경우 신뢰의 전환을 관리 합니다.
* Device Provisioning Service와 같은 클라우드 서비스의 디바이스 구성 요소 호스트 및 보호.
* 고유 id를 사용 하 여 IoT Edge 모듈을 프로 비전 합니다.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

* Azure IoT Edge 장치를 부트스트랩 합니다.
* 공증 서비스를 통한 신뢰의 장치 하드웨어 루트에 대 한 액세스를 제어 합니다.
* 런타임 시 IoT Edge 작업의 무결성 모니터링.
* 장치 id를 프로 비전 하 고 해당 하는 경우 신뢰의 전환을 관리 합니다.
* IoT Hub에 대 한 장치 업데이트 및 IoT 용 Azure Defender를 비롯 하 여 서비스에 대 한 클라이언트 에이전트의 안전 작업을 보장 합니다.
:::moniker-end

IoT Edge 보안 관리자는 다음과 같은 세 가지 구성 요소로 구성 됩니다.

<!--1.1-->
:::moniker range="iotedge-2018-06"

* IoT Edge 보안 디먼
* 하드웨어 보안 모듈 HSM PAL (플랫폼 추상화 계층)
* 하드웨어 실리콘 신뢰 또는 HSM 루트 (선택 사항 이지만 권장 됨)
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

* IoT Edge 모듈 런타임
* PKCS # 11 및 신뢰할 수 있는 플랫폼 모듈 (TPM)와 같은 표준 구현을 통한 HSM (하드웨어 보안 모듈) 추상화
* 하드웨어 실리콘 신뢰 또는 HSM 루트 (선택 사항 이지만 권장 됨)

## <a name="changes-in-version-12"></a>버전 1.2의 변경 내용

IoT Edge 버전 1.0 및 1.1에서 **보안 디먼** 이라는 구성 요소는 보안 관리자의 논리적 보안 작업을 담당 합니다. 버전 1.2에 대 한 업데이트에서 몇 가지 주요 책임이 [Azure IoT Identity Service](https://azure.github.io/iot-identity-service/) security subsystem에 위임 되었습니다. 이러한 보안 기반 작업이 보안 디먼에서 제거 되 면 해당 이름이 더 이상 적합 하지 않습니다. 이 구성 요소가 버전 1.2 이상에서 수행 하는 작업을 더 잘 반영 하기 위해 **모듈 런타임으로** 이름이 변경 되었습니다.
:::moniker-end

<!--1.1-->
:::moniker range="iotedge-2018-06"
## <a name="the-iot-edge-security-daemon"></a>IoT Edge 보안 디먼

IoT Edge 보안 디먼은 보안 관리자의 논리적 보안 작업을 담당 합니다. 이는 IoT Edge 디바이스의 Trusted Computing Base에서 상당한 부분을 차지합니다.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
## <a name="the-iot-edge-module-runtime"></a>IoT Edge 모듈 런타임

IoT Edge 모듈 런타임에서는 [Azure IoT Identity Service](https://azure.github.io/iot-identity-service/) security subsystem의 신뢰를 위임 하 여 IoT Edge container runtime 환경을 보호 합니다. 모듈 런타임은 보안 관리자의 논리적 보안 작업을 담당 합니다. 이는 IoT Edge 디바이스의 Trusted Computing Base에서 상당한 부분을 차지합니다. 모듈 런타임은 IoT Id 서비스의 보안 서비스를 사용 합니다 .이 서비스는 장치 제조업체의 HSM (하드웨어 보안 모듈) 선택에 의해 강화 됩니다. 장치 강화를 위해 Hsm을 사용 하는 것이 좋습니다.
:::moniker-end

### <a name="design-principles"></a>디자인 원칙

IoT Edge는 두 가지 핵심 원칙을 따릅니다. 운영 무결성을 최대화 하 고, 팽창 및 변동 (code)을 최소화

#### <a name="maximize-operational-integrity"></a>작동 무결성 극대화

<!--1.1-->
:::moniker range="iotedge-2018-06"
IoT Edge 보안 디먼은 지정된 신뢰할 수 있는 하드웨어 루트의 방어 기능 내에서 가능한 최고 수준의 무결성을 유지하면서 작동합니다. 적절한 통합을 통해 트러스트 하드웨어의 루트는 변조를 방지하기 위해 보안 디먼을 런타임 시 정적으로 측정하고 모니터링합니다.
:::moniker-end

<!--1.1-->
:::moniker range=">=iotedge-2020-11"
IoT Edge 모듈 런타임은 지정 된 신뢰 하드웨어 루트의 방어 기능 내에서 가능한 가장 높은 무결성으로 작동 합니다. 적절한 통합을 통해 트러스트 하드웨어의 루트는 변조를 방지하기 위해 보안 디먼을 런타임 시 정적으로 측정하고 모니터링합니다.
:::moniker-end

장치에 대 한 악성 물리적 액세스는 항상 IoT에서 위협입니다. 신뢰의 하드웨어 루트는 IoT Edge 장치의 무결성을 방어 하는 데 중요 한 역할을 합니다.  신뢰할 수 있는 하드웨어 루트는 두 가지로 제공됩니다.

* 비밀 및 암호화 키와 같은 중요 한 정보의 보호를 위한 보안 요소
* 키와 같은 비밀 및 기밀 기계 학습 모델 및 계량 작업과 같은 중요 한 워크 로드를 보호 하기 위한 보안 enclaves.

신뢰할 수 있는 하드웨어 루트는 두 종류의 실행 환경에서 사용할 수 있습니다.

* 보안 요소를 사용하여 중요한 정보를 보호하는 표준 환경 또는 REE(rich execution environment)
* 보안 enclave 기술을 사용하여 중요한 정보를 보호하고 소프트웨어 실행에 대한 보호를 제공하는 TEE(신뢰 실행 환경)

<!--1.1-->
:::moniker range="iotedge-2018-06"
Secure enclaves를 신뢰의 하드웨어 루트로 사용 하는 장치의 경우, IoT Edge 보안 디먼 내의 중요 한 논리가 enclave 내에 있어야 합니다.  보안 디먼의 중요하지 않은 부분은 TEE 외부에 있을 수 있습니다.  모든 경우에 ODM (원래 설계 제조업체) 및 OEM (원본 장비 제조업체)에서 HSM 으로부터 신뢰를 확장 하 여 부팅 및 런타임에 IoT Edge 보안 디먼의 무결성을 측정 하 고 보호 하는 것이 좋습니다.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Secure enclaves를 신뢰의 하드웨어 루트로 사용 하는 장치의 경우 IoT Edge 모듈 런타임 내의 중요 한 논리가 enclave 내에 있어야 합니다.  모듈 런타임의 중요 하지 않은 부분은 티의 외부에 있을 수 있습니다.  모든 경우에 ODM (원래 설계 제조업체) 및 OEM (원본 장비 제조업체)에서 HSM 으로부터 신뢰를 확장 하 여 부팅 및 런타임에 IoT Edge 모듈 런타임의 무결성을 측정 하 고 보호 하는 것이 좋습니다.
:::moniker-end

#### <a name="minimize-bloat-and-churn"></a>블로트 및 변동 최소화

<!--1.1-->
:::moniker range="iotedge-2018-06"
IoT Edge 보안 디먼이 따르는 또 다른 핵심 원칙은 변동의 최소화합니다.  최고 수준의 신뢰를 보장하기 위해 IoT Edge 보안 디먼은 신뢰할 수 있는 디바이스 하드웨어 루트와 밀접하게 결합되어 네이티브 코드로 작동할 수 있습니다.  이러한 경우 운영 체제의 업데이트 메커니즘이 아닌 신뢰의 보안 업데이트 경로 하드웨어 루트를 통해 IoT Edge 소프트웨어를 업데이트 하는 것이 일반적입니다 .이 작업은 어려울 수 있습니다.  IoT 장치에는 보안 갱신을 권장 하지만, 과도 한 업데이트 요구 사항 또는 대량 업데이트 페이로드는 여러 가지 방법으로 위협 화면을 확장할 수 있습니다. 예를 들어 장치 가용성을 최대화 하기 위해 일부 업데이트를 건너뛸 수 있습니다. 따라서 IoT Edge 보안 디먼의 설계는 업데이트를 자주 하기 위해 잘 격리 된 신뢰할 수 있는 컴퓨팅 기반을 유지 하기 위해 간결 합니다.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
IoT Edge 모듈 런타임에 대 한 또 다른 핵심 원칙은 변동를 최소화 하는 것입니다.  가장 높은 신뢰 수준에서 IoT Edge 모듈 런타임은 신뢰의 장치 하드웨어 루트와 긴밀 하 게 결합 하 고 네이티브 코드로 작동할 수 있습니다.  이러한 경우 운영 체제의 업데이트 메커니즘이 아닌 신뢰의 보안 업데이트 경로 하드웨어 루트를 통해 IoT Edge 소프트웨어를 업데이트 하는 것이 일반적입니다 .이 작업은 어려울 수 있습니다.  IoT 장치에는 보안 갱신을 권장 하지만, 과도 한 업데이트 요구 사항 또는 대량 업데이트 페이로드는 여러 가지 방법으로 위협 화면을 확장할 수 있습니다. 예를 들어 장치 가용성을 최대화 하기 위해 일부 업데이트를 건너뛸 수 있습니다. 따라서 IoT Edge 모듈 런타임의 디자인은 업데이트를 자주 수행 하기 위해 잘 격리 된 신뢰할 수 있는 컴퓨팅 기반을 유지 하기 위해 간결 합니다.
:::moniker-end

### <a name="architecture"></a>Architecture

<!--1.1-->
:::moniker range="iotedge-2018-06"

IoT Edge 보안 디먼은 보안 강화를 위해 사용 가능한 모든 신뢰할 수 있는 하드웨어 루트 기술을 활용합니다.  또한 하드웨어 기술에서 신뢰할 수 있는 실행 환경을 제공할 때는 표준 REE(Rich Execution Environment)와 TEE(Trusted Executive Environment) 간의 분할 작업도 가능합니다. 역할별 인터페이스를 통해 IoT Edge의 주요 구성 요소가 IoT Edge 디바이스와 해당 디바이스 작동의 무결성을 보장할 수 있습니다.

![Azure IoT Edge 보안 디먼 아키텍처](media/edge-security-manager/iot-edge-security-daemon.png)
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

IoT Edge 모듈 런타임은 보안 강화를 위해 사용 가능한 신뢰 기술의 하드웨어 루트를 활용 합니다.  또한 하드웨어 기술에서 신뢰할 수 있는 실행 환경을 제공할 때는 표준 REE(Rich Execution Environment)와 TEE(Trusted Executive Environment) 간의 분할 작업도 가능합니다. 역할별 인터페이스를 통해 IoT Edge의 주요 구성 요소가 IoT Edge 디바이스와 해당 디바이스 작동의 무결성을 보장할 수 있습니다.

![Azure IoT Edge 모듈 런타임 아키텍처](media/edge-security-manager/iot-edge-module-runtime.png)
:::moniker-end

#### <a name="cloud-interface"></a>클라우드 인터페이스

클라우드 인터페이스를 사용 하 여 장치 보안을 보완 하는 클라우드 서비스에 액세스할 수 있습니다.  예를 들어이 인터페이스는 장치 id 수명 주기 관리를 위해 [장치 프로 비전 서비스](../iot-dps/index.yml) 에 대 한 액세스를 허용 합니다.  

#### <a name="management-api"></a>관리 API

<!--1.1-->
:::moniker range="iotedge-2018-06"
관리 API는 IoT Edge 모듈을 생성/시작/중지/제거할 때 IoT Edge 에이전트에서 호출 됩니다. 보안 디먼은 모든 활성 모듈에 대해 "등록"을 저장 합니다. 이러한 등록은 모듈의 id를 모듈의 일부 속성에 매핑합니다. 예를 들어 이러한 모듈 속성에는 컨테이너에서 실행 중인 프로세스의 pid (프로세스 식별자)와 docker 컨테이너 콘텐츠의 해시가 포함 됩니다.

해당 속성은 워크로드 API(아래 설명 참조)에서 호출자가 작업을 수행할 수 있는 권한이 있는지 확인하는 데 사용됩니다.

관리 API는 권한이 있는 API로 IoT Edge 에이전트에서만 호출할 수 있습니다.  IoT Edge 보안 디먼은 IoT Edge 에이전트를 부트스트랩하고 시작하기 때문에 IoT Edge 에이전트가 변조되지 않았음을 확인한 후에, IoT Edge 에이전트에 대한 암시적인 등록을 만들 수 있습니다. 또한 워크로드 API가 사용하는 동일한 증명 프로세스는 관리 API에 대한 액세스를 IoT Edge 에이전트에만 제한합니다.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
관리 API는 IoT Edge 모듈을 생성/시작/중지/제거할 때 IoT Edge 에이전트에서 호출됩니다. 모듈 런타임은 모든 활성 모듈에 대한 "등록"을 저장합니다. 이러한 등록은 모듈의 ID를 모듈의 일부 속성에 매핑합니다. 예를 들어 이러한 모듈 속성에는 컨테이너에서 실행되는 프로세스의 프로세스 식별자(pid)와 Docker 컨테이너 콘텐츠의 해시가 포함됩니다.

해당 속성은 워크로드 API(아래 설명 참조)에서 호출자가 작업을 수행할 수 있는 권한이 있는지 확인하는 데 사용됩니다.

관리 API는 권한이 있는 API로 IoT Edge 에이전트에서만 호출할 수 있습니다.  IoT Edge 모듈 런타임이 부트스트랩되고 IoT Edge 에이전트를 시작하기 때문에 IoT Edge 에이전트가 변조되지 않았는지 확인한 다음 IoT Edge 에이전트에 대한 암시적 등록을 만들 수 있습니다. 또한 워크로드 API가 사용하는 동일한 증명 프로세스는 관리 API에 대한 액세스를 IoT Edge 에이전트에만 제한합니다.
:::moniker-end

#### <a name="container-api"></a>컨테이너 API

컨테이너 API는 Moby 또는 Docker와 같이 모듈 관리에 사용되는 컨테이너 시스템과 상호 작용합니다.

#### <a name="workload-api"></a>워크로드 API

워크로드 API는 모든 모듈에 액세스할 수 있습니다. 이 API는 ID 증명(HSM 루트 서명된 토큰 또는 X509 인증서) 및 관련 트러스트 번들을 모듈에 제공합니다. 트러스트 번들에는 모듈이 신뢰해야 하는 다른 모든 서버용 CA 인증서가 포함됩니다.

<!--1.1-->
:::moniker range="iotedge-2018-06"
IoT Edge 보안 디먼은 증명 프로세스를 사용하여 이 API를 보호합니다. 모듈에서 이 API를 호출하면 보안 디먼이 ID 등록을 찾으려고 시도합니다. 성공하면 등록의 속성을 사용하여 모듈을 측정합니다. 측정 프로세스의 결과가 등록과 일치하면 새로운 ID 증명이 생성됩니다. 그리고 해당 CA 인증서(트러스트 번들)가 모듈에 반환됩니다.  모듈은 이 인증서를 사용하여 IoT Hub나 다른 모듈에 연결하거나 서버를 시작합니다. 서명된 토큰 또는 인증서가 곧 만료되는 경우, 모듈이 새로운 인증서를 요청하는 역할을 담당합니다.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
IoT Edge 모듈 런타임은 증명 프로세스를 사용하여 이 API를 보호합니다. 모듈이 이 API를 호출하면 모듈 런타임은 ID에 대한 등록을 찾으려고 시도합니다. 성공하면 등록의 속성을 사용하여 모듈을 측정합니다. 측정 프로세스의 결과가 등록과 일치하면 새로운 ID 증명이 생성됩니다. 그리고 해당 CA 인증서(트러스트 번들)가 모듈에 반환됩니다.  모듈은 이 인증서를 사용하여 IoT Hub나 다른 모듈에 연결하거나 서버를 시작합니다. 서명된 토큰 또는 인증서가 곧 만료되는 경우, 모듈이 새로운 인증서를 요청하는 역할을 담당합니다.
:::moniker-end

### <a name="integration-and-maintenance"></a>통합 및 유지 관리

<!--1.1-->
:::moniker range="iotedge-2018-06"
Microsoft는 [IoT Edge 보안 디먼용 기본 코드 베이스를 GitHub](https://github.com/Azure/iotedge/tree/release/1.1/edgelet)에 유지 관리합니다.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Microsoft는 [GitHub IoT Edge 모듈 런타임](https://github.com/Azure/iotedge/tree/master/edgelet) 및 [Azure IoT ID 서비스에](https://github.com/Azure/iot-identity-service) 대한 기본 코드 베이스를 유지 관리합니다.

IoT Edge 코드베이스를 읽을 때 **모듈 런타임은** **보안 디먼** 에서 진화했습니다. 코드베이스는 여전히 보안 디먼에 대한 참조를 포함할 수 있습니다.
:::moniker-end

#### <a name="installation-and-updates"></a>설치 및 업데이트

<!--1.1-->
:::moniker range="iotedge-2018-06"
IoT Edge 보안 디먼의 설치 및 업데이트는 운영 체제의 패키지 관리 시스템을 통해 관리됩니다. 신뢰할 수 있는 하드웨어 루트가 있는 IoT Edge 디바이스는 보안 부트 및 업데이트 관리 시스템을 통해 수명 주기를 관리하여 디먼의 무결성을 추가로 강화해야 합니다. 디바이스 제조업체는 자사의 각 디바이스 기능에 따라 해당 방안을 살펴봐야 합니다.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
IoT Edge 모듈 런타임의 설치 및 업데이트는 운영 체제의 패키지 관리 시스템 통해 관리됩니다. 신뢰할 수 있는 하드웨어 루트가 있는 IoT Edge 디바이스는 보안 부팅 및 업데이트 관리 시스템을 통해 수명 주기를 관리하여 모듈 런타임의 무결성을 추가로 강화해야 합니다. 디바이스 제조업체는 자사의 각 디바이스 기능에 따라 해당 방안을 살펴봐야 합니다.
:::moniker-end

#### <a name="versioning"></a>버전 관리

<!--1.1-->
:::moniker range="iotedge-2018-06"
IoT Edge 런타임은 IoT Edge 보안 디먼의 버전을 추적 및 보고합니다. 버전은 IoT Edge 에이전트 모듈이 보고한 속성의 *runtime.platform.version* 특성으로 보고됩니다.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
IoT Edge 런타임은 IoT Edge 모듈 런타임의 버전을 추적하고 보고합니다. 버전은 IoT Edge 에이전트 모듈이 보고한 속성의 *runtime.platform.version* 특성으로 보고됩니다.
:::moniker-end

## <a name="hardware-security-module"></a>하드웨어 보안 모듈

<!--1.1-->
:::moniker range="iotedge-2018-06"
HSM PAL(하드웨어 보안 모듈 플랫폼 추상화 계층)은 신뢰 하드웨어의 모든 루트를 추상화하여 IoT Edge 개발자 또는 사용자를 복잡성으로부터 격리합니다.  이는 API(애플리케이션 프로그래밍 인터페이스) 및 트랜스 도메인 통신 프로시저(예: 표준 실행 환경과 보안 enclave 간의 통신)의 조합으로 구성됩니다.  HSM PAL의 실제 구현은 사용 중인 특정 보안 하드웨어에 따라 달라집니다. 보안 하드웨어가 있으면 사실상 모든 보안 실리콘 하드웨어를 사용할 수 있습니다.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
IoT Edge 보안 관리자는 HSM(하드웨어 보안 모듈)을 통합하기 위한 신뢰할 수 있는 플랫폼 모듈 및 PKCS#11 인터페이스 표준을 구현합니다. 이러한 표준을 사용하면 독점 인터페이스가 있는 HSM을 비롯한 거의 모든 HSM을 통합할 수 있습니다. 보안 강화를 위해 HSM을 사용하는 것이 좋습니다.
:::moniker-end

## <a name="secure-silicon-root-of-trust-hardware"></a>트러스트 하드웨어의 보안 실리콘 루트

보안 실리콘은 IoT Edge 디바이스 하드웨어 내부에 신뢰를 확보하는 데 필요합니다.  보안 실리콘은 TPM(신뢰할 수 있는 플랫폼 모듈), eSE(Embedded Secure Element), Arm TrustZone, Intel SGX 및 사용자 지정 보안 실리콘 기술을 포함하기 위해 다양합니다.  IoT 디바이스의 물리적 접근성에 관련된 위협을 고려하면 디바이스에 신뢰할 수 있는 보안 실리콘 루트를 사용하는 것이 좋습니다.

IoT Edge 보안 관리자의 목표는 사용자 지정 강화를 위해 Azure IoT Edge 플랫폼의 보안 및 무결성을 방어하는 구성 요소를 식별하고 격리하는 것입니다. 디바이스 제조업체 등의 타사는 디바이스 하드웨어에서 제공되는 사용자 지정 보안 기능을 사용해야 합니다.  

소프트웨어 또는 가상 TPM을 사용하여 TPM(신뢰할 수 있는 플랫폼 모듈)으로 Azure IoT 보안 관리자를 강화하는 방법을 알아봅니다.  

[Linux 가상 머신에서 가상 TPM을 사용하는 IoT Edge 디바이스](how-to-provision-devices-at-scale-linux-tpm.md)를 만들고 프로비전합니다.

<!--1.1-->
:::moniker range="iotedge-2018-06"
[Windows에서 시뮬레이션된 TPM을 사용하는 IoT Edge 디바이스](how-to-provision-devices-at-scale-windows-tpm.md)를 만들고 프로비전합니다.
:::moniker-end

## <a name="next-steps"></a>다음 단계

IoT Edge 디바이스 보안에 대한 자세한 내용은 다음 블로그 게시물을 참조하세요.

* [지능형 에지 보안.](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)
* [대규모 IoT 디바이스의 제로 터치 프로비저닝을 안전하게 해결하는 청사진](https://azure.microsoft.com/blog/the-blueprint-to-securely-solve-the-elusive-zerotouch-provisioning-of-iot-devices-at-scale/)
* [표준을 통해 대규모로 IoT 디바이스 보안 해결](https://azure.microsoft.com/blog/solving-iot-device-security-at-scale-through-standards/)