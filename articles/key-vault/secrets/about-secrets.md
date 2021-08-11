---
title: Azure Key Vault 비밀 정보 - Azure Key Vault
description: Azure Key Vault 비밀 개요.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 232a3293d6529955a768ed8d7700289ddbed5177
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136367"
---
# <a name="about-azure-key-vault-secrets"></a>Azure Key Vault 비밀 정보

[Key Vault](../general/overview.md)는 암호 및 데이터베이스 연결 문자열과 같은 안전한 일반 비밀 스토리지를 제공합니다.

개발자의 관점에서 Key Vault API는 비밀 값을 수락하고 문자열로 반환합니다. 내부적으로 Key Vault는 비밀을 8진수(8비트 바이트) 시퀀스로 저장 및 관리하며, 각각의 최대 크기는 25k 바이트입니다. Key Vault 서비스는 비밀에 대한 의미 체계를 제공하지 않습니다. 단순히 데이터를 수락하고, 암호화하고, 저장하고, 비밀 식별자("id")를 반환할 뿐입니다. 식별자는 나중에 비밀을 검색하는 데 사용할 수 있습니다.  

매우 중요한 데이터의 경우 클라이언트가 데이터에 대한 추가 보호 레이어를 고려해야 합니다. 예를 들어 별도의 보호 키를 사용하여 데이터를 암호화한 후 Key Vault에 스토리지합니다.  

Key Vault는 비밀에 대한 contentType 필드도 지원합니다. 클라이언트는 비밀 데이터에 대해 검색 시 해당 데이터를 해석하는 데 도움이 되는 콘텐츠 형식을 지정할 수 있습니다. 이 필드의 최대 길이는 255자이며, 비밀 데이터를 해석하기 위한 힌트로 사용하는 것이 좋습니다. 예를 들어 구현에서는 암호와 인증서를 모두 비밀로 저장한 다음, 이 필드를 사용하여 구분할 수 있습니다. 미리 정의된 값이 없습니다.  

## <a name="encryption"></a>암호화

Key Vault의 모든 비밀은 암호화된 상태로 저장됩니다. 이 암호화는 투명하며 사용자의 작업이 필요하지 않습니다. Azure Key Vault 서비스는 비밀을 추가할 때 암호화하고 읽을 때 암호를 자동으로 해독합니다. 암호화 키는 각 키 자격 증명 모음에 대해 고유합니다.

## <a name="secret-attributes"></a>비밀 특성

비밀 데이터 외에도 지정할 수 있는 특성은 다음과 같습니다.  

