---
title: Azure Portal - SSE로 고객 관리형 키 사용 - 관리 디스크
description: Azure Portal을 통해 관리 디스크에서 고객 관리형 키를 사용하도록 설정합니다.
author: roygara
ms.date: 09/27/2021
ms.topic: how-to
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: c5941c8307e5aacc99f6fce627dafc154b6bc786
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091179"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>Azure Portal을 사용하여 관리 디스크용 고객 관리형 키로 서버 쪽 암호화를 사용하도록 설정합니다

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 

Azure Disk Storage를 사용하면 관리 디스크에 대해 SSE(서버 쪽 암호화)를 사용하도록 선택하는 경우 사용자의 자체 키를 관리할 수 있습니다. 고객 관리형 키 및 기타 관리 디스크 암호화 유형을 사용하는 SSE에 대한 개념 정보는 디스크 암호화 문서 [고객 관리형 키](disk-encryption.md#customer-managed-keys)의 **고객 관리형 키** 섹션을 참조하세요.

## <a name="restrictions"></a>제한

현재, 고객 관리형 키에는 다음과 같은 제한 사항이 있습니다.

- 디스크에 대해 이 기능을 사용하는 경우에는 사용하지 않도록 설정할 수 없습니다.
    이 문제를 해결해야 하는 경우 고객 관리형 키를 사용하지 않는 완전히 다른 관리 디스크로 모든 데이터를 복사해야 합니다.

    - Linux: [관리 디스크 복사](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - Windows: [관리 디스크 복사](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

다음 섹션에서는 관리 디스크용 고객 관리형 키를 사용하도록 설정하고 사용하는 방법을 설명합니다.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>VM 배포

이제 키 자격 증명 모음 및 디스크 암호화 집합을 만들고 설정했으므로, 암호화를 사용하여 VM을 배포할 수 있습니다.
VM 배포 프로세스는 표준 배포 프로세스와 유사합니다. 유일한 차이점은 다른 리소스와 동일한 지역에 VM을 배포하고 고객 관리형 키를 사용하도록 선택해야 한다는 점 뿐입니다.

1. **가상 머신** 을 검색하고 **+ 추가** 를 선택하여 VM을 만듭니다.
1. **기본** 블레이드에서 디스크 암호화 집합과 동일한 지역 및 Azure Key Vault를 선택합니다.
1. 원하는 값을 **기본** 블레이드에 입력합니다.

    ![지역 값이 강조 표시된 VM 만들기 환경의 스크린샷.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. **디스크** 블레이드에서 **고객 관리형 키로 미사용 암호화** 를 선택합니다.
1. **디스크 암호화 집합** 드롭다운에서 디스크 암호화 집합을 선택합니다.
1. 원하는 대로 나머지 항목을 선택합니다.

    ![VM 만들기 환경의 디스크 블레이드 스크린샷. 디스크 암호화 집합 드롭다운이 강조 표시되어 있습니다.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>기존 디스크에서 사용

> [!CAUTION]
> VM에 연결된 모든 디스크에서 디스크 암호화를 사용하도록 설정하려면 VM을 중지해야 합니다.
    
1. 디스크 암호화 집합 중 하나와 동일한 지역에 있는 VM으로 이동합니다.
1. VM을 열고 **중지** 를 선택합니다.

    ![중지 단추가 강조 표시된 예제 VM에 대한 메인 오버레이 스크린샷.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. VM이 중지되면 **디스크** 를 선택한 후 암호화하려는 디스크를 선택합니다.

    ![디스크 블레이드가 열려 있는 예제 VM의 스크린샷. OS 디스크는 선택할 수 있는 예제 디스크로 강조 표시됩니다.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. **암호화** 를 선택하고 **고객 관리형 키로 미사용 암호화** 를 선택한 후 드롭다운 목록에서 디스크 암호화 집합을 선택합니다.
1. **저장** 을 선택합니다.

    ![예제 OS 디스크의 스크린샷. 암호화 블레이드가 열려 있고, 고객 관리형 키로 미사용 암호화 및 Azure Key Vault 예제가 선택되어 있습니다. 이러한 항목을 선택하면 저장 단추가 선택됩니다.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. 암호화하려는 VM에 연결된 다른 모든 디스크에서 이 프로세스를 반복합니다.
1. 디스크가 고객 관리형 키로 전환되면 암호화하려는 다른 연결된 디스크가 없는 경우 VM을 시작할 수 있습니다.

> [!IMPORTANT]
> 고객 관리형 키는 Azure AD(Azure Active Directory)의 기능 중 하나인 Azure 리소스에 대한 관리 ID를 사용합니다. 고객 관리형 키를 구성하는 경우 관리 ID가 내부적으로 리소스에 자동으로 할당됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동하는 경우, 관리 디스크와 연결된 관리 ID는 새로운 테넌트로 전송되지 않으므로 고객 관리형 키가 더 이상 작동하지 않을 수 있습니다. 자세한 정보는 [Azure AD 디렉터리 간에 구독 전송](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조하세요.

### <a name="enable-automatic-key-rotation-on-an-existing-disk-encryption-set"></a>기존 디스크 암호화 집합에서 자동 키 순환 사용

1. [자동 키 순환](disk-encryption.md#automatic-key-rotation-of-customer-managed-keys)을 사용하도록 설정할 디스크 암호화 집합으로 이동합니다.
1. **설정** 아래에서 **키** 를 선택합니다.
1. **자동 키 순환** 을 선택하고 **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [고객 관리형 키를 사용하여 암호화된 디스크를 만들기 위한 Azure Resource Manager 템플릿 살펴보기](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault란?](../key-vault/general/overview.md)
- [고객 관리형 키를 사용하도록 설정된 디스크를 사용하여 컴퓨터 복제](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [PowerShell을 사용하여 Azure로 VMware VM의 재해 복구 설정](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [PowerShell과 Azure Resource Manager를 사용하여 Hyper-V VM용 Azure에 대한 재해 복구 설정](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)