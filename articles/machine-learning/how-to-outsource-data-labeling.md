---
title: 데이터 레이블 지정 공급업체 회사와 협력하여 데이터에 레이블을 지정합니다.
titleSuffix: Azure Machine Learning
description: 데이터 레이블 지정 공급업체 회사를 사용하여 데이터 레이블 지정 프로젝트의 데이터 레이블 지정 지원
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
author: kvijaykannan
ms.author: vkann
ms.date: 09/30/2021
ms.topic: how-to
ms.openlocfilehash: bffdeb76f3fc7a8c740fd090b41b674ba71c8f19
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372845"
---
# <a name="work-with-a-data-labeling-vendor-company"></a>데이터 레이블 지정 공급업체 회사에서 작업

데이터 레이블 지정 공급업체 회사에 참여하여 데이터에 레이블을 지정하는 방법을 알아봅니다. 이러한 회사와 해당 회사에서 제공하는 레이블 지정 서비스에 대한 자세한 내용은 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend)목록 페이지에서 확인할 수 있습니다.


## <a name="workflow-summary"></a>워크플로 요약

데이터 레이블 지정 프로젝트를 만들기 전에 다음을 수행합니다.

1. 레이블 지정 서비스 공급자를 선택합니다.  Azure Marketplace 공급자를 찾으려면 다음을 수행합니다.
    1. 이러한 [공급업체 레이블 지정 회사의 목록 세부 정보를 검토합니다.](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend)
    1. 공급업체 레이블 지정 회사가 요구 사항을 충족하는 경우 Azure Marketplace **연락처** 옵션을 선택합니다. Azure Marketplace 문의를 공급업체 레이블 지정 회사로 라우팅합니다. 최종 회사를 선택하기 전에 여러 공급업체 레이블 지정 회사에 문의할 수 있습니다.

1. 에 문의하고 레이블 지정 서비스 공급자와 계약을 맺습니다.

공급업체 레이블 지정 회사와의 계약이 완료되면 다음을 수행합니다.

