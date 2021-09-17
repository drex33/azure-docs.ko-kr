---
title: Azure Portal에서 템플릿 내보내기
description: Azure Portal을 사용하여 구독의 리소스에서 Azure Resource Manager 템플릿을 내보냅니다.
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: c6987f95f2ccb953977244d8ff70b2f925f35f2e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436249"
---
# <a name="use-azure-portal-to-export-a-template"></a>Azure Portal 사용하여 템플릿 내보내기

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

이 문서에서는 **포털을** 통해 템플릿을 내보내는 방법을 보여줍니다. 다른 옵션은 다음을 참조하세요.

* [Azure CLI 템플릿 내보내기](export-template-cli.md)
* [Azure PowerShell 템플릿 내보내기](export-template-powershell.md)
* [REST API 리소스 그룹에서 내보내고](/rest/api/resources/resourcegroups/exporttemplate) [배포 기록 에서 내보내는 REST API.](/rest/api/resources/deployments/export-template)

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>리소스 그룹에서 템플릿 내보내기

리소스 그룹에서 하나 이상의 리소스를 내보내려면 다음을 수행합니다.

1. 내보낼 리소스를 포함하는 리소스 그룹을 선택합니다.

1. 확인란을 선택하여 하나 이상의 리소스를 선택합니다.  모두 선택하려면 **이름** 왼쪽에 있는 확인란을 선택합니다. **템플릿 내보내기** 메뉴 항목은 하나 이상의 리소스를 선택한 후에만 사용하도록 설정됩니다.

   ![모든 리소스 내보내기](./media/export-template-portal/select-all-resources.png)

    스크린샷에는 스토리지 계정만 선택되어 있습니다.
1. **템플릿 내보내기** 를 선택합니다.

1. 내보낸 템플릿이 표시되며 다운로드하고 배포할 수 있습니다.

   ![템플릿 표시](./media/export-template-portal/show-template.png)

   기본적으로 **매개 변수 포함** 이 선택됩니다.  선택하면 템플릿을 생성할 때 모든 템플릿 매개 변수가 포함됩니다. 고유 매개 변수를 작성하려면 매개 변수를 포함하지 않도록 이 확인란을 토글합니다.

## <a name="export-template-from-a-resource"></a>리소스에서 템플릿 내보내기

한 리소스를 내보내려면 다음을 수행합니다.

1. 내보내려는 리소스를 포함하는 리소스 그룹을 선택합니다.

1. 리소스를 열기 위해 내보낼 리소스를 선택합니다.

1. 해당 리소스에 대해 왼쪽 창에서 **템플릿 내보내기** 를 선택합니다.

   ![리소스 내보내기](./media/export-template-portal/export-single-resource.png)

1. 내보낸 템플릿이 표시되며 다운로드하고 배포할 수 있습니다. 템플릿에는 단일 리소스만 포함됩니다. 기본적으로 **매개 변수 포함** 이 선택됩니다.  선택하면 템플릿을 생성할 때 모든 템플릿 매개 변수가 포함됩니다. 고유 매개 변수를 작성하려면 매개 변수를 포함하지 않도록 이 확인란을 토글합니다.

## <a name="download-template-before-deployment"></a>배포 전 템플릿 다운로드

포털에는 템플릿을 배포하기 전에 다운로드할 수 있는 옵션이 있습니다. 이 옵션은 PowerShell 또는 Azure CLI 통해 사용할 수 없습니다.

1. 배포하려는 Azure 서비스를 선택합니다.

1. 새 서비스의 값을 입력합니다.

1. 유효성 검사를 통과한 후 배포를 시작하기 전에 **자동화를 위한 템플릿 다운로드** 를 선택합니다.

   ![템플릿 다운로드](./media/export-template-portal/download-before-deployment.png)

1. 템플릿이 표시되며 다운로드하고 배포할 수 있습니다.

## <a name="export-template-after-deployment"></a>배포 후 템플릿 내보내기

기존 리소스를 배포하는 데 사용된 템플릿을 내보낼 수 있습니다. 배포에 사용된 것과 똑같은 템플릿을 얻을 수 있습니다.

1. 내보내려는 리소스 그룹을 선택합니다.

1. **배포** 에서 링크를 선택합니다.

   ![배포 기록 선택](./media/export-template-portal/select-deployment-history.png)

1. 배포 기록에서 배포 중 하나를 선택합니다.

   ![배포 선택](./media/export-template-portal/select-details.png)

1. **템플릿** 을 선택합니다. 이 배포에 사용되는 템플릿이 표시되며 다운로드할 수 있습니다.

   ![템플릿 선택](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>다음 단계

- [Azure CLI](export-template-cli.md), [Azure PowerShell](export-template-powershell.md), [REST API](/rest/api/resources/resourcegroups/exporttemplate)를 사용하여 템플릿을 내보내는 방법을 알아봅니다.
- Resource Manager 템플릿 구문에 대해 알아보려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하세요.
- 템플릿을 개발하는 방법을 알아보려면 [단계별 자습서](../index.yml)를 참조하세요.