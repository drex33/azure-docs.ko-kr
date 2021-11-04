---
title: Azure-SSIS 통합 런타임 삽입을 위한 가상 네트워크 구성
description: Azure-SSIS 통합 런타임 삽입을 위해 가상 네트워크를 구성하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 11/03/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e3683011794e89acbac6bbbb985c7cb85ebd9ebd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475992"
---
# <a name="configure-a-virtual-network-for-injection-of-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 삽입을 위한 가상 네트워크 구성

ADF(Azure Data Factory)에서 SSIS(SQL Server Integration Services)를 사용하는 경우 Azure-SSIS IR(통합 런타임)을 가상 네트워크에 조인하는 두 가지 방법인 표준 및 express가 있습니다. express 메서드는 Azure-SSIS IR 더 빠르게 시작하고 인바운드가 없고 아웃바운드 트래픽 요구 사항이 적지만 표준 메서드에 비해 몇 가지 제한 사항이 있습니다.

## <a name="compare-the-standard-and-express-virtual-network-injection-methods"></a><a name="compare"></a>표준 및 Express 가상 네트워크 삽입 방법 비교

표준 및 Express 가상 네트워크 삽입 방법 간의 차이점을 강조 표시한 표는 다음과 같습니다.

| 비교 | 표준 가상 네트워크 주입 | Express 가상 네트워크 주입 |
|------------|------------------------------------|-----------------------------------|
| **Azure-SSIS IR 시작 기간** | 약 30분. | 약 5분. | 
| **Azure 구독 & 리소스 그룹 설정** | *Microsoft.Batch는* 가상 네트워크 구독에서 리소스 공급자로 등록되어야 합니다.<br/><br/>가상 네트워크 리소스 그룹에서 공용 IP 주소, 부하 분산기 및 NSG(네트워크 보안 그룹)의 생성을 허용해야 합니다. | *Microsoft.Batch는* 가상 네트워크 구독에서 리소스 공급자로 등록되어야 합니다. | 
| **가상 네트워크 서브넷** | 서브넷은 다른 Azure 서비스에 전용으로 사용할 수 없습니다. | 서브넷은 다른 Azure 서비스에 전용으로 사용할 수 없습니다.<br/><br/>서브넷은 *Microsoft.Batch/batchAccounts* 에 위임되어야 합니다. | 
| **가상 네트워크 권한** | Azure-SSIS IR 만드는 _사용자에게는 Microsoft.Network/virtualNetworks/ \* /join_ 권한이 있어야 합니다. | Azure-SSIS IR 만드는 *사용자에게는 Microsoft.Network/virtualNetworks/subnets/join/action* 권한이 있어야 합니다. | 
| **고정 공용 IP 주소** | (선택 사항) Azure-SSIS IR 대한 BYOIP(사용자 고유의 고정 공용 IP 주소)를 가져옵니다. | (선택 사항) Azure-SSIS IR 대한 고정 공용 IP 주소를 설정하도록 가상 네트워크 NAT(네트워크 주소 변환)를 구성합니다. | 
| **사용자 지정 DNS 서버** | 확인되지 않은 DNS 요청을 Azure 재귀 확인자에 전달하는 것이 좋습니다. | 확인되지 않은 DNS 요청을 Azure 재귀 확인자에 전달하는 것이 좋습니다.<br/><br/>Azure-SSIS IR 대한 표준 사용자 지정 설정이 필요합니다. | 
| **인바운드 트래픽** | 포트 *29876, 29877은* *BatchNodeManagement* 서비스 태그가 원본인 TCP 트래픽에 대해 열려 있어야 합니다. | 필수 아님. | 
| **아웃바운드 트래픽** | *AzureCloud* 서비스 태그를 대상으로 하는 TCP 트래픽에 대해 포트 *443이* 열려 있어야 합니다. | *DataFactoryManagement* 서비스 태그를 대상으로 하는 TCP 트래픽에 대해 포트 *443이* 열려 있어야 합니다. | 
| **리소스 잠금** | 리소스 그룹에서 허용되지 않습니다. | 가상 네트워크에서 허용되지 않습니다. | 
| **가상 네트워크당 Azure-SSIS RS** | 무제한. | 하나만. | 

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/standard-express-virtual-network-injection.png" alt-text="표준 및 Express 가상 네트워크 삽입 방법의 스크린샷" lightbox="media/join-azure-ssis-integration-runtime-virtual-network/standard-express-virtual-network-injection.png":::

가상 네트워크는 삽입 방법에 따라 다르게 구성되어야 합니다. Express 메서드를 사용하는 경우 [Express 가상 네트워크 삽입 방법](azure-ssis-integration-runtime-express-virtual-network-injection.md) 문서를 참조하세요. 그렇지 않으면 표준 가상 네트워크 삽입 [방법](azure-ssis-integration-runtime-standard-virtual-network-injection.md) 문서를 참조하세요.
  
## <a name="register-azure-batch-as-a-resource-provider"></a><a name="registerbatch"></a>리소스 공급자로 Azure Batch 등록

