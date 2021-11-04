---
title: 데이터 레이블 공급 업체 회사
titleSuffix: Azure Machine Learning
description: 데이터 레이블 지정 공급 업체 회사를 사용 하 여 데이터 레이블 지정 프로젝트의 데이터에 레이블을 지정할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.reviewer: sgilley
author: kvijaykannan
ms.author: vkann
ms.date: 10/21/2021
ms.topic: how-to
ms.openlocfilehash: 449a6de24b9b02d64e1bda66f24afaa683868a56
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558557"
---
# <a name="work-with-a-data-labeling-vendor-company"></a>데이터 레이블 공급 업체 회사에 대 한 작업

데이터 레이블을 지정 하는 데 도움이 되는 데이터 레이블 공급 업체 회사에 참여 하는 방법을 알아봅니다. 이러한 회사와 이러한 회사에서 제공 하는 레이블 지정 서비스에 대 한 자세한 내용은 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend)의 목록 페이지에서 확인할 수 있습니다.


## <a name="workflow-summary"></a>워크플로 요약

데이터 레이블 지정 프로젝트를 만들기 전에 다음을 수행 합니다.

1. 레이블 지정 서비스 공급자를 선택 합니다.  Azure Marketplace에서 공급자를 찾으려면 다음을 수행 합니다.
    1. [이러한 공급 업체 레이블 지정 회사에 대 한 세부 정보](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend)를 검토 합니다.
    1. 공급 업체 레이블 지정 회사가 사용자의 요구 사항을 충족 하는 경우 Azure Marketplace에서 **연락처** 옵션을 선택 합니다. Azure Marketplace는 해당 조회를 공급 업체 레이블 회사로 라우팅합니다. 최종 회사를 선택 하기 전에 여러 공급 업체 레이블 회사에 문의할 수 있습니다.

1. 에 문의 하 고 레이블 지정 서비스 공급자를 사용 하 여 계약에 입력 합니다.

공급 업체 레이블 회사의 계약이 준비 되 면 다음을 수행 합니다.

1. [Azure Machine Learning studio](https://ml.azure.com)에서 레이블 지정 프로젝트를 만듭니다. 프로젝트를 만드는 방법에 대 한 자세한 내용은 [이미지 레이블 지정 프로젝트](how-to-create-image-labeling-projects.md) 또는 [텍스트 레이블 지정 프로젝트](how-to-create-text-labeling-projects.md)를 만드는 방법을 참조 하세요.
1. Azure Marketplace의 데이터 레이블 지정 공급자를 사용 하는 것으로 제한 되지 않습니다.  그러나 Azure Marketplace 공급자를 사용 하는 경우:
    1. 직원 단계의 **Azure Marketplace에서 공급 업체 레이블 지정 회사를 사용** 합니다 .를 선택 합니다.
    1. 드롭다운에서 적절 한 데이터 레이블 지정 회사를 선택 합니다.

    > [!NOTE]
    > 레이블 지정 프로젝트를 만든 후에는 회사 이름을 지정 하는 공급 업체 이름을 변경할 수 없습니다.

1. Azure Marketplace 또는 다른 위치에 있는지 여부에 관계 없이 모든 공급자의 경우 `labeler` `techlead` Azure RBAC (역할 기반 액세스)를 사용 하 여 공급 업체 레이블 회사에 대 한 액세스 (역할, 역할)를 사용 하도록 설정 합니다. 이러한 역할을 통해 회사는 리소스에 액세스 하 여 데이터에 주석을 달 수 있습니다.

## <a name="select-a-company"></a><a name="review"></a> 회사 선택

Microsoft는 사용자의 요구를 충족할 수 있는 지식과 경험을 제공 하는 몇 가지 레이블 지정 서비스 공급자를 식별 했습니다. 레이블 지정 서비스 공급자에 대해 알아보고 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend)의 목록 페이지에 있는 프로젝트 요구 사항 및 요구 사항을 고려 하 여 공급자를 선택할 수 있습니다.

