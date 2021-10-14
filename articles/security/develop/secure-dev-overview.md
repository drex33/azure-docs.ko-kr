---
title: Microsoft Azure의 보안 개발 모범 사례
description: 더 안전한 코드를 개발하고 클라우드에서 보다 안전한 애플리케이션을 배포하는 데 도움이 되는 모범 사례입니다.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 3b0ebb1989e350962317adce64c0542b4a296924
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000767"
---
# <a name="secure-development-best-practices-on-azure"></a>Azure의 보안 개발 모범 사례
이 문서 시리즈에는 클라우드용 애플리케이션을 개발할 때 고려해야 할 보안 작업과 제어 기능이 나와 있습니다. Microsoft SDL(Security Development Lifecycle)의 단계와 주명 주기의 각 단계에서 고려해야 하는 보안 질문 및 개념에 대해 설명합니다. 목적은 수명 주기의 각 단계에서 더 안전한 애플리케이션을 디자인하고 개발 및 배포하기 위해 사용할 수 있는 활동과 Azure 서비스를 정의하는 데 도움을 주는 것입니다.

이 문서의 권장 사항은 Azure 보안과 고객의 경험을 기반으로 합니다. 이러한 문서를 개발 프로젝트의 특정 단계를 진행할 때 고려해야 할 사항의 참조로 사용할 수는 있지만, 모든 문서를 처음부터 끝까지 한 번 이상 읽어 보는 것이 좋습니다. 모든 문서를 읽으면 프로젝트의 이전 단계에서 누락했을 수 있는 개념을 이해할 수 있습니다. 제품을 출시하기 전에 이러한 개념을 구현하면 보안 소프트웨어를 빌드하고, 보안 규정 준수 요구 사항을 해결하며, 개발 비용을 절감할 수 있습니다.

이러한 문서는 보안 Azure 솔루션을 빌드, 배포하는 모든 수준의 소프트웨어 디자이너, 개발자 및 테스터를 위한 리소스입니다.

## <a name="overview"></a>개요

보안은 모든 애플리케이션의 가장 중요한 측면 중 하나로, 올바르게 수행하기가 그리 간단하지 않습니다. 다행히도 Azure는 클라우드에서 애플리케이션을 보호하는 데 도움이 될 수 있는 많은 서비스를 제공합니다. 이러한 문서에서는 더 안전한 코드를 개발하고 클라우드에서 보다 안전한 애플리케이션을 배포할 수 있도록 소프트웨어 개발 수명 주기의 각 단계에서 구현할 수 있는 활동과 Azure 서비스를 다룹니다.

## <a name="security-development-lifecycle"></a>보안 개발 수명 주기

