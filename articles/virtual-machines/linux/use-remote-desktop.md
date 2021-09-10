---
title: Linux에 xrdp 사용
description: Azure에서 그래픽 도구를 사용하여 Linux VM에 연결하도록 원격 데스크톱(xrdp)을 설치 및 구성하는 방법 알아보기
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.openlocfilehash: d6dd3fd6e4f4196c49fc6431218448e632c9e8b4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698876"
---
# <a name="install-and-configure-xrdp-to-use-remote-desktop-with-ubuntu"></a>Ubuntu에 원격 데스크톱을 사용하도록 xrdp 설치 및 구성

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure의 Linux VM(가상 머신)은 SSH(보안 셸) 연결을 사용하여 명령줄에서 일반적으로 관리됩니다. Linux를 처음 사용하거나 빠른 문제 해결 시나리오의 경우 원격 데스크톱을 사용하는 편이 더 쉬울 수 있습니다. 이 문서에서는 Ubuntu를 실행 중인 Linux VM에 데스크톱 환경([xfce](https://www.xfce.org)) 및 원격 데스크톱([xrdp](http://xrdp.org))을 설치하고 구성하는 방법에 대해 자세히 설명합니다.

이 문서에서는 Ubuntu 18.04 VM을 사용하여 작성하고 테스트했습니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 문서에는 Azure에 있는 기존 Ubuntu 18.04 LTS VM이 필요합니다. VM을 만들어야 하는 경우 다음 방법 중 하나를 사용합니다.

- [Azure CLI](quick-create-cli.md)
- [Azure Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Linux VM에서 데스크톱 환경 설치

Azure의 Linux VM은 대부분 데스크톱 환경을 기본적으로 설치할 필요가 없습니다. Linux VM은 일반적으로 데스크톱 환경이 아닌 SSH 연결을 사용하여 관리됩니다. Linux에서 다양한 데스크톱 환경을 선택할 수 있습니다. 데스크톱 환경 선택에 따라 1~2GB의 디스크 공간을 사용하고 모든 필수 패키지를 설치 및 구성하는 데 5~10분이 걸릴 수 있습니다.

다음 예제에서는 Ubuntu 18.04 LTS VM에 경량 [xfce4](https://www.xfce.org/) 데스크톱 환경을 설치합니다. 다른 배포에 대한 명령은 약간씩 다릅니다. 예를 들어 `yum`을 사용하여 Red Hat Enterprise Linux에 설치하고 적절한 `selinux` 규칙을 구성하거나 `zypper`를 사용하여 SUSE에 설치합니다.

먼저 VM에 SSH를 사용합니다. 다음 예제에서는 사용자 이름 *azureuser* 를 사용하여 *myvm.westus.cloudapp.azure.com* 이라는 VM에 연결합니다. 고유한 값을 사용합니다.

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Windows를 사용하고 SSH를 사용하는 방법에 대해 자세히 알아보려면 [Windows에서 SSH 키 사용 방법](ssh-from-windows.md)을 참조하세요.

다음으로 다음과 같이 `apt`를 사용하여 xfce를 설치합니다.

```bash
sudo apt-get update
sudo apt-get -y install xfce4
sudo apt install xfce4-session
```

## <a name="install-and-configure-a-remote-desktop-server"></a>원격 데스크톱 서버 설치 및 구성
데스크톱 환경을 설치했으므로 들어오는 연결을 수신하도록 원격 데스크톱 서비스를 구성합니다. [xrdp](http://xrdp.org)는 대부분의 Linux 배포판에서 사용할 수 있는 오픈 소스 RDP(원격 데스크톱 프로토콜) 서버로 xfce와 함께 잘 작동합니다. 다음과 같이 Ubuntu VM에 xrdp를 설치합니다.

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

사용자 세션을 시작할 때 사용할 데스크톱 환경을 xrdp에 알립니다. xfce를 구성하여 다음과 같이 xfce를 데스크톱 환경으로 사용합니다.

```bash
echo xfce4-session >~/.xsession
```

다음과 같이 적용되려면 변경을 위해 xrdp 서비스를 다시 시작합니다.

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>로컬 사용자 계정 암호 설정
VM을 만들 때 사용자 계정의 암호를 만든 경우 이 단계를 건너뜁니다. SSH 키 인증을 사용하고 로컬 계정 암호를 설정하지 않은 경우 xrdp를 사용하여 VM에 로그인하기 전에 암호를 지정합니다. xrdp는 인증을 위해 SSH 키를 수락할 수 없습니다. 다음 예제에서는 사용자 계정 *azureuser* 의 암호를 지정합니다.

```bash
sudo passwd azureuser
```

> [!NOTE]
> 암호를 지정하더라도 현재 암호 로그인을 허용하지 않으면 암호 로그인을 허용하도록 SSHD 구성이 업데이트되지 않습니다. 보안의 관점에서 키 기반 인증을 사용하여 SSH 터널을 통해 VM에 연결한 다음 xrdp에 연결하려고 할 수 있습니다. 그렇다면, 원격 데스크톱 트래픽을 허용하도록 네트워크 보안 그룹 규칙을 만드는 방법에 대한 다음 단계를 건너뜁니다.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>원격 데스크톱 트래픽의 네트워크 보안 그룹 규칙 만들기
Linux VM에 도달하는 원격 데스크톱 트래픽을 허용하려면 포트 3389에 대한 TCP가 VM에 도달하도록 네트워크 보안 그룹 규칙을 만들어야 합니다. 네트워크 보안 그룹 규칙에 대한 자세한 내용은 [네트워크 보안 그룹이란?](../../virtual-network/network-security-groups-overview.md)을 참조하세요. [Azure Portal을 사용하여 네트워크 보안 그룹 규칙을 만들](../windows/nsg-quickstart-portal.md) 수도 있습니다.

다음 예제에서는 *3389* 포트에서 [az vm open-port](/cli/azure/vm#az_vm_open_port)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다. VM에 대한 SSH 세션이 아닌 Azure CLI에서 다음 네트워크 보안 그룹 규칙을 엽니다.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>원격 데스크톱 클라이언트와 Linux VM 연결

로컬 원격 데스크톱 클라이언트를 열고 IP 주소 또는 Linux VM의 DNS 이름에 연결합니다. 

:::image type="content" source="media/use-remote-desktop/remote-desktop.png" alt-text="원격 데스크톱 클라이언트의 스크린샷.":::

사용자 계정의 사용자 이름 및 암호를 VM에 다음과 같이 입력합니다.

:::image type="content" source="media/use-remote-desktop/xrdp-login.png" alt-text="xrdp 로그인 화면 스크린샷.":::

인증한 후에 xfce 데스크톱 환경이 로드되면 다음 예제와 유사합니다.

![xrdp를 통한 xfce 데스크톱 환경](./media/use-remote-desktop/xfce-desktop-environment.png)

로컬 RDP 클라이언트가 NLA(네트워크 수준 인증)를 사용하는 경우, 해당 연결 설정을 사용하지 않도록 설정해야 할 수 있습니다. XRDP는 현재 NLA를 지원하지 않습니다. [FreeRDP](https://www.freerdp.com) 등 NLA를 지원하는 대체 RDP 솔루션을 확인할 수도 있습니다.


## <a name="troubleshoot"></a>문제 해결
원격 데스크톱 클라이언트를 사용하여 Linux VM에 연결할 수 없으면 Linux VM에서 `netstat`을 사용하여 다음과 같이 VM이 RDP 연결을 수신 대기하는지 확인합니다.

```bash
sudo netstat -plnt | grep rdp
```

다음 예제에서는 TCP 포트 3389를 예상대로 수신 대기하는 VM을 보여 줍니다.

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

*xrdp-sesman* 서비스가 수신 대기하지 않는 경우 Ubuntu VM에서 다음과 같이 서비스를 다시 시작합니다.

```bash
sudo service xrdp restart
```

서비스가 응답하지 않는 이유는 Ubuntu VM의 */var/log* 에 있는 로그를 검토합니다. 또한 원격 데스크톱 연결을 시도하는 동안 syslog를 모니터링하여 모든 오류를 볼 수 있습니다.

```bash
tail -f /var/log/syslog
```

Red Hat Enterprise Linux 및 SUSE와 같은 기타 Linux 배포에는 검토할 서비스 및 다른 로그 파일 위치를 다시 시작하는 다른 방법이 있을 수 있습니다.

원격 데스크톱 클라이언트에서 응답을 수신하지 않고 시스템 로그에 이벤트가 표시되지 않는 경우 이 동작은 원격 데스크톱 트래픽이 VM에 도달할 수 없다는 것을 나타냅니다. 네트워크 보안 그룹 규칙을 검토하여 포트 3389에서 TCP를 허용하는 규칙이 있는지 확인합니다. 자세한 내용은 [애플리케이션 연결 문제 해결](/troubleshoot/azure/virtual-machines/troubleshoot-app-connection)을 참조하세요.


## <a name="next-steps"></a>다음 단계
Linux VM에서 SSH 키를 만들고 사용하는 방법에 대한 자세한 내용은 [Azure에서 Linux VM의 SSH 키 만들기](mac-create-ssh-keys.md)를 참조하세요.

Windows에서 SSH를 사용하는 방법에 대한 자세한 내용은 [Windows에서 SSH 키를 사용하는 방법](ssh-from-windows.md)을 참조하세요.