> [!IMPORTANT]
> 이러한 회사와 이러한 회사에서 제공 하는 레이블 지정 서비스에 대 한 자세한 내용은 Azure Marketplace의 목록 페이지에서 확인할 수 있습니다. Azure Marketplace를 통해 서비스를 제공 하는 레이블 지정 회사를 사용 하는 모든 결정에 대 한 책임은 사용자가 레이블 지정 회사와 해당 환경, 서비스, 인력 관리, 약관 등이 프로젝트 요구 사항에 맞는지 독립적으로 평가 해야 합니다. Azure Marketplace에서 **연락처** 옵션을 사용 하 여 Azure Marketplace 통해 서비스를 제공 하는 레이블 지정 회사에 문의할 수 있으며 영업일 3 일 이내에 연결 된 회사에서이를 듣게 될 수 있습니다. 를 사용 하 여 계약을 체결 하 고 레이블 회사에 직접 지불 합니다.

Microsoft는 Azure Marketplace에서 잠재적 레이블 지정 서비스 공급자 목록을 정기적으로 검토 하 고 언제 든 지 목록에서 공급자를 추가 하거나 제거할 수 있습니다.  

* 공급자가 제거 되 면 기존 프로젝트 또는 해당 프로젝트에 대 한 해당 회사의 액세스에 영향을 주지 않습니다.
* 더 이상 Azure Marketplace에 나열 되지 않은 공급자를 사용 하는 경우 새 프로젝트에서 **공급 업체 레이블 지정 회사 Azure Marketplace** 옵션을 선택 하지 마세요.
* 제거 된 공급자는 더 이상 Azure Marketplace에 나열 되지 않습니다.
* 제거 된 공급자는 Azure Marketplace를 통해 더 이상 연결할 수 없게 됩니다.

다양 한 레이블 지정 프로젝트 요구 사항에 따라 여러 공급 업체 레이블 지정 회사에 참여할 수 있습니다. 각 프로젝트는 하나의 공급 업체 레이블 회사에 연결 됩니다.

다음은 Azure Machine Learning 데이터 레이블 서비스를 사용 하 여 레이블이 지정 된 데이터를 가져오는 데 도움이 될 수 있는 공급 업체 레이블 회사입니다. [공급 업체 회사의 목록을](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend)봅니다.

* [I부드러운 석재](https://azuremarketplace.microsoft.com/marketplace/consulting-services/isoftstoneinc1614950352893.20210527) 

* [사분면 리소스](https://azuremarketplace.microsoft.com/marketplace/consulting-services/quadrantresourcellc1587325810226.quadrant_resource_data_labeling)

## <a name="enter-into-a-contract"></a>계약에 들어가기 

작업할 레이블 지정 회사를 선택한 후 참여 약관에 따라 레이블 지정 회사 설정을 사용 하 여 직접 계약을 입력 해야 합니다. Microsoft는 본 계약에 대 한 파티가 아닙니다. 해당 약관을 결정 하거나 협상할 때 역할을 수행 하지 않습니다. 본 계약의 지불 금액은 레이블 지정 회사에 직접 지불 됩니다.

레이블 지정 프로젝트에서 ML 보조 레이블 지정을 사용 하도록 설정 하는 경우 Microsoft는이 서비스와의 연결에 사용 되는 계산 리소스에 대해 별도로 요금을 청구 합니다. Azure Machine Learning 사용과 관련 된 기타 모든 요금 (예: Azure Machine Learning 작업 영역에서 사용 되는 데이터의 저장소)은 Microsoft와 계약 조건에 따라 적용 됩니다.

## <a name="enable-access"></a>액세스 사용

공급 업체 레이블 지정 회사에서 프로젝트에 액세스할 수 있도록 하려면 작업 영역 수준에서 [AZURE RBAC (역할 기반 액세스) ](how-to-assign-roles.md#manage-workspace-access) 를 통해 액세스할 수 있도록 설정 합니다.  여러 개의 레이블 지정 프로젝트에 여러 공급 업체 레이블 지정 회사를 사용 하려는 경우 각 회사에 대 한 별도의 작업 영역을 만드는 것이 좋습니다.

> [!IMPORTANT]
> Microsoft가 아닌 사용자는 범위 지정 회사의 모든 측면 (범위, 품질, 일정 및 가격 책정과 관련 된 문제를 포함 하 여이에 국한 되지 않음)을 담당 합니다.

## <a name="next-steps"></a>다음 단계

* [이미지 레이블 지정 프로젝트 만들기 및 레이블 내보내기](how-to-create-image-labeling-projects.md)
* [텍스트 레이블 지정 프로젝트 및 레이블 내보내기 (미리 보기)](how-to-create-text-labeling-projects.md)