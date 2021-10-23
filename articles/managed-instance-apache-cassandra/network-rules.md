---
title: Apache Cassandra용 Azure Managed Instance에 필요한 아웃바운드 네트워크 규칙
description: Apache Cassandra용 Azure Managed Instance에 필요한 아웃바운드 네트워크 규칙 및 FQDN에 대해 알아봅니다.
author: christopheranderson
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 05/21/2021
ms.author: chrande
ms.openlocfilehash: b663a926732c116aa1a057b485b91fb6a8ed4f78
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241804"
---
# <a name="required-outbound-network-rules"></a>필수 아웃바운드 네트워크 규칙

> [!IMPORTANT]
> Apache Cassandra용 Azure Managed Instance는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Apache Casandra용 Azure Managed Instance 서비스는 서비스를 제대로 관리하기 위해 특정 네트워크 규칙이 필요합니다. 적절한 규칙이 노출되도록 하면 서비스를 안전하게 유지하고 운영 문제를 방지할 수 있습니다.

## <a name="virtual-network-service-tags"></a>가상 네트워크 서비스 태그

Azure Firewall을 사용하여 아웃바운드 액세스를 제한하는 경우 [가상 네트워크 서비스 태그](../virtual-network/service-tags-overview.md)를 사용하는 것이 좋습니다. 다음은 Apache Cassandra용 Azure Managed Instance가 제대로 작동하는 데 필요한 태그입니다.

| 대상 서비스 태그                                                             | 프로토콜 | 포트    | 사용  |
|----------------------------------------------------------------------------------|----------|---------|------|
| Storage | HTTPS | 443 | 제어 평면 통신 및 구성을 위한 노드와 Azure Storage 간의 보안 통신에 필요합니다.|
| AzureKeyVault | HTTPS | 443 | 노드와 Azure Key Vault 간의 보안 통신에 필요합니다. 인증서 및 키는 클러스터 내부의 통신을 보호하는 데 사용됩니다.|
| EventHub | HTTPS | 443 | Azure에 로그를 전달하는 데 필요 |
| AzureMonitor | HTTPS | 443 | Azure에 메트릭을 전달하는 데 필요 |
| AzureActiveDirectory| HTTPS | 443 | Azure Active Directory 인증에 필요합니다.|
| AzureResourceManager| HTTPS | 443 | Cassandra 노드에 대한 정보를 수집하고 관리하는 데 필요(예: 재부팅)|
| AzureFrontDoor.Firstparty| HTTPS | 443 | 로깅 작업에 필요합니다.|
| GuestAndHybridManagement | HTTPS | 443 |  Cassandra 노드에 대한 정보를 수집하고 관리하는 데 필요(예: 재부팅) |
| ApiManagement  | HTTPS | 443 | Cassandra 노드에 대한 정보를 수집하고 관리하는 데 필요(예: 재부팅) |

> [!NOTE]
> 위 외에도 관련 서비스에 대한 서비스 태그가 없으면 다음 주소 접두사를 추가해야 합니다. 104.40.0.0/13 13.104.0.0/14 40.64.0.0/10

## <a name="user-defined-routes"></a>사용자 정의 경로

