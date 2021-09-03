---
title: Azure 보안 리소스, Azure 독일을 글로벌 Azure로 마이그레이션
description: 이 문서에서는 Azure 독일에서 글로벌 Azure로 Azure 보안 리소스를 마이그레이션하는 방법에 대한 정보를 제공합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: db8c13e214145df91f4b385f95c54e8084f3206c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2021
ms.locfileid: "122566185"
---
# <a name="migrate-security-resources-to-global-azure"></a>보안 리소스를 글로벌 Azure로 마이그레이션

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

이 문서에는 Azure 독일에서 글로벌 Azure로 Azure 보안 리소스를 마이그레이션하는 데 도움이 되는 정보가 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory 마이그레이션에 대한 자세한 내용은 [ID 마이그레이션](./germany-migration-identity.md#azure-active-directory)을 참조하세요.

## <a name="key-vault"></a>Key Vault

Azure Key Vault의 일부 기능은 Azure 독일에서 글로벌 Azure로 마이그레이션할 수 없습니다.

### <a name="encryption-keys"></a>암호화 키

암호화 키는 마이그레이션할 수 없습니다. 대상 지역에서 새 키를 만든 다음, 키를 사용하여 대상 리소스(예: Azure Storage 또는 Azure SQL Database)를 보호합니다. 이전 지역에서 새 지역으로 데이터를 안전하게 마이그레이션합니다.

### <a name="application-secrets"></a>애플리케이션 비밀

애플리케이션 비밀은 인증서, 스토리지 계정 키 및 기타 애플리케이션 관련 비밀입니다. 마이그레이션하는 동안 먼저 글로벌 Azure에서 새 키 자격 증명 모음을 만듭니다. 그런 후 다음 작업 중 하나를 완료합니다.

- 새 애플리케이션 비밀을 만듭니다.
- Azure 독일에서 현재 비밀을 읽은 다음 새 자격 증명 모음에 값을 입력합니다.

```powershell
Get-AzKeyVaultSecret -vaultname mysecrets -name Deploydefaultpw
```

추가 정보는 다음 항목을 참조하세요.

- [Key Vault 자습서](../key-vault/index.yml)를 완료하여 지식을 새로 고칩니다.
- [Key Vault 개요](../key-vault/general/overview.md)를 검토하세요.
- [Key Vault PowerShell cmdlet](/powershell/module/az.keyvault/)을 검토하세요.

## <a name="vpn-gateway"></a>VPN Gateway

Azure 독일에서 글로벌 Azure로 Azure VPN Gateway 인스턴스를 마이그레이션하는 것은 현재 지원되지 않습니다. 글로벌 Azure에서 VPN Gateway의 새 인스턴스를 만들고 구성하는 것이 좋습니다.

포털 또는 PowerShell을 사용하여 현재 VPN Gateway 구성에 대한 정보를 수집할 수 있습니다. PowerShell에서 `Get-AzVirtualNetworkGateway*`로 시작하는 cmdlet 집합을 사용합니다.

온-프레미스 구성을 업데이트해야 합니다. 또한 Azure 네트워크 환경을 업데이트한 후 이전 IP 주소 범위에 대한 기존 규칙을 삭제합니다.

추가 정보는 다음 항목을 참조하세요.

- [VPN Gateway 자습서](../vpn-gateway/index.yml)를 완료하여 지식을 새로 고칩니다.
- [사이트 간 연결 만들기](../vpn-gateway/tutorial-site-to-site-portal.md) 방법에 대해 알아봅니다.
- [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) PowerShell cmdlet을 검토합니다.
- 블로그 게시물 [사이트 간 연결 만들기](/archive/blogs/ralfwi/connecting-clouds)를 읽습니다.
  
## <a name="application-gateway"></a>Application Gateway

Azure 독일에서 글로벌 Azure로의 Azure Application Gateway 인스턴스 마이그레이션은 현재 지원되지 않습니다. 글로벌 Azure에서 새 게이트웨이를 만들고 구성하는 것이 좋습니다.

포털 또는 PowerShell을 사용하여 현재 게이트웨이 구성에 대한 정보를 수집할 수 있습니다. PowerShell에서 `Get-AzApplicationGateway*`로 시작하는 cmdlet 집합을 사용합니다.

추가 정보는 다음 항목을 참조하세요.

- [Application Gateway 자습서](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)를 완료하여 지식을 새로 고칩니다.
- [애플리케이션 게이트웨이 만드는 방법](../application-gateway/quick-create-portal.md)을 알아봅니다.
- [Get-AzApplicationGateway](/powershell/module/az.network/get-azapplicationgateway) PowerShell cmdlet을 검토합니다.

## <a name="next-steps"></a>다음 단계

다음 서비스 범주에서 리소스 마이그레이션을 위한 도구, 기술 및 권장 사항에 대해 알아봅니다.

- [컴퓨팅](./germany-migration-compute.md)
- [네트워킹](./germany-migration-networking.md)
- [스토리지](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [데이터베이스](./germany-migration-databases.md)
- [분석](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [통합](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [관리 도구](./germany-migration-management-tools.md)
- [미디어](./germany-migration-media.md)