---
title: 에이전트 없는 VMware VM 마이그레이션에서 복제 문제 해결
description: 복제 주기 오류에 대한 도움말 제공
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: d1654a195f694b9ef8ab8150380939c0e647bcd1
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580677"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>에이전트 없는 VMware VM 마이그레이션에서 복제 문제 해결

이 문서에서는 Azure Migrate: 서버 마이그레이션 에이전트 없는 방법을 사용하여 온-프레미스 VMware VM을 복제할 때 발생할 수 있는 몇 가지 일반적인 문제와 특정 오류를 설명합니다.

에이전트 없는 복제 방법을 사용하여 VMware 가상 머신을 복제하면 가상 머신 디스크(vmdks)의 데이터가 Azure 구독의 복제본 관리 디스크에 복제됩니다. VM에 대한 복제가 시작되면 초기 복제 주기가 발생하며 이때 디스크의 전체 복사본이 복제됩니다. 초기 복제를 완료한 후에는 증분 복제 주기가 주기적으로 예약되어 이전 복제 주기 이후에 발생한 변경 내용을 전송합니다.

VM의 복제 주기가 실패하는 경우가 있습니다. 이러한 오류는 온-프레미스 네트워크 구성의 문제부터 Azure Migrate 클라우드 서비스 백 엔드의 문제까지 다양한 이유로 인해 발생할 수 있습니다. 이 문서의 내용은 다음과 같습니다.

 - 복제 상태를 모니터링하고 오류를 해결하는 방법을 보여줍니다.
 - 자주 발생하는 복제 오류를 나열하고 오류를 해결하기 위한 추가 단계를 제안합니다.

## <a name="monitor-replication-status-using-the-azure-portal"></a>Azure Portal을 사용하여 복제 상태 모니터링

다음 단계에 따라 가상 머신의 복제 상태를 모니터링합니다.

  1. Azure Portal에서 Azure Migrate의 서버 페이지로 이동합니다.
  ![이미지 1](./media/troubleshoot-changed-block-tracking-replication/image0.png)
  1. 서버 마이그레이션 타일에서 "서버 복제"를 클릭하여 "머신 복제" 페이지로 이동합니다.
  ![이미지 2](./media/troubleshoot-changed-block-tracking-replication/image1.png)
  1. 상태, 마지막 동기화 시간 등의 추가 정보와 함께 복제 서버 목록이 표시됩니다. 상태 열은 VM의 현재 복제 상태를 나타냅니다. 상태 열의 '위험' 또는 '경고' 값은 일반적으로 VM의 이전 복제 주기가 실패했음을 나타냅니다. 자세한 내용을 보려면 VM을 마우스 오른쪽 단추로 클릭하고 "오류 세부 정보"를 선택합니다. 오류 세부 정보 페이지에는 오류에 대한 정보와 문제 해결 방법에 대한 추가 세부 정보가 포함되어 있습니다. VM의 이벤트 페이지로 이동하는 데 사용할 수 있는 "최근 이벤트" 링크도 표시됩니다.
  ![이미지 3](./media/troubleshoot-changed-block-tracking-replication/image2.png)
  1. "최근 이벤트"를 클릭하여 VM의 이전 복제 주기 실패를 확인합니다. 이벤트 페이지에서 VM에 대한 "복제 주기 실패" 또는 "디스크의 복제 주기 실패" 유형의 최신 이벤트를 찾습니다.
  ![이미지 4](./media/troubleshoot-changed-block-tracking-replication/image3.png)
  1. 이벤트를 클릭하여 오류의 원인과 권장 수정 단계를 파악합니다. 제공된 정보를 사용하여 오류를 해결하고 수정합니다.
 ![이미지 5](./media/troubleshoot-changed-block-tracking-replication/image4.png)

## <a name="common-replication-errors"></a>일반적인 복제 오류

이 섹션에서는 몇 가지 일반적인 오류와 이러한 오류를 해결하는 방법을 설명합니다.

## <a name="key-vault-operation-failed-error-when-trying-to-replicate-vms"></a>VM을 복제하는 동안 Key Vault 작업 실패 오류

