---
title: 프로비저닝 에이전트 없이 Linux 이미지 만들기
description: Azure에서 프로비저닝 에이전트 없이 일반화된 Linux 이미지를 만듭니다.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 95802a767521da81cc6fdd63aac8bb3db0628e68
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111531"
---
# <a name="creating-generalized-images-without-a-provisioning-agent"></a>프로비저닝 에이전트 없이 일반화된 이미지 만들기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Microsoft Azure는 Linux VM에 대한 프로비저닝 에이전트를 [walinuxagent](https://github.com/Azure/WALinuxAgent) 또는 [cloud-init](https://github.com/canonical/cloud-init)(권장) 형식으로 제공합니다. 그러나 다음과 같은 프로비저닝 에이전트에 이러한 애플리케이션 중 하나를 사용하지 않으려는 시나리오가 있을 수 있습니다.

- Linux 배포판/버전은 cloud-init/Linux 에이전트를 지원하지 않습니다.
- 호스트 이름과 같은 특수화된 VM 속성을 설정해야 합니다.

> [!NOTE] 
>
> 속성을 설정할 필요가 없거나 어떤 형태의 프로비저닝도 필요하지 않은 경우 특수 이미지를 만드는 것을 고려해야 합니다.

이 문서에서는 프로비저닝 에이전트를 설치하지 않고도 Azure 플랫폼 요구 사항을 충족하고 호스트 이름을 설정하도록 VM 이미지를 설정하는 방법을 보여 줍니다.

## <a name="networking-and-reporting-ready"></a>네트워킹 및 보고 준비 완료

Linux VM이 Azure 구성 요소와 통신하도록 하려면 DNS 확인 및 경로 관리 수행뿐만 아니라 가상 네트워크에서 호스트 IP를 검색하기 위한 DHCP 클라이언트가 필요합니다. 대부분의 배포판은 이러한 유틸리티를 기본 제공합니다. Linux 배포판 공급업체가 Azure에서 테스트한 도구에는 `dhclient`, `network-manager`, `systemd-networkd` 등이 있습니다.

> [!NOTE]
>
> 현재 프로비저닝 에이전트 없이 일반화된 이미지 만들기는 DHCP 사용 VM만 지원합니다.

네트워킹을 설정하고 구성한 후 ‘보고 준비’를 해야 합니다. 그러면 Azure에 VM이 성공적으로 프로비저닝 중임을 알 수 있습니다.

> [!IMPORTANT]
>
> Azure에 준비를 보고하지 못하면 VM이 다시 부팅됩니다.

## <a name="demosample"></a>데모/샘플

이 데모에서는 기존 Marketplace 이미지(이 경우 Debian Buster VM)를 사용하여 Linux 에이전트(walinuxagent)를 제거할 뿐만 아니라 VM이 ‘준비’되었음을 Azure에 보고하는 가장 기본적인 프로세스를 만드는 방법을 보여 줍니다.

### <a name="create-the-resource-group-and-base-vm"></a>리소스 그룹 및 기본 VM 만들기:

```bash
$ az group create --location eastus --name demo1
```

기본 VM 만들기:

```bash
$ az vm create \
    --resource-group demo1 \
    --name demo1 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo1 \
    --image "debian:debian-10:10:latest"
```

### <a name="remove-the-image-provisioning-agent"></a>이미지 프로비저닝 에이전트 제거

VM이 프로비저닝 중이면 SSH에 연결하고 Linux 에이전트를 제거할 수 있습니다.

```bash
$ sudo apt purge -y waagent
$ sudo rm -rf /var/lib/waagent /etc/waagent.conf /var/log/waagent.log
```

### <a name="add-required-code-to-the-vm"></a>VM에 필요한 코드 추가

또한 VM 내부에서 Azure Linux 에이전트를 제거했으므로 준비를 보고할 수 있는 메커니즘을 제공해야 합니다. 

#### <a name="python-script"></a>Python 스크립트

