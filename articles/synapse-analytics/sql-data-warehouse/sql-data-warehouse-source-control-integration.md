---
title: 소스 제어 통합
description: Azure Repos(Git 및 GitHub)를 사용하는 네이티브 소스 제어 통합이 포함된 전용 SQL 풀에 대한 엔터프라이즈급 데이터베이스 DevOps 환경.
services: synapse-analytics
author: WilliamDAssafMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: wiassaf
ms.reviewer: igorstan
ms.openlocfilehash: 4f4936c84c400590dc228473884c2a05631a424a
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133364988"
---
# <a name="source-control-integration-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 전용 SQL 풀에 대한 소스 제어 통합

이 자습서에서는 SSDT(SQL Server Data Tools) 데이터베이스 프로젝트를 소스 제어와 통합하는 방법에 대해 간략하게 설명합니다.  소스 제어 통합은 Azure Synapse Analytics에서 전용 SQL 풀 리소스를 사용하여 연속 통합 및 배포 파이프라인을 빌드하는 첫 번째 단계입니다.

## <a name="before-you-begin"></a>시작하기 전에

- [Azure DevOps 조직](https://azure.microsoft.com/services/devops/)에 가입
- [만들기 및 연결](create-data-warehouse-portal.md) 자습서 진행
- [Visual Studio 2019 설치](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Azure DevOps 설정 및 연결

1. Azure DevOps 조직에서 Azure Repo 리포지토리를 통해 SSDT 데이터베이스 프로젝트를 호스트하는 프로젝트를 만듭니다.

   ![프로젝트 만들기](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "프로젝트 만들기")

2. Visual Studio를 열고 **연결 관리** 를 선택하여 1단계에서 Azure DevOps 조직 및 프로젝트에 연결합니다.

   ![연결 관리](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "연결 관리")

3. **연결 관리** 를 선택한 다음, 프 **프로젝트에 연결** 을 선택하여 프로젝트에 연결합니다.
 
    ![Connect1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "연결")


4. 1단계에서 만든 프로젝트를 찾고 **연결** 을 선택합니다.
 
    ![Connect2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "연결")


3. 프로젝트에서 로컬 머신으로 Azure DevOps 리포지토리를 복제합니다.

   ![리포지토리 복제](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "리포지토리 복제")

Visual Studio를 사용하여 프로젝트를 연결하는 방법에 대한 자세한 내용은 [팀 탐색기에서 프로젝트에 연결](/visualstudio/ide/connect-team-project?view=vs-2019&preserve-view=true)을 참조하세요. Visual Studio를 사용하여 리포지토리를 복제하는 방법에 대한 지침은 [기존 Git 리포지토리 복제](/azure/devops/repos/git/clone?tabs=visual-studio) 문서를 검토하세요. 

## <a name="create-and-connect-your-project"></a>프로젝트 생성 및 연결

1. Visual Studio에서 **로컬 복제 리포지토리** 의 디렉터리와 로컬 Git 리포지토리를 모두 사용하여 새 SQL Server 데이터베이스 프로젝트를 만듭니다.

   ![새 프로젝트 만들기](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "새 프로젝트 만들기")  

2. 빈 sqlproject를 마우스 오른쪽 단추로 클릭하고 데이터 웨어하우스를 데이터베이스 프로젝트로 가져옵니다.

   ![프로젝트 가져오기](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "프로젝트 가져오기")  

3. Visual Studio의 팀 탐색기에서 로컬 Git 리포지토리에 대한 변경 내용을 커밋합니다.

   ![커밋](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Commit")  

4. 변경 내용이 복제된 리포지토리에 로컬로 커밋되었으므로 Azure DevOps 프로젝트에서 변경 내용을 Azure Repo 리포지토리에 동기화하고 푸시합니다.

   ![동기화 및 푸시 - 스테이징](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "동기화 및 푸시 - 스테이징")

   ![동기화 및 푸시](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "동기화 및 푸시")  

## <a name="validation"></a>유효성 검사

1. Visual Studio SSDT(SQL Server Data Tools)에서 데이터베이스 프로젝트의 테이블 열을 업데이트하여 변경 내용이 Azure Repo에 푸시되었는지 확인합니다.

   ![업데이트 열 유효성 검사](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "업데이트 열 유효성 검사")

2. 변경 내용을 로컬 리포지토리에서 Azure Repo로 커밋하고 푸시합니다.

   ![변경 내용 푸시](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "변경 내용 푸시")

3. 변경 내용이 Azure Repo 리포지토리에 푸시되었는지 확인합니다.

   ![Verify](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "변경 내용 확인")

4. (**선택 사항**) 스키마 비교를 사용하고 SSDT를 사용하여 대상 전용 SQL 풀에 대한 변경 내용을 업데이트하여 Azure Repo 리포지토리 및 로컬 리포지토리의 개체 정의가 전용 SQL 풀에 반영되도록 합니다.

## <a name="next-steps"></a>다음 단계

- [전용 SQL 풀 개발](sql-data-warehouse-overview-develop.md)