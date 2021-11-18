---
title: Azure Key Vault 보안 개요
description: Azure Key Vault에 대한 보안 기능 및 모범 사례에 대한 개요입니다.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: f3ec562a6a33c2211f6acaf661a1ee3125a86149
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718073"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault 보안

Azure Key Vault는 클라우드에서 암호화 키, 인증서(및 인증서와 연결된 개인 키) 및 비밀(예: 연결 문자열 및 암호)을 보호합니다. 그러나 중요한 데이터와 중요 비즈니스용 데이터를 저장하는 경우 자격 증명 모음과 여기에 저장된 데이터의 보안을 극대화하기 위한 단계를 수행해야 합니다.

이 문서에서는 Azure Key Vault에 대한 보안 기능 및 모범 사례에 대한 개요를 제공합니다.

> [!NOTE]
> Azure Key Vault 보안 권장 사항에 대한 포괄적인 목록은 [Azure Key Vault 보안 기준](security-baseline.md)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

액세스 권한이 있는 IP 주소를 지정하여 자격 증명 모음의 노출을 줄일 수 있습니다. Azure Key Vault의 가상 네트워크 서비스 엔드포인트를 사용하면 지정된 가상 네트워크에 대한 액세스를 제한할 수 있습니다. 엔드포인트를 사용하면 IPv4(인터넷 프로토콜 버전 4) 주소 범위 목록에 대한 액세스를 제한할 수도 있습니다. 해당 소스 외부에서 키 자격 증명 모음에 연결하는 모든 사용자는 액세스가 거부됩니다.  자세한 내용은 [Azure Key Vault에 대한 가상 네트워크 서비스 엔드포인트](overview-vnet-service-endpoints.md)를 참조하세요.

방화벽 규칙이 적용되면 사용자의 요청이 허용되는 가상 네트워크 또는 IPv4 주소 범위에서 시작된 경우에만 사용자가 Key Vault에서 데이터를 읽을 수 있습니다. Azure Portal에서 Key Vault에 액세스하는 경우도 마찬가지입니다. 사용자가 Azure Portal에서 키 자격 증명 모음으로 이동할 수 있다고 해도 해당 클라이언트 머신이 허용 목록에 없는 경우 키, 비밀 또는 인증서를 나열하지 못할 수 있습니다. 구현 단계는 [Azure Key Vault 방화벽 및 가상 네트워크 구성](network-security.md)을 참조하세요.

Azure Private Link Service를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure Key Vault 및 Azure 호스팅 고객/파트너 서비스에 액세스할 수 있습니다. Azure 프라이빗 엔드포인트는 Azure Private Link에서 제공하는 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.  구현 단계는 [Azure Private Link와 Key Vault 통합](private-link-service.md)을 참조하세요.

## <a name="tls-and-https"></a>TLS 및 HTTPS

- Key Vault 프런트 엔드(데이터 평면)는 다중 테넌트 서버입니다. 즉, 서로 다른 고객의 키 자격 증명 모음이 동일한 공용 IP 주소를 공유할 수 있습니다. 격리를 위해 각 HTTP 요청은 다른 요청과 독립적으로 인증되고 권한이 부여됩니다.
- 이전 버전의 TLS는 취약성을 보고할 수 있지만 공용 IP 주소는 공유되므로 키 자격 증명 모음 서비스 팀에서 전송 수준에서 개별 키 자격 증명 모음에 대해 이전 버전의 TLS를 사용하지 않도록 설정할 수 없습니다.
- HTTPS 프로토콜을 사용하면 클라이언트가 TLS 협상에 참여할 수 있습니다. **클라이언트는 최신 버전의 TLS를 적용할 수 있으며** 클라이언트가 해당 작업을 수행할 때마다 전체 연결에서 해당하는 수준 보호를 사용합니다. 통신하거나 Azure Active Directory 대해 인증하는 애플리케이션은 TLS 1.2 또는 최신 버전을 사용하여 통신할 수 없는 경우 예상대로 작동하지 않을 수 있습니다.
- TLS 프로토콜의 알려진 취약성에도 불구하고 공격자가 취약성이 있는 TLS 버전과의 연결을 시작할 때 악성 에이전트가 키 자격 증명 모음에서 어떤 정보든 추출할 수 있도록 하는 알려진 공격은 없습니다. 공격자는 자신을 인증하고 권한을 부여해야 하며 합법적인 클라이언트가 항상 최신 TLS 버전에 연결하는 한 이전 TLS 버전의 취약성으로 인해 자격 증명이 누출되지 않습니다.

