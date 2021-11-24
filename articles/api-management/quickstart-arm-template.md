---
title: 빠른 시작 - ARM 템플릿을 사용하여 Azure API Management 인스턴스 만들기
description: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 개발자 계층에서 Azure API Management 인스턴스를 만드는 방법을 알아봅니다.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs, mode-arm
ms.author: danlep
ms.date: 10/09/2020
ms.openlocfilehash: 8522a755e613d659f818cf7c5c1b32621e55448b
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133068957"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 새 Azure API Management 서비스 인스턴스 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure APIM(API Management) 서비스 인스턴스를 만드는 방법을 설명합니다. APIM은 조직이 API를 외부, 파트너 및 내부 개발자에게 게시하여 해당 데이터 및 서비스의 잠재력을 발휘하도록 도와줍니다. API Management는 개발자 참여, 비즈니스 통찰력, 분석, 보안과 보호 등을 통해 성공적인 API 프로그램을 보장하는 핵심적인 역량을 제공합니다. APIM을 사용하면 어디서든 호스팅되는 기존 백 엔드 서비스를 위한 최신 API 게이트웨이를 만들고 관리할 수 있습니다. 자세한 내용은 [개요](api-management-key-concepts.md)를 참조하세요.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fazure-api-management-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/azure-api-management-create/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.apimanagement/azure-api-management-create/azuredeploy.json":::

템플릿에 다음 리소스가 정의되어 있습니다.

- **[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service)**

추가 Azure API Management 템플릿 샘플은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Apimanagement&pageNumber=1&sort=Popular)에서 찾을 수 있습니다.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿은 자동으로 생성된 이름으로 API Management 서비스 인스턴스를 만듭니다.

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.apimanagement%2Fazure-api-management-create%2Fazuredeploy.json)

    이 예제에서 인스턴스는 Azure API Management를 평가하는 경제적인 옵션인 개발자 계층에서 구성됩니다. 이 계층은 프로덕션 용도가 아닙니다. API Management 계층 크기 조정에 대한 자세한 내용은 [업그레이드 및 크기 조정](upgrade-and-scale.md)을 참조하세요.

1. 다음 값을 선택하거나 입력합니다.
    - **구독**: Azure 구독을 선택합니다.
    - **리소스 그룹**: **새로 만들기** 를 선택하고 리소스 그룹에 고유한 이름을 입력한 다음, **확인** 을 선택합니다.
    - **지역**: 리소스 그룹에 대한 위치를 선택합니다. 예제: **미국 중부**
    - **게시자 이메일**: 알림을 받을 이메일 주소를 입력합니다.
    - **게시자 이름**: API 게시자에 대해 선택한 이름을 입력합니다.
    - **Sku**: **개발자** 의 기본값을 적용합니다.
    - **Sku 수**: 기본값을 적용합니다.
    - **위치**: API Management 서비스에 대해 생성된 위치를 적용합니다.

    :::image type="content" source="media/quickstart-arm-template/create-instance-template.png" alt-text="API Management 템플릿 속성":::

1. **검토 + 만들기** 를 선택한 다음, 사용 약관을 검토합니다. 동의하면 **만들기** 를 선택합니다.

    > [!TIP]
    >  개발자 계층에서 API Management 서비스를 만들고 활성화하는 데 30~40분 정도 걸릴 수 있습니다.

1. 인스턴스가 성공적으로 만들어지면 다음과 같이 알림을 받게 됩니다.

    :::image type="content" source="media/quickstart-arm-template/deployment-notification.png" alt-text="배포 알림":::

 Azure Portal은 템플릿을 배포하는데 사용됩니다. Azure Portal 외에도 Azure PowerShell, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../azure-resource-manager/templates/deploy-cli.md)를 참조하세요.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal을 사용하여 배포된 리소스를 확인하거나 Azure CLI 또는 Azure PowerShell과 같은 도구를 사용하여 배포된 리소스를 나열합니다.

1. [Azure Portal](https://portal.azure.com)에서 **API Management 서비스** 를 검색하여 선택하고 만든 서비스 인스턴스를 선택합니다.
1. **개요** 페이지에서 서비스의 속성을 검토합니다.

:::image type="content" source="media/quickstart-arm-template/service-instance-created.png" alt-text="서비스 개요 페이지":::

API Management 서비스 인스턴스가 온라인 상태이면 사용할 준비가 된 것입니다. 자습서를 시작하여 첫 번째 API를 [가져오고 게시](import-and-publish.md)하세요.

## <a name="clean-up-resources"></a>리소스 정리

후속 자습서를 계속 사용하려는 경우 API Management 인스턴스를 그대로 두는 것이 좋습니다. 더 이상 필요 없으면 리소스 그룹을 삭제합니다. 그러면 리소스 그룹의 리소스가 삭제됩니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 그룹** 을 검색하여 선택합니다. **홈** 페이지에서 **리소스 그룹** 을 선택할 수도 있습니다.
1. **리소스 그룹** 페이지에서 리소스 그룹을 선택합니다.
1. 리소스 그룹 페이지에서 **리소스 그룹 삭제** 를 선택합니다.

    리소스 그룹 삭제
1. 리소스 그룹의 이름을 입력하고 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 첫 번째 API 가져오기 및 게시](import-and-publish.md)
