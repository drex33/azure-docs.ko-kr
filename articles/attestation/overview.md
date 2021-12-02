---
title: Azure Attestation 개요
description: TEE(신뢰할 수 있는 실행 환경)를 증명하기 위한 솔루션인 Microsoft Azure Attestation에 대한 개요입니다.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: fad075959eb85566f5e6312d02b32afeefa18546
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133436098"
---
# <a name="microsoft-azure-attestation"></a>Microsoft Azure Attestation 

Microsoft Azure Attestation은 플랫폼의 신뢰성과 플랫폼 내부에서 실행되는 이진 파일의 무결성을 원격으로 확인하기 위한 통합 솔루션입니다. 이 서비스는 [Intel® SGX(Software Guard Extensions)](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) enclave 및 [VBS(가상화 기반 보안)](/windows-hardware/design/device-experiences/oem-vbs) enclave와 같은 TEE(Trusted Execution Environment) 상태를 증명하는 기능과 함께 TPM(Trusted Platform Module)이 지원하는 플랫폼 증명을 지원합니다. 

증명은 소프트웨어 이진 파일이 신뢰할 수 있는 플랫폼에서 올바르게 인스턴스화되었음을 보여주는 프로세스입니다. 그러면 원격 신뢰 당사자가 이러한 의도된 소프트웨어만 신뢰할 수 있는 하드웨어에서 실행된다고 확신할 수 있습니다. Azure Attestation은 증명을 위한 통합 고객 지향 서비스 및 프레임워크입니다.

Azure Attestation을 사용하면 [Azure 기밀 컴퓨팅](../confidential-computing/overview.md) 및 인텔리전트 에지 보호와 같은 최첨단 보안 패러다임을 사용할 수 있습니다. 고객은 컴퓨터의 위치, 해당 컴퓨터의 VM(가상 머신) 상태 및 해당 VM에서 enclave가 실행되는 환경을 독립적으로 확인할 수 있는 기능을 요청했습니다. Azure Attestation은 이러한 고객과 많은 추가적인 고객 요청에 권한을 부여합니다.

Azure Attestation은 컴퓨팅 엔터티로부터 증거를 받고, 이를 클레임 세트로 전환하며, 구성 가능한 정책에 대해 유효성을 검사하고, 클레임 기반 애플리케이션(예: 신뢰 당사자 및 감사 기관)에 대한 암호화 증명을 생성합니다.

## <a name="use-cases"></a>사용 사례

Azure Attestation은 여러 환경 및 고유한 사용 사례에 대한 포괄적인 증명 서비스를 제공합니다.

### <a name="sgx-attestation"></a>SGX 증명

SGX는 특정 Intel CPU 모델에서 지원되는 하드웨어 등급 격리를 나타냅니다. SGX를 사용하면 코드를 SGX enclave라는 삭제된 구획에서 실행할 수 있습니다. 그런 다음, 하드웨어에서 액세스 및 메모리 권한을 관리하여 적절한 격리를 통해 공격 노출 영역을 최소화합니다.

클라이언트 애플리케이션은 보안에 중요한 작업을 SGX enclave 내에서 수행하도록 위임하여 이러한 enclave를 활용하도록 설계할 수 있습니다. 그런 다음, 이러한 애플리케이션은 Azure Attestation을 사용하여 enclave에서 정기적으로 트러스트를 설정하고 중요한 데이터에 액세스할 수 있습니다.

