---
title: SGX enclave를 통해 빌드 - Azure Virtual Machines
description: 기밀 컴퓨팅 워크로드를 사용할 수 있게 하는 Intel SGX 하드웨어에 대해 알아봅니다.
author: JenCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2021
ms.author: JenCook
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0870c0cc762078ec38e978c8dab4815ff37741a4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067606"
---
# <a name="build-with-sgx-enclaves"></a>SGX enclave를 통해 빌드 

Azure 기밀 컴퓨팅은 [DCsv2 시리즈](../virtual-machines/dcv2-series.md) 및 [DCsv3/DCdsv3 시리즈](../virtual-machines/dcv3-series.md)* VM(가상 머신)을 제공합니다. 이러한 VM에는 [Intel® Software Guard Extensions(SGX)](https://intel.com/sgx)가 있습니다. 

Intel SGX 기술을 사용하면 고객이 데이터를 보호하는 enclave를 만들고 CPU가 데이터를 처리하는 동안 데이터를 암호화된 상태로 유지할 수 있습니다. OS(운영 체제) 및 하이퍼바이저는 데이터에 액세스할 수 없습니다. 물리적 액세스 권한이 있는 데이터 센터 관리자도 데이터에 액세스할 수 없습니다.

## <a name="enclaves-concept"></a>Enclave 개념

Enclave는 하드웨어 프로세서 및 메모리의 보안 부분입니다. 디버거를 사용하더라도 enclave 내의 데이터 또는 코드를 볼 수 없습니다. 신뢰할 수 없는 코드가 enclave 메모리의 콘텐츠를 변경하려고 하면 SGX는 환경을 사용하지 않도록 설정하고 작업을 거부합니다. 이러한 고유한 기능을 사용하면 비밀에 명확하게 액세스할 수 로부터 보호할 수 있습니다.  

![enclave에서 보안이 유지되는 데이터를 보여 주는 VM 모델의 다이어그램.](media/overview/hardware-backed-enclave.png)

enclave를 보안 lockbox로 간주합니다. 암호화된 코드와 데이터를 lockbox 내에 배치합니다. 외부에서는 아무것도 볼 수 없습니다. enclave에 데이터의 암호를 해독하는 키를 제공합니다. enclave는 데이터를 다시 보내기 전에 데이터를 처리하고 다시 암호화합니다.

각 enclave에는 설정된 크기의 암호화된 EPC(페이지 캐시)가 있습니다. EPC는 enclave가 보유할 수 있는 메모리 양을 결정합니다. [DCsv2 시리즈](../virtual-machines/dcv2-series.md) VM은 최대 168 MiB를 보유합니다. [DCsv3/DCdsv3 시리즈](../virtual-machines/dcv3-series.md)* VM은 메모리를 많이 사용하는 워크로드를 위해 최대 256GB를 보유합니다.

> [!NOTE]
> *DCsv3 및 DCdsv3은 2021년 11월 1일부터 **공개 미리 보기로** 제공됩니다.

자세한 내용은 [하드웨어 기반 신뢰할 수 있는 enclave를 사용하여 Intel SGX VM을 배포하는 방법을 참조하세요.](virtual-machine-solutions-sgx.md)

## <a name="developing-for-enclaves"></a>enclave 개발

[Enclave에서 실행되는 애플리케이션을 개발하기 위해](application-development.md)다양한 소프트웨어 도구를 사용할 수 있습니다. 이러한 도구는 enclave 내에서 코드 및 데이터의 일부를 보호하는 데 도움이 됩니다. 신뢰할 수 있는 환경 외부의 누구도 이러한 도구를 사용하여 데이터를 보거나 수정할 수 없도록 합니다.

## <a name="next-steps"></a>다음 단계
- [DCsv2 또는 DCsv3/DCdsv3 시리즈 가상 머신 배포](quick-create-portal.md)
- OE SDK를 사용하여 [enclave 인식 애플리케이션 개발](application-development.md)
