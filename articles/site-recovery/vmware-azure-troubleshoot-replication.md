---
title: Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구에 대한 복제 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용한 VMware VM 및 물리적 서버와 Azure 간 재해 복구 중에 일반적인 복제 문제를 해결하는 방법을 제공합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: 6c98b3f5090dcc14dea92e5bcfa4b6c751225c5f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008867"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>VMware VM 및 실제 서버에 대한 복제 문제 해결

이 문서에서는 [Site Recovery](site-recovery-overview.md)를 사용하여 온-프레미스 VMware VM 및 실제 서버를 Azure에 복제하는 경우 발생할 수 있는 몇 가지 일반적인 문제 및 특정 오류를 설명합니다.

## <a name="step-1-monitor-process-server-health"></a>1단계: 프로세스 서버 상태 모니터링

Site Recovery는 [프로세스 서버](vmware-physical-azure-config-process-server-overview.md#process-server)를 사용하여 복제된 데이터를 수신하고 최적화한 후 Azure로 보냅니다.

포털에서 프로세스 서버의 상태를 모니터링하여 연결되어 있고 제대로 작동하는지와 프로세스 서버와 연결된 원본 머신에 대한 복제가 진행되고 있는지 확인하는 것이 좋습니다.

- 프로세스 서버 모니터링에 대한 [정보](vmware-physical-azure-monitor-process-server.md)
- [모범 사례 검토](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- 프로세스 서버 상태의 [문제 해결](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health)

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>2단계: 연결 및 복제 문제 해결

종종 원본 서버와 프로세스 서버 간 또는 프로세스 서버와 Azure 간의 연결 문제로 인해 초기 및 진행 중인 복제가 실패할 수 있습니다.

이러한 문제를 해결하려면 [연결 및 복제 문제를 해결합니다](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication).




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>3단계: 복제에 사용할 수 없는 원본 머신 문제 해결

Site Recovery를 사용하여 복제를 사용하도록 설정할 원본 컴퓨터를 선택하려고 하면 다음과 같은 이유 중 하나로 컴퓨터를 계속 사용하지 못할 수 있습니다.

* **인스턴스 UUID가 같은 두 개의 가상 머신**: vCenter에 인스턴스 UUID가 같은 두 개의 가상 머신이 있는 경우 구성 서버에서 첫 번째로 검색된 가상 머신이 Azure Portal에 표시됩니다. 이 문제를 해결하려면 두 개의 가상 머신에 동일한 인스턴스 UUID가 없는지 확인합니다. 이 시나리오는 백업 VM이 활성화되고 검색 레코드에 기록되는 경우에 일반적으로 발생합니다. [Azure Site Recovery VMware-Azure: 중복되거나 오래된 항목을 정리하는 방법](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)을 참조하세요.
* **잘못된 vCenter 사용자 자격 증명**: OVF 템플릿 또는 통합 설정을 사용하여 구성 서버를 설정하는 동안 올바른 vCenter 자격 증명을 추가했는지 확인합니다. 설치 중에 추가한 자격 증명을 확인하려면 [자동 검색에 대한 자격 증명 수정](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery)을 참조하세요.
* **vCenter 권한 부족**: vCenter에 액세스하기 위해 제공된 권한에 필요한 사용 권한이 없으면 가상 머신을 검색하지 못할 수 있습니다. [자동 검색용 계정 준비](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)에 설명된 사용 권한을 vCenter 사용자 계정에 추가해야 합니다.
* **Azure Site Recovery 관리 서버**: 구성 서버/스케일 아웃 프로세스 서버/마스터 대상 서버 역할 중 하나 이상에서 가상 머신이 관리 서버로 사용되는 경우 포털에서 가상 머신을 선택할 수 없습니다. 관리 서버는 복제할 수 없습니다.
* **Azure Site Recovery 서비스를 통해 이미 보호/장애 조치(Failover)됨**: Site Recovery를 통해 가상 머신을 이미 보호 또는 장애 조치하는 경우 포털에서 보호하기 위해 가상 머신을 선택할 수 없습니다. 포털에서 찾고 있는 가상 머신이 다른 사용자 또는 다른 구독을 통해 아직 보호되고 있지 않은지 확인합니다.
* **연결되지 않은 vCenter** VCenter가 연결된 상태인지 확인합니다. 확인하려면 [Recovery Services 가격 증명 모음] > [Site Recovery 인프라] > [구성 서버]로 이동하고 > 해당 구성 서버를 클릭하면 > 연결된 서버의 세부 정보와 함께 블레이드가 오른쪽에 열립니다. VCenter가 연결되었는지 확인합니다. “연결되지 않음” 상태인 경우 문제를 해결한 다음, 포털에서 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.
* **ESXi 전원 꺼짐**: 가상 머신이 상주하는 ESXi 호스트의 전원이 꺼진 상태이면 가상 머신이 Azure Portal에 나열되지 않거나 선택할 수 없습니다. ESXi 호스트 전원을 켜고 포털에서 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.
* **보류 중인 재부팅**: 가상 머신에 보류 중인 재부팅이 있는 경우 Azure Portal에서 머신을 선택할 수 없습니다. 보류 중인 재부팅 활동을 완료하고 [구성 서버를 새로 고쳐야 합니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.
* **IP 찾을 수 없음**: 가상 머신에 연결된 유효한 IP 주소가 없는 경우 Azure Portal에서 머신을 선택할 수 없습니다. 가상 머신에 유효한 IP 주소를 할당하고 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server). 그러면 포털에 가상 머신이 나열됩니다.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>포털에 회색으로 표시된 보호된 가상 머신 문제 해결

Site Recovery에서 복제된 가상 머신은 시스템에 중복된 항목이 있으면 Azure Portal에서 사용할 수 없습니다. 오래된 항목을 삭제하고 이 문제를 해결하는 방법에 대한 자세한 내용은 [Azure Site Recovery VMware-Azure: 중복되거나 오래된 항목을 정리하는 방법](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx)을 참조하세요.

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>지난 ‘XXX’분 동안 VM에 사용할 수 있는 크래시 일관성 복구 지점이 없습니다.

가장 일반적인 몇 가지 문제는 다음과 같습니다.

### <a name="initial-replication-issues-error-78169"></a>초기 복제 문제 [오류 78169]

위 오류의 경우 연결, 대역폭 또는 시간 동기화 관련 문제가 없으면 다음을 확인합니다.

- 바이러스 백신 소프트웨어가 Azure Site Recovery를 차단하고 있지 않습니다. Azure Site Recovery에 필요한 폴더 제외에 대해 [자세히](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) 알아보세요.

### <a name="source-machines-with-high-churn-error-78188"></a>높은 변동의 원본 머신 [오류 78188]

가능한 원인:
- 나열된 가상 머신의 디스크에서 데이터 변경률(초당 쓰기 바이트)이 선택한 복제 대상 스토리지 계정 형식에 대한 [Azure Site Recovery 지원 제한](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits)보다 큽니다.
- 업로드 보류 중인 데이터양이 많아 변동률이 급격하게 변동되었습니다.

이 문제를 해결하려면
- 원본의 변동률 요구 사항에 따라 대상 스토리지 계정 유형(표준 또는 프리미엄)이 프로비저닝되었는지 확인합니다.
- 프리미엄 관리 디스크(asrseeddisk 유형)에 이미 복제하고 있는 경우 디스크 크기가 Site Recovery 제한에 따라 관찰된 변동률을 지원하는지 확인합니다. 필요한 경우 asrseeddisk 크기를 늘릴 수 있습니다. 아래 단계를 수행하세요.
    - 영향을 받는 복제된 머신의 디스크 블레이드로 이동하여 복제본 디스크 이름을 복사합니다.
    - 이 복제본 관리 디스크로 이동합니다.
    - 개요 블레이드에 SAS URL이 생성되었다는 배너가 표시될 수 있습니다. 이 배너를 클릭하고 내보내기를 취소합니다. 배너가 표시되지 않으면 이 단계를 무시합니다.
    - SAS URL이 호출되는 즉시 관리 디스크의 구성 블레이드로 이동하여 Azure Site Recovery가 원본 디스크에서 관찰된 변동을 지원하도록 크기를 늘립니다.
- 관찰된 변동이 일시적인 경우 보류 중인 데이터 업로드를 처리하고 복구 지점이 생성될 때까지 몇 시간이 걸릴 수 있습니다.
- 디스크가 임시 로그, 테스트 데이터 등의 중요하지 않은 데이터를 포함하는 경우 이 데이터를 다른 곳으로 이동하거나 복제에서 이 디스크를 완전히 제외하는 것이 좋습니다.
- 문제가 지속되면 Site Recovery [Deployment Planner](site-recovery-deployment-planner.md#overview)를 사용하여 복제 계획을 세울 수 있습니다.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>하트비트가 없는 원본 머신 [오류 78174]

이 오류는 원본 머신의 Azure Site Recovery Mobility 에이전트가 CS(구성 서버)와 통신하지 않을 때 발생합니다.

이 문제를 해결하려면 다음 단계에 따라 원본 VM에서 구성 서버로의 네트워크 연결을 확인합니다.

1. 원본 머신이 실행 중인지 확인합니다.
2. 관리자 권한이 있는 계정을 사용하여 원본 머신에 로그인합니다.
3. 다음 서비스가 실행 중인지 확인하고, 실행 중이지 않은 경우 서비스를 다시 시작합니다.
   - Svagents(InMage Scout VX 에이전트)
   - InMage Scout Application 서비스
4. 원본 머신에서 오류 세부 정보에 대한 위치에서 로그를 검사합니다.

    *C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents\*.log*

### <a name="process-server-with-no-heartbeat-error-806"></a>하트비트가 없는 프로세스 서버 [오류 806]
PS(프로세스 서버)에서 하트비트가 없는 경우 다음을 확인합니다.
1. PS VM이 실행되고 있습니다.
2. PS에서 오류 세부 정보에 대한 다음 로그를 확인합니다.

    *C:\ProgramData\ASR\home\svsystems\eventmanager\*.log*\
    및\
    *C:\ProgramData\ASR\home\svsystems\monitor_protection\*.log*

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>하트비트가 없는 마스터 대상 서버 [오류 78022]

이 오류는 마스터 대상의 Azure Site Recovery Mobility 에이전트가 구성 서버와 통신하지 않을 때 발생합니다.

이 문제를 해결하려면 다음 단계에 따라 서비스 상태를 확인합니다.

1. 마스터 대상 VM이 실행 중인지 확인합니다.
2. 관리자 권한이 있는 계정을 사용하여 마스터 대상 VM에 로그인합니다.
    - svagents 서비스가 실행 중인지 확인합니다. 실행 중이라면 서비스를 다시 시작합니다.
    - 위치의 로그에서 오류 정보를 확인합니다.

        *C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents\*.log*
3. 마스터 대상을 구성 서버에 등록하려면 **%PROGRAMDATA%\ASR\Agent** 폴더로 이동하고 명령 프롬프트에서 다음을 실행합니다.
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>오류 ID 78144 - 지난 ‘XXX’분 동안 VM에 사용할 수 있는 앱 일관성 복구 지점이 없습니다.

모바일 에이전트 [9.23](vmware-physical-mobility-service-overview.md#mobility-service-agent-version-923-and-higher) & [9.27](site-recovery-whats-new.md#update-rollup-39) 버전에서 VSS 설치 오류 동작을 처리할 수 있도록 기능이 향상되었습니다. VSS 오류 문제 해결에 대한 모범 지침을 사용하려면 최신 버전인지 확인하세요.

가장 일반적인 몇 가지 문제는 다음과 같습니다.

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>원인 1: SQL Server 2008/2008 R2의 알려진 문제
**해결 방법**: SQL Server 2008/2008 R2의 알려진 문제가 있습니다. 기술 자료 문서 [Azure Site Recovery 에이전트 또는 기타 구성 요소가 아닌 VSS 백업이 SQL Server 2008 R2를 호스트하는 서버에서 실패함](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)을 참조하세요.

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>원인 2: AUTO_CLOSE DB를 사용하여 모든 버전의 SQL Server 인스턴스를 호스트하는 서버에서 Azure Site Recovery 작업이 실패함
**해결 방법**: 기술 자료 [문서](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)를 참조하세요.


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>원인 3: SQL Server 2016 및 2017의 알려진 문제
**해결 방법**: 기술 자료 [문서](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component)를 참조하세요.

#### <a name="cause-4-app-consistency-not-enabled-on-linux-servers"></a>원인 4: Linux 서버에서 앱 일관성이 사용하도록 설정되지 않음
**해결 방법**: Linux 운영 체제용 Azure Site Recovery는 앱 일관성을 위한 애플리케이션 사용자 지정 스크립트를 지원합니다. 사전 및 사후 옵션이 포함된 사용자 지정 스크립트는 앱 일관성을 위해 Azure Site Recovery 모바일 에이전트에서 사용됩니다. 사용하도록 설정하는 단계는 [여기](./site-recovery-faq.yml)를 참조하세요.

### <a name="more-causes-due-to-vss-related-issues"></a>VSS 관련 문제로 인한 추가 원인:

추가로 문제를 해결하려면 원본 머신의 파일을 확인하여 오류에 대한 정확한 오류 코드를 확인합니다.

*C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log*

파일에서 오류를 찾으려면
편집기에서 vacp.log 파일을 열어 “vacpError” 문자열을 검색합니다.

`Ex: `**`vacpError`**`:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|`

위의 예제에서 **2147754994** 는 아래와 같이 오류를 알려 주는 오류 코드입니다.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS 기록기가 설치되어 있지 않음 - 오류 2147221164

해결 방법: 애플리케이션 일관성 태그를 생성하려면 Azure Site Recovery에서 Microsoft VSS(볼륨 섀도 복사본 서비스)를 사용합니다. 그러면 애플리케이션 일관성 스냅샷을 찍는 작업을 위한 VSS 공급자를 설치합니다. 이 VSS 공급자는 서비스로 설치됩니다. VSS 공급자 서비스가 설치되지 않은 경우 애플리케이션 일관성 스냅샷 만들기에 실패하고 오류 ID 0x80040154 “클래스가 등록되지 않았습니다.”가 표시됩니다. </br>
[VSS 기록기 설치 문제 해결에 대한 문서](./vmware-azure-troubleshoot-push-install.md#vss-installation-failures)를 참조하세요.

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS 기록기를 사용할 수 없음 - 오류 2147943458

**해결 방법**: 애플리케이션 일관성 태그를 생성하려면 Azure Site Recovery에서 Microsoft VSS(볼륨 섀도 복사본 서비스)를 사용합니다. 그러면 애플리케이션 일관성 스냅샷을 찍는 작업을 위한 VSS 공급자를 설치합니다. 이 VSS 공급자는 서비스로 설치됩니다. VSS 공급자 서비스가 사용하지 않도록 설정된 경우 애플리케이션 일관성 스냅샷 만들기에 실패하고 오류 ID “지정된 서비스가 사용 안 함으로 지정되어 있어 시작될 수 없습니다(0x80070422).”가 표시됩니다. </br>

- VSS를 사용할 수 없는 경우
    - VSS 공급자 서비스의 시작 유형이 **자동** 으로 설정되어 있는지 확인합니다.
    - 다음 서비스를 다시 시작합니다.
        - VSS 서비스
        - Azure Site Recovery VSS 공급자
        - VDS 서비스

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - 오류 2147754756

**해결 방법**: 애플리케이션 일관성 태그를 생성하려면 Azure Site Recovery에서 Microsoft VSS(볼륨 섀도 복사본 서비스)를 사용합니다.
Azure Site Recovery VSS 공급자 서비스가 설치되었는지 확인합니다. </br>

- 다음 명령을 사용하여 공급자 설치를 다시 시도합니다.
- 기존 공급자 제거: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- 다시 설치: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd

VSS 공급자 서비스의 시작 유형이 **자동** 으로 설정되어 있는지 확인합니다.
    - 다음 서비스를 다시 시작합니다.
        - VSS 서비스
        - Azure Site Recovery VSS 공급자
        - VDS 서비스

## <a name="error-id-95001---insufficient-permissions-found"></a>오류 ID 95001 - 권한이 없음

이 오류는 복제를 사용하도록 설정하려고 하는데 애플리케이션 폴더에 대한 충분한 권한이 없는 경우에 발생합니다.

**해결 방법**: 해당 이슈를 해결하려면 아래에 언급된 모든 폴더에 대한 소유자 역할이 IUSR 사용자에게 있는지 확인합니다.

- *C\ProgramData\Microsoft Azure Site Recovery\private*
- 설치 디렉터리. 예를 들어 설치 디렉터리가 F 드라이브인 경우 다음에 대한 올바른 권한을 제공합니다.
    - *F:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems*
- 설치 디렉터리의 *\pushinstallsvc* 폴더. 예를 들어 설치 디렉터리가 F 드라이브인 경우 다음에 대한 올바른 권한을 제공합니다.
    - *F:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc*
- 설치 디렉터리의 *\etc* 폴더. 예를 들어 설치 디렉터리가 F 드라이브인 경우 다음에 대한 올바른 권한을 제공합니다.
    - *F:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\etc*
- *C:\Temp*
- *C:\thirdparty\php5nts*
- 아래 경로에 있는 모든 항목 -
    - *C:\thirdparty\rrdtool-1.2.15-win32-perl58\rrdtool\Release\**

## <a name="next-steps"></a>다음 단계

도움이 필요한 경우 [Azure Site Recovery의 Microsoft Q&A 질문 페이지](/answers/topics/azure-site-recovery.html)에 질문을 게시하세요. 활발히 유지되는 커뮤니티가 있으며 엔지니어 중 하나가 도움을 줄 수 있습니다.
