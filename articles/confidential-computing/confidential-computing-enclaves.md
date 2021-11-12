---
title: SGX enclaves를 사용 하 여 빌드-Azure Virtual Machines
description: 기밀 컴퓨팅 워크로드를 사용할 수 있게 하는 Intel SGX 하드웨어에 대해 알아봅니다.
author: stempesta
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3ac8c9ddced2fd37c829277c925b8953e7c71151
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310018"
---
# <a name="build-with-sgx-enclaves"></a>SGX enclaves를 사용 하 여 빌드 

Azure 기밀 컴퓨팅 [은 DCsv2 시리즈](../virtual-machines/dcv2-series.md) 및 [DCsv3/DCdsv3 시리즈](../virtual-machines/dcv3-series.md)* vm (가상 머신)을 제공 합니다. 이러한 Vm에는 [Intel® 소프트웨어 보호 확장 (SGX)](https://intel.com/sgx)이 있습니다. 

Intel SGX 기술을 통해 고객은 데이터를 보호 하 고 CPU가 데이터를 처리 하는 동안 데이터를 암호화 하는 enclaves을 만들 수 있습니다. OS (운영 체제) 및 하이퍼바이저는 데이터에 액세스할 수 없습니다. 또한 물리적 액세스 권한이 있는 데이터 센터 관리자는 데이터에 액세스할 수 없습니다.

## <a name="enclaves-concept"></a>Enclaves 개념

Enclaves는 하드웨어 프로세서와 메모리의 보안 부분입니다. 디버거를 사용 하는 경우에도 enclave 내에서 데이터 또는 코드를 볼 수 없습니다. 신뢰할 수 없는 코드가 enclave 메모리의 콘텐츠를 변경 하려고 하는 경우에는 SGX에서 환경을 사용 하지 않도록 설정 하 고 작업을 거부 합니다. 이러한 고유 기능을 통해 암호화 되지 않은 암호를 안전 하 게 보호할 수 있습니다.  

![Enclaves에서 보안이 유지 되는 데이터를 보여 주는 VM 모델의 다이어그램입니다.](media/overview/hardware-backed-enclave.png)

Enclave을 보안 lockbox로 간주 합니다. 암호화 되지 않은 코드와 데이터를 lockbox 내에 배치 합니다. 외부에서 어떤 것도 볼 수 없습니다. Enclave에 데이터 암호를 해독 하는 키를 제공 합니다. Enclave는 데이터를 다시 보내기 전에 데이터를 처리 하 고 다시 암호화 합니다.

각 enclave에는 설정 된 크기의 EPC (암호화 된 페이지 캐시)가 있습니다. EPC은 enclave에서 보유할 수 있는 메모리의 양을 결정 합니다. [DCsv2 시리즈](../virtual-machines/dcv2-series.md) Vm은 최대 168 MiB를 보유 합니다. [DCsv3/DCdsv3 시리즈](../virtual-machines/dcv3-series.md)* vm은 메모리를 많이 사용 하는 워크 로드를 위해 최대 256 GB를 보유 합니다.

> [!NOTE]
> * DCsv3 및 DCdsv3는 2021 년 11 월 1 일에 **공개 미리 보기로** 제공 됩니다.

자세한 내용은 [하드웨어 기반의 신뢰할 수 있는 enclaves를 사용 하 여 INTEL SGX vm을 배포 하는 방법](virtual-machine-solutions-sgx.md)을 참조 하세요.

## <a name="developing-for-enclaves"></a>Enclaves 용 개발

[Enclaves에서 실행 되는 응용 프로그램을 개발 하는 데 다양 한 소프트웨어 도구](application-development.md)를 사용할 수 있습니다. 이러한 도구는 코드와 데이터의 일부를 enclave 내에 보호 하는 데 도움이 됩니다. 트러스트 된 환경 외부의 아무도 이러한 도구로 데이터를 보거나 수정할 수 있도록 합니다.

## <a name="next-steps"></a>다음 단계
- [DCsv2 또는 DCsv3/DCdsv3 시리즈 가상 머신 배포](quick-create-portal.md)
- OE SDK를 사용하여 [enclave 인식 애플리케이션 개발](application-development.md)
