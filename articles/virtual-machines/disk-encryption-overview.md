---
title: 관리 디스크 암호화 옵션 개요
description: 관리 디스크 암호화 옵션 개요
author: msmbaldwin
ms.date: 06/05/2021
ms.topic: conceptual
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 989a7fcc058284c1c706cbfc30c97a77200de111
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315315"
---
# <a name="overview-of-managed-disk-encryption-options"></a>관리 디스크 암호화 옵션 개요

ADE(Azure Disk Encryption), SSE(서버 쪽 암호화) 및 호스트의 암호화를 포함하여 관리 디스크에 사용할 수 있는 여러 유형의 암호화가 있습니다.

- **Azure Disk Encryption** 을 사용하여 고객 조직의 보안 및 규정 준수 약정에 맞게 데이터를 안전하게 보호할 수 있습니다. ADE는 Linux의 [DM-Crypt](https://wikipedia.org/wiki/Dm-crypt) 기능이나 Windows의 [BitLocker](https://wikipedia.org/wiki/BitLocker) 기능을 사용하여 Azure VM(가상 머신)의 OS 및 데이터 디스크에 대한 볼륨 암호화를 제공합니다. ADE는 Azure Key Vault와 통합되어 디스크 암호화 키와 비밀을 제어하고 관리할 수 있습니다.  자세한 내용은 [Linux VM용 Azure Disk Encryption](./linux/disk-encryption-overview.md) 또는 [Windows VM용 Azure Disk Encryption](./windows/disk-encryption-overview.md)을 참조하세요.

- **서버 쪽 암호화**(미사용 암호화 또는 Azure Storage 암호화라고도 함)는 클라우드에 유지할 때 Azure 관리 디스크(OS 및 데이터 디스크)에 저장된 데이터를 자동으로 암호화합니다.  자세한 내용은 [Azure Disk Storage의 서버 쪽 암호화](./disk-encryption.md)를 참조하세요.

- **호스트에서 암호화** 를 사용하면 VM 호스트에 저장된 데이터는 미사용 시 암호화되고 스토리지 서비스에 암호화된 채로 전송됩니다. 호스트에서 암호화가 설정된 디스크는 SSE로 암호화되지 않습니다. 대신, VM을 호스트하는 서버는 데이터에 대한 암호화를 제공하고 암호화된 데이터가 Azure Storage로 흐릅니다. 자세한 내용은 [호스트에서 암호화 - VM 데이터에 대한 엔드투엔드 암호화](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)를 참조하세요.

## <a name="comparison"></a>비교

다음은 호스트의 SSE, ADE 및 암호화에 대한 비교입니다.

| | 미사용 데이터 암호화(OS 및 데이터 디스크) | 임시 디스크 암호화 | 캐시 암호화 | 컴퓨팅과 스토리지 간에 암호화된 데이터 흐름 | 키에 대한 고객 제어 | 클라우드용 Microsoft Defender 디스크 암호화 상태 |
|--|--|--|--|--|--|--|
| **플랫폼 관리형 키(SSE+PMK)를 사용하여 미사용 데이터 암호화** | &#x2705; | &#10060; | &#10060; | &#10060; | &#10060; | 비정상, 예외인 경우 해당되지 않음 |
| **고객 관리형 키(SSE+CMK)를 사용하여 미사용 데이터 암호화** | &#x2705; | &#10060; | &#10060; | &#10060; | &#x2705; | 비정상, 예외인 경우 해당되지 않음 |
| **Azure 디스크 암호화** | &#x2705; | &#x2705; | &#x2705; | &#x2705; | &#x2705; | 정상 |
| **호스트에서 암호화**  | &#x2705; | &#x2705; | &#x2705; | &#x2705; | &#x2705; | 비정상, 예외인 경우 해당되지 않음 |

> [!Important]
> 호스트에서 암호화의 경우 Microsoft Defender for Cloud는 암호화 상태를 검색하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Linux VM용 Azure Disk Encryption](./linux/disk-encryption-overview.md)
- [Windows VM용 Azure Disk Encryption](./windows/disk-encryption-overview.md)
- [Azure Disk Storage의 서버 쪽 암호화](./disk-encryption.md)
- [호스트에서 암호화](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure 보안 기본 사항 - Azure 암호화 개요](../security/fundamentals/encryption-overview.md)
