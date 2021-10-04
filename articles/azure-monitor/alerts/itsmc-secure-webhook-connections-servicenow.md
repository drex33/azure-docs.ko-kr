---
title: Azure Monitor의 IT 서비스 관리 커넥터 - 보안 내보내기 - ServiceNow를 사용한 구성
description: 이 문서에서는 Azure Monitor의 보안 내보내기에서 ITSM 제품/서비스를 ServiceNow와 연결하는 방법을 보여 줍니다.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 20cfd57ea759138ff3e266632eb87b9f90669934
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426476"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Azure Monitor에 ServiceNow 연결

다음 섹션에서는 ServiceNow 제품과 Azure의 보안 내보내기를 연결하는 방법을 자세히 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

다음 사전 요구 사항을 충족했는지 확인합니다.

* Azure AD가 등록됩니다.
* The ServiceNow Event Management - ITOM(뉴욕 이상 버전)의 지원되는 버전이 있습니다.
* ServiceNow 인스턴스에 설치된 [애플리케이션](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.3.1?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3DEvent%2520Management%2520Connectors&sl=sh)입니다.

## <a name="configure-the-servicenow-connection"></a>ServiceNow 연결 구성

1. https://(인스턴스 이름).service-now.com/api/sn_em_connector/em/inbound_event?source=azuremonitor 링크를 사용하여 보안 내보내기 정의에 대한 URI를 모니터링합니다.

2. 버전에 따라 다음 지침을 따르세요.
   * [Quebec](https://docs.servicenow.com/bundle/quebec-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [파리](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [올랜도](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/concept/azure-integration.html)