> [!NOTE]
> Azure Key Vault 경우 Keyvault 서비스에 액세스하는 애플리케이션이 TLS 1.2 또는 최신 버전을 지원하는 계획형에서 실행되어야 합니다. 애플리케이션이 .Net framework에 종속된 경우 업데이트해야 합니다. 또한 [이 문서에](/troubleshoot/azure/active-directory/enable-support-tls-environment) 언급된 레지스트리를 변경하여 OS 수준 및 .Net Framework에서 TLS 1.2를 명시적으로 사용하도록 설정할 수 있습니다.

## <a name="key-vault-authentication-options"></a>Key Vault 인증 옵션

Azure 구독에 Key Vault을 만들 때 해당 구독의 Azure AD 테넌트에 자동으로 연결됩니다. 두 평면의 모든 호출자가 이 테넌트에 등록해야 하고, 해당 Key Vault에 액세스하기 위해 인증을 받아야 합니다. 두 경우 모두 애플리케이션에서 다음과 같은 두 가지 방법으로 Key Vault에 액세스할 수 있습니다.

- **애플리케이션 전용**: 애플리케이션은 서비스 주체 또는 관리 ID를 나타냅니다. 해당 ID는 주기적으로 키 자격 증명 모음에서 인증서, 키 또는 암호에 액세스해야 하는 애플리케이션에 가장 일반적으로 사용되는 시나리오입니다. 해당 시나리오가 작동하려면 액세스 정책에서 애플리케이션의 `objectId`가 지정되어야 하며 `applicationId`는 지정되지 _않거나_ `null`이어야 합니다.
- **사용자 전용**: 사용자는 테넌트에 등록된 애플리케이션에서 키 자격 증명 모음에 액세스합니다. Azure PowerShell과 Azure Portal이 이러한 액세스 유형의 예제입니다. 해당 시나리오가 작동하려면 액세스 정책에서 사용자의 `objectId`가 지정되어야 하며 `applicationId`는 지정되지 _않거나_ `null`이어야 합니다.
- **애플리케이션 + 사용자**(_복합 ID_ 라고도 함): 사용자는 특정 애플리케이션에서 키 자격 증명 모음에 액세스해야 _하며_ 애플리케이션은 사용자로 가장하기 위해 OBO(On-Behalf-Of 인증) 흐름을 사용해야 합니다. 해당 시나리오가 작동하려면 액세스 정책에서 `applicationId` 및 `objectId` 모두 지정되어야 합니다. `applicationId`는 필수 애플리케이션을 식별하고 `objectId`는 사용자를 식별합니다. 현재 해당 옵션은 데이터 평면 Azure RBAC에서 사용할 수 없습니다.

모든 유형의 액세스에서 애플리케이션은 Microsoft Azure AD를 사용하여 인증합니다. 애플리케이션은 애플리케이션 유형에 따라 [지원되는 인증 방법](../../active-directory/develop/authentication-vs-authorization.md)을 사용합니다. 애플리케이션은 액세스 권한을 부여하기 위해 평면의 리소스에 대해 토큰을 획득합니다. 리소스는 Azure 환경에 따라 관리 또는 데이터 평면의 엔드포인트입니다. 애플리케이션은 해당 토큰을 사용하고 REST API 요청을 Key Vault에 보냅니다. 자세한 내용은 [전체 인증 흐름](../../active-directory/develop/v2-oauth2-auth-code-flow.md)을 참조하세요.

