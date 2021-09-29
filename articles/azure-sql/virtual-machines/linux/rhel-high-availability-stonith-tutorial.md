---
title: Azure에서 RHEL 가상 머신의 SQL Server에 대한 가용성 그룹 구성 - Linux 가상 머신 | Microsoft Docs
description: RHEL 클러스터 환경에서 고가용성을 설정하고 STONITH를 설정하는 방법을 알아봅니다.
ms.service: virtual-machines-sql
ms.subservice: ''
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 06/25/2020
ms.openlocfilehash: 66e07130084b6a17521c215e8727df2bfd5ac326
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661358"
---
# <a name="tutorial-configure-availability-groups-for-sql-server-on-rhel-virtual-machines-in-azure"></a>자습서: Azure에서 RHEL 가상 머신의 SQL Server에 대한 가용성 그룹 구성 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> 이 자습서에서는 RHEL 7.6에서 SQL Server 2017을 사용하지만, RHEL 7 또는 RHEL 8에서 SQL Server 2019를 사용하여 고가용성을 구성할 수 있습니다. RHEL 8에서 pacemake 클러스터 및 가용성 그룹 리소스를 구성하는 명령이 변경되었습니다. 올바른 명령에 대한 자세한 내용은 문서 [가용성 그룹 리소스 만들기](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) 및 RHEL 8 리소스를 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> - 새 리소스 그룹, 가용성 집합 및 Linux VM(가상 머신) 만들기
> - HA(고가용성)를 사용하도록 설정
> - Pacemaker 클러스터 만들기
> - STONITH 디바이스를 만들어 펜싱 에이전트 구성
> - RHEL에 SQL Server 및 mssql-tools 설치
> - SQL Server Always On 가용성 그룹 구성
> - Pacemaker 클러스터에서 AG(가용성 그룹) 리소스 구성
> - 장애 조치 및 펜싱 에이전트 테스트

이 자습서에서는 Azure CLI를 사용하여 리소스를 Azure에 배포합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.30 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

둘 이상의 구독이 있는 경우 이러한 리소스를 배포하려는 [구독을 설정](/cli/azure/manage-azure-subscriptions-azure-cli)합니다.

다음 명령을 사용하여 `<resourceGroupName>` 리소스 그룹을 지역에 만듭니다. `<resourceGroupName>`을 선택한 이름으로 바꿉니다. 이 자습서에서는 `East US 2`를 사용합니다. 자세한 내용은 이 [빠른 시작](../../../application-gateway/quick-create-cli.md)을 참조하세요.

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus2
```

## <a name="create-an-availability-set"></a>가용성 집합 만들기

다음 단계는 가용성 집합을 만드는 것입니다. Azure Cloud Shell에서 다음 명령을 실행하고, `<resourceGroupName>`을 리소스 그룹 이름으로 바꿉니다. `<availabilitySetName>`에 대한 이름을 선택합니다.

```azurecli-interactive
az vm availability-set create \
    --resource-group <resourceGroupName> \
    --name <availabilitySetName> \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

명령이 완료되면 다음 결과를 얻습니다.

```output
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/availabilitySets/<availabilitySetName>",
  "location": "eastus2",
  "name": "<availabilitySetName>",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 2,
  "proximityPlacementGroup": null,
  "resourceGroup": "<resourceGroupName>",
  "sku": {
    "capacity": null,
    "name": "Aligned",
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```

## <a name="create-rhel-vms-inside-the-availability-set"></a>가용성 집합 내에 RHEL VM 만들기

> [!WARNING]
> PAYG(종량제) RHEL 이미지를 선택하고 HA(고가용성)를 구성하는 경우 구독을 등록해야 할 수 있습니다. 이 경우 VM의 Microsoft Azure RHEL 구독과 Red Hat에 대한 구독과 관련된 요금이 청구되므로 구독에 대해 두 번 지불할 수 있습니다. 자세한 내용은 https://access.redhat.com/solutions/2458541 을 참조하세요.
>
> "이중 청구"되지 않도록 하려면 Azure VM을 만들 때 RHEL HA 이미지를 사용합니다. RHEL-HA 이미지로 제공되는 이미지도 HA 리포지토리를 사용하도록 미리 설정된 PAYG 이미지입니다.

