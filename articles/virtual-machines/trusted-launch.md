---
title: Azure VM에 대한 신뢰할 수 있는 시작
description: Azure 가상 머신에 대한 신뢰할 수 있는 시작에 대해 알아봅니다.
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: conceptual
ms.date: 10/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: 0db7b5a92820e299658d793e66edba1e6e84c087
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281460"
---
# <a name="trusted-launch-for-azure-virtual-machines"></a>Azure 가상 머신에 대한 신뢰할 수 있는 시작

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합

Azure는 [2세대](generation-2.md) VM의 보안을 향상시키기 위한 원활한 방법으로 신뢰할 수 있는 시작을 제공합니다. 신뢰할 수 있는 시작은 지속적인 고급 공격 기술로부터 보호합니다. 신뢰할 수 있는 시작은 독립적으로 사용하도록 설정할 수 있는 몇 가지 조정된 인프라 기술로 구성되어 있습니다. 각 기술은 정교한 위협에 대한 또 다른 방어 계층을 제공합니다.

> [!IMPORTANT]
> 신뢰할 수 있는 시작을 사용하려면 새 가상 머신을 만들어야 합니다. 처음 신뢰할 수 있는 시작을 사용하지 않고 만든 기존 가상 머신에서는 이를 사용할 수 없습니다.



## <a name="benefits"></a>이점 

- 확인된 부트 로더, OS 커널 및 드라이버를 사용하여 가상 머신을 안전하게 배포합니다.
- 가상 머신에서 키, 인증서 및 암호를 안전하게 보호합니다.
- 전체 부팅 체인의 무결성에 대한 인사이트와 확신을 얻으세요.
- 워크로드를 신뢰할 수 있고 확인할 수 있는지 확인합니다.

## <a name="limitations"></a>제한 사항

**VM 크기 지원:**
- B 시리즈
- Dav4 시리즈, Dasv4 시리즈
- DCsv2 시리즈
- Dv4 시리즈, Dsv4 시리즈, Dsv3 시리즈, Dsv2 시리즈
- Ddv4 시리즈, Ddsv4 시리즈
- Fsv2 시리즈
- Eav4 시리즈, Easv4 시리즈
- Ev4 시리즈, Esv4 시리즈, Esv3 시리즈
- Edv4 시리즈, Edsv4 시리즈
- Lsv2 시리즈

**OS 지원**:
- Redhat Enterprise Linux 8.3
- SUSE 15 SP2
- Ubuntu 20.04 LTS
- Ubuntu 18.04 LTS
- Debian 11
- CentOS 8.4
- Oracle Linux 8.3
- CBL-Mariner
- Windows Server 2022
- Windows Server 2019
- Windows Server 2016
- Windows 11 Pro
- Windows 11 Enterprise
- 다중 세션 Windows 11 Enterprise
- Windows 10 Pro
- Windows 10 Enterprise
- Windows 10 Enterprise 다중 세션

**지역**: 
- 모든 공용 지역

**가격 책정**: 기존 VM 가격 책정에 대한 추가 비용은 없습니다.

**다음 기능은 지원되지 않습니다.**
- Backup
- Azure Site Recovery
- Azure Compute 갤러리(이전의 Shared Image Gallery)
- 임시 OS 디스크
- 공유 디스크
- Ultra disk
- 관리형 이미지
- Azure Dedicated Host 
- 중첩된 가상화

## <a name="secure-boot"></a>보안 부팅

신뢰할 수 있는 시작의 루트에는 VM 보안 부팅이 있습니다. 플랫폼 펌웨어에서 구현되는 이 모드는 맬웨어 기반 루트킷 및 부팅 키트의 설치를 방지합니다. 보안 부팅은 서명된 운영 체제 및 드라이버만 부팅할 수 있도록 하기 위해 작동합니다. VM의 소프트웨어 스택에 대한 “신뢰할 수 있는 루트”를 설정합니다. 보안 부팅을 사용하도록 설정하면 신뢰할 수 있는 게시자가 모든 OS 부팅 구성 요소(부트 로더, 커널, 커널 드라이버)에 서명해야 합니다. Windows 및 일부 Linux 배포판은 모두 보안 부팅을 지원합니다. 보안 부팅이 신뢰할 수 있는 게시자가 서명한 이미지를 인증하지 못하는 경우 VM은 부팅을 허용하지 않습니다. 자세한 내용은 [보안 부팅](/windows-hardware/design/device-experiences/oem-secure-boot)을 참조하세요.

## <a name="vtpm"></a>vTPM

