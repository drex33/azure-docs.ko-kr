---
title: 관리 허브
description: Azure Data Factory 관리 허브에서 연결, 소스 제어 구성 및 전역 작성 속성 관리
ms.service: data-factory
ms.subservice: authoring
ms.topic: conceptual
author: minhe-msft
ms.author: hemin
ms.date: 04/27/2021
ms.openlocfilehash: 68935be6983a6503016deadc7cddd5b8ae9bb588
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793116"
---
# <a name="management-hub-in-azure-data-factory"></a>Azure Data Factory의 관리 허브

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory UX의 *관리* 탭에서 액세스하는 관리 허브는 데이터 팩터리에 대한 전역 관리 작업을 호스팅하는 포털입니다. 여기에서 데이터 저장소 및 외부 컴퓨팅, 소스 제어 구성 및 트리거 설정에 대한 연결을 관리할 수 있습니다.

## <a name="manage-connections"></a>연결 관리

### <a name="linked-services"></a>연결된 서비스

연결된 서비스는 외부 데이터 저장소 및 컴퓨팅 환경에 연결하기 위한 Azure Data Factory에 대한 연결 정보를 정의합니다. 자세한 내용은 [연결된 서비스 개념](concepts-linked-services.md)을 참조하세요. 연결된 서비스 만들기, 편집 및 삭제는 관리 허브에서 수행됩니다.

:::image type="content" source="media/author-management-hub/management-hub-linked-services.png" alt-text="연결된 서비스 관리":::

### <a name="integration-runtimes"></a>통합 런타임

통합 런타임은 서로 다른 네트워크 환경에서 데이터 통합 기능을 제공하기 위해 Azure Data Factory에서 사용하는 컴퓨팅 인프라입니다. 자세한 내용은 [Azure 통합 런타임 개념](concepts-integration-runtime.md)에 대해 알아보세요. 관리 허브에서 통합 런타임을 만들고, 삭제하고, 모니터링할 수 있습니다.

:::image type="content" source="media/author-management-hub/management-hub-integration-runtime.png" alt-text="통합 런타임 관리":::

## <a name="manage-source-control"></a>소스 제어 관리

### <a name="git-configuration"></a>Git 구성

관리 허브의 Git 구성 설정에서 모든 Git 관련 정보를 보고/편집할 수 있습니다. 

마지막으로 게시된 커밋 정보도 나열되며, 환경에서 마지막으로 게시/배포된 정확한 커밋을 이해하는 데 도움이 될 수 있습니다. 프로덕션에서 핫 픽스를 수행하는 경우에도 유용할 수 있습니다.

자세한 내용은 [Azure Data Factory의 소스 제어](source-control.md)에 대해 알아보세요.

:::image type="content" source="media/author-management-hub/management-hub-git.png" alt-text="git 리포지토리 관리":::

### <a name="parameterization-template"></a>매개 변수화 템플릿

협업 분기에서 게시할 때 생성된 Resource Manager 템플릿 매개 변수를 재정의하기 위해 사용자 지정 매개 변수 파일을 생성하거나 편집할 수 있습니다. 자세한 내용은 [Resource Manager 템플릿에서 사용자 지정 매개 변수를 사용](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template)하는 방법을 알아보세요. 매개 변수화 템플릿은 git 리포지토리에서 작업하는 경우에만 사용할 수 있습니다. *arm-template-parameters-definition.json* 파일이 작업 분기에 없는 경우 기본 템플릿을 편집하면 해당 파일이 생성됩니다.

:::image type="content" source="media/author-management-hub/management-hub-custom-parameters.png" alt-text="사용자 지정 매개 변수 관리":::

## <a name="manage-authoring"></a>작성 관리

### <a name="triggers"></a>트리거

트리거는 파이프라인 실행을 시작해야 하는 시기를 결정합니다. 현재 트리거는 벽시계 일정에 있거나, 정기적으로 작동하거나, 이벤트에 따라 달라질 수 있습니다. 자세한 내용은 [트리거 실행](concepts-pipeline-execution-triggers.md#trigger-execution)에 대해 알아보세요. 관리 허브에서 트리거의 현재 상태를 생성, 편집, 삭제 또는 볼 수 있습니다.

:::image type="content" source="media/author-management-hub/management-hub-triggers.png" alt-text="트리거의 현재 상태를 생성, 편집, 삭제 또는 볼 수 있는 위치를 보여주는 스크린샷.":::

### <a name="global-parameters"></a>글로벌 매개 변수

전역 매개 변수는 모든 식의 파이프라인에서 사용할 수 있는 데이터 팩터리 간의 상수입니다. 자세한 내용은 [전역 매개 변수](author-global-parameters.md)에 대해 알아보세요.

:::image type="content" source="media/author-global-parameters/create-global-parameter-3.png" alt-text="전역 매개 변수 만들기":::

## <a name="next-steps"></a>다음 단계

ADF에 [git 리포지토리를 구성](source-control.md)하는 방법 알아보기


