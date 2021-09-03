---
title: 'Azure AD Connect: LocalDB 10GB 제한 문제에서 복구하는 방법 | Microsoft Docs'
description: 이 항목에서는 LocalDB 10GB 제한 문제가 발생한 경우 Azure AD Connect 동기화 서비스를 복구하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 41d081af-ed89-4e17-be34-14f7e80ae358
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16172c5c11eba90680604d64672f4bdeed090830
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442281"
---
# <a name="azure-ad-connect-how-to-recover-from-localdb-10-gb-limit"></a>Azure AD Connect: LocalDB 10GB 제한에서 복구하는 방법
Azure AD Connect는 ID 데이터를 저장하기 위한 SQL Server 데이터베이스가 필요합니다. Azure AD Connect로 설치된 기본 SQL Server 2019 Express LocalDB를 사용하거나 사용자 고유의 전체 SQL을 사용할 수 있습니다. SQL Server Express는 10GB 크기 제한을 적용합니다. LocalDB를 사용하고 이 제한에 도달하는 경우 Azure AD Connect 동기화 서비스는 더 이상 제대로 시작하거나 동기화할 수 없습니다. 이 문서에서는 복구 단계를 제공합니다.

## <a name="symptoms"></a>증상
두 가지 일반적인 증상이 있습니다.

* Azure AD Connect 동기화 서비스가 **실행** 되지만 *“stopped-database-disk-full”* 오류로 동기화에 실패합니다.

* Azure AD Connect 동기화 서비스를 **시작할 수 없습니다**. 서비스를 시작하려고 할 때 이벤트 6323 및 오류 메시지 *"SQL Server의 디스크 공간이 부족하기 때문에 서버에서 오류가 발생했습니다."* 와 함께 실패합니다.

