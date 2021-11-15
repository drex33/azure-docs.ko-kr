---
title: Azure AD Connect 클라우드 동기화 실수로 인한 삭제
description: 이 문서에서는 실수로 인한 삭제 기능을 사용하여 삭제를 방지하는 방법에 대해 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5055b847eab1ee53688abfa308b5969f7526f668
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742674"
---
# <a name="accidental-delete-prevention"></a>실수로 인한 삭제 방지

다음 문서에서는 Azure AD Connect 클라우드 동기화에 사용하는 실수로 인한 삭제 기능을 설명합니다. 실수로 인한 삭제 기능은 여러 사용자 및 그룹에 영향을 주는 실수로 인한 구성 변경과 온-프레미스 디렉터리 변경을 방지하도록 설계되었습니다.  이 기능을 사용하여 다음을 할 수 있습니다.

- 실수로 인한 삭제를 자동으로 방지하는 기능을 구성합니다. 
- 구성이 적용되는 개체의 수(임계값)를 설정합니다. 
- 이 시나리오를 위해 문제가 되는 동기화 작업이 격리되면 메일 알림을 받을 수 있도록 알림 메일 주소를 설정합니다. 

이 기능을 사용하기 위해서는 삭제된 경우 동기화가 중지되어야 하는 개체 수의 임계값을 설정합니다.  이 경우 설정된 임계값에 도달하면 동기화가 중지되고 지정된 메일로 알림이 전송됩니다.  이 알림을 통해 진행 상황을 조사할 수 있습니다.

추가 정보 및 예제는 다음 비디오를 참조하세요.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWK5mV]


## <a name="configure-accidental-delete-prevention"></a>실수로 인한 삭제 방지 구성
새 기능을 사용하려면 다음 단계를 따릅니다.


1.  Azure Portal에서 **Azure Active Directory** 를 선택합니다.
2.  **Azure AD Connect** 를 선택합니다.
3.  **클라우드 동기화 관리** 를 선택합니다.
4. **구성** 아래에서 구성을 선택합니다.
5. **설정** 아래에서 다음을 입력합니다.
    - **알림 메일** - 알림에 사용되는 메일
    - **실수로 인한 삭제 방지** - 이 확인란을 선택하여 기능을 사용하도록 설정합니다.
    - **실수로 인한 삭제 임계값** - 동기화를 중지하고 알림을 보내는 개체 수를 입력합니다.

![실수로 삭제](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>실수로 삭제된 인스턴스의 복구
실수로 삭제한 경우 프로비저닝 에이전트 구성의 상태에서 이를 확인할 수 있습니다.  **삭제 임계값이 초과됨** 이라고 표시됩니다.
 
![실수로 삭제 상태](media/how-to-accidental-deletes/delete-1.png)

**삭제 임계값이 초과됨** 을 클릭하여 동기화 상태 정보를 확인합니다.  그러면 추가 정보가 제공됩니다. 
 
 ![동기화 상태](media/how-to-accidental-deletes/delete-2.png)

줄임표를 마우스 오른쪽 단추로 클릭하면 다음 옵션이 표시됩니다.
 - 프로비저닝 로그 보기
 - 에이전트 보기
 - 삭제 허용

 ![마우스 오른쪽 단추로 클릭](media/how-to-accidental-deletes/delete-3.png)

**프로비저닝 로그 보기** 를 사용하면 **StagedDelete** 항목을 확인하고 삭제된 사용자에 대해 제공된 정보를 검토할 수 있습니다.
 
 ![프로비저닝 로그](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>삭제 허용

**삭제 허용** 작업은 실수로 삭제 임계값을 트리거한 개체를 삭제합니다.  다음 절차에 따라 삭제를 허용합니다.  

1. 타원을 마우스 오른쪽 단추로 클릭하고 **삭제 허용** 을 선택합니다.
2. 확인 메시지에서 **예** 를 클릭하여 삭제를 허용합니다.
 
 ![확인의 예](media/how-to-accidental-deletes/delete-4.png)

3. 삭제가 허용되었다는 확인 메시지가 표시되고 상태가 다음 주기에 정상으로 복귀됩니다. 
 
 ![삭제 허용](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>삭제 거부

삭제를 허용하지 않으려면 다음을 수행해야 합니다.
- 삭제 원본 조사
- 문제 해결(예: OU가 실수로 범위를 벗어났으며, 이제 다시 범위에 추가함)
- 에이전트 구성에서 **동기화 다시 시작** 실행

## <a name="next-steps"></a>다음 단계 

- [Azure AD Connect 클라우드 동기화 관련 문제 해결](how-to-troubleshoot.md)
- [Azure AD Connect 클라우드 동기화 오류 코드](reference-error-codes.md)
 

