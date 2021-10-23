---
title: Azure VM 크기 - 범용 | Microsoft Docs
description: Azure의 가상 머신에 사용할 수 있는 다양한 범용 크기를 나열합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: 91071eeba17bab96a18c4f1a669d6ef42f1ac4e3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254085"
---
# <a name="general-purpose-virtual-machine-sizes"></a>범용 가상 머신 크기

**적용 대상:** :heavy_check_mark: Linux VMs :heavy_check_mark: Windows VMs :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

> [!TIP]
> 워크로드에 가장 적합한 다른 크기를 찾으려면 **[가상 머신 선택 도구](https://aka.ms/vm-selector)** 를 사용해 보세요.

범용 VM 크기는 적당한 CPU 대 메모리 비율을 제공합니다. 테스트 및 개발, 중소 규모 데이터베이스 및 트래픽이 적거나 중간 정도인 웹 서버에 적합합니다. 이 문서에서는 범용 컴퓨팅을 위한 제공 사항에 대한 정보를 제공합니다.

- 다양한 하드웨어 유형 및 프로세서에 [Av2 시리즈](av2-series.md) VM을 배포할 수 있습니다. A 시리즈 VM은 개발과 테스트 등 항목별 작업에 가장 적합한 CPU 성능 및 메모리 구성을 갖추고 있습니다. 배포된 하드웨어에 관계없이 인스턴스 실행 시 일관된 프로세서 성능을 제공하기 위해 하드웨어에 따라 크기가 제한됩니다. 이 크기가 배포되는 실제 하드웨어를 확인하려면 Virtual Machine 내에서 가상 하드웨어를 쿼리합니다. 사용 사례로는 개발 및 테스트 서버, 트래픽이 적은 웹 서버, 중소 규모의 데이터베이스, 개념 증명, 코드 리포지토리 등이 있습니다.

  > [!NOTE]
  > A8, A9, A10 A11 VM은 2021년 3월에 사용 중지될 예정입니다. 자세한 내용은 [HPC 마이그레이션 가이드](https://azure.microsoft.com/resources/hpc-migration-guide/)를 참조하세요. 이러한 VM 크기는 "v2"가 아닌 원래 "A_v1" 시리즈에 있습니다.

- [버스트 가능한 B 시리즈](sizes-b-series-burstable.md) VM은 웹 서버, 소규모 데이터베이스 및 개발 및 테스트 환경과 같이 CPU의 전체 성능이 지속적으로 필요하지 않은 작업에 적합합니다. 이러한 작업에는 일반적으로 버스트 가능한 성능 요구 사항이 있습니다. B 시리즈는 이러한 고객들에게 VM에서 기준 성능보다 적게 사용할 때 VM 인스턴스에서 크레딧을 축적할 수 있는 양심적인 가격 위주의 기준 성능을 갖춘 VM 크기를 구입할 수 있는 기능을 제공합니다. VM에 대한 크레딧이 적립되면 애플리케이션에 더 높은 CPU 성능이 필요할 때 VM이 CPU의 최대 100%까지 사용하여 VM의 기준 이상으로 버스트할 수 있습니다.

- [Dav4 및 Dasv4 시리즈](dav4-dasv4-series.md)는 최대 256MB L3 캐시의 다중 스레드 구성으로 AMD의 2.35Ghz EPYC<sup>TM</sup> 7452 프로세서를 활용하는 새로운 크기로, 코어 8개 모두에 해당 L3 캐시 중 8MB를 전용으로 지정하여 범용 워크로드 실행을 위한 고객 옵션을 늘립니다. Dav4 시리즈와 Dasv4 시리즈는 D 및 Dsv3 시리즈와 동일한 메모리 및 디스크 구성을 가집니다.

- [Dv4 및 Dsv4 시리즈](dv4-dsv4-series.md) Dv4 및 Dsv4 시리즈는 하이퍼 스레드 구성의 Intel® Xeon® Platinum 8272CL(Cascade Lake) 프로세서에서 실행되어 대부분의 범용 워크로드에 더 나은 가치 제안을 제공합니다. 3\.4GHz의 모든 코어 터보 클록 속도를 제공합니다.

- [Ddv4 및 Ddsv4-시리즈](ddv4-ddsv4-series.md) Ddv4 및 Ddsv4 시리즈는 하이퍼 스레드 구성의 Intel&reg; Xeon&reg; Platinum 8272CL(Cascade Lake) 프로세서에서 실행되어 대부분의 범용 워크로드에 더 나은 가치 제안을 제공합니다. 3\.4GHz의 모든 코어 터보 클록 속도를 제공하며, [Intel&reg; Turbo Boost Technology 2.0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel&reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) 및 [Intel&reg; Advanced Vector Extensions 512(Intel&reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)를 사용합니다. 또한 [Intel&reg; 딥 러닝 부스트](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)를 지원합니다. 이 새로운 VM 크기는 [2세대 VM](./generation-2.md)이 포함된 [Dv3/Dsv3](./dv3-dsv3-series.md)에 비해 로컬 스토리지가 50% 확장되고, 읽기 쓰기 로컬 디스크 IOPS가 향상됩니다.

- [Dv3 및 Dsv3 시리즈](dv3-dsv3-series.md) VM은 하이퍼 스레드 구성의 Intel® Xeon® Platinum 8272CL(Cascade Lake), Intel® Xeon® 8171M 2.1GHz(Skylake), Intel® Xeon® E5-2673 v4 2.3GHz(Broadwell) 또는 Intel® Xeon® E5-2673 v3 2.4GHz(Haswell) 프로세서에서 실행되므로 대부분의 범용 워크로드에 더 나은 가치 제안을 제공합니다. 하이퍼 스레딩으로 이동하기 위해 디스크 및 네트워크 제한이 코어 단위로 조정되는 동안 메모리가 확장되었습니다(~ 3.5GiB/vCPU에서 4GiB/vCPU로). Dv3 시리즈는 더 이상 D/Dv2 시리즈의 높은 메모리 VM 크기를 갖지 않으며, 메모리 최적화 [Ev3 및 Esv3 시리즈](ev3-esv3-series.md)로 이동되었습니다.

- [Dv2 및 DSv2 시리즈](dv2-dsv2-series.md) 원본 D 시리즈에 대 한 후속 기능을 제공 하는 Vm은 더 강력한 CPU와 메모리를 최적으로 구성 하 여 대부분의 프로덕션 워크 로드에 적합 합니다. Dv2 시리즈는 D 시리즈보다 약 35% 빠릅니다. Dv2 시리즈는 Intel Turbo Boost Technology 2.0과 함께 Intel® Xeon® Platinum 8272CL(Cascade Lake), Intel® Xeon® 8171M 2.1GHz(Skylake), Intel® Xeon® E5-2673 v4 2.3GHz(Broadwell) 또는 Intel® Xeon® E5-2673 v3 2.4GHz(Haswell) 프로세서에서 실행됩니다. Dv2 시리즈는 D 시리즈와 메모리 및 디스크 구성이 같습니다.

- [DCv2 시리즈](dcv2-series.md)는 퍼블릭 클라우드에서 처리되는 동안 데이터 및 코드의 기밀성 및 무결성을 보호할 수 있습니다. 이러한 머신은 SGX 기술이 적용된 최신 세대의 Intel XEON E-2288G 프로세서로 지원됩니다. Intel Turbo Boost 기술을 사용하면 이 머신은 최대 5.0GHz까지 작동할 수 있습니다. DCv2 시리즈 인스턴스를 사용하면 고객은 사용 중인 고객의 코드와 데이터를 보호할 수 있는 보안 Enclave 기반 애플리케이션을 빌드할 수 있습니다.

## <a name="other-sizes"></a>기타 크기

- [컴퓨팅 최적화](sizes-compute.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.

Azure가 VM의 이름을 지정하는 방법에 대한 자세한 내용은 [Azure 가상 머신 크기 명명 규칙](./vm-naming-conventions.md)을 참조하세요.
