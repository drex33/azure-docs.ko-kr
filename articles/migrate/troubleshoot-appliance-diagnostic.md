---
title: Azure Migrate 어플라이언스 진단 문제 해결
description: Azure Migrate 어플라이언스에서 발생할 수 있는 문제를 진단하고 해결하는 데 도움을 받으세요.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 08/11/2021
ms.openlocfilehash: 03be4d6a4b5afb15b5820c1c8b334e3fcb80bbbb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122538022"
---
# <a name="diagnose-and-solve-issues-with-azure-migrate-appliance"></a>Azure Migrate 어플라이언스 문제 진단 및 해결

Azure Migrate 어플라이언스의 **진단 및 해결** 기능은 사용자가 검색 시작을 차단할 수 있는 어플라이언스 구성 문제 또는 어플라이언스에서 검색, 평가 및/또는 복제(*VMware 어플라이언스의 경우*)와 같은 진행 중인 마이그레이션 작업과 관련된 문제를 식별하고 자체 평가하는 데 도움이 됩니다. 

어플라이언스 구성 관리자에서 언제든지 **진단 및 해결** 을 실행하여 진단 보고서를 생성할 수 있습니다. 보고서는 수행된 검사, 상태, 식별된 문제 및 문제 해결을 위한 권장 단계에 대한 정보를 제공합니다. 

> [!IMPORTANT]
> **진단 및 해결** 기능은 현재 Azure Migrate 어플라이언스에서 미리 보기로 제공됩니다.
> 해당 미리 보기는 고객 지원에 포함되며 프로덕션 워크로드에 사용할 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


## <a name="diagnostic-checks"></a>진단 검사

*진단 및 해결* 은 사전 검증을 실행하여 필수 구성 파일이 누락되거나 어플라이언스의 바이러스 백신 소프트웨어에 의해 차단되지 않았는지 확인한 후 다음 검사를 수행합니다. 

**범주** | **진단 검사** |**설명**
--- | --- | --- |
**필수 구성 요소 확인** | 연결 검사 | 어플라이언스가 직접 또는 프록시를 통해 Azure에 연결되어 있는지 확인합니다.
|| 시간 동기화 확인 | 어플라이언스 서버 시간이 네트워크 시간과 동기화되어 있는지 확인합니다.
|| 자동 업데이트 확인 | 자동 업데이트가 사용하도록 설정되어 있고 어플라이언스에서 실행 중인 모든 에이전트가 최신 상태인지 확인합니다.
||VDDK 확인 | 필요한 VDDK 파일이 어플라이언스 서버의 필요한 위치에 다운로드 및 복사되었는지 확인합니다.
**서비스 상태 검사** |작동 상태 |어플라이언스의 에이전트가 실행 중인 상태인지 확인합니다. <br>*그렇지 않은 경우 어플라이언스는 에이전트를 다시 시작하여 자동으로 해결합니다.* 
||서비스 엔드포인트 연결 |에이전트가 직접 또는 프록시를 통해 Azure의 해당 서비스와 통신할 수 있는지 확인합니다.
**Azure 관련 검사** |AAD 앱 가용성* | 어플라이언스 등록 중에 만들어진 AAD 앱을 사용할 수 있고 어플라이언스에서 액세스할 수 있는지 확인합니다.
||Migrate 프로젝트 가용성* | 어플라이언스가 등록된 Migrate 프로젝트가 여전히 존재하고 어플라이언스에서 액세스할 수 있는지 확인합니다.
||필수 리소스 가용성*| 어플라이언스 등록 중에 만들어진 마이그레이션 리소스가 여전히 존재하고 어플라이언스에서 액세스할 수 있는지 확인합니다.
**어플라이언스별 검사** | Key Vault 인증서 가용성* | 어플라이언스 등록 중에 Key Vault에서 다운로드한 인증서를 어플라이언스 서버에서 계속 사용할 수 있는지 확인합니다. <br> *그렇지 않은 경우 Key Vault를 사용할 수 있고 액세스할 수 있다면 어플라이언스는 인증서를 다시 다운로드하여 자동 해결합니다*.
|| 자격 증명 저장소 가용성 | 어플라이언스 서버의 자격 증명 저장소 리소스가 이동/삭제/편집되지 않았는지 확인합니다.
|| 복제 어플라이언스/ASR 구성 요소 | ASR/복제 어플라이언스 구성 요소를 설치하는 데에도 동일한 서버가 사용되었는지 확인합니다. *현재 동일한 서버에 Azure Migrate와 복제 어플라이언스(에이전트 기반 마이그레이션용)를 모두 설치하는 것은 지원되지 않습니다.*
|| OS 라이선스 가용성 | OVA/VHD에서 만들어진 어플라이언스 서버의 평가 라이선스가 여전히 유효한지 확인합니다. *Windows Server 2016 평가판 라이선스는 180일 동안 유효합니다.*
|| CPU 및 메모리 사용률 | 어플라이언스 서버에서 Migrate 에이전트가 사용하는 CPU 및 메모리를 확인합니다.  

