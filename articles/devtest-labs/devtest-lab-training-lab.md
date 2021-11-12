---
title: 학습에 Azure DevTest Labs 사용
description: 이 문서에서는 Azure DevTest Labs에서 학습을 위한 랩을 설정하는 자세한 단계를 제공합니다.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 6809ee2a9d08e059184ccbcb98c20ac5ea2160fa
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398120"
---
# <a name="use-azure-devtest-labs-for-training"></a>학습에 Azure DevTest Labs 사용
Azure DevTest Labs 사용하여 개발/테스트 외에도 많은 주요 시나리오를 구현할 수 있습니다. 한 가지 시나리오는 학습을 위한 랩을 설정하는 것입니다. Azure DevTest Labs 사용하면 각 실습생이 동일하고 격리된 학습 환경을 만들 수 있는 사용자 지정 템플릿을 제공하는 랩을 만들 수 있습니다. 각 실습생이 필요할 때만 학습 환경을 사용할 수 있도록 정책을 적용하고 학습에 충분한 리소스(예: 가상 머신)를 포함할 수 있습니다. 마지막으로 실습생이 한 번의 클릭으로 액세스할 수 있도록 랩을 쉽게 공유할 수 있습니다.

![학습에 DevTest Labs 사용](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs 모든 가상 환경에서 교육을 수행하기 위한 다음 요구 사항을 충족합니다. 

* 실습생은 다른 실습생이 만든 VM을 볼 수 없습니다.
* 모든 학습 머신은 동일해야 합니다.
* 실습생은 학습 환경을 신속하게 프로비전할 수 있습니다.
* 학습생이 필요한 것보다 더 많은 VM을 얻을 수 없도록 하여 비용을 제어하고 VM을 사용하지 않을 때 VM을 종료합니다.
* 학습 랩을 각 실습생과 쉽게 공유합니다.
* 학습 랩을 반복해서 다시 사용하십시오.

이 문서에서는 학습 요구 사항을 충족하는 데 사용할 수 있는 다양한 Azure DevTest Labs 기능에 대해 알아봅니다. 자세한 단계에 따라 학습을 위한 랩을 설정할 수 있습니다.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Azure DevTest Labs로 학습 구현
1. **랩 만들기** 
   
    랩은 Azure DevTest Labs의 시작 지점입니다. 랩을 만든 후 랩에 사용자(실습생)를 추가하고, 비용을 제어하는 정책을 설정하고, 신속하게 만들 수 있는 VM 이미지를 정의하는 등의 작업을 수행할 수 있습니다.   
   
    다음 표에 있는 링크를 클릭하면 자세히 알아볼 수 있습니다.
   
   | Task | 학습 내용 |
   | --- | --- |
   | [Azure DevTest Labs에서 랩 만들기](devtest-lab-create-lab.md) |Azure Portal의 Azure DevTest Labs에서 랩을 만드는 방법을 알아봅니다. |
2. **바로 사용할 수 있는 마켓플레이스 이미지 및 사용자 지정 이미지를 사용하여 몇 분 만에 학습 VM 만들기** 
   
    Azure Marketplace의 다양한 이미지에서 바로 사용할 수 있는 이미지를 선택하고 랩의 실습생이 사용하도록 할 수 있습니다. 즉시 사용할 수 있는 이미지가 요구 사항을 충족하지 않는 경우 사용자 지정 이미지를 만들 수 있습니다. Azure Marketplace 미리 만든 이미지를 사용하여 랩 VM을 만들고, 학습에 필요한 소프트웨어를 설치하고, 랩에서 VM을 사용자 지정 이미지로 저장합니다. 
   
    다음 표에 있는 링크를 클릭하면 자세히 알아볼 수 있습니다.
   
   | Task | 학습 내용 |
   | --- | --- |
   | [Azure Marketplace 이미지 구성](devtest-lab-configure-marketplace-images.md) |학습용으로 원하는 이미지만 선택할 수 있도록 Azure Marketplace 이미지를 허용하는 방법을 알아봅니다. |
   | [사용자 지정 이미지 만들기](devtest-lab-create-template.md) |실습생이 사용자 지정 이미지를 사용하여 신속하게 VM을 만들 수 있도록 학습에 필요한 소프트웨어를 미리 설치하여 사용자 지정 이미지를 만듭니다. |
3. **학습용 컴퓨터에 대한 재사용 가능 템플릿 만들기** 
   
    Azure DevTest Labs의 수식은 VM을 만드는 데 사용되는 기본 속성 값의 목록입니다. 이미지, VM 크기(CPU와 RAM의 조합) 및 가상 네트워크를 선택하여 랩에서 수식을 만들 수 있습니다. 각 실습생은 랩에서 수식을 보고 수식을 사용하여 VM을 만들 수 있습니다. 
   
    다음 표에 있는 링크를 클릭하면 자세히 알아볼 수 있습니다.
   
   | Task | 학습 내용 |
   | --- | --- |
   | [VM을 만드는 DevTest Labs 수식 관리](devtest-lab-manage-formulas.md) |이미지, VM 크기(CPU와 RAM의 조합) 및 가상 네트워크를 선택하여 수식을 만들 수 있는 방법을 알아봅니다. |
4. **비용 제어**
   
    Azure DevTest Labs 사용하면 랩에서 정책을 설정하여 실습생이 랩에서 만들 수 있는 최대 VM 수를 지정할 수 있습니다. 
   
    여러 날의 교육을 수행하는 경우 하루 중 특정 시간에 모든 VM을 중지한 다음, 다음 날 자동으로 다시 시작할 수 있습니다. 이렇게 하려면 랩에서 자동 종료 및 자동 시작 정책을 설정합니다. 
   
    마지막으로 학습이 완료되면 단일 PowerShell 스크립트를 실행하여 모든 VM을 한 번에 삭제할 수 있습니다. 
   
    다음 표에 있는 링크를 클릭하면 자세히 알아볼 수 있습니다.
   
   | Task | 학습 내용 |
   | --- | --- |
   | [랩 정책 정의](devtest-lab-set-lab-policy.md) |랩에 정책을 설정하여 비용을 제어합니다. |
   | [PowerShell 스크립트를 사용하여 모든 랩 VM 삭제](./devtest-lab-faq.yml) |학습이 완료되면 한 번에 모든 랩을 삭제합니다. |
5. **각 실습생과 랩 공유**
   
    실습생과 공유하는 링크를 사용하여 랩에 직접 액세스할 수 있습니다. [Microsoft 계정](./devtest-lab-faq.yml)이 있는 실습생은 Azure 계정이 없어도 됩니다. 실습생은 다른 실습생이 만든 VM을 볼 수 없습니다.  
   
    다음 표에 있는 링크를 클릭하면 자세히 알아볼 수 있습니다.
   
   | Task | 학습 내용 |
   | --- | --- |
   | [Azure DevTest Labs에서 랩에 실습생 추가](devtest-lab-add-devtest-user.md) |Azure Portal을 사용하여 학습 랩에 실습생을 추가합니다. |
   | [PowerShell 스크립트를 사용하여 랩에 실습생 추가](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |PowerShell을 사용하여 학습 랩에 자동으로 실습생을 추가합니다. |
   | [랩에 대한 링크 가져오기](./devtest-lab-faq.yml) |하이퍼링크를 통해 랩에 바로 액세스할 수 있는 방법을 알아봅니다. |
6. **랩을 반복해서 다시 사용** 
   
    Resource Manager 템플릿을 만들어 동일한 랩을 반복해서 다시 만드는 데 사용하면 사용자 지정 설정을 포함하여 랩 만들기를 자동화할 수 있습니다. 
   
    다음 표에 있는 링크를 클릭하면 자세히 알아볼 수 있습니다.
   
   | Task | 학습 내용 |
   | --- | --- |
   | [Resource Manager 템플릿을 사용하여 랩 만들기](./devtest-lab-faq.yml) |Resource Manager 템플릿을 사용하여 Azure DevTest Labs에서 랩을 만듭니다. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
