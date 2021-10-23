---
title: SSIS Integration Runtime 관리 문제 해결
description: 이 문서에서는 SSIS IR(SSIS Integration Runtime)의 관리 이슈 해결에 대한 참고 자료를 제공합니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: b7d6605f1a387a917c9d106078ead404842ea02c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251930"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Azure Data Factory에서 SSIS Integration Runtime 문제 해결

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 SSIS IR이라고도 하는 Azure SSIS IR(SQL Server Integration Services)의 관리 이슈 해결에 대한 참고 자료를 제공합니다.

## <a name="overview"></a>개요

SSIS IR을 프로비저닝하거나 프로비저닝을 해제하는 동안 이슈가 발생하면 Microsoft Azure Data Factory 포털 또는 PowerShell cmdlet에서 반환된 오류에 대한 메시지가 표시됩니다. 오류 메시지는 항상 오류에 대한 자세한 내용과 함께 오류 코드 형식으로 표시됩니다.

오류 코드가 InternalServerError인 경우 서비스에 일시적으로 이슈가 발생한 것이므로 나중에 작업을 다시 시도해야 합니다. 다시 시도해도 문제가 계속될 경우, Azure Data Factory 지원 팀에 문의하세요.

그렇지 않으면 Azure SQL Database 또는 Azure SQL Managed Instance, 사용자 지정 설치 스크립트 및 가상 네트워크 구성이라는 세 가지 주요 외부 종속성으로 인해 오류가 발생할 수 있습니다.

## <a name="sql-database-or-sql-managed-instance-issues"></a>SQL Database 또는 SQL Managed Instance

SSIS 카탈로그 데이터베이스를 사용하여 SSIS IR을 프로비저닝하는 경우 SQL Database 또는 SQL Managed Instance가 필요합니다. SSIS IR은 Azure SQL Database 서버 또는 SQL Managed Instance에 액세스할 수 있어야 합니다. 또한 SQL Database 또는 SQL Managed Instance의 로그인 계정에는 SSISDB(SSIS 카탈로그 데이터베이스)를 만들 수 있는 권한이 있어야 합니다. 오류가 발생하는 경우 자세한 SQL 예외 메시지와 함께 오류 코드가 Data Factory 포털에 표시됩니다. 다음 목록의 정보를 사용하여 오류 코드 문제를 해결합니다.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

새 SSIS IR을 프로비전할 때 또는 IR을 실행하는 동안 이 이슈가 표시될 수 있습니다. IR을 프로비전하는 동안 이 오류가 발생하는 경우 오류 메시지에 다음과 같은 문제 중 하나를 나타내는 자세한 SqlException 메시지가 표시될 수 있습니다.

* 네트워크 연결 이슈. SQL Database 또는 SQL Managed Instance의 호스트 이름에 액세스할 수 있는지 확인합니다. 또한 방화벽 또는 NSG(네트워크 보안 그룹)가 서버에 대한 SSIS IR 액세스를 차단하고 있지 않은지 확인합니다.
* SQL 인증 중에 로그인에 실패했습니다. 제공된 계정은 SQL Server 데이터베이스에 로그인할 수 없습니다. 올바른 사용자 계정을 제공해야 합니다.
* Microsoft Azure AD(Active Directory) 인증 중에 로그인에 실패했습니다(관리 ID). AAD 그룹에 팩터리의 관리 ID를 추가하고 관리 ID에 카탈로그 데이터베이스 서버에 대한 액세스 권한이 있는지 확인합니다.
* 연결 시간 제한. 이 오류는 항상 보안 관련 구성으로 인해 발생합니다. 다음을 수행하는 것이 좋습니다.
  1. 새 VM을 만듭니다.
  1. IR이 가상 네트워크에 있는 경우 VM을 IR의 동일한 Microsoft Azure Virtual Network에 참가합니다.
  1. SSMS를 설치하고 SQL Database 또는 SQL Managed Instance 상태를 확인합니다.

다른 문제가 있는 경우 자세한 SQL 예외 오류 메시지에 표시된 이슈를 해결합니다. 문제가 여전히 발생하는 경우 SQL Database 또는 SQL Managed Instance 지원 팀에 문의하세요.

