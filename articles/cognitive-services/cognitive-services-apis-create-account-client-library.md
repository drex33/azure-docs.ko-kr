---
title: Azure Management 클라이언트 라이브러리를 사용하여 Cognitive Services 리소스 만들기
titleSuffix: Azure Cognitive Services
description: Azure Management 클라이언트 라이브러리를 사용하여 Azure Cognitive Services 리소스를 만들고 관리합니다.
services: cognitive-services
keywords: 인지 서비스, 인지 인텔리전스, 인지 솔루션, AI 서비스
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 06/04/2021
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.custom: mode-other
ms.openlocfilehash: df88752aa5b61837c660a95dea01137d744229a3
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133046534"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>빠른 시작: Azure Management 클라이언트 라이브러리를 사용하여 Cognitive Services 리소스 만들기

이 빠른 시작을 사용하여 Azure Management 클라이언트 라이브러리를 통해 Azure Cognitive Services 리소스를 만들고 관리합니다.

Azure Cognitive Services는 개발자가 애플리케이션에 인식 인텔리전스를 빌드하는 데 도움이 되는 REST API 및 클라이언트 라이브러리가 포함된 클라우드 기반 서비스 제품군입니다. 개발자는 직접 AI(인공 지능) 또는 데이터 과학 기술이나 지식 없어도 성공할 수 있습니다. Azure Cognitive Services를 사용하는 개발자는 보고, 듣고, 말하고, 이해하고, 추론할 수도 있는 인지적 솔루션을 통해 애플리케이션에 인지적 기능을 손쉽게 추가할 수 있습니다.

개별 AI 서비스는 Azure 구독에서 만든 Azure [리소스](../azure-resource-manager/management/manage-resources-portal.md)로 표시됩니다. 리소스를 만든 후 생성된 키와 엔드포인트를 사용하여 애플리케이션을 인증할 수 있습니다.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end