```python
import http.client
import sys
from xml.etree import ElementTree

wireserver_ip = '168.63.129.16'
wireserver_conn = http.client.HTTPConnection(wireserver_ip)

print('Retrieving goal state from the Wireserver')
wireserver_conn.request(
    'GET',
    '/machine?comp=goalstate',
    headers={'x-ms-version': '2012-11-30'}
)

resp = wireserver_conn.getresponse()

if resp.status != 200:
    print('Unable to connect with wireserver')
    sys.exit(1)

wireserver_goalstate = resp.read().decode('utf-8')

xml_el = ElementTree.fromstring(wireserver_goalstate)

container_id = xml_el.findtext('Container/ContainerId')
instance_id = xml_el.findtext('Container/RoleInstanceList/RoleInstance/InstanceId')
incarnation = xml_el.findtext('Incarnation')
print(f'ContainerId: {container_id}')
print(f'InstanceId: {instance_id}')
print(f'Incarnation: {incarnation}')

# Construct the XML response we need to send to Wireserver to report ready.
health = ElementTree.Element('Health')
goalstate_incarnation = ElementTree.SubElement(health, 'GoalStateIncarnation')
goalstate_incarnation.text = incarnation
container = ElementTree.SubElement(health, 'Container')
container_id_el = ElementTree.SubElement(container, 'ContainerId')
container_id_el.text = container_id
role_instance_list = ElementTree.SubElement(container, 'RoleInstanceList')
role = ElementTree.SubElement(role_instance_list, 'Role')
instance_id_el = ElementTree.SubElement(role, 'InstanceId')
instance_id_el.text = instance_id
health_second = ElementTree.SubElement(role, 'Health')
state = ElementTree.SubElement(health_second, 'State')
state.text = 'Ready'

out_xml = ElementTree.tostring(
    health,
    encoding='unicode',
    method='xml'
)
print('Sending the following data to Wireserver:')
print(out_xml)

wireserver_conn.request(
    'POST',
    '/machine?comp=health',
    headers={
        'x-ms-version': '2012-11-30',
        'Content-Type': 'text/xml;charset=utf-8',
        'x-ms-agent-name': 'custom-provisioning'
    },
    body=out_xml
)

resp = wireserver_conn.getresponse()
print(f'Response: {resp.status} {resp.reason}')

wireserver_conn.close()
```

#### <a name="generic-steps-without-using-python"></a>일반 단계(Python을 사용하지 않음)

VM에 Python이 설치되어 있지 않거나 사용할 수 없는 경우 다음 단계를 사용하 여 위의 스크립트 논리를 프로그래밍 방식으로 재현할 수 있습니다.

1. WireServer: `curl -X GET -H 'x-ms-version: 2012-11-30' http://168.63.129.16/machine?comp=goalstate`에서 응답을 구문 분석하여 `ContainerId`, `InstanceId` 및 `Incarnation`을 검색합니다.

2. 위 단계에서 구문 분석된 `ContainerId`, `InstanceId` 및 `Incarnation`을 삽입하여 다음 XML 데이터를 생성합니다.
   ```xml
   <Health>
     <GoalStateIncarnation>INCARNATION</GoalStateIncarnation>
     <Container>
       <ContainerId>CONTAINER_ID</ContainerId>
       <RoleInstanceList>
         <Role>
           <InstanceId>INSTANCE_ID</InstanceId>
           <Health>
             <State>Ready</State>
           </Health>
         </Role>
       </RoleInstanceList>
     </Container>
   </Health>
   ```

3. WireServer: `curl -X POST -H 'x-ms-version: 2012-11-30' -H "x-ms-agent-name: WALinuxAgent" -H "Content-Type: text/xml;charset=utf-8" -d "$REPORT_READY_XML" http://168.63.129.16/machine?comp=health`에 이 데이터 게시

### <a name="automating-running-the-code-at-first-boot"></a>처음 부팅할 때 코드 실행 자동화

이 데모는 최신 Linux 배포판에서 가장 일반적인 init 시스템인 systemd를 사용합니다. 따라서 이 보고 준비 메커니즘이 적시에 실행되도록 하는 가장 쉽고 기본적인 방법은 systemd 서비스 단위를 만드는 것입니다. 다음 단위 파일을 `/etc/systemd/system`에 추가할 수 있습니다(이 예제는 단위 파일 이름을 `azure-provisioning.service`로 지정).

```bash
[Unit]
Description=Azure Provisioning

[Service]
Type=oneshot
ExecStart=/usr/bin/python3 /usr/local/azure-provisioning.py
ExecStart=/bin/bash -c "hostnamectl set-hostname $(curl \
    -H 'metadata: true' \
    'http://169.254.169.254/metadata/instance/compute/name?api-version=2019-06-01&format=text')"
ExecStart=/usr/bin/systemctl disable azure-provisioning.service

[Install]
WantedBy=multi-user.target
```

이 systemd 서비스는 기본 프로비저닝을 위한 세 가지 작업을 수행합니다.

