---
title: Resource Manager 템플릿의 수동 승격
description: Azure Data Factory 연속 통합 및 제공을 통해 Resource Manager 템플릿을 여러 환경으로 수동으로 승격하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 367a0b7f231fcdd88a28237e83916995fd58062b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129220438"
---
# <a name="manually-promote-a-resource-manager-template-to-each-environment"></a>Resource Manager 템플릿을 각 환경으로 수동으로 승격

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory 연속 통합 및 제공을 위해 Resource Manager 템플릿을 각 환경으로 승격하려면 아래 단계를 사용합니다.

## <a name="steps-to-manually-promote-a-template"></a>템플릿을 수동으로 승격하는 단계

1. 데이터 팩터리의 **관리** 허브로 이동하고 “원본 제어” 섹션에서 **ARM 템플릿** 을 선택합니다. **ARM 템플릿** 섹션에서 **ARM 템플릿 내보내기** 를 선택하여 개발 환경에서 데이터 팩터리의 Resource Manager 템플릿을 내보냅니다.

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image-1.png" alt-text="Resource Manager 템플릿 내보내기":::

1. 테스트 및 프로덕션 Data Factory에서 **ARM 템플릿 가져오기** 를 선택합니다. 이 작업을 통해 Azure Portal로 이동합니다. 여기서 내보낸 템플릿을 가져올 수 있습니다. **편집기에서 사용자 고유의 템플릿 빌드** 를 선택하여 Resource Manager 템플릿 편집기를 엽니다.

   :::image type="content" source="media/continuous-integration-delivery/custom-deployment-build-your-own-template.png" alt-text="사용자 고유의 템플릿 빌드"::: 

1. **파일 로드** 를 선택하고 생성된 Resource Manager 템플릿을 선택합니다. 이는 1단계에서 내보낸 .zip 파일에 있는 **arm_template.json** 파일입니다.

   :::image type="content" source="media/continuous-integration-delivery/custom-deployment-edit-template.png" alt-text="템플릿 편집":::

1. 설정 섹션에서 연결된 서비스 자격 증명과 같은 구성 값을 입력합니다. 완료되면 **구매** 를 선택하여 Resource Manager 템플릿을 배포합니다.

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image5.png" alt-text="설정 섹션":::

## <a name="next-steps"></a>다음 단계

- [연속 통합 및 지속적인 업데이트 개요](continuous-integration-delivery.md)
- [Azure Pipelines 릴리스를 사용하여 연속 통합 자동화](continuous-integration-delivery-automate-azure-pipelines.md)
- [Resource Manager 템플릿에서 사용자 지정 매개 변수 사용](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [연결된 Resource Manager 템플릿](continuous-integration-delivery-linked-templates.md)
- [핫픽스 프로덕션 환경 사용](continuous-integration-delivery-hotfix-environment.md)
- [샘플 배포 전 및 배포 후 스크립트](continuous-integration-delivery-sample-script.md)