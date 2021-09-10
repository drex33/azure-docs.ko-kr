---
title: Azure Migrate에서 에이전트 없는 종속성 분석 설정
description: Azure Migrate에서 에이전트 없는 종속성 분석을 설정합니다.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 7f4066f466b6dd9e8cb41bf68592aa88c192c44d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536498"
---
# <a name="analyze-server-dependencies-agentless"></a>서버 종속성 분석(에이전트 없음)

이 문서에서는 Azure Migrate: 검색 및 평가 도구를 사용하여 에이전트 없는 종속성 분석을 설정하는 방법에 대해 설명합니다. [종속성 분석](concepts-dependency-visualization.md)을 사용하면 평가 및 Azure로의 마이그레이션을 위한 서버 간의 종속성을 파악하고 이해하는 데 도움이 됩니다.

> [!IMPORTANT]
>에이전트 없는 종속성 분석은 현재 VMware 환경에서 실행되는 서버에 대해서만 제공되며, Azure Migrate: 검색 및 평가 도구를 사용하여 검색할 수 있습니다.

## <a name="current-limitations"></a>현재 제한 사항

- 현재 종속성 분석 보기에서는 그룹에서 서버를 추가하거나 제거할 수 없습니다.
- 현재 서버 그룹에 대한 종속성 맵은 사용할 수 없습니다.
- Azure Migrate 프로젝트에서 종속성 데이터 수집은 1,000개의 서버에 대해 동시에 사용할 수 있습니다. 1,000개의 서버를 일괄로 시퀀싱하여 더 많은 수의 서버를 분석할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

