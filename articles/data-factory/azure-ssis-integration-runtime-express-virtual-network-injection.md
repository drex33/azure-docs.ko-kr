---
title: Azure-SSIS 통합 런타임의 Express 삽입을 위한 가상 네트워크 구성
description: Azure-SSIS 통합 런타임의 명시적 주입을 위해 가상 네트워크를 구성하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1d904e33f830f36e41acd0afd32dc7dd7dfaf2c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102968"
---
# <a name="express-virtual-network-injection-method-preview"></a>Express 가상 네트워크 삽입 방법(미리 보기)

ADF(Azure Data Factory)에서 SSIS(SQL Server Integration Services)를 사용하는 경우 Azure-SSIS IR(통합 런타임)을 가상 네트워크에 조인하는 두 가지 방법인 표준 및 express가 있습니다. Express 메서드를 사용하는 경우 다음 요구 사항을 충족하도록 가상 네트워크를 구성해야 합니다. 

- *Microsoft.Batch가* Azure-SSIS IR 조인할 가상 네트워크가 있는 Azure 구독에 등록된 리소스 공급자인지 확인합니다. 자세한 지침은 [리소스 공급자로 Azure Batch 등록 섹션을](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch) 참조하세요.

- 가상 네트워크에 리소스 잠금이 없는지 확인합니다.

