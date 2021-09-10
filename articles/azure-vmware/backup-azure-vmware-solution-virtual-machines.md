---
title: Azure Backup Server를 사용하여 Azure VMware Solution VM 백업
description: Azure Backup Server를 사용하여 가상 머신을 백업하도록 Azure VMware Solution 환경을 구성합니다.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: c632acdf6f97a906fec5ba42faad0b56db89c328
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538751"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Azure Backup Server를 사용하여 Azure VMware Solution VM 백업

이 문서에서는 Azure Backup Server를 사용하여 Azure VMware Solution에서 실행되는 VMware VM(가상 머신)을 백업하는 방법을 보여 줍니다.  먼저 [Azure VMware Solution용 Microsoft Azure Backup Server 설정](set-up-backup-server-for-azure-vmware-solution.md)을 철저히 살펴봅니다.

그런 다음, 필요한 모든 절차를 진행하겠습니다.

> [!div class="checklist"] 
> * Azure Backup Server가 HTTPS를 통해 VMware 서버와 통신할 수 있도록 보안 채널을 설정합니다. 
> * Azure Backup Server에 계정 자격 증명을 추가합니다. 
> * Azure Backup Server에 vCenter를 추가합니다. 
> * 백업할 VMware VM을 포함하는 보호 그룹을 설정하고 백업 설정을 지정한 다음, 백업을 예약합니다.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>vCenter Server에 대한 보안 연결 만들기

기본적으로 Azure Backup Server는 HTTPS를 통해 VMware 서버와 통신합니다. HTTPS 연결을 설정하려면 VMware CA(인증 기관) 인증서를 다운로드하여 Azure Backup Server에서 가져옵니다.

### <a name="set-up-the-certificate"></a>인증서 설정

1. Azure Backup Server 컴퓨터의 브라우저에 vSphere Web Client URL을 입력합니다.

   > [!NOTE] 
   > VMware **시작** 페이지가 나타나지 않으면 연결 및 브라우저 프록시 설정을 확인하고 다시 시도합니다.

1. VMware **시작** 페이지에서 **신뢰할 수 있는 루트 CA 인증서 다운로드** 를 선택합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="vSphere에 원격으로 액세스하기 위한 vSphere 웹 클라이언트 시작 창을 보여 주는 스크린샷":::

1. **download.zip** 파일을 Azure Backup Server 시스템에 저장한 다음 해당 콘텐츠를 다음이 포함된 **certs** 폴더에 추출합니다.

   - .0 및 .1과 같은 숫자가 매겨진 시퀀스로 시작하는 확장명을 가진 루트 인증서 파일.
   - .r0 또는 .r1과 같은 시퀀스로 시작하는 확장명을 가진 CRL 파일.

1. **인증서** 폴더에서 루트 인증서 파일을 마우스 오른쪽 단추로 클릭하고 **이름 바꾸기** 를 선택하여 확장자를 **.crt** 로 변경합니다.

   파일 아이콘이 루트 인증서를 나타내는 아이콘으로 변경됩니다.

1. 루트 인증서를 마우스 오른쪽 단추로 클릭하고 **인증서 설치** 를 선택합니다.

1. **인증서 가져오기 마법사** 에서 **로컬 머신** 을 인증서의 대상으로 선택하고 **다음** 을 선택합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png" alt-text="로컬 머신이 선택된 인증서 가져오기 마법사 대화 상자를 보여 주는 스크린샷":::

   > [!NOTE] 
   > 메시지가 표시되면 컴퓨터에 대한 변경을 허용할 것인지 확인합니다.

1. **모든 인증서를 다음 저장소에 저장** 을 선택하고 **찾아보기** 를 선택하여 인증서 저장소를 선택합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png" alt-text="모든 인증서를 다음 저장소에 저장 옵션이 선택된 인증서 저장소 대화 상자를 보여 주는 스크린샷":::

