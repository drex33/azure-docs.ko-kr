---
title: Azure Site Recovery 복제 어플라이언스 배포 - 미리 보기
description: 이 문서에서는 Azure Site Recovery - 미리 보기를 사용하여 Azure로 VMware 재해 복구를 위해 복제 어플라이언스를 배포할 때 지원 및 요구 사항에 대해 설명합니다.
ms.service: site-recovery
ms.topic: article
ms.date: 08/19/2021
ms.openlocfilehash: e4021aa0f5572a51ca4d3ddda37f64f4da46a3b4
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122539543"
---
# <a name="deploy-azure-site-recovery-replication-appliance---preview"></a>Azure Site Recovery 복제 어플라이언스 배포 - 미리 보기

>[!NOTE]
> 이 문서의 정보는 Azure Site Recovery - 미리 보기에 적용됩니다. 클래식 릴리스의 구성 서버 요구 사항에 대한 자세한 내용은 [이 문서를 참조](vmware-azure-configuration-server-requirements.md)하세요.

>[!NOTE]
> 미리 보기 어플라이언스를 설정하기 위해 새 Recovery Services 자격 증명 모음을 만들어야 합니다. 기존 자격 증명 모음을 사용하지 마세요.

Azure로 VMware VM과 물리적 서버의 재해 복구를 위해 [Azure Site Recovery](site-recovery-overview.md)를 사용할 경우 온-프레미스 복제 어플라이언스를 배포합니다.

- 복제 어플라이언스는 온-프레미스 VMware와 Azure 간의 통신을 조정합니다. 또한 데이터 복제를 관리합니다.
- Azure Site Recovery 복제 어플라이언스 구성 요소 및 프로세스에 대해 [자세히 알아보세요](vmware-azure-architecture-preview.md).

## <a name="hardware-requirements"></a>하드웨어 요구 사항

**구성 요소** | **요구 사항**
--- | ---
CPU 코어 | 8
RAM | 32GB
디스크 수 | 3, OS 디스크 포함 - 80GB, 데이터 디스크 1 - 620GB, 데이터 디스크 2 - 620GB

## <a name="software-requirements"></a>소프트웨어 요구 사항

