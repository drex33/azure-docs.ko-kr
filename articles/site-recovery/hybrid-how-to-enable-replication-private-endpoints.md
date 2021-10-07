---
title: 프라이빗 엔드포인트를 사용하여 온-프레미스 머신에 대해 복제 사용
description: 이 문서에서는 Site Recovery에서 프라이빗 엔드포인트를 사용하여 온-프레미스 머신에 대해 복제를 구성하는 방법을 설명합니다.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 939eb89de2e347aeec2d8946f456f5ba2aa6be29
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658579"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>프라이빗 엔드포인트를 사용하여 온-프레미스 머신 복제

Azure Site Recovery를 사용하면 [Azure Private Link](../private-link/private-endpoint-overview.md) 프라이빗 엔드포인트를 사용하여 온-프레미스 머신을 Azure의 가상 네트워크에 복제할 수 있습니다. 복구 자격 증명 모음에 대한 프라이빗 엔드포인트 액세스는 모든 Azure 상용 및 정부 지역에서 지원됩니다.

이 항목에서는 다음 단계를 수행하는 방법에 대해 설명합니다.

- 머신을 보호하기 위해 Azure Backup Recovery Services 자격 증명 모음을 만듭니다.
- 자격 증명 모음의 관리 ID를 사용하도록 설정합니다. 온-프레미스에서 Azure 대상 위치로의 트래픽을 복제할 수 있도록 스토리지 계정에 액세스하는 데 필요한 권한을 부여합니다. 스토리지에 대한 관리 ID 액세스 권한은 자격 증명 모음에 대한 프라이빗 링크 액세스를 위해 필요합니다.

- 프라이빗 엔드포인트에 필요한 DNS 변경을 수행합니다.
- 가상 네트워크 내에서 자격 증명 모음에 대한 프라이빗 엔드포인트를 만들고 승인합니다.
- 스토리지 계정에 대한 프라이빗 엔드포인트를 만듭니다. 필요에 따라 스토리지에 대한 퍼블릭 또는 방화벽 적용 액세스를 계속 허용할 수 있습니다. Azure Site Recovery에서는 스토리지에 액세스하기 위한 프라이빗 엔드포인트를 만들 필요가 없습니다.
  
다음 다이어그램에서는 프라이빗 엔드포인트를 사용한 하이브리드 재해 복구에 대한 복제 워크플로를 보여 줍니다. 온-프레미스 네트워크에는 프라이빗 엔드포인트를 만들 수 없습니다. 프라이빗 링크를 사용하려면 Azure 가상 네트워크(이 문서에서는 *바이패스 네트워크* 라고 함)를 만들고, 온-프레미스 및 바이패스 네트워크 간에 프라이빗 연결을 설정한 다음, 바이패스 네트워크에서 프라이빗 엔드포인트를 만들어야 합니다. 모든 형태의 프라이빗 연결을 선택할 수 있습니다.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Azure Site Recovery 및 프라이빗 엔드포인트의 아키텍처를 보여 주는 다이어그램입니다.":::

## <a name="prerequisites-and-caveats"></a>필수 구성 요소 및 주의 사항

- 프라이빗 링크는 Site Recovery 9.35 이상에서 지원됩니다.
- 프라이빗 엔드포인트는 해당 Recovery Services 자격 증명 모음에 등록된 항목이 없는 새 자격 증명 모음에 대해서만 만들 수 있습니다. 따라서 자격 증명 모음에 항목을 추가하기 전에 프라이빗 엔드포인트를 만들어야 합니다. 가격 책정 정보는 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link/)을 참조하세요.
- 자격 증명 모음에 대한 프라이빗 엔드포인트를 만들 때 자격 증명 모음이 잠깁니다. 이러한 자격 증명 모음에는 프라이빗 엔드포인트가 있는 네트워크에서만 액세스할 수 있습니다.
- Azure Active Directory는 현재 프라이빗 엔드포인트를 지원하지 않습니다. 따라서 보안 Azure 가상 네트워크로부터 지역에서 Azure Active Directory가 작동하는 데 필요한 IP 및 정규화된 도메인 이름에 대한 아웃바운드 액세스를 허용해야 합니다.
  해당하는 경우 네트워크 보안 그룹 태그 "Azure Active Directory" 및 Azure Firewall 태그를 사용하여 Azure Active Directory에 대한 액세스를 허용할 수도 있습니다.
