---
title: 가상 네트워크에 조인하도록 Azure-SSIS 통합 런타임을 구성하는 자습서
description: 가상 네트워크에 조인하도록 Azure-SSIS 통합 런타임을 구성하는 방법을 알아봅니다.
author: swinarko
ms.author: sawinark
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
ms.openlocfilehash: 8ee28e9c310002c0a0b2e29a77b35c71045c9fc4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040867"
---
# <a name="configure-azure-ssis-integration-runtime-to-join-a-virtual-network"></a>가상 네트워크에 조인하도록 Azure-SSIS 통합 런타임 구성

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 자습서에서는 ADF(Azure Portal/Azure Data Factory) UI를 통해 최소 단계를 제공하여 SSIS(Azure-SQL Server Integration Services) IR(통합 런타임)을 구성하여 가상 네트워크에 Express 삽입 방법을 조인하도록 구성합니다.  단계는 다음과 같습니다.

1. Azure Portal 통해 Express 삽입 방법에 대한 가상 네트워크를 구성합니다.
1. ADF UI를 통해 express 삽입 방법을 사용하여 가상 네트워크에 Azure-SSIS IR 조인

## <a name="prerequisites"></a>사전 요구 사항

- **Azure-SSIS IR:** 없는 경우 먼저 [ADF UI를 통해 Azure-SSIS IR 만듭니다.](tutorial-deploy-ssis-packages-azure.md)