## <a name="short-term-recovery-steps"></a>단기 복구 단계
이 섹션에서는 Azure AD Connect 동기화 서비스를 다시 시작하는 작업에 필요한 DB 공간을 회수하는 단계를 제공합니다. 단계는 다음과 같습니다.
1. [동기화 서비스 상태 확인](#determine-the-synchronization-service-status)
2. [데이터베이스 축소](#shrink-the-database)
3. [실행 기록 데이터 삭제](#delete-run-history-data)
4. [실행 기록 데이터에 대한 보존 기간 단축](#shorten-retention-period-for-run-history-data)

### <a name="determine-the-synchronization-service-status"></a>동기화 서비스 상태 확인
먼저 동기화 서비스가 계속 실행 중인지 여부를 확인합니다.

1. 관리자 권한으로 Azure AD Connect 서버에 로그인합니다.

2. **서비스 제어 관리자** 로 이동합니다.

3. **Microsoft Azure AD Sync** 의 상태를 확인합니다.


4. 실행 중인 경우 서비스를 중지하거나 다시 시작하지 마십시오. [데이터베이스 축소](#shrink-the-database) 단계를 건너뛰고 [실행 기록 데이터 삭제](#delete-run-history-data) 단계로 이동합니다.

5. 실행 중이 아닌 경우 서비스를 시작하세요. 서비스가 성공적으로 시작하는 경우 [데이터베이스 축소](#shrink-the-database) 단계를 건너뛰고 [실행 기록 데이터 삭제](#delete-run-history-data) 단계로 이동합니다. 그렇지 않으면 [데이터베이스 축소](#shrink-the-database) 단계를 계속합니다.

### <a name="shrink-the-database"></a>데이터베이스 축소
축소 작업을 사용하여 동기화 서비스를 시작하는 데 충분한 DB 공간을 확보합니다. 데이터베이스에서 공백을 제거하여 DB 공간을 확보합니다. 항상 공백을 복구할 수 있다고 보장되지 않으므로 이 단계가 최선적입니다. 축소 작업에 대한 자세한 내용은 이 [데이터베이스 축소](/sql/relational-databases/databases/shrink-a-database) 문서를 참조하세요.

> [!IMPORTANT]
> 동기화 서비스를 실행할 수 있는 경우 이 단계를 건너뜁니다. 늘어난 조각화로 인해 성능 저하가 발생할 수 있으므로 SQL DB를 축소하는 것은 좋지 않습니다.

Azure AD Connect에 대해 만든 데이터베이스의 이름은 **ADSync** 입니다. 축소 작업을 수행하려면 sysadmin 또는 데이터베이스의 DBO로 로그인해야 합니다. Azure AD Connect를 설치하는 동안 다음 계정에 sysadmin 권한이 부여됩니다.
* 로컬 관리자
* Azure AD Connect 설치를 실행하는 데 사용한 사용자 계정입니다.
* Azure AD Connect 동기화 서비스의 운영 컨텍스트로 사용되는 동기화 서비스 계정입니다.
* 설치 중 생성된 로컬 그룹 ADSyncAdmins입니다.

1. `%ProgramFiles%\Microsoft Azure AD Sync\Data` 아래에 위치한 **ADSync.mdf** 및 **ADSync_log.ldf** 파일을 복사하여 데이터베이스를 안전한 위치로 백업합니다.

2. 새 PowerShell 세션을 시작합니다.

3. `%ProgramFiles%\Microsoft SQL Server\110\Tools\Binn` 폴더로 이동합니다.

4. `./SQLCMD.EXE -S "(localdb)\.\ADSync" -U <Username> -P <Password>` 명령을 실행하고 sysadmin 또는 DBO 데이터베이스의 자격 증명을 사용하여 **sqlcmd** 유틸리티를 시작합니다.

5. 데이터베이스를 축소하려면 sqlcmd 프롬프트(`1>`)에서 `DBCC Shrinkdatabase(ADSync,1);`를 입력하고 그다음 줄에 `GO`를 입력합니다.

6. 작업이 성공한 경우 동기화 서비스를 다시 시작합니다. 동기화 서비스를 시작할 수 있는 경우 [실행 기록 데이터 삭제](#delete-run-history-data) 단계로 이동합니다. 그렇지 않은 경우 고객 지원으로 문의하세요.

### <a name="delete-run-history-data"></a>실행 기록 데이터 삭제
기본적으로 Azure AD Connect는 7일 동안 실행 기록 데이터를 유지합니다. 이 단계에서는 실행 기록 데이터를 삭제하여 Azure AD Connect 동기화 서비스가 동기화를 다시 시작할 수 있도록 DB 공간을 확보합니다.

1. 시작 → 동기화 서비스로 이동하여 **Synchronization Service Manager** 를 시작합니다.

2. **Operations** 탭으로 이동합니다.

3. **Actions** 아래에서 **Clear Runs** 를 선택합니다.

4. **Clear all runs** 또는 **Clear runs before… \<date>** 옵션 중에서 선택할 수 있습니다. 2일보다 오래된 실행 기록 데이터를 삭제하여 시작하는 것이 좋습니다. DB 크기 문제가 계속되면 **Clear all runs** 옵션을 선택합니다.

### <a name="shorten-retention-period-for-run-history-data"></a>실행 기록 데이터에 대한 보존 기간 단축
이 단계는 여러 동기화 주기 후 10GB 제한 문제의 발생 가능성을 줄이는 것입니다.

1. 새 PowerShell 세션을 엽니다.

2. `Get-ADSyncScheduler`를 실행하고 현재 보존 기간을 지정하는 PurgeRunHistoryInterval 속성을 기록해 둡니다.

3. `Set-ADSyncScheduler -PurgeRunHistoryInterval 2.00:00:00`을 실행하여 보존 기간을 2일로 설정합니다. 보존 기간을 적절하게 조정합니다.

## <a name="long-term-solution--migrate-to-full-sql"></a>장기 솔루션 – 전체 SQL로 마이그레이션
일반적으로 문제는 Azure AD Connect가 온-프레미스 Active Directory를 Azure AD로 동기화하는 데 10GB의 데이터베이스 크기는 더 이상 충분하지 않다는 것을 나타냅니다. SQL 서버의 전체 버전을 사용하도록 전환하는 것이 좋습니다. 기존 Azure AD Connect 배포의 LocalDB를 정식 버전의 SQL 데이터베이스와 직접 바꿀 수 없습니다. 대신 SQL의 정식 버전으로 새 Azure AD Connect 서버를 배포해야 합니다. 새 Azure AD Connect 서버(SQL DB로)가 기존 Azure AD Connect 서버(LocalDB로) 옆의 스테이징 서버로 배포되는 스윙 마이그레이션을 수행하는 것이 좋습니다. 
* Azure AD Connect로 원격 SQL을 구성하는 방법에 대한 지침은 [Azure AD Connect의 사용자 지정 설치](./how-to-connect-install-custom.md) 문서를 참조하세요.
* Azure AD Connect 업그레이드를 위한 스윙 마이그레이션에 대한 지침은 [Azure AD Connect:이전 버전에서 최신 버전으로 업그레이드](./how-to-upgrade-previous-version.md#swing-migration) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
