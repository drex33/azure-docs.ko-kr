---
title: '참조: 알려진 문제 및 문제 해결'
titleSuffix: Azure Data Science Virtual  Machine
description: Azure Data Science Virtual Machine에 대해 알려진 문제, 해결 방법 및 문제 해결의 목록을 가져옵니다.
services: machine-learning
ms.service: data-science-vm
author: michalmar
ms.author: mimarusa
ms.topic: reference
ms.date: 08/02/2021
ms.openlocfilehash: c928dfa133172f83c097aa9df7d92486524f62ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535816"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine에 대해 알려진 문제 및 문제 해결

본 문서는 Azure Data Science Virtual Machine을 사용할 때 발생할 수 있는 오류나 실패를 찾아 수정하는 데 도움이 됩니다.


## <a name="ubuntu"></a>Ubuntu

### <a name="fix-gpu-on-nvidia-a100-gpu-chip---azure-ndasrv4-series"></a>NVIDIA A100 GPU 칩의 GPU 수정 - Azure NDasrv4 시리즈 

ND A100 v4 시리즈 가상 머신은 고급 Deep Learning 학습 및 긴밀하게 결합된 HPC 워크로드 스케일 업 및 스케일 아웃을 위해 설계된 Azure GPU 제품군에 새로 추가된 주력 가상 머신입니다.

아키텍처가 다르기 때문에 TensorFlow 또는 PyTorch 프레임워크를 사용하는 GPU 가속을 활용하려면 까다로운 워크로드에 대해 서로 다른 설정이 필요합니다.

ND A100 머신 GPU를 지원하기 위해 노력하고 있습니다. 한편 NVIDIA의 Fabric Manager를 추가하고 드라이버를 업데이트하여 GPU를 작동시킬 수 있습니다. 

터미널에서 다음 간단한 단계를 수행합니다.

