---
title: Microsoft 센티널에 플레이 북 인증 | Microsoft Docs
description: 플레이 북에 게 Microsoft 센티널 및 권한 부여에 대 한 액세스 권한을 부여 하 여 수정 작업을 수행 하는 방법에 대해 알아봅니다.
author: yelevin
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 93a239fea5694430608d6c2fbafb9837624f3f50
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132755624"
---
# <a name="authenticate-playbooks-to-microsoft-sentinel"></a>Microsoft Sentinel에 플레이북 인증

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Logic Apps 작동 방식은 Microsoft 센티널 자체를 포함 하 여 상호 작용 하는 모든 형식의 모든 리소스에 대해 개별적으로 연결 하 고 인증 해야 합니다. Logic Apps는 이 용도로 [특수 커넥터](/connectors/connector-reference/)를 사용하며, 각 리소스 종류에는 고유한 커넥터가 있습니다. 이 문서에서는 작업 영역 테이블의 정보에 액세스 하기 위해 플레이 북이 Microsoft 센티널과 상호 작용 하는 데 사용할 수 있는 [Logic Apps Microsoft 센티널 커넥터](/connectors/azuresentinel/)의 연결 및 인증 유형에 대해 설명 합니다.

이 문서는 플레이 [북에서 트리거와 작업을 사용](playbook-triggers-actions.md)하는 지침과 함께 다른 플레이 북 설명서와 함께 제공 됩니다. [자습서: Microsoft 센티널의 자동화 규칙과 함께 플레이 북을 사용](tutorial-respond-threats-playbook.md)합니다.

플레이 북에 대 한 소개는 [Microsoft 센티널에서 플레이 북을 통해 위협 대응 자동화](automate-responses-with-playbooks.md)를 참조 하세요.

Microsoft 센티널 커넥터의 전체 사양은 [Logic Apps 커넥터 설명서](/connectors/azuresentinel/)를 참조 하세요.

## <a name="authentication"></a>인증

Logic Apps의 Microsoft 센티널 커넥터와 해당 구성 요소의 트리거 및 작업은 관련 작업 영역에서 필요한 권한 (읽기 및/또는 쓰기)이 있는 id를 대신 하 여 작동할 수 있습니다. 커넥터는 다양한 ID 유형을 지원합니다.