또한 신뢰할 수 있는 시작은 Azure VM에 vTPM을 도입합니다. TPM2.0 사양 규격인 하드웨어 [신뢰할 수 있는 플랫폼 모듈](/windows/security/information-protection/tpm/trusted-platform-module-overview)의 가상화된 버전입니다. 키 및 측정을 위한 전용 보안 자격 증명 모음 역할을 합니다. 신뢰할 수 있는 시작은 VM의 범위 외부에 있는 보안 환경에서 실행되는 고유한 전용 TPM 인스턴스를 VM에 제공합니다. vTPM은 VM(UEFI, OS, 시스템 및 드라이버)의 전체 부팅 체인을 측정하여 [증명](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation)을 사용합니다. 

신뢰할 수 있는 시작은 클라우드에서 원격 증명을 수행하는 데 vTPM을 사용합니다. 이는 플랫폼 상태 검사 및 신뢰 기반 결정을 내리는 데 사용됩니다. 상태 검사를 통해 신뢰할 수 있는 시작은 VM이 올바르게 부팅되었음을 암호화하여 인증할 수 있습니다. VM이 권한 없는 구성 요소를 실행하고 있기 때문에 프로세스가 실패하면 Microsoft Defender for Cloud에서 무결성 경고를 실행합니다. 경고에는 무결성 검사를 통과하지 못한 구성 요소에 대한 세부 정보가 포함됩니다.

## <a name="virtualization-based-security"></a>가상화 기반 보안

[VBS(가상화 기반 보안)](/windows-hardware/design/device-experiences/oem-vbs)는 하이퍼바이저를 사용하여 안전하고 격리된 메모리 영역을 만듭니다. Windows는 이러한 영역을 사용하여 취약성 및 악의적인 익스플로잇에 대한 보호 기능이 향상된 다양한 보안 솔루션을 실행합니다. 신뢰할 수 있는 시작을 통해 HVCI(하이퍼바이저 코드 무결성) 및 Windows Defender Credential Guard를 사용하도록 설정할 수 있습니다.

HVCI는 악성 또는 확인되지 않은 코드의 삽입 및 실행에 대해 Windows 커널 모드 프로세스를 보호하는 강력한 시스템 완화 기능입니다. 커널 모드 드라이버와 바이너리가 실행되기 전에 검사하여 서명되지 않은 파일이 메모리로 로드되는 것을 방지합니다. 이렇게 하면 해당 실행 파일 코드의 로드가 허용되면 이를 수정할 수 없습니다. VBS 및 HVCI에 대한 자세한 내용은 [VBS(가상화 기반 보안) 및 HVCI(하이퍼바이저 적용 코드 무결성)](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571)를 참조하세요.

신뢰할 수 있는 시작 및 VBS를 사용하면 Windows Defender Credential Guard를 사용할 수 있습니다. 이 기능은 권한 있는 시스템 소프트웨어만 액세스할 수 있도록 암호를 격리하고 보호합니다. PtH(Pass-the-Hash) 공격과 같은 비밀 및 자격 증명 탈취 공격에의 무단 액세스를 방지하는 데 유용합니다. 자세한 내용은 [Credential Guard](/windows/security/identity-protection/credential-guard/credential-guard)를 참조하세요.


## <a name="defender-for-cloud-integration"></a>Defender for Cloud 통합

신뢰할 수 있는 시작은 Microsoft Defender for Cloud와 통합되어 VM이 제대로 구성되었는지 확인합니다. Microsoft Defender for Cloud는 호환되는 VM을 지속적으로 평가하고 관련 권장 사항을 발급합니다.

- **보안 부팅을 사용하도록 설정하는 권장 사항:** 이 권장 사항은 신뢰할 수 있는 출시를 지원하는 VM에만 적용됩니다. Microsoft Defender for Cloud는 보안 부팅을 사용하도록 설정할 수 있지만 사용하지 않도록 설정한 VM을 식별합니다. 이 기능을 사용하도록 낮은 심각도 권장 사항을 발급합니다.
- **vTPM을 사용하도록 설정하는 권장 사항:** VM에서 vTPM을 사용하도록 설정한 경우 Microsoft Defender for Cloud에서 VM을 사용하여 게스트 증명을 수행하고 고급 위협 패턴을 식별할 수 있습니다. Microsoft Defender for Cloud가 신뢰할 수 있는 발사를 지원하고 vTPM을 사용하지 않도록 설정한 VM을 식별하는 경우 사용하도록 설정하기 위해 낮은 심각도 권장 사항을 실행합니다. 
- **게스트 증명 확장을 설치하는 권장 사항:** VM에서 보안 부팅 및 vTPM을 사용하도록 설정했지만 게스트 증명 확장이 설치되지 않은 경우 Microsoft Defender for Cloud는 낮은 심각도 권장 사항을 발급하여 게스트 증명 확장을 설치합니다. 이 확장을 통해 Microsoft Defender for Cloud는 VM의 부팅 무결성을 사전에 점검하고 모니터링할 수 있습니다. 부팅 무결성은 원격 증명을 통해 증명됩니다.  
- **증명 상태 평가:** VM에 vTPM이 사용하도록 설정되고 증명 확장이 설치된 경우 Microsoft Defender for Cloud는 VM이 정상으로 부팅되었는지 원격으로 확인할 수 있습니다. 이를 원격 증명이라고 합니다. 클라우드용 Microsoft Defender는 원격 증명의 상태를 나타내는 평가를 실행합니다.

