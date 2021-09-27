---
title: Azure Data Factory의 통합 런타임 모니터링
description: Azure Data Factory에서 다양한 유형의 통합 런타임을 모니터링하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 08/11/2020
author: minhe-msft
ms.author: hemin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8633ee6f76319afcd9c62a3aa5d70db77113f235
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750594"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Azure Data Factory의 통합 런타임 모니터링

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**통합 런타임** 은 서로 다른 네트워크 환경에서 다양한 데이터 통합 기능을 제공하기 위해 Azure Data Factory(ADF)에서 사용하는 컴퓨팅 인프라입니다. Data Factory는 세 가지 유형의 통합 런타임을 제공합니다.

- Azure 통합 런타임
- 자체 호스팅 통합 런타임
- Azure SQL Server Integration Services(SSIS) 통합 런타임

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

IR(통합 런타임) 인스턴스의 상태를 가져 오려면 다음 PowerShell 명령을 실행합니다. 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

이 cmdlet은 여러 유형의 통합 런타임에 대해 서로 다른 정보를 반환합니다. 이 문서는 각 통합 런타임 유형의 속성 및 상태에 대해 설명합니다.  

## <a name="azure-integration-runtime"></a>Azure 통합 런타임

Azure 통합 런타임의 컴퓨팅 리소스는 Azure에서 완전히 탄력적으로 관리됩니다. 다음 테이블은 **Get-AzDataFactoryV2IntegrationRuntime** 명령에 의해 반환되는 속성에 대한 설명을 제공합니다.

### <a name="properties"></a>속성

다음 테이블은 Azure 통합 런타임에 대해 cmdlet에서 반환하는 속성에 대한 설명을 제공합니다.

| 속성 | Description |
-------- | ------------- | 
| Name | Azure 통합 런타임의 이름. |  
| 주 | Azure 통합 런타임의 상태. | 
| 위치 | Azure 통합 런타임의 위치. Azure 통합 런타임의 위치에 대한 자세한 내용은 [통합 런타임 소개](concepts-integration-runtime.md)를 참조하세요. |
| DataFactoryName | Azure 통합 런타임이 속한 데이터 팩터리의 이름. | 
| ResourceGroupName | 데이터 팩터리가 속한 리소스 그룹의 이름.  |
| Description | 통합 런타임에 대한 설명.  |

### <a name="status"></a>상태

다음 테이블은 Azure 통합 런타임의 가능한 상태를 제공합니다.

| 상태 | 설명/시나리오 | 
| ------ | ------------------ |
| 온라인 | Azure 통합 런타임이 온라인 상태이며 사용할 준비가 되었습니다. | 
| 오프라인 | Azure 통합 런타임이 내부 오류로 인해 오프라인 상태입니다. |

## <a name="self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임

이 섹션은 Get-AzDataFactoryV2IntegrationRuntime cmdlet에 의해 반환되는 속성에 대한 설명을 제공합니다. 

> [!NOTE] 
> 반환된 속성 및 상태에는 전반적인 자체 호스팅 통합 런타임 및 런타임의 각 노드에 대한 정보가 포함됩니다.  

### <a name="properties"></a>속성

다음 테이블은 **각 노드** 의 속성 모니터링에 대한 설명을 제공합니다.

