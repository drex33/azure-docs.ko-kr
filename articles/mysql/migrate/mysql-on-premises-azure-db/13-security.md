---
title: 'MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 보안'
description: 클라우드 기반 서비스로 이동한다고 해서 전체 인터넷이 항상 액세스할 수 있는 것은 아닙니다.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: f0d0613dca258050cec75d544438b781c1f9fe96
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285235"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-security"></a>MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션: 보안

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>사전 요구 사항

[비즈니스 연속성 및 재해 복구](12-business-continuity-and-disaster-recovery.md)

## <a name="overview"></a>개요

클라우드 기반 서비스로 이동한다고 해서 전체 인터넷이 항상 액세스할 수 있는 것은 아닙니다. Azure는 데이터 워크로드가 악의적인 행위자와 불량 프로그램으로부터 지속적으로 보호되도록 하는 동급 최고의 보안을 제공합니다.

## <a name="authentication"></a>인증

Azure Database for MySQL은 MySQL 사용자 연결을 위한 기본 인증 메커니즘을 지원하지만 [Azure Active Directory와의 통합](../../concepts-azure-ad-authentication.md)도 지원합니다. 이 보안 통합은 MySQL 로그인 프로세스 동안 암호처럼 작동하는 토큰을 발행하여 작동합니다. [Active Directory 통합 구성](../../howto-configure-sign-in-azure-ad-authentication.md)은 매우 간단하며 사용자뿐만 아니라 AAD 그룹도 지원합니다.

이러한 긴밀한 통합을 통해 관리자와 애플리케이션은 [Azure Identity Protection](../../../active-directory/identity-protection/overview-identity-protection.md)의 향상된 보안 기능을 활용하여 모든 ID 문제를 파악할 수 있습니다.

> [!NOTE] 
> 이 보안 기능은 MySQL 5.7 이상에서 지원됩니다. `clear-text` 옵션이 제공되는 한 대부분의 [애플리케이션 드라이버](../../howto-configure-sign-in-azure-ad-authentication.md)가 지원됩니다.

## <a name="threat-protection"></a>위협 보호