- [관리 ID(미리 보기)](#authenticate-with-managed-identity)
- [Azure AD 사용자](#authenticate-as-an-azure-ad-user)
- [서비스 주체(Azure AD 애플리케이션)](#authenticate-as-a-service-principal-azure-ad-application)

    ![인증 옵션](media/authenticate-playbooks-to-sentinel/auth-methods.png)

### <a name="permissions-required"></a>필요한 사용 권한

| 역할/커넥터 구성 요소 | 트리거 | “Get” 작업 | 인시던트 업데이트,<br>주석 추가 |
| ------------- | :-----------: | :------------: | :-----------: |
| **[Microsoft 센티널 판독기](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader)** | &#10003; | &#10003; | &#10007; |
| **Microsoft 센티널 [응답기](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder) / [참가자](../role-based-access-control/built-in-roles.md#microsoft-sentinel-contributor)** | &#10003; | &#10003; | &#10003; |
| 

[Microsoft 센티널의 권한에](./roles.md)대해 자세히 알아보세요.

### <a name="authenticate-with-managed-identity"></a>관리 ID를 사용하여 인증

이 인증 방법을 사용 하면 플레이 북 (논리 앱 워크플로 리소스)에 직접 권한을 부여할 수 있으므로 플레이 북에서 수행 하는 Microsoft 센티널 커넥터 작업은 Microsoft 센티널에 대 한 고유한 사용 권한이 있는 독립 개체인 것 처럼 플레이 북의 대신 작동 합니다. 이 방법을 사용하면 관리해야 하는 ID 수가 줄어듭니다. 

> [!NOTE]
> Microsoft 센티널 작업 영역 같은 다른 리소스에 대 한 관리 id 액세스 권한을 부여 하려면 로그인 한 사용자에 게 Microsoft 센티널 작업 영역의 소유자 또는 사용자 액세스 관리자와 같은 역할 할당을 쓸 수 있는 권한이 있는 역할이 있어야 합니다.

관리 ID를 사용하여 인증하려면 다음을 수행합니다.

1. Logic Apps 워크플로 리소스에서 [관리 ID를 사용](../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)합니다. 요약:

    - 논리 앱 메뉴의 **설정** 에서 **ID** 를 선택합니다. **시스템 할당됨 > 켜짐 > 저장** 을 선택합니다. Azure에서 확인하라는 메시지가 표시되면 **예** 를 선택합니다.

    - 이제 논리 앱에서 시스템이 할당한 ID를 사용할 수 있으며, Azure Active Directory에 등록되고 개체 ID로 표시됩니다.

1. Microsoft 센티널 작업 영역에 대 한 [id 액세스를 제공](../logic-apps/create-managed-service-identity.md#give-identity-access-to-resources) 합니다.
    1. Microsoft 센티널 메뉴에서 **설정** 를 선택 합니다.
    1. **작업 영역 설정** 탭을 선택합니다. 작업 영역 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.
    1. 상단의 단추 모음에서 **추가** 를 선택하고 **역할 할당 추가** 를 선택합니다. **역할 할당 추가** 옵션이 사용되지 않도록 설정되면 역할을 할당할 수 있는 권한이 없는 것입니다.
    1. 표시되는 새 패널에서 적절한 역할을 할당합니다.

        | 역할 | 상황 |
        | --- | --- |
        | [**Microsoft 센티널 응답자**](../role-based-access-control/built-in-roles.md#microsoft-sentinel-responder) | 플레이북에는 인시던트 또는 관심 목록을 업데이트하는 단계가 있습니다. |
        | [**Microsoft 센티널 판독기**](../role-based-access-control/built-in-roles.md#microsoft-sentinel-reader) | 플레이북은 인시던트만 받습니다. |
        |

        [Microsoft 센티널에서](./roles.md)사용 가능한 역할에 대해 자세히 알아보세요.
    1. **액세스 권한 할당** 에서 **논리 앱** 을 선택합니다.
    1. 플레이북이 속한 구독을 선택하고 플레이북 이름을 선택합니다.
    1. **저장** 을 선택합니다.

1. Microsoft 센티널 Logic Apps 커넥터에서 관리 되는 id 인증 방법을 사용 하도록 설정 합니다.

    1. Logic Apps 디자이너에서 Microsoft 센티널 Logic Apps 커넥터 단계를 추가 합니다. 기존 연결에 대한 커넥터를 이미 사용하도록 설정한 경우 **연결 변경** 링크를 클릭합니다.

        ![연결 변경](media/authenticate-playbooks-to-sentinel/change-connection.png)

    1. 연결의 결과 목록에서 맨 아래에 있는 **새 항목 추가** 를 선택합니다. 

    1. **관리 ID를 사용하여 연결(미리 보기)** 을 선택해 새 연결을 만듭니다.

        ![관리 ID 옵션](media/authenticate-playbooks-to-sentinel/auth-methods-msi-choice.png)

    1. 이 연결의 이름을 채운 다음, **시스템이 할당한 관리 ID** 와 **만들기** 를 차례로 선택합니다.

        ![관리 ID를 사용하여 연결](media/authenticate-playbooks-to-sentinel/auth-methods-msi.png)

### <a name="authenticate-as-an-azure-ad-user"></a>Azure AD 사용자로 인증

연결하려면 **로그인** 을 선택합니다. 계정 정보를 입력하라는 메시지가 나타납니다. 작업을 완료한 후에는 화면의 나머지 지침에 따라 연결을 만듭니다.

### <a name="authenticate-as-a-service-principal-azure-ad-application"></a>서비스 주체로 인증(Azure AD 애플리케이션)

Azure AD 애플리케이션을 등록하여 서비스 주체를 만들 수 있습니다. 사용자 계정을 이용하는 대신, 커넥터의 ID로 등록된 애플리케이션을 사용하는 것이 **좋습니다**. 이는 사용 권한을 제어하고, 자격 증명을 관리하며, 커넥터 사용에 대한 특정 제한을 사용하도록 설정할 수 있기 때문입니다.

Microsoft 센티널 커넥터에서 사용자 고유의 응용 프로그램을 사용 하려면 다음 단계를 수행 합니다.

1. Azure AD를 사용하여 애플리케이션을 등록하고 서비스 주체를 만듭니다. [방법을 알아보세요](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).

1. 자격 증명을 가져옵니다(향후 인증용).

    등록된 애플리케이션 블레이드에서 로그인용 애플리케이션 자격 증명을 가져옵니다.

    - **클라이언트 ID**: **개요** 아래
    - **클라이언트 암호**: **인증서 및 비밀** 아래

1. Microsoft 센티널 작업 영역에 권한을 부여 합니다.

    이 단계에서 앱은 Microsoft 센티널 작업 영역에 대 한 작업 권한을 얻게 됩니다.

    1. Microsoft 센티널 작업 영역에서 **설정**  ->  **작업 영역 설정**  ->  **Access control (IAM)** 로 이동 합니다.

    1. **역할 할당 추가** 를 선택합니다.

    1. 애플리케이션에 할당할 역할을 선택합니다. 예를 들어 응용 프로그램이 인시던트를 업데이트 하는 것과 같이 센티널 작업 영역에서 변경 작업을 수행할 수 있도록 하려면 **Microsoft 센티널 참가자** 역할을 선택 합니다. 데이터를 읽기만 하는 작업의 경우 **Microsoft 센티널 Reader** 역할만으로 충분 합니다. [Microsoft 센티널에서 사용 가능한 역할에 대해 자세히 알아보세요](./roles.md).

    1. 필요한 애플리케이션을 찾아서 저장합니다. 기본적으로 Azure AD 애플리케이션이 사용 가능한 옵션에 표시되지 않습니다. 애플리케이션을 찾으려면 이름을 검색하여 선택합니다.

1. Authenticate

    이 단계에서는 앱 자격 증명을 사용하여 Logic Apps의 Sentinel 커넥터에서 인증을 받습니다.

    - **서비스 주체와 연결** 을 선택합니다.

        ![서비스 주체 옵션](media/authenticate-playbooks-to-sentinel/auth-methods-spn-choice.png)

    - 필수 매개 변수를 채웁니다(등록된 애플리케이션 블레이드에서 찾을 수 있음).
        - **테넌트**: **개요** 아래
        - **클라이언트 ID**: **개요** 아래
        - **클라이언트 암호**: **인증서 및 비밀** 아래
        
        ![서비스 주체를 사용하여 연결](media/authenticate-playbooks-to-sentinel/auth-methods-spn.png)

### <a name="manage-your-api-connections"></a>API 연결 관리

처음으로 인증을 만들 때마다 API 연결 형식의 새로운 Azure 리소스가 만들어집니다. 동일한 리소스 그룹의 모든 Microsoft 센티널 작업 및 트리거에서 동일한 API 연결을 사용할 수 있습니다.

모든 API 연결은 **API 연결** 블레이드(Azure Portal에서 *API 연결* 검색)를 찾을 수 있습니다.

**리소스** 블레이드로 이동한 후 표시를 *API 연결* 형식으로 필터링하여 찾을 수도 있습니다. 이 방법으로 대량 작업 시 여러 연결을 선택할 수 있습니다.

기존 연결에 대한 권한 부여를 변경하려면 연결 리소스를 입력하고 **API 연결 편집** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 센티널에 Logic Apps 기반 플레이 북을 인증 하는 다양 한 방법에 대해 알아보았습니다.

- [플레이북에서 트리거와 작업을 사용](playbook-triggers-actions.md)하는 방법에 대해 자세히 알아보세요.
