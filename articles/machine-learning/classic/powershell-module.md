---
title: 'ML Studio(클래식): PowerShell 모듈 - Azure'
description: PowerShell을 사용하여 Machine Learning Studio(클래식) 작업 영역, 실험, 웹 서비스 등을 만들고 관리합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 662a550145ece8821a8cdbed0533696b265f7952
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690013"
---
# <a name="powershell-modules-for-machine-learning-studio-classic"></a>Machine Learning Studio(클래식)용 PowerShell 모듈

**적용 대상:**  ![적용 대상:](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio(클래식)  ![적용되지 않는 대상:](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [ML Studio (classic) retirement](../../../includes/machine-learning-studio-classic-deprecation.md)]

PowerShell 모듈을 사용하면 Studio(클래식) 리소스 및 작업 영역/데이터 세트/웹 서비스 등의 자산을 프로그래밍 방식으로 관리할 수 있습니다.

다음의 세 가지 PowerShell 모듈을 통해 Studio(클래식) 리소스를 조작할 수 있습니다.

* [Azure PowerShell Az](#az-rm)(2018년 출시) - cmdlet 이름은 다르지만 AzureRM의 모든 기능이 포함되어 있습니다.
* [AzureRM](#az-rm)(2016년 출시) - PowerShell Az로 바뀌었습니다.
* [Machine Learning PowerShell 클래식](#classic)(2016년 출시)

이러한 PowerShell 모듈은 다소 비슷하기는 하지만 각각 특정 시나리오용으로 설계되었습니다. 이 문서에서는 PowerShelll 모듈 간의 차이점을 설명하고 선택해야 하는 모듈을 결정하는 데 도움이 되는 정보를 제공합니다.  

아래의 [지원 표](#support-table)에서 각 모듈이 지원하는 리소스를 확인하세요. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell Az 및 AzureRM

현재 Azure 조작용 PowerShell 모듈로 사용되고 있는 Az는 AzureRM의 모든 이전 기능을 포함합니다. AzureRM에도 버그 수정은 계속 제공되지만 신규 cmdlet이나 기능은 제공되지 않습니다.  Az와 AzureRM은 모두 **Azure Resource Manager** 배포 모델을 사용하여 배포된 솔루션을 관리합니다. 이러한 리소스에는 Studio(클래식) 작업 영역 및 Studio(클래식) '새' 웹 서비스가 포함됩니다. 

PowerShell 클래식은 Az 또는 AzureRM과 함께 설치하여 '새' 리소스 종류와 '클래식' 리소스 종류를 모두 포함할 수 있습니다. 그러나 Az와 AzureRM은 동시에 설치하지 않는 것이 좋습니다. Az와 AzureRM 중에 설치할 모듈을 결정해야 하는 경우 향후 모든 배포에 사용할 수 있는 Az를 선택하는 것이 좋습니다.  [Azure PowerShell az 소개](/powershell/azure/new-azureps-module-az)에서 Az 및 AzureRM에 대한 자세한 내용과 마이그레이션 경로를 자세히 알아보세요.

Az 사용을 시작하려면 [Azure Az 설치 지침](/powershell/azure/install-az-ps)을 따르세요.

## <a name="powershell-classic"></a><a name="classic"></a> PowerShell 클래식

Studio(클래식) [PowerShell 클래식 모듈](https://aka.ms/amlps)에서는 **클래식 배포 모델** 을 사용하여 배포된 리소스를 관리할 수 있습니다. 이러한 리소스에는 Studio(클래식) 사용자 자산, '클래식' 웹 서비스, '클래식' 웹 서비스 엔드포인트가 포함됩니다.

그러나 리소스 배포와 관리를 간편하게 수행하려면 향후의 리소스에는 모두 Resource Manager 배포 모델을 사용하는 것이 좋습니다. 배포 모델에 대해 자세히 알아보려면 [Azure Resource Manager와 클래식 배포 비교](../../azure-resource-manager/management/deployment-models.md)를 참조하세요.

PowerShell 클래식 사용을 시작하려면 GitHub에서 [릴리스 패키지](https://github.com/hning86/azuremlps/releases)를 다운로드하고 [설치 지침](https://github.com/hning86/azuremlps/blob/master/README.md)을 따릅니다. 지침은 다운로드/압축 해제된 DLL의 차단을 해제한 다음 PowerShell 환경으로 가져오는 방법을 설명합니다.

PowerShell 클래식은 Az 또는 AzureRM과 함께 설치하여 '새' 리소스 종류와 '클래식' 리소스 종류를 모두 포함할 수 있습니다.

## <a name="powershell-support-table"></a><a name="support-table"></a> PowerShell 지원 표


| Task | **Az** |  **PowerShell 클래식** |
| --- | --- | --- |
| 작업 영역 만들기/삭제 | [Resource Manager 템플릿](./deploy-with-resource-manager-template.md) |  |
| 작업 영역 약정 플랜 관리 | [New-AzMlCommitmentPlan](/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| 작업 영역 사용자 관리 |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| 웹 서비스 관리 | [New-AzMlWebService](/powershell/module/az.machinelearning/new-azmlwebservice) <br>('새' 웹 서비스)| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>('클래식' 웹 서비스) |
| 웹 서비스 엔드포인트/키 관리 |  [Get-AzMlWebServiceKey](/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| 사용자 데이터 세트/학습된 모델 관리| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| 사용자 실험 관리 |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| 사용자 지정 모듈 관리 | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>다음 단계
다음 PowerShell 모듈에 대한 전체 설명서를 참조하세요.
* [PowerShell 클래식](https://aka.ms/amlps)
* [Azure PowerShell Az](/powershell/module/az.machinelearning/#machine_learning)