1. 대상 폴더로 **신뢰할 수 있는 루트 인증 기관** 을 선택하고 **확인** 을 선택합니다.

1. 설정을 검토하고 **마침** 을 선택하여 인증서 가져오기를 시작합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png" alt-text="인증서 가져오기 마법사를 보여 주는 스크린샷":::

1. 인증서 가져오기가 확인된 후 vCenter Server에 로그인하여 연결이 안전한지 확인합니다.

### <a name="enable-tls-12-on-azure-backup-server"></a>Azure Backup Server에서 TLS 1.2 사용

VMware 6.7 이상에서는 통신 프로토콜로 TLS를 사용하도록 설정했습니다. 

1. 다음 레지스트리 설정을 복사하여 메모장에 붙여넣습니다. 그런 다음 파일을 .txt 확장자 없이 TLS.REG로 저장합니다.

   ```text
   
   Windows Registry Editor Version 5.00
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   ```

1. TLS.REG 파일을 마우스 오른쪽 단추로 클릭하고 **병합** 또는 **열기** 를 선택하여 설정을 레지스트리에 추가합니다.


## <a name="add-the-account-on-azure-backup-server"></a>Azure Backup Server에 계정 추가

1. Azure Backup Server를 열고 Azure Backup Server 콘솔에서 **관리** > **프로덕션 서버** > **VMware 관리** 를 선택합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png" alt-text="Microsoft Azure Backup 콘솔을 보여 주는 스크린샷":::

1. **자격 증명 관리** 대화 상자에서 **추가** 를 선택합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png" alt-text="Azure Backup Server에서 자격 증명 관리를 보여 주는 스크린샷":::

1. **자격 증명 추가** 대화 상자에서 새 자격 증명에 대한 이름 및 설명을 입력합니다. VMware 서버에서 정의한 사용자 이름 및 암호를 지정합니다.

   > [!NOTE] 
   > VMware 서버와 Azure Backup Server가 있는 도메인이 동일하지 않으면 **사용자 이름** 상자에 도메인을 지정합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png" alt-text="Azure Backup Server에서 자격 증명 세부 정보를 보여 주는 스크린샷":::

1. **추가** 를 선택하여 새 자격 증명을 선택합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png" alt-text="새 자격 증명이 표시된 Azure Backup Server 자격 증명 관리 대화 상자를 보여 주는 스크린샷":::

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Azure Backup Server에 vCenter Server를 추가합니다.

1. Azure Backup Server 콘솔에서 **관리** > **프로덕션 서버** > **추가** 를 선택합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png" alt-text="추가 단추가 선택된 Microsoft Azure Backup 콘솔을 보여 주는 스크린샷":::

1. **VMware 서버** 를 선택하고 **다음** 을 선택합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png" alt-text="선택된 VMware 서버 옵션을 표시하는 프로덕션 서버 추가 마법사를 보여 주는 스크린샷":::

1. vCenter의 IP 주소를 지정합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png" alt-text="VMware vCenter 또는 ESXi 호스트 서버와 해당 자격 증명을 추가하는 방법을 표시하는 프로덕션 서버 추가 마법사를 보여 주는 스크린샷":::

1. **SSL 포트** 상자에 vCenter와 통신하는 데 사용되는 포트를 입력합니다.

   > [!TIP] 
   > 포트 443은 기본 포트이지만 vCenter가 다른 포트에서 수신 대기하는 경우 이 포트를 변경할 수 있습니다.

1. **자격 정보 지정** 상자에서 이전 섹션에서 만든 자격 증명을 선택합니다.

1. **추가** 를 선택하여 vCenter를 서버 목록에 추가하고 **다음** 을 선택합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png" alt-text="정의된 VMware 서버와 자격 증명을 표시하는 프로덕션 서버 추가 마법사를 보여 주는 스크린샷":::

