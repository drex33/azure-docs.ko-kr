---
title: Azure Virtual Network의 Cloud Shell
description: Azure Virtual Network에 Cloud Shell 배포
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2ce108b7f36b7f7d4caf5133aafe99eb837df1ce
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536465"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>Azure Virtual Network에 Cloud Shell 배포

일반 Cloud Shell 세션은 리소스와 별도로 Microsoft 네트워크의 컨테이너에서 실행됩니다. 즉, 컨테이너 내에서 실행되는 명령은 특정 가상 네트워크에서만 액세스할 수 있는 리소스에는 액세스할 수 없습니다. 예를 들어 SSH를 사용하여 Cloud Shell에서 개인 IP 주소만 있는 가상 머신에 연결하거나, kubectl을 사용하여 액세스를 잠근 Kubernetes 클러스터에 연결할 수 없습니다. 

이 선택적 기능은 이러한 제한을 해결하고 사용자가 제어하는 Azure 가상 네트워크에 Cloud Shell을 배포할 수 있도록 합니다. 여기서 컨테이너는 선택한 가상 네트워크 내의 리소스와 상호작용할 수 있습니다.  

이 시나리오에서 배포 및 사용되는 리소스 아키텍처를 아래에서 확인할 수 있습니다.

![Cloud Shell 격리 VNET 아키텍처를 보여줍니다.](media/private-vnet/data-diagram.png)

Azure Virtual Network에서 Cloud Shell을 사용하려면 이 기능을 지원하기 위해 여러 리소스를 만들어야 합니다. 이 문서에서는 ARM 템플릿을 사용하여 필요한 리소스를 설정하는 방법을 보여줍니다.

> [!NOTE]
> 이러한 리소스는 가상 네트워크에 대해 한 번만 설정하면 됩니다. 그런 다음 가상 네트워크에 대한 액세스 권한이 있는 모든 관리자가 공유할 수 있습니다.

## <a name="required-network-resources"></a>필요한 네트워크 리소스

### <a name="virtual-network"></a>가상 네트워크
가상 네트워크는 하나 이상의 서브넷이 생성되는 주소 공간을 정의합니다.

Cloud Shell에 사용할 수 있는 원하는 가상 네트워크를 식별해야 합니다. 이는 일반적으로 관리하려는 리소스를 포함하는 기존 가상 네트워크 또는 리소스를 포함하는 네트워크와 피어링하는 네트워크입니다.

### <a name="subnet"></a>서브넷
선택한 가상 네트워크 내에서 전용 서브넷을 Cloud Shell 컨테이너에 사용해야 합니다. 이 서브넷은 ACI(Azure Container Instances) 서비스로 위임됩니다.  사용자가 가상 네트워크에서 Cloud Shell 컨테이너를 요청하면 Cloud Shell은 ACI를 사용하여 이 위임된 서브넷에 있는 컨테이너를 만듭니다.  이 서브넷에는 다른 리소스를 만들 수 없습니다.

### <a name="network-profile"></a>네트워크 프로필
네트워크 프로필은 리소스의 특정 네트워크 속성을 지정하는 Azure 리소스에 대한 네트워크 구성 템플릿입니다.

### <a name="azure-relay"></a>Azure Relay
[Azure Relay](../azure-relay/relay-what-is-it.md)를 사용하면 직접 연결할 수 없는 두 개의 엔드포인트가 통신할 수 있습니다. 이 경우는 관리자의 브라우저가 프라이빗 네트워크의 컨테이너와 통신할 수 있도록 하는 데 사용됩니다.

Cloud Shell에 사용되는 Azure Relay 인스턴스는 컨테이너 리소스에 액세스할 수 있는 네트워크를 제어하도록 구성할 수 있습니다. 
- 공용 인터넷에서 액세스할 수 있습니다. 이 구성에서는 Cloud Shell이 외부에서 내부 리소스에 연결할 수 있는 방법을 제공합니다. 
- 지정된 네트워크에서 액세스할 수 있습니다. 이 구성에서 관리자가 Cloud Shell을 사용할 수 있으려면 적절한 네트워크에서 실행되는 컴퓨터에서 Azure Portal에 액세스해야 합니다.

## <a name="storage-requirements"></a>저장소 요구 사항
표준 Cloud Shell과 마찬가지로 가상 네트워크에서 Cloud Shell을 사용할 때는 스토리지 계정이 필요합니다. 각 관리자는 파일을 저장하기 위해 파일 공유가 필요합니다.  스토리지 계정은 Cloud Shell이 사용하는 가상 네트워크에서 액세스할 수 있어야 합니다. 

