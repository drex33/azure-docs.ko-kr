---
title: 암호 관리에 대 한 모범 사례-Azure Key Vault | Microsoft Docs
description: Azure Key Vault 암호 관리에 대 한 모범 사례를 알아봅니다.
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 09/21/2021
ms.author: mbaldwin
ms.openlocfilehash: 4735fcfd28830c8469ddfbed7b521a507b3ca521
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534681"
---
# <a name="best-practices-for-secrets-management-in-key-vault"></a>Key Vault의 비밀 관리 모범 사례

Azure Key Vault를 사용 하면 암호 및 액세스 키와 같은 서비스 또는 응용 프로그램 자격 증명을 비밀으로 안전 하 게 저장할 수 있습니다. 키 자격 증명 모음의 모든 암호는 소프트웨어 키를 사용 하 여 암호화 됩니다. Key Vault를 사용 하는 경우 응용 프로그램에 보안 정보를 더 이상 저장할 필요가 없습니다. 애플리케이션에 보안 정보를 저장하지 않으면 코드의 해당 정보 부분을 만들 필요가 없습니다.

Key Vault에 저장 해야 하는 암호의 예:

- 클라이언트 응용 프로그램 암호
- 연결 문자열
- 암호
- 액세스 키 (Redis Cache, Azure Event Hubs, Azure Cosmos DB)
- SSH 키

IP 주소, 서비스 이름 및 기타 구성 설정과 같은 기타 중요 한 정보는 Key Vault 대신 [Azure 앱 구성](../../azure-app-configuration/overview.md) 에 저장 해야 합니다.

각 개인 키 자격 증명 모음은 비밀에 대 한 보안 경계를 정의 합니다. 환경 별로 응용 프로그램당 응용 프로그램당 단일 key vault의 경우 응용 프로그램에 대해 비밀의 세분화 된 격리를 제공 하는 것이 좋습니다.

Key Vault에 대 한 모범 사례에 대 한 자세한 내용은 [Key Vault 사용 모범 사례](../general/best-practices.md)를 참조 하세요.

## <a name="configuration-and-storing"></a>구성 및 저장 

회전 하는 동안 생성 되는 자격 증명의 동적 부분을 값으로 저장 합니다. 예를 들면 클라이언트 응용 프로그램 암호, 암호 및 액세스 키가 있습니다. 사용자 이름, 응용 프로그램 Id 및 서비스 Url과 같은 관련 정적 특성 및 식별자는 모두 비밀 태그로 저장 되 고 회전 하는 동안 새 버전의 암호로 복사 됩니다.

비밀에 대 한 자세한 내용은 [Azure Key Vault 비밀](about-secrets.md)정보를 참조 하세요.

## <a name="secrets-rotation"></a>비밀 회전
비밀은 종종 응용 프로그램 메모리에 환경 변수 또는 전체 응용 프로그램 수명 주기에 대 한 구성 설정으로 저장 되므로 원치 않는 노출을 인식 하 게 됩니다. 암호는 누출 또는 노출을 인식 하므로 적어도 60 일 마다 자주 회전 하는 것이 중요 합니다.

비밀 회전 프로세스에 대 한 자세한 내용은 [두 개의 인증 자격 증명 집합이 있는 리소스에 대 한 비밀의 회전 자동화](tutorial-rotation-dual.md)를 참조 하세요. 

## <a name="access-and-network-isolation"></a>액세스 및 네트워크 격리

액세스 권한이 있는 IP 주소를 지정하여 자격 증명 모음의 노출을 줄일 수 있습니다. 응용 프로그램 및 관련 서비스가 자격 증명 모음의 암호에 액세스 하도록 허용 하 여 공격자가 암호에 액세스 하는 기능을 줄이도록 방화벽을 구성 합니다.

네트워크 보안에 대 한 자세한 내용은 [Azure Key Vault 네트워킹 설정 구성](../general/how-to-azure-key-vault-network-security.md)을 참조 하세요.

또한 응용 프로그램은 읽기 암호에 대 한 액세스 권한만 부여 하 여 최소한의 권한 있는 액세스를 수행 해야 합니다. 액세스 정책 또는 Azure 역할 기반 액세스 제어를 사용 하 여 암호에 대 한 액세스를 제어할 수 있습니다. 

Azure Key Vault의 access control에 대 한 자세한 내용은 다음을 참조 하세요.
- [Azure 역할 기반 액세스 제어를 사용 하 여 Key Vault 키, 인증서 및 암호에 대 한 액세스 제공](../general/rbac-guide.md)
- [Key Vault 액세스 정책 할당](../general/assign-access-policy.md)
 
## <a name="service-limits-and-caching"></a>서비스 제한 및 캐싱
Key Vault 원래 [Azure Key Vault 서비스 제한](../general/service-limits.md)에 지정 된 제한 제한을 사용 하 여 만들어졌습니다. 처리량 속도를 최대화 하기 위해 권장 되는 두 가지 모범 사례는 다음과 같습니다.
- 응용 프로그램에서 8 시간 이상 암호를 캐시 합니다.
- 서비스 제한을 초과 하는 경우 시나리오를 처리 하는 지 수 백오프 재시도 논리를 구현 합니다.

제한 지침에 대 한 자세한 내용은 [Azure Key Vault 제한 지침](../general/overview-throttling.md)을 참조 하세요.

## <a name="monitoring"></a>모니터링
비밀 및 해당 수명 주기에 대 한 액세스를 모니터링 하려면 Key Vault 로깅을 설정 합니다. [Azure Monitor](../../azure-monitor/overview.md) 를 사용 하 여 모든 자격 증명 모음의 모든 비밀 활동을 한 곳에서 모니터링할 수 있습니다. 또는 [Azure Event Grid](../../event-grid/overview.md) 를 사용 하 여 Azure Logic Apps 및 Azure Functions와 쉽게 통합할 수 있으므로 암호의 수명 주기를 모니터링할 수 있습니다.

자세한 내용은 다음을 참조하십시오.
- [Event Grid 원본으로 사용되는 Azure Key Vault](../../event-grid/event-schema-key-vault.md?tabs=event-grid-event-schema.md)
- [Azure Key Vault 로깅](../general/logging.md)
- [Azure Key Vault 모니터링 및 경고](../general/alert.md)

## <a name="backup-and-purge-protection"></a>백업 및 보호 제거
[제거 방지](../general/soft-delete-overview.md#purge-protection) 를 설정 하 여 비밀의 강제 삭제를 방지 합니다. 자격 증명 모음 내에서 비밀을 업데이트, 삭제 또는 만들 때 자격 증명 모음을 정기적으로 백업 합니다.

- Azure PowerShell 백업 명령에 대 한 자세한 내용은 [백업 비밀](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)을 참조 하세요.
- Azure CLI 백업 명령에 대 한 자세한 내용은 [백업 비밀](/cli/azure/keyvault/secret#az_keyvault_secret_backup)을 참조 하세요.

## <a name="learn-more"></a>자세한 정보
- [Azure Key Vault 비밀 정보](about-secrets.md)
- [Key Vault 사용에 대한 모범 사례](../general/best-practices.md)
