---
title: Azure Stack Edge 2110 릴리스 정보
description: 2110 릴리스를 실행 하는 Azure Stack Edge에 대 한 중요 한 미해결 문제 및 해결 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/26/2021
ms.author: alkohli
ms.openlocfilehash: 36c7e05e2cce3e93fcd35521582190f2d7cef2b5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469164"
---
# <a name="azure-stack-edge-2110-release-notes"></a>Azure Stack Edge 2110 릴리스 정보

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

다음 릴리스 정보는 Azure Stack Edge 장치의 2110 릴리스에 대 한 중요 한 미해결 문제 및 해결 된 문제를 식별 합니다. 이러한 릴리스 정보는 Azure Stack Edge Pro GPU, Azure Stack Edge Pro R 및 Azure Stack Edge Mini R 디바이스에만 해당됩니다. 특정 모델에 해당하는 기능 및 문제가 설명됩니다(해당하는 경우).

릴리스 정보는 계속 업데이트되며, 해결 방법이 필요한 중대한 문제가 발견되면 해당 내용이 추가됩니다. 디바이스를 배포하기 전에 릴리스 정보에 포함된 정보를 주의 깊게 검토하세요.

이 문서는 소프트웨어 버전 번호 **2.2.1758.4034** 에 매핑되는 **Azure Stack Edge 2110** 릴리스에 적용 됩니다. 2.2.1636.3457 (Azure Stack Edge 2106) 소프트웨어를 하나 이상 실행 하는 경우이 소프트웨어를 장치에 적용할 수 있습니다.

## <a name="whats-new"></a>새로운 기능

Azure Stack Edge 2110 릴리스에서는 다음과 같은 새로운 기능을 사용할 수 있습니다. 

