---
title: Azure VMware Solution by CloudSimple - CloudSimple 프라이빗 클라우드에 대한 DNS 구성
description: 온-프레미스 워크스테이션에서 CloudSimple 프라이빗 클라우드의 vCenter Server에 액세스하기 위해 DNS 이름 확인을 설정하는 방법을 설명합니다.
author: shortpatti
ms.author: v-patsho
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a14d99a81aaad48c8daf0c0bf0d49b3a7e229f3d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600288"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>온-프레미스 워크스테이션에서 프라이빗 클라우드 vCenter의 이름 확인을 위한 DNS 구성

온-프레미스 워크스테이션에서 CloudSimple 프라이빗 클라우드의 vCenter Server에 액세스하려면 IP 주소뿐만 아니라 호스트 이름을 사용하여 vCenter Server 주소를 지정할 수 있도록 DNS 주소를 구성해야 합니다.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>프라이빗 클라우드에 대한 DNS 서버의 IP 주소를 가져옵니다.

1. [CloudSimple 포털](access-cloudsimple-portal.md)에 로그인합니다.

2. **리소스** > **프라이빗 클라우드** 로 이동하여 연결하려는 프라이빗 클라우드를 선택합니다.

3. 프라이빗 클라우드의 **요약** 페이지에 있는 **기본 정보** 에서 프라이빗 클라우드 DNS 서버의 IP 주소를 복사합니다.

    ![프라이빗 클라우드 DNS 서버](media/private-cloud-dns-server.png)


DNS 구성에 대해 이러한 옵션 중 하나를 사용합니다.

* [DNS 서버에서 *.cloudsimple.io에 대한 영역을 만듭니다.](#create-a-zone-on-a-microsoft-windows-dns-server)
* [온-프레미스 DNS 서버에서 조건 전달자를 만들어 *.cloudsimple.io를 확인합니다.](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>DNS 서버에서 *.cloudsimple.io에 대한 영역을 만듭니다.

스텁 영역으로 영역을 설정하고 이름 확인을 위해 프라이빗 클라우드의 DNS 서버를 가리킬 수 있습니다. 이 섹션에서는 BIND DNS 서버 또는 Microsoft Windows DNS 서버를 사용하는 방법에 대한 정보를 제공합니다.

### <a name="create-a-zone-on-a-bind-dns-server"></a>BIND DNS 서버에 영역 만들기

구성할 특정 파일 및 매개 변수는 개별 DNS 설정에 따라 달라질 수 있습니다.

예를 들어 기본 BIND 서버 구성의 경우 `/etc/named.conf` DNS 서버에서 파일을 편집하고 다음 영역 정보를 추가합니다.

> [!NOTE]
>이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *slave* 에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

```
zone "az.cloudsimple.io"
{
    type stub;
    masters { IP address of DNS servers; };
    file "slaves/cloudsimple.io.db";
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Microsoft Windows DNS 서버에 영역 만들기

1. DNS 서버를 마우스 오른쪽 단추로 클릭하고 **새 영역** 을 선택합니다. 
  
    ![스크린샷은 가져오기 메뉴 옵션을 강조 표시합니다.](media/DNS01.png)
2. **스텁 영역** 을 선택하고 **다음** 을 클릭합니다.

    ![스텁 영역 옵션을 강조 표시하는 스크린샷.](media/DNS02.png)
3. 사용자 환경에 따라 적절한 옵션을 선택하고 **다음** 을 클릭합니다.

    ![영역 데이터 복제 옵션을 표시하는 스크린샷.](media/DNS03.png)
4. **전달 조회 영역** 을 선택하고 **다음** 을 클릭합니다.

    ![전달 조회 영역 옵션을 강조 표시하는 스크린샷.](media/DNS01.png)
5. 영역 이름을 입력하고 **다음** 을 클릭합니다.

    ![영역 이름을 입력할 위치를 보여 주는 스크린샷.](media/DNS05.png)
6. CloudSimple 포털에서 가져온 프라이빗 클라우드에 대한 DNS 서버의 IP 주소를 입력합니다.

    ![새 영역](media/DNS06.png)
7. 필요에 따라 **다음** 을 클릭하여 마법사 설치를 완료합니다.

## <a name="create-a-conditional-forwarder"></a>조건부 전달자 만들기

조건부 전달자는 모든 DNS 이름 확인 요청을 지정된 서버에 전달합니다. 이 설정을 사용하면 *.cloudsimple.io에 대한 모든 요청은 프라이빗 클라우드에 있는 DNS 서버로 전달됩니다. 다음 예에서는 여러 유형의 DNS 서버에서 전달자를 설정하는 방법을 보여 줍니다.

### <a name="create-a-conditional-forwarder-on-a-bind-dns-server"></a>BIND DNS 서버에 조건부 전달자 만들기

구성할 특정 파일 및 매개 변수는 개별 DNS 설정에 따라 달라질 수 있습니다.

예를 들어 기본 BIND 서버 구성의 경우 DNS 서버에서/etc/named.conf 파일을 편집하고 다음과 같은 조건부 전달 정보를 추가합니다.

```
zone "az.cloudsimple.io" {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Microsoft Windows DNS 서버에서 조건부 전달자 만들기

1. DNS 서버에서 DNS 관리자를 엽니다.
2. **조건부 전달자** 를 마우스 오른쪽 단추로 클릭하고 옵션을 선택하여 새 조건부 전달자를 추가합니다.

    ![조건부 전달자 1 Windows DNS](media/DNS08.png)
3. 프라이빗 클라우드에 DNS 도메인 및 DNS 서버의 IP 주소를 입력하고 **확인** 을 클릭합니다.
