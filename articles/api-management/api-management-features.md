---
title: Azure API Management 계층의 기능 기반 비교 | Microsoft Docs
description: 제공되는 기능을 기준으로 API Management 계층을 비교합니다. 각 가격 책정 계층에서 사용할 수 있는 주요 기능이 요약된 표를 참조하세요.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: danlep
ms.openlocfilehash: 55e5d8de3761bb50295f849232363b5643329c2b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596356"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Azure API Management 계층의 기능 기반 비교

각 API Management [가격 책정 계층](https://aka.ms/apimpricing)은 고유한 기능 세트 및 단위당 [용량](api-management-capacity.md)을 제공합니다. 다음 표에는 각 계층에서 사용할 수 있는 주요 기능이 요약되어 있습니다. 일부 기능은 다르게 작동하거나, 계층에 따라 다른 기능을 제공할 수 있습니다. 이러한 경우 해당 차이점은 개별 기능을 설명하는 설명서 문서에 제공됩니다.

> [!IMPORTANT]
> 개발자 계층은 비프로덕션 사용 사례 및 평가용입니다. SLA는 제공하지 않습니다.

| 기능                                                                                      | Consumption | 개발자 | Basic | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD 통합<sup>1</sup>                                                             | 예          | 예       | 아니요    | 예      | 예     |
| 가상 네트워크(VNet) 지원                                                               | 예          | 예       | 아니요    | 아니요       | 예     |
| 다중 지역 배포                                                                      | 예          | 아니요        | 아니요    | 아니요       | 예     |
| 가용성 영역                                                                           | 예          | 아니요        | 아니요    | 아니요       | 예     |
| 여러 사용자 지정 도메인 이름                                                                 | 예          | 예        | 아니요    | 아니요       | 예     |
| 개발자 포털<sup>2</sup>                                                                 | 예          | 예       | 예   | 예      | 예     |
| 기본 제공 캐시                                                                               | 예          | 예       | 예   | 예      | 예     |
| 기본 제공 분석                                                                           | 예          | 예       | 예   | 예      | 예     |
| [자체 호스팅 게이트웨이](self-hosted-gateway-overview.md)<sup>3</sup>                           | 예          | 예       | 아니요    | 아니요       | 예     |
| [TLS 설정](api-management-howto-manage-protocols-ciphers.md)                             | 예         | 예       | 예   | 예      | 예     |
| [외부 캐시](./api-management-howto-cache-external.md)                                                    | 예         | 예       | 예   | 예      | 예     |
| [클라이언트 인증서 인증](api-management-howto-mutual-certificates-for-clients.md) | 예         | 예       | 예   | 예      | 예     |
| [Backup 및 복원](api-management-howto-disaster-recovery-backup-restore.md)               | 예          | 예       | 예   | 예      | 예     |
| [Git을 통한 관리](api-management-configuration-repository-git.md)                        | 예          | 예       | 예   | 예      | 예     |
| 직접 관리 API                                                                        | 예          | 예       | 예   | 예      | 예     |
| Azure Monitor 로그 및 메트릭                                                               | 예          | 예       | 예   | 예      | 예     |
| 고정 IP                                                                                    | 예          | 예       | 예   | 예      | 예     |
| [WebSocket API(미리 보기)](websocket-api.md)                                                                                    | 아니요          | 예       | 예   | 예      | 예     |

<sup>1</sup> 개발자 포털에서 사용자 로그인의 ID 공급자로 Azure AD(및 Azure AD B2C)를 사용할 수 있습니다.<br/>
<sup>2</sup> 관련 기능(예: 사용자, 그룹, 문제, 애플리케이션, 이메일 템플릿 및 알림)을 포함합니다.<br/>
<sup>3</sup> 개발자 계층에서 자체 호스팅 게이트웨이는 단일 게이트웨이 노드로 제한됩니다.<br/>