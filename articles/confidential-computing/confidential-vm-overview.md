---
title: Azure DCasv5/ECasv5 시리즈 기밀 가상 머신 정보(미리 보기)
description: Azure 기밀 컴퓨팅은 보안 및 기밀성 요구 사항이 높은 테넌트를 위한 기밀 VM(기밀 가상 머신)을 제공합니다.
services: virtual-machines
author: edendcohen
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 11/15/2021
ms.author: edcohen
ms.openlocfilehash: bde192723a102299ec9505c5cfdb5242c40d641f
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132942642"
---
# <a name="about-azure-dcasv5ecasv5-series-confidential-virtual-machines-preview"></a>Azure DCasv5/ECasv5 시리즈 기밀 가상 머신 정보(미리 보기)

> [!IMPORTANT]
> Azure 기밀 컴퓨팅의 기밀 VM(기밀 가상 머신)은 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 기밀 컴퓨팅은 [SEV-SNP 기술이 적용된 AMD 프로세서](virtual-machine-solutions-amd.md)를 바탕으로 기밀 VM을 제공합니다. 기밀 VM은 보안 및 기밀성 요구 사항이 높은 테넌트를 위한 것입니다. 이러한 VM은 고객의 보안 요구에 부응하기 위해 강력한 하드웨어 기반의 경계를 제공합니다. VM의 상태를 읽거나 수정하지 않게 보호하는 플랫폼을 통해, 코드 변경 없는 마이그레이션에 기밀 VM을 사용할 수 있습니다.

> [!IMPORTANT]
> 보호 수준은 구성 및 기본 설정에 따라 달라집니다. 예를 들어 Microsoft는 추가 비용 없이 편의를 위해 암호화 키를 소유하거나 관리할 수 있습니다.

## <a name="benefits"></a>이점

기밀 VM에는 다음과 같은 혜택이 있습니다.

- 가상 머신, 하이퍼바이저 및 호스트 관리 코드 간의 강력한 하드웨어 기반 격리
- 배포 전에 호스트의 호환성을 보장하기 위해 사용자 지정 가능한 증명 정책
- 첫 번째 부팅 전에 클라우드 기반 전체 디스크 암호화
- 플랫폼 또는 고객(선택 사항)이 소유하고 관리하는 VM 암호화 키
- 플랫폼의 성공적인 증명과 VM의 암호화 키 간의 암호화 바인딩으로 안전한 키 릴리스
- 가상 머신의 키와 비밀을 증명 및 보호하기 위한 전용 가상 [TPM(Trusted Platform Module)](/windows/security/information-protection/tpm/trusted-platform-module-overview) 인스턴스
- [Azure VM용 신뢰할 수 있는 시작](../virtual-machines/trusted-launch.md)과 유사한 보안 부팅 기능

## <a name="full-disk-encryption"></a>전체 디스크 암호화

기밀 VM은 향상된 새 디스크 암호화 체계를 제공합니다. 이 선택적 체계는 `root` 및 `boot` 등, 중요한 디스크 파티션을 보호합니다. 이 체계는 디스크 암호화 키를 가상 머신의 TPM에 바인딩하고, 보호되는 디스크 콘텐츠를 VM에서만 액세스할 수 있도록 합니다. 이러한 암호화 키는 하이퍼바이저 및 호스트 운영 체제를 포함하여 Azure 구성 요소를 안전하게 무시할 수 있습니다. 공격 가능성을 최소화하기 위해 전용 및 별도의 클라우드 서비스는 VM을 처음 만들 때도 디스크를 암호화합니다.

