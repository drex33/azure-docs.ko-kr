---
title: Azure AD Connect 동기화 V2 엔드포인트 | Microsoft Docs
description: 이 문서에서는 Azure AD Connect 동기화 v2 엔드포인트 API에 대한 업데이트를 다룹니다.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f964829c0d775bd33ea0c44386820ae2ba921ce1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466051"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Azure AD Connect 동기화 V2 엔드포인트 API 
Microsoft는 Azure AD Connect에 대한 새 엔드포인트(API)를 배포하여 Azure Active Directory에 대한 동기화 서비스 작업의 성능을 향상시켰습니다. 새 V2 엔드포인트를 활용하면 Azure AD로 내보내기 및 가져오기에 대한 성능이 크게 향상됩니다. 이 새 엔드포인트는 다음을 지원합니다.
    
 - 최대 250k 멤버가 포함된 그룹 동기화
 - Azure AD로 내보내기 및 가져오기 성능 향상
 
> [!NOTE]
> 현재 새 엔드포인트에는 다시 작성되는 Microsoft 365 그룹에 대한 그룹 크기 제한이 구성되어 있지 않습니다. 이는 Active Directory 및 동기화 주기 대기 시간에 영향을 미칠 수 있습니다. 그룹 크기를 점진적으로 늘리는 것이 좋습니다.  

>[!NOTE]
> Azure AD Connect 동기화 V2 엔드포인트 API는 일반 공급되지만 현재는 다음 Azure 환경에서만 사용할 수 있습니다.
> - Azure Commercial
> - Azure 중국 클라우드
> - Azure 미국 정부 클라우드는 Azure 독일 클라우드에서 사용할 수 없습니다

## <a name="prerequisites"></a>사전 요구 사항  
새 V2 엔드포인트를 사용하려면 Azure AD Connect v2.0을 사용해야 합니다. AADConnect V2.0을 배포하면 V2 엔드포인트가 자동으로 사용하도록 설정됩니다.
V2 엔드포인트에 대한 지원은 더 이상 V1.x 버전에서 사용할 수 없습니다. 그룹을 50,000명 넘는 구성원과 동기화해야 하는 경우 Azure AD Connect V2.0으로 업그레이드해야 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답  
 
**언제 새 엔드포인트가 업그레이드 및 새 설치의 의 기본값이 되나요?**   V2 엔드포인트는 AADConnect V2.0에 대한 기본 설정이며 AADConnect V1.x에는 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](how-to-connect-sync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)
