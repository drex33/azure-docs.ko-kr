---
title: Azure Migrate 어플라이언스 문제 해결
description: Azure Migrate 어플라이언스에서 발생할 수 있는 문제를 해결하는 데 도움을 받으세요.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: c67ad02737fc8a40895f99cd3acf05ba98e7643e
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123305898"
---
# <a name="troubleshoot-the-azure-migrate-appliance"></a>Azure Migrate 어플라이언스 문제 해결

이 문서는 [Azure Migrate](migrate-services-overview.md) 어플라이언스를 배포할 때와 어플라이언스를 사용하여 온-프레미스 서버를 검색할 때 발생하는 문제를 해결하는 데 도움이 됩니다.

## <a name="whats-supported"></a>지원되는 기능

어플라이언스 지원 요구 사항을 [검토](migrate-appliance.md)합니다.

## <a name="invalid-ovf-manifest-entry-error-occurs-during-appliance-setup"></a>어플라이언스를 설정하는 동안 ‘잘못된 OVF 매니페스트 항목’ 오류가 발생합니다.

OVA 템플릿을 사용하여 어플라이언스를 설정할 때 ‘제공된 매니페스트 파일이 잘못됨: 잘못된 OVF 매니페스트 항목’ 오류가 표시됩니다.

### <a name="remediation"></a>수정

