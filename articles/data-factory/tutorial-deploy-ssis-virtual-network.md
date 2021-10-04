---
title: Azure-SSIS 통합 런타임을 구성하여 가상 네트워크에 조인하기 위한 자습서
description: Azure-SSIS 통합 런타임을 조인하여 Azure 가상 네트워크에 조인하는 방법에 대해 알아보기.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: a79229537b76dc62bca48985720f9c911de0c68c
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400670"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>가상 네트워크에 조인하기 위한 Azure SQL Server Integration Services(SSIS) IR(통합 런타임) 구성

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 자습서에서는 Azure Portal을 사용하여 가상 네트워크에 조인하기 위해 Azure SQL Server Integration Services(SSIS) IR(통합 런타임)을 구성하는 기본 단계를 제공합니다.

단계는 다음과 같습니다.

- 가상 네트워크 구성
- Azure Data Factory 포털에서 가상 네트워크에 Azure-SSIS IR을 조인합니다.

## <a name="prerequisites"></a>필수 조건

- **Azure-SSIS 통합 런타임**. Azure-SSIS 통합 런타임이 없는 경우 시작하기 전에 [Azure Data Factory에서 Azure-SSIS 통합 런타임을 프로비저닝](tutorial-deploy-ssis-packages-azure.md)합니다.

