---
title: Azure API for FHIR 데이터베이스 설정 구성
description: 이 문서에서는 Azure API for FHIR 데이터베이스 설정을 구성하는 방법을 설명합니다.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/15/2019
ms.author: zxue
ms.openlocfilehash: 1874db9ba1cbefa4fb3bf749aeaccafc23171d37
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122435585"
---
# <a name="configure-database-settings"></a>데이터베이스 설정 구성 

Azure API for FHIR가 데이터베이스를 사용하여 해당 데이터를 저장합니다. 기본 데이터베이스의 성능은 서비스를 프로비저닝하는 동안 선택된 RU(요청 단위) 수 또는 서비스가 프로비저닝된 후의 데이터베이스 설정에 따라 달라집니다.

Azure API for FHIR 기본 데이터베이스의 성능을 설정하는 경우 Cosmos DB(Azure [Cosmos DB의 요청 단위](../../cosmos-db/request-units.md)참조)에서 RUS 개념을 활용합니다. 

데이터베이스에 대해 항상 충분한 시스템 리소스를 사용할 수 있도록 처리량을 프로비전해야 합니다. 애플리케이션에 필요한 CPU의 개수는 수행하는 작업에 따라 달라집니다. 작업은 간단한 읽기 및 쓰기부터 더 복잡한 쿼리까지 다양할 수 있습니다. 

> [!NOTE]
> 다른 작업에서 다른 수의 RU를 사용함에 따라 응답 헤더의 모든 API 호출에서 사용하는 실제 RU 수를 반환합니다. 이러한 방식으로 애플리케이션에서 사용하는 CPU 수를 프로파일할 수 있습니다.

## <a name="update-throughput"></a>처리량 업데이트

Azure Portal 이 설정을 변경하려면 Azure API for FHIR 이동하여 데이터베이스 블레이드를 엽니다. 다음으로, 프로비전된 처리량을 성능 요구 사항에 따라 원하는 값으로 변경합니다. 최대 10,000 RU/s까지 값을 변경할 수 있습니다. 더 높은 값이 필요한 경우 Azure 지원에 문의하세요.

데이터베이스 처리량이 10,000 RU/s보다 크거나 데이터베이스에 저장된 데이터가 50GB를 초과하는 경우 클라이언트 애플리케이션에서 연속 토큰을 처리할 수 있어야 합니다. 10,000 RU/s의 처리량이 증가할 때마다 또는 저장된 데이터 양이 50GB를 초과하는 경우 데이터베이스에 새 파티션이 만들어집니다. 여러 파티션은 연속 토큰을 사용하여 페이지 매김이 구현되는 다중 페이지 응답을 만듭니다.

> [!NOTE] 
> 값이 높을수록 Azure API for FHIR 처리량이 증가하고 서비스 비용이 높아질 수 있습니다.

![구성 Cosmos DB](media/database/database-settings.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure API for FHIR 위해 RUS를 업데이트하는 방법을 배웠습니다. 고객 관리형 키를 데이터베이스 설정으로 구성하는 방법을 알아보려면 다음을 수행합니다.

>[!div class="nextstepaction"]
>[고객 관리형 키 구성](customer-managed-key.md)

또는 완전 관리형 Azure API for FHIR 배포할 수 있습니다.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR 배포](fhir-paas-portal-quickstart.md)