## <a name="virtual-network-deployment-limitations"></a>가상 네트워크 배포 제한 사항
* 관련된 추가 네트워킹 리소스로 인해 가상 네트워크에서 Cloud Shell을 시작하는 것이 일반적으로 표준 Cloud Shell 세션보다 느립니다.

* 현재 인도 중부를 제외한 모든 Cloud Shell 주 지역에서 지원됩니다. 

* [Azure Relay](../azure-relay/relay-what-is-it.md)가 무료 서비스가 아닌 경우 [가격 책정](https://azure.microsoft.com/pricing/details/service-bus/)을 확인하세요. Cloud Shell 시나리오에서는 각 관리자가 Cloud Shell을 사용하는 동안 하나의 하이브리드 연결이 사용됩니다. Cloud Shell 세션이 완료되면 연결이 자동으로 종료됩니다.

## <a name="register-the-resource-provider"></a>리소스 공급자 등록

사용하려는 가상 네트워크가 있는 구독에 Microsoft.ContainerInstances 리소스 공급자를 등록해야 합니다. `Set-AzContext -Subscription {subscriptionName}`으로 적절한 구독을 선택한 다음, 다음을 실행합니다.

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

**RegistrationState** 가 `Registered`이면 아무 작업도 필요하지 않습니다. `NotRegistered`인 경우 `Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance`를 실행합니다. 

## <a name="deploy-network-resources"></a>네트워크 리소스 배포
 
### <a name="create-a-resource-group-and-virtual-network"></a>리소스 그룹 및 가상 네트워크 만들기
연결하려는 VNET이 이미 있는 경우 이 섹션을 건너뜁니다.

Azure Portal에서 또는 Azure CLI, Azure PowerShell 등을 사용하여 새 리소스 그룹에 리소스 그룹 및 가상 네트워크를 만들려면 **리소스 그룹 및 가상 네트워크는 동일한 지역에 있어야 합니다**.

### <a name="arm-templates"></a>ARM 템플릿
[Azure 빠른 시작 템플릿](https://aka.ms/cloudshell/docs/vnet/template)을 사용하여 가상 네트워크에 Cloud Shell 리소스를 만들고, 필요한 저장소를 만들기 위해 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/cloud-shell-vnet-storage/)을 활용합니다. 리소스 이름(주로 파일 공유 이름)을 기록해 둡니다.

### <a name="open-relay-firewall"></a>오픈 릴레이 방화벽
위의 템플릿을 사용하여 만든 릴레이로 이동하고, 설정에서 "네트워킹"을 선택하고 브라우저 네트워크에서 릴레이로의 액세스를 허용합니다. 기본적으로 릴레이는 생성된 가상 네트워크에서만 액세스할 수 있습니다. 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>가상 네트워크를 사용하도록 Cloud Shell 구성
> [!NOTE]
> 각 관리자가 Cloud Shell을 사용할 수 있도록 이 단계를 완료해야 합니다.

위의 단계를 완료하여 배포한 후에는 Azure Portal 또는 https://shell.azure.com 에서 Cloud Shell로 이동합니다. 격리된 Cloud Shell 환경에 연결하려 할 때마다 이러한 환경 중 하나를 사용해야 합니다.

> [!NOTE]
> 이전에 Cloud Shell을 사용한 경우 기존 clouddrive를 분리해야 합니다. 활성 Cloud Shell 세션에서 이 `clouddrive unmount`를 실행하려면 페이지를 새로 고칩니다.

Cloud Shell에 연결하고 첫 실행 환경에 대한 메시지가 표시됩니다. 원하는 셸 환경을 선택하고 "고급 설정 표시"를 선택한 다음 "VNET 격리 설정 표시" 상자를 선택합니다. 팝업의 필드를 입력합니다.  대부분의 필드는 가상 네트워크의 Cloud Shell과 연결될 수 있는 사용 가능한 리소스로 자동 채우기 됩니다.  사용자가 파일 공유 이름을 입력해야 합니다.


![Cloud Shell 격리 VNET 우선 환경 설정에 대해 설명합니다.](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>다음 단계
[Azure Virtual Network에 대한 자세한 정보](../virtual-network/virtual-networks-overview.md)
