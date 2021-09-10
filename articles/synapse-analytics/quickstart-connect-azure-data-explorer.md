---
title: '빠른 시작: Azure Synapse Analytics 작업 영역에 Azure Data Explorer 연결'
description: Azure Synapse Analytics용 Apache Spark를 사용하여 Azure Data Explorer를 Azure Synapse Analytics 작업 영역에 연결합니다.
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: 9f45bc134c656701ff02beca464ac00fdf222f88
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830206"
---
# <a name="connect-to-azure-data-explorer-using-apache-spark-for-azure-synapse-analytics"></a>Azure Synapse Analytics용 Apache Spark를 사용하여 Azure Data Explorer에 연결

이 문서에서는 Azure Synapse Analytics용 Apache Spark를 사용하여 Synapse Studio에서 Azure Data Explorer 데이터베이스에 액세스하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Azure Data Explorer 클러스터 및 데이터베이스 만들기](/azure/data-explorer/create-cluster-database-portal).
* 기존 Azure Synapse Analytics 작업 영역. 기존 작업 영역이 없으면 [빠른 시작: Azure Synapse 작업 영역 만들기](./quickstart-create-workspace.md)의 단계에 따라 새 작업 영역을 만듭니다.
* 기존 Apache Spark 풀. 기존 풀이 없으면 [빠른 시작: Azure Portal을 사용하여 Apache Spark 풀 만들기](./quickstart-create-apache-spark-pool-portal.md)의 단계에 따라 새 풀을 만듭니다.
* [Azure AD 애플리케이션을 프로비저닝하여 Azure AD(Azure Active Directory) 앱을 만듭니다.](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app)
* [Azure Data Explorer 데이터베이스 권한 관리](/azure/data-explorer/manage-database-permissions)의 단계에 따라 데이터베이스 액세스 권한을 Azure AD 앱에 부여합니다.

## <a name="go-to-synapse-studio"></a>Synapse Studio로 이동

Azure Synapse 작업 영역에서 **Synapse Studio 시작** 을 선택합니다. Synapse Studio 홈페이지에서 **데이터** 를 선택하여 **데이터 개체 탐색기** 로 이동합니다.

## <a name="connect-an-azure-data-explorer-database-to-an-azure-synapse-workspace"></a>Azure Synapse 작업 영역에 Azure Data Explorer 데이터베이스 연결

Azure Data Explorer 데이터베이스를 작업 영역에 연결하는 작업은 연결된 서비스를 통해 수행됩니다. Azure Data Explorer 연결된 서비스를 사용하면 Azure Synapse용 Apache Spark의 데이터를 탐색하고 읽고 쓸 수 있습니다. 파이프라인에서 통합 작업을 실행할 수도 있습니다.

데이터 개체 탐색기에서 다음 단계에 따라 Azure Data Explorer 클러스터를 직접 연결합니다.

1. **데이터** 옆에 있는 **+** 아이콘을 선택합니다.
1. **연결** 을 선택하여 외부 데이터에 연결합니다.
1. **Azure Data Explorer(Kusto)** 를 선택합니다.
1. **계속** 을 선택합니다.
1. 식별 이름을 사용하여 연결된 서비스의 이름을 지정합니다. 이름은 개체 탐색기에 표시되며, Azure Synapse 런타임에서 데이터베이스에 연결하는 데 사용됩니다.
1. 구독에서 Azure Data Explorer 클러스터를 선택하거나 URI를 입력합니다.
1. **서비스 주체 ID** 및 **서비스 주체 키** 를 입력합니다. 이 서비스 주체에게 읽기 작업을 위한 데이터베이스 보기 액세스 권한과 데이터 수집을 위한 수집기 액세스 권한이 있는지 확인합니다.
1. Azure Data Explorer 데이터베이스 이름을 입력합니다.
1. **연결 테스트** 를 클릭하여 올바른 사용 권한이 있는지 확인합니다.
1. **만들기** 를 선택합니다.

    ![새 연결된 서비스를 보여주는 스크린샷](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > (선택 사항) **연결 테스트** 는 쓰기 액세스의 유효성을 검사하지 않습니다. Azure Data Explorer 데이터베이스에 대한 쓰기 액세스 권한이 서비스 주체 ID에 있는지 확인하세요.

1. Azure Data Explorer 클러스터 및 데이터베이스는 **Azure Data Explorer** 섹션의 **연결됨** 탭에 표시됩니다.

    ![클러스터 탐색을 보여주는 스크린샷](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

1. Notebook에서 연결된 서비스와 상호 작용하려면 먼저 작업 영역에 게시해야 합니다. 도구 모음에서 **게시** 를 클릭하고 보류 중인 변경 내용을 검토한 다음 **확인** 을 클릭합니다.

    > [!NOTE]
    > 현재 릴리스에서 데이터베이스 개체는 Azure Data Explorer 데이터베이스에 대한 Azure AD 계정 권한에 따라 채워집니다. Apache Spark Notebook 또는 통합 작업을 실행하면 연결된 서비스의 자격 증명(예: 서비스 주체)이 사용됩니다.

## <a name="quickly-interact-with-code-generated-actions"></a>코드 생성 작업과 빠르게 상호 작용

데이터베이스 또는 테이블을 마우스 오른쪽 단추로 클릭하면 샘플 Spark Notebook 목록이 표시됩니다. 데이터를 읽는 옵션 또는 Azure Data Explorer에 데이터를 쓰거나 스트리밍하는 옵션을 선택합니다.

[![새 샘플 Notebook을 보여주는 스크린샷](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

다음은 데이터 읽기 예제입니다. Notebook을 Spark 풀에 연결하고, 셀을 실행합니다.

[![새로운 읽기 Notebook을 보여주는 스크린샷](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE]
   > 처음 실행하는 경우 Spark 세션을 시작하는 데 3분 넘게 걸릴 수 있습니다. 이후 실행은 훨씬 더 빠릅니다.

## <a name="limitations"></a>제한 사항

Azure Data Explorer 커넥터는 현재 Azure Synapse 관리형 가상 네트워크에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [고급 옵션을 사용하는 샘플 코드](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Azure Data Explorer(Kusto) Spark 커넥터](https://github.com/Azure/azure-kusto-spark)