타사 방화벽을 사용하여 아웃바운드 액세스를 제한하는 경우 자체 방화벽을 통한 연결을 허용하는 대신 Microsoft 주소 접두사에 대해 [UDR(사용자 정의 경로)을](../virtual-network/virtual-networks-udr-overview.md#user-defined) 구성하는 것이 좋습니다. 사용자 정의 경로에 필요한 주소 접두사를 추가하려면 샘플 [bash 스크립트를](https://github.com/Azure-Samples/cassandra-managed-instance-tools/blob/main/configureUDR.sh) 참조하세요.

## <a name="azure-global-required-network-rules"></a>Azure 글로벌 필수 네트워크 규칙

필수 네트워크 규칙 및 IP 주소 종속성은 다음과 같습니다.

| 대상 엔드포인트                                                             | 프로토콜 | 포트    | 사용  |
|----------------------------------------------------------------------------------|----------|---------|------|
|snovap\<region\>.blob.core.windows.net:443</br> 또는</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  Azure Storage | HTTPS | 443 | 제어 평면 통신 및 구성을 위한 노드와 Azure Storage 간의 보안 통신에 필요합니다.|
|\*.store.core.windows.net:443</br> 또는</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  Azure Storage | HTTPS | 443 | 제어 평면 통신 및 구성을 위한 노드와 Azure Storage 간의 보안 통신에 필요합니다.|
|\*.blob.core.windows.net:443</br> 또는</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  Azure Storage | HTTPS | 443 | 노드와 백업을 저장하기 위해 노드와 Azure Storage 간의 보안 통신에 필요합니다. *백업 기능이 수정 중이며 스토리지 이름은 GA의 패턴을 따름*|
|vmc-p-\<region\>.vault.azure.net:443</br> 또는</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure KeyVault | HTTPS | 443 | 노드와 Azure Key Vault 간의 보안 통신에 필요합니다. 인증서 및 키는 클러스터 내부의 통신을 보호하는 데 사용됩니다.|
|management.azure.com:443</br> 또는</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure Virtual Machine Scale Sets/Azure Management API | HTTPS | 443 | Cassandra 노드에 대한 정보를 수집하고 관리하는 데 필요(예: 재부팅)|
|\*.servicebus.windows.net:443</br> 또는</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure EventHub | HTTPS | 443 | Azure에 로그를 전달하는 데 필요|
|jarvis-west.dc.ad.msft.net:443</br> 또는</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure Monitor | HTTPS | 443 | 메트릭 Azure를 전달하는 데 필요 |
|login.microsoftonline.com:443</br> 또는</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure AD | HTTPS | 443 | Azure Active Directory 인증에 필요합니다.|
| packages.microsoft.com | HTTPS | 443 | Azure 보안 스캐너 정의 및 서명 업데이트에 필요 |
| azure.microsoft.com | HTTPS | 443 | 가상 머신 확장 집합에 대 한 정보를 가져오는 데 필요 합니다. |
| \<region\>-dsms.dsms.core.windows.net | HTTPS | 443 | 로깅을 위한 인증서 |
| gcs.prod.monitoring.core.windows.net | HTTPS | 443 | 로깅에 필요한 로깅 끝점 |
| global.prod.microsoftmetrics.com | HTTPS | 443 | 메트릭에 필요 |
| shavsalinuxscanpkg.blob.core.windows.net | HTTPS | 443 | 보안 스캐너를 다운로드/업데이트 하는 데 필요 합니다. |
| crl.microsoft.com | HTTPS | 443 | 공용 Microsoft 인증서에 액세스 하는 데 필요 합니다. |
| global-dsms.dsms.core.windows.net | HTTPS | 443 | 공용 Microsoft 인증서에 액세스 하는 데 필요 합니다. |

### <a name="dns-access"></a>DNS 액세스

시스템에서 DNS 이름을 사용 하 여이 문서에서 설명 하는 Azure 서비스에 연결 하 여 부하 분산 장치를 사용할 수 있도록 합니다. 따라서 가상 네트워크에서 해당 주소를 확인할 수 있는 DNS 서버를 실행 해야 합니다. 가상 네트워크의 가상 머신은 DHCP 프로토콜을 통해 전달 되는 이름 서버를 준수 합니다. 대부분의 경우 Azure는 가상 네트워크에 대 한 DNS 서버를 자동으로 설정 합니다. 시나리오에서 발생 하지 않는 경우이 문서에서 설명 하는 DNS 이름을 시작 하는 것이 좋습니다.

## <a name="internal-port-usage"></a>내부 포트 사용

다음 포트는 VNET(또는 피어링된 vnets./express 경로) 내에서만 액세스할 수 있습니다. Apache Cassandra 인스턴스용 Managed Instance에는 공용 IP가 없으므로 인터넷에서 액세스할 수 없도록 해야 합니다.

| 포트 | 사용 |
| ---- | --- |
| 8443 | 내부 |
| 9443 | 내부 |
| 7001 | 가십 - Cassandra 노드가 서로 대화하는 데 사용 |
| 9042 | Cassandra - 클라이언트에서 Cassandra에 연결하는 데 사용 |
| 7199 | 내부 |

## <a name="next-steps"></a>다음 단계

이 문서에서는 서비스를 적절하게 관리하기 위한 네트워크 규칙에 대해 알아보았습니다. 다음 문서에서 Apache Cassandra용 Azure Managed Instance에 대해 자세히 알아보세요.

* [Apache Cassandra용 Azure Managed Instance 개요](introduction.md)
* [Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 리소스 관리](manage-resources-cli.md)