컴퓨팅 플랫폼에서 VM의 격리를 위한 중요 설정이 누락된 경우, 부팅 중에 [Azure Attestation](https://azure.microsoft.com/services/azure-attestation/)이 플랫폼의 상태를 증명하지 않습니다. 이렇게 하면 VM이 시작되지 않습니다. 예를 들어 SEV-SNP를 사용하지 않도록 설정한 경우 이 시나리오가 발생합니다. 

이 프로세스 때문에 처음 VM을 만드는 시간이 늘어날 수 있기 때문에 전체 디스크 암호화는 선택 사항입니다. 다음 중에서 선택할 수 있습니다.

 - PMK(플랫폼 관리형 키)를 사용하는 VM 배포 이전에 전체 OS 디스크 암호화가 적용된 기밀 VM
 - VM 배포 이전에 OS 디스크 암호화가 적용되지 않은 기밀 VM

무결성 및 보호 증대를 위해 기밀 VM은 [보안 부팅](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-secure-boot)을 제공합니다. 보안 부팅에서는 신뢰할 수 있는 게시자가 OS 부팅 구성 요소(부팅 로더, 커널, 커널 드라이버 포함)에 서명해야 합니다. 호환되는 모든 기밀 VM 이미지는 보안 부팅을 지원합니다. 

### <a name="encryption-pricing-differences"></a>암호화 가격 책정 차이

기밀 VM은 OS 디스크와, 몇 메가바이트의 암호화된 소형 VMGS(가상 머신 게스트 상태) 디스크를 모두 사용합니다. VMGS 디스크는 VM 구성 요소의 보안 상태를 포함합니다. 일부 구성 요소는 vTPM 및 UEFI 부팅 로더를 포함합니다. 소형 VMGS 디스크에서 월별 스토리지 비용이 발생할 수 있습니다.

2022년부터 암호화된 OS 디스크의 비용이 더 높아질 수 있습니다. 암호화된 OS 디스크에서 더 많은 공간을 사용하고, 압축이 불가능하기 때문입니다. 자세한 정보는 [관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요.

## <a name="attestation"></a>증명

기밀 VM은 플랫폼의 중요한 구성 요소 및 보안 설정에 대한 증명에 성공한 후에만 부팅됩니다. 증명 보고서에는 다음이 포함됩니다.

- AMD SEV-SNP에서 발급한 서명된 증명 보고서
- 플랫폼 부팅 설정
- 플랫폼 펌웨어 측정
- OS 측정

기밀 VM은 Azure VM용 vTPM(가상 TPM) 기능입니다. vTPM은 하드웨어 TPM의 가상화된 버전으로, TPM2.0 사양을 준수합니다. vTPM을 키 및 측정을 위한 전용 보안 자격 증명 모음으로 사용할 수 있습니다. 기밀 VM에는 VM의 범위 밖에 있는 보안 환경에서 실행되는 자체 전용 vTPM 인스턴스가 있습니다. vTPM은 UEFI, OS, 시스템 및 드라이버를 포함한 VM의 전체 부팅 체인을 측정하여 [증명](https://docs.microsoft.com/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation)을 사용합니다. 

## <a name="limitations"></a>제한 사항

기밀 VM에는 다음과 같은 제한 사항이 있습니다. 질문과 대답은 [AMD 프로세서에서의 기밀 VM에 대한 FAQ](./confidential-vm-faq-amd.yml)를 참조하세요.

### <a name="size-support"></a>크기 지원

기밀 VM은 다음 VM 크기를 지원합니다.

- DCasv5 시리즈
- DCadsv5 시리즈 
- ECasv5 시리즈
- ECadsv5 시리즈

 자세한 정보는 [AMD 배포 옵션](virtual-machine-solutions-amd.md)을 참조하세요.
### <a name="os-support"></a>OS 지원

기밀 VM은 다음 OS 옵션을 지원합니다.

- Ubuntu 20.04 LTS
- Windows Server 2019
- Windows Server 2022

### <a name="regions"></a>영역

기밀 VM은 특정 [VM 지역](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)에서 사용할 수 있는 특수 하드웨어에서 실행됩니다.
 
### <a name="pricing"></a>가격 책정

가격 책정은 기밀 VM 크기에 따라 달라집니다. 자세한 내용은 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 참조하세요.

### <a name="feature-support"></a>기능 지원

기밀 VM은 다음을 *지원하지 않습니다*.

- Azure Batch
- Azure Backup
- Azure Site Recovery
- Azure Dedicated Host 
- 가상 머신 크기 집합
- VM 이미지 캡처
- Azure Compute Gallery
- 사용 후 삭제 OS 디스크
- 공유 디스크
- Ultra disks
- 가속 네트워킹
- 사용자 증명 가능 플랫폼 보고서
- 실시간 마이그레이션
- OS 디스크 사전 암호화용 고객 관리형 키

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Portal에서 AMD에 기밀 VM 배포](quick-create-confidential-vm-portal-amd.md)