| 속성 | Description | 
| -------- | ----------- | 
| Name | 자체 호스팅 통합 런타임의 이름 및 이와 연결된 노드. 노드는 자체 호스팅 통합 런타임이 설치된 온-프레미스 Windows 컴퓨터입니다. |  
| 상태 | 전반적인 자체 호스팅 통합 런타임 및 각 노드의 상태. 예제: 온라인/오프라인/제한됨/기타. 해당 상태에 대한 자세한 정보는 다음 섹션을 참조하세요. | 
| 버전 | 자체 호스팅 통합 런타임 및 각 노드의 버전. 자체 호스팅 통합 런타임 버전은 그룹에 있는 노드의 대다수 버전을 기반으로 결정됩니다. 자체 호스팅 통합 런타임 설정에 다른 버전의 노드가 있는 경우 논리 자체 호스팅 통합 런타임과 버전 번호가 동일한 노드만 제대로 작동합니다. 다른 버전의 노드는 제한된 모드에 있으므로 수동으로 업데이트해야 합니다(자동 업데이트가 실패할 경우에만). | 
| 사용 가능한 메모리 | 자체 호스팅 통합 런타임 노드에서 사용 가능한 메모리. 이 값은 거의 실시간 스냅샷입니다. | 
| CPU 사용률 | 자체 호스팅 통합 런타임 노드의 CPU 사용률. 이 값은 거의 실시간 스냅샷입니다. |
| 네트워킹(수신/송신) | 자체 호스팅 통합 런타임 노드의 네트워크 사용률. 이 값은 거의 실시간 스냅샷입니다. | 
| 동시 작업(실행/제한) | **실행 중**. 각 노드에서 실행되는 작업 또는 태스크의 수입니다. 이 값은 거의 실시간 스냅샷입니다. <br/><br/>**제한** 제한은 각 노드의 최대 동시 작업 수를 나타냅니다. 이 값은 컴퓨터 크기에 따라 정의됩니다. CPU, 메모리 또는 네트워크가 충분히 활용되지 않는 경우에도 작업 시간이 초과하면 제한을 늘려 고급 시나리오에서 동시 작업 실행을 강화할 수 있습니다. 이 기능은 단일 노드 자체 호스팅 통합 런타임에서도 사용할 수 있습니다. |
| 역할 | 다중 노드 자체 호스팅 통합 런타임에는 디스패처 및 작업자라는 두 가지 유형의 역할이 있습니다. 모든 노드는 작업자이며, 이는 모두 작업을 실행하는 데 사용할 수 있음을 의미합니다. 디스패처 노드는 하나만 존재합니다. 이 노드는 클라우드 서비스에서 작업/태스크를 가져와서 다른 작업자 노드에 발송하는 데 사용됩니다. 디스패처 노드는 작업자 노드이기도 합니다. |

자체 호스팅 통합 런타임에 둘 이상의 노드가 있는 경우(즉, 규모 확장 시나리오) 속성의 일부 설정이 더 적합합니다.

#### <a name="concurrent-jobs-limit"></a>동시 작업 제한

동시 작업 제한의 기본값은 머신 크기에 따라 설정됩니다. 이 값을 계산하는 데 사용된 요소는 머신의 RAM 크기 및 CPU 코어 수에 따라 달라집니다. 따라서 코어와 메모리가 많을 수록 동시 작업의 기본 제한이 높아집니다.

노드 수를 늘려서 규모를 확장할 수 있습니다. 노드의 수를 늘리면 동시 작업 제한은 사용 가능한 모든 노드의 동시 작업 제한 값의 합계입니다.  예를 들어 하나의 노드를 사용하여 최대 12개의 동시 작업을 실행할 수 있는 경우 세 가지 유사한 노드를 추가하면 최대 48개(4x12)의 동시 작업을 실행할 수 있습니다. 각 노드의 기본 값을 포함하는 낮은 리소스 사용량이 표시되는 경우에만 동시 작업 제한을 늘리는 것이 좋습니다.

Azure Portal에서 계산된 기본값을 재정의할 수 있습니다. 작성자 > 연결 > 통합 런타임 > 편집 > 노드 > 노드당 동시 작업 값 수정을 차례로 선택합니다. PowerShell [update-Azdatafactoryv2integrationruntimenode](/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) 명령을 사용할 수도 있습니다.
  
### <a name="status-per-node"></a>상태(노드당)

다음 테이블은 자체 호스팅 통합 런타임 노드의 가능한 상태를 제공합니다.

| 상태 | Description |
| ------ | ------------------ | 
| 온라인 | 노드가 Data Factory 서비스에 연결되어 있습니다. |
| 오프라인 | 노드가 오프라인 상태입니다. |
| 업그레이드 중 | 노드가 자동 업데이트 중입니다. |
| 제한됨 | 연결 문제로 인해 제한되는 상태입니다. 8060 HTTP 포트 문제, 서비스 버스 연결 문제 또는 자격 증명 동기화 이슈 때문일 수 있습니다. |
| 비활성 | 노드의 구성이 다른 주 노드의 구성과 다릅니다. |

