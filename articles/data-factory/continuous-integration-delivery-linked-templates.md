---
title: 연결된 Resource Manager 템플릿 사용
description: Azure Data Factory 파이프라인에서 지속적인 통합 및 배달을 통해 연결된 Resource Manager 템플릿을 사용하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d2df62471818bbca069802f3c60993ce79bb50dd
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129220449"
---
# <a name="linked-resource-manager-templates-with-cicd"></a>CI/CD를 통해 연결된 Resource Manager 템플릿

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

데이터 팩터리의 CI/CD(연속 통합 및 업데이트)를 설정한 경우 팩터리 증가에 따라 Azure Resource Manager 템플릿 제한을 초과할 수 있습니다. 예를 들어 한 한도는 Resource Manager 템플릿의 최대 리소스 수입니다. 팩터리에 대한 전체 Resource Manager 템플릿을 생성하면서 대형 팩터리를 수용하기 위해 Data Factory가 이제 연결된 Resource Manager 템플릿을 생성합니다. 이 기능을 사용하면 전체 팩터리 페이로드가 여러 파일로 분할되므로 한도에 제한되지 않습니다.

## <a name="finding-the-linked-templates"></a>연결된 템플릿 찾기

Git를 구성한 경우에는 연결된 템플릿이 생성되어 linkedTemplates라는 새 폴더의 adf_publish 분기에 전체 Resource Manager 템플릿과 함께 저장됩니다.

:::image type="content" source="media/continuous-integration-delivery/linked-resource-manager-templates.png" alt-text="연결된 Resource Manager 템플릿 폴더":::

연결된 Resource Manager 템플릿은 일반적으로 마스터 템플릿과 마스터에 연결된 자식 템플릿 세트로 구성됩니다. 부모 템플릿은 ArmTemplate_master.json, 자식 템플릿은 ArmTemplate_0.json, ArmTemplate_1.json 등의 패턴으로 이름이 지정됩니다. 

## <a name="using-linked-templates"></a>연결된 템플릿 사용
전체 Resource Manager 템플릿 대신 연결된 템플릿을 사용하려면 ArmTemplateForFactory.json(전체 Resource Manager 템플릿)이 아닌 ArmTemplate_master.json을 가리키도록 CI/CD 작업을 업데이트합니다. 또한 Resource Manager에서는 Azure가 배포하는 동안 액세스할 수 있도록 연결된 템플릿을 스토리지 계정에 업로드해야 합니다. 자세한 내용은 [VSTS로 연결된 Resource Manager 템플릿 배포](/archive/blogs/najib/deploying-linked-arm-templates-with-vsts)를 참조하세요.

배포 작업 전후에 CI/CD 파이프라인에 Data Factory 스크립트를 추가해야 합니다.

Git가 구성되지 않은 경우 **ARM 템플릿** 목록에서 **ARM 템플릿 내보내기** 를 통해 연결된 템플릿에 액세스할 수 있습니다.

리소스를 배포할 때 배포를 증분 업데이트 또는 전체 업데이트할지 지정합니다. 이러한 두 모드 간 차이점은 Resource Manager가 템플릿에 없는 리소스 그룹에서 기존 리소스를 처리하는 방식입니다. [배포 모드](../azure-resource-manager/templates/deployment-modes.md)를 검토하세요.

## <a name="next-steps"></a>다음 단계

- [연속 통합 및 지속적인 업데이트 개요](continuous-integration-delivery.md)
- [Azure Pipelines 릴리스를 사용하여 연속 통합 자동화](continuous-integration-delivery-automate-azure-pipelines.md)
- [Resource Manager 템플릿을 각 환경으로 수동으로 승격](continuous-integration-delivery-manual-promotion.md)
- [Resource Manager 템플릿에서 사용자 지정 매개 변수 사용](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [핫픽스 프로덕션 환경 사용](continuous-integration-delivery-hotfix-environment.md)
- [샘플 배포 전 및 배포 후 스크립트](continuous-integration-delivery-sample-script.md)