## <a name="microsoft-defender-for-cloud-integration"></a>클라우드용 Microsoft Defender 통합

VM이 신뢰할 수 있는 시작으로 올바르게 설정된 경우 Microsoft Defender for Cloud는 VM 상태 문제를 감지하고 경고할 수 있습니다.

- **VM 증명 실패에 대한 경고:** Microsoft Defender for Cloud는 VM에서 정기적으로 증명을 수행합니다. VM이 부팅된 후에도 이 증명을 수행합니다. 증명이 실패하면 보통 심각도 경고가 트리거됩니다.
    VM 증명은 다음과 같은 이유로 실패할 수 있습니다.
    - 부팅 로그를 포함하는 증명된 정보가 신뢰할 수 있는 기준에서 벗어납니다. 이는 신뢰할 수 없는 모듈이 로드되었으며 OS가 손상되었을 수 있음을 나타낼 수 있습니다.
    - 증명된 VM의 vTPM에서 가져온 증명 견적을 확인할 수 없습니다. 이는 맬웨어가 있고 vTPM에 대한 트래픽을 가로챌 수 있음을 나타낼 수 있습니다.
    
    > [!NOTE]
    >  이 경고는 vTPM을 사용하고 증명 확장이 설치된 VM에 사용할 수 있습니다. 증명을 통과하려면 보안 부팅을 사용하도록 설정해야 합니다. 보안 부팅을 사용하지 않도록 설정하면 증명이 실패합니다. 보안 부팅을 사용하지 않도록 설정해야 하는 경우, 가양성을 방지하기 위해 이 경고를 억제할 수 있습니다.

- **신뢰할 수 없는 Linux 커널 모듈에 대한 경고:** 보안 부팅을 사용하도록 설정된 신뢰할 수 있는 시작의 경우 커널 드라이버가 유효성 검사에 실패하고 로드가 금지된 경우에도 VM이 부팅될 수 있습니다. 이 경우 Microsoft Defender for Cloud는 낮은 심각도 경고를 발생합니다. 신뢰할 수 없는 드라이버가 로드되지 않았기 때문에 즉각적인 위협은 없지만 이러한 이벤트는 조사해야 합니다. 다음을 살펴보세요.
    - 어떤 커널 드라이버가 실패했나요? 이 드라이버에 익숙하고 로드될 것으로 생각하나요?
    - 예상되는 드라이버 버전이 정확한가요? 드라이버 바이너리가 손상되지 않았나요? 타사 드라이버인 경우 공급업체에서 OS 준수 테스트를 통과하여 서명되었나요?


## <a name="faq"></a>FAQ

신뢰할 수 있는 시작에 대한 자주 묻는 질문입니다.

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>신뢰할 수 있는 시작을 사용해야 하는 이유는 무엇인가요? 신뢰할 수 있는 시작은 무엇을 보호하나요?

신뢰할 수 있는 시작은 부팅 키트, 루트킷 및 커널 수준 맬웨어로부터 보호합니다. 이러한 고급 유형의 맬웨어는 커널 모드에서 실행되고 사용자에게는 숨겨진 상태를 유지합니다. 예를 들면 다음과 같습니다.
- 펌웨어 루트킷: 이러한 키트는 가상 머신 BIOS의 펌웨어를 덮어쓰므로 OS보다 먼저 루트킷을 시작할 수 있습니다. 
- 부팅 키트: 이러한 키트는 OS의 부트 로더를 대체하여 가상 머신이 OS 이전에 부팅 키트를 로드하도록 합니다.
- 커널 루트킷: 이러한 키트는 OS가 로드될 때 루트킷이 자동으로 시작되도록 OS 커널의 일부를 교체합니다.
- 드라이버 루트킷: 이러한 키트는 OS에서 가상 머신의 구성 요소와 통신하는 데 사용하는 신뢰할 수 있는 드라이버 중 하나로 위장합니다.

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>보안 부팅과 계획 부팅 간의 차이점은 무엇인가요?