1. **요약** 페이지에서 **추가** 를 선택하여 vCenter를 Azure Backup Server에 추가합니다.

   새 서버가 즉시 추가됩니다. vCenter는 에이전트가 필요하지 않습니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/tasks-screen.png" alt-text="정의된 VMware 서버와 자격 증명의 요약, 선택된 추가 단추를 표시하는 프로덕션 서버 추가 마법사를 보여 주는 스크린샷":::

1. **마침** 페이지에서 설정을 검토한 다음 **닫기** 를 선택합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/summary-screen.png" alt-text="추가된 VMware 서버와 자격 증명의 요약을 표시하는 프로덕션 서버 추가 마법사를 보여 주는 스크린샷":::

   **프로덕션 서버** 아래에 다음 조건의 vCenter Server가 나열되어 표시됩니다.
   - 유형: **VMware 서버** 
   - 에이전트 상태: **정상** 
   
      **에이전트 상태** 가 **알 수 없음** 으로 표시되면 **새로 고침** 을 선택합니다.

## <a name="configure-a-protection-group"></a>보호 그룹 구성

보호 그룹은 여러 VM을 수집하고 동일한 데이터 보존 및 백업 설정을 그룹의 모든 VM에 적용합니다.

1. Azure Backup Server 콘솔에서 **보호** > **새로 만들기** 를 선택합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png" alt-text="새 보호 그룹을 만들기 위해 새 단추가 선택된 Microsoft Azure Backup 콘솔을 보여 주는 스크린샷":::

1. **새 보호 그룹 만들기** 마법사 시작 페이지에서 **다음** 을 선택합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/protection-wizard.png" alt-text="보호 그룹 마법사를 보여 주는 스크린샷":::

1. **보호 그룹 형식 선택** 페이지에서 **서버** 를 선택하고 **다음** 을 선택합니다. **그룹 구성원 선택** 페이지가 나타납니다.

1. **그룹 구성원 선택** 페이지에서 백업할 VM(또는 VM 폴더)을 선택한 후 **다음** 을 선택합니다.

   > [!NOTE]
   > 폴더 또는 VM을 선택하면 해당 폴더 내의 폴더도 백업되도록 선택됩니다. 백업하지 않으려는 폴더 또는 VM을 선택 취소할 수 있습니다. VM 또는 폴더가 이미 백업 중인 경우에는 VM을 선택할 수 없으므로 VM에 대해 중복 복구 지점이 생성되지 않습니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png" alt-text="그룹 멤버를 선택하기 위한 보호 그룹 마법사 만들기를 보여 주는 스크린샷":::

1. **데이터 보호 방법 선택** 페이지에서 보호 그룹의 이름과 보호 설정을 입력합니다. 

1. 단기 보호를 **디스크** 로 설정하고 온라인 보호를 사용한 후 **다음** 을 선택합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/name-protection-group.png" alt-text="데이터 보호 방법을 선택하기 위한 보호 그룹 마법사 만들기를 보여 주는 스크린샷":::

1. 데이터를 디스크에 백업할 기간을 지정합니다.

   - **보존 기간**: 디스크 복구 지점이 보관되는 일 수.
   - **빠른 전체 백업**: 디스크 복구 지점이 생성되는 빈도. 단기 백업이 발생하는 시간 또는 날짜를 변경하려면 **수정** 을 선택합니다.

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="디스크 기반 보호의 단기 복구 목표를 지정하기 위한 보존 범위를 보여 주는 스크린샷":::

