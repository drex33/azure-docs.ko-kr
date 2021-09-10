---
title: Azure Migrate 서버 마이그레이션을 사용하여 Hyper-V VM을 Azure로 마이그레이션
description: Azure Migrate 서버 마이그레이션을 사용하여 온-프레미스 Hyper-V VM을 Azure로 마이그레이션하는 방법 알아보기
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: aa745b9a5c19f45563b6fe30b80f67a776660d39
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272424"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Hyper-V VM을 Azure로 마이그레이션

이 문서에서는 [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) 도구를 사용하여 온-프레미스 Hyper-V VM을 Azure로 마이그레이션하는 방법을 보여줍니다.

이 자습서는 머신을 평가하고 Azure로 마이그레이션하는 방법을 보여주는 시리즈의 세 번째 자습서입니다.

> [!NOTE]
> 자습서에서는 개념 증명을 빠르게 설정할 수 있도록 시나리오에 대한 가장 간단한 배포 경로를 보여 줍니다. 자습서는 가능한 경우 기본 옵션을 사용하며, 가능한 모든 설정과 경로는 보여 주지 않습니다.

 이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Migrate:Server Migration 도구를 추가합니다.
> * 마이그레이션하려는 VM을 검색합니다.
> * VM 복제를 시작합니다.
> * 테스트 마이그레이션을 실행하여 모든 것이 예상대로 작동하는지 확인합니다.
> * 전체 VM 마이그레이션을 실행합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)을 만듭니다.


## <a name="prerequisites"></a>필수 구성 요소


이 자습서를 시작하기 전에 다음을 수행해야 합니다.

1. Hyper-V 마이그레이션 아키텍처를 [검토](hyper-v-migration-architecture.md)합니다.
1. 마이그레이션을 위한 Hyper-V 호스트 요구 사항 및 VM 마이그레이션을 위해 Hyper-V 호스트 및 클러스터에서 액세스해야 하는 Azure URL을 [검토](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements)합니다.
1. Azure로 마이그레이션하려는 Hyper-V VM에 대한 요구 사항을 [검토](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms)합니다.
1. Azure로 마이그레이션하기 전에 [Hyper-V VM을 평가](tutorial-assess-hyper-v.md)하는 것이 좋지만, 반드시 그럴 필요는 없습니다.
1. 이미 만든 프로젝트로 이동하거나 [새 프로젝트를 만듭니다](./create-manage-projects.md).
1. Azure 계정에 대한 사용 권한 확인 - Azure 계정에는 VM을 만들고 Azure 관리 디스크에 쓸 수 있는 권한이 필요합니다.

## <a name="download-the-provider"></a>공급자 다운로드

Hyper-V VM을 마이그레이션하는 경우 Azure Migrate:Server Migration은 Hyper-V 호스트 또는 클러스터 노드에 소프트웨어 공급자(Microsoft Azure Site Recovery 공급자 및 Microsoft Azure Recovery Service 에이전트)를 설치합니다. [Azure Migrate 어플라이언스](migrate-appliance.md)는 Hyper-V 마이그레이션에 사용되지 않습니다.

1. Azure Migrate 프로젝트 > **서버**, **Azure Migrate: 서버 마이그레이션** 에서 **검색** 을 클릭합니다.
1. **머신 검색** > **머신이 가상화되어 있습니까?** 에서 **예, Hyper-V 사용** 을 선택합니다.
1. **대상 지역** 에서 머신을 마이그레이션하려는 Azure 지역을 선택합니다.
1. **마이그레이션의 대상 지역이 region-name인지 확인합니다.** 를 선택합니다.
1. **리소스 만들기** 를 클릭합니다. 그러면 Azure Site Recovery 자격 증명 모음이 백그라운드에서 만들어집니다.
    - Azure Migrate 서버 마이그레이션을 사용하여 마이그레이션을 이미 설정한 경우 리소스가 이전에 설정되었으므로 이 옵션은 표시되지 않습니다.
    - 이 단추를 클릭한 후에는 이 프로젝트의 대상 지역을 변경할 수 없습니다.
    - 이후의 모든 마이그레이션은 이 지역으로 수행됩니다.

