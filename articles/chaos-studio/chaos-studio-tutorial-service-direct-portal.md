---
title: Azure Chaos Studio에서 서비스 직접 오류를 사용하는 실험 만들기
description: 서비스 직접 오류를 사용하는 실험 만들기
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: how-to
ms.date: 11/01/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: fd945f5a96228bc30c7d1f801fcc16ca34717cde
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373864"
---
# <a name="create-a-chaos-experiment-that-uses-a-service-direct-fault-to-fail-over-an-azure-cosmos-db-instance"></a>서비스 직접 오류를 사용하여 Azure Cosmos DB 인스턴스를 장애 조치(failover)하는 카오스 실험 만들기

비정상 상황에서는 비정상 상황에서 이러한 오류를 발생시켜 애플리케이션이 오류에 대해 복원력이 있는지 확인할 수 있습니다. 이 가이드에서는 Chaos 실험 및 Azure Chaos Studio를 사용하여 다중 읽기 단일 쓰기 Azure Comos DB 장애 조치(failover)를 발생합니다. 이 실험을 실행하면 장애 조치(failover) 이벤트가 발생할 때 데이터 손실을 방어할 수 있습니다.

이러한 동일한 단계를 사용하여 서비스 직접 오류에 대한 실험을 설정하고 실행할 수 있습니다. **서비스 직접** 오류는 비정상 상태 에이전트를 설치해야 하는 에이전트 기반 오류와 달리 계측할 필요 없이 Azure 리소스에 대해 직접 실행됩니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Azure Cosmos DB 계정. Azure Cosmos DB 계정이 없는 경우 다음 단계에 [따라 하나를 만들](../cosmos-db/sql/create-cosmosdb-resources-portal.md)수 있습니다.
- Azure Cosmos DB 계정에 대한 하나 이상의 읽기 및 쓰기 지역 설정


## <a name="enable-chaos-studio-on-your-azure-cosmos-db-account"></a>Azure Cosmos DB 계정에서 Chaos Studio 사용

Chaos Studio는 해당 리소스가 Chaos Studio에 먼저 온보딩되지 않은 한 리소스에 오류를 삽입할 수 없습니다. 리소스에 [대한 대상 및 기능을](chaos-studio-targets-capabilities.md) 만들어 리소스를 Chaos Studio에 온보딩합니다. Azure Cosmos DB 계정에는 하나의 대상 유형(서비스 다이렉트)과 하나의 기능(장애 조치(failover))만 있지만, 다른 리소스에는 서비스 직접 오류와 에이전트 기반 오류에 대한 대상 유형 및 여러 기능 등 최대 두 가지 대상 유형이 있을 수 있습니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
2. 검색 표시줄에서 **Chaos Studio(미리 보기)를** 검색합니다.
3. **대상을** 클릭하고 Azure Cosmos DB 계정으로 이동합니다.
![Azure Portal 대상 보기](images/tutorial-service-direct-targets.png)
4. Azure Cosmos DB 계정 옆의 확인란을 **선택하고, 대상 사용을** 클릭한 다음, 드롭다운 메뉴에서 서비스 직접 대상 **사용을** 클릭합니다.
![Azure Portal 대상 사용](images/tutorial-service-direct-targets-enable.png)
5. 선택한 리소스가 사용하도록 설정되었음을 나타내는 알림이 표시됩니다.
![대상을 사용하도록 설정됨을 보여주는 알림](images/tutorial-service-direct-targets-enable-confirm.png)

이제 Azure Cosmos DB 계정을 Chaos Studio에 성공적으로 온보딩했습니다. **대상** 보기에서 이 리소스에 사용하도록 설정된 기능을 관리할 수도 있습니다. 리소스 옆에 있는 **작업 관리** 링크를 클릭하면 해당 리소스에 대해 사용하도록 설정된 기능이 표시됩니다.

## <a name="create-an-experiment"></a>실험 만들기
Azure Cosmos DB 계정이 온보딩된 경우 실험을 만들 수 있습니다. 비정상 상황에서는 대상 리소스에 대해 수행할 작업을 순차적으로 실행되는 단계와 병렬로 실행되는 분기로 구성하여 정의합니다.