- **사용자 권한**. Azure-SSIS IR를 만드는 사용자는 아래 옵션 중 하나를 사용하여 적어도 Azure Data Factory 리소스에서 [역할 할당](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)이 있어야 합니다.

    - 기본 제공 네트워크 참가자 역할을 사용합니다. 이 역할에는 범위가 필요 이상으로 큰 _Microsoft.Network/\*_ 권한이 제공됩니다.
    - 필요한 _Microsoft.Network/virtualNetworks/\*/join/action_ 권한만 포함하는 사용자 지정 역할을 만듭니다. 또한 Azure Resource Manager 가상 네트워크에 조인하면서 Azure-SSIS IR의 고유한 퍼블릭 IP 주소를 불러오기 위해서는 역할에 _Microsoft.Network/publicIPAddresses/*/join/action_ 권한을 포함합니다.

- **가상 네트워크**.

    - 가상 네트워크가 없는 경우, [Azure Portal을 사용하여 가상 네트워크를 만듭니다](../virtual-network/quick-create-portal.md).

    - 가상 네트워크의 리소스 그룹이 특정 Azure 네트워크 리소스를 만들고 삭제할 수 있는지 확인합니다.
    
        Azure-SSIS IR은 가상 네트워크와 동일한 리소스 그룹에 특정 네트워크 리소스를 만들어야 합니다. 해당 리소스는 다음과 같습니다.
        - 이름이 *\<Guid>-azurebatch-cloudserviceloadbalancer* 인 Azure Load Balancer
        - 이름이 *\<Guid>-azurebatch-cloudservicenetworksecuritygroup인 네트워크 보안 그룹
        - 이름이 -azurebatch-cloudservicepublicip인 Azure 퍼블릭 IP 주소
    
        해당 리소스는 Azure-SSIS IR이 시작될 때 생성됩니다. Azure-SSIS IR이 중지될 때 삭제됩니다. Azure-SSIS IR 중지가 차단되지 않게 하려면 다른 리소스에서 해당 네트워크 리소스를 다시 사용하지 마세요.

    - 가상 네트워크가 속한 리소스 그룹/구독에 [리소스 잠금](../azure-resource-manager/management/lock-resources.md)이 없는지 확인합니다. 읽기 전용/삭제 잠금을 구성하는 경우 Azure-SSIS IR 시작 및 중지에 실패하거나 응답이 중지됩니다.

    - 가상 네트워크가 속한 리소스 그룹/구독에 다음 리소스를 만들 수 없도록 하는 Azure Policy 할당이 없는지 확인합니다.
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- 아래 **네트워크 구성** 시나리오는 이 자습서에서 다루지 않습니다.
    - Azure-SSIS IR에 고유한 퍼블릭 IP 주소가 필요한 경우
    - 자체 DNS(Domain Name System) 서버를 사용하는 경우.
    - 서브넷에서 NSG(네트워크 보안 그룹)를 사용하는 경우.
    - Azure ExpressRoute 또는 UDR(사용자 정의 경로)을 사용하는 경우.
    - 사용자 지정 Azure-SSIS IR을 사용하는 경우
    
    자세한 내용은 [가상 네트워크 구성](azure-ssis-integration-runtime-virtual-network-configuration.md)을 참조하세요.

## <a name="configure-a-virtual-network"></a>가상 네트워크 구성

Azure Portal을 사용하여 Azure-SSIS IR에 조인하기 전에 가상 네트워크를 구성합니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 두 가지 웹 브라우저에서만 Data Factory UI가 지원됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **추가 서비스** 를 선택합니다. **가상 네트워크** 를 필터링하여 선택합니다.

1. 목록에서 가상 네트워크를 필터링하고 선택합니다.

1. **가상 네트워크** 페이지에서 **속성** 을 선택합니다.

1. **리소스 ID** 에 대한 복사 단추를 선택하여 가상 네트워크에 대한 리소스 ID를 클립보드에 복사합니다. 클립 보드의 ID를 OneNote 또는 파일에 저장합니다.

1. 왼쪽 메뉴에서 **서브넷** 을 선택합니다.

    - 선택한 서브넷에 Azure-SSIS IR을 사용할 수 있는 충분한 주소 공간이 있는지 확인합니다. 사용 가능한 IP 주소를 IR 노드 수의 두 배 이상 남겨 둡니다. Azure는 각 서브넷 내의 일부 IP 주소를 예약합니다. 이와 같은 주소는 사용할 수 없습니다. 서브넷의 첫 번째와 마지막 IP 주소는 프로토콜 규칙에 예약되어 있으며, 추가로 3개의 주소가 Azure 서비스에 사용됩니다. 자세한 내용은 [이러한 서브넷 내에서 IP 주소를 사용하는데 제한 사항이 있습니까?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Azure-SSIS IR을 배포하려면 GatewaySubnet을 선택하지 마세요. 가상 네트워크 게이트웨이 전용입니다.
    - 다른 Azure 서비스(예: SQL Database Managed Instance, App Service 등)에서 독점 사용되는 서브넷은 사용하지 않습니다.

1. 가상 네트워크가 있는 Azure 구독에 Azure Batch 공급자가 등록되었는지 확인합니다. 또는 Azure Batch 공급자를 등록합니다. 구독에 Azure Batch 계정이 이미 있는 경우에는 Azure Batch에 대한 구독이 등록됩니다. (Data Factory 포털에 Azure-SSIS IR을 만들면 Azure Batch 공급자가 자동으로 등록됩니다.)

   1. Azure Portal의 왼쪽 메뉴에서 **구독** 을 선택합니다.

   1. 구독을 선택합니다.

   1. 왼쪽에서 **리소스 공급자** 를 선택하고 **Microsoft.Batch** 가 등록된 공급자인지 확인합니다.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="등록된 &quot; 상태 &quot; 확인":::

   목록에서 **Microsoft.Batch** 가 보이지 않으면 지금 등록할 수 있도록 구독에서 [빈 Azure Batch 계정을 만듭니다](../batch/batch-account-create-portal.md). 나중에 삭제할 수 있습니다.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Azure-SSIS IR을 가상 네트워크에 조인

Azure Resource Manager 가상 네트워크 또는 클래식 가상 네트워크를 구성한 후 가상 네트워크에 Azure-SSIS IR을 조인할 수 있습니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 두 가지 웹 브라우저에서만 Data Factory UI가 지원됩니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **데이터 팩터리** 를 선택합니다. 메뉴에 **데이터 팩터리** 가 표시되지 않으면 **다른 서비스** 를 선택한 뒤 **INTELLIGENCE + ANALYTICS** 섹션에서 **데이터 팩터리** 를 선택합니다.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png" alt-text="데이터 팩터리 목록":::

1. 목록에서 Azure-SSIS IR이 있는 데이터 팩터리를 선택합니다. 데이터 팩터리의 홈 페이지가 표시됩니다. **작성자 & 모니터** 타일을 선택합니다. 별도의 탭에 Data Factory UI가 표시됩니다.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png" alt-text="데이터 팩터리 홈페이지":::

1. 데이터 팩터리 UI에서 **편집** 탭으로 전환하고 **연결** 을 선택한 다음, **통합 런타임** 탭으로 전환합니다.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png" alt-text="&quot;통합 런타임 &quot; 탭":::

1. Azure-SSIS IR이 실행 중이면 **통합 런타임** 목록의 **동작** 열에서 Azure-SSIS IR의 **중지** 단추를 선택합니다. Azure-SSIS IR을 중지하지 않으면 편집할 수 없습니다.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png" alt-text="IR 중지":::

1. **통합 런타임** 목록의 **작업** 열에서 Azure-SSIS IR의 **편집** 단추를 선택합니다.

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png" alt-text="통합 런타임 편집":::

1. 통합 런타임 설정 패널에서 **다음** 단추를 선택하여 **일반 설정** 및 **SQL 설정** 섹션을 진행합니다.

1. **고급 설정** 섹션에서 다음을 수행합니다.
   1. **조인할 Azure-SSIS 통합 런타임의 VNet을 선택하고, ADF가 특정 네트워크 리소스를 만들도록 허용하고, 원하는 경우 자체 고정 공용 IP 주소 가져오기** 확인란을 선택합니다.

   1. **구독** 에 대해서는 가상 네트워크가 있는 Azure 구독을 선택합니다.

   1. **위치** 에는 통합 런타임의 동일한 위치가 선택됩니다.

   1. **유형** 에서는 클래식 가상 네트워크 또는 Azure Resource Manager 가상 네트워크 유형을 선택합니다. 클래식 가상 네트워크는 곧 사용되지 않으므로 Azure Resource Manager 가상 네트워크를 선택하는 것이 좋습니다.

   1. **VNet 이름** 에서 가상 네트워크의 이름을 선택합니다. 가상 네트워크 서비스 엔드포인트가 있는 SQL Database나 SSISDB를 호스트하기 위한 프라이빗 엔드포인트가 있는 SQL Managed Instance에 사용되는 것과 동일해야 합니다. 또는 온-프레미스 네트워크에 연결된 것과 동일해야 합니다. 그렇지 않으면 Azure-SSIS IR에 자체 정적 공용 IP를 가져오는 임의의 가상 네트워크일 수 있습니다.

   1. **서브넷 이름** 에서 가상 네트워크의 서브넷 이름을 선택합니다. 가상 네트워크 서비스 엔드포인트를 사용하여 SSISDB를 호스트하는 SQL Database에 사용되는 것과 동일해야 합니다. 또는 SQL Managed Instance에 사용된 것과 다른 서브넷이어야 합니다. 이 서브넷은 SSISDB를 호스트하기 위해 프라이빗 엔드포인트를 사용합니다. 그렇지 않은 경우에는 Azure-SSIS IR에 대한 고정 퍼블릭 IP 주소를 가져오는 서브넷이 될 수 있습니다.

   1. **VNet 유효성 검사** 를 선택합니다. 유효성 검사에 성공하면 **계속** 을 선택합니다.

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="가상 네트워크 관련 고급 설정":::

1. **요약** 섹션에서 Azure-SSIS IR에 대한 모든 설정을 검토합니다. 그런 다음 **업데이트** 를 선택합니다.

1. Azure-SSIS IR의 **작업** 열에서 **시작** 단추를 선택하여 Azure-SSIS IR을 시작합니다. Azure-SSIS IR이 가상 네트워크에 조인을 시작하는 데 약 20~30분이 소요됩니다.

## <a name="next-steps"></a>다음 단계

[가상 네트워크에 Azure-SSIS IR을 조인](join-azure-ssis-integration-runtime-virtual-network.md)하는 방법에 대한 자세한 정보.