- **가상 네트워크:** 가상 네트워크가 없는 경우 먼저 [Azure Portal 통해 가상 네트워크를 만듭니다.](../virtual-network/quick-create-portal.md) 다음을 확인합니다.
  - 가상 네트워크에 리소스 잠금이 없습니다.
  - Azure-SSIS IR 만드는 사용자에게 가상 네트워크/서브넷에 조인하는 데 필요한 RBAC(역할 기반 액세스 제어) [권한이 부여됩니다. 가상 네트워크 권한 선택 섹션을 참조하세요.](azure-ssis-integration-runtime-express-virtual-network-injection.md#perms)

다음 가상 네트워크 구성은 이 자습서에서 다루지 않습니다.

- Azure-SSIS IR 고정 공용 IP 주소를 사용하는 경우
- 사용자 고유의 DNS(도메인 이름 시스템) 서버를 사용하는 경우
- NSG(네트워크 보안 그룹)를 사용하는 경우
- UDR(사용자 정의 경로)을 사용하는 경우

이러한 선택적 단계를 수행하려면 [Express 가상 네트워크 삽입 방법](azure-ssis-integration-runtime-express-virtual-network-injection.md) 문서를 참조하세요.

## <a name="configure-a-virtual-network"></a>가상 네트워크 구성

Azure-SSIS IR 연결하기 전에 Azure Portal 사용하여 가상 네트워크를 구성합니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 ADF UI를 지원합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **추가 서비스** 를 선택합니다. **가상 네트워크** 를 필터링하여 선택합니다.

1. 목록에서 가상 네트워크를 필터링하고 선택합니다.

1. 왼쪽 메뉴에서 **서브넷을 선택합니다.**

   - Azure-SSIS IR 조인할 적절한 서브넷이 있는지 확인합니다. [서브넷 선택 섹션을](azure-ssis-integration-runtime-express-virtual-network-injection.md#subnet) 참조하세요.

   - 선택한 서브넷이 Azure Batch 위임되었는지 확인합니다. Azure Batch [서브넷 위임 섹션을](azure-ssis-integration-runtime-virtual-network-configuration.md#delegatesubnet) 참조하세요.

1. *Microsoft.Batch가* Azure-SSIS IR 조인할 가상 네트워크가 있는 Azure 구독에 등록된 리소스 공급자인지 확인합니다. 자세한 지침은 [리소스 공급자로 Azure Batch 등록 섹션을](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch) 참조하세요.

## <a name="join-azure-ssis-ir-to-the-virtual-network"></a>가상 네트워크에 Azure-SSIS IR 조인

가상 네트워크를 구성한 후에는 Azure-SSIS IR 가상 네트워크에 조인할 수 있습니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 ADF UI를 지원합니다.

1. [Azure Portal](https://portal.azure.com)왼쪽 메뉴에서 **데이터 팩터리** 를 선택합니다. 메뉴에 **데이터 팩터리** 가 표시되지 않으면 **다른 서비스** 를 선택한 뒤 **INTELLIGENCE + ANALYTICS** 섹션에서 **데이터 팩터리** 를 선택합니다.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png" alt-text="데이터 팩터리 목록":::

1. 목록에서 Azure-SSIS IR ADF를 선택합니다. ADF의 홈페이지가 표시됩니다. **작성자 & 모니터** 타일을 선택합니다. 별도의 탭에 ADF UI가 표시됩니다.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png" alt-text="데이터 팩터리 홈페이지":::

1. ADF UI에서 **편집** 탭으로 전환하고, **연결을** 선택하고, 통합 런타임 탭으로 **전환합니다.**

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png" alt-text="&quot;통합 런타임 &quot; 탭":::

1. Azure-SSIS IR이 실행 중이면 **통합 런타임** 목록의 **동작** 열에서 Azure-SSIS IR의 **중지** 단추를 선택합니다. Azure-SSIS IR을 중지하지 않으면 편집할 수 없습니다.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png" alt-text="IR 중지":::

1. **통합 런타임** 목록의 **작업** 열에서 Azure-SSIS IR의 **편집** 단추를 선택합니다.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png" alt-text="통합 런타임 편집":::

1. 통합 **런타임 설정** 창에서 **다음** 단추를 선택하여 **일반 설정** 및 배포 **설정** 페이지를 진행합니다.

1. 고급 **설정** 페이지에서 다음 단계를 완료합니다.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet-express.png" alt-text="Express 가상 네트워크 주입에 대한 고급 설정":::

   1. **조인할 Azure-SSIS 통합 런타임의 VNet을 선택하고, ADF가 특정 네트워크 리소스를 만들도록 허용하고, 원하는 경우 자체 고정 공용 IP 주소 가져오기** 확인란을 선택합니다.

   1. **구독** 에 대해서는 가상 네트워크가 있는 Azure 구독을 선택합니다.

   1. **위치** 에는 통합 런타임의 동일한 위치가 선택됩니다.

   1. **형식에서** 가상 네트워크의 **유형(Azure Resource Manager Virtual Network/클래식** 가상 네트워크)을 선택합니다. 클래식 가상 네트워크는 곧 더 이상 사용되지 **않으므로 Azure Resource Manager Virtual Network** 선택하는 것이 좋습니다.

   1. **VNet 이름** 에서 가상 네트워크의 이름을 선택합니다. SSISDB를 호스트하는 Azure SQL Database 서버에 대한 가상 네트워크 서비스 엔드포인트/프라이빗 엔드포인트를 구성하는 데 사용되는 것과 동일해야 합니다. 또는 SSISDB를 호스트하는 Azure SQL Managed Instance 조인한 것과 동일해야 합니다. 또는 온-프레미스 네트워크에 연결된 것과 동일해야 합니다.

   1. **서브넷 이름** 에서 가상 네트워크의 서브넷 이름을 선택합니다. SSISDB를 호스트하는 Azure SQL Database 서버에 대한 가상 네트워크 서비스 엔드포인트를 구성하는 데 사용되는 것과 동일해야 합니다. 또는 SSISDB를 호스트하는 Azure SQL Managed Instance 조인한 서브넷과 다른 서브넷이어야 합니다.

   1. **VNet 삽입 방법** 의 경우 Express 가상 네트워크 삽입에 대해 **Express를** 선택합니다.

   1. **VNet 유효성 검사** 를 선택합니다. 유효성 검사에 성공하면 **계속** 을 선택합니다.

1. **요약** 페이지에서 Azure-SSIS IR 대한 모든 설정을 검토한 다음, **업데이트를** 선택합니다.

1. Azure-SSIS IR **작업** 열에서 시작 단추를 선택하여 Azure-SSIS IR **시작합니다.** 가상 네트워크를 Express 삽입 방법으로 조인하는 Azure-SSIS IR 시작하는 데 약 5분이 걸립니다.

## <a name="next-steps"></a>다음 단계

- [Azure-SSIS IR 삽입하도록 가상 네트워크 구성](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Express 가상 네트워크 삽입 방법](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [ADF UI를 통해 가상 네트워크에 Azure-SSIS IR 조인](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure PowerShell 통해 가상 네트워크에 Azure-SSIS IR 조인](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR에 대한 자세한 내용은 다음 문서를 참조하세요. 

- [Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR 등 IR에 대한 일반적인 개념적 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](tutorial-deploy-ssis-packages-azure.md). 이 자습서에서는 Azure-SSIS IR을 만드는 단계별 지침을 제공합니다. Azure SQL Database 서버를 사용하여 SSISDB를 호스트합니다. 
- [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 더 자세히 설명합니다. 가상 네트워크 서비스 엔드포인트/IP 방화벽 규칙/프라이빗 엔드포인트로 구성된 Azure SQL Database 서버 또는 가상 네트워크를 조인하여 SSISDB를 호스트하는 Azure SQL Managed Instance 사용하는 방법에 대한 지침을 제공합니다. 가상 네트워크에 Azure-SSIS IR 조인하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure SSIS IR에 대한 정보를 가져와 파악하는 방법을 설명합니다.
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법도 설명합니다.
