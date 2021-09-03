---
title: Azure Key Vault 관리형 HSM 일시 삭제 | Microsoft Docs
description: 관리형 HSM에서 일시 삭제를 사용하면 삭제된 HSM 인스턴스 및 키를 복구할 수 있습니다. 이 문서는 기능에 대한 개요를 제공합니다.
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
author: mbaldwin
ms.author: mbaldwin
ms.date: 06/01/2021
ms.openlocfilehash: b832aa066e1d31bfc064f988c722d2503ff96507
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122530214"
---
# <a name="managed-hsm-soft-delete-overview"></a>관리형 HSM 일시 삭제 개요

> [!IMPORTANT]
> 관리형 HSM 리소스에 대해서는 일시 삭제를 끌 수 없습니다.

> [!IMPORTANT]
> 일시 삭제된 관리형 HSM 리소스는 제거될 때까지 시간 단위로 계속 청구됩니다.

관리형 HSM 일시 삭제 기능을 사용하면 삭제된 HSM 및 키를 복구할 수 있습니다. 특히 이 기능은 다음과 같은 보호 기능을 제공합니다.

- HSM 또는 키가 삭제된 후 7~90일의 구성 가능 기간에는 복구할 수 있습니다. HSM을 만들 때 보존 기간을 설정할 수 있습니다. 값을 지정하지 않으면 기본 보존 기간인 90일이 사용됩니다. 이 기간은 사용자가 실수로 인한 키 또는 HSM 삭제를 인지하고 대응할 수 있는 충분한 시간을 제공합니다.
- 키를 영구적으로 삭제하려면 사용자가 두 가지 작업을 수행해야 합니다. 첫 번째 작업은 키를 삭제하여 일시 삭제된 상태로 만드는 것입니다. 두 번째 작업은 일시 삭제된 상태인 키를 제거하는 것입니다. 제거 작업에는 관리형 HSM Crypto 책임자 역할이 필요합니다. 이러한 추가 보호 기능은 사용자가 실수로 또는 악의적으로 키나 HSM을 삭제할 위험을 줄일 수 있습니다.


## <a name="soft-delete-behavior"></a>일시 삭제 동작

관리형 HSM 리소스에 대해서는 일시 삭제를 끌 수 없습니다.

삭제된 것으로 표시된 리소스는 지정된 기간 동안 보관됩니다. 삭제된 HSM 또는 키를 복구하는 메커니즘도 있으므로 삭제를 취소할 수 있습니다.

기본 보존 기간은 90일입니다. HSM 리소스를 만들 때 보존 정책 간격을 7~90일 값으로 설정할 수 있습니다. 제거 보호 보존 정책은 동일한 간격을 사용합니다. 보존 정책을 설정한 후에는 변경할 수 없습니다.

일시 삭제된 HSM 리소스의 이름은 보존 기간이 종료되고 HSM 리소스가 제거(영구적으로 삭제)될 때까지 다시 사용할 수 없습니다.

## <a name="purge-protection"></a>제거 보호

퍼지 보호는 선택적 동작입니다. 기본적으로 사용하지 않도록 설정됩니다. [Azure CLI](./recovery.md?tabs=azure-cli) 또는 [PowerShell](./recovery.md?tabs=azure-powershell)을 사용하여 켤 수 있습니다.

제거 보호를 설정하면 보존 기간이 끝나기 전에는 삭제된 상태의 HSM이나 키를 제거할 수 없습니다. 일시 삭제된 HSM 및 키는 계속 복구할 수 있으므로 보존 정책이 적용됩니다.

기본 보존 기간은 90일입니다. HSM을 만들 때 보존 정책 간격을 7~90일 사이의 값으로 설정할 수 있습니다. 보존 정책 간격은 HSM을 만들 때만 설정할 수 있습니다. 나중에 변경할 수 없습니다.