**구성 요소** | **요구 사항**
--- | ---
운영 체제 | Windows Server 2016
운영 체제 로케일 | 영어(en-*)
Windows Server 역할 | 다음 역할을 사용하지 않도록 설정함: <br> - Active Directory Domain Services <br>- 인터넷 정보 서비스 <br> - Hyper-V
그룹 정책 | 다음 그룹 정책을 사용하지 않도록 설정함: <br> - 명령 프롬프트에 대한 액세스 방지 <br> - 레지스트리 편집 도구에 대한 액세스 방지 <br> - 파일 첨부를 위한 트러스트 논리 <br> - 스크립트 실행 켜기 <br> [자세한 정보](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - 기존의 기본 웹 사이트 없음 <br> - 포트 443에서 수신 대기하는 기존의 웹 사이트/애플리케이션 없음 <br>- [익명 인증](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) 사용 <br> - [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 설정 사용
FIPS(Federal Information Processing Standards) | FIPS 모드 사용 안 함|

## <a name="network-requirements"></a>네트워크 요구 사항

|**구성 요소** | **요구 사항**|
|--- | ---|
|IP 주소 유형 | 정적|
|포트 | 443(컨트롤 채널 오케스트레이션)<br>9443(데이터 전송)|
|NIC 유형 | VMXNET3(어플라이언스가 VMware VM인 경우)|


### <a name="allow-urls"></a>URL 허용

연속 연결을 위해 Azure Site Recovery 복제 어플라이언스에서 다음 URL이 허용 및 연결 가능한지 확인합니다.

  | **URL**                  | **세부 정보**                             |
  | ------------------------- | -------------------------------------------|
  | portal.azure.com          | Azure Portal로 이동합니다.              |
  | `*.windows.net `<br>`*.msftauth.net`<br>`*.msauth.net`<br>`*.microsoft.com`<br>`*.live.com `<br>`*.office.com ` | Azure 구독에 로그인하려면 다음을 수행합니다.  |
  |`*.microsoftonline.com `|어플라이언스에서 Azure Site Recovery와 통신할 수 있도록 Azure AD(Active Directory) 앱을 만듭니다. |
  |management.azure.com |어플라이언스에서 Azure Site Recovery 서비스와 통신할 수 있도록 Azure AD 앱을 만듭니다. |
  |`*.services.visualstudio.com `|내부 모니터링에 사용되는 앱 로그를 업로드합니다. |
  |`*.vault.azure.net `|Azure Key Vault에서 비밀을 관리합니다. 참고: 복제할 컴퓨터에서 이 URL에 액세스할 수 있는지 확인합니다. |
  |aka.ms |링크라고도 하는 액세스를 허용합니다. Azure Site Recovery 어플라이언스 업데이트에 사용됩니다. |
  |download.microsoft.com/download |Microsoft 다운로드에서 다운로드할 수 있습니다. |
  |`*.servicebus.windows.net `|어플라이언스와 Azure Site Recovery 서비스 간의 통신입니다. |
  |`*.discoverysrv.windowsazure.com `|Azure Site Recovery 검색 서비스 URL에 연결합니다. |
  |`*.hypervrecoverymanager.windowsazure.com `|Azure Site Recovery 마이크로 검색 URL에 연결합니다.  |
  |`*.blob.core.windows.net `|대상 디스크를 만드는 데 사용되는 Azure Storage에 데이터를 업로드합니다. |
  |`*.backup.windowsazure.com `|보호 서비스 URL – Azure에서 복제된 디스크를 처리하고 만들기 위해 Azure Site Recovery에서 사용하는 마이크로 서비스입니다. |

> [!NOTE]
> 프라이빗 링크는 미리 보기 릴리스에서 지원되지 않습니다.

## <a name="prepare-azure-account"></a>Azure 계정 준비

Azure Site Recovery 복제 어플라이언스를 만들고 등록하려면 다음 권한이 있는 Azure 계정이 필요합니다.

- Azure 구독에 대한 기여자 또는 소유자 권한.
- AAD(Azure Active Directory) 앱을 등록할 수 있는 권한.
- Azure에 Azure Site Recovery 복제 어플라이언스를 등록할 때 사용되는 Key Vault 생성을 위한 Azure 구독에 대한 소유자 또는 기여자 및 사용자 액세스 관리자 권한입니다.

Azure 체험 계정을 방금 만든 경우 자신이 구독에 대한 소유자입니다. 구독 소유자가 아닌 경우 필수 사용 권한에 대해 소유자에게 문의합니다.

## <a name="prerequisites"></a>필수 구성 요소

**Key Vault 사용 권한은 다음과 같습니다**.

- Microsoft.OffAzure/*
- Microsoft.KeyVault/register/action
- Microsoft.KeyVault/vaults/read
- Microsoft.KeyVault/vaults/keys/read
- Microsoft.KeyVault/vaults/secrets/read
- Microsoft.Recoveryservices/*

**다음 단계에 따라 필수 사용 권한을 할당합니다**.

1. Azure Portal에서 **구독** 을 검색하여 **서비스** 아래에서 **구독** 검색 상자를 선택하여 Azure 구독에 대해 검색합니다.

2. **구독** 페이지에서 Recovery Services 자격 증명 모음을 만든 구독을 선택합니다.

3. 선택한 구독에서 **액세스 제어**(IAM) > **액세스 확인** 을 차례로 선택합니다. **액세스 확인** 에서 관련 사용자 계정을 검색합니다.

4. **역할 할당 추가** 에서 **추가** 를 선택하고 기여자 또는 소유자 역할을 선택하고 계정을 선택합니다. 그런 다음, **저장** 을 선택합니다.

  어플라이언스를 등록하려면 Azure 계정에 AAD 앱을 등록할 수 있는 권한이 필요합니다.

  **다음 단계에 따라 필수 사용 권한을 할당합니다**.

  - Azure Portal에서 **Azure Active Directory** > **사용자** > **사용자 설정** 으로 차례로 이동합니다. **사용자 설정** 에서 Azure AD 사용자가 애플리케이션을 등록할 수 있는지 확인합니다(기본적으로 *예* 로 설정됨).

  - **앱 등록** 설정이 *아니요* 로 설정되는 경우 테넌트/전역 관리자에게 필요한 권한을 할당하도록 요청합니다. 또는 테넌트/전역 관리자가 애플리케이션 개발자 역할을 계정에 할당하여 AAD 앱 등록을 허용할 수 있습니다.


## <a name="prepare-infrastructure"></a>인프라 준비

온-프레미스 머신에서 복구를 사용하도록 설정하려면 온-프레미스 환경에서 Azure Site Recovery 복제 어플라이언스를 설정해야 합니다. 어플라이언스에서 수행하는 작업에 대한 자세한 내용은 [이 섹션을 참조](vmware-azure-architecture-preview.md)하세요.

**Recovery Services 자격 증명 모음** > **시작** 으로 이동합니다. VMware 머신에서 Azure로에서 **인프라 준비** 를 선택하고 아래 자세히 설명된 섹션으로 진행합니다.

![Recovery Services 자격 증명 모음 미리 보기](./media/deploy-vmware-azure-replication-appliance-preview/prepare-infra.png)

새 어플라이언스를 설정하려면 OVF 템플릿(권장) 또는 PowerShell을 사용할 수 있습니다. Ensure you meet all the [하드웨어](#hardware-requirements) 및 [소프트웨어 요구 사항](#software-requirements)과 다른 필수 구성 요소를 모두 충족해야 합니다.

## <a name="create-azure-site-recovery-replication-appliance"></a>Azure Site Recovery 복제 어플라이언스 만들기

사용자는 OVF 템플릿을 사용하거나 PowerShell을 통해 Site Recovery 복제 어플라이언스를 만들 수 있습니다.

>[!NOTE]
> 어플라이언스 설치는 순차적으로 수행해야 합니다. 여러 어플라이언스의 병렬 등록은 실행할 수 없습니다.


### <a name="create-replication-appliance-through-ovf-template"></a>OVF 템플릿을 통해 복제 어플라이언스 만들기

Azure Site Recovery는 모든 필수 구성 요소를 템플릿에 의해 처리하므로 이 방법을 사용하는 것이 좋습니다.
OVF 템플릿은 필수 사양을 사용하여 머신을 회전합니다.

![어플라이언스 만들기를 위한 인프라 준비](./media/deploy-vmware-azure-replication-appliance-preview/prepare-infra.png)

**다음 단계를 수행합니다.**

1. OVF 템플릿을 다운로드하여 온-프레미스 환경에서 어플라이언스를 설정합니다.
2. 배포가 완료되면 어플라이언스 VM을 켜고 Microsoft 평가 라이선스를 수락합니다.
3. 다음 화면에서 관리자 사용자에 대한 암호를 제공합니다.
4. **완료** 를 선택하고 시스템을 재부팅하면 관리자 사용자 계정으로 로그인할 수 있습니다.

### <a name="set-up-the-appliance-through-powershell"></a>PowerShell을 통해 어플라이언스 설정

조직 제한의 경우 PowerShell을 통해 Site Recovery 복제 어플라이언스를 수동으로 설정할 수 있습니다. 다음 단계를 수행합니다.

1. [여기](https://aka.ms/V2ARcmApplianceCreationPowershellZip)에서 설치 관리자를 다운로드하고 이 폴더를 Azure Site Recovery 복제 어플라이언스에 배치합니다.
2. zip 폴더를 성공적으로 복사한 후 압축을 풀어 폴더의 구성 요소를 추출합니다.
3. 폴더가 추출되는 경로로 이동하고 관리자 권한으로 다음 PowerShell 스크립트를 실행합니다.

    **DRInstaller.ps1**  

## <a name="register-appliance"></a>어플라이언스 등록
  어플라이언스를 만든 후 Microsoft Azure 어플라이언스 구성 관리자가 자동으로 시작됩니다. 인터넷 연결, 시간 동기화, 시스템 구성 및 그룹 정책(아래에 나열됨)과 같은 필수 구성 요소가 확인됩니다.

  - CheckRegistryAccessPolicy - 레지스트리 편집 도구에 대한 액세스를 금지합니다.
      - 키: HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
      - DisableRegistryTools 값은 0과 같을 수 없습니다.

  - CheckCommandPromptPolicy - 명령 프롬프트에 대한 액세스를 금지합니다.

      - 키: HKLM\SOFTWARE\Policies\Microsoft\Windows\System
      - DisableCMD 값은 0과 같을 수 없습니다.

  - CheckTrustLogicAttachmentsPolicy - 파일 첨부에 대한 신뢰 로직입니다.

      - 키: HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Attachments
      - UseTrustedHandlers 값은 3과 같을 수 없습니다.

  - CheckPowershellExecutionPolicy - 스크립트 실행을 켭니다.

      - PowerShell 실행 정책은 AllSigned 또는 Restricted가 될 수 없습니다.
      - 그룹 정책 ‘스크립트 실행 첨부 파일 관리자 켜기’가 사용 안 함 또는 ‘서명된 스크립트만 허용’으로 설정되어 있지 않은지 확인합니다.


  **어플라이언스를 등록하려면 다음 단계를 사용합니다**.

1. **프록시를 사용하여 인터넷에 연결** 옵션을 전환하여 프록시 설정을 구성합니다.

    모든 Azure Site Recovery 서비스는 이러한 설정을 사용하여 인터넷에 연결합니다. HTTP 프록시만 지원됩니다.

2. 연속 연결을 위해 Azure Site Recovery 복제 어플라이언스에서 [필수 URL](#allow-urls)이 허용 및 연결 가능한지 확인합니다.

3. 필수 구성 요소가 확인되면 모든 어플라이언스 구성 요소에 대한 다음 단계 정보가 페치됩니다. 모든 구성 요소의 상태를 검토한 다음, **계속** 을 클릭합니다. 세부 정보를 저장한 후에는 어플라이언스 연결을 선택하여 진행합니다.

4. 연결 세부 정보를 저장한 후 **계속** 을 선택하여 Microsoft Azure에 등록합니다.

5. [필수 구성 요소](#prerequisites)가 충족되었는지 확인하고 등록을 계속합니다.

    ![어플라이언스 등록](./media/deploy-vmware-azure-replication-appliance-preview/app-setup-register.png)

  - **어플라이언스의 친숙한 이름**: Azure Portal의 복구 서비스 자격 증명 모음 인프라에서 이 어플라이언스를 추적할 이름을 입력합니다.

  - **Azure Site Recovery 복제 어플라이언스 키**: **Recovery Services 자격 증명 모음** > **시작** > **VMware에서 Azure로 인프라 준비** 로 이동하여 포털에서 키를 복사합니다.

  - 키를 붙여넣은 후 **로그인** 을 선택합니다.
    새 인증 탭으로 리디렉션됩니다.

      기본적으로 인증 코드는 인증 관리자 페이지에서 아래 강조 표시된 대로 생성됩니다. 인증 탭에 이 코드를 사용합니다.

  - Microsoft Azure 자격 증명을 입력하여 등록을 완료합니다.

      등록을 완료한 후에는 탭을 닫고 구성 관리자로 이동하여 설치를 계속할 수 있습니다.

      ![인증 코드](./media/deploy-vmware-azure-replication-appliance-preview/enter-code.png)

      > [!NOTE]
      > 인증 코드는 생성 후 5분 이내에 만료됩니다. 이 기간보다 오랫동안 비활성 상태인 경우 Azure에 다시 로그인하라는 메시지가 표시됩니다.


6. **로그인** 을 선택하여 세션으로 다시 연결합니다. 인증 코드의 경우 구성 관리자의 *요약* 또는 *Azure Recovery Services 자격 증명 모음으로 등록* 섹션을 참조하세요.

7. 로그인에 성공하면 구독, 리소스 그룹 및 Recovery Services 자격 증명 모음 정보가 표시됩니다. 자격 증명 모음을 변경하려는 경우에는 로그아웃할 수 있습니다. 아니면 **계속** 을 선택하여 진행합니다.

    ![등록된 어플라이언스](./media/deploy-vmware-azure-replication-appliance-preview/app-setup.png)

    등록을 완료한 후 vCenter 세부 정보 구성으로 진행합니다.

    ![vCenter 구성](./media/deploy-vmware-azure-replication-appliance-preview/vcenter-information.png)

8. **vCenter Server 추가** 를 선택하여 vCenter 정보를 추가합니다. vCenter 및 포트 정보의 서버 이름 또는 IP 주소를 입력합니다. 게시하고, 사용자 이름, 암호 및 친숙한 이름을 입력하면, [vCenter를 통해 관리되는 가상 머신](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)의 세부 정보를 페치하는 데 사용됩니다. 사용자 계정 세부 정보는 암호화되고 머신에 로컬로 저장됩니다.

>[!NOTE]
> 여러 어플라이언스에 동일한 vCenter Server을 추가하려는 경우 두 어플라이언스 모두에 동일한 이름을 사용해야 합니다.

9. vCenter 정보를 성공적으로 저장한 후 **가상 머신 자격 증명 추가** 를 선택하여 vCenter를 통해 검색된 VM에 대한 사용자 세부 정보를 제공합니다.

   >[!NOTE]
   > - Linux OS의 경우 루트 자격 증명을 제공하고 Windows OS의 경우 관리자 권한이 있는 사용자 계정을 추가해야 합니다. 이러한 자격 증명은 복제 사용 작업을 수행하는 동안 원본 VM에 모바일 에이전트를 푸시하는 데 사용됩니다. 복제 워크플로를 사용하도록 설정하는 동안 Azure Portal에서 VM별로 자격 증명을 선택할 수 있습니다.
   > - 어플라이언스 구성기를 방문하여 머신에 액세스하기 위한 자격 증명을 편집하거나 추가합니다.

10. 세부 정보를 성공적으로 추가한 후에는 **계속** 을 선택하여 모든 Azure Site Recovery 복제 어플라이언스 구성 요소를 설치하고 Azure 서비스에 등록합니다. 이 작업은 최대 30분까지 걸릴 수 있습니다.

    구성이 진행 중인 동안에는 브라우저를 닫지 마세요.


## <a name="view-azure-site-recovery-replication-appliance-in-azure-portal"></a>Azure Portal에서 Azure Site Recovery 복제 어플라이언스 보기

Azure Site Recovery 복제 어플라이언스를 성공적으로 구성한 후에는 Azure Portal, **Recovery Services Vault** 로 이동합니다.

**시작** 에서 **인프라 준비(미리 보기)** 를 선택합니다. Azure Site Recovery 복제 어플라이언스가 이 자격 증명 모음에 이미 등록되어 있음을 확인할 수 있습니다. 이제 모든 설정이 완료되었습니다! 이 복제 어플라이언스를 통해 원본 머신 보호를 시작합니다.

*1개 어플라이언스 선택* 을 클릭하면 이 자격 증명 모음에 등록된 어플라이언스 목록이 표시되는 Azure Site Recovery 복제 어플라이언스 보기로 리디렉션됩니다.

**검색된 항목** 에 대한 탭에서 검색된 vCenter Servers/vSphere 호스트가 모두 나열되는 것을 볼 수 있습니다.

![복제 어플라이언스 미리 보기](./media/deploy-vmware-azure-replication-appliance-preview/discovered-items.png)

## <a name="sizing-and-capacity"></a>크기 조정 및 용량
기본 제공 프로세스 서버를 사용하여 워크로드를 보호하는 어플라이언스는 다음 구성에 따라 최대 200개의 가상 머신을 처리할 수 있습니다.

  |CPU |    메모리 |    캐시 디스크 크기 |    데이터 변경률 |    보호된 컴퓨터 |
  |---|-------|--------|------|-------|
  |16개 vCPU(2개 소켓 * 8코어 @ 2.5GHz)    | 32GB |    1TB |    >1TB~2TB    | 151~200개의 머신을 복제하는 데 사용됩니다.|

- 자격 증명 모음에 있는 복제 어플라이언스를 사용하여 vCenter Server의 모든 머신에 대한 검색을 수행할 수 있습니다.

- 선택한 어플라이언스가 정상인 경우 동일한 자격 증명 모음에 있는 서로 다른 어플라이언스 간에 [보호된 머신을 전환](switch-replication-appliance-preview.md)할 수 있습니다.

여러 어플라이언스를 사용하고 복제 어플라이언스를 장애 조치(failover)하는 방법에 대한 자세한 내용은 [이 문서](switch-replication-appliance-preview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure에 [VMware VM](vmware-azure-tutorial.md)의 재해 복구를 설정합니다.
