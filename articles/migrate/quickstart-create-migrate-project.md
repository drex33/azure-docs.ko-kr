---
title: Azure Resource Manager 템플릿을 사용하여 Azure Migrate 프로젝트를 만들기 위한 빠른 시작입니다.
description: 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Migrate 프로젝트를 만드는 방법을 알아봅니다.
ms.date: 04/23/2021
author: rahulg1190
manager: bsiva
ms.author: rahugup
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 4380b52489a483faf8063516cf2d3fd6af6b061d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726235"
---
# <a name="quickstart-create-an-azure-migrate-project-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure Migrate 프로젝트 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Migrate 프로젝트 복구를 설정하는 방법을 설명합니다. Azure Migrate는 온-프레미스 서버, 인프라, 애플리케이션 및 데이터를 평가하고 Azure로 마이그레이션할 수 있는 중앙 허브를 제공합니다. Azure Migrate는 온-프레미스 VMware VM, Hyper-V VM, 물리적 서버, 기타 가상화된 VM, 데이터베이스, 웹앱 및 가상 데스크톱의 평가 및 마이그레이션을 지원합니다.

이 템플릿은 Azure 온-프레미스 서버, 인프라, 애플리케이션 및 데이터를 더욱 자세히 평가하고 마이그레이션하는 데 사용되는 Azure Migrate 프로젝트를 만듭니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.migrate%2Fmigrate-project-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

활성 Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만든 후에 시작할 수 있습니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/migrate-project-create/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.migrate/migrate-project-create/azuredeploy.json":::

## <a name="deploy-the-template"></a>템플릿 배포

템플릿을 배포하려면 **구독**, **리소스 그룹**, **프로젝트 이름** 및 **위치** 가 필요합니다.

1. Azure에 로그인하고 템플릿을 열려면 **Azure에 배포** 이미지를 선택합니다.

   [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.migrate%2Fmigrate-project-create%2Fazuredeploy.json)

2. 다음 값을 선택하거나 입력합니다.

   :::image type="content" source="media/quickstart-create-migrate-project-template/create-migrate-project.png" alt-text="Azure Migrate 프로젝트를 만드는 템플릿":::

   - **구독**: Azure 구독을 선택합니다.
   - **리소스 그룹**: 기존 그룹을 선택하거나 **새로 만들기** 를 선택하여 그룹을 추가합니다.
   - **지역**: 리소스 그룹 위치를 기본값으로 설정합니다. 리소스 그룹을 선택한 후에는 사용할 수 없게 됩니다.
   - **Migrate 프로젝트 이름**: Vault 이름을 입력합니다.
   - **위치**: Azure Migrate 프로젝트 및 해당 리소스를 배포하려는 위치를 선택합니다.

3. **검토 + 만들기** 단추를 클릭하여 배포를 시작합니다.

## <a name="validate-the-deployment"></a>배포 유효성 검사

Azure Migrate 프로젝트를 만들었는지 확인하려면 Azure Portal을 사용합니다.


1. Azure Portal의 검색 창에서 **Azure Migrate** 를 검색하여 Azure Migrate로 이동합니다.
2. 서버, 데이터베이스 및 웹앱 타일 아래에서 **검색**, **평가** 및 **마이그레이션** 단추를 클릭합니다.
3. 배포에 지정된 값에 따라 **Azure 구독** 및 **프로젝트** 를 선택합니다.


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Migrate 프로젝트를 만들었습니다. Azure Migrate 및 해당 기능을 자세히 알아보려면 Azure Migrate 개요를 계속하세요.

> [!div class="nextstepaction"]
> [Azure Migrate 개요](migrate-services-overview.md)
>
