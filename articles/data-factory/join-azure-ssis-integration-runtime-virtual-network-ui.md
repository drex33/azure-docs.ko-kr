---
title: Azure Portal 통해 Azure-SSIS 통합 런타임을 가상 네트워크에 조인
description: Azure Portal 통해 Azure-SSIS 통합 런타임을 가상 네트워크에 조인하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 15b2a1bbac1d89c74a2fdea2f5ce3af51261b29e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087897"
---
# <a name="join-azure-ssis-integration-runtime-to-a-virtual-network-via-azure-portal"></a>Azure Portal 통해 Azure-SSIS 통합 런타임을 가상 네트워크에 조인

이 문서에서는 Azure Portal/ADF UI를 통해 ADF(Azure Data Factory)의 기존 SSIS(Azure-SQL Server Integration Services) IR(통합 런타임)을 가상 네트워크에 조인하는 방법을 보여 줍니다. 

Azure-SSIS IR 가상 네트워크에 조인하기 전에 먼저 가상 네트워크를 제대로 구성해야 합니다. [Azure-SSIS IR 삽입하도록 가상 네트워크 구성](azure-ssis-integration-runtime-virtual-network-configuration.md) 문서를 참조하세요. 다음으로, 가상 네트워크(Azure Resource Manager/클래식)의 유형에 적용되는 아래 섹션의 단계를 따릅니다. 마지막으로, 마지막 섹션의 단계에 따라 Azure-SSIS IR 가상 네트워크에 조인합니다. 

## <a name="configure-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager 가상 네트워크 구성

Azure-SSIS IR 연결하기 전에 Azure Portal 사용하여 Azure Resource Manager 가상 네트워크를 구성합니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 ADF UI를 지원합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. **추가 서비스** 를 선택합니다. **가상 네트워크** 를 필터링하여 선택합니다. 

1. 목록에서 가상 네트워크를 필터링하고 선택합니다. 

