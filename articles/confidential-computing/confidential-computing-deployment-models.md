---
title: 배포 모델 중에서 선택
description: 배포 모델 중에서 선택
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/04/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8ed7f4b88e38581bea6eaf1a19ce945858031f1d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347692"
---
# <a name="confidential-computing-deployment-models"></a>기밀 컴퓨팅 배포 모델

Azure 기밀 컴퓨팅은 여러 배포 모델을 지원 합니다. 이러한 여러 모델은 최신 클라우드 컴퓨팅을 위한 다양 한 고객 보안 요구 사항을 지원 합니다.

## <a name="infrastructure-as-a-service-iaas"></a>IaaS(Infrastructure as a Service)

IaaS (Infrastructure as a Service)의 경우 기밀 컴퓨팅에서 기밀 Vm (가상 머신)을 사용할 수 있습니다. [Intel 소프트웨어 가드 확장 (SGX) 응용 프로그램 enclaves](confidential-computing-enclaves.md) 또는 AMD 심각도-.snp 기술을 기반으로 vm을 사용할 수 있습니다.

## <a name="platform-as-a-service-paas"></a>PaaS(Platform as a Service)

PaaS (Platform as a Service)의 경우 기밀 컴퓨팅에서 [기밀 컨테이너](confidential-containers.md) 를 사용할 수 있습니다. 이 제품에는 AKS (Azure Kubernetes Service)의 enclave 인식 컨테이너가 포함 됩니다.

올바른 배포 모델을 선택 하는 것은 많은 요인에 따라 다릅니다. 레거시 응용 프로그램, 운영 체제 기능 및 온-프레미스 네트워크에서의 마이그레이션의 존재를 고려해 야 할 수 있습니다.

Vm을 사용 하는 많은 이유가 있지만 컨테이너는 최신 IT의 많은 소프트웨어 환경에 대 한 추가 유연성을 제공 합니다. 

컨테이너는 다음과 같은 앱을 지원할 수 있습니다.

- 여러 클라우드에서를 실행 합니다.
- 마이크로 서비스에 커넥트 합니다.
- 다양 한 프로그래밍 언어 및 프레임 워크를 사용 합니다.
- CI/CD (지속적인 통합 및 지속적인 배포) 구현을 비롯 하 여 자동화 및 Azure Pipelines을 사용 합니다.

또한 컨테이너는 Azure 클라우드의 탄력성 적용 하 여 응용 프로그램의 이식성을 향상 시키고 리소스 사용을 개선 합니다.

일반적으로 다음과 같은 경우 기밀 Vm에 솔루션을 배포할 수 있습니다.

- 수정 하거나 컨테이너 화 된 수 없는 레거시 응용 프로그램이 있습니다. 그러나 데이터를 처리 하는 동안에도 메모리의 데이터를 보호 해야 합니다.
- 단일 인프라에서 다른 OS (운영 체제)를 필요로 하는 여러 응용 프로그램을 실행 하 고 있습니다.
- 모든 OS 리소스를 포함 하 여 전체 컴퓨팅 환경을 에뮬레이트 하려고 합니다.
- 온-프레미스에서 Azure로 기존 Vm을 마이그레이션하는 중입니다.

다음과 같은 경우 기밀 컨테이너 기반 방법을 선택할 수 있습니다.

- 비용 및 리소스 할당에 대해 우려 하 고 있습니다. 그러나 독점 앱 및 데이터 집합을 배포 하기 위한 더 민첩 한 플랫폼이 필요 합니다.
- 최신 클라우드 네이티브 솔루션을 구축 하 고 있습니다. 또한 소스 코드와 배포 프로세스를 완벽 하 게 제어할 수 있습니다.
- 다중 클라우드를 지원 해야 합니다.