1. **디스크 스토리지 할당 검토** 페이지에서 VM 백업에 제공된 디스크 공간을 검토합니다.

   - 권장되는 디스크 할당은 지정한 보존 범위, 워크로드 유형, 보호된 데이터 크기를 기반으로 합니다. 필요에 따라 변경하고 **다음** 을 선택합니다.
   - **데이터 크기:** 보호 그룹의 데이터 크기입니다.
   - **디스크 공간:** 보호 그룹에 권장되는 디스크 공간의 크기입니다. 이 설정을 수정하려면 각 데이터 원본이 증가할 것으로 예상되는 양보다 약간 큰 공간을 선택합니다.
   - **스토리지 풀 세부 정보:** 총 디스크 크기 및 남아 있는 디스크 크기를 포함하는 스토리지 풀의 상태를 표시합니다.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="각 데이터 원본에 대해 할당된 대상 스토리지를 검토하기 위한 디스크 스토리지 할당 검토 대화 상자를 보여 주는 스크린샷":::

   > [!NOTE]
   > 일부 시나리오에서는 보고된 데이터 크기가 실제 VM 크기보다 큽니다. 문제를 인식하고 현재 조사하고 있습니다.

1. **복제본 생성 방법 선택** 페이지에서 원하는 초기 백업 방법을 지정하고 **다음** 을 선택합니다.

   - 기본 설정은 **네트워크를 통해 자동으로** 및 **지금** 입니다. 기본값을 사용하는 경우 사용량이 적은 시간을 지정합니다. **나중에** 를 선택한 경우 날짜와 시간을 지정합니다.
   - 대용량 데이터이거나 네트워크 상태가 최적화되지 않은 경우 이동식 미디어를 사용하여 데이터를 오프라인으로 복제하는 것을 고려하세요.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/name-protection-group.png" alt-text="복제본 만들기 방법을 선택하기 위한 보호 그룹 마법사 만들기를 보여 주는 스크린샷":::

1. **일관성 검사 옵션** 의 경우 일관성 검사를 자동화하는 방법과 시간을 선택하고 **다음** 을 선택합니다.

   - 복제 데이터가 일관성을 잃은 경우 또는 설정된 일정에 따라 일관성 확인을 실행할 수 있습니다.
   - 자동 일관성 검사를 구성하지 않으려면 보호 그룹 **일관성 검사 수행** 을 마우스 오른쪽 단추로 클릭하여 수동 검사를 실행할 수 있습니다.

1. **온라인 보호 데이터 지정** 페이지에서 백업할 VM 또는 VM 폴더를 선택한 후 **다음** 을 선택합니다. 

   > [!TIP]
   > 구성원을 개별적으로 선택하거나 **모두 선택** 을 선택하여 모든 구성원을 선택할 수 있습니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png" alt-text="DPM을 통해 온라인으로 보호할 데이터를 지정하기 위한 보호 그룹 마법사 만들기를 보여 주는 스크린샷":::

1. **온라인 백업 예약 지정** 페이지에 로컬 스토리지의 데이터를 Azure로 백업할 빈도를 표시합니다. 

   - 일정에 따라 데이터에 대한 클라우드 복구 지점이 생성됩니다. 
   - 복구 지점이 생성되면 Azure에서 Recovery Services 자격 증명 모음으로 전송됩니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png" alt-text="DPN이 보호 계획을 생성하는 데 사용할 온라인 백업 일정을 지정하기 위한 보호 그룹 마법사 만들기를 보여 주는 스크린샷":::

1. **온라인 보존 정책 지정** 페이지에서, 백업에서 만들어진 복구 지점을 Azure에 보관할 기간을 지정합니다.

   - Azure에서 데이터를 유지할 수 있는 기간에 대한 시간 제한은 없습니다.
   - 보호된 인스턴스당 9,999개 이하의 복구 지점을 사용해야 한다는 점이 유일한 제한입니다. 이 예제에서 보호되는 인스턴스는 VMware 서버입니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/retention-policy.png" alt-text="온라인 보존 정책을 지정하기 위한 보호 그룹 마법사 만들기를 보여 주는 스크린샷":::

1. **요약** 페이지에서 설정을 검토한 다음 **그룹 만들기** 를 선택합니다.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png" alt-text="요약하기 위한 보호 그룹 마법사 만들기를 보여 주는 스크린샷":::

## <a name="monitor-with-the-azure-backup-server-console"></a>Azure Backup Server 콘솔에서 모니터링

