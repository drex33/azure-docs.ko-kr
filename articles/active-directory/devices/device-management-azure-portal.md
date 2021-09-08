---
title: Azure Portal을 사용하여 디바이스를 관리하는 방법 | Microsoft Docs
description: Azure Portal을 사용하여 디바이스를 관리하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 03/23/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4042ce416b2af83447af283d21c059050b9a144c
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866592"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Azure Portal을 사용하여 디바이스 ID 관리

Azure AD는 디바이스 ID 관리를 위한 중앙 위치를 제공합니다.

**모든 디바이스** 페이지에서 다음을 수행할 수 있습니다.

- 다음을 포함하여 디바이스를 식별합니다.
   - Azure AD에 조인되었거나 등록된 디바이스.
   - [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)을 사용하여 배포된 디바이스.
   - [유니버설 인쇄](/universal-print/fundamentals/universal-print-getting-started)를 사용하는 프린터
- 사용, 사용 안 함, 삭제 또는 관리와 같은 디바이스 ID 관리 작업을 수행합니다.
   - [프린터](/universal-print/fundamentals/) 및 [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) 디바이스는 Azure AD에서 제한된 관리 옵션을 포함합니다. 이러한 디바이스는 해당 관리 인터페이스에서 관리되어야 합니다.
- 디바이스 ID 설정을 구성합니다.
- Enterprise State Roaming을 사용 또는 사용하지 않도록 설정합니다.
- 디바이스 관련 감사 로그 검토
- 디바이스 다운로드(미리 보기)