두 옵션 모두 Azure 서비스에 가장 높은 보안 수준을 제공 합니다. [기밀 vm](#confidential-vms-on-amd-sev-snp) 및 [기밀 컨테이너](#secure-enclaves-on-intel-sgx) 의 보안 postures 다음과 같은 몇 가지 차이점이 있습니다.

### <a name="confidential-vms-on-amd-sev-snp"></a>AMD 심각도의 기밀 Vm-.SNP

**AMD 심각도-.snp의 기밀 vm** 은 호스트 관리자의 무단 액세스 로부터 전체 VM에 대 한 하드웨어 암호화 보호 기능을 제공 합니다. 일반적으로이 수준에는 CSP (클라우드 서비스 공급자)가 관리 하는 하이퍼바이저가 포함 됩니다. 이 유형의 기밀 VM을 사용 하 여 CSP가 VM 내에서 실행 되는 데이터 및 코드에 액세스 하지 못하도록 할 수 있습니다.

Vm 관리자 또는 VM 내에서 실행 중인 다른 모든 앱 또는 서비스는 보호 되는 경계를 넘어 작동 합니다. 이러한 사용자와 서비스는 VM 내의 데이터 및 코드에 액세스할 수 있습니다.

AMD 심각도-.SNP 기술은 하이퍼바이저에서 VM 격리를 제공 합니다. 하드웨어 기반 메모리 무결성 보호는 악의적인 하이퍼바이저 기반 공격을 방지 하는 데 도움이 됩니다. 심각도-.SNP 모델은 AMD Secure Processor 및 VM을 신뢰 합니다. 모델은 다른 하드웨어 및 소프트웨어 구성 요소를 신뢰 하지 않습니다. 신뢰할 수 없는 구성 요소에는 호스트 시스템의 BIOS와 하이퍼바이저가 포함 됩니다.

:::image type="content" source="media/confidential-computing-deployment-models/amd-sev-snp-vm.png" alt-text="신뢰할 수 있고 신뢰할 수 없는 구성 요소를 정의 하는 AMD 심각도-.SNP VM 아키텍처의 다이어그램입니다.":::

### <a name="secure-enclaves-on-intel-sgx"></a>Intel SGX에서 enclaves 보안

하드웨어 기반 암호화를 사용 하 여 VM 내 **에서 INTEL SGX** 보호 메모리 공간을 보호 하는 enclaves을 보호 합니다. Application enclaves의 보안 경계는 AMD 심각도-.SNP의 기밀 Vm 보다 더 제한적입니다. Intel SGX의 경우 보안 경계는 VM 내의 메모리 부분에 적용 됩니다. Intel SGX 기반 VM 내에서 실행 되는 사용자, 앱 및 서비스는 enclave 내에서 실행 중인 데이터 및 코드에 액세스할 수 없습니다.

Intel SGX는 응용 프로그램 격리에 사용 되는 데이터를 보호 합니다. 개발자는 선택한 코드와 데이터를 수정 되지 않도록 보호 함으로써 응용 프로그램 보안을 향상 시키기 위해 응용 프로그램을 강화 된 enclaves 또는 신뢰할 수 있는 실행 모듈로 분할할 수 있습니다. Enclave 외부 엔터티는 사용 권한 수준에 관계 없이 enclave 메모리를 읽거나 쓸 수 없습니다. 하이퍼바이저 또는 운영 체제는 일반적인 OS 수준 호출을 통해이 액세스를 가져올 수도 없습니다. Enclave 함수를 호출 하려면 Intel SGX Cpu에서 새로운 명령 집합을 사용 해야 합니다. 이 프로세스에는 몇 가지 보호 검사가 포함 되어 있습니다.

:::image type="content" source="media/confidential-computing-deployment-models/intel-sgx-enclave.png" alt-text="앱 enclave 내에 보안 정보를 표시 하는 Intel SGX enclaves 아키텍처의 다이어그램입니다.":::

## <a name="next-steps"></a>다음 단계

- [기밀 컨테이너에 대해 알아보기](confidential-containers.md)
- [기밀 컴퓨팅 enclaves에 대 한 자세한 정보](confidential-computing-enclaves.md)