- Azure Migrate: 검색 및 평가 도구가 추가된 [프로젝트를 만들었는지](./create-manage-projects.md) 확인합니다.
- [VMware 요구 사항](migrate-support-matrix-vmware.md#vmware-requirements)을 검토하여 종속성 분석을 수행합니다.
- 어플라이언스를 설정하기 전에 [어플라이언스 요구 사항](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements)을 검토합니다.
- 서버에서 종속성 분석을 사용하기 전에 [종속성 분석 요구 사항을 검토](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)합니다.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Azure Migrate 어플라이언스 배포 및 구성

1. Azure Migrate 어플라이언스 배포 [요구 사항을 검토](migrate-appliance.md#appliance---vmware)합니다.
2. 어플라이언스가 [퍼블릭](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스해야 하는 Azure URL을 검토합니다.
3. 검색 및 평가 중에 어플라이언스에서 수집하는 [데이터를 검토](migrate-appliance.md#collected-data---vmware)합니다.
4. 어플라이언스에 대한 포트 액세스 요구 사항에 [유의](migrate-support-matrix-vmware.md#port-access-requirements)하세요.
5. [Azure Migrate 어플라이언스를 배포](how-to-set-up-appliance-vmware.md)하여 검색을 시작합니다. 어플라이언스를 배포하려면 OVA 템플릿을 다운로드하고 VMware로 가져와 vCenter Server에서 실행되는 서버를 만듭니다. 어플라이언스를 배포한 후에는 프로젝트에 등록하고 검색을 시작하도록 구성해야 합니다.
6. 어플라이언스를 구성할 때는 어플라이언스 구성 관리자에서 다음을 지정해야 합니다.
    - 연결할 vCenter Server의 세부 정보.
    - VMware 환경에서 서버를 검색하도록 범위가 지정된 vCenter Server 자격 증명.
    - 서버 자격 증명으로, 도메인/Windows(비도메인)/Linux(비도메인) 자격 증명일 수 있습니다. 자격 증명을 제공하는 방법과 처리하는 방법에 대해 [자세히 알아보세요](add-server-credentials.md).

## <a name="verify-permissions"></a>권한 확인

- 검색 및 평가를 위해 [vCenter Server 읽기 전용 계정을 만들어야](./tutorial-discover-vmware.md#prepare-vmware) 합니다. 읽기 전용 계정이 서버와 상호 작용하여 종속성 데이터를 수집하기 위해서는 **가상 머신** > **게스트 작업** 에 대해 사용된 권한이 있어야 합니다.
- Azure Migrate 서버에 액세스하여 종속성 데이터를 수집할 수 있도록 사용자 계정이 필요합니다. Windows 및 Linux 서버에 대한 계정 요구 사항에 대해 [알아봅니다](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

### <a name="add-credentials-and-initiate-discovery"></a>자격 증명 추가 및 검색 시작

1. 어플라이언스 구성 관리자를 열고 어플라이언스의 필수 구성 요소 확인 및 등록을 완료합니다.
2. **자격 증명 관리 및 검색 원본** 패널로 이동합니다.
1.  **1단계: vCenter Server 자격 증명 제공** 에서 **자격 증명 추가** 를 클릭하여 어플라이언스가 vCenter Server에서 실행 중인 서버를 검색하는 데 사용할 vCenter Server 계정에 대한 자격 증명을 제공합니다.
1. **2단계: vCenter Server 세부 정보 제공** 에서 **검색 원본 추가** 를 클릭하여 드롭다운에서 자격 증명 이름을 선택하고, vCenter Server 인스턴스 :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="어플라이언스 구성 관리자의 vCenter Server 세부 정보에 대한 패널 3":::의 **IP 주소/FQDN** 을 지정합니다.
1. **3단계: 소프트웨어 인벤토리, 에이전트 없는 종속성 분석, SQL Server 인스턴스, 데이터베이스 검색을 수행하는 데 필요한 서버 자격 증명 제공** 에서 **자격 증명 추가** 를 클릭하여 소프트웨어 인벤토리를 시작할 수 있는 여러 서버 자격 증명을 제공합니다.
1. **검색 시작** 을 선택하여 vCenter Server 검색을 시작합니다.

 vCenter Server 검색이 완료되면 어플라이언스는 설치된 애플리케이션, 역할 및 기능(소프트웨어 인벤토리)에 대한 검색을 시작합니다. 소프트웨어 인벤토리 중에 추가된 서버 자격 증명은 서버에 대해 반복되고 에이전트 없는 종속성 분석에 대해 유효성이 검사됩니다. 포털에서 서버에 에이전트 없는 종속성 분석을 사용할 수 있습니다. 유효성 검사에 성공한 서버만 선택하여 에이전트 없는 종속성 분석을 사용하도록 설정할 수 있습니다.

## <a name="start-dependency-discovery"></a>종속성 검색 시작

종속성 검색을 사용할 서버를 선택합니다.

1. **Azure Migrate: 검색 및 평가** 에서 **검색된 서버** 를 클릭합니다.
2. 검색을 검토하려는 **어플라이언스 이름** 을 선택합니다.
1. **종속성(에이전트 없음)** 열에서 서버의 유효성 검사 상태를 확인할 수 있습니다.
1. **종속성 분석** 드롭다운을 클릭합니다.
1. **서버 추가** 를 클릭합니다.
1. **서버 추가** 페이지에서 종속성 분석을 사용할 서버를 선택합니다. 유효성 검사를 성공적으로 완료한 서버에서만 종속성 매핑을 사용할 수 있습니다. 다음 유효성 검사 주기는 마지막 유효성 검사 타임스탬프 후 24시간 후에 실행됩니다.
1. 서버를 선택한 후 **서버 추가** 를 클릭합니다.

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="종속성 분석 시작":::

서버에 대한 종속성 분석을 사용한 후의 약 6시간에 대한 종속성을 시각화할 수 있습니다. 여러 서버를 동시에 종속성 분석에 사용하려면 [PowerShell](#start-or-stop-dependency-analysis-using-powershell)을 사용하여 작업을 수행할 수 있습니다.

## <a name="visualize-dependencies"></a>앱 종속성 시각화

1. **Azure Migrate: 검색 및 평가** 에서 **검색된 서버** 를 클릭합니다.
1. 검색을 검토하려는 **어플라이언스 이름** 을 선택합니다.
1. 검토하려는 종속성이 있는 서버를 검색합니다.
1. **종속성(에이전트 없음)** 열에서 **종속성 보기** 를 클릭합니다.
1. **기간 드롭다운** 을 사용하여 맵을 보려는 기간을 변경합니다.
1. **클라이언트** 그룹을 확장하여 선택한 서버에 종속된 서버를 나열합니다.
1. **포트** 그룹을 확장하여 선택한 서버에서 종속성이 있는 서버를 나열합니다.
1. 종속 서버의 맵 보기로 이동하려면 서버 이름 > **서버 맵 로드**
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="서버 포트 그룹 확장 및 서버 맵 로드":::를 클릭합니다.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="클라이언트 그룹 확장":::

8. 각 종속성에 대한 프로세스 수준 세부 정보를 보려면 선택한 서버를 확장합니다.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="서버를 확장하여 프로세스 표시":::

> [!NOTE]
> 종속성에 대한 프로세스 정보가 항상 제공되는 것은 아닙니다. 제공되지 않는 경우 종속성은 “알 수 없는 프로세스”로 표시된 프로세스로 나타납니다.

## <a name="export-dependency-data"></a>종속성 데이터 내보내기

1. **Azure Migrate: 검색 및 평가** 에서 **검색된 서버** 를 클릭합니다.
2. **종속성 분석** 드롭다운을 클릭합니다.
3. **애플리케이션 종속성 내보내기** 를 클릭합니다.
4. **애플리케이션 종속성 내보내기** 페이지에서 원하는 서버를 검색하는 어플라이언스 이름을 선택합니다.
5. 시작 시간 및 종료 시간을 선택합니다. 최근 30일 동안의 데이터만 다운로드할 수 있습니다.
6. **종속성 내보내기** 를 클릭합니다.

종속성 데이터는 CSV 형식으로 내보내고 다운로드됩니다. 다운로드한 파일에는 종속성 분석에 대해 사용 설정된 모든 서버의 종속성 데이터가 포함됩니다. 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="종속성 내보내기":::

### <a name="dependency-information"></a>종속성 정보

내보낸 CSV의 각 행은 지정된 시간 슬롯에서 관찰된 종속성에 해당합니다.

다음 표에서는 내보낸 CSV의 필드를 요약하여 보여 줍니다. 서버 이름, 애플리케이션, 프로세스 필드는 에이전트 없는 종속성 분석이 사용된 서버에 대해서만 채워집니다.

**필드 이름** | **세부 정보**
--- | --- 
시간 슬롯 | 종속성이 관찰된 시간 슬롯입니다. <br/> 현재 종속성 데이터는 6시간 슬롯에 대해 캡처됩니다.
원본 서버 이름 | 원본 서버의 이름 
원본 애플리케이션 | 원본 서버의 애플리케이션 이름  
원본 프로세스 | 원본 서버에 있는 프로세스의 이름  
대상 서버 이름 | 대상 서버의 이름
대상 IP | 대상 서버의 IP 주소
대상 응용 프로그램 | 대상 서버의 애플리케이션 이름
대상 프로세스 | 대상 서버의 프로세스 이름  
대상 포트 | 대상 서버의 포트 번호

## <a name="stop-dependency-discovery"></a>종속성 검색 중지

종속성 검색을 중지하려는 서버를 선택합니다.

1. **Azure Migrate: 검색 및 평가** 에서 **검색된 서버** 를 클릭합니다.
1. 검색을 검토하려는 **어플라이언스 이름** 을 선택합니다.
1. **종속성 분석** 드롭다운을 클릭합니다.
1. **서버 제거** 를 클릭합니다.
1. **서버 제거** 페이지에서 종속성 분석을 위해 중지하려는 서버를 선택합니다.
1. 서버를 선택한 후 **서버 제거** 를 클릭합니다.

여러 서버에서 종속성을 동시에 중지하려는 경우 [PowerShell](#start-or-stop-dependency-analysis-using-powershell)을 사용하여 작업을 수행할 수 있습니다.

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>PowerShell을 사용하여 종속성 분석 시작 또는 중지

GitHub의 [Azure PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) 리포지토리에서 PowerShell 모듈을 다운로드합니다.

### <a name="log-in-to-azure"></a>Azure에 로그인

1. Connect-AzAccount cmdlet을 사용하여 Azure 구독에 로그인합니다.

    ```PowerShell
    Connect-AzAccount
    ```
    Azure Government를 사용하는 경우 다음 명령을 사용합니다.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. 프로젝트를 만든 구독을 선택합니다. 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. 다운로드한 AzMig_Dependencies PowerShell 모듈을 가져옵니다.

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>종속성 데이터 수집을 사용 또는 사용하지 않습니다.

1. 다음 명령을 사용하여 프로젝트에서 검색된 서버 목록을 가져옵니다. 아래 예제에서 프로젝트 이름은 FabrikamDemoProject이고, 속한 리소스 그룹은 FabrikamDemoRG입니다. 서버 목록이 FabrikamDemo_VMs.csv에 저장됩니다.

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    파일에서 서버 표시 이름, 종속성 컬렉션의 현재 상태 및 검색된 모든 서버의 ARM ID를 확인할 수 있습니다. 

2. 종속성을 사용 또는 사용하지 않으려면 입력 CSV 파일을 만듭니다. 이 파일에는 “ARM ID” 헤더가 있는 열이 있어야 합니다. CSV 파일에 있는 모든 추가 헤더는 무시됩니다. 이전 단계에서 생성된 파일을 사용하여 CSV를 만들 수 있습니다. 종속성을 사용 또는 사용하지 않을 서버를 유지하는 파일의 복사본을 만듭니다. 

    다음 예에서는 입력 파일 FabrikamDemo_VMs_Enable.csv의 서버 목록에서 종속성 분석을 사용합니다.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    다음 예에서는 입력 파일 FabrikamDemo_VMs_Enable.csv의 서버 목록에서 종속성 분석을 사용하지 않습니다.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Power BI에서 네트워크 연결 시각화

Azure Migrate는 여러 서버의 네트워크 연결을 한 번에 시각화하고 프로세스 및 서버를 기준으로 필터링하는 데 사용할 수 있는 Power BI 템플릿을 제공합니다. 네트워크 연결을 시각화하려면 아래 지침에 따라 종속성 데이터를 사용하여 Power BI를 로드합니다.

1. GitHub의 [Azure PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) 리포지토리에서 PowerShell 모듈 및 Power BI 템플릿을 다운로드합니다.

2. 아래 지침을 사용하여 Azure에 로그인합니다. 
    - Connect-AzAccount cmdlet을 사용하여 Azure 구독에 로그인합니다.

        ```PowerShell
        Connect-AzAccount
        ```

    - Azure Government를 사용하는 경우 다음 명령을 사용합니다.

        ```PowerShell
        Connect-AzAccount -EnvironmentName AzureUSGovernment
        ```

    - 프로젝트를 만든 구독을 선택합니다.

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. 다운로드한 AzMig_Dependencies PowerShell 모듈을 가져옵니다.

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. 다음 명령을 실행합니다. 해당 명령은 CSV에 종속성 데이터를 다운로드하고 처리하여 Power BI에서 시각화에 사용할 수 있는 고유한 종속성 목록을 생성합니다. 아래 예제에서 프로젝트 이름은 FabrikamDemoProject이고, 속한 리소스 그룹은 FabrikamDemoRG입니다. FabrikamAppliance에서 검색한 서버에 대한 종속성이 다운로드됩니다. 고유 종속성은 FabrikamDemo_Dependencies.csv에 저장됩니다.

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. 다운로드된 Power BI 템플릿을 엽니다.

6. 다운로드된 종속성 데이터를 Power BI에서 로드합니다.
    - Power BI에서 템플릿을 엽니다.
    - 도구 모음에서 **데이터 가져오기** 를 클릭합니다. 
    - 일반 데이터 원본에서 **텍스트/CSV** 를 선택합니다.
    - 다운로드된 종속성 파일을 선택합니다.
    - **로드** 를 클릭합니다.
    - CSV 파일의 이름을 가진 테이블을 가져온 것을 확인할 수 있습니다. 오른쪽의 필드 막대에서 테이블을 확인할 수 있습니다. 테이블의 이름을 AzMig_Dependencies로 바꿉니다.
    - 도구 모음에서 새로 고침을 클릭합니다.

    네트워크 연결 차트와 원본 서버 이름, 대상 서버 이름, 원본 프로세스 이름, 대상 프로세스 이름 슬라이서가 가져온 데이터와 함께 강조 표시됩니다.

7. 서버 및 프로세스별로 필터링하여 네트워크 연결 맵을 시각화합니다. 파일을 저장합니다.

## <a name="next-steps"></a>다음 단계

평가를 위해 [서버를 그룹화](how-to-create-a-group.md)합니다.