Intel® Xeon® 스케일러블 프로세서는 [ECDSA 기반 증명 솔루션](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/attestation-services.html#Elliptic%20Curve%20Digital%20Signature%20Algorithm%20(ECDSA)%20Attestation)을 사용한 SGX enclave 원격 증명만 지원합니다. ECDSA 기반 증명 모델을 활용하여 Azure Attestation은 Intel® Xeon® E3 프로세서 및 Intel® Xeon® 스케일러블 프로세서 기반 서버 플랫폼의 유효성 검사를 지원합니다. 

> [!NOTE]
> Azure Attestation을 사용하여 Intel® Xeon® 스케일러블 프로세서 기반 서버 플랫폼 증명을 수행하려면 사용자가 [Azure DCAP 버전 1.10.0](https://github.com/microsoft/Azure-DCAP-Client) 이상을 설치해야 합니다.

### <a name="open-enclave"></a>Open Enclave
[OE(Open Enclave)](https://openenclave.io/sdk/)는 개발자가 TEE 기반 애플리케이션을 빌드하는 데 사용할 수 있는 단일 통합 enclave 추상화를 만들기 위한 라이브러리의 컬렉션입니다. 플랫폼 특정성을 최소화하는 범용 보안 앱 모델을 제공합니다. Microsoft는 이를 SGX와 같은 하드웨어 기반 enclave 기술을 대중화하고 Azure에서 더 많이 활용할 수 있도록 하기 위한 필수적인 디딤돌로 보고 있습니다.

OE는 enclave 증거를 확인하기 위한 특정 요구 사항을 표준화합니다. 이를 통해 OE는 Azure Attestation의 매우 적합한 증명 소비자로 인정됩니다.

### <a name="tpm-attestation"></a>TPM 증명 

TPM(신뢰할 수 있는 플랫폼 모듈) 기반 증명은 플랫폼의 상태를 증명하는 데 중요합니다. TPM은 신뢰의 루트이자 보안 보조 프로세서의 역할을 하여 측정(증명 정보)에 암호화 유효성을 제공합니다. TPM이 있는 디바이스는 증명을 사용하여 부팅 중 기능 상태를 검색하기 위해 클레임을 사용하는 것과 함께 부팅 무결성이 손상되지 않았음을 증명할 수 있습니다. 

클라이언트 애플리케이션은 보안에 중요한 작업을 플랫폼이 안전한 것으로 확인된 후에만 수행되도록 위임하여 TPM 증명을 활용하도록 디자인할 수 있습니다. 그런 다음, 이러한 애플리케이션은 Azure Attestation을 사용하여 플랫폼에서 정기적으로 트러스트를 설정하고 중요한 데이터에 액세스할 수 있습니다.

## <a name="azure-attestation-can-run-in-a-tee"></a>TEE에서 실행 가능한 Azure Attestation

Azure Attestation은 다음 작업을 수행하므로 기밀 컴퓨팅 시나리오에 매우 중요합니다.

- enclave 증거가 유효한지 확인합니다.
- 고객이 정의한 정책에 대해 enclave 증거를 평가합니다.
- 테넌트별 정책을 관리하고 저장합니다.
- 신뢰 당사자가 enclave와 상호 작용하는 데 사용하는 토큰을 생성하고 서명합니다.

Azure Attestation은 다음 두 가지 유형의 환경에서 실행되도록 빌드됩니다.
- SGX를 사용하도록 설정된 TEE에서 실행되는 Azure Attestation
- 비 TEE에서 실행되는 Azure Attestation

Azure Attestation 고객은 Microsoft에서 운영상 TCB(신뢰할 수 있는 컴퓨팅 기반)를 벗어나야 한다는 요구 사항을 표명했습니다. 이는 VM 관리자, 호스트 관리자 및 Microsoft 개발자와 같은 Microsoft 엔터티에서 증명 요청, 정책 및 Azure Attestation 발급 토큰을 수정하지 못하도록 방지하기 위한 것입니다. 또한 Azure Attestation은 따옴표 유효성 검사, 토큰 생성 및 토큰 서명과 같은 Azure Attestation의 기능이 SGX enclave로 이동되는 TEE에서 실행되도록 빌드됩니다.

## <a name="why-use-azure-attestation"></a>Azure Attestation을 사용하는 이유

Azure Attestation은 다음과 같은 이점을 제공하므로 TEE를 증명하는 데 기본적으로 선택됩니다. 

- TPM, SGX enclave 및 VBS enclave와 같은 여러 환경을 증명하기 위한 통합 프레임워크 
- 사용자 지정 증명 공급자를 만들고 정책을 구성하여 토큰 생성을 제한할 수 있음
- 사용자의 구성 없이 증명할 수 있는 지역 공유 공급자를 제공합니다.
- SGX enclave에서 구현을 통해 사용하는 동안 데이터를 보호합니다.
- 고가용성 서비스 

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>BCDR(비즈니스 연속성 및 재해 복구) 지원

Azure Attestation용 [BCDR(비즈니스 연속성 및 재해 복구)](../availability-zones/cross-region-replication-azure.md)을 사용하면 지역의 심각한 가용성 문제 또는 재해 이벤트로 인한 서비스 중단을 완화할 수 있습니다.

두 지역에 배포된 클러스터는 정상적인 환경에서 독립적으로 작동합니다. 한 지역에서 오류가 발생하거나 중단되는 경우 수행되는 작업은 다음과 같습니다.

- Azure Attestation BCDR은 고객이 복구하기 위해 추가 단계를 수행할 필요가 없는 원활한 장애 조치를 제공합니다.
- 지역의 [Azure Traffic Manager](../traffic-manager/index.yml)는 상태 프로브가 저하되었음을 감지하고 엔드포인트를 쌍으로 연결된 지역으로 전환합니다.
- 기존 연결이 작동하지 않고 내부 서버 오류 또는 시간 제한 문제가 발생합니다.
- 모든 컨트롤 플레인 작업이 차단됩니다. 고객은 주 지역에서 증명 공급자를 만들 수 없습니다.
- 증명 호출 및 정책 구성을 비롯한 모든 데이터 평면 작업은 보조 지역에서 제공됩니다. 고객은 주 지역에 해당하는 원래 URI를 사용하여 데이터 평면 작업을 계속할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Azure Attestation 기본 개념](basic-concepts.md)에 대해 알아보기
- [증명 정책을 작성하고 서명하는 방법](author-sign-policy.md)
- [PowerShell을 사용하여 Azure Attestation 설정](quickstart-powershell.md)
