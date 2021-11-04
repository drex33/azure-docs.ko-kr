---
title: Azure Sentinel 작업할 때 유용한 리소스
description: 이 문서에서는 Azure Sentinel로 작업할 때 유용한 리소스 목록을 제공합니다.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: b21d70747dd7d3c08025787efb8ab1d7e29b2fa9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467609"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Azure Sentinel 작업에 유용한 리소스

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 문서에는 Azure Sentinel 사용에 대한 자세한 정보를 얻는 데 유용한 리소스가 나열되어 있습니다.

## <a name="learn-more-about-creating-queries"></a>쿼리 만들기에 대한 자세한 정보를 알아봅니다

Azure Sentinel은 Azure Monitor Log Analytics의 KQL(Kusto 쿼리 언어)을 사용하여 쿼리를 빌드합니다. 자세한 내용은 다음을 참조하세요.

- [KQL 개념](/azure/data-explorer/kusto/concepts/)
- [KQL 쿼리](/azure/data-explorer/kusto/query/)
- [KQL 빠른 참조 가이드](/azure/data-explorer/kql-quick-reference).
- [KQL 쿼리 시작](../azure-monitor/logs/get-started-queries.md)

## <a name="learn-more-about-creating-automation"></a>자동화 만들기에 대한 자세한 정보를 알아봅니다

기본 제공 플레이북의 확장된 갤러리로 Azure Logic Apps를 사용하여 Azure Sentinel에서 자동화를 만듭니다. 

자세한 내용은 [Azure Logic Apps 커넥터](/connectors/)를 참조하세요.

## <a name="compare-playbooks-workbooks-and-notebooks"></a>플레이북, 통합 문서 및 Notebook 비교

다음 표에서는 Azure Sentinel 플레이북, 통합 문서 및 Notebook 간의 차이점을 설명합니다.

| 범주 |플레이북  |통합 문서  |Notebooks  |
|---------|---------|---------|---------|
|**가상 사용자**     |   <ul><li>SOC 엔지니어</li><li>모든 계층의 분석가</li></ul>      | <ul><li> SOC 엔지니어</li><li>모든 계층의 분석가</li></ul>       | <ul><li>위협 위협 요소 및 계층 2/계층-3 분석가</li><li>인시던트 조사자</li><li>데이터 과학자</li><li>보안 연구원</li></ul>       |
|**사용**     | 단순하고 반복 가능한 작업의 자동화:<ul><li>외부 데이터 수집 </li><li>TI, GeoIP 조회 등을 통해 데이터 보강 </li><li> 조사 </li><li>수정 </li></ul>       | <ul><li>시각화</li></ul>        |   <ul><li>Azure Sentinel 데이터 및 외부 데이터 쿼리 </li><li>TI, GeoIP 조회 및 WhoIs 조회 등을 통해 데이터 보강 </li><li> 조사 </li><li> 시각화 </li><li> 사냥 </li><li>기계 학습 및 빅 데이터 분석 </li></ul>      |
|**장점**     |<ul><li> 반복 가능한 단일 작업에 가장 적합합니다. </li><li>코딩 지식이 필요하지 않습니다.  </li></ul>      |<ul><li>Azure Sentinel 데이터의 상위 수준 보기에 가장 적합합니다. </li><li>코딩 지식이 필요하지 않습니다.</li></ul>       | <ul><li>반복 가능한 작업의 복잡한 체인에 가장 적합합니다. </li><li>임시, 더 많은 절차적 제어</li><li>대화형 기능을 사용하여 피벗하기 쉽습니다. </li><li>데이터 조작 및 시각화를 위한 다양한 Python 라이브러리 </li><li>기계 학습 및 사용자 지정 분석 </li><li>분석 증거를 문서화하고 공유하기 쉽습니다. </li></ul>       |
|**당면 과제**     | <ul><li>임시 및 복잡한 작업 체인에 적합하지 않음 </li><li>증거를 문서화하고 공유하는 데 적합하지 않음</li></ul>        |   <ul><li>외부 데이터와 통합할 수 없음 </li></ul>     |    <ul><li> 높은 학습 곡선 및 코딩 지식 필요 </li></ul>   |
|  **자세한 정보**   | [Azure Sentinel의 플레이북을 사용하여 위협 대응 자동화](automate-responses-with-playbooks.md)        | [수집된 데이터 시각화](get-visibility.md)        | [Jupyter Notebook을 사용하여 보안 위협 헌트](notebooks.md)        |
|     |         |         |         |

## <a name="comment-on-our-blogs-and-forums"></a>블로그 및 포럼에 댓글을 달아주세요

사용자 의견을 주시면 감사합니다.

Azure Sentinel의 TechCommunity 공간에서는 다음을 할 수 있습니다.

- [최근 블로그 게시물 보기 및 댓글 달기](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog)
- [Azure Sentinel에 대한 사용자 질문 게시](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel)

또한 [사용자 의견](https://feedback.azure.com/d365community/forum/37638d17-0625-ec11-b6e6-000d3a4f07b8) 프로그램을 통해 기능 향상에 대한 제안을 보낼 수 있습니다.

## <a name="join-the-azure-sentinel-github-community"></a>Azure Sentinel GitHub 커뮤니티 조인

[Azure Sentinel GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel)는 위협 검색 및 자동화를 위한 강력한 리소스입니다. 

Microsoft 보안 분석가들은 새로운 통합 문서, 플레이 북 및 헌팅 쿼리를 지속적으로 만들고 추가하며, 작업 환경에서 사용할 수 있게 커뮤니티에 게시하고 있습니다. 

프라이빗 커뮤니티 GitHub 리포지토리에서 샘플 콘텐츠를 다운로드하여 Azure Sentinel용 사용자 지정 통합 문서, 헌팅 쿼리, Notebook 및 플레이북을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인증을 받으세요.](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [고객 사용 사례 스토리 읽기](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)
