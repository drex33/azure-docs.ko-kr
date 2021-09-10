---
title: Visual Studio를 사용하여 Virtual Machine Scale Set 배포
description: Visual Studio 및 Resource Manager 템플릿을 사용하여 Virtual Machine Scale Sets 배포 | Microsoft Azure
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.date: 09/09/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f33cedfa9220510e23cfa5b1c72ac4182e9dd028
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692372"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Visual Studio에서 Virtual Machine Scale Set를 만드는 방법

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 단일 확장 집합

이 문서는 Visual Studio 리소스 그룹 배포를 사용하여 Azure Virtual Machine Scale Set를 배포하는 방법을 보여줍니다.

[Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/)는 자동 크기 조정 및 부하 분산을 사용하여 유사한 가상 머신 컬렉션을 배포하고 관리하기 위한 Azure Compute 리소스입니다. [Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates)을 사용하여 Virtual Machine Scale Sets를 프로비전하고 배포할 수 있습니다. Azure Resource Manager 템플릿은 Azure CLI, PowerShell, REST를 사용하여 배포가 가능하고 Visual Studio에서 직접 배포할 수도 있습니다. Visual Studio는 Azure 리소스 그룹 배포 프로젝트의 일부로 배포할 수 있는 예제 템플릿 세트를 제공합니다.

Azure 리소스 그룹 배포는 단일 배포 작업을 통해 관련된 Azure 리소스 집합을 그룹화하여 게시하는 방법입니다. 자세한 내용은 [Visual Studio를 통해 Azure 리소스 그룹 만들기 및 배포](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Visual Studio에서 Virtual Machine Scale Sets 배포를 시작하려면 다음 필수 조건이 필요합니다.

* Visual Studio 2013 이상
* Azure SDK 2.7, 2.8 또는 2.9

>[!NOTE]
>이 문서에서는 [Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)을 사용하는 Visual Studio 2019를 사용합니다.

## <a name="create-a-project"></a>프로젝트 만들기<a name="creating-a-project"></a> 

1. Visual Studio를 열고 **새 프로젝트 만들기** 를 선택합니다.

1. **새 프로젝트 만들기** 에서 C#을 위해 **Azure 리소스 그룹** 을 선택하고 **다음** 을 선택합니다.

1. **새 프로젝트 구성** 에서 이름을 입력하고 **만들기** 를 선택합니다.

    ![프로젝트 이름 지정 및 만들기](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. 템플릿 목록에서 **Windows Virtual Machine Scale Set** 또는 **Linux Virtual Machine Scale Set** 템플릿을 선택합니다. **확인** 을 선택합니다.

   ![가상 머신 템플릿 만들기](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

프로젝트를 만든 후 **솔루션 탐색기** PowerShell 배포 스크립트, Azure Resource Manager 템플릿, Virtual Machine Scale Set의 매개 변수 파일이 표시됩니다.

## <a name="customize-your-project"></a>프로젝트 사용자 지정

이제 템플릿을 편집하여 애플리케이션의 요구에 맞게 사용자 지정할 수 있습니다. 가상 머신 확장 속성을 추가하거나 부하 분산 규칙을 편집할 수 있습니다. 기본적으로 Virtual Machine Scale Set 템플릿은 자동 크기 조정 규칙을 손쉽게 추가할 수 있게 **AzureDiagnostics** 확장을 배포하도록 구성됩니다. 템플릿은 또한 인바운드 NAT 규칙을 사용하여 구성된 공용 IP 주소를 가진 부하 분산 장치를 배포합니다.

부하 분산 장치를 통해 SSH(Linux) 또는 RDP(Windows)를 사용하여 가상 머신 인스턴스를 연결할 수 있습니다. 프런트 엔드 포트 범위는 50000에서 시작합니다. Linux의 경우 포트 50000으로 SSH하는 경우 부하 분산은 확장 집합에 있는 첫 번째 가상 머신의 포트 22로 라우팅합니다. 50001 포트로 연결하면 두 번째 가상 머신의 22 포트로 라우팅되며 이런 방식으로 계속됩니다.

 Visual Studio에서 템플릿을 편집하는 좋은 방법은 **JSON 개요** 를 사용하는 것입니다. 매개 변수, 변수 및 리소스를 구성할 수 있습니다. 스키마에 대한 이해를 바탕으로, Visual Studio는 템플릿을 배포하기 전에 거기에 포함된 오류를 알려줄 수 있습니다.

![JSON 탐색기](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>프로젝트 배포

Azure Resource Manager 템플릿을 배포하여 Virtual Machine Scale Set 리소스를 만듭니다.

1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **배포** > **신규** 를 선택합니다.

    ![프로젝트 배포](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. **리소스 그룹에 배포** 에서 사용할 구독을 선택하고 리소스 그룹을 선택합니다. 필요한 경우 리소스 그룹을 만들 수 있습니다.

1. 그런 다음, **매개 변수 편집** 을 선택하여 템플릿에 전달되는 매개 변수를 입력합니다.

   ![구독 입력 및 리소스 그룹](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. 운영 체제에 대한 사용자 이름과 암호를 제공합니다. 이러한 값은 배포를 만드는 데 필요합니다. PowerShell Tools for Visual Studio가 설치되지 않았다면 **암호 저장** 을 확인하여 숨겨진 PowerShell 명령 프롬프트를 방지하거나 [Key Vault 지원](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/)을 선택하십시오. **저장** 을 선택하여 계속합니다.

    ![배포 매개 변수 편집](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. **리소스 그룹에 배포** 에서 **배포** 를 선택합니다. 작업이 **Deploy-AzureResourceGroup.ps1** 스크립트를 실행 중입니다. **출력** 창에 배포 진행률이 표시됩니다.

   ![출력이 결과를 나타냄](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>Virtual Machine Scale Set <a name="exploring-your-virtual-machine-scale-set"></a> 탐색

**보기** > **클라우드 탐색기** 를 선택하여 새 Virtual Machine Scale Set을 확인합니다. 필요한 경우 **모두 새로 고침** 을 사용합니다.

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**Cloud Explorer** 를 사용하면 애플리케이션을 배포하는 동안 Visual Studio에서 Azure 리소스를 관리할 수 있습니다. [Azure Portal](https://portal.azure.com) 및 [Azure 리소스 탐색기](https://resources.azure.com/)에서 Virtual Machine Scale Set를 볼 수도 있습니다.

 포털은 웹 브라우저로 Azure 인프라를 관리하는 방법 중 가장 좋은 방법을 제시합니다. Azure Resource Explorer에서 Azure 리소스를 탐색하고 손쉽게 디버그할 수 있습니다. Azure Resource Explorer는 인스턴스 보기를 제공하고, 보고 있는 리소스에 대한 PowerShell 명령도 보여줍니다.

## <a name="next-steps"></a>다음 단계

Visual Studio를 통해 Virtual Machine Scale Sets를 성공적으로 배포하면 애플리케이션 요구 사항에 맞게 프로젝트를 추가로 사용자 지정할 수 있습니다. 예를 들어 **Insights** 리소스를 추가하여 자동 크기 조정을 구성합니다. 독립 실행형 가상 컴퓨터와 같은 템플릿에 인프라를 추가하거나 사용자 지정 스크립트 확장을 사용하여 애플리케이션을 배포할 수 있습니다. [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates) GitHub 리포지토리에서 유용한 예제 템플릿을 찾을 수 있습니다. `vmss`를 검색합니다.
