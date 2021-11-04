---
title: HPC 애플리케이션 크기 조정 - Azure Virtual Machines | Microsoft Docs
description: Azure VM에서 HPC 애플리케이션 크기를 조정하는 방법을 알아봅니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 408f55592ee7a192172c85e7a417db0b277f0f46
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054517"
---
# <a name="scaling-hpc-applications"></a>HPC 애플리케이션 크기 조정

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

Azure에서 HPC 애플리케이션의 규모 확장/규모 축소 성능을 최적화하려면 특정 워크로드에 대한 성능 조정 및 최적화 실험이 필요합니다. 이 섹션과 VM 시리즈별 페이지에서는 애플리케이션 크기 조정에 대한 일반적인 지침을 제공합니다.

## <a name="application-setup"></a>애플리케이션 설정
[azurehpc repo](https://github.com/Azure/azurehpc)에는 다음과 같은 많은 예제가 포함되어 있습니다.
- 최적으로 [애플리케이션](https://github.com/Azure/azurehpc/tree/master/apps) 설정 및 실행
- [파일 시스템 및 클러스터](https://github.com/Azure/azurehpc/tree/master/examples) 구성
- 몇 가지 일반적인 애플리케이션 워크플로를 사용하여 쉽게 시작하는 방법에 대한 [자습서](https://github.com/Azure/azurehpc/tree/master/tutorials)

## <a name="optimally-scaling-mpi"></a>최적의 MPI 크기 조정 

애플리케이션 크기 조정의 효율성, 성능 및 일관성을 최적화하기 위해 다음과 같은 제안이 적용됩니다.

- 소규모 작업(예: < 256K 연결)은 다음 옵션을 사용합니다.
   ```bash
   UCX_TLS=rc,sm
   ```

- 대규모 작업(예: > 256K 연결)은 다음 옵션을 사용합니다.
   ```bash
   UCX_TLS=dc,sm
   ```

- 위에서 MPI 작업의 연결 수를 계산하려면 다음을 사용합니다.
   ```bash
   Max Connections = (processes per node) x (number of nodes per job) x (number of nodes per job) 
   ```

## <a name="adaptive-routing"></a>적응형 라우팅
AR(적응형 라우팅)는 EDR/HDR InfiniBand를 실행하는 Azure VM(Azure Virtual Machines)에서 좀 더 최적화된 네트워크 경로를 동적으로 선택하여 네트워크 정체를 자동으로 검색하고 방지할 수 있도록 합니다. 결과적으로 AR는 InfiniBand 네트워크에 대한 대기 시간과 대역폭을 개선하여 더 높은 성능과 크기 조정 효율성을 제공합니다. 자세한 내용은 [TechCommunity 문서](https://techcommunity.microsoft.com/t5/azure-compute/adaptive-routing-on-azure-hpc/ba-p/1205217)를 참조하세요.

## <a name="process-pinning"></a>프로세스 고정

- 순차적 고정 접근 방식(자동 밸런스 접근 방식 아님)을 사용하여 코어에 프로세스를 고정합니다. 
- Numa/Core/HwThread에 의한 바인딩이 기본 바인딩보다 좋습니다.
- 하이브리드 병렬 애플리케이션(OpenMP + MPI)은 HB/HBv2 VM 크기에서 CCX당 1개의 MPI 순위와 4개의 스레드를 사용합니다.
- 순수 MPI 애플리케이션은 HB/HBv2 VM 크기에서 성능을 최적화하기 위해 CCX당 1~4개의 MPI 순위를 실험해 보세요.
- 메모리 대역폭에 극도로 민감한 일부 애플리케이션은 CCX당 코어 수를 줄이는 것이 유용할 수 있습니다. 이러한 애플리케이션에서 CCX당 코어를 3개 또는 2개 사용하면 메모리 대역폭 경합을 줄이고 실제 성능을 더 높이거나 확장성의 일관성을 향상시킬 수 있습니다. 특히 MPI Allreduce는 이 접근 방식의 이점을 누릴 수 있습니다.
- 크기를 대폭 증가시키려면 UD 또는 하이브리드 RC+UD 전송을 사용하는 것이 좋습니다. 많은 MPI 라이브러리/런타임 라이브러리는 내부적으로 이 작업을 수행합니다(예: UCX 또는 MVAPICH2). 대규모 실행에 대한 전송 구성을 확인합니다.

## <a name="compiling-applications"></a>애플리케이션 컴파일
<br>
<details>
<summary>클릭하여 확장</summary>

필수는 아니지만, 적절한 최적화 플래그를 사용하여 애플리케이션을 컴파일하면 HB/HC 시리즈 VM에서 최상의 확장 성능을 제공합니다.

### <a name="amd-optimizing-cc-compiler"></a>AMD 최적화 C/C++ 컴파일러

AMD 최적화 C/C++ 컴파일러(AOCC) 컴파일러 시스템은 전역 최적화, 벡터화, 절차 간 분석, 루프 변환, 코드 생성을 포함하는 높은 수준의 고급 최적화, 멀티 스레딩, 프로세서 지원 기능을 제공합니다. AOCC 컴파일러 이진은 glibc(GNU C Library) 버전 2.17 이상을 갖춘 Linux 시스템에 적합합니다. 컴파일러 제품군은 C/C++ 컴파일러(clang), Fortran 컴파일러(FLANG) 및 Fortran 프런트 엔드-Clang(Dragon Egg)으로 구성됩니다.

### <a name="clang"></a>Clang

Clang은 전처리, 구문 분석, 최적화, 코드 생성, 어셈블리 및 연결을 처리하는 C, C++ 및 Objective-C 컴파일러입니다. Clang은 `-march=znver1` 플래그를 지원하여 AMD의 Zen 기반 x86 아키텍처에서 최상의 코드 생성 및 조정을 사용할 수 있도록 합니다.

### <a name="flang"></a>FLANG

FLANG 컴파일러는 AOCC 제품군(2018년 4월에 추가됨)에 최근 추가되었으며, 현재 개발자가 다운로드/테스트할 수 있도록 사전 릴리스 중입니다. Fortran 2008을 기반으로 하는 AMD는 GitHub 버전의 FLANG(https://github.com/flang-compiler/flang) )을 확장합니다. FLANG 컴파일러는 모든 Clang 컴파일러 옵션뿐만 아니라 추가적인 FLANG 관련 컴파일러 옵션을 지원합니다.

### <a name="dragonegg"></a>DragonEgg

DragonEgg는 GCC의 최적화 프로그램 및 코드 생성기를 LLVM 프로젝트의 최적화 프로그램 및 코드 생성기로 바꾸는 GCC 플러그인입니다. AOCC와 함께 제공되는 DragonEgg는 gcc-4.8.x에서 작동하고, x86-32/x86-64 대상용으로 테스트했으며, 다양한 Linux 플랫폼에서 성공적으로 사용되었습니다.

GFortran은 GCC GIMPLE 중간 표현(IR)을 생성하는 전처리, 구문 분석 및 의미 체계 분석을 담당하는 Fortran 프로그램의 실제 프런트 엔드입니다. DragonEgg는 GFortran 컴파일 흐름에 연결되는 GNU 플러그인입니다. 이는 GNU 플러그인 API를 구현합니다. 플러그인 아키텍처를 사용하면 DragonEgg가 컴파일러 드라이버가 되어 컴파일의 여러 단계를 구동합니다.  다운로드/설치 지침을 수행한 후 다음을 사용하여 Dragon Egg를 호출할 수 있습니다. 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI 컴파일러
PGI Community 버전 17이 AMD EPYC와 함께 작동하는 것으로 확인되었습니다. PGI 컴파일된 버전의 STREAM은 플랫폼의 전체 메모리 대역폭을 제공합니다. 최신 Community Edition 18.10(2018년 11월)도 마찬가지로 잘 작동해야 합니다. 다음은 Intel 컴파일러를 사용해 최적으로 컴파일하기 위한 샘플 CLI입니다.

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel 컴파일러
Intel 컴파일러 18이 AMD EPYC와 함께 작동하는 것으로 확인되었습니다. 다음은 Intel 컴파일러를 사용해 최적으로 컴파일하기 위한 샘플 CLI입니다.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC 컴파일러 
HPC의 경우 AMD는 GCC 컴파일러 7.3 이상을 권장합니다. RHEL/CentOS 7.4에 포함된 4.8.5 등의 이전 버전은 권장되지 않습니다. GCC 7.3 이상은 HPL, HPCG, DGEMM 테스트에서 훨씬 더 높은 성능을 제공합니다.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```
</details>

## <a name="next-steps"></a>다음 단계

- [Azure에서 HPC 애플리케이션 최적화에 대한 학습 모듈](/learn/modules/optimize-tightly-coupled-hpc-apps/)을 사용하여 지식을 테스트합니다.
- [HBv3 시리즈 개요](hbv3-series-overview.md) 및 [HC 시리즈 개요](hc-series-overview.md)를 검토합니다.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- Azure의 [HPC](/azure/architecture/topics/high-performance-computing/)에 대해 자세히 알아봅니다.