1. Azure에 준비를 보고합니다(성공적으로 완료되었음을 나타냄).
1. [Azure IMDS(Azure Instance Metadata Service)](./instance-metadata-service.md)에서 이 데이터를 가져와 사용자가 제공한 VM 이름에 따라 VM 이름을 변경합니다. **참고** IMDS는 또한 SSH 공개 키와 같은 다른 [인스턴스 메타데이터](./instance-metadata-service.md#access-azure-instance-metadata-service)를 제공하므로 호스트 이름보다 더 많은 항목을 설정할 수 있습니다.
1. 처음 부팅할 때만 실행되며 이후에 재부팅할 때는 실행되지 않도록 자체 비활성화합니다.

파일 시스템의 단위를 사용해 다음을 실행하여 활성화합니다.

```bash
$ sudo systemctl enable azure-provisioning.service
```

이제 VM을 일반화할 준비가 되었고, VM에서 이미지를 만들 수 있습니다. 

#### <a name="completing-the-preparation-of-the-image"></a>이미지 준비 완료

개발 컴퓨터로 돌아가서 다음을 실행하여 기본 VM에서 이미지를 만들 준비를 합니다.

```bash
$ az vm deallocate --resource-group demo1 --name demo1
$ az vm generalize --resource-group demo1 --name demo1
```

그리고 이 VM에서 이미지를 만듭니다.

```bash
$ az image create \
    --resource-group demo1 \
    --source demo1 \
    --location eastus \
    --name demo1img
```

이제 이미지에서 새 VM(또는 여러 VM)을 만들 준비가 되었습니다.

```bash
$ IMAGE_ID=$(az image show -g demo1 -n demo1img --query id -o tsv)
$ az vm create \
    --resource-group demo12 \
    --name demo12 \
    --location eastus \
    --ssh-key-value <ssh_pub_key_path> \
    --public-ip-address-dns-name demo12 \
    --image "$IMAGE_ID" 
    --enable-agent false
```

> [!NOTE]
>
> 이미지에서 생성될 이 VM에 walinuxagent이 존재하지 않기 때문에 `--enable-agent`(을)를 `false`(으)로 설정하는 것이 중요합니다.

이 VM은 성공적으로 프로비저닝되어야 합니다. 새로 프로비저닝된 VM에 로그인하면 보고 준비가 완료된 systemd 서비스의 출력을 볼 수 있습니다.

```bash
$ sudo journalctl -u azure-provisioning.service
-- Logs begin at Thu 2020-06-11 20:28:45 UTC, end at Thu 2020-06-11 20:31:24 UTC. --
Jun 11 20:28:49 thstringnopa systemd[1]: Starting Azure Provisioning...
Jun 11 20:28:54 thstringnopa python3[320]: Retrieving goal state from the Wireserver
Jun 11 20:28:54 thstringnopa python3[320]: ContainerId: 7b324f53-983a-43bc-b919-1775d6077608
Jun 11 20:28:54 thstringnopa python3[320]: InstanceId: fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2
Jun 11 20:28:54 thstringnopa python3[320]: Sending the following data to Wireserver:
Jun 11 20:28:54 thstringnopa python3[320]: <Health><GoalStateIncarnation>1</GoalStateIncarnation><Container><ContainerId>7b324f53-983a-43bc-b919-1775d6077608</ContainerId><RoleInstanceList><Role><InstanceId>fbb84507-46cd-4f4e-bd78-a2edaa9d059b._thstringnopa2</InstanceId><Health><State>Ready</State></Health></Role></RoleInstanceList></Container></Health>
Jun 11 20:28:54 thstringnopa python3[320]: Response: 200 OK
Jun 11 20:28:56 thstringnopa bash[472]:   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Jun 11 20:28:56 thstringnopa bash[472]:                                  Dload  Upload   Total   Spent    Left  Speed
Jun 11 20:28:56 thstringnopa bash[472]: [158B blob data]
Jun 11 20:28:56 thstringnopa2 systemctl[475]: Removed /etc/systemd/system/multi-user.target.wants/azure-provisioning.service.
Jun 11 20:28:56 thstringnopa2 systemd[1]: azure-provisioning.service: Succeeded.
Jun 11 20:28:56 thstringnopa2 systemd[1]: Started Azure Provisioning.
```

## <a name="support"></a>지원

자체 프로비저닝 코드/에이전트를 구현하고 이 코드에 대한 지원을 소유한 경우 Microsoft 지원에서는 사용할 수 없는 프로비저닝 인터페이스와 관련된 문제만 조사합니다. 이 영역에서 지속적으로 개선 및 변경 작업을 수행하고 있으므로 API 변경 사항을 프로비전하기 위해 cloud-init 및 Azure Linux 에이전트에서 변경 사항을 모니터링해야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Linux 프로비저닝](provisioning.md)을 참조하세요.
