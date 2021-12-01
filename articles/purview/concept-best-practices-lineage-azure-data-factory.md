---
title: Azure Purview 데이터 계보 모범 사례
description: 이 문서에서는 Azure Purview의 다양한 데이터 원본 계보에 대한 모범 사례를 제공합니다.
author: amberz
ms.author: amberz
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 10/25/2021
ms.openlocfilehash: 48fa2b7d5a770c2dccc83554712d963c2464d883
ms.sourcegitcommit: 9567c42d1e5270af16a1a8090f11a3b12131010d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133425470"
---
# <a name="azure-purview-data-lineage-best-practices"></a>Azure Purview 데이터 계보 모범 사례

데이터 계보는 데이터의 원본을 포괄하는 수명 주기로 광범위하게 이해되며 데이터 자산 전체에서 시간이 지남에 따라 이동하는 위치로 이해됩니다. Purview는 조직 데이터 자산의 여러 부분에 있는 데이터에 대한 계보를 다음을 포함한 여러 준비 수준에서 캡처할 수 있습니다. 
* 다양한 플랫폼에서 스테이징된 완전한 원시 데이터 
* 변환 및 준비된 데이터 
* 시각화 플랫폼에서 사용되는 데이터

## <a name="why-do-you-need-adopt-lineage"></a>계보를 채택해야 하는 이유는 무엇인가요?  

데이터 계보는 존재하는 데이터, 저장된 위치 및 시스템 간에 흐르는 방식을 설명하는 프로세스입니다. 데이터 계보가 중요한 이유는 여러 가지가 있지만, 개개인에서는 모두 여기서 살펴볼 세 가지 범주로 요약할 수 있습니다. 
* 보고서에서 데이터 추적 
* 영향 분석 
* 변경 내용 캡처 및 데이터 수명 주기 동안 데이터가 상주하는 위치 

## <a name="azure-data-factory-lineage-best-practice-and-considerations"></a>Azure Data Factory 계보 모범 사례 및 고려 사항 

### <a name="azure-data-factory-instance"></a>Azure Data Factory 인스턴스 

* Data Factory 연결 상태가 연결됨으로 전환될 때까지 데이터 계보가 카탈로그에 자동으로 보고되지 않습니다. 나머지 상태 Disconnected 및 CannotAccess는 계보를 캡처할 수 없습니다. 

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="데이터 팩터리 연결 목록을 보여주는 스크린샷" lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

* 각 Data Factory 인스턴스는 하나의 Purview 계정에만 연결할 수 있습니다. 다른 Purview 계정에서 새 연결을 설정할 수 있지만 이렇게 하면 기존 연결이 끊어집니다.  

    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Azure Data Factory 연결을 끊을 경고를 보여주는 스크린샷.":::

* 데이터 팩터리의 관리 ID는 Purview 계정에서 계보를 인증하는 데 사용되며 Purview 루트 컬렉션에 대한 데이터 팩터리의 관리 ID 데이터 자료 역할이 필요합니다. 
* 한 번에 10개 이하의 데이터 팩터리만 지원합니다. 10개 이상의 데이터 팩터리를 한 번에 추가하려면 지원 티켓을 제출하세요. 

### <a name="azure-data-factory-activities"></a>Azure Data Factory 활동  

* Azure Purview는 다음과 같은 Azure Data Factory 작업에서 런타임 계보를 캡처합니다. 
    * [복사 작업 ](../data-factory/copy-activity-overview.md)
    * [데이터 흐름 작업](../data-factory/concepts-data-flow-overview.md)
    * [SSIS 패키지 실행 작업](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

* 원본 또는 대상이 지원되지 않는 데이터 스토리지 시스템을 사용하는 경우 Azure Purview가 계보를 삭제합니다.  
    * 복사 작업에서 지원되는 데이터 원본은 **커넥트 지원 복사 작업** [나열됩니다Azure Data Factory](how-to-link-azure-data-factory.md)
    * 데이터 흐름 작업에서 지원되는 데이터 원본은 **커넥트 지원되는 데이터** [Flow 나열되어 Azure Data Factory](how-to-link-azure-data-factory.md)
    * SSIS에서 지원되는 데이터 원본은 **SSIS에서** 계보의 패키지 실행 작업 지원을 [나열합니다SQL Server Integration Services](how-to-lineage-sql-server-integration-services.md)

* Azure Data Factory 복사 작업에서 커넥트 복사 작업 **계보에 대한 제한에** 나열된 복사 작업 기능을 사용하여 [계보를 캡처할 수 Azure Data Factory](how-to-link-azure-data-factory.md)  

* 데이터 흐름 작업의 계보의 경우 Purview는 원본 및 싱크만 지원합니다. 데이터 흐름 변환의 계보는 아직 지원되지 않습니다. 

* 데이터 흐름 계보는 Purview 리소스 집합과 통합되지 않습니다. 

    **리소스 집합 예제 1**    

    정규화된 이름:https://myblob.blob.core.windows.net/sample-data/data{N}.csv 

    표시 이름: "data" 

* SSIS 패키지 실행 작업의 계보의 경우 원본 및 대상만 지원됩니다. 변환의 계보는 아직 지원되지 않습니다. 

    :::image type="content" source="./media/concept-best-practices-lineage/ssis-lineage.png" alt-text="Purview에서 SSIS 계보 실행의 스크린샷" lightbox="./media/concept-best-practices-lineage/ssis-lineage.png":::

* [Purview에서 Azure Data Factory 계보를 푸시하려면](../data-factory/tutorial-push-lineage-to-purview.md)다음 단계별 가이드를 참조하세요.  

## <a name="next-steps"></a>다음 단계
-  [데이터 원본 관리](./manage-data-sources.md)
