---
title: Azure에서 RHEL 가상 머신의 SQL Server에 대한 가용성 그룹 수신기 구성 - Linux 가상 머신 | Microsoft Docs
description: Azure에서 가용성 그룹 수신기를 RHEL 가상 머신의 SQL Server에 설정하는 방법을 알아봅니다.
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.date: 03/11/2020
ms.openlocfilehash: 502b70c9cceaabb5eaafd35e132ab6388c1561e3
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133368490"
---
# <a name="tutorial-configure-an-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>자습서: Azure에서 RHEL 가상 머신의 SQL Server에 대한 가용성 그룹 수신기 구성
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> 제공된 자습서는 **공개 미리 보기** 에 있습니다. 
>
> 이 자습서에서는 RHEL 7.6에서 SQL Server 2017을 사용하지만, RHEL 7 또는 RHEL 8에서 SQL Server 2019를 사용하여 고가용성을 구성할 수 있습니다. 가용성 그룹 리소스를 구성하는 명령이 RHEL 8에서 변경되었습니다. 올바른 명령에 대한 자세한 내용은 [가용성 그룹 리소스 만들기](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) 및 RHEL 8 리소스 문서를 참조하세요.

이 자습서에서는 Azure의 RHEL VM(가상 머신)에서 SQL Server에 대한 가용성 그룹 수신기를 만드는 방법에 대한 단계를 설명합니다. 다음 방법을 알게 됩니다.

> [!div class="checklist"]
> - Azure Portal에서 부하 분산 장치 만들기
> - 부하 분산 장치에 대한 백 엔드 풀 구성
> - 부하 분산 장치에 대한 프로브 만들기
> - 부하 분산 규칙 설정
> - 클러스터에 부하 분산 장치 리소스 만들기
> - 가용성 그룹 수신기 만들기
> - 수신기에 대한 연결 테스트
> - 장애 조치 테스트

## <a name="prerequisite"></a>필수 요소

[자습서: Azure에서 RHEL 가상 머신의 SQL Server에 대한 가용성 그룹 구성](rhel-high-availability-stonith-tutorial.md)을 완료합니다.

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Azure Portal에서 부하 분산 장치 만들기

