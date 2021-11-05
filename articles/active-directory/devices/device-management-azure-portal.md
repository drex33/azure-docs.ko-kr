---
title: Azure Portal을 사용하여 Azure AD에서 디바이스 관리
description: 이 문서에서는 Azure Portal을 사용하여 디바이스 ID를 관리하고 관련 이벤트 정보를 모니터링하는 방법을 설명합니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 10/14/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9cdb24ac332530a8294cd6a39b5fe58ab2727ed
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049941"
---
# <a name="manage-device-identities-by-using-the-azure-portal"></a>Azure Portal을 사용하여 디바이스 ID 관리

Azure AD(Azure Active Directory)는 디바이스 ID를 관리하고 관련 이벤트 정보를 모니터링하는 중앙 위치를 제공합니다.

[![Azure Portal의 디바이스 개요를 보여 주는 스크린샷.](./media/device-management-azure-portal/devices-azure-portal.png)](./media/device-management-azure-portal/devices-azure-portal.png#lightbox)

다음 단계를 완료하여 디바이스 개요에 액세스할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** > **디바이스** 로 이동합니다.

디바이스 개요에서 총 디바이스, 부실 디바이스, 비준수 디바이스 및 관리되지 않는 디바이스의 수를 볼 수 있습니다. Intune, 조건부 액세스, BitLocker 키 및 기본 모니터링에 대한 링크도 찾을 수 있습니다. 

개요 페이지의 디바이스 수는 실시간으로 업데이트되지 않습니다. 변경 내용은 몇 시간마다 반영되어야 합니다.

여기에서 **모든 디바이스** 로 이동하여 다음을 수행할 수 있습니다.

- 다음을 포함하여 디바이스를 식별합니다.
   - Azure AD에 조인되었거나 등록된 디바이스.
   - [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)을 통해 배포된 디바이스.
   - [유니버설 인쇄](/universal-print/fundamentals/universal-print-getting-started)를 사용하는 프린터.
- 사용, 사용 안 함, 삭제 및 관리와 같은 디바이스 ID 관리 작업을 완료합니다.
   - [프린터](/universal-print/fundamentals/) 및 [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)에 대한 관리 옵션은 Azure AD에서 제한됩니다. 이러한 디바이스는 해당 관리 인터페이스에서 관리해야 합니다.
- 디바이스 ID 설정을 구성합니다.
- Enterprise State Roaming을 사용하거나 사용하지 않습니다.
- 디바이스 관련 감사 로그를 검토합니다.
- 디바이스를 다운로드합니다(미리 보기).

[![Azure Portal의 모든 디바이스 보기를 보여 주는 스크린샷.](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

> [!TIP]
> - 하이브리드 Azure AD 조인된 Windows 10 디바이스에는 소유자가 없습니다. 소유자별로 디바이스를 찾고 있는데 찾지 못한 경우 디바이스 ID로 검색합니다.
>
> - **등록됨** 열에 **보류 중** 상태로 **하이브리드 Azure AD 조인된** 디바이스가 표시되면 디바이스가 Azure AD 연결에서 동기화되어 클라이언트의 등록 완료를 기다립니다. [하이브리드 Azure AD 조인 구현을 계획하는 방법](hybrid-azuread-join-plan.md)을 참조하세요. 자세한 내용은 [디바이스 관리 FAQ](faq.yml)를 참조하세요.
>
> - 일부 iOS 디바이스의 경우 아포스트로피가 포함된 디바이스 이름은 아포스트로피처럼 보이는 다른 문자를 사용할 수 있습니다. 따라서 이러한 디바이스를 찾는 것은 약간 까다롭습니다. 올바른 검색 결과가 표시되지 않으면 검색 문자열에 일치하는 아포스트로피 문자가 포함되어 있는지 확인합니다.

## <a name="manage-an-intune-device"></a>Intune 디바이스 관리

Intune에서 디바이스를 관리할 수 있는 권한이 있는 경우 모바일 디바이스 관리가 **Microsoft Intune** 으로 나열되는 디바이스를 관리할 수 있습니다. 디바이스가 Microsoft Intune에 등록되어 있지 않으면 **관리** 옵션을 사용할 수 없습니다.

## <a name="enable-or-disable-an-azure-ad-device"></a>Azure AD 디바이스 사용 또는 사용 안 함

디바이스를 사용하거나 사용하지 않도록 설정하는 두 가지 방법이 있습니다.

- 하나 이상의 디바이스를 선택한 후 **모든 디바이스** 페이지의 도구 모음
- 특정 디바이스에 대해 드릴다운한 후의 도구 모음.

> [!IMPORTANT]
> - 디바이스를 사용 또는 사용하지 않도록 설정하려면 Azure AD에서 전역 관리자, Intune 관리자 또는 클라우드 디바이스 관리자여야 합니다. 
> - 디바이스를 사용하지 않도록 설정하면 Azure AD를 통해 인증할 수 없습니다. 이렇게 하면 디바이스 기반 조건부 액세스로 보호되는 Azure AD 리소스에 액세스하고 비즈니스용 Windows Hello 자격 증명을 사용할 수 없습니다.
> - 디바이스를 사용하지 않도록 설정하면 기본 새로 고침 토큰(PRT)과 디바이스의 모든 새로 고침 토큰이 취소됩니다.
> - Azure AD에서 프린터를 사용하거나 사용하지 않도록 설정할 수 없습니다.

## <a name="delete-an-azure-ad-device"></a>Azure AD 디바이스 삭제

디바이스를 삭제하는 방법에는 두 가지가 있습니다.

- 하나 이상의 디바이스를 선택한 후 **모든 디바이스** 페이지의 도구 모음
- 특정 디바이스에 대해 드릴다운한 후의 도구 모음.

> [!IMPORTANT]
> - 디바이스를 삭제하려면 Azure AD의 클라우드 디바이스 관리자, Intune 관리자 또는 전역 관리자여야 합니다.
> - 프린터 및 Windows Autopilot 디바이스는 Azure AD에서 삭제할 수 없습니다.
> - 디바이스 삭제:
>    - Azure AD 리소스에 액세스하지 못하도록 합니다.
>    - 디바이스에 연결된 모든 세부 정보를 제거합니다. 예를 들어 Windows 디바이스용 BitLocker 키입니다.  
>    - 복구할 수 없는 작업입니다. 꼭 필요한 경우가 아니면 권장하지 않습니다.

Microsoft Intune과 같은 다른 관리 기관에서 디바이스를 관리하는 경우 삭제하기 전에 디바이스를 초기화하거나 사용 중지해야 합니다. 디바이스를 삭제하기 전에 [부실 디바이스를 관리하는 방법](manage-stale-devices.md)을 참조하세요.

## <a name="view-or-copy-a-device-id"></a>디바이스 ID 보기 또는 복사

디바이스 ID를 사용하여 디바이스에서 디바이스 ID 세부 정보를 확인하거나 PowerShell을 통해 문제를 해결할 수 있습니다. 복사 옵션에 액세스하려면 디바이스를 선택합니다.

![디바이스 ID와 복사 단추를 보여 주는 스크린샷.](./media/device-management-azure-portal/35.png)
  
## <a name="view-or-copy-bitlocker-keys"></a>BitLocker 키 확인 또는 복사

BitLocker 키를 보고 복사하면 사용자가 암호화된 드라이브를 복구할 수 있습니다. 이러한 키는 암호화되고 해당 키를 Azure AD에 저장하는 Windows 디바이스에만 사용할 수 있습니다. **복구 키 표시** 를 선택하여 디바이스 세부 정보를 볼 때 이러한 키를 찾을 수 있습니다. **복구 키 표시** 를 선택하면 `KeyManagement` 범주에서 찾을 수 있는 감사 로그가 생성됩니다.

![BitLocker 키를 보는 방법을 보여 주는 스크린샷.](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

BitLocker 키를 보거나 복사하려면 디바이스 소유자이거나 다음 역할 중 하나가 있어야 합니다.

- 클라우드 디바이스 관리자
- 전역 관리자
- 기술 지원팀 관리자
- Intune 서비스 관리자
- 보안 관리자
- 보안 Reader

## <a name="device-list-filtering-preview"></a>디바이스 목록 필터링(미리 보기)

이전에는 작업 및 사용하도록 설정 상태로만 디바이스 목록을 필터링할 수 있었습니다. 이 미리 보기에서 다음 디바이스 특성으로 디바이스 목록을 필터링할 수 있습니다.

- 사용하도록 설정된 상태
- 준수 상태
- 조인 유형(Azure AD에 조인됨, 하이브리드 Azure AD에 조인됨, Azure AD에 등록됨)
- 활동 타임스탬프
- OS
- 디바이스 유형(프린터, 보안 VM, 공유 디바이스, 등록 디바이스)

**모든 디바이스** 보기에서 미리 보기 필터링 기능을 사용하도록 설정하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** > **디바이스** 로 이동합니다.
1. **새로운 디바이스 필터링 개선 기능을 사용해보세요. 미리 보기를 사용하려면 클릭하세요.** 배너를 선택합니다.

   ![필터링 미리 보기 기능 사용](./media/device-management-azure-portal/device-filter-preview-enable.png)

이제 **모든 디바이스** 보기에 필터를 추가할 수 있습니다.

## <a name="download-devices-preview"></a>디바이스 다운로드(미리 보기)

클라우드 디바이스 관리자, Intune 관리자 및 전역 관리자는 **디바이스 다운로드(미리 보기)** 옵션을 사용하여 디바이스를 나열하는 CSV 파일을 내보낼 수 있습니다. 필터를 적용하여 나열할 디바이스를 결정할 수 있습니다. 필터를 적용하지 않으면 모든 디바이스가 나열됩니다. 내보내기 작업은 선택 사항에 따라 한 시간 동안 실행될 수 있습니다.

내보낸 목록에는 다음과 같은 디바이스 ID 특성이 포함됩니다.

`accountEnabled, approximateLastLogonTimeStamp, deviceOSType, deviceOSVersion, deviceTrustType, dirSyncEnabled, displayName, isCompliant, isManaged, lastDirSyncTime, objectId, profileType, registeredOwners, systemLabels, registrationTime, mdmDisplayName`

## <a name="configure-device-settings"></a>디바이스 설정 구성

Azure Portal을 사용하여 디바이스 ID를 관리하려면 디바이스를 Azure AD에 [등록하거나 조인](overview.md)해야 합니다. 관리자는 다음 디바이스 설정을 구성하여 디바이스 등록 및 조인 프로세스를 제어할 수 있습니다.

Azure Portal에서 디바이스 설정을 보고 관리하려면 다음 역할 중 하나를 할당 받아야 합니다.

- 전역 관리자
- 클라우드 디바이스 관리자
- 전역 Reader
- 디렉터리 읽기 권한자

![Azure AD와 관련된 디바이스 설정을 보여 주는 스크린샷.](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **사용자가 디바이스를 Azure AD에 조인할 수 있음**: 이 설정을 사용하면 디바이스를 Azure AD에 조인된 디바이스로 등록할 수 있는 사용자를 선택할 수 있습니다. 기본값은 **All** 입니다.

   > [!NOTE]
   > **사용자가 디바이스를 Azure AD에 조인할 수 있음** 설정은 Windows 10의 Azure AD 조인에만 적용됩니다. 이 설정은 하이브리드 Azure AD 조인된 디바이스, [Azure의 Azure AD 조인된 VM](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) 또는 [Windows Autopilot 자체 배포 모드](/mem/autopilot/self-deploying)를 사용하는 Azure AD 조인된 디바이스에는 적용되지 않습니다. 사용자 없는 컨텍스트.

- **Azure AD 조인된 디바이스의 추가 로컬 관리자**: 이 설정을 사용하면 디바이스에 대한 로컬 관리자 권한이 부여된 사용자를 선택할 수 있습니다. 이러한 사용자는 Azure AD의 디바이스 관리자 역할에 추가됩니다. Azure AD의 전역 관리자 및 디바이스 소유자에게는 기본적으로 로컬 관리자 권한이 부여됩니다. 이 옵션은 Azure AD Premium 및 Enterprise Mobility + Security와 같은 제품을 통해 사용할 수 있는 프리미엄 버전 기능입니다.
- **사용자가 디바이스를 Azure AD에 등록할 수 있음**: 사용자가 Windows 10 Personal, iOS, Android 및 macOS 디바이스를 Azure AD에 등록할 수 있도록 이 설정을 구성해야 합니다. **없음** 을 선택하면 디바이스를 Azure AD에 등록할 수 없습니다. Microsoft 365용 Microsoft Intune 또는 모바일 디바이스 관리에 등록하려면 먼저 디바이스를 등록해야 합니다. 이러한 서비스 중 하나를 구성한 경우 **모두** 가 선택되고 **없음** 은 사용할 수 없습니다.
- **Azure AD에 디바이스를 등록하거나 조인하려면 Multi-Factor Authentication 필요**: 이 설정을 사용하면 사용자가 디바이스를 Azure AD에 조인하거나 등록하기 위해 다른 인증 요소를 제공해야 하는지 여부를 지정할 수 있습니다. 기본값은 **No** 입니다. 디바이스를 등록하거나 조인할 때 다단계 인증을 요구하는 것이 좋습니다. 이 서비스에 대해 다단계 인증을 사용하도록 설정하기 전에 디바이스를 등록하는 사용자에 대해 다단계 인증이 구성되어 있는지 확인해야 합니다. Azure AD Multi-Factor Authentication 서비스에 대한 자세한 내용은 [Azure AD 다단계 인증 시작](../authentication/concept-mfa-howitworks.md)을 참조하세요. 

   > [!NOTE]
   > **Azure AD에 디바이스를 등록하거나 조인하려면 Multi-Factor Authentication 필요** 설정은 Azure AD 조인(일부 예외 있음)되거나 Azure AD에 등록된 디바이스에 적용됩니다. 이 설정은 하이브리드 Azure AD 조인된 디바이스, [Azure의 Azure AD 조인된 VM](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) 또는 [Windows Autopilot 자체 배포 모드](/mem/autopilot/self-deploying)를 사용하는 Azure AD 조인된 디바이스에는 적용되지 않습니다.

   > [!IMPORTANT]
   > - 조건부 액세스에서 [디바이스 사용자 등록 또는 조인](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) 작업을 사용하여 디바이스 조인 또는 등록 시 다단계 인증을 적용하는 것이 좋습니다. 
   > - 조건부 액세스 정책을 사용하여 다단계 인증을 요구하는 경우 이 설정을 **아니요** 로 구성해야 합니다. 

- **최대 디바이스 수**: 이 설정을 사용하면 사용자가 Azure AD에 설정할 수 있는 Azure AD에 조인된 또는 Azure AD에 등록된 디바이스의 최대 개수를 선택할 수 있습니다. 사용자가 이 제한에 도달하면 하나 이상의 기존 디바이스가 제거될 때까지 디바이스를 더 추가할 수 없습니다. 기본값은 **50** 입니다. 값을 최대 100까지 늘릴 수 있습니다. 100보다 큰 값을 입력하면 Azure AD에서 100으로 설정합니다. 또한 **무제한** 을 사용하여 기존 할당량 한도 이외의 제한을 적용하지 않을 수 있습니다.

   > [!NOTE]
   > **최대 디바이스 수** 설정은 Azure AD에 조인된 또는 Azure AD에 등록된 디바이스에 적용됩니다. 이 설정은 하이브리드 Azure AD 조인 디바이스에는 적용되지 않습니다.

- **Enterprise State Roaming**: 이 설정에 대한 자세한 내용은 [개요 문서](enterprise-state-roaming-overview.md)를 참조하세요.

## <a name="audit-logs"></a>감사 로그

디바이스 작업은 활동 로그에서 볼 수 있습니다. 이러한 로그에는 디바이스 등록 서비스 및 사용자에 의해 트리거된 활동이 포함됩니다.

- 디바이스 만들기 및 디바이스에 소유자/사용자 추가
- 디바이스 설정 변경
- 디바이스 삭제 또는 업데이트와 같은 디바이스 작업

감사 데이터에 대한 진입점은 **디바이스** 페이지의 **작업** 섹션에 있는 **감사 로그** 입니다.

감사 로그에는 다음 항목을 보여주는 기본 목록 보기가 포함됩니다.

- 발생 날짜와 시간.
- 대상.
- 작업의 초기자/작업자.
- 작업입니다.

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="디바이스 페이지의 작업 섹션에 있는 표를 보여 주는 스크린샷. 이 표는 4개의 감사 로그에 대한 날짜, 대상, 작업자 및 작업을 보여 줍니다." border="false":::

도구 모음에서 **열** 을 선택하여 목록 보기를 사용자 지정할 수 있습니다.

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="디바이스 페이지의 도구 모음을 보여 주는 스크린샷." border="false":::

보고된 데이터를 적합한 수준으로 줄이려면 다음 필드를 사용하여 필터링할 수 있습니다.

- **범주**
- **활동 리소스 종류**
- **활동**
- **날짜 범위**
- **대상**
- **초기자(작업자)** :

특정 항목을 검색할 수도 있습니다.

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="감사 데이터 필터링 제어를 보여 주는 스크린샷." border="false":::

## <a name="next-steps"></a>다음 단계

- [Azure AD에서 부실 디바이스 관리 방법](manage-stale-devices.md)
- [보류 중인 디바이스 상태 문제 해결](/troubleshoot/azure/active-directory/pending-devices)
