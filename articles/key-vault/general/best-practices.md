---
title: Key Vault 사용에 대한 모범 사례 - Azure Key Vault | Microsoft Docs
description: 액세스 제어, 개별 키 자격 증명 모음을 사용하는 시기, 백업, 로깅 및 복구 옵션을 포함하여 Azure Key Vault의 모범 사례에 대해 알아봅니다.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: 0461228678762adbc4db936c35849f16a482c1a9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605509"
---
# <a name="best-practices-to-use-key-vault"></a>Key Vault 사용에 대한 모범 사례

## <a name="use-separate-key-vaults"></a>별도의 키 Key Vault 사용

환경(개발, 사전 프로덕션, 프로덕션)별로 애플리케이션당 자격 증명 모음을 사용하는 것이 좋습니다. 이를 통해 환경에서 비밀을 공유하지 않고 위반 시 위협을 줄일 수 있습니다.

### <a name="why-we-recommend-separate-key-vaults"></a>별도의 키 자격 증명 모음을 권장하는 이유

액세스 정책은 Azure Key Vault "전부 또는 전혀" 개념입니다. ID에 특정 권한(예:**Get**)이 있는 경우 ID는 자격 증명 모음에서 *비밀,* 키 또는 인증서를 얻을 수 있습니다. 즉, 중요한 데이터를 동일한 자격 증명 모음으로 그룹화하면 공격이 우려되는 중요한 정보에 액세스할 수 있기 때문에 보안 이벤트의 *반경이* 증가합니다. 이를 완화하려면 특정 애플리케이션이 *액세스해야* 하는 중요한 정보를 고려한 다음, 이 설명에 따라 키 자격 증명 모음을 분리합니다. 앱별로 키 자격 증명 모음을 분리하는 것이 가장 일반적인 경계입니다.

## <a name="control-access-to-your-vault"></a>자격 증명 모음에 대한 액세스 제어

Azure Key Vault는 암호화 키와 비밀(예: 인증서, 연결 문자열 및 암호)을 보호하는 클라우드 서비스입니다. 이 데이터는 민감하고 업무상 중요하기 때문에 권한이 부여된 애플리케이션과 사용자만 허용하여 Key Vault에 대한 액세스를 보호해야 합니다. 이 [문서](security-features.md)에는 Key Vault 액세스 모델에 대한 개요가 나와 있습니다. 여기서는 인증 및 권한 부여에 대해 설명하고 Key Vault 액세스의 보안을 유지하는 방법을 설명합니다.

자격 증명 모음에 대한 액세스를 제어할 때 제안 사항은 다음과 같습니다.
1. 구독, 리소스 그룹 및 Key Vault에 대한 액세스 잠금(Azure RBAC)
2. 모든 자격 증명 모음에 대한 액세스 정책 만들기
3. 최소 권한 액세스 보안 주체를 사용하여 액세스 권한 부여
4. 방화벽 및 [VNET 서비스 엔드포인트](overview-vnet-service-endpoints.md) 켜기

## <a name="backup"></a>Backup

자격 증명 모음 내에서 개체의 업데이트/삭제/만들기 자격 증명 모음을 정기적으로 백업해야 합니다.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell 백업 명령

* [인증서 백업](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [키 백업](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [백업 비밀](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Azure CLI 백업 명령

* [인증서 백업](/cli/azure/keyvault/certificate#az_keyvault_certificate_backup)
* [키 백업](/cli/azure/keyvault/key#az_keyvault_key_backup)
* [백업 비밀](/cli/azure/keyvault/secret#az_keyvault_secret_backup)


## <a name="turn-on-logging"></a>로깅 켜기

자격 증명 모음에 대한 [로깅을 켭니다](logging.md). 또한 경고를 설정합니다.

## <a name="turn-on-recovery-options"></a>복구 옵션 설정

1. [일시 삭제](soft-delete-overview.md)를 켭니다.
2. 일시 삭제를 켠 후에도 비밀/자격 증명 모음을 강제 삭제되지 않도록 보호하려면 제거 보호를 켭니다.

## <a name="learn-more"></a>자세한 정보
- [Key Vault 비밀 관리에 대한 모범 사례](../secrets/secrets-best-practices.md)