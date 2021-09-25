---
title: 비밀 관리에 대한 모범 사례 - Azure Key Vault | Microsoft Docs
description: Azure Key Vault 비밀 관리에 대한 모범 사례 알아보기
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: mbaldwin
ms.openlocfilehash: 977986805d88ef2677f22911bd63513a813810d3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634248"
---
# <a name="best-practices-for-secrets-management-in-key-vault"></a>Key Vault 비밀 관리에 대한 모범 사례

Azure Key Vault 사용하면 서비스 또는 애플리케이션 자격 증명(예: 암호, 액세스 키)을 비밀로 안전하게 저장할 수 있습니다. Key Vault 모든 비밀은 소프트웨어 키로 암호화되어 저장됩니다. Key Vault를 사용하면 애플리케이션 개발자가 더 이상 애플리케이션에 보안 정보를 저장할 필요가 없습니다. 애플리케이션에 보안 정보를 저장하지 않으면 코드의 해당 정보 부분을 만들 필요가 없습니다. 

Key Vault 저장해야 하는 비밀의 예:

- 클라이언트 애플리케이션 비밀
- 연결 문자열
- 암호
- 액세스 키(Redis Cache, Event Hub, Cosmos DB)
- SSH 키

IP 주소, 서비스 이름 및 기타 구성 설정과 같은 기타 중요한 정보는 [대신 Azure App Configuration](../../azure-app-configuration/overview.md) 저장해야 합니다.

각 개별 키 자격 증명 모음은 비밀에 대한 보안 경계를 정의합니다. 애플리케이션에 대한 세부적인 비밀 격리를 제공하려면 애플리케이션당, 지역별, 환경별 단일 키 자격 증명 모음을 권장합니다. 

키 자격 증명 모음에 대한 모범 사례에 대한 자세한 내용은 다음을 참조하세요.
- [Key Vault 사용에 대한 모범 사례](../general/best-practices.md)

## <a name="configuration-and-storing"></a>구성 및 저장 

클라이언트 애플리케이션 비밀, 암호, 액세스 키와 같은 자격 증명의 동적 부분을 값으로 비밀에 저장하는 것이 좋습니다. 사용자 이름, AppIds, 서비스 URL과 같은 모든 관련 정적 특성 및 식별자는 비밀 태그로 저장해야 합니다.

비밀에 대한 자세한 내용은 다음을 참조하세요.
- [Azure Key Vault 비밀 정보](about-secrets.md)

## <a name="secrets-rotation"></a>비밀 회전
비밀은 애플리케이션 메모리에 환경 변수 또는 전체 애플리케이션 수명 주기의 구성 설정으로 여러 번 저장되어 원치 않는 노출에 민감합니다. 비밀은 누출 또는 노출에 민감하기 때문에 적어도 60일마다 비밀을 자주 순환하는 것이 중요합니다. 

비밀 회전 프로세스에 대한 자세한 내용은 다음을 참조하세요.
- [두 개의 인증 자격 증명 세트를 사용하는 리소스의 비밀 순환 자동화](tutorial-rotation-dual.md) 

## <a name="access-and-network-isolation"></a>액세스 및 네트워크 격리

액세스 권한이 있는 IP 주소를 지정하여 자격 증명 모음의 노출을 줄일 수 있습니다. 애플리케이션 및 관련 서비스만 자격 증명 모음의 비밀에 액세스하도록 허용하여 공격자가 비밀에 액세스하는 기능을 줄이도록 방화벽을 구성합니다. 

네트워크 보안에 대한 자세한 내용은 다음을 참조하세요.
- [Azure Key Vault 네트워킹 설정 구성](../general/how-to-azure-key-vault-network-security.md)

또한 애플리케이션은 비밀을 읽을 수 있는 액세스 권한만 있으면 최소 권한 액세스를 따라야 합니다. 비밀 액세스는 액세스 정책 또는 Azure 역할 기반 액세스 제어를 통해 제어할 수 있습니다. 

Azure Key Vault 액세스 제어에 대한 자세한 내용은 다음을 참조하세요.
- [Azure 역할 기반 액세스 제어를 사용하여 Key Vault 키, 인증서 및 비밀에 대한 액세스 제공](../general/rbac-guide.md)
- [Key Vault 액세스 정책 할당](../general/assign-access-policy.md)
 
## <a name="service-limits-and-caching"></a>서비스 제한 및 캐싱
Key Vault는 원래 [Azure Key Vault 서비스 제한](../general/service-limits.md)에 지정된 한도에 의해 만들어졌습니다. Key Vault 처리량 속도를 최대화하려면 처리량을 최대화하기 위한 몇 가지 권장 지침/모범 사례는 다음과 같습니다.
- 최소 8시간 동안 애플리케이션에서 비밀 캐시
- 서비스 제한을 초과하는 시나리오를 처리하기 위한 지수 백오프 재시도 논리 구현

제한 지침에 대한 자세한 내용은 다음을 참조하세요.
- [Azure Key Vault 제한 지침](../general/overview-throttling.md)

## <a name="monitoring"></a>모니터링
자격 증명 모음에 대한 로깅을 켜서 비밀 및 수명 주기에 대한 액세스를 모니터링합니다. [Azure Monitor](https://docs.microsoft.com//azure/azure-monitor/overview) 사용하여 한 곳에서 모든 자격 증명 모음의 모든 비밀 활동을 모니터링하거나 [Azure Event Grid Logic Apps](https://docs.microsoft.com/azure/event-grid/overview) 및 Azure Functions 쉽게 통합하여 비밀 수명 주기를 모니터링할 수 있습니다.

자세한 내용은 다음을 참조하세요.
- [Event Grid 원본으로 사용되는 Azure Key Vault](https://docs.microsoft.com/azure/event-grid/event-schema-key-vault?tabs=event-grid-event-schema)
- [Azure Key Vault 로깅](../general/logging.md)
- [Azure Key Vault 모니터링 및 경고](../general/alert.md)

## <a name="backup-and-purge-protection"></a>백업 및 제거 보호
[비밀의](../general/soft-delete-overview.md#purge-protection) 강제 삭제를 방지하려면 제거 보호를 켭니다. 자격 증명 모음 내에서 비밀을 업데이트/삭제/만들 때 자격 증명 모음을 정기적으로 백업합니다.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell 백업 명령

* [백업 비밀](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Azure CLI 백업 명령

* [백업 비밀](/cli/azure/keyvault/secret#az_keyvault_secret_backup)

## <a name="learn-more"></a>자세한 정보
- [Azure Key Vault 비밀 정보](about-secrets.md)
- [Key Vault 사용에 대한 모범 사례](../general/best-practices.md)
