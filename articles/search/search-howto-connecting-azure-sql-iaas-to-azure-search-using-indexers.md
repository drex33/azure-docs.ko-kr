---
title: Azure VM의 SQL Server에 대한 인덱서 연결
titleSuffix: Azure Cognitive Search
description: 암호화된 연결을 사용하도록 설정하고 Azure Cognitive Search의 인덱서에서 Azure VM(가상 머신)의 SQL Server로의 연결을 허용하도록 방화벽을 구성합니다.
author: nitinme
ms.author: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/19/2021
ms.openlocfilehash: d405b28acfcac8270b0e2ec4b3493f151108e4cf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076114"
---
# <a name="indexer-connections-to-sql-server-on-an-azure-virtual-machine"></a>Azure 가상 머신의 SQL Server에 대한 인덱서 연결

Azure 가상 머신의 데이터베이스에서 콘텐츠를 추출하도록 [Azure SQL 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)를 구성하는 경우 보안 연결을 위한 추가 단계가 필요합니다. 

Azure Cognitive Search에서 가상 머신의 SQL Server로의 연결은 공용 인터넷 연결입니다. 보안 연결에 성공하려면 다음 단계를 완료합니다.

+ 가상 컴퓨터에서 SQL Server 인스턴스의 정규화된 도메인 이름에 대한 인증서를 [인증 기관 공급자](https://en.wikipedia.org/wiki/Certificate_authority#Providers)에서 가져옵니다.

+ 가상 컴퓨터에 인증서를 설치한 후 이 문서의 지침에 따라 VM에서 암호화된 연결을 사용하도록 설정하고 구성합니다.

## <a name="enable-encrypted-connections"></a>암호화 연결 사용

Azure Cognitive Search에는 공용 인터넷 연결을 통한 모든 인덱서 요청에 암호화된 채널이 필요합니다. 이 섹션에는 작동하도록 하는 단계가 나열되어 있습니다.

1. 인증서의 속성을 확인하여 주체 이름이 Azure VM의 정규화된 도메인 이름(FQDN)인지 확인합니다. 속성을 보기 위해 CertUtils 또는 인증서 스냅인과 같은 도구를 사용할 수 있습니다. VM 서비스 블레이드의 필수 패키지 섹션, **공용 IP 주소/DNS 이름 레이블** 필드, [Azure 포털](https://portal.azure.com/)에서 FQDN을 가져올 수 있습니다.
  
   + 최신 **Resource Manager** 템플릿을 사용하여 만든 VM의 경우 FQDN 형식은 `<your-VM-name>.<region>.cloudapp.azure.com`과 같습니다.

   + **클래식** VM으로 만든 이전 VM의 경우 FQDN 형식은 `<your-cloud-service-name.cloudapp.net>`과 같습니다.

1. 레지스트리 편집기(regedit)를 사용하여 인증서를 사용하도록 SQL Server를 구성합니다. 

   이 작업에는 보통 SQL Server 구성 관리자가 사용되지만 이 시나리오에 대해서는 사용할 수 없습니다. Azure에서 VM의 FQDN이 VM에서 결정한 것과 일치하지 않으므로 가져온 인증서를 찾지 못합니다(로컬 컴퓨터 또는 가입된 네트워크 도메인으로 도메인 식별). 이름이 일치하지 않는 경우 regedit를 사용하여 인증서를 지정합니다.

   + regedit에서 다음 레지스트리 키를 찾습니다. `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.

     `[MSSQL13.MSSQLSERVER]` 부분은 버전 및 인스턴스 이름에 따라 다릅니다. 

   + **인증서** 키 값을 VM에 가져온 TLS/SSL 인증서의 **지문** 으로 설정합니다.

     지문을 가져오는 방법에는 여러 가지가 있으며 여기에는 보다 나은 방법이 있습니다. MMC의 **인증서** 스냅인에서 복사하는 경우 [이 지원 문서에 설명된 대로](https://support.microsoft.com/kb/2023869/)보이지 않는 선행 문자를 선택할 가능성이 있으며 이로 인해 연결을 시도할 때 오류가 발생합니다. 이 문제를 해결하기 위한 여러 가지 해결 방법이 존재합니다. 가장 쉬운 방법은 백스페이스 키로 덮은 후 지문의 첫 문자를 다시 입력하여 regedit의 키 값 필드에서 선행 문자를 제거하는 것입니다. 또는 다른 도구를 사용하여 지문을 복사할 수 있습니다.

1. 서비스 계정에 권한을 부여합니다. 

    SQL Server 서비스 계정에 TLS/SSL 인증서의 프라이빗 키에 적절한 권한이 부여되어 있는지 확인합니다. 이 단계를 무시하면 SQL Server가 시작되지 않습니다. 이 작업에 대해 **인증서** 스냅인 또는 **CertUtils** 를 사용할 수 있습니다.

1. SQL Server 서비스를 다시 시작합니다.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>VM에서 SQL Server 연결을 구성합니다.

Azure Cognitive Search에 필요한 암호화된 연결을 설정한 후에는 Azure VM의 SQL Server에 고유한 추가 구성 단계가 있습니다. 아직 수행하지 않은 경우, 다음 단계로 이 문서 중 하나를 사용하여 구성을 완료합니다.

+ **Resource Manager** VM인 경우 [Azure에서 Resource Manager를 사용하여 SQL Server Virtual Machine에 연결](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md)을 참조하세요. 

+ **클래식** VM인 경우 [Azure 클래식에서 SQL Server Virtual Machine에 연결](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect)을 참조하세요.

특히 "인터넷을 통한 연결"의 각 문서에서 해당 섹션을 검토하세요.

## <a name="configure-the-network-security-group-nsg"></a>NSG(네트워크 보안 그룹) 구성

Azure VM에서 다른 대상에 액세스할 수 있게 하기 위해 NSG 및 해당 Azure 엔드포인트 또는 ACL(Access Control 목록)을 구성하는 것은 특별하지 않습니다. 이전에 이러한 구성을 수행하여 자체 애플리케이션 논리가 SQL Azure VM에 연결되도록 했을 것입니다. 이는 SQL Azure VM에 Azure Cognitive Search를 연결하는 것과는 다릅니다. 

아래 링크는 VM 배포를 위한 NSG 구성에 대한 지침을 제공합니다. 이러한 지침에 따라 IP 주소를 기반으로 Azure Cognitive Search 엔드포인트를 ACL로 지정합니다.

> [!NOTE]
> 배경 지식은 [네트워크 보안 그룹이란?](../virtual-network/network-security-groups-overview.md)

+ **Resource Manager** VM의 경우 [ARM 배포를 위해 NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)을 참조하세요.

+ **클래식** VM의 경우 [클래식 배포를 위해 NSG를 만드는 방법](/previous-versions/azure/virtual-network/virtual-networks-create-nsg-classic-ps)을 참조하세요.

IP 주소 지정의 경우 몇 가지 문제를 내포할 수 있으며 사용자가 문제와 잠재적인 해결 방법을 인식하고 있는 경우 쉽게 극복할 수 있습니다. 나머지 섹션에서는 ACL에서 IP 주소와 관련된 문제 처리를 위한 권장 사항을 제공합니다.

### <a name="restrict-access-to-the-azure-cognitive-search"></a>Azure Cognitive Search에 대한 액세스 제한

모든 연결 요청에 대해 SQL Azure VM이 열리도록 하는 대신, ACL에 포함된 `AzureCognitiveSearch` [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags)의 IP 주소 범위와 검색 서비스의 IP 주소로 액세스를 제한하는 것이 좋습니다.

검색 서비스의 FQDN(예: `<your-search-service-name>.search.windows.net`)을 ping하여 IP 주소를 확인할 수 있습니다. 검색 서비스 IP 주소는 변경할 수 있지만 변경될 가능성은 거의 없습니다. IP 주소는 서비스의 수명 기간 동안 고정되는 경향이 있습니다.

[다운로드 가능한 JSON 파일](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)을 사용하거나 [서비스 태그 검색 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api)를 통해 `AzureCognitiveSearch` [서비스 태그](../virtual-network/service-tags-overview.md#available-service-tags)의 IP 주소 범위를 확인할 수 있습니다. IP 주소 범위는 매주 업데이트됩니다.

### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Azure Cognitive Search 포털 IP 주소 포함

Azure Portal을 사용하여 인덱서를 만드는 경우 SQL Azure 가상 머신에 대한 포털 인바운드 액세스 권한을 부여해야 합니다. 방화벽의 인바운드 규칙을 사용하려면 포털의 IP 주소를 제공해야 합니다.

포털 IP 주소를 가져오려면 트래픽 관리자의 도메인인 `stamp2.ext.search.windows.net`을 ping합니다. 요청 시간이 초과되지만 IP 주소는 상태 메시지에 표시됩니다. 예를 들어 "Ping azsyrie.northcentralus.cloudapp.azure.com [52.252.175.48]" 메시지에서 IP 주소는 "52.252.175.48"입니다.

> [!NOTE]
> 다른 지역의 클러스터는 서로 다른 트래픽 관리자에게 연결됩니다. 도메인 이름과 상관없이 ping에서 반환된 IP 주소는 해당 지역의 Azure Portal에 대한 인바운드 방화벽 규칙을 정의할 때 사용할 올바른 IP 주소입니다.

## <a name="next-steps"></a>다음 단계

구성이 해결되면 이제 Azure VM의 SQL Server를 Azure Cognitive Search 인덱서에 대한 데이터 원본으로 지정할 수 있습니다. 자세한 내용은 [인덱서를 사용하여 Azure Cognitive Search에 Azure SQL Database 연결](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)을 참조하세요.
