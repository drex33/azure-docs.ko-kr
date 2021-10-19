---
title: FHIR 서비스에 대 한 Azure RBAC 구성-Azure 의료 Api
description: 이 문서에서는 FHIR에 대해 Azure RBAC를 구성 하는 방법을 설명 합니다.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 09/10/2021
ms.author: zxue
ms.openlocfilehash: ff2a488a7ed8a693f23b533242748eed400bd802
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782498"
---
# <a name="configure-azure-rbac-for-the-fhir-service"></a>FHIR 서비스에 대 한 Azure RBAC 구성

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 azure [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/index.yml) 를 사용 하 여 의료 api 데이터 평면에 대 한 액세스 권한을 할당 하는 방법을 알아봅니다. azure RBAC는 데이터 평면 사용자가 azure 구독과 연결 된 Azure Active Directory 테 넌 트에서 관리 되는 경우 데이터 평면 액세스를 할당 하는 데 선호 되는 방법입니다. 

## <a name="assign-roles"></a>역할 할당

사용자, 서비스 사용자 또는 그룹에 FHIR 데이터 평면에 대 한 액세스 권한을 부여 하려면 Azure Portal에서 FHIR 서비스를 선택 합니다. **액세스 제어 (IAM)** 를 선택한 다음 **역할 할당** 탭을 선택 합니다. **+ 추가** 를 선택한 다음 **역할 할당 추가** 를 선택 합니다.
 
역할 할당 옵션이 회색으로 표시 되 면 Azure 구독 관리자에 게 구독 또는 리소스 그룹에 대 한 사용 권한을 부여 하도록 요청 합니다 (예: "사용자 액세스 관리자"). Azure 기본 제공 역할에 대 한 자세한 내용은 [azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조 하세요.

[![액세스 제어 역할 할당입니다. ](media/rbac/role-assignment.png) ](media/rbac/role-assignment.png#lightbox)

역할 선택에서 FHIR 데이터 평면의 기본 제공 역할 중 하나 (예: "FHIR 데이터 참가자")를 검색 합니다. 아래에서 다른 역할을 선택할 수 있습니다.

* **Fhir 데이터 판독기**: 데이터를 읽고 검색 합니다.
* **Fhir 데이터 기록기**: fhir 데이터를 읽고, 쓰고, 일시 삭제할 수 있습니다.
* **Fhir 데이터 내보내기**: 데이터를 읽고 내보낼 수 있습니다 ($export 연산자).
* **Fhir 데이터 참가자**: 모든 데이터 평면 작업을 수행할 수 있습니다.
* **Fhir 데이터 변환기**: 변환기를 사용 하 여 데이터 변환을 수행할 수 있습니다.

**선택** 섹션에서 클라이언트 응용 프로그램 등록 이름을 입력 합니다. 이름이 발견 되 면 응용 프로그램 이름이 나열 됩니다. 응용 프로그램 이름을 선택 하 고 **저장** 을 선택 합니다. 

클라이언트 응용 프로그램을 찾을 수 없는 경우 응용 프로그램 등록을 확인 하 여 이름이 올바른지 확인 합니다. 클라이언트 응용 프로그램이 Azure 의료 Api의 FHIR 서비스 (FHIR 서비스 라고 함)가 배포 되는 동일한 테 넌 트에 만들어졌는지 확인 합니다.


[![역할 할당을 선택 합니다. ](media/rbac/select-role-assignment.png) ](media/rbac/select-role-assignment.png#lightbox)

**액세스 제어 (IAM)** 메뉴 옵션에서 **역할** 할당 탭을 선택 하 여 역할 할당을 확인할 수 있습니다.
 

> [!NOTE]
> 시스템에서 역할 할당을 전파 하는 데 몇 분 정도 걸릴 수 있습니다. 응용 프로그램 또는 다른 테스트 도구에서 FHIR 서비스에 액세스할 수 없는 경우 몇 분 정도 기다릴 수 있습니다. 또한 응용 프로그램 등록에서 Azure 의료 Api에 대 한 user_impersonation 권한을 부여 했는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 데이터 평면에 대 한 Azure 역할을 할당 하는 방법을 배웠습니다. Postman을 사용 하 여 FHIR 서비스에 액세스 하는 방법을 알아보려면 다음을 참조 하세요.

>[!div class="nextstepaction"]
>[Postman을 사용 하 여 FHIR 서비스 액세스](../use-postman.md)