[![Azure Portal의 모든 디바이스 보기](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

다음 단계를 사용하여 장치 포털에 액세스할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** > **디바이스** 로 이동합니다.

## <a name="manage-devices"></a>디바이스 관리

Azure AD에는 디바이스 관리를 위해 두 개의 위치가 있습니다.

- **Azure Portal** > **Azure Active Directory** > **디바이스**
- **Azure Portal** > **Azure Active Directory** > **사용자** > 사용자 선택 > **디바이스**

두 옵션 모두 관리자가 다음을 수행할 수 있습니다.

- 디바이스를 검색합니다.
- 다음을 포함하여 디바이스 세부 정보를 참조하세요.
    - 디바이스 이름
    - 디바이스 ID
    - OS 및 버전
    - 조인 유형
    - 소유자
    - 모바일 디바이스 관리 및 규정 준수
    - BitLocker 복구 키
- 사용, 사용 안 함, 삭제 또는 관리와 같은 디바이스 ID 관리 작업을 수행합니다.
   - [프린터](/universal-print/fundamentals/) 및 [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) 디바이스는 Azure AD에서 제한된 관리 옵션을 포함합니다. 이러한 디바이스는 해당 관리 인터페이스에서 관리되어야 합니다.

> [!TIP]
> - 하이브리드 Azure AD 조인 Windows 10 디바이스에는 소유자가 없습니다. 따라서 소유자로 디바이스를 찾으려는 경우 찾지 못하면 디바이스 ID로 검색합니다.
>
> - REGISTERED 열 아래에 “대기 중” 상태로 디바이스가 “하이브리드 Azure AD에 조인”되었다고 표시되면 디바이스가 Azure ADConnect에서 동기화되었고 클라이언트에서 등록을 마치기 위해 대기 중임을 나타냅니다. [하이브리드 Azure AD 조인 구현 계획](hybrid-azuread-join-plan.md) 방법을 자세히 알아보세요. [디바이스 질문과 대답](faq.yml)에서 추가 정보를 확인할 수 있습니다.
>
> - 일부 iOS 디바이스의 경우 아포스트로피를 포함하는 디바이스 이름이 아포스트로피처럼 보이는 다른 문자를 사용할 수 있습니다. 이러한 디바이스를 검색하는 것은 약간 까다로울 수 있습니다. 따라서 검색 결과가 올바르게 표시되지 않으면 검색 문자열에 일치하는 아포스트로피 문자가 포함되어 있는지 확인하세요.

### <a name="manage-an-intune-device"></a>Intune 디바이스 관리

Intune 관리자의 경우 MDM이 **Microsoft Intune** 으로 표시된 디바이스를 관리할 수 있습니다. 디바이스가 Microsoft Intune에 등록되지 안았으면 “관리” 옵션이 회색으로 표시됩니다.

### <a name="enable-or-disable-an-azure-ad-device"></a>Azure AD 디바이스 사용 또는 사용 안 함

디바이스를 사용 또는 사용하지 않도록 설정하는 데에는 두 가지 옵션이 있습니다.

- 하나 이상의 디바이스를 선택한 후 **모든 디바이스** 페이지에서 도구 모음을 사용합니다.
- 특정 디바이스로 드릴다운 한 후 도구 모음을 사용합니다.

> [!IMPORTANT]
> - 디바이스를 사용 또는 사용하지 않도록 설정하려면 Azure AD에서 전역 관리자, Intune 관리자 또는 클라우드 디바이스 관리자여야 합니다. 
> - 디바이스를 사용하지 않도록 설정하면 디바이스가 Azure AD에 성공적으로 인증되지 않습니다. 따라서 디바이스 기반 조건부 액세스 또는 비즈니스용 Windows Hello 자격 증명을 사용하여 보호되는 Azure AD 리소스에 디바이스가 액세스하지 못하게 됩니다.
> - 디바이스를 사용하지 않도록 설정하면 디바이스에서 PRT(기본 새로 고침 토큰) 및 모든 RT(새로 고침 토큰)이 모두 철회됩니다.
> - 프린터는 Azure AD에서 사용 또는 사용하지 않도록 설정될 수 없습니다.

### <a name="delete-an-azure-ad-device"></a>Azure AD 디바이스 삭제

디바이스를 삭제하려면 두 가지 옵션이 있습니다.

- 하나 이상의 디바이스를 선택한 후 **모든 디바이스** 페이지에서 도구 모음을 사용합니다.
- 특정 디바이스로 드릴다운 한 후 도구 모음을 사용합니다.

> [!IMPORTANT]
> - 디바이스를 삭제하려면 Azure AD에서 클라우드 디바이스 관리자, Intune 관리자 또는 전역 관리자 역할을 할당 받아야 합니다.
> - 프린터 및 Windows Autopilot 디바이스는 Azure AD에서 삭제될 수 없습니다.
> - 디바이스 삭제:
>    - 디바이스에서 Azure AD 리소스에 액세스할 수 없게 됩니다.
>    - 디바이스에 연결되어 있는 모든 세부 정보(예: Windows 디바이스에 대한 BitLocker 키)를 제거합니다.  
>    - 복구할 수 없으며, 반드시 필요한 경우가 아니면 권장되지 않는 작업을 나타냅니다.

디바이스를 다른 관리 기관(예: Microsoft Intune)에서 관리하는 경우 Azure AD에서 디바이스를 삭제하기 전에 디바이스를 초기화/사용 중지했는지 확인합니다. 디바이스를 삭제하기 전 [부실 디바이스 관리](manage-stale-devices.md) 방법을 검토합니다.

### <a name="view-or-copy-device-id"></a>디바이스 ID 확인 또는 복사

디바이스 ID를 사용하여 디바이스의 디바이스 ID 세부 정보를 확인하거나 문제 해결 동안 PowerShell을 사용할 수 있습니다. 복사 옵션에 액세스하려면 디바이스를 클릭합니다.

![디바이스 ID 보기](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>BitLocker 키 확인 또는 복사

BitLocker 키를 확인 및 복사하여 사용자가 암호화된 드라이브를 복구하도록 허용할 수 있습니다. 이러한 키는 암호화되고 해당 키가 Azure AD에 저장된 Windows 디바이스에 대해서만 사용할 수 있습니다. **복구 키 표시** 를 선택하여 디바이스 세부 정보에 액세스할 때 이러한 키를 찾을 수 있습니다. **복구 키 표시** 를 선택하면 `KeyManagement` 범주에서 찾을 수 있는 감사 로그가 생성됩니다.

![BitLocker 키 보기](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

BitLocker 키를 보거나 복사하려면, 디바이스의 소유자 또는 다음 역할 중 하나 이상이 할당된 사용자여야 합니다.

- 클라우드 디바이스 관리자
- 전역 관리자
- 기술 지원팀 관리자
- Intune 서비스 관리자
- 보안 관리자
- 보안 Reader

### <a name="device-list-filtering-preview"></a>디바이스 목록 필터링(미리 보기)

이전에는 작업 및 사용하도록 설정된 상태 기준으로만 디바이스 목록을 필터링할 수 있었습니다. 이제는 이 미리 보기에서 디바이스에 대한 다음 속성을 기준으로 디바이스 목록을 필터링할 수 있습니다.

- 사용하도록 설정된 상태
- 준수 상태
- 조인 유형(Azure AD에 조인됨, 하이브리드 Azure AD에 조인됨, Azure AD에 등록됨)
- 활동 타임스탬프
- OS
- 디바이스 유형(프린터, 보안 VM, 공유 디바이스, 등록된 디바이스)

**모든 디바이스** 보기에서 미리 보기 필터링 기능을 사용하도록 설정하려면 다음을 수행합니다.

![필터링 미리 보기 기능 사용](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** > **디바이스** 로 이동합니다.
1. **새로운 디바이스 필터링 향상 기능을 사용해보세요. 미리 보기를 사용하려면 클릭하세요.** 배너를 선택합니다.

이제 **모든 디바이스** 보기에 **필터를 추가** 할 수 있습니다.

### <a name="download-devices-preview"></a>디바이스 다운로드(미리 보기)

클라우드 디바이스 관리자, Intune 관리자 및 전역 관리자가 **디바이스 다운로드(미리 보기)** 옵션을 사용하여 적용된 필터를 기준으로 디바이스의 CSV 파일을 내보낼 수 있습니다. 필터를 목록에 적용하지 않으면 모든 디바이스를 내보냅니다. 내보내기는 다음을 기준으로 최대 1시간 동안 실행될 수 있습니다. 

내보내기 목록에는 다음 디바이스 ID 특성이 포함됩니다.

`accountEnabled, approximateLastLogonTimeStamp, deviceOSType, deviceOSVersion, deviceTrustType, dirSyncEnabled, displayName, isCompliant, isManaged, lastDirSyncTime, objectId, profileType, registeredOwners, systemLabels, registrationTime, mdmDisplayName`

## <a name="configure-device-settings"></a>디바이스 설정 구성

Azure AD 포털을 사용하여 디바이스 ID를 관리하려면 해당 디바이스가 Azure AD에 [등록 또는 조인](overview.md)된 상태여야 합니다. 관리자는 다음 디바이스 설정을 구성하여 디바이스 등록 및 조인 프로세스를 제어할 수 있습니다.

Azure Portal에서 디바이스 설정을 보고 관리하려면 다음 역할 중 하나를 할당 받아야 합니다.

- 전역 관리자
- 클라우드 디바이스 관리자
- 전역 독자
- 디렉터리 읽기 권한자

![Azure AD 관련 디바이스 설정](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **사용자가 디바이스를 Azure AD에 조인할 수 있음** - 이 설정을 사용하면 디바이스를 Azure AD에 조인된 디바이스로 등록할 수 있는 사용자를 선택할 수 있습니다. 기본값은 **All** 입니다.

> [!NOTE]
> **사용자가 디바이스를 Azure AD에 조인할 수 있음** 설정은 Windows 10의 Azure AD 조인에만 적용할 수 있습니다. 이 설정은 하이브리드 Azure AD에 조인된 디바이스, [Azure에서 Azure AD에 조인된 VM](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure), [Windows Autopilot 자체 배포 모드](/mem/autopilot/self-deploying)를 사용하여 Azure AD에 조인된 디바이스에 적용되지 않습니다. 이러한 메서드는 사용자가 없는 컨텍스트에서 작동하기 때문입니다.

- **Azure AD 조인 디바이스의 추가 로컬 관리자** - 디바이스에서 로컬 관리자 권한이 부여된 사용자를 선택할 수 있습니다. 이러한 사용자는 Azure AD의 *디바이스 관리자* 역할에 추가됩니다. Azure AD의 전역 관리자 및 디바이스 소유자에게는 기본적으로 로컬 관리자 권한이 부여됩니다. 이 옵션은 Azure AD Premium 또는 EMS(Enterprise Mobility Suite) 등의 제품을 통해 사용할 수 있는 프리미엄 버전 기능입니다.
- **사용자가 Azure AD에 디바이스를 등록할 수 있음** - Azure AD에 Windows 10 개인용, iOS, Android 및 macOS 디바이스가 등록되도록 하려면 이 설정을 구성해야 합니다. **없음** 을 선택하면 Azure AD에 디바이스 등록이 허용되지 않습니다. Microsoft 365용 Microsoft Intune 또는 MDM(모바일 디바이스 관리)에 등록하려면 먼저 디바이스를 등록해야 합니다. 이러한 서비스 중 하나를 구성한 경우 **모두** 가 선택되고 **없음** 은 사용할 수 없습니다.
- **Azure AD에 조인하거나 Azure AD에 등록할 디바이스에 다단계 인증 필요** - 디바이스를 Azure AD에 조인하거나 등록하기 위해 사용자가 추가 인증을 제공해야 하는지 여부를 선택할 수 있습니다. 기본값은 **No** 입니다. 디바이스를 등록하거나 조인할 때 다단계 인증을 사용하는 것이 좋습니다. 이 서비스에 대해 Multi-Factor Authentication을 사용하도록 설정하기 전에 디바이스를 등록하는 사용자에 대해 Multi-Factor Authentication을 구성해야 합니다. 여러 가지 Azure AD 다단계 인증 서비스에 대한 자세한 내용은 [Azure AD 다단계 인증 시작](../authentication/concept-mfa-howitworks.md)을 참조하세요. 

> [!NOTE]
> **Azure AD에 조인하거나 Azure AD에 등록할 디바이스에 다단계 인증 필요** 설정은 Azure AD에 조인하거나(몇 가지 예외 포함) Azure AD에 등록할 디바이스에 적용됩니다. 이러한 설정은 하이브리드 Azure AD에 조인된 디바이스, [Azure의 Azure AD에 조인된 VM](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) 및 [Windows Autopilot 자체 배포 모드](/mem/autopilot/self-deploying)를 사용하여 Azure AD에 조인된 디바이스에 적용되지 않습니다.

> [!IMPORTANT]
> - 디바이스 조인 또는 등록에 다단계 인증을 적용하기 위해서는 조건부 액세스에서 ["디바이스 등록 또는 조인" 사용자 작업](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions)을 수행하는 것이 좋습니다. 
> - 다단계 인증을 요구하도록 조건부 액세스 정책을 사용할 경우 이 설정을 **아니요** 로 설정해야 합니다. 

- **최대 디바이스 수** - 이 설정을 사용하면 사용자가 Azure AD에 설정할 수 있는 Azure AD에 조인된 또는 Azure AD에 등록된 디바이스의 최대 개수를 선택할 수 있습니다. 사용자가 이 할당량에 도달하는 경우 기존 디바이스 중 하나 이상이 제거될 때까지 디바이스를 더 추가할 수 없습니다. 기본값은 **50** 입니다. 값을 최대 100까지 늘릴 수 있으며, 값을 100 위로 입력해도 Azure AD에서 값이 100으로 설정됩니다. 또한 무제한 값을 사용하여 기존 할당량 제한 대신 제한을 적용하지 않을 수 있습니다.

> [!NOTE]
> **최대 디바이스 수** 설정은 Azure AD에 조인된 또는 Azure AD에 등록된 디바이스에 적용됩니다. 이 설정은 하이브리드 Azure AD에 조인된 디바이스에 적용되지 않습니다.

- [엔터프라이즈 상태 로밍](enterprise-state-roaming-overview.md)

## <a name="audit-logs"></a>감사 로그

디바이스 활동은 활동 로그를 통해 사용할 수 있습니다. 이러한 로그에는 디바이스 등록 서비스 및 사용자에 의해 트리거된 활동이 포함됩니다.

- 디바이스 만들기 및 디바이스에 소유자/사용자 추가
- 디바이스 설정 변경
- 디바이스 삭제 또는 업데이트 등의 디바이스 작업

감사 데이터에 대한 진입점은 **디바이스** 페이지의 **작업** 섹션에 있는 **감사 로그** 입니다.

감사 로그에는 다음 항목을 보여주는 기본 목록 보기가 포함됩니다.

- 발생 날짜와 시간
- 대상
- 작업의 초기자/행위자(누가)
- 작업(무엇)

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="4개의 감사 로그에 대해 날짜, 대상, 작업자 및 활동을 나열하는 디바이스 페이지의 활동 섹션에 있는 표를 보여주는 스크린샷입니다." border="false":::

도구 모음에서 **열** 을 클릭하여 목록 보기를 사용자 지정할 수 있습니다.

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="디바이스 페이지의 도구 모음을 보여주는 스크린샷입니다. 열 항목이 강조 표시되어 있습니다." border="false":::

보고된 데이터를 자신에게 적합한 수준으로 좁히려면 다음 필드를 사용하여 감사 데이터를 필터링할 수 있습니다.

- 범주
- 활동 리소스 종류
- 활동
- 날짜 범위
- 대상
- 초기자(작업자)

필터 이외의 방법으로도 특정 항목을 검색할 수 있습니다.

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="범주, 활동 리소스 유형, 활동, 날짜 범위, 대상 및 작업자 필드와 검색 필드가 있는 감사 데이터 필터 컨트롤의 스크린샷입니다." border="false":::

## <a name="next-steps"></a>다음 단계

[Azure AD에서 부실 디바이스 관리 방법](manage-stale-devices.md)

[엔터프라이즈 상태 로밍](enterprise-state-roaming-overview.md)
