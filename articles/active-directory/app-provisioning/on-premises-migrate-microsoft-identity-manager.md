---
title: Azure AD ECMA 커넥터 호스트에서 사용할 Microsoft Identity Manager 커넥터 내보내기
description: Azure AD ECMA 커넥터 호스트와 함께 사용할 MIM 동기화에서 커넥터를 만들고 내보내는 방법을 설명합니다.
services: active-directory
author: billmath
manager: karenh444
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0044f96f58ba3aa868577b0d0664d71daeddbc7f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129991074"
---
# <a name="export-a-microsoft-identity-manager-connector-for-use-with-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA 커넥터 호스트에서 사용할 Microsoft Identity Manager 커넥터 내보내기

>[!IMPORTANT]
> 온-프레미스 프로비저닝 미리 보기는 현재 초대 전용 미리 보기로 제공됩니다. 기능에 대한 액세스를 요청하려면 [액세스 요청 양식](https://aka.ms/onpremprovisioningpublicpreviewaccess)을 사용하세요. 일반 공급을 준비하면서 향후 몇 개월 동안 더 많은 고객 및 커넥터에 대한 미리 보기를 공개할 예정입니다.

Forefront Identity Manager 동기화 서비스 또는 MIM 동기화(Microsoft Identity Manager 동기화 서비스) 설치에서 특정 커넥터에 대한 구성을 Azure AD(Azure Active Directory) ECMA 커넥터 호스트로 가져올 수 있습니다. MIM 동기화 설치는 Azure AD에서 진행 중인 동기화가 아니라 구성에만 사용됩니다.

>[!IMPORTANT]
>현재 Azure AD ECMA 커넥터 호스트에는 일반 SQL 커넥터만 사용할 수 있습니다.

## <a name="create-and-export-a-connector-configuration-in-mim-sync"></a>MIM 동기화에서 커넥터 구성 만들기 및 내보내기
MIM 동기화에서 이미 ECMA 커넥터가 구성된 경우 10단계로 건너뜁니다.

 1. Azure AD ECMA 커넥터 호스트를 실행하는 데 사용할 서버와는 다른 Windows Server 2016 서버를 준비합니다. 이 호스트 서버에는 SQL Server 2016 데이터베이스가 함께 배치되어 있거나 SQL Server 2016 데이터베이스에 대한 네트워크 연결이 있어야 합니다. 이 서버를 설치하는 한 가지 방법은 **Windows Server 2016에서 SQL Server 2016 SP1 Standard** 이미지를 사용하여 Azure Virtual Machine을 배포하는 것입니다. 이 서버는 설치를 위한 원격 데스크톱 액세스 이외에는 인터넷 연결이 필요하지 않습니다.
 1. MIM 동기화 설치 중에 사용할 계정을 만듭니다. 이 계정은 해당 Windows Server 인스턴스의 로컬 계정일 수 있습니다. 로컬 계정을 만들려면 **제어판** > **사용자 계정** 을 열고 사용자 계정 **mimsync** 를 추가합니다.
 1. 이전 단계에서 만든 계정을 로컬 관리자 그룹에 추가합니다.
 1. 이전에 만든 계정에 서비스를 실행할 수 있는 기능을 제공합니다. **로컬 보안 정책** 을 시작하고 **로컬 정책** > **사용자 권한 할당** > **서비스로 로그온** 을 선택합니다. 앞서 언급한 계정을 추가합니다.
 1. 이 호스트에 MIM 동기화를 설치합니다. MIM 동기화 이진 파일이 없는 경우 [Microsoft 다운로드 센터](https://www.microsoft.com/en-us/download/details.aspx?id=48244)에서 ZIP 파일을 다운로드하고, ISO 이미지를 탑재하고, **Synchronization Service** 폴더를 Windows Server 호스트에 복사하여 평가판을 설치할 수 있습니다. 그런 다음, 해당 폴더에 포함된 설치 프로그램을 실행합니다. 평가 소프트웨어는 시간 제한이 있고 만료됩니다. 프로덕션 용도로 사용하기 위한 것이 아닙니다.
 1. MIM 동기화 설치가 완료되면 로그아웃했다가 다시 로그인합니다.
 1. MIM 동기화와 동일한 서버에 커넥터를 설치합니다. 설명을 위해 이 테스트 랩 가이드에서는 [Microsoft 다운로드 센터](https://www.microsoft.com/en-us/download/details.aspx?id=51495)에서 다운로드할 수 있는, Microsoft에서 제공하는 커넥터 중 하나를 사용하는 방법을 설명합니다.
 1. 동기화 서비스 UI를 시작합니다. **관리 에이전트** 를 선택합니다. **만들기** 를 선택하고 커넥터 관리 에이전트를 지정합니다. ECMA 기반의 커넥터 관리 에이전트를 선택해야 합니다.
 1. 커넥터의 이름을 지정하고, 데이터를 커넥터로 가져오고 내보내는 데 필요한 매개 변수를 구성합니다. 커넥터가 사용자 또는 사람 개체 형식의 단일 값 문자열 특성을 가져오고 내보낼 수 있도록 구성해야 합니다.
 1. MIM 동기화 서버 컴퓨터에서 동기화 서비스 UI를 시작합니다(아직 실행되고 있지 않은 경우). **관리 에이전트** 를 선택합니다.
 1. 커넥터를 선택하고 **관리 에이전트 내보내기** 를 선택합니다. ECMA 커넥터 호스트를 유지할 Windows Server에 XML 파일과 커넥터에 대한 DLL 및 관련 소프트웨어를 저장합니다.

이 시점에서는 MIM 동기화 서버가 더 이상 필요하지 않습니다.

 1. Azure AD ECMA 커넥터 호스트가 실행될 계정으로 Windows Server에 로그인합니다.
 1. C:\Program Files\Microsoft ECMA2host\Service\ECMA 디렉터리로 변경합니다. 해당 디렉터리에 이미 하나 이상의 DLL이 있는지 확인합니다. 이러한 DLL은 Microsoft에서 제공하는 커넥터에 해당합니다.
 1. 커넥터에 대한 MA DLL 및 해당 필수 조건 DLL을 Service 디렉터리의 동일한 ECMA 하위 디렉터리에 복사합니다.
 1. C:\Program Files\Microsoft ECMA2Host\Wizard 디렉터리로 변경합니다. Microsoft.ECMA2Host.ConfigWizard.exe 프로그램을 실행하여 ECMA 커넥터 호스트 구성을 설정합니다.
 1. 커넥터 목록이 포함된 새 창이 표시됩니다. 기본적으로 커넥터는 없습니다. **새 커넥터** 를 선택합니다.
 1. 이전에 MIM 동기화에서 내보낸 관리 에이전트 XML 파일을 지정합니다. "커넥터 구성" 섹션에서 구성 및 스키마 매핑 지침을 계속 진행합니다.

## <a name="next-steps"></a>다음 단계

- [앱 프로비저닝](user-provisioning.md)
- [일반 SQL 커넥터](on-premises-sql-connector-configure.md)