[CLI에서 관리형 HSM 일시 삭제를 사용하는 방법](./recovery.md?tabs=azure-cli#managed-hsms-cli) 또는 [PowerShell에서 관리형 HSM 일시 삭제를 사용하는 방법](./recovery.md?tabs=azure-powershell#managed-hsms-powershell)을 참조하세요.

## <a name="managed-hsm-recovery"></a>관리형 HSM 복구

HSM을 삭제하면 서비스가 구독에 프록시 리소스를 만들어 복구를 사용하도록 설정하기에 충분한 메타데이터를 추가합니다. 프록시 리소스는 저장된 개체입니다. 삭제된 HSM과 동일한 위치에서 사용할 수 있습니다. 

## <a name="key-recovery"></a>키 복구

키를 삭제하면 서비스가 키를 삭제된 상태로 배치하므로 작업에서 액세스할 수 없게 됩니다. 이 상태에서 키를 나열, 복구 또는 제거할 수 있습니다. 개체를 보려면 Azure CLI `az keyvault key list-deleted` 명령([CLI를 사용한 관리형 HSM 일시 삭제 및 제거 보호](./recovery.md?tabs=azure-cli#keys-cli)에 설명) 또는 Azure PowerShell `-InRemovedState` 매개 변수([PowerShell을 사용한 관리형 HSM 일시 삭제 및 제거 보호에 설명](./recovery.md?tabs=azure-powershell#keys-powershell))를 사용합니다.  

키를 삭제하면 관리형 HSM은 삭제된 HSM 또는 키에 해당하는 기본 데이터 삭제가 미리 결정된 보존 간격 후에 발생하도록 예약합니다. HSM에 해당하는 DNS 레코드도 보존 간격 동안 유지됩니다.

## <a name="soft-delete-retention-period"></a>일시 삭제 보존 기간

일시 삭제된 리소스는 설정된 기간인 90일 동안 유지됩니다. 일시 삭제 보존 기간 동안 다음 조건이 적용됩니다.

- 구독에 대한 일시 삭제 상태의 모든 HSM 및 키를 나열할 수 있습니다. 또한 그들에 대한 삭제 및 복구 정보에 액세스할 수 있습니다.
- 관리형 HSM 기여자 역할이 있는 사용자만 삭제된 HSM을 나열할 수 있습니다. 삭제된 자격 증명 모음 처리를 위해 이러한 사용 권한을 가진 사용자 지정 역할을 만드는 것이 좋습니다.
- 관리형 HSM 이름은 지정된 위치에서 고유해야 합니다. 키를 만들 때 HSM에 삭제된 상태의 해당 이름을 가진 키가 포함된 경우 해당 이름을 사용할 수 없습니다.
- 관리형 HSM 기여자 역할이 있는 사용자만 관리형 HSM을 나열, 확인, 복구 및 제거할 수 있습니다.
- 관리형 HSM 암호화 책임자 역할이 있는 사용자만 키를 나열, 보기, 복구 및 제거할 수 있습니다.
  
관리형 HSM 또는 키를 복구하지 않으면 보존 간격이 끝난 후 서비스가 일시 삭제된 HSM 또는 키를 제거합니다. 리소스 삭제를 다시 예약할 수 없습니다.

### <a name="billing-implications"></a>요금 청구 영향

관리형 HSM은 단일 테넌트 서비스입니다. 관리형 HSM을 만들 때 서비스는 HSM에 할당된 기본 리소스를 예약합니다. 이러한 리소스는 HSM이 삭제된 상태에 있는 경우에도 할당된 상태를 유지합니다. HSM이 삭제된 상태에 있는 동안 요금이 청구됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 일시 삭제를 사용하는 주요 시나리오를 설명합니다.

- [PowerShell에서 관리형 HSM 일시 삭제를 사용하는 방법](./recovery.md?tabs=azure-powershell) 
- [Azure CLI에서 관리형 HSM 일시 삭제를 사용하는 방법](./recovery.md?tabs=azure-cli)
