---
title: 디바이스 ID 및 데스크톱 가상화 - Azure Active Directory
description: VDI와 Azure AD 디바이스 ID를 함께 사용하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca70009eea6f8988fadd8f09a0460911fac299de
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630322"
---
# <a name="device-identity-and-desktop-virtualization"></a>디바이스 ID 및 데스크톱 가상화

관리자는 일반적으로 조직에서 Windows 운영 체제를 호스팅하는 VDI(가상 데스크톱 인프라) 플랫폼을 배포합니다. 관리자는 다음 목적을 위해 VDI를 배포합니다.

- 관리 간소화.
- 리소스 통합 및 중앙 집중화를 통한 비용 절감.
- 언제 어디서나 모든 디바이스에서 가상 데스크톱에 액세스할 수 있도록 최종 사용자에게 이동성 및 자유 제공.

가상 데스크톱에는 다음과 같은 두 가지 기본 형식이 있습니다.

- 영구적
- 비영구적

영구 버전은 각 사용자 또는 사용자 풀에 고유한 데스크톱 이미지를 사용합니다. 고유한 데스크톱은 나중에 사용할 수 있도록 사용자 지정하고 저장할 수 있습니다. 

비영구 버전은 사용자가 필요에 따라 액세스할 수 있는 데스크톱 컬렉션을 사용합니다. 비영구 데스크톱은 Windows 현재<sup>1</sup>(가상 머신이 종료/다시 시작/OS 초기화 프로세스를 통과할 때 발생) 및 Windows 하위 수준<sup>2</sup>(사용자가 로그아웃할 때 발생)일 때 원래 상태로 되돌아갑니다.

원격 작업이 계속해서 뉴노멀로 자리 잡음에 따라 비영구 VDI 배포가 증가했습니다. 고객이 비영구 VDI를 배포하는 경우 디바이스 수명 주기 관리를 위한 적절한 전략 없이 빈번한 디바이스 등록으로 인해 발생하는 부실 디바이스를 관리하는 것이 중요합니다.

> [!IMPORTANT]
> 부실 디바이스를 관리하지 못하면 테넌트 할당량을 초과할 때 테넌트 할당량 사용에 대한 압박이 증가하고 잠재적 서비스 중단 위험이 발생할 수 있습니다. 해당 상황을 방지하려면 비영구 VDI 환경을 배포할 때 아래에 설명된 참고 자료를 따라야 합니다.

이 문서에서는 Microsoft의 관리자를 위한 디바이스 ID 및 VDI 지원 관련 참고 자료를 설명합니다. 디바이스 ID에 대한 자세한 내용은 [디바이스 ID란?](overview.md)을 참조하세요.

## <a name="supported-scenarios"></a>지원되는 시나리오

VDI 환경을 위한 Azure AD에서 디바이스 ID를 구성하기 전에 지원되는 시나리오를 숙지합니다. 다음 표에서는 지원되는 프로비저닝 시나리오를 보여 줍니다. 이 컨텍스트에서 프로비저닝하는 것은 관리자가 최종 사용자 개입 없이도 디바이스 ID를 대규모로 구성할 수 있음을 의미합니다.

| 디바이스 ID 형식 | ID 인프라 | Windows 디바이스 | VDI 플랫폼 버전 | 지원 여부 |
| --- | --- | --- | --- | --- |
| 하이브리드 Azure AD 가입 | 페더레이션된<sup>3</sup> | Windows 현재 및 Windows 하위 수준 | 영구적 | 예 |
|   |   | Windows 현재 | 비영구적 | 예<sup>5</sup> |
|   |   | Windows 하위 수준 | 비영구적 | 예<sup>6</sup> |
|   | 관리형<sup>4</sup> | Windows 현재 및 Windows 하위 수준 | 영구적 | 예 |
|   |   | Windows 현재 | 비영구적 | 아니요 |
|   |   | Windows 하위 수준 | 비영구적 | 예<sup>6</sup> |
| Azure AD 가입 | 페더레이션 | Windows 현재 | 영구적 | Limited<sup>7</sup> |
|   |   |   | 비영구적 | 아니요 |
|   | 관리 | Windows 현재 | 영구적 | Limited<sup>7</sup> |
|   |   |   | 비영구적 | 아니요 |
| Azure AD 등록됨 | 페더레이션된/관리형 | Windows 현재/Windows 하위 수준 | 영구/비영구 | 해당 사항 없음 |