**어플라이언스가 이미 등록된 경우에만 확인하고 보고합니다. 이러한 검사는 어플라이언스에 로그인한 현재 Azure 사용자의 컨텍스트에서 실행됩니다*.

## <a name="running-diagnostic-checks"></a>진단 검사 실행

구성하는 동안 어플라이언스에 문제가 발생하거나 포털에서 검색, 평가 및/또는 복제(*VMware 어플라이언스의 경우*)와 같이 진행 중인 마이그레이션 작업에 문제가 있는 경우 어플라이언스 구성 관리자로 이동하여 진단을 실행합니다.

> [!NOTE]
> 현재 **진단 및 해결** 은 Azure에 대한 어플라이언스 연결, 어플라이언스 서버 및/또는 Azure에서 필요한 리소스의 가용성과 관련된 검사를 수행할 수 있습니다. vCenter Server/ESXi 호스트/Hyper-V 호스트/VM/물리적 서버와 같은 원본 환경의 연결 또는 검색 문제는 현재 **진단 및 해결** 에서 다루지 않습니다.
 
1. 구성 관리자 상단의 리본에서 **진단 및 해결** 을 선택합니다.

    ![진단 및 해결 선택](./media/troubleshoot-appliance-diagnostic-solve/appliance-configuration-manager-diagnose-solve.png)
    
    **진단 및 해결** 을 선택하면 어플라이언스가 진단 검사 실행을 자동으로 시작합니다. 완료하려면 5분 정도 걸릴 수 있습니다.
    *이전에 검사를 실행한 적이 있다면 마지막 진단 보고서의 타임스탬프가 표시됩니다.*
     
    ![진단 보고서](./media/troubleshoot-appliance-diagnostic-solve/diagnostic-report.png)

1. 진단 검사가 완료되면 PDF 형식으로 저장할 수 있는 다른 탭에서 보고서를 보거나 보고서가 HTML 형식으로 자동 저장되는 어플라이언스 서버의 이 위치(**C:\Users\Public\Desktop\DiagnosticsReport**)로 이동할 수 있습니다.

    ![진단 보고서 보기](./media/troubleshoot-appliance-diagnostic-solve/view-diagnostic-report.png)

1. 보고서는 수행된 검사, 상태, 식별된 문제 및 문제 해결을 위한 권장 단계에 대한 정보를 제공합니다.

    ![진단 보고서 상태 보기](./media/troubleshoot-appliance-diagnostic-solve/view-status.png)

1. 보고서의 수정 단계에 따라 문제를 해결할 수 있습니다. 문제를 해결할 수 없는 경우 신속한 해결에 도움이 되도록 Microsoft 지원 사례를 생성하는 동안 진단 보고서를 첨부하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
**진단 및 해결** 에서 다루지 않는 문제가 발생하는 경우 [Azure Migrate 어플라이언스 문제 해결](./troubleshoot-appliance.md)로 이동하여 수정 단계를 찾을 수 있습니다.