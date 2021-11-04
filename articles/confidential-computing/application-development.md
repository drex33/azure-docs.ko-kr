---
title: Azure 기밀 컴퓨팅 개발 도구
description: 도구 및 라이브러리를 사용하여 Intel SGX에서 기밀 컴퓨팅용 애플리케이션 개발
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: JenCook
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6300e0cee2659f767c2d765de5a24591fc962e6f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131081318"
---
# <a name="application-enclave-development"></a>애플리케이션 enclave 개발 

Azure 기밀 컴퓨팅을 사용하면 Intel SGX(Software Guard Extensions)를 실행하는 VM(가상 머신)에 대한 애플리케이션 Enclave를 만들 수 있습니다. 개발을 시작하기 전에 관련 도구와 소프트웨어를 이해하는 것이 중요합니다.

> [!NOTE]
> [Intel SGX VM 및 enclave에 대한 소개를](confidential-computing-enclaves.md)아직 읽지 않은 경우 계속하기 전에 이 작업을 수행합니다.

## <a name="application-enclaves"></a>애플리케이션 enclave

애플리케이션 enclave는 특정 코드와 데이터를 보호하는 격리된 환경입니다. enclave를 만들 때 enclave 내에서 실행되는 애플리케이션의 부분을 결정해야 합니다. enclave를 만들거나 관리하는 경우 선택한 배포 스택에 호환되는 SDK 및 프레임워크를 사용해야 합니다. 

기밀 컴퓨팅은 현재 애플리케이션 enclave를 제공합니다. 특히 [Intel SGX가 활성화된 기밀 VM을](virtual-machine-solutions-sgx.md)사용하여 애플리케이션 Enclave를 사용하여 배포하고 개발할 수 있습니다. 

## <a name="intel-sgx"></a>Intel SGX

Intel SGX 기술을 사용하면 액세스할 수 없는 키를 CPU 내에 저장하여 애플리케이션 enclave 또는 신뢰할 수 있는 실행 환경을 암호화할 수 있습니다. enclave 내의 코드 및 데이터의 암호 해독은 프로세서 내에서 발생합니다. CPU만 액세스할 수 있습니다. 이 수준의 격리는 사용 중 데이터를 보호하고 하드웨어 및 소프트웨어 공격으로부터 보호합니다. 자세한 내용은 Intel [SGX 웹 사이트 를 참조하세요.](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) 

Azure는 DC 시리즈의 다양한 VM 크기를 통해 가상화 환경에서 Intel SGX를 제공합니다. 여러 VM 크기를 사용하면 다양한 EPC(Enclave 페이지 캐시) 크기를 사용할 수 있습니다. EPC는 해당 VM의 enclave에 대한 최대 메모리 영역 크기입니다. 현재 Intel SGX VM은 [DCsv2 시리즈](../virtual-machines/dcv2-series.md) VM 및 [DCsv3/DCdsv3 시리즈](../virtual-machines/dcv3-series.md) VM에서 사용할 수 있습니다.


### <a name="developing-applications"></a>애플리케이션 개발

enclave를 사용하여 빌드된 애플리케이션에는 두 개의 파티션이 있습니다. 

**호스트는** "신뢰할 수 없는" 구성 요소입니다. enclave 애플리케이션은 호스트 위에서 실행됩니다. 호스트는 신뢰할 수 없는 환경입니다. 호스트에 enclave 코드를 배포하는 경우 호스트는 해당 코드에 액세스할 수 없습니다.

**enclave는** "신뢰할 수 있는" 구성 요소입니다. 애플리케이션 코드와 캐시된 데이터 및 메모리는 enclave에서 실행됩니다. enclave 환경은 비밀 및 중요한 데이터를 보호합니다. Enclave에서 보안 계산이 발생하는지 확인합니다.

![호스트 및 enclave 파티션을 보여 주는 애플리케이션의 다이어그램. enclave 내부에는 데이터 및 애플리케이션 코드 구성 요소가 있습니다.](media/application-development/oe-sdk.png)

enclave 및 격리된 환경의 강력한 성능을 사용하려면 기밀 컴퓨팅을 지원하는 도구를 선택합니다. 다양한 도구는 enclave 애플리케이션 개발을 지원합니다. 예를 들어 다음과 같은 오픈 소스 프레임워크를 사용할 수 있습니다. 

- [OE SDK(Open Enclave 소프트웨어 개발 키트)](enclave-development-oss.md#oe-sdk)
- [The Intel SGX SDK](enclave-development-oss.md#intel-sdk)
- [EGo 소프트웨어 개발 키트](enclave-development-oss.md#ego)
- [CCF(기밀 컨소시엄 프레임워크)](enclave-development-oss.md#ccf)

애플리케이션을 디자인할 때 enclave에서 실행해야 하는 부분을 식별하고 결정합니다. 신뢰할 수 있는 구성 요소의 코드는 애플리케이션의 나머지 부분과 격리됩니다. enclave가 초기화되고 코드가 메모리에 로드된 후에는 신뢰할 수 없는 구성 요소가 해당 코드를 읽거나 변경할 수 없습니다.

## <a name="next-steps"></a>다음 단계 

- [기밀 컴퓨팅 Intel SGX VM 배포](quick-create-portal.md)
- [오픈 소스 소프트웨어를 통해 애플리케이션 개발 시작](enclave-development-oss.md)
