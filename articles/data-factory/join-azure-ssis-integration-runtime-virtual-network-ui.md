---
title: UI를 사용하여 Azure-SSIS 통합 런타임을 가상 네트워크에 조인
description: Azure Data Factory Studio UI를 사용하여 Azure-SSIS 통합 런타임을 Azure 가상 네트워크에 조인하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d02a5d1025e470d8751d87816cbcc6395be41593
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129403384"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network-with-azure-data-factory-studio-ui"></a>Azure Data Factory Studio UI를 사용하여 가상 네트워크에 Azure-SSIS 통합 런타임 조인

이 섹션에서는 Azure Portal 및 Azure Data Factory Studio UI를 사용하여 기존 Azure-SSIS IR 가상 네트워크(클래식 또는 Azure Resource Manager)에 조인하는 방법을 보여줍니다. 

Azure-SSIS IR을 가상 네트워크에 조인하기 전에 가상 네트워크를 올바르게 구성해야 합니다. 가상 네트워크의 유형(클래식 또는 Azure Resource Manager)에 해당하는 섹션의 단계를 따릅니다. 그런 다음 세 번째 섹션에 따라 Azure SSIS IR을 가상 네트워크에 조인합니다. 

## <a name="configure-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager 가상 네트워크 구성

Azure-SSIS IR을 조인하기 전에 포털을 사용하여 Azure Resource Manager 가상 네트워크를 구성합니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 두 가지 웹 브라우저에서만 Data Factory UI가 지원됩니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. **추가 서비스** 를 선택합니다. **가상 네트워크** 를 필터링하여 선택합니다. 

1. 목록에서 가상 네트워크를 필터링하고 선택합니다. 

1. **가상 네트워크** 페이지에서 **속성** 을 선택합니다. 

1. **리소스 ID** 에 대한 복사 단추를 선택하여 가상 네트워크에 대한 리소스 ID를 클립보드에 복사합니다. 클립 보드의 ID를 OneNote 또는 파일에 저장합니다. 

1. 왼쪽 메뉴에서 **서브넷** 을 선택합니다. 사용 가능한 주소 수가 Azure-SSIS IR의 노드보다 많은지 확인합니다. 

1. 가상 네트워크가 있는 Azure 구독에 Azure Batch 공급자가 등록되었는지 확인합니다. 또는 Azure Batch 공급자를 등록합니다. 구독에 Azure Batch 계정이 이미 있는 경우에는 Azure Batch에 대한 구독이 등록됩니다. (Data Factory 포털에 Azure-SSIS IR을 만들면 Azure Batch 공급자가 자동으로 등록됩니다.) 

   1. Azure Portal의 왼쪽 메뉴에서 **구독** 을 선택합니다. 

   1. 구독을 선택합니다. 

   1. 왼쪽에서 **리소스 공급자** 를 선택하고 **Microsoft.Batch** 가 등록된 공급자인지 확인합니다. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png" alt-text="등록된 &quot; 상태 &quot; 확인":::

   목록에서 **Microsoft.Batch** 가 보이지 않으면 지금 등록할 수 있도록 구독에서 [빈 Azure Batch 계정을 만듭니다](../batch/batch-account-create-portal.md). 나중에 삭제할 수 있습니다. 

## <a name="configure-a-classic-virtual-network"></a>클래식 가상 네트워크 구성

Azure-SSIS IR을 조인하기 전에 포털을 사용하여 클래식 가상 네트워크를 구성합니다. 

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 두 가지 웹 브라우저에서만 Data Factory UI가 지원됩니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. **추가 서비스** 를 선택합니다. **가상 네트워크(클래식)** 를 필터링하여 선택합니다. 

1. 목록에서 가상 네트워크를 필터링하고 선택합니다. 

1. **가상 네트워크(클래식)** 페이지에서 **속성** 을 선택합니다. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png" alt-text="클래식 가상 네트워크 리소스 ID":::

1. **RESOURCE ID** 에 대한 복사 단추를 선택하여 클래식 네트워크의 리소스 ID를 클립보드에 복사합니다. 클립 보드의 ID를 OneNote 또는 파일에 저장합니다. 

1. 왼쪽 메뉴에서 **서브넷** 을 선택합니다. 사용 가능한 주소 수가 Azure-SSIS IR의 노드보다 많은지 확인합니다. 

   :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png" alt-text="가상 네트워크에서 사용 가능한 주소 수":::

