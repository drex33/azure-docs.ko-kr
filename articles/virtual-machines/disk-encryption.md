---
title: Azure Managed Disks의 서버 쪽 암호화
description: Azure Storage는 미사용 데이터를 암호화한 후 저장소 클러스터에 보관하여 데이터를 보호합니다. 고객 관리형 키를 사용하여 사용자 고유의 키로 암호화를 관리하거나 Microsoft 관리형 키를 사용하여 관리 디스크를 암호화할 수 있습니다.
author: roygara
ms.date: 09/03/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 98bbc2f84b0f56c4e50e98e99e7bcaf86588fb48
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803981"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Azure Disk Storage의 서버 쪽 암호화

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

대부분의 Azure 관리 디스크는 SSE(서버 측 암호화)를 사용하여 데이터를 보호하고 조직의 보안 및 규정 준수 약정을 준수하는 Azure Storage 암호화를 사용하여 암호화됩니다. Azure Storage 암호화는 Azure 관리 디스크(OS 및 데이터 디스크)에 저장된 미사용 데이터를 클라우드에 보존할 때 기본적으로 자동으로 암호화합니다. 하지만 호스트에서 암호화가 설정된 디스크는 Azure Storage를 통해 암호화되지 않습니다. 호스트에서 암호화가 사용 설정되는 디스크의 경우 VM을 호스트하는 서버에서 데이터에 대한 암호화를 제공하고, 이 암호화된 데이터는 Azure Storage로 흐릅니다.

Azure Managed Disks의 데이터는 사용 가능한 가장 강력한 암호 중 하나인 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용하여 투명하게 암호화되며 FIPS 140-2 규격입니다. 암호화 모듈의 기본 Azure Managed Disks에 대한 자세한 정보는 [Cryptography API: Next Generation](/windows/desktop/seccng/cng-portal)을 참조하세요.

Azure Storage 암호화는 관리 디스크의 성능에 영향을 주지 않으며 추가 비용은 없습니다. Azure Storage 암호화에 대한 자세한 내용은 [Azure Storage 암호화](../storage/common/storage-service-encryption.md)를 참조하세요.

> [!NOTE]
> 호스트에서 암호화를 사용하도록 설정하지 않는 한 임시 디스크는 관리 디스크가 아니고 SSE로 암호화되지 않습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

플랫폼 관리형 키를 사용하여 관리 디스크를 암호화하거나 사용자 고유의 키를 사용하여 암호화를 관리할 수 있습니다. 사용자 고유의 키를 사용하여 암호화를 관리하는 경우 관리 디스크의 모든 데이터를 암호화 및 암호 해독하는 데 사용할 *고객 관리형 키* 를 지정할 수 있습니다. 

다음 섹션에서는 키 관리에 대한 각 옵션을 자세히 설명합니다.

### <a name="platform-managed-keys"></a>플랫폼 관리형 키

기본적으로 관리 디스크는 플랫폼 관리형 암호화 키를 사용합니다. 기존 관리 디스크에 기록되는 모든 관리 디스크, 스냅샷, 이미지 및 데이터는 플랫폼 관리 키를 사용하여 미사용 시 자동으로 암호화됩니다.

### <a name="customer-managed-keys"></a>고객 관리형 키

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>제한

현재, 고객 관리형 키에는 다음과 같은 제한 사항이 있습니다.

- 디스크에 대해 이 기능을 사용하는 경우에는 사용하지 않도록 설정할 수 없습니다.
    이 문제를 해결해야 하는 경우 [Azure PowerShell module](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) 또는 [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)를 사용하여 모든 데이터를 고객 관리형 키를 사용하지 않는 완전히 다른 관리 디스크로 복사해야 합니다.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>지원되는 지역

관리 디스크를 사용할 수 있는 모든 지역에서 고객 관리형 키를 사용할 수 있습니다.

