---
title: Azure Backup에서 암호화
description: 백업 데이터를 보호하고 비즈니스의 보안 요구를 충족하는 데 도움이 되는 Azure Backup의 암호화 기능에 대해 알아 봅니다.
ms.topic: conceptual
ms.date: 05/25/2021
ms.custom: references_regions
ms.openlocfilehash: 2e32bb880e42aad72a526e7515ba9156de75de9a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233059"
---
# <a name="encryption-in-azure-backup"></a>Azure Backup에서 암호화

Azure Storage 암호화를 사용하여 클라우드에 저장되면 백업된 모든 데이터는 자동으로 암호화되며, 이 암호화 기능은 보안 및 규정 준수 약정을 충족하는 데 도움이 됩니다. 이 미사용 데이터는 사용할 수 있는 가장 강력한 블록 암호 중 하나인 256비트 AES 암호화를 사용하여 암호화되며 FIPS 140-2 규격을 준수합니다. 미사용 데이터의 암호화 외에도 전송 중인 모든 백업 데이터가 HTTPS를 통해 전송됩니다. 이는 항상 Azure 백본 네트워크에 남아 있습니다.

## <a name="levels-of-encryption-in-azure-backup"></a>Azure Backup의 암호화 수준

Azure Backup은 다음과 같은 두 가지 수준의 암호화를 포함합니다.

- **Recovery Services 자격 증명 모음의 데이터 암호화**
  - **플랫폼 관리형 키 사용**: 기본적으로 모든 데이터는 플랫폼 관리형 키를 사용하여 암호화됩니다. 이 암호화를 사용하도록 설정하기 위해 사용자 쪽에서 명시적인 작업을 수행할 필요는 없습니다. Recovery Services 자격 증명 모음에 백업되는 모든 워크로드에 적용됩니다.
  - **고객 관리형 키 사용**: Azure Virtual Machines 백업 시 사용자가 소유하여 관리하는 암호화 키를 사용하여 데이터를 암호화하도록 선택할 수 있습니다. Azure Backup를 사용하면 Azure Key Vault에 저장된 RSA 키를 사용하여 백업을 암호화할 수 있습니다. 백업 암호화에 사용되는 암호화 키는 원본에 사용된 것과 다를 수 있습니다. 데이터는 AES 256 기반 DEK(데이터 암호화 키)를 사용하여 보호되며, 이는 다시 사용자의 키를 사용하여 보호됩니다. 이를 통해 데이터와 키에 대한 모든 권한을 제공합니다. 암호화를 허용하려면 Recovery Services 자격 증명 모음에 Azure Key Vault 암호화 키에 대한 액세스 권한을 부여해야 합니다. 필요할 때마다 키를 사용하지 않도록 설정하거나 액세스를 취소할 수 있습니다. 그러나 자격 증명 모음에 대한 항목을 보호하기 전에 키를 사용하여 암호화를 사용하도록 설정해야 합니다. [여기를 참조하세요](encryption-at-rest-with-cmk.md).
  - **인프라 수준 암호화**: 고객 관리형 키를 사용하여 Recovery Services 자격 증명 모음에서 데이터를 암호화할 수도 있지만 스토리지 인프라에 추가적인 암호화 계층을 구성하도록 선택할 수도 있습니다. 이 인프라 암호화는 플랫폼에서 관리됩니다. 고객 관리형 키를 사용하는 미사용 암호화와 함께 백업 데이터를 2계층 암호화할 수 있습니다. 미사용 암호화에 먼저 고유한 키를 사용하도록 선택하는 경우 인프라 암호화만 구성할 수 있습니다. 인프라 암호화는 데이터 암호화에 플랫폼 관리형 키를 사용합니다.
- **백업 중인 워크로드에만 적용되는 암호화**  
  - **Azure 가상 머신 백업**: Azure Backup은 사용자가 소유하고 관리하는 [고객 관리형 키](../virtual-machines/disk-encryption.md#customer-managed-keys) 뿐만 아니라 [플랫폼 관리형 키](../virtual-machines/disk-encryption.md#platform-managed-keys)를 사용하여 암호화된 디스크가 있는 VM의 백업을 지원합니다. 또한 [Azure Disk Encryption](backup-azure-vms-encryption.md#encryption-support-using-ade)를 사용하여 암호화된 OS 또는 데이터 디스크를 포함하는 Azure 가상 머신을 백업할 수도 있습니다. ADE는 Windows VM에 BitLocker를 사용하고 Linux VM의 DM-Crypt를 사용하여 게스트 간 암호화를 수행합니다.

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](../storage/common/storage-service-encryption.md)
- [Azure Backup FAQ](./backup-azure-backup-faq.yml): 암호화 관련 문의 사항
