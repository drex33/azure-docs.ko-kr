---
title: Azure 역할 기반 액세스 제어 - Custom Vision
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Custom Vision 프로젝트에 대해 Azure 역할 기반 액세스 제어를 구성하는 방법을 보여 줍니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: e2f59dc309181308f409b3533d58d3b6b4b1fdd8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361880"
---
# <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어

Custom Vision은 Azure 리소스에 대한 개별 액세스를 관리하는 권한 부여 시스템인 Azure RBAC(Azure 역할 기반 액세스 제어)를 지원합니다. Azure RBAC를 사용하여 Custom Vision 프로젝트에 대해 다른 수준의 사용 권한을 다른 팀 멤버에게 할당합니다. Azure RBAC에 대한 자세한 내용은 [AZURE RBAC 설명서](../../role-based-access-control/index.yml)를 참조하세요.

## <a name="add-role-assignment-to-custom-vision-resource"></a>Custom Vision 리소스에 역할 할당 추가

Azure RBAC는 Custom Vision 리소스에 할당할 수 있습니다. Azure 리소스에 대한 액세스 권한을 부여하려면 역할 할당을 추가합니다.
1. [Azure Portal](https://ms.portal.azure.com/)에서 **모든 서비스** 를 선택합니다. 
1. 그런 다음 **Cognitive Services** 를 선택하고 특정 Custom Vision 학습 리소스로 이동합니다.
   > [!NOTE]
   > 또한 전체 리소스 그룹, 구독 또는 관리 그룹에 대해 Azure RBAC를 설정할 수 있습니다. 원하는 범위 수준을 선택한 다음 원하는 항목으로 이동하여 이 작업을 수행합니다(예: **리소스 그룹** 을 선택하고 원하는 리소스 그룹으로 클릭하여 선택).
1. 왼쪽 탐색 창에서 **액세스 제어(IAM)** 를 선택합니다.
1. **추가** -> **역할 할당 추가** 를 선택합니다.
1. 다음 화면의 **역할** 탭에서 추가할 역할을 선택합니다.
1. **구성원** 탭에서 사용자, 그룹, 서비스 주체 또는 관리 ID를 선택합니다.
1. **검토 + 할당** 탭에서 **검토 + 할당** 을 선택하여 역할을 할당합니다.

몇 분 이내에 선택한 범위에서 선택한 역할이 대상에 할당됩니다. 이 단계에 대한 도움말은 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

## <a name="custom-vision-role-types"></a>Custom Vision 역할 유형

다음 표를 사용하여 Custom Vision 리소스에 대한 액세스 요구 사항을 확인합니다.

|역할  |사용 권한  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | 프로젝트를 만들거나, 편집하거나, 삭제할 수 있는 기능을 포함하는 프로젝트에 대한 모든 권한입니다.        |
|`Cognitive Services Custom Vision Trainer`     | 프로젝트를 만들거나 삭제하는 기능을 제외한 모든 권한입니다. 훈련자는 프로젝트를 보고 편집할 수 있으며 모델을 학습, 게시, 게시 취소, 또는 내보내기할 수 있습니다.        |
|`Cognitive Services Custom Vision Labeler`     | 학습 이미지를 업로드, 편집 또는 삭제하고 태그를 만들거나 추가, 제거 또는 삭제할 수 있는 기능입니다. 레이블 지정자는 프로젝트를 볼 수 있지만 학습 이미지 및 태그 이외의 항목을 업데이트할 수는 없습니다.         |
|`Cognitive Services Custom Vision Deployment`     | 모델을 게시, 게시 취소 또는 내보낼 수 있는 기능입니다. 배포자는 프로젝트를 볼 수 있지만 프로젝트, 학습 이미지 또는 태그를 업데이트할 수는 없습니다.        |
|`Cognitive Services Custom Vision Reader`     | 프로젝트를 볼 수 있는 기능입니다. 읽기 권한자는 아무것도 변경할 수 없습니다.        |