IR을 실행하는 동안 오류가 발생하는 경우 네트워크 보안 그룹 또는 방화벽 변경으로 인해 SSIS IR 작업자 노드가 Azure SQL Database 서버 또는 SQL Managed Instance에 액세스하지 못할 수 있습니다. SSIS IR 작업자 노드가 Azure SQL Database 또는 SQL Managed Instance에 액세스할 수 있도록 차단을 해제합니다.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

이런 종류의 오류 메시지는 “데이터베이스 SSISDB가 할당 크기에 도달. 데이터를 분할 또는 삭제, 인덱스를 삭제하거나 기타 해결 방법에 대한 설명서 참조”로 표시됩니다. 

가능한 해결 방법은 다음과 같습니다.
* SSISDB의 할당 크기를 늘립니다.
* SSISDB의 구성을 변경하여 크기를 줄입니다.
   * 보존 기간 및 프로젝트 버전 수 줄이기
   * 로그의 보존 기간 단축
   * 로그의 기본 수준 변경

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

해당 오류는 SQL Database 또는 SQL Managed Instance에 이미 SSISDB가 있고 다른 IR에서 사용되고 있음을 의미합니다. 다른 SQL Database 또는 SQL Managed Instance를 제공하거나 기존 SSISDB를 삭제하고 새로운 IR을 다시 시작해야 합니다.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

이 오류는 다음 이유 중 하나로 인해 발생할 수 있습니다.

* SSIS IR에 대해 구성된 사용자 계정에는 데이터베이스를 만들 수 있는 권한이 없습니다. 사용자에게 데이터베이스를 만들 수 있는 권한을 부여할 수 있습니다.
* 데이터베이스를 만드는 동안 실행 시간 초과 또는 DB 작업 시간 초과와 같은 시간 초과가 발생합니다. 나중에 작업을 다시 시도해야 합니다. 다시 시도가 작동하지 않는 경우 SQL Database 또는 SQL Managed Instance 지원 팀에 문의하세요.

다른 이슈의 경우 SQL 예외 오류 메시지를 확인하고 오류 세부 정보에 설명된 이슈를 해결합니다. 문제가 여전히 발생하는 경우 SQL Database 또는 SQL Managed Instance 지원 팀에 문의하세요.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

이런 종류의 오류 메시지는 ‘잘못된 개체 이름 catalog.catalog_properties’로 표시됩니다. 이 경우 이름이 SSISDB인 데이터베이스가 이미 있지만 SSIS IR로 생성되지 않았거나 마지막 SSIS IR 프로비저닝 시 오류로 인해 데이터베이스가 잘못된 상태입니다. 이름이 SSISDB인 기존 데이터베이스를 삭제하거나 IR에 대한 새 SQL Database 또는 SQL Managed Instance을 구성할 수 있습니다.

## <a name="custom-setup-issues"></a>사용자 지정 설치 이슈

사용자 지정 설정은 SSIS IR의 프로비전 또는 재구성 중에 자체 설정 단계를 추가하는 인터페이스를 제공합니다. 자세한 내용은 [Azure-SSIS Integration Runtime을 위한 사용자 지정 설정](./how-to-configure-azure-ssis-ir-custom-setup.md)을 참조하세요.

컨테이너에 필요한 사용자 지정 설정 파일만 포함되어 있는지 확인합니다. 컨테이너의 모든 파일이 SSIS IR 작업자 노드에 다운로드됩니다. SSIS IR에서 스크립트를 실행하기 전에 스크립트 실행 이슈를 해결하려면 로컬 컴퓨터에서 사용자 지정 설정 스크립트를 테스트하는 것이 좋습니다.

SSIS IR은 정기적으로 업데이트되기 때문에 IR을 실행하는 동안 사용자 지정 설정 스크립트 컨테이너가 검사됩니다. 이렇게 업데이트하려면 컨테이너에 액세스하여 사용자 지정 설정 스크립트를 다운로드하고 다시 설치해야 합니다. 또한 이 프로세스에서 컨테이너에 액세스할 수 있는지 여부와 main.cmd 파일이 있는지 여부도 확인합니다.

사용자 지정 설정을 포함하는 모든 오류의 경우 CustomSetupScriptBlobContainerInaccessible 또는 CustomSetupScriptNotFound와 같은 하위 코드를 포함하는 CustomSetupScriptFailure 오류 코드가 표시됩니다.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

