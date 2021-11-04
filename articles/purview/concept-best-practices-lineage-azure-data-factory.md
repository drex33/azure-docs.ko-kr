---
title: Azure 부서의 범위 데이터 계보 모범 사례
description: 이 문서에서는 Azure 부서의 범위의 다양 한 데이터 원본에 대 한 모범 사례를 제공 합니다.
author: amberz
ms.author: amberz
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 10/25/2021
ms.openlocfilehash: 0036004e8222ce79fc43e5d6603abecc2e375b1a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482920"
---
# <a name="azure-purview-data-lineage-best-practices"></a>Azure 부서의 범위 데이터 계보 모범 사례

데이터 계보는 데이터의 원본에 걸쳐 있는 수명 주기와 널리 이해 되며 데이터 공간에서 시간이 지남에 따라 이동 합니다. Purview는 조직 데이터 자산의 여러 부분에 있는 데이터에 대한 계보를 다음을 포함한 여러 준비 수준에서 캡처할 수 있습니다. 
* 다양한 플랫폼에서 스테이징된 완전한 원시 데이터 
* 변환 및 준비된 데이터 
* 시각화 플랫폼에서 사용 하는 데이터

 
## <a name="intended-audience"></a>대상 그룹

* 데이터 엔지니어 
* 데이터 과학자 
* 데이터 소유자 

## <a name="why-do-you-need-adopt-lineage"></a>계보를 채택 해야 하는 이유는 무엇 인가요?  

데이터 계보는 존재 하는 데이터, 데이터가 저장 되는 위치 및 시스템 간에 흐르는 방식을 설명 하는 프로세스입니다. 데이터 계보가 중요 한 이유는 여러 가지가 있지만, 높은 수준에서는 여기에서 살펴볼 수 있는 세 가지 범주로 boiled 수 있습니다. 
* 보고서의 데이터 추적 
* 영향 분석 
* 데이터 수명 주기 동안 변경 내용 및 데이터가 있었던 위치 캡처 

## <a name="azure-data-factory-lineage-best-practice-and-considerations"></a>Azure Data Factory 계보 모범 사례 및 고려 사항 

### <a name="azure-data-factory-instance"></a>Azure Data Factory 인스턴스 

* Data Factory 연결 상태가 연결 됨으로 바뀔 때까지 데이터 계보가 자동으로 카탈로그에 보고 되지 않습니다. 상태를 끊은 나머지 상태와 CannotAccess는 계보를 캡처할 수 없습니다. 

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="데이터 팩터리 연결 목록을 보여주는 스크린샷" lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

* 각 Data Factory 인스턴스는 하나의 부서의 범위 계정에만 연결할 수 있습니다. 다른 부서의 범위 계정에 새 연결을 설정할 수 있지만 이렇게 하면 기존 연결이 끊어집니다.  

    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Azure Data Factory 연결을 끊을 경고를 보여주는 스크린샷.":::

* 데이터 팩터리의 관리 id는 부서의 범위 계정에서 계보를 인증 하는 데 사용 되며, 부서의 범위 root collection에서 데이터 팩터리의 관리 되는 id 데이터 큐레이터 역할이 필요 합니다. 
* 한 번에 10 개 이하의 데이터 팩터리를 지원 합니다. 10개 이상의 데이터 팩터리를 한 번에 추가하려면 지원 티켓을 제출하세요. 

### <a name="azure-data-factory-activities"></a>작업 Azure Data Factory  

* Azure Purview는 다음과 같은 Azure Data Factory 작업에서 런타임 계보를 캡처합니다. 
    * [복사 작업 ](../data-factory/copy-activity-overview.md)
    * [데이터 흐름 작업](../data-factory/concepts-data-flow-overview.md)
    * [SSIS 패키지 실행 작업](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

* 원본 또는 대상이 지원되지 않는 데이터 스토리지 시스템을 사용하는 경우 Azure Purview가 계보를 삭제합니다.  
    * 복사 작업에서 지원 되는 데이터 원본은 커넥트의 **복사 작업 지원** 목록에 나열 됩니다 [Azure Data Factory](how-to-link-azure-data-factory.md)
    * 데이터 흐름 작업에서 지원 되는 데이터 원본 [에는 커넥트에](how-to-link-azure-data-factory.md) 대 한 **데이터 Flow 지원이** 나열 됩니다 Azure Data Factory
    * ssis에서 지원 되는 데이터 원본은 [SQL Server에서 계보](how-to-lineage-sql-server-integration-services.md) 의 **ssis 실행 패키지 작업 지원** 에 나열 되어 있습니다 Integration Services

* Azure Data Factory 복사 작업에서 커넥트의 **복사 작업 계보에 대 한 제한 사항** 에 나열 된 복사 작업 기능 [을 사용 하](how-to-link-azure-data-factory.md) 는 경우 부서의 범위에서 계보를 캡처할 수 없습니다 Azure Data Factory  

* 데이터 흐름 작업의 계보의 경우 부서의 범위는 소스 및 싱크로만 지원 합니다. 데이터 흐름 변환의 계보는 아직 지원되지 않습니다. 

* 데이터 흐름 계보가 부서의 범위 리소스 집합과 통합 되지 않습니다. 

    **리소스 집합 예 1**    

    정규화 된 이름:https://myblob.blob.core.windows.net/sample-data/data{N}.csv 

    표시 이름: "data" 

* SSIS 패키지 실행 작업의 계보의 경우 원본 및 대상만 지원됩니다. 변환의 계보는 아직 지원되지 않습니다. 

    :::image type="content" source="./media/concept-best-practices-lineage/ssis-lineage.png" alt-text="Purview에서 SSIS 계보 실행의 스크린샷" lightbox="./media/concept-best-practices-lineage/ssis-lineage.png":::

* [부서의 범위에 Azure Data Factory 계보를 푸시하](../data-factory/tutorial-push-lineage-to-purview.md)는 다음 단계별 가이드를 참조 하세요.  

## <a name="next-steps"></a>다음 단계
-  [데이터 원본 관리](./manage-data-sources.md)
