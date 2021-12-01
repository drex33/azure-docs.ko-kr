---
title: '빠른 시작: Azure Synapse 작업 영역 Azure Resource Manager 템플릿 만들기(ARM 템플릿)'
description: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Synapse 작업 영역을 만드는 방법을 알아봅니다.
services: azure-resource-manager
author: WilliamDAssafMSFT
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: quickstart
ms.custom: subject-armqs, mode-arm
ms.author: wiassaf
ms.date: 08/07/2020
ms.openlocfilehash: 2c92881cff1d773fbf51e10d40de77ef355d4b67
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133358739"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure Synapse 작업 영역 만들기

이 ARM 템플릿(Azure Resource Manager 템플릿)은 기본 Data Lake Storage가 있는 Azure Synapse 작업 영역을 만듭니다. Azure Synapse 작업 영역은 Azure Synapse Analytics의 분석 프로세스에 대한 보안 가능한 협업 경계입니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure 1에 배포](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

**시각화** 링크를 선택하여 템플릿을 검토할 수 있습니다. 그런 다음, **템플릿 편집** 을 선택합니다.

[![시각화](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

템플릿은 다음 두 가지 리소스를 정의합니다.

- 스토리지 계정
- 작업 영역

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하여 Azure에 로그인하고 템플릿을 엽니다. 이 템플릿은 Synapse 작업 영역을 만듭니다.

   [![Azure 2에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. 다음 값을 입력하거나 업데이트합니다.

   - **구독**: Azure 구독을 선택합니다.
   - **리소스 그룹**: **새로 만들기** 를 선택하고, 리소스 그룹의 고유한 이름을 입력하고, **확인** 을 선택합니다. 새 리소스 그룹은 리소스 정리를 용이하게 합니다.
   - **지역**: 지역을 선택합니다.  예: **미국 중부**
   - **Name**: 작업 영역의 이름을 입력합니다.
   - **SQL 관리자 로그인**: SQL Server의 관리자 사용자 이름을 입력합니다.
   - **SQL 관리자 암호**: SQL Server의 관리자 암호를 입력합니다.
   - **태그 값**: 기본값을 적용합니다.
   - **검토 및 만들기**: 선택합니다.
   - **만들기**: 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure Synapse Analytics 및 Azure Resource Manager에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Synapse Analytics 개요](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 읽기
- [Azure Resource Manager](../azure-resource-manager/management/overview.md)에 대해 자세히 알아보기
- [첫 번째 ARM 템플릿 만들기 및 배포](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
