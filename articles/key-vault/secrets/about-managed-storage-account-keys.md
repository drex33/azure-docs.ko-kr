---
title: Azure Key Vault 관리형 스토리지 계정 키 정보 - Azure Key Vault
description: Azure Key Vault 관리형 스토리지 계정 키의 개요입니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 10/01/2021
ms.author: mbaldwin
ms.openlocfilehash: e2b72dd6960232dd3b9afd05ff19edccaeface12
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390660"
---
# <a name="about-azure-key-vault-managed-storage-account-keys"></a>Azure Key Vault 관리형 스토리지 계정 키 정보

> [!IMPORTANT]
> Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스인 Azure AD(Azure Active Directory)와 Azure Storage를 통합하여 사용하는 것이 좋습니다. Azure AD 통합은 [Azure BLOB 및 큐](../../storage/blobs/authorize-access-azure-active-directory.md)에 사용할 수 있으며, Azure Key Vault와 마찬가지로 Azure Storage에 대한 OAuth2 토큰 기반 액세스를 제공합니다. Azure AD를 사용하면 스토리지 계정 자격 증명 대신 애플리케이션 또는 사용자 ID를 사용하여 클라이언트 애플리케이션을 인증할 수 있습니다. Azure에서 실행할 때 [Azure AD 관리 ID](../../active-directory/managed-identities-azure-resources/index.yml)를 사용할 수 있습니다. 관리 ID를 사용하면 클라이언트 인증이 필요 없는 것은 물론이고, 애플리케이션에 또는 애플리케이션을 통해 자격 증명을 저장할 필요가 없습니다. Azure AD 인증을 사용할 수 없는 경우에만 아래 솔루션을 사용하세요.

Azure 스토리지 계정은 계정 이름과 키로 구성된 자격 증명을 사용합니다. 키는 자동 생성되며 암호화 키가 아닌 암호 역할을 합니다. Key Vault는 스토리지 계정에서 스토리지 계정 키를 주기적으로 다시 생성하여 스토리지 계정 키를 관리하며, 스토리지 계정의 리소스에 대한 위임된 액세스가 가능하도록 공유 액세스 서명 토큰을 제공합니다.

Key Vault 관리형 스토리키 계정 키 기능을 사용하여 키를 나열(Azure 스토리지 계정과 동기화)하고, 주기적으로 키를 다시 생성(순환)할 수 있습니다. 스토리지 계정과 클래식 스토리지 계정의 키를 모두 관리할 수 있습니다.

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

## <a name="next-steps"></a>다음 단계

- [Key Vault 및 Azure CLI를 사용하여 스토리지 계정 키 관리](overview-storage-keys.md)
- [Key Vault 정보](../general/overview.md)
- [키, 비밀 및 인증서에 대한 정보](../general/about-keys-secrets-certificates.md)
- [Key Vault의 비밀 관리 모범 사례](secrets-best-practices.md)
- [Key Vault 개발자 가이드](../general/developers-guide.md)