1. **Hyper-V 호스트 서버 준비** 에서 Hyper-V 복제 공급자와 등록 키 파일을 다운로드합니다.
    - 등록 키는 Azure Migrate 서버 마이그레이션에 Hyper-V 호스트를 등록하는 데 필요합니다.
    - 이 키는 생성된 날로부터 5일간 유효합니다.

    ![공급자 및 키 다운로드](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

1. 복제하려는 VM을 실행하는 각 Hyper-V 호스트(또는 클러스터 노드)에 공급자 설치 파일 및 등록 키 파일을 복사합니다.  

## <a name="install-and-register-the-provider"></a>공급자 설치 및 등록

복제하려는 VM을 실행하는 각 Hyper-V 호스트(또는 클러스터 노드)에 공급자 설치 파일 및 등록 키 파일을 복사합니다. 공급자를 설치하고 등록하려면 UI 또는 명령을 사용하여 아래 단계를 수행합니다.

# <a name="using-ui"></a>[UI 사용](#tab/UI)

다음에 설명된 대로 각 호스트에서 공급자 설치 파일을 실행합니다.

1. 작업 표시줄에서 파일 아이콘을 클릭하여 설치 관리자 파일 및 등록 키가 다운로드된 폴더를 엽니다.
1. **AzureSiteRecoveryProvider.exe** 파일을 선택합니다.
    - 공급자 설치 마법사에서 **켜기(권장)** 가 선택되어 있는지 확인한 후 **다음** 을 클릭합니다.
    - **설치** 를 선택하여 기본 설치 폴더를 적용합니다.
    - **등록** 을 선택하여 Azure Site Recovery 자격 증명 모음에 이 서버를 등록합니다.
    - **찾아보기** 를 클릭합니다.
    - 등록 키를 찾고 **열기** 를 클릭합니다.
    - **다음** 을 클릭합니다.
    - **프록시 서버 없이 Azure Site Recovery에 직접 연결** 을 선택했는지 확인하고 **다음** 을 클릭합니다.
    - **Finish** 를 클릭합니다.


# <a name="using-commands"></a>[명령 사용](#tab/commands) 

다음에 설명된 대로 각 호스트에서 다음 명령을 실행합니다.

1. 다음과 같이 설치 관리자 파일(AzureSiteRecoveryProvider.exe)의 콘텐츠를 머신의 로컬 폴더(예: .\Temp)에 추출합니다.

    ```
     AzureSiteRecoveryProvider.exe /q /x:.\Temp\Extracted
    ```

1. 추출된 파일이 있는 폴더로 이동합니다.

    ```
    cd .\Temp\Extracted
    ```
1. Hyper-V 복제본 공급자를 설치합니다. 결과가 에 기록됩니다.

    ```
    .\setupdr.exe /i 
    ```

1. Azure Migrate에 Hyper-V 호스트를 등록합니다.

    ```
    "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Credentials <key file path>
    ```

    **프록시 규칙 구성:** 프록시를 통해 인터넷에 연결해야 하는 경우 선택적 매개 변수 /proxyaddress 및 /proxyport 매개 변수를 사용하여 프록시 주소(양식 http://ProxyIPAddress) 및 프록시 수신 대기 포트)를 지정합니다. 인증된 프록시의 경우 선택적 매개 변수 /proxyusername 및 /proxypassword를 사용할 수 있습니다.

    ``` 
   "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r [/proxyaddress http://ProxyIPAddress] [/proxyport portnumber] [/proxyusername username] [/proxypassword password]
    ```
 
    **프록시 바이패스 규칙 구성:** 프록시 바이패스 규칙을 구성하려면 선택적 매개 변수 /AddBypassUrls를 사용하고 ';'로 구분된 프록시에 대한 바이패스 URL을 제공하고 다음 명령을 실행합니다.

    ```
    "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r [/proxyaddress http://ProxyIPAddress] [/proxyport portnumber] [/proxyusername username] [/proxypassword password] [/AddBypassUrls URLs]
    ```
    및
    ```
    "C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /configure /AddBypassUrls URLs 
    ```
---

호스트에 공급자를 설치한 후 Azure Portal로 이동하고 **머신 검색** 에서 **등록 완료** 를 클릭합니다.

 ![등록 완료](./media/tutorial-migrate-hyper-v/finalize-registration.png) 

검색된 VM이 Azure Migrate 서버 마이그레이션에 표시될 때 등록 완료 후 최대 15분 정도 걸릴 수 있습니다. VM이 검색됨에 따라 **검색된 서버** 수가 증가합니다.

 ![검색된 서버](./media/tutorial-migrate-hyper-v/discovered-servers.png)

## <a name="replicate-hyper-v-vms"></a>Hyper-V VM 복제

검색이 완료되면 Hyper-V VM을 Azure로 복제할 수 있습니다.

> [!NOTE]
> 최대 10대의 머신을 함께 복제할 수 있습니다. 추가로 복제해야 하는 경우 10개씩 일괄 처리로 동시에 복제합니다.

1. Azure Migrate 프로젝트 > **서버**, **Azure Migrate: 서버 마이그레이션** 에서 **복제** 를 클릭합니다.
1. **복제** > **원본 설정** > **머신이 가상화되어 있습니까?** 에서 **예, Hyper-V 사용** 을 선택합니다. 그런 다음, **다음: 가상 머신** 을 클릭합니다.
1. **가상 머신** 에서 복제하려는 머신을 선택합니다.
    - VM 평가를 실행한 경우 평가 결과에서 VM 크기 조정 및 디스크 유형(프리미엄/표준) 추천 사항을 적용할 수 있습니다. 이렇게 하려면 **Azure Migrate 평가에서 마이그레이션 설정을 가져오시겠습니까?** 에서 **예** 옵션을 선택합니다.
    - 평가를 실행하지 않았거나 평가 설정을 사용하지 않으려면 **아니요** 옵션을 선택합니다.
    - 평가를 사용하도록 선택한 경우 VM 그룹 및 평가 이름을 선택합니다.

        ![평가 선택](./media/tutorial-migrate-hyper-v/select-assessment.png)

1. **가상 머신** 에서 필요에 따라 VM을 검색하고, 마이그레이션하려는 각 VM을 확인합니다. 그런 후 **다음: 대상 설정** 을 클릭합니다.

    ![VM 선택](./media/tutorial-migrate-hyper-v/select-vms.png)

1. **대상 설정** 에서 마이그레이션할 대상 지역, 구독 및 마이그레이션 후 Azure VM이 상주할 리소스 그룹을 선택합니다.
1. **복제 스토리지 계정** 에서 복제된 데이터를 Azure에 저장할 Azure Storage 계정을 선택합니다.
1. **Virtual Network** 에서 마이그레이션 후 Azure VM이 조인될 Azure VNet/서브넷을 선택합니다.
1. **가용성 옵션** 에서 다음을 선택합니다.
    -  마이그레이션된 머신을 지역의 특정 가용성 영역에 고정하는 가용성 영역. 이 옵션을 사용하여 가용성 영역에서 다중 노드 애플리케이션 계층을 구성하는 서버를 배포합니다. 이 옵션을 선택하는 경우 Compute 탭에서 선택한 각 머신에 사용할 가용성 영역을 지정해야 합니다. 이 옵션은 마이그레이션을 위해 선택한 대상 지역이 가용성 영역을 지원하는 경우에만 사용할 수 있습니다.
    -  마이그레이션된 머신을 가용성 집합에 배치하기 위한 가용성 집합입니다. 이 옵션을 사용하려면 선택한 대상 리소스 그룹에 하나 이상의 가용성 집합이 있어야 합니다.
    - 마이그레이션된 머신에 대해 이러한 가용성 구성이 필요하지 않은 경우에는 인프라 중복이 필요하지 않습니다.
1. **Azure 하이브리드 혜택** 에서

    - Azure 하이브리드 혜택을 적용하지 않으려면 **아니요** 를 선택합니다. 그런 후 **다음** 을 클릭합니다.
    - 활성 Software Assurance 또는 Windows Server 구독이 적용되는 Windows Server 머신이 있고 마이그레이션할 머신에 이 혜택을 적용하려면 **예** 를 선택합니다. 그런 후 **Next** 를 클릭합니다.

    ![대상 설정](./media/tutorial-migrate-hyper-v/target-settings.png)

1. **Compute** 에서 VM 이름, 크기, OS 디스크 유형 및 가용성 구성을 검토합니다(이전 단계에서 선택한 경우). VM은 [Azure 요구 사항](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)을 준수해야 합니다.

    - **VM 크기**: 평가 권장 사항을 사용하는 경우 VM 크기 드롭다운에 권장 크기가 포함됩니다. 그렇지 않으면 Azure Migrate는 Azure 구독에서 가장 일치하는 항목을 기준으로 크기를 선택합니다. 또는 **Azure VM 크기** 에서 수동 크기를 선택합니다.
    - **OS 디스크**: VM에 맞는 OS(부팅) 디스크를 지정합니다. OS 디스크는 운영 체제 부팅 로더 및 설치 관리자가 있는 디스크입니다.
    - **가용성 집합**: 마이그레이션 후에 VM이 Azure 가용성 집합에 있어야 하는 경우 해당 집합을 지정합니다. 이 집합은 마이그레이션을 위해 지정한 대상 리소스 그룹에 있어야 합니다.

    ![VM 컴퓨팅 설정](./media/tutorial-migrate-hyper-v/compute-settings.png)

1. **디스크** 에서 Azure에 복제해야 하는 VM 디스크를 지정합니다. 그런 후 **Next** 를 클릭합니다.
    - 디스크를 복제에서 제외할 수 있습니다.
    - 디스크를 제외하는 경우 마이그레이션 후 Azure VM에 표시되지 않습니다.

    ![스크린샷은 복제 대화 상자의 디스크 탭을 표시합니다.](./media/tutorial-migrate-hyper-v/disks.png)

1. **검토 및 복제 시작** 에서 설정을 검토하고, **복제** 를 클릭하여 서버에 대한 초기 복제를 시작합니다.

> [!NOTE]
> 복제가 시작되기 전에 언제든지 **관리** > **머신 복제 중** 에서 복제 설정을 업데이트할 수 있습니다. 복제가 시작된 후에는 설정을 변경할 수 없습니다.

## <a name="provision-for-the-first-time"></a>처음으로 프로비전

Azure Migrate 프로젝트에서 복제하는 첫 번째 VM인 경우 Azure Migrate: 서버 마이그레이션에서 프로젝트와 동일한 리소스 그룹에서 이러한 리소스를 자동으로 프로비저닝합니다.
- **캐시 스토리지 계정**: Hyper-V 호스트에 설치된 Azure Site Recovery 공급자 소프트웨어는 복제를 위해 구성된 VM에 대한 복제 데이터를 구독의 스토리지 계정(캐시 스토리지 계정 또는 로그 스토리지 계정이라고 함)에 업로드합니다. 그런 다음, Azure Migrate 서비스는 업로드된 복제 데이터를 스토리지 계정에서 VM에 해당하는 복제본 관리 디스크로 복사합니다. VM에 대한 복제를 구성하는 동안 캐시 스토리지 계정을 지정해야 하며 Azure Migrate 포털은 프로젝트에서 처음으로 복제를 구성할 때 Azure Migrate 프로젝트에 대한 계정을 자동으로 만듭니다.

## <a name="track-and-monitor"></a>추적 및 모니터링


- **복제** 를 클릭하면 복제 시작 작업이 시작됩니다.
- 복제 시작 작업이 완료되면 머신에서 Azure로의 초기 복제를 시작합니다.
- 초기 복제가 완료되면 델타 복제가 시작됩니다. 온-프레미스 디스크에 대한 증분 변경 내용은 Azure에 주기적으로 복제됩니다.

작업 상태는 포털 알림에서 추적할 수 있습니다.

**서버 복제 중** 을 **Azure Migrate: 서버 마이그레이션** 에서 클릭하여 복제 상태를 모니터링할 수 있습니다.
![복제 모니터링](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>테스트 마이그레이션 실행


델타 복제가 시작되면 Azure로 전체 마이그레이션을 실행하기 전에 VM에 대한 테스트 마이그레이션을 실행할 수 있습니다. 이 작업은 마이그레이션하기 전에 각 머신에 대해 한 번 이상 수행하는 것이 좋습니다.

- 테스트 마이그레이션을 실행하면 작동 상태를 유지하고 복제를 계속하는 온-프레미스 머신에 영향을 주지 않고 마이그레이션이 예상대로 작동하는지 확인합니다.
- 테스트 마이그레이션은 복제된 데이터를 사용하여 Azure VM을 만들어 마이그레이션을 시뮬레이션합니다(일반적으로 Azure 구독에서 비프로덕션 Azure VNet으로 마이그레이션).
- 복제된 테스트 Azure VM을 사용하여 마이그레이션의 유효성을 검사하고, 애플리케이션 테스트를 수행하며, 전체 마이그레이션을 수행하기 전에 문제를 해결할 수 있습니다.

다음과 같이 테스트 마이그레이션을 수행합니다.


1. **마이그레이션 목표** > **서버** > **Azure Migrate: 서버 마이그레이션** 에서 **마이그레이션된 서버 테스트** 를 클릭합니다.

     ![마이그레이션된 서버 테스트](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

1. 마우스 오른쪽 단추로 테스트할 VM을 클릭하고, **테스트 마이그레이션** 을 클릭합니다.

    ![테스트 마이그레이션](./media/tutorial-migrate-hyper-v/test-migrate.png)

1. **테스트 마이그레이션** 에서 마이그레이션 후 Azure VM이 위치할 Azure 가상 네트워크를 선택합니다. 비프로덕션 가상 네트워크를 사용하는 것이 좋습니다.
1. **테스트 마이그레이션** 작업이 시작됩니다. 포털 알림에서 작업을 모니터링합니다.
1. 마이그레이션이 완료되면 Azure Portal의 **Virtual Machines** 에서 마이그레이션된 Azure VM을 확인합니다. 머신 이름에는 **-Test** 접미사가 있습니다.
1. 테스트가 완료되면 **머신 복제 중** 에서 마우스 오른쪽 단추로 Azure VM을 클릭하고, **테스트 마이그레이션 정리** 를 클릭합니다.

    ![마이그레이션 정리](./media/tutorial-migrate-hyper-v/clean-up.png)
    > [!NOTE]
    > 이제 SQL IaaS 에이전트 확장을 사용하여 자동화된 패치, 자동화된 백업 및 간소화된 라이선스 관리를 활용하기 위해 SQL Server를 실행하는 서버를 SQL VM RP에 등록할 수 있습니다.
    >- **관리** > **복제 서버** > **SQL 서버를 포함한 머신** > **컴퓨팅 및 네트워크** 를 선택하고 **예** 를 선택하여 SQL VM RP에 등록합니다.
    >- 활성 Software Assurance 또는 SQL Server 구독이 적용되는 SQL Server 인스턴스가 있고 마이그레이션할 머신에 이 혜택을 적용하려면 SQL Server에 대한 Azure 하이브리드 혜택을 선택합니다.

## <a name="migrate-vms"></a>VM 마이그레이션

테스트 마이그레이션이 예상대로 작동하는지 확인한 후에는 온-프레미스 머신을 마이그레이션할 수 있습니다.

1. Azure Migrate 프로젝트 > **서버** > **Azure Migrate: 서버 마이그레이션** 에서 **서버 복제 중** 을 클릭합니다.

    ![서버 복제 중](./media/tutorial-migrate-hyper-v/replicate-servers.png)

1. **머신 복제 중** 에서 마우스 오른쪽 단추로 VM > **마이그레이션** 을 차례로 클릭합니다.
1. **마이그레이션** > **가상 머신을 종료하고 데이터 손실 없이 계획된 마이그레이션을 수행하시겠습니까?** 에서 **예** > **확인** 을 차례로 선택합니다.
    - 기본적으로 Azure Migrate는 온-프레미스 VM을 종료하고, 요청 시 복제를 실행하여 마지막 복제가 수행된 이후 발생한 모든 VM 변경 내용을 동기화합니다. 이렇게 하면 데이터가 손실되지 않습니다.
    - VM을 종료하지 않으려면 **아니요** 를 선택합니다.
1. VM에 대한 마이그레이션 작업이 시작됩니다. Azure 알림에서 작업을 추적합니다.
1. 작업이 완료되면 **Virtual Machines** 페이지에서 VM을 보고 관리할 수 있습니다.

## <a name="complete-the-migration"></a>마이그레이션 완료

1. 마이그레이션이 완료되면 마우스 오른쪽 단추로 VM > **복제 중지** 를 차례로 클릭합니다. 다음을 수행합니다.
    - 온-프레미스 머신에 대한 복제를 중지합니다.
    - Azure Migrate의 **서버 복제** 수에서 머신을 제거합니다. Server Migration에 연결합니다.
    - VM에 대한 복제 상태 정보를 정리합니다.
1. [Azure VM에서 모든 Windows 정품 인증 문제를 확인하고 해결합니다.](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems)
1. 호스트 이름, 데이터베이스 연결 문자열 업데이트, 웹 서버 구성 등의 마이그레이션 후 앱 조정을 수정합니다.
1. 이제 Azure에서 실행 중인 마이그레이션된 애플리케이션에서 최종 애플리케이션 및 마이그레이션 수용 테스트를 수행합니다.
1. 트래픽을 마이그레이션된 Azure VM 인스턴스로 전환합니다.
1. 로컬 VM 인벤토리에서 온-프레미스 VM을 제거합니다.
1. 로컬 백업 작업에서 온-프레미스 VM을 제거합니다.
1. 내부 문서를 업데이트하여 Azure VM의 새 위치 및 IP 주소를 표시합니다.

## <a name="post-migration-best-practices"></a>마이그레이션 후 작업 모범 사례

- 복원력 개선:
    - Azure Backup 서비스를 통해 Azure VM을 백업하여 데이터 보안을 유지합니다. [자세히 알아보기](../backup/quick-backup-vm-portal.md).
    - Site Recovery를 통해 Azure VM을 보조 지역에 복제하면 워크로드를 계속 실행하고 지속적으로 사용할 수 있습니다. [자세히 알아보기](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- 보안 강화:
    - [Azure Security Center - Just-In-Time 관리](../security-center/security-center-just-in-time.md)를 사용하여 인바운드 트래픽 액세스를 잠그고 제한합니다.
    - [네트워크 보안 그룹](../virtual-network/network-security-groups-overview.md)을 사용하여 관리 엔드포인트에 대한 네트워크 트래픽을 제한합니다.
    - [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)을 배포하여 디스크를 보호하고 데이터를 도난 및 무단 액세스로부터 안전하게 유지합니다.
    - [IaaS 리소스 보호](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)에 대해 자세히 알아보고 [Azure Security Center](https://azure.microsoft.com/services/security-center/)를 방문하세요.
- 모니터링 및 관리 앱:
-  리소스 사용량과 비용을 모니터링하려면 [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md)를 배포하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

Azure 클라우드 채택 프레임워크에서 [클라우드 마이그레이션 과정](/azure/architecture/cloud-adoption/getting-started/migrate)을 조사합니다.