1. [Azure Machine Learning Studio에서](https://ml.azure.com)레이블 지정 프로젝트를 만듭니다. 프로젝트 만들기에 대한 자세한 내용은 이미지 [레이블](how-to-create-image-labeling-projects.md) 지정 프로젝트 또는 텍스트 레이블 지정 [프로젝트를](how-to-create-text-labeling-projects.md)만드는 방법을 참조하세요.
1. Azure Marketplace 데이터 레이블 지정 공급자를 사용하는 것으로 제한되지 않습니다.  그러나 Azure Marketplace 공급자를 사용하는 경우:
    1. 직원 단계에서 **Azure Marketplace 공급업체 레이블 지정 회사 사용을** 선택합니다.
    1. 드롭다운에서 적절한 데이터 레이블 지정 회사를 선택합니다.

    > [!NOTE]
    > 레이블 지정 프로젝트를 만든 후에는 공급업체 레이블 지정 회사 이름을 변경할 수 없습니다.

1. Azure Marketplace 통해 찾은 공급자의 경우 `labeler` `techlead` RBAC(Azure 역할 기반 액세스)를 사용하여 공급업체 레이블 지정 회사에 대한 액세스(역할, 역할)를 사용하도록 설정합니다. 이러한 역할을 통해 회사는 리소스에 액세스하여 데이터에 주석을 추가합니다.

## <a name="select-a-company"></a><a name="review"></a> 회사 선택

Microsoft는 요구 사항을 충족할 수 있는 지식과 경험이 있는 일부 레이블 지정 서비스 공급자를 확인했습니다. 레이블 지정 서비스 공급자에 대해 알아보고 Azure Marketplace 목록 페이지에서 프로젝트의 요구 사항과 요구 사항을 고려하여 공급자를 선택할 수 [있습니다.](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend)

> [!IMPORTANT]
> 이러한 회사와 해당 회사가 제공하는 레이블 지정 서비스에 대한 자세한 내용은 Azure Marketplace 목록 페이지에서 확인할 수 있습니다. 귀하는 Azure Marketplace 통해 서비스를 제공하는 레이블 지정 회사를 사용할 책임이 있으며 레이블 지정 회사와 해당 환경, 서비스, 인력, 사용 약관 등이 프로젝트 요구 사항을 충족하는지 여부를 독립적으로 평가해야 합니다. Azure Marketplace **연락처** 옵션을 사용하여 Azure Marketplace 통해 서비스를 제공하는 레이블 지정 회사에 문의할 수 있으며 영업일 3일 이내에 연락한 회사의 의견을 받을 수 있습니다. 과 계약을 체결하고 레이블 지정 회사에 직접 결제합니다.

Microsoft는 Azure Marketplace 잠재적 레이블 지정 서비스 공급자 목록을 정기적으로 검토하고 언제든지 목록에서 공급자를 추가하거나 제거할 수 있습니다.  

* 공급자가 제거되면 기존 프로젝트 또는 해당 프로젝트에 대한 해당 회사의 액세스에 영향을 주지 않습니다.
* 더 이상 Azure Marketplace 나열되지 않는 공급자를 사용하는 경우 새 프로젝트에서 **Azure Marketplace 공급업체 레이블 지정 회사 사용** 옵션을 선택하지 마세요.
* 제거된 공급자는 더 이상 Azure Marketplace 목록을 갖지 않습니다.
* 제거된 공급자는 더 이상 Azure Marketplace 통해 연결할 수 없습니다.

다양한 레이블 지정 프로젝트 요구 사항에 대해 여러 공급업체 레이블 지정 회사에 참여할 수 있습니다. 각 프로젝트는 하나의 공급업체 레이블 지정 회사에 연결됩니다.

다음은 Azure Machine Learning 데이터 레이블 지정 서비스를 사용하여 레이블이 지정되는 데이터를 얻는 데 도움이 될 수 있는 공급업체 레이블 지정 회사입니다. 공급업체 [회사 목록을 봅니다.](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend)

* [iSoftStone](https://azuremarketplace.microsoft.com/marketplace/consulting-services/isoftstoneinc1614950352893.20210527) 

* [사분면 리소스](https://azuremarketplace.microsoft.com/marketplace/consulting-services/quadrantresourcellc1587325810226.quadrant_resource_data_labeling)

## <a name="enter-into-a-contract"></a>계약 입력 

작업하려는 레이블 지정 회사를 선택한 후에는 계약조건에 따라 레이블 지정 회사 설정을 사용하여 직접 계약을 체결해야 합니다. Microsoft는 본 계약의 당사자가 아니며 약관을 결정하거나 협상하는 데 아무런 역할도 하지 않습니다. 이 계약에 따라 지불되는 금액은 레이블 지정 회사에 직접 지급됩니다.

레이블 지정 프로젝트에서 ML 지원 레이블 지정을 사용하도록 설정하면 Microsoft는 이 서비스와 관련하여 사용하는 컴퓨팅 리소스에 대해 별도로 요금을 청구합니다. Azure Machine Learning 사용과 관련된 기타 모든 요금(예: Azure Machine Learning 작업 영역에서 사용되는 데이터 스토리지)은 Microsoft와의 계약 조건에 따라 적용됩니다.

## <a name="enable-access"></a>액세스 사용

공급업체 레이블 지정 회사에서 프로젝트에 액세스할 수 있도록 작업 영역 수준에서 [Azure RBAC(역할 기반 액세스)를 ](how-to-assign-roles.md#manage-workspace-access) 통해 프로젝트에 액세스할 수 있도록 합니다.  여러 레이블 지정 프로젝트에 여러 공급업체 레이블 지정 회사를 사용하려는 경우 각 회사에 대해 별도의 작업 영역을 만드는 것이 좋습니다.

> [!IMPORTANT]
> Microsoft가 아닌 귀하는 범위, 품질, 일정 및 가격 책정과 관련된 문제를 포함하지만 이에 국한되지 않는 레이블 지정 회사와의 계약에서 모든 측면을 담당합니다.

## <a name="next-steps"></a>다음 단계

* [이미지 레이블 지정 프로젝트 만들기 및 레이블 내보내기](how-to-create-image-labeling-projects.md)
* [텍스트 레이블 지정 프로젝트 만들기 및 레이블 내보내기(미리 보기)](how-to-create-text-labeling-projects.md)