---
title: Service Fabric 클러스터 환경 만들기
description: 자체 포함된 Service Fabric 클러스터를 사용하여 환경을 만드는 방법을 알아봅니다. 일정을 사용하여 클러스터를 시작하고 중지하는 방법을 참조하세요.
author: EMaher
ms.topic: how-to
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: c9c7eec0d5c909285e4f7eb8768dbb300c86822e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325280"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Azure DevTest Labs에서 자체 포함 Service Fabric 클러스터로 환경 만들기
이 문서에서는 Azure DevTest Labs에서 자체 포함 Service Fabric 클러스터로 환경을 만드는 방법에 대한 정보를 제공합니다. 

## <a name="overview"></a>개요
DevTest Labs는 Azure Resource Manager 템플릿에서 정의한 대로 자체 포함 테스트 환경을 만들 수 있습니다. 이 환경에는 가상 머신 같은 IaaS 리소스와 Service Fabric 같은 PaaS 리소스가 모두 포함됩니다. DevTest Labs를 사용하면 가상 머신을 제어하는 명령을 제공하여 환경에서 가상 머신을 관리할 수 있습니다. 명령은 일정에 따라 가상 머신을 시작하거나 중지하는 기능을 제공합니다. 마찬가지로, DevTest Labs는 환경에서 Service Fabric 클러스터를 관리하는 데도 도움이 될 수 있습니다. 환경에서 수동으로 또는 일정을 통해 Service Fabric 클러스터를 시작하거나 중지할 수 있습니다.

## <a name="create-a-service-fabric-cluster"></a>Service Fabric 클러스터 만들기
Service Fabric 클러스터는 DevTest Labs에서 환경을 사용하여 만들어집니다. 각 환경은 Git 리포지토리의 Azure Resource Manager 템플릿에 의해 정의됩니다. DevTest Labs의 [퍼블릭 Git 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Environments/)에는 Service Fabric 클러스터를 만드는 Resource Manager 템플릿이 [ServiceFabric-Cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) 폴더에 있습니다. 

1. 먼저 [랩 만들기](devtest-lab-create-lab.md) 문서의 지침에 따라 Azure DevTest Labs에서 랩을 만듭니다. **공용 환경** 옵션은 기본적으로 **설정** 되어 있습니다. 
2. 다음 단계를 수행하여 구독에 Service Fabric 공급자가 등록되어 있는지 확인합니다.
    1. 왼쪽 탐색 메뉴에서 **구독** 을 선택하고 사용 중인 **구독** 을 선택합니다.
    2. **구독** 페이지의 왼쪽 메뉴에 있는 **설정** 섹션에서 **리소스 공급자** 를 선택합니다. 
    3. **Microsoft.ServiecFabric** 이 등록되어 있지 않으면 **등록** 을 선택합니다. 
3. 랩의 **DevTest Labs** 페이지에서 도구 모음의 **+ 추가** 를 선택합니다. 
    
    ![도구 모음의 추가 단추를 보여 주는 스크린샷.](./media/create-environment-service-fabric-cluster/add-button.png)
