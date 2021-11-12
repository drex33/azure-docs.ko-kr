---
title: 사용자 만들기 및 관리
description: 센서 및 온-프레미스 관리 콘솔의 사용자를 만들고 관리합니다. 사용자는 관리자, 보안 분석가 또는 읽기 전용 사용자의 역할로 할당될 수 있습니다.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 73e08c7cdca6e57445566a99cb779a322306f944
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318943"
---
# <a name="about-defender-for-iot-console-users"></a>Defender for IoT 콘솔 사용자 정보

이 문서에서는 센서 및 온-프레미스 관리 콘솔의 사용자를 만들고 관리하는 방법을 설명합니다. 사용자 역할에는 관리자, 보안 분석가 또는 읽기 전용 사용자가 포함됩니다. 각 역할은 센서 또는 온-프레미스 관리 콘솔용 도구에 대한 권한 범위와 관련이 있습니다. 역할은 Microsoft Defender for IoT에 대한 세부적인 보안 액세스를 용이하게 하기 위해 설계되었습니다.

사용자 활동을 추적하고 Active Directory 로그인을 지원하는 기능도 제공됩니다.

기본적으로 각 센서 및 온-프레미스 관리 콘솔은 ‘cyberx 및 지원’ 사용자를 통해 설치됩니다. 해당 사용자는 문제 해결 및 설치를 위한 고급 도구에 액세스할 수 있습니다. 관리자 사용자는 해당 사용자 자격 증명을 사용하여 로그인하고, 관리 사용자를 만든 후 보안 분석가 및 읽기 전용 사용자를 위한 추가 사용자를 만들어야 합니다.

## <a name="role-based-permissions"></a>역할 기반 권한
다음과 같은 사용자 역할을 사용할 수 있습니다.

- **읽기 전용**: 읽기 전용 사용자는 디바이스 맵에서 경고 및 디바이스 보기와 같은 작업을 수행합니다. 해당 사용자는 **탐색** 아래에 표시되는 옵션에 액세스할 수 있습니다.

- **보안 분석가**: 보안 분석가에게는 읽기 전용 사용자 권한이 있습니다. 또한 디바이스에 대한 작업을 수행하고, 경고를 승인하고, 조사 도구를 사용할 수 있습니다. 해당 사용자는 **탐색** 및 **분석** 아래에 표시되는 옵션에 액세스할 수 있습니다.

- **관리자**: 관리자는 시스템 구성 정의, 사용자 만들기, 관리 등의 모든 도구에 액세스할 수 있습니다. 해당 사용자는 **탐색**, **분석**, **관리** 아래에 표시되는 옵션에 액세스할 수 있습니다.

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>온-프레미스 관리 콘솔 도구에 대한 역할 기반 권한

이 섹션에서는 온-프레미스 관리 콘솔의 관리자, 보안 분석가, 읽기 전용 사용자가 사용할 수 있는 권한에 대해 설명합니다.  

| 사용 권한 | 읽기 전용 | 보안 분석가 | 관리자 |
|--|--|--|--|
| 엔터프라이즈 맵 보기 및 필터링 | ✓ | ✓ | ✓ |
| 사이트 빌드 |  |  | ✓ |
| 사이트 관리(영역 추가 및 편집) |  |  | ✓ |
| 디바이스 인벤토리 보기 및 필터링 | ✓ | ✓ | ✓ |
| 경고 보기 및 관리: 승인, 정보 확인, 고정 | ✓ | ✓ | ✓ |
| 보고서 생성 |  | ✓ | ✓ |
| 위험 평가 보고서 보기 |  | ✓ | ✓ |
| 경고 제외 설정 |  | ✓ | ✓ |
| 액세스 그룹 보기 또는 정의 |  |  | ✓ |
| 시스템 설정 관리 |  |  | ✓ |
| 사용자 관리 |  |  | ✓ |
| 파트너에게 경고 데이터 보내기 |  |  | ✓ |
| 인증서 관리 |  |  | ✓ |
| 사용자가 활성 상태가 아닐 때의 세션 시간 제한 | 30분 | 30분  | 30분  |

#### <a name="assign-users-to-access-groups"></a>액세스 그룹에 사용자 할당

