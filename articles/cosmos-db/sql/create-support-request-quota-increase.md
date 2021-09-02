---
title: Azure Cosmos DB 리소스의 할당량 증가를 요청하는 방법
description: Azure Cosmos DB 리소스의 할당량 증가를 요청하는 방법을 알아봅니다. 구독이 지역에 액세스할 수 있도록 하는 방법도 알아봅니다.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 8911bca3f93ec71559f79254dd5794c2387857b3
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117053"
---
# <a name="how-to-request-quota-increase-for-azure-cosmos-db-resources"></a>Azure Cosmos DB 리소스의 할당량 증가를 요청하는 방법
[!INCLUDE[appliesto-all-apis](../includes/appliesto-all-apis.md)]

Azure Cosmos DB의 리소스에는 [기본 할당량/한도](../concepts-limits.md)가 있습니다. 그러나 워크로드에 기본값보다 많은 할당량이 필요한 경우도 있을 수 있습니다. 이 경우 Azure Cosmos DB 팀에 연락하여 할당량 증가를 요청해야 합니다. 이 문서에서는 Azure Cosmos DB 리소스의 할당량 증가를 요청하는 방법을 설명합니다. 구독이 지역에 액세스할 수 있도록 하는 방법도 알아봅니다.

## <a name="create-a-new-support-request"></a>새 지원 요청 만들기

할당량 증가를 요청하려면 워크로드 세부 정보를 사용하여 새 지원 요청을 만들어야 합니다. 그러면 Azure Cosmos DB 팀이 요청을 평가하고 승인합니다. Azure Portal에서 새 지원 요청을 만들려면 다음 단계를 수행합니다.

1. Azure Portal에 로그인합니다.

1. 왼쪽 메뉴에서 **도움말 + 지원** 을 선택한 다음, **새 지원 요청** 을 선택합니다.

1. **기본 사항** 탭에서 다음 세부 정보를 입력합니다.

   * **문제점 유형** 에 대해 **서비스 및 구독 제한(할당량)** 을 선택합니다.
   * **구독** 에서 할당량을 늘리려는 구독을 선택합니다.
   * **할당량 유형** 에서 **Cosmos DB** 를 선택합니다.

   :::image type="content" source="./media/create-support-request-quota-increase/create-quota-increase-request.png" alt-text="할당량 증가를 위한 새 Cosmos DB 지원 요청 만들기":::

1. **세부 정보** 탭에서 할당량 요청에 해당하는 세부 정보를 입력합니다. 이 탭에서 제공한 정보는 문제를 자세히 평가하고 지원 엔지니어가 문제를 해결할 수 있도록 돕는 데 사용됩니다.

1. 다음 형식으로 세부 정보를 입력합니다.

   * **설명:** 워크로드, 기본값이 충분하지 않은 이유 등 요청에 대한 간단한 설명을 제공합니다. 할당량을 늘리려는 리소스 종류에 따라 **설명** 필드 내에서 다음 세부 정보를 제공해야 합니다.

     **지역 요청** 요청이 허용 목록에 지역 추가에 해당하는 경우 다음 값을 제공해야 합니다.

        * 지역 이름
        * 구독 ID

     **처리량 한도 요청** 요청이 처리량 할당량 증가에 해당하는 경우 다음 값을 제공해야 합니다.

        * 데이터베이스 이름
        * 구독 ID
        * 새 처리량 한도

     **데이터베이스 계정 한도 요청** 요청이 구독의 데이터베이스 계정 수 할당량 증가에 해당하는 경우 다음 값을 제공해야 합니다.

       * 구독 ID
       * 새 데이터베이스 계정 한도

   * **파일 업로드**: 진단 파일 또는 지원 요청과 관련이 있다고 생각되는 기타 파일을 업로드합니다. 파일 업로드 지침에 대한 자세한 내용은 [Azure 지원](../../azure-portal/supportability/how-to-manage-azure-support-request.md#upload-files) 문서를 참조하세요.

   * **심각도**: 비즈니스 영향에 따라 사용 가능한 심각도 수준 중 하나를 선택합니다.

   * **기본 연락 방법**: **메일** 또는 **전화** 로 연락하도록 선택할 수 있습니다.

1. 가용성, 지원 언어, 연락처 정보, 메일, 전화 번호와 같은 나머지 세부 정보를 양식에 입력합니다.

1. **다음: 검토+만들기** 를 선택합니다. 제공한 정보의 유효성을 검사하고 **만들기** 를 선택하여 지원 요청을 만듭니다.

Azure Cosmos DB 지원 팀이 24시간 이내에 요청을 평가하고 연락을 드립니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB 기본 서비스 할당량](../concepts-limits.md)을 참조하세요.
