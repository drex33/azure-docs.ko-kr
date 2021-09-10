---
title: 리소스 및 할당량 관리
titleSuffix: Azure Purview
description: Azure Purview의 리소스 할당량 및 제한과 할당량 증가를 요청하는 방법에 대해 자세히 알아봅니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 422c096533e8b470446a29b6d45f9127d963f913
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122568027"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>Azure Purview를 사용하여 리소스 할당량 관리 및 증가
 
Azure Purview는 데이터 사용자가 사용할 수 있는 클라우드 서비스입니다. Azure Purview를 사용하면 클라우드와 온-프레미스 환경 전반에 걸쳐 있는 데이터 자산의 데이터 거버넌스를 중앙에서 관리할 수 있습니다. 비즈니스 분석가는 이 서비스를 통해 의미 있는 비즈니스 용어를 사용하여 관련 데이터를 검색할 수 있습니다. 구독에 대한 최대 제한 값을 높이려면 고객 지원에 문의하세요.
 
## <a name="azure-purview-limits"></a>Azure Purview 제한
 
|**리소스**|  **기본 제한**  |**최대 제한**|
|---|---|---|
|지역당, 테넌트당 Purview 계정(모든 구독 결합)|3|지원에 문의|
|계정당 검사에 사용할 수 있는 vCore*|160|160|
|지정된 지점에 있는 계정당 동시 검색입니다. 이 제한은 검사된 데이터 원본의 유형에 따라 달라집니다.*|5 | 10 |
|검사를 실행할 수 있는 최대 시간|7 일|7 일|
|[데이터 맵 CU(용량 단위)](concept-elastic-data-map.md) |1CU(25개 작업/초 처리량 및 2GB 메타데이터 스토리지) | 100CU(더 높은 CU에 대한 지원 문의)|
|데이터 맵 작업 처리량 |각 용량 단위에 대해 25개 작업/초 | 100CU의 경우 2,500개 작업/초(추가 처리량에 대한 지원 문의)| 
|데이터 맵 스토리지 |각 용량 단위당 2GB | 100CU의 경우 200GB(추가 스토리지에 대한 지원 문의) |
|데이터 맵 탄력성 기간 | 1~8CU(데이터 맵은 탄력성 기간 내의 처리량에 따라 자동 스케일 업/다운할 수 있음) | 더 높은 탄력성 기간을 얻으려면 지원팀에 문의하세요. |
|계정당 자산 크기|100M 물리적 자산 |지원에 문의|
|카탈로그에 있는 자산의 최대 크기|2MB|2MB|
|자산 이름과 분류 이름의 최대 길이|4KB|4KB|
|자산 속성 이름 및 값의 최대 길이|32KB|32KB|
|분류 특성 이름 및 값의 최대 길이|32KB|32KB|
|계정당 최대 용어집 용어 수|100K|100K|
 
*자체 호스팅 통합 런타임 시나리오는 위의 표에 정의된 제한 범위를 벗어납니다. 
 
## <a name="next-steps"></a>다음 단계
 
> [!div class="nextstepaction"]
>[개념: Azure Purview의 탄력적 데이터 맵](concept-elastic-data-map.md)

> [!div class="nextstepaction"]
>[자습서: Azure Purview를 사용하여 데이터 검사](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[자습서: 홈페이지를 탐색하고 자산 검색](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[자습서: 자산을 찾아보고 데이터 계보 보기](tutorial-browse-and-view-lineage.md)