이 오류는 SSIS IR이 사용자 지정 설정을 위해 Azure Blob 컨테이너에 액세스할 수 없음을 의미합니다. 컨테이너의 SAS URI에 연결할 수 있고 만료되지 않았는지 확인합니다.

IR이 실행 중인 경우 IR을 중지하고 새 사용자 지정 설정 컨테이너 SAS URI를 사용하여 IR을 다시 구성한 다음 IR을 다시 시작합니다.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

이 오류는 SSIS IR이 Blob 컨테이너에서 사용자 지정 설정 스크립트(main.cmd)를 찾을 수 없음을 의미합니다. main.cmd.exe가 컨테이너에 있는지 확인합니다. 이는 사용자 지정 설정 프로그램 설치를 위한 진입점입니다.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

이 오류는 사용자 지정 설정 스크립트(main.cmd)의 실행이 실패했음을 의미합니다. 먼저 로컬 컴퓨터에서 스크립트를 사용해 보거나 Blob 컨테이너에서 사용자 지정 설정 실행 로그를 확인합니다.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

이 오류는 사용자 지정 설정 스크립트 실행 시간 초과를 나타냅니다. 스크립트가 자동으로 실행되고 대화형 입력이 필요하지 않은지 확인하고 BLOB 컨테이너에 필요한 사용자 지정 설정 파일만 포함되어 있는지 확인합니다. 먼저 로컬 컴퓨터에서 스크립트를 테스트하는 것이 좋습니다. 또한 Blob 컨테이너에서 사용자 지정 설정 실행 로그도 확인해야 합니다. 사용자 지정 설정의 최대 기간은 시간이 초과되기 전 45분이며, 최대 기간에는 컨테이너에서 모든 파일을 다운로드하여 SSIS IR에 설치하는 시간이 포함됩니다. 시간이 더 필요한 경우 지원 티켓을 요청합니다.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

이 오류는 사용자 지정 설정 실행 로그를 Blob 컨테이너에 업로드하지 못했음을 의미합니다. 이 문제는 SSIS IR이 Blob 컨테이너에 대한 쓰기 권한을 가지고 있지 않거나 스토리지 또는 네트워크 이슈 때문에 발생합니다. 사용자 지정 설정이 성공하면 이 오류가 SSIS 기능에 영향을 주지 않지만 로그가 누락됩니다. 다른 오류로 인해 사용자 지정 설정에 실패하고 로그가 업로드되지 않은 경우 분석을 위해 로그를 업로드할 수 있도록 먼저 이 오류를 보고합니다. 또한 이 이슈가 해결된 후 더 구체적인 이슈를 보고합니다. 다시 시도한 후에도 이 이슈가 해결되지 않으면 Azure Data Factory 지원 팀에 문의하세요.

## <a name="virtual-network-configuration"></a>가상 네트워크(VNet) 구성

SSIS IR을 Azure Virtual Network에 조인할 때 SSIS IR은 사용자 구독에 속한 가상 네트워크를 사용합니다. 자세한 내용은 [Azure-SSIS Integration Runtime을 가상 네트워크에 조인](./join-azure-ssis-integration-runtime-virtual-network.md)을 참조하세요.
SSIS IR을 성공적으로 시작한 후 네트워크 연결 문제가 발생하는 경우 [연결 진단 도구](ssis-integration-runtime-diagnose-connectivity-faq.md)를 사용하여 문제를 직접 진단할 수 있습니다.
Virtual Network 관련 이슈가 있는 경우 다음 오류 중 하나가 표시됩니다.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

이 오류는 다양한 원인으로 발생할 수 있습니다. 문제를 해결하려면 [사용할 수 없음](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) 및 [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings) 섹션을 참조하세요.

### <a name="forbidden"></a>사용할 수 없음

이런 종류의 오류는 “현재 계정에서 SubnetId를 사용할 수 없음. Microsoft.Batch 리소스 공급자가 동일한 VNet 구독에 등록되어 있지 않음”과 유사합니다.

이러한 세부 정보는 Azure Batch가 가상 네트워크에 액세스할 수 없음을 의미합니다. Virtual Network와 동일한 구독에 Microsoft.Batch 리소스 공급자를 등록합니다.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

이러한 종류의 오류는 다음 중 하나와 유사할 수 있습니다. 

- ‘지정된 VNet이 없거나 Batch 서비스에서 액세스할 수 없음’
- ‘지정된 서브넷(xxx) 없음’