1. 왼쪽 메뉴에서 **서브넷을 선택합니다.**

   - Azure-SSIS IR 조인할 적절한 서브넷이 있는지 확인합니다. [서브넷 선택 섹션을](azure-ssis-integration-runtime-standard-virtual-network-injection.md#subnet) 참조하세요.

   - Express 가상 네트워크 삽입 방법을 사용하는 경우 선택한 서브넷이 Azure Batch 위임되었는지 확인합니다. [Azure Batch 서브넷 위임 섹션을](azure-ssis-integration-runtime-virtual-network-configuration.md#delegatesubnet) 참조하세요.

1. *Microsoft.Batch가* Azure-SSIS IR 조인할 가상 네트워크가 있는 Azure 구독의 등록된 리소스 공급자인지 확인합니다. 자세한 지침은 [리소스 공급자로 Azure Batch 등록 섹션을](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch) 참조하세요.

## <a name="configure-a-classic-virtual-network"></a>클래식 가상 네트워크 구성

Azure-SSIS IR 연결하기 전에 Azure Portal 사용하여 클래식 가상 네트워크를 구성합니다. 

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 ADF UI를 지원합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. **추가 서비스** 를 선택합니다. **가상 네트워크(클래식)** 를 필터링하여 선택합니다. 

1. 목록에서 가상 네트워크를 필터링하고 선택합니다. 

1. **가상 네트워크(클래식)** 페이지에서 **속성** 을 선택합니다. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png" alt-text="클래식 가상 네트워크 리소스 ID":::

1. **리소스 ID에** 대한 복사 단추를 선택하여 클래식 가상 네트워크의 리소스 ID를 클립보드에 복사합니다. 클립 보드의 ID를 OneNote 또는 파일에 저장합니다. 

1. 왼쪽 메뉴에서 **서브넷을** 선택합니다. 선택한 서브넷에서 사용 가능한 IP 주소 수가 Azure-SSIS IR 노드 수의 두 배보다 큰지 확인합니다. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png" alt-text="가상 네트워크에서 사용 가능한 주소 수":::

1. **MicrosoftAzureBatch** 를 가상 네트워크의 **클래식 가상 머신 참가자** 역할에 조인합니다. 

   1. 왼쪽 메뉴에서 **액세스 제어(IAM)** 를 선택하고 역할 할당 **탭을** 선택합니다. 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png" alt-text="&quot;액세스 제어 &quot; 및 &quot; 추가 &quot; 단추":::

   1. **역할 할당 추가** 를 선택합니다.

   1. **역할 할당 추가** 페이지에서 **역할** 로 **클래식 가상 머신 기여자** 를 선택합니다. **선택** 상자에 **ddbf3205-c6bd-46ae-8127-60eb93363864** 를 붙여넣은 다음, 검색 결과 목록에서 **MicrosoftAzureBatch를** 선택합니다. 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png" alt-text="역할 할당 &quot; 추가 &quot; 페이지의 검색 결과":::

   1. **저장** 을 선택하여 설정을 저장하고 페이지를 닫습니다. 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png" alt-text="액세스 설정 저장":::

   1. 참여자 목록에 **MicrosoftAzureBatch** 가 보이는지 확인합니다. 

      :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png" alt-text="Azure Batch 액세스 확인":::

1. *Microsoft.Batch가* Azure-SSIS IR 조인할 가상 네트워크가 있는 Azure 구독의 등록된 리소스 공급자인지 확인합니다. 자세한 지침은 [리소스 공급자로 Azure Batch 등록 섹션을](azure-ssis-integration-runtime-virtual-network-configuration.md#registerbatch) 참조하세요.

## <a name="join-azure-ssis-ir-to-the-virtual-network"></a>가상 네트워크에 Azure-SSIS IR 조인

Azure Resource Manager/클래식 가상 네트워크를 구성한 후 Azure-SSIS IR 가상 네트워크에 조인할 수 있습니다.

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

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="가상 네트워크 관련 고급 설정":::

   1. **조인할 Azure-SSIS 통합 런타임의 VNet을 선택하고, ADF가 특정 네트워크 리소스를 만들도록 허용하고, 원하는 경우 자체 고정 공용 IP 주소 가져오기** 확인란을 선택합니다. 

   1. **구독** 에 대해서는 가상 네트워크가 있는 Azure 구독을 선택합니다.

   1. **위치** 에는 통합 런타임의 동일한 위치가 선택됩니다.

   1. **형식에서** 가상 네트워크의 **유형(Azure Resource Manager Virtual Network/클래식** 가상 네트워크)을 선택합니다. 클래식 가상 네트워크는 곧 더 이상 사용되지 **않으므로 Azure Resource Manager Virtual Network** 선택하는 것이 좋습니다.

   1. **VNet 이름** 에서 가상 네트워크의 이름을 선택합니다. SSISDB를 호스트하는 Azure SQL Database 서버에 대한 가상 네트워크 서비스 엔드포인트/프라이빗 엔드포인트를 구성하는 데 사용되는 것과 동일해야 합니다. 또는 SSISDB를 호스트하는 Azure SQL Managed Instance 조인한 것과 동일해야 합니다. 또는 온-프레미스 네트워크에 연결된 것과 동일해야 합니다. 그렇지 않으면 Azure-SSIS IR에 자체 정적 공용 IP를 가져오는 임의의 가상 네트워크일 수 있습니다.

   1. **서브넷 이름** 에서 가상 네트워크의 서브넷 이름을 선택합니다. SSISDB를 호스트하는 Azure SQL Database 서버에 대한 가상 네트워크 서비스 엔드포인트를 구성하는 데 사용되는 것과 동일해야 합니다. 또는 SSISDB를 호스트하는 Azure SQL Managed Instance 조인한 서브넷과 다른 서브넷이어야 합니다. 그렇지 않은 경우에는 Azure-SSIS IR의 자체 고정 공용 IP를 가져오는 임의의 서브넷이 될 수 있습니다.

   1. **VNet 삽입 방법** 의 경우 가상 네트워크 삽입 방법 **Express** / **Standard** 를 선택합니다. 
   
      이러한 방법을 비교하려면 [표준 및 Express 가상 네트워크 삽입 방법 비교](azure-ssis-integration-runtime-virtual-network-configuration.md#compare) 문서를 참조하세요. 
   
      **Express를** 선택하는 경우 [Express 가상 네트워크 삽입 방법](azure-ssis-integration-runtime-express-virtual-network-injection.md) 문서를 참조하세요. 
      
      **표준을** 선택하는 경우 [표준 가상 네트워크 삽입 방법](azure-ssis-integration-runtime-standard-virtual-network-injection.md) 문서를 참조하세요.  이 메서드를 사용하여 다음을 수행할 수도 있습니다.

      1. Azure-SSIS Integration Runtime **고정 공용 IP 주소** 가져오기 확인란을 선택하여 데이터 저장소의 방화벽에서 허용할 수 있도록 Azure-SSIS IR 고유한 고정 공용 IP 주소를 가져올지 여부를 선택합니다.

         확인란을 선택하는 경우 다음 단계를 완료하세요.

         1. **첫 번째 고정 공용 IP 주소** 는 Azure-SSIS IR [요구 사항을 충족](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip)하는 첫 번째 고정 공용 IP를 선택합니다. 없는 경우 **새로 만들기** 링크를 클릭하여 Azure Portal 고정 공용 IP 주소를 만든 다음 여기에서 새로 고침 단추를 클릭하여 선택할 수 있습니다.
      
         1. **두 번째 고정 공용 IP** 는 Azure-SSIS IR [요구 사항을 충족](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip)하는 두 번째 고정 공용 IP 주소를 선택합니다. 없는 경우 **새로 만들기** 링크를 클릭하여 Azure Portal 고정 공용 IP 주소를 만든 다음 여기에서 새로 고침 단추를 클릭하여 선택할 수 있습니다.

   1. **VNet 유효성 검사** 를 선택합니다. 유효성 검사에 성공하면 **계속** 을 선택합니다. 

1. **요약** 페이지에서 Azure-SSIS IR 대한 모든 설정을 검토한 다음, **업데이트를** 선택합니다.

1. Azure-SSIS IR **작업** 열에서 시작 단추를 선택하여 Azure-SSIS IR **시작합니다.** 가상 네트워크에 각각 express/standard 삽입 방법을 조인하는 Azure-SSIS IR 시작하는 데 약 5/20-30분이 걸립니다. 

## <a name="next-steps"></a>다음 단계

- [Azure-SSIS IR를 삽입 하도록 가상 네트워크 구성](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Express virtual network 삽입 방법](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [표준 가상 네트워크 삽입 방법](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [Azure PowerShell를 통해 Azure-SSIS IR를 가상 네트워크에 조인](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR에 대한 자세한 내용은 다음 문서를 참조하세요. 

- [Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR 등 IR에 대한 일반적인 개념적 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](tutorial-deploy-ssis-packages-azure.md). 이 자습서에서는 Azure-SSIS IR을 만드는 단계별 지침을 제공합니다. Azure SQL Database server를 사용 하 여 SSISDB를 호스팅합니다. 
- [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 더 자세히 설명합니다. 가상 네트워크를 호스트 SSISDB에 연결 하는 가상 네트워크 서비스 엔드포인트/IP 방화벽 규칙/개인 끝점 또는 Azure SQL Managed Instance를 사용 하 여 구성 된 Azure SQL Database 서버를 사용 하는 방법에 대 한 지침을 제공 합니다. Azure-SSIS IR를 가상 네트워크에 조인 하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure SSIS IR에 대한 정보를 가져와 파악하는 방법을 설명합니다.
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법도 설명합니다.
