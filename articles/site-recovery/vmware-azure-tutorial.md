---
title: Azure Site Recovery를 사용하여 Azure로 VMware VM 재해 복구 설정 - 클래식
description: Azure Site Recovery를 사용하여 Azure에 온-프레미스 VMware VM에 대한 재해 복구를 설정하는 방법을 알아봅니다 - 클래식.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.custom: MVC
ms.openlocfilehash: 8f53f7598b58e46e8020275cdf96e339c69c857a
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446444"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms---classic"></a>Azure에 온-프레미스 VMware VM 재해 복구 설정 - 클래식

이 문서에서는 Azure로 재해 복구를 위해 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 VMware VM을 복제하도록 설정하는 방법을 설명합니다 - 클래식.

Azure Site Recovery 미리 보기의 재해 복구에 대한 자세한 내용은 [이 문서](vmware-azure-set-up-replication-tutorial-preview.md)를 참조하세요.

이 자습서는 온-프레미스 VMware VM을 Azure로 재해 복구하도록 설정하는 방법을 보여주는 자습서 시리즈 중 세 번째 자습서입니다. 이전 자습서에서는 Azure로 재해 복구하기 위한 [온-프레미스 VMware 환경을 준비](vmware-azure-tutorial-prepare-on-premises.md)했습니다.


이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 원본 복제 설정 및 온-프레미스 Site Recovery 구성 서버 설정
> * 복제 대상 설정 지정
> * 복제 정책을 만듭니다.
> * VMware VM을 복제하도록 설정

> [!NOTE]
> 자습서는 시나리오에 맞는 가장 간단한 배포 경로를 보여줍니다. 가능한 경우 기본 옵션을 사용하고 가능한 모든 설정과 경로를 보여주지 않습니다. 자세한 내용은 Site Recovery 목차의 방법 섹션에 있는 문서를 참조하세요.

## <a name="before-you-start"></a>시작하기 전에

