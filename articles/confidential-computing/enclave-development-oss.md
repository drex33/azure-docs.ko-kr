---
title: Azure 기밀 컴퓨팅에서 오픈 소스 솔루션을 사용 하 여 응용 프로그램 enclaves 개발
description: 도구를 사용 하 여 Azure 기밀 컴퓨팅을 위한 Intel SGX 응용 프로그램을 개발 하는 방법을 알아봅니다.
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 71be688b3f69860468ce6c638e484caae0ba72f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290213"
---
# <a name="open-source-solutions-to-build-enclave-applications"></a>Enclave 응용 프로그램을 빌드하기 위한 오픈 소스 솔루션

이 문서에서는 응용 프로그램 enclaves를 사용 하는 응용 프로그램을 빌드하기 위한 오픈 소스 솔루션을 설명 합니다. 읽기 전에 [enclave 응용 프로그램](application-development.md) 개념 페이지를 확인 해야 합니다. 

## <a name="intel-sgx-compatible-tools"></a>Intel SGX-Compatible 도구
Azure  [는 AZURE SGX (Software Guard Extensions)를 사용 하는 기밀 가상 머신을](virtual-machine-solutions-sgx.md)통해 응용 프로그램 enclaves을 제공 합니다. Intel SGX 가상 컴퓨터를 배포한 후에는 응용 프로그램을 "enclave 인식" 하도록 만드는 특수 도구가 필요 합니다. 이러한 방식으로 신뢰할 수 있는 코드와 신뢰할 수 없는 코드 부분을 모두 포함 하는 응용 프로그램을 빌드할 수 있습니다. 

예를 들어 다음과 같은 오픈 소스 프레임워크를 사용할 수 있습니다. 

- [OE (Open Enclave) SDK (소프트웨어 개발 키트)](#oe-sdk)
- [EGo SDK](#ego)
- [Intel SGX SDK](#intel-sdk)
- [CCF(기밀 컨소시엄 프레임워크)](#ccf)

새 응용 프로그램 코드를 작성 하지 않으려는 경우 [기밀 컨테이너 조력자](confidential-containers.md) 를 사용 하 여 컨테이너 화 된 응용 프로그램을 래핑할 수 있습니다.

### <a name="open-enclave-software-development-kit-oe-sdk"></a>OE SDK(Open Enclave 소프트웨어 개발 키트) <a id="oe-sdk"></a>

enclave에서 실행되는 코드를 작성하려면 공급자를 통해 지원되는 라이브러리 또는 프레임워크를 사용합니다. [OE SDK(Open Enclave SDK)](https://github.com/openenclave/openenclave)는 다양한 다른 기밀 컴퓨팅 사용 하드웨어를 추상화할 수 있는 오픈 소스 SDK입니다. 

OE SDK는 모든 CSP의 모든 하드웨어에서 단일 추상화 계층으로 빌드됩니다. OE SDK는 Azure 기밀 컴퓨팅 가상 머신에서 사용되어 enclave를 기반으로 하는 애플리케이션을 만들고 실행할 수 있습니다. Open Enclave 리포지토리는 Microsoft에서 유지 관리 합니다.

### <a name="ego-software-development-kit"></a>EGo 소프트웨어 개발 키트<a id="ego"></a>

[EGo](https://ego.dev/)는 enclave 내에서 Go 프로그래밍 언어로 작성된 애플리케이션을 실행할 수 있게 해주는 오픈 소스 SDK입니다. EGo는 OE SDK를 기반으로 하며 증명 및 봉인을 위한 enclave 내 Go 라이브러리와 함께 제공됩니다. 많은 기존 Go 애플리케이션은 수정 없이 EGo에서 실행됩니다.  

### <a name="intel-sgx-software-development-kit"></a>Intel SGX 소프트웨어 개발 키트 <a id="intel-sdk"></a>
Intel [SGX SDK](https://01.org/intel-softwareguard-extensions) 는 INTEL의 sgx 팀에서 개발 하 고 유지 관리 합니다. SDK는 소프트웨어 개발자가 C/c + +에서 Intel SGX 사용 응용 프로그램을 만들고 디버그할 수 있도록 하는 컬렉션 도구입니다.

### <a name="confidential-consortium-framework-ccf"></a>CCF(기밀 컨소시엄 프레임워크) <a id="ccf"></a>

[Ccf](https://www.microsoft.com/research/project/confidential-consortium-framework/)( [기밀 컨소시엄 framework](https://www.microsoft.com/research/project/confidential-consortium-framework/) )는 분산 blockchain 프레임 워크의 한 예입니다. CCF는 Azure 기밀 컴퓨팅을 기반으로 빌드됩니다. Microsoft Research에서 Spearheaded이 프레임 워크는 TEEs (신뢰할 수 있는 실행 환경)의 강력한 기능을 사용 하 여 증명에 대 한 원격 enclaves의 네트워크를 만듭니다. 노드는 Azure Intel SGX virtual machines에서 실행할 수 있으며 enclave 인프라를 활용할 수 있습니다. 블록체인 사용자는 증명 프로토콜을 통해 하나의 CCF 노드에 대한 무결성을 확인하고 전체 네트워크를 효과적으로 확인할 수 있습니다.

CCF에서 분산 원장은 모든 네트워크 노드에서 복제되는 키-값 저장소에 기록된 변경 내용으로 구성됩니다. 이러한 각 노드는 TLS를 통해 블록체인 사용자가 트리거할 수 있는 트랜잭션 엔진을 실행합니다. 엔드포인트가 트리거되면 키-값 저장소를 변경할 수 있습니다. 암호화 된 변경이 분산 원장에 기록 되기 전에 계약에 도달 하려면 둘 이상의 노드에서 합의 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [기밀 컴퓨팅 Intel SGX 가상 머신 배포](quick-create-portal.md)
