---
title: Grafana를 사용하여 Azure 서비스 및 애플리케이션 모니터링
description: Azure Monitor 및 Application Insights 데이터를 라우팅하여 Grafana에서 볼 수 있도록 합니다.
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 1bfb3322dda2b6702eb3e18eb44cc0cb2cd942ec
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113666864"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Grafana에서 Azure 서비스 모니터링
이제 [Azure Monitor 데이터 원본 플러그 인](https://grafana.com/plugins/grafana-azure-monitor-datasource)을 사용하여 [Grafana](https://grafana.com/)에서 Azure 서비스 및 애플리케이션을 모니터링할 수 있습니다. 플러그 인은 다양한 로그 및 메트릭을 포함하여 Azure Monitor에서 수집된 애플리케이션 성능 데이터를 수집합니다. 그런 다음 Grafana 대시보드에 이 데이터를 표시할 수 있습니다.

Grafana 서버를 설정하고 Azure Monitor에서 메트릭 및 로그에 대한 대시보드를 빌드하려면 다음 단계를 사용합니다.

## <a name="set-up-a-grafana-server"></a>Grafana 서버 설정

### <a name="set-up-grafana-locally"></a>Grafana를 로컬로 설정
Grafana 서버를 로컬로 설정하려면 [로컬 환경에서 Grafana를 다운로드하여 설치](https://grafana.com/grafana/download)합니다. 플러그 인의 Azure Monitor 통합을 사용하려면 Grafana 버전 5.3 이상을 설치합니다.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Azure Marketplace를 통해 Azure에서 Grafana 설정
1. Azure Marketplace로 이동하여 Grafana Labs에서 Grafana를 선택합니다.

2. 이름 및 세부 정보를 입력합니다. 새 리소스 그룹 만들기 VM 사용자 이름, VM 암호 및 Grafana 서버 관리자 암호에 대해 선택한 값을 추적합니다.  

3. VM 크기 및 스토리지 계정을 선택합니다.

4. 네트워크 구성 설정을 구성합니다.

5. 요약을 보고 사용 약관에 동의한 후 **만들기** 를 선택합니다.

6. 배포가 완료되면 **리소스 그룹으로 이동** 을 선택합니다. 새로 만든 리소스의 목록이 표시됩니다.

    ![Grafana 리소스 그룹 개체](media/grafana-plugin/grafana1.png)

    네트워크 보안 그룹(이 경우 *grafana-nsg*)을 선택하면 3000 포트가 Grafana 서버에 액세스하는 데 사용됨을 알 수 있습니다.

7. Grafana 서버의 공용 IP 주소를 가져옵니다. 리소스의 목록으로 돌아가서 **공용 IP 주소** 를 선택합니다.

## <a name="sign-in-to-grafana"></a>Grafana에 로그인

> [!IMPORTANT]
> Internet Explorer 브라우저 및 이전 Microsoft Edge 브라우저는 Grafana와 호환되지 않으므로 Microsoft Edge를 포함한 chromium 기반 브라우저를 사용해야 합니다. [Grafana에 대해 지원되는 브라우저](https://grafana.com/docs/grafana/latest/installation/requirements/#supported-web-browsers)를 참조하세요.

1. 서버의 IP 주소를 사용하여 브라우저에서 *http://\<IP address\>:3000* 또는 *\<DNSName>\:3000* 의 로그인 페이지를 엽니다. 3000은 기본 포트이지만, 설정하는 동안 다른 포트를 설치했을 수도 있습니다. 빌드한 Grafana 서버에 대한 로그인 페이지가 표시됩니다.

    ![Grafana 로그인 화면](./media/grafana-plugin/grafana-login-screen.png)

2. 사용자 이름 *admin* 및 이전에 만든 Grafana 서버 관리자 암호를 사용하여 로그인합니다. 로컬 설정을 사용하는 경우 기본 암호는 *admin* 이며 첫 번째 로그인에서 변경하라는 메시지가 표시됩니다.

## <a name="configure-data-source-plugin"></a>데이터 원본 플러그 인 구성

성공적으로 로그인하면 Azure Monitor 데이터 원본 플러그 인이 이미 포함되어 있는지 확인해야 합니다.

![Grafana에 Azure Monitor 플러그 인이 포함됨](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. **데이터 원본 추가** 를 선택하여 Azure Monitor 데이터 원본을 추가하고 구성합니다.

2. 데이터 원본에 사용할 이름을 선택하고 드롭다운에서 **Azure Monitor** 를 유형으로 선택합니다.

3. 서비스 주체를 만듭니다. Grafana는 Azure Monitor API에 연결하고 데이터를 수집하는 데 Azure Active Directory 서비스 주체를 사용합니다. Azure 리소스에 대한 액세스를 관리하려면 기존 서비스 주체를 사용하거나 새로 만들어야 합니다.
    * 서비스 주체를 만들려면 [이러한 지침](../../active-directory/develop/howto-create-service-principal-portal.md)을 참조하세요. 테넌트 ID(디렉터리 ID), 클라이언트 ID(애플리케이션 ID) 및 클라이언트 비밀(애플리케이션 키 값)을 복사하고 저장합니다.
    * [역할에 애플리케이션 할당](../../active-directory/develop/howto-create-service-principal-portal.md)을 참조하여 모니터링할 구독, 리소스 그룹 또는 리소스의 Azure Active Directory 애플리케이션에 읽기 권한자 역할을 할당합니다. 
    Log Analytics API에는 읽기 권한자 역할의 사용 권한을 포함하며 여기에 추가하는 [Log Analytics Reader 역할](../../role-based-access-control/built-in-roles.md#log-analytics-reader)이 필요합니다.

4. 사용하려는 API에 연결 세부 정보를 제공합니다. 전부 또는 일부에만 연결할 수 있습니다. 
    * Azure Monitor에서 메트릭과 로그 모두에 연결하는 경우 **Azure Monitor API와 동일한 세부 사항** 을 선택하여 동일한 자격 증명을 재사용할 수 있습니다.
    * 플러그 인을 구성하는 동안 플러그 인을 모니터링할 Azure 클라우드(공용, Azure 미국 정부, Azure 독일 또는 Azure 중국)를 지정할 수 있습니다.
    * Application Insights를 사용하는 경우 Application Insights API 및 애플리케이션 ID를 포함하여 Application Insights 기반 메트릭을 수집할 수도 있습니다. 자세한 내용은 [API 키 및 애플리케이션 ID 가져오기](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)를 참조하세요.

        > [!NOTE]
        > 일부 데이터 원본 필드의 이름은 관련 Azure 설정과 다릅니다.
        > * 테넌트 ID는 Azure Directory ID입니다.
        > * 클라이언트 ID는 Azure Active Directory 애플리케이션 ID입니다.
        > * 클라이언트 비밀은 Azure Active Directory 애플리케이션 키 값입니다.

5. Application Insights를 사용하는 경우 Application Insights API 및 애플리케이션 ID를 포함하여 Application Insights 기반 메트릭을 수집할 수도 있습니다. 자세한 내용은 [API 키 및 애플리케이션 ID 가져오기](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID)를 참조하세요.

6. **저장** 을 선택하면 Grafana에서 각 API에 대한 자격 증명을 테스트합니다. 다음과 비슷한 메시지가 표시됩니다.  
    ![Grafana 데이터 원본 구성 승인됨](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Grafana 대시보드 빌드

1. Grafana 홈 페이지로 이동하고 **새 대시보드** 를 선택합니다.

2. 새 대시보드에서 **그래프** 를 선택합니다. 다른 차트 옵션을 사용해 볼 수도 있지만 이 문서에서는 *그래프* 를 예로 사용합니다.

3. 빈 그래프가 대시보드에 표시됩니다. 패널 제목을 클릭하고 **편집** 을 선택하여 이 그래프 차트에 그리려는 데이터의 세부 정보를 입력합니다.
    ![Grafana 새 그래프](./media/grafana-plugin/grafana-new-graph-dark.png)

4. 구성한 경우 Azure Monitor 데이터 원본을 선택합니다.
   * Azure Monitor 메트릭 수집 - 서비스 드롭다운에서 **Azure Monitor** 를 선택합니다. 선택 목록이 표시되면 이 차트에서 모니터링할 리소스 및 메트릭을 선택할 수 있습니다. VM에서 메트릭을 수집하려면 네임스페이스 **Microsoft.Compute/VirtualMachines** 를 사용합니다. VM 및 메트릭을 선택하고 나면 대시보드에서 해당 데이터 보기를 시작할 수 있습니다.
     ![Azure Monitor를 위한 Grafana 그래프 구성](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Azure Monitor 로그 데이터 수집 - 서비스 드롭다운에서 **Azure Log Analytics** 를 선택합니다. 쿼리할 작업 영역을 선택하고 쿼리 텍스트를 설정합니다. 이미 가지고 있는 로그 쿼리를 여기에 복사하거나 새로 만들 수 있습니다. 쿼리에 입력할 때 IntelliSense가 표시되어 자동 완성 옵션을 제안합니다. 시각화 유형인 **시계열** **테이블** 을 선택하고 쿼리를 실행합니다.
    
     > [!NOTE]
     >
     > 플러그 인을 사용하여 제공된 기본 쿼리는 "$__timeFilter() 및 $__interval의 매크로 두 개를 사용합니다. 
     > 차트의 일부를 확대하는 경우 이러한 매크로를 통해 Grafana는 시간 범위 및 시간 세분화를 동적으로 계산할 수 있습니다. 이러한 매크로 제거하고 *TimeGenerated > ago(1h)* 같은 표준 시간 필터를 사용할 수 있습니다. 단, 그래프는 확대/축소 기능을 지원하지 않습니다.
    
     ![Azure Log Analytics를 위한 Grafana 그래프 구성](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. 다음은 두 개의 차트가 있는 간단한 대시보드입니다. 왼쪽 차트에는 두 VM의 CPU 백분율이 표시됩니다. 오른쪽 차트에는 트랜잭션 API 유형별로 분류된 Azure Storage 계정의 트랜잭션이 표시됩니다.
    ![두 Grafana 차트 예제](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>선택 사항: 동일한 Grafana 서버에서 사용자 지정 메트릭 모니터링

Telegraf 및 InfluxDB를 설치하여 동일한 Grafana 인스턴스에서 사용자 지정 및 에이전트 기반 메트릭을 수집하고 그릴 수도 있습니다. 대시보드에서 이러한 메트릭을 함께 가져오는 데 사용할 수 있는 많은 데이터 원본 플러그 인이 있습니다.

또한 이 설정을 다시 사용하여 Prometheus 서버의 메트릭을 포함할 수도 있습니다. Grafana의 플러그 인 갤러리에서 Prometheus 데이터 원본 플러그 인을 사용합니다.

다음은 Telegraf, InfluxDB, Prometheus 및 Docker를 사용하는 방법에 대한 좋은 참조 문서입니다.
 - [Ubuntu 16.04에서 TICK 스택을 사용하여 시스템 메트릭을 모니터링하는 방법](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Docker 호스트, 컨테이너 및 컨테이너화된 서비스에 대한 모니터링 솔루션](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

다음은 Azure Monitor 및 Application Insights의 메트릭이 포함된 전체 Grafana 대시보드에 대한 이미지입니다.
![Grafana 예제 메트릭](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>고급 Grafana 기능

### <a name="variables"></a>변수
일부 쿼리 값은 UI 드롭다운을 통해 선택하고 쿼리에서 업데이트할 수 있습니다. 한 예로 다음 쿼리를 고려해 보세요.
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

사용 가능한 모든 **솔루션** 값을 나열하는 변수를 구성한 다음, 이를 사용하도록 쿼리를 업데이트할 수 있습니다.
새 변수를 만들려면 오른쪽 위 영역에서 대시보드의 설정 단추를 클릭하고 **변수**, **새로 만들기** 를 차례로 선택합니다.
변수 페이지에서 값 목록을 가져오기 위해 실행할 데이터 원본 및 쿼리를 정의합니다.
![Grafana 구성 변수](./media/grafana-plugin/grafana-configure-variable-dark.png)

만든 후 선택한 값을 사용하도록 쿼리를 조정하면 차트가 그에 따라 응답합니다.
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![변수를 사용하는 Grafana](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>대시보드 재생 목록 만들기

Grafana의 많은 유용한 기능 중 하나는 대시보드 재생 목록입니다. 여러 개의 대시보드를 만들고 각 대시보드에서 표시할 간격을 구성하여 재생 목록에 추가할 수 있습니다. **재생** 을 선택하여 대시보드가 순환하는지 확인합니다. 그룹에 상태 보드를 제공하기 위해 대형 벽 모니터에 표시할 수도 있습니다.

![Grafana 재생 목록 예제](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>리소스 정리

Azure에서 Grafana 환경 설정한 경우 사용자가 VM을 사용 중인지 여부에 상관 없이 VM이 실행되면 요금이 청구됩니다. 추가 비용이 발생하지 않도록 방지하려면 이 문서에서 만든 리소스 그룹을 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 클릭한 다음 **Grafana** 를 클릭합니다.
2. 리소스 그룹 페이지에서 **삭제** 를 클릭하고, 텍스트 상자에서 **Grafana** 를 입력한 다음, **삭제** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계
* [Azure Monitor 메트릭 개요](../data-platform.md)