다음 지침에서는 [부하 분산 장치 - Azure Portal](../windows/availability-group-load-balancer-portal-configure.md) 문서의 [Azure Portal에서 부하 분산 장치 만들기 및 구성](../windows/availability-group-load-balancer-portal-configure.md#create--configure-load-balancer) 섹션에 나오는 1~4단계를 안내합니다.

### <a name="create-the-load-balancer"></a>부하 분산 장치 만들기

1. Azure 포털에서 SQL Server 가상 머신을 포함하는 리소스 그룹을 엽니다. 

2. 리소스 그룹에서 **추가** 를 클릭합니다.

3. **부하 분산 장치** 를 검색한 후 검색 결과에서 **Microsoft** 에서 게시하는 **부하 분산 장치** 를 선택합니다.

4. **부하 분산 장치** 블레이드에서 **만들기** 를 클릭합니다.

5. **부하 분산 장치 만들기** 대화 상자에서 다음과 같이 부하 분산 장치를 구성합니다.

   | 설정 | 값 |
   | --- | --- |
   | **이름** |부하 분산 장치를 나타내는 텍스트 이름입니다. 예를 들어 **sqlLB** 입니다. |
   | **형식** |**내부** |
   | **가상 네트워크** |이전에 만든 기본 가상 머신의 이름은 **VM1VNET** 입니다. |
   | **서브넷** |SQL Server 인스턴스가 있는 서브넷을 선택합니다. 기본값은 **VM1Subnet** 입니다.|
   | **IP 주소 할당** |**정적** |
   | **개인 IP 주소** |클러스터에서 만든 `virtualip` IP 주소를 사용합니다. |
   | **구독** |리소스 그룹에 사용된 구독을 사용합니다. |
   | **리소스 그룹** |SQL Server 인스턴스가 있는 리소스 그룹을 선택합니다. |
   | **위치** |SQL Server 인스턴스가 있는 Azure 위치를 선택합니다. |

### <a name="configure-the-back-end-pool"></a>백 엔드 풀 구성
Azure에서 백 엔드 주소 풀 *백 엔드 풀* 이 호출됩니다. 이 경우 백 엔드 풀은 가용성 그룹에 있는 세 개의 SQL Server 인스턴스 주소입니다. 

1. 리소스 그룹에서, 만든 부하 분산 장치를 클릭합니다. 

2. **설정** 에서 **백 엔드 풀** 을 클릭합니다.

3. **백 엔드 풀** 에서 **추가** 를 클릭하여 백 엔드 주소 풀을 만듭니다. 

4. **백 엔드 풀 추가** 에서 **이름** 에 백 엔드 풀의 이름을 입력합니다.

5. **다음에 연결됨** 아래에서 **가상 머신** 을 선택합니다. 

6. 환경의 각 가상 머신을 선택하고, 적절한 IP 주소를 각 선택 항목에 연결합니다.

    :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-backend-pool.png" alt-text="백 엔드 풀 추가":::

7. **추가** 를 클릭합니다. 

### <a name="create-a-probe"></a>프로브 만들기

프로브는 Azure에서 현재 가용성 그룹 수신기를 소유하는 SQL Server 인스턴스를 확인하는 방법을 정의합니다. Azure는 프로브를 만들 때 정의한 포트의 IP 주소를 기반으로 서비스를 프로브합니다.

1. 부하 분산 장치 **설정** 블레이드에서 **상태 프로브** 를 클릭합니다. 

2. **상태 프로브** 블레이드에서 **추가** 를 클릭합니다.

3. **프로브 추가** 블레이드에서 프로브를 구성합니다. 다음 값을 사용하여 프로브를 구성합니다.

   | 설정 | 값 |
   | --- | --- |
   | **이름** |프로브를 나타내는 텍스트 이름입니다. 예를 들어 **SQLAlwaysOnEndPointProbe** 입니다. |
   | **프로토콜** |**TCP** |
   | **포트** |사용 가능한 모든 포트를 사용할 수 있습니다. 예를 들어 *59999* 입니다. |
   | **간격** |*5* |
   | **비정상 임계값** |*2* |

4.  **확인** 을 클릭합니다. 

5. 모든 가상 머신에 로그인하고 다음 명령을 사용하여 프로브 포트를 엽니다.

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Azure는 프로브를 만든 후 가용성 그룹에 대한 수신기가 있는 SQL Server 인스턴스를 테스트하는 데 프로브를 사용합니다.

### <a name="set-the-load-balancing-rules"></a>부하 분산 규칙 설정

부하 분산 규칙은 부하 분산 장치가 트래픽을 SQL Server 인스턴스로 라우트하는 방법을 구성합니다. 이 부하 분산 장치의 경우 세 개의 SQL Server 인스턴스 중 하나만 가용성 그룹 수신기 리소스를 한 번에 소유하므로 DSR(Direct Server Return)을 사용하도록 설정합니다.

1. 부하 분산 장치 **설정** 블레이드에서 **부하 분산 규칙** 을 클릭합니다. 

2. **부하 분산 규칙** 블레이드에서 **추가** 를 클릭합니다.

3. **부하 분산 규칙 추가** 블레이드에서 부하 분산 규칙을 구성합니다. 다음 설정을 사용합니다. 

   | 설정 | 값 |
   | --- | --- |
   | **이름** |부하 분산 규칙을 나타내는 텍스트 이름입니다. 예를 들어 **SQLAlwaysOnEndPointListener** 입니다. |
   | **프로토콜** |**TCP** |
   | **포트** |*1433* |
   | **백 엔드 포트** |*1433*. 이 규칙은 **부동 IP(Direct Server Return)** 를 사용하므로 이 값은 무시됩니다. |
   | **프로브** |이 부하 분산 장치에 대해 만든 프로브의 이름을 사용합니다. |
   | **세션 지속성** |**없음** |
   | **유휴 제한 시간(분)** |*4* |
   | **부동 IP(Direct Server Return)** |**Enabled** |

   :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-load-balancing-rule.png" alt-text="부하 분산 규칙 추가":::

4. **확인** 을 클릭합니다. 
5. Azure에서 부하 분산 규칙이 구성됩니다. 이제 가용성 그룹에 대한 수신기를 호스트하는 SQL Server 인스턴스로 트래픽을 라우트하도록 부하 분산 장치가 구성되었습니다. 

이 시점에서 리소스 그룹에는 모든 SQL Server 머신에 연결하는 부하 분산 장치가 있습니다. 부하 분산 장치에는 SQL Server Always On 가용성 그룹 수신기에 대한 IP 주소도 있으므로 모든 머신에서 가용성 그룹에 대한 요청에 응답할 수 있습니다.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>클러스터에 부하 분산 장치 리소스 만들기

1. 기본 가상 머신에 로그인합니다. Azure 부하 분산 장치 프로브 포트(이 예제에서는 59999가 사용됨)를 사용하도록 설정하기 위한 리소스를 만들어야 합니다. 다음 명령을 실행합니다.

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. `virtualip` 및 `azure_load_balancer` 리소스가 포함된 그룹을 만듭니다.

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>제약 조건 추가

1. Azure 부하 분산 장치 IP 주소와 AG 리소스가 동일한 노드에서 실행되도록 공동 배치 제약 조건을 구성해야 합니다. 다음 명령을 실행합니다.

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. AG 리소스가 시작되어 실행되도록 하는 순서 지정 제약 조건을 Azure 부하 분산 장치 IP 주소 앞에 만듭니다. 공동 배치 제약 조건은 정렬 제약 조건을 암시하지만 정렬 제약 조건이 적용됩니다.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. 제약 조건을 확인하려면 다음 명령을 실행합니다.

    ```bash
    sudo pcs constraint list --full
    ```

    다음 출력이 표시됩니다.

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>가용성 그룹 수신기 만들기

1. 주 노드의 SQLCMD 또는 SSMS에서 다음 명령을 실행합니다.

    - 아래에 사용된 IP 주소를 `virtualip` IP 주소로 바꿉니다.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. 각 VM 노드에 로그인합니다. 다음 명령을 사용하여 호스트 파일을 열고, 각 머신에서 `ag1-listener`에 대한 호스트 이름 확인을 설정합니다.

    ```
    sudo vi /etc/hosts
    ```

    **vi** 편집기에서 텍스트를 삽입할 `i`를 입력하고 빈 줄에 `ag1-listener`의 IP를 추가합니다. 그런 다음, IP 옆의 공백 뒤에 `ag1-listener`를 추가합니다.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    **vi** 편집기를 종료하려면 먼저 **Esc** 키를 누른 다음, `:wq` 명령을 입력하여 파일을 작성하고 종료합니다. 각 노드에서 이 작업을 수행합니다.

## <a name="test-the-listener-and-a-failover"></a>수신기 및 장애 조치 테스트

### <a name="test-logging-in-to-sql-server-using-the-availability-group-listener"></a>가용성 그룹 수신기를 사용하여 SQL Server에 로그인 테스트

1. SQLCMD를 통해 가용성 그룹 수신기 이름을 사용하여 SQL Server의 기본 노드에 로그인합니다.

    - 이전에 만든 로그인을 사용하고 `<YourPassword>`를 올바른 암호로 바꿉니다. 아래 예제에서는 SQL Server로 사용하여 만든 `sa` 로그인을 사용합니다.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. 연결된 서버의 이름을 확인합니다. SQLCMD에서 다음 명령을 실행합니다.

    ```sql
    SELECT @@SERVERNAME
    ```

    출력에 현재 주 노드가 표시됩니다. 장애 조치를 테스트한 적이 없는 경우 이 노드는 `VM1`입니다.

    `exit` 명령을 입력하여 SQL Server 세션을 종료합니다.

### <a name="test-a-failover"></a>장애 조치 테스트

1. 다음 명령을 실행하여 기본 복제본을 `<VM2>` 또는 다른 복제본으로 수동으로 장애 조치(failover)합니다. `<VM2>`를 서버 이름의 값으로 바꿉니다.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. 제약 조건을 확인하면 수동 장애 조치로 인해 다른 제약 조건이 추가되었음을 알 수 있습니다.

    ```bash
    sudo pcs constraint list --full
    ```

    ID가 `cli-prefer-ag_cluster-master`인 제약 조건이 추가되었습니다.

1. 다음 명령을 사용하여 ID가 `cli-prefer-ag_cluster-master`인 제약 조건을 제거합니다.

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. `sudo pcs resource` 명령을 사용하여 클러스터 리소스를 확인합니다. 그러면 이제 주 인스턴스가 `<VM2>`임을 알 수 있습니다.

    > [!NOTE]
    > 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *슬레이브* 용어에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.


    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. SQLCMD를 통해 수신기 이름을 사용하여 기본 복제본에 로그인합니다.

    - 이전에 만든 로그인을 사용하고 `<YourPassword>`를 올바른 암호로 바꿉니다. 아래 예제에서는 SQL Server로 사용하여 만든 `sa` 로그인을 사용합니다.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
     ```

1. 연결된 서버를 확인합니다. SQLCMD에서 다음 명령을 실행합니다.

    ```sql
    SELECT @@SERVERNAME
    ```

    이제 장애 조치한 VM에 연결되었습니다.

## <a name="next-steps"></a>다음 단계

Azure의 부하 분산 장치에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure VM의 SQL Server에서 가용성 그룹에 대한 부하 분산 구성](../windows/availability-group-load-balancer-portal-configure.md)