- 프라이빗 엔드포인트를 만드는 바이패스 네트워크에는 5개의 IP 주소가 필요합니다. 자격 증명 모음에 대한 프라이빗 엔드포인트를 만들 때 Site Recovery는 마이크로 서비스에 액세스하기 위해 5개의 프라이빗 링크를 만듭니다.
- 캐시 스토리지 계정에 대한 프라이빗 엔드포인트 연결을 위해 바이패스 네트워크에서 하나의 추가 IP 주소가 필요합니다. 온-프레미스와 스토리지 계정 엔드포인트 간에 어떤 연결 방법도 사용할 수 있습니다. 예를 들어, 인터넷 또는 Azure [ExpressRoute](../expressroute/index.yml)를 사용할 수 있습니다. 프라이빗 링크 설정은 선택 사항입니다. 범용 v2 계정에서만 스토리지에 대한 프라이빗 엔드포인트를 만들 수 있습니다. 범용 v2 계정의 데이터 전송 가격 책정에 대한 자세한 내용은 [Azure 페이지 Blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/page-blobs/)을 참조하세요.

> [!NOTE]
> VMware 및 물리적 컴퓨터를 보호 하기 위해 개인 끝점을 설정 하는 동안 구성 서버에 MySQL을 수동으로 설치 해야 합니다. 수동 설치를 수행 하려면 [여기](vmware-azure-deploy-configuration-server.md#configure-settings) 의 단계를 따르세요. 

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>사이트 복구를 위한 프라이빗 엔드포인트 만들기 및 사용

 다음 섹션에서는 가상 네트워크에서 사이트 복구를 위한 프라이빗 엔드포인트를 만들고 사용하기 위해 수행해야 하는 단계에 대해 설명합니다.

> [!NOTE]
> 이러한 단계를 표시된 순서대로 따르는 것이 좋습니다. 그러지 않으면 자격 증명 모음에서 프라이빗 엔드포인트를 사용하지 못할 수 있으며 새 자격 증명 모음을 사용하여 프로세스를 다시 시작해야 할 수 있습니다.

### <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음에는 머신의 복제 정보가 포함됩니다. Site Recovery 작업을 트리거하는 데 사용됩니다. 재해가 발생한 경우 장애 조치(failover)하려는 Azure 지역에서 Recovery Services 자격 증명 모음을 만드는 방법에 대한 내용은 [Recovery Services 자격 증명 모음 만들기](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)를 참조하세요.

### <a name="enable-the-managed-identity-for-the-vault"></a>자격 증명 모음에 관리 ID 사용

[관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하면 자격 증명 모음에서 스토리지 계정에 액세스할 수 있습니다. Site Recovery는 사용자의 요구 사항에 따라 대상 스토리지 및 캐시/로그 스토리지 계정에 액세스해야 할 수 있습니다. 관리 ID 액세스는 자격 증명 모음에 프라이빗 링크 서비스를 사용하는 경우에 필요합니다.

1. Recovery Services 자격 증명 모음으로 이동합니다. **설정** 아래에서 **ID** 를 선택합니다.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="ID 설정 페이지를 보여 주는 스크린샷":::

1. **상태** 를 **켜기** 로 변경하고 **저장** 을 선택합니다.

   개체 ID가 생성됩니다. 이제 자격 증명 모음이 Azure Active Directory에 등록됩니다.

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 대한 프라이빗 엔드포인트 만들기

온-프레미스 원본 네트워크의 머신을 보호하려면 바이패스 네트워크에서 자격 증명 모음에 대한 하나의 프라이빗 엔드포인트가 필요합니다. Azure Portal에서 Private Link 센터를 사용하거나 [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)을 사용하여 프라이빗 엔드포인트를 만듭니다.

1. Azure Portal 검색 상자에서 "프라이빗 링크"를 검색합니다. **프라이빗 링크** 를 선택하여 Private Link 센터로 이동합니다.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Azure Portal에서 Private Link 센터를 검색하는 방법을 보여 주는 스크린샷":::

1. 왼쪽 창에서 **프라이빗 엔드포인트** 를 선택합니다. **프라이빗 엔드포인트** 페이지에서 **추가** 를 선택하여 자격 증명 모음에 대한 프라이빗 엔드포인트 만들기를 시작합니다.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Private Link 센터에서 프라이빗 엔드포인트를 만드는 방법을 보여 주는 스크린샷":::

1. **프라이빗 엔드포인트 만들기** 페이지에서 세부 정보를 지정하여 프라이빗 엔드포인트 연결을 만듭니다.

   1. **기본 사항**. 프라이빗 엔드포인트에 대한 기본 세부 정보를 제공합니다. 바이패스 네트워크에 사용했던 지역을 사용합니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="프라이빗 엔드포인트를 만들기 위한 기본 탭을 보여 주는 스크린샷":::

   1. **리소스**. 이 탭에서 연결을 만들 PaaS(Platform as a Service) 리소스를 지정해야 합니다. 선택한 구독에 대한 **리소스 종류** 에서 **Microsoft.RecoveryServices/vaults** 를 선택합니다. **리소스** 에서 Recovery Services 자격 증명 모음의 이름을 선택합니다. **대상 하위 리소스** 로 **Azure Site Recovery** 를 선택합니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="프라이빗 엔드포인트에 연결하기 위한 리소스 탭을 보여 주는 스크린샷":::

   1. **구성**. 이 탭에서 프라이빗 엔드포인트를 만들려는 바이패스 네트워크 및 서브넷을 지정합니다. 

      **예** 를 선택하여 프라이빗 DNS 영역과의 통합을 사용하도록 설정합니다.
      기존 DNS 영역을 선택하거나 새로 만듭니다. **예** 를 선택하면 자동으로 영역을 바이패스 네트워크에 연결합니다. 이 작업은 또한 프라이빗 엔드포인트에 대해 만든 새 IP 및 정규화된 도메인 이름의 DNS 확인에 필요한 DNS 레코드도 추가합니다.

      동일한 자격 증명 모음에 연결하는 모든 새 프라이빗 엔드포인트에 대한 새 DNS 영역을 만들도록 선택해야 합니다. 기존 프라이빗 DNS 영역을 선택하는 경우 이전 CNAME 레코드를 덮어씁니다. 계속하기 전에 [프라이빗 엔드포인트 지침](../private-link/private-endpoint-overview.md#private-endpoint-properties)을 참조하세요.

      환경에 허브 및 스포크 모델이 있는 경우 전체 설정을 위한 하나의 프라이빗 엔드포인트 및 하나의 프라이빗 DNS 영역만 있으면 됩니다. 이미 모든 가상 네트워크 간에 피어링이 사용하도록 설정되어 있기 때문입니다. 자세한 내용은 [프라이빗 엔드포인트 DNS 통합](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)을 참조하세요.

      프라이빗 DNS 영역을 수동으로 만들려면 [수동으로 프라이빗 DNS 영역 만들기 및 DNS 레코드 추가](#create-private-dns-zones-and-add-dns-records-manually)의 단계를 따르세요.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="프라이빗 엔드포인트의 구성에 대한 구성 탭을 보여 주는 스크린샷":::

   1. **태그**. 필요에 따라 프라이빗 엔드포인트에 대한 태그를 추가할 수 있습니다.

   1. **검토 \+ 만들기** 유효성 검사가 완료되면 **만들기** 를 선택하여 프라이빗 엔드포인트를 만듭니다.

프라이빗 엔드포인트를 만들 때 5개의 FQDN(정규화된 도메인 이름)이 프라이빗 엔드포인트에 추가됩니다. 이러한 링크를 통해 온-프레미스 네트워크의 머신은 바이패스 네트워크를 통해 자격 증명 모음 컨텍스트에서 필요한 모든 Site Recovery 마이크로 서비스에 액세스할 수 있습니다. 바이패스 네트워크 및 모든 피어링 네트워크에서 모든 Azure 머신의 보호를 위해 동일한 프라이빗 엔드포인트를 사용할 수 있습니다.

5개의 도메인 이름은 다음 패턴으로 형식이 지정됩니다.

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>사이트 복구를 위한 프라이빗 엔드포인트 승인

프라이빗 엔드포인트를 만들려고 하며 Recovery Services 자격 증명 모음의 소유자이기도 한 경우 이전에 만든 프라이빗 엔드포인트가 몇 분 내에 자동으로 승인됩니다. 그렇지 않으면 자격 증명 모음 소유자가 프라이빗 엔드포인트를 승인해야 해당 엔드포인트를 사용할 수 있습니다. 요청된 프라이빗 엔드포인트 연결을 승인하거나 거부하려면 복구 자격 증명 모음 페이지의 **설정** 에서 **프라이빗 엔드포인트 연결** 로 이동합니다.

계속하기 전에 프라이빗 엔드포인트 리소스로 이동하여 연결 상태를 검토할 수 있습니다.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="자격 증명 모음의 프라이빗 엔드포인트 연결 페이지와 연결 목록을 보여 주는 스크린샷":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>(선택 사항) 캐시 스토리지 계정에 대한 프라이빗 엔드포인트 만들기

Azure Storage에 대해 프라이빗 엔드포인트를 사용할 수 있습니다. Azure Site Recovery 복제를 위해 스토리지 액세스에 대한 프라이빗 엔드포인트를 만드는 것은 선택 사항입니다. 스토리지에 대한 프라이빗 엔드포인트를 만드는 경우 바이패스 가상 네트워크의 캐시/로그 스토리지 계정에 대해 프라이빗 엔드포인트가 필요합니다.

> [!NOTE]
> 스토리지 계정에서 프라이빗 엔드포인트를 사용하도록 설정하지 않은 경우에도 성공적으로 보호됩니다. 그러나 복제 트래픽은 인터넷을 통해 Azure Site Recovery 퍼블릭 엔드포인트로 전송됩니다. 복제 트래픽이 프라이빗 링크를 통해 전달되도록 하려면 프라이빗 엔드포인트에서 스토리지 계정을 사용하도록 설정해야 합니다.

> [!NOTE]
> 범용 v2 스토리지 계정에서만 스토리지에 대한 프라이빗 엔드포인트를 만들 수 있습니다. 가격 책정 정보는 [Azure 페이지 Blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/page-blobs/)을 참조하세요.

[프라이빗 스토리지 만들기에 대한 지침](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint)에 따라 프라이빗 엔드포인트를 사용하여 스토리지 계정을 만듭니다. **프라이빗 DNS 영역과 통합** 에서 **예** 를 선택해야 합니다. 기존 DNS 영역을 선택하거나 새로 만듭니다.

### <a name="grant-required-permissions-to-the-vault"></a>자격 증명 모음에 필요한 권한 부여

설정에 따라 대상 Azure 지역에 하나 이상의 스토리지 계정이 필요할 수 있습니다. 그런 다음, Site Recovery에 필요한 모든 캐시/로그 스토리지 계정에 대한 관리 ID 권한을 부여합니다. 이 경우 필요한 스토리지 계정을 미리 만들어야 합니다.

가상 머신의 복제를 사용하도록 설정하기 전에 스토리지 계정 유형에 따라 자격 증명 모음의 관리 ID에 다음 역할 권한이 있어야 합니다.

- Resource Manager 기반 스토리지 계정(표준 유형):
  - [기여자](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage Blob 데이터 기여자](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager 기반 스토리지 계정(프리미엄 유형):
  - [기여자](../role-based-access-control/built-in-roles.md#contributor)
  - [Storage Blob 데이터 소유자](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 클래식 스토리지 계정:
  - [클래식 Storage 계정 기여자](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [클래식 스토리지 계정 키 운영자 서비스 역할](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

이러한 단계에서는 스토리지 계정에 역할 할당을 추가하는 방법에 대해 설명합니다.

1. 스토리지 계정으로 이동합니다. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.

1. **역할 할당 추가** 섹션에서 **추가** 를 선택합니다.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="스토리지 계정에 대한 액세스 제어(IAM) 페이지를 보여 주는 스크린샷":::

1. **역할 할당 추가** 페이지의 **역할** 목록에서 이 섹션의 시작 부분에 있는 목록에 포함된 역할을 선택합니다. 자격 증명 모음의 이름을 입력하고 **저장** 을 선택합니다.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="역할 할당 추가 페이지를 보여 주는 스크린샷.":::

이러한 권한을 추가한 후에는 Microsoft 신뢰할 수 있는 서비스에 대한 액세스를 허용해야 합니다. **방화벽 및 가상 네트워크** 로 이동하고 **예외** 에서 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용** 을 선택합니다.

### <a name="protect-your-virtual-machines"></a>가상 머신 보호

위의 작업을 완료한 후 온-프레미스 인프라의 설정을 계속 진행합니다. 다음 작업 중 하나를 완료하여 계속합니다. 

- [VMware 및 물리적 머신용 구성 서버 배포](./vmware-azure-deploy-configuration-server.md)
- [복제용 Hyper-V 환경 설정](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

설치가 완료되면 원본 머신에 대한 복제를 사용하도록 설정합니다. 바이패스 네트워크에서 자격 증명 모음에 대한 프라이빗 엔드포인트를 만들 때까지 인프라를 설정하지 마세요.

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>수동으로 프라이빗 DNS 영역 만들기 및 DNS 레코드 추가

자격 증명 모음에 대한 프라이빗 엔드포인트를 만들 때 프라이빗 DNS 영역과 통합하는 옵션을 선택하지 않은 경우 이 섹션의 단계를 따릅니다.

Site Recovery 공급자(Hyper-V 머신의 경우) 또는 프로세스 서버(VMware/물리적 머신의 경우)에서 프라이빗 FQDN을 개인 IP로 확인할 수 있도록 하나의 프라이빗 DNS 영역을 만듭니다.

1. 프라이빗 DNS 영역을 만듭니다.

   1. **모든 서비스** 검색 상자에서 "프라이빗 DNS 영역"을 검색한 다음, 결과에서 **프라이빗 DNS 영역** 을 선택합니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Azure Portal의 새 리소스 페이지에서 프라이빗 DNS 영역을 검색하는 방법을 보여 주는 스크린샷":::

   1. **프라이빗 DNS 영역** 페이지에서 **추가** 단추를 선택하여 새 영역 만들기를 시작합니다.

   1. **프라이빗 DNS 영역 만들기** 페이지에서 필요한 세부 정보를 입력합니다. 프라이빗 DNS 영역 이름으로 **privatelink.siterecovery.windowsazure.com** 을 입력합니다. 임의 리소스 그룹 및 구독을 선택할 수 있습니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="프라이빗 DNS 영역 만들기 페이지의 기본 사항 탭을 보여 주는 스크린샷":::

   1. **검토 \+ 만들기** 탭으로 이동하여 DNS 영역을 검토하고 만듭니다.

1. 가상 네트워크에 프라이빗 DNS 영역을 연결합니다.

   이제 만든 프라이빗 DNS 영역을 바이패스에 연결해야 합니다.

   1. 이전 단계에서 만든 프라이빗 DNS 영역으로 이동한 후 왼쪽 창에 있는 **가상 네트워크 링크** 로 이동합니다. **추가** 를 선택합니다.

   1. 필수 세부 정보를 입력합니다. **구독** 및 **가상 네트워크** 목록에서 바이패스 네트워크에 해당하는 세부 정보를 선택합니다. 다른 필드에는 기본값을 그대로 둡니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="가상 네트워크 링크 추가 페이지를 보여 주는 스크린샷":::

1. DNS 레코드를 추가합니다.

   이제 필요한 프라이빗 DNS 영역 및 프라이빗 엔드포인트를 만들었으므로 DNS 영역에 DNS 레코드를 추가해야 합니다.

   > [!NOTE]
   > 사용자 지정 프라이빗 DNS 영역을 사용하는 경우 다음 단계에 설명된 것과 유사하게 입력해야 합니다.

   이 단계에서는 프라이빗 DNS 영역에 프라이빗 엔드포인트의 각 FQDN에 대한 항목을 입력해야 합니다.

   1. 프라이빗 DNS 영역으로 이동한 다음, 왼쪽 창에서 **개요** 섹션으로 이동합니다. **레코드 집합** 을 선택하여 레코드 추가를 시작합니다.

   1. **레코드 집합 추가** 페이지에서 각 정규화된 도메인 이름 및 개인 IP 항목을 **A** 형식 레코드로 추가합니다. **개요** 의 **프라이빗 엔드포인트** 페이지에서 정규화된 도메인 이름 및 IP 목록을 가져올 수 있습니다. 다음 스크린샷에 표시된 것처럼 프라이빗 엔드포인트의 첫 번째 정규화된 도메인 이름이 프라이빗 DNS 영역의 레코드 집합에 추가됩니다.

      이러한 정규화된 도메인 이름은 `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com` 패턴과 일치합니다.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="레코드 집합 추가 페이지를 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

가상 머신 복제를 위한 프라이빗 엔드포인트를 사용하도록 설정했으므로 다음 문서에서 추가 및 관련 정보를 참조하세요.

- [온-프레미스 구성 서버 배포](./vmware-azure-deploy-configuration-server.md)
- [온-프레미스 Hyper-V VM의 Azure로의 재해 복구 설정](./hyper-v-azure-tutorial.md)