- Azure-SSIS IR 조인할 가상 네트워크에서 적절한 서브넷을 선택합니다. 자세한 내용은 아래 [서브넷 선택 섹션을](#subnet) 참조하세요.

- Azure-SSIS IR 만드는 사용자에게 가상 네트워크/서브넷에 조인하는 데 필요한 RBAC(역할 기반 액세스 제어) 권한이 부여되었는지 확인합니다.  자세한 내용은 아래의 [가상 네트워크 권한 선택 섹션을](#perms) 참조하세요.

특정 시나리오에 따라 필요에 따라 다음을 구성할 수 있습니다.

- Azure-SSIS IR 아웃바운드 트래픽에 고정 공용 IP 주소를 사용하려면 아래의 고정 [공용 IP 주소 구성 섹션을](#ip) 참조하세요.

- 가상 네트워크에서 자체 DNS(도메인 이름 시스템) 서버를 사용하려면 [아래의 사용자 지정 DNS 서버 구성 섹션을](#dns) 참조하세요.

- NSG(네트워크 보안 그룹)를 사용하여 서브넷의 아웃바운드 트래픽을 제한하려면 [아래의 NSG 구성 섹션을](#nsg) 참조하세요.

- UDR(사용자 정의 경로)을 사용하여 아웃바운드 트래픽을 감사/검사하려면 [아래의 UDR 구성](#udr) 섹션을 참조하세요.

다음은 Azure-SSIS IR에 필요한 연결을 보여 주는 다이어그램입니다.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-express.png" alt-text="Express 가상 네트워크 주입에서 Azure-SSIS IR 필요한 연결을 보여 주는 다이어그램.":::

## <a name="select-a-subnet"></a><a name="subnet"></a>서브넷 선택

Express 가상 네트워크 주입을 사용하도록 설정하려면 Azure-SSIS IR 조인할 적절한 서브넷을 선택해야 합니다.

- 가상 네트워크 게이트웨이 전용이므로 GatewaySubnet을 선택하지 마세요.

- 선택한 서브넷에 Azure-SSIS IR 노드 번호의 두 배 이상 사용 가능한 IP 주소가 있는지 확인합니다. 이는 Azure-SSIS IR 대한 패치/업그레이드를 롤아웃할 때 중단을 방지하기 위해 필요합니다. 또한 Azure는 각 서브넷에서 사용할 수 없는 일부 IP 주소를 예약합니다. 첫 번째 및 마지막 IP 주소는 프로토콜 준수를 위해 예약된 반면, 세 개의 주소는 Azure 서비스용으로 예약됩니다. 자세한 내용은 [서브넷 IP 주소 제한 섹션을 참조하세요.](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

- 다른 Azure 서비스(예: Azure SQL Managed Instance, App Service 등)에서 단독으로 사용하는 서브넷을 사용하지 마세요.

- 선택한 서브넷은 *Microsoft.Batch/batchAccounts* 서비스에 위임되어야 합니다. 자세한 내용은 [서브넷 위임 개요](../virtual-network/subnet-delegation-overview.md) 문서를 참조하세요. 자세한 지침은 [Azure Batch 서브넷 위임 섹션을](azure-ssis-integration-runtime-virtual-network-configuration.md#delegatesubnet) 참조하세요.

## <a name="select-virtual-network-permissions"></a><a name="perms"></a>가상 네트워크 권한 선택

Express 가상 네트워크 주입을 사용하려면 Azure-SSIS IR 만드는 사용자에게 가상 네트워크/서브넷에 조인하는 데 필요한 RBAC 권한이 부여되어야 합니다. 다음 두 가지 옵션을 사용할 수 있습니다.

- 기본 제공 *네트워크 기여자* 역할을 사용합니다. 이 역할에는 범위가 필요 이상으로 큰 _Microsoft.Network/\*_ 권한이 제공됩니다.

- 필요한 *Microsoft.Network/virtualNetworks/subnets/join/action* 권한만 포함하는 사용자 지정 역할을 만듭니다.

자세한 지침은 [가상 네트워크 권한 부여 섹션을 참조하세요.](azure-ssis-integration-runtime-virtual-network-configuration.md#grantperms)

## <a name="configure-a-static-public-ip-address"></a><a name="ip"></a>고정 공용 IP 주소 구성

Azure-SSIS IR 아웃바운드 트래픽에 고정 공용 IP 주소를 사용하려는 경우 방화벽에서 이를 허용하려면 [가상 네트워크 NAT(네트워크 주소 변환)를](../virtual-network/nat-gateway/nat-overview.md) 구성하여 설정해야 합니다.

## <a name="configure-a-custom-dns-server"></a><a name="dns"></a>사용자 지정 DNS 서버 구성

가상 네트워크에서 자체 DNS 서버를 사용하여 프라이빗 호스트 이름을 확인하려면 전역 Azure 호스트 이름(예: Azure Blob Storage)도 확인할 수 있는지 `<your storage account>.blob.core.windows` 확인합니다.

확인되지 않은 DNS 요청을 Azure 재귀 확인자의 IP *주소(168.63.129.16)로* 전달하도록 자체 DNS 서버를 구성하는 것이 좋습니다.

자세한 내용은 DNS 서버 이름 확인 섹션을 [참조하세요.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)

현재 Azure-SSIS IR 자체 DNS 서버를 사용하려면 다음 단계에 따라 표준 사용자 지정 설정으로 구성해야 합니다.

1. 사용자 지정 설치 스크립트([main.cmd](https://expressvnet.blob.core.windows.net/customsetup/main.cmd)) + 관련 파일([setupdnsserver.ps1](https://expressvnet.blob.core.windows.net/customsetup/setupdnsserver.ps1))을 다운로드합니다.

1. main.cmd의 "your-dns-server-ip"를 자체 DNS 서버의 IP 주소로 대체합니다.

1. 표준 사용자 지정 설정을 위해 고유한 Azure Storage Blob 컨테이너에 main.cmd + setupdnsserver.ps1 업로드 Azure-SSIS IR 프로비전할 때 SAS [URI를](how-to-configure-azure-ssis-ir-custom-setup.md) 입력합니다. Azure-SSIS IR 사용자 지정 문서를 참조하세요.

> [!NOTE]
> 프라이빗 호스트 이름에 FQDN(정규화된 도메인 이름)을 사용하세요(예: `<your_private_server>.contoso.com` 대신 `<your_private_server>` 사용). 또는 Azure-SSIS IR 표준 사용자 지정 설정을 사용하여 정규화되지 않은 단일 레이블 도메인 이름에 자체 DNS 접미사(예: )를 자동으로 추가한 다음 `contoso.com` DNS 쿼리에서 사용하기 전에 FQDN으로 변환할 수 있습니다. [표준 사용자 지정 설정 샘플](how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples) 섹션을 참조하세요. 

## <a name="configure-an-nsg"></a><a name="nsg"></a>NSG 구성

Azure-SSIS IR 조인된 서브넷에서 NSG를 사용하려면 다음 아웃바운드 트래픽을 허용합니다. 

| 전송 프로토콜 | 원본 | 원본 포트 | 대상 | 대상 포트 | 주석 | 
|--------------------|--------|--------------|-------------|-------------------|----------| 
| TCP | *VirtualNetwork* | * | *DataFactoryManagement* | *443* | Azure-SSIS IR ADF 서비스에 액세스하는 데 필요합니다.<br/><br/>아웃바운드 트래픽은 현재 ADF 공용 엔드포인트만 사용합니다. | 
| TCP | *VirtualNetwork* | * | *Sql/VirtualNetwork* | *1433, 11000-11999* | (선택 사항) Azure SQL Database 서버/Managed Instance 사용하여 SSIS 카탈로그(SSISDB)를 호스트하는 경우에만 필요합니다.<br/><br/>Azure SQL Database 서버/Managed Instance 공용 엔드포인트/가상 네트워크 서비스 엔드포인트로 구성된 경우 *Sql* 서비스 태그를 대상으로 사용합니다.<br/><br/>Azure SQL Database 서버/Managed Instance 프라이빗 엔드포인트로 구성된 경우 *VirtualNetwork* 서비스 태그를 대상으로 사용합니다.<br/><br/>서버 연결 정책이 *리디렉션* 대신 *프록시로* 설정된 경우 포트 *1433만* 필요합니다. | 
| TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *443* | (선택 사항) Azure Storage Blob 컨테이너를 사용하여 표준 사용자 지정 설정 스크립트/파일을 저장하는 경우에만 필요합니다.<br/><br/>Azure Storage 공용 엔드포인트/가상 네트워크 서비스 엔드포인트로 구성된 경우 *Storage* 서비스 태그를 대상으로 사용합니다.<br/><br/>Azure Storage 프라이빗 엔드포인트로 구성된 경우 *VirtualNetwork* 서비스 태그를 대상으로 사용합니다. | 
| TCP | *VirtualNetwork* | * | *Storage/VirtualNetwork* | *445* | (선택 사항) Azure Files 액세스해야 하는 경우에만 필요합니다.<br/><br/>Azure Storage 공용 엔드포인트/가상 네트워크 서비스 엔드포인트로 구성된 경우 *Storage* 서비스 태그를 대상으로 사용합니다.<br/><br/>Azure Storage 프라이빗 엔드포인트로 구성된 경우 *VirtualNetwork* 서비스 태그를 대상으로 사용합니다. | 

## <a name="configure-udrs"></a><a name="udr"></a>UDR 구성

Azure-SSIS IR 아웃바운드 트래픽을 감사/검사하려는 경우 [UDR(사용자 정의 경로)을](../virtual-network/virtual-networks-udr-overview.md) 사용하여 BGP(경계 게이트웨이 프로토콜) 경로 *0.0.0.0/0을* 가상 네트워크로 보급하는 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 강제 터널링을 통해 온-프레미스 방화벽 어플라이언스로 리디렉션하거나 방화벽으로 구성된 NVA(네트워크 가상 어플라이언스)로 리디렉션하거나 [Azure Firewall](../firewall/overview.md) 서비스로 리디렉션할 수 있습니다.

위의 [NSG 구성](#nsg) 섹션에 있는 지침에 따라 방화벽 어플라이언스/서비스에 유사한 규칙을 구현하여 Azure-SSIS IR 아웃바운드 트래픽을 허용해야 합니다.

- Azure Firewall 사용하는 경우:
  - *DataFactoryManagement* 서비스 태그를 대상으로 하여 아웃바운드 TCP 트래픽에 대해 포트 *443을* 열어야 합니다.

  - Azure SQL Database 서버/Managed Instance 사용하여 SSISDB를 호스트하는 경우 *Sql/VirtualNetwork* 서비스 태그를 대상으로 하는 아웃바운드 TCP 트래픽에 대해 *포트 1433, 11000-11999를* 열어야 합니다.

  - Azure Storage Blob 컨테이너를 사용하여 표준 사용자 지정 설정 스크립트/파일을 저장하는 경우 *대상으로 Storage/VirtualNetwork* 서비스 태그를 사용하여 아웃바운드 TCP 트래픽에 대한 포트 *443을* 열어야 합니다.

  - Azure Files 액세스해야 하는 경우 *Storage/VirtualNetwork* 서비스 태그를 대상으로 사용하여 아웃바운드 TCP 트래픽에 대해 포트 *445를* 열어야 합니다.

- 다른 방화벽 어플라이언스/서비스를 사용하는 경우:
  - *0.0.0.0/0* 또는 다음 Azure 환경별 FQDN을 대상으로 하는 아웃바운드 TCP 트래픽에 대해 포트 *443을* 열어야 합니다.

    | Azure 환경 | FQDN |
    |-------------------|------|
    | <b>Azure 퍼블릭</b> | _\*.frontend.clouddatahub.net_ |
    | <b>Azure Government</b> | _\*. frontend.datamovement.azure.us_ |
    | <b>Azure China 21Vianet</b> | _\*. frontend.datamovement.azure.cn_ |

  - Azure SQL Database server/Managed Instance를 사용 하 여 SSISDB를 호스트 하는 경우 *0.0.0.0/0* 또는 Azure SQL Database 서버/Managed Instance FQDN이 있는 아웃 바운드 TCP 트래픽에 대해 *1433, 11000-11999 포트를* 열어야 합니다.

  - Azure Storage blob 컨테이너를 사용 하 여 표준 사용자 지정 설치 스크립트/파일을 저장 하는 경우 *0.0.0.0/0* 또는 Azure Blob Storage FQDN을 대상으로 하는 아웃 바운드 TCP 트래픽에 대해 포트 *443* 을 열어야 합니다.

  - Azure Files에 액세스 해야 하는 경우 *0.0.0.0/0* 또는 Azure Files FQDN을 대상으로 하는 아웃 바운드 TCP 트래픽에 대해 포트 *445* 을 열어야 합니다.

## <a name="next-steps"></a>다음 단계

- [ADF UI를 통해 Azure-SSIS IR를 가상 네트워크에 가입](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure PowerShell를 통해 Azure-SSIS IR를 가상 네트워크에 조인](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR에 대한 자세한 내용은 다음 문서를 참조하세요. 

- [Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR 등 IR에 대한 일반적인 개념적 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](tutorial-deploy-ssis-packages-azure.md). 이 자습서에서는 Azure-SSIS IR을 만드는 단계별 지침을 제공합니다. Azure SQL Database server를 사용 하 여 SSISDB를 호스팅합니다. 
- [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 더 자세히 설명합니다. 가상 네트워크를 호스트 SSISDB에 연결 하는 가상 네트워크 서비스 엔드포인트/IP 방화벽 규칙/개인 끝점 또는 Azure SQL Managed Instance를 사용 하 여 구성 된 Azure SQL Database 서버를 사용 하는 방법에 대 한 지침을 제공 합니다. Azure-SSIS IR를 가상 네트워크에 조인 하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure SSIS IR에 대한 정보를 가져와 파악하는 방법을 설명합니다.
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법도 설명합니다.