**오류**: “Key Vault 작업이 실패했습니다. 작업: 관리되는 스토리지 계정, Key Vault: Key-vault-name, 스토리지 계정: 스토리지 계정 이름이 다음 오류와 함께 실패했습니다.”

**오류**: “Key Vault 작업이 실패했습니다. 작업: 공유 액세스 서명 정의 생성, Key Vault: Key-vault-name, 스토리지 계정: 스토리지 계정 이름이 다음 오류와 함께 실패했습니다.”

![Key Vault](./media/troubleshoot-changed-block-tracking-replication/key-vault.png)

이 오류는 일반적으로 Key Vault에 대한 사용자 액세스 정책에서 현재 로그인한 사용자에게 스토리지 계정을 Key Vault 관리형으로 구성하는 데 필요한 권한을 제공하지 않기 때문에 발생합니다. 키 자격 증명 모음에 대한 사용자 액세스 정책을 확인하려면 키 자격 증명 모음에 대한 포털의 키 자격 증명 모음 페이지로 이동하여 액세스 정책을 선택합니다. 

포털에서는 키 자격 증명 모음을 만들 때 현재 로그인한 사용자에게 스토리지 계정을 Key Vault 관리형으로 구성하는 데 필요한 권한을 부여하는 사용자 액세스 정책도 추가합니다. 이 작업이 다음과 같은 두 가지 이유로 실패할 수 있습니다.

- 로그인한 사용자가 고객 Azure 테넌트(CSP 구독)의 원격 보안 주체(그리고 로그인한 사용자가 파트너 관리자)입니다. 이 문제를 해결하는 방법은 키 자격 증명 모음을 삭제하고 포털에서 로그아웃한 다음, 원격 보안 주체가 아닌 고객 테넌트의 사용자 계정으로 로그인하여 작업을 다시 시도하는 것입니다. CSP 파트너는 일반적으로 고객 Azure Active Directory 테넌트에서 사용할 수 있는 사용자 계정이 있습니다. 이러한 계정이 없는 경우 고객 Azure Active Directory 테넌트에서 직접 새 사용자 계정을 만들고, 포털에 새 사용자로 로그인하여 복제 작업을 다시 시도할 수 있습니다. 사용되는 계정에는 리소스 그룹(Migrate 프로젝트 리소스 그룹)의 계정에 부여된 소유자 또는 기여자+사용자 액세스 관리자 권한이 있어야 합니다.

- 이 오류가 발생할 수 있는 또 다른 상황은 한 사용자(user1)가 처음에 복제를 설정하려고 시도할 때 오류가 발생했지만 키 자격 증명 모음이 이미 생성된(그리고 사용자 액세스 정책이 이 사용자에게 적절하게 할당된) 경우입니다. 이제 나중에 다른 사용자(user2)가 복제를 설정하려고 시도하지만 키 자격 증명 모음에 user2에 해당하는 사용자 액세스 정책이 없으므로 관리형 스토리지 계정 구성 또는 SAS 정의 생성 작업이 실패합니다.

**해결 방법**: 이 문제를 해결하려면 user2에게 관리형 스토리지 계정을 구성하고 SAS 정의를 생성할 수 있는 권한을 부여하는 user2에 대한 사용자 액세스 정책을 키 자격 증명 모음에 만듭니다. User2는 Azure PowerShell에서 아래 cmdlet을 사용하여 이 작업을 수행할 수 있습니다.

$userPrincipalId = $(Get-AzureRmADUser -UserPrincipalName "user2_email_address").Id

Set-AzureRmKeyVaultAccessPolicy -VaultName "keyvaultname" -ObjectId $userPrincipalId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge


## <a name="disposeartefactstimedout"></a>DisposeArtefactsTimedOut

**오류 ID:** 181008

**오류 메시지:** VM: VMName. 오류: &'['Gateway.Service.StateMachine.SnapshotReplication.SnapshotReplicationEngine+WaitingForArtefactsDisposalPreCycle'('WaitingForArtefactsDisposalPreCycle')]' 상태에서 시간 제한 이벤트 'DisposeArtefactsTimeout'이 발생했습니다.

