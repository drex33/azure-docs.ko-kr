---
title: 고객 관리형 키를 사용하여 Azure HPC Cache의 데이터 암호화
description: Azure HPC Cache에서 Azure Key Vault를 사용하여 기본 Microsoft 관리형 암호화 키를 사용하지 않고 암호화 키 액세스를 제어하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/15/2021
ms.author: v-erkel
ms.openlocfilehash: dbb2834d6d51f2555da05863c606b4678b9a17b7
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772288"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Azure HPC Cache에 고객 관리형 암호화 키 사용

Azure Key Vault를 사용하여 Azure HPC Cache에서 데이터를 암호화하는 데 사용되는 키의 소유권을 제어할 수 있습니다. 이 문서에서는 캐시 데이터 암호화에 고객 관리형 키를 사용하는 방법을 설명합니다.

> [!NOTE]
> 캐시 디스크를 포함하여 Azure에 저장된 모든 데이터는 기본적으로 Microsoft 관리형 키를 사용하여 미사용 암호화됩니다. 데이터를 암호화하는 데 사용되는 키를 관리하려는 경우에만 이 문서의 단계를 수행해야 합니다.

또한 Azure HPC Cache는 캐시 디스크에 대한 고객 키를 추가하는 경우에도 캐시된 데이터를 보관하는 관리 디스크에서 [VM 호스트 암호화](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)를 통해 보호됩니다. 이중 암호화를 위해 고객 관리형 키를 추가하면 보안 요구 사항이 높은 고객에게 추가 보안 수준이 제공됩니다. 자세한 내용은 [Server-side encryption of Azure disk storage](../virtual-machines/disk-encryption.md)(Azure Disk Storage의 서버 쪽 암호화)를 참조하세요.

Azure HPC Cache에 고객 관리형 키 암호화를 사용하도록 설정하는 세 가지 단계가 있습니다.

