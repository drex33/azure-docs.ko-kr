---
title: Windows VM용 Azure Disk Encryption 사용
description: 이 문서에서는 Windows VM용 Microsoft Azure Disk Encryption을 사용하도록 설정하는 방법에 대한 지침을 제공합니다.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 4bd75896654711a1188ca260abbe1a076baab1db
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597356"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Windows VM을 위한 Azure Disk Encryption

Azure Disk Encryption은 조직의 보안 및 규정 준수 약정에 따라 데이터를 안전하게 보호하는 데 도움이 됩니다. 이 기능은 Windows의 [BitLocker](https://en.wikipedia.org/wiki/BitLocker) 기능을 사용하여 Azure VM(가상 머신)의 데이터 디스크 및 OS에 볼륨 암호화를 제공하며, [Azure Key Vault](../../key-vault/index.yml)와 통합되어 디스크 암호화 키와 비밀을 제어하고 관리하는 데 도움을 줍니다.

Azure Disk Encryption은 Virtual Machines와 동일한 방식으로 영역 복원력이 있습니다. 자세한 내용은 [가용성 영역을 지원하는 Azure Services](../../availability-zones/az-region.md)를 참조하세요.

[Azure Security Center](../../security-center/index.yml)를 사용하는 경우 암호화되지 않은 VM이 있는 경우 경고 메시지가 표시됩니다. 이 경고는 심각도가 높다고 표시되며 이러한 VM을 암호화하도록 권장합니다.

![Azure Security Center 디스크 암호화 경고](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - 이전에 VM을 암호화하기 위해 Azure AD에서 Azure Disk Encryption을 사용한 적이 있다면 VM을 암호화하는 데 이 옵션을 계속 사용해야 합니다. 자세한 내용은 [Azure AD(이전 릴리스)를 포함한 Azure Disk Encryption](disk-encryption-overview-aad.md)을 참조하세요. 
> - 특정 권장 사항으로 인해 데이터, 네트워크 또는 컴퓨팅 리소스 사용량이 증가할 수 있으며 이로 인해 라이선스 또는 구독 비용이 발생합니다. 사용자는 유효한 활성 Azure 구독을 포함하여 지원되는 지역에서 Azure에 리소스를 만들어야 합니다.

[Azure CLI를 사용하여 Windows VM 만들기 및 암호화 빠른 시작](disk-encryption-cli-quickstart.md) 또는 [Azure PowerShell을 사용하여 Windows VM 만들기 및 암호화 빠른 시작](disk-encryption-powershell-quickstart.md)을 사용하여 몇 분 만에 Windows용 Azure Disk Encryption의 기본 사항을 배울 수 있습니다.

## <a name="supported-vms-and-operating-systems"></a>지원되는 VM 및 운영 체제

### <a name="supported-vms"></a>지원되는 VM

Windows VM은 [다양한 크기](../sizes-general.md)로 사용할 수 있습니다. Azure Disk Encryption은 1세대 및 2세대 VM에서 지원됩니다. Azure Disk Encryption은 Premium Storage가 있는 VM에 사용할 수도 있습니다.

Azure Disk Encryption는 [기본, A-시리즈 VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/) 또는 메모리가 2GB 미만인 가상 머신에서 사용할 수 없습니다.  추가 예외는 [Azure Disk Encryption: 지원되지 않는 시나리오](disk-encryption-windows.md#unsupported-scenarios)를 참조하세요.

### <a name="supported-operating-systems"></a>지원되는 운영 체제

- Windows 클라이언트: Windows 8 이상.
- Windows Server: Windows Server 2008 R2 이상.
- Windows 10 Enterprise 다중 세션  
 
> [!NOTE]
> Windows Server 2008 R2를 사용하려면 암호화를 위해 .NET Framework 4.5가 설치되어 있어야 하며, 설치는 Windows 업데이트에서 Windows Server 2008 R2 x64 베이스 시스템([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983))용 Microsoft .NET Framework 4.5.2의 선택적 업데이트를 통해 합니다.  
>  
> Windows Server 2012 R2 Core 및 Windows Server 2016 Core를 사용하려면 암호화를 위해 VM에 bdehdcfg 구성 요소가 설치되어 있어야 합니다.


## <a name="networking-requirements"></a>네트워킹 요구 사항
Azure Disk Encryption을 사용하려면, VM이 다음 네트워크 엔드포인트 구성 요구 사항을 충족해야 합니다.
  - 키 자격 증명 모음에 연결할 토큰을 가져오려면 Windows VM이 Azure Active Directory 엔드포인트 \[login.microsoftonline.com\]에 연결할 수 있어야 합니다.
  - 암호화 키를 고객 Key Vault에 쓰려면 Windows VM에서 Key Vault 엔드포인트에 연결할 수 있어야 합니다.
  - Windows VM은 Azure 확장 리포지토리를 호스트하는 Azure 스토리지 엔드포인트 및 VHD 파일을 호스트하는 Azure 스토리지 계정에 연결할 수 있어야 합니다.
  -  보안 정책이 Azure VM에서 인터넷으로 액세스를 제한하는 경우 이전 URI를 확인하고 IP에 대한 아웃바운드 연결을 허용하도록 특정 규칙을 구성할 수 있습니다. 자세한 내용은 [방화벽 뒤에 있는 Azure Key Vault](../../key-vault/general/access-behind-firewall.md)를 참조하세요.    

## <a name="group-policy-requirements"></a>그룹 정책 요구 사항

Azure Disk Encryption은 Windows VM에 BitLocker 외부 키 보호기를 사용합니다. 도메인 가입 VM의 경우 TPM 보호기를 적용하는 그룹 정책을 푸시하지 않습니다. “호환되는 TPM이 없이 BitLocker 허용”에 대한 그룹 정책 정보는 [BitLocker 그룹 정책 참조](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)를 확인하세요.

사용자 지정 그룹 정책을 사용하는 도메인 가입 가상 머신의 BitLocker 정책에는 [BitLocker 복구 정보의 사용자 스토리지 구성 -> 256비트 복구 키 허용](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings) 설정이 포함되어야 합니다. BitLocker에 대한 사용자 지정 그룹 정책 설정이 호환되지 않으면 Azure Disk Encryption이 실패합니다. 올바른 정책 설정이 없는 컴퓨터에서 새 정책을 적용하고, 새 정책을 강제로 업데이트(gpupdate.exe /force)합니다.  다시 시작해야 할 수 있습니다.

MBAM(Microsoft Bitlocker 관리 및 모니터링) 그룹 정책 기능은 Azure Disk Encryption과 호환되지 않습니다.

> [!WARNING]
> Azure Disk Encryption은 **복구 키를 저장하지 않습니다**. [대화형 로그온: 컴퓨터 계정 잠금 임계값](/windows/security/threat-protection/security-policy-settings/interactive-logon-machine-account-lockout-threshold) 보안 설정이 사용하도록 설정된 경우 직렬 콘솔을 통해 복구 키를 제공해야만 컴퓨터를 복구할 수 있습니다. 적절한 복구 정책이 사용하도록 설정되었는지 확인하기 위한 지침은 [Bitlocker 복구 가이드 계획](/windows/security/information-protection/bitlocker/bitlocker-recovery-guide-plan)에서 찾을 수 있습니다.

도메인 수준 그룹 정책이 BitLocker에서 사용하는 AES-CBC 알고리즘을 차단하는 경우 Azure Disk Encryption이 실패합니다.

## <a name="encryption-key-storage-requirements"></a>암호화 키 스토리지 요구 사항  

Azure Disk Encryption은 Azure Key Vault를 사용하여 키 디스크 암호화 키와 비밀을 제어하고 관리합니다. Key Vault 및 VM은 동일한 Azure 지역 및 구독에 있어야 합니다.

자세한 내용은 [Azure Disk Encryption을 위한 Key Vault 만들기 및 구성](disk-encryption-key-vault.md)을 참조하세요.

## <a name="terminology"></a>용어
다음 표에서는 Azure Disk Encryption 설명서에서 사용되는 일반적인 용어 중 일부를 정의합니다.

| 용어 | 정의 |
| --- | --- |
| Azure Key Vault | Key Vault는 FIPS(Federal Information Processing Standard) 검증 하드웨어 보안 모듈을 기반으로 하는 암호화 키 관리 서비스입니다. 이러한 표준은 암호화 키 및 중요한 비밀을 보호하는 데 도움이 됩니다. 자세한 내용은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서 및 [Azure Disk Encryption을 위한 Key Vault 만들기 및 구성](disk-encryption-key-vault.md)을 참조하세요. |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli)는 명령줄에서 Azure 리소스를 관리하고 관리하는 데 최적화되어 있습니다.|
| BitLocker |[BitLocker](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831713(v=ws.11))는 Windows VM에서 디스크 암호화를 설정하는 데 사용되는 업계에서 공인된 Windows 볼륨 암호화 기술입니다. |
| KEK(키 암호화 키) | 비밀을 보호하거나 래핑하는 데 사용할 수 있는 비대칭 키(RSA 2048)입니다. HSM(하드웨어 보안 모듈) 보호 키 또는 소프트웨어 보호 키를 제공할 수 있습니다. 자세한 내용은 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 설명서 및 [Azure Disk Encryption을 위한 Key Vault 만들기 및 구성](disk-encryption-key-vault.md)을 참조하세요. |
| PowerShell cmdlet | 자세한 내용은 [Azure PowerShell cmdlet](/powershell/azure/)을 참조하세요. |

## <a name="next-steps"></a>다음 단계

- [빠른 시작 - Azure CLI를 사용하여 Windows VM 만들기 및 암호화](disk-encryption-cli-quickstart.md)
- [빠른 시작 - Azure PowerShell을 사용하여 Windows VM 만들기 및 암호화](disk-encryption-powershell-quickstart.md)
- [Windows VM에 대한 Azure Disk Encryption 시나리오](disk-encryption-windows.md)
- [Azure Disk Encryption 필수 구성 요소 CLI 스크립트](https://github.com/ejarvi/ade-cli-getting-started) 
- [Azure Disk Encryption 필수 조건 PowerShell 스크립트](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Azure Disk Encryption을 위한 키 자격 증명 모음 만들기 및 구성](disk-encryption-key-vault.md)