1. **MicrosoftAzureBatch** 를 가상 네트워크의 **클래식 가상 머신 참가자** 역할에 조인합니다. 

   1. 왼쪽 메뉴에서 **액세스 제어(IAM)** 를 선택하고 **역할 할당** 탭을 선택합니다. 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png" alt-text="&quot;액세스 제어 &quot; 및 &quot; 추가 &quot; 단추":::

   1. **역할 할당 추가** 를 선택합니다.

   1. **역할 할당 추가** 페이지에서 **역할** 로 **클래식 가상 머신 기여자** 를 선택합니다. **선택** 상자에 **ddbf3205-c6bd-46ae-8127-60eb93363864** 를 붙여넣고 검색 결과 목록에서 **Microsoft Azure Batch** 를 선택합니다. 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png" alt-text="역할 할당 &quot; 추가 &quot; 페이지의 검색 결과":::

   1. **저장** 을 선택하여 설정을 저장하고 페이지를 닫습니다. 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png" alt-text="액세스 설정 저장":::

   1. 참가자 목록에 **Microsoft Azure Batch** 가 보이는지 확인합니다. 

       :::image type="content" source="media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png" alt-text="Azure Batch 액세스 확인":::

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

   1. **서브넷 이름** 에서 가상 네트워크의 서브넷 이름을 선택합니다. 가상 네트워크 서비스 엔드포인트를 사용하여 SSISDB를 호스트하는 SQL Database에 사용되는 것과 동일해야 합니다. 또는 SSISDB를 호스트하기 위한 프라이빗 엔드포인트가 있는 SQL Managed Instance에 사용된 것과 다른 서브넷이어야 합니다. 그렇지 않은 경우에는 Azure-SSIS IR의 자체 고정 공용 IP를 가져오는 임의의 서브넷이 될 수 있습니다.

   1. **Azure-SSIS Integration Runtime용 고정 공용 IP 가져오기** 확인란을 선택하여 Azure-SSIS IR용 고정 공용 IP 주소를 만들지 여부를 선택합니다. 그래야만 데이터 원본용 방화벽에서 허용할 수 있습니다.

      확인란을 선택하는 경우 다음 단계를 완료하세요.

      1. **첫 번째 고정 공용 IP 주소** 는 Azure-SSIS IR [요구 사항을 충족](azure-ssis-integration-runtime-virtual-network-configuration.md#publicIP)하는 첫 번째 고정 공용 IP를 선택합니다. 주소가 없으면 **새 링크 만들기** 를 클릭하여 Azure Portal에서 고정 공용 IP를 만든 다음 새로 고침 단추를 클릭하면 선택할 수 있습니다.
      
      1. **두 번째 고정 공용 IP** 는 Azure-SSIS IR [요구 사항을 충족](azure-ssis-integration-runtime-virtual-network-configuration.md#publicIP)하는 두 번째 고정 공용 IP 주소를 선택합니다. 주소가 없으면 **새 링크 만들기** 를 클릭하여 Azure Portal에서 고정 공용 IP를 만든 다음 새로 고침 단추를 클릭하면 선택할 수 있습니다.

   1. **VNet 유효성 검사** 를 선택합니다. 유효성 검사에 성공하면 **계속** 을 선택합니다. 

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="가상 네트워크 관련 고급 설정":::

1. **요약** 섹션에서 Azure-SSIS IR에 대한 모든 설정을 검토합니다. 그런 다음 **업데이트** 를 선택합니다.

1. Azure-SSIS IR의 **작업** 열에서 **시작** 단추를 선택하여 Azure-SSIS IR을 시작합니다. 가상 네트워크에 조인하는 Azure-SSIS IR이 시작되는 데 약 20~30분이 소요됩니다. 

## <a name="next-steps"></a>다음 단계
- [가상 네트워크에 Azure-SSIS 통합 런타임 조인 - 개요](join-azure-ssis-integration-runtime-virtual-network.md)
- [Azure-SSIS 통합 런타임 가상 네트워크 구성 세부 정보](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Azure PowerShell 사용하여 가상 네트워크에 Azure-SSIS 통합 런타임 조인](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR에 대한 자세한 내용은 다음 문서를 참조하세요. 
- [Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR 등 IR에 대한 일반적인 개념적 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](./tutorial-deploy-ssis-packages-azure.md). 이 자습서에서는 Azure-SSIS IR을 만드는 단계별 지침을 제공합니다. Azure SQL Database를 사용하여 SSIS 카탈로그를 호스트합니다. 
- [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 더 자세히 설명합니다. 가상 네트워크에서 가상 네트워크 서비스 엔드포인트 또는 SQL Managed Instance와 함께 Azure SQL Database를 사용하여 SSIS 카탈로그를 호스트하는 방법에 대한 지침을 제공합니다. 이 문서는 Azure-SSIS IR을 가상 네트워크에 조인하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR에 대한 정보를 가져오는 방법을 보여 줍니다. 반환된 정보의 상태 설명도 제공합니다. 
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR을 규모 확장하는 방법을 보여줍니다.
