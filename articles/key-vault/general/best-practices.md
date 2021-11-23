---
title: Azure Key Vault 사용에 대 한 모범 사례 | Microsoft Docs
description: 액세스 제어, 개별 키 자격 증명 모음을 사용하는 시기, 백업, 로깅 및 복구 옵션을 포함하여 Azure Key Vault의 모범 사례에 대해 알아봅니다.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: 1cddc0870f9a90b315c9cb9ef2635f320dad5469
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132953713"
---
# <a name="best-practices-for-using-azure-key-vault"></a>Azure Key Vault 사용에 대 한 모범 사례

Azure Key Vault는 암호화 키 및 암호 (예: 인증서, 연결 문자열 및 암호)를 보호 합니다. 이 문서는 주요 자격 증명 모음 사용을 최적화 하는 데 도움이 됩니다.

## <a name="use-separate-key-vaults"></a>별도의 키 자격 증명 모음 사용

환경 (개발, 프로덕션 전 및 프로덕션) 별로 응용 프로그램당 자격 증명 모음을 사용 하는 것이 좋습니다. 이렇게 하면 환경 및 지역에서 비밀을 공유 하지 않습니다. 또한 위반이 발생할 경우 위협을 감소 시킵니다.

### <a name="why-we-recommend-separate-key-vaults"></a>별도의 키 자격 증명 모음을 권장 하는 이유

키 자격 증명 모음은 저장 된 암호의 보안 경계를 정의 합니다. 동일한 자격 증명 모음에 비밀을 그룹화 하면 공격이 문제에 대 한 암호에 액세스할 수 있으므로 보안 이벤트의 *폭발 반지름이* 높아집니다. 문제에 대 한 액세스를 완화 하려면 특정 응용 프로그램이 액세스할 *수 있는 암호* 를 고려 하 고이 경계에 따라 키 자격 증명 모음을 분리 합니다. 주요 자격 증명 모음을 응용 프로그램 별로 분리 하는 것이 가장 일반적인 경계입니다. 그러나 보안 경계는 관련 된 서비스 그룹당 예를 들어, 규모가 많은 응용 프로그램에 대해 더 세부적으로 지정할 수 있습니다.

## <a name="control-access-to-your-vault"></a>자격 증명 모음에 대 한 액세스 제어

인증서, 연결 문자열, 암호 등의 암호화 키와 암호는 중요 하 고 업무상 중요 합니다. 인증 된 응용 프로그램 및 사용자만 허용 하 여 키 자격 증명 모음에 대 한 액세스를 보호 해야 합니다. [Azure Key Vault 보안 기능](security-features.md) Key Vault 액세스 모델의 개요를 제공 합니다. 인증 및 권한 부여에 대해 설명 합니다. 또한 키 자격 증명 모음에 대 한 액세스를 보호 하는 방법을 설명 합니다.

자격 증명 모음에 대 한 액세스를 제어 하기 위한 제안 사항은 다음과 같습니다.
- 구독, 리소스 그룹 및 주요 자격 증명 모음 (RBAC (역할 기반 액세스 제어))에 대 한 액세스를 잠급니다.
- 모든 자격 증명 모음에 대 한 액세스 정책을 만듭니다.
- 최소 권한 액세스의 원칙을 사용 하 여 액세스 권한을 부여 합니다.
- 방화벽 및 [가상 네트워크 서비스 끝점](overview-vnet-service-endpoints.md)을 켭니다.

## <a name="backup"></a>Backup

자격 증명 모음의 정기 백업을 수행 해야 합니다. 자격 증명 모음에서 개체를 업데이트, 삭제 또는 만들 때 백업을 수행 해야 합니다. 

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell 백업 명령

* [인증서 백업](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [백업 키](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [백업 비밀](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Azure CLI 백업 명령

* [인증서 백업](/cli/azure/keyvault/certificate#az_keyvault_certificate_backup)
* [백업 키](/cli/azure/keyvault/key#az_keyvault_key_backup)
* [백업 비밀](/cli/azure/keyvault/secret#az_keyvault_secret_backup)


## <a name="turn-on-logging"></a>로깅 켜기

자격 증명 모음에 대 한 [로깅을 설정](logging.md) 합니다. 또한 경고를 설정 합니다.

## <a name="turn-on-recovery-options"></a>복구 옵션 설정

- [일시 삭제](soft-delete-overview.md)를 켭니다.
- 일시 삭제가 설정 된 후에도 암호 및 키 자격 증명 모음을 강제로 삭제 하지 않도록 보호 하려면 제거 보호를 설정 합니다.

## <a name="learn-more"></a>자세한 정보
- [Key Vault의 비밀 관리 모범 사례](../secrets/secrets-best-practices.md)
