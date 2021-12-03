---
title: 핫픽스 프로덕션 환경 사용
description: Azure Data Factory 파이프라인에서 지속적인 통합 및 배달과 함께 핫픽스 프로덕션 환경을 사용 하는 방법에 대해 알아봅니다.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5ee1ce5ff46d44a77ce7412857f9c311fdba6f3e
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133519717"
---
# <a name="using-a-hotfix-production-environment"></a>핫픽스 프로덕션 환경 사용

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

팩터리를 프로덕션에 배포하고 즉시 해결해야 하는 버그가 있지만 현재의 협업 분기를 배포할 수 없는 경우 핫픽스를 배포해야 할 수 있습니다. 이 접근 방식을 QFE(Quick-Fix Engineering)라고 합니다.

## <a name="steps-to-deploy-a-hotfix"></a>핫픽스를 배포 하는 단계

프로덕션 및 테스트 환경에 핫픽스를 배포 하려면 다음 단계를 사용 합니다.

1.    Azure DevOps에서 프로덕션에 배포된 릴리스로 이동합니다. 배포된 마지막 커밋을 찾습니다.

1.    커밋 메시지에서 협업 분기의 커밋 ID를 가져옵니다.

1.    해당 커밋에서 새 핫픽스 분기를 생성합니다.

1.    Azure Data Factory Studio로 이동 하 여 핫픽스 분기로 전환 합니다.

1.    Azure Data Factory Studio를 사용 하 여 버그를 수정 합니다. 변경 내용을 테스트합니다.

1.    수정 사항을 확인한 후 **ARM 템플릿 내보내기** 를 선택하여 핫픽스 Resource Manager 템플릿을 가져옵니다.

1.    adf_publish 분기에서 이 빌드를 수동으로 확인합니다.

1.    adf_publish 체크 인에 따라 자동으로 트리거되도록 릴리스 파이프라인을 구성한 경우 새 릴리스가 자동으로 시작됩니다. 그렇지 않은 경우 수동으로 릴리스를 큐에 넣습니다.

1.    핫픽스 릴리스를 테스트 및 프로덕션 팩터리에 배포합니다. 이 릴리스에는 이전 프로덕션 페이로드와 5단계에서 생성된 수정 사항이 포함되어 있습니다.

1.   이후 릴리스에 동일한 버그가 포함되지 않도록 핫픽스의 변경 내용을 개발 분기에 추가합니다.

## <a name="video-tutorial"></a>비디오 자습서
아래 비디오에서 환경을 핫픽스하는 방법에 대한 자세한 비디오 자습서를 참조하세요. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4I7fi]

## <a name="next-steps"></a>다음 단계

- [연속 통합 및 지속적인 업데이트에 대한 자동화된 게시](continuous-integration-delivery-improvements.md)
- [연속 통합 및 지속적인 업데이트 개요](continuous-integration-delivery.md)
- [Azure Pipelines 릴리스를 사용 하 여 연속 통합 자동화](continuous-integration-delivery-automate-azure-pipelines.md)
- [리소스 관리자 템플릿을 각 환경으로 수동으로 승격](continuous-integration-delivery-manual-promotion.md)
- [Resource Manager 템플릿에서 사용자 지정 매개 변수 사용](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [연결된 Resource Manager 템플릿](continuous-integration-delivery-linked-templates.md)
- [ 샘플 배포 전 및 배포 후 스크립트](continuous-integration-delivery-sample-script.md)