이러한 오류는 가상 네트워크가 없거나, Azure Batch 서비스에서 액세스할 수 없거나, 제공된 서브넷이 존재하지 않음을 의미합니다. 가상 네트워크 및 서브넷이 존재하고 Azure Batch에서 액세스할 수 있는지 확인합니다.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

이런 종류의 오류 메시지는 “VNet에서 Integration Runtime 프로비저닝 실패. DNS 서버 또는 NSG 설정이 구성된 경우, DNS 서버 액세스 가능 여부 및 NSG가 올바르게 구성되었는지 확인”으로 표시됩니다.

이 경우에는 사용자 지정된 DNS 서버 또는 NSG 설정 구성이 있을 수 있습니다. 그러면 SSIS IR에 필요한 Azure 서버 이름을 확인하거나 액세스할 수 없습니다. 자세한 내용은 [SSIS IR Virtual Network 구성](./join-azure-ssis-integration-runtime-virtual-network.md)을 참조하세요. 여전히 문제가 발생하는 경우 Azure Data Factory 지원 팀에 문의하세요.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR은 정기적으로 자동으로 업데이트됩니다. 업그레이드하는 동안 새 Azure Batch 풀이 만들어지고 이전 Azure Batch 풀이 삭제됩니다. 또한 이전 풀에 대한 Virtual Network 관련 리소스가 삭제되고 구독에 새 Virtual Network 관련 리소스가 생성됩니다. 이 오류는 구독 또는 리소스 그룹 수준에서 삭제 잠금으로 인해 이전 풀의 Virtual Network 관련 리소스를 삭제하지 못했음을 의미합니다. 고객이 삭제 잠금을 제어하고 설정하므로 이 경우 삭제 잠금을 제거해야 합니다.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

SSIS IR 프로비전이 실패하면 생성된 모든 리소스가 삭제됩니다. 그러나 구독 또는 리소스 그룹 수준에서 리소스 삭제 잠금이 있는 경우 Virtual Network 리소스가 예상대로 삭제되지 않습니다. 이 오류를 해결하려면 삭제 잠금을 제거하고 IR을 다시 시작합니다.

### <a name="vnetresourcegrouplockedduringstopvnetdeletelock"></a>VNetResourceGroupLockedDuringStop/VNetDeleteLock

SSIS IR을 중지하면 모든 Virtual Network 관련 리소스가 삭제됩니다. 그러나 구독 또는 리소스 그룹 수준에서 리소스 삭제 잠금이 있는 경우 삭제가 실패할 수 있습니다. 이 경우에도 고객이 삭제 잠금을 제어하고 설정합니다. 따라서 삭제 잠금을 제거한 후 SSIS IR을 다시 중지해야 합니다.

### <a name="nodeunavailable"></a>NodeUnavailable

이 오류는 IR이 실행 중일 때 발생하며 IR이 비정상 상태임을 의미합니다. 이 오류는 항상 SSIS IR이 필요한 서비스에 연결되지 않도록 차단하는 DNS 서버 또는 NSG 구성의 변경으로 인해 발생합니다. DNS 서버 및 NSG의 구성은 고객에 의해 제어되므로 결국 고객이 차단 이슈를 해결해야 합니다. 자세한 내용은 [SSIS IR Virtual Network 구성](./join-azure-ssis-integration-runtime-virtual-network.md)을 참조하세요. 여전히 문제가 발생하는 경우 Azure Data Factory 지원 팀에 문의하세요.

## <a name="static-public-ip-addresses-configuration"></a>고정 공용 IP 주소 구성

Azure-SSIS IR을 Azure Virtual Network에 참가하는 경우 IR이 특정 IP 주소에 대한 액세스를 제한하는 데이터 원본에 액세스할 수 있도록 IR에 대한 고정 공용 IP 주소를 가져올 수도 있습니다. 자세한 내용은 [Azure-SSIS Integration Runtime을 가상 네트워크에 조인](./join-azure-ssis-integration-runtime-virtual-network.md)을 참조하세요.

위의 가상 네트워크 이슈 외에도 고정 공용 IP 주소 관련 이슈가 발생할 수 있습니다. 도움이 필요하면 다음 오류를 확인하세요.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>InvalidPublicIPSpecified

해당 오류는 Azure-SSIS IR을 시작할 때 다양한 이유로 발생할 수 있습니다.

