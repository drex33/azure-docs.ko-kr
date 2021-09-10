---
title: Azure CLI를 사용하여 Resource Manager로 VM 마이그레이션
description: 이 문서에서는 플랫폼 지원 방식의 Azure CLI를 사용하여 클래식에서 Azure Resource Manager로 리소스를 마이그레이션하는 과정을 안내합니다.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurecli
ms.openlocfilehash: 468c1edfd5be093b092be810d73d4c110a5f90be
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690372"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>Azure CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM

> [!IMPORTANT]
> 현재 IaaS VM의 90%가 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)를 사용하고 있습니다. 2020년 2월 28일부터 클래식 VM은 더 이상 사용되지 않으며 2023년 3월 1일에 완전히 사용 중지될 예정입니다. 사용 중단 및 [그 영향](classic-vm-deprecation.md#how-does-this-affect-me)에 대해 [자세히 알아보세요]( https://aka.ms/classicvmretirement).

이러한 단계에서는 Azure CLI(명령줄 인터페이스) 명령을 사용하여 클래식 배포 모델의 laaS(Infrastructure as a Service) 리소스를 Azure Resource Manager 배포 모델로 마이그레이션하는 방법을 보여 줍니다. 이 문서는 [Azure 클래식 CLI](/cli/azure/install-classic-cli)가 필요합니다. Azure CLI는 Azure Resource Manager 리소스에만 적용할 수 있으므로 이 마이그레이션에 사용할 수 없습니다.

> [!NOTE]
> 여기에 설명된 모든 작업은 idempotent 방식입니다. 지원되지 않는 기능 또는 구성 오류 이외의 문제가 발생하는 경우 준비, 중단 또는 커밋 작업을 다시 시도하는 것이 좋습니다. 그러면 플랫폼에서 작업을 다시 시도합니다.
> 
> 

<br>
다음은 마이그레이션 프로세스 중에 단계를 실행해야 하는 순서를 나타내는 순서도입니다.

![Screenshot that shows the migration steps](./media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>1단계: 마이그레이션 준비
클래식에서 Resource Manager로 IaaS 리소스 마이그레이션을 평가하는 몇 가지 모범 사례가 있습니다.

* [지원되지 않는 구성 또는 기능 목록](migration-classic-resource-manager-overview.md)을 읽어보세요. 지원되지 않는 구성 또는 기능을 사용하는 가상 머신이 있다면, 해당 기능/구성 지원이 발표되기를 기다리는 것이 좋습니다. 아니면, 필요에 맞을 경우 마이그레이션이 가능하도록 해당 기능을 제거하거나 해당 구성을 사용하지 않을 수 있습니다.
* 현재 인프라 및 애플리케이션을 배포하는 스크립트를 자동화한 경우 마이그레이션을 위해 해당 스크립트를 사용하여 유사한 테스트 설정을 만들어봅니다. 또는 Azure 포털을 사용하여 샘플 환경을 설정할 수도 있습니다.

> [!IMPORTANT]
> Application Gateway는 현재 클래식에서 Resource Manager로 마이그레이션될 수 없습니다. Application Gateway를 사용하여 클래식 가상 네트워크를 마이그레이션하려면 준비 작업을 실행하여 네트워크를 이동하기 전에 게이트웨이를 제거합니다. 마이그레이션을 완료한 후 Azure Resource Manager에서 게이트웨이를 다시 연결합니다. 
>
>다른 구독에서 ExpressRoute 회로에 연결하는 ExpressRoute 게이트웨이를 자동으로 마이그레이션할 수 없습니다. 이러한 경우에 ExpressRoute 게이트웨이를 제거하고 가상 네트워크를 마이그레이션한 다음 게이트웨이를 다시 만듭니다. 자세한 내용은 [클래식에서 Resource Manager 배포 모델로 ExpressRoute 회로 및 연결된 가상 네트워크 마이그레이션](../expressroute/expressroute-migration-classic-resource-manager.md)을 참조하세요.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>2단계: 구독 설정 및 공급자 등록
마이그레이션 시나리오의 경우 클래식 및 Resource Manager에 대한 환경을 설정해야 합니다. [Azure CLI를 설치](/cli/azure/install-classic-cli)하고 [구독을 선택](/cli/azure/authenticate-azure-cli)합니다.

계정에 로그인합니다.

```azurecli
azure login
```

다음 명령을 사용하여 Azure 구독을 선택합니다.

```azurecli
azure account set "<azure-subscription-name>"
```

> [!NOTE]
> 등록은 일회용 단계이지만 마이그레이션 전에 한 번 수행해야 합니다. 등록하지 않으면 다음과 같은 오류 메시지가 표시됩니다. 
> 
> *BadRequest : 구독이 마이그레이션에 대해 등록되지 않았습니다.* 
> 
> 

다음 명령을 사용하여 마이그레이션 리소스 공급자에 등록합니다. 일부 경우에는 이 명령이 시간 초과됩니다. 그러나 등록은 성공적으로 수행됩니다.

```azurecli
azure provider register Microsoft.ClassicInfrastructureMigrate
```

등록이 완료될 때까지 5분 정도 기다려 주세요. 다음 명령을 사용하여 승인 상태를 확인할 수 있습니다. 계속 진행하기 전에 RegistrationState가 `Registered` 인지 확인합니다.

```azurecli
azure provider show Microsoft.ClassicInfrastructureMigrate
```

이제 CLI를 `asm` 모드로 전환합니다.

```azurecli
azure config mode asm
```

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>3단계: 현재 배포의 Azure 지역 또는 VNET에 Azure Resource Manager Virtual Machine vCPU가 충분한지 확인
이 단계를 위해 `arm` 모드로 전환해야 합니다. 다음 명령을 사용하여 이 작업을 수행합니다.

```azurecli
azure config mode arm
```

다음 CLI 명령을 사용하여 Azure Resource Manager에 있는 현재 vCPU 수를 확인할 수 있습니다. vCPU 할당량에 대한 자세한 내용은 [제한 및 Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)를 참조하세요.

```azurecli
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

이 단계의 확인이 끝나면 `asm` 모드로 다시 전환해도 됩니다.

```azurecli
azure config mode asm
```

## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>4단계: 옵션 1 - 클라우드 서비스에서 가상 머신 마이그레이션
다음 명령을 사용하여 클라우드 서비스 목록을 가져와서 마이그레이션할 클라우드 서비스를 선택합니다. 클라우드 서비스의 VM이 가상 네트워크이거나 VM에 웹/작업자 역할이 있으면, 오류 메시지가 표시됩니다.

```azurecli
azure service list
```

다음 명령을 실행하여 자세하게 출력된 정보에서 클라우드 서비스에 대한 배포 이름을 확보합니다. 대부분의 경우, 배포 이름은 클라우드 서비스 이름과 같습니다.

```azurecli
azure service show <serviceName> -vv
```

먼저 다음 명령을 사용하여 클라우드 서비스를 마이그레이션할 수 있는지 유효성을 검사합니다.

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

마이그레이션을 위해 클라우드 서비스의 가상 머신을 준비합니다. 두 가지 옵션 중 선택할 수 있습니다.

VM을 플랫폼에서 만든 가상 네트워크에 마이그레이션하려면, 다음 명령을 사용합니다.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""
```

Resource Manager 배포 모델에서 기존 가상 네트워크로 마이그레이션하려면, 다음 명령을 사용합니다.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>
```

준비 작업이 완료되면 자세한 정보 출력을 확인하여 VM의 마이그레이션 상태를 가져오고 `Prepared` 상태인지 확인할 수 있습니다.

```azurecli
azure vm show <vmName> -vv
```

CLI 또는 Azure 포털을 사용하여 준비된 리소스에 대한 구성을 확인합니다. 마이그레이션할 준비가 되지 않았으며 이전 상태로 되돌아가려면 다음 명령을 사용합니다.

```azurecli
azure service deployment abort-migration <serviceName> <deploymentName>
```

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

```azurecli
azure service deployment commit-migration <serviceName> <deploymentName>
```

## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>4단계: 옵션 2 - 가상 네트워크에서 가상 머신 마이그레이션
마이그레이션할 가상 네트워크를 선택합니다. 가상 네트워크에 웹/작업자 역할이 포함되어 있거나 지원되지 않는 구성을 포함하는 VM이 있으면, 유효성 검사 오류 메시지가 표시됩니다.

다음 명령을 사용하여 구독의 모든 가상 네트워크를 가져옵니다.

```azurecli
azure network vnet list
```

출력은 다음과 비슷할 것입니다.

![전체 가상 네트워크 이름이 강조 표시된 명령줄의 스크린샷.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

위의 예제에서 **virtualNetworkName** 은 전체 이름 **"Group classicubuntu16 classicubuntu16"** 입니다.

먼저 다음 명령을 사용하여 가상 네트워크를 마이그레이션할 수 있는지 유효성을 검사합니다.

```shell
azure network vnet validate-migration <virtualNetworkName>
```

다음 명령을 사용하여 마이그레이션을 위해 선택한 가상 네트워크를 준비합니다.

```azurecli
azure network vnet prepare-migration <virtualNetworkName>
```

CLI 또는 Azure 포털을 사용하여 준비된 가상 머신에 대한 구성을 확인합니다. 마이그레이션할 준비가 되지 않았으며 이전 상태로 되돌아가려면 다음 명령을 사용합니다.

```azurecli
azure network vnet abort-migration <virtualNetworkName>
```

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

```azurecli
azure network vnet commit-migration <virtualNetworkName>
```

## <a name="step-5-migrate-a-storage-account"></a>5단계: 스토리지 계정 마이그레이션
가상 머신 마이그레이션이 완료되면 스토리지 계정을 마이그레이션하는 것이 좋습니다.

다음 명령을 사용하여 마이그레이션을 위한 스토리지 계정을 준비합니다.

```azurecli
azure storage account prepare-migration <storageAccountName>
```

CLI 또는 Azure 포털을 사용하여 준비된 스토리지 계정에 대한 구성을 확인합니다. 마이그레이션할 준비가 되지 않았으며 이전 상태로 되돌아가려면 다음 명령을 사용합니다.

```azurecli
azure storage account abort-migration <storageAccountName>
```

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

```azurecli
azure storage account commit-migration <storageAccountName>
```

## <a name="next-steps"></a>다음 단계

* [클래식에서 Azure Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션 개요](migration-classic-resource-manager-overview.md)
* [클래식에서 Azure Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](migration-classic-resource-manager-deep-dive.md)
* [클래식에서 Azure Resource Manager로 IaaS 리소스의 마이그레이션 계획](migration-classic-resource-manager-plan.md)
* [PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](migration-classic-resource-manager-ps.md)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션을 지원하기 위한 커뮤니티 도구](migration-classic-resource-manager-community-tools.md)
* [가장 일반적인 마이그레이션 오류 검토](migration-classic-resource-manager-errors.md)
* [클래식에서 Azure Resource Manager로의 IaaS 리소스 마이그레이션과 관련된 가장 자주 묻는 질문 검토](migration-classic-resource-manager-faq.yml)