보안 부팅 체인에서 부팅 프로세스의 각 단계는 이후 단계의 암호화 서명을 확인합니다. 예를 들어, BIOS는 로더에서 서명을 확인하고 로더는 로드되는 모든 커널 개체에 대한 서명을 검사하는 식입니다. 개체 중 하나라도 손상되면 서명이 일치하지 않고 VM이 부팅되지 않습니다. 자세한 내용은 [보안 부팅](/windows-hardware/design/device-experiences/oem-secure-boot)을 참조하세요. 계획 부팅은 부팅 프로세스를 중단하지 않으며 체인에서 다음 개체의 해시를 측정하거나 컴퓨팅하고 vTPM의 PCR(플랫폼 구성 레지스터)에 해시를 저장합니다. 계획 부팅 레코드는 부팅 무결성 모니터링에 사용됩니다.

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>무결성 오류가 검색되면 어떻게 되나요?

Azure 가상 머신의 신뢰할 수 있는 시작은 고급 위협에 대해 모니터링됩니다. 이러한 위협이 검색되면 경고가 트리거됩니다. 경고는 [Defender for Cloud의 향상된 보안 기능을](../security-center/enable-enhanced-security.md) 사용하도록 설정한 경우에만 사용할 수 있습니다.

Defender for Cloud는 정기적으로 증명을 수행합니다. 증명이 실패하면 보통 심각도 경고가 트리거됩니다. 신뢰할 수 있는 시작 증명은 다음과 같은 이유로 실패할 수 있습니다.

- TCB(신뢰할 수 있는 컴퓨팅 기반)의 로그가 포함된 증명된 정보가 신뢰할 수 있는 기준(예: 보안 부팅을 사용함)에서 벗어납니다. 이는 신뢰할 수 없는 모듈이 로드되었으며 OS가 손상되었을 수 있음을 나타낼 수 있습니다.
- 증명된 VM의 vTPM에서 가져온 증명 견적을 확인할 수 없습니다. 이는 맬웨어가 있고 TPM에 대한 트래픽을 가로챌 수 있음을 나타낼 수 있습니다.
- VM의 증명 확장이 응답하지 않습니다. 이는 맬웨어 또는 OS 관리자에 의한 서비스 거부 공격을 나타낼 수 있습니다.

### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>신뢰할 수 있는 시작은 Hyper-V 보호된 VM과 어떻게 다른가요?

Hyper-V 보호된 VM은 현재 Hyper-V에서만 사용할 수 있습니다. [Hyper-V 보호된 VM](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms)은 일반적으로 보호된 패브릭과 함께 배포됩니다. 보호된 패브릭은 HGS(호스트 보호 서비스), 하나 이상의 보호된 호스트 및 보호된 VM 집합으로 구성됩니다. Hyper-V 보호된 VM은 Hyper-V 호스트에서 실행될 수 있는 패브릭 관리자와 신뢰할 수 없는 소프트웨어 모두에서 가상 머신의 데이터와 상태를 보호해야 하는 패브릭에서 사용하기 위한 것입니다. 반면에 신뢰할 수 있는 시작은 HGS를 추가 배포하고 관리하지 않고도 Azure의 독립 실행형 가상 머신 또는 Virtual Machine Scale Sets로 배포할 수 있습니다. 배포 코드를 간단하게 변경하거나 Azure Portal 확인란을 선택하여 모든 신뢰할 수 있는 시작 기능을 사용할 수 있습니다.  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>기존 VM을 신뢰할 수 있는 시작으로 변환하려면 어떻게 해야 하나요?

2세대 VM의 경우 신뢰할 수 있는 시작으로 변환할 마이그레이션 경로는 GA(일반 공급) 이후 대상으로 지정됩니다.

### <a name="what-is-vm-guest-state-vmgs"></a>VMGS(VM 게스트 상태)란?  

VMGS(VM 게스트 상태)는 신뢰할 수 있는 시작 VM에만 해당됩니다. Azure에서 관리되며 UEFI(통합 확장 가능 펌웨어 인터페이스) 보안 부팅 서명 데이터베이스 및 기타 보안 정보를 포함하는 Blob입니다. VMGS Blob의 수명 주기는 OS 디스크의 수명 주기와 연결됩니다.  

## <a name="next-steps"></a>다음 단계

[포털을 사용하여 신뢰할 수 있는 시작 VM](trusted-launch-portal.md)을 배포합니다.