Azure VMware Solution VM을 백업하도록 보호 그룹을 구성한 후 Azure Backup Server 콘솔을 사용하여 백업 작업 상태와 경고를 모니터링할 수 있습니다. 다음을 모니터링할 수 있습니다.

- **모니터링** 작업 영역에서:
   - **경고**(Alerts)에서 오류, 경고(warnings) 및 일반 정보를 모니터링할 수 있습니다.  활성 및 비활성 경고를 보고 메일 알림을 설정할 수 있습니다.
   - **작업** 에서 특정 보호된 데이터 원본 또는 보호 그룹에 대해 Azure Backup Server에서 시작된 작업을 볼 수 있습니다. 작업에서 사용되는 리소스를 확인하거나 작업 진행 상황을 따를 수 있습니다.
- **보호** 작업 영역에서 보호 그룹의 볼륨 및 공유 상태를 확인할 수 있습니다. 복구 설정, 디스크 할당, 백업 일정 등의 구성 설정을 확인할 수도 있습니다.
- **관리** 작업 영역에서 **디스크, 온라인** 및 **에이전트** 탭을 보고 스토리지 풀의 디스크 상태, Azure에 등록 및 배포된 DPM 에이전트 상태를 확인할 수 있습니다.

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="Azure Backup Server의 백업 작업을 보여 주는 스크린샷":::

## <a name="restore-vmware-virtual-machines"></a>VMware 가상 컴퓨터 복원

Azure Backup Server 관리자 콘솔에서 복구 가능한 데이터를 찾는 방법에는 두 가지가 있습니다. 검색하거나 찾아볼 수 있습니다. 데이터를 복구할 때 데이터 또는 VM을 동일한 위치로 복원하거나 복원하지 않을 수 있습니다. 따라서 Azure Backup Server는 VMware VM 백업에 대한 세 가지 복구 옵션을 지원합니다.

- **OLR(원래 위치 복구)** : OLR을 사용하여 보호된 VM을 원래 위치로 복원합니다. 백업이 수행된 후 디스크가 추가되거나 삭제되지 않은 경우에만 VM을 원래 위치로 복원할 수 있습니다. 디스크가 추가되거나 삭제된 경우에는 대체 위치 복구를 사용해야 합니다.
- **ALR(대체 위치 복구)** : 원래 VM이 없거나 원래 VM을 방해하지 않으려는 경우 사용합니다. ESXi 호스트, 리소스 풀, 폴더 및 스토리지 데이터 스토리지 및 경로의 위치를 제공합니다. 원래 VM과 복원 VM을 구별할 수 있도록 Azure Backup Server는 VM 이름에 *"-Recovered"* 를 추가합니다.
- **ILR(개별 파일 위치 복구)** : 보호된 VM이 Windows Server VM인 경우 Azure Backup Server의 ILR 기능을 사용하여 VM 내부의 개별 파일 또는 폴더를 복구할 수 있습니다. 개별 파일을 복구하려면 이 문서의 뒤에 나오는 절차를 참조하세요. VM에서 개별 파일 복원은 Windows VM 및 디스크 복구 지점에만 사용할 수 있습니다.

### <a name="restore-a-recovery-point"></a>복구 지점 복원

1. Azure Backup Server 관리자 콘솔에서 **복구** 보기를 선택합니다. 

1. **찾아보기** 창에서 찾아보기 또는 필터를 사용하여 복구할 VM을 찾습니다. VM 또는 폴더를 선택한 후 **복구 지점 사용 항목 창에 사용 가능한 복구 지점이 표시됩니다.

   :::image type="content" source="../backup/media/restore-azure-backup-server-vmware/recovery-points.png" alt-text="VMware 서버에 사용 가능한 복구 지점을 보여 주는 스크린샷":::

