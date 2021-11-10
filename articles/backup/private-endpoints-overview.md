---
title: 프라이빗 엔드포인트 개요
description: Azure Backup에 대한 프라이빗 엔드포인트를 사용하는 방법과 프라이빗 엔드포인트를 사용하여 리소스의 보안을 유지하는 시나리오를 이해합니다.
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: devx-track-azurepowershell
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: c9ff432c1de5ebdb44ae8f5fa0d4889026a32252
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136470"
---
# <a name="overview-and-concepts-of-private-endpoints-for-azure-backup"></a>Azure Backup에 대한 프라이빗 엔드포인트의 개요 및 개념

Azure Backup에서는 [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하여 Recovery Services 자격 증명 모음에서 데이터를 안전하게 백업하고 복원할 수 있습니다. 프라이빗 엔드포인트는 Azure VNet(가상 네트워크)에서 하나 이상의 개인 IP 주소를 사용하여 서비스를 VNet에 효과적으로 제공합니다.

이 문서는 Azure Backup에 대한 프라이빗 엔드포인트가 작동하는 방법과 프라이빗 엔드포인트를 사용하여 리소스의 보안을 유지하는 시나리오를 이해하는 데 도움이 됩니다.

## <a name="before-you-start"></a>시작하기 전에

- 프라이빗 엔드포인트는 자격 증명 모음에 등록된 항목이 없는 새 Recovery Services 자격 증명 모음에 대해서만 만들 수 있습니다. 따라서 자격 증명 모음에 대한 항목을 보호하려면 프라이빗 엔드포인트를 만들어야 합니다.
- 한 개의 가상 네트워크에는 여러 Recovery Services 자격 증명 모음에 대한 프라이빗 엔드포인트가 포함될 수 있습니다. 또한 하나의 Recovery Services 자격 증명 모음에는 여러 가상 네트워크에 이 자격 증명 모음에 대한 프라이빗 엔드포인트가 포함될 수 있습니다. 그러나 자격 증명 모음에 대해 만들 수 있는 최대 프라이빗 엔드포인트 수는 12입니다.
- 자격 증명 모음에 대한 프라이빗 엔드포인트를 만들면 자격 증명 모음이 잠깁니다. 자격 증명 모음에 대한 프라이빗 엔드포인트가 포함된 네트워크 이외의 네트워크에서 액세스할 수 없습니다(백업 및 복원의 경우). 자격 증명 모음에 대한 모든 프라이빗 엔드포인트를 제거하는 경우 모든 네트워크에서 자격 증명 모음에 액세스할 수 있습니다.
- 백업을 위한 프라이빗 엔드포인트 연결에는 Azure Backup에서 스토리지에 사용하는 IP를 포함하여 서브넷에 총 11개의 개인 IP가 사용됩니다. 이 숫자는 특정 Azure 지역의 경우 더 클 수 있습니다(최대 25개). 따라서 백업용 프라이빗 엔드포인트를 만들려는 경우 사용할 수 있는 충분한 개인 IP가 있는 것이 좋습니다.
- Recovery Services 자격 증명 모음은 Azure Backup 및 Azure Site Recovery에서 모두 사용하지만 이 문서에서는 Azure Backup 전용 프라이빗 엔드포인트를 사용하는 방법을 설명합니다.
- 백업용 프라이빗 엔드포인트에는 Azure AD(Azure Active Directory)에 대한 액세스 권한이 포함되어 있지 않으며 동일한 권한이 별도로 보장되어야 합니다. 따라서 Azure AD가 지역에서 작동하는 데 필요한 IP 및 FQDN은 Azure VM에서 데이터베이스의 백업을 수행하고 MARS 에이전트를 사용하여 백업할 때 보안 네트워크에서의 아웃바운드 액세스가 허용되어야 합니다. 해당하는 경우 Azure AD 액세스를 허용하기 위해 NSG 태그 및 Azure Firewall 태그를 사용할 수도 있습니다.
- 프라이빗 엔드포인트에서는 네트워크 정책을 사용하는 가상 네트워크가 지원되지 않습니다. 계속하려면 [네트워크 정책을 사용하지 않도록 설정](../private-link/disable-private-endpoint-network-policy.md)해야 합니다.
- 2020년 5월 1일 이전에 등록한 경우 구독에 Recovery Services 리소스 공급 기업을 다시 등록해야 합니다. 공급 기업을 다시 등록하려면 Azure Portal에서 구독으로 이동하고, 왼쪽 탐색 모음에서 **리소스 공급자** 로 이동한 다음 **Microsoft.RecoveryServices** 를 선택하고, **다시 등록** 을 선택합니다.
- 자격 증명 모음에 프라이빗 엔드포인트를 사용하도록 설정된 경우 SQL 및 SAP HANA 데이터베이스 백업에 대한 [지역 간 복원](backup-create-rs-vault.md#set-cross-region-restore)이 지원되지 않습니다.
- 프라이빗 엔드포인트를 사용하여 이미 Recovery Services 자격 증명 모음을 새 테넌트로 이동하는 경우 자격 증명 모음의 관리 ID를 다시 만들고 구성하고 필요에 따라 새 프라이빗 엔드포인트를 만들기 위해 Recovery Services 자격 증명 모음을 업데이트해야 합니다. 이 작업을 수행하지 않으면 백업 및 복원 작업이 실패하기 시작합니다. 또한 구독 내에서 설정 하는 모든 Azure 역할 기반 액세스 제어 (Azure RBAC) 권한을 다시 구성 해야 합니다.

## <a name="recommended-and-supported-scenarios"></a>권장 및 지원되는 시나리오

자격 증명 모음에 프라이빗 엔드포인트를 사용하도록 설정하면 Azure VM 및 MARS 에이전트 백업에서만 SQL 및 SAP HANA 워크로드의 백업 및 복원에 사용됩니다. 다른 워크로드의 백업에도 자격 증명 모음을 사용할 수 있습니다. 그러나 프라이빗 엔드포인트는 필요하지 않습니다. 프라이빗 엔드포인트는 SQL 및 SAP HANA 워크로드 백업 및 MARS 에이전트를 사용한 백업 외에도 Azure VM 백업을 위한 파일 복구를 수행하는 데 사용됩니다. 자세한 내용은 다음 표를 참조하세요.

| 시나리오 | 권장 사항 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure VM에서 워크로드 백업(SQL, SAP HANA), MARS 에이전트를 사용하여 백업 | 가상 네트워크에서 Azure Backup 또는 Azure Storage에 대해 허용 목록에 IP/FQDN을 추가하지 않고도 백업 및 복원을 허용하려면 프라이빗 엔드포인트를 사용하는 것이 좋습니다. 해당 시나리오에서는 SQL Database를 호스트하는 VMs에서 Azure AD IP 또는 FQDN에 연결할 수 있는지 확인합니다. |
| **Azure VM 백업**                                         | VM 백업을 위해 IP 또는 FQDN에 액세스하지 않아도 됩니다. 따라서 디스크 백업 및 복원용 프라이빗 엔드포인트가 필요하지 않습니다.  <br><br>   그러나 프라이빗 엔드포인트가 포함된 자격 증명 모음에서 파일 복구는 자격 증명 모음에 대한 프라이빗 엔드포인트가 포함된 가상 네트워크로 제한됩니다. <br><br>    ACL로 지정된 비관리 디스크를 사용하는 경우 디스크가 포함된 스토리지 계정이 **신뢰할 수 있는 Microsoft 서비스** 에 대한 액세스를 허용하는지 확인합니다(ACL로 지정된 경우). |
| **Azure Files 백업**                                      | Azure Files 백업은 로컬 스토리지 계정에 저장됩니다. 따라서 백업 및 복원을 위한 프라이빗 엔드포인트가 필요하지 않습니다. |

>[!Note]
>프라이빗 엔드포인트는 DPM 및 MABS 서버에서 지원되지 않습니다. 

## <a name="difference-in-network-connections-due-to-private-endpoints"></a>프라이빗 엔드포인트로 인한 네트워크 연결 차이

위에서 언급했듯이 프라이빗 엔드포인트는 Azure VM 및 MARS 에이전트 백업에서 워크로드(SQL, SAP HANA) 백업에 특히 유용합니다. 프라이빗 엔드포인트가 있거나 없는 모든 시나리오에서 워크로드 확장(Azure VM 내에서 실행되는 SQL 및 SAP HANA 인스턴스의 백업용)과 MARS 에이전트는 모두 AAD([Microsoft 365 Common 및 Office Online](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) 섹션 56 및 59에 언급된 FQDN)에 대한 연결 호출을 수행합니다.

워크로드 확장 또는 MARS 에이전트가 Recovery Services 자격 증명 모음을 위해 _프라이빗 엔드포인트 없이_ 설치된 경우 이러한 연결 외에도 다음 도메인에 대한 연결도 필요합니다.

| 서비스 | 도메인 이름 |
| ------- | ------------ |
| Azure Backup  | *.backup.windowsazure.com |
| Azure Storage | *.blob.core.windows.net <br> *.queue.core.windows.net <br> *.blob.storage.azure.net |

프라이빗 엔드포인트가 있는 Recovery Services 자격 증명 모음에 대해 워크로드 확장 또는 MARS 에이전트가 설치된 경우 다음 엔드포인트에 도달합니다.

| 서비스 | 도메인 이름 |
| ------- | ------------ |
| Azure Backup  | `*.privatelink.<geo>.backup.windowsazure.com` |
| Azure Storage | *.blob.core.windows.net <br> *.queue.core.windows.net <br> *.blob.storage.azure.net |

>[!Note]
>위의 텍스트에서는 `<geo>`는 지역 코드(예: 미국 동부의 경우 **eus**, 유럽 북부의 경우 **ne**)를 나타냅니다. 지역 코드에 대해서는 다음 목록을 참조하세요.
>- [모든 퍼블릭 클라우드](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
>- [중국](/azure/china/resources-developer-guide#check-endpoints-in-azure)
>- [독일](../germany/germany-developer-guide.md#endpoint-mapping)
>- [US Gov](../azure-government/documentation-government-developer-guide.md)

두 시나리오에서 도달한 스토리지 FQDN은 동일합니다. 그러나 프라이빗 엔드포인트 설정이 있는 Recovery Services 자격 증명 모음의 경우 이러한 이름 확인은 개인 IP 주소를 반환해야 합니다. 이는 프라이빗 DNS 영역을 사용하거나, 호스트 파일의 스토리지 계정에 대한 DNS 항목을 만들거나, 해당 DNS 항목과 함께 사용자 지정 DNS에 대한 조건부 전달자를 사용하여 달성할 수 있습니다. 스토리지 계정에 대한 개인 IP 매핑은 포털의 스토리지 계정에 대한 프라이빗 엔드포인트 블레이드에 나열됩니다.

>Blob 및 큐의 프라이빗 엔드포인트는 표준 이름 지정 패턴을 따르며  **\<the name of the private endpoint>_ecs** 또는 **\<the name of the private endpoint>_prot** 로 시작하고  **\_blob** 및  **\_queue** 가 접미사로 붙습니다.

Azure Backup 서비스의 엔드포인트는 프라이빗 엔드포인트 사용 자격 증명 모음에 대해 수정됩니다.  
타사 프록시 서버 및 방화벽을 사용 하 여 DNS 프록시 서버를 구성한 경우 위의 도메인 이름을 허용 하 고 개인 IP 주소 매핑을 사용 하 여 사용자 지정 DNS로 리디렉션하거나,이 개인 IP 주소 매핑을 사용 하 여 개인 DNS 영역에 대 한 가상 네트워크 링크를 사용 하 여 168.63.129.16 해야 합니다.

다음 예제에서는 Recovery Services 자격 증명 모음, blob, 큐 및 AAD에 대 한 도메인 이름 쿼리를 168.63.129.16로 리디렉션하는 DNS 프록시로 사용 되는 Azure 방화벽을 보여 줍니다.

:::image type="content" source="./media/private-endpoints-overview/azure-firewall-used-as-dns-proxy-inline.png" alt-text="Azure Firewall을 DNS 프록시로 사용하여 도메인 이름 쿼리를 리디렉션하는 방법을 보여 주는 다이어그램." lightbox="./media/private-endpoints-overview/azure-firewall-used-as-dns-proxy-expanded.png":::

자세한 내용은 [프라이빗 엔드포인트 만들기 및 사용](private-endpoints.md)을 참조하세요.

## <a name="network-connectivity-setup-for-vault-with-private-endpoints"></a>프라이빗 엔드포인트가 있는 자격 증명 모음에 대한 네트워크 연결 설정

복구 서비스의 프라이빗 엔드포인트는 개인 IP가 있는 NIC(네트워크 인터페이스)와 연결됩니다. 프라이빗 엔드포인트 연결이 작동하려면(Azure 백본을 통해 모든 트래픽을 서비스로 라우팅하고 VNet 내의 클라이언트에 대한 서비스 액세스 제한), 서비스에 대한 모든 통신 트래픽이 해당 네트워크 인터페이스로 리디렉션되어야 합니다. 이는 확장/에이전트가 실행 중인 컴퓨터의 VNet 또는 호스트 파일 항목에 연결된 DNS를 사용하여 수행할 수 있습니다.

워크로드 백업 확장 및 MARS 에이전트는 VNet의 Azure VM 또는 VNet과 피어링된 온-프레미스 VM에서 실행됩니다. 이 VNet에 조인된 프라이빗 엔드포인트가 있는 Recovery Services 자격 증명 모음에 등록하면 확장 및 에이전트에 대한 Azure Backup 클라우드 서비스의 서비스 URL이 `<azure_backup_svc >.<geo>.backup.windowsazure.com`에서 `<vault_id>.<azure_backup_svc>.privatelink.<geo>.backup`.windowsazure.com**으로 변경됩니다.

>[!Note]
>위의 텍스트에서는 `<geo>`는 지역 코드(예: 미국 동부의 경우 **eus**, 유럽 북부의 경우 **ne**)를 나타냅니다. 지역 코드에 대해서는 다음 목록을 참조하세요.
>- [모든 퍼블릭 클라우드](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
>- [중국](/azure/china/resources-developer-guide#check-endpoints-in-azure)
>- [독일](../germany/germany-developer-guide.md#endpoint-mapping)
>- [US Gov](../azure-government/documentation-government-developer-guide.md)

수정된 URL은 자격 증명 모음에만 해당됩니다.  URL 이름에서 `<vault_id>`를 참조하세요. 이 자격 증명 모음에 등록된 확장 및 에이전트만 이러한 엔드포인트를 통해 Azure Backup과 통신할 수 있습니다. 이렇게 하면 이 VNet 내의 클라이언트에 대한 액세스가 제한됩니다. NIC에서 해당하는 개인 IP를 확인해야 하는 확장/에이전트는 `*.privatelink.<geo>.backup.windowsazure.com`을 통해 통신합니다.

Recovery Services 자격 증명 모음의 프라이빗 엔드포인트가 **프라이빗 DNS 영역과 통합** 옵션을 사용하여 Azure Portal을 통해 만들어지면 Azure Backup 서비스(`*.privatelink.<geo>backup.windowsazure.com`)의 개인 IP 주소에 필요한 DNS 항목은 리소스가 할당될 때마다 자동으로 만들어집니다. 그렇지 않으면 사용자 지정 DNS 또는 호스트 파일에서 이러한 FQDN에 대한 DNS 항목을 수동으로 만들어야 합니다.

통신 채널(BLOB/큐)에 대한 VM 검색 후 DNS 레코드의 수동 관리는 [첫 번째 등록 후 Blob 및 큐에 대한 DNS 레코드(사용자 지정 DNS 서버/호스트 파일에만 해당)](./private-endpoints.md#dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration)를 참조하세요. 백업 스토리지 계정 Blob에 대한 첫 번째 백업 후 DNS 레코드를 수동으로 관리하려면 [첫 번째 등록 후 Blob 및 큐에 대한 DNS 레코드(사용자 지정 DNS 서버/호스트 파일에만 해당)](./private-endpoints.md#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup)를 참조하세요.

>FQDN의 개인 IP 주소는 Recovery Services 자격 증명 모음에 대해 만든 프라이빗 엔드포인트의 프라이빗 엔드포인트 블레이드에서 찾을 수 있습니다.

다음 다이어그램에서는 프라이빗 DNS 영역을 사용하여 이러한 수정된 서비스 FQDN을 확인하는 방식을 보여 줍니다. 

:::image type="content" source="./media/private-endpoints-overview/use-private-dns-zone-to-resolve-modified-service-fqdns-inline.png" alt-text="프라이빗 DNS 영역을 사용하여 수정된 서비스 FQDN을 확인하는 방식을 보여 주는 다이어그램." lightbox="./media/private-endpoints-overview/use-private-dns-zone-to-resolve-modified-service-fqdns-expanded.png":::

Azure Backup 클라우드 서비스에 대한 연결 외에도 워크로드 확장 및 에이전트에는 Azure Storage 계정 및 Azure Active Directory에 대한 연결이 필요합니다. Azure VM에서 실행되는 워크로드 확장에는 최소 두 개의 스토리지 계정에 대한 연결이 필요합니다. 첫 번째 계정은 큐 메시지를 통해 통신 채널로 사용되고 두 번째 계정은 백업 데이터를 저장하는 데 사용됩니다. MARS 에이전트는 백업 데이터를 저장하는 데 사용되는 하나의 스토리지 계정에 대한 액세스 권한이 필요합니다.

프라이빗 엔드포인트 사용 자격 증명 모음의 경우 Azure Backup은 Azure 백본 네트워크를 통해 통신 채널 및 백업 데이터에 대한 트래픽을 라우팅하는 이러한 스토리지 계정에 대한 프라이빗 엔드포인트를 만듭니다. 이렇게 하면 Azure Backup과 관련된 모든 네트워크 트래픽이 가상 네트워크에서 벗어나는 것을 방지할 수 있습니다.

필수 조건으로 Recovery Services 자격 증명 모음에는 동일한 리소스 그룹에서 추가 프라이빗 엔드포인트를 만들기 위한 권한이 필요합니다. 또한 Recovery Services 자격 증명 모음에 프라이빗 DNS 영역(privatelink.blob.core.windows.net, privatelink.queue.core.windows.net)에서 DNS 항목을 만들 수 있는 권한을 제공하는 것이 좋습니다. Recovery Services 자격 증명 모음은 VNet 및 프라이빗 엔드포인트가 만들어지는 리소스 그룹에서 프라이빗 DNS 영역을 검색합니다. 이러한 영역에 DNS 항목을 추가할 수 있는 권한이 있는 경우 자격 증명 모음에서 항목을 만들고, 그렇지 않으면 사용자 지정 DNS 또는 VNet에 연결된 프라이빗 DNS 영역에서 사용자가 수동으로 항목을 만들어야 합니다.

>개인 IP 매핑은 포털의 Blob 및 큐에 대한 프라이빗 엔드포인트 블레이드에서 사용할 수 있습니다.

다음 다이어그램에서는 프라이빗 DNS 영역을 사용하는 스토리지 계정에 대해 이름 확인이 작동하는 방식을 보여 줍니다.

:::image type="content" source="./media/private-endpoints-overview/name-resolution-works-for-storage-accounts-using-private-dns-zone-inline.png" alt-text="프라이빗 DNS 영역을 사용하는 스토리지 계정에 대해 이름 확인이 작동하는 방식을 보여 주는 다이어그램." lightbox="./media/private-endpoints-overview/name-resolution-works-for-storage-accounts-using-private-dns-zone-expanded.png":::

## <a name="next-steps"></a>다음 단계

- [프라이빗 엔드포인트를 만들고 사용합니다.](private-endpoints.md)