1. 해당 해시 값을 확인하여 Azure Migrate appliance OVA 파일이 올바르게 다운로드되는지 확인합니다. [자세히 알아보기](./tutorial-discover-vmware.md). 해시 값이 일치하지 않는 경우 OVA 파일을 다시 다운로드하고 배포를 다시 시도합니다.
1. 그래도 배포가 실패하거나 VMware vSphere 클라이언트를 사용하여 OVF 파일을 배포하는 경우 vSphere 웹 클라이언트를 통해 배포해 보세요. 그래도 배포가 실패하면 다른 웹 브라우저를 사용하여 시도해보세요.
1. vSphere 웹 클라이언트를 사용 중이며 vCenter Server 6.5 또는 6.7에서 이를 배포하려는 경우, ESXi 호스트에서 직접 OVA 배포를 시도합니다.
   - 웹 클라이언트(https://<*host IP Address*>/ui)를 사용하여 ESXi 호스트에 직접 연결합니다(vCenter Server 대신).
   - **Home**  >  **Inventory** 에서 **파일**  >  **Deploy OVF 템플릿** 을 선택합니다. OVA로 찾아보고 배포를 완료합니다.
1. 배포가 여전히 실패하는 경우 Azure Migrate 지원에 문의합니다.

## <a name="connectivity-check-fails-during-the-prerequisites-setup"></a>필수 조건을 설정하는 동안 연결 확인 실패

어플라이언스의 연결 확인에서 오류가 발생합니다.

### <a name="remediation"></a>수정

1. 어플라이언스에서 필요한 [URL](./migrate-appliance.md#url-access)에 연결할 수 있는지 확인합니다.
1. 이러한 URL에 대한 액세스를 차단하는 프록시 또는 방화벽이 있는지 확인합니다. 허용 목록을 만들어야 하는 경우 모든 URL이 포함되어 있는지 확인합니다.
1. 온-프레미스에 구성된 프록시 서버가 있는 경우 동일한 단계에서 **프록시 설정** 을 선택하여 프록시 세부 정보를 입력합니다. 프록시에 필요한 경우 권한 부여 자격 증명을 입력합니다.
1. 서버가 이전에 [복제 어플라이언스](./migrate-replication-appliance.md)를 설정하는 데 사용된 적이 없거나 서버에 Mobility Service 에이전트가 설치되어 있는지 확인합니다.

## <a name="connectivity-check-fails-for-the-akams-url-during-the-prerequisites-setup"></a>필수 조건을 설정하는 동안 aka.ms URL에 대한 연결 확인 실패

aka.ms URL에 대한 어플라이언스의 연결 확인에서 오류가 발생합니다.

### <a name="remediation"></a>수정

1. 인터넷에 연결되어 있고 최신 버전의 서비스를 다운로드할 수 있도록 URL-aka.ms/*를 허용 목록에 추가했는지 확인합니다.
1. 이 URL에 대한 액세스를 차단하는 프록시/방화벽이 있는지 확인합니다. 구성 관리자의 필수 조건 단계에서 프록시 세부 정보를 올바르게 제공했는지 확인합니다.
1. 어플라이언스 구성 관리자로 돌아가 필수 조건을 다시 실행하여 자동 업데이트를 시작합니다.
1. 다시 시도해도 문제가 해결되지 않으면 [이 웹 사이트](https://aka.ms/latestapplianceservices)에서 *latestcomponents.json* 파일을 다운로드하여 실패한 서비스의 최신 버전을 확인합니다. 파일의 다운로드 링크에서 수동으로 업데이트합니다.

 **프라이빗 엔드포인트 연결** 에 대해 어플라이언스를 사용하도록 설정했고 인터넷을 통해 이 URL에 대한 액세스를 허용하지 않으려면 aka.ms 링크가 이 서비스에 필요하므로 [자동 업데이트를 사용하지 않도록 설정](./migrate-appliance.md#turn-off-auto-update)합니다.

 >[!Note]
 >자동 업데이트 서비스를 사용하지 않도록 설정하는 경우 어플라이언스에서 실행되는 서비스는 최신 업데이트를 자동으로 가져오지 않습니다. 이 상황을 해결하려면 [어플라이언스 서비스를 수동으로 업데이트](./migrate-appliance.md#manually-update-an-older-version)합니다.

## <a name="auto-update-check-fails-during-the-prerequisites-setup"></a>필수 조건을 설정하는 동안 자동 업데이트 확인 실패

어플라이언스의 자동 업데이트 확인에서 오류가 발생합니다.

### <a name="remediation"></a>수정

1. [필수 URL](./migrate-appliance.md#url-access)에 대한 허용 목록을 만들었고 URL을 차단하는 프록시/방화벽 설정이 없는지 확인합니다.
1. 모든 어플라이언스 구성 요소에 대한 업데이트가 실패하는 경우 필수 조건을 다시 실행하거나 [구성 요소를 수동으로 업데이트](./migrate-appliance.md#manually-update-an-older-version)합니다.

## <a name="time-sync-check-fails-during-the-prerequisites-setup"></a>필수 조건을 설정하는 동안 시간 동기화 확인 실패

시간 동기화에 대한 오류는 서버 클록이 현재 시간과 5분 넘게 동기화되지 않았을 수 있음을 나타냅니다.

### <a name="remediation"></a>수정

- 제어판에서 날짜 및 시간 설정을 확인하여 어플라이언스 서버 시간이 인터넷 시간과 동기화되었는지 확인합니다.
- 다음 단계에 따라 어플라이언스 서버의 시계 시간을 현재 시간과 일치하도록 변경할 수도 있습니다.
     1. 서버에서 관리자 명령 프롬프트를 엽니다.
     1. 표준 시간대를 확인하려면 **w32tm /tz** 명령을 실행합니다.
     1. 시간을 동기화하려면 **w32tm /resync** 명령을 실행합니다.

## <a name="vddk-check-fails-during-the-prerequisites-setup-on-the-vmware-appliance"></a>VMware 어플라이언스에서 필수 조건을 설정하는 동안 VDDK 확인 실패

어플라이언스가 어플라이언스에 설치된 필수 VDDK(Virtual Disk Development Kit)를 찾을 수 없어 VDDK 검사가 실패했습니다. 이 문제로 인해 지속적 복제가 실패할 수 있습니다.

### <a name="remediation"></a>수정

1. VDDK 6.7을 다운로드하고 해당 파일을 어플라이언스 서버의 **C:\Program Files\VMware\VMware Virtual Disk Development Kit** 에 복사했는지 확인합니다.
1. 다른 소프트웨어나 애플리케이션이 어플라이언스에서 다른 버전의 VDDK를 사용하고 있지 않은지 확인합니다.

## <a name="project-key-related-error-occurs-during-appliance-registration"></a>어플라이언스를 등록하는 동안 프로젝트 키 관련 오류 발생

 프로젝트에서 복사된 Azure Migrate 프로젝트 키를 사용하여 어플라이언스를 등록하려고 하면 문제가 발생합니다.

### <a name="remediation"></a>수정

1. 프로젝트에서 올바른 키를 복사했는지 확인합니다. 프로젝트의 **Azure Migrate: 검색 및 평가** 카드에서 **검색** 을 선택합니다. 그런 다음 1단계의 **기존 어플라이언스 관리** 를 선택합니다. 드롭다운 메뉴에서 이전에 키를 생성한 어플라이언스 이름을 선택합니다. 해당 키를 복사합니다.
1. 키를 오른쪽 **클라우드 유형**(공용/US Gov) 및 **어플라이언스 유형**(VMware/Hyper-V/물리적 또는 기타)에 붙여넣었는지 확인합니다. 어플라이언스 구성 관리자의 맨 위를 확인하여 클라우드 및 시나리오 유형을 확인합니다.

## <a name="failed-to-connect-to-the-azure-migrate-project-error-occurs-during-appliance-registration"></a>어플라이언스를 등록하는 동안 ‘Azure Migrate 프로젝트에 연결하지 못함’ 오류 발생

Azure 사용자 계정에 성공적으로 로그인한 후에 다음 메시지와 함께 어플라이언스 등록 단계가 실패합니다. "Azure Migrate 프로젝트에 연결하지 못했습니다. 오류 세부 정보를 확인하고 다시 시도를 클릭하여 수정 단계를 따르세요."

어플라이언스 구성 관리자에서 로그인하는 데 사용한 Azure 사용자 계정이 포털에서 Azure Migrate 프로젝트 키를 생성하는 데 사용된 사용자 계정과 다른 경우 문제가 발생합니다.

### <a name="remediation"></a>수정

다음과 같은 두 가지 옵션이 있습니다.

- 어플라이언스 등록을 완료하려면 포털에서 Azure Migrate 프로젝트 키를 생성한 것과 동일한 Azure 사용자 계정을 사용합니다.
- 또한 어플라이언스 등록에 사용되는 다른 Azure 사용자 계정에 필요한 역할 및 [권한](./tutorial-discover-vmware.md#prepare-an-azure-user-account)을 할당할 수도 있습니다.

## <a name="azure-active-directory-aad-operation-failed-with-status-forbidden-error-occurs-during-appliance-registration"></a>어플라이언스를 등록하는 동안 ‘AAD(Azure Active Directory) 작업이 사용할 수 없음 상태로 실패함’ 오류 발생

Azure Active Directory 권한이 부족하여 등록을 완료할 수 없고 ‘AAD(Azure Active Directory) 작업이 사용할 수 없음 상태로 실패함’ 오류가 발생합니다.

### <a name="remediation"></a>수정

Azure에서 Azure Active Directory 애플리케이션을 만들고 관리하는 데 [필요한 권한](./tutorial-discover-vmware.md#prepare-an-azure-user-account)이 있는지 확인합니다. **애플리케이션 개발자** 역할 또는 테넌트 수준에서 허용된 **사용자가 애플리케이션을 등록할 수 있음** 사용자 역할이 있어야 합니다.

## <a name="forbidden-to-access-key-vault-error-occurs-during-appliance-registration"></a>어플라이언스를 등록하는 동안 ‘Key Vault에 액세스할 수 없음’ 오류 발생

‘{KeyVaultErrorMessage}’ 오류로 인해 ‘{KeyVaultName}’에 대한 Azure Key Vault 만들기 또는 업데이트 작업이 실패했습니다.

이 문제는 일반적으로 어플라이언스를 등록하는 데 사용된 Azure 사용자 계정이 포털에서 Azure Migrate 프로젝트 키를 생성하는 데(즉, 키 자격 증명 모음이 생성되었을 때) 사용되는 계정과 다를 때 발생합니다.

### <a name="remediation"></a>수정

1. 어플라이언스에 현재 로그인한 사용자 계정에 키 자격 증명 모음에 대한 필수 권한(오류 메시지에 언급됨)이 있는지 확인합니다. 사용자 계정에는 [이 웹 사이트](./tutorial-discover-vmware.md#prepare-an-azure-user-account)에서 언급된 권한이 필요합니다.
1. 키 자격 증명 모음으로 이동하여 사용자 계정에 **키 자격 증명 모음 액세스 정책** 에서 할당된 모든 **키**, **비밀** 및 **인증서** 권한이 있는 액세스 정책이 있는지 확인합니다. [자세히 알아보기](../key-vault/general/assign-access-policy-portal.md).
1. **프라이빗 엔드포인트 연결** 에 대해 어플라이언스를 사용하도록 설정한 경우 어플라이언스가 키 자격 증명 모음이 만들어진 동일한 가상 네트워크에서 호스트되거나 키 자격 증명 모음이 만들어진 Azure 가상 네트워크에 프라이빗 링크를 통해 연결되어 있는지 확인합니다. 키 자격 증명 모음 프라이빗 링크를 어플라이언스에서 확인할 수 있는지 확인합니다. **Azure Migrate: 검색 및 평가** > **속성** 으로 이동하여 Azure Migrate 키가 만들어지는 중에 만들어진 키 자격 증명 모음과 같은 리소스의 프라이빗 엔드포인트 세부 정보를 찾습니다. [자세히 알아보기](./troubleshoot-network-connectivity.md).
1. 필요한 권한 및 연결이 있는 경우 잠시 후 어플라이언스에서 등록을 다시 시도합니다.

## <a name="unable-to-connect-to-vcenter-server-during-validation"></a>유효성 검사 중에 vCenter Server에 연결할 수 없음

이 연결 오류가 발생하는 경우 vCenter Server *Servername*.com:9443에 연결하지 못할 수 있습니다. 오류 세부 정보는 메시지를 수락할 수 있는 `https://\*servername*.com:9443/sdk`에 수신 대기하는 엔드포인트가 없음을 나타냅니다.

### <a name="remediation"></a>수정

- 최신 버전의 어플라이언스를 실행하고 있는지 확인합니다. 그렇지 않은 경우 어플라이언스를 [최신 버전](./migrate-appliance.md)으로 업그레이드하세요.
- 최신 버전에서 문제가 계속 발생하는 경우, 기기가 지정된 vCenter Server 이름을 확인할 수 없거나 지정된 포트가 잘못되었을 수 있습니다. 기본적으로 포트가 지정되지 않은 경우 수집기는 포트 번호 443에서 연결을 시도합니다.

    1. 어플라이언스에서 *Servername*.com을 Ping 합니다.
    1. 1단계가 실패하는 경우 IP 주소를 사용하여 vCenter Server에 연결해 봅니다.
    1. vCenter Server에 연결할 정확한 포트 번호를 식별합니다.
    1. vCenter Server가 실행 중인지 확인합니다.

## <a name="server-credentials-domain-fails-validation-on-the-vmware-appliance"></a>VMware 어플라이언스에서 서버 자격 증명(도메인) 유효성 검사 실패

소프트웨어 인벤토리 및 에이전트 없는 종속성 분석을 수행하기 위해 VMware 어플라이언스에 추가된 도메인 자격 증명에 대해 ‘유효성 검사 실패’가 표시됩니다.

### <a name="remediation"></a>수정

1. 올바른 도메인 이름과 자격 증명을 제공했는지 확인합니다.
1. 자격 증명의 유효성을 검사하기 위해 어플라이언스에서 도메인에 연결할 수 있는지 확인합니다. 어플라이언스에 시야 문제가 있거나 어플라이언스 서버에서 도메인 이름을 확인할 수 없는 것일 수 있습니다.
1. **편집** 을 선택하여 도메인 이름 또는 자격 증명을 업데이트합니다. 얼마 후에 **자격 증명 유효성 재검사** 를 선택하여 자격 증명의 유효성을 다시 검사합니다.

## <a name="access-is-denied-error-occurs-when-you-connect-to-hyper-v-hosts-or-clusters-during-validation"></a>유효성 검사 중 Hyper-V 호스트 또는 클러스터에 연결할 때 ‘액세스 거부됨’오류 발생

‘액세스 거부됨’ 오류로 인해 추가된 Hyper-V 호스트 또는 클러스터의 유효성을 검사할 수 없습니다.

### <a name="remediation"></a>수정

1. [Hyper-V 호스트의 필수 조건](./migrate-support-matrix-hyper-v.md#hyper-v-host-requirements)을 모두 충족했는지 확인합니다. 
1. 수동으로 또는 프로비저닝 PowerShell 스크립트를 사용하여 Hyper-V 호스트를 준비하는 방법은 [이 웹 사이트](./tutorial-discover-hyper-v.md#prepare-hyper-v-hosts)에서 단계를 확인하세요.

## <a name="the-server-does-not-support-ws-management-identify-operations-error-occurs-during-validation"></a>유효성 검사 중 ‘서버가 WS-Management 식별 작업을 지원하지 않음’ 오류 발생

‘서버가 WS-Management 식별 작업을 지원하지 않음’ 오류로 인해 어플라이언스에서 Hyper-V 클러스터의 유효성을 검사할 수 없습니다. 요청의 TestConnection 부분을 건너뛰고 다시 시도하세요."

### <a name="remediation"></a>수정

이 오류는 일반적으로 어플라이언스에 프록시 구성을 제공한 경우에 발생합니다. 노드의 FQDN를 제공하는 경우에도 어플라이언스는 클러스터 노드의 짧은 이름을 사용하여 클러스터에 연결합니다. 클러스터 노드의 짧은 이름을 어플라이언스의 바이패스 프록시 목록에 추가하면 문제가 해결되고 Hyper-V 클러스터의 유효성 검사가 성공합니다.

## <a name="cant-connect-to-host-or-cluster-error-occurs-during-validation-on-a-hyper-v-appliance"></a>Hyper-V 어플라이언스에서 유효성 검사 중 ‘호스트 또는 클러스터에 연결할 수 없음’ 오류 발생

오류 "서버 이름을 확인할 수 없기 때문에 호스트 또는 클러스터에 연결할 수 없습니다. WinRM 오류 코드: 0x803381B9 "는 어플라이언스에 대한 Azure DNS 서비스에서 사용자가 제공한 클러스터 또는 호스트 이름을 확인할 수 없을 때 발생할 수 있습니다.

이 문제는 일반적으로 DNS로 확인할 수 없는 호스트의 IP 주소를 추가한 경우에 발생합니다. 클러스터의 호스트에 대해 이 오류가 나타날 수도 있습니다. 이는 어플라이언스를 클러스터에 연결할 수 있지만 클러스터에서 FQDN이 아닌 호스트 이름을 반환한다는 것을 뜻합니다.

### <a name="remediation"></a>수정

이 오류를 해결하려면 IP 주소와 호스트 이름의 매핑을 추가하여 어플라이언스의 hosts 파일을 업데이트합니다.
1. 관리자 권한으로 메모장을 엽니다.
1. C:\Windows\System32\Drivers\etc\hosts file을 엽니다.
1. IP 주소와 호스트 이름을 행에 추가합니다. 이 오류가 표시되는 각 호스트 또는 클러스터에 대해 반복합니다.
1. 변경 내용을 저장하고 호스트 파일을 닫습니다.
1. 어플라이언스 관리 앱을 사용하여 어플라이언스가 호스트에 연결할 수 있는지 여부를 확인합니다. 30분 후 Azure Portal에 이러한 호스트에 대한 최신 정보가 표시됩니다.

## <a name="unable-to-connect-to-server-error-occurs-during-validation-of-physical-servers"></a>물리적 서버의 유효성 검사 중 ‘서버에 연결할 수 없음’ 오류 발생

### <a name="remediation"></a>수정

- 어플라이언스에서 대상 서버로의 연결이 있는지 확인합니다.
- Linux 서버인 경우 다음 단계를 사용하여 암호 기반 인증을 사용하도록 설정해야 합니다.
    1. Linux 서버에 로그인하고 **vi /etc/ssh/sshd_config** 명령을 사용하여 ssh 구성 파일을 엽니다.
    1. **PasswordAuthentication** 옵션을 예로 설정합니다. 파일을 저장합니다.
    1. **service sshd restart** 를 실행하여 ssh 서비스를 다시 시작합니다.
- Windows 서버인 경우 원격 WMI 호출을 허용하려면 포트 5985가 열려 있어야 합니다.
- GCP Linux 서버를 검색하고 루트 사용자를 사용하는 경우, 다음 명령을 사용하여 루트 로그인에 대한 기본 설정을 변경합니다.
    1. Linux 서버에 로그인하고 **vi /etc/ssh/sshd_config** 명령을 사용하여 ssh 구성 파일을 엽니다.
    1. **PermitRootLogin** 옵션을 예로 설정합니다.
    1. **service sshd restart** 를 실행하여 ssh 서비스를 다시 시작합니다.

## <a name="failed-to-fetch-bios-guid-error-occurs-for-the-server-during-validation"></a>유효성 검사 중 서버에 대해 ‘BIOS GUID를 가져오지 못함’ 오류 발생

어플라이언스에서 ‘BIOS GUID를 가져오지 못함’이라는 오류 메시지와 함께 물리적 서버의 유효성 검사가 실패합니다.

### <a name="remediation"></a>수정

**Linux 서버:**

유효성 검사가 실패한 대상 서버에 연결합니다. 다음 명령을 실행하여 서버의 BIOS GUID를 반환하는지 확인합니다.

````
cat /sys/class/dmi/id/product_uuid
dmidecode | grep -i uuid | awk '{print $2}'
````
다음 명령을 사용하여 대상 Linux 서버에 SSH 연결을 설정하여 어플라이언스 서버의 명령 프롬프트에서 명령을 실행할 수도 있습니다.
````
ssh <username>@<servername>
````

**Windows 서버:**

유효성 검사에 실패한 대상 서버의 어플라이언스 서버에서 PowerShell의 다음 코드를 실행하여 서버의 BIOS GUID를 반환하는지 확인합니다.

````
[CmdletBinding()]
Param(
  [Parameter(Mandatory=$True,Position=1)]
   [string]$Hostname
)
$HostNS = "root\cimv2"
$error.Clear()

$Cred = Get-Credential
$Session = New-CimSession -Credential $Cred -ComputerName $Hostname

if ($Session -eq $null -or $Session.TestConnection() -eq $false)
{
    Write-Host "Connection failed with $Hostname due to $error"
    exit -1
}

Write-Host "Connection established with $Hostname"
#Get-WmiObject -Query "select uuid from Win32_ComputerSystemProduct" 

$HostIntance = $Session.QueryInstances($HostNS, "WQL", "Select UUID from Win32_ComputerSystemProduct")
$HostIntance | fl *
````

위의 코드를 실행할 때 대상 서버의 호스트 이름을 제공해야 합니다. IP 주소/FQDN/호스트 이름입니다. 그런 다음 서버에 연결하기 위한 자격 증명을 제공하라는 메시지가 표시됩니다.

## <a name="no-suitable-authentication-method-found-error-occurs-for-the-server-during-validation"></a>유효성 검사 중 서버에 대해 ‘적합한 인증 방법을 찾을 수 없음’ 오류 발생

물리적 어플라이언스를 통해 Linux 서버의 유효성을 검사하려고 하면 ‘적절한 인증 방법을 찾을 수 없음’ 오류가 발생합니다.

### <a name="remediation"></a>수정

다음 단계를 사용하여 Linux 서버에서 암호 기반 인증을 사용하도록 설정했는지 확인합니다.

1. Linux 서버에 로그인합니다. **vi /etc/ssh/sshd_config** 명령을 사용하여 ssh 구성 파일을 엽니다.
1. **PasswordAuthentication** 옵션을 **예** 로 설정합니다. 파일을 저장합니다.
1. **service sshd restart** 를 실행하여 ssh 서비스를 다시 시작합니다.

## <a name="access-is-denied-error-occurs-when-you-connect-to-physical-servers-during-validation"></a>유효성 검사 중 물리적 서버에 연결할 때 ‘액세스 거부됨’ 오류 발생

물리적 어플라이언스 통해 Windows 서버의 유효성을 검사하려고 할 때 "WS-Management 서비스에서 요청을 처리할 수 없습니다. WMI 서비스가 액세스 거부 오류를 반환했습니다." 오류가 발생합니다.

### <a name="remediation"></a>수정

- 이 오류가 발생하면 어플라이언스 구성 관리자에 제공된 사용자 계정(도메인/로컬)이 원격 관리 사용자, 성능 모니터 사용자 및 성능 로그 사용자 그룹에 추가되었는지 확인합니다.
- 원격 관리 사용자 그룹이 없는 경우 WinRMRemoteWMIUsers_ 그룹에 사용자 계정을 추가합니다.
- 대상 서버의 명령 프롬프트에서 다음 명령을 실행하여 서버에서 WS-Management 프로토콜이 사용하도록 설정되어 있는지 확인할 수도 있습니다.
    
    ```` winrm qc ````

- 여전히 문제가 발생하면 사용자 계정에 WMI 제어판의 CIMV2 네임스페이스 및 하위 네임스페이스에 대한 액세스 권한이 있는지 확인합니다. 다음 단계에 따라 액세스 권한을 설정할 수 있습니다.

    1. 어플라이언스에서 유효성 검사가 실패한 서버로 이동합니다.
    1. **시작** 메뉴에서 **실행** 을 검색하여 선택합니다. **실행** 대화 상자의 **열기** 텍스트 상자에 **wmimgmt.msc** 를 입력하고 **Enter** 를 선택합니다.
    1. 열린 wmimgmt 콘솔의 왼쪽 패널에서 **WMI 컨트롤(로컬)** 을 찾을 수 있습니다. 이를 마우스 오른쪽 단추로 클릭하고 메뉴에서 **속성** 을 선택합니다.
    1. **WMI 컨트롤(로컬) 속성** 대화 상자에서 **보안** 탭을 선택합니다.
    1. **보안** 탭에서 네임스페이스 트리의 **루트** 폴더를 확장하고 **cimv2** 네임스페이스를 선택합니다.
    1. **보안** 을 선택하여 **ROOT\cimv2에 대한 보안** 대화 상자를 엽니다.
    1. **그룹 또는 사용자 이름** 섹션에서 **추가** 를 선택하여 **사용자, 컴퓨터, 서비스 계정 또는 그룹 선택** 대화 상자를 엽니다.
    1. 사용자 계정을 검색하고 선택한 후 **확인** 을 선택하여 **ROOT\cimv2에 대한 보안** 대화 상자로 돌아갑니다.
    1. **그룹 또는 사용자 이름** 섹션에서 방금 추가한 사용자 계정을 선택합니다. 다음 권한이 허용되는지 확인합니다.<br/>
       - 계정 사용 <br/>
       - 원격으로부터 사용 가능
    1. **적용** 을 선택하여 사용자 계정에 설정된 권한을 사용하도록 설정합니다.

- 비 도메인/작업 그룹이 아닌 서버의 로컬 사용자 계정에도 동일한 단계를 적용할 수 있습니다. 경우에 따라 명령이 표준 사용자로 실행될 때 [UAC](/windows/win32/wmisdk/user-account-control-and-wmi) 필터링이 일부 WMI 속성을 차단할 수 있으므로 로컬 관리자 계정을 사용하거나 UAC를 사용하지 않도록 설정하여 로컬 사용자 계정이 필터링되지 않고 대신 전체 관리자가 되도록 할 수 있습니다.
- 원격 UAC를 제어하는 레지스트리 항목을 변경하여 원격 UAC를 사용하지 않도록 설정하는 것은 권장되지 않지만 작업 그룹에서는 필요할 수 있습니다. 레지스트리 항목은 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\system\LocalAccountTokenFilterPolicy입니다. 이 항목의 값이 영(0)이면 원격 UAC 액세스 토큰 필터링이 사용하도록 설정됩니다. 값이 1이면 원격 UAC가 사용하지 않도록 설정됩니다.

## <a name="appliance-is-disconnected"></a>어플라이언스 연결이 끊어졌습니다.

포털에서 일부 VMware 서버에 복제를 사용하도록 설정하려고 하면 “어플라이언스 연결이 끊어졌습니다." 오류 메시지가 나타납니다.

이 오류는 어플라이언스가 종료 상태이거나 어플라이언스의 DRA 서비스가 Azure와 통신할 수 없는 경우 발생할 수 있습니다.

### <a name="remediation"></a>수정

 1. 어플라이언스 구성 관리자로 이동하고 필수 조건을 다시 실행하여 **어플라이언스 서비스 보기** 에서 DRA 서비스의 상태를 확인합니다. 
 1. 서비스가 실행되고 있지 않으면 다음 명령을 사용하여 명령 프롬프트에서 서비스를 중지하고 다시 시작하세요.

    ````
    net stop dra
    net start dra
    ````

## <a name="next-steps"></a>다음 단계

[VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md) 또는 [물리적 서버](how-to-set-up-appliance-physical.md)에 대해 어플라이언스를 설정합니다.