Azure Portal 사용하여 ADF의 SSIS에 대한 기본 인프라인 Azure Batch 조인할 Azure-SSIS IR 대한 가상 네트워크가 있는 Azure 구독의 리소스 공급자로 등록할 수 있습니다. 이미 Azure Batch 사용했거나 해당 구독에서 ADF UI를 통해 Azure-SSIS IR 만든 경우 이미 등록되어 있습니다. 그렇지 않으면 다음 단계에 따라 수행할 수 있습니다.

1. Azure Portal 가상 네트워크를 선택한 후 **개요** 페이지에서 강조 표시된 구독을 선택합니다.  

1. 왼쪽 메뉴에서 **리소스 공급자를** 선택합니다.

1. 아직 등록되지 않은 경우 *Microsoft.Batch* 를 선택하고 등록합니다.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="등록된 &quot; 상태 &quot; 확인":::

목록에 *Microsoft.Batch가* 표시되지 않으면 해당 구독에서 [빈 Azure Batch 계정을 만들어](../batch/batch-account-create-portal.md) 나중에 삭제할 수 있습니다. 

## <a name="delegate-a-subnet-to-azure-batch"></a><a name="delegatesubnet"></a>Azure Batch 서브넷 위임

Express 가상 네트워크 주입을 사용하는 경우 Azure-SSIS IR 삽입할 서브넷을 ADF의 SSIS에 대한 기본 인프라인 Azure Batch 위임해야 합니다. Azure Portal 사용하여 다음 단계를 수행할 수 있습니다.

1. Azure Portal 가상 네트워크를 선택한 후 왼쪽 메뉴에서 **서브넷을** 선택합니다.

1. 서브넷 이름을 선택하여 해당 창을 열고 Azure-SSIS IR 노드 번호의 두 배 이상 사용 가능한 IP 주소가 있는지 확인합니다.

1. **서비스에 서브넷 위임** 드롭다운 메뉴에서 *Microsoft.Batch/batchAccounts를* 선택합니다.

1. **저장** 단추를 선택합니다.

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/delegate-subnet-to-batch.png" alt-text="Azure Batch 서브넷 위임":::

## <a name="grant-virtual-network-permissions"></a><a name="grantperms"></a>가상 네트워크 권한 부여

Azure Portal 사용하여 가상 네트워크/서브넷에 조인하는 데 필요한 RBAC(역할 기반 액세스 제어) 권한을 Azure-SSIS IR 만드는 사용자에게 부여할 수 있습니다. 다음 단계에 따라 수행할 수 있습니다.

1. Azure Portal 가상 네트워크를 선택한 후 표준 가상 네트워크 삽입을 사용하는 경우 왼쪽 메뉴에서 **액세스 제어(IAM)를** 선택할 수 있습니다. Express 가상 네트워크 주입을 사용하는 경우 왼쪽 메뉴에서 **서브넷을** 선택하고 서브넷 행을 선택한 다음, 위쪽 메뉴에서 **사용자 관리를** 선택하여 **Access Control** 페이지를 열 수 있습니다.

1. 역할 **할당 추가** 단추를 선택하여 **역할 할당 추가** 페이지를 엽니다.

1. **역할** 목록에서 *네트워크 참가자* 또는 사용자 지정 역할을 선택하고 **다음** 단추를 선택합니다.

1. **액세스 할당** 섹션에서 **사용자, 그룹 또는 서비스 주체** 옵션을 선택합니다.

1. 멤버 **선택** 링크를 선택하여 Azure-SSIS IR 만드는 사용자를 검색하고 선택합니다.

1. 선택 , **다음** 및 **검토 + 할당** 단추를 **선택합니다.**

:::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/grant-virtual-network-permissions.png" alt-text="가상 네트워크 권한 부여":::

## <a name="next-steps"></a>다음 단계

- [Express 가상 네트워크 삽입 방법](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [표준 가상 네트워크 삽입 방법](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [ADF UI를 통해 가상 네트워크에 Azure-SSIS IR 조인](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure PowerShell 통해 가상 네트워크에 Azure-SSIS IR 조인](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR에 대한 자세한 내용은 다음 문서를 참조하세요. 

- [Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR 등 IR에 대한 일반적인 개념적 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](tutorial-deploy-ssis-packages-azure.md). 이 자습서에서는 Azure-SSIS IR을 만드는 단계별 지침을 제공합니다. Azure SQL Database 서버를 사용하여 SSISDB를 호스트합니다. 
- [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 더 자세히 설명합니다. 가상 네트워크 서비스 엔드포인트/IP 방화벽 규칙/프라이빗 엔드포인트로 구성된 Azure SQL Database 서버 또는 가상 네트워크를 조인하여 SSISDB를 호스트하는 Azure SQL Managed Instance 사용하는 방법에 대한 지침을 제공합니다. 가상 네트워크에 Azure-SSIS IR 조인하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure SSIS IR에 대한 정보를 가져와 파악하는 방법을 설명합니다.
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법도 설명합니다.
