---
title: Azure AD Connect 클라우드 동기화의 주문형 프로비전
description: 이 문서에서는 Azure AD Connect의 클라우드 동기화 기능을 사용하여 구성 변경 내용을 테스트하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20719182a9b97bc783ef2bdc16ccad327dba79aa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742575"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Azure AD Connect 클라우드 동기화의 주문형 프로비전

Azure AD (Azure Active Directory) Connect의 클라우드 동기화 기능을 사용하여 변경 내용을 단일 사용자에게 적용하여 이러한 구성 변경을 테스트할 수 있습니다. 이러한 주문형 프로비전을 사용하여 구성에 만들어진 변경 내용이 제대로 적용되었으며 Azure AD에 올바르게 동기화되었는지 확인할 수 있습니다.  

> [!IMPORTANT] 
> 주문형 프로비전을 사용하는 경우, 범위 지정 필터는 선택한 사용자에게 적용되지 않습니다. 지정한 조직 구성 단위 외부에 있는 사용자에 대해 주문형 프로비전을 사용할 수 있습니다.

추가 정보 및 예제는 다음 비디오를 참조하세요.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWK5mW]

## <a name="validate-a-user"></a>사용자 유효성 검사
주문형 프로비전을 사용하려면 다음 단계를 수행합니다.

1.  Azure Portal에서 **Azure Active Directory** 를 선택합니다.
2.  **Azure AD Connect** 를 선택합니다.
3.  **클라우드 동기화 관리** 를 선택합니다.

    ![클라우드 동기화를 관리하기 위한 링크를 보여 주는 스크린샷](media/how-to-install/install-6.png)
4. **구성** 아래에서 구성을 선택합니다.
5. **유효성 검사** 아래에서 **사용자 프로비전** 단추를 선택합니다. 

   ![사용자를 프로비전하는 단추를 보여 주는 스크린샷](media/how-to-on-demand-provision/on-demand-2.png)

6. **요청 시 프로비전** 화면에서 사용자의 고유 이름을 입력하고 **프로비전** 단추를 선택합니다.  
 
   ![사용자 이름 및 프로비전 단추를 보여 주는 스크린샷](media/how-to-on-demand-provision/on-demand-3.png)
7. 프로비전이 완료되면 4개의 녹색 확인 표시와 함께 성공 화면이 나타납니다. 모든 오류는 왼쪽에 표시됩니다.

   ![프로비전 성공을 보여 주는 스크린샷](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>프로비전에 대한 세부 정보 가져오기
이제 사용자 정보를 살펴보고 구성에서 변경한 내용이 적용되었는지 여부를 판단할 수 있습니다. 이 문서의 나머지 부분에서는 성공적으로 동기화된 사용자의 세부 정보에 표시되는 개별 섹션에 대해 설명합니다.

### <a name="import-user"></a>사용자 가져오기
**사용자 가져오기** 섹션은 Active Directory에서 가져온 사용자에 대한 정보를 제공합니다. 이는 Azure AD에 프로비전하기 전에 사용자에 대한 정보를 보여 줍니다. **자세히 보기** 링크를 선택하여 이 정보를 표시합니다.

![가져온 사용자에 대한 세부 정보를 보는 단추의 스크린샷](media/how-to-on-demand-provision/on-demand-5.png)

이 정보를 사용하여 가져온 다양한 특성(및 해당 값)을 볼 수 있습니다. 사용자 지정 특성 매핑을 만든 경우, 여기에서 값을 확인할 수 있습니다.

![사용자 세부 정보를 보여 주는 스크린샷](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>사용자가 범위 내에 있는지 확인
**사용자가 범위 내에 있는지 확인** 섹션은 Azure AD로 가져온 사용자가 범위 내에 있는지 여부에 대한 정보를 제공합니다. **자세히 보기** 링크를 선택하여 이 정보를 표시합니다.

![가져온 사용자에 대한 세부 정보를 보는 단추의 스크린샷](media/how-to-on-demand-provision/on-demand-7.png)

이 정보를 사용하여 사용자가 범위 내에 있는지 확인할 수 있습니다.

![사용자 범위 세부 정보를 보여 주는 스크린샷](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>원본 및 대상 시스템 간 사용자 일치
**원본 및 대상 시스템 간 사용자 일치** 섹션에서는 사용자가 Azure AD에 이미 있는지 여부와 새 사용자를 프로비전하는 대신 조인(join)이 발생하는지 여부에 대한 정보를 제공합니다. **자세히 보기** 링크를 선택하여 이 정보를 표시합니다.

![가져온 사용자에 대한 세부 정보를 보는 단추의 스크린샷](media/how-to-on-demand-provision/on-demand-8.png)

이 정보를 사용하여 일치 항목을 찾았는지 아니면 새 사용자를 만들 것인지를 확인할 수 있습니다.

![사용자 정보를 보여 주는 스크린샷](media/how-to-on-demand-provision/on-demand-11.png)

일치하는 세부 정보에는 다음 세 가지 작업 중 하나가 포함된 메시지가 표시됩니다.
- **만들기**: 사용자가 Azure AD에서 만들어집니다.
- **업데이트**: 구성에서 변경한 내용에 따라 사용자가 업데이트됩니다.
- **삭제**: 사용자가 Azure AD에서 제거됩니다.

수행한 작업 유형에 따라 메시지는 달라집니다.

### <a name="perform-action"></a>수행 작업
**작업 수행** 섹션에서는 구성이 적용된 후 Azure AD로 프로비전되거나 내보내진 사용자에 대한 정보를 제공합니다. 이는 Azure AD에 프로비전하기 전에 사용자에 대한 정보를 보여 줍니다. **자세히 보기** 링크를 선택하여 이 정보를 표시합니다.

![수행된 작업에 대한 세부 정보를 보는 단추의 스크린샷](media/how-to-on-demand-provision/on-demand-9.png)

이 정보를 사용하여 구성이 적용된 후 특성 값을 확인할 수 있습니다. 그것들이 가져온 것과 유사한가요? 아니면 다른가요? 구성이 성공적으로 적용되었나요?  

이 프로세스를 통해 클라우드를 통해 Azure AD 테넌트로 이동할 때 특성 변환을 추적할 수 있습니다.

![추적 특성 세부 정보를 보여 주는 스크린샷](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>다음 단계 

- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)
- [Azure AD Connect 클라우드 동기화 설치](how-to-install.md)
 