---
title: SGX enclaves 증명
description: 기밀 컴퓨팅 SGX enclave 증명을 사용 하 여 안전 한지 확인 합니다.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2020
ms.author: JenCook
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9672a40ac7008b60195b07b6a077ed5bac0eb1b4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011449"
---
# <a name="attestation-for-sgx-enclaves"></a>SGX Enclaves 증명

Azure의 기밀 컴퓨팅은 코드 또는 데이터의 일부를 격리할 수 있는 Intel SGX 기반 가상 머신을 제공 합니다. 이러한 [enclaves](confidential-computing-enclaves.md)작업할 때 신뢰할 수 있는 환경이 안전한 지 확인 하 고 유효성을 검사할 수 있습니다. 이 확인은 증명 프로세스입니다. 

## <a name="overview"></a>개요 

증명을 사용 하면 신뢰 당사자가 해당 소프트웨어의 신뢰성을 높일 수 있습니다.

1. Enclave에서 실행
1. 최신
1. 안전

예를 들어 enclave는 기본 하드웨어를 요청 하 여 자격 증명을 생성할 수 있습니다. 이 자격 증명은 플랫폼에 enclave 있는지 증명을 포함 합니다. 두 번째 enclave는 동일한 플랫폼이 보고서를 생성 했는지를 수신 하 고 확인할 수 있습니다.

![데이터 및 응용 프로그램 코드를 포함 하는 enclave를 사용 하 여 클라이언트의 보안 교환을 보여 주는 증명 프로세스의 다이어그램입니다.](media/attestation/attestation.png)

시스템 소프트웨어 및 실리콘과 호환 되는 보안 증명 서비스를 사용 하 여 증명을 구현 합니다. 예:

- [Microsoft Azure Attestation](../attestation/overview.md) 
- [Intel의 증명 및 프로비저닝 서비스](https://software.intel.com/sgx/attestation-services)


두 서비스 모두 Azure 기밀 컴퓨팅 Intel SGX DCsv2 시리즈 Vm과 호환 됩니다. DCsv3 시리즈 및 DCdsv3 시리즈 Vm은 Intel 증명 서비스와 호환 되지 않습니다. 

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [Microsoft Azure Enclave 인식 앱에 대 한 증명 샘플](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)