1. Chaos Studio 탐색에서 **실험** 탭을 클릭합니다. 이 보기에서는 모든 비정실적 실험을 보고 관리할 수 있습니다. Azure Portal **실험** 
 ![ 추가 보기를 클릭합니다.](images/tutorial-service-direct-add.png)
2. 비정상 상황에서 실험을 배포하려는 **구독,** **리소스 그룹** 및 **위치를** 입력합니다. 실험에 **이름** 을 지정합니다. **다음: 실험 디자이너 >** 기본 실험 세부 
 ![ 정보 추가를 클릭합니다.](images/tutorial-service-direct-add-basics.png)
3. 이제 Chaos Studio 실험 디자이너에 있습니다. 실험 디자이너를 사용하면 단계, 분기 및 오류를 추가하여 실험을 빌드할 수 있습니다. **단계** 및 **분기에** 친숙한 이름을 지정한 다음 **오류 추가를** 클릭합니다.
![실험 디자이너](images/tutorial-service-direct-add-designer.png)
4. 드롭다운에서 **CosmosDB 장애 조치(failover)를** 선택한 다음, 실패를 지속할 시간(분)으로 **기간을** 입력하고 Azure Cosmos DB 계정의 읽기 지역으로 **readRegion을** 입력합니다. **다음: 대상 리소스 >** 
 ![ 오류 속성을 클릭합니다.](images/tutorial-service-direct-add-fault.png)
5. Azure Cosmos DB 계정을 선택하고 **다음** 
 ![ 대상 추가를 클릭합니다.](images/tutorial-service-direct-add-target.png)
6. 실험이 올바른지 확인한 다음 **검토 + 만들기, 만들기를** 차례로 **클릭합니다.** 
 ![ 실험 검토 및 만들기](images/tutorial-service-direct-add-review.png)

## <a name="give-experiment-permission-to-your-azure-cosmos-db-account"></a>Azure Cosmos DB 계정에 실험 권한 부여
비정상 상황에서 실험을 만들면 Chaos Studio는 대상 리소스에 대해 오류를 실행하는 시스템 할당 관리 ID를 만듭니다. 실험을 성공적으로 실행하려면 이 ID에 대상 리소스에 [대한 적절한 권한이](chaos-studio-fault-providers.md) 부여되어야 합니다.

1. Azure Cosmos DB 계정으로 이동하고 액세스 **제어(IAM)** 를 클릭합니다.
![Azure Cosmos DB 개요 페이지](images/tutorial-service-direct-access-resource.png)
2. **추가를** 클릭한 다음 **역할 할당 추가를** 클릭합니다.
![액세스 제어 개요](images/tutorial-service-direct-access-iam.png)
3. Cosmos **DB 운영자를** 검색하고 역할을 선택합니다.  
 ![ 다음 Azure Cosmos DB 운영자 역할 할당을 클릭합니다.](images/tutorial-service-direct-access-role.png)
4. **멤버 선택을** 클릭하고 실험 이름을 검색합니다. 실험을 선택하고 **선택을** 클릭합니다. 동일한 테넌트에서 동일한 이름을 가진 여러 실험이 있는 경우 임의의 문자가 추가되어 실험 이름이 잘립니다.
![역할에 실험 추가](images/tutorial-service-direct-access-experiment.png)
5. **검토 + 할당을** 클릭한 다음 **검토 + 할당을** 클릭합니다.

## <a name="run-your-experiment"></a>실험 실행
이제 실험을 실행할 준비가 되었습니다. 영향을 확인하려면 Azure Cosmos DB 계정 개요를 열고 별도의 브라우저 탭에서 **전역적으로 데이터 복제로** 이동합니다. 실험 중에 주기적으로 새로 고치면 지역 교환이 표시됩니다.

1. **실험** 보기에서 실험을 클릭하고 **시작을** 클릭한 다음 **확인을** 클릭합니다.
2. **상태가** **실행 중으로** 변경되면 **기록** 아래의 최신 실행에 대한 **세부 정보를** 클릭하여 실행 중인 실험에 대한 세부 정보를 확인합니다.

## <a name="next-steps"></a>다음 단계
이제 Azure Cosmos DB 서비스 직접 실험을 실행했으므로 다음을 수행할 준비가 되었습니다.
- [에이전트 기반 오류를 사용하는 실험 만들기](chaos-studio-tutorial-agent-based-portal.md)
- [실험 관리](chaos-studio-run-experiment.md)
