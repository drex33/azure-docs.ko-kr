---
title: Azure AD Connect 구성 설정을 가져오기 및 내보내기 방법
description: 이 문서에서는 클라우드 프로비저닝에 대해 자주 묻는 질문을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ef5d481cebcdd4c7f08813e8523a03fd45d777b
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113768084"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings"></a>Azure AD Connect 구성 설정 가져오기 및 내보내기 

Azure AD(Azure Active Directory) Connect 배포는 단일 포리스트 기본 모드 설치부터 사용자 지정 동기화 규칙을 사용하여 여러 포리스트에 걸쳐 동기화하는 복잡한 배포에 이르기까지 다양합니다. 구성 옵션과 메커니즘 수가 많기 때문에 어떤 설정이 적용되고 있는지 파악하고 동일한 구성으로 서버를 신속하게 배포할 수 있어야 합니다. 이 기능에는 지정된 동기화 서버의 구성을 카탈로그로 만들고 새 배포로 설정을 가져올 수 있는 기능이 도입되었습니다. 서로 다른 동기화 설정 스냅숏을 비교하여 두 서버 간 차이 또는 시간이 지남에 따른 동일한 서버의 변화를 쉽게 시각화할 수 있습니다.

Azure AD Connect 마법사에서 구성이 변경될 때마다 새 시간 스탬프 JSON 설정 파일이  **%ProgramData%\AADConnect** 에 자동으로 내보내집니다. 설정 파일 이름은 **Applied-SynchronizationPolicy-*.JSON** 형식이며, 파일 이름의 마지막 부분은 타임 스탬프입니다.

> [!IMPORTANT]
> Azure AD Connect에서 변경한 내용만 자동으로 내보내집니다. PowerShell, Synchronization Service Manager 또는 동기화 규칙 편집기를 사용하여 변경한 내용은 최신 복사본을 유지하기 위해 필요에 따라 요청 시 내보내야 합니다. 요청 시 내보내기를 하여 재해 복구를 위해 안전한 위치에 설정 복사본을 저장할 수도 있습니다.

## <a name="export-azure-ad-connect-settings"></a>Azure AD Connect 설정 내보내기 

구성 설정에 대한 요약을 보려면 Azure AD Connect 도구를 열고 **현재 구성 보기 또는 내보내기** 라는 추가 작업을 선택합니다. 설정에 대한 빠른 요약이 서버 전체 구성 내보내기 기능과 함께 표시됩니다.

기본적으로 설정은 **%ProgramData%\AADConnect** 로 내보내집니다. 또한 재해가 발생하는 경우 가용성을 보장하기 위해 설정을 보호된 위치에 저장하도록 선택할 수 있습니다. 설정은 JSON 파일 형식으로 내보내집니다. 논리적 일관성을 유지하기 위해 직접 만들거나 편집해서는 안 됩니다. 직접 만들었거나 편집한 파일 가져오기는 지원되지 않으며 예기치 않은 결과가 발생할 수 있습니다.

## <a name="import-azure-ad-connect-settings"></a>Azure AD Connect 설정 가져오기

이전에 내보낸 설정 가져오기:
 
1. 새 서버에 **Azure AD Connect** 를 설치합니다.
1. **시작** 페이지에서 **사용자 지정** 옵션을 선택합니다.
1. **동기화 설정 가져오기** 를 선택합니다. 이전에 내보낸 JSON 설정 파일을 찾습니다.
1. **설치** 를 선택합니다.

   ![필수 구성 요소 설치 화면을 보여주는 스크린샷](media/how-to-connect-import-export-config/import-1.png)

> [!NOTE]
> LocalDB 대신 SQL Server를 사용하거나 기본 VSA 대신 기존 서비스 계정을 사용하는 것과 같이 이 페이지의 설정을 재정의합니다. 이러한 설정은 구성 설정 파일에서 가져오지 않습니다. 정보 및 비교 목적으로 제공됩니다.

>[!NOTE]
>내보낸 JSON 파일을 수정하여 구성을 변경하는 것은 지원되지 않습니다.

### <a name="import-installation-experience"></a>설치 경험 가져오기 

설치 경험 가져오기는 기존 서버에 대한 재현 가능성을 쉽게 제공하기 위해 사용자의 입력을 최소화하면서 의도적으로 단순하게 유지됩니다.

