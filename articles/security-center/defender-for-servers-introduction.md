---
title: 서버용 Azure Defender - 이점 및 특징
description: 서버용 Azure Defender의 이점 및 특징에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 09/05/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 1a7b5d1e7ed3c4c1de2c535fb126b67f3ffadecd
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711716"
---
# <a name="introduction-to-azure-defender-for-servers"></a>서버용 Azure Defender 소개

서버용 Azure Defender는 Windows 및 Linux 머신에 대한 위협 탐지 및 고급 방어를 추가합니다.

Windows의 경우 Azure Security Center가 Azure 서비스와 통합되어 Windows 기반 머신을 모니터링하고 보호합니다. Security Center는 이러한 모든 서비스의 경고 및 수정 제안을 사용하기 쉬운 형식으로 제공합니다.

Linux의 경우 Azure Defender가 가장 일반적인 Linux 감사 프레임워크 중 하나인 auditd를 사용하여 Linux 컴퓨터에서 감사 레코드를 수집합니다.


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>서버용 Azure Defender의 이점은?

서버용 Azure Defender와 함께 제공되는 위협 탐지 및 보호 기능에는 다음이 포함됩니다.

- **엔드포인트용 Microsoft Defender에 대한 통합 라이선스** - 서버용 Azure Defender에는 [엔드포인트용 Microsoft Defender](https://www.microsoft.com/microsoft-365/security/endpoint-defender)가 포함되어 있습니다. 또한 포괄적인 EDR(엔드포인트 검색 및 응답) 기능을 제공합니다. 자세한 내용은 [엔드포인트 보호](security-center-wdatp.md)를 참조하세요.

    엔드포인트용 Defender는 위협을 감지하면 경고를 트리거합니다. 이 경고는 Security Center에 표시됩니다. Security Center에서 엔드포인트용 Defender 콘솔로 피벗하고 자세히 조사하여 공격 범위를 확인할 수도 있습니다. 엔드포인트용 Microsoft Defender에 대해 자세히 알아봅니다.

    > [!IMPORTANT]
    > Security Center와 엔드포인트용 Microsoft Defender의 통합은 기본적으로 사용하도록 설정되어 있습니다. 따라서 Azure Defender를 사용하도록 설정하는 경우 서버용 Azure Defender에서 취약성, 설치된 소프트웨어 및 엔드포인트에 대한 경고와 관련된 엔드포인트용 Microsoft Defender 데이터에 액세스하도록 동의합니다.
    >
    > 현재 Linux 컴퓨터용 센서를 미리 보기로 제공하고 있습니다. [Security Center의 통합 EDR 솔루션으로 엔드포인트를 보호: 엔드포인트용 Microsoft Defender](security-center-wdatp.md)를 자세히 알아봅니다.

- **머신용 취약점 평가 도구** - 서버용 Azure Defender에는 머신에 대한 취약성 검색 및 관리 도구가 포함되어 있습니다. Security Center의 설정 페이지에서 머신에 배포할 도구를 선택하고 검색된 취약점을 보안 권장 사항에 표시할 수 있습니다.

    - **Microsoft 위협 및 취약성 관리** - 엔드포인트용 Microsoft Defender를 사용하여 추가 에이전트나 정기 검사 없이 실시간으로 취약성 및 잘못된 구성을 검색합니다. [위협 및 취약성 관리](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt)는 위협 환경, 조직에서의 탐지, 취약한 디바이스에 대한 중요한 정보 및 비즈니스 컨텍스트를 기반으로 취약성의 우선 순위를 지정합니다. [엔드포인트용 Microsoft Defender의 위협 및 취약성 관리를 통한 취약성 조사](deploy-vulnerability-assessment-tvm.md)에서 자세히 알아보기

    - **Qualys에서 제공하는 취약성 스캐너** - Qualys의 스캐너는 Azure 및 하이브리드 가상 머신에서 실시간으로 취약성을 식별하기 위한 최고의 도구 중 하나입니다. Qualys 라이선스 또는 Qualys 계정이 필요하지 않습니다. 모든 항목이 Security Center 내에서 원활하게 처리됩니다. [Azure 및 하이브리드 머신을 위한 Azure Defender의 통합 Qualys 스캐너](deploy-vulnerability-assessment-vm.md)에서 자세히 알아봅니다.

- **JIT(Just-In-Time) VM(가상 머신) 액세스** - 위협 행위자는 RDP 또는 SSH와 같은 개방형 관리 포트를 사용하여 액세스 가능한 머신을 적극적으로 헌팅합니다. 모든 가상 머신은 공격의 대상이 될 수 있습니다. VM이 손상되면 사용자 환경 내 추가 리소스를 공격하는 진입점으로 사용됩니다.

    서버용 Azure Defender를 사용하도록 설정하면 Just-In-Time VM 액세스를 사용하여 VM에 대한 인바운드 트래픽을 잠가서 공격에 대한 노출을 줄이는 동시에 VM에 쉽게 액세스하여 필요할 때 연결할 수 있습니다. 자세한 내용은 [JIT VM 이해](just-in-time-explained.md)를 참조하세요.

- **FIM(파일 무결성 모니터링)** - 변경 모니터링이라고도 하는 FIM(파일 무결성 모니터링)은 운영 체제, 애플리케이션 소프트웨어 및 다른 요소의 파일과 레지스트리에서 공격으로 간주될 수 있는 변경을 검사합니다. 비교 메서드는 파일의 현재 상태가 파일의 마지막 검사와 다른지 확인하는 데 사용됩니다. 이 비교를 통해 파일에 유효하거나 의심스러운 수정 사항이 있는지 확인할 수 있습니다.

    서버용 Azure Defender를 사용하도록 설정하면 FIM을 사용하여 Windows 파일, Windows 레지스트리 및 Linux 파일의 무결성에 대한 유효성을 검사할 수 있습니다. 자세한 내용은 [Azure Security Center에서 파일 무결성 모니터링](security-center-file-integrity-monitoring.md)을 참조하세요.

- **AAC(적응형 애플리케이션 제어)** - 적응형 애플리케이션 제어는 머신에 대해 알려진 안전한 애플리케이션의 허용 목록을 정의하기 위한 인텔리전트 자동 솔루션입니다.

    적응형 애플리케이션 제어를 사용하도록 설정하고 구성하면 안전한 것으로 정의한 애플리케이션이 아닌 다른 애플리케이션이 실행될 경우 보안 경고가 표시됩니다. 자세한 내용은 [적응형 애플리케이션 제어를 사용하여 머신의 공격 표면 줄이기](security-center-adaptive-application.md)를 참조하세요.

- **ANH(적응형 네트워크 강화)** - NSG(네트워크 보안 그룹)를 적용하여 리소스에서 들어오고 나가는 트래픽을 필터링하면 네트워크 보안 상태가 개선됩니다. 그러나 NSG를 통과하는 실제 트래픽이 정의된 NSG 규칙의 하위 집합인 경우도 있을 수 있습니다. 이러한 경우 실제 트래픽 패턴에 따라 NSG 규칙을 강화하여 보안 상태를 더욱 개선할 수 있습니다.

    적응형 네트워크 강화는 NSG 규칙을 강화하기 위한 권장 사항을 제공합니다. 실제 트래픽, 알려진 신뢰할 수 있는 구성, 위협 인텔리전스 및 기타 손상 지표를 고려하는 기계 학습 알고리즘을 사용한 후 특정 IP/포트 튜플의 트래픽만 허용하는 권장 사항을 제공합니다. 자세한 내용은 [적응형 네트워크 강화를 통해 네트워크 보안 상태 개선](security-center-adaptive-network-hardening.md)을 참조하세요.


- **Docker 호스트 강화** - Azure Security Center는 IaaS Linux VM 또는 Docker 컨테이너를 실행하는 다른 Linux 머신에서 호스팅되는 관리되지 않는 컨테이너를 식별합니다. Security Center는 이러한 컨테이너의 구성을 지속적으로 평가합니다. 그런 다음, 이러한 항목을 CIS(Center for Internet Security) Docker 벤치마크와 비교합니다. Security Center는 CIS Docker 벤치마크의 전체 규칙 세트를 포함하며 컨테이너가 규칙을 위반할 경우 경고를 표시합니다. 자세한 내용은 [Docker 호스트 강화](harden-docker-hosts.md)를 참조하세요.

- **파일리스 공격 검색** - 파일리스 공격은 디스크 기반 검사 기술의 검색을 방지하기 위해 악의적인 페이로드를 메모리에 삽입합니다. 그러면 공격자 페이로드가 손상된 프로세스의 메모리 내에서 유지되며 광범위한 악의적인 활동을 수행합니다.

  파일리스 공격 탐지를 통해 자동화된 메모리 포렌식 기술은 파일리스 공격 도구 키트, 기술 및 동작을 식별합니다. 이 솔루션은 런타임에 머신을 정기적으로 검사하고, 프로세스의 메모리에서 직접 인사이트를 추출합니다. 특정 인사이트에는 다음에 대한 식별이 포함됩니다. 

  - 잘 알려진 도구 키트 및 암호화 마이닝 소프트웨어 

  - Shellcode. 일반적으로 소프트웨어 취약성을 악용하는 페이로드로 사용되는 작은 코드 조각입니다.

  - 프로세스 메모리에 삽입된 악성 실행 파일

  파일리스 공격 검색은 네트워크 활동 등의 프로세스 메타데이터를 사용한 설명을 포함하는 상세 보안 경고를 생성합니다. 이러한 세부 정보는 경고 심사, 상관 관계 및 다운스트림 응답 시간이 가속화됩니다. 이 방법은 이벤트 기반 EDR 솔루션을 보완하고 검색 범위를 더 확장합니다.

  파일리스 공격 탐지 경고에 대한 자세한 내용은 [경고 참조 표](alerts-reference.md#alerts-windows)를 참조하세요.

- **Linux auditd 경고 및 Log Analytics 에이전트 통합(Linux만 해당)** - auditd 시스템은 시스템 호출을 모니터링해야 하는 커널 수준 하위 시스템으로 구성됩니다. 지정된 규칙 세트를 기준으로 필터링하고 이에 대한 메시지를 소켓에 씁니다. Security Center는 Log Analytics 에이전트 내에서 auditd 패키지의 기능을 통합합니다. 이러한 통합을 통해 사전 요구 사항 없이 지원되는 모든 Linux 배포에서 auditd 이벤트를 수집할 수 있습니다.

    Linux용 Log Analytics 에이전트는 감사된 레코드를 수집하고 이를 이벤트로 강화 및 집계합니다. Security Center는 Linux 신호를 사용하여 클라우드 및 온-프레미스 Linux 컴퓨터에서 악의적인 동작을 탐지하는 새 분석을 지속적으로 추가합니다. Windows 기능과 비슷하게 이러한 분석은 의심스러운 프로세스, 의심스러운 로그인 시도, 커널 모듈 로드 및 기타 활동 전반에 걸쳐 있습니다. 이러한 활동은 컴퓨터가 공격을 받고 있거나 위반되었음을 나타낼 수 있습니다.  

    Linux 경고 목록은 [경고 참조 표](alerts-reference.md#alerts-linux)를 참조하세요.


## <a name="simulating-alerts"></a>경고 시뮬레이션

다음 플레이북 중 하나를 다운로드하여 경고를 시뮬레이션할 수 있습니다.

- Windows의 경우: [Azure Security Center 플레이북: 보안 경고](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- Linux의 경우: [Azure Security Center 플레이북: Linux 탐지](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf)를 다운로드하여 Linux 경고를 시뮬레이션할 수 있습니다.




## <a name="next-steps"></a>다음 단계

이 문서에서는 서버용 Azure Defender에 대해 알아보았습니다. 

> [!div class="nextstepaction"]
> [Azure Defender 사용](enable-azure-defender.md)

관련 자료는 다음 페이지를 참조하세요.

- Security Center에서 경고를 생성하든 Security Center에서 다른 보안 제품으로부터 경고를 받든 이를 내보낼 수 있습니다. 경고를 Azure Sentinel, 타사 SIEM 또는 기타 외부 도구로 내보내려면 [SIEM으로 경고 내보내기](continuous-export.md)의 지침을 따릅니다.