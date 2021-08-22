---
title: Hadoop에서 SSH 사용 - Azure HDInsight
description: SSH(보안 셸)를 사용하여 HDInsight에 액세스할 수 있습니다. 이 문서에서는 Windows, Linux, Unix 또는 macOS 클라이언트에서 ssh 명령을 사용하여 HDInsight에 연결하는 방법에 대해 설명합니다.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020, devx-track-azurepowershell
ms.date: 02/28/2020
ms.openlocfilehash: 874782828c4d7e63c0e1ad5ea607ddbd09b31881
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112291270"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>SSH를 사용하여 HDInsight(Apache Hadoop)에 연결

[SSH(Secure Shell)](https://en.wikipedia.org/wiki/Secure_Shell)를 사용하여 Azure HDInsight의 Apache Hadoop에 안전하게 연결하는 방법에 대해 알아봅니다. 가상 네트워크를 통해 연결하는 방법에 대한 자세한 내용은 [Azure HDInsight 가상 네트워크 아키텍처](./hdinsight-virtual-network-architecture.md)를 참조하세요. 자세한 내용은 [Azure HDInsight 클러스터에 대한 가상 네트워크 배포 계획](./hdinsight-plan-virtual-network-deployment.md)을 참조하세요.

다음 테이블에서는 SSH를 사용하여 HDInsight에 연결할 때 필요한 주소와 포트 정보를 포함합니다.

| 주소 | 포트 | 다음에 연결... |
| ----- | ----- | ----- |
| `<clustername>-ssh.azurehdinsight.net` | 22 | 기본 헤드 노드 |
| `<clustername>-ssh.azurehdinsight.net` | 23 | 보조 헤드 노드 |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | 에지 노드(에지 노드가 있는 경우 다른 클러스터 형식) |

`<clustername>`을 클러스터의 이름으로 바꿉니다. `<edgenodename>`을 에지 노드의 이름으로 바꿉니다.

클러스터에 에지 노드가 있는 경우 __항상 SSH를 사용하여 에지 노드에 연결__ 하는 것이 좋습니다. 헤드 노드는 Hadoop의 상태에 중요한 서비스를 호스팅합니다. 에지 노드는 배치한 내용을 실행합니다. 에지 노드를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 에지 노드 사용](hdinsight-apps-use-edge-node.md#access-an-edge-node)을 참조하세요.

> [!TIP]
> HDInsight에 처음 연결할 때 호스트의 신뢰성을 설정할 수 없다는 경고가 SSH 클라이언트에 표시될 수도 있습니다. 메시지가 표시되면 ‘예’를 선택하여 SSH 클라이언트의 신뢰할 수 있는 서버 목록에 호스트를 추가합니다.
>
> 이전에 같은 이름의 서버에 연결한 경우 저장된 호스트 키 서버의 호스트 키와 일치하지 않는다는 경고가 나타날 수도 있습니다. 서버 이름에 대한 기존 항목을 제거하는 방법에 대해 SSH 클라이언트에 대한 설명서를 참조하세요.

## <a name="ssh-clients"></a>SSH 클라이언트

Linux, Unix 및 macOS 시스템은 `ssh` 및 `scp` 명령을 제공합니다. `ssh` 클라이언트는 일반적으로 Linux 또는 Unix 기반 시스템에서 원격 명령줄 세션을 만드는 데 사용됩니다. `scp` 클라이언트는 클라이언트와 원격 시스템 간에 파일을 안전하게 복사하는 데 사용됩니다.

Microsoft Windows는 기본적으로 SSH 클라이언트를 설치하지 않습니다. `ssh` 및 `scp` 클라이언트는 Windows에서 다음 패키지를 통해 사용할 수 있습니다.

* [OpenSSH 클라이언트](/windows-server/administration/openssh/openssh_install_firstuse). 이 클라이언트는 Windows 10 Fall Creators Update에 도입된 선택적 기능입니다.

* [Windows 10의 Ubuntu에 있는 Bash](/windows/wsl/about).

* [Azure Cloud Shell](../cloud-shell/quickstart.md). Cloud Shell은 브라우저에서 Bash 환경을 제공합니다.

* [Git](https://git-scm.com/)

또한 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) 및 [MobaXterm](https://mobaxterm.mobatek.net/)과 같은 여러 그래픽 SSH 클라이언트가 있습니다. 이러한 클라이언트를 사용하여 HDInsight에 연결할 수 있지만, 연결하는 프로세스는 `ssh` 유틸리티를 사용하는 것과 다릅니다. 자세한 내용은 사용하는 그래픽 클라이언트의 설명서를 참조하세요.

## <a name="authentication-ssh-keys"></a><a id="sshkey"></a>인증: SSH 키

SSH 키는 [공개 키 암호화](https://en.wikipedia.org/wiki/Public-key_cryptography)를 사용하여 SSH 세션을 인증합니다. SSH 키는 암호보다 더 안전하며, Hadoop 클러스터에 대한 액세스를 안전하게 보호하는 쉬운 방법을 제공합니다.

키를 사용하여 SSH 계정을 보호한 경우 클라이언트는 연결할 때 일치하는 프라이빗 키를 제공해야 합니다.

* 대부분의 클라이언트는 __기본 키__ 를 사용하도록 구성될 수 있습니다. 예를 들어 `ssh` 클라이언트는 Linux 및 Unix 환경의 `~/.ssh/id_rsa`에서 프라이빗 키를 찾습니다.

* __프라이빗 키에 대한 경로__ 를 지정할 수 있습니다. `ssh` 클라이언트에서 `-i` 매개 변수를 사용하여 프라이빗 키에 대한 경로를 지정합니다. 예들 들어 `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`입니다.

* 다른 서버에 사용할 수 있는 __여러 프라이빗 키__ 가 있는 경우 [ssh-agent(https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent) 같은 유틸리티를 고려해 볼 수 있습니다. `ssh-agent` 유틸리티를 사용하여 SSH 세션을 설정할 때 사용할 키를 자동으로 선택할 수 있습니다.

> [!IMPORTANT]
> 암호를 사용하여 프라이빗 키를 보호하는 경우 키를 사용할 때 암호를 입력해야 합니다. `ssh-agent`와 같은 유틸리티는 편의상 암호를 캐시할 수 있습니다.

### <a name="create-an-ssh-key-pair"></a>SSH 키 쌍 만들기

`ssh-keygen` 명령을 사용하여 공용 및 프라이빗 키 파일을 만듭니다. 다음 명령은 HDInsight와 함께 사용할 수 있는 2048비트 RSA 키 쌍을 생성합니다.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```

키 생성 프로세스 중에 정보를 묻는 메시지가 표시됩니다. 예를 들어, 키가 저장되는 위치 또는 암호를 사용할지 여부를 묻습니다. 프로세스가 완료된 후에 공개 키와 프라이빗 키라는 두 개의 파일이 만들어집니다.

* __공개 키__ 는 HDInsight 클러스터를 만드는 데 사용됩니다. 공개 키의 확장명은 `.pub`입니다.

* __프라이빗 키__ 는 HDInsight 클러스터에 클라이언트를 인증하는 데 사용됩니다.

> [!IMPORTANT]
> 암호를 사용하여 키를 보호할 수 있습니다. 암호는 사실상 프라이빗 키의 암호입니다. 누군가 사용자의 프라이빗 키를 얻게 되더라도 키를 사용하기 위해 암호가 있어야 합니다.

### <a name="create-hdinsight-using-the-public-key"></a>공용 키를 사용하여 HDInsight 만들기

| 생성 방법 | 공개 키를 사용하는 방법 |
| ------- | ------- |
| Azure portal | __SSH에 클러스터 로그인 암호 사용__ 선택을 취소하고 SSH 인증 형식으로 __공개 키__ 를 선택합니다. 마지막으로 공개 키 파일을 선택하거나 __SSH 공개 키__ 필드에 파일의 텍스트 내용을 붙여 넣습니다.</br>:::image type="content" source="./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png" alt-text="HDInsight 클러스터 생성의 SSH 공개 키 대화 상자"::: |
| Azure PowerShell | [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) cmdlet의 `-SshPublicKey` 매개 변수를 사용하고 문자열로 공개 키의 내용을 전달합니다.|
| Azure CLI | [`az hdinsight create`](/cli/azure/hdinsight#az_hdinsight_create) 명령의 `--sshPublicKey` 매개 변수를 사용하여 문자열로 공개 키의 내용을 전달합니다. |
| Resource Manager 템플릿 | 템플릿에서 SSH 키를 사용하는 예제는 [SSH 키를 사용하여 Linux에서 HDInsight 배포](https://azure.microsoft.com/resources/templates/hdinsight-linux-ssh-publickey/)를 참조하세요. [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.hdinsight/hdinsight-linux-ssh-publickey/azuredeploy.json) 파일의 `publicKeys` 요소는 클러스터를 만들 때 Azure에 키를 전달하는 데 사용됩니다. |

## <a name="authentication-password"></a>인증: 암호

SSH 계정은 암호를 사용하여 보호될 수 있습니다. SSH를 사용하여 HDInsight에 연결할 경우 암호를 입력하라는 메시지가 표시됩니다.

> [!WARNING]
> Microsoft는 SSH에 암호 인증을 사용하는 것을 권장하지 않습니다. 암호는 추측할 수 있고 무차별 암호 대입 공격에 취약합니다. 대신 [인증하기 위한 SSH 키](#sshkey)를 사용하는 것이 좋습니다.

> [!IMPORTANT]
> SSH 계정 암호는 HDInsight 클러스터가 만들어지고 70일 후에 만료됩니다. 암호가 만료되면 [HDInsight 관리 ](hdinsight-administer-use-portal-linux.md#change-passwords) 문서의 정보를 사용하여 암호를 변경할 수 있습니다.

### <a name="create-hdinsight-using-a-password"></a>암호를 사용하여 HDInsight 만들기

| 생성 방법 | 암호를 지정하는 방법 |
| --------------- | ---------------- |
| Azure portal | 기본적으로 SSH 사용자 계정에는 클러스터 로그인 계정인 동일한 암호가 있습니다. 다른 암호를 사용하려면 __SSH에 클러스터 로그인 암호 사용__ 선택을 취소하고 __SSH 암호__ 필드에 암호를 입력합니다.</br>:::image type="content" source="./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png" alt-text="HDInsight 클러스터 생성의 SSH 암호 대화 상자":::|
| Azure PowerShell | [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) cmdlet의 `--SshCredential` 매개 변수를 사용하고 SSH 사용자 계정 및 암호를 포함하는 `PSCredential` 개체를 전달합니다. |
| Azure CLI | [`az hdinsight create`](/cli/azure/hdinsight#az_hdinsight_create) 명령의 `--ssh-password` 매개 변수를 사용하고 암호 값을 제공합니다. |
| Resource Manager 템플릿 | 템플릿에서 암호를 사용하는 예제는 [SSH 암호를 사용하여 Linux에서 HDInsight 배포](https://azure.microsoft.com/resources/templates/hdinsight-linux-ssh-password/)를 참조하세요. [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.hdinsight/hdinsight-linux-ssh-password/azuredeploy.json) 파일의 `linuxOperatingSystemProfile` 요소는 클러스터를 만들 때 Azure에 SSH 계정 이름 및 암호를 전달하는 데 사용됩니다.|

### <a name="change-the-ssh-password"></a>SSH 암호 변경

SSH 사용자 계정 암호를 변경하는 방법에 대한 내용은 [HDInsight 관리](hdinsight-administer-use-portal-linux.md#change-passwords) 문서의 __암호 변경__ 섹션을 참조하세요.

## <a name="authentication-domain-joined-hdinsight"></a>인증 도메인에 조인된 HDInsight

__도메인 조인 HDInsight 클러스터__ 를 사용하는 경우 SSH 로컬 사용자와 연결한 후 `kinit` 명령을 사용해야 합니다. 이 명령은 도메인 사용자 및 암호를 묻는 메시지를 표시하고 클러스터와 연결된 Azure Active Directory 도메인을 사용하여 세션을 인증합니다.

도메인 계정을 사용하여 ssh가 가능하도록 도메인 조인 노드(예: 헤드 노드, 에지 노드)마다 Kerberos 인증을 사용할 수도 있습니다. 이렇게 하려면 sshd 구성 파일을 편집합니다.

```bash
sudo vi /etc/ssh/sshd_config
```

주석 처리를 제거하고 `KerberosAuthentication`을 `yes`로 변경합니다.

```bash
sudo service sshd restart
```

Kerberos 인증에 성공했는지 확인하려면 `klist` 명령을 사용합니다.

자세한 내용은 [도메인에 조인된 HDInsight 구성](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)을 참조하세요.

## <a name="connect-to-nodes"></a>노드에 연결

헤드 노드 및 에지 노드(있는 경우)는 인터넷을 통해 포트 22 및 23에 액세스할 수 있습니다.

* __헤드 노드__ 에 연결할 경우 기본 헤드 노드에 연결하려면 __22__ 포트, 보조 헤드 노드에 연결하려면 __23__ 포트를 사용하세요. 사용할 정규화된 도메인 이름은 `clustername-ssh.azurehdinsight.net`이며, 여기서 `clustername`은 사용자의 클러스터 이름입니다.

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```

* __에지 노드__ 에 연결할 때 포트 22를 사용합니다. 정규화된 도메인 이름은 `edgenodename.clustername-ssh.azurehdinsight.net`이며, 여기서 `edgenodename`은 에지 노드 연결 시 사용자가 제공한 이름입니다. `clustername`은 클러스터의 이름입니다.

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]
> 이전 예에서는 암호 인증을 사용하고 있고 인증서 인증이 자동으로 발생하고 있다고 가정합니다. 인증에 SSH 키 쌍을 사용하고 인증서가 자동으로 사용되지 않을 경우 `-i` 매개 변수를 사용하여 프라이빗 키를 지정합니다. 예들 들어 `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`입니다.

연결되면 SSH 사용자 이름 및 사용자가 연결된 노드를 표시하도록 프롬프트가 변경됩니다. 예를 들어, `sshuser`로 기본 헤드 노드에 연결된 경우 프롬프트는 `sshuser@<active-headnode-name>:~$`입니다.

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>작업자 및 Apache Zookeeper 노드에 연결

작업자 노드와 Zookeeper 노드는 인터넷에서 직접 액세스할 수 없으며, 클러스터 헤드 노드 또는 에지 노드에서 액세스할 수 있습니다. 다음은 다른 노드에 연결하는 일반적인 단계입니다.

1. SSH를 사용하여 헤드 또는 가장자리 노드에 연결:

    ```bash
    ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net
    ```

2. 헤드 또는 가장자리 노드에 대한 SSH 연결에서는 `ssh` 명령을 사용하여 클러스터의 작업자 노드에 연결합니다.

    ```bash
    ssh sshuser@wn0-myhdi
    ```

    노드 이름 목록을 검색하려면 [Apache Ambari REST API를 사용하여 HDInsight 관리](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) 문서를 참조하세요.

__암호__ 를 사용하여 SSH 계정을 보호하는 경우 연결할 때 해당 암호를 입력합니다.

__SSH 키__ 를 사용하여 SSH 계정을 보호하는 경우 SSH 전달이 클라이언트에서 활성화되어 있는지 확인합니다.

> [!NOTE]
> 클러스터에서 모든 노드에 직접 액세스하는 다른 방법은 Azure Virtual Network에 HDInsight를 설치하는 것입니다. 그런 다음 동일한 가상 네트워크에 원격 컴퓨터를 조인하고 클러스터에 있는 모든 노드에 직접 액세스할 수 있습니다.
>
> 자세한 내용은 [HDInsight에 대한 가상 네트워크 계획](hdinsight-plan-virtual-network-deployment.md)을 참조하세요.

### <a name="configure-ssh-agent-forwarding"></a>SSH 에이전트 전달 구성

> [!IMPORTANT]
> 다음 단계에서는 Linux 또는 UNIX 기반 시스템이며 Windows 10의 Bash를 통해 작업한다고 가정합니다. 이 단계가 시스템에서 작동하지 않는다면 해당 SSH 클라이언트에 대한 설명서를 참조해야 할 수 있습니다.

1. 텍스트 편집기를 사용하여 `~/.ssh/config`를 엽니다. 이 파일이 존재하지 않으면 명령줄에 `touch ~/.ssh/config`를 입력하여 만들 수 있습니다.

2. `config` 파일에 다음 텍스트를 추가합니다.

    ```
    Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
        ForwardAgent yes
    ```

    __호스트__ 정보를 SSH를 사용하여 연결한 노드의 주소로 바꿉니다. 이전 예제에서는 에지 노드를 사용합니다. 이 항목은 지정된 노드에 대한 SSH 에이전트 전달을 구성합니다.

3. 터미널에서 다음 명령을 사용하여 SSH 에이전트 전달을 테스트합니다.

    ```bash
    echo "$SSH_AUTH_SOCK"
    ```

    이 명령은 다음 텍스트와 유사한 정보를 반환합니다.

    ```output
    /tmp/ssh-rfSUL1ldCldQ/agent.1792
    ```

    아무 것도 반환되지 않는 경우 `ssh-agent`가 실행되지 않는 것입니다. 자세한 내용은 [ssh에서 ssh-agent 사용(http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh)의 에이전트 시작 스크립트 정보 또는 SSH 클라이언트 설명서를 참조하세요.

4. **ssh-agent** 가 실행 중인지 확인한 후 다음을 사용하여 에이전트에 SSH 프라이빗 키를 추가합니다.

    ```bash
    ssh-add ~/.ssh/id_rsa
    ```

    프라이빗 키가 다른 파일에 저장된 경우 `~/.ssh/id_rsa`를 해당 파일의 경로로 바꿉니다.

5. SSH를 사용하여 클러스터 에지 노드 또는 헤드 노드에 연결합니다. SSH 명령을 사용하여 작업자 또는 zookeeper 노드에 연결합니다. 전달된 키를 사용하여 연결이 설정됩니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight와 함께 SSH 터널링 사용](hdinsight-linux-ambari-ssh-tunnel.md)
* [HDInsight에서 에지 노드 사용](hdinsight-apps-use-edge-node.md#access-an-edge-node)
* [HDInsight와 함께 SCP 사용](./use-scp.md)