관리자는 특정 ‘액세스 그룹’에 사용자를 할당하여 Defender for IoT의 사용자 액세스 제어를 강화할 수 있습니다. 액세스 그룹은 센서가 있는 영역, 사이트, 지역, 사업부에 할당됩니다. 사용자에게 액세스 그룹을 할당하여 관리자는 사용자가 디바이스 검색 항목을 관리하고 분석하는 위치를 제어할 수 있습니다. 

해당 방식은 사용자 권한이 복잡하거나 전역 조직 보안 정책에 따라 결정될 수 있는 대기업에서도 충분히 사용할 수 있습니다. 자세한 내용은 [전역 액세스 제어 정의](how-to-define-global-user-access-control.md)를 참조하세요.

### <a name="role-based-permissions-to-sensor-tools"></a>센서 도구에 대한 역할 기반 권한

이 섹션에서는 센서 관리자, 보안 분석가 및 읽기 전용 사용자가 사용할 수 있는 권한에 대해 설명합니다.  

| 사용 권한 | 읽기 전용 | 보안 분석가 | 관리자 |
|--|--|--|--|
| 대시보드 보기 | ✓ | ✓ | ✓ |
| 맵 확대/축소 보기 제어 |  |  | ✓ |
| 경고 보기 | ✓ | ✓ | ✓ |
| 경고 관리: 승인, 정보 확인, 고정 |  | ✓ | ✓ |
| 타임라인에서 이벤트 보기 |  | ✓ | ✓ |
| 디바이스, 알려진 검색용 디바이스, 프로그래밍용 디바이스에 권한 부여 |  | ✓ | ✓ |
| 조사 데이터 보기 | ✓ | ✓ | ✓ |
| 시스템 설정 관리 |  |  | ✓ |
| 사용자 관리 |  |  | ✓ |
| 역방향 조회를 위한 DNS 서버 |  |  | ✓ |
| 파트너에게 경고 데이터 보내기 |  | ✓ | ✓ |
| 경고 주석 만들기 |  | ✓ | ✓ |
| 프로그래밍 변경 내역 보기 | ✓ | ✓ | ✓ |
| 사용자 지정 경고 규칙 만들기 |  | ✓ | ✓ |
| 동시에 여러 알림 관리 |  | ✓ | ✓ |
| 인증서 관리 |  |  | ✓ |
| 사용자가 활성 상태가 아닐 때의 세션 시간 제한 | 30분 | 30분 | 30분 |

## <a name="define-users"></a>사용자 정의

이 섹션에서는 사용자를 정의하는 방법을 설명합니다. Cyberx, 지원, 관리자 사용자는 다른 사용자 정의를 추가, 제거, 업데이트할 수 있습니다.

**사용자를 정의하려면**:

