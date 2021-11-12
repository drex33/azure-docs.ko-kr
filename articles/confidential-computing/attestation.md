---
title: SGX Enclave에 대한 증명
description: 증명을 통해 기밀 컴퓨팅 SGX Enclave가 안전한지 확인합니다.
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/01/2020
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 03a389facebbfcb242cf63afd0a8fcca6ad7b670
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347673"
---
# <a name="attestation-for-sgx-enclaves"></a>SGX Enclave에 대한 증명

Azure의 기밀 컴퓨팅은 코드 또는 데이터의 일부를 격리할 수 있는 Intel SGX 기반 가상 머신을 제공합니다. 이러한 [enclave를](confidential-computing-enclaves.md)사용할 때 신뢰할 수 있는 환경이 안전한지 확인하고 유효성을 검사할 수 있습니다. 이 확인은 증명 프로세스입니다. 

## <a name="overview"></a>개요 

증명을 사용하면 신뢰 당사자가 소프트웨어에 대한 신뢰도를 높일 수 있습니다.

1. Enclave에서 실행
1. 최신
1. 보안

예를 들어 enclave는 기본 하드웨어에 자격 증명을 생성하도록 요청할 수 있습니다. 이 자격 증명에는 enclave가 플랫폼에 있다는 증명이 포함됩니다. 두 번째 enclave는 동일한 플랫폼에서 보고서를 생성했는지를 수신하고 확인할 수 있습니다.

![데이터 및 애플리케이션 코드를 보유하는 enclave와 클라이언트의 보안 교환을 보여 주는 증명 프로세스 다이어그램.](media/attestation/attestation.png)

시스템 소프트웨어 및 실리콘과 호환되는 보안 증명 서비스를 통해 증명을 구현합니다. 예를 들면 다음과 같습니다.

- [Microsoft Azure Attestation](../attestation/overview.md) 
- [Intel의 증명 및 프로비저닝 서비스](https://software.intel.com/sgx/attestation-services)


두 서비스 모두 Azure 기밀 컴퓨팅 Intel SGX DCsv2 시리즈 VM과 호환됩니다. DCsv3 시리즈 및 DCdsv3 시리즈 VM은 Intel 증명 서비스와 호환되지 않습니다. 

## <a name="next-step"></a>다음 단계

> [!div class="nextstepaction"]
> [Microsoft Azure Enclave 인식 앱에 대한 증명 샘플](/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)