설치 경험에서 수행할 수 있는 유일한 변경 내용은 다음과 같습니다. Azure AD Connect 마법사에서 설치 후 다른 모든 변경 작업을 수행할 수 있습니다.
- **Azure Active Directory 자격 증명**: 원래 서버를 구성하는 데 사용되는 Azure 전역 관리자의 계정 이름이 기본적으로 제안됩니다. 새 디렉터리에 정보를 동기화하려는 경우 *변경해야*  합니다.
- **사용자 로그인**: 원래 서버에 대해 구성된 로그온 옵션이 기본적으로 선택되며, 구성 중에 필요한 자격 증명 또는 기타 정보를 자동으로 확인합니다. 드문 경우지만 활성 서버의 동작을 변경하지 않도록 다른 옵션을 사용하여 서버를 설정해야 할 수 있습니다. 그렇지 않은 경우 **다음** 을 선택하여 동일한 설정을 사용합니다.
- **온-프레미스 디렉터리 자격 증명**: 동기화 설정에 포함된 각 온-프레미스 디렉터리에 대해 동기화 계정을 만들거나 미리 만든 사용자 지정 동기화 계정을 공급하기 위한 자격 증명을 제공해야 합니다. 이 프로시저는 디렉터리를 추가하거나 제거할 수 없다는 예외를 제외하면 새로 설치 경험과 동일합니다.
- **구성 옵션**: 새로 설치하는 경우와 마찬가지로 자동 동기화를 시작할지 아니면 준비 모드를 사용할지에 대한 초기 설정을 구성하도록 선택할 수 있습니다. 주요 차이점은 기본적으로 준비 모드는 의도적으로 사용하도록 설정되어 결과를 Azure에 적극적으로 내보내기 전에 구성과 동기화 결과를 비교할 수 있다는 점입니다.

![디렉터리 연결 화면을 보여주는 스크린샷](media/how-to-connect-import-export-config/import-2.png)

> [!NOTE]
> 하나의 동기화 서버만 주 역할에 있을 수 있으며 구성 변경 내용을 Azure에 적극적으로 내보낼 수 있습니다. 다른 모든 서버는 준비 모드에 배치해야 합니다.

## <a name="migrate-settings-from-an-existing-server"></a>기존 서버에서 설정 마이그레이션 

기존 서버에서 설정 관리를 지원하지 않는 경우 서버를 현재 위치로 업그레이드하거나 새 준비 서버에서 사용할 설정을 마이그레이션하도록 선택할 수 있습니다.

마이그레이션하려면 새 설치에 사용할 기존 설정을 추출하는 PowerShell 스크립트를 실행해야 합니다.이 메서드를 사용하여 기존 서버의 설정을 카탈로그로 만든 후 새로 설치된 준비 서버에 적용할 수 있습니다.원래 서버의 설정을 새로 만든 서버와 비교하면 서버 간의 변경 내용을 신속하게 시각화할 수 있습니다.항상 조직의 인증 프로세스를 수행하여 추가 구성이 필요하지 않도록 합니다.

### <a name="migration-process"></a>마이그레이션 프로세스 
설정 마이그레이션하기:

 1. 새 준비 서버에서 **AzureADConnect.msi** 를 시작하고 Azure AD Connect의 **시작** 페이지에서 중지합니다.

 2. Microsoft Azure AD Connect\Tools 디렉터리에서 기존 서버 위치로 **MigrateSettings.ps1** 을 복사합니다. 예를 들어, C:\setup이 있습니다. 여기서 setup은 기존 서버에서 만든 디렉터리입니다.</br>
     ![Azure AD Connect 디렉터리를 보여주는 스크린샷.](media/how-to-connect-import-export-config/migrate-1.png)

     >[!NOTE]
     > 다음과 같이 **"True** 인수를 허용하는 위치 매개 변수를 찾을 수 없습니다."라는 메시지가 표시되는 경우:
     >
     >
     >![오류 스크린샷](media/how-to-connect-import-export-config/migrate-5.png) 그런 다음, MigrateSettings.ps1 파일을 편집하고, **$true** 를 제거하고 스크립트를 실행합니다. ![구성을 편집하는 스크린샷](media/how-to-connect-import-export-config/migrate-6.png)
 



 3. 여기에 표시된 대로 스크립트를 실행하고 하위 수준 서버 구성 디렉터리 전체를 저장합니다. 이 디렉터리를 새 준비 서버에 복사합니다. **Exported-ServerConfiguration-** * 폴더 전체를 새 서버에 복사해야 합니다.
     ![Windows PowerShell의 스크립트를 보여주는 스크린샷.](media/how-to-connect-import-export-config/migrate-2.png)![Exported-ServerConfiguration-* 폴더 복사를 보여주는 스크린샷.](media/how-to-connect-import-export-config/migrate-3.png)

 4. 바탕 화면에서 아이콘을 두 번 클릭하여 **Azure AD Connect** 를 시작합니다. Microsoft 소프트웨어 사용 조건에 동의하고 다음 페이지에서 **사용자 지정** 을 선택합니다.
 5. **동기화 설정 가져오기** 확인란을 선택합니다. **찾아보기** 를 선택하여 복사된 Exported-ServerConfiguration-* 폴더를 찾아봅니다. MigratedPolicy.json을 선택하여 마이그레이션된 설정을 가져옵니다.

     ![동기화 설정 가져오기 옵션을 보여주는 스크린샷.](media/how-to-connect-import-export-config/migrate-4.png)

