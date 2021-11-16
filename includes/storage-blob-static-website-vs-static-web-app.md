---
title: 포함 파일
description: 포함 파일
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 11/04/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 74ee76424ccbbdcaaebb4652acbe42bf569ac86d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132096906"
---
고정적인 웹 사이트에는 몇 가지 제한 사항이 있습니다. 예를 들어 헤더를 구성하려면 Azure Content Delivery Network(Azure CDN)를 사용해야 합니다. 고정적인 웹 사이트 기능 자체의 일부로 헤더를 구성할 수 있는 방법은 없습니다. 또한 AuthN 및 AuthZ는 지원되지 않습니다. 

이러한 기능이 시나리오에 중요한 경우 [Azure Static Web Apps](https://azure.microsoft.com/services/app-service/static/)를 사용하는 것이 좋습니다. 정적 웹 사이트에 대한 좋은 대안이며 웹 서버에서 콘텐츠를 렌더링하지 않아도 되는 경우에도 적합합니다. 헤더를 구성할 수 있으며 AuthN/AuthZ가 완전히 지원 됩니다. App Service Static Web Apps는 GitHub 원본으로부터 전체 배포까지 완전 관리형 연속 통합 및 지속적인 업데이트(CI/CD) 워크플로를 제공합니다.
