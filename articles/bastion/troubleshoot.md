---
title: Azure Bastion 문제 해결
description: Azure Bastion 문제를 해결하는 방법을 알아봅니다.
services: bastion
author: charwen
ms.service: bastion
ms.topic: troubleshooting
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 86be88a7e8900ef871af1a2ad2c1c301f7487042
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673904"
---
# <a name="troubleshoot-azure-bastion"></a>Azure Bastion 문제 해결

이 문서에서는 Azure Bastion 문제를 해결하는 방법을 보여 줍니다.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>AzureBastionSubnet에서 NSG를 만들 수 없음

**Q:** Azure Bastion 서브넷에서 NSG를 만들려고 하면 다음과 같은 오류가 발생합니다. ‘네트워크 보안 그룹 \<NSG name\>에 Azure Bastion 서브넷 AzureBastionSubnet에 필요한 규칙이 없습니다.’

**A:** NSG를 만들고 *AzureBastionSubnet* 에 적용하는 경우 NSG에 필요한 규칙을 추가했는지 확인합니다. 필요한 규칙 목록은 [NSG 액세스 및 Azure Bastion 사용](./bastion-nsg.md)을 참조하세요. 규칙을 추가하지 않으면 NSG 만들기/업데이트에 실패합니다.

NSG 규칙의 예는 [빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/azure-bastion-nsg/)에서 참조할 수 있습니다.
자세한 내용은 [Azure Bastion에 대한 NSG 지침](bastion-nsg.md)을 참조하세요.

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Azure Bastion에서 내 SSH 키를 사용할 수 없음

**Q:** 내 SSH 키 파일을 찾아보려고 하면 다음과 같은 오류가 발생합니다. ‘SSH 프라이빗 키는 -----BEGIN RSA PRIVATE KEY-----로 시작하고 -----END RSA PRIVATE KEY-----로 끝나야 합니다.’

**A:** Azure Bastion은 현재 RSA SSH 키만 지원합니다. 대상 VM에 퍼블릭 키가 프로비저닝된 상태에서 SSH용 RSA 프라이빗 키인 키 파일을 찾아야 합니다. 

예를 들어 다음 명령을 사용하여 새 RSA SSH 키를 만들 수 있습니다.

**ssh-keygen -t rsa -b 4096 -C “email@domain.com”**

출력:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Windows 도메인 조인 가상 머신에 로그인할 수 없음

**Q:** 도메인 조인된 Windows 가상 머신에 연결할 수 없습니다.

**A:** Azure Bastion은 사용자 이름-암호 기반의 도메인 로그인에 대해서만 도메인 조인 VM 로그인을 지원합니다. Azure Portal에서 도메인 자격 증명을 지정하는 경우 *domain\username* 형식 대신 UPN(username@domain) 형식을 사용하여 로그인합니다. 이 형식은 도메인 조인 또는 하이브리드 조인(도메인 조인 및 Azure AD 조인) 가상 머신에서 지원됩니다. Azure AD 조인 전용 가상 머신에서는 지원되지 않습니다.

## <a name="unable-to-connect-to-virtual-machine"></a><a name="connectivity"></a> 가상 머신에 연결할 수 없음

**Q:** 내 가상 머신에 연결할 수 없습니다(위의 문제가 발생하지 않음).

**A:** Azure Portal에서 Azure Bastion 리소스의 **연결 문제 해결** 탭(**모니터링** 섹션)으로 이동하여 연결 문제를 해결할 수 있습니다. Network Watcher 연결 문제 해결은 VM(가상 머신)에서 VM, FQDN(정규화된 도메인 이름), URI 또는 IPv4 주소로의 직접 TCP 연결을 확인하는 기능을 제공합니다. 시작하려면 연결을 시작할 원본과 연결할 대상을 선택한 다음 "확인"을 선택합니다. [자세히 알아보기](../network-watcher/network-watcher-connectivity-overview.md).


## <a name="file-transfer-issues"></a><a name="filetransfer"></a>파일 전송 문제

**Q:** Azure Bastion에서 파일 전송이 지원되나요?

**A:** 현재 파일 전송은 지원되지 않습니다. 지원을 추가하기 위한 작업이 진행 중입니다.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Azure Portal의 검은색 화면

**Q:** Azure Bastion을 사용하여 연결하려고 하면 대상 VM에 연결할 수 없고 Azure Portal에 검은색 화면이 표시됩니다.

**A:** 이 문제는 웹 브라우저와 Azure Bastion 간에 네트워크 연결 문제(클라이언트 인터넷 방화벽이 WebSockets 트래픽을 차단하거나 이와 유사한 문제)가 있거나 Azure Bastion과 대상 VM 간에 네트워크 연결 문제가 있는 경우에 발생합니다. 대부분의 경우 AzureBastionSubnet 또는 대상 VM 서브넷에 적용된 NSG가 가상 네트워크의 RDP/SSH 트래픽을 차단하는 것입니다. 클라이언트 인터넷 방화벽에서 WebSockets 트래픽을 허용하고 대상 VM 서브넷에서 NSG를 확인합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Bastion FAQ](bastion-faq.md)를 참조하세요.