> [!IMPORTANT]
> 고객 관리형 키는 Azure AD(Azure Active Directory)의 기능 중 하나인 Azure 리소스에 대한 관리 ID를 사용합니다. 고객 관리형 키를 구성하는 경우 관리 ID가 내부적으로 리소스에 자동으로 할당됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동하는 경우, 관리 디스크와 연결된 관리 ID는 새로운 테넌트로 전송되지 않으므로 고객 관리형 키가 더 이상 작동하지 않을 수 있습니다. 자세한 정보는 [Azure AD 디렉터리 간에 구독 전송](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조하세요.

관리 디스크에 대해 고객 관리형 키를 사용하도록 설정하려면 [Azure PowerShell module](windows/disks-enable-customer-managed-keys-powershell.md), [Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) 또는 [Azure Portal](disks-enable-customer-managed-keys-portal.md) 중 하나를 통해 이를 사용 설정하는 방법에 대한 문서를 참조하세요. 

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>호스트에서의 암호화 - VM 데이터의 엔드투엔드 암호화

호스트에서 암호화를 사용하도록 설정하면 VM이 할당되는 Azure 서버인 VM 호스트 자체에서 암호화가 시작됩니다. 임시 디스크 및 OS/데이터 디스크 캐시의 데이터는 해당 VM 호스트에 저장됩니다. 호스트에서 암호화를 사용하도록 설정한 후에는 이 모든 데이터가 미사용 시 암호화되며, 스토리지 서비스로 흘러 유지됩니다. 기본적으로 호스트에서의 암호화는 엔드투엔드에서 데이터를 암호화합니다. 호스트에서의 암호화는 VM의 CPU를 사용하지 않으며, VM의 성능에 영향을 미치지 않습니다. 

엔드투엔드 암호화를 사용하는 경우 임시 디스크와 임시 OS 디스크는 플랫폼 관리형 키를 사용하여 미사용 시 암호화됩니다. OS 및 데이터 디스크 캐시는 선택한 디스크 암호화 유형에 따라 고객 관리형 키 또는 플랫폼 관리형 키를 사용하여 미사용 데이터를 암호화합니다. 예를 들어 디스크가 고객 관리형 키를 사용하여 암호화된 경우 디스크의 캐시는 고객 관리형 키를 사용하여 암호화되고, 디스크가 플랫폼 관리형 키를 사용하여 암호화된 경우 디스크의 캐시는 플랫폼 관리형 키를 사용하여 암호화됩니다.

### <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-vm-sizes"></a>지원되는 VM 크기

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

VM 크기를 프로그래밍 방식으로 찾을 수도 있습니다. 프로그래밍 방식으로 검색하는 방법을 알아보려면 [Azure PowerShell 모듈](windows/disks-enable-host-based-encryption-powershell.md#finding-supported-vm-sizes) 또는 [Azure CLI](linux/disks-enable-host-based-encryption-cli.md#finding-supported-vm-sizes) 문서의 지원되는 VM 크기 찾기 섹션을 참조하세요.

호스트에서의 암호화를 사용하여 엔드투엔드 암호화를 사용 설정하려면 [Azure PowerShell 모듈](windows/disks-enable-host-based-encryption-powershell.md), [Azure CLI](linux/disks-enable-host-based-encryption-cli.md) 또는 [Azure Portal](disks-enable-host-based-encryption-portal.md) 중 하나를 통해 사용 설정하는 방법에 대한 문서를 참조하세요.

## <a name="double-encryption-at-rest"></a>미사용 이중 암호화

특정 암호화 알고리즘, 구현 또는 손상되는 키와 연결된 위험에 관해 우려하는 높은 수준의 보안을 중요시하는 고객은 플랫폼 관리형 암호화 키를 사용하는 인프라 계층에서 다른 암호화 알고리즘/모드를 사용하는 추가적인 암호화 계층을 선택할 수 있습니다. 이 새 계층은 지속형 OS와 데이터 디스크, 스냅샷 및 이미지에 적용할 수 있으며, 이는 모두 이중 암호화를 사용하여 미사용 시 암호화됩니다.

### <a name="supported-regions"></a>지원되는 지역

관리 디스크를 사용할 수 있는 모든 지역에서 이중 암호화를 사용할 수 있습니다.

관리 디스크에 대해 미사용 시 이중 암호화를 사용하도록 설정하려면 [Azure PowerShell module](windows/disks-enable-double-encryption-at-rest-powershell.md), [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) 또는 [Azure Portal](disks-enable-double-encryption-at-rest-portal.md) 중 하나를 통해 이를 사용 설정하는 방법에 대한 문서를 참조하세요.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>서버 쪽 암호화 및 Azure Disk Encryption

[Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)은 Linux의 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 기능 또는 Windows의 [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) 기능을 활용하여 게스트 VM 내에서 고객 관리형 키를 사용하여 관리 디스크를 암호화합니다.  고객 관리형 키를 사용하는 서버 쪽 암호화는 스토리지 서비스의 데이터를 암호화하여 VM에 대한 모든 OS 유형 및 이미지를 사용할 수 있도록 설정하여 ADE에서 향상됩니다.
> [!IMPORTANT]
> 고객 관리형 키는 Azure AD(Azure Active Directory)의 기능 중 하나인 Azure 리소스에 대한 관리 ID를 사용합니다. 고객 관리형 키를 구성하는 경우 관리 ID가 내부적으로 리소스에 자동으로 할당됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동하는 경우, 관리 디스크와 연결된 관리 ID는 새로운 테넌트로 전송되지 않으므로 고객 관리형 키가 더 이상 작동하지 않을 수 있습니다. 자세한 정보는 [Azure AD 디렉터리 간에 구독 전송](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

**Q: 관리 디스크를 만들 때 서버 쪽 암호화가 기본적으로 사용하도록 설정되어 있나요?**

**A:** 예. 관리 디스크는 서버 쪽 암호화 및 플랫폼 관리 키를 사용하여 암호화됩니다.

**Q: 관리 디스크에서 부팅 볼륨이 기본적으로 암호화되어 있나요?**

**A:** 예. OS 디스크를 비롯한 모든 관리 디스크가 기본적으로 암호화됩니다.

**Q: 암호화 키는 누가 관리하나요?**

**A:** 플랫폼 관리형 키는 Microsoft에서 관리합니다. Azure Key Vault 저장된 사용자 고유의 키를 사용하고 관리할 수도 있습니다.

**Q: 내 관리 디스크에 대해 서버 쪽 암호화를 사용하지 않도록 설정할 수 있나요?**

**A:** 아니요.

**Q: 서버 쪽 암호화는 특정 지역에서만 사용할 수 있나요?**

**A:** 아니요. 플랫폼 관리형 키와 고객 관리형 키를 모두 사용하는 서버 쪽 암호화는 Azure Managed Disks 사용할 수 있는 모든 지역에서 사용할 수 있습니다.

**Q: Azure Site Recovery 온-프레미스-Azure 및 Azure-Azure 재해 복구 시나리오에 고객 관리형 키를 사용하는 서버 쪽 암호화를 지원하나요?**

**A:** 예.

**Q: Azure Backup 서비스를 사용하여 고객 관리형 키를 사용하는 서버 쪽 암호화로 암호화된 관리 디스크를 백업할 수 있나요?**

**A:** 예.

**Q: 관리되는 스냅샷 및 이미지가 암호화되어 있나요?**

**A:** 예. 모든 관리형 스냅샷과 이미지는 자동으로 암호화됩니다.

**Q: 해당 디스크가 이전에 암호화되었거나 암호화된 스토리지 계정에 있는 경우 VM 비관리 디스크를 관리 디스크로 변환할 수 있나요?**

**A:** 예.

**Q: 관리 디스크 또는 스냅샷에서 내보낸 VHD도 암호화됩니까?**

**A:** 아니요. 하지만 암호화된 Managed Disk 또는 스냅샷의 암호화된 스토리지 계정에 VHD를 내보낼 경우 암호화됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell 모듈](windows/disks-enable-host-based-encryption-powershell.md), [Azure CLI](linux/disks-enable-host-based-encryption-cli.md) 또는 [Azure Portal](disks-enable-host-based-encryption-portal.md) 중 하나를 통해 호스트에서의 암호화를 사용하여 엔드투엔드 암호화를 사용하도록 설정합니다.
- [Azure PowerShell 모듈](windows/disks-enable-double-encryption-at-rest-powershell.md), [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) 또는 [Azure Portal](disks-enable-double-encryption-at-rest-portal.md) 중 하나를 통해 관리 디스크에 대한 미사용 시 이중 암호화를 사용하도록 설정합니다.
- [Azure PowerShell 모듈](windows/disks-enable-customer-managed-keys-powershell.md), [Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) 또는 [Azure Portal](disks-enable-customer-managed-keys-portal.md) 중 하나를 통해 관리 디스크에 대한 고객 관리형 키를 사용하도록 설정합니다.
- [고객 관리형 키를 사용하여 암호화된 디스크를 만들기 위한 Azure Resource Manager 템플릿 살펴보기](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault란?](../key-vault/general/overview.md)
