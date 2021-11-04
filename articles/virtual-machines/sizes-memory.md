---
title: Azure VM 크기 - 메모리 | Microsoft Docs
description: Azure의 가상 머신에 사용할 수 있는 다양한 메모리 최적화 크기를 나열합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
services: virtual-machines
documentationcenter: ''
author: brbell
tags: azure-resource-manager,azure-service-management
keywords: VM 격리, 격리된 VM, 격리, 격리됨
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/20/2021
ms.author: brbell
ms.openlocfilehash: 50a187435b1a47fe2559ce5ce2d36905570b8419
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131456597"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>메모리 최적화 가상 머신 크기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

> [!TIP]
> 워크로드에 가장 적합한 다른 크기를 찾으려면 **[가상 머신 선택 도구](https://aka.ms/vm-selector)** 를 사용해 보세요.

메모리 최적화 VM 크기는 관계형 데이터베이스 서버, 중대형 캐시 및 메모리 내 분석에 적합한 높은 메모리 대 CPU 비율을 제공합니다. 이 문서에서는 이 그룹화에서 각 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.

- 원래 D 시리즈의 후속판인 [Dv2 및 DSv2 시리즈](dv2-dsv2-series-memory.md)는 더 강력한 CPU가 특징입니다. Dv2 시리즈는 D 시리즈보다 약 35% 빠릅니다. Intel Turbo Boost Technology 2.0과 함께 Intel&reg; Xeon &reg; 8171M 2.1GHz(Skylake) 또는 Intel&reg; Xeon&reg; E5-2673 v4 2.3GHz(Broadwell) 또는 Intel&reg; Xeon&reg; E5-2673 v3 2.4GHz(Haswell)에서 실행됩니다. Dv2 시리즈는 D 시리즈와 메모리 및 디스크 구성이 같습니다.

    Dv2 및 DSv2 시리즈는 더 빠른 vCPU와 더 좋은 임시 스토리지 성능을 요구하거나 더 높은 메모리 요구량을 가진 애플리케이션에 이상적입니다. 이들은 많은 엔터프라이즈급 애플리케이션을 위한 강력한 조합을 제공합니다.

- [Eav4 및 Easv4 시리즈](eav4-easv4-series.md)는 최대 256MB의 L3 캐시를 갖춘 다중 스레드 구성에서 AMD의 2.35Ghz EPYC<sup>TM</sup> 7452 프로세서를 활용하여 대부분의 메모리 최적화 워크로드 실행 옵션을 늘립니다. Eav4 시리즈와 Easv4 시리즈는 Ev3 및 Esv3 시리즈와 동일한 메모리 및 디스크 구성을 가집니다.

- [Ev3 및 Esv3 시리즈](ev3-esv3-series.md)는 하이퍼 스레드 구성에서 Intel&reg; Xeon&reg; 8171M 2.1GHz(Skylake) 또는 Intel&reg; Xeon&reg; E5-2673 v4 2.3GHz(Broadwell) 프로세서를 사용하며, 대부분의 범용 워크로드에 더 나은 가치 제안을 제공하고 Ev3을 다른 대부분 클라우드의 범용 VM과 맞춥니다. 하이퍼 스레딩으로 이동하기 위해 디스크 및 네트워크 제한이 코어 단위로 조정되는 동안 메모리가 확장되었습니다(7GiB/vCPU에서 8GiB/vCPU로). Ev3는 D/Dv2 제품군의 상위 메모리 VM 크기에 대한 후속 시리즈입니다.

- [Ev4 및 Esv4 시리즈](ev4-esv4-series.md)는 하이퍼 스레드 구성의 2세대 Intel&reg; Xeon&reg; Platinum 8272CL(Cascade Lake) 프로세서에서 실행되며, 다양한 메모리 집약적 엔터프라이즈 애플리케이션에 이상적이며, 최대 504GiB의 RAM을 제공합니다. [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) 및 [Intel&reg; Advanced Vector Extensions 512(Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)를 사용합니다. Ev4 및 Esv4 시리즈는 로컬 임시 디스크를 포함하지 않습니다. 자세한 내용은  [로컬 임시 디스크가 없는 AZURE VM 크기](azure-vms-no-temp-disk.yml)를 참조하세요.

- [Edv4 및 Edsv4 시리즈](edv4-edsv4-series.md)는 2세대 Intel&reg; Xeon&reg; 플래티넘 8272CL(Cascade Lake) 프로세서에서 실행되며 매우 큰 데이터베이스나 높은 vCPU 수와 많은 양의 메모리를 활용하는 기타 애플리케이션에 이상적입니다. 또한 이러한 VM 크기에는 대기 시간이 짧고 고속 로컬 스토리지를 활용하는 애플리케이션에 대한 빠르고 더 큰 로컬 SSD 스토리지가 포함됩니다. 3\.4GHz의 모든 코어 터보 클록 속도를 제공하며, [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) 및 [Intel&reg; Advanced Vector Extensions 512(Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)를 사용합니다.

- [Easv5 및 Eadsv5 시리즈는](easv5-eadsv5-series.md) 최대 256MB L3 캐시가 있는 다중 스레드 구성에서 AMD의 3세대 EPYC<sup>TM</sup> 7763v 프로세서를 활용하여 대부분의 메모리 최적화 워크로드를 실행하기 위한 고객 옵션을 증가합니다. 이러한 가상 머신은 관계형 데이터베이스 서버 및 메모리 내 분석 워크로드와 같은 대부분의 메모리 집약적 엔터프라이즈 애플리케이션과 관련된 요구 사항을 충족하기 위해 vC CPU와 메모리의 조합을 제공합니다. 

- [Edv5 및 Edsv5 시리즈는](edv5-edsv5-series.md) 하이퍼 &reg; 스레드 구성의 Intel Xeon &reg; 8272CL(Ice Lake) 프로세서에서 실행되며, 메모리 집약적인 다양한 엔터프라이즈 애플리케이션 및 최대 512 GiB RAM, [Intel Turbo Boost Technology &reg; 2.0, Intel](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)Hyper-Threading [ &reg; Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) 및 [Intel &reg; &reg; AVX-512(Intel Advanced Vector Extensions 512)에 적합합니다.](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html) 또한 [Intel&reg; Deep Learning Boost](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)를 지원합니다. 이 새로운 VM 크기는 [Gen2 VM](./generation-2.md)이 포함된 [Ev3/Esv3](./ev3-esv3-series.md)에 비해 로컬 스토리지가 50% 확장되고, 읽기 쓰기 로컬 디스크 IOPS가 향상됩니다. 3\.4GHz의 모든 코어 터보 클록 속도를 제공합니다.

- [Ev5 및 Esv5 시리즈는](ev5-esv5-series.md) 하이퍼 &reg; 스레드 구성의 Intel Xeon &reg; 8272CL(Ice Lake) 프로세서에서 실행되며, 메모리 집약적인 다양한 엔터프라이즈 애플리케이션에 적합하며 최대 512 GiB RAM 기능을 제공합니다. 3\.4GHz의 모든 코어 터보 클록 속도를 제공합니다.

- [M 시리즈](m-series.md)는 높은 vCPU 수(최대 128개의 vCPU)와 많은 양의 메모리(최대 3.8TiB)를 제공합니다. 높은 vCPU 수와 많은 양의 메모리를 활용하는 매우 큰 데이터베이스나 다른 애플리케이션에도 이상적입니다.

- [Mv2 시리즈](mv2-series.md)는 클라우드의 VM 중에서 가장 높은 vCPU 수(최대 416개의 vCPU)와 가장 큰 메모리(최대 11.4TiB)를 제공합니다. 높은 vCPU 수와 많은 양의 메모리를 활용하는 매우 큰 데이터베이스나 다른 애플리케이션에 이상적입니다.

Azure Compute는 특정 하드웨어 유형에서 격리되고 단일 고객 전용인 가상 머신 크기를 제공합니다. 이러한 가상 머신 크기는 규정 준수 및 규정 요구 사항과 같은 요소를 포함하는 작업에 대해 다른 고객으로부터 높은 수준의 격리가 필요한 작업에 가장 적합합니다. 고객은 [중첩된 가상 머신에 대한 Azure 지원](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)을 사용하여 이러한 격리된 가상 머신의 리소스를 보다 세분화할 수도 있습니다. 격리된 VM 옵션은 아래 가상 머신 제품군에 대한 페이지를 참조하세요.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.

Azure가 VM의 이름을 지정하는 방법에 대한 자세한 내용은 [Azure 가상 머신 크기 명명 규칙](./vm-naming-conventions.md)을 참조하세요.