두 평면에 대한 인증에 단일 메커니즘을 사용할 경우 다음과 같은 몇 가지 이점이 있습니다.

- 조직에서는 조직의 모든 Key Vault에 대한 액세스를 중앙에서 제어할 수 있습니다.
- 사용자가 떠나는 경우 곧바로 조직의 모든 Key Vault에 대한 액세스 권한을 잃게 됩니다.
- 조직은 Azure AD에 있는 옵션(예: 추가 보안을 위해 다단계 인증 사용)을 사용하여 인증을 사용자 지정할 수 있습니다.

자세한 내용은 [키 자격 증명 모음 인증 기본 사항](authentication.md)을 참조하세요.

## <a name="access-model-overview"></a>액세스 모델 개요

Key Vault에 대한 액세스는 두 인터페이스, 즉 **관리 평면** 및 **데이터 평면** 을 통해 제어됩니다. 관리 평면에서는 Key Vault 자체를 관리합니다. 이 평면의 작업에는 Key Vault 만들기와 삭제, Key Vault 속성 검색 및 액세스 정책 업데이트가 포함됩니다. 데이터 평면에서는 Key Vault에 저장된 데이터로 작업을 수행합니다. 키, 비밀 및 인증서를 추가, 삭제 및 수정할 수 있습니다.

두 평면은 인증을 위해 [Microsoft Azure AD(Azure Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md)를 사용합니다. 권한 부여를 위해 관리 평면에서는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 사용하고, 데이터 평면에서는 [Key Vault 액세스 정책](./assign-access-policy-portal.md)과 [Key Vault 데이터 평면 작업용 Azure RBAC](./rbac-guide.md)를 사용합니다.

두 평면에서 key vault에 액세스하려면 모든 호출자(사용자 또는 애플리케이션)에게 적절한 인증 및 권한이 있어야 합니다. 인증은 호출자의 ID를 설정합니다. 권한은 호출자가 실행할 수 있는 작업을 결정합니다. Key Vault를 사용한 인증은 [Azure AD(Azure Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md)와 함께 작동하며, 이는 지정된 **보안 주체** 의 ID를 인증하는 역할을 담당합니다.

보안 주체는 사용자, 그룹 또는 Azure 리소스에 대한 액세스를 요청하는 애플리케이션을 나타내는 개체입니다. Azure는 모든 보안 주체에 고유한 **개체 ID** 를 할당합니다.

- **사용자** 보안 주체는 Azure Active Directory에 프로필이 있는 개인을 식별합니다.
- **그룹** 보안 주체는 Azure Active Directory에서 만든 사용자 세트를 식별합니다. 그룹에 할당된 모든 역할 또는 사용 권한은 그룹 내의 모든 사용자에 부여됩니다.
- **서비스 주체** 는 애플리케이션 또는 서비스를 식별하는 보안 주체의 유형입니다. 즉, 사용자 또는 그룹이 아닌 코드 조각입니다. 서비스 주체의 개체 ID를 **클라이언트 ID** 라고 하며, 사용자 이름처럼 작동합니다. 서비스 주체의 **클라이언트 암호** 또는 **인증서** 는 암호처럼 작동합니다. 많은 Azure 서비스는 **클라이언트 ID** 와 **인증서** 의 자동 관리를 통해 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md) 할당을 지원합니다. 관리 ID는 Azure 내의 인증에서 가장 안전하고 권장되는 옵션입니다.

Key Vault 인증에 대한 자세한 내용은 [Azure Key Vault에 대한 인증](authentication.md)을 참조하세요.

## <a name="conditional-access"></a>조건부 액세스 

Key Vault Azure Azure Active Directory 조건부 액세스 정책에 대한 지원을 제공합니다. 조건부 액세스 정책을 사용하면 필요한 경우 올바른 액세스 제어를 Key Vault 적용하여 조직의 보안을 유지하고 필요하지 않은 경우 사용자의 방해를 받지 않도록 할 수 있습니다.

자세한 내용은 [조건부 액세스 개요를 참조하세요.](../../active-directory/conditional-access/overview.md)

## <a name="privileged-access"></a>권한 있는 액세스

권한은 호출자가 수행할 수 있는 작업을 결정합니다. Key Vault의 권한 부여에서는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/overview.md)와 Azure Key Vault 액세스 정책을 함께 사용합니다.