1. HA를 사용하는 RHEL을 제공하는 VM(가상 머신) 이미지 목록을 가져옵니다.

    ```azurecli-interactive
    az vm image list --all --offer "RHEL-HA"
    ```

    다음 결과가 보일 것입니다.

    ```output
    [
      {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.4",
    "urn": "RedHat:RHEL-HA:7.4:7.4.2019062021",
    "version": "7.4.2019062021"
       },
       {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.5",
    "urn": "RedHat:RHEL-HA:7.5:7.5.2019062021",
    "version": "7.5.2019062021"
        },
        {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "7.6",
    "urn": "RedHat:RHEL-HA:7.6:7.6.2019062019",
    "version": "7.6.2019062019"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.0",
    "urn": "RedHat:RHEL-HA:8.0:8.0.2020021914",
    "version": "8.0.2020021914"
         },
         {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "8.1",
    "urn": "RedHat:RHEL-HA:8.1:8.1.2020021914",
    "version": "8.1.2020021914"
          },
          {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "80-gen2",
    "urn": "RedHat:RHEL-HA:80-gen2:8.0.2020021915",
    "version": "8.0.2020021915"
           },
           {
    "offer": "RHEL-HA",
    "publisher": "RedHat",
    "sku": "81_gen2",
    "urn": "RedHat:RHEL-HA:81_gen2:8.1.2020021915",
    "version": "8.1.2020021915"
           }
    ]
    ```

    이 자습서에서는 RHEL 7 예제에 대해 이미지 `RedHat:RHEL-HA:7.6:7.6.2019062019`를 선택하고 RHEL 8 예제에 대해 `RedHat:RHEL-HA:8.1:8.1.2020021914`를 선택합니다.
    
    RHEL8-HA 이미지에 미리 설치된 SQL Server 2019를 선택할 수도 있습니다. 이러한 이미지 목록을 가져오려면 다음 명령을 실행합니다.  
    
    ```azurecli-interactive
    az vm image list --all --offer "sql2019-rhel8"
    ```

    다음 결과가 보일 것입니다.

    ```output
    [
      {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200317",
    "version": "15.0.200317"
       },
       }
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "enterprise",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:enterprise:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "sqldev",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:sqldev:15.0.200512",
    "version": "15.0.200512"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200317",
    "version": "15.0.200317"
       },
       {
    "offer": "sql2019-rhel8",
    "publisher": "MicrosoftSQLServer",
    "sku": "standard",
    "urn": "MicrosoftSQLServer:sql2019-rhel8:standard:15.0.200512",
    "version": "15.0.200512"
       }
    ]
    ```

    위의 이미지 중 하나를 사용하여 가상 머신을 만드는 경우 SQL Server 2019가 미리 설치되어 있습니다. 이 문서에 설명된 대로 [SQL Server 및 mssql-tools 설치](#install-sql-server-and-mssql-tools) 섹션을 건너뜁니다.
    
    
    > [!IMPORTANT]
    > 가용성 그룹을 설정하려면 머신 이름이 15자 미만이어야 합니다. 사용자 이름은 대문자를 포함할 수 없으며, 암호는 13자 이상이어야 합니다.

1. 3개의 VM을 가용성 집합에 만들려고 합니다. 아래 명령에서 다음 항목을 바꿉니다.

    - `<resourceGroupName>`
    - `<VM-basename>`
    - `<availabilitySetName>`
    - `<VM-Size>` - 예를 들어 "Standard_D16_v3"입니다.
    - `<username>`
    - `<adminPassword>`

    ```azurecli-interactive
    for i in `seq 1 3`; do
           az vm create \
             --resource-group <resourceGroupName> \
             --name <VM-basename>$i \
             --availability-set <availabilitySetName> \
             --size "<VM-Size>"  \
             --image "RedHat:RHEL-HA:7.6:7.6.2019062019" \
             --admin-username "<username>" \
             --admin-password "<adminPassword>" \
             --authentication-type all \
             --generate-ssh-keys
    done
    ```

위의 명령은 VM을 만들고, 해당 VM에 대한 기본 VNet을 만듭니다. 다른 구성에 대한 자세한 내용은 [az vm create](/cli/azure/vm) 문서를 참조하세요.

각 VM에 대한 명령이 완료되면 다음과 비슷한 결과를 얻습니다.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<VM1>",
  "location": "eastus2",
  "macAddress": "<Some MAC address>",
  "powerState": "VM running",
  "privateIpAddress": "<IP1>",
  "publicIpAddress": "",
  "resourceGroup": "<resourceGroupName>",
  "zones": ""
}
```

> [!IMPORTANT]
> 위의 명령으로 만든 기본 이미지는 기본적으로 32GB OS 디스크를 만듭니다. 이 기본 설치를 사용하면 공간이 부족할 수 있습니다. 위의 `az vm create` 명령에 추가된 `--os-disk-size-gb 128` 매개 변수를 사용하여 128GB의 OS 디스크를 만들 수 있습니다.
>
> 그런 다음, 적절한 폴더 볼륨을 확장하여 설치를 완료해야 하는 경우 [LVM(논리 볼륨 관리자)을 구성](/previous-versions/azure/virtual-machines/linux/configure-lvm)할 수 있습니다.

### <a name="test-connection-to-the-created-vms"></a>만든 VM에 대한 연결 테스트

Azure Cloud Shell에서 다음 명령을 사용하여 VM1 또는 다른 VM에 연결합니다. VM IP를 찾을 수 없는 경우 [Azure Cloud Shell에서 이 빠른 시작](../../../cloud-shell/quickstart.md#ssh-into-your-linux-vm)을 수행합니다.

```azurecli-interactive
ssh <username>@publicipaddress
```

연결에 성공하면 Linux 터미널을 나타내는 다음 출력이 표시됩니다.

```output
[<username>@<VM1> ~]$
```

`exit`를 입력하여 SSH 세션을 종료합니다.

## <a name="enable-high-availability"></a>고가용성 사용

> [!IMPORTANT]
> 자습서의 이 부분을 완료하려면 RHEL 및 고가용성 추가 기능에 대한 구독이 있어야 합니다. 이전 섹션에서 추천하는 이미지를 사용하는 경우 다른 구독을 등록할 필요가 없습니다.
 
각 VM 노드에 연결하고 아래 지침에 따라 HA를 사용하도록 설정합니다. 자세한 내용은 [RHEL에 대해 고가용성 구독 사용](/sql/linux/sql-server-linux-availability-group-cluster-rhel#enable-the-high-availability-subscription-for-rhel)을 참조하세요.

> [!TIP]
> 이 문서 전체에서 각 VM에 대해 동일한 명령을 실행해야 하므로 각 VM에 대한 SSH 세션을 동시에 여는 경우 더 쉽게 사용할 수 있습니다.
>
> 여러 `sudo` 명령을 복사하여 붙여넣을 때 암호를 입력하라는 메시지가 표시되면 추가 명령이 실행되지 않습니다. 이 경우 각 명령을 개별적으로 실행하세요.


1. 각 VM에서 다음 명령을 실행하여 Pacemaker 방화벽 포트를 엽니다.

    ```bash
    sudo firewall-cmd --permanent --add-service=high-availability
    sudo firewall-cmd --reload
    ```

1. 다음 명령을 사용하여 모든 노드에서 Pacemaker 패키지를 업데이트하고 설치합니다.

    > [!NOTE]
    > **nmap** 는 네트워크에서 사용 가능한 IP 주소를 찾는 도구이며 이 명령 블록의 일부로 설치됩니다. **nmap** 를 설치할 필요는 없지만 이는 자습서의 뒷부분에서 유용합니다.

    ```bash
    sudo yum update -y
    sudo yum install -y pacemaker pcs fence-agents-all resource-agents fence-agents-azure-arm nmap
    sudo reboot
    ```

1. Pacemaker 패키지를 설치할 때 만들어지는 기본 사용자의 암호를 설정합니다. 모든 노드에서 같은 암호를 사용합니다.

    ```bash
    sudo passwd hacluster
    ```

1. 다음 명령을 사용하여 호스트 파일을 열고 호스트 이름 확인을 설정합니다. 자세한 내용은 호스트 파일을 구성하는 방법에 대한 [AG 구성](/sql/linux/sql-server-linux-availability-group-configure-ha#prerequisites)을 참조하세요.

    ```
    sudo vi /etc/hosts
    ```

    **vi** 편집기에서 `i`를 입력하여 텍스트를 삽입하고, 해당 VM의 **개인 IP** 를 빈 줄에 추가합니다. 그런 다음, IP 옆의 공백 뒤에 VM 이름을 추가합니다. 각 줄에는 별도의 항목이 있어야 합니다.

    ```output
    <IP1> <VM1>
    <IP2> <VM2>
    <IP3> <VM3>
    ```

    > [!IMPORTANT]
    > 위의 **개인 IP** 주소를 사용하는 것이 좋습니다. 이 구성에서 공용 IP 주소를 사용하면 설정이 실패하므로 VM을 외부 네트워크에 공개하지 않는 것이 좋습니다.

    **vi** 편집기를 종료하려면 먼저 **Esc** 키를 누른 다음, `:wq` 명령을 입력하여 파일을 작성하고 종료합니다.

## <a name="create-the-pacemaker-cluster"></a>Pacemaker 클러스터 만들기

이 섹션에서는 pcsd 서비스를 사용하도록 설정하여 시작한 다음, 클러스터를 구성합니다. SQL Server on Linux의 경우 클러스터 리소스가 자동으로 만들어지지 않습니다. Pacemaker 리소스를 수동으로 사용하도록 설정하고 만들어야 합니다. 자세한 내용은 [RHEL에 대한 장애 조치 클러스터 인스턴스 구성](/sql/linux/sql-server-linux-shared-disk-cluster-red-hat-7-configure#install-and-configure-pacemaker-on-each-cluster-node) 문서를 참조하세요.

### <a name="enable-and-start-pcsd-service-and-pacemaker"></a>pcsd 서비스 및 Pacemaker 사용 및 시작

1. 모든 노드에서 명령을 실행합니다. 이러한 명령을 사용하면 다시 부팅된 후 노드가 클러스터에 다시 조인할 수 있습니다.

    ```bash
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ``` 

1. 모든 노드에서 기존 클러스터 구성을 제거합니다. 다음 명령을 실행합니다.

    ```bash
    sudo pcs cluster destroy 
    sudo systemctl enable pacemaker 
    ```

1. 주 노드에서 다음 명령을 실행하여 클러스터를 설정합니다.

    - `pcs cluster auth` 명령을 실행하여 클러스터 노드를 인증하는 경우 암호를 입력하라는 메시지가 표시됩니다. 이전에 만든 **hacluster** 사용자의 암호를 입력합니다.

    **RHEL7**

    ```bash
    sudo pcs cluster auth <VM1> <VM2> <VM3> -u hacluster
    sudo pcs cluster setup --name az-hacluster <VM1> <VM2> <VM3> --token 30000
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

    **RHEL8**

    RHEL 8의 경우 노드를 별도로 인증해야 합니다. 메시지가 표시되면 **hacluster** 의 사용자 이름 및 암호를 수동으로 입력합니다.

    ```bash
    sudo pcs host auth <node1> <node2> <node3>
    sudo pcs cluster setup <clusterName> <node1> <node2> <node3>
    sudo pcs cluster start --all
    sudo pcs cluster enable --all
    ```

