---
title: Azure Stream Analytics에 대 한 Azure 데이터 탐색기 출력 (미리 보기)
description: 이 문서에서는 Azure Stream Analytics에 대 한 출력으로 Azure 데이터베이스 탐색기를 사용 하는 방법을 설명 합니다.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/19/2021
ms.openlocfilehash: 20235c3d30185427cde69e64ea77b59c4a89a096
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132871788"
---
# <a name="azure-data-explorer-output-for-azure-stream-analytics-preview"></a>Azure Stream Analytics에 대 한 Azure 데이터 탐색기 출력 (미리 보기)

[Azure 데이터 탐색기](https://azure.microsoft.com/services/data-explorer/) 를 웹 사이트, 응용 프로그램, IoT 장치 등의 모든 데이터 원본에서 다양 한 데이터를 분석 하기 위한 출력으로 사용할 수 있습니다. Azure 데이터 탐색기는 로그 및 원격 분석 데이터에 사용 가능한 빠르고 확장성이 우수한 데이터 탐색 서비스입니다. 최신 소프트웨어에서 생성되는 많은 데이터 스트림을 처리할 수 있으므로 데이터를 수집, 저장 및 분석할 수 있습니다. 이 데이터는 진단, 모니터링, 보고, 기계 학습 및 추가 분석 기능에 사용됩니다.

Azure 데이터 탐색기는 Event Hub 같은 일반 서비스에 대한 커넥터, SDK(예: .NET 및 Python)를 사용한 프로그래밍 방식 수집 및 탐색을 위한 엔진 직접 액세스를 비롯하여 여러 가지 수집 방법을 지원합니다. Azure 데이터 탐색기는 데이터의 추가 분석 및 시각화를 위해 분석 및 모델링 서비스와 통합됩니다.

Azure 데이터 탐색기에 대 한 자세한 내용은 [azure 데이터 탐색기 설명서](https://docs.microsoft.com/azure/data-explorer/data-explorer-overview/) 를 참조 하세요.

Azure Portal를 사용 하 여 Azure 데이터 탐색기 및 클러스터를 만드는 방법에 대 한 자세한 내용은 빠른 시작 [: azure 데이터 탐색기 클러스터 및 데이터베이스 만들기](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal/) 를 참조 하세요.


> [!NOTE] 
> 다중 테 넌 트 클러스터에서는 현재 테스트 연결이 지원 되지 않습니다.

## <a name="output-configuration"></a>출력 구성

다음 표에서는 Azure 데이터 탐색기 출력을 만들기 위한 속성 이름 및 해당 설명을 나열 합니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 |쿼리 출력을 이 데이터베이스로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
| 구독 | 클러스터에 사용할 Azure 구독을 선택합니다. |
| 클러스터 | 클러스터를 식별하는 고유한 이름을 선택합니다. 입력한 클러스터 이름에 도메인 이름 [region].kusto.windows.net이 추가됩니다. 이름에는 소문자와 숫자만 포함할 수 있습니다. 4-22자를 포함해야 합니다. |
| 데이터베이스 | 출력을 보내는 데이터베이스의 이름입니다. 데이터베이스 이름은 클러스터 내에서 고유해야 합니다. |
| 인증 | [Azure Active Directory의 관리 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview/)를 사용하면 클러스터에서 다른 Azure AD 보호 리소스(예: Azure Key Vault)에 쉽게 액세스할 수 있습니다. ID는 Azure 플랫폼에서 관리되며, 비밀을 프로비저닝하거나 회전하지 않아도 됩니다. 관리 id 구성은 현재 [클러스터에 대해 고객이 관리](https://docs.microsoft.com/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault/)하는 키를 사용 하도록 설정 하는 경우에만 지원 됩니다. |
| 테이블 | 출력을 기록되는 테이블 이름입니다. 테이블 이름은 대/소문자를 구분합니다. 이 테이블의 스키마는 작업 출력에서 생성하는 필드 수 및 해당 형식과 정확히 일치해야 합니다. |


## <a name="partitioning"></a>분할

파티션은 사용하도록 설정해야 하며 쿼리의 PARTITION BY 절을 기반으로 합니다. 분할 상속 옵션을 사용하도록 설정하는 경우 [완전히 병렬 처리 가능한 쿼리](stream-analytics-scale-jobs.md)에 대한 입력 분할을 따릅니다. 

## <a name="limitation"></a>제한 사항

성공적으로 수집 하려면 다음을 확인 해야 합니다.

* Azure Stream Analytics 작업 쿼리의 열 수는 Azure 데이터 탐색기 테이블과 일치 해야 하며 동일한 순서 여야 합니다.
* & 데이터 형식의 열 이름은 Azure Stream Analytics SQL 쿼리와 Azure 데이터 탐색기 테이블 사이에 일치 해야 합니다.
* Azure Data Explorer에는 데이터 수집을 위한 집계(일괄 처리) 정책이 있으며, 이는 수집 프로세스를 최적화하도록 설계되었습니다. 이 정책은 기본적으로 5분, 1000개 항목 또는 1GB 데이터로 구성되어 있으므로 대기 시간이 발생할 수 있습니다. 집계 옵션에 대한 내용은 [일괄 처리 정책](https://docs.microsoft.com/azure/data-explorer/kusto/management/batchingpolicy)을 참조하세요.



## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
