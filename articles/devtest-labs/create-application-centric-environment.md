---
title: 애플리케이션 중심 환경 만들기
description: 이 문서에서는 Cloud Shell Colony 및 Microsoft Azure를 사용하여 애플리케이션 중심 환경을 만드는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 11/26/2020
ms.openlocfilehash: 8b56a39996aa93e10ede28de91ae20857a8eefc0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627580"
---
# <a name="create-an-application-centric-environment"></a>애플리케이션 중심 환경 만들기

[Quali의 CloudShell Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview)는 Azure 및 Kubernetes를 포함한 클라우드 기술의 복잡한 애플리케이션 중심 환경에 대규모로 인프라 자동화를 제공하기 위한 SaaS 플랫폼입니다. CloudShell Colony는 Azure DevTest Labs를 보완하여 개발 팀이 프로덕션에 이르는 데이터 값 스트림 전반에 복잡한 애플리케이션을 배포할 수 있도록 지원합니다.

이 문서에서는 CloudShell Colony 및 Microsoft Azure를 사용하여 애플리케이션 중심 환경을 만드는 방법을 보여 줍니다.

## <a name="set-up-the-environment-with-cloudshell-colony-and-microsoft-azure"></a>CloudShell Colony 및 Microsoft Azure를 사용하여 환경 설정

1. [Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) 무료 평가판에 등록하세요.

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="무료 평가판 등록":::    
1. Azure 계정([여기에서 단계 보기](https://colonysupport.quali.com/hc/articles/360008222234))을 연결합니다.

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="Colony 시작":::     
1. 사용자를 공간에 초대합니다.
1. YAML 파일을 사용하여 첫 번째 청사진을 생성합니다([여기에서 단계 보기](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint)).
    1. GitHub 또는 BitBucket의 청사진 리포지토리를 Colony에 연결합니다.
    1. Colony 샘플 청사진을 기반으로 사용하고 적절하게 수정합니다.

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="스트레스 테스트":::    
    1. 다른 사람이 사용할 수 있도록 청사진을 게시합니다.
1. Colony를 사용하여 애플리케이션 환경을 샌드박스로 시작합니다.

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="Colony를 사용하여 애플리케이션 환경을 샌드박스로 시작":::    

> [!NOTE]
> Azure DevOps에서 청사진을 CI/CD 워크플로의 일부로 통합할 수도 있습니다([여기에서 단계 보기](https://colonysupport.quali.com/hc/articles/360008464234)).

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Azure DevOps 파이프라인에 연결":::    

## <a name="next-steps"></a>다음 단계

[Colony 데모 요청](https://info.quali.com/cloudshell-colony-demo-request)