| 오류 메시지 | 솔루션|
|:--- |:--- |
| 제공된 고정 공용 IP 주소가 이미 사용 중입니다. Azure-SSIS Integration Runtime에 사용하지 않는 두 개의 주소를 제공하세요. | 사용하지 않는 고정 공용 IP 주소 두 개를 선택하거나 지정된 공용 IP 주소에 대한 현재 참조를 제거한 후 Azure-SSIS IR을 다시 시작해야 합니다. |
| 제공된 고정 공용 IP 주소에 DNS 이름이 없습니다. Azure-SSIS IR에 대한 DNS 이름 두 개를 제시하세요. | 아래 그림처럼 Azure Portal에서 공용 IP 주소의 DNS 이름을 설정할 수 있습니다. 구체적인 단계는 다음과 같습니다. (1) Azure Portal을 열고 해당 공용 IP 주소의 리소스 페이지로 이동. (2) **구성** 섹션을 선택하고 DNS 이름을 설정한 다음 **저장** 버튼 클릭. (3) Azure-SSIS IR 다시 시작. |
| Azure-SSIS IR에 제공된 VNet 및 고정 공용 IP 주소는 동일한 위치에 있어야 합니다. | Azure 네트워크 요구 사항에 따라 고정 공용 IP 주소와 가상 네트워크는 동일한 위치 및 구독에 있어야 합니다. 두 개의 유효한 고정 공용 IP 주소를 제공하고 Azure-SSIS IR을 다시 시작하세요. |
| 제공된 고정 공용 IP 주소는 기본 IP 주소입니다. Azure-SSIS IR에 대해 IP 두 개의 표준 주소를 제공하세요. | 도움이 필요하면 [공용 IP 주소의 SKU](../virtual-network/ip-services/public-ip-addresses.md#sku)를 참조하세요. |

:::image type="content" source="media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png" alt-text="Azure-SSIS IR":::

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

Azure SSIS IR의 프로비저닝이 실패하면 생성된 모든 리소스가 삭제됩니다. 그러나 구독 또는 리소스 그룹(고정 공용 IP 주소를 포함) 단계에서 리소스 삭제에 잠금이 설정되어 있는 경우 네트워크 리소스는 삭제되지 않습니다. 해당 오류를 해결하려면 삭제 잠금을 해지하고 IR을 다시 시작합니다.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

Azure-SSIS IR을 중지하면 공용 IP 주소를 포함하는 리소스 그룹에 만들어진 모든 네트워크 리소스가 삭제됩니다. 그러나 구독 또는 리소스 그룹(고정 공용 IP 주소를 포함) 수준에서 리소스 삭제에 잠금이 설정되어 있는 경우 삭제에 실패할 수 있습니다. 해당 오류를 해결하려면 삭제 잠금을 제거하고 IR을 다시 시작합니다.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

Azure SSIS IR은 정기적으로 자동 업데이트됩니다. 업그레이드하는 동안 새 IR 노드가 만들어지고 이전 노드는 삭제됩니다. 또한 이전 노드에 대해 생성된 네트워크 리소스(예: Load Balancer 및 네트워크 보안 그룹)가 삭제되고 새로운 네트워크 리소스가 구독에 생성됩니다. 해당 오류는 구독 또는 리소스 그룹(고정 공용 IP 주소 포함)의 삭제 잠금 설정으로 인해 이전 노드에 대한 네트워크 리소스를 삭제하지 못했음을 의미합니다. 이전 노드를 정리하고 이전 노드에 대한 고정 공용 IP 주소를 릴리스할 수 있도록 삭제 잠금을 제거하세요. 그렇지 않으면 고정 공용 IP 주소를 릴리스할 수 없고 IR을 추가로 업그레이드할 수 없게 됩니다.

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableDuringUpgrade

사용자 고유의 고정 공용 IP 주소를 가져오려면 두 개의 공용 IP 주소를 제공해야 합니다. 그 중 하나는 IR 노드를 즉시 만드는 데 사용되고 다른 하나는 IR을 업그레이드하는 동안 사용됩니다. 업그레이드하는 동안 다른 공용 IP 주소를 사용할 수 없는 경우 해당 오류가 발생할 수 있습니다. 가능한 원인은  [InvalidPublicIPSpecified](#InvalidPublicIPSpecified)를 참조하세요.