## <a name="post-installation-verification"></a>설치 후 확인 

새로 배포된 서버의 내보낸 설정 파일과 원래 가져온 설정 파일 비교는 의도된 배포와 결과 배포 간의 차이점을 파악하는 필수 단계입니다. 선호하는 병렬 텍스트 비교 애플리케이션을 사용하면 원하는 대로 또는 실수로 변경된 내용을 빠르게 강조 표시하는 즉각적인 시각화가 산출됩니다.

이전의 수동 구성 단계는 이제 대부분 제거되었지만 추가적인 구성이 필요하지 않은지 확인하려면 여전히 조직의 인증 프로세스를 수행해야 합니다. 현재 이 설정 관리 릴리스에서 캡처되지 않은 고급 설정을 사용하는 경우 이 구성이 발생할 수 있습니다.

다음은 알려진 제한 사항입니다.
- **동기화 규칙**: 사용자 지정 규칙에 대한 우선 순위는 Microsoft 표준 규칙과의 충돌을 피하기 위해 0에서 99 사이여야 합니다. 사용자 지정 규칙을 예약된 범위 밖에 두면 표준 규칙이 구성에 추가되면 사용자 지정 규칙이 이동되는 결과를 야기할 수 있습니다. 구성에 수정된 표준 규칙이 포함되는 경우 유사한 문제가 발생합니다. 표준 규칙을 수정하지 않는 것이 좋으며 규칙 배치가 잘못된 것일 수 있습니다.
- **디바이스 쓰기 저장**: 이러한 설정은 카탈로그됩니다. 구성하는 동안 현재 적용되지 않습니다. 원래 서버에 디바이스 쓰기 저장을 사용하도록 설정된 경우 새로 배포된 서버에서 이 기능을 수동으로 구성해야 합니다.
- **동기화된 개체 형식**: Synchronization Service Manager를 사용하여 동기화된 개체 형식(예: 사용자, 연락처 및 그룹) 목록을 제한할 수 있지만, 이 기능은 현재 동기화 설정을 통해 지원되지 않습니다. 설치를 완료한 후 고급 구성을 수동으로 다시 적용해야 합니다.
- **사용자 지정 실행 프로필**: Synchronization Service Manager를 사용하여 기본 프로필 실행 집합을 수정할 수 있지만 이 기능은 현재 동기화 설정을 통해 지원되지 않습니다. 설치를 완료한 후 고급 구성을 수동으로 다시 적용해야 합니다.
- **프로비전 계층 구성**: Synchronization Service Manager의 이 고급 기능은 동기화 설정을 통해 지원되지 않습니다. 초기 배포를 완료한 후 수동으로 다시 구성해야 합니다.
- **AD FS(Active Directory Federation Services) 및 PingFederate 인증**: 이러한 인증 기능과 연결된 로그인 방법이 자동으로 미리 선택됩니다. 다른 모든 필수 구성 매개 변수를 대화형으로 공급해야 합니다.
- **사용할 수 없는 사용자 지정 동기화 규칙을 사용할 수 있는 것으로 가져옴**: 사용할 수 없는 사용자 지정 동기화 규칙을 사용할 수 있는 것으로 가져옵니다. 새 서버에서도 사용하지 않도록 설정해야 합니다.

 ## <a name="next-steps"></a>다음 단계

- [하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md) 
- [Express 설정](how-to-connect-install-express.md)
- [사용자 지정된 설정](how-to-connect-install-custom.md)
- [Azure AD Connect Health 에이전트 설치](how-to-connect-health-agent-install.md) 
