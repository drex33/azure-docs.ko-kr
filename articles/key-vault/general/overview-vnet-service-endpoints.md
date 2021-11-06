---
title: Azure Key Vault의 가상 네트워크 서비스 엔드포인트
description: 사용 시나리오를 포함하여 Azure Key Vault의 가상 네트워크 서비스 엔드포인트를 사용하면 지정된 가상 네트워크에 대한 액세스를 제한하는 방법을 알아봅니다.
services: key-vault
author: mbaldwin
ms.author: mbaldwin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 134e73dc93e46e6af0d12ef1e52facb305d868a5
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893484"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Azure Key Vault의 가상 네트워크 서비스 엔드포인트

Azure Key Vault의 가상 네트워크 서비스 엔드포인트를 사용하면 지정된 가상 네트워크에 대한 액세스를 제한할 수 있습니다. 엔드포인트를 사용하면 IPv4(인터넷 프로토콜 버전 4) 주소 범위 목록에 대한 액세스를 제한할 수도 있습니다. 해당 소스 외부에서 키 자격 증명 모음에 연결하는 모든 사용자는 액세스가 거부됩니다.

이 제한에서 한 가지 중요한 예외가 있습니다. 사용자가 신뢰할 수 있는 Microsoft 서비스를 허용하도록 옵트인한 경우 해당 서비스에서 방화벽을 통해 연결할 수 있습니다. 예를 들어 이러한 서비스에는 Office 365 Exchange Online, Office 365 SharePoint Online, Azure Compute, Azure Resource Manager 및 Azure Backup 등이 있습니다. 이러한 사용자는 유효한 Azure Active Directory 토큰을 제공해야 하며, 요청된 작업을 수행하기 위해 사용 권한(액세스 정책으로 구성된)이 있어야 합니다. 자세한 내용은 [가상 네트워크 서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)를 참조하세요.

## <a name="usage-scenarios"></a>사용 시나리오

기본적으로 모든 네트워크(인터넷 트래픽 포함)의 트래픽에 대한 액세스를 거부하도록 [Key Vault 방화벽 및 가상 네트워크](network-security.md)를 구성할 수 있습니다. 특정 Azure 가상 네트워크 및 공용 인터넷 IP 주소 범위의 트래픽에 대한 액세스 권한을 부여하여 애플리케이션에 대한 보안 네트워크 경계를 빌드하도록 할 수 있습니다.