이전 자습서 완료:
1. 온-프레미스 VMware를 Azure로 재해 복구하도록 [Azure를 설정](tutorial-prepare-azure.md)했는지 확인합니다.
2. [다음 단계](vmware-azure-tutorial-prepare-on-premises.md)에 따라 Azure로 재해 복구하도록 온-프레미스 VMware 배포를 준비합니다.
3. 이 자습서에서는 단일 VM을 복제하는 방법을 보여줍니다. 여러 VMware VM을 배포하는 경우 [Deployment Planner 도구](https://aka.ms/asr-deployment-planner)를 사용해야 합니다. [자세히 알아보세요](site-recovery-deployment-planner.md) .
4. 이 자습서에서는 다양한 옵션을 사용하며 다른 방식을 선택할 수도 있습니다.
    - 이 자습서에서는 OVA 템플릿을 사용하여 구성 서버 VMware VM을 만듭니다. 어떤 이유로 이 방법을 사용할 수 없는 경우 [다음 지침](physical-manage-configuration-server.md)에 따라 구성 서버를 수동으로 설정합니다.
    - 이 자습서에서 Site Recovery는 자동으로 MySQL을 다운로드하고 구성 서버에 설치합니다. 원한다면 대신 직접 설정할 수 있습니다. [자세히 알아보기](vmware-azure-deploy-configuration-server.md#configure-settings).




## <a name="select-a-protection-goal"></a>보호 목표 선택

1. **Recovery Services 자격 증명 모음** 에서 자격 증명 모음 이름을 선택합니다. 이 시나리오에서는 **ContosoVMVault** 를 사용합니다.
2. **시작** 에서 Site Recovery를 선택합니다. 그런 다음, **인프라 준비** 를 선택합니다.
3. **보호 목표** > **컴퓨터가 있는 위치** 에서 **온-프레미스** 를 선택합니다.
4. **컴퓨터를 복제할 위치를 선택하세요.** 에서 **Azure** 를 선택합니다.
5. **컴퓨터가 가상화된 경우** 에서 **예, VMware vSphere 하이퍼바이저 사용** 을 선택합니다. 그런 다음, **확인** 을 선택합니다.



## <a name="set-up-the-source-environment"></a>원본 환경 설정

원본 환경에서는 온-프레미스 Site Recovery 구성 요소를 호스팅할 단일 고가용성 온-프레미스 머신이 필요합니다.

- **구성 서버**: 구성 서버는 온-프레미스와 Azure 간의 통신을 조정하여 데이터 복제를 관리합니다.
- **프로세스 서버**: 프로세스 서버는 복제 게이트웨이의 역할을 합니다. 복제 데이터를 수신하여 캐싱, 압축 및 암호화를 사용하여 최적화한 후 Azure의 캐시 스토리지 계정으로 보냅니다. 또한 프로세스 서버는 복제하려는 VM에 모바일 서비스 에이전트를 설치하고, 온-프레미스 VMware VM을 자동으로 검색합니다.
- **마스터 대상 서버**: 마스터 대상 서버는 Azure에서 장애 복구 중 복제 데이터를 처리합니다.


이 모든 구성 요소가 *구성 서버* 라고 하는 단일 온-프레미스 머신에 함께 설치됩니다. 기본적으로 VMware 재해 복구의 경우 구성 서버를 고가용성 VMware VM으로 설정합니다. 이렇게 하려면 준비된 OVA(Open Virtualization Application) 템플릿을 다운로드하고 VMware로 가져와서 VM을 만듭니다.

- 최신 버전의 구성 서버는 포털에서 받을 수 있습니다. [Microsoft 다운로드 센터](https://aka.ms/asrconfigurationserver)에서 직접 다운로드할 수도 있습니다.
- 어떤 이유로 OVA 템플릿을 사용하여 VM을 설정할 수 없는 경우 [다음 지침](physical-manage-configuration-server.md)에 따라 구성 서버를 수동으로 설정합니다.
- OVF 템플릿에 제공되는 라이선스는 180일 동안 유효한 평가 라이선스입니다. VM에서 실행 중인 Windows는 필요한 라이선스로 활성화해야 합니다.


### <a name="download-the-vm-template"></a>VM 템플릿 다운로드

1. 자격 증명 모음에서 **인프라 준비** > **원본** 으로 이동합니다.
2. **원본 준비** 에서 **+구성 서버** 를 선택합니다.
3. **서버 추가** 에서 **VMware에 대한 구성 서버** 가 **서버 형식** 에 표시되는지 확인합니다.
4. 구성 서버에 대한 OVA 템플릿을 다운로드합니다.



## <a name="import-the-template-in-vmware"></a>VMware에서 템플릿 가져오기


1. VMware vSphere 클라이언트를 사용하여 VMware vCenter 서버 또는 vSphere ESXi 호스트에 로그인합니다.
2. **파일** 메뉴에서 **OVF 템플릿 배포** 를 선택하여 **OVF 템플릿 배포 마법사** 를 시작합니다.

     ![VMWare vSphere 클라이언트의 OVF 템플릿 배포 명령 스크린샷](./media/vmware-azure-tutorial/vcenter-wizard.png)

3. **원본 선택** 에서 다운로드한 OVF의 위치를 입력합니다.
4. **리뷰 세부 정보** 에서 **다음** 을 선택합니다.
5. **이름 및 폴더 선택** 과 **구성 선택** 에서 기본 설정을 그대로 적용합니다.
6. 최상의 성능을 발휘할 수 있도록 **스토리지 선택** 의 **가상 디스크 형식 선택** 에서 **씩 프로비전 Eager Zeroed** 를 선택합니다.
7. 마법사 페이지의 나머지 부분에서는 기본 설정을 적용합니다.
8. **완료 준비** 에서 기본 설정을 사용하여 VM을 설정하려면 **배포 후 전원 켜기** > **마침** 을 선택합니다.

   > [!TIP]
   > 추가 NIC를 추가하려면 **배포 후 전원 켜기** > **마침** 의 선택을 취소합니다. 템플릿에는 기본적으로 단일 NIC가 포함됩니다. 배포 후 NIC를 추가할 수 있습니다.

## <a name="add-an-additional-adapter"></a>어댑터 추가

구성 서버에 NIC를 추가하려면 자격 증명 모음에 서버를 등록하기 전에 추가하세요. 등록 후에는 어댑터를 추가할 수 없습니다.

1. VSphere 클라이언트 인벤토리에서 VM을 마우스 오른쪽 단추로 클릭하고 **설정 편집** 을 선택합니다.
2. **하드웨어** 에서 **추가** > **이더넷 어댑터** 를 선택합니다. 그런 후 **다음** 을 선택합니다.
3. 어댑터 유형 및 네트워크를 선택합니다.
4. VM이 켜질 때 가상 NIC에 연결하려면 **전원이 켜지면 연결** 을 선택합니다. **다음** > **마침** 을 선택합니다. 그런 다음, **확인** 을 선택합니다.


## <a name="register-the-configuration-server"></a>구성 서버 등록

구성 서버를 설정한 후에는 자격 증명 모음에 구성 서버를 등록합니다.

1. VMware vSphere 클라이언트 콘솔에서 VM을 켭니다.
2. VM이 Windows Server 2016 설치 환경으로 부팅됩니다. 사용권 계약에 동의하고 관리자 암호를 입력합니다.
3. 설치가 완료되면 VM에 관리자 권한으로 로그인합니다.
4. 처음으로 로그인하면 몇 초 내로 Azure Site Recovery 구성 도구가 시작됩니다.
5. Site Recovery에 구성 서버를 등록하는 데 사용된 이름을 입력합니다. 그런 후 **다음** 을 선택합니다.
6. VM이 Azure에 연결할 수 있는지 도구에서 확인합니다. 연결이 설정되면 **로그인** 을 선택하여 Azure 구독에 로그인합니다. 구성 서버를 등록하려는 자격 증명 모음에 자격 증명이 액세스할 수 있어야 합니다. 필요한 [역할](vmware-azure-deploy-configuration-server.md#azure-active-directory-permission-requirements)이 사용자에게 할당되었는지 확인합니다.
7. 도구에서 몇 가지 구성 작업을 수행한 후 다시 부팅합니다.
8. 컴퓨터에 다시 로그인합니다. 몇 초 내로 구성 서버 관리 마법사가 자동으로 시작됩니다.


### <a name="configure-settings-and-add-the-vmware-server"></a>설정 구성 및 VMware 서버 추가

구성 서버 설정 및 등록을 완료합니다. 계속하기 전에 구성 서버를 성공적으로 설정하기 위해 모든 [필수 구성 요소](vmware-azure-deploy-configuration-server.md#prerequisites)가 충족되는지 확인합니다.


1. 구성 서버 관리 마법사에서 **연결 설정** 을 선택합니다. 드롭다운 목록에서 먼저 기본 제공 프로세스 서버가 원본 머신에서 Mobility Service의 검색 및 푸시 설치에 사용하는 NIC를 선택한 다음, 구성 서버에서 Azure와의 연결에 사용하는 NIC를 선택합니다. 그런 다음 **저장** 을 선택합니다. 구성된 후에는 이 설정을 변경할 수 없습니다.
2. **Recovery Services 자격 증명 모음 선택** 에서 Azure 구독을 선택한 다음, 관련 리소스 그룹 및 자격 증명 모음을 선택합니다.
3. **타사 소프트웨어 설치** 에서 사용권 계약에 동의합니다. **다운로드 및 설치** 를 선택하여 MySQL 서버를 설치합니다. 경로에 MySQL을 배치하는 경우 이 단계를 건너뛸 수 있습니다. [자세히](vmware-azure-deploy-configuration-server.md#configure-settings) 알아보세요.
4. 계속하기 전에 **어플라이언스 구성 유효성 검사** 에서 필수 구성 요소가 확인됩니다.
5. **vCenter Server/vSphere ESXi 서버 구성** 에 복제하려는 VM이 있는 vCenter 서버 또는 vSphere 호스트의 FQDN 또는 IP 주소를 입력합니다. 서버가 수신 대기하는 포트를 입력합니다. 자격 증명 모음의 VMware 서버에 사용할 이름을 입력합니다.
6. 구성 서버가 VMware 서버에 연결하는 데 사용할 사용자 자격 증명을 입력합니다. 사용자 이름과 암호가 정확하고 보호할 가상 머신의 관리자 그룹에 속하는지 확인합니다. Site Recovery는 이러한 자격 증명을 사용하여 복제에 사용 가능한 VMware VM을 자동으로 검색합니다. **추가** 를 선택한 다음, **계속** 을 선택합니다.
7. **가상 머신 자격 증명 구성** 에서 복제가 사용되면 VM에서 모바일 서비스를 자동으로 설치하는 데 사용할 사용자 이름 및 암호를 입력합니다.
    - Windows 컴퓨터의 경우 복제하려는 컴퓨터에 대한 로컬 관리자 권한이 필요합니다.
    - Linux의 경우 루트 계정에 대한 세부 정보를 제공합니다.
8. **구성 완료** 를 선택하여 등록을 완료합니다.
9. 등록이 완료된 후 Azure Portal을 열고 구성 서버와 VMware 서버가 **Recovery Services 자격 증명 모음** > **관리** > **Site Recovery 인프라** > **구성 서버** 에 나열되어 있는지 확인합니다.


구성 서버가 등록되면 Site Recovery는 지정된 설정을 사용하여 VMware 서버에 연결하고 VM을 검색합니다.

> [!NOTE]
> 포털에 계정 이름이 표시되는 데 15분 이상 걸릴 수 있습니다. 즉시 업데이트하려면 **구성 서버** > **_서버 이름_ *_ > _* 서버 새로 고침** 을 선택합니다.

## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 리소스를 선택하고 확인합니다.

1. **인프라 준비** > **대상** 을 선택합니다. 사용할 Azure 구독을 선택합니다. Resource Manager 모델을 사용합니다.
2. Site Recovery는 가상 네트워크가 하나 이상 있는지 확인합니다. 이 자습서 시리즈의 [첫 번째 자습서](tutorial-prepare-azure.md)에서 Azure 구성 요소를 설정할 때 다음과 같은 항목이 있어야 합니다.

   ![인프라 준비 > 대상 옵션의 스크린샷](./media/vmware-azure-tutorial/storage-network.png)

## <a name="create-a-replication-policy"></a>복제 정책 만들기

1. [Azure Portal](https://portal.azure.com)을 엽니다. **Recovery Services 자격 증명 모음** 을 검색하여 선택합니다.
2. Recovery Services 자격 증명 모음 선택(이 자습서에서는 **ContosoVMVault**).
3. 복제 정책을 만들려면 **Site Recovery 인프라** > **복제 정책** >  **+복제 정책** 을 선택합니다.
4. **복제 정책 만들기** 에서 정책 이름을 입력합니다. **VMwareRepPolicy** 를 사용합니다.
5. **RPO 임계값** 에서 기본값인 60분을 사용합니다. 이 값은 복구 지점을 만드는 빈도를 지정합니다. 연속 복제가 이 제한을 초과하면 경고가 생성됩니다.
6. **복구 지점 보존** 에서 각 복구 지점이 보존되는 방법을 지정합니다. 이 자습서에서는 72시간을 사용합니다. 복제된 VM은 보존 기간 중 임의의 시점으로 복구될 수 있습니다.
7. **앱 일치 스냅샷 빈도** 에서 앱 일치 스냅샷이 만들어지는 빈도를 지정합니다. 기본값은 60분을 사용하고 있습니다. **확인** 을 선택하여 정책을 만듭니다.

   ![복제 정책 만들기 옵션의 스크린샷](./media/vmware-azure-tutorial/replication-policy.png)

- 정책은 구성 서버와 자동으로 연결됩니다.
- 기본적으로 장애 복구(failback)에 대해 일치 정책이 자동으로 만들어집니다. 예를 들어 복제 정책이 **rep-policy** 인 경우 장애 복구(failback) 정책은 **rep-policy-failback** 이 됩니다. 이 정책은 Azure에서 장애 복구(failback)를 시작하기 전에는 사용되지 않습니다.

참고: VMware와 Azure 간 시나리오에서 크래시 일관성 스냅샷은 5분 간격으로 수행됩니다.

## <a name="enable-replication"></a>복제 사용

다음과 같이 VM에 대해 복제를 활성화합니다.

1. **애플리케이션 복제** > **원본** 을 선택합니다.
2. **원본** 에서 **온-프레미스** 를 선택하고, **원본 위치** 에서 구성 서버를 선택합니다.
3. **컴퓨터 형식** 에서 **Virtual Machines** 를 선택합니다.
4. **vCenter/vSphere 하이퍼바이저** 에서 vSphere 호스트를 관리하는 vSphere 호스트 또는 vCenter Server를 선택하거나 해당 호스트를 선택합니다.
5. 프로세스 서버(기본적으로 구성 서버 VM에 설치됨)를 선택합니다. 그런 다음, **확인** 을 선택합니다. 각 프로세스 서버의 상태는 권장되는 제한 및 기타 매개 변수에 따라 표시됩니다. 정상 프로세스 서버를 선택합니다. [위험한](vmware-physical-azure-monitor-process-server.md#process-server-alerts) 프로세스 서버는 선택할 수 없습니다. 오류를 [해결](vmware-physical-azure-troubleshoot-process-server.md)**하거나** [스케일 아웃 프로세스 서버](vmware-azure-set-up-process-server-scale.md)를 설정할 수 있습니다.
6. **대상** 에서 장애 조치(Failover)된 VM을 만들려는 구독 및 리소스 그룹을 선택합니다. Resource Manager 배포 모델을 사용하는 경우입니다.
7. 장애 조치(failover) 후 Azure VM이 생성될 때 연결될 Azure 네트워크 및 서브넷을 선택합니다.
8. 복제를 활성화한 모든 VM에 네트워크 설정을 적용하려면 **선택한 컴퓨터에 대해 지금 구성** 을 선택합니다. 네트워크가 없는 경우 **만들어야** 합니다.
9. **Virtual Machines** > **Virtual Machines 선택** 에서 복제하려는 각 컴퓨터를 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 다음, **확인** 을 선택합니다. 특정 가상 머신을 보거나 선택할 수 없으면 문제 해결 방법에 대한 [자세한 정보](./vmware-azure-troubleshoot-replication.md)를 확인하세요.
10. **속성** > **속성 구성** 에서 프로세스 서버가 자동으로 컴퓨터에 모바일 서비스를 설치하는 데 사용할 계정을 선택합니다.
11. **복제 설정** > **복제 설정 구성** 에서 올바른 복제 정책이 선택되어 있는지 확인합니다.
12. **복제 사용** 을 선택합니다. VM에 복제를 사용하도록 설정하면 Site Recovery에서는 모바일 서비스를 설치합니다.
13. **설정** > **작업** > **Site Recovery 작업** 에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **Finalize Protection** 작업이 실행되고 복구 지점 생성이 완료되면 머신이 장애 조치(failover)를 수행할 준비가 된 것입니다.
14. 변경 내용이 적용되어 포털에 표시되는 데 15분 이상 걸릴 수 있습니다.
15. 추가하는 VM을 모니터링하려면, **구성 서버** > **마지막 연락** 에서 VM을 마지막으로 검색한 시간을 확인합니다. 예약된 검색을 기다리지 않고 VM을 추가하려면 구성 서버를 강조 표시하고(클릭하지 않음) **새로 고침** 을 선택합니다.

## <a name="next-steps"></a>다음 단계
복제를 설정한 후에는 모든 것이 예상대로 작동하도록 보장하기 위한 훈련을 실행합니다.
> [!div class="nextstepaction"]
> [재해 복구 훈련 실행](site-recovery-test-failover-to-azure.md)