1. 키를 저장할 Azure Key Vault를 설정합니다.
1. Azure HPC Cache를 만들 때 고객 관리형 키 암호화를 선택하고 사용할 키 자격 증명 모음 및 키를 지정합니다. 필요에 따라 Key Vault에 액세스하는 데 사용할 캐시에 대한 관리 ID를 제공합니다.

   이 단계에서 선택한 항목에 따라 3단계를 건너뛸 수 있습니다. 자세한 내용은 [캐시에 대한 관리 ID 옵션 선택](#choose-a-managed-identity-option-for-the-cache)을 참조하세요.

1. **시스템 할당 관리 ID** 또는 **Key Vault 액세스로 구성되지 않은 사용자 할당 ID** 를 사용하는 경우, 새로 만든 캐시로 이동하여 Key Vault 액세스 권한을 부여합니다.

   관리 ID에 Azure Key Vault에 대한 액세스 권한이 없는 경우 새로 만든 캐시에서 권한을 부여할 때까지 암호화가 완전히 설정되지 않습니다(3단계).

   시스템 관리 ID를 사용하는 경우 캐시를 만들 때 ID가 만들어집니다. 캐시 생성 후 권한 있는 사용자로 만들려면 캐시의 ID를 Key Vault에 전달해야 합니다.

   이미 Key Vault에 대한 액세스 권한이 있는 사용자 관리 ID를 할당하는 경우 이 단계를 건너뛸 수 있습니다.

캐시를 만든 후에는 고객 관리형 키와 Microsoft 관리형 키 간에 변경할 수 없습니다. 그러나 캐시에서 고객 관리형 키를 사용하는 경우 필요에 따라 암호화 키, 키 버전, 키 자격 증명 모음을 [변경](#update-key-settings)할 수 있습니다.

## <a name="understand-key-vault-and-key-requirements"></a>키 자격 증명 모음 및 키 요구 사항 이해

키 자격 증명 모음 및 키가 Azure HPC Cache와 함께 작동하려면 다음 요구 사항을 충족해야 합니다.

키 자격 증명 모음 속성:

* **구독** - 캐시에 사용되는 것과 동일한 구독을 사용합니다.
* **지역** - 키 자격 증명 모음은 Azure HPC Cache와 동일한 지역에 있어야 합니다.
* **가격 책정 계층** - 표준 계층은 Azure HPC Cache에서 사용하기에 충분합니다.
* **일시 삭제** - Azure HPC Cache는 키 자격 증명 모음에 아직 구성되지 않은 경우 일시 삭제를 사용하도록 설정합니다.
* **제거 방지** - 제거 방지를 사용하도록 설정해야 합니다.
* **액세스 정책** - 기본 설정으로 충분합니다.
* **네트워크 연결** - Azure HPC Cache는 선택한 엔드포인트 설정과 관계없이 키 자격 증명 모음에 액세스할 수 있어야 합니다.

키 속성:

* **키 유형** - RSA
* **RSA 키 크기** - 2048
* **사용** - 예

키 자격 증명 모음 액세스 권한:

* Azure HPC Cache를 만드는 사용자에게 [Key Vault 기여자 역할](../role-based-access-control/built-in-roles.md#key-vault-contributor)과 동일한 권한이 있어야 합니다. Azure Key Vault를 설정하고 관리하는 데에도 동일한 사용 권한이 필요합니다.

  자세한 내용은 [키 자격 증명 모음에 대한 보안 액세스](../key-vault/general/security-features.md)를 참조하세요.

## <a name="choose-a-managed-identity-option-for-the-cache"></a>캐시에 대한 관리 ID 옵션 선택
<!-- check for cross-references from here and create -->

HPC Cache는 관리 ID 자격 증명을 사용하여 Key Vault에 연결합니다.

Azure HPC Cache는 두 가지의 관리 ID를 사용할 수 있습니다.

* **시스템 할당** 관리 ID - 캐시에 대해 자동으로 생성된 고유 ID입니다. 이 관리 ID는 HPC Cache가 있는 동안에만 존재하며 직접 관리하거나 수정할 수 없습니다.

* **관리 ID** - 캐시와 별도로 관리하는 독립 실행형 ID 자격 증명입니다. 원하는 액세스 권한이 있는 사용자 할당 관리 ID를 구성하고 이를 여러 HPC 캐시에서 사용할 수 있습니다.

캐시를 만들 때 관리 ID를 할당하지 않으면 Azure에서 캐시에 대한 시스템 할당 관리 ID를 자동으로 만듭니다.

사용자가 할당한 관리 ID를 사용하면 Key Vualt에 대한 액세스 권한이 이미 있는 ID를 제공할 수 있습니다. (예를 들어 Key Vault 액세스 정책에 추가되었거나 액세스를 허용하는 Azure RBAC 역할이 있습니다.) 시스템 할당 ID를 사용하거나 액세스 권한이 없는 관리 ID를 제공하는 경우, 생성 후 캐시에서 액세스를 요청해야 합니다. 아래의 [3단계](#3-authorize-azure-key-vault-encryption-from-the-cache-if-needed)에 설명된 수동 단계입니다.

* [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보세요.

* [Azure Key Vault 기본 사항](../key-vault/general/basic-concepts.md) 알아보기

## <a name="1-set-up-azure-key-vault"></a>1. Azure Key Vault 설정

키 자격 증명 모음 및 키 설정은 캐시를 만들기 전에 또는 캐시를 만드는 과정에서 수행할 수 있습니다. 이러한 리소스가 [위에](#understand-key-vault-and-key-requirements) 설명된 요구 사항을 충족하는지 확인합니다.

캐시를 만들 때 캐시의 암호화에 사용할 자격 증명 모음, 키, 키 버전을 지정해야 합니다.

자세한 내용은 [Azure Key Vault 설명서](../key-vault/general/overview.md)를 참조하세요.

> [!NOTE]
> Azure Key Vault는 동일한 구독을 사용해야 하며, Azure HPC Cache와 동일한 지역에 있어야 합니다. 선택한 지역이 [두 제품을 모두 지원](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault)하는지 확인합니다.

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. 고객 관리형 키를 사용하는 캐시 만들기

Azure HPC Cache를 만들 때 암호화 키 원본을 지정해야 합니다. [Azure HPC Cache 만들기](hpc-cache-create.md)의 지침을 따르고 **디스크 암호화 키** 페이지에서 키 자격 증명 모음 및 키를 지정합니다. 캐시를 만드는 동안 새 키 자격 증명 모음 및 키를 만들 수 있습니다.

> [!TIP]
> **디스크 암호화 키** 페이지가 표시되지 않는 경우 캐시가 [지원되는 지역](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault) 중 하나에 있는지 확인합니다.

![포털의 캐시 생성 인터페이스의 일부인 완료된 디스크 암호화 키 화면의 스크린샷](media/customer-keys-populated.png)

캐시를 만드는 사용자에게는 [Key Vault 기여자 역할](../role-based-access-control/built-in-roles.md#key-vault-contributor)과 동일한 권한이 있어야 합니다.

1. 단추를 클릭하여 프라이빗 관리형 키를 사용하도록 설정합니다. 이 설정을 변경하면 키 자격 증명 모음 설정이 표시됩니다.

1. **키 자격 증명 모음 선택** 을 클릭하여 키 선택 페이지를 엽니다. 이 캐시의 디스크에서 데이터를 암호화할 키 자격 증명 모음 및 키를 선택하거나 만듭니다.

   Azure Key Vault가 목록에 표시되지 않으면 다음 요구 사항을 확인하세요.

   * 캐시가 키 자격 증명 모음과 동일한 구독에 있나요?
   * 캐시가 키 자격 증명 모음과 동일한 지역에 있나요?
   * Azure Portal과 키 자격 증명 모음 사이에 네트워크 연결이 있나요?

1. 자격 증명 모음을 선택한 후 사용 가능한 옵션에서 개별 키를 선택하거나 새 키를 만듭니다. 키는 2048비트 RSA 키여야 합니다.

1. 선택한 키의 버전을 지정합니다. [Azure Key Vault 설명서](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)에서 버전 관리에 대해 자세히 알아보세요.

이러한 설정은 선택 사항입니다.

* [자동 키 순환](../virtual-machines/disk-encryption.md#automatic-key-rotation-of-customer-managed-keys)을 사용하려면 **항상 현재 키 버전 사용** 확인란을 선택합니다.

* 이 캐시에 특정 관리 ID를 사용하려면 **관리 ID** 섹션에서 **사용자 할당** 을 선택하고 사용할 ID를 선택합니다. 도움이 필요한 경우 [관리 ID 설명서](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)를 참조하세요.

  > [!TIP]
  > 사용자가 할당한 관리 ID는 이미 Key Vault에 액세스하도록 구성된 ID를 전달하는 경우 캐시 만들기를 간소화할 수 있습니다. 시스템 할당 관리 ID를 사용하면 캐시가 새로 만든 시스템 할당 ID가 Key Vault를 사용할 수 있도록 권한을 부여하기 위해 캐시를 만든 후 추가 단계를 수행해야 합니다.

  > [!NOTE]
  > 캐시를 만든 후에는 할당된 ID를 변경할 수 없습니다.

나머지 사양을 계속 진행하고 [Azure HPC Cache 만들기](hpc-cache-create.md)에 설명된 대로 캐시를 만듭니다.

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache-if-needed"></a>3. 캐시에서 Azure Key Vault 암호화 권한 부여(필요한 경우)
<!-- header is linked from create article, update if changed -->

> [!NOTE]
> 캐시를 만들 때 Key Vault 액세스 권한이 있는 사용자가 할당한 관리 ID를 제공한 경우에는 이 단계가 필요하지 않습니다.

몇 분 후에 새 Azure HPC Cache가 Azure Portal에 표시됩니다. **개요** 페이지로 이동하여 Azure Key Vault에 대한 액세스 권한을 부여하고 고객 관리형 키 암호화를 사용하도록 설정합니다.

> [!TIP]
> "배포 진행 중"이라는 메시지가 사라지기 전에 캐시가 리소스 목록에 표시될 수 있습니다. 성공 알림이 표시될 때까지 기다리지 않고 1~2분 후에 리소스 목록을 확인합니다.
>
> 캐시를 만든 후 90분 이내에 암호화 권한을 부여해야 합니다. 이 단계를 완료하지 않으면 캐시 시간이 초과되고 실패합니다. 실패한 캐시는 다시 만들어야 하며 수정할 수 없습니다.

캐시에 **키를 기다리는 중** 상태가 표시됩니다. 페이지 맨 위에 있는 **암호화 사용** 단추를 클릭하여 캐시에 지정된 키 자격 증명 모음에 액세스할 수 있는 권한을 부여합니다.

![암호화 사용 단추(위쪽 행) 및 상태: 키를 기다리는 중이 강조 표시된 포털의 캐시 개요 페이지 스크린샷](media/waiting-for-key.png)

**암호화 사용** 을 클릭한 다음 **예** 단추를 클릭하여 캐시에 암호화 키를 사용할 수 있는 권한을 부여합니다. 이 작업을 수행하면 키 자격 증명 모음에 대해 일시 삭제 및 제거 방지(아직 사용하도록 설정하지 않는 경우)를 사용할 수도 있습니다.

![예를 클릭하여 사용자에게 암호화를 사용하도록 요청하는 배너 메시지가 표시된 포털의 캐시 개요 페이지 스크린샷](media/enable-keyvault.png)

캐시가 키 자격 증명 모음에 대한 액세스를 요청하면 캐시된 데이터를 저장하는 디스크를 만들고 암호화할 수 있습니다.

암호화 권한을 부여하면 Azure HPC Cache가 암호화된 디스크 및 관련된 인프라를 만들기 위해 몇 분 간 설치를 진행합니다.

## <a name="update-key-settings"></a>키 설정 업데이트

Azure Portal에서 캐시에 대한 키 자격 증명 모음, 키 또는 키 버전을 변경할 수 있습니다. 캐시의 **암호화** 설정 링크를 클릭하여 **고객 키 설정** 페이지를 엽니다.

고객 관리형 키와 시스템 관리형 키 간에 캐시를 변경할 수 없습니다.

![Azure Portal의 캐시 페이지에서 설정 > 암호화를 클릭하여 도달한 "고객 키 설정" 페이지의 스크린샷](media/change-key-click.png)

**키 변경** 링크를 클릭한 다음 **키 자격 증명 모음, 키 또는 버전 변경** 을 클릭하여 키 선택기를 엽니다.

![세 개의 드롭다운 선택기를 사용하여 키 자격 증명 모음, 키, 버전을 선택하는 "Azure Key Vault에서 키 선택" 페이지의 스크린샷](media/select-new-key.png)

이 캐시와 동일한 구독 및 동일한 지역에 있는 키 자격 증명 모음은 목록에 표시됩니다.

새 암호화 키 값을 선택한 후 **선택** 을 클릭합니다. 새 값이 포함된 확인 페이지가 나타납니다. **저장** 을 클릭하여 선택을 완료합니다.

![왼쪽 위에 저장 단추가 있는 확인 페이지의 스크린샷](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Azure의 고객 관리형 키에 대해 자세히 알아보기

다음 문서에서는 Azure Key Vault 및 고객 관리형 키를 사용하여 Azure에서 데이터를 암호화하는 방법에 대해 자세히 설명합니다.

* [Azure Storage 암호화 개요](../storage/common/storage-service-encryption.md)
* [고객 관리형 키를 사용하여 디스크 암호화](../virtual-machines/disk-encryption.md#customer-managed-keys) - Azure HPC Cache와 유사한 시나리오인 관리 디스크에서 Azure Key Vault 사용에 대한 설명서

## <a name="next-steps"></a>다음 단계

Azure HPC Cache와 권한 있는 Key Vault 기반 암호화를 만든 후 데이터 원본에 대한 액세스 권한을 부여하여 캐시를 계속 설정합니다.

* [스토리지 대상 추가](hpc-cache-add-storage.md)