자격 증명 모음에 대한 액세스는 두 가지 인터페이스 또는 평면에서 이루어집니다. 평면에는 관리 평면과 데이터 평면이 있습니다.

- ‘관리 평면’은 Key Vault 자체를 관리하는 곳으로, 자격 증명 모음을 만들고 삭제하는 데 사용되는 인터페이스입니다. 키 자격 증명 모음 속성을 읽고 액세스 정책을 관리할 수도 있습니다.
- ‘데이터 평면’에서는 키 자격 증명 모음에 저장된 데이터로 작업할 수 있습니다. 키, 비밀 및 인증서를 추가, 삭제 및 수정할 수 있습니다.

애플리케이션은 엔드포인트를 통해 평면에 액세스합니다. 두 평면에 대한 액세스 제어는 독립적으로 작동합니다. 애플리케이션에 키 자격 증명 모음의 키를 사용하기 위한 액세스 권한을 부여하려면 Key Vault 액세스 정책 또는 Azure RBAC를 사용하여 데이터 평면 액세스 권한을 부여합니다. 사용자에게 Key Vault 속성 및 태그에 대한 액세스 권한을 부여하려고 하지만 데이터(키, 비밀 또는 인증서)에 대한 액세스 권한은 부여하지 않으려면 Azure RBAC를 사용하여 관리 평면 액세스 권한을 부여합니다.

다음 표에서는 관리 및 데이터 평면에 대한 엔드포인트를 보여 줍니다.

| 액세스&nbsp;평면 | 액세스 엔드포인트 | 작업 | 액세스&nbsp;제어 메커니즘 |
| --- | --- | --- | --- |
| 관리 평면 | **전역:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure 미국 정부:**<br> management.usgovcloudapi.net:443<br><br> **Azure 독일:**<br> management.microsoftazure.de:443 | Key Vault 만들기, 읽기, 업데이트 및 삭제<br><br>Key Vault 액세스 정책 설정<br><br>Key Vault 태그 설정 | Azure RBAC |
| 데이터 평면 | **전역:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure 미국 정부:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure 독일:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | 키: 암호화, 암호 해독, wrapKey, unwrapKey, 서명, 확인, 가져오기, 목록, 만들기, 업데이트, 가져오기, 삭제, 복구, 백업, 복원, 제거<br><br> 인증서: managecontacts, getissuers, listissuers, setissuers, deleteissuers, manageissuers, 가져오기, 목록, 만들기, 가져오기, 업데이트, 삭제, 복구, 백업, 복원, 제거<br><br>  비밀: 가져오기, 목록, 설정, 삭제, 복구, 백업, 복원, 제거 | Key Vault 액세스 정책 또는 Azure RBAC |

### <a name="managing-administrative-access-to-key-vault"></a>Key Vault에 대한 관리 액세스 관리

리소스 그룹에서 키 자격 증명 모음을 만든 경우 Azure AD를 사용하여 액세스를 관리합니다. 사용자 또는 그룹에 리소스 그룹에서 key vault를 관리하는 기능을 부여합니다. 적절한 Azure 역할을 할당하여 특정 범위 수준에서 액세스 권한을 부여할 수 있습니다. key vault를 관리하기 위해 사용자에게 액세스 권한을 부여하려면 특정 범위에 속한 사용자에게 미리 정의된 `key vault Contributor` 역할을 할당합니다. Azure 역할에 할당될 수 있는 범위 수준은 다음과 같습니다.

