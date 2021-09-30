---
title: Azure IoT Hub Device Provisioning Service에서 사용되는 용어 | Microsoft Docs
description: DPS(Device Provisioning Service) 및 IoT Hub에서 사용되는 일반적인 용어 설명
author: wesmc7777
ms.author: wesmc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: ff65da89c12366fe477ca0f93779a8b2188d4ef3
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129277274"
---
# <a name="iot-hub-device-provisioning-service-dps-terminology"></a>IoT Hub DPS(Device Provisioning Service) 용어

IoT Hub Device Provisioning Service는 지정된 IoT 허브에 대한 제로 터치 디바이스 프로비저닝을 구성하도록 사용하는 IoT Hub에 대한 도우미 서비스입니다. Device Provisioning Service를 사용하여 안전하고 확장 가능한 방식으로 수백만 개의 디바이스를 [프로비전](about-iot-dps.md#provisioning-process)할 수 있습니다.

디바이스 프로비전은 두 부분의 프로세스로 구성됩니다. 첫 번째 부분은 디바이스를 *등록* 함으로써 디바이스와 IoT 솔루션 간 초기 연결을 설정하는 것입니다. 두 번째 부분은 솔루션의 특정 요구 사항에 따라 디바이스에 적절한 *구성* 을 적용하는 것입니다. 두 단계가 수행되고 나면 디바이스가 완벽히 *프로비전된* 것입니다. Device Provisioning Service는 두 단계를 모두 자동화하여 디바이스에 원활한 프로비전 환경을 제공합니다.

이 문서는 *서비스* 관리에 가장 적합한 프로비전 개념의 개요를 제공합니다. 이 문서는 배포를 위해 디바이스를 준비하는 [클라우드 설치 단계](about-iot-dps.md#cloud-setup-step)에 관련된 사람에게 가장 적합합니다.

## <a name="service-operations-endpoint"></a>서비스 작업 엔드포인트

서비스 작업 엔드포인트는 서비스 설정을 관리하고 등록 목록을 유지 관리하기 위한 엔드포인트입니다. 이 엔드포인트는 서비스 관리자에 의해서만 사용됩니다. 디바이스에서 사용되지 않습니다.

## <a name="device-provisioning-endpoint"></a>디바이스 프로비저닝 엔드포인트

디바이스 프로비전 엔드포인트는 모든 디바이스가 자동 프로비전에 사용하는 단일 엔드포인트입니다. URL은 공급 체인 시나리오에서 새 연결 정보를 사용하여 디바이스를 새로 고쳐야 하는 필요성을 경감하기 위해 모든 프로비전 서비스 인스턴스에 대해 동일합니다. ID 범위는 테넌트 격리를 보장합니다.

## <a name="linked-iot-hubs"></a>연결된 IoT 허브

Device Provisioning 서비스는 Device Provisioning 서비스에 연결된 IoT 허브에만 프로비전할 수 있습니다. IoT hub를 장치 프로 비전 서비스의 인스턴스에 연결 하면 서비스는 IoT hub의 장치 레지스트리에 대 한 읽기/쓰기 권한을 부여 합니다. 링크를 사용 하 여 장치 프로 비전 서비스는 장치 ID를 등록 하 고 장치 쌍의 초기 구성을 설정할 수 있습니다. 연결된 IoT 허브는 모든 Azure 지역에 있을 수 있습니다. 다른 구독의 허브를 프로비전 서비스에 연결할 수 있습니다.


## <a name="allocation-policy"></a>할당 정책

서비스 수준 설정은 Device Provisioning Service에서 IoT 허브에 디바이스를 할당하는 방법을 결정합니다. 세 가지의 지원되는 할당 정책이 있습니다.

* **균등하게 가중치 적용 배포**: 연결된 IoT 허브는 디바이스를 동일하게 프로비전했을 가능성이 있습니다. 기본 설정 디바이스를 단 하나의 IoT Hub에 프로비전하려는 경우 이 설정을 유지할 수 있습니다.

* **최소 대기 시간**: 디바이스가 디바이스에 대한 최소 대기 시간으로 IoT 허브에 프로비전됩니다. 연결된 여러 IoT 허브가 동일한 최소 대기 시간을 제공하는 경우 프로비전 서비스는 이러한 허브에서 디바이스를 해싱합니다.

* **등록 목록을 통한 고정 구성**: 등록 목록에서 원하는 IoT Hub의 사양을 서비스 수준 할당 정책보다 우선합니다.

* **사용자 지정(Azure 함수 사용)** : 사용자 지정 할당 정책을 사용하면 IoT Hub에 디바이스를 할당하는 방법을 더 구체적으로 제어할 수 있습니다. 이 작업을 수행하려면 Azure 함수에서 사용자 지정 코드를 사용하여 IoT Hub에 디바이스를 할당합니다. 디바이스 프로비저닝 서비스는 Azure Function 코드를 호출하여 디바이스 및 등록과 관련된 모든 정보를 코드에 제공합니다. 실행된 함수 코드는 디바이스를 프로비전하는 데 사용된 IoT Hub 정보를 반환합니다.

## <a name="enrollment"></a>등록

등록은 자동 프로비전을 통해 등록될 수 있는 디바이스 또는 디바이스 그룹의 레코드입니다. 등록 레코드에는 다음을 포함하여 디바이스 또는 디바이스 그룹에 대한 정보가 들어 있습니다.
- 디바이스에서 사용되는 [증명 메커니즘](#attestation-mechanism)
- 원하는 초기 구성(선택 사항)
- 원하는 IoT Hub
- 원하는 디바이스 ID

Device Provisioning 서비스에는 다음 두 가지 유형의 등록을 지원합니다.

### <a name="enrollment-group"></a>등록 그룹

등록 그룹은 특정 증명 메커니즘을 공유하는 디바이스의 그룹입니다. 등록 그룹은 X.509뿐만 아니라 대칭을 모두 지원합니다. X.509 등록 그룹의 모든 디바이스는 동일한 루트 또는 중간 CA(인증 기관)에서 서명한 X.509 인증서를 제공합니다. 대칭 키 등록 그룹의 각 디바이스는 그룹 대칭 키에서 파생된 SAS 토큰을 제공합니다. 등록 그룹 이름 및 인증서 이름은 영숫자, 소문자여야 하며 하이픈을 포함할 수 있습니다.

> [!TIP]
> 원하는 초기 구성을 공유하는 다수의 디바이스 또는 동일한 테넌트로 이동하는 디바이스에 대한 등록 그룹을 사용하는 것이 좋습니다.

### <a name="individual-enrollment"></a>개별 등록

개별 등록은 등록할 수 있는 단일 디바이스에 대한 항목입니다. 개별 등록은 증명 메커니즘으로 X.509 리프 인증서 또는 SAS 토큰(실제 또는 가상 TPM) 중 하나를 사용할 수 있습니다. 개별 등록의 등록 ID는 영숫자, 소문자이며 하이픈을 포함할 수 있습니다. 개별 등록은 지정된 원하는 IoT Hub 디바이스 ID가 있을 수 있습니다.

> [!TIP]
> 고유한 초기 구성이 필요한 디바이스 또는 TPM 증명을 통해 SAS 토큰만으로 인증될 수 있는 디바이스의 경우 개별 등록을 사용하는 것이 좋습니다.


## <a name="attestation-mechanism"></a>증명 메커니즘

증명 메커니즘은 디바이스의 ID 확인에 사용되는 방법입니다. 증명 메커니즘은 등록 항목에 대해 구성되며 등록하는 동안 디바이스 ID를 확인할 때 사용할 방법을 프로비전 서비스에 알립니다.

> [!NOTE]
> IoT Hub는 해당 서비스에서 비슷한 개념에 대해 "인증 체계"를 사용합니다.

Device Provisioning Service는 다음 형식의 증명을 지원합니다.
* 표준 X.509 인증서 인증 흐름을 기반으로 하는 **X.509 인증서** 자세한 내용은 [X.509 증명](concepts-x509-attestation.md)을 참조하세요.
* nonce 문제를 기반으로 하는 **TPM(신뢰할 수 있는 플랫폼 모듈)** 은 서명된 SAS(공유 액세스 서명) 토큰을 표시하기 위해 키에 TPM 표준을 사용합니다. 이를 사용하는 경우 디바이스에 실제 TPM이 필요하지는 않지만, [TPM 사양](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)에 따라 인증 키를 사용하여 증명해야 합니다. 자세한 내용은 [TMP 증명](concepts-tpm-attestation.md)을 참조하세요.
* 해시된 서명과 포함된 만료 날짜가 들어 있는 SAS(공유 액세스 서명) [보안 토큰](../iot-hub/iot-hub-dev-guide-sas.md#security-tokens)을 기반으로 하는 **대칭 키**. 자세한 내용은 [대칭 키 증명](concepts-symmetric-key-attestation.md)을 참조하세요.


## <a name="hardware-security-module"></a>하드웨어 보안 모듈

하드웨어 보안 모듈 또는 HSM은 안전한 디바이스 비밀의 하드웨어 기반 스토리지를 위해 사용되며 가장 안전한 형태의 비밀 스토리지입니다. X.509 인증서 및 SAS 토큰은 HSM에 저장될 수 있습니다. HSM은 증명 메커니즘 및 프로비전 서비스 지원에 사용될 수 있습니다.

> [!TIP]
> 디바이스에 비밀을 안전하게 저장하는 데 디바이스와 함께 HSM을 사용하는 것이 좋습니다.

디바이스 비밀은 소프트웨어(메모리)에도 저장될 수 있지만 HSM보다 덜 안전한 형태의 스토리지입니다.



## <a name="id-scope"></a>ID 범위

ID 범위는 사용자에 의해 생성되고 디바이스가 등록할 특정 프로비전 서비스를 고유하게 식별하는 데 사용되는 경우 Device Provisioning Service에 할당됩니다. ID 범위는 서비스에 의해 생성되고 변경할 수 없으며 고유성이 보장됩니다.

> [!NOTE]
> 고유성은 장기 실행되는 배포 작업 및 병합 및 취득 시나리오에 중요합니다.


## <a name="registration"></a>등록

등록은 Device Provisioning 서비스를 통해 IoT Hub에 성공적으로 등록/프로비전하는 디바이스의 레코드입니다. 등록 레코드는 자동으로 생성됩니다. 삭제될 수 있지만 업데이트할 수 없습니다.


## <a name="registration-id"></a>등록 ID

등록 ID는 Device Provisioning Service를 사용한 디바이스 등록을 고유하게 식별하는 데 사용됩니다. 디바이스 ID는 프로비전 서비스 [ID 범위](#id-scope)에서 고유해야 합니다. 각 디바이스에는 등록 ID가 있어야 합니다. 등록 ID는 영숫자로 대/소문자를 구분하지 않고, 콜론, 마침표, 밑줄, 하이픈 등의 특수 문자를 포함할 수 있습니다.

* TPM의 경우 TPM 자체에서 등록 ID를 제공합니다.
* X.509 기반 증명의 경우 등록 ID는 인증서의 주체 이름으로 제공됩니다.

## <a name="device-id"></a>디바이스 ID

디바이스 ID는 IoT Hub에 표시되는 ID입니다. 등록 항목에서 원하는 디바이스 ID를 설정할 수 있지만 설정할 필요가 없습니다. 원하는 디바이스 ID 설정은 개별 등록에서만 지원됩니다. 등록 목록에 원하는 디바이스 ID가 지정되지 않은 경우 등록 ID는 디바이스를 등록할 때 디바이스 ID로 사용됩니다. [IoT Hub의 디바이스 ID](../iot-hub/iot-hub-devguide-identity-registry.md)에 대해 자세히 알아봅니다.



## <a name="operations"></a>작업

작업은 Device Provisioning 서비스의 대금 청구 단위입니다. 하나의 작업은 서비스에 대한 하나의 명령의 성공적인 완료입니다. 작업에는 디바이스 등록 및 재등록이 포함되며, 등록 목록 항목 추가, 등록 목록 항목 업데이트 같은 서비스 쪽 변경도 포함됩니다.