1. 다음 명령을 실행하여 모든 노드가 온라인 상태인지 확인합니다.

    ```bash
    sudo pcs status
    ```

   **RHEL 7** 
   
    모든 노드가 온라인 상태이면 다음과 비슷한 출력이 표시됩니다.

    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Stack: corosync
    Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    Last updated: Fri Aug 23 18:27:57 2019
    Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
     
    3 nodes configured
    0 resources configured
     
    Online: [ <VM1> <VM2> <VM3> ]
     
    No resources
     
     
    Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    ```
   
   **RHEL 8** 
   
    ```output
    Cluster name: az-hacluster
     
    WARNINGS:
    No stonith devices and stonith-enabled is not false
     
    Cluster Summary:
    * Stack: corosync
    * Current DC: <VM2> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
    * Last updated: Fri Aug 23 18:27:57 2019
    * Last change: Fri Aug 23 18:27:56 2019 by hacluster via crmd on <VM2>
    * 3 nodes configured
    * 0 resource instances configured
     
   Node List:
    * Online: [ <VM1> <VM2> <VM3> ]
   
   Full List of Resources:
   * No resources
     
   Daemon Status:
          corosync: active/enabled
          pacemaker: active/enabled
          pcsd: active/enabled
    
    ```
    
1. 라이브 클러스터에서 예상 투표를 3으로 설정합니다. 이 명령은 라이브 클러스터에만 영향을 주며 구성 파일을 변경하지 않습니다.

    모든 노드에서 다음 명령을 사용하여 예상 투표를 설정합니다.

    ```bash
    sudo pcs quorum expected-votes 3
    ```

## <a name="configure-the-fencing-agent"></a>펜싱 에이전트 구성

STONITH 디바이스는 펜싱 에이전트를 제공합니다. 아래 지침은 이 자습서에서 수정되었습니다. 자세한 내용은 [STONITH 디바이스 만들기](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#create-stonith-device)를 참조하세요.
 
[Azure Fence Agent의 버전을 확인하여 업데이트되었는지 확인합니다](../../../virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker.md#cluster-installation). 다음 명령을 사용합니다.

```bash
sudo yum info fence-agents-azure-arm
```

아래 예제와 비슷한 출력이 표시됩니다.

```output
Loaded plugins: langpacks, product-id, search-disabled-repos, subscription-manager
Installed Packages
Name        : fence-agents-azure-arm
Arch        : x86_64
Version     : 4.2.1
Release     : 11.el7_6.8
Size        : 28 k
Repo        : installed
From repo   : rhel-ha-for-rhel-7-server-eus-rhui-rpms
Summary     : Fence agent for Azure Resource Manager
URL         : https://github.com/ClusterLabs/fence-agents
License     : GPLv2+ and LGPLv2+
Description : The fence-agents-azure-arm package contains a fence agent for Azure instances.
```

### <a name="register-a-new-application-in-azure-active-directory"></a>Azure Active Directory에서 새 애플리케이션 등록
 
 1. [https://editor.swagger.io](https://portal.azure.com ) 으로 이동합니다.
 2. [Azure Active Directory 블레이드](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)를 엽니다. 속성으로 이동하여 Directory ID 기록 `tenant ID`입니다.
 3. [**앱 등록**](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)을 클릭합니다.
 4. **새 등록** 을 클릭합니다.
 5. **이름**(예: `<resourceGroupName>-app`)을 입력하고, **이 조직 디렉터리의 계정만** 을 선택합니다.
 6. **웹** 애플리케이션 유형을 선택하고, 로그온 URL(예: http://localhost) )을 입력하고, [추가]를 클릭합니다. 로그온 URL이 사용되지 않으며, 이 URL은 임의의 올바른 URL이 될 수 있습니다. 작업이 완료되면 **등록** 을 클릭합니다.
 7. 새 등록에 대해 **인증서 및 비밀** 을 선택한 다음, **새 클라이언트 암호** 를 클릭합니다.
 8. 새 키(클라이언트 암호)에 대한 설명을 입력하고, **만료 기한 제한 없음** 을 선택하고, **추가** 를 클릭합니다.
 9. 비밀의 값을 적어 둡니다. 서비스 주체의 암호로 사용됩니다.
10. **개요** 를 선택합니다. 애플리케이션 ID를 적어둡니다. 서비스 주체의 사용자 이름(아래 단계의 로그인 ID)으로 사용됩니다.
 
### <a name="create-a-custom-role-for-the-fence-agent"></a>펜스 에이전트에 대한 사용자 지정 역할 만들기

자습서를 따라 [Azure CLI를 사용하여 Azure 사용자 지정 역할을 만듭니다](../../../role-based-access-control/tutorial-custom-role-cli.md#create-a-custom-role).

json 파일은 다음과 비슷해야 합니다.

- `<username>`은 원하는 이름으로 바꿉니다. 이는 이 역할 정의를 만들 때 중복되지 않도록 하기 위한 것입니다.
- `<subscriptionId>`를 Azure 구독 ID로 바꿉니다.

```json
{
  "Name": "Linux Fence Agent Role-<username>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscriptionId>"
  ]
}
```

역할을 추가하려면 다음 명령을 실행합니다.

- `<filename>`을 파일 이름으로 바꿉니다.
- 파일이 저장된 폴더 이외의 다른 경로에서 명령을 실행하는 경우 명령에 파일의 해당 폴더 경로를 포함합니다.

```azurecli-interactive
az role definition create --role-definition "<filename>.json"
```

다음 출력이 표시됩니다.

```output
{
  "assignableScopes": [
    "/subscriptions/<subscriptionId>"
  ],
  "description": "Allows to power-off and start virtual machines",
  "id": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/roleDefinitions/<roleNameId>",
  "name": "<roleNameId>",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/virtualMachines/powerOff/action",
        "Microsoft.Compute/virtualMachines/start/action"
      ],
      "dataActions": [],
      "notActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Linux Fence Agent Role-<username>",
  "roleType": "CustomRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="assign-the-custom-role-to-the-service-principal"></a>서비스 주체에 사용자 지정 역할 할당