**가능한 원인:**

Azure에 데이터를 복제하려는 구성 요소가 다운되었거나 응답하지 않습니다. 가능한 원인은 다음과 같습니다.

- Azure Migrate 어플라이언스에서 실행되는 게이트웨이 서비스가 다운되었습니다.
- 게이트웨이 서비스에서 Service Bus/이벤트 허브/어플라이언스 스토리지 계정에 연결하는 데 문제가 있습니다.

다음과 같이 **DisposeArtefactsTimedOut의 정확한 원인과 해결 방법을 파악** 합니다.

1. Azure Migrate 어플라이언스가 실행 중인지 확인합니다.
2. 다음과 같이 게이트웨이 서비스가 어플라이언스에서 실행 중인지 확인합니다.
   1.  원격 데스크톱을 사용하여 Azure Migrate 어플라이언스에 로그인하고 다음을 수행합니다.

   2.  Microsoft 서비스 MMC 스냅인을 열고(실행 > services.msc) "Microsoft Azure Gateway Service"가 실행 중인지 확인합니다. 서비스가 중지되었거나 실행 중이 아니면 서비스를 시작합니다. 또는 명령 프롬프트나 PowerShell을 열고 "Net Start asrgtype" 명령을 실행합니다.

3. 다음과 같이 Azure Migrate 어플라이언스와 어플라이언스 스토리지 계정 간의 연결 문제를 확인합니다. 

    Azure Migrate 어플라이언스에서 azcopy를 다운로드한 후 다음 명령을 실행합니다.
    
    _azcopy bench https://[account].blob.core.windows.net/[container]?SAS_
    
    **성능 벤치마크 테스트를 실행하는 단계:**
    
      1. azcopy를 [다운로드](../storage/common/storage-use-azcopy-v10.md)합니다.
        
      2. 리소스 그룹에서 어플라이언스 스토리지 계정을 찾습니다. 스토리지 계정의 이름은 migrategwsa\*\*\*\*\*\*\*\*\*\* 형식입니다. 이 이름은 위의 명령에서 매개 변수 [계정]의 값입니다.
        
      3. Azure Portal에서 해당 스토리지 계정을 검색합니다. 검색에 사용하는 구독이 스토리지 계정이 만들어진 구독과 동일한 구독(대상 구독)인지 확인합니다. Blob Service 섹션의 컨테이너로 이동합니다. +컨테이너를 클릭하고 컨테이너를 만듭니다. 공용 액세스 수준을 기본값으로 둡니다.
        
      4. 설정에서 공유 액세스 서명으로 이동합니다. "허용되는 리소스 종류"에서 컨테이너를 선택합니다. SAS 및 연결 문자열 생성을 클릭합니다. SAS 값을 복사합니다.
        
      5. 명령 프롬프트에서 계정, 컨테이너, SAS를 각각 2, 3, 4단계에서 얻은 값으로 바꿔 위의 명령을 실행합니다.
        
      또는 어플라이언스에 Azure Storage Explore를 [다운로드](https://go.microsoft.com/fwlink/?linkid=2138967)하고 스토리지 계정에 ~64MB Blob 10개를 업로드해 봅니다. 아무 문제가 없으면 업로드가 성공적으로 수행됩니다.
        
    **해결 방법:** 이 테스트가 실패하면 네트워킹 문제가 있는 것입니다. 이 경우 로컬 네트워킹 팀에 문의하여 연결 문제를 확인합니다. 일반적으로 방화벽 설정 때문에 오류가 발생할 수 있습니다.
    
4.  다음과 같이 Azure Migrate 어플라이언스와 Service Bus 간의 연결 문제를 확인합니다.

    이 테스트는 Azure Migrate 어플라이언스가 Azure Migrate Cloud Service 백 엔드와 통신할 수 있는지 확인합니다. 어플라이언스는 Service Bus 및 이벤트 허브 메시지 큐를 통해 서비스 백 엔드와 통신합니다. 어플라이언스에서 Service Bus로 연결의 유효성을 검사하려면 Service Bus Explorer를 [다운로드](https://go.microsoft.com/fwlink/?linkid=2139104)하고, 어플라이언스 Service Bus에 연결하여 메시지 보내기/메시지 받기를 수행합니다. 아무 문제가 없으면 이 작업이 성공적으로 수행됩니다.

    **테스트를 실행하는 단계:**

    1. Service Bus에서 Migrate 프로젝트에 생성된 연결 문자열을 복사합니다.
    2. Service Bus Explorer를 엽니다.
    3. 파일 > 연결로 이동합니다.
    4. 연결 문자열을 붙여넣고 연결을 클릭합니다.
    5. 그러면 Service Bus 네임스페이스가 열립니다.
    6. 토픽에서 Snapshot Manager를 선택합니다. Snapshot Manager를 마우스 오른쪽 단추로 클릭하고 "메시지 받기" > "피킹"을 선택한 다음, 확인을 클릭합니다.
    7. 연결이 성공하면 콘솔 출력에 "[x]개의 메시지가 수신"되었다고 표시됩니다. 연결이 실패하면 연결이 실패했다는 메시지가 표시됩니다.
    
    **해결 방법:** 이 테스트가 실패하면 네트워킹 문제가 있는 것입니다. 이 경우 로컬 네트워킹 팀에 문의하여 연결 문제를 확인합니다. 일반적으로 방화벽 설정 때문에 오류가 발생할 수 있습니다.

5. 다음과 같이 Azure Migrate 어플라이언스와 Azure Key Vault 간의 연결 문제를 확인합니다.

    이 테스트는 Azure Migrate 어플라이언스와 Azure Key Vault 간의 연결 문제를 확인합니다. Key Vault는 복제에 사용되는 스토리지 계정 액세스를 관리하는 데 사용됩니다.
    
    **연결을 확인하는 단계** 는 다음과 같습니다.
    
    1. Azure Migrate 프로젝트에 해당하는 리소스 그룹의 리소스 목록에서 Key Vault URI를 가져옵니다.
    
    1. Azure Migrate 어플라이언스에서 PowerShell을 열고 다음 명령을 실행합니다.
    
    _test-netconnection Key Vault URI -P 443_
    
    이 명령은 TCP 연결을 시도하고 출력을 반환합니다.
    
     - 출력에서 "_TcpTestSucceeded_" 필드를 확인합니다. 값이 "_True_"이면 Azure Migrate 어플라이언스와 Azure Key Vault 간에 연결 문제가 없는 것입니다. 값이 "False"이면 연결 문제가 있는 것입니다.
    
    **해결 방법:** 이 테스트가 실패하면 Azure Migrate 어플라이언스와 Azure Key Vault 간에 연결 문제가 있는 것입니다. 이 경우 로컬 네트워킹 팀에 문의하여 연결 문제를 확인합니다. 일반적으로 방화벽 설정 때문에 오류가 발생할 수 있습니다.
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

**오류 ID:** 1011

**오류 메시지:** 디스크 DiskPath, 가상 머신 VMName의 DiskId 및 VMId에 대한 데이터 업로드가 예상 시간 내에 완료되지 않았습니다.

이 오류는 일반적으로 복제를 수행하는 Azure Migrate 어플라이언스가 Azure Cloud Services에 연결할 수 없거나 복제가 느리게 진행되어 복제 주기가 시간 초과됨을 나타냅니다.

가능한 원인은 다음과 같습니다.

- Azure Migrate 어플라이언스가 다운되었습니다.
- 어플라이언스의 복제 게이트웨이 서비스가 실행되고 있지 않습니다.
- 복제 게이트웨이 서비스에서 복제에 사용되는 Azure 서비스 구성 요소(Service Bus/이벤트 허브/Azure 캐시 스토리지 계정/Azure Key Vault) 중 하나에 연결하는 데 문제가 있습니다.
- 디스크를 읽으려고 할 때 게이트웨이 서비스가 vCenter 수준에서 제한됩니다.

다음과 같이 **근본 원인을 파악하고 문제를 해결** 합니다.

1. Azure Migrate 어플라이언스가 실행 중인지 확인합니다.
2. 다음과 같이 게이트웨이 서비스가 어플라이언스에서 실행 중인지 확인합니다.
   1.  원격 데스크톱을 사용하여 Azure Migrate 어플라이언스에 로그인하고 다음을 수행합니다.

   2.  Microsoft 서비스 MMC 스냅인을 열고(실행 > services.msc) "Microsoft Azure Gateway Service"가 실행 중인지 확인합니다. 서비스가 중지되었거나 실행 중이 아니면 서비스를 시작합니다. 또는 명령 프롬프트나 PowerShell을 열고 "Net Start asrgtype" 명령을 실행합니다.


3. **다음과 같이 Azure Migrate 어플라이언스와 캐시 스토리지 계정 간의 연결 문제를 확인** 합니다. 

    Azure Migrate 어플라이언스에서 azcopy를 다운로드한 후 다음 명령을 실행합니다.
    
    _azcopy bench https://[account].blob.core.windows.net/[container]?SAS_
    
    **성능 벤치마크 테스트를 실행하는 단계:**
    
      1. azcopy를 [다운로드](../storage/common/storage-use-azcopy-v10.md)합니다.
        
      2. 리소스 그룹에서 어플라이언스 스토리지 계정을 찾습니다. 스토리지 계정의 이름은 migratelsa\*\*\*\*\*\*\*\*\*\* 형식입니다. 이 이름은 위의 명령에서 매개 변수 [계정]의 값입니다.
        
      3. Azure Portal에서 해당 스토리지 계정을 검색합니다. 검색에 사용하는 구독이 스토리지 계정이 만들어진 구독과 동일한 구독(대상 구독)인지 확인합니다. Blob Service 섹션의 컨테이너로 이동합니다. +컨테이너를 클릭하고 컨테이너를 만듭니다. 공용 액세스 수준을 기본값으로 둡니다.
        
      4. 설정에서 공유 액세스 서명으로 이동합니다. "허용되는 리소스 종류"에서 컨테이너를 선택합니다. SAS 및 연결 문자열 생성을 클릭합니다. SAS 값을 복사합니다.
        
      5. 명령 프롬프트에서 계정, 컨테이너, SAS를 각각 2, 3, 4단계에서 얻은 값으로 바꿔 위의 명령을 실행합니다.
        
      또는 어플라이언스에 Azure Storage Explore를 [다운로드](https://go.microsoft.com/fwlink/?linkid=2138967)하고 스토리지 계정에 ~64MB Blob 10개를 업로드해 봅니다. 아무 문제가 없으면 업로드가 성공적으로 수행됩니다.
        
    **해결 방법:** 이 테스트가 실패하면 네트워킹 문제가 있는 것입니다. 이 경우 로컬 네트워킹 팀에 문의하여 연결 문제를 확인합니다. 일반적으로 방화벽 설정 때문에 오류가 발생할 수 있습니다.
                
4.  다음과 같이 **Azure Migrate 어플라이언스와 Azure Service Bus 간의 연결 문제를 확인** 합니다.

    이 테스트는 Azure Migrate 어플라이언스가 Azure Migrate Cloud Service 백 엔드와 통신할 수 있는지 확인합니다. 어플라이언스는 Service Bus 및 이벤트 허브 메시지 큐를 통해 서비스 백 엔드와 통신합니다. 어플라이언스에서 Service Bus로 연결의 유효성을 검사하려면 Service Bus Explorer를 [다운로드](https://go.microsoft.com/fwlink/?linkid=2139104)하고, 어플라이언스 Service Bus에 연결하여 메시지 보내기/메시지 받기를 수행합니다. 아무 문제가 없으면 이 작업이 성공적으로 수행됩니다.

    **테스트를 실행하는 단계:**
    
    1. Service Bus에서 Azure Migrate 프로젝트에 해당하는 리소스 그룹에 생성된 연결 문자열을 복사합니다.
    
    1. Service Bus Explorer를 엽니다.
    
    1. 파일 > 연결로 이동합니다.
    
    1. 1단계에서 복사한 연결 문자열을 붙여넣고 연결을 클릭합니다.
    
    1. 그러면 Service Bus 네임스페이스가 열립니다.
    
    1. 네임스페이스의 토픽에서 Snapshot Manager를 선택합니다. Snapshot Manager를 마우스 오른쪽 단추로 클릭하고 "메시지 받기" > "피킹"을 선택한 다음, 확인을 클릭합니다.
    
    연결이 성공하면 콘솔 출력에 "[x]개의 메시지가 수신"되었다고 표시됩니다. 연결이 실패하면 연결이 실패했다는 메시지가 표시됩니다.
    
    **해결 방법:** 이 테스트가 실패하면 Azure Migrate 어플라이언스와 Service Bus 간에 연결 문제가 있는 것입니다. 이 경우 로컬 네트워킹 팀에 문의하여 연결 문제를 확인합니다. 일반적으로 방화벽 설정 때문에 오류가 발생할 수 있습니다.
    
 5. 다음과 같이 **Azure Migrate 어플라이언스와 Azure Key Vault 간의 연결 문제를 확인** 합니다.

    이 테스트는 Azure Migrate 어플라이언스와 Azure Key Vault 간의 연결 문제를 확인합니다. Key Vault는 복제에 사용되는 스토리지 계정 액세스를 관리하는 데 사용됩니다.
    
    **연결을 확인하는 단계** 는 다음과 같습니다.
    
    1. Azure Migrate 프로젝트에 해당하는 리소스 그룹의 리소스 목록에서 Key Vault URI를 가져옵니다.
    
    1. Azure Migrate 어플라이언스에서 PowerShell을 열고 다음 명령을 실행합니다.
    
    _test-netconnection Key Vault URI -P 443_
    
    이 명령은 TCP 연결을 시도하고 출력을 반환합니다.
    
    1. 출력에서 "_TcpTestSucceeded_" 필드를 확인합니다. 값이 "_True_"이면 Azure Migrate 어플라이언스와 Azure Key Vault 간에 연결 문제가 없는 것입니다. 값이 "False"이면 연결 문제가 있는 것입니다.
    
    **해결 방법:** 이 테스트가 실패하면 Azure Migrate 어플라이언스와 Azure Key Vault 간에 연결 문제가 있는 것입니다. 이 경우 로컬 네트워킹 팀에 문의하여 연결 문제를 확인합니다. 일반적으로 방화벽 설정 때문에 오류가 발생할 수 있습니다.
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>변경된 블록을 가져오는 동안 오류 발생

오류 메시지: '변경 블록을 가져오는 동안 오류가 발생했습니다.'

에이전트 없는 복제 방법은 VMware의 CBT(변경된 블록 추적) 기술을 사용하여 데이터를 Azure에 복제합니다. CBT를 사용하면 Server Migration 도구에서 마지막 복제 주기 이후에 변경된 블록만 추적하여 복제할 수 있습니다. 복제 가상 머신에 대한 변경된 블록 추적이 다시 설정되거나 변경된 블록 추적 파일이 손상된 경우 이 오류가 발생합니다.

이 오류는 다음과 같은 두 가지 방법으로 해결할 수 있습니다.

- VM 복제를 트리거할 때 "예"를 선택하여 "자동으로 복제 복구"를 선택한 경우 이 도구가 자동으로 복구를 시도합니다. VM을 마우스 오른쪽 단추로 클릭하고 "복제 복구"를 선택합니다.
- "자동으로 복제 복구"를 선택하지 않았거나 위의 단계가 작동하지 않는 경우 가상 머신 복제를 중지하고 가상 머신에서 [변경된 블록 추적을 다시 설정](https://go.microsoft.com/fwlink/?linkid=2139203)한 다음, 복제를 다시 구성합니다.

VMware vSphere 5.5에서 가상 머신의 CBT를 다시 설정해야 하는 것으로 알려진 문제는 [VMware KB 1020128:](https://kb.vmware.com/s/article/1020128) vSphere 5.x에서 스토리지 vMotion 작업 후 변경된 블록 추적이 다시 설정됨 문서에 설명되어 있습니다. VMware vSphere 5.5를 사용하는 경우 이 KB에 설명된 업데이트를 적용합니다.

또는 가상 머신에서 VMware PowerCLI를 사용하여 VMware 변경된 블록 추적을 다시 설정할 수 있습니다.

## <a name="an-internal-error-occurred"></a>내부 오류 발생

VMware 환경/API의 문제로 인해 오류가 발생하는 경우가 가끔 있습니다. 현재까지 확인된 VMware 환경 관련 오류는 다음과 같습니다. 이러한 오류의 형식은 고정되어 있습니다.

_오류 메시지: 내부 오류가 발생 했습니다. [오류 메시지]_

예: 오류 메시지: 내부 오류가 발생했습니다. [잘못된 스냅샷 구성이 발견되었습니다.]

다음 섹션에는 자주 발생하는 몇 가지 VMware 오류와 이러한 오류를 완화하는 방법이 나열되어 있습니다.

### <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>오류 메시지: 내부 오류가 발생했습니다. [서버에서 연결을 거부했습니다.]

이 문제는 알려진 VMware 문제이며 VDDK 6.7에서 발생합니다. Azure Migrate 어플라이언스에서 실행되는 게이트웨이 서비스를 중지하고, [VMware KB에서 업데이트를 다운로드](https://go.microsoft.com/fwlink/?linkid=2138889)하고, 게이트웨이 서비스를 다시 시작해야 합니다.

게이트웨이 서비스를 중지하는 단계는 다음과 같습니다.

1. Windows + R 키를 누르고 services.msc를 엽니다. "Microsoft Azure Gateway Service"를 클릭하고 서비스를 중지합니다.
2. 또는 명령 프롬프트나 PowerShell을 열고 Net Stop asrgwy 명령을 실행합니다. 서비스가 더 이상 실행되지 않는 메시지가 나타날 때까지 기다려야 합니다.

게이트웨이 서비스를 시작하는 단계는 다음과 같습니다.

1. Windows + R 키를 누르고 services.msc를 엽니다. "Microsoft Azure Gateway Service"를 클릭하고 서비스를 시작합니다.
2. 또는 명령 프롬프트나 PowerShell을 열고 Net Start asrgtype 명령을 실행합니다.

### <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>오류 메시지: 내부 오류가 발생했습니다. ['잘못된 스냅샷 구성이 발견되었습니다.']

여러 디스크가 포함된 가상 머신이 있는 경우 가상 머신에서 디스크를 제거하면 이 오류가 발생할 수 있습니다. 이 문제를 해결하려면 [이 VMware 문서](https://go.microsoft.com/fwlink/?linkid=2138890)의 단계를 참조하세요.

### <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>오류 메시지: 내부 오류가 발생했습니다. [스냅샷 생성이 중지되었습니다.]

이 문제는 스냅샷 생성이 응답을 중지할 때 발생합니다. 이 문제가 발생하면 스냅샷 만들기 작업이 95% 또는 99%에서 중지됩니다. 이 문제를 해결하려면 이 [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138969)를 참조하세요.

### <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>오류 메시지: 내부 오류가 발생했습니다. [VM에서 디스크를 통합하지 못했습니다. _[이유]_ ]

복제 주기가 끝날 때 디스크를 통합하면 작업이 실패합니다. 문제를 해결하려면 적절한 _이유_ 를 선택하여 [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138970)의 지침을 따릅니다.

VMware 스냅샷 관련 작업(디스크 만들기, 삭제 또는 통합)이 실패할 때 다음 오류가 발생합니다. 다음 섹션의 지침에 따라 오류를 수정합니다.

### <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>오류 메시지: 내부 오류가 발생했습니다. [다른 작업이 이미 진행 중입니다.]

이 문제는 백그라운드에서 실행 중인 가상 머신 작업이 충돌하거나 vCenter Server 내의 작업이 시간 초과될 때 발생합니다. 다음 [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138891)에 제공된 해결 방법을 따르세요.

### <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>오류 메시지: 내부 오류가 발생했습니다. [현재 상태에서 작업이 허용되지 않습니다.]

이 문제는 vCenter Server 관리 에이전트가 작동을 멈출 때 발생합니다. 이 문제를 해결하려면 다음 [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138971)의 해결 방법을 참조하세요.

### <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>오류 메시지: 내부 오류가 발생했습니다. [스냅샷 디스크 크기가 잘못되었습니다.]

스냅샷으로 표시된 디스크 크기가 0이 되는 알려진 VMware 문제입니다. [VMware KB](https://kb.vmware.com/s/)에 제공된 해결 방법을 따르세요.

### <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>오류 메시지: 내부 오류가 발생했습니다. [메모리 할당이 실패했습니다. 메모리가 부족합니다.]

이 문제는 NFC 호스트 버퍼의 메모리가 부족할 때 발생합니다. 이 문제를 해결하려면 사용 가능한 리소스가 있는 다른 호스트로 VM(컴퓨팅 vMotion)을 이동해야 합니다.

### <a name="error-message-an-internal-error-occurred-file-is-larger-than-maximum-file-size-supported-1012384"></a>오류 메시지: 내부 오류가 발생했습니다. [파일이 지원되는 최대 파일 크기(1012384)보다 큽니다.]
이는 스냅샷을 만들 때 파일 크기가 지원되는 최대 파일 크기보다 클 때 발생합니다. [VMware KB](https://kb.vmware.com/s/article/1012384)에 제공된 해결 방법을 따르세요.

### <a name="error-message-an-internal-error-occurred-cannot-connect-to-the-host-1004109"></a>오류 메시지: 내부 오류가 발생했습니다. [호스트에 연결할 수 없음(1004109)]
이 문제는 ESXi 호스트에서 네트워크에 연결할 수 없는 경우에 발생합니다. [VMware KB](https://kb.vmware.com/s/article/1004109)에 제공된 해결 방법을 따르세요.

### <a name="error-message-an-error-occurred-while-saving-the-snapshot-invalid-change-tracker-error-code"></a>오류 메시지: 스냅샷을 저장하는 동안 오류가 발생했습니다. 변경 추적기 오류 코드가 잘못되었습니다.
이 오류는 스냅샷이 저장되는 기본 데이터 저장소에 문제가 있는 경우에 발생합니다. [VMware KB](https://kb.vmware.com/s/article/2042742)에 제공된 해결 방법을 따르세요.

### <a name="error-message-an-error-occurred-while-taking-a-snapshot-unable-to-open-the-snapshot-file"></a>오류 메시지: 스냅샷을 만드는 동안 오류가 발생했습니다. 스냅샷 파일을 열 수 없습니다.
이 오류는 생성된 스냅샷 파일의 크기가 VM이 있는 데이터 저장소의 사용 가능한 공간보다 클 때 발생합니다. 이 [문서](https://go.microsoft.com/fwlink/?linkid=2166464)에 제공된 해결 방법을 따르세요.

## <a name="replication-cycle-failed"></a>복제 주기 실패

**오류 ID:** 181008

**오류 메시지:** VM: 'VMName'. 오류: 스냅샷 복제에 사용할 스냅샷 Id가 'SnapshotID'인 디스크 스냅샷을 찾을 수 없습니다.

**가능한 원인:**

가능한 원인은 다음과 같습니다.
1. 스토리지 VMotion으로 인해 포함된 디스크 중 하나 이상의 경로가 변경되었습니다.
2. 포함된 디스크 중 하나 이상이 더 이상 VM에 연결되어 있지 않습니다.
      
**권장 사항:**

권장 사항은 다음과 같습니다.
1. 스토리지 vMotion를 사용하여 포함된 디스크를 원래 경로로 복원한 다음, 스토리지 vMotion를 사용하지 않도록 설정합니다.
2. 스토리지 VMotion를 사용하지 않도록 설정합니다. 사용하도록 설정한 경우 가상 머신에서 복제를 중지하고 가상 머신을 다시 복제합니다. 문제가 지속되면 고객 지원 팀에 문의하세요.

## <a name="next-steps"></a>다음 단계

VM 복제를 계속하고 [마이그레이션 테스트](./tutorial-migrate-vmware.md#run-a-test-migration)를 수행합니다.