- **Windows 업데이트 및 보안 수정** - [Windows 및 9 월의 최신 누적 업데이트 (LCU)](https://support.microsoft.com/topic/september-14-2021-kb5005623-monthly-rollup-bcdb6598-517e-4d53-aa7c-dd7fcfdca204) 는 Azure Stack에 지에 대 한 업데이트 패키지에 롤오버 되었습니다.
- **원격 지원** -이 릴리스에서는 사용자가 장치에 원격으로 액세스 하 여 문제를 진단 하 고 수정할 수 Microsoft 지원 있도록 Azure Stack Edge 장치에서 원격 지원을 사용 하도록 설정할 수 있습니다. 이 기능을 사용 하도록 설정 하면 액세스 수준 및 액세스 기간에 대 한 동의를 제공 합니다. 자세한 내용은 [Azure Stack Edge에 대 한 원격 지원 및 진단 사용](azure-stack-edge-gpu-remote-support-diagnostics-repair.md)을 참조 하세요.
- **고성능 네트워크 가상 컴퓨터** -이 릴리스를 시작 하면 고성능 네트워크 가상 컴퓨터를 Azure Stack Edge 장치에 배포할 수 있습니다. 자세한 내용은 [Azure Stack Edge에 고성능 네트워크 가상 컴퓨터 배포](azure-stack-edge-gpu-deploy-virtual-machine-high-performance-network.md)를 참조 하세요.
- **Edge 컨테이너 레지스트리 및 Kubernetes 대시보드의 인증서** -edge 컨테이너 레지스트리 및 Kubernetes 대시보드의 인증서가 이제 지원 됩니다. 로컬 UI를 통해 인증서를 만들고 업로드할 수 있습니다. 자세한 내용은 [Kubernetes certificate](azure-stack-edge-gpu-certificates-overview.md#kubernetes-certificates) and [업로드 Kubernetes certificate](azure-stack-edge-gpu-manage-certificates.md#upload-kubernetes-certificates)항목을 참조 하세요.
- **Bgp 모드의 Metallb** -이 릴리스를 시작 하면 bgp (Border Gateway Protocol)를 통해 Metallb를 사용 하 여 Azure Stack Edge 장치에서 부하 분산을 구성할 수 있습니다. 장치를 PowerShell 인터페이스에 연결 하 고 특정 cmdlet을 실행 하 여 구성을 수행 합니다. 자세한 내용은 [Azure Stack Edge 장치에서 MetalLB를 사용 하 여 부하 분산 구성](azure-stack-edge-gpu-configure-metallb-bgp-mode.md)을 참조 하세요.



## <a name="issues-fixed-in-2110-release"></a>2110 릴리스에서 해결 된 문제

다음 테이블에는 이전 릴리스의 릴리스 정보에 언급되고 현재 릴리스에서 수정된 이슈가 나열되어 있습니다.

| 아니요. | 기능 | 문제 | 
| --- | --- | --- |
|**1.**|Azure Arc를 지원하는 Kubernetes |GA 릴리스의 경우 Azure Arc 지원 Kubernetes는 버전 0.1.18에서 0.2.9로 업데이트됩니다. Azure Arc 지원 Kubernetes 업데이트는 Azure Stack Edge 디바이스에서 지원되지 않으므로 Azure Arc 지원 Kubernetes를 다시 배포해야 합니다.|
|**2.**|Azure Arc를 지원하는 Kubernetes|웹 프록시가 Azure Stack Edge Pro 디바이스에 구성된 경우 Azure Arc 배포가 지원되지 않습니다.|
|**3.**|IoT Edge |IoT Edge를 통해 배포된 모듈은 호스트 네트워크를 사용할 수 없습니다. | 
|**4.**|Kubernetes + 업데이트 |2008 릴리스와 같은 이전 소프트웨어 버전에는 ClusterConnectionException을 나타내며 업데이트가 실패하도록 하는 경합 상태 업데이트 문제가 있습니다. |
|**5.**|Kubernetes 대시보드 | SSL 인증서가 포함된 Kubernetes 대시보드의 *Https* 엔드포인트는 지원되지 않습니다. | 
|**6.**|VM |VNF를 배포 하는 동안 VM 관리 NIC에 고정 IP 중복 검사가 추가 됩니다. 명시적 오류 메시지가 반환 됩니다. | 
|**7.**|VM |주소 공간에서 처음 4 개의 IP 주소에 대 한 IP 예약 검사가 제거 되었습니다.  | 
|**8.**|다중 액세스에 지 계산 |VNF를 배포 하는 동안 로컬 Azure Resource Manager 토큰 만료 문제가 해결 되었습니다. 이전 릴리스에서는 VHD를 다운로드 하는 데 오랜 시간이 걸릴 때 Azure Resource Manager 토큰이 만료 되 면 VNF 배포가 실패 합니다.  | 
|**9.**|다중 액세스에 지 계산 |VNF를 배포 하는 동안 Azure Resource Manager 호출에 대 한 제한 시간이 추가 되었습니다. 이전 릴리스에서는 Azure Resource Manager 호출이 실패 한 경우 VNF 배포에 오랜 시간이 걸렸습니다.   | 
|**10.**|다중 액세스에 지 계산 |다중 액세스에 지 계산 VHD 다운로드가 완료 된 후 Azure Resource Manager 템플릿 배포를 정리 합니다. 이전 릴리스에서는 많은 VNF 배포 후 사용자가 배포 할당량 초과 오류를 발생 시킬 수 있습니다. 기본 할당량은 리소스 그룹당 800 배포 였습니다.  | 



## <a name="known-issues-in-2110-release"></a>2110 릴리스의 알려진 문제

다음 표에서는 2110 릴리스의 알려진 문제에 대 한 요약을 제공 합니다.

| 아니요. | 기능 | 문제 | 해결 방법/설명 |
| --- | --- | --- | --- |
|**1.**|미리 보기 기능 |이 릴리스의 경우 로컬 Azure Resource Manager, vm, vm의 클라우드 관리, Kubernetes 클라우드 관리, Azure Stack Edge Pro R 및 Azure Stack edge 미니 R 용 VPN, mp (다중 프로세스 서비스), Azure Stack에 지 Pro에 대 한 mec (다중 액세스에 지 컴퓨팅)는 모두 미리 보기에서 사용할 수 있습니다.  |이러한 기능은 이후 릴리스에서 일반적으로 제공될 예정입니다. |



## <a name="known-issues-from-previous-releases"></a>이전 릴리스의 알려진 이슈

다음 테이블에서는 이전 릴리스에서 전달된 알려진 이슈를 간략하게 설명합니다.

| 아니요. | 기능 | 문제 | 해결 방법/설명 |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | SQL 데이터베이스를 만들려면 관리자 액세스 권한이 필요합니다.   |[Create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database)에서 1~2단계 대신 다음 단계를 수행합니다. <ol><li>디바이스의 로컬 UI에서 컴퓨팅 인터페이스를 사용하도록 설정합니다. **컴퓨팅 > 포트 번호 > 컴퓨팅에 사용 > 적용** 을 선택합니다.</li><li>[SQL 명령 유틸리티](/sql/tools/sqlcmd-utility)에서 `sqlcmd`를 클라이언트 머신에 다운로드합니다. </li><li>컴퓨팅 인터페이스 IP 주소에 연결하고(사용하도록 설정된 포트) 주소 끝에 “,1401”을 추가합니다.</li><li>최종 명령은 sqlcmd -S {Interface IP},1401 -U SA -P “Strong!Passw0rd”와 비슷합니다.</li>그 이후에 현재 설명서의 3-4단계가 같아야 합니다. </li></ol> |
| **2.** |새로 고침| **새로 고침** 을 통해 복원된 Blob에 대한 증분 변경은 지원되지 않습니다. |Blob 엔드포인트의 경우 새로 고침 후 Blob의 부분 업데이트로 인해 업데이트가 클라우드에 업로드되지 않을 수 있습니다. 다음과 같은 작업 시퀀스를 예로 들 수 있습니다.<ol><li>클라우드에서 Blob을 만듭니다. 또는 디바이스에서 이전에 업로드된 Blob을 삭제합니다.</li><li>새로 고침 기능을 사용하여 클라우드에서 어플라이언스로 Blob을 새로 고칩니다.</li><li>Azure SDK REST API를 사용하여 Blob의 일부만 업데이트합니다.</li></ol>이러한 작업으로 인해 Blob의 업데이트된 섹션이 클라우드에서 업데이트되지 않을 수 있습니다. <br>**해결 방법**: robocopy와 같은 도구나 탐색기 또는 명령줄을 통한 일반 파일 복사를 사용하여 전체 Blob을 바꿉니다.|
|**3.**|제한|제한하는 동안 디바이스에 대한 새 쓰기가 허용되지 않는 경우 NFS 클라이언트의 쓰기가 “사용 권한이 거부됨” 오류를 나타내며 실패합니다.| 이 오류는 다음과 같이 표시됩니다.<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: ‘test’ 디렉터리를 만들 수 없습니다. 사용 권한이 거부되었습니다.|
|**4.**|Blob Storage 수집|Blob Storage 수집에 AzCopy 버전 10을 사용하는 경우 인수 `Azcopy <other arguments> --cap-mbps 2000`을 사용하여 AzCopy를 실행합니다.| AzCopy에 대해 이러한 제한을 제공하지 않으면 디바이스에 많은 요청을 전송하여 서비스에 문제를 유발할 수 있습니다.|
|**5.**|계층화된 스토리지 계정|계층화된 스토리지 계정을 사용하는 경우 다음이 적용됩니다.<ul><li> 블록 Blob만 지원됩니다. 페이지 Blob은 지원되지 않습니다.</li><li>스냅샷 또는 복사 API는 지원되지 않습니다.</li><li> `distcp`를 통한 Hadoop 워크로드 수집은 복사 작업을 과도하게 사용하므로 지원되지 않습니다.</li></ul>||
|**6.**|NFS 공유 연결|여러 프로세스에서 같은 공유로 복사하고 `nolock` 특성을 사용하지 않는 경우 복사하는 동안 오류가 표시될 수 있습니다.|NFS 공유에 파일을 복사하려면 `nolock` 특성을 탑재 명령에 전달해야 합니다. 예: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`|
|**7.**|Kubernetes 클러스터|Kubernetes 클러스터를 실행하는 디바이스에 업데이트를 적용하는 경우 kubernetes 가상 머신이 다시 시작되고 재부팅됩니다. 이 인스턴스에서 지정된 복제본과 함께 배포되는 Pod만 업데이트 후에 자동으로 복원됩니다.  |복제본 세트를 지정하지 않고 복제 컨트롤러 외부에서 개별 Pod를 만든 경우 이러한 Pod는 디바이스 업데이트 후에 자동으로 복원되지 않습니다. 이러한 Pod를 복원해야 합니다.<br>복제본 세트는 노드 실패 또는 중단형 노드 업그레이드 등을 비롯한 이유로 삭제되었거나 종료된 Pod를 대체합니다. 따라서 애플리케이션에 단일 Pod만 필요한 경우에도 복제본 세트를 사용하는 것이 좋습니다.|
|**8.**|Kubernetes 클러스터|Azure Stack Edge Pro의 Kubernetes는 Helm v3 이상에서만 지원됩니다. 자세한 내용은 [질문과 대답: Tiller 제거](https://v3.helm.sh/docs/faq/)를 참조하세요.|
|**9.**|Kubernetes |포트 31000은 Kubernetes 대시보드용으로 예약되어 있습니다. 포트 31001은 Edge 컨테이너 레지스트리용으로 예약되어 있습니다. 마찬가지로 기본 구성에서 IP 주소 172.28.0.1과 172.28.0.10은 각각 Kubernetes 서비스 및 Core DNS 서비스용으로 예약되어 있습니다.|예약된 IP는 사용하지 마세요.|
|**10.**|Kubernetes |현재 Kubernetes는 다중 프로토콜 LoadBalancer 서비스를 허용하지 않습니다. TCP와 UDP 모두에서 수신 대기해야 하는 DNS 서비스를 예로 들 수 있습니다. |MetalLB를 사용하여 Kubernetes의 제한을 해결하기 위해 같은 Pod 선택기에 두 가지 서비스(TCP용 1개, UDP용 1개)를 만들 수 있습니다. 이러한 서비스는 같은 공유 키와 spec.loadBalancerIP를 사용하여 같은 IP 주소를 공유합니다. 사용 가능한 IP 주소보다 더 많은 서비스를 사용하는 경우 IP를 공유할 수도 있습니다. <br> 자세한 내용은 [IP 주소 공유](https://metallb.universe.tf/usage/#ip-address-sharing)를 참조하세요.|
|**11.**|Kubernetes 클러스터|기존 Azure IoT Edge 마켓플레이스 모듈은 Azure Stack Edge 디바이스에서 IoT Edge를 실행하기 위해 수정이 필요할 수 있습니다.|자세한 내용은 [Azure Stack Edge Pro GPU 디바이스에서 Azure Stack Edge Pro FPGA 디바이스에서 기존 IoT Edge 모듈 실행을 참조하세요.](azure-stack-edge-gpu-modify-fpga-modules-gpu.md)|
|**12.**|Kubernetes |파일 기반 바인드 탑재는 Azure Stack Edge 디바이스에 있는 Kubernetes의 Azure IoT Edge에서 지원되지 않습니다.|IoT Edge는 변환 계층을 사용하여 `ContainerCreate` 옵션을 Kubernetes 구문으로 변환합니다. `hostpath` 디렉터리에 대한 `Binds` 맵을 만드는 작업, 즉 파일 기반 바인드 탑재는 IoT Edge 컨테이너의 경로에 바인딩될 수 없습니다. 가능하면 부모 디렉터리를 매핑합니다.|
|**13.**|Kubernetes |IoT Edge에 대한 자체 인증서를 가져와 디바이스에서 컴퓨팅을 구성한 후 Azure Stack Edge 디바이스에 해당 인증서를 추가하는 경우 새 인증서가 선택되지 않습니다.|이 문제를 해결하려면 디바이스에서 컴퓨팅을 구성하기 전에 인증서를 업로드해야 합니다. 컴퓨팅이 이미 구성되어 있으면 [디바이스의 PowerShell 인터페이스에 연결하고 IoT Edge 명령을 실행](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands)합니다. `iotedged` 및 `edgehub` Pod를 다시 시작합니다.|
|**14.**|인증서 |특정한 경우 로컬 UI의 인증서 상태를 업데이트하는 데 몇 초 정도 걸릴 수 있습니다. |로컬 UI의 다음 시나리오에 영향을 줄 수 있습니다.<ul><li>**인증서** 페이지의 **상태** 열.</li><li>**시작** 페이지의 **보안** 타일.</li><li>**개요** 페이지의 **구성** 타일.</li></ul>  |
|**15.**|컴퓨팅 + Kubernetes |컴퓨팅/Kubernetes는 NTLM 웹 프록시를 지원하지 않습니다. ||
|**16**|Internet Explorer|향상된 보안 기능을 사용하도록 설정한 경우 로컬 웹 UI 페이지에 액세스하지 못할 수 있습니다. | 향상된 보안을 사용하지 않도록 설정하고 브라우저를 다시 시작합니다.|
|**17.**|Kubernetes |Kubernetes는 .NET 애플리케이션에서 사용하는 환경 변수 이름에서 ":"을 지원하지 않습니다. 이러한 조건은 Event Grid IoT Edge 모듈이 Azure Stack Edge 디바이스 및 기타 애플리케이션에서 작동하는 데도 필요합니다. 자세한 내용은 [ASP.NET core 설명서](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration#environment-variables)를 참조하세요.|“:”을 이중 밑줄로 바꿉니다. 자세한 내용은 [Kubernetes 이슈](https://github.com/kubernetes/kubernetes/issues/53201)를 참조하세요.|
|**18.** |Azure Arc + Kubernetes 클러스터 |기본적으로 Git 리포지토리에서 리소스 `yamls`가 삭제되면 해당 리소스가 Kubernetes 클러스터에서 삭제되지 않습니다.  |Git 리포지토리에서 삭제될 때 리소스 삭제를 허용하려면 Arc OperatorParams에서 `--sync-garbage-collection`을 설정합니다. 자세한 내용은 [구성 삭제](../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md#additional-parameters)를 참조하세요. |
|**19.**|NFS |디바이스에서 데이터를 쓰기 위해 NFS 공유 탑재를 사용하는 애플리케이션은 단독 쓰기를 사용해야 합니다. 이렇게 하면 디스크에 쓸 수 있습니다.| |
|**20.**|컴퓨팅 구성 |네트워크 구성에서 컴퓨팅 구성이 실패합니다. 게이트웨이나 스위치 또는 라우터가 네트워크에 존재하지 않는 시스템에 대한 ARP(주소 확인 프로토콜) 요청에 응답하기 때문입니다.| |
|**21.**|컴퓨팅 및 Kubernetes |Kubernetes가 디바이스에 처음으로 설정된 경우 사용 가능한 모든 GPU를 클레임합니다. 따라서 Kubernetes를 설정한 후에는 GPU를 사용하여 Azure Resource Manager VM을 만들 수 없습니다. |디바이스에 GPU가 2개 있는 경우 GPU를 사용하는 VM 1개를 만든 다음, Kubernetes를 구성할 수 있습니다. 이 경우 Kubernetes는 사용 가능한 나머지 1개의 GPU를 사용합니다. |
|**22.**|사용자 지정 스크립트 VM 확장 |이전 릴리스에서 만든 Windows VM에 알려진 문제가 있으며 디바이스가 2103으로 업데이트되었습니다. <br> 해당 VM에 사용자 지정 스크립트 확장을 추가하면 Windows VM Guest Agent(버전 2.7.41491.901만 해당)의 업데이트에서 중단되어 확장 배포 시간이 초과됩니다. | 이 문제를 해결하려면 <ol><li> RDP(원격 데스크톱 프로토콜)을 사용하여 Windows VM에 연결합니다. </li><li> `waappagent.exe`가 머신에서 실행 중인지 확인: `Get-Process WaAppAgent`. </li><li> `waappagent.exe`가 실행 중이지 않으면 `rdagent` 서비스 다시 시작: `Get-Service RdAgent` \| `Restart-Service`. 5분 동안 기다립니다.</li><li> `waappagent.exe` 실행 중에 `WindowsAzureGuest.exe` 프로세스를 종료합니다. </li><li>프로세스를 종료하면 프로세스가 최신 버전으로 다시 실행되기 시작합니다.</li><li>`Get-Process WindowsAzureGuestAgent` \| `fl ProductVersion` 명령을 사용하여 Windows VM Guest Agent 버전이 2.7.41491.971인지 확인합니다.</li><li>[Windows VM에서 사용자 지정 스크립트 확장을 설정](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md)합니다. </li><ol> |
|**23.**|GPU VM |이 릴리스 이전에는 GPU VM 수명 주기가 업데이트 흐름에서 관리되지 않았습니다. 따라서 2103 릴리스로 업데이트할 때 GPU VM은 업데이트 중에 자동으로 중지되지 않습니다. 디바이스를 업데이트하기 전에 `stop-stayProvisioned` 플래그를 사용하여 GPU VM을 수동으로 중지해야 합니다. 자세한 내용은 [VM 일시 중단 또는 종료](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm)를 참조하세요.<br> 업데이트 전에 계속 실행되는 모든 GPU VM은 업데이트 후에 시작됩니다. 이 경우 VM에서 실행 중인 워크로드는 정상적으로 종료되지 않습니다. 업데이트 후 VM이 원하는 상태가 아닐 수 있습니다. <br>업데이트 전에 `stop-stayProvisioned`를 통해 중지된 모든 GPU VM은 업데이트 후 자동으로 시작됩니다. <br>Azure Portal을 통해 GPU VM을 중지하는 경우 디바이스를 업데이트 다음 VM을 수동으로 시작해야 합니다.| Kubernetes를 사용하여 GPU VM을 실행하는 경우 업데이트 직전에 GPU VM을 중지합니다. <br>GPU VM이 중지되면 Kubernetes는 원래 VM에서 사용했던 GPU를 인수합니다. <br>GPU VM이 중지된 상태에 있는 시간이 길수록 Kubernetes가 GPU를 인수할 가능성이 커집니다. |
|**24.**|MPS(다중 프로세스 서비스) |디바이스 소프트웨어와 Kubernetes 클러스터를 업데이트하면 워크로드의 MPS 설정이 유지되지 않습니다.   |[MPS를 사용하도록 다시 설정](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)하고 MPS를 사용한 워크로드를 다시 배포합니다. |


## <a name="next-steps"></a>다음 단계

- [디바이스 업데이트](azure-stack-edge-gpu-install-update.md)