다른 노드에 연결할 수 없을 때 노드가 비활성 상태일 수 있습니다.

### <a name="status-overall-self-hosted-integration-runtime"></a>상태(전반적인 자체 호스팅 통합 런타임)

다음 테이블은 자체 호스팅 통합 런타임의 가능한 상태를 제공합니다. 이 상태는 런타임에 속한 모든 노드의 상태에 따라 달라집니다. 

| 상태 | Description |
| ------ | ----------- | 
| 등록 필요 | 자체 호스팅 통합 런타임에 등록된 노드가 아직 없습니다. |
| 온라인 | 모든 노드가 온라인 상태입니다. |
| 오프라인 | 온라인 상태인 노드가 없습니다. |
| 제한됨 | 자체 호스팅 통합 런타임의 일부 노드가 정상 상태가 아닙니다. 이 상태는 일부 노드가 중단되었을 수 있다는 경고입니다. 이 상태는 디스패처/작업자 노드의 자격 증명 동기화 문제 때문일 수 있습니다. |

**Get-AzDataFactoryV2IntegrationRuntimeMetric** cmdlet을 사용하여 세부적인 자체 호스팅 통합 런타임 속성 및 cmdlet이 실행되는 동안 스냅샷 값이 포함된 JSON 페이로드를 가져옵니다.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

