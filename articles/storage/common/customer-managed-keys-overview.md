---
title: 계정 암호화를 위해 고객 관리형 키 사용
titleSuffix: Azure Storage
description: 사용자 고유의 암호화 키를 사용하여 스토리지 계정에서 데이터를 보호할 수 있습니다. 고객 관리형 키를 지정하는 경우 해당 키는 데이터를 암호화하는 키에 대한 액세스를 보호하고 제어하는 데 사용됩니다. 고객 관리형 키를 사용하면 훨씬 더 유연하게 액세스 제어를 관리할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.date: 06/01/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 18131a4519d571a109559ed65d9e95ec6ffc3129
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593100"
---
# <a name="customer-managed-keys-for-azure-storage-encryption"></a>Azure Storage 암호화용 고객 관리형 키

사용자 고유의 암호화 키를 사용하여 스토리지 계정에서 데이터를 보호할 수 있습니다. 고객 관리형 키를 지정하는 경우 해당 키는 데이터를 암호화하는 키에 대한 액세스를 보호하고 제어하는 데 사용됩니다. 고객 관리형 키를 사용하면 훨씬 더 유연하게 액세스 제어를 관리할 수 있습니다.

다음 Azure 키 저장소 중 하나를 사용하여 고객 관리형 키를 저장해야 합니다.

- [Azure Key Vault](../../key-vault/general/overview.md)
- [Azure Key Vault 관리형 HSM(하드웨어 보안 모듈)](../../key-vault/managed-hsm/overview.md)

사용자 고유의 키를 만들어 키 자격 증명 모음 또는 관리되는 HSM에 저장할 수도 있고, Azure Key Vault API를 사용하여 키를 생성할 수도 있습니다. 스토리지 계정 및 키 자격 증명 모음 또는 관리되는 HSM은 동일한 지역 및 동일한 Azure AD(Azure Active Directory) 테넌트에 있어야 하지만 서로 다른 구독에 있을 수 있습니다.

> [!NOTE]
> Azure Key Vault 및 Azure Key Vault 관리되는 HSM은 구성에 대해 동일한 API 및 관리 인터페이스를 지원합니다.

## <a name="about-customer-managed-keys"></a>고객 관리형 키 정보

다음 다이어그램은 Azure Storage가 고객 관리형 키를 사용하여 요청을 보내기 위해 Azure Active Directory 및 키 자격 증명 모음 또는 관리되는 HSM을 사용하는 방법을 보여 줍니다.

![Azure Storage에서 고객 관리형 키가 작동하는 방식을 보여 주는 다이어그램](media/customer-managed-keys-overview/encryption-customer-managed-keys-diagram.png)

다음은 다이어그램의 번호가 매겨진 단계에 대해 설명하는 목록입니다.

1. Azure Key Vault 관리자는 스토리지 계정과 연결된 관리 ID에 암호화 키에 대한 권한을 부여합니다.
2. Azure Storage 관리자는 스토리지 계정에 고객 관리형 키를 사용하여 암호화를 구성합니다.
3. Azure Storage는 스토리지 계정과 연결된 관리 ID를 사용하여 Azure Active Directory를 통해 Azure Key Vault에 대한 액세스를 인증합니다.
4. Azure Storage는 Azure Key Vault의 고객 관리형 키로 계정 암호화 키를 래핑합니다.
5. 읽기/쓰기 작업의 경우 Azure Storage는 계정 암호화 키를 래핑 해제하라는 요청을 Azure Key Vault에 보내서 암호화 및 암호 해독 작업을 수행합니다.

Azure Key Vault의 고객 관리형 키에 액세스하려면 스토리지 계정과 연결된 관리 ID에 최소한 다음 권한이 있어야 합니다.

- *wrapkey*
- *unwrapkey*
- *get*