1. **복구 지점 사용 항목** 창에서 복구 지점을 생성한 날짜를 선택합니다. 예를 들어, 굵게 표시된 달력 날짜에는 사용 가능한 복구 지점이 있습니다. 또는 VM을 마우스 오른쪽 단추로 클릭하고, **모든 복구 지점 표시** 를 선택한 다음, 목록에서 복구 지점을 선택할 수 있습니다.

   > [!NOTE] 
   > 단기 보호의 경우 빠른 복구를 위해 디스크 기반 복구 지점을 선택합니다. 단기 복구 지점이 만료되면 복구할 **온라인** 복구 지점만 표시됩니다.

1. 온라인 복구 지점에서 복구하기 전에 준비 위치에 복구하려는 VM의 압축되지 않은 전체 크기를 수용할 수 있는 충분한 여유 공간이 있는지 확인합니다. 스테이징 위치는 **구독 설정 구성 마법사** 를 실행하여 보거나 변경할 수 있습니다.

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="복구 폴더 위치를 보여 주는 스크린샷":::

1. **복구** 를 선택하여 **복구 마법사** 를 엽니다.

   :::image type="content" source="../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png" alt-text="복구 마법사 검토 대화 상자를 보여 주는 스크린샷":::

1. **다음** 을 선택하여 **복구 옵션 지정** 화면으로 이동합니다. 다시 **다음** 을 선택하여 **복구 유형 선택** 화면으로 이동합니다. 

   > [!NOTE]
   > VMware 워크로드는 네트워크 대역폭 제한 사용을 지원하지 않습니다.

1. **복구 유형 선택** 페이지에서 원래 인스턴스 또는 새 위치로 복구합니다.

   - **원본 인스턴스에 복구** 를 선택하면 마법사에서 추가 항목을 선택할 필요가 없습니다. 원본 인스턴스에 대한 데이터가 사용됩니다.
   - **임의의 호스트에 가상 머신으로 복구** 를 선택한 경우 **대상 지정** 화면에서 **ESXi 호스트**, **리소스 풀**, **폴더** 및 **경로** 에 대한 정보를 제공합니다.

   :::image type="content" source="../backup/media/restore-azure-backup-server-vmware/recovery-type.png" alt-text="복구 유형을 선택하기 위한 복구 마법사를 보여 주는 스크린샷":::

1. **요약** 페이지에서 설정을 검토하고 **복구** 를 선택하여 복구 프로세스를 시작합니다. 

   **복구 상태** 화면에 복구 작업의 진행 상태가 표시됩니다.

### <a name="restore-an-individual-file-from-a-vm"></a>VM에서 개별 파일 복원

보호된 VM 복구 지점에서 개별 파일을 복원할 수 있습니다. 이 기능은 Windows Server VM에만 사용할 수 있습니다. 개별 파일을 복원하는 것은 VM을 복원하는 것과 비슷하지만, 복구 프로세스를 시작하기 전에 VMDK로 이동하고 원하는 파일을 찾는 점이 다릅니다. 

> [!NOTE]
> VM에서 개별 파일 복원은 Windows VM 및 디스크 복구 지점에만 사용할 수 있습니다.

1. Azure Backup Server 관리자 콘솔에서 **복구** 보기를 선택합니다.

1. **찾아보기** 창에서 찾아보기 또는 필터를 사용하여 복구할 VM을 찾습니다. VM 또는 폴더를 선택한 후 **복구 지점 사용 항목 창에 사용 가능한 복구 지점이 표시됩니다.

   :::image type="content" source="../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png" alt-text="VMware 서버의 복구 지점을 보여 주는 스크린샷":::

1. **복구 지점 사용 항목** 창에서 달력을 사용하여 원하는 복구 지점의 날짜를 선택합니다. 백업 정책이 구성된 방식에 따라 날짜에는 복구 지점이 둘 이상 있을 수 있습니다. 

1. 복구 지점을 만든 날짜를 선택한 후 올바른 **복구 시간** 을 선택했는지 확인합니다. 

   > [!NOTE]
   > 선택한 날짜에 복구 지점이 여러 개 있는 경우 **복구 시간** 드롭다운 메뉴에서 복구 지점을 선택합니다. 

   복구 지점을 선택한 후 복구 가능한 항목 목록이 **경로** 창에 표시됩니다.

