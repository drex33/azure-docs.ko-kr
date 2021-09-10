---
title: 'Azure AD Connect: DirSync에서 업그레이드 | Microsoft Docs'
description: DirSync에서 Azure AD Connect로 업그레이드하는 방법을 알아봅니다. 이 문서에서는 DirSync에서 Azure AD Connect로 업그레이드하기 위한 단계를 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: baf52da7-76a8-44c9-8e72-33245790001c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9193b71f2634e2cf9ac5970fb6f9fe2a965dc93
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114458260"
---
# <a name="azure-ad-connect-upgrade-from-dirsync"></a>Azure AD Connect: DirSync에서 업그레이드
Azure AD Connect는 DirSync의 후속 도구입니다. 이 항목의 DirSync에서 업그레이드하는 방법을 찾습니다. 다음 단계는 Azure AD Connect의 다른 버전 또는 Azure AD Sync에서 업그레이드하는 경우에 작동하지 않습니다.

DirSync 및 Azure AD Sync는 지원되지 않으며 더 이상 작동하지 않습니다. 이러한 작업을 계속 사용하는 경우 동기화 프로세스를 다시 시작하려면 AADConnect로 업그레이드해야 합니다.

Azure AD Connect 설치를 시작하기 전에 [Azure AD Connect를 다운로드](https://go.microsoft.com/fwlink/?LinkId=615771)하고 [Azure AD Connect: 하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md)의 필수 구성 요소 단계를 완료해야 합니다. 특히, 이러한 영역이 DirSync와 다르기 때문에 다음에 대해 참고해야 합니다.

* .NET 및 PowerShell의 필수 버전입니다. 서버에는 DirSync에서보다 최신 버전이 필요합니다.
* 프록시 서버 구성입니다. 프록시 서버를 사용하여 인터넷에 연결하는 경우 업그레이드하기 전에 이 설정을 구성해야 합니다. DirSync을 설치하는 사용자를 위해 구성된 프록시 서버를 항상 사용하지만 Azure AD Connect는 대신 컴퓨터 설정을 사용합니다.
* URL은 프록시 서버에서 열려야 합니다. 또한 기본 시나리오의 경우 해당 시나리오는 DirSync에서 지원되며 요구 사항은 동일합니다. Azure AD Connect에 포함된 새로운 기능 중 일부를 사용하려는 경우 몇 가지 새 URL이 열려 있어야 합니다.

> [!NOTE]
> 새 Azure AD Connect 서버에서 Azure AD에 대한 변경 내용 동기화를 시작하도록 설정한 후에는 DirSync 또는 Azure AD Sync를 사용하도록 롤백해서는 안 됩니다. Azure AD Connect에서 DirSync 및 Azure AD Sync를 포함한 레거시 클라이언트로 다운그레이드하는 것은 지원되지 않으며 Azure AD에서 데이터 손실과 같은 문제가 발생할 수 있습니다.

DirSync에서 업그레이드하지 않는 경우 다른 시나리오에 대한 관련 설명서를 참조하세요.

## <a name="upgrade-from-dirsync"></a>DirSync에서 업그레이드
현재 DirSync 배포에 따라 업그레이드에 대한 여러 옵션이 있습니다. 예상되는 업그레이드 시간이 3시간 미만인 경우 전체 업그레이드를 수행하는 것이 좋습니다. 예상되는 업그레이드 시간이 3시간 이상인 경우 다른 서버에서 병렬 배포를 수행하는 것이 좋습니다. 50,000개 이상의 개체가 있는 경우 업그레이드를 수행하는 데 3시간 이상이 걸릴 수 있습니다.

| 시나리오 |
| --- |
| [현재 위치 업그레이드](#in-place-upgrade) |
| [병렬 배포](#parallel-deployment) |

> [!NOTE]
> DirSync에서 Azure AD Connect로 업그레이드하려는 경우 업그레이드하기 전에 DirSync를 직접 제거하지 마세요. Azure AD Connect는 DirSync에서 구성을 읽고 마이그레이션하며 서버를 검사한 후에 제거합니다.

**현재 위치 업그레이드**  
업그레이드를 완료하는 데 예상되는 시간은 마법사에서 표시됩니다. 이 예상치는 50,000개의 개체(사용자, 연락처 및 그룹)를 가진 데이터베이스에 대한 업그레이드를 완료하려면 3시간이 소요된다는 가정에 기반합니다. 데이터베이스의 개체 수가 50,000보다 작은 경우 Azure AD Connect는 전체 업그레이드를 권장합니다. 계속하기로 결정한 경우 업그레이드하는 동안 현재 설정을 자동으로 적용하고 서버는 자동으로 활성 동기화를 다시 시작합니다.

구성 마이그레이션을 수행하고 병렬 배포를 수행하려는 경우 전체 업그레이드 권장 사항을 재정의할 수 있습니다. 예를 들어 하드웨어 및 운영 체제를 새로 고칠 수 있는 기회를 얻을 수 있습니다. 자세한 내용은 [병렬 배포](#parallel-deployment) 섹션을 참조하세요.

**병렬 배포**  
50,000개 이상의 개체가 있는 경우 병렬 배포를 사용하는 것이 좋습니다. 이 배포는 사용자에게 운영 지연이 발생하지 않도록 방지합니다. Azure AD Connect 설치는 업그레이드에 대한 가동 중지 시간을 예측하려고 합니다. 하지만 과거에 DirSync를 업그레이드한 적이 있다면 자신만의 경험이 최선의 가이드일 수 있습니다.

### <a name="supported-dirsync-configurations-to-be-upgraded"></a>업그레이드될 지원되는 DirSync 구성
다음 구성 변경은 업그레이드된 DirSync로 지원됩니다.

* 도메인 및 OU 필터링
* 대체 ID(UPN)
* 암호 동기화 및 Exchange 하이브리드 설정
* 포리스트/도메인 및 Azure AD 설정
* 사용자 특성에 기반하여 필터링

다음 변경 내용은 업그레이드할 수 없습니다. 이렇게 구성한 경우 업그레이드가 차단됩니다.

* 지원되지 않는 DirSync 변경 내용, 예: 제거된 특성 및 사용자 지정 확장 DLL 사용

![업그레이드 차단](./media/how-to-dirsync-upgrade-get-started/analysisblocked.png)

이러한 경우 [준비 모드](how-to-connect-sync-staging-server.md) 에서 새 Azure AD Connect 서버를 설치하고 이전 DirSync 및 새 Azure AD Connect 구성을 확인하는 것이 좋습니다. [Azure AD Connect 동기화 사용자 지정 구성](how-to-connect-sync-whatis.md)에서 설명한 대로 사용자 지정 구성을 사용하여 변경 내용을 다시 적용합니다.

서비스 계정에 DirSync에서 사용한 암호는 검색할 수 없고 마이그레이션되지 않습니다. 이러한 암호는 업그레이드하는 동안 다시 설정됩니다.

### <a name="high-level-steps-for-upgrading-from-dirsync-to-azure-ad-connect"></a>DirSync에서 Azure AD Connect로 업그레이드하기 위한 대략적인 단계
1. Azure AD Connect 시작
2. 현재 DirSync 구성 분석
3. Azure AD 전역 관리자 암호 수집
4. 엔터프라이즈 관리자 계정에 대한 자격 증명 수집(Azure AD Connect 설치 도중에만 사용됨)
5. Azure AD Connect 설치
   * DirSync 제거(또는 일시적으로 사용 안 함)
   * Azure AD Connect 설치
   * (선택 사항) 동기화 시작

다음과 같은 경우에 추가 단계가 필요합니다.

* 현재 로컬 또는 원격으로 전체 SQL Server를 사용 중임
* 동기화 범위에 50,000개가 넘는 개체가 있음

## <a name="in-place-upgrade"></a>전체 업그레이드
1. Azure AD Connect 설치 관리자(MSI) 시작
2. 사용 조건 및 개인 정보 알림을 검토하고 동의합니다.  
   ![Azure AD 시작](./media/how-to-dirsync-upgrade-get-started/Welcome.png)
3. 다음을 클릭하여 기존 DirSync 설치 분석을 시작합니다.  
   ![기존 디렉터리 동기화 설치 분석](./media/how-to-dirsync-upgrade-get-started/Analyze.png)
4. 분석이 완료되면 진행 방법에 대해 권장 사항을 표시합니다.  
   * SQL Server Express를 사용하고 50,000개 미만의 개체가 있는 경우 다음과 같은 화면이 표시됩니다.  
     ![분석이 완료되어 DirSync에서 업그레이드할 준비가 됨](./media/how-to-dirsync-upgrade-get-started/AnalysisReady.png)
   * DirSync에 전체 SQL Server를 사용하는 경우 대신 다음 페이지가 표시됩니다.  
     ![사용 중인 기존 SQL 데이터베이스 서버를 보여주는 스크린샷.](./media/how-to-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
     DirSync에서 사용 중인 기존 SQL Server 데이터베이스 서버와 관련된 정보는 표시됩니다. 필요한 경우 적절하게 조정합니다. **다음** 을 클릭하여 설치를 계속 진행합니다.
   * 50,000개 이상의 개체가 있는 경우 대신 다음 화면이 표시됩니다.  
     ![업그레이드할 개체가 50,000개를 초과하는 경우 표시되는 화면을 보여주는 스크린샷.](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
     현재 위치 업그레이드를 진행하려면 **이 컴퓨터에서 계속 DirSync 업그레이드합니다.** 라는 메시지 옆에 있는 확인란을 클릭합니다.
     대신 [병렬 배포](#parallel-deployment)를 수행하려면 DirSync 구성 설정을 내보내고 해당 구성을 새 서버로 이동합니다.
5. Azure AD에 연결하는 데 현재 사용하는 계정의 암호를 제공합니다. DirSync에서 현재 사용한 계정이어야 합니다.  
   ![Azure AD 자격 증명 입력](./media/how-to-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
   오류가 발생하고 연결에 문제가 있는 경우 [연결 문제 해결](tshoot-connect-connectivity.md)을 참조하세요.
6. Active Directory에 대한 엔터프라이즈 관리자 계정을 지정합니다.  
   ![ADDS 자격 증명 입력](./media/how-to-dirsync-upgrade-get-started/ConnectToADDS.png)
7. 이제 구성할 준비가 되었습니다. **업그레이드** 를 클릭하면 DirSync가 제거되고 Azure AD Connect가 구성되어 동기화를 시작합니다.  
   ![구성할 준비 완료](./media/how-to-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. 설치가 완료된 후 로그아웃하고 Synchronization Service Manager, 동기화 규칙 편집기 또는 다른 구성의 변경을 시도하기 전에 Windows에 다시 로그인합니다.

## <a name="parallel-deployment"></a>병렬 배포
### <a name="export-the-dirsync-configuration"></a>DirSync 구성 내보내기
**50,000개 이상의 개체를 가진 병렬 배포**

개체가 50,000개를 넘는 경우 Azure AD Connect 설치 시 병렬 배포가 권장됩니다.

다음과 비슷한 화면이 표시됩니다.  
![분석 완료](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

병렬 배포를 진행하려는 경우 다음 단계를 수행해야 합니다.

* **설정 내보내기** 단추를 클릭합니다. 별도 서버에 Azure AD Connect를 설치하면 이 설정을 현재 DirSync에서 새 Azure AD Connect 설치로 이미그레이션됩니다.

설정을 성공적으로 내보내고 나면 DirSync 서버에서 Azure AD Connect 마법사를 종료할 수 있습니다. 다음 단계를 계속하여 별도 서버에 Azure AD Connect를 설치합니다.

**50,000개 미만의 개체를 가진 병렬 배포**

50,000개 미만의 개체가 있지만 병렬 배포를 하려는 경우 다음을 수행합니다.

1. Azure AD Connect 설치 관리자(MSI)를 실행합니다.
2. **Azure AD Connect 시작** 화면이 표시되면 창 오른쪽 위 모서리에서 "X"를 클릭하여 설치 마법사를 종료합니다.
3. 명령 프롬프트를 엽니다.
4. Azure AD Connect 설치 위치(기본값: C:\Program Files\Microsoft Azure Active Directory Connect)에서 `AzureADConnect.exe /ForceExport` 명령을 실행합니다.
5. **설정 내보내기** 단추를 클릭합니다. 별도 서버에 Azure AD Connect를 설치하면 이 설정을 현재 DirSync에서 새 Azure AD Connect 설치로 이미그레이션됩니다.

![설정을 새 Azure AD Connect 설치로 마이그레이션하기 위한 설정 내보내기 옵션을 보여주는 스크린샷.](./media/how-to-dirsync-upgrade-get-started/forceexport.png)

설정을 성공적으로 내보내고 나면 DirSync 서버에서 Azure AD Connect 마법사를 종료할 수 있습니다. 다음 단계를 계속하여 별도 서버에 Azure AD Connect를 설치합니다.

### <a name="install-azure-ad-connect-on-separate-server"></a>별도 서버에 Azure AD Connect 설치
새 서버에 Azure AD Connect를 설치할 때 Azure AD Connect 새로 설치를 수행한다고 가정합니다. DirSync 구성을 사용하려면 수행해야 할 몇 가지 추가 단계가 있습니다.

1. Azure AD Connect 설치 관리자(MSI)를 실행합니다.
2. **Azure AD Connect 시작** 화면이 표시되면 창 오른쪽 위 모서리에서 "X"를 클릭하여 설치 마법사를 종료합니다.
3. 명령 프롬프트를 엽니다.
4. Azure AD Connect 설치 위치(기본값: C:\Program Files\Microsoft Azure Active Directory Connect)에서 `AzureADConnect.exe /migrate` 명령을 실행합니다.
   Azure AD Connect 설치 마법사가 시작되면 다음 화면이 표시됩니다.  
   ![업그레이드할 때 설정 파일을 가져올 위치를 보여주는 스크린샷.](./media/how-to-dirsync-upgrade-get-started/ImportSettings.png)
5. DirSync 설치에서 내보낸 설정 파일을 선택합니다.
6. 다음을 포함한 고급 옵션을 구성합니다.
   * Azure AD Connect에 대한 사용자 지정 설치 위치
   * 기존 SQL Server 인스턴스(기본값: Azure AD Connect는 SQL Server 2019 Express를 설치함) DirSync 서버와 동일한 데이터베이스 인스턴스를 사용하지 마세요.
   * SQL Server에 연결하는 데 사용되는 서비스 계정(SQL Server 데이터베이스가 원격인 경우에는 이 계정이 도메인 서비스 계정이어야 함) 이러한 옵션은 이 화면에서 볼 수 있습니다.
     이러한 옵션은 이 화면에서 볼 수 있습니다.  
     ![DirSync에서 업그레이드하기 위한 고급 구성 옵션을 보여주는 스크린샷.](./media/how-to-dirsync-upgrade-get-started/advancedsettings.png)
7. **다음** 을 클릭합니다.
8. **구성 준비 완료** 페이지에서 **구성이 완료되자마자 동기화 프로세스를 시작합니다.** 를 선택한 상태로 둡니다. 서버가 이제 [준비 모드](how-to-connect-sync-staging-server.md) 이므로 변경 내용을 Azure AD로 내보내지 않습니다.
9. **설치** 를 클릭합니다.
10. 설치가 완료된 후 로그아웃하고 Synchronization Service Manager, 동기화 규칙 편집기 또는 다른 구성의 변경을 시도하기 전에 Windows에 다시 로그인합니다.

> [!NOTE]
> Windows Server Active Directory와 Azure Active Directory 간의 동기화가 시작되지만 Azure AD로 내보내는 변경 내용은 없습니다. 하나의 동기화 도구만이 변경 내용을 한 번에 내보낼 수 있습니다. 이 상태를 [준비 모드](how-to-connect-sync-staging-server.md)라고 합니다.

### <a name="verify-that-azure-ad-connect-is-ready-to-begin-synchronization"></a>Azure AD Connect가 동기화를 시작할 준비가 되었는지 확인
Azure AD Connect가 DirSync로부터 인수할 준비가 되었는지 확인하려면 시작 메뉴에서 **Azure AD Connect** 그룹의 **동기화 서비스 관리자** 를 열어야 합니다.

애플리케이션에서 **작업** 탭으로 이동합니다. 이 탭에서 다음 작업이 완료되었는지 확인합니다.

* AD 커넥터에 가져오기
* Azure AD 커넥터에 가져오기
* AD 커넥터에 전체 동기화
* Azure AD 커넥터에 전체 동기화

![가져오기 및 동기화 완료](./media/how-to-dirsync-upgrade-get-started/importsynccompleted.png)

이러한 작업에서 결과를 검토하고 오류가 없는지 확인합니다.

Azure AD로 내보낼 변경 내용을 확인하고 검사하려는 경우 [준비 모드](how-to-connect-sync-staging-server.md)에서 구성을 확인하는 방법을 읽습니다. 예상하지 못한 것이 나타나지 않을 때까지 필요한 구성을 변경합니다.

이러한 단계를 완료하고 결과에 만족하면 DirSync에서 Azure AD로 전환할 준비가 되었습니다.

### <a name="uninstall-dirsync-old-server"></a>DirSync 제거(이전 서버)
* **프로그램 및 기능** 에서 **Microsoft Azure Active Directory 동기화 도구** 찾기
* **Microsoft Azure Active Directory 동기화 도구**
* 제거를 완료하는 데 최대 15분이 걸릴 수 있습니다.

나중에 DirSync를 제거하려는 경우 일시적으로 서버를 종료 하거나 서비스를 사용하지 않도록 설정할 수 있습니다. 오류가 있는 경우 이 메서드를 사용하면 다시 사용하도록 설정할 수 있습니다. 그러나 다음 단계가 실패하지 않아서 이 메서드가 필요하지 않아야 합니다.

DirSync를 제거하거나 사용하지 않으면 Azure AD로 내보내는 활성 서버는 없습니다. Azure AD로 동기화할 온-프레미스 Active Directory의 변경 내용을 계속하기 전에 Azure AD Connect를 사용하도록 설정하는 다음 단계를 완료해야 합니다.

### <a name="enable-azure-ad-connect-new-server"></a>Azure AD Connect 사용(새 서버)
설치 후 Azure AD Connect를 다시 열면 추가적으로 구성을 변경할 수 있습니다. 시작 메뉴 또는 바탕 화면의 바로 가기에서 **Azure AD Connect** 를 시작합니다. 다시 설치 MSI를 실행하지 않도록 합니다.

다음이 표시되어야 합니다.  
![추가 작업](./media/how-to-dirsync-upgrade-get-started/AdditionalTasks.png)

* **준비 모드 구성** 을 선택합니다.
* **준비 모드 사용** 확인란의 선택을 취소하여 준비를 해제합니다.

![스테이징 모드를 사용하도록 설정하는 옵션을 보여주는 스크린샷.](./media/how-to-dirsync-upgrade-get-started/configurestaging.png)

* **다음** 단추를 클릭합니다.
* 확인 페이지에서 **설치** 단추를 클릭합니다.

이제 Azure AD Connect는 활성 서버이며 기존 DirSync 서버를 사용하도록 전환하지 않아야 합니다.

## <a name="next-steps"></a>다음 단계
Azure AD Connect를 설치했으므로 [설치를 확인하고 라이선스를 할당](how-to-connect-post-installation.md)할 수 있습니다.

[자동 업그레이드](how-to-connect-install-automatic-upgrade.md), [실수로 인한 삭제 방지](how-to-connect-sync-feature-prevent-accidental-deletes.md) 및 [Azure AD Connect Health](how-to-connect-health-sync.md)를 설치하여 사용할 수 있는 이러한 새 기능에 대해 자세히 알아봅니다.

공통 항목인 [스케줄러 및 동기화를 트리거하는 방법](how-to-connect-sync-feature-scheduler.md)에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