마지막 단계에서 만든 `Linux Fence Agent Role-<username>` 사용자 지정 역할을 서비스 주체에 할당합니다. 소유자 역할을 더 이상 사용하지 마십시오!
 
1. [https://editor.swagger.io](https://portal.azure.com ) 으로 이동합니다.
2. [모든 리소스 블레이드](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAll)를 엽니다.
3. 첫 번째 클러스터 노드의 가상 머신 선택
4. **액세스 제어(IAM)** 를 클릭합니다.
5. **역할 할당 추가** 를 클릭합니다.
6. **역할** 목록에서 `Linux Fence Agent Role-<username>` 역할을 선택합니다.
7. **선택** 목록에서 위에서 만든 애플리케이션의 이름(`<resourceGroupName>-app`)을 입력합니다.
8. 페이지 맨 아래에 있는 **저장**
9. 모든 클러스터 노드에 대해 위의 단계를 반복합니다.

### <a name="create-the-stonith-devices"></a>STONITH 디바이스 만들기

노드 1에서 다음 명령을 실행합니다.

- `<ApplicationID>`를 애플리케이션 등록의 ID 값으로 바꿉니다.
- `<servicePrincipalPassword>`를 클라이언트 암호의 값으로 바꿉니다.
- `<resourceGroupName>`을 이 자습서에 사용된 구독의 리소스 그룹으로 바꿉니다.
- Azure 구독에서 `<tenantID>` 및 `<subscriptionId>`를 바꿉니다.

```bash
sudo pcs property set stonith-timeout=900
sudo pcs stonith create rsc_st_azure fence_azure_arm login="<ApplicationID>" passwd="<servicePrincipalPassword>" resourceGroup="<resourceGroupName>" tenantId="<tenantID>" subscriptionId="<subscriptionId>" power_timeout=240 pcmk_reboot_timeout=900
```

이미 HA 서비스를 허용하는 규칙을 방화벽에 추가했으므로(`--add-service=high-availability`) 모든 노드에서 2224, 3121, 21064, 5405 방화벽 포트를 열 필요가 없습니다. 그러나 HA와 관련된 유형의 연결 문제가 발생하면 다음 명령을 사용하여 HA와 연결된 이러한 포트를 엽니다.

> [!TIP]
> 필요에 따라 시간을 절약하기 위해 이 자습서의 모든 포트를 한 번에 추가할 수 있습니다. 열어야 하는 포트는 아래의 관련 섹션에서 설명합니다. 지금 모든 포트를 추가하려면 1433 및 5022 추가 포트를 추가합니다.

```bash
sudo firewall-cmd --zone=public --add-port=2224/tcp --add-port=3121/tcp --add-port=21064/tcp --add-port=5405/tcp --permanent
sudo firewall-cmd --reload
```

## <a name="install-sql-server-and-mssql-tools"></a>SQL Server 및 mssql-tools 설치

> [!NOTE]
> RHEL8-HA에 사전 설치된 SQL Server 2019를 사용하여 VM을 만든 경우에는 아래 단계를 건너뛰어 SQL Server 및 mssql-tools를 설치하고 모든 VM에서 `sudo /opt/mssql/bin/mssql-conf set-sa-password` 명령을 실행하여 모든 VM에 대한 sa 암호를 설정한 후 **가용성 그룹 구성** 섹션을 시작할 수 있습니다.

아래 섹션을 사용하여 SQL Server 및 mssql-tools를 VM에 설치합니다. 아래 샘플 중 하나를 선택하여 RHEL 7에 SQL Server 2017을 설치하거나 RHEL 8에 SQL Server 2019를 설치할 수 있습니다. 모든 노드에서 이러한 각 작업을 수행합니다. 자세한 내용은 [Red Hat VM에 SQL Server 설치](/sql/linux/quickstart-install-connect-red-hat)를 참조하세요.


### <a name="installing-sql-server-on-the-vms"></a>VM에 SQL Server 설치

다음 명령을 사용하여 SQL Server를 설치합니다.

**SQL Server 2017이 포함된 RHEL 7** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/7/mssql-server-2017.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```

**SQL Server 2019가 포함된 RHEL 8** 

```bash
sudo curl -o /etc/yum.repos.d/mssql-server.repo https://packages.microsoft.com/config/rhel/8/mssql-server-2019.repo
sudo yum install -y mssql-server
sudo /opt/mssql/bin/mssql-conf setup
sudo yum install mssql-server-ha
```
### <a name="open-firewall-port-1433-for-remote-connections"></a>원격 연결에 대한 1433 방화벽 포트 열기

원격으로 연결하려면 VM에서 1433 포트를 열어야 합니다. 다음 명령을 사용하여 각 VM의 방화벽에서 1433 포트를 엽니다.

```bash
sudo firewall-cmd --zone=public --add-port=1433/tcp --permanent
sudo firewall-cmd --reload
```

### <a name="installing-sql-server-command-line-tools"></a>SQL Server 명령줄 도구 설치

다음 명령을 사용하여 SQL Server 명령줄 도구를 설치합니다. 자세한 내용은 [SQL Server 명령줄 도구 설치](/sql/linux/quickstart-install-connect-red-hat#tools)를 참조하세요.

**RHEL 7** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/7/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```

**RHEL 8** 

```bash
sudo curl -o /etc/yum.repos.d/msprod.repo https://packages.microsoft.com/config/rhel/8/prod.repo
sudo yum install -y mssql-tools unixODBC-devel
```
 
> [!NOTE] 
> 편의상 /opt/msql-tools/bin/을 PATH 환경 변수에 추가합니다. 이렇게 하면 전체 경로를 지정하지 않고 도구를 실행할 수 있습니다. 다음 명령을 실행하여 로그인 세션 및 대화형/비로그인 세션 모두에 대한 PATH를 수정하세요.</br></br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile`</br>
`echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc`</br>
`source ~/.bashrc`


### <a name="check-the-status-of-the-sql-server"></a>SQL Server 상태 확인

구성이 완료되면 SQL Server의 상태를 확인하고 SQL Server가 실행되고 있는지 확인할 수 있습니다.

```bash
systemctl status mssql-server --no-pager
```

다음 출력이 표시됩니다.

```output
● mssql-server.service - Microsoft SQL Server Database Engine
   Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-12-05 17:30:55 UTC; 20min ago
     Docs: https://docs.microsoft.com/en-us/sql/linux
 Main PID: 11612 (sqlservr)
   CGroup: /system.slice/mssql-server.service
           ├─11612 /opt/mssql/bin/sqlservr
           └─11640 /opt/mssql/bin/sqlservr
```

## <a name="configure-an-availability-group"></a>가용성 그룹 구성

다음 단계에 따라 VM에 대해 SQL Server Always On 가용성 그룹을 구성합니다. 자세한 내용은 [Linux에서 고가용성을 위한 SQL Server Always On 가용성 그룹 구성](/sql/linux/sql-server-linux-availability-group-configure-ha)을 참조하세요.

### <a name="enable-always-on-availability-groups-and-restart-mssql-server"></a>AlwaysOn 가용성 그룹을 사용하도록 설정하고 mssql-server 다시 시작

SQL Server 인스턴스를 호스트하는 각 노드에서 Always On 가용성 그룹을 사용하도록 설정합니다. 그런 다음, mssql-server를 다시 시작합니다. 다음 스크립트를 실행합니다.

```
sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
sudo systemctl restart mssql-server
```

### <a name="create-a-certificate"></a>인증서 만들기

AG 엔드포인트에 대한 AD 인증은 현재 지원하지 않습니다. 따라서 인증서는 AG 엔드포인트 암호화에 사용해야 합니다.

1. SSMS(SQL Server Management Studio) 또는 SQL CMD를 사용하여 **모든 노드** 에 연결합니다. 다음 명령을 실행하여 AlwaysOn_health 세션을 사용하도록 설정하고 마스터 키를 만듭니다.

    > [!IMPORTANT]
    > SQL Server 인스턴스에 원격으로 연결하는 경우 방화벽에서 1433 포트가 열려 있어야 합니다. 또한 각 VM의 NSG에서 1433 포트에 대한 인바운드 연결을 허용해야 합니다. 자세한 내용은 인바운드 보안 규칙을 만드는 [보안 규칙 만들기](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)를 참조하세요.

    - `<Master_Key_Password>`를 사용자 고유의 암호로 바꿉니다.


    ```sql
    ALTER EVENT SESSION  AlwaysOn_health ON SERVER WITH (STARTUP_STATE=ON);
    GO
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Master_Key_Password>';
    ```

 
1. SSMS 또는 SQL CMD를 사용하여 주 복제본에 연결합니다. 다음 명령은 주 SQL Server 복제본에서 인증서를 `/var/opt/mssql/data/dbm_certificate.cer`에 만들고, 프라이빗 키를 `var/opt/mssql/data/dbm_certificate.pvk`에 만듭니다.

    - `<Private_Key_Password>`를 사용자 고유의 암호로 바꿉니다.
    
    ```sql
    CREATE CERTIFICATE dbm_certificate WITH SUBJECT = 'dbm';
    GO
    
    BACKUP CERTIFICATE dbm_certificate
       TO FILE = '/var/opt/mssql/data/dbm_certificate.cer'
       WITH PRIVATE KEY (
               FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
               ENCRYPTION BY PASSWORD = '<Private_Key_Password>'
           );
    GO
    ```

`exit` 명령을 실행하여 SQL CMD 세션을 종료하고 SSH 세션으로 돌아갑니다.
 
### <a name="copy-the-certificate-to-the-secondary-replicas-and-create-the-certificates-on-the-server"></a>보조 복제본에 인증서 복사 및 서버에 인증서 만들기

1. 가용성 복제본을 호스팅할 모든 서버의 동일한 위치에 만든 두 파일을 복사합니다.
 
    주 서버에서 다음 `scp` 명령을 실행하여 인증서를 대상 서버에 복사합니다.

    - `<username>` 및 `<VM2>`를 사용하는 사용자 이름 및 대상 VM 이름으로 바꿉니다.
    - 모든 보조 복제본에 대해 이 명령을 실행합니다.

    > [!NOTE]
    > 루트 환경을 제공하는 `sudo -i`를 실행할 필요가 없습니다. 이 자습서에서 이전에 수행한 대로 각 명령 앞에서 `sudo` 명령을 실행하기만 하면 됩니다.

    ```bash
    # The below command allows you to run commands in the root environment
    sudo -i
    ```

    ```bash
    scp /var/opt/mssql/data/dbm_certificate.* <username>@<VM2>:/home/<username>
    ```

1. 대상 서버에서 다음 명령을 실행합니다.

    - `<username>`을 사용자 이름으로 바꿉니다.
    - `mv` 명령은 파일 또는 디렉터리를 한 위치에서 다른 위치로 이동합니다.
    - `chown` 명령은 파일, 디렉터리 또는 링크의 소유자와 그룹을 변경하는 데 사용됩니다.
    - 모든 보조 복제본에 대해 이러한 명령을 실행합니다.

    ```bash
    sudo -i
    mv /home/<username>/dbm_certificate.* /var/opt/mssql/data/
    cd /var/opt/mssql/data
    chown mssql:mssql dbm_certificate.*
    ```

1. 다음 Transact-SQL 스크립트는 주 SQL Server 복제본에서 만든 백업에서 인증서를 만듭니다. 강력한 암호로 스크립트를 업데이트합니다. 해독 암호는 이전 단계에서 .pvk 파일을 만들 때 사용한 암호와 동일합니다. 인증서를 만들려면 모든 보조 서버에서 SQL CMD 또는 SSMS를 사용하여 다음 스크립트를 실행합니다.

    ```sql
    CREATE CERTIFICATE dbm_certificate
        FROM FILE = '/var/opt/mssql/data/dbm_certificate.cer'
        WITH PRIVATE KEY (
        FILE = '/var/opt/mssql/data/dbm_certificate.pvk',
        DECRYPTION BY PASSWORD = '<Private_Key_Password>'
                );
    GO
    ```

### <a name="create-the-database-mirroring-endpoints-on-all-replicas"></a>모든 복제본에서 데이터베이스 미러링 엔드포인트 만들기

SQL CMD 또는 SSMS를 사용하여 모든 SQL Server 인스턴스에서 다음 스크립트를 실행합니다.

```sql
CREATE ENDPOINT [Hadr_endpoint]
    AS TCP (LISTENER_PORT = 5022)
    FOR DATABASE_MIRRORING (
    ROLE = ALL,
    AUTHENTICATION = CERTIFICATE dbm_certificate,
ENCRYPTION = REQUIRED ALGORITHM AES
);
GO

ALTER ENDPOINT [Hadr_endpoint] STATE = STARTED;
GO
```

### <a name="create-the-availability-group"></a>가용성 그룹 만들기

SQL CMD 또는 SSMS를 사용하여 주 복제본을 호스팅하는 SQL Server 인스턴스에 연결합니다. 다음 명령을 실행하여 가용성 그룹을 만듭니다.

- `ag1`을 원하는 가용성 그룹 이름으로 바꿉니다.
- `<VM1>`, `<VM2>` 및 `<VM3>` 값을 복제본을 호스팅하는 SQL Server 인스턴스의 이름으로 바꿉니다.

```sql
CREATE AVAILABILITY GROUP [ag1]
     WITH (DB_FAILOVER = ON, CLUSTER_TYPE = EXTERNAL)
     FOR REPLICA ON
         N'<VM1>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM1>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM2>'
          WITH (
             ENDPOINT_URL = N'tcp://<VM2>:5022',
             AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
             FAILOVER_MODE = EXTERNAL,
             SEEDING_MODE = AUTOMATIC
             ),
         N'<VM3>'
         WITH(
            ENDPOINT_URL = N'tcp://<VM3>:5022',
            AVAILABILITY_MODE = SYNCHRONOUS_COMMIT,
            FAILOVER_MODE = EXTERNAL,
            SEEDING_MODE = AUTOMATIC
            );
GO

ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
GO
```

### <a name="create-a-sql-server-login-for-pacemaker"></a>Pacemaker용 SQL Server 로그인 만들기

모든 SQL Server 인스턴스에서 Pacemaker를 위한 SQL Server 로그인을 만듭니다. 다음 Transact-SQL은 로그인을 만듭니다.

- `<password>`를 사용자 고유의 복합 암호로 바꿉니다.

```sql
USE [master]
GO

CREATE LOGIN [pacemakerLogin] with PASSWORD= N'<password>';
GO

ALTER SERVER ROLE [sysadmin] ADD MEMBER [pacemakerLogin];
GO
```

모든 SQL Server 인스턴스에서 SQL Server 로그인에 사용되는 자격 증명을 저장합니다. 

1. 파일을 만듭니다.

    ```bash
    sudo vi /var/opt/mssql/secrets/passwd
    ```

1. 다음 두 줄을 파일에 추가합니다.

    ```bash
    pacemakerLogin
    <password>
    ```

    **vi** 편집기를 종료하려면 먼저 **Esc** 키를 누른 다음, `:wq` 명령을 입력하여 파일을 작성하고 종료합니다.

1. 파일을 루트에서만 읽을 수 있도록 설정합니다.

    ```bash
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

### <a name="join-secondary-replicas-to-the-availability-group"></a>가용성 그룹에 보조 복제본 조인

1. 보조 복제본을 AG에 조인하려면 모든 서버에 대한 방화벽에서 5022 포트를 열어야 합니다. SSH 세션에서 다음 명령을 실행합니다.

    ```bash
    sudo firewall-cmd --zone=public --add-port=5022/tcp --permanent
    sudo firewall-cmd --reload
    ```

1. 보조 복제본에서 다음 명령을 실행하여 AG에 조인합니다.

    ```sql
    ALTER AVAILABILITY GROUP [ag1] JOIN WITH (CLUSTER_TYPE = EXTERNAL);
    GO

    ALTER AVAILABILITY GROUP [ag1] GRANT CREATE ANY DATABASE;
    GO
    ```

1. 주 복제본과 각 보조 복제본에서 다음 Transact-SQL 스크립트를 실행합니다.

    ```sql
    GRANT ALTER, CONTROL, VIEW DEFINITION ON AVAILABILITY GROUP::ag1 TO pacemakerLogin;
    GO
    
    GRANT VIEW SERVER STATE TO pacemakerLogin;
    GO
    ```

1. 보조 복제본이 조인되면 **Always On 고가용성** 노드를 펼쳐서 SSMS 개체 탐색기에서 해당 복제본을 볼 수 있습니다.

    ![스크린샷은 기본 및 보조 가용성 복제본을 보여줍니다.](./media/rhel-high-availability-stonith-tutorial/availability-group-joined.png)

### <a name="add-a-database-to-the-availability-group"></a>가용성 그룹에 데이터베이스 추가

[데이터베이스 추가에 대한 가용성 그룹 구성 문서](/sql/linux/sql-server-linux-availability-group-configure-ha#add-a-database-to-the-availability-group)를 따릅니다.

이 단계에서는 다음 Transact-SQL 명령이 사용됩니다. 주 복제본에서 다음 명령을 실행합니다.

```sql
CREATE DATABASE [db1]; -- creates a database named db1
GO

ALTER DATABASE [db1] SET RECOVERY FULL; -- set the database in full recovery mode
GO

BACKUP DATABASE [db1] -- backs up the database to disk
   TO DISK = N'/var/opt/mssql/data/db1.bak';
GO

ALTER AVAILABILITY GROUP [ag1] ADD DATABASE [db1]; -- adds the database db1 to the AG
GO
```

### <a name="verify-that-the-database-is-created-on-the-secondary-servers"></a>데이터베이스가 보조 서버에 생성되었는지 확인

각 보조 SQL Server 복제본에서 다음 쿼리를 실행하여 db1 데이터베이스가 만들어졌고 SYNCHRONIZED 상태인지 확인합니다.

```
SELECT * FROM sys.databases WHERE name = 'db1';
GO
SELECT DB_NAME(database_id) AS 'database', synchronization_state_desc FROM sys.dm_hadr_database_replica_states;
```

`synchronization_state_desc` 목록에서 `db1`에 대해 SYNCHRONIZED이면 해당 복제본이 동기화된 것입니다. 보조 복제본에서 기본 복제본의 `db1`이 표시됩니다.

## <a name="create-availability-group-resources-in-the-pacemaker-cluster"></a>Pacemaker 클러스터에서 가용성 그룹 리소스 만들기

[Pacemaker 클러스터에서 가용성 그룹 리소스 만들기](/sql/linux/sql-server-linux-create-availability-group#create-the-availability-group-resources-in-the-pacemaker-cluster-external-only)의 지침을 따릅니다.

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *slave* 에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

### <a name="create-the-ag-cluster-resource"></a>AG 클러스터 리소스 만들기

1. 이전에 선택한 환경에 따라 다음 명령 중 하나를 사용하여 가용성 그룹 `ag1`에서 `ag_cluster` 리소스를 만듭니다.

    **RHEL 7**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s master notify=true
    ```

    **RHEL 8**
  
    ```bash
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 meta failure-timeout=30s promotable notify=true
    ```

2. 다음 명령을 사용하기 전에 리소스를 확인하고 해당 리소스가 온라인 상태인지 확인합니다.

    ```bash
    sudo pcs resource
    ```

    다음 출력이 표시됩니다.
    
    **RHEL 7** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
    Masters: [ <VM1> ]
    Slaves: [ <VM2> <VM3> ]
    ```
    
    **RHEL 8** 
    
    ```output
    [<username>@VM1 ~]$ sudo pcs resource
    * Clone Set: ag_cluster-clone [ag_cluster] (promotable):
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel3 (Monitoring) 
    * ag_cluster             (ocf::mssql:ag) :            Master VMrhel1 (Monitoring)
    * ag_cluster             (ocf::mssql:ag) :            Slave VMrhel2 (Monitoring)
    ```


### <a name="create-a-virtual-ip-resource"></a>가상 IP 리소스 만들기

1. 네트워크에서 사용 가능한 고정 IP 주소를 사용하여 가상 IP 리소스를 만듭니다. `nmap` 명령 도구를 사용하여 찾을 수 있습니다.

    ```bash
    nmap -sP <IPRange>
    # For example: nmap -sP 10.0.0.*
    # The above will scan for all IP addresses that are already occupied in the 10.0.0.x space.
    ```

2. **stonith-enabled** 속성을 false로 설정합니다.

    ```bash
    sudo pcs property set stonith-enabled=false
    ```

3. 다음 명령을 사용하여 가상 IP 리소스를 만듭니다.

    - 아래 `<availableIP>` 값을 사용하지 않는 IP 주소로 바꿉니다.

    ```bash
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<availableIP>
    ```

### <a name="add-constraints"></a>제약 조건 추가

1. IP 주소와 AG 리소스가 동일한 노드에서 실행되고 있는지 확인하려면 공동 배치 제약 조건을 구성해야 합니다. 다음 명령을 실행합니다.

   **RHEL 7**
  
    ```bash
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

   **RHEL 8**
   
    ```bash
     sudo pcs constraint colocation add virtualip with master ag_cluster-clone INFINITY with-rsc-role=Master
    ```
  
2. AG 리소스가 IP 주소보다 먼저 가동 상태가 되도록 하려면 정렬 제약 조건을 만듭니다. 공동 배치 제약 조건은 정렬 제약 조건을 암시하지만 정렬 제약 조건이 적용됩니다.

   **RHEL 7**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs constraint order promote ag_cluster-clone then start virtualip
    ```
  
3. 제약 조건을 확인하려면 다음 명령을 실행합니다.

    ```bash
    sudo pcs constraint list --full
    ```

    다음 출력이 표시됩니다.
    
    **RHEL 7**

    ```
    Location Constraints:
    Ordering Constraints:
          promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
          virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```
    
    **RHEL 8**
    
    ```output
    Location Constraints:
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```

### <a name="re-enable-stonith"></a>stonith를 사용하도록 다시 설정

테스트할 준비가 되었습니다. 노드 1에서 다음 명령을 실행하여 클러스터에서 stonith를 사용하도록 다시 설정합니다.

```bash
sudo pcs property set stonith-enabled=true
```

### <a name="check-cluster-status"></a>클러스터 상태 확인

다음 명령을 사용하여 클러스터 리소스의 상태를 확인할 수 있습니다.

```output
[<username>@VM1 ~]$ sudo pcs status
Cluster name: az-hacluster
Stack: corosync
Current DC: <VM3> (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
Last updated: Sat Dec  7 00:18:38 2019
Last change: Sat Dec  7 00:18:02 2019 by root via cibadmin on VM1

3 nodes configured
5 resources configured

Online: [ <VM1> <VM2> <VM3> ]

Full list of resources:

 Master/Slave Set: ag_cluster-master [ag_cluster]
     Masters: [ <VM2> ]
     Slaves: [ <VM1> <VM3> ]
 virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
 rsc_st_azure   (stonith:fence_azure_arm):      Started <VM1>

Daemon Status:
  corosync: active/enabled
  pacemaker: active/enabled
  pcsd: active/enabled
```

## <a name="test-failover"></a>테스트 장애 조치

지금까지 구성이 성공했는지 확인하기 위해 장애 조치를 테스트합니다. 자세한 내용은 [Linux의 Always On 가용성 그룹 장애 조치](/sql/linux/sql-server-linux-availability-group-failover-ha)를 참조하세요.

1. 다음 명령을 실행하여 주 복제본을 `<VM2>`로 수동으로 장애 조치합니다. `<VM2>`를 서버 이름의 값으로 바꿉니다.

   **RHEL 7**
   
    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

   **RHEL 8**
   
    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master
    ```

   리소스를 원하는 노드로 이동하기 위해 만든 임시 제약 조건이 자동으로 비활성화되고 아래의 2단계와 3단계를 수행할 필요가 없도록 추가 옵션을 지정할 수도 있습니다.

   **RHEL 7**

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master lifetime=30S
    ```

   **RHEL 8**

    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master lifetime=30S
    ```

   리소스 이동 명령 자체의 임시 제약 조건을 제거하는 아래의 2단계와 3단계를 자동화하는 또 다른 대안은 여러 명령을 한 줄로 결합하는 것입니다. 

   **RHEL 7**

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master && sleep 30 && pcs resource clear ag_cluster-master
    ```

   **RHEL 8**

    ```bash
    sudo pcs resource move ag_cluster-clone <VM2> --master && sleep 30 && pcs resource clear ag_cluster-clone
    ```
    
2. 제약 조건을 다시 확인하면 수동 장애 조치로 인해 다른 제약 조건이 추가되었음을 알 수 있습니다.
    
    **RHEL 7**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-master)
    Ordering Constraints:
            promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

    **RHEL 8**
    
    ```output
    [<username>@VM1 ~]$ sudo pcs constraint list --full
    Location Constraints:
          Resource: ag_cluster-master
            Enabled on: VM2 (score:INFINITY) (role: Master) (id:cli-prefer-ag_cluster-clone)
    Ordering Constraints:
            promote ag_cluster-clone then start virtualip (kind:Mandatory) (id:order-ag_cluster-clone-virtualip-mandatory)
    Colocation Constraints:
            virtualip with ag_cluster-clone (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-clone-INFINITY)
    Ticket Constraints:
    ```
    
3. 다음 명령을 사용하여 ID가 `cli-prefer-ag_cluster-master`인 제약 조건을 제거합니다.

    **RHEL 7**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

    **RHEL 8**
    
    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-clone
    ```

1. `sudo pcs resource` 명령을 사용하여 클러스터 리소스를 확인합니다. 그러면 이제 주 인스턴스가 `<VM2>`임을 알 수 있습니다.

    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         ag_cluster (ocf::mssql:ag):        FAILED <VM1> (Monitoring)
         Masters: [ <VM2> ]
         Slaves: [ <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
         Masters: [ <VM2> ]
         Slaves: [ <VM1> <VM3> ]
    virtualip      (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

## <a name="test-fencing"></a>펜싱 테스트

다음 명령을 실행하여 STONITH를 테스트할 수 있습니다. `<VM1>`에서 `<VM3>`에 대해 다음 명령을 실행해 보세요.

```bash
sudo pcs stonith fence <VM3> --debug
```

> [!NOTE]
> 기본적으로 펜스 작업은 노드를 껐다가 다시 켭니다. 노드를 오프라인으로 전환하려는 경우에만 명령에서 `--off` 옵션을 사용합니다.

다음과 같은 출력을 얻습니다.

```output
[<username>@<VM1> ~]$ sudo pcs stonith fence <VM3> --debug
Running: stonith_admin -B <VM3>
Return Value: 0
--Debug Output Start--
--Debug Output End--
 
Node: <VM3> fenced
```
펜스 디바이스를 테스트하는 방법에 대한 자세한 내용은 이 [Red Hat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/s1-stonithtest-haar) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

SQL Server 인스턴스에 가용성 그룹 수신기를 활용하려면 부하 분산 장치를 만들고 구성해야 합니다.

> [!div class="nextstepaction"]
> [자습서: Azure에서 RHEL 가상 머신의 SQL Server에 대한 가용성 그룹 수신기 구성](rhel-high-availability-listener-tutorial.md)