1. 복구할 파일을 찾으려면 **경로** 창의 **복구 가능한 항목** 열에서 항목을 두 번 클릭하여 엽니다. 복구할 파일 또는 폴더를 선택합니다. 여러 항목을 선택하려면 각 항목을 선택하는 동안 **Ctrl** 키를 선택합니다. **경로** 창을 사용하여 **복구 가능한 항목** 열에 표시된 파일 또는 폴더 목록을 검색합니다.
    
   > [!NOTE]
   > **아래 검색 목록** 은 하위 폴더를 검색하지 않습니다. 하위 폴더를 검색하려면 폴더를 두 번 클릭합니다. **위로** 단추를 사용하여 자식 폴더에서 부모 폴더로 이동합니다. 여러 항목(파일 및 폴더)을 선택할 수 있지만 항목이 동일한 부모 폴더에 있어야 합니다. 동일한 복구 작업에서 여러 폴더의 항목을 복구할 수 없습니다.

   :::image type="content" source="../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png" alt-text="선택된 사용 가능한 복구 지점의 날짜와 시간을 보여 주는 스크린샷":::

1. 복구할 항목을 선택한 경우 관리자 콘솔 도구 리본에서 **복구** 를 선택하여 **복구 마법사** 를 엽니다. **복구 마법사** 의 **복구 선택 사항 검토** 화면에 복구하도록 선택한 항목이 표시됩니다.

1. **복구 옵션 지정** 화면에서 다음 단계 중 하나를 수행합니다.

   - 네트워크 대역폭 제한을 사용하려면 **수정** 을 선택합니다. **제한** 대화 상자에서 **네트워크 대역폭 사용량 제한 사용** 을 선택하여 켭니다. 사용하도록 설정된 후 **설정** 및 **근무 일정** 을 구성합니다.
   - 네트워크 제한을 사용하지 않으려면 **다음** 을 선택합니다.

1. **복구 유형 선택** 화면에서 **다음** 을 선택합니다. 네트워크 폴더로는 파일 또는 폴더만 복구할 수 있습니다.

1. **대상 지정** 화면에서 **찾아보기** 를 선택하여 파일 또는 폴더의 네트워크 위치를 찾습니다. Azure Backup Server는 복구된 모든 항목이 복사되는 폴더를 만듭니다. 폴더 이름에는 MABS_day-month-year 접두사가 있습니다. 복구된 파일이나 폴더의 위치를 선택하면 해당 위치에 대한 세부 정보가 제공됩니다.

   :::image type="content" source="../backup/media/restore-azure-backup-server-vmware/specify-destination.png" alt-text="선택된 사용 가능한 복구 지점의 날짜와 시간, 대상, 대상 경로를 보여 주는 스크린샷":::

1. **복구 옵션 지정** 화면에서 적용할 보안 설정을 선택합니다. 네트워크 대역폭 사용량 제한을 수정하도록 선택할 수 있지만, 제한은 기본적으로 사용되지 않습니다. 또한 **SAN 복구** 및 **알림** 이 사용하도록 설정되지 않습니다.

1. **요약** 화면에서 설정을 검토하고 **복구** 를 선택하여 복구 프로세스를 시작합니다. **복구 상태** 화면에 복구 작업의 진행 상태가 표시됩니다.

## <a name="next-steps"></a>다음 단계

이제 Azure Backup Server를 사용하여 Azure VMware Solution VM을 백업하는 방법을 살펴보았으므로 다음에 대해 알아보겠습니다. 

- [Azure Backup Server에서 백업을 설정할 때 발생하는 문제 해결](../backup/backup-azure-mabs-troubleshoot.md).
- [Azure VMware Solution VM의 수명 주기 관리](./integrate-azure-native-services.md)
