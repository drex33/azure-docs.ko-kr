---
title: Grafana를 사용하여 Azure 서비스 및 애플리케이션 모니터링
description: Azure Monitor 및 Application Insights 데이터를 라우팅하여 Grafana에서 볼 수 있도록 합니다.
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 65bd221bd2f5574db33e6c164f75bb0760dabd3b
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812003"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Grafana에서 Azure 서비스 모니터링
[Grafana](https://grafana.com/) 및 포함된 Azure Monitor 데이터 원본 플러그 [인](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/)을 사용하여 Azure 서비스 및 애플리케이션을 모니터링할 수 있습니다. 플러그 인은 세 가지 Azure 서비스에서 데이터를 검색합니다.
- Azure 리소스의 데이터에서 숫자 계열 데이터에 대한 메트릭을 Azure Monitor. 
- KQL(Kusto 쿼리 언어)을 사용하여 쿼리할 수 있는 Azure 리소스의 로그 및 성능 데이터에 대한 로그를 Azure Monitor.
- Azure Resource Graph 구독에서 Azure 리소스를 신속하게 쿼리하고 식별할 수 있습니다.

그러면 Grafana 대시보드에 이 성능 및 가용성 데이터를 표시할 수 있습니다.

다음 단계를 사용하여 Grafana 서버를 설정하고, Azure Monitor 대시보드를 사용하고, Azure Monitor 메트릭 및 로그를 사용하여 사용자 지정 대시보드를 빌드합니다.

## <a name="set-up-a-grafana-server"></a>Grafana 서버 설정

### <a name="set-up-grafana-locally"></a>Grafana를 로컬로 설정
Grafana 서버를 로컬로 설정하려면 [로컬 환경에서 Grafana를 다운로드하여 설치](https://grafana.com/grafana/download)합니다.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Azure Marketplace를 통해 Azure에서 Grafana 설정
1. Azure Marketplace로 이동하여 Grafana Labs에서 Grafana를 선택합니다.

2. 이름 및 세부 정보를 입력합니다. 새 리소스 그룹 만들기 VM 사용자 이름, VM 암호 및 Grafana 서버 관리자 암호에 대해 선택한 값을 추적합니다.  

3. VM 크기 및 스토리지 계정을 선택합니다.

4. 네트워크 구성 설정을 구성합니다.

5. 요약을 보고 사용 약관에 동의한 후 **만들기** 를 선택합니다.

6. 배포가 완료되면 **리소스 그룹으로 이동** 을 선택합니다. 새로 만든 리소스의 목록이 표시됩니다.

    ![Grafana 리소스 그룹 개체](media/grafana-plugin/grafana-resource-group.png)

    네트워크 보안 그룹(이 경우 *grafana-nsg*)을 선택하면 3000 포트가 Grafana 서버에 액세스하는 데 사용됨을 알 수 있습니다.

7. Grafana 서버의 공용 IP 주소를 가져옵니다. 리소스의 목록으로 돌아가서 **공용 IP 주소** 를 선택합니다.

## <a name="sign-in-to-grafana"></a>Grafana에 로그인

> [!IMPORTANT]
> Internet Explorer 브라우저 및 이전 Microsoft Edge 브라우저는 Grafana와 호환되지 않습니다. 현재 버전의 Microsoft Edge 포함하여 chromium 기반 브라우저를 사용해야 합니다. [Grafana에 대해 지원되는 브라우저](https://grafana.com/docs/grafana/latest/installation/requirements/#supported-web-browsers)를 참조하세요.

1. 서버의 IP 주소를 사용하여 브라우저에서 *http://\<IP address\>:3000* 또는 *\<DNSName>\:3000* 의 로그인 페이지를 엽니다. 3000은 기본 포트이지만, 설정하는 동안 다른 포트를 설치했을 수도 있습니다. 빌드한 Grafana 서버에 대한 로그인 페이지가 표시됩니다.

    ![Grafana 로그인 화면](./media/grafana-plugin/login-screen.png)

2. 사용자 이름 *admin* 및 이전에 만든 Grafana 서버 관리자 암호를 사용하여 로그인합니다. 로컬 설정을 사용하는 경우 기본 암호는 *admin* 이며 첫 번째 로그인에서 변경하라는 메시지가 표시됩니다.

## <a name="configure-data-source-plugin"></a>데이터 원본 플러그 인 구성

성공적으로 로그인되면 첫 번째 데이터 원본을 추가하는 옵션이 표시됩니다.

![데이터 원본 추가](./media/grafana-plugin/add-data-source.png)

1. **데이터 원본 추가를** 선택하고 *Azure* 이름으로 필터링하고 **Azure Monitor** 데이터 원본을 선택합니다.

![Azure Monitor 데이터 원본](./media/grafana-plugin/azure-monitor-data-source-list.png)

2. 데이터 원본의 이름을 선택하고 인증을 위해 관리 ID 또는 앱 등록 중에서 선택합니다.

Grafana 인스턴스가 관리 ID를 사용하도록 설정된 Azure VM에서 호스트되는 경우 인증에 이 방법을 사용할 수 있습니다. 그러나 Grafana 인스턴스가 Azure에서 호스트되지 않거나 관리 ID를 사용하도록 설정하지 않은 경우 Azure 서비스 주체와 함께 앱 등록을 사용하여 인증을 설정해야 합니다.

### <a name="use-managed-identity"></a>관리형 ID 사용

1. VM에서 관리 ID를 사용하도록 설정하고 Grafana 서버 관리 ID 지원 설정을 true로 변경합니다.
    * 호스팅 VM의 관리 ID에는 Grafana에서 시각화할 구독, 리소스 그룹 또는 리소스에 대한 [모니터링 읽기 프로그램 역할이](/azure/azure-monitor/roles-permissions-security) 할당되어 있어야 합니다.
    * 또한 Grafana 서버 구성에서 'managed_identity_enabled = true' 설정을 업데이트해야 합니다. 자세한 내용은 [Grafana 구성을](https://grafana.com/docs/grafana/latest/administration/configuration/) 참조하세요. 두 단계가 모두 완료되면 액세스를 저장하고 테스트할 수 있습니다.

2. **저장 & 테스트를** 선택하면 Grafana에서 자격 증명을 테스트합니다. 다음과 비슷한 메시지가 표시됩니다.  
    
   ![Grafana 데이터 원본 관리 ID 구성 승인](./media/grafana-plugin/managed-identity.png)

### <a name="or-use-app-registration"></a>또는 앱 등록 사용

1. 서비스 주체를 만듭니다. Grafana는 Azure Monitor API에 연결하고 데이터를 수집하는 데 Azure Active Directory 서비스 주체를 사용합니다. Azure 리소스에 대한 액세스를 관리하려면 기존 서비스 주체를 사용하거나 새로 만들어야 합니다.
    * 서비스 주체를 만들려면 [이러한 지침](/azure/active-directory/develop/howto-create-service-principal-portal)을 참조하세요. 테넌트 ID(디렉터리 ID), 클라이언트 ID(애플리케이션 ID) 및 클라이언트 비밀(애플리케이션 키 값)을 복사하고 저장합니다.
    * 모니터링하려는 구독, 리소스 그룹 또는 리소스의 Azure Active Directory 애플리케이션에 [모니터링 읽기](/azure/azure-monitor/roles-permissions-security) 프로그램 역할을 할당하려면 역할에 애플리케이션 [할당을](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) 참조하세요.
  
2. 사용하려는 연결 세부 정보를 제공합니다.
    * 플러그 인을 구성하는 동안 플러그 인을 모니터링할 Azure 클라우드(공용, Azure 미국 정부, Azure 독일 또는 Azure 중국)를 지정할 수 있습니다.
        > [!NOTE]
        > 일부 데이터 원본 필드의 이름은 관련 Azure 설정과 다릅니다.
        > * 테넌트 ID는 Azure Directory ID입니다.
        > * 클라이언트 ID는 Azure Active Directory 애플리케이션 ID입니다.
        > * 클라이언트 비밀은 Azure Active Directory 애플리케이션 키 값입니다.

3. **저장 & 테스트를** 선택하면 Grafana에서 자격 증명을 테스트합니다. 다음과 비슷한 메시지가 표시됩니다.  
    
   ![Grafana 데이터 원본 앱 등록 구성 승인](./media/grafana-plugin/app-registration.png)

## <a name="use-azure-monitor-data-source-dashboards"></a>Azure Monitor 데이터 원본 대시보드 사용

Azure Monitor 플러그 인에는 시작하기 위해 가져올 수 있는 몇 가지 바로 가기 대시보드가 포함되어 있습니다.

1. 사용 가능한 **대시보드** 목록을 보려면 Azure Monitor 플러그 인의 대시보드 탭을 클릭합니다.

   ![데이터 원본 대시보드 Azure Monitor](./media/grafana-plugin/azure-data-source-dashboards.png)

2. **가져오기를** 클릭하여 대시보드를 다운로드합니다.

3. 가져온 대시보드의 이름을 클릭하여 엽니다.

4. 대시보드 맨 위에 있는 드롭다운 선택기를 사용하여 관심 있는 구독, 리소스 그룹 및 리소스를 선택합니다.

   ![Storage Insights 대시보드](./media/grafana-plugin/storage-insights-dashboard.png)

## <a name="build-grafana-dashboards"></a>Grafana 대시보드 빌드

1. Grafana 홈 페이지로 이동하여 **첫 번째 대시보드 만들기를** 선택합니다.

2. 새 대시보드에서 **빈 패널 추가를** 선택합니다.

3. 아래와 같이 쿼리 편집기가 있는 빈 *Time Series* 패널이 대시보드에 표시됩니다. 구성한 경우 Azure Monitor 데이터 원본을 선택합니다.
   * Azure Monitor 메트릭 수집 - 서비스 드롭다운에서 **메트릭을** 선택합니다. 선택 목록이 표시되면 이 차트에서 모니터링할 리소스 및 메트릭을 선택할 수 있습니다. VM에서 메트릭을 수집하려면 네임스페이스 **Microsoft.Compute/VirtualMachines** 를 사용합니다. VM 및 메트릭을 선택하고 나면 대시보드에서 해당 데이터 보기를 시작할 수 있습니다.
     ![Azure Monitor 메트릭에 대한 Grafana 그래프 구성](./media/grafana-plugin/metrics-config.png)
   * Azure Monitor 로그 데이터 수집 - 서비스 드롭다운에서 **로그를** 선택합니다. 쿼리하려는 리소스 또는 Log Analytics 작업 영역을 선택하고 쿼리 텍스트를 설정합니다. Azure Monitor 플러그 인을 사용하면 Log Analytics 작업 영역에서 또는 특정 리소스에 대한 로그를 쿼리할 수 있습니다. 아래 쿼리 편집기에서 이미 있는 로그 쿼리를 복사하거나 새로 만들 수 있습니다. 쿼리에 입력할 때 IntelliSense가 표시되어 자동 완성 옵션을 제안합니다. 마지막으로 시각화 유형인 Time **series** 를 선택하고 쿼리를 실행합니다.
    
     > [!NOTE]
     >
     > 플러그 인을 사용하여 제공된 기본 쿼리는 "$__timeFilter() 및 $__interval의 매크로 두 개를 사용합니다. 
     > 차트의 일부를 확대하는 경우 이러한 매크로를 통해 Grafana는 시간 범위 및 시간 세분화를 동적으로 계산할 수 있습니다. 이러한 매크로 제거하고 *TimeGenerated > ago(1h)* 같은 표준 시간 필터를 사용할 수 있습니다. 단, 그래프는 확대/축소 기능을 지원하지 않습니다.
    
     다음 예제에서는 모든 요청에 대한 평균 응답 시간에 대한 애플리케이션 Insights 리소스에서 실행되는 쿼리를 보여 니다.

     ![Azure Log Analytics를 위한 Grafana 그래프 구성](./media/grafana-plugin/logs-config.png)

    * 위에 표시된 메트릭 및 로그 쿼리 외에도 Azure Monitor 플러그 인은 [Azure Resource Graph](/azure/governance/resource-graph/concepts/explore-resources) 쿼리를 지원합니다.

## <a name="advanced-grafana-features"></a>고급 Grafana 기능

### <a name="variables"></a>변수
UI 드롭다운을 통해 대시보드 사용자가 일부 리소스 및 쿼리 값을 선택하고 리소스 또는 쿼리에서 업데이트할 수 있습니다.
Log Analytics 작업 영역의 사용량을 보여 주는 다음 쿼리를 예로 들어 보겠습니다.
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

사용 가능한 모든 작업 영역을 나열하는 **변수를** 구성한 다음, 사용자 선택에 따라 쿼리되는 리소스를 업데이트할 수 있습니다.
새 변수를 만들려면 오른쪽 위 영역에서 대시보드의 설정 단추를 클릭하고 **변수**, **새로 만들기** 를 차례로 선택합니다.
변수 페이지에서 값 목록을 가져오기 위해 실행할 데이터 원본 및 쿼리를 정의합니다.

![Grafana 변수 정의](./media/grafana-plugin/define-variable.png)

만든 후에는 선택한 값을 사용하도록 쿼리에 대한 리소스를 변경하면 차트가 그에 따라 응답합니다.

![변수를 가진 쿼리](./media/grafana-plugin/query-with-variable.png)

Azure Monitor 플러그 인에서 사용할 수 있는 [템플릿 변수의](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/template-variables/) 전체 목록을 참조하세요.

### <a name="create-dashboard-playlists"></a>대시보드 재생 목록 만들기

Grafana의 많은 유용한 기능 중 하나는 대시보드 재생 목록입니다. 여러 개의 대시보드를 만들고 각 대시보드에서 표시할 간격을 구성하여 재생 목록에 추가할 수 있습니다. 대시보드 메뉴 항목으로 이동하고 **재생 목록을** 선택하여 순환할 기존 대시보드의 재생 목록을 만듭니다. 그룹에 상태 보드를 제공하기 위해 대형 벽 모니터에 표시할 수도 있습니다.

![Grafana 재생 목록 예제](./media/grafana-plugin/playlist.png)

## <a name="clean-up-resources"></a>리소스 정리

Azure에서 Grafana 환경 설정한 경우 사용자가 VM을 사용 중인지 여부에 상관 없이 VM이 실행되면 요금이 청구됩니다. 추가 비용이 발생하지 않도록 방지하려면 이 문서에서 만든 리소스 그룹을 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 클릭한 다음 **Grafana** 를 클릭합니다.
2. 리소스 그룹 페이지에서 **삭제** 를 클릭하고, 텍스트 상자에서 **Grafana** 를 입력한 다음, **삭제** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계
* [Azure Monitor 메트릭 및 로그 비교](../data-platform.md)