키 권한에 대한 자세한 내용은 [키 유형, 알고리즘 및 작업](../../key-vault/keys/about-keys-details.md#key-access-control)을 참조하세요.

Azure Policy는 스토리지 계정이 Blob Storage 및 Azure Files 워크로드에 대해 고객 관리형 키를 사용하도록 요구하는 기본 제공 정책을 제공합니다. 자세한 내용은 [Azure Policy 기본 제공 정책 정의](../../governance/policy/samples/built-in-policies.md#storage)의 **스토리지** 섹션을 참조하세요.

## <a name="customer-managed-keys-for-queues-and-tables"></a>큐 및 테이블에 대한 고객 관리형 키

스토리지 계정에 고객 관리형 키를 사용하도록 설정한 경우, Queue 및 Table Storage에 저장된 데이터는 고객 관리형 키를 사용하여 자동으로 보호되지 않습니다. 스토리지 계정을 만들 때 이러한 서비스를 이러한 보호에 포함하도록 선택적으로 구성할 수 있습니다.

큐 및 테이블의 고객 관리형 키를 지원하는 스토리지 계정을 만드는 방법에 대한 자세한 내용은 [테이블 및 큐의 고객 관리형 키를 지원하는 계정 만들기](account-encryption-key-create.md)를 참조하세요.

Blob Storage 및 Azure Files의 데이터는 스토리지 계정에 고객 관리형 키가 구성된 경우 항상 고객 관리형 키로 보호됩니다.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>스토리지 계정에 고객 관리형 키를 사용하도록 설정

고객 관리형 키를 구성하면 Azure Storage가 연결된 키 자격 증명 모음 또는 관리되는 HSM의 고객 관리형 키를 사용하여 계정의 루트 데이터 암호화 키를 래핑합니다. 고객 관리형 키를 사용하도록 설정해도 성능에 영향을 주지 않으며 설정이 즉시 적용됩니다.

고객 관리형 키를 사용하거나 사용하지 않도록 설정하거나, 키 또는 키 버전을 수정하면 루트 암호화 키의 보호 상태가 변경되지만 Azure Storage 계정의 데이터는 다시 암호화할 필요가 없습니다.

고객 관리형 키는 기존 스토리지 계정에서만 사용할 수 있습니다. 키 자격 증명 모음 또는 관리되는 HSM은 스토리지 계정과 연결된 관리 ID에 권한을 부여하도록 구성해야 합니다. 관리 ID는 스토리지 계정이 만들어진 후에만 사용할 수 있습니다.

언제든지 고객 관리형 키와 Microsoft 관리형 키 간에 전환할 수 있습니다. Microsoft 관리형 키에 관한 자세한 내용은 [암호화 키 관리 정보](storage-service-encryption.md#about-encryption-key-management)를 참조하세요.

키 자격 증명 모음의 고객 관리형 키로 Azure Storage 암호화를 구성하는 방법에 대해 알아보려면 [Azure Key Vault에 저장된 고객 관리형 키로 암호화 구성](customer-managed-keys-configure-key-vault.md)을 참조하세요. 관리되는 HSM의 고객 관리형 키를 구성하려면 [Azure Key Vault 관리형 HSM에 저장된 고객 관리형 키로 암호화 구성](customer-managed-keys-configure-key-vault-hsm.md)을 참조하세요.

> [!IMPORTANT]
> 고객 관리형 키는 Azure AD의 기능 중 하나인 Azure 리소스에 대한 관리 ID를 사용합니다. 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. Azure Portal에 고객 관리형 키를 구성하는 경우 관리 ID가 스토리지 계정에 내부적으로 자동 할당됩니다. 이후에 구독, 리소스 그룹 또는 스토리지 계정을 Azure AD 디렉터리 간에 이동하는 경우, 스토리지 계정과 연결된 관리 ID는 새로운 테넌트로 전송되지 않으므로 고객 관리형 키가 더 이상 작동하지 않을 수 있습니다. 자세한 내용은 FAQ에서 **Azure AD 디렉터리 간 구독 전송** 및 [Azure 리소스에 대한 관리 ID의 알려진 문제](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)를 참조하세요.

Azure Storage 암호화는 2048, 3072, 4096 크기의 RSA 및 RSA HSM 키를 지원합니다. 키에 대한 자세한 내용은 [키 정보](../../key-vault/keys/about-keys.md)를 참조하세요.

키 자격 증명 모음 또는 관리되는 HSM 사용 시 관련 비용이 청구됩니다. 자세한 내용은 [Key Vault 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/)을 참조하세요.

## <a name="update-the-key-version"></a>키 버전 업데이트

고객 관리형 키를 사용하여 암호화를 구성하는 경우 키 버전을 업데이트하는 두 가지 옵션이 있습니다.

- **키 버전 자동 업데이트:** 새 버전을 사용할 수 있을 때 고객 관리형 키를 자동으로 업데이트하려면 스토리지 계정에 고객 관리형 키로 암호화를 사용하도록 설정할 때 키 버전을 생략합니다. 키 버전을 생략하면 Azure Storage가 매일 키 자격 증명 모음 또는 관리되는 HSM에서 새 버전의 고객 관리형 키를 확인합니다. Azure Storage는 자동으로 최신 버전의 키를 사용합니다.
- **수동으로 키 버전 업데이트:** Azure Storage 암호화에 특정 버전의 키를 사용하려면 스토리지 계정에 고객 관리형 키로 암호화를 사용하도록 설정할 때 해당 키 버전을 지정합니다. 키 버전을 지정하면 키 버전을 수동으로 업데이트할 때까지 Azure Storage에서 해당 버전을 암호화에 사용합니다.

    키 버전이 명시적으로 지정된 경우 새 버전을 만들 때 새 키 버전 URI를 사용하도록 스토리지 계정을 수동으로 업데이트해야 합니다. 새 버전의 키를 사용하도록 스토리지 계정을 업데이트하는 방법을 알아보려면 [Azure Key Vault에 저장된 고객 관리형 키를 사용하여 암호화 구성](customer-managed-keys-configure-key-vault.md) 또는 [Azure Key Vault 관리형 HSM에 저장된 고객 관리형 키를 사용하여 암호화 구성](customer-managed-keys-configure-key-vault-hsm.md)을 참조하세요.

키 버전을 업데이트하면 루트 암호화 키의 보호 상태는 변경되지만 Azure Storage 계정의 데이터는 다시 암호화되지 않습니다. 사용자의 추가 작업은 필요하지 않습니다.

> [!NOTE]
> 키를 순환하려면 규정 준수 정책에 따라 키 자격 증명 모음 또는 관리되는 HSM에 새 버전의 키를 만듭니다. 키를 수동으로 순환하거나 일정에 따라 키를 순환하는 함수를 만들 수 있습니다.

## <a name="revoke-access-to-customer-managed-keys"></a>고객 관리형 키에 대한 액세스 권한 철회

언제든지 고객 관리형 키에 대한 스토리지 계정의 액세스 권한을 철회할 수 있습니다. 고객 관리형 키에 대한 액세스 권한이 철회되거나 키가 사용하지 않도록 설정되거나 삭제되면 클라이언트가 Blob 또는 해당 메타데이터에서 읽거나 쓰는 작업을 호출할 수 없습니다. 모든 사용자에 대해 다음 작업을 호출하려고 하면 오류 코드 403(사용할 수 없음)과 함께 실패합니다.

- [Blob 나열](/rest/api/storageservices/list-blobs)(요청 URI에 `include=metadata` 매개 변수를 사용하여 호출하는 경우)
- [Blob 가져오기](/rest/api/storageservices/get-blob)
- [Blob 속성 가져오기](/rest/api/storageservices/get-blob-properties)
- [Blob 메타데이터 가져오기](/rest/api/storageservices/get-blob-metadata)
- [Blob 메타데이터 설정](/rest/api/storageservices/set-blob-metadata)
- [Blob 스냅샷 생성](/rest/api/storageservices/snapshot-blob)(`x-ms-meta-name` 요청 헤더를 사용하여 호출할 경우)
- [Blob 복사](/rest/api/storageservices/copy-blob)
- [URL에서 Blob 복사](/rest/api/storageservices/copy-blob-from-url)
- [Blob 계층 설정](/rest/api/storageservices/set-blob-tier)
- [블록 배치](/rest/api/storageservices/put-block)
- [URL에서 블록 배치](/rest/api/storageservices/put-block-from-url)
- [추가 블록](/rest/api/storageservices/append-block)
- [URL에서 블록 추가](/rest/api/storageservices/append-block-from-url)
- [Blob 배치](/rest/api/storageservices/put-blob)
- [페이지 배치](/rest/api/storageservices/put-page)
- [URL에서 페이지 배치](/rest/api/storageservices/put-page-from-url)
- [Blob 증분 복사](/rest/api/storageservices/incremental-copy-blob)

이러한 작업을 다시 호출하려면 고객 관리형 키에 대한 액세스를 복원합니다.

이 섹션에 나열되지 않은 모든 데이터 작업은 고객 관리형 키가 해지되거나, 키가 비활성화되거나 삭제된 후에도 계속 진행될 수 있습니다.

고객 관리형 키에 대한 액세스 권한을 철회하려면 [PowerShell](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys) 또는 [Azure CLI](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys)를 사용합니다.

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Azure 관리 디스크용 고객 관리형 키

고객 관리형 키를 사용하여 Azure 관리 디스크의 암호화를 관리할 수도 있습니다. 고객 관리형 키는 관리 디스크에서 Azure Storage 리소스와 다르게 작동합니다. 자세한 내용은 Windows용 [Azure 관리 디스크의 서버 쪽 암호화](../../virtual-machines/disk-encryption.md) 또는 Linux용 [Azure 관리 디스크의 서버 쪽 암호화](../../virtual-machines/disk-encryption.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)
- [Azure Key Vault에 저장된 고객 관리형 키를 사용하여 암호화 구성](customer-managed-keys-configure-key-vault.md)
- [Azure Key Vault 관리형 HSM에 저장된 고객 관리형 키를 사용하여 암호화 구성](customer-managed-keys-configure-key-vault-hsm.md).
