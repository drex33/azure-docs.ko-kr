---
title: Microsoft Threat Modeling Tool 개요 - Azure
description: Microsoft Threat Modeling Tool에 대한 개요 페이지로, 도구 시작에 관련된 정보 및 위협 모델링 프로세스를 포함합니다.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 02/16/2017
ms.openlocfilehash: 6a637dc431b0f9cd8c40cfa1c048559a56a6b97f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94518059"
---
# <a name="microsoft-threat-modeling-tool"></a>Microsoft 위협 모델링 도구

위협 모델링 도구는 Microsoft SDL(Security Development Lifecycle)의 핵심 요소입니다. 소프트웨어 설계자는 이 도구를 사용하여 비교적 쉽고 비용 효과적으로 문제를 해결할 수 있는 조기에 잠재적인 보안 문제를 식별하고 완화할 수 있습니다. 결과적으로 전체 개발 비용이 크게 절감됩니다. 또한 보안 전문가가 아닌 사용자를 염두에 두고 개발되었으므로 위협 모델을 만들고 분석하기 위한 정확한 지침을 제공하여 모든 개발자가 쉽게 위협 모델링을 수행할 수 있도록 합니다. 

이 도구를 사용하면 누구나 다음을 수행할 수 있습니다.

* 시스템의 보안 설계에 대한 기본 사항 전달
* 입증된 방법을 사용하여 이러한 설계의 잠재적인 보안 문제 분석
* 보안 문제에 대한 해결 방법 제안 및 관리

일부 도구 기능 및 혁신 기능은 다음과 같습니다.

* **Automation:** 모형 그리기에 대한 지침 및 피드백
* **요소 기준 STRIDE:** 위협 및 해결 방법에 대한 단계별 분석
* **보고:** 확인 단계의 보안 작업 및 테스트
* **고유한 방법:** 위협을 보다 잘 시각화하고 이해하도록 지원
* **소프트웨어 중심의 개발자를 위한 설계:** 많은 접근 방식이 자산 또는 공격자에 주안점을 둡니다. Microsoft는 소프트웨어 중심 방식을 제공합니다. 소프트웨어 아키텍처에 대한 그림 그리기와 같이 모든 소프트웨어 개발자와 설계자에게 친숙한 작업을 토대로 기능을 구현하고 있습니다.
* **설계 분석에 주력:** "위협 모델링"이라는 용어는 요구 사항 또는 설계 분석 기법을 나타낼 수 있습니다. 경우에 따라 두 가지가 복잡하게 혼합된 경우를 나타냅니다. Microsoft SDL의 위협 모델링 방법은 설계 분석 기법에 초점을 맞춥니다.

## <a name="next-steps"></a>다음 단계

아래 표에는 Threat Modeling Tool을 시작하기 위한 중요한 링크가 포함되어 있습니다. [시스템 요구 사항](threat-modeling-tool-releases.md)도 함께 참조하세요.

| 단계  | Description                                                                                   |
| ----- | --------------------------------------------------------------------------------------------- |
| **1** | [위협 모델링 도구 다운로드](https://aka.ms/threatmodelingtool)                                |
| **2** | [시작 가이드 읽기](threat-modeling-tool-getting-started.md)    |
| **3** | [기능 익히기](threat-modeling-tool-feature-overview.md)   |
| **4** | [생성된 위협 범주에 대해 알아보기](threat-modeling-tool-threats.md)   |
| **5** | [생성된 위협에 대한 해결 방법 찾기](threat-modeling-tool-mitigations.md) |

## <a name="resources"></a>리소스

위협 모델링에 여전히 관련이 있는 몇 가지 이전 문서는 다음과 같습니다.

* [위협 모델링의 중요성에 대한 문서](/archive/msdn-magazine/2009/january/security-briefs-getting-started-with-the-sdl-threat-modeling-tool)
* [신뢰할 수 있는 컴퓨팅으로 게시된 학습](https://www.microsoft.com/download/details.aspx?id=16420)

몇 가지 위협 모델링 도구 전문가가 수행한 작업을 확인해 보세요.

* [위협 관리자](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Simone Curzi 보안 블로그](https://simoneonsecurity.com/)