<sup>1</sup> **Windows 현재** 디바이스는 Windows 10, Windows Server 2016 v1803 이상, Windows Server 2019를 의미합니다.

<sup>2</sup> **Windows 하위 수준** 디바이스는 Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2를 나타냅니다. Windows 7에 관한 지원 정보는[Windows 7 지원 종료](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)를 참조하세요. Windows Server 2008 R2에 관한 지원 정보는 [Windows Server 2008 지원 종료 준비](https://www.microsoft.com/cloud-platform/windows-server-2008)를 참조하세요.

<sup>3</sup> **페더레이션** ID 인프라 환경은 AD FS 또는 기타 타사 IDP와 같은 ID 공급자를 사용하는 환경을 나타냅니다.

<sup>4</sup> **관리형** ID 인프라 환경은 [원활한 Single Sign-On](../hybrid/how-to-connect-sso.md)을 사용하여 Azure AD를 [PHS(암호 해시 동기화)](../hybrid/whatis-phs.md) 또는 [PTA(통과 인증)](../hybrid/how-to-connect-pta.md)로 배포된 ID 공급자로 사용하는 환경을 나타냅니다.

<sup>5</sup> **Windows 현재를 위한 비지속성 지원** 은 아래 참고 자료 섹션에서 설명하는 추가 고려 사항이 필요합니다. 이 시나리오에는 Windows 10 1803, Windows Server 2019 또는 Windows Server(반기 채널) 시작 버전 1803이 필요합니다.

<sup>6</sup> **Windows 하위 수준을 위한 비지속성 지원** 은 아래 참고 자료 섹션에서 설명하는 추가 고려 사항이 필요합니다.

<sup>7</sup> **Azure AD 조인 지원** 은 Azure Virtual Desktop 및 Windows 365에서만 사용할 수 있습니다.

## <a name="microsofts-guidance"></a>Microsoft 참고 자료

관리자가 하이브리드 Azure AD 조인을 구성하는 방법을 알아보려면 해당 ID 인프라를 참조하여 다음 문서를 참조해야 합니다.

- [페더레이션된 환경에 대한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-federated-domains.md)
- [관리형 환경에 대한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-managed-domains.md)

### <a name="non-persistent-vdi"></a>비영구 VDI

비영구 VDI를 배포하는 경우 IT 관리자가 아래 참고 자료를 구현하는 것이 좋습니다. 이렇게 하지 않으면 디렉터리에 비영구 VDI 플랫폼에 등록된 오래된 하이브리드 Azure AD 조인 디바이스가 많은 생기게 됩니다. 그러면 테넌트 할당량에 대한 압력이 증가하고 테넌트 할당량이 부족하여 서비스 중단이 발생할 수 있습니다.

- 시스템 준비 도구(sysprep.exe)를 사용하고 설치에 Windows 10 1809 이전 이미지를 사용하는 경우, 하이브리드 Azure AD 조인되었다고 Azure AD에 이미 등록된 디바이스에서 이미지를 사용하고 있지 않은지 확인합니다.
- VM(가상 머신) 스냅샷을 사용하여 추가 VM을 만드는 경우, 스냅샷이 Azure AD에 이미 하이브리드 Azure AD 조인으로 등록된 VM에서 만들어지지 않았는지 확인합니다.
- 데스크톱을 비영구 VDI 기반으로 표시하는 컴퓨터의 표시 이름(예: NPVDI)을 위한 접두사를 만들고 사용합니다.
- Windows 하위 수준의 경우
   - **autoworkplacejoin/leave** 명령을 로그오프 스크립트의 일부로 구현합니다. 이 명령은 사용자의 컨텍스트에서 실행되어야 하며, 사용자가 완전히 로그오프하기 전, 네트워크에 연결되어 있을 때 실행해야 합니다.
- 페더레이션된 환경의 Windows 현재의 경우(예: AD FS)
   - 사용자가 로그인하기 전에 VM 부팅 시퀀스/순서의 일부로 **dsregcmd /join** 을 구현합니다.
   - dsregcmd /leave를 VM 종료/다시 시작 프로세스의 일부로 실행 **하지 마세요**.
- [부실 디바이스를 관리](manage-stale-devices.md)하는 프로세스를 정의하고 구현합니다.
   - 비영구 하이브리드 Azure AD 조인된 디바이스를 식별하는 전략이 있으면(예: 컴퓨터 표시 이름 접두사 사용), 해당 디바이스를 적극적으로 정리하여 디렉터리가 여러 부실 디바이스에서 사용되지 않도록 해야 합니다.
   - Windows 현재 및 하위 수준에서 비영구 VDI를 배포하는 경우 **ApproximateLastLogonTimestamp** 가 15일 이상인 디바이스를 삭제해야 합니다.

> [!NOTE]
> 비영구 VDI를 사용하는 경우 회사 또는 학교 계정 추가를 방지하려면 다음 레지스트리 키를 설정해야 합니다.  
> `HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin"=dword:00000001`    
>
> Windows 10 버전 1803 이상을 실행하고 있는지 확인합니다.  
>
> `%localappdata%` 경로 아래의 모든 데이터를 로밍하는 것은 지원되지 않습니다. `%localappdata%` 아래에서 콘텐츠를 이동하도록 선택하는 경우, 다음 폴더 및 레지스트리 키의 내용이 어떤 조건에서도 디바이스를 외부로 이동하지 **않게** 합니다. 예: 프로필 마이그레이션 도구는 다음 폴더 및 키를 건너뛰어야 합니다.
>
> * `%localappdata%\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy`
> * `%localappdata%\Packages\Microsoft.Windows.CloudExperienceHost_cw5n1h2txyewy`
> * `%localappdata%\Packages\<any app package>\AC\TokenBroker`
> * `%localappdata%\Microsoft\TokenBroker`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\IdentityCRL`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\AAD`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows NT\CurrentVersion\WorkplaceJoin`
>
> 작업 계정의 디바이스 인증서 로밍은 지원되지 않습니다. "MS-Organization-Access"에서 발급한 인증서는 현재 사용자의 개인(MY) 인증서 저장소 및 로컬 컴퓨터에 저장됩니다.


### <a name="persistent-vdi"></a>영구적 VDI

영구 VDI를 배포하는 경우 IT 관리자는 아래 지침을 구현하는 것이 좋습니다. 실패하면 배포 문제 및 인증 문제가 발생합니다. 

- 시스템 준비 도구(sysprep.exe)를 사용하고 설치에 Windows 10 1809 이전 이미지를 사용하는 경우, 하이브리드 Azure AD 조인되었다고 Azure AD에 이미 등록된 디바이스에서 이미지를 사용하고 있지 않은지 확인합니다.
- VM(가상 머신) 스냅샷을 사용하여 추가 VM을 만드는 경우, 스냅샷이 Azure AD에 이미 하이브리드 Azure AD 조인으로 등록된 VM에서 만들어지지 않았는지 확인합니다.

또한 [부실 디바이스를 관리](manage-stale-devices.md)하는 프로세스를 구현하는 것이 좋습니다. 이렇게 하면 VM을 정기적으로 초기화할 때 여러 부실 디바이스에서 디렉터리가 사용되지 않습니다.
 
## <a name="next-steps"></a>다음 단계

[페더레이션된 환경을 위한 하이브리드 Azure Active Directory 조인 구성](hybrid-azuread-join-federated-domains.md)
