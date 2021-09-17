---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/31/2021
ms.author: tomfitz
ms.openlocfilehash: 76c1ed0a2a1b1a2cca77988d218e2460f99003e6
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436226"
---
## <a name="limitations"></a>제한 사항

리소스 그룹 또는 리소스에서 내보낼 때 내보낸 템플릿은 각 리소스 종류의 [게시된 스키마](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas)에서 생성됩니다. 스키마에 리소스 종류의 최신 버전이 없는 경우도 있습니다. 내보낸 템플릿을 확인하여 필요한 속성이 포함되어 있는지 확인합니다. 필요한 경우, 필요한 API 버전을 사용하도록 내보낸 템플릿을 편집합니다.

템플릿 내보내기 기능은 Azure Data Factory 리소스 내보내기를 지원하지 않습니다. Data Factory 리소스를 내보내는 방법에 관한 자세한 내용은 [Azure Data Factory에서 데이터 팩터리 복사 또는 복제](../articles/data-factory/copy-clone-data-factory.md)를 참조하세요.

클래식 배포 모델을 통해 만든 리소스를 내보내려면 [Resource Manager 배포 모델로 마이그레이션](../articles/virtual-machines/migration-classic-resource-manager-overview.md)해야 합니다.

리소스 종류를 내보내지 못했음을 나타내는 템플릿을 내보낼 때 경고가 표시되면 해당 리소스의 속성을 계속 검색할 수 있습니다. 리소스 속성은 [템플릿 참조](/azure/templates)를 참조 하세요. 리소스 종류의 [Azure REST API](/rest/api/azure/)도 살펴볼 수 있습니다.

내보낸 템플릿을 만들 리소스 그룹의 리소스는 200개로 제한됩니다. 200개가 넘는 리소스가 있는 리소스 그룹을 내보내려는 경우 `Export template is not supported for resource groups more than 200 resources` 오류 메시지가 표시됩니다.
