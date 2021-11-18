---
title: Blob Storage의 암호화 범위
description: 암호화 범위에서는 개별 Blob 또는 컨테이너 수준에서 암호화를 관리할 수 있습니다. 암호화 범위를 사용하여 같은 스토리지 계정에 있지만 다른 고객에 속한 데이터 간에 보안 경계를 만들 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.date: 07/19/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: e9395b9a988ee0eedb7cc9e26246bbb77f35d317
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724086"
---
# <a name="encryption-scopes-for-blob-storage"></a>Blob Storage의 암호화 범위

암호화 범위를 사용하면 컨테이너 또는 개별 Blob으로 범위가 지정된 키로 암호화를 관리할 수 ​​있습니다. 암호화 범위를 사용하여 같은 스토리지 계정에 있지만 다른 고객에 속한 데이터 간에 보안 경계를 만들 수 있습니다.

암호화 범위 작업에 관한 자세한 내용은 [암호화 범위 만들기 및 관리](encryption-scope-manage.md)를 참조하세요.

## <a name="how-encryption-scopes-work"></a>암호화 범위 작동 방식

기본적으로 스토리지 계정은 전체 스토리지 계정으로 범위가 지정된 키로 암호화됩니다. 암호화 범위를 정의할 때 컨테이너 또는 개별 Blob으로 범위가 지정될 수 있는 키를 지정합니다. 암호화 범위가 Blob에 적용되면 해당 키로 Blob이 암호화됩니다. 암호화 범위가 컨테이너에 적용되면 해당 컨테이너에 있는 Blob의 기본 범위 역할을 하므로 해당 컨테이너에 업로드되는 모든 Blob이 같은 키로 암호화될 수 있습니다. 컨테이너는 컨테이너의 모든 Blob에 기본 암호화 범위를 적용하거나 기본값이 아닌 암호화 범위를 사용하여 개별 Blob을 컨테이너에 업로드할 수 있도록 구성할 수 있습니다.

암호화 범위를 사용하여 만든 Blob에 대한 읽기 작업은 암호화 범위를 사용하지 않게 설정하지 않는 경우 투명하게 수행됩니다.

### <a name="key-management"></a>키 관리

