---
title: NP 시리즈 - Azure Virtual Machines
description: NP 시리즈 VM의 사양입니다.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 12bbaeddadb925cc6057c902a528121c16e05cd7
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546795"
---
# <a name="np-series"></a>NP 시리즈 

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

NP 시리즈 가상 머신은 기계 학습 유추, 비디오 트랜스코딩 및 데이터베이스 검색 & 분석을 비롯한 워크로드를 가속화하기 위해 [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) FPGA를 통해 구동됩니다. NP 시리즈 VM은 Intel Xeon 8171M(Skylake) CPU에서 구동되며 모든 코어 turbo 클록 속도는 3.2GHz입니다.

[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원되지 않음<br>
VM 생성 지원: 1세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨([미리 보기](ephemeral-os-disks.md#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks))<br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | FPGA | FPGA 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수/예상 네트워크 대역폭(Mbps) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>질문과 대답

**Q:** NP VM에 대한 할당량을 요청하려면 어떻게 해야 하나요?

**A:** 이 페이지 [VM 시리즈별 제한 늘리기](../azure-portal/supportability/per-vm-quota-requests.md)를 따르세요. NP VM은 미국 동부, 미국 서부 2, 서유럽 및 동남 아시아에서 사용할 수 있습니다.

**Q:** 어떤 버전의 Vitis를 사용해야 하나요? 

**A:** Xilinx는 [Vitis 2020.2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html)를 권장하며, 개발 VM 마켓플레이스 옵션(Ubuntu 18.04 및 Centos 7.8의 경우 Vitis 2020.2 개발 VM)을 사용할 수도 있습니다.

**Q:** NP VM을 사용하여 솔루션을 개발해야 하나요? 

**A:** 아니요, 온-프레미스를 개발하고 클라우드에 배포할 수 있습니다. [증명 설명서](./field-programmable-gate-arrays-attestation.md)에 따라 NP VM에 배포해야 합니다. 

**Q:** NP VM에서 내 FPGA를 프로그래밍할 때 사용해야 하는 증명에서 반환된 파일은 무엇인가요?

**A:** 증명은 두 개의 xclbin인 **design.bit.xclbin** 및 **design.azure.xclbin** 을 반환합니다. **design.azure.xclbin** 을 사용하세요.

**Q:** 모든 XRT/플랫폼 파일은 어디에서 가져와야 하나요?

**A:** 모든 파일을 보려면 Xilinx의 [Microsoft-Azure](https://www.xilinx.com/microsoft-azure.html) 사이트를 방문하세요.

**Q:** 어떤 버전의 XRT를 사용해야 하나요?

**A:** xrt_202020.2.8.832 

**Q:** 대상 배포 플랫폼은 무엇인가요?

**A:** 다음 플랫폼을 사용합니다.
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1 

**Q:** 개발 대상으로 지정해야 하는 플랫폼은 무엇인가요?

**A:** xilinx-u250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

**Q:** 지원되는 OS(운영 체제)는 무엇인가요? 

**A:** Xilinx 및 Microsoft는 Ubuntu 18.04 LTS 및 CentOS 7.8의 유효성을 검사했습니다.

 Xilinx는 이러한 VM의 배포를 간소화하도록 다음 마켓플레이스 이미지를 만들었습니다. 

[Xilinx Alveo U250 Deployment VM – Ubuntu18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Xilinx Alveo U250 Deployment VM – CentOS7.8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**Q:** 나만의 Ubuntu/CentOS VM을 배포하고 XRT/배포 대상 플랫폼을 설치할 수 있나요? 

**A:** 예.

**Q:** 나만의 Ubuntu 18.04 VM을 배포하는 경우 필요한 패키지 및 단계는 무엇인가요?

**A:** [Xilinx XRT 설명서](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf)당 Kernel 4.1X 사용
       
다음 패키지를 설치합니다.
- xrt_202020.2.8.832_18.04-amd64-xrt.deb
       
- xrt_202020.2.8.832_18.04-amd64-azure.deb
       
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all_18.04.deb.tar.gz
       
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1_all.deb  

**Q:** Ubuntu에서 VM을 다시 부팅한 후 FPGA를 찾을 수 없습니다. 

**A:** 커널이 업그레이드되지 않았는지 확인하세요(uname -a). 그렇다면 커널 4.1X로 다운그레이드하세요. 

**Q:** 나만의 CentOS 7.8 VM을 배포하는 경우 필요한 패키지 및 단계는 무엇인가요?

**A:** 3.10.0-1160.15.2.el7.x86_64 커널 버전 사용

 다음 패키지를 설치합니다.
   
 - xrt_202020.2.8.832_7.4.1708-x86_64-xrt.rpm 
      
 - xrt_202020.2.8.832_7.4.1708-x86_64-azure.rpm 
     
 - xilinx-u250-gen3x16-xdma-platform-2.1-3.noarch.rpm.tar.gz 
      
 - xilinx-u250-gen3x16-xdma-validate-2.1-3005608.1.noarch.rpm  

**Q:** CentOS에서 xbutil 유효성 검사를 실행할 때 다음 경고가 표시됩니다. "경고: 커널 버전 3.10.0-1160.15.2.el7.x86_64는 공식적으로 지원되지 않습니다. 4.18.0-193이 지원되는 최신 버전입니다." 

**A:** 무시해도 됩니다. 

**Q:** OnPrem 및 NP VM 간 차이점은 무엇인가요?

**A:**  
<br>
<b>- XOCL/XCLMGMT 관련: </b>
<br>
Azure NP VM에는 XOCL 드라이버를 사용하는 역할 엔드포인트(디바이스 ID 5005)만 있습니다.

OnPrem FPGA에는 각각 XCLMGMT 및 XOCL 드라이버를 사용하는 관리 엔드포인트(디바이스 ID 5004) 및 역할 엔드포인트(디바이스 ID 5005)가 둘 다 있습니다.

<br>
<b>- XRT 관련: </b>
<br>
Azure NP VM에서 XDMA 2.1 플랫폼은 Host_Mem(SB) 및 DDR 데이터 보존 기능만 지원합니다. 
<br>
Host_Mem(SB)(최대 1Gb RAM)을 사용하도록 설정하려면: sudo xbutil host_mem --enable --size 1g 

Host_Mem(SB)를 사용하지 않도록 설정하려면: sudo xbutil host_mem --disable 

**Q:** xbmgmt 명령을 실행할 수 있나요? 

**A:** 아니요, Azure VM에는 Azure VM에서 직접 관리 지원이 없습니다. 

 **Q:** PLP를 로드해야 하나요? 

**A:** 아니요, PLP는 자동으로 로드되므로 xbmgmt 명령을 통해 로드할 필요가 없습니다. 

 
**Q:** Azure에서 서로 다른 PLP를 지원하나요? 

**A:** 현재는 지원하지 않습니다. 배포 플랫폼 패키지에 제공된 PLP만 지원합니다. 

**Q:** PLP 정보를 쿼리하려면 어떻게 해야 하나요? 

**A:** xbutil 쿼리를 실행하고 하위 부분을 확인해야 합니다. 



## <a name="other-sizes-and-information"></a>기타 크기 및 정보

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

가격 계산기: [가격 계산기](https://azure.microsoft.com/pricing/calculator/)

디스크 유형에 대한 자세한 내용은 [Azure에서 사용할 수 있는 디스크 유형](disks-types.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