> [!NOTE]
> Key Vault 방화벽 및 가상 네트워크 규칙은 Key Vault의 [데이터 평면](security-features.md#privileged-access)에만 적용됩니다. Key Vault 제어 평면 작업(예: 작업 만들기, 삭제 및 수정, 액세스 정책 설정, 방화벽 및 가상 네트워크 규칙 설정, ARM 템플릿을 통한 비밀 또는 키 배포)은 방화벽 및 가상 네트워크 규칙의 영향을 받지 않습니다.

서비스 엔드포인트를 사용할 수 있는 방법의 몇 가지 예는 다음과 같습니다.

* Key Vault를 사용하여 암호화 키, 애플리케이션 비밀 및 인증서를 저장하고, 공용 인터넷에서 키 자격 증명 모음에 대한 액세스를 차단하려고 합니다.
* 사용자 애플리케이션 또는 지정된 호스트의 간단한 목록만 키 자격 증명 모음에 연결할 수 있도록 키 자격 증명 모음에 대한 액세스를 잠그려고 합니다.
* Azure 가상 네트워크에서 실행되는 애플리케이션이 있으며, 이 가상 네트워크는 모든 인바운드 및 아웃바운드 트래픽에 대해 잠겨 있습니다. 애플리케이션은 비밀 또는 인증서를 가져오거나 암호화 키를 사용하려면 여전히 Key Vault에 연결해야 합니다.

## <a name="trusted-services"></a>신뢰할 수 있는 서비스

**신뢰할 수 있는 서비스 허용** 옵션을 사용하도록 설정하는 경우 키 자격 증명 모음에 액세스하도록 허용되는 신뢰할 수 있는 서비스 목록은 다음과 같습니다.

|신뢰할 수 있는 서비스|지원되는 사용 시나리오|
| --- | --- |
|Azure Virtual Machines 배포 서비스|[고객 관리 Key Vault에서 VM으로 인증서를 배포합니다](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Azure Resource Manager 템플릿 배포 서비스|[배포 중 보안 값을 전달합니다](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Azure Disk Encryption 볼륨 암호화 서비스|가상 머신을 배포하는 동안 BitLocker 키(Windows VM) 또는 DM 암호(Linux VM) 및 키 암호화 키에 대한 액세스를 허용합니다. 그러면 [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)이 설정됩니다.|
|Azure Backup|Azure Virtual Machines를 백업하는 동안 [Azure Backup](../../backup/backup-overview.md)을 사용하여 관련 키 및 비밀의 백업 및 복원을 허용합니다.|
|Exchange Online 및 SharePoint Online|[고객 키](/microsoft-365/compliance/customer-key-overview)를 사용하여 Azure Storage 서비스 암호화를 위한 고객 키에 대한 액세스를 허용합니다.|
|Azure Information Protection|[Azure Information Protection](/azure/information-protection/what-is-information-protection)을 위해 테넌트 키 액세스 허용|
|Azure App Service|App Service는 [Key Vault를 통해 Azure 웹 앱 인증서를 배포](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html)하는 경우에만 신뢰할 수 있으며, 개별 앱 자체에 대해 아웃 바운드 ip를 KEY VAULT의 IP 기반 규칙에 추가할 수 있습니다.|
|Azure SQL Database|[Azure SQL Database 및 Azure Synapse Analytics에 대한 Bring Your Own Key 지원을 통한 투명한 데이터 암호화](../../azure-sql/database/transparent-data-encryption-byok-overview.md).|
|Azure Storage|[Azure Key Vault의 고객 관리 키를 사용하여 Storage 서비스 암호화](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Azure Data Lake Store|고객 관리 키를 사용하여 [Azure Data Lake Store의 데이터 암호화](../../data-lake-store/data-lake-store-encryption.md).|
|Azure Synapse Analytics|[Azure Key Vault에서 고객 관리형 키를 사용하여 데이터 암호화](../../synapse-analytics/security/workspaces-encryption.md)|
|Azure Databricks|[빠르고 쉬우며 공동 작업이 가능한 Apache Spark 기반 분석 서비스](/azure/databricks/scenarios/what-is-azure-databricks)|
|Azure API Management|[MSI를 사용하여 Key Vault에서 사용자 지정 도메인에 대한 인증서 배포](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure 데이터 팩터리|[Data Factory에서 Key Vault의 데이터 저장소 자격 증명 페치](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[고객 관리형 키 시나리오에 대한 키 자격 증명 모음에 액세스 허용](../../event-hubs/configure-customer-managed-key.md)|
|Azure Service Bus|[고객 관리형 키 시나리오에 대한 키 자격 증명 모음에 액세스 허용](../../service-bus-messaging/configure-customer-managed-key.md)|
|Azure Import/Export| [Azure Key Vault에서 Import/Export 서비스에 고객 관리형 키 사용](../../import-export/storage-import-export-encryption-key-portal.md)
|Azure Container Registry|[고객 관리형 키를 사용한 레지스트리 암호화](../../container-registry/container-registry-customer-managed-keys.md)
|Azure Application Gateway |[HTTPS 사용 수신기에 Key Vault 인증서 사용](../../application-gateway/key-vault-certs.md)
|Azure Front Door|[HTTPS용 Key Vault 인증서 사용](../../frontdoor/front-door-custom-domain-https.md#prepare-your-azure-key-vault-account-and-certificate)

> [!NOTE]
> 해당 서비스가 Key Vault에 액세스할 수 있도록 하려면 관련 Key Vault 액세스 정책을 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

- 단계별 지침은 [Key Vault 방화벽 및 가상 네트워크 구성](network-security.md)을 참조하세요.
- [Azure Key Vault 보안 개요](security-features.md)를 참조하세요.
