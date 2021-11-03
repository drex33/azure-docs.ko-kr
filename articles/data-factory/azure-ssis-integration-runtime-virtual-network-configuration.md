---
title: Azure SSIS integration runtime의 삽입을 위해 가상 네트워크 구성
description: Azure SSIS integration runtime의 삽입을 위해 가상 네트워크를 구성 하는 방법에 대해 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 32930edc0aa02fa537380ceadffb04a1fd5729da
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068806"
---
# <a name="configure-a-virtual-network-for-injection-of-azure-ssis-integration-runtime"></a>Azure SSIS integration runtime의 삽입을 위해 가상 네트워크 구성

Azure Data Factory (ADF)에서 ssis (SQL Server Integration Services)를 사용 하는 경우 Azure ssis IR (통합 런타임)을 가상 네트워크 (standard 및 express)에 조인 하는 두 가지 방법이 있습니다. Express Azure-SSIS IR 메서드는 인바운드 뿐만 아니라 아웃 바운드, 트래픽 요구 사항이 적고, 표준 방법과 관련 하 여 몇 가지 제한이 있습니다.

## <a name="compare-the-standard-and-express-virtual-network-injection-methods"></a><a name="compare"></a>표준 및 express virtual network 삽입 방법 비교

다음 표에서는 표준 및 express virtual network 주입 방법 간의 차이점을 강조 표시 합니다.

| 비교 | 표준 가상 네트워크 주입 | Express virtual network 주입 |
|------------|------------------------------------|-----------------------------------|
| **Azure-SSIS IR 시작 기간** | 약 30 분입니다. | 약 5 분입니다. | 
| **Azure 구독 & 리소스 그룹 설정** | *Microsoft Batch* 는 가상 네트워크 구독에서 리소스 공급자로 등록 되어야 합니다.<br/><br/>가상 네트워크 리소스 그룹에서 공용 IP 주소, 부하 분산 장치 및 NSG (네트워크 보안 그룹) 만들기를 허용 해야 합니다. | *Microsoft Batch* 는 가상 네트워크 구독에서 리소스 공급자로 등록 되어야 합니다. | 
| **가상 네트워크 서브넷** | 서브넷 되어서는 안됩니다 다른 Azure 서비스에 전용으로 사용할 수 있습니다. | 서브넷 되어서는 안됩니다 다른 Azure 서비스에 전용으로 사용할 수 있습니다.<br/><br/>서브넷은 *Microsoft Batch/batchAccounts* 에 위임 해야 합니다. | 
| **가상 네트워크 권한** | Azure-SSIS IR를 만드는 사용자에 게는 _Microsoft. Network/virtualNetworks/ \* /cjoin_ 권한이 있어야 합니다. | Azure-SSIS IR를 만드는 사용자에 게는 *Microsoft. Network/virtualNetworks/서브넷/join/action* 권한이 있어야 합니다. | 
| **고정 공용 IP 주소** | 필드 Azure-SSIS IR에 대 한 고정 공용 IP 주소 (BYOIP)를 가져옵니다. | 필드 Azure-SSIS IR에 대 한 고정 공용 IP 주소를 설정 하도록 가상 NAT (network address translation)를 구성 합니다. | 
| **사용자 지정 DNS 서버** | 확인 되지 않은 DNS 요청을 Azure 재귀 확인자에 전달 하는 것이 좋습니다. | 확인 되지 않은 DNS 요청을 Azure 재귀 확인자에 전달 하는 것이 좋습니다.<br/><br/>Azure-SSIS IR에 대 한 표준 사용자 지정 설정이 필요 합니다. | 
| **인바운드 트래픽** | *Batchnodemanagement* service 태그를 원본으로 사용 하는 TCP 트래픽에 대해 포트 *29876, 29877이* 열려 있어야 합니다. | 필수 아님. | 
| **아웃 바운드 트래픽** | *Azurecloud* service 태그를 대상으로 사용 하는 TCP 트래픽에 대해 포트 *443* 가 열려 있어야 합니다. | *DataFactoryManagement* 서비스 태그를 대상으로 사용 하는 TCP 트래픽에 대해 포트 *443* 가 열려 있어야 합니다. | 
| **리소스 잠금** | 리소스 그룹에는 허용 되지 않습니다. | 가상 네트워크에서 허용 되지 않습니다. | 
| **Azure-가상 네트워크 당 SSIS IRs** | 무제한. | 하나만 있습니다. | 

사용자의 가상 네트워크는 삽입 방법에 따라 다르게 구성 해야 합니다. Express 메서드를 사용 하는 경우 [express virtual network 주입 방법](azure-ssis-integration-runtime-express-virtual-network-injection.md) 문서를 참조 하세요. 그렇지 않으면 [표준 virtual network 주입 방법](azure-ssis-integration-runtime-standard-virtual-network-injection.md) 문서를 참조 하세요.
  
## <a name="register-azure-batch-as-a-resource-provider"></a><a name="registerbatch"></a>리소스 공급자로 Azure Batch 등록