1. 센서 또는 온-프레미스 관리 콘솔의 왼쪽 창에서 **사용자** 를 선택합니다.
1. **사용자** 창에서 **사용자 만들기** 를 선택합니다.
1. **사용자 만들기** 창에서 다음 매개 변수를 정의합니다.

   - **사용자 이름**: 사용자 이름을 입력합니다.
   - **메일**: 사용자의 메일 주소를 입력합니다.
   - **이름**: 사용자의 이름을 입력합니다.
   - **성**: 사용자의 성을 입력합니다.
   - **역할**: 사용자의 역할을 정의합니다. [역할 기반 권한](#role-based-permissions)을 참조하세요.
   - **액세스 그룹**: 온-프레미스 관리 콘솔 사용자를 만드는 경우 사용자의 액세스 그룹을 정의합니다. [전역 액세스 제어 정의](how-to-define-global-user-access-control.md)를 참조하세요.
   - **암호**: 다음과 같은 사용자 유형을 선택합니다.
     - **로컬 사용자**: 센서 또는 온-프레미스 관리 콘솔 사용자의 암호를 정의합니다. 암호는 6자 이상이어야 하며 문자와 숫자를 포함해야 합니다.
     - **Active Directory 사용자**: Active Directory 자격 증명을 사용하여 사용자가 센서 또는 관리 콘솔에 로그인하도록 할 수 있습니다. 정의된 Active Directory 그룹은 특정 권한 수준에 연결할 수 있습니다. 예를 들어 특정 Active Directory 그룹을 구성하고 그룹의 모든 사용자를 읽기 전용 사용자 유형에 할당합니다.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="사용자를 관리합니다.":::

## <a name="user-session-timeout"></a>사용자 세션 시간 제한

사용자가 특정 시간 동안 키보드 또는 마우스를 사용하지 않는 경우 세션에서 로그아웃되며 다시 로그인해야 합니다.

사용자가 30분 동안 콘솔 마우스나 키보드를 사용하여 작업하지 않은 경우 세션 로그아웃이 강제 적용됩니다.

업그레이드 시 이 기능은 기본적으로 사용되지만 사용되지 않도록 할 수도 있습니다. 또한 세션 계산 시간을 업데이트할 수 있습니다. 세션 시간은 초 단위로 정의됩니다. 정의는 센서 및 온-프레미스 관리 콘솔별로 적용됩니다.

비활성 시간 제한이 경과되면 세션 시간 제한 메시지가 콘솔에 표시됩니다.

### <a name="control-inactivity-sign-out"></a>비활성 로그아웃 제어

관리자 사용자는 비활성 로그아웃을 사용 및 사용하지 않고 비활성 임계값을 조정할 수 있습니다.

**명령에 액세스하려면**:

1. Defender for IoT 관리 자격 증명을 사용하여 센서 또는 온-프레미스 관리 콘솔용 CLI에 로그인합니다.

1. `sudo nano /var/cyberx/properties/authentication`를 입력합니다.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

기능을 사용하지 않으려면 `infinity_session_expiration = true`를 `infinity_session_expiration = false`로 변경합니다.

로그아웃 계산 기간을 업데이트하려면 `= <number>` 값을 필요한 시간으로 조정합니다.

## <a name="track-user-activity"></a>사용자 활동 추적

각 센서의 이벤트 타임라인에서 사용자 활동을 추적할 수 있습니다. 타임라인에는 이벤트 또는 영향을 받는 디바이스 및 사용자가 활동을 수행한 시간과 날짜가 표시됩니다.

**사용자 활동을 보려면**:

1. 센서에 로그인합니다.

1. 이벤트 타임라인에서 **사용자 작업** 옵션을 사용합니다.

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="사용자의 활동을 봅니다.":::

## <a name="integrate-with-active-directory-servers"></a>Active Directory 서버와 통합

Active Directory와 함께 사용할 수 있도록 센서 또는 온-프레미스 관리 콘솔을 구성합니다. 이를 통해 사용자 Active Directory Active Directory 자격 증명을 사용하여 Defender for IoT 콘솔에 액세스할 수 있습니다.

> [!Note]
> LDAP v3이 지원됩니다.

지원되는 두 가지 LDAP 기반 인증 유형은 다음과 같습니다.

- **전체 인증**: LDAP 서버에서 사용자 세부 정보를 검색합니다. 예를 들면 이름, 성, 메일, 사용자 권한이 있습니다.

- **신뢰할 수 있는 사용자**: 사용자 암호만 검색됩니다. 검색되는 다른 사용자 세부 정보는 센서에 정의된 사용자를 기반으로 합니다.

### <a name="active-directory-and-defender-for-iot-permissions"></a>Active Directory 및 Defender for IoT 권한

여기에 정의된 Active Directory 그룹을 특정 권한 수준에 연결할 수 있습니다. 예를 들어 특정 Active Directory 그룹을 구성하고 그룹의 모든 사용자에게 읽기 전용 권한을 할당합니다.

**Active Directory를 구성하려면**:

1. 왼쪽 창에서 **시스템 설정** 을 선택합니다.

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Active Directory 시스템 설정을 봅니다.":::

1. **시스템 설정** 창에서 **Active Directory** 를 선택합니다.

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Active Directory 구성을 편집합니다.":::

1. **Active Directory 구성 편집** 대화 상자에서 **Active Directory 통합 사용** > **저장** 을 선택합니다. **Active Directory 구성 편집** 대화 상자가 확장되고, 이제 Active Directory를 구성할 매개 변수를 입력할 수 있습니다.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Active Directory를 구성할 매개 변수를 입력합니다.":::

> [!NOTE]
> - LDAP 매개 변수는 Active Directory에 표시된 대로 정확하게 정의해야 합니다.
> - 모든 Active Directory 매개 변수에는 소문자만 사용합니다. Active Directory의 구성이 대문자를 사용하는 경우에도 소문자를 사용하세요.
> - 동일한 도메인에 LDAP와 LDAPS를 모두 구성할 수는 없습니다. 그러나 서로 다른 도메인에는 동시에 둘 다 사용할 수 있습니다.

1. 다음과 같이 Active Directory 서버 매개 변수를 설정합니다.

   | 서버 매개 변수 | Description |
   |--|--|
   | 도메인 컨트롤러 FQDN | FQDN(정규화된 도메인 이름)을 LDAP 서버에 표시된 대로 정확하게 설정합니다. 예를 들어 다음과 같이 입력합니다. `host1.subdomain.domain.com` |
   | 도메인 컨트롤러 포트 | LDAP가 구성된 포트를 정의합니다. |
   | 기본 도메인 | LDAP 구성에 따라 도메인 이름(예: `subdomain.domain.com`) 및 연결 형식을 설정합니다. |
   | Active Directory 그룹 | LDAP 서버의 Active Directory 구성에 정의된 그룹 이름을 입력합니다. |
   | 트러스트된 도메인 | 트러스트된 도메인을 추가하려면 트러스트된 도메인의 도메인 이름 및 연결 형식을 추가합니다. <br />사용자에서 정의된 사용자에 대해서만 트러스트된 도메인을 구성할 수 있습니다. |

#### <a name="activedirectory-groups-for-the-on-premises-management-console"></a>온-프레미스 관리 콘솔용 Active Directory 그룹

온-프레미스 관리 콘솔 사용자에 대한 Active Directory 그룹을 만드는 경우 각 Active Directory 그룹에 대한 액세스 그룹 규칙을 만들어야 합니다. Active Directory 사용자 그룹에 대한 액세스 그룹 규칙이 없는 경우 온-프레미스 관리 콘솔 Active Directory 자격 증명이 작동하지 않습니다. [전역 액세스 제어 정의](how-to-define-global-user-access-control.md)를 참조하세요.

1. **저장** 을 선택합니다.

1. 신뢰할 수 있는 서버를 추가하려면 **서버 추가** 를 선택하고 다른 서버를 구성합니다.

## <a name="change-a-users-password"></a>사용자 암호를 변경합니다.

관리자는 보안 분석가 및 읽기 전용 역할의 암호를 변경할 수 있습니다. 관리자 역할 사용자는 자신의 암호를 변경할 수 없으며, 상위 수준 역할에 연결해야 합니다. 보안 분석가 및 읽기 전용 역할은 자신 또는 다른 역할의 암호를 재설정할 수 없습니다. 보안 분석가 및 읽기 전용 역할은 더 높은 역할 수준을 가진 사용자에게 연락하여 암호를 재설정해야 합니다. CyberX 역할은 모든 사용자 역할의 암호를 변경할 수 있습니다. 지원 역할은 지원, 관리자, 보안 분석가 및 읽기 전용 사용자의 역할에 대한 암호를 변경할 수 있습니다.  

**센서용 사용자 암호를 다시 설정하려면**:

1. 역할 관리자, 지원 또는 CyberX 사용자를 사용하여 센서에 로그인합니다.

1. 왼쪽 패널에서 **사용자** 를 선택합니다.

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="왼쪽 창에서 사용자 옵션을 선택합니다.":::

1. 사용자를 찾고 **작업** 드롭다운 메뉴에서 **편집** 을 선택합니다.

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="작업 드롭다운 메뉴에서 편집을 선택합니다.":::

1. **새 암호** 및 **새 암호 확인** 필드에 새 암호를 입력합니다.

    > [!NOTE]
    > 암호는 16자 이상이어야 하며 소문자 및 대문자 알파벳 문자, 숫자 및 다음 기호 중 하나를 포함해야 합니다. #%*+,-./:=?@[]^_{}~

1. **업데이트** 를 선택합니다.

**온-프레미스 관리 콘솔에서 사용자의 암호를 다시 설정하려면**:

1. 역할 관리자, 지원 또는 CyberX 사용자를 사용하여 온-프레미스 관리 콘솔에 로그인합니다.

1. 왼쪽 패널에서 **사용자** 를 선택합니다.

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="왼쪽 패널에서 사용자의 옵션을 선택합니다.":::

1. 사용자를 찾고 편집 아이콘 :::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false":::을 선택합니다.

1. **새 암호** 및 **새 암호 확인** 필드에 새 암호를 입력합니다.

    > [!NOTE]
    > 암호는 16자 이상이어야 하며 소문자 및 대문자 알파벳 문자, 숫자 및 다음 기호 중 하나를 포함해야 합니다. #%*+,-./:=?@[]^_{}~

1. **업데이트** 를 선택합니다.

## <a name="recover-the-password-for-the-on-premises-management-console-or-the-sensor"></a>온-프레미스 관리 콘솔 또는 센서에 대한 암호를 복구합니다.

온-프레미스 관리 콘솔 또는 암호 복구 기능을 사용하여 센서에 대한 암호를 복구할 수 있습니다. CyberX 및 지원 사용자만 암호 복구 기능에 액세스할 수 있습니다.

**온-프레미스 관리 콘솔 또는 센서의 암호를 복구하려면**:

1. 온-프레미스 관리 콘솔 또는 센서의 로그인 화면에서 **암호 복구** 를 선택합니다. **암호 복구** 화면이 열립니다.

    :::image type="content" source="media/how-to-create-and-manage-users/password-recovery.png" alt-text="온-프레미스 콘솔 또는 센서의 로그인 화면에서 암호 복구를 선택합니다.":::

1. 드롭다운 메뉴에서 **CyberX** 또는 **지원** 을 선택하고 고유 식별자 코드를 복사합니다.

    :::image type="content" source="media/how-to-create-and-manage-users/password-recovery-screen.png" alt-text="드롭다운 메뉴에서 CyberX 사용자 또는 지원 사용자를 선택합니다.":::

1. Azure Portal로 이동하고 **사이트 및 센서** 를 선택합니다.  

1. 상단 도구 모음에서 **구독 필터** 아이콘 :::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false":::을 선택하고 센서가 연결된 구독을 선택합니다.

1. **추가 작업** 드롭다운 메뉴를 선택하고 **온-프레미스 콘솔 암호 복구** 를 선택합니다.

    :::image type="content" source="media/how-to-create-and-manage-users/recover-password.png" alt-text="센서를 선택하고 온-프레미스 관리 콘솔 암호 복구 옵션을 선택합니다.":::   

1. **암호 복구** 화면에서 받은 고유 식별자를 입력하고 **복구** 를 선택합니다. `password_recovery.zip` 파일이 다운로드됩니다.

    :::image type="content" source="media/how-to-create-and-manage-users/enter-identifier.png" alt-text="고유 식별자를 입력한 다음, 복구를 선택합니다.":::

    > [!NOTE]
    > 암호 복구 파일을 변경하지 마세요. 이 파일은 서명된 파일이며 변조 시 작동하지 않습니다.

1. 암호 복구 화면에서 **업로드** 를 선택합니다. **암호 복구 파일 업로드** 창이 열립니다.

1. **찾아보기** 를 선택하여 `password_recovery.zip` 파일을 찾거나 `password_recovery.zip`을 창으로 끕니다.

    > [!NOTE]
    > 파일이 잘못되었음을 나타내는 오류 메시지가 표시될 수 있습니다. 이 오류 메시지를 해결하려면 `password_recovery.zip`을 다운로드하기 전에 올바른 구독을 선택하고 다시 다운로드합니다.  

1. **다음** 을 선택하면 관리 콘솔에 사용할 수 있는 사용자, 그리고 시스템에서 생성된 암호가 표시됩니다.

## <a name="next-steps"></a>다음 단계

[센서 활성화 및 설정](how-to-activate-and-set-up-your-sensor.md)
[온-프레미스 관리 콘솔 활성화 및 설정](how-to-activate-and-set-up-your-on-premises-management-console.md)
[센서 활동 추적](how-to-track-sensor-activity.md)
