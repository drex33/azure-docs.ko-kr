---
title: 작업 항목 통합 - Application Insights
description: Application Insights 데이터가 포함되어 있는 GitHub 또는 Azure DevOps에서 작업 항목을 만드는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 06/27/2021
ms.openlocfilehash: 02fbb15f417d01d1f9c5b572fdb5553ad1127037
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112989143"
---
# <a name="work-item-integration"></a>작업 항목 통합 

작업 항목 통합 기능을 사용하면 관련 Application Insights 데이터가 포함되어 있는 GitHub 또는 Azure DevOps에서 쉽게 작업 항목을 만들 수 있습니다.


새 작업 항목 통합은 [클래식](#classic-work-item-integration)에 비해 다음과 같은 기능을 제공합니다.
- 담당자, 프로젝트 또는 이정표와 같은 고급 필드.
- GitHub와 Azure DevOps 통합 문서를 구분할 수 있도록 리포지토리 아이콘.
- 임의의 수의 리포지토리 또는 작업 항목에 대한 다중 구성.
- Azure Resource Manager 템플릿을 통한 배포.
- 작업 항목에 Application Insights 데이터를 추가하기 위한 사전 구축 및 사용자 지정 가능한 KQL(키워드 쿼리 언어) 쿼리입니다.
- 사용자 정의 가능한 통합 문서 템플릿.


## <a name="create-and-configure-a-work-item-template"></a>작업 항목 템플릿 만들기 및 구성

1. 작업 항목 템플릿을 만들려면 Application Insights 리소스로 이동하여 ‘구성’ 아래 왼쪽에서 **작업 항목** 을 선택하고 맨 위에서 **새 템플릿 만들기** 를 선택합니다.

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" 새 템플릿 만들기가 선택된 작업 항목 탭 스크린샷입니다." lightbox="./media/work-item-integration/create-work-item-template.png":::

    현재 템플릿이 없는 경우 엔드투엔드 간 트랜잭션 세부 정보 탭에서 작업 항목 템플릿을 만들 수도 있습니다. 이벤트를 선택하고 오른쪽에서 **작업 항목 만들기**, **통합 문서 템플릿으로 시작** 을 차례로 선택합니다.

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" 작업 항목 만들기, 통합 문서 템플릿으로 시작이 선택된 엔드투엔드 트랜잭션 세부 정보 탭 스크린샷입니다." lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. **새 템플릿 만들기** 를 선택하면 추적 시스템을 선택하고 통합 문서 이름을 지정하고 선택한 추적 시스템에 연결하고 템플릿을 저장할 지역(기본값은 Application Insights 리소스가 있는 지역)을 선택할 수 있습니다. URL 매개 변수는 리포지토리의 기본 URL입니다(예: `https://github.com/myusername/reponame` 또는 `https://mydevops.visualstudio.com/myproject`).

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" 새 작업 항목 통합 문서 템플릿 만들기 스크린샷입니다.":::

    템플릿 자체에서 직접 특정 작업 항목 속성을 설정할 수 있습니다. 버전 제어 공급자에 따라 담당자, 반복 경로, 프로젝트, 등이 포함됩니다.

## <a name="create-a-work-item"></a>작업 항목 만들기

 성능, 오류, 가용성 또는 기타 탭에서 액세스할 수 있는 엔드투엔드 트랜잭션 세부 정보에서 새 템플릿에 액세스할 수 있습니다.

1. 작업 항목을 만들려면 엔드투엔드 트랜잭션 세부 정보로 이동하여 이벤트를 선택하고 **작업 항목 만들기** 를 선택하고 작업 항목 템플릿을 선택합니다.

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" 작업 항목 만들기가 선택된 엔드투엔드 트랜잭션 세부 정보 탭 스크린샷입니다." lightbox="./media/work-item-integration/create-work-item.png":::

1. 브라우저에 추적 시스템을 선택하는 새 탭이 열립니다. Azure DevOps에서는 버그나 작업을 만들 수 있고 GitHub에서는 리포지토리에 새 이슈를 만들 수 있습니다. Application Insights에서 제공하는 컨텍스트 정보로 새 작업 항목이 자동으로 만들어집니다.

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" 자동으로 만들어진 GitHub 이슈 스크린샷입니다." lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" Azure DevOps의 자동으로 버그 만들기 스크린샷입니다." lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>템플릿 편집

템플릿을 편집하려면 ‘구성’ 아래의 **작업 항목** 탭으로 이동하여 업데이트할 통합 문서 옆에 있는 연필 아이콘을 선택합니다.

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" 편집 연필 아이콘이 선택된 작업 항목 탭의 스크린샷입니다.":::

맨 위에서 편집(![편집 아이콘](./media/work-item-integration/edit-icon.png))을 선택하여 템플릿 편집을 시작합니다. 작업 항목 템플릿은 [Azure Monitor 통합 문서](../visualize/workbooks-overview.md)를 기반으로 합니다. 작업 항목 정보는 키워드 쿼리 언어를 사용하여 생성됩니다. 쿼리를 수정하여 팀에 필요한 컨텍스트를 더 추가할 수 있습니다. 편집을 완료한 후에는 맨 위 도구 모음에서 저장 아이콘(![저장 아이콘](./media/work-item-integration/save-icon.png))을 선택하여 통합 문서를 저장합니다.

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" 편집 모드의 작업 항목 템플릿 통합 문서 스크린샷입니다." lightbox="./media/work-item-integration/edit-workbook.png":::

각 시나리오에 맞는 사용자 지정 통합 문서가 포함되도록 작업 항목 구성을 두 개 이상 만들 수 있습니다. 환경 전체에서 표준 구현이 보장되도록 Azure Resource Manager에서 통합 문서를 배포할 수도 있습니다.

## <a name="classic-work-item-integration"></a>클래식 작업 항목 통합 

1. *구성* 아래의 Application Insights 리소스에서 **작업 항목** 을 선택합니다.
1. **클래식으로 전환** 을 선택하고 필드에 정보를 입력한 다음 승인합니다. 

    :::image type="content" source="./media/work-item-integration/classic.png" alt-text=" 클래식 작업 항목을 구성하는 방법에 대한 스크린샷." lightbox="./media/work-item-integration/classic.png":::

1. 엔드투엔드 트랜잭션 세부정보로 이동하여 작업 항목을 만들고 이벤트를 선택한 다음 **작업 항목 만들기(클래식)** 를 선택합니다. 


### <a name="migrate-to-new-work-item-integration"></a>새 작업 항목 통합으로 마이그레이션

마이그레이션하려면 기본 작업 항목 구성을 삭제한 다음 [작업 항목 템플릿을 만들고 구성](#create-and-configure-a-work-item-template)하여 통합을 다시 만드십시오.

삭제하려면 Application Insights 리소스의 *구성* 으로 이동하여 **작업 항목** 을 선택한 다음 상단에서 **기본으로 전환** 및 **삭제* 를 선택합니다.


## <a name="next-steps"></a>다음 단계
[가용성 테스트](availability-overview.md)