Azure Portal를 사용 하 여 ADF의 SSIS에 대 한 기본 인프라로 Azure-SSIS IR 가입할 가상 네트워크가 있는 Azure 구독의 리소스 공급자로 Azure Batch를 등록할 수 있습니다. 이미 Azure Batch를 사용 했거나 해당 구독의 ADF UI를 통해 Azure-SSIS IR 만든 경우 이미 등록 되어 있습니다. 그렇지 않으면 다음 단계를 수행 하 여 수행할 수 있습니다.

1. Azure Portal에서 가상 네트워크를 선택한 후 **개요** 페이지에서 강조 표시 된 구독을 선택 합니다.  

1. 왼쪽 메뉴에서 **리소스 공급자** 를 선택 합니다.

1. 아직 등록 되지 않은 경우에는 *Microsoft Batch* 를 선택 하 고 등록 합니다.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="&quot;등록 된 &quot; 상태 확인":::

목록에 *Microsoft Batch* 가 표시 되지 않는 경우 해당 구독에서 [빈 Azure Batch 계정을 만들어](../batch/batch-account-create-portal.md) 나중에 삭제할 수 있습니다. 

## <a name="delegate-a-subnet-to-azure-batch"></a><a name="delegatesubnet"></a>Azure Batch에 서브넷 위임

Express virtual network 주입을 사용 하는 경우 ADF의 SSIS에 대 한 기본 인프라를 Azure Batch 위해 Azure-SSIS IR를 삽입할 서브넷을 위임 해야 합니다. Azure Portal를 사용 하면 다음 단계를 수행 하 여 수행할 수 있습니다.

1. Azure Portal에서 가상 네트워크를 선택한 후 왼쪽 메뉴에서 **서브넷** 을 선택 합니다.

1. 서브넷 이름을 선택 하 여 해당 창을 열고 Azure-SSIS IR 노드 번호의 두 배 이상에 사용 가능한 IP 주소가 있는지 확인 합니다.

1. **서비스에 대 한 서브넷 위임** 드롭다운 메뉴에서 *Microsoft Batch/batchaccounts* 를 선택 합니다.

1. **저장** 단추를 선택합니다.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/delegate-subnet-to-batch.png" alt-text="Azure Batch에 서브넷 위임":::

## <a name="grant-virtual-network-permissions"></a><a name="grantperms"></a>가상 네트워크 권한 부여

Azure Portal를 사용 하 여 사용자가 가상 네트워크/서브넷에 가입 하는 데 필요한 RBAC (역할 기반 액세스 제어) 권한을 Azure-SSIS IR 만들 수 있습니다. 이렇게 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 가상 네트워크를 선택한 후 표준 가상 네트워크 주입을 사용 하는 경우 왼쪽 메뉴에서 **액세스 제어 (IAM)** 를 선택할 수 있습니다. Express virtual network 주입을 사용 하는 경우 왼쪽 메뉴에서 **서브넷** 을 선택 하 고, 서브넷 행을 선택한 다음, 위쪽의 메뉴에서 **사용자 관리** 를 선택 하 여 **Access Control** 페이지를 열 수 있습니다.

1. 역할 **할당 추가** 단추를 선택 하 여 **역할 할당 추가** 페이지를 엽니다.

1. **역할** 목록에서 *네트워크 참가자* 또는 사용자 지정 역할을 선택 하 고 **다음** 단추를 선택 합니다.

1. **액세스 권한 할당** 섹션에서 **사용자, 그룹 또는 서비스 사용자** 옵션을 선택 합니다.

1. **멤버 선택** 링크를 선택 하 여 검색 하 고 Azure-SSIS IR 만들 사용자를 선택 합니다.

1. **선택**, **다음** 및 **검토 + 할당** 단추를 선택 합니다.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/grant-virtual-network-permissions.png" alt-text="가상 네트워크 권한 부여":::

## <a name="next-steps"></a>다음 단계

- [Express virtual network 삽입 방법](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [표준 가상 네트워크 삽입 방법](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [ADF UI를 통해 Azure-SSIS IR를 가상 네트워크에 가입](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure PowerShell를 통해 Azure-SSIS IR를 가상 네트워크에 조인](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR에 대한 자세한 내용은 다음 문서를 참조하세요. 

- [Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR 등 IR에 대한 일반적인 개념적 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](tutorial-deploy-ssis-packages-azure.md). 이 자습서에서는 Azure-SSIS IR을 만드는 단계별 지침을 제공합니다. Azure SQL Database server를 사용 하 여 SSISDB를 호스팅합니다. 
- [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 더 자세히 설명합니다. 가상 네트워크를 호스트 SSISDB에 연결 하는 가상 네트워크 서비스 엔드포인트/IP 방화벽 규칙/개인 끝점 또는 Azure SQL Managed Instance를 사용 하 여 구성 된 Azure SQL Database 서버를 사용 하는 방법에 대 한 지침을 제공 합니다. Azure-SSIS IR를 가상 네트워크에 조인 하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure SSIS IR에 대한 정보를 가져와 파악하는 방법을 설명합니다.
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법도 설명합니다.