- **구독**: 구독 수준에서 할당된 Azure 역할은 해당 구독 내 모든 리소스 그룹 및 리소스에 적용됩니다.
- **리소스 그룹**: 리소스 그룹 수준에서 할당된 Azure 역할은 해당 리소스 그룹의 모든 리소스에 적용됩니다.
- **특정 리소스**: 특정 리소스에 할당된 Azure 역할이 해당 리소스에 적용됩니다. 이 경우 리소스는 특정 Key Vault입니다.

미리 정의된 몇 가지 역할이 있습니다. 미리 정의된 역할이 요구에 맞지 않는 경우 고유한 역할을 정의할 수 있습니다. 자세한 내용은 [Azure RBAC: 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

> [!IMPORTANT]
> 사용자에게 Key Vault 관리 평면에 대한 `Contributor` 사용 권한이 있는 경우 이 사용자는 Key Vault 액세스 정책을 설정하여 스스로 데이터 평면에 대한 액세스 권한을 부여할 수 있습니다. Key Vault에 대한 `Contributor` 역할 액세스 권한이 있는 사용자를 긴밀하게 제어해야 합니다. 권한이 있는 사람만 키 자격 증명 모음, 키, 비밀 및 인증서에 액세스하고 관리할 수 있는지 확인합니다.

### <a name="controlling-access-to-key-vault-data"></a>Key Vault 데이터에 대한 액세스 제어

Key Vault 액세스 정책은 키, 비밀 또는 인증서에 대해 개별적으로 권한을 부여합니다. 비밀이 아닌 키에 대해서만 사용자 액세스 권한을 부여할 수 있습니다. 키, 비밀 및 인증서에 대한 액세스 권한은 자격 증명 모음 수준에서 관리됩니다.

> [!IMPORTANT]
> Key Vault 액세스 정책은 특정 키, 비밀 또는 인증서와 같은 세분화된 개체 수준 권한을 지원하지 않습니다. 사용자에게 키를 만들고 삭제하는 권한이 부여되면 해당 사용자는 Key Vault의 모든 키에 대해 이러한 작업을 수행할 수 있습니다.

키 자격 증명 모음에 대한 액세스 정책은 [Azure Portal](assign-access-policy-portal.md), [Azure CLI](assign-access-policy-cli.md), [Azure PowerShell](assign-access-policy-powershell.md) 또는 [Key Vault 관리 REST API](/rest/api/keyvault/)를 사용하여 설정할 수 있습니다.

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

Key Vault 로깅은 자격 증명 모음에서 수행된 작업에 대한 정보를 저장합니다. 자세한 내용은 [Key Vault 로깅](logging.md)을 참조하세요.

키 상태, 인증서 또는 키 자격 증명 모음에 저장된 비밀이 변경되는 경우 알림을 받을 Key Vault를 Event Grid와 통합할 수 있습니다. 자세한 내용은 [Azure Event Grid를 사용하여 Key Vault 모니터링](event-grid-overview.md)을 참조하세요.

키 자격 증명 모음의 상태를 모니터링하여 서비스가 의도한 대로 작동하는지 확인하는 것도 중요합니다. 해당 작업을 수행하는 방법에 대한 자세한 내용은 [Azure Key Vault에 대한 모니터링 및 경고](alert.md)를 참조하세요.

## <a name="backup-and-recovery"></a>백업 및 복구

Azure Key Vault 일시 삭제 및 제거 방지를 사용하면 삭제된 자격 증명 모음 및 자격 증명 모음 개체를 복구할 수 있습니다. 자세한 내용은 [Azure Key Vault 일시 삭제 개요](soft-delete-overview.md)를 참조하세요.

또한 자격 증명 모음 내에서 개체의 업데이트/삭제/만들기에 대해 자격 증명 모음을 정기적으로 백업해야 합니다.  

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 보안 기준](security-baseline.md)
- [Azure Key Vault 모범 사례](security-baseline.md)
- [Azure Key Vault의 가상 네트워크 서비스 엔드포인트](overview-vnet-service-endpoints.md)
- [Azure RBAC: 기본 제공 역할](../../role-based-access-control/built-in-roles.md)