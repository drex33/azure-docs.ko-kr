---
title: Azure Backup 에이전트 문제 해결
description: 이 문서에서는 Azure Backup 에이전트의 설치 및 등록 문제를 해결하는 방법에 대해 알아봅니다.
ms.topic: troubleshooting
ms.date: 06/04/2021
ms.openlocfilehash: c3e253f04e74ed2e6042a905e4a8165af93d0012
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233190"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>MARS(Microsoft Azure Recovery Services) 에이전트 문제 해결

이 문서에서는 구성, 등록, 백업 및 복원하는 동안 발생할 수 있는 오류를 해결하는 방법에 대해 설명합니다.

## <a name="basic-troubleshooting"></a>기본 문제 해결

MARS(Microsoft Azure Recovery Services) 에이전트 문제 해결을 시작하기 전에 다음 사항을 확인하는 것이 좋습니다.

- [MARS 에이전트가 최신 상태인지 확인](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)합니다.
- [MARS 에이전트와 Azure 간에 네트워크 연결이 있는지 확인](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)합니다.
- 서비스 콘솔에서 MARS가 실행 중인지 확인합니다. 필요한 경우 다시 시작하고 작업을 다시 시도합니다.
- [스크래치 폴더 위치에서 5~10%의 볼륨 여유 공간을 사용할 수 있는지 확인](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)합니다.
- [다른 프로세스 또는 바이러스 백신 소프트웨어가 Azure Backup을 방해하는지 확인합니다](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- 백업 작업이 완료되었지만 경고가 표시되면 [경고와 함께 완료된 백업 작업](#backup-jobs-completed-with-warning)을 참조하세요.
- 예약 백업은 실패하지만 수동 백업은 작동하는 경우 [예약에 따라 백업이 실행되지 않음](#backups-dont-run-according-to-schedule)을 참조하세요.
- OS에 최신 업데이트가 설치되었는지 확인합니다.
- [미지원 특성이 포함된 지원되지 않는 드라이브 및 파일이 백업에서 제외되는지 확인](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)합니다.
- 보호되는 시스템의 클록이 올바른 표준 시간대로 구성되었는지 확인합니다.
- [.NET Framework 4.5.2 이상 버전이 서버에 설치되어 있는지 확인](https://www.microsoft.com/download/details.aspx?id=30653)합니다.
- 자격 증명 모음에 서버를 다시 등록하려는 경우:
  - 에이전트가 서버에서 제거되고 포털에서 삭제되었는지 확인합니다.
  - 처음 서버 등록에 사용한 것과 동일한 암호를 사용합니다.
- 오프라인 백업의 경우 백업을 시작하기 전에 원본 컴퓨터와 복사 컴퓨터 모두에 Azure PowerShell 3.7.0이 설치되어 있는지 확인합니다.
- 백업 에이전트가 Azure 가상 머신에서 실행 중인 경우 [이 문서](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)를 참조합니다.

## <a name="invalid-vault-credentials-provided"></a>잘못된 자격 증명 모음이 제공되었습니다.

**오류 메시지**: 잘못된 자격 증명 모음이 제공되었습니다. 파일이 손상되었거나 복구 서비스와 연결된 최신 자격 증명이 없습니다. (ID: 34513)

| 원인 | 권장 작업 |
| ---     | ---    |
| **자격 증명 모음 자격 증명이 유효하지 않음** <br/> <br/> 자격 증명 모음 파일이 손상되었거나, 만료되었거나, *.vaultCredentials* 와 다른 파일 확장명이 있을 수 있습니다. (예를 들어 등록 시점으로부터 10일 이전에 다운로드했을 수 있습니다.)| Azure Portal의 Recovery Services 자격 증명 모음에서 [새 자격 증명을 다운로드](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-)합니다. 그런 후에 다음 단계를 적절하게 수행합니다. <ul><li> MARS를 이미 설치하고 등록한 경우 Microsoft Azure Backup 에이전트 MMC 콘솔을 엽니다. 그런 다음 **작업** 창에서 **서버 등록** 을 선택하여 새 자격 증명으로 등록을 완료합니다. <br/> <li> 새로 설치하는 데 실패하는 경우 새 자격 증명을 사용하여 다시 설치해 봅니다.</ul> **참고**: 여러 자격 증명 모음 자격 증명 파일을 다운로드한 경우, 최신 파일만 다음 10일 동안 유효합니다. 새 자격 증명 모음 자격 증명 파일을 다운로드하는 것이 좋습니다.
| **프록시 서버/방화벽이 등록을 차단하고 있음** <br/>또는 <br/>**인터넷 연결 없음** <br/><br/> 머신의 인터넷 액세스가 제한되어 있고 방화벽, 프록시 및 네트워크 설정에서 FQDN 및 공용 IP 주소에 대한 액세스를 허용하도록 하지 않으면 등록이 실패합니다.| 다음 단계를 수행하세요.<br/> <ul><li> IT 팀과 협력하여 시스템이 인터넷에 연결되어 있는지 확인합니다.<li> 프록시 서버가 없는 경우 에이전트를 등록할 때 프록시 옵션이 선택되어 있지 않은지 확인합니다. [프록시 설정을 확인](#verifying-proxy-settings-for-windows)합니다.<li> 방화벽/프록시 서버를 사용하는 경우 네트워킹 팀과 협의하여 다음 FQDN 및 공용 IP 주소에 대한 액세스를 허용합니다. 위에 나열된 모든 URL 및 IP 주소에 대한 액세스는 포트 443에서 HTTPS 프로토콜을 사용하여 진행됩니다.<br/> <br> **URL**<br> `www.msftncsi.com` <br> `www.msftconnecttest.com` <br> \*.microsoft.com <br> \*.windowsazure.com <br> \*.microsoftonline.com <br>\*.windows.net<br><br>**IP 주소**<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/><li>미국 정부 고객인 경우 다음 URL에 대한 액세스 권한이 있는지 확인합니다.<br><br> `www.msftncsi.com` <br> \*.microsoft.com <br> \*.windowsazure.us <br> \*.microsoftonline.us <br> `*.windows.net` <br> \*.usgovcloudapi.net</li></ul></ul>위의 문제 해결 단계를 완료한 후 다시 등록해 봅니다.<br></br> Azure ExpressRoute를 통해 연결하는 경우에는 [Azure ExpressRoute 지원](../backup/backup-support-matrix-mars-agent.md#azure-expressroute-support)에 설명된 대로 설정을 구성합니다.
| **바이러스 백신 소프트웨어가 등록을 차단하고 있음** | 서버에 바이러스 백신 소프트웨어가 설치되어 있는 경우 바이러스 백신 검사에서 다음과 같은 파일 및 폴더에 대해 필요한 제외 규칙을 추가합니다. <br/><ul> <li> CBengine.exe <li> CSC.exe<li> 스크래치 폴더. 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch입니다. <li> bin 폴더(C:\Program Files\Microsoft Azure Recovery Services Agent\Bin).

### <a name="additional-recommendations"></a>추가 권장 사항

- C:/Windows/Temp로 이동하여 확장명이 .tmp인 파일이 60,000 또는 65,000개 넘게 있는지 확인합니다. 있는 경우 해당 파일을 삭제합니다.
- 머신의 날짜와 시간이 현지 표준 시간대와 일치하는지 확인합니다.
- [관련 사이트](install-mars-agent.md#verify-internet-access)가 Internet Explorer의 신뢰할 수 있는 사이트에 추가되었는지 확인합니다.

### <a name="verifying-proxy-settings-for-windows"></a>Windows에 대한 프록시 설정 확인

1. [Sysinternals](/sysinternals/downloads/psexec) 페이지에서 PsExec를 다운로드합니다.
1. 관리자 권한 명령 프롬프트에서 `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"`를 실행합니다.

   이 명령은 Internet Explorer를 엽니다.
1. **도구** > **인터넷 옵션** > **연결** > **LAN 설정** 으로 이동합니다.
1. 시스템 계정에 대한 프록시 설정을 확인합니다.
1. 프록시를 구성하지 않았는데 프록시 세부 정보가 있는 경우 해당 세부 정보를 제거합니다.
1. 프록시가 구성되어 있고 프록시 세부 정보가 잘못된 경우 **프록시 IP** 및 **포트** 세부 정보가 올바른지 확인합니다.
1. Internet Explorer를 닫습니다.

## <a name="unable-to-download-vault-credential-file"></a>자격 증명 모음 파일을 다운로드할 수 없음

| Error   | 권장 작업 |
| ---     | ---    |
|자격 증명 모음 파일을 다운로드하지 못했습니다. (ID: 403) | <ul><li> 다른 브라우저를 사용하여 자격 증명 모음을 다운로드해 보거나 다음 단계를 수행합니다. <ul><li> Internet Explorer를 시작합니다. F12를 선택합니다. </li><li> **네트워크** 탭으로 이동하여 캐시와 쿠키를 지웁니다. </li> <li> 페이지를 새로 고칩니다.<br></li></ul> <li> 구독이 사용 안 함/만료된 상태인지 확인합니다.<br></li> <li> 방화벽 규칙에서 다운로드를 차단하고 있는지 확인합니다. <br></li> <li> 자격 증명 모음의 한도(자격 증명 모음당 50개 머신)를 모두 사용하지 않았는지 확인합니다.<br></li>  <li> 사용자에게 자격 증명 모음 자격 증명을 다운로드하고 자격 증명 모음에 서버를 등록하는 데 필요한 Azure Backup 권한이 있는지 확인합니다. [Azure RBAC(역할 기반 액세스 제어)를 사용하여 Azure Backup 복구 지점 관리](backup-rbac-rs-vault.md)를 참조합니다.</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Recovery Services 에이전트에서 Microsoft Azure Backup에 연결할 수 없습니다.

| Error  | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure Recovery Services 에이전트에서 Microsoft Azure Backup에 연결할 수 없습니다. (ID: 100050) 네트워크 설정을 확인하고 인터넷에 연결할 수 있는지 확인하세요.<li>(407) 프록시 인증 필요. |프록시가 연결을 차단하고 있습니다. |  <ul><li>Internet Explorer에서 **도구** > **인터넷 옵션** > **보안** > **인터넷** 으로 이동합니다. **사용자 지정 수준** 을 선택하고 **파일 다운로드** 섹션까지 아래로 스크롤합니다. **사용** 을 선택합니다.<p>Internet Explorer에서 신뢰할 수 있는 사이트에 [URL 및 IP 주소](install-mars-agent.md#verify-internet-access)를 추가해야 할 수도 있습니다.<li>프록시 서버를 사용하도록 설정을 변경합니다. 그런 다음 프록시 서버 세부 정보를 제공합니다.<li> 머신의 인터넷 액세스가 제한된 경우 머신 또는 프록시의 방화벽 설정이 다음 [URL 및 IP 주소](install-mars-agent.md#verify-internet-access)를 허용하는지 확인합니다. <li>서버에 바이러스 백신 소프트웨어가 설치되어 있는 경우 다음 파일을 바이러스 백신 검사에서 제외합니다. <ul><li>CBEngine.exe(dpmra.exe 대신)<li>CSC.exe(.NET Framework와 관련됨) 서버에 설치된 모든 .NET Framework 버전에 대한 CSC.exe 파일이 있습니다. 영향을 받는 서버의 모든 .NET Framework 버전에 대한 CSC.exe 파일을 제외합니다. <li>스크래치 폴더 또는 캐시 위치. <br>스크래치 폴더 또는 캐시 경로의 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch입니다.<li>bin 폴더(C:\Program Files\Microsoft Azure Recovery Services Agent\Bin).

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>지정된 자격 증명 모음 자격 증명 파일은 이 서버와 연결된 자격 증명 모음에서 다운로드하지 않아 사용할 수 없습니다.

| Error  | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| 지정된 자격 증명 모음 자격 증명 파일은 이 서버와 연결된 자격 증명 모음에서 다운로드하지 않아 사용할 수 없습니다. (ID: 100110) 적합한 자격 증명 모음 자격 증명을 제공하세요. | 자격 증명 모음 자격 증명 파일은 이 서버가 이미 등록된 자격 증명 모음과 다른 자격 증명 모음에 있습니다. | 대상 머신 및 원본 머신이 동일한 Recovery Services 자격 증명 모음에 등록되었는지 확인합니다. 대상 서버가 다른 자격 증명 모음에 이미 등록된 경우 **서버 등록** 옵션을 사용하여 올바른 자격 증명 모음에 등록합니다.

## <a name="backup-jobs-completed-with-warning"></a>경고와 함께 완료된 백업 작업

- 파일 및 폴더를 백업하는 동안 MARS 에이전트가 반복되면 백업이 완료된 것으로 표시될 수 있는 다양한 조건이 발생할 수 있습니다. 이런 조건에서는 작업이 경고와 함께 완료로 표시됩니다. 이는 문제가 없지만 하나 이상의 파일을 백업할 수 없음을 의미합니다. 따라서 작업이 해당 파일을 건너뛰었으나 데이터 원본에서 문제의 다른 모든 파일을 백업했습니다.

  ![경고와 함께 완료된 백업 작업](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- 백업에서 파일을 건너뛸 수 있는 조건은 다음과 같습니다.
  - 지원되지 않는 파일 특성(예: OneDrive 폴더, 압축된 스트림, 재분석 지점의 경우) 전체 목록은 [지원 매트릭스](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup)를 참조하세요.
  - 파일 시스템 문제
  - 다른 프로세스의 방해(예를 들어 파일에 대한 핸들을 보유하는 바이러스 백신 소프트웨어는 MARS 에이전트가 파일에 액세스하지 못하도록 차단할 수 있음)
  - 애플리케이션에 의해 잠긴 파일

- 백업 서비스는 다음 명명 규칙을 사용하여 *C:\Program Files\Microsoft Azure Recovery Service Agent\temp* 폴더의 *LastBackupFailedFilesxxxx.txt* 와 같은 파일을 실패한 것으로 로그 파일에 표시합니다.
- 문제를 해결하려면 로그 파일을 검토하여 문제의 근원을 파악합니다.

  | 오류 코드             | 이유                                             | 권장 사항                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | 파일 또는 디렉터리가 손상되어 읽을 수 없습니다. | 원본 볼륨에서 **chkdsk** 를 실행합니다.                             |
  | 0x80070002, 0x80070003 | 시스템에서 지정된 파일을 찾을 수 없습니다.         | [스크래치 폴더가 꽉 차지 않았는지 확인](./backup-azure-file-folder-backup-faq.yml)합니다.  <br><br>  스크래치 공간이 구성된 볼륨이 있는지 확인합니다(삭제 안 됨).  <br><br>   [MARS 에이전트가 머신에 설치된 바이러스 백신에서 제외되어 있는지 확인](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)합니다.  |
  | 0x80070005             | 액세스 거부됨                                    | [바이러스 백신 또는 기타 타사 소프트웨어가 액세스를 차단하는지 확인](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)합니다.     |
  | 0x8007018b             | 클라우드 파일에 대한 액세스가 거부되었습니다.                | OneDrive 파일, Git 파일 또는 머신에서 오프라인 상태일 수 있는 기타 파일 |

- [기존 정책에 제외 규칙 추가](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy)를 사용하여 백업 정책에서 지원되지 않거나 누락되거나 삭제된 파일을 제외하고 백업이 성공적으로 수행되도록 할 수 있습니다.

- 최상위 폴더에서 동일한 이름의 보호된 폴더를 삭제하거나 다시 만들지 마세요. 이렇게 하면 ‘중요한 불일치가 감지되어 변경 내용을 복제할 수 없습니다’라는 오류를 포함한 경고와 함께 백업이 완료될 수 있습니다.  폴더를 삭제하고 다시 만들어야 하는 경우 보호된 최상위 폴더의 하위 폴더에서 해당 작업을 수행하는 것이 좋습니다.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>안전한 백업을 위한 암호화 키를 설정하지 못했습니다.

| Error | 가능한 원인 | 권장 작업 |
| ---     | ---     | ---    |
| <br />안전한 백업을 위한 암호화 키를 설정하지 못했습니다. 활성화가 완전히 성공한 것은 아니지만 암호화 암호가 다음 파일에 저장되었습니다. |<li>서버가 다른 자격 증명 모음에 이미 등록되어 있습니다.<li>구성하는 동안 암호가 손상되었습니다.| 자격 증명 모음에서 서버의 등록을 취소하고 새 암호로 다시 등록합니다.

## <a name="the-activation-did-not-complete-successfully"></a>정품 인증이 완료되지 않았습니다.

| Error  | 가능한 원인 | 권장 작업 |
|---------|---------|---------|
|<br />정품 인증이 완료되지 않았습니다. 내부 서비스 오류 [0x1FC07]로 인해 현재 작업이 실패했습니다. 잠시 후 작업을 다시 시도하세요. 문제가 지속되면 Microsoft 지원에 문의하세요.     | <li> 스크래치 폴더가 공간이 부족한 볼륨에 있습니다. <li> 스크래치 폴더가 잘못 이동되었습니다. <li> OnlineBackup.KEK 파일이 없습니다.         | <li>[최신 버전](https://aka.ms/azurebackup_agent)의 MARS 에이전트로 업그레이드합니다.<li>스크래치 폴더 또는 캐시 위치를 백업 데이터 전체 크기의 5~10%에 사이의 여유 공간이 있는 볼륨으로 이동합니다. 캐시 위치를 올바르게 이동하려면 [파일 및 폴더 백업에 대한 일반적인 질문](./backup-azure-file-folder-backup-faq.yml)에 나오는 단계를 참조하세요.<li> OnlineBackup.KEK 파일이 있는지 확인합니다. <br>*스크래치 폴더 또는 캐시 경로의 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch입니다*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>암호화 암호가 올바르게 구성되지 않았습니다.

| Error  | 가능한 원인 | 권장 작업 |
|---------|---------|---------|
| <br />34506 오류입니다. 이 컴퓨터에 저장된 암호화 암호가 제대로 구성되지 않았습니다.    | <li> 스크래치 폴더가 공간이 부족한 볼륨에 있습니다. <li> 스크래치 폴더가 잘못 이동되었습니다. <li> OnlineBackup.KEK 파일이 없습니다.        | <li>[최신 버전](https://aka.ms/azurebackup_agent)의 MARS 에이전트로 업그레이드합니다.<li>스크래치 폴더 또는 캐시 위치를 백업 데이터 전체 크기의 5~10%에 사이의 여유 공간이 있는 볼륨으로 이동합니다. 캐시 위치를 올바르게 이동하려면 [파일 및 폴더 백업에 대한 일반적인 질문](./backup-azure-file-folder-backup-faq.yml)에 나오는 단계를 참조하세요.<li> OnlineBackup.KEK 파일이 있는지 확인합니다. <br>*스크래치 폴더 또는 캐시 경로의 기본 위치는 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch입니다*.         |

## <a name="backups-dont-run-according-to-schedule"></a>백업이 일정에 따라 실행되지 않음

예약 백업은 자동으로 트리거되지 않지만 수동 백업이 문제없이 실행되는 경우 다음 작업을 시도해 봅니다.

- Windows Server 백업 일정이 Azure 파일 및 폴더 백업 일정과 충돌하지 않는지 확인합니다.

- 온라인 백업 상태가 **사용** 으로 설정되어 있는지 확인합니다. 상태를 확인하려면 다음 단계를 수행합니다.

  1. 작업 스케줄러에서 **Microsoft** 를 확장하고 **온라인 백업** 을 선택합니다.
  1. **Microsoft OnlineBackup** 을 두 번 클릭하고 **트리거** 탭으로 이동합니다.
  1. 상태가 **사용** 으로 설정되어 있는지 확인합니다. 설정되어 있지 않으면 **편집** 을 선택하고 **사용** 을 선택한 다음 **확인** 을 선택합니다.

- 작업 실행을 위해 선택한 사용자 계정이 **시스템** 또는 서버의 **로컬 관리자 그룹** 에 해당하는지 확인합니다. 사용자 계정을 확인하려면 **일반** 탭으로 이동하고 **보안** 옵션을 확인합니다.

- PowerShell 3.0 이상이 서버에 설치되었는지 확인합니다. PowerShell 버전을 확인하려면 다음 명령을 실행하고 `Major` 버전 번호가 3 이상인지 확인합니다.

  `$PSVersionTable.PSVersion`

- 다음 경로가 `PSMODULEPATH` 환경 변수에 속하는지 확인합니다.

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- `LocalMachine`에 대한 PowerShell 실행 정책이 `restricted`로 설정된 경우 백업 작업을 트리거하는 PowerShell cmdlet이 실패할 수 있습니다. 관리자 권한 모드로 다음 명령을 실행하고 실행 정책을 `Unrestricted` 또는 `RemoteSigned`로 설정합니다.

```powershell
Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
```

- PowerShell 모듈 MSOnlineBackup 파일이 없거나 손상되지 않았는지 확인합니다. 파일이 없거나 손상된 경우 다음 단계를 수행합니다.

  1. 제대로 작동하는 MARS 에이전트가 있는 머신에서 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules의 MSOnlineBackup 폴더를 복사합니다.
  1. 문제가 있는 머신에서 복사한 파일을 동일한 폴더 위치(C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules)에 붙여넣습니다.

    머신에 MSOnlineBackup 폴더가 이미 있는 경우 파일을 붙여넣거나 기존 파일을 바꿉니다.

> [!TIP]
> 변경 내용이 일관되게 적용되도록 위의 단계를 수행한 후 서버를 다시 시작합니다.

## <a name="resource-not-provisioned-in-service-stamp"></a>리소스가 서비스 스탬프에 프로비저닝되지 않음

Error | 가능한 원인 | 권장 작업
--- | --- | ---
내부 서비스 오류 “리소스가 서비스 스탬프에 프로비저닝되지 않음”으로 인해 현재 작업이 실패했습니다. 잠시 후 작업을 다시 시도하세요. (ID: 230006) | 보호된 서버의 이름이 바뀌었습니다. | <li> 자격 증명 모음에 등록된 원래 이름으로 다시 서버 이름을 바꿉니다. <br> <li> 새 이름을 사용하여 자격 증명 모음에 서버를 다시 등록합니다.

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>다른 작업이 진행 중이므로 작업을 시작할 수 없음

**MARS 콘솔** > **작업 기록** 에 "다른 작업이 진행 중이므로 작업을 시작할 수 없음"이라는 경고 메시지가 표시되는 경우 이는 작업 스케줄러에 의해 트리거된 작업의 중복 인스턴스 때문일 수 있습니다.

![다른 작업이 진행 중이므로 작업을 시작할 수 없음](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

이 문제를 해결하려면:

1. 실행 창에 *taskschd.msc* 를 입력하여 작업 스케줄러 스냅인을 시작합니다.
1. 왼쪽 창에서 **작업 스케줄러 라이브러리** -> **Microsoft** -> **OnlineBackup** 으로 이동합니다.
1. 이 라이브러리의 각 태스크에 대해 작업을 두 번 클릭하여 속성을 열고 다음 단계를 수행합니다.
    1. **설정** 탭으로 전환합니다.

         ![설정 탭](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. **태스크가 이미 실행 중인 경우에 대한 옵션을 변경하면 다음 규칙이 적용** 됩니다. **새 인스턴스 시작 안 함** 을 선택합니다.

         ![새 인스턴스 시작 안 함으로 규칙 변경](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>복원 문제 해결

몇 분 후에도 Azure Backup이 복구 볼륨을 성공적으로 탑재하지 않을 수 있습니다. 그리고 프로세스 중에 오류 메시지가 표시될 수도 있습니다. 정상적으로 복구를 시작하려면 다음 단계를 수행합니다.

1. 몇 분 동안 실행되고 있는 경우 탑재 프로세스를 취소합니다.

2. 최신 버전의 백업 에이전트가 있는지 확인합니다. 버전을 확인하려면 MARS 콘솔의 **작업** 창에서 **Microsoft Azure Recovery Services 에이전트 정보** 를 선택합니다. **버전** 번호가 [이 문서](https://go.microsoft.com/fwlink/?linkid=229525)에 언급된 버전보다 높거나 같은지 확인합니다. 이 링크를 선택하여 [최신 버전을 다운로드](https://go.microsoft.com/fwLink/?LinkID=288905)합니다.

3. **디바이스 관리자** > **스토리지 컨트롤러** 로 이동하여 **Microsoft iSCSI 초기자** 를 찾습니다. 찾은 경우 바로 7단계로 이동합니다.

4. Microsoft iSCSI 초기자 서비스를 찾을 수 없는 경우 하드웨어 ID **ROOT\ISCSIPRT** 를 포함한 **알 수 없는 디바이스** 라는 항목을 **디바이스 관리자** > **스토리지 컨트롤러** 에서 찾을 수 있는지 확인합니다.

5. **알 수 없는 디바이스** 를 마우스 오른쪽 단추로 클릭하고 **업데이트 드라이버 소프트웨어** 를 선택합니다.

6. **업데이트된 드라이버 소프트웨어를 자동으로 검색** 하는 옵션을 선택하여 드라이버를 업데이트합니다. 이 업데이트는 **알 수 없는 디바이스** 를 **Microsoft iSCSI 초기자** 로 변경해야 합니다.

    ![스토리지 컨트롤러가 강조 표시된 Azure Backup 디바이스 관리자의 스크린샷](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. **작업 관리자** > **서비스(로컬)**  > **Microsoft iSCSI 초기자 서비스** 로 이동합니다.

    ![서비스(로컬)가 강조 표시된 Azure Backup 작업 관리자의 스크린샷](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Microsoft iSCSI 초기자 서비스를 다시 시작합니다. 이렇게 하려면 해당 서비스를 마우스 오른쪽 단추로 클릭하고 **중지** 를 선택합니다. 그런 다음 다시 마우스 오른쪽 단추를 클릭하고 **시작** 을 선택합니다.

9. [인스턴트 복원](backup-instant-restore-capability.md)을 사용하여 복구를 다시 시도합니다.

복구가 계속 실패하면 서버 또는 클라이언트를 다시 시작합니다. 다시 시작하지 않으려는 경우 또는 서버를 다시 시작한 후에도 복구가 계속 실패하는 경우 [다른 머신에서 복구](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)를 시도해 봅니다.

## <a name="troubleshoot-cache-problems"></a>캐시 문제 해결

캐시 폴더(스크래치 폴더라고도 함)가 잘못 구성되었거나, 필수 구성 요소가 없거나, 액세스가 제한된 경우 백업 작업이 실패할 수 있습니다.

### <a name="prerequisites"></a>필수 구성 요소

MARS 에이전트 작업을 성공적으로 수행하려면 캐시 폴더가 다음 요구 사항을 준수해야 합니다.

- [스크래치 폴더 위치에서 5~10%의 볼륨 여유 공간을 사용할 수 있어야 함](backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- [스크래치 폴더 위치가 유효하고 액세스 가능한지 확인](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)
- [캐시 폴더의 파일 특성을 지원해야 함](backup-azure-file-folder-backup-faq.yml#are-there-any-attributes-of-the-cache-folder-that-aren-t-supported-)
- [할당된 섀도 복사본 스토리지 공간이 백업 프로세스에 충분해야 함](#increase-shadow-copy-storage)
- [캐시 폴더에 대한 액세스를 제한하는 다른 프로세스(예: 바이러스 백신 소프트웨어)가 없어야 함](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>섀도 복사본 스토리지 늘리기

데이터 원본을 보호하는 데 필요한 섀도 복사본 스토리지 공간이 충분하지 않은 경우 백업 작업이 실패할 수 있습니다. 이 문제를 해결하려면 아래와 같이 **vssadmin** 을 사용하여 보호된 볼륨의 섀도 복사본 스토리지 공간을 늘립니다.

- 관리자 권한 명령 프롬프트에서 현재 섀도 스토리지 공간을 확인합니다.<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- 다음 명령을 사용하여 섀도 스토리지 공간을 늘립니다.<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>캐시 폴더에 대한 액세스를 차단하는 다른 프로세스 또는 바이러스 백신 소프트웨어

서버에 바이러스 백신 소프트웨어가 설치되어 있는 경우 바이러스 백신 검사에서 다음과 같은 파일 및 폴더에 대해 필요한 제외 규칙을 추가합니다.

- 스크래치 폴더. 기본 위치: `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
- bin 폴더 위치: `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>일반적인 문제

이 섹션에서는 MARS 에이전트를 사용하는 동안 발생할 수 있는 일반적인 오류에 대해 설명합니다.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

오류 메시지 | 권장 작업
--|--
Microsoft Azure Recovery Services 에이전트가 스크래치 위치에 저장된 백업 체크섬에 액세스할 수 없습니다. | 이 문제를 해결하려면 다음 단계를 수행하고 서버를 다시 시작합니다. <br/> - [스크래치 위치 파일을 잠그는 바이러스 백신 또는 다른 프로세스가 있는지 확인](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [스크래치 위치가 유효하고 MARS 에이전트에서 액세스할 수 있는지 확인](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

오류 메시지 | 권장 작업
--|--
Microsoft Azure Recovery Services 에이전트가 VHD를 초기화하기 위해 스크래치 위치에 액세스할 수 없음 | 이 문제를 해결하려면 다음 단계를 수행하고 서버를 다시 시작합니다. <br/> - [바이러스 백신 또는 다른 프로세스가 스크래치 위치 파일을 잠그는지 확인](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [스크래치 위치가 유효하고 MARS 에이전트에서 액세스할 수 있는지 확인](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

오류 메시지 | 권장 작업
--|--
스크래치 폴더가 있는 볼륨에 스토리지가 부족하여 백업하지 못했음 | 이 문제를 해결하려면 다음 단계를 확인하고 작업을 다시 시도합니다.<br/>- [MARS 에이전트가 최신 상태인지 확인](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [백업 스크래치 공간에 영향을 주는 스토리지 문제 확인 및 해결](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

오류 메시지 | 권장 작업
--|--
파일에 변경 내용을 찾을 수 없습니다. 이는 다양한 이유 때문일 수 있습니다. 작업을 다시 시도하세요. | 이 문제를 해결하려면 다음 단계를 확인하고 작업을 다시 시도합니다.<br/> - [MARS 에이전트가 최신 상태인지 확인](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [백업 스크래치 공간에 영향을 주는 스토리지 문제 확인 및 해결](#prerequisites)

## <a name="next-steps"></a>다음 단계

- [Azure Backup 에이전트로 Windows Server를 백업하는 방법](tutorial-backup-windows-server-to-azure.md)에서 자세한 내용을 참조하세요.
- 백업을 복원해야 하는 경우 [Windows 머신으로 파일 복원](backup-azure-restore-windows-server.md)을 참조하세요.