3. **기본 선택** 페이지의 목록에서 **Service Fabric 랩 클러스터** 를 선택합니다. 

    ![목록에서 Service Fabric 랩 클러스터 선택을 보여 주는 스크린샷.](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. **설정 구성** 페이지에서 다음 단계를 수행합니다. 
    1. Service Fabric 클러스터의 Azure 리소스 그룹인 클러스터 **환경의** **이름을** 지정합니다. 
    2. 클러스터 가상 머신의 **OS(운영 체제)** 를 선택합니다. 기본값은 **Windows** 입니다.
    3. 클러스터의 **관리자** 이름을 지정합니다. 
    4. 관리자의 **암호** 를 지정합니다. 
    5. **인증서** 의 경우 인증서 정보를 Base64로 인코딩된 문자열로 입력합니다. 인증서를 만들려면 다음 단계를 수행합니다.
        1. [Git 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)에서 **Create-ClusterCertificate.ps1** 파일을 다운로드합니다. 또는 컴퓨터에서 리포지토리를 복제합니다. 
        2. **PowerShell** 을 시작합니다. 
        3. `.\Create-ClusterCertificate.ps1` 명령을 사용하여 **ps1** 파일을 실행합니다. 이 페이지의 인증서 관련 필드에 입력해야 하는 정보가 포함된 텍스트 파일이 메모장으로 표시됩니다.
    6. 인증서 **암호** 를 입력합니다.
    7. 인증서 **지문** 을 지정합니다.
    8. **설정 구성** 페이지에서 **추가** 를 선택합니다. 

        ![클러스터 설정 구성을 보여 주는 스크린샷](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. 클러스터를 만들면 이전 단계에서 지정한 환경 이름을 사용하는 리소스 그룹이 표시됩니다. 확장하면 Service Fabric 클러스터가 표시됩니다. 리소스 그룹의 상태가 **만드는 중** 에 머물러 있으면 도구 모음에서 **새로 고침** 을 선택합니다. **Service Fabric 클러스터** 환경에서는 Linux 또는 Windows에 1노드 형식의 5노드 클러스터를 만듭니다.

    다음 예제에서 **mysfabricclusterrg는** Service Fabric 클러스터에 대해 만든 리소스 그룹의 이름입니다. 랩 환경은 만든 리소스 그룹에 자체 포함되어 있습니다. 이 제한은 환경을 정의하는 템플릿이 새로 만든 리소스 그룹 또는 랩에서 사용하는 가상 네트워크 내에서만 리소스에 액세스할 수 [있음을 의미합니다.](devtest-lab-configure-vnet.md) 이 샘플은 동일한 리소스 그룹에 필요한 모든 리소스를 만듭니다.

    ![생성된 클러스터를 보여 주는 스크린샷.](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>클러스터 시작 또는 중지
DevTest Labs 페이지 자체 또는 DevTest Labs에서 제공하는 Service Fabric 클러스터 페이지에서 클러스터를 시작하거나 중지할 수 있습니다. 

### <a name="from-the-devtest-labs-page"></a>DevTest Labs 페이지에서
랩의 DevTest Labs 페이지에서 클러스터를 시작하거나 중지할 수 있습니다. 

1. 다음 이미지에 표시된 것처럼 Service Fabric 클러스터의 **점 세 개(...)** 를 선택합니다. 

    ![클러스터에 대한 시작 및 중지 명령을 보여 주는 스크린샷](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. 바로 가기 메뉴에 클러스터를 **시작** 하고 **중지** 하는 두 개의 명령이 표시됩니다. 시작 명령은 클러스터의 모든 노드를 시작합니다. 중지 명령은 클러스터의 모든 노드를 중지합니다. 클러스터를 중지하면 Service Fabric 클러스터 자체는 준비 상태로 유지합니다. 그러나 랩의 클러스터에서 시작 명령을 다시 실행하기 전에는 노드를 사용할 수 없습니다.

    테스트 환경에서 Service Fabric 클러스터를 사용할 때 유의해야 할 몇 가지 사항이 있습니다. 노드가 다시 시작된 후 Service Fabric 클러스터가 완전히 리하이드레이션할 때까지 약간의 시간이 걸릴 수 있습니다. 데이터를 종료부터 시작까지 유지하려면 가상 머신에 연결된 관리 디스크에 데이터를 저장해야 합니다. 연결된 관리 디스크를 사용하는 경우 성능에 영향을 주므로 테스트 환경에만 권장됩니다. 데이터 스토리지에 사용되는 디스크가 백업되지 않으면 클러스터에서 stop 명령을 실행하면 데이터가 손실됩니다.

### <a name="from-the-service-fabric-cluster-page"></a>Service Fabric 클러스터 페이지 사용 
클러스터를 시작하거나 중지하는 또 다른 방법이 있습니다. 

1. DevTest Labs 페이지의 트리 뷰에서 Service Fabric 클러스터를 선택합니다. 

    ![클러스터 선택을 보여 주는 스크린샷.](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. 클러스터의 **Service Fabric 클러스터** 페이지에서 클러스터를 시작하거나 중지하는 명령이 도구 모음에 표시됩니다. 

    ![Service Fabric 클러스터 페이지에서 시작 또는 중지 명령을 보여 주는 스크린샷.](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>자동 시작 및 자동 종료 일정 구성
Service Fabric 클러스터는 일정에 따라 시작되거나 중지될 수도 있습니다. 이 환경은 랩의 가상 머신 환경과 비슷합니다. 비용을 절약하기 위해 기본적으로 랩에서 만든 모든 클러스터는 랩 [종료 정책](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)에 정의된 시간에 자동으로 종료됩니다. 클러스터 종료 여부를 지정하거나 클러스터 종료 시간을 지정하여 이 설정을 재정의할 수 있습니다. 

![자동 시작 및 자동 종료에 대한 기존 일정을 보여 주는 스크린샷.](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>자동 시작 일정 옵트인
시작 일정을 옵트인하려면 다음 단계를 수행합니다.

1. 왼쪽 메뉴에서 **자동 시작** 을 선택합니다.
2. **이 Service Fabric 클러스터에 대해 자동 시작을 예약하도록 허용** 에서 **설정** 을 선택합니다. 랩 소유자가 자신의 가상 머신 또는 Service Fabric 클러스터를 사용자가 자동으로 시작하도록 허용한 경우에만 이 페이지가 사용하도록 설정됩니다.
3. 도구 모음에서 **저장** 을 선택합니다. 

    ![자동 시작 페이지를 보여 주는 스크린샷.](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>자동 종료 설정 구성 
종료 설정을 변경하려면 다음 단계를 수행합니다.

1. 왼쪽 메뉴에서 **자동 종료** 를 선택합니다. 
2. 이 페이지에서 **사용** 에 **해제** 를 선택하여 자동 종료를 옵트아웃할 수 있습니다. 
3. **사용으로** **켜기** 를 선택한 경우 다음 단계를 수행합니다.
    1. 종료 **시간** 을 지정합니다.
    2. 해당 시간의 **표준 시간대** 를 지정합니다. 
    3. 자동 종료 전에 DevTest Labs에서 알림을 보낼지 여부를 **지정합니다.** 
    4. 알림 옵션에 대해 **예** 를 선택한 경우 알림을 송신할 **웹후크 URL** 및/또는 **메일 주소** 를 지정합니다. 
    5. 도구 모음에서 **저장** 을 선택합니다.

        ![자동 종료 페이지를 보여 주는 스크린샷.](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Service Fabric 클러스터의 노드를 보려면
이전 단계의 Service Fabric 클러스터 페이지는 DevTest Labs 페이지입니다. 이 페이지에는 클러스터의 노드가 표시 되지 않습니다. 클러스터에 대한 자세한 정보를 보려면 다음 단계를 수행합니다.

1. 랩의 **DevTest Lab** 페이지에서 **내 가상 머신** 섹션에 있는 트리 뷰의 **리소스 그룹** 을 선택합니다.

    ![리소스 그룹 선택을 보여 주는 스크린샷](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. **리소스 그룹** 페이지에는 리소스 그룹의 모든 리소스가 목록으로 표시됩니다. 목록에서 **Service Fabric 클러스터** 를 선택합니다. 

    ![목록에서 클러스터를 선택 하는 것을 보여 주는 스크린샷](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. 클러스터에 대해 제공 하는 Service Fabric **Service Fabric 클러스터** 페이지가 표시 됩니다. 페이지에는 노드 및 노드 유형과 같은 클러스터에 대 한 모든 정보가 표시 됩니다.

    ![Service Fabric 클러스터 홈 페이지를 보여 주는 스크린샷](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>다음 단계
환경에 관한 자세한 내용은 다음 문서를 참조하세요. 

- [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [Azure DevTest Labs에서 공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)