사용자 또는 애플리케이션 자격 증명이 손상된 경우 실패한 로그인 시도가 로그에 반영되지 않을 수 있습니다. 손상된 자격 증명으로 인해 악의적인 사용자가 데이터에 액세스하고 다운로드할 수 있습니다. [Azure Threat Protection](../../concepts-security.md#threat-protection)은 로그인의 이상(예: 비정상적인 위치, 희귀 사용자 또는 무차별 대입 공격) 및 기타 의심스러운 활동을 감시할 수 있습니다. 올바르지 않은 것처럼 `look` 경우 관리자에게 알림을 보낼 수 있습니다.

## <a name="audit-logging"></a>감사 로깅

MySQL에는 강력한 기본 제공 감사 로그 기능이 있습니다. 기본적으로 이 [감사 로그 기능은 Azure Database for MySQL에서 사용하지 않도록 설정됩니다](../../concepts-audit-logs.md). `audit\_log\_enabled` 서버 매개 변수를 변경하여 서버 수준 로깅을 사용하도록 설정할 수 있습니다. 사용하도록 설정되면 [진단 로깅](../../howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)을 켜면 [Azure Monitor](../../../azure-monitor/overview.md) 및 [Log Analytics](../../../azure-monitor/logs/design-logs-deployment.md)를 통해 로그에 액세스할 수 있습니다.

사용자 연결 관련 이벤트를 쿼리하려면 다음 KQL 쿼리를 실행하세요.

```
AzureDiagnostics  
| where ResourceProvider =="MICROSOFT.DBFORMYSQL" 
| where Category == 'MySqlAuditLogs' and event\_class\_s == "connection\_log"  
| project TimeGenerated, LogicalServerName\_s, event\_class\_s, event\_subclass\_s  
, event\_time\_t, user\_s , ip\_s , sql\_text\_s  
| order by TimeGenerated asc
```

## <a name="encryption"></a>암호화

MySQL 인스턴스의 데이터는 기본적으로 미사용 시 암호화됩니다. 모든 자동 백업도 암호화되어 권한이 없는 당사자에게 데이터가 누출될 가능성을 방지합니다. 이 암호화는 일반적으로 인스턴스가 만들어질 때 만들어진 키로 수행됩니다. 이 기본 암호화 키 외에도 관리자는 [BYOK(Bring Your Own Key)](../../concepts-data-encryption-mysql.md) 옵션을 사용할 수 있습니다.

고객 관리형 키 전략을 사용할 때 키 수명 주기 관리에 대한 책임을 이해하는 것이 중요합니다. 고객 키는 [Azure Key Vault](../../../key-vault/general/basic-concepts.md)에 저장된 다음 정책을 통해 액세스됩니다. 키 관리에 대한 모든 권장 사항을 따르는 것이 중요합니다. 암호화 키 손실은 데이터 액세스 손실과 동일합니다.

고객 관리형 키 외에도 서비스 수준 키를 사용하여 [이중 암호화를 추가](../../concepts-infrastructure-double-encryption.md)합니다. 이 기능을 구현하면 높은 수준으로 암호화된 미사용 데이터를 제공할 수 있지만 암호화 성능이 저하됩니다. 테스트를 수행해야 합니다.

SSL/TLS를 사용하여 전송 중에 데이터를 암호화할 수 있습니다. 앞에서 설명한 것처럼 이 변경을 지원하고 적절한 TLS 유효성 검사 설정도 구성하려면 [애플리케이션을 수정](../../howto-configure-ssl.md)해야 할 수 있습니다.

## <a name="firewall"></a>방화벽

사용자가 설정되고 미사용 데이터가 암호화되면 마이그레이션 팀은 네트워크 데이터 흐름을 검토해야 합니다. Azure Database for MySQL은 권한이 부여된 사용자, 애플리케이션 및 디바이스로만 액세스를 제한하여 네트워킹 계층을 보호하는 여러 메커니즘을 제공합니다.

MySQL 인스턴스를 보호하기 위한 첫 번째 방어선은 [방화벽 규칙](../../concepts-firewall-rules.md)을 구현하는 것입니다. 내부 또는 외부 IP를 통해 인스턴스에 액세스할 때 IP 주소는 유효한 위치로만 제한될 수 있습니다. MySQL 인스턴스가 내부 애플리케이션만 제공하는 경우 [공개 액세스를 제한](../../howto-deny-public-network-access.md)합니다.

애플리케이션을 MySQL 워크로드와 함께 Azure로 이동할 때 [가상 네트워크 피어링](../../../virtual-network/virtual-network-peering-overview.md)을 구성해야 하는 허브 및 스포크 패턴에 여러 가상 네트워크가 설정되었을 수 있습니다.

## <a name="private-link"></a>프라이빗 링크

Azure Database for MySQL에 대한 액세스를 내부 Azure 리소스로 제한하려면 [Private Link](../../concepts-data-access-security-private-link.md)를 사용하도록 설정합니다. Private Link는 MySQL 인스턴스에 공용 IP 주소가 아닌 개인 IP가 할당되도록 합니다.

> [!NOTE]
> 이 가이드에서 중점적으로 설명하지 않은 다른 많은 [기본 Azure 네트워킹 고려 사항](../../concepts-data-access-and-security-vnet.md)을 고려해야 합니다.

모든 Azure 리소스에서 구현할 수 있는 잠재적인 [보안 기준](/azure/mysql/security-baseline) 작업 집합을 검토합니다. 참조 링크에 설명된 모든 항목이 특정 데이터 워크로드 또는 Azure 리소스에 적용되는 것은 아닙니다.

## <a name="security-checklist"></a>보안 검사 목록

  - 가능한 경우 Azure AD 인증을 사용합니다.

  - Advanced Threat Protection을 사용하도록 설정합니다.

  - 모든 감사 기능을 사용하도록 설정합니다.

  - BYOK(Bring-Your-Own-Key) 전략을 고려합니다.

  - 방화벽 규칙을 구현합니다.

  - 인터넷을 통해 이동하지 않는 워크로드에 대해 프라이빗 엔드포인트를 활용합니다.  


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [요약](./14-summary.md)