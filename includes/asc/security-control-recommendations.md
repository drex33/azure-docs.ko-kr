---
author: memildin
ms.service: security-center
ms.topic: include
ms.date: 09/05/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: 5ef21b7d66532adad450dadf0d6c0c57b73ae8c6
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129725887"
---
<table class="tg">
<thead>
  <tr>
    <th class="tg-cly1"><b>보안 점수</b></th>
    <th class="tg-cly1"><b>보안 컨트롤 및 설명</b></th>
    <th class="tg-cly1"><b>권장 사항</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-lboi"><strong>10</strong></td>
    <td class="tg-lboi"><strong>MFA 사용</strong> - Security Center는 MFA (multi-factor authentication)에 높은 값을 배치 합니다. 이러한 권장 사항을 사용하여 구독 사용자를 보호합니다. <br>MFA를 사용하고 권장 사항을 준수하는 세 가지 방법인 보안 기본값, 사용자별 할당, 조건부 액세스 정책이 있습니다. <a href="/azure/security-center/security-center-identity-access">구독에 대한 MFA 적용 관리</a>에서 이러한 옵션에 대해 자세히 알아봅니다.</td>
    <td class="tg-lboi" width=55%>- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.<br />- 구독에서 소유자 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.<br />- 구독에 대한 쓰기 권한이 있는 계정에서 MFA를 사용하도록 설정해야 합니다.<br />- 구독에 대한 쓰기 권한이 있는 계정에서 MFA를 사용하도록 설정해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>8</strong></td>
    <td class="tg-lboi"><strong>보안 관리 포트</strong> - 무차별 암호 대입 공격은 종종 관리 포트를 대상으로 합니다. 이러한 권장 사항을 사용하여 <a href="/azure/security-center/just-in-time-explained">Just-In-Time VM 액세스</a> 및 <a href="/azure/virtual-network/network-security-groups-overview">네트워크 보안 그룹</a>과 같은 도구로 노출을 줄입니다.</td>
    <td class="tg-lboi" width=55%>- 네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 합니다.<br />- 가상 머신의 관리 포트는 Just-In-Time 네트워크 액세스 제어로 보호해야 합니다.<br />- 가상 머신에서 관리 포트를 닫아야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>6</strong></td>
    <td class="tg-lboi"><strong>시스템 업데이트 적용</strong> - 업데이트를 적용 하지 않으면 패치가 적용 되지 않은 취약성이 발생 하 고 공격에 취약 한 환경이 발생 합니다. 이러한 권장 사항을 사용하여 운영 효율성을 유지하고, 보안 취약성을 줄이고, 최종 사용자에게 보다 안정적인 환경을 제공합니다. 시스템 업데이트를 배포하려면 <a href="/azure/automation/update-management/overview">업데이트 관리 솔루션을 사용하여 머신에 대한 패치 및 업데이트를 관리</a>할 수 있습니다.</td>
    <td class="tg-lboi" width=55%>- Linux 기반 Azure Arc 머신에 Log Analytics 에이전트를 설치해야 합니다.<br />- 가상 머신에 Log Analytics 에이전트를 설치해야 합니다.<br />-가상 머신 확장 집합에 Log Analytics 에이전트를 설치해야 합니다.<br />- Windows 기반 Azure Arc 머신에 Log Analytics 에이전트를 설치해야 합니다.<br />- 가상 머신 확장 집합에 대한 시스템 업데이트를 설치해야 합니다.<br />- 시스템 업데이트를 머신에 설치해야 합니다.<br />- 머신에 시스템 업데이트를 설치해야 합니다(업데이트 센터에서 제공).</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>6</strong></td>
    <td class="tg-lboi"><strong>취약성</strong> - 해결 Security Center에는 위협 행위자가 활용할 수 있는 약점에 대해 컴퓨터, 데이터베이스 및 컨테이너 레지스트리를 확인 하는 여러 취약점 평가 스캐너가 포함 되어 있습니다. 이러한 권장 사항을 사용하여 이러한 스캐너를 활성화하고 해당 결과를 검토합니다.<br><a href="/azure/security-center/deploy-vulnerability-assessment-vm">머신</a>, <a href="/azure/security-center/defender-for-sql-on-machines-vulnerability-assessment">SQL 서버</a> 및 <a href="/azure/security-center/defender-for-container-registries-usage">컨테이너 레지스트리</a> 검사에 대해 자세히 알아봅니다.</td>
    <td class="tg-lboi" width=55%>- 가상 머신에서 취약성 평가 솔루션을 사용하도록 설정해야 합니다.<br />- Azure Kubernetes 서비스 클러스터에는 Kubernetes 용 Azure Policy 추가 기능이 설치 되어 있어야 합니다.<br />- 신뢰할 수 있는 레지스트리의 컨테이너 이미지만 배포해야 합니다.<br />- Azure Container Registry 이미지의 취약성을 수정해야 합니다(Qualys 제공).<br />- 가상 머신의 취약성을 수정해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>4</strong></td>
    <td class="tg-lboi">전송 중인 데이터 <strong>암호화</strong> - 이러한 권장 사항을 사용 하 여 구성 요소, 위치 또는 프로그램 간에 이동 하는 데이터를 보호 합니다. 이러한 데이터는 중간자(man-in-the-middle) 공격, 도청 및 세션 하이재킹에 취약합니다.</td>
    <td class="tg-lboi" width=55%>- API 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.<br />- MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.<br />- PostgreSQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.<br />- API 앱에서 FTPS를 요구해야 합니다.<br />- 함수 앱에서 FTPS를 요구해야 합니다.<br />- 웹앱에서 FTPS를 요구해야 합니다.<br />- 함수 앱은 HTTPS를 통해서만 액세스할 수 있어야 합니다.<br />- Redis Cache에 보안 연결만 사용하도록 설정해야 합니다.<br />- 스토리지 계정에 보안 전송을 사용하도록 설정해야 합니다.<br />- TLS를 최신 API 앱 버전으로 업데이트해야 합니다.<br />- TLS를 최신 함수 앱 버전으로 업데이트해야 합니다.<br />- TLS를 최신 웹앱 버전으로 업데이트해야 합니다.<br />- 웹 애플리케이션은 HTTPS를 통해서만 액세스할 수 있어야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>4</strong></td>
    <td class="tg-lboi"><strong>무단 네트워크 액세스 제한</strong> - Azure는 네트워크를 통한 액세스가 최고 수준의 보안 표준을 충족 하도록 설계 된 도구 모음을 제공 합니다.<br>이러한 권장 사항을 사용하여 <a href="/azure/security-center/security-center-adaptive-network-hardening">Security Center의 적응형 네트워크 강화</a> 설정을 관리하고, 모든 관련 PaaS 서비스에 대해 <a href="/azure/private-link/private-link-overview">Azure Private Link</a>를 구성했는지 확인하고, 가상 네트워크 등에서 <a href="/azure/firewall/overview">Azure Firewall</a>을 활성화합니다.</td>
    <td class="tg-lboi" width=55%>- 인터넷 연결 가상 머신에 적응형 네트워크 강화 권장 사항을 적용해야 합니다.<br />- 모든 네트워크는 가상 머신과 연결된 네트워크 보안 그룹에서 제한되어야 합니다.<br />- App Configuration은 프라이빗 링크를 사용해야 합니다.<br />- Azure Cache for Redis는 가상 네트워크 내에 있어야 합니다.<br />- Azure Event Grid 도메인은 프라이빗 링크를 사용해야 합니다.<br />- Azure Event Grid 토픽은 프라이빗 링크를 사용해야 합니다.<br />- Azure Kubernetes 서비스 클러스터에는 Kubernetes 용 Azure Policy 추가 기능이 설치 되어 있어야 합니다.<br />- Azure Machine Learning 작업 영역은 프라이빗 링크를 사용해야 합니다.<br />- Azure SignalR Service는 프라이빗 링크를 사용해야 합니다.<br />- Azure Spring Cloud는 네트워크 주입을 사용해야 합니다.<br />- 컨테이너 레지스트리는 무제한 네트워크 액세스를 허용하지 않아야 합니다.<br />- 컨테이너 레지스트리는 프라이빗 링크를 사용해야 합니다.<br />- 컨테이너는 허용되는 포트에서만 수신 대기해야 합니다.<br />- CORS에서 모든 리소스가 API 앱에 액세스하도록 허용하지 않아야 합니다.<br />- CORS에서 모든 리소스가 함수 앱에 액세스하도록 허용하지 않아야 합니다.<br />- CORS에서 모든 리소스가 웹 애플리케이션에 액세스하도록 허용하면 안 됩니다.<br />- Key Vault에서 방화벽을 사용하도록 설정해야 합니다.<br />- 네트워크 보안 그룹을 사용하여 인터넷 연결 가상 머신을 보호해야 합니다.<br />- 가상 머신에서 IP 전달을 사용하지 않도록 설정해야 합니다.<br />- 제한 된 액세스로 Kubernetes API 서버를 구성 해야 합니다.<br />- Key Vault의 프라이빗 엔드포인트를 구성해야 합니다.<br />- 프라이빗 엔드포인트를 MariaDB 서버에서 사용하도록 설정해야 합니다.<br />- 프라이빗 엔드포인트를 MySQL 서버에서 사용하도록 설정해야 합니다.<br />- 프라이빗 엔드포인트를 PostgreSQL 서버에서 사용하도록 설정해야 합니다.<br />- MariaDB 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.<br />- MySQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.<br />- PostgreSQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.<br />- 서비스는 허용되는 포트에서만 수신 대기해야 합니다.<br />- 스토리지 계정은 프라이빗 링크 연결을 사용해야 합니다.<br />- 스토리지 계정은 가상 네트워크 규칙을 사용하여 네트워크 액세스를 제한해야 합니다.<br />- 호스트 네트워킹 및 포트 사용을 제한해야 합니다.<br />- 가상 네트워크는 Azure Firewall로 보호해야 합니다.<br />- VM Image Builder 템플릿은 프라이빗 링크를 사용해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>4</strong></td>
    <td class="tg-lboi"><strong>미사용</strong> - 암호화 사용 이러한 권장 사항을 사용 하 여 저장 된 데이터의 보호와 관련 된 잘못 된 구성을 완화할 수 있습니다.</td>
    <td class="tg-lboi" width=55%>- Service Fabric 클러스터는 ClusterProtectionLevel 속성을 EncryptAndSign으로 설정해야 합니다.<br />- SQL 데이터베이스에 투명한 데이터 암호화를 사용하도록 설정해야 합니다.<br />- 가상 머신은 Compute 및 Storage 리소스 간에 임시 디스크, 캐시 및 데이터 흐름을 암호화해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>4</strong></td>
    <td class="tg-lboi"><strong>액세스 및 사용 권한 관리</strong> - 보안 프로그램의 핵심 부분은 사용자에 게 작업을 수행 하는 데 필요한 액세스 권한이 있는지 확인 하는 것입니다. 최소 권한 액세스 모델의 경우에는 그 이상입니다. 이러한 권장 사항을 사용하여 ID 및 액세스 요구 사항을 관리합니다.</td>
    <td class="tg-lboi" width=55%>- Linux 머신에 대한 인증에 SSH 키가 필요합니다.<br />- Azure Kubernetes 서비스 클러스터에는 Kubernetes 용 Azure Policy 추가 기능이 설치 되어 있어야 합니다.<br />- 권한 상승을 포함하는 컨테이너를 사용하지 않아야 합니다.<br />- 중요한 호스트 네임스페이스를 공유하는 컨테이너를 사용하지 않아야 합니다.<br />- 사용되지 않는 계정을 구독에서 제거해야 합니다.<br />- 사용되지 않는 계정을 구독에서 제거해야 합니다.<br />- 소유자 권한이 있는 사용되지 않는 계정을 구독에서 제거해야 합니다.<br />- 소유자 권한이 있는 사용되지 않는 계정을 구독에서 제거해야 합니다.<br />- 소유자 권한이 있는 외부 계정을 구독에서 제거해야 합니다.<br />- 소유자 권한이 있는 외부 계정을 구독에서 제거해야 합니다.<br />- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.<br />- 쓰기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.<br />- 함수 앱은 클라이언트 인증서(들어오는 클라이언트 인증서)를 사용하도록 설정해야 합니다.<br />- 게스트 구성 확장을 머신에 설치해야 합니다.<br />- 변경 불가능한(읽기 전용) 루트 파일 시스템을 컨테이너에 적용해야 합니다.<br />- 최소 권한 Linux 기능을 컨테이너에 적용해야 합니다.<br />- API 앱에서 관리 ID를 사용해야 합니다.<br />- 함수 앱에서 관리 ID를 사용해야 합니다.<br />- 웹앱에서 관리 ID를 사용해야 합니다.<br />- 권한 있는 컨테이너를 피해야 합니다.<br />- Kubernetes 서비스에서 역할 기반 액세스 제어를 사용해야 합니다.<br />- 컨테이너를 루트 사용자로 실행하지 않아야 합니다.<br />- Service Fabric 클러스터는 클라이언트 인증에 대해서만 Azure Active Directory를 사용해야 합니다.<br />- 관리 인증서 대신 서비스 주체를 사용하여 구독을 보호해야 합니다.<br />- 스토리지 계정 공용 액세스가 허용되지 않아야 합니다.<br />- 손상된 컨테이너에서 노드 액세스를 제한하려면 Pod HostPath 볼륨 탑재 사용을 알려진 목록으로 제한해야 합니다.<br />- 가상 머신의 게스트 구성 확장은 시스템이 할당한 관리 ID를 사용하여 배포해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>4</strong></td>
    <td class="tg-lboi"><strong>보안 구성</strong> - 수정 잘못 구성 된 IT 자산은 공격 당할 위험이 높습니다. 이러한 권장 사항을 사용하여 인프라 전체에서 식별된 잘못된 구성을 강화합니다.</td>
    <td class="tg-lboi" width=55%>- Azure Kubernetes 서비스 클러스터에는 Kubernetes 용 Azure Policy 추가 기능이 설치 되어 있어야 합니다.<br />- Linux 기반 Azure Arc 머신에 Log Analytics 에이전트를 설치해야 합니다.<br />- 가상 머신에 Log Analytics 에이전트를 설치해야 합니다.<br />-가상 머신 확장 집합에 Log Analytics 에이전트를 설치해야 합니다.<br />- Windows 기반 Azure Arc 머신에 Log Analytics 에이전트를 설치해야 합니다.<br />- 컨테이너 AppArmor 프로필의 재정의 또는 비활성화를 제한해야 합니다.<br />- Kubernetes 서비스에 Pod 보안 정책을 정의해야 합니다(더 이상 사용되지 않음).<br />- SQL 데이터베이스는 발견한 취약성을 해결해야 합니다.<br />- 머신의 SQL Server는 발견한 취약성을 해결해야 합니다.<br />- 컨테이너 보안 구성의 취약성을 수정해야 합니다.<br />- Linux 컴퓨터에서 보안 구성의 취약성을 재구성 해야 합니다 (게스트 구성에서 구동).<br />- 머신 보안 구성의 취약성을 수정해야 합니다.<br />- 가상 머신 확장 집합에서 보안 구성의 취약성을 수정해야 합니다.<br />-Windows 컴퓨터에서 보안 구성의 취약성을 재구성 해야 합니다 (게스트 구성에서 구동).<br />- SQL 관리형 인스턴스에서 취약성 평가를 사용하도록 설정해야 합니다.<br />- SQL 서버에서 취약성 평가를 사용하도록 설정해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>3</strong></td>
    <td class="tg-lboi"><strong>적응 응용 프로그램 제어 적용</strong> - 적응 응용 프로그램 제어는 컴퓨터에서 실행할 수 있는 응용 프로그램을 제어 하는 지능적이 고 자동화 된 종단 간 솔루션입니다. 또한 맬웨어로부터 머신을 보호하는 데 유용합니다.</td>
    <td class="tg-lboi" width=55%>- 머신에서 안전한 애플리케이션을 정의하기 위해 적응형 애플리케이션 제어를 사용하도록 설정해야 합니다.<br />- 적응형 애플리케이션 제어 정책의 허용 목록 규칙을 업데이트해야 합니다.<br />- Linux 기반 Azure Arc 머신에 Log Analytics 에이전트를 설치해야 합니다.<br />- 가상 머신에 Log Analytics 에이전트를 설치해야 합니다.<br />- Windows 기반 Azure Arc 머신에 Log Analytics 에이전트를 설치해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>2</strong></td>
    <td class="tg-lboi"><strong>DDoS 공격</strong> - 으로부터 응용 프로그램 보호 Azure의 고급 네트워킹 솔루션에는 <a href="/azure/ddos-protection/ddos-protection-overview"></a> Azure DDoS Protection </a> , <a href="/azure/web-application-firewall/overview"></a> Azure 웹 응용 프로그램 방화벽 </a> 및 <a href="/azure/governance/policy/concepts/policy-for-kubernetes"></a> Kubernetes 용 Azure Policy 추가 기능이 포함 </a> 됩니다. 이러한 권장 사항을 사용하여 애플리케이션이 이러한 도구 및 기타 도구로 보호되도록 합니다.</td>
    <td class="tg-lboi" width=55%>- Azure DDoS Protection 표준을 사용하도록 설정해야 합니다.<br />- Azure Kubernetes 서비스 클러스터에는 Kubernetes 용 Azure Policy 추가 기능이 설치 되어 있어야 합니다.<br />- 컨테이너 CPU 및 메모리 한도를 적용해야 합니다.<br />- Application Gateway에 WAF(웹 애플리케이션 방화벽)를 사용하도록 설정해야 합니다.<br />- WAF(Web Application Firewall)를 Azure Front Door Service 서비스에 사용하도록 설정해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>2</strong></td>
    <td class="tg-lboi"><strong>Endpoint Protection 사용</strong> - Security Center는 조직의 끝점에서 끝점에 대 한 Microsoft Defender, <a href="/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions-">이 목록</a>에 표시 된 주요 솔루션 등의 활성 위협 검색 및 응답 솔루션을 확인 합니다.<br>끝점 검색 및 응답 (EDR) 솔루션을 찾을 수 없는 경우 이러한 권장 사항을 사용 하 여 끝점에 대 한 Microsoft Defender ( <a href="/azure/security-center/defender-for-servers-introduction">서버에 대 한 Azure defender</a>의 일부로 포함)를 배포할 수 있습니다.<br>이 컨트롤의 기타 권장 사항은 Log Analytics 에이전트를 배포 하 고 <a href="/azure/security-center/security-center-file-integrity-monitoring">파일 무결성 모니터링</a>을 구성 하는 데 도움이 됩니다.</td>
    <td class="tg-lboi" width=55%>- V가상 머신 확장 집합에서 Endpoint Protection 상태 실패를 해결해야 합니다.<br />- 컴퓨터에서 Endpoint Protection 상태 문제를 해결해야 합니다.<br />- 컴퓨터에서 Endpoint Protection 상태 문제를 해결해야 합니다.<br />- 머신에 Endpoint Protection을 설치해야 합니다.<br />- 가상 머신 확장 집합에 Endpoint Protection 솔루션을 설치해야 합니다.<br />- 서버에 파일 무결성 모니터링을 사용하도록 설정해야 합니다.<br />- 가상 머신에 Endpoint Protection 솔루션을 설치해야 합니다.<br />- 머신에 Endpoint Protection 솔루션을 설치해야 합니다.<br />- Linux 기반 Azure Arc 머신에 Log Analytics 에이전트를 설치해야 합니다.<br />- 가상 머신에 Log Analytics 에이전트를 설치해야 합니다.<br />-가상 머신 확장 집합에 Log Analytics 에이전트를 설치해야 합니다.<br />- Windows 기반 Azure Arc 머신에 Log Analytics 에이전트를 설치해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>1</strong></td>
    <td class="tg-lboi"><strong>감사 및 로깅 사용</strong> - 자세한 로그는 인시던트 조사 및 기타 많은 문제 해결 작업의 중요 한 부분입니다. 이 컨트롤의 권장 사항은 언제 든 지 진단 로그를 사용 하도록 설정 하는 데 중점을 둡니다.</td>
    <td class="tg-lboi" width=55%>- SQL 서버에 대한 감사가 사용되도록 설정되어야 합니다.<br />- Azure Data Lake Store의 진단 로그를 사용하도록 설정해야 합니다.<br />- Azure Stream Analytics의 진단 로그를 사용하도록 설정해야 합니다.<br />- Batch 계정의 진단 로그를 사용하도록 설정해야 합니다.<br />- Data Lake Analytics의 진단 로그를 사용하도록 설정해야 합니다.<br />- Event Hub의 진단 로그를 사용하도록 설정해야 합니다.<br />- Key Vault의 진단 로그를 사용하도록 설정해야 합니다.<br />- Search 서비스에서 진단 로그를 사용하도록 설정해야 합니다.<br />- Service Bus의 진단 로그를 사용하도록 설정해야 합니다.<br />- Virtual Machine Scale Sets의 진단 로그를 사용하도록 설정해야 합니다.<br />- 논리 앱의 진단 로그를 사용하도록 설정해야 합니다.<br />- App Service에서 진단 로그를 사용하도록 설정해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>0</strong></td>
    <td class="tg-lboi"><strong>보안 모범 사례 구현</strong> - 이 컨트롤은 보안 점수에 영향을 주지 않습니다. 이러한 이유로 조직의 보안을 위해 충족 해야 하는 권장 사항의 모음 이지만 전체 점수를 평가 하는 방법에는 포함 되지 않아야 합니다.</td>
    <td class="tg-lboi" width=55%>- [필요한 경우 사용] Azure Cosmos DB 계정은 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 합니다.<br />- [필요한 경우 사용] Azure Machine Learning 작업 영역은 CMK(고객 관리형 키)를 사용하여 암호화해야 합니다.<br />- [필요한 경우 사용] Cognitive Services 계정은 CMK(고객 관리형 키)를 사용하여 데이터 암호화를 사용하도록 설정해야 합니다.<br />- [필요한 경우 사용] 컨테이너 레지스트리는 CMK(고객 관리형 키)를 사용하여 암호화해야 합니다.<br />- [필요한 경우 사용] MySQL 서버는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 합니다.<br />- [필요한 경우 사용] PostgreSQL 서버는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 합니다.<br />- [필요한 경우 사용] SQL 관리형 인스턴스는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 합니다.<br />- [필요한 경우 사용] SQL 서버는 고객 관리형 키를 사용하여 미사용 데이터를 암호화해야 합니다.<br />- [필요한 경우 사용] 스토리지 계정은 CMK(고객 관리형 키)를 암호화에 사용해야 합니다.<br />- 구독에 최대 3명의 소유자를 지정해야 합니다.<br />- 방화벽 및 가상 네트워크 구성을 사용한 스토리지 계정에 대한 액세스를 제한해야 합니다.<br />- SQL 관리형 인스턴스의 Advanced Data Security 설정에서 모든 Advanced Threat Protection 유형을 사용하도록 설정해야 합니다.<br />- SQL Server의 Advanced Data Security 설정에서 모든 Advanced Threat Protection 유형을 사용하도록 설정해야 합니다.<br />- SQL 서버에 대해 Azure Active Directory 관리자를 프로비저닝해야 합니다.<br />- API Management 서비스에서 가상 네트워크를 사용해야 합니다.<br />- SQL 서버에 대한 감사 보존 기간은 90일 이상으로 설정해야 합니다.<br />- 구독에 Log Analytics 에이전트의 자동 프로비저닝을 사용하도록 설정해야 합니다.<br />- Automation 계정 변수를 암호화해야 합니다.<br />- Azure Backup을 가상 머신에 사용하도록 설정해야 합니다.<br />- Azure Cosmos DB 계정에 방화벽 규칙이 있어야 합니다.<br />- 보호되지 않는 Azure SQL 서버에 대해 Azure Defender for SQL을 사용하도록 설정해야 합니다.<br />- 보호되지 않는 SQL Managed Instances에 대해 Azure Defender for SQL을 사용하도록 설정해야 합니다.<br />- Cognitive Services 계정은 데이터 암호화를 사용하도록 설정해야 합니다.<br />- Cognitive Services 계정은 네트워크 액세스를 제한해야 합니다.<br />- Cognitive Services 계정은 고객 소유 스토리지를 사용하거나 데이터 암호화를 사용하도록 설정해야 합니다.<br />- 기본 IP 필터 정책은 거부여야 합니다.<br />- IoT Hub의 진단 로그를 사용하도록 설정해야 합니다.<br />- 심각도가 높은 경고에 대해 이메일 알림을 사용하도록 설정해야 합니다.<br />- 심각도가 높은 경고에 대해 구독 소유자에게 이메일 알림을 사용하도록 설정해야 합니다.<br />- API 앱에서 클라이언트 인증서(들어오는 클라이언트 인증서)가 ‘켜기’로 설정되어 있는지 확인합니다.<br />- 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.<br />- 읽기 권한이 있는 외부 계정을 구독에서 제거해야 합니다.<br />- Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.<br />- Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.<br />- Azure Database for PostgreSQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.<br />- 지원되는 Linux 가상 머신 확장 집합에 게스트 증명 확장을 설치해야 합니다.<br />- 지원되는 Linux 가상 머신에 게스트 증명 확장을 설치해야 합니다.<br />- 지원되는 Windows 가상 머신 확장 집합에 게스트 증명 확장을 설치해야 합니다.<br />- 지원되는 Windows 가상 머신에 게스트 증명 확장을 설치해야 합니다.<br />- 게스트 구성 확장을 머신에 설치해야 합니다.<br />- 동일한 인증 자격 증명<br />- IoT 디바이스 - 에이전트에서 미달 사용된 메시지를 보내고 있습니다.<br />- IoT 디바이스 - Auditd 프로세스가 이벤트 전송을 중지합니다.<br />- IoT 디바이스 - 디바이스에서 포트를 엽니다.<br />- IoT 디바이스 - 운영 체제 기준 유효성 검사 실패<br />- IoT 디바이스 - 체인 중 하나에서 허용되는 방화벽 정책을 찾습니다.<br />- IoT 디바이스 - 입력 체인 중 하나에서 허용되는 방화벽 규칙이 발견되었습니다.<br />- IoT 디바이스 - 출력 체인에서 허용되는 방화벽 규칙이 발견되었습니다.<br />- IoT 디바이스 - TLS 암호화 도구 모음 업그레이드가 필요합니다.<br />- IP 필터 규칙 큰 IP 범위<br />- Java를 최신 API 앱 버전으로 업데이트해야 합니다.<br />- Java를 최신 함수 앱 버전으로 업데이트해야 합니다.<br />- Java를 최신 웹앱 버전으로 업데이트해야 합니다.<br />- Key Vault 키에 만료 날짜가 있어야 합니다.<br />- Key Vault 비밀에 만료 날짜가 있어야 합니다.<br />- 키 자격 증명 모음에 제거 방지를 사용하도록 설정해야 합니다.<br />- 키 자격 증명 모음에 일시 삭제를 사용하도록 설정해야 합니다.<br />- Kubernetes 클러스터는 HTTPS를 통해서만 액세스할 수 있어야 합니다.<br />- Kubernetes 클러스터는 자동 탑재 API 자격 증명을 사용하지 않도록 설정해야 합니다.<br />- Kubernetes 클러스터는 CAPSYSADMIN 보안 기능을 부여하지 않아야 합니다.<br />- Kubernetes 클러스터는 기본 네임스페이스를 사용하지 않아야 합니다.<br />- Linux 가상 머신은 커널 모듈 서명 유효성 검사를 적용해야 합니다.<br />- Linux 가상 머신은 서명 되 고 신뢰할 수 있는 부팅 구성 요소만 사용 해야 합니다.<br />- Linux 가상 머신은 보안 부팅을 사용해야 합니다.<br />- 보안 구성 업데이트를 적용하려면 머신을 다시 시작해야 합니다.<br />- 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.<br />- 구독에서 읽기 권한이 있는 계정에 MFA를 사용하도록 설정해야 합니다.<br />- Linux 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 합니다.<br />- Windows 가상 머신에 네트워크 트래픽 데이터 수집 에이전트를 설치해야 합니다.<br />- Network Watcher를 사용하도록 설정해야 합니다.<br />- 네트워크 보안 그룹을 사용하여 비인터넷 연결 가상 머신을 보호해야 합니다.<br />- PHP를 최신 API 앱 버전으로 업데이트해야 합니다.<br />- PHP를 최신 웹앱 버전으로 업데이트해야 합니다.<br />- Azure SQL Database에서 프라이빗 엔드포인트 연결을 사용하도록 설정해야 합니다.<br />- Azure SQL Database에서 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.<br />- Cognitive Services 계정에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.<br />- Python을 최신 API 앱 버전으로 업데이트해야 합니다.<br />- Python을 최신 함수 앱 버전으로 업데이트해야 합니다.<br />- Python을 최신 웹앱 버전으로 업데이트해야 합니다.<br />- API 앱에 대해 원격 디버깅을 해제해야 합니다.<br />- 함수 앱에 대해 원격 디버깅을 해제해야 합니다.<br />- 웹 애플리케이션에 대해 원격 디버깅을 해제해야 합니다.<br />- 지원되는 Windows 가상 머신에서 보안 부팅을 사용하도록 설정해야 합니다.<br />- 스토리지 계정을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.<br />- 서브넷을 네트워크 보안 그룹과 연결해야 합니다.<br />- 구독에 보안 문제에 대한 연락처 이메일 주소가 있어야 합니다.<br />- 구독에 둘 이상의 소유자를 할당해야 합니다.<br />- Azure Key Vault에 저장된 인증서의 유효 기간이 12개월을 넘어서는 안 됩니다.<br />- 가상 머신 게스트 증명 상태가 정상이어야 합니다.<br />- 가상 머신을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.<br />- 가상 머신의 게스트 구성 확장은 시스템이 할당한 관리 ID를 사용하여 배포해야 합니다.<br />- 지원되는 가상 머신에서 vTPM을 사용하도록 설정해야 합니다.<br />- 웹앱에서 들어오는 모든 요청에 대해 SSL 인증서를 요청해야 합니다.<br />- Windows Defender Exploit Guard를 머신에서 사용하도록 설정해야 합니다.<br />- Windows 웹 서버는 보안 통신 프로토콜을 사용하도록 구성해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>0</strong></td>
    <td class="tg-lboi"><strong>데이터 분류 적용</strong> - 민감도 및 비즈니스 영향을 기준으로 조직의 데이터를 분류 하면 데이터에 대 한 가치를 확인 하 고 할당 하 고 거 버 넌 스 전략 및 기본을 제공할 수 있습니다.</td>
    <td class="tg-lboi" width=55%>- SQL 데이터베이스의 중요한 데이터를 분류해야 합니다.</td>
  </tr>
  <tr>
    <td class="tg-lboi"><strong>0</strong></td>
    <td class="tg-lboi"><strong>Azure Defender 사용</strong> - 이러한 권장 사항을 사용 하 여 <a href="/azure/security-center/azure-defender">Azure Defender 계획</a>을 사용 하도록 설정 합니다.</td>
    <td class="tg-lboi" width=55%>- Azure Arc 사용 가능 Kubernetes 클러스터에는 Azure Defender의 확장이 설치 되어 있어야 합니다.<br />- Azure Defender for App Service를 사용하도록 설정해야 합니다.<br />- Azure SQL Database 서버용 Azure Defender를 사용하도록 설정해야 합니다.<br />- 컨테이너 레지스트리용 Azure Defender를 사용하도록 설정해야 합니다.<br />- Azure Defender for DNS를 사용하도록 설정해야 합니다.<br />- Azure Defender for Key Vault를 사용하도록 설정해야 합니다.<br />- Azure Defender for Kubernetes를 사용하도록 설정해야 합니다.<br />- Azure Defender for Resource Manager를 사용하도록 설정해야 합니다.<br />- 서버용 Azure Defender를 사용하도록 설정해야 합니다.<br />- 작업 영역에서 서버에 대한 Azure Defender 사용하도록 설정해야 합니다.<br />- 머신에서 SQL 서버용 Azure Defender를 사용하도록 설정해야 합니다.<br />- 스토리지용 Azure Defender를 사용하도록 설정해야 합니다.</td>
  </tr>
</tbody>
</table>
