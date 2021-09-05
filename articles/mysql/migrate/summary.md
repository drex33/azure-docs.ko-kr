---
title: MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션 가이드 요약
description: 이 문서에서는 온-프레미스 MySQL에서 Azure Database for MySQL로 애플리케이션을 마이그레이션하는 것과 관련된 몇 가지 항목을 설명했습니다.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: e343a16cc27ff607025673d1c48ef872c14fdb14
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110656937"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-summary"></a>MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션 가이드 요약

이 문서에서는 온-프레미스 MySQL에서 Azure Database for MySQL로 애플리케이션을 마이그레이션하는 것과 관련된 몇 가지 항목을 설명했습니다. 애플리케이션을 중단하는 모든 방법으로 프로젝트를 시작하고 평가하는 방법을 다루었습니다.

선택한 항목이 프로젝트 타임라인에 영향을 줄 수 있기 때문에 마이그레이션 팀은 항목을 신중하게 검토해야 합니다. 제공되는 많은 엔터프라이즈 준비 기능을 고려할 때 총 소유 비용은 매우 중요합니다.

마이그레이션 프로젝트 접근 방식이 중요합니다. 팀은 애플리케이션 및 데이터베이스 복잡성을 평가하여 변환 시간을 결정해야 합니다. 변환 도구는 전환을 더 쉽게 만드는 데 도움이 되지만 항상 수동 검토 및 업데이트가 필요한 요소가 있었습니다. 마이그레이션 전 작업 및 마이그레이션 후 테스트를 스크립팅하는 것이 중요합니다.

애플리케이션 아키텍처 및 디자인은 필요한 활동 수준에 대한 강력한 지표를 제공할 수 있습니다. 예를 들어 ORM 프레임워크를 활용하는 애플리케이션은 특히 비즈니스 논리가 데이터베이스 개체 대신 애플리케이션에 포함된 경우 좋은 후보가 될 수 있습니다.

결국 마켓플레이스에는 무료에서 상용까지 다양한 도구가 있습니다. 이 문서에서는 팀이 가장 널리 사용되는 오픈 소스 도구 옵션 중 하나를 사용하여 데이터베이스 마이그레이션을 계획하는 경우 필요한 단계를 다룹니다. 어떤 경로를 선택하든 Microsoft와 MySQL 커뮤니티에는 데이터베이스 마이그레이션에 성공하기 위한 도구와 전문 지식이 있습니다.

### <a name="questions-and-feedback"></a>질문 및 피드백

Azure Database for MySQL 작업에 대해 질문이나 제안하고 싶은 의견이 있으면 Azure Database for MySQL 팀(AskAzureDBforMySQL@service.microsoft.com)으로 이메일을 보내주세요. 

> [!Important]
> Azure Database for MySQL 팀 주소는 지원 티켓이 아닌 일반적인 질문을 위한 것입니다.

또한 문의 시 다음 사항을 적절히 고려합니다.

  - Azure 지원에 문의하거나 계정 관련 문제를 해결하려면 [Azure Portal에서 티켓을 제출](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)하세요.

  - 피드백을 제공하거나 새 기능을 요청하려면 [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql)를 통해 항목을 만드세요.

### <a name="find-a-partner-to-help-migrating"></a>마이그레이션을 지원할 파트너 찾기

이 가이드는 너무 어려울 수 있지만 걱정할 필요는 없습니다.\! 커뮤니티에는 검증된 마이그레이션 트랙 레코드를 가진 많은 전문가가 있습니다. 가장 적합한 마이그레이션 전략을 찾는 데 도움이 되는 [Microsoft 파트너](https://www.microsoft.com/solution-providers/home) 또는 [Microsoft MVP](https://mvp.microsoft.com/MvpSearch)를 검색합니다. 단독으로는 안 됩니다.\!

기술 포럼 및 소셜 그룹에서 보다 자세한 실제 정보를 찾아볼 수도 있습니다.

  - [Microsoft Community 포럼](/answers/topics/azure-database-mysql.html)

  - [Azure Database for MySQL Tech Community ](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/bg-p/ADforMySQL)

  - [Azure MySQL용 StackOverflow](https://stackoverflow.com/questions/tagged/azure-database-mysql)

  - [Azure Facebook 그룹](https://www.facebook.com/groups/MsftAzure)

  - [LinkedIn Azure 그룹](https://www.linkedin.com/groups/2733961/)

  - [LinkedIn Azure 개발자 그룹](https://www.linkedin.com/groups/1731317/)

## <a name="next-steps"></a>다음 단계

엔드투엔드 MySQL 마이그레이션 가이드를 사용하여 샘플 애플리케이션을 배포하려면 [부록 A](appendix-a-environment-setup.md) 및 [부록 B](appendix-b-arm-templates.md)를 방문하세요.

사용 가능한 서버 매개 변수를 검토하려면 [부록 C](appendix-c-default-server-parameters-mysql-55-and-azure-database-for-mysql.md)를 방문하세요.