샘플 출력(자체 호스팅 통합 런타임과 연결된 노드가 두 개라고 가정):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```

## <a name="azure-ssis-integration-runtime"></a>Azure SSIS 통합 런타임

Azure-SSIS IR은 SSIS 패키지 실행 전용 Azure 가상 머신(VM 또는 노드)의 완전 관리형 클러스터입니다. Azure-SSIS IR에서 SSIS 패키지 실행을 다양한 메서드로 호출할 수 있습니다. 예를 들어 Azure 사용 SQL Server Data Tools(SSDT), AzureDTExec 명령줄 유틸리티, SQL Server Management Studio(SSMS)/SQL Server Agent의 T-SQL 및 ADF 파이프라인의 Execute SSIS Package가 있습니다. Azure-SSIS IR은 기타 ADF 작업을 실행하지 않습니다. 프로비저닝되면 Azure PowerShell, Azure Portal 및 Azure Monitor를 통해 전체/노드별 속성 및 상태를 모니터링할 수 있습니다.

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-powershell"></a>Azure PowerShell을 사용하여 Azure-SSIS 통합 런타임 모니터링

다음 Azure PowerShell cmdlet을 사용하여 Azure-SSIS IR의 전체/노드별 속성 및 상태를 모니터링할 수 있습니다.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

#### <a name="properties"></a>속성

다음 테이블은 Azure-SSIS IR에 대해 cmdlet에서 반환하는 속성에 대한 설명을 제공합니다.

| 속성/상태              | Description                  |
| ---------------------------- | ---------------------------- |
| CreateTime                   | Azure-SSIS IR이 만들어진 UTC 시간입니다. |
| 노드                        | 노드별 상태(시작 중/사용 가능/재생 중/사용할 수 없음)와 조치 가능한 오류가 있는 Azure-SSIS IR의 할당/사용 가능한 노드입니다. |
| OtherErrors                  | Azure-SSIS IR의 비노드별 조치 가능한 오류입니다. |
| LastOperation                | 실패한 경우, 조치 가능한 오류가 발생한 Azure-SSIS IR의 마지막 시작/중지 작업 결과입니다. |
| 주                        | Azure-SSIS IR의 전반적인 상태(초기/시작/시작됨/중지/중지됨)입니다. |
| 위치                     | Azure-SSIS IR의 위치입니다. |
| NodeSize                     | Azure-SSIS IR의 각 노드 크기입니다. |
| NodeCount                    | Azure-SSIS IR의 노드 수입니다. |
| MaxParallelExecutionsPerNode | Azure-SSIS IR의 노드당 최대 병렬 실행 수입니다. |
| CatalogServerEndpoint        | SSIS 카탈로그(SSISDB)를 호스트하는 기존 Azure SQL Database 서버 또는 관리형 인스턴스의 엔드포인트입니다. |
| CatalogAdminUserName         | 기존 Azure SQL Database 서버 또는 관리형 인스턴스에 대한 관리자의 사용자 이름입니다. ADF는 이 정보를 사용하여 사용자 대신 SSISDB를 준비하고 관리합니다. |
| CatalogAdminPassword         | 기존 Azure SQL Database 서버 또는 관리형 인스턴스에 대한 관리자 암호입니다. |
| CatalogPricingTier           | 기존 Azure SQL Database 서버가 호스트하는 SSISDB의 가격 책정 계층입니다.  SSISDB를 호스트하는 Azure SQL Managed Instance에는 해당되지 않습니다. |
| VNetId                       | Azure-SSIS IR이 조인할 가상 네트워크 리소스 ID입니다. |
| 서브넷                       | Azure-SSIS IR이 조인할 서브넷 이름입니다. |
| ID                           | Azure-SSIS IR의 리소스 ID입니다. |
| 형식                         | Azure-SSIS IR의 유형(관리형/자체 호스팅)입니다. |
| ResourceGroupName            | ADF 및 Azure-SSIS IR이 만들어진 Azure 리소스 그룹의 이름입니다. |
| DataFactoryName              | ADF의 이름입니다. |
| Name                         | Azure-SSIS IR 이름입니다. |
| Description                  | Azure-SSIS IR에 대한 설명입니다. |
  
#### <a name="status-per-azure-ssis-ir-node"></a>상태(Azure-SSIS IR 노드당)

다음 테이블은 Azure-SSIS IR 노드의 가능한 상태를 제공합니다.

| 노드별 상태 | Description |
| -------------------- | ----------- | 
| 시작 중             | 이 노드가 준비 중입니다. |
| 사용 가능            | 이 노드는 SSIS 패키지를 배포/실행할 준비가 되었습니다. |
| 재생 중            | 이 노드는 복구 중/다시 시작 중입니다. |
| Unavailable          | 이 노드는 SSIS 패키지를 배포/실행할 준비가 되지 않았으며 해결할 수 있는 조치 가능한 오류/문제를 포함합니다. |

#### <a name="status-overall-azure-ssis-ir"></a>상태(전체 Azure-SSIS IR)

다음 테이블에서는 가능한 Azure-SSIS IR의 전체 상태를 제공합니다. 전체 상태는 Azure-SSIS IR에 속하는 모든 노드의 결합 상태에 따라 달라집니다. 

| 전반적인 상태 | Description | 
| -------------- | ----------- | 
| Initial        | Azure-SSIS IR 노드가 할당/준비되지 않았습니다. | 
| 시작 중       | Azure-SSIS IR의 노드가 할당/준비 중이고 청구가 시작되었습니다. |
| 시작됨        | Azure-SSIS IR 노드가 할당/준비되었으며 SSIS 패키지를 배포/실행할 준비가 되었습니다. |
| 중지 중       | Azure-SSIS IR의 노드가 해제되고 있습니다. |
| 중지됨        | Azure-SSIS IR의 노드가 해제되었고 청구가 중단되었습니다. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-azure-portal"></a>Azure Portal에서 Azure SSIS 통합 런타임 모니터링

Azure Portal에서 Azure-SSIS IR를 모니터링하려면 모든 통합 런타임을 볼 수 있는 ADF UI에서 **모니터** 허브의 **통합 런타임** 페이지로 이동합니다.

:::image type="content" source="media/monitor-integration-runtime/monitor-integration-runtimes.png" alt-text="모든 통합 런타임 모니터링":::

그런 다음 Azure-SSIS IR 이름을 선택하여 해당 모니터링 페이지를 엽니다. 여기서 전체/노드별 속성 및 상태를 볼 수 있습니다. 이 페이지에서는 Azure-SSIS IR의 일반, 배포 및 고급 설정을 구성하는 방법에 따라 다양한 정보 제공/기능 타일을 찾을 수 있습니다.

**TYPE** 및 **REGION** 정보 제공 타일에는 각각 Azure-SSIS IR의 유형과 지역이 표시됩니다.

**NODE SIZE** 정보 제공 타일에는 SKU(SSIS edition_VM tier_VM 시리즈), CPU 코어 수, Azure-SSIS IR 노드당 RAM 크기 등이 표시됩니다. 

**RUNNING / REQUESTED NODE(S)** 정보 제공 타일은 현재 실행 중인 노드 수를 Azure-SSIS IR에 대해 이전에 요청된 총 노드 수와 비교합니다.

**DUAL STANDBY PAIR / ROLE** 정보 제공 타일은 BCDR(무중단 업무 방식 및 재해 복구)를 위해 Azure SQL Database/Managed Instance 장애 조치(failover) 그룹을 동기화하는 데 사용되는 이중 대기 Azure-SSIS IR 쌍의 이름과 Azure-SSIS IR의 현재 주/보조 역할을 보여 줍니다. SSISDB 장애 조치(failover)가 발생하면 기본 및 보조 Azure SSIS IR에서 역할을 전환합니다([BCDR에 대한 Azure-SSIS IR 구성](./configure-bcdr-azure-ssis-integration-runtime.md) 참조).

기능 타일에 대한 세부 정보는 다음을 참조하세요.

:::image type="content" source="media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime.png" alt-text="Azure-SSIS IR 모니터링":::

#### <a name="status-tile"></a>STATUS 타일

Azure-SSIS IR 모니터링 페이지의 **STATUS** 타일에서 전체 상태(예: **실행 중** 또는 **중지 됨**)를 볼 수 있습니다. **실행** 상태를 선택하면 Azure-SSIS IR를 중지하는 라이브 **중지** 단추가 있는 창이 표시됩니다. **중지 됨** 상태를 선택하면 Azure-SSIS IR을 시작하는 라이브 **시작** 단추가 있는 창이 표시됩니다. 또한 팝업 창에는 Azure-SSIS IR에서 실행되는 Execute SSIS Package 작업을 사용하여 ADF 파이프라인을 자동 생성할 수 있는 **Execute SSIS package** 단추([ADF 파이프라인에서 Execute SSIS Package 작업으로 SSIS packages 실행](./how-to-invoke-ssis-package-ssis-activity.md) 참조) 및 Azure-SSIS IR 리소스 ID(`/subscriptions/YourAzureSubscripton/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourADF/integrationruntimes/YourAzureSSISIR`)를 복사할 수 있는 **리소스 ID** 텍스트 상자가 있습니다. ADF 및 Azure-SSIS IR 이름을 포함하는 Azure-SSIS IR 리소스 ID의 접미사는 ISVs(독립 소프트웨어 공급 업체)에서 추가 프리미엄/라이선스 SSIS 구성 요소를 구매하고 Azure-SSIS IR에 바인딩하는 데 사용할 수 있는 클러스터 ID를 형성합니다([Azure-SSIS IR에서 프리미엄/라이선스 구성 요소 설치](./how-to-develop-azure-ssis-ir-licensed-components.md) 참조).

:::image type="content" source="media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-status.png" alt-text="Azure-SSIS IR - STATUS 타일 모니터링":::

#### <a name="ssisdb-server-endpoint-tile"></a>SSISDB SERVER ENDPOINT 타일

Azure SQL Database 서버 또는 관리형 인스턴스가 호스트하는 SSISDB에 패키지가 저장되는 프로젝트 배포 모델을 사용하는 경우 Azure-SSIS IR 모니터링 페이지에 **SSISDB SERVER ENDPOINT** 타일이 표시됩니다([Azure-SSIS IR 배포 설정 구성](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page) 참조). 이 타일에서는 Azure SQL Database 서버 또는 관리형 인스턴스를 지정하는 링크를 선택하여 창을 표시할 수 있습니다. 이 창에서는 텍스트 상자에서 서버 엔드포인트를 복사하고 SSMS에서 연결하여 패키지를 배포, 구성, 실행 및 관리할 수 있습니다. 팝업 창에서 **Azure SQL Database 또는 관리형 인스턴스 설정 보기** 링크를 선택하여 Azure Portal에서 SSISDB를 다시 구성하거나 크기를 조정할 수도 있습니다.

:::image type="content" source="media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-ssisdb.png" alt-text="Azure-SSIS IR - SSISDB 타일 모니터링":::

#### <a name="proxy--staging-tile"></a>PROXY / STAGING 타일

온-프레미스 데이터에 액세스하기 위해 Azure-SSIS IR의 프록시로 Self-Hosted IR(SHIR)을 다운로드, 설치 및 구성하는 경우 Azure-SSIS IR 모니터링 페이지에 **PROXY / STAGING** 타일이 표시됩니다([Azure-SSIS IR에 대한 프록시로 SHIR 구성](./self-hosted-integration-runtime-proxy-ssis.md) 참조). 이 타일에서 SHIR을 지정하는 링크를 선택하여 해당 모니터링 페이지를 열 수 있습니다. 준비용 Azure Blob Storage를 지정하여 연결된 서비스를 다시 구성하는 기타 링크를 선택할 수도 있습니다.

#### <a name="validate-vnet--subnet-tile"></a>VALIDATE VNET / SUBNET 타일

Azure-SSIS IR을 VNet에 조인하는 경우 Azure-SSIS IR 모니터링 페이지에서 **VALIDATE VNET / SUBNET** 타일이 표시됩니다([VNet에 Azure-SSIS IR 조인](./join-azure-ssis-integration-runtime-virtual-network.md) 참조). 이 타일에서는 VNet 및 서브넷을 지정하는 링크를 선택하여 창을 표시할 수 있습니다. 여기서 VNet 리소스 ID(`/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworks/YourARMVNet`)와 서브넷 이름을 텍스트 상자에서 복사하고 VNet 및 서브넷 구성의 유효성을 검사하여 필요한 인바운드/아웃바운드 네트워크 트래픽 및 Azure-SSIS IR 관리에 방해되지 않도록 할 수 있습니다.

:::image type="content" source="media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-validate.png" alt-text="Azure-SSIS IR - VALIDATE 타일 모니터링":::

#### <a name="diagnose-connectivity-tile"></a>DIAGNOSE CONNECTIVITY 타일

Azure-SSIS IR 모니터링 페이지의 **DIAGNOSE CONNECTIVITY** 타일에서 **연결 테스트** 링크를 선택하여 창을 표시할 수 있습니다. 여기서는 Azure-SSIS IR 및 관련 패키지/구성/데이터 저장소 및 관리 서비스는 FQDN(정규화된 도메인 이름)/IP 주소 및 지정 포트를 통해 연결을 확인할 수 있습니다([Azure-SSIS IR에서 연결 테스트](./ssis-integration-runtime-diagnose-connectivity-faq.md) 참조).

:::image type="content" source="media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-diagnose.png" alt-text="Azure-SSIS IR - DIAGNOSE 타일 모니터링":::

#### <a name="static-public-ip-addresses-tile"></a>STATIC PUBLIC IP ADDRESSES 타일

Azure-SSIS IR에 대한 고정적인 공용 IP 주소를 가져오는 경우 Azure-SSIS IR 모니터링 페이지에 **STATIC PUBLIC IP ADDRESSES** 타일이 표시됩니다([Azure-SSIS IR에 대한 고정 공용 IP 주소 가져오기](./join-azure-ssis-integration-runtime-virtual-network.md#publicIP) 참조). 이 타일에서 Azure-SSIS IR에 대한 첫 번째/두 번째 고정적인 공용 IP 주소를 지정하는 링크를 선택하여 텍스트 상자에서 해당 리소스 ID(`/subscriptions/YourAzureSubscripton/resourceGroups/YourResourceGroup/providers/Microsoft.Network/publicIPAddresses/YourPublicIPAddress`)를 복사할 수 있는 창을 표시할 수 있습니다. 팝업 창에서 **첫 번째/두 번째 고정적인 공용 IP 주소 설정 확인** 링크를 선택하여 Azure Portal에서 첫 번째/두 번째 고정 공용 IP 주소를 관리할 수도 있습니다.

:::image type="content" source="media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-static.png" alt-text="Azure-SSIS IR - STATIC 타일 모니터링":::

#### <a name="package-stores-tile"></a>PACKAGE STORES 타일

패키지 배포 모델을 사용하는 경우 패키지는 Azure SQL Managed Instance에서 호스트하고 Azure-SSIS IR 패키지 저장소를 통해 관리형 파일 시스템/Azure Files/SQL Server 데이터베이스(MSDB)에 저장되며, Azure-SSIS IR 모니터링 페이지에 **PACKAGE STORES** 타일이 표시됩니다([Azure-SSIS IR 배포 설정 구성](./tutorial-deploy-ssis-packages-azure.md#deployment-settings-page) 참조). 이 타일에서 Azure-SSIS IR에 연결된 패키지 저장소 수를 지정하는 링크를 선택하여 창을 표시할 수 있습니다. 이 링크를 통해 Azure-SSIS IR 패키지 저장소에 대한 관련 연결 서비스를 Azure SQL Managed Instance에서 호스트하는 파일 시스템/Azure Files/MSDB 위에 다시 구성할 수 있습니다.

:::image type="content" source="media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-package.png" alt-text="Azure-SSIS IR - PACKAGE 타일 모니터링":::

#### <a name="errors-tile"></a>ERROR(S) 타일

Azure-SSIS IR의 시작/중지/유지 관리/업그레이드에 문제가 있는 경우 Azure-SSIS IR 모니터링 페이지에 추가 **ERROR(S)** 타일이 표시됩니다. 이 타일에서는 Azure-SSIS IR에서 생성된 오류 수를 지정하는 링크를 선택하여 창을 표시할 수 있습니다. 여기에서 해당 오류에 대한 세부 정보를 확인하고 복사하여 문제 해결 가이드에서 권장 해결 방법을 찾을 수 있습니다([Azure-SSIS IR 문제 해결](./ssis-integration-runtime-management-troubleshoot.md) 참조).

:::image type="content" source="media/monitor-integration-runtime/monitor-azure-ssis-integration-runtime-error.png" alt-text="Azure-SSIS IR - ERROR 타일 모니터링":::

### <a name="monitor-the-azure-ssis-integration-runtime-with-azure-monitor"></a>Azure Monitor를 사용하여 Azure-SSIS 통합 런타임 모니터링

Azure Monitor로 Azure-SSIS IR를 모니터링하려면 [Azure Monitor를 사용하여 SSIS 작업 모니터링](./monitor-ssis.md)을 참조하세요.

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임에 대한 자세한 정보

Azure-SSIS 통합 런타임에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure-SSIS 통합 런타임](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR을 비롯한 일반적인 통합 런타임에 대한 개념 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](./tutorial-deploy-ssis-packages-azure.md). 이 문서는 Azure-SSIS IR을 만들고 Azure SQL 데이터베이스를 사용하여 SSIS 카탈로그(SSISDB)를 호스트하는 단계별 지침을 제공합니다. 
- [방법: Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 확장하고 Azure SQL Managed Instance를 사용하여 SSISDB를 호스트하는 방법에 대한 지침을 제공합니다. 
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서는 Azure-SSIS IR을 중지, 시작 또는 제거하는 방법을 설명합니다. 또한 노드를 더 추가하여 규모를 확장하는 방법을 보여 줍니다. 
- [Azure-SSIS IR을 가상 네트워크에 조인](join-azure-ssis-integration-runtime-virtual-network.md). 이 문서에서는 Azure-SSIS IR를 Azure 가상 네트워크에 조인하는 방법에 대한 지침을 제공합니다.

## <a name="next-steps"></a>다음 단계
다른 방식으로 파이프라인을 모니터링하려면 다음 문서를 참조하세요. 

- [빠른 시작: 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md).
- [Azure Monitor를 사용하여 Data Factory 파이프라인 모니터링](monitor-using-azure-monitor.md)
