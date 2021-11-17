---
title: Azure AD 로그인 로그의 기본 정보 식별자 | Microsoft Docs
description: 기본 정보 식별자의 용도에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/30/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ca1ea9524a10ac46a33f6430228d8a45ee66f28
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372797"
---
# <a name="basic-info-identifiers-in-the-azure-ad-sign-in-log"></a>Azure AD 로그인 로그의 기본 정보 식별자

Azure AD는 규정 준수를 위해 Azure 테넌트에 모든 로그인을 기록합니다. IT 관리자는 로그 값을 올바르게 해석할 수 있도록 로그인 로그의 값을 알고 있어야 합니다.
이 문서에서는 로그인 로그의 기본 정보 탭에 있는 식별자에 대해 설명합니다.

## <a name="unique-identifiers"></a>고유 식별자 

Azure AD에서 리소스 액세스에는 다음과 같은 세 가지 관련 구성 요소가 있습니다.

- **누가** – 로그인을 수행하는 ID(사용자)입니다. 
- **방법** – 액세스에 사용되는 클라이언트(애플리케이션)입니다.  
- **대상** – ID가 액세스하는 대상(리소스)입니다.


각 구성 요소에는 연결된 고유 식별자(ID)가 있습니다. 다음은 Windows Azure Service Management API를 사용하여 Azure Portal에 액세스하는 사용자의 예입니다.

![감사 로그 열기](./media/reference-basic-info-identifiers/sign-in-details-basic-info.png)

## <a name="tenant-identifiers"></a>테넌트 식별자

로그인 로그는 두 개의 테넌트 식별자를 추적합니다.

- **홈 테넌트** – 사용자 ID를 소유한 테넌트입니다. 
- **리소스 테넌트** – (대상) 리소스를 소유한 테넌트입니다.

이러한 식별자는 테넌트 간 시나리오와 관련이 있습니다. 예를 들어 테넌트 외부의 사용자가 리소스에 액세스하는 방법을 알아보려면 홈 테넌트가 리소스 테넌트와 일치하지 않는 모든 항목을 선택합니다.

## <a name="request-id"></a>요청 ID

요청 ID는 발급된 토큰에 해당하는 식별자입니다. 특정 토큰을 사용하여 수행된 로그인을 찾으려면 먼저 토큰에서 요청 ID를 추출해야 합니다.


## <a name="correlation-id"></a>상관관계 ID

상관 관계 ID는 동일한 로그인 세션에서 로그인의 상관 관계를 나타내는 식별자입니다. 식별자는 편의를 위해 구현되었습니다. 값이 클라이언트에서 전달한 매개 변수를 기반으로 하기 때문에 정확성은 보장되지 않습니다. 




## <a name="next-steps"></a>다음 단계

* [Azure Active Directory 로그인 로그](concept-sign-ins.md)
* [Azure AD의 로그인 진단이란?](overview-sign-in-diagnostics.md)