1. NVIDIA의 리포지토리를 추가하여 드라이버를 설치/업데이트합니다. 단계별 지침은 [여기](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html#ubuntu-lts)에서 찾을 수 있습니다.
2. [선택 사항] 위의 리포지토리에서 CUDA 드라이버를 업데이트할 수도 있습니다.
3. NVIDIA의 Fabric Manager 드라이버를 설치합니다.

    ```
    sudo apt-get install cuda-drivers-460
    sudo apt-get install cuda-drivers-fabricmanager-460
    ```

4. VM을 다시 부팅하여 드라이버를 준비합니다.
5. 새로 설치된 NVIDIA Fabric Manager 서비스를 사용하도록 설정하고 시작합니다.

    ```
    sudo systemctl enable nvidia-fabricmanager
    sudo systemctl start nvidia-fabricmanager
    ```

이제 다음을 실행하여 드라이버 및 GPU가 작동하는지 확인할 수 있습니다.
```
systemctl status nvidia-fabricmanager.service
``` 

그 후 ![nvidia-fabric-manager-status](./media/nvidia-fabricmanager-status-ok-marked.png)를 실행하는 Fabric Manager 서비스가 표시됩니다.


### <a name="connection-to-desktop-environment-fails"></a>데스크톱 환경에 대한 연결 실패

SSH 터미널을 통해 DSVM에 연결할 수 있지만 x2go를 통해 연결할 수 없는 경우 x2go에서 잘못된 세션 유형을 설정했을 수 있습니다.
DSVM의 데스크톱 환경에 연결하려면 *x2go/session preferences/session* 의 세션 유형이 *XFCE* 로 설정되어 있어야 합니다. 다른 데스크톱 환경은 현재 지원되지 않습니다.

### <a name="fonts-look-wrong-when-connecting-to-dsvm-using-x2go"></a>x2go를 사용하여 DSVM에 연결할 때 글꼴이 잘못 표시됨

x2go에 연결하고 일부 글꼴이 잘못 표시되면 x2go의 세션 설정과 관련이 있을 수 있습니다. DSVM에 연결하기 전에 세션 기본 설정 대화 상자의 "입력/출력" 탭에서 "디스플레이 DPI 설정" 확인란의 선택을 취소합니다.

### <a name="prompted-for-unknown-password"></a>알 수 없는 암호를 묻는 메시지 표시

DSVM 설정 *인증 유형* 을 *SSH 공개 키* 로 생성할 때(암호 인증 사용보다 권장됨), 암호가 제공되지 않습니다. 그러나 일부 시나리오에서는 일부 애플리케이션에서 여전히 암호를 요청합니다. `sudo passwd <user_name>`을 실행하여 특정 사용자에 대한 새로운 암호를 만듭니다. `sudo passwd`를 사용하여 루트 사용자에 대한 새로운 암호를 만들 수 있습니다.

이러한 명령을 실행해도 SSH의 구성은 변경되지 않으며 허용되는 로그인 메커니즘은 동일하게 유지됩니다. 

### <a name="prompted-for-password-when-running-sudo-command"></a>sudo 명령을 실행할 때 암호를 묻는 메시지 표시

Ubuntu 머신에서 `sudo` 명령을 실행할 때 로그인한 사용자가 실제로 맞는지 확인하기 위해 암호를 다시 입력하라는 메시지가 표시될 수 있습니다. 이 동작은 예상되며 Ubuntu의 기본값입니다. 그러나 경우에 따라 반복되는 인증이 필요하지 않고 귀찮게 됩니다.

대부분의 경우 재인증을 사용하지 않도록 설정하려면 터미널에서 다음 명령을 실행하면 됩니다.

 `echo -e "\n$USER ALL=(ALL) NOPASSWD: ALL\n" | sudo tee -a /etc/sudoers`

터미널을 다시 시작한 후 sudo는 다른 로그인을 요청하지 않고 세션 로그인의 인증이 충분한 것으로 간주합니다.

### <a name="cannot-use-docker-as-non-root-user"></a>루트가 아닌 사용자로 docker를 사용할 수 없음

docker를 루트가 아닌 사용자로 사용하려면 사용자가 docker 그룹의 멤버여야 합니다. `getent group docker` 명령을 실행하여 해당 그룹에 속한 사용자를 확인할 수 있습니다. docker 그룹에 사용자를 추가하려면 `sudo usermod -aG docker $USER`를 실행합니다.

### <a name="docker-containers-cannot-interact-with-the-outside-via-network"></a>docker 컨테이너가 네트워크를 통해 외부와 상호 작용할 수 없음

기본적으로 docker는 `172.17.0.0/16`이라는 "브리지 네트워크"에 새 컨테이너를 추가합니다. 해당 브리지 네트워크의 서브넷이 DSVM의 서브넷 또는 구독에 있는 다른 프라이빗 서브넷과 겹치는 경우 호스트와 컨테이너 간의 네트워크 통신이 불가능합니다. 이 경우 컨테이너에서 실행되는 웹 애플리케이션에 연결할 수 없으며 컨테이너는 apt에서 패키지를 업데이트할 수 없습니다.

이 문제를 해결하려면 구독의 다른 네트워크와 겹치지 않는 브리지 네트워크에 IP 주소 공간을 사용하도록 docker를 다시 구성해야 합니다. 예를 들면 다음과 같습니다.

```json
"default-address-pools": [
        {
            "base": "10.255.248.0/21",
            "size": 21
        }
    ]
```

`/etc/docker/daemon.json` 파일에 포함된 JSON 문서에 위의 내용을 추가하면 docker는 브리지 네트워크에 다른 서브넷을 할당합니다. (예를 들어 `sudo nano /etc/docker/daemon.json`을 실행하여 sudo를 사용해 파일을 편집해야 합니다.)

변경 후 `service docker restart`를 실행하여 docker 서비스를 다시 시작해야 합니다.

변경 내용이 적용되었는지 확인하려면 `docker network inspect bridge`를 실행하면 됩니다. *IPAM.Config.Subnet* 아래의 값은 위에서 지정한 주소 풀과 일치해야 합니다.

### <a name="gpus-not-available-in-docker-container"></a>docker 컨테이너에서 GPU를 사용할 수 없음

DSVM에 설치된 docker는 기본적으로 GPU를 지원합니다. 그러나 충족해야 하는 몇 가지 필수 구성 요소가 있습니다.

* DSVM의 VM 크기에는 하나 이상의 GPU가 포함되어야 합니다.
* `docker run`으로 docker 컨테이너를 시작할 때 *--gpus* 매개 변수를 추가해야 합니다(예: `--gpus all`).
* NVIDIA A100 GPU를 포함하는 VM 크기에는 추가 소프트웨어 패키지(특히 [NVIDIA Fabric Manager](https://docs.nvidia.com/datacenter/tesla/pdf/fabric-manager-user-guide.pdf))가 설치되어 있어야 합니다. 이러한 패키지는 이미지에 아직 미리 설치되지 않았을 수 있습니다.


## <a name="windows"></a>Windows

### <a name="accessing-sql-server"></a>SQL Server 액세스

사전 설치된 SQL Server 인스턴스에 연결하려고 할 때 "로그인 실패"오류가 발생할 수 있습니다. SQL Server 인스턴스에 성공적으로 연결하려면 연결 중인 프로그램(예: SQL Server Management Studio(SSMS))을 관리자 모드로 실행해야 합니다. 관리자 모드에서는 DSVM의 기본값 때문에 관리자만 연결할 수 있어야 합니다.

### <a name="hyper-v-does-not-work"></a>Hyper-V가 작동하지 않음

Hyper-V가 처음에 Windows에서 작동하지 않는 것은 예상된 동작입니다. 부팅 성능을 위해 일부 서비스를 사용하지 않도록 설정했습니다.
Hyper-V를 사용하도록 설정하려면 다음을 수행합니다.

1. Windows DSVM에서 검색 창 열기
1. "서비스"를 입력
1. 모든 Hyper-V 서비스를 "수동"으로 설정
1. "Hyper-V 가상 머신 관리"를 "자동"으로 설정

최종 화면이 다음과 같아야 합니다.

   

![Hyper-V 사용](./media/workaround/hyperv-enable-dsvm.png)
