---
title: Azure VMware Solution by CloudSimple - vRealize Automation을 위해 프라이빗 클라우드에 vCenter 설정
description: CloudSimple 프라이빗 클라우드에서 VMware vCenter Server를 VMware vRealize Automation의 엔드포인트로 설정하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c005ab9d957ba72e42dcbb7f41f65b97a486df65
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322705"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>프라이빗 클라우드에서 VMware vRealize Automation을 위해 vCenter 설정

CloudSimple 프라이빗 클라우드에서 VMware vCenter Server를 VMware vRealize Automation에 대한 엔드포인트로 설정할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

vCenter Server를 구성하기 전에 다음 작업을 완료합니다.

* 온-프레미스 환경과 프라이빗 클라우드 사이에 [사이트 간 VPN 연결](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)을 구성합니다.
* 프라이빗 클라우드의 DNS 서버에 대한 [온-프레미스 DNS 요청의 DNS 전달을 구성](on-premises-dns-setup.md)합니다.
* [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출하여 다음 표에 나열된 사용 권한이 있는 vRealize Automation IaaS 관리 사용자를 만듭니다.

| 특성 값 | 사용 권한 |
------------ | ------------- |  
| 데이터 저장소 |  공간 할당 <br> 데이터 저장소 찾아보기 |
| 데이터 저장소 클러스터 | 데이터 저장소 클러스터 구성 |
| 폴더 | 폴더 만들기 <br>폴더 삭제 |
| 전역 |  사용자 지정 특성 관리<br>사용자 지정 특성 설정 |
| 네트워크 | 네트워크 할당 |
| 사용 권한 | 권한 수정 |
| 리소스 | 리소스 풀에 VM 할당<br>전원이 꺼진 가상 머신 마이그레이션<br>전원이 켜진 가상 머신 마이그레이션 |
| 가상 머신 인벤토리 |  기존에서 만들기<br>새로 만들기<br>이동<br>제거 | 
| 가상 머신 상호 작용 |  CD 미디어 구성<br>상호 작용 콘솔<br>디바이스 연결<br>전원 끄기<br>전원 켜기<br>다시 설정<br>일시 중지됨<br>도구 설치 | 
| 가상 머신 구성 |  기존 디스크 추가<br>새 디스크 추가<br>추가 또는 제거...<br>디스크 제거<br>고급<br>CPU 수 변경<br>리소스 변경<br>가상 디스크 확장<br>디스크 변경 내용 추적<br>메모리<br>디바이스 설정 수정<br>이름 바꾸기<br>주석 설정(버전 5.0 이상)<br>설정<br>스왑 파일 배치 |
| 프로비전 |  사용자 지정<br>템플릿 복제<br>가상 컴퓨터 복제<br>템플릿 배포<br>사용자 지정 사양 읽기 |
| 가상 컴퓨터 상태 | 스냅샷 만들기<br>스냅샷 제거<br>스냅샷으로 되돌리기 |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>온-프레미스 환경에 vRealize Automation 설치

1. CloudSimple 지원에서 만든 IaaS 관리자로 vRealize Automation IaaS 서버 어플라이언스에 로그인합니다.
2. vRealize Automation 엔드포인트에 대한 vSphere 에이전트를 배포합니다.
    1. https://*vra-url*:5480/installer로 이동합니다. 여기서 *vra-url* 은 vRealize Automation 관리 UI에 액세스하는 데 사용하는 URL입니다.
    2. **IaaS 설치 관리자** 를 클릭하여 설치 관리자를 다운로드합니다.<br>
    설치 관리자 파일의 명명 규칙은 setup_ *vra-url*@5480.exe입니다.
    3. 설치 관리자를 실행합니다. 시작 화면에서 **다음** 을 클릭합니다.
    4. EULA에 동의하고 **다음** 을 클릭합니다.
    5. 로그인 정보를 제공하고 **인증서 수락** 을 클릭한 후 **다음** 을 클릭합니다.
    ![vRA 자격 증명](media/configure-vra-endpoint-login.png)
    6. **사용자 지정 설치** 및 **프록시 에이전트** 를 선택하고 **다음** 을 클릭합니다.
    ![vRA 설치 유형](media/configure-vra-endpoint-install-type.png)
    7. IaaS 서버 로그인 정보를 입력하고 **다음** 을 클릭합니다. Active Directory를 사용하는 경우 **domain\user** 형식으로 사용자 이름을 입력합니다. 그렇지 않으면 **user@domain** 형식을 사용합니다.
    ![vRA 로그인 정보](media/configure-vra-endpoint-account.png)
    8. 프록시 설정에서 **에이전트 유형** 에 **vSphere** 를 입력합니다. 에이전트의 이름을 입력합니다.
    9. **관리자 서비스 호스트** 및 **모델 관리자 웹 서비스 호스트** 필드에 IAAS 서버 FQDN을 입력합니다. **테스트** 를 클릭하여 각 FQDN 값에 대한 연결을 테스트합니다. 테스트가 실패할 경우 IaaS 서버 호스트 이름이 확인되도록 DNS 설정을 수정합니다.
    10. 프라이빗 클라우드의 vCenter Server 엔드포인트에 대한 이름을 입력합니다. 나중에 구성 프로세스에서 사용할 이름을 기록합니다.

        ![vRA 설치 프록시](media/configure-vra-endpoint-proxy.png)

    11. **다음** 을 클릭합니다.
    12. **설치** 를 클릭합니다.

## <a name="configure-the-vsphere-agent"></a>vSphere 에이전트 구성

1. https://*vra-url*/vcac로 이동하여 **ConfigurationAdmin** 으로 로그인합니다.
2. **인프라** > **엔드포인트** > **엔드포인트** 를 선택합니다.
3. **새로 만들기** > **가상** > **vSphere** 를 선택합니다.
4. 이전 절차에서 지정한 vSphere 엔드포인트 이름을 입력합니다.
5. **주소** 에 프라이빗 클라우드 vCenter Server URL을 https://*vcenter-fqdn*/sdk 형식으로 입력합니다. 여기서 *vcenter-fqdn* 은 vCenter Server의 이름입니다.
6. CloudSimple 지원에서 만든 vRealize Automation IaaS 관리 사용자에 대한 자격 증명을 입력합니다.
7. **연결 테스트** 를 클릭하여 사용자 자격 증명의 유효성을 검사합니다. 테스트가 실패하면 URL, 계정 정보 및 [엔드포인트 이름](#verify-the-endpoint-name)을 확인하고 다시 테스트합니다.
8. 테스트가 성공하면 **확인** 을 클릭하여 vSphere 엔드포인트를 만듭니다.
    ![vRA 엔드포인트 구성 액세스](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>엔드포인트 이름을 확인합니다.

올바른 vCenter Server 엔드포인트 이름을 식별하려면 다음을 수행합니다.

1. IaaS 어플라이언스에서 명령 프롬프트를 엽니다.
2. 디렉터리를 C:\Program Files (x86)\VMware\vCAC\Agents\agent-name으로 변경합니다. 여기서 *agent-name* 은 vCenter Server 엔드포인트에 할당한 이름입니다.
3. 다음 명령을 실행합니다.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

다음과 유사하게 출력됩니다. `managementEndpointName` 필드의 값은 엔드포인트 이름입니다.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