암호화 범위를 정의할 때 범위가 Microsoft 관리형 키로 보호되는지 아니면 Azure Key Vault에 저장된 고객 관리형 키로 보호되는지 지정할 수 있습니다. 같은 스토리지 계정의 서로 다른 암호화 범위에서는 Microsoft 관리형 또는 고객 관리형 키를 사용할 수 있습니다. 암호화 범위를 보호 하는 데 사용 되는 키 유형을 고객이 관리 하는 키에서 Microsoft 관리 키로 또는 그 반대로 전환할 수도 있습니다. 고객 관리형 키에 관한 자세한 내용은 [Azure Storage 암호화용 고객 관리형 키](../common/customer-managed-keys-overview.md)를 참조하세요. Microsoft 관리형 키에 관한 자세한 내용은 [암호화 키 관리 정보](../common/storage-service-encryption.md#about-encryption-key-management)를 참조하세요.

고객 관리형 키로 암호화 범위를 정의하는 경우 키 버전을 자동 또는 수동으로 업데이트하도록 선택할 수 있습니다. 키 버전을 자동으로 업데이트하도록 선택하면 Azure Storage는 매일 키 자격 증명 모음 또는 관리형 HSM에서 새 버전의 고객 관리형 키를 확인하고 키를 최신 버전으로 자동 업데이트합니다. 고객 관리형 키의 키 버전 업데이트에 관한 자세한 내용은 [키 버전 업데이트](../common/customer-managed-keys-overview.md#update-the-key-version)를 참조하세요.

Azure Policy는 암호화 범위에서 고객 관리 키를 사용하도록 요구하는 기본 제공 정책을 제공합니다. 자세한 내용은 [기본 제공 정책 정의](../../governance/policy/samples/built-in-policies.md#storage)의 **저장소** 섹션을 참조하세요.

스토리지 계정에는 키 버전이 자동으로 업데이트되는 고객 관리형 키로 보호되는 암호화 범위가 최대 10,000개일 수 있습니다. 스토리지 계정에 자동으로 업데이트되는 고객 관리형 키로 보호되는 암호화 범위가 이미 10,000개 있는 경우 고객 관리형 키로 보호되는 추가 암호화 범위에 대해 키 버전을 수동으로 업데이트해야 합니다.

### <a name="infrastructure-encryption"></a>인프라 암호화

Azure Storage의 인프라 암호화를 사 하면 데이터를 이중으로 암호화할 수 있습니다. 인프라 암호화를 사용하면 데이터가 다른 암호화 알고리즘 2개와 다른 키 2개를 사용하여 두 번(&mdash;서비스 수준에서 한 번, 인프라 수준에서 한 번&mdash;) 암호화됩니다.

인프라 암호화는 저장소 계정 수준 뿐만 아니라 암호화 범위에도 지원됩니다. 계정에 인프라 암호화를 사용하도록 설정하면 해당 계정에서 만든 암호화 범위가 인프라 암호화를 자동으로 사용합니다. 계정 수준에서 인프라 암호화를 사용하도록 설정하지 않은 경우, 범위를 만들 때 암호화 범위에 대해 사용하도록 설정할 수 있는 옵션이 있습니다. 범위를 만든 후에는 암호화 범위에 대한 인프라 암호화 설정을 변경할 수 없습니다.

인프라 암호화에 대한 자세한 내용은 [데이터의 이중 암호화를 위한 인프라 암호화 사용](../common/infrastructure-encryption-enable.md)을 참조하세요.

### <a name="encryption-scopes-for-containers-and-blobs"></a>컨테이너와 Blob의 암호화 범위

컨테이너를 만들 때 나중에 해당 컨테이너에 업로드되는 Blob의 기본 암호화 범위를 지정할 수 있습니다. 컨테이너에 대해 기본 암호화 범위를 지정하는 경우 기본 암호화 범위를 적용하는 방법을 결정할 수 있습니다.

- 컨테이너에 업로드된 모든 Blob이 기본 암호화 범위를 사용하도록 요구할 수 있습니다. 이 경우 컨테이너의 모든 Blob은 같은 키를 사용하여 암호화됩니다.
- 클라이언트가 컨테이너의 기본 암호화 범위를 재정의하도록 허용하므로 기본 범위가 아닌 암호화 범위로 Blob을 업로드할 수 있습니다. 이 경우 컨테이너의 Blob은 다른 키로 암호화될 수 있습니다.

다음 표에는 컨테이너의 기본 암호화 범위가 구성되는 방식에 따라 Blob 업로드 작업의 동작이 요약되어 있습니다.

| 컨테이너에 정의 된 암호화 범위는 ... | 기본 암호화 범위를 사용 하 여 blob을 업로드 하는 중 ... | 기본 범위 이외의 암호화 범위를 사용 하 여 blob을 업로드 하는 중 ... |
|--|--|--|
| 재정의가 허용된 기본 암호화 범위 | 성공 | 성공 |
| 재정의가 허용되지 않는 기본 암호화 범위 | 성공 | 실패 |

컨테이너를 만들 때 컨테이너에 기본 암호화 범위를 지정해야 합니다.

컨테이너에 기본 암호화 범위가 지정되지 않으면 스토리지 계정에 정의한 암호화 범위를 사용하여 Blob을 업로드할 수 있습니다. Blob을 업로드할 때 암호화 범위를 지정해야 합니다.

## <a name="disabling-an-encryption-scope"></a>암호화 범위를 사용하지 않도록 설정

암호화 범위를 사용하지 않도록 설정하면 암호화 범위를 사용한 후속 읽기 또는 쓰기 작업이 HTTP 오류 코드 403(금지됨)을 표시하며 실패합니다. 암호화 범위를 다시 사용으로 설정하면 읽기 및 쓰기 작업이 정상적으로 다시 진행됩니다.

암호화 범위를 사용하지 않도록 설정하면 더는 요금이 청구되지 않습니다. 불필요한 요금을 방지할 수 있도록 필요하지 않은 모든 암호화 범위를 사용하지 않도록 설정합니다.

암호화 범위가 고객 관리형 키로 보호되고 키 자격 증명 모음에서 키를 해지하면 데이터에 액세스할 수 없게 됩니다. 암호화 범위에 대한 요금이 청구되지 않도록 키 자격 증명 모음에서 키를 해지하기 전에 암호화 범위를 사용하지 않도록 설정해야 합니다.

고객 관리형 키는 키 자격 증명 모음의 소프트 삭제 및 제거 방지로 보호되며 삭제된 키는 해당 속성에 정의된 동작의 영향을 받습니다. 자세한 내용은 Azure Key Vault 설명서에서 다음 토픽 중 하나를 참조하세요.

- [PowerShell에서 일시 삭제를 사용하는 방법](../../key-vault/general/key-vault-recovery.md)
- [CLI에서 일시 삭제를 사용하는 방법](../../key-vault/general/key-vault-recovery.md)

> [!IMPORTANT]
> 암호화 범위를 삭제할 수 없습니다.

## <a name="feature-support"></a>기능 지원

이 표에서는 사용자 계정에서 이 기능이 지원되는 방법과 특정 기능을 활성화할 때 지원에 미치는 영향을 보여 줍니다.

| Storage 계정 유형 | Blob Storage(기본 지원) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|--|--|--|--|--|
| 표준 범용 v2 | ![예](../media/icons/yes-icon.png) |![예](../media/icons/no-icon.png)              | ![예](../media/icons/no-icon.png) | ![예](../media/icons/no-icon.png) |
| Premium 블록 Blob          | ![예](../media/icons/yes-icon.png) |![예](../media/icons/no-icon.png)              | ![예](../media/icons/no-icon.png) | ![예](../media/icons/no-icon.png) |

<sup>1</sup> Data Lake Storage Gen2, NFS (네트워크 파일 시스템) 3.0 프로토콜 및 SFTP (Secure file Transfer protocol) 지원에는 모두 계층적 네임 스페이스를 사용 하는 저장소 계정이 필요 합니다.

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](../common/storage-service-encryption.md)
- [암호화 범위 만들기 및 관리](encryption-scope-manage.md)
- [Azure Storage 암호화의 고객 관리형 키](../common/customer-managed-keys-overview.md)
- [Azure Key Vault란?](../../key-vault/general/overview.md)
