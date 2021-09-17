---
title: Synapse Studio를 통해 Azure Synapse 파트너의 타사 솔루션 검색
description: Azure Synapse 파트너에서 긴밀하게 통합된 새로운 타사 솔루션을 검색하는 방법을 알아봅니다.
services: synapse-analytics
manager: santoshb
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/14/2021
author: periclesrocha
ms.author: procha
ms.reviewer: omafnan, wiassaf
ms.custom: seo-lt-2019
ms.openlocfilehash: c765fcce0b95027ffa28915752bd3f370070a894
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123482216"
---
# <a name="discover-partner-solutions-through-synapse-studio"></a>Synapse Studio를 통해 파트너 솔루션 검색

Synapse Studio를 사용하면 Azure Synapse의 기능을 확장하는 솔루션 파트너를 검색할 수 있습니다. 데이터 커넥터에서 데이터 랭글링 도구, 오케스트레이션 엔진 및 기타 워크로드를 통해 **파트너 찾아보기** 페이지는 Azure Synapse Analytics와 함께 작동하도록 확인된 타사 ISV 애플리케이션 및 솔루션을 검색하는 허브 역할을 합니다. Synapse Studio는 대부분의 경우 파트너 플랫폼에 대한 초기 연결을 자동으로 설정하여 이러한 파트너의 시작을 간소화합니다.

## <a name="participating-partners"></a>참가 파트너
다음 표에서는 현재 지원되는 파트너 솔루션을 보여줍니다. 이 목록에 새 파트너가 추가되므로 자주 확인해야 합니다. 

| 파트너 | 솔루션 이름 |
| ------- | ------------- |
| ![Incorta](./media/data-integration/incorta-logo.png) | Incorta Intelligent Ingest for Azure Synapse |
| ![Informatica](./media/data-integration/informatica_logo.png) | Informatica Intelligent Data Management Cloud |
| ![Qlik Data Integration(이전의 Attunity)](./media/business-intelligence/qlik_logo.png) | Qlik Data Integration(이전의 Attunity) |

## <a name="requirements"></a>요구 사항
파트너 애플리케이션을 선택하면 Azure Synapse Studio에서 이 평가판에 사용할 수 있는 샌드박스 환경을 프로비저닝하므로 프로덕션 데이터와 함께 사용하기 전에 파트너 솔루션을 신속하게 시험해 볼 수 있습니다. 다음 개체가 만들어집니다. 

|  개체  |    세부 정보    |
| -------- | ------------- |
| 이름이 **Partner_[PartnerName]_pool** 인 [전용 SQL 풀](../overview-what-is.md) | DW100c 성능 수준입니다. |
| 이름이 **Partner_[PartnerName]_login** 인 [SQL 로그인](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) | `master` 데이터베이스에서 만들어집니다. 이 SQL 로그인의 암호는 평가판을 만들 때 사용자가 지정합니다.|
| [데이터베이스 사용자](../../azure-sql/database/logins-create-manage.md) | 새 SQL 로그인에 매핑된 새 데이터베이스 사용자입니다. 이 사용자는 새로 만든 데이터베이스에 대한 db_owner 역할에 추가됩니다. |

모든 경우 **[PartnerName]** 은 평가판을 제공하는 타사 ISV의 이름입니다. 

### <a name="security"></a>보안 
필수 개체를 만든 후 Synapse Studio는 새 샌드박스 환경에 대한 정보를 파트너 애플리케이션에 전송하여 사용자 지정된 평가판 환경을 허용합니다. 다음과 같은 정보가 파트너에게 전송됩니다. 
- 이름
- 성
- 전자 메일 주소
-  연결을 설정하는 데 필요한 Synapse 환경에 대한 세부 정보:     
    - Synapse 작업 영역의 DNS 이름(서버 이름)
    - SQL 풀의 이름(데이터베이스)
    - SQL 로그인(사용자 이름에만 해당)
    
새로 만든 SQL 로그인의 암호를 포함하여 파트너 애플리케이션과 암호를 공유하지 않습니다. 파트너 애플리케이션에 암호를 입력해야 합니다.

### <a name="costs"></a>비용
파트너 평가판에 대해 만들어진 전용 SQL 풀에는 DWU 블록 수와 실행 시간을 기반으로 지속적인 비용이 발생합니다. 불필요한 요금이 청구되지 않도록 이 파트너 평가판에 대해 만들어진 SQL 풀을 사용하지 않을 때 일시 중지해야 합니다. 

## <a name="starting-a-new-partner-trial"></a>새 파트너 평가판 시작 

1) Synapse Studio 홈페이지의 **추가 검색** 에서 **파트너 찾아보기** 를 선택합니다.
2) 파트너 찾아보기 페이지에는 Azure Synapse와의 직접 연결을 허용하는 평가판을 현재 제공하는 모든 파트너가 표시됩니다. 파트너 솔루션을 선택합니다.
3) 파트너 세부 정보 페이지에는 이 애플리케이션에 대한 관련 정보와 해당 솔루션에 대한 자세한 내용을 확인할 수 있는 링크가 표시됩니다. 평가판을 시작할 준비가 되면 **파트너에 연결** 을 선택합니다.
4) **[PartnerName] 솔루션에 연결** 페이지에서 이 파트너 연결의 요구 사항을 확인합니다. 원하는 경우 SQL 풀 이름 및 SQL 로그인 매개 변수를 변경하고(또는 기본값을 그대로 적용) 새 SQL 로그인의 암호를 입력하고 **연결** 을 선택합니다.

파트너 평가판에 대해 필요한 개체가 만들어집니다. 그런 다음, 추가 정보(필요한 경우)를 입력하고 평가판을 시작할 수 있는 파트너 페이지로 이동됩니다. 

> [!NOTE]
> Microsoft에서는 파트너 평가판 환경을 제어하지 않습니다. 파트너는 각자의 조건에 따라 제품 평가판을 제공하고 환경, 평가판 가용성 및 기능이 파트너에 따라 달라질 수 있습니다. Microsoft에서는 Synapse Studio에서 제공하는 타사 애플리케이션에 대한 지원을 제공하지 않습니다. 

## <a name="next-steps"></a>다음 단계

다른 파트너에 대한 자세한 내용은 [데이터 통합 파트너, 데이터](data-integration.md) [관리 파트너,](data-management.md) [Machine Learning 및 AI 파트너를](machine-learning-ai.md)참조하세요.