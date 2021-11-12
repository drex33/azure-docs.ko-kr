---
title: 콜론을 사용하여 애플리케이션 중심 환경 만들기
description: 이 문서에서는 Colony 및 Azure를 사용하여 애플리케이션 중심 환경을 만드는 방법을 보여줍니다.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 9ce80a7467ac94a69227cc9616c72c622a6f575f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347246"
---
# <a name="create-an-application-centric-environment-with-colony"></a>콜론을 사용하여 애플리케이션 중심 환경 만들기

[Quali CloudShell Colony는](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) 대규모 인프라 자동화를 제공하기 위한 SaaS(Software-as-a-Service) 플랫폼입니다. Colony는 개발자가 Azure 및 Kubernetes와 같은 복잡한 클라우드 환경에서 애플리케이션을 배포하는 데 도움을 주기 위해 작동합니다. 콜론이 애플리케이션 배포 프로세스 전체에서 프로덕션까지 Azure DevTest Labs 보완합니다. 이 문서에서는 Colony 및 Azure를 사용하여 애플리케이션 중심 환경을 만드는 방법을 보여줍니다.

## <a name="set-up-the-environment-with-colony-and-microsoft-azure"></a>콜론 및 Microsoft Azure 환경 설정

1. [Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) 무료 평가판에 등록하세요.

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="콜론 평가판 등록을 보여 주는 스크린샷.":::
1. [Azure 계정을 연결합니다.](https://colonysupport.quali.com/hc/articles/360008222234)

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="콜론 시작 화면의 스크린샷.":::
1. 사용자를 공간에 초대합니다.
1. [YAML 파일 를 사용하여 첫 번째 청사진을 만듭니다.](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint)
    1. GitHub 또는 BitBucket 청사진 리포지블리를 Colony에 연결합니다.
    1. 콜론 샘플 청사진을 기초로 사용하고 적절하게 수정합니다.

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="스트레스 테스트를 보여 주는 스크린샷":::
    1. 다른 사람이 사용할 수 있도록 청사진을 게시합니다.
1. 콜론을 사용하여 애플리케이션 환경을 샌드박스로 시작합니다.

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="콜론을 사용하여 샌드박스로 애플리케이션 환경을 시작하는 스크린샷.":::

Azure Pipelines CI/CD(연속 통합 및 지속적인 업데이트) 워크플로의 일부로 청사진을 통합할 수도 있습니다. 단계는 [VSTS(Azure DevOps 샌드박스 시작)를 참조하세요.](https://colonysupport.quali.com/hc/articles/360008464234)

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Azure Pipelines 파이프라인에 연결을 보여 주는 스크린샷.":::

## <a name="next-steps"></a>다음 단계

[Colony 데모 요청](https://info.quali.com/cloudshell-colony-demo-request)