프로젝트 방법론([waterfall](https://en.wikipedia.org/wiki/Waterfall_model), [Agile](https://en.wikipedia.org/wiki/Agile_software_development), [DevOps](https://en.wikipedia.org/wiki/DevOps))에 관계없이 보안 소프트웨어 개발 모범 사례를 따르려면 요구 사항 분석부터 유지 관리까지 아우르는 소프트웨어 개발 수명 주기의 각 단계에 보안을 통합해야 합니다. 세간의 이목을 끄는 데이터 침해가 발생하고 운영 보안상의 결함을 악용함에 따라, 더 많은 개발자가 개발 프로세스 전반에 걸쳐 보안 문제를 해결해야 한다는 점을 이해하고 있습니다.

개발 수명 주기의 문제를 늦게 수정할수록 수정 비용이 더 많이 발생합니다. 보안 문제도 예외는 아닙니다. 소프트웨어 개발 초기 단계에서 보안 문제를 무시하면, 이어지는 각 단계에서 이전 단계의 취약성이 이어질 수 있습니다. 최종 제품에는 여러 가지 보안 문제와 위반 가능성이 누적됩니다. 개발 수명 주기의 각 단계에 보안 기능을 빌드하면 문제를 조기에 파악할 수 있으며, 개발 비용을 줄이는 데 도움이 됩니다.

Microsoft [SDL(Security Development Lifecycle)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) 단계에 따라 수명 주기의 각 단계에서 보안 소프트웨어 개발 사례를 충족하는 데 사용할 수 있는 활동과 Azure 서비스를 소개합니다.

SDL 단계는 다음과 같습니다.

  - 학습
  - 요구 사항
  - 디자인
  - 구현
  - 확인
  - 해제
  - 응답

![보안 개발 수명 주기](./media/secure-dev-overview/01-sdl-phase.png)

이러한 문서에서는 SDL 단계를 디자인, 개발, 배포로 그룹화합니다.

## <a name="engage-your-organizations-security-team"></a>조직의 보안팀 참여

조직에는 개발 수명 주기 동안의 보안 활동을 처음부터 끝까지 지원하는 공식적인 애플리케이션 보안 프로그램이 있을 수 있습니다. 조직에 보안팀과 규정 준수팀이 있는 경우, 애플리케이션 개발을 시작하기 전에 해당 팀을 참여시켜야 합니다. SDL의 각 단계에서 누락된 작업이 있는지 확인합니다.

많은 독자에게 참여할 보안팀 또는 규정 준수팀이 없을 수도 있음을 알고 있습니다. 이러한 문서는 SDL의 각 단계에서 고려해야 하는 보안 질문과 결정 사항을 안내하는 데 도움이 될 수 있습니다.

## <a name="resources"></a>리소스

다음 리소스를 사용하여 보안 애플리케이션을 개발하고 Azure에서 애플리케이션을 보호하는 데 도움이 되는 방법을 자세히 알아보세요.

[Microsoft SDL(Security Development Lifecycle)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) – SDL은 개발자가 더 안전한 소프트웨어를 빌드하는 데 도움이 되는 Microsoft의 소프트웨어 개발 프로세스입니다. 이는 개발 비용을 절감하면서 보안 규정 준수 요구 사항을 해결하는 데 도움이 됩니다.

[OWASP(Open Web Application Security Project)](https://www.owasp.org/index.php/Main_Page) – OWASP는 웹 애플리케이션 보안 필드에서 무료로 사용할 수 있는 문서, 방법론, 설명서, 도구 및 기술을 생성하는 온라인 커뮤니티입니다.

[Pushing Left, Like a Boss](https://wehackpurple.com/pushing-left-like-a-boss-part-1/) -개발자가 보다 안전한 코드를 만들려면 완료해야 하는 다양한 유형의 애플리케이션 보안 활동을 개괄적으로 설명하는 일련의 온라인 문서입니다.

[Microsoft ID 플랫폼](../../active-directory/develop/index.yml) – Microsoft ID 플랫폼은 Azure AD ID 서비스와 개발자 플랫폼의 발전된 형태입니다. 인증 서비스, 오픈 소스 라이브러리, 애플리케이션 등록/구성, 완전한 개발자 설명서, 코드 샘플 및 기타 개발자 콘텐츠로 구성되는 모든 기능을 갖춘 플랫폼입니다. Microsoft ID 플랫폼은 OAuth 2.0 및 OpenID Connect 등의 산업 표준 프로토콜을 지원합니다.

[Azure 솔루션의 보안 모범 사례](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) - Azure를 사용하여 클라우드 솔루션을 디자인, 배포 및 관리할 때 사용할 수 있는 보안 모범 사례 컬렉션입니다. 이 문서는 IT 전문가를 위한 리소스입니다. 여기에는 보안 Azure 솔루션을 빌드하고 배포하는 디자이너, 설계자, 개발자 및 테스터가 포함될 수 있습니다.

[Azure의 보안 및 규정 준수 청사진](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – Azure 보안 및 규정 준수 청사진은 엄격한 규정과 표준을 준수하는 클라우드 기반 애플리케이션을 빌드하고 시작하는 데 도움을 받을 수 있는 리소스입니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서는 보안 애플리케이션을 디자인하고 개발, 배포하는 데 도움을 받을 수 있는 보안 제어 및 활동 사항을 권장합니다.

- [보안 애플리케이션 디자인](secure-design.md)
- [보안 애플리케이션 개발](secure-develop.md)
- [보안 애플리케이션 배포](secure-deploy.md)