- *exp*: IntDate, 선택 사항, 기본값은 **forever** 입니다. *exp*(만료 시간) 특성은 [특정 조건](#date-time-controlled-operations)에 있는 경우를 제외하고는 비밀 데이터를 검색하지 않아야 하는 만료 시간 또는 그 이후를 식별합니다. 이 필드는 키 자격 증명 모음 서비스의 사용자에게 특정 비밀이 사용되지 않음을 알리므로 오직 **정보 제공** 을 목적으로 합니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.   
- *nbf*: IntDate, 선택 사항, 기본값은 **now** 입니다. *nbf*(이전이 아님) 특성은 [특정 조건](#date-time-controlled-operations)에 있는 경우를 제외하고는 비밀 데이터를 검색하지 않아야 하는 시간 이전을 식별합니다. 이 필드는 오직 **정보 제공** 을 목적으로 합니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다. 
- *enabled*: 부울, 선택 사항, 기본값은 **true** 입니다. 이 특성은 비밀 데이터이 검색 가능 여부를 지정합니다. enabled 특성은 *nbf* 및 *exp* 사이에 작업이 발생하는 경우 *nbf* 및 *exp* 와 함께 사용되며, enabled가 **true** 로 설정된 경우에만 허용됩니다. [특정 조건](#date-time-controlled-operations)에 있는 경우를 제외하고는 *nbf* 및 *exp* 시간 범위에 속하지 않은 작업이 자동으로 허용되지 않습니다.  

비밀 특성이 포함된 모든 응답에 포함되는 추가 읽기 전용 특성이 있습니다.  

- *created*: IntDate, 선택 사항입니다. created 특성은 이 버전의 비밀을 만든 시점을 나타냅니다. 이 특성을 추가하기 전에 만든 비밀에 대한 값은 null입니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  
- *updated*: IntDate, 선택 사항입니다. updated 특성은 이 버전의 비밀을 업데이트한 시점을 나타냅니다. 이 특성을 추가하기 전에 마지막으로 업데이트한 비밀에 대한 값은 null입니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.

각 키 자격 증명 모음 개체 유형의 일반 특성에 대한 자세한 내용은 [Azure Key Vault 키, 비밀 및 인증서 개요](../general/about-keys-secrets-certificates.md)를 참조하세요.

### <a name="date-time-controlled-operations"></a>날짜 및 시간 제어 작업

*nbf* / *exp* 시간 범위에 속하지 않은 비밀의 **get** 작업은 아직 유효하지 않은 비밀과 만료된 비밀에 대해 작동합니다. 테스트를 위해 비밀의 **get** 작업 호출을 아직 유효하지 않은 비밀에 사용할 수 있습니다. 복구 작업을 위해 만료된 비밀을 검색(**get**)할 수 있습니다.

## <a name="secret-access-control"></a>비밀 액세스 제어

Key Vault에서 관리되는 비밀에 대한 액세스 제어는 해당 비밀을 포함하는 Key Vault 수준에서 제공됩니다. 비밀에 대한 액세스 제어 정책은 동일한 Key Vault의 키에 대한 액세스 제어 정책과 다릅니다. 사용자는 비밀을 보관할 하나 이상의 자격 증명 모음을 만들 수 있으며, 시나리오에 따라 비밀을 적절하게 세분화하고 관리해야 합니다.   

자격 증명 모음의 비밀 액세스 제어 항목에서 보안 주체별로 사용할 수 있고 비밀 개체에 허용되는 작업과 매우 비슷한 권한은 다음과 같습니다.  

- 비밀 관리 작업에 필요한 권한
  - *get*: 비밀 읽기  
  - *list*: Key Vault에 저장된 비밀 또는 비밀 버전 나열  
  - *set*: 비밀 만들기  
  - *delete*: 비밀 삭제  
  - *recover*: 삭제된 비밀 복구
  - *backup*: 비밀을 키 자격 증명 모음에 백업
  - *restore*: 키 자격 증명 모음에 백업된 비밀 복원

- 권한 있는 작업에 필요한 권한
  - *purge*: 삭제된 비밀 제거(영구적으로 삭제)

비밀을 사용하는 방법에 대한 자세한 내용은 [Key Vault REST API 참조에서 비밀 작업](/rest/api/keyvault)을 참조하세요. 권한 설정에 대한 내용은 [자격 증명 모음 - 만들기 또는 업데이트](/rest/api/keyvault/vaults/createorupdate) 및 [자격 증명 모음 - 액세스 정책 업데이트](/rest/api/keyvault/vaults/updateaccesspolicy)를 참조하세요. 

Key Vault에서 액세스를 제어하는 방법 가이드:
- [CLI를 사용하여 Key Vault 액세스 정책 할당](../general/assign-access-policy-cli.md)
- [PowerShell을 사용하여 Key Vault 액세스 정책 할당](../general/assign-access-policy-powershell.md)
- [Azure Portal을 사용하여 Key Vault 액세스 정책 할당](../general/assign-access-policy-portal.md)
- [Azure 역할 기반 액세스 제어를 사용하여 Key Vault 키, 인증서 및 비밀에 대한 액세스 제공](../general/rbac-guide.md)

## <a name="secret-tags"></a>비밀 태그  
애플리케이션 관련 메타데이터를 태그 형식으로 추가로 지정할 수 있습니다. Key Vault는 최대 15개의 태그를 지원하며, 각 태그는 256자 이름과 256자의 값을 가질 수 있습니다.  

>[!Note]
>태그는 *list* 또는 *get* 권한이 있는 경우 호출자가 읽을 수 있습니다.

## <a name="azure-storage-account-key-management"></a>Azure Storage 계정 키 관리

Key Vault는 [Azure 스토리지 계정](../../storage/common/storage-account-overview.md) 키를 관리할 수 있습니다.

- 내부적으로 Key Vault는 키를 Azure Storage 계정과 함께 나열(동기화)할 수 있습니다. 
- Key Vault는 정기적으로 키를 다시 생성(회전)합니다.
- 키 값은 호출자에게 응답으로 반환되지 않습니다.
- Key Vault는 스토리지 계정과 클래식 스토리지 계정의 키를 모두 관리합니다.

자세한 내용은 다음을 참조하십시오.
- [스토리지 계정 액세스 키](../../storage/common/storage-account-keys-manage.md)
- [Azure Key Vault의 스토리지 계정 키 관리](../secrets/overview-storage-keys.md)


## <a name="storage-account-access-control"></a>스토리지 계정 액세스 제어

다음 권한은 관리되는 스토리지 계정에서 작업을 수행할 사용자 또는 애플리케이션 보안 주체에 권한을 부여할 때 사용할 수 있습니다.  

- 관리되는 스토리지 계정 및 SaS 정의 작업에 대한 권한
  - *get*: 스토리지 계정에 대한 정보 가져오기 
  - *list*: Key Vault에서 관리하는 스토리지 계정 나열
  - *update*: 스토리지 계정 업데이트
  - *delete*: 스토리지 계정 삭제  
  - *recover*: 삭제된 스토리지 계정 복구
  - *backup*: 스토리지 계정 백업
  - *restore*: Key Vault에 백업된 스토리지 계정 복원
  - *set*: 스토리지 계정 만들기 또는 업데이트
  - *regeneratekey*: 스토리지 계정에 대해 지정된 키 값을 다시 생성
  - *getsas*: 스토리지 계정의 SAS 정의에 대한 정보 가져오기
  - *listsas*: 스토리지 계정에 대한 스토리지 SAS 정의 나열
  - *deletesas*: 스토리지 계정에서 SAS 정의 삭제
  - *setsas*: 스토리지 계정에 대한 새 SAS 정의/특성 만들기 또는 업데이트

- 권한 있는 작업에 필요한 권한
  - *purge*: 관리 스토리지 계정 제거(영구적으로 삭제)

자세한 내용은 [Key Vault REST API 참조의 스토리지 계정 작업](/rest/api/keyvault)을 참조하세요. 권한 설정에 대한 내용은 [자격 증명 모음 - 만들기 또는 업데이트](/rest/api/keyvault/vaults/createorupdate) 및 [자격 증명 모음 - 액세스 정책 업데이트](/rest/api/keyvault/vaults/updateaccesspolicy)를 참조하세요.

Key Vault에서 액세스를 제어하는 방법 가이드:
- [CLI를 사용하여 Key Vault 액세스 정책 할당](../general/assign-access-policy-cli.md)
- [PowerShell을 사용하여 Key Vault 액세스 정책 할당](../general/assign-access-policy-powershell.md)
- [Azure Portal을 사용하여 Key Vault 액세스 정책 할당](../general/assign-access-policy-portal.md)
- [Azure 역할 기반 액세스 제어를 사용하여 Key Vault 키, 인증서 및 비밀에 대한 액세스 제공](../general/rbac-guide.md)


## <a name="next-steps"></a>다음 단계

- [Key Vault 정보](../general/overview.md)
- [키, 비밀 및 인증서에 대한 정보](../general/about-keys-secrets-certificates.md)
- [키 정보](../keys/about-keys.md)
- [인증서 정보](../certificates/about-certificates.md)
- [Key vault에 대한 액세스 보안](../general/security-features.md)
- [Key Vault 개발자 가이드](../general/developers-guide.md)
