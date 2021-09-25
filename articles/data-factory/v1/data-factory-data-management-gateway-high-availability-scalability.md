---
title: Azure Data Factory에서 데이터 관리 게이트웨이를 통한 고가용성
description: 이 문서에서는 노드를 더 많이 추가하여 데이터 관리 게이트웨이를 확장하고, 노드에서 실행할 수 있는 동시 작업 수를 늘려 강화하는 방법을 설명합니다.
author: nabhishek
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 5274c7fe926b1766d0b7767b2b44718a33139b97
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658529"
---
# <a name="data-management-gateway---high-availability-and-scalability-preview"></a>데이터 관리 게이트웨이 - 고가용성 및 확장성(미리 보기)
> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [Data Factory의 자체 호스팅 통합 런타임](../create-self-hosted-integration-runtime.md)을 참조하세요. 


이 문서에서는 데이터 관리 게이트웨이/통합을 사용하여 고가용성 및 확장성 솔루션을 구성하는 방법에 대해 설명합니다.    

> [!NOTE]
> 이 문서에서는 사용자가 이미 Integration Runtime(이전의 데이터 관리 게이트웨이)에 대한 기본 사항을 잘 알고 있다고 가정합니다. 그렇지 않은 경우 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md)를 참조하세요.
> 
> **이 미리 보기 기능은 데이터 관리 게이트웨이 버전 2.12.xxxx.x 이상에서 공식적으로 지원됩니다**. 버전 2.12.xxxx.x 이상을 사용하고 있는지 확인하세요. [여기서](https://www.microsoft.com/download/details.aspx?id=39717) 데이터 관리 게이트웨이 최신 버전을 다운로드합니다.

## <a name="overview"></a>개요
여러 온-프레미스 컴퓨터에 설치된 데이터 관리 게이트웨이를 포털의 단일 논리 게이트웨이와 연결할 수 있습니다. 이러한 컴퓨터를 **노드** 라고 합니다. 논리 게이트웨이와 연결되는 **노드는 4개까지** 갖출 수 있습니다. 논리 게이트웨이에 여러 다중 노드(게이트웨이가 설치된 온-프레미스 컴퓨터)를 사용하는 이점은 다음과 같습니다.  

- 온-프레미스 및 클라우드 데이터 저장소 간의 데이터 이동 성능을 향상시킵니다.  
- 노드 중 하나가 어떤 이유로 중단되는 경우 다른 노드를 사용하여 데이터를 계속 이동할 수 있습니다. 
- 유지 관리를 위해 노드 중 하나를 오프라인으로 전환해야 하는 경우 다른 노드를 사용하여 데이터를 계속 이동할 수 있습니다.

또한 노드에서 실행할 수 있는 **동시 데이터 이동 작업** 수를 구성하여 온-프레미스 및 클라우드 데이터 저장소 간에 데이터를 이동하는 기능을 강화할 수 있습니다. 

Azure Portal을 사용하면 이러한 노드의 상태를 모니터링할 수 있으며, 이는 논리 게이트웨이에서 노드를 추가하거나 제거할지 여부를 결정하는 데 도움이 됩니다. 

## <a name="architecture"></a>Architecture 
다음 다이어그램에서는 데이터 관리 게이트웨이의 확장성 및 가용성 기능에 대한 아키텍처 개요를 제공합니다. 

:::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-high-availability-and-scalability.png" alt-text="데이터 관리 게이트웨이 - 고가용성 및 확장성":::

**논리 게이트웨이** 는 Azure Portal에서 데이터 팩터리에 추가하는 게이트웨이입니다. 이전에는 온-프레미스 Windows 컴퓨터 하나만 논리 게이트웨이가 설치된 데이터 관리 게이트웨이와 연결할 수 있었습니다. 이 온-프레미스 게이트웨이 컴퓨터를 노드라고 합니다. 이제는 최대 **4개의 실제 노드** 를 논리 게이트웨이와 연결할 수 있습니다. 여러 노드가 있는 논리 게이트웨이를 **다중 노드 게이트웨이** 라고 합니다.  

이러한 노드는 모두 **활성** 상태입니다. 이러한 노드 모두에서 데이터 이동 작업을 처리하여 온-프레미스와 클라우드 데이터 저장소 간에 데이터를 이동할 수 있습니다. 노드 중 하나는 디스패처와 작업자 역할을 모두 수행합니다. 그룹의 다른 노드는 작업자 노드입니다. **디스패처** 노드는 클라우드 서비스에서 데이터 이동 태스크/작업을 가져와서 작업자 노드(자체 포함)에 전달합니다. **작업자** 노드는 데이터 이동 작업을 실행하여 온-프레미스 및 클라우드 데이터 저장소 간에 데이터를 이동합니다. 모든 노드는 작업자입니다. 하나의 노드만 디스패처 및 작업자가 모두 될 수 있습니다.    

일반적으로 하나의 노드로 시작하고, 기존 노드의 데이터 이동 부하가 심한 경우 **규모 확장** 하여 더 많은 노드를 추가할 수 있습니다. 또한 노드에서 실행하도록 허용된 동시 작업의 수를 늘림으로써 게이트웨이 노드의 데이터 이동 기능을 **강화** 할 수도 있습니다. 이 기능은 단일 노드 게이트웨이에서도 사용할 수 있습니다(확장성 및 가용성 기능을 사용할 수 없는 경우에도 적용됨). 

여러 노드가 있는 게이트웨이는 모든 노드에서 데이터 저장소 자격 증명을 동기화 상태로 유지합니다. 노드 간 연결 문제가 있는 경우 자격 증명이 동기화되지 않을 수 있습니다. 게이트웨이를 사용하는 온-프레미스 데이터 저장소에 대한 자격 증명을 설정하면 해당 자격 증명이 디스패처/작업자 노드에 저장됩니다. 디스패처 노드는 다른 작업자 노드와 동기화됩니다. 이 프로세스를 **자격 증명 동기화** 라고 합니다. 노드 간 통신 채널은 공용 SSL/TLS 인증서로 **암호화** 될 수 있습니다. 

## <a name="set-up-a-multi-node-gateway"></a>다중 노드 게이트웨이 설정
이 섹션에서는 다음 두 가지 문서를 살펴보았거나 해당 문서의 개념을 잘 알고 있다고 가정합니다. 

- [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) - 게이트웨이에 대한 자세한 개요를 제공합니다.
- [온-프레미스 및 클라우드 저장소 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) - 단일 노드가 있는 게이트웨이를 사용하기 위한 단계별 지침이 포함된 연습을 제공합니다.  

> [!NOTE]
> 온-프레미스 Windows 컴퓨터에 데이터 관리 게이트웨이를 설치하기 전에 [주 문서](data-factory-data-management-gateway.md#prerequisites)에 나열된 필수 조건을 참조하세요.

1. [연습](data-factory-move-data-between-onprem-and-cloud.md#create-gateway)에서 논리 게이트웨이를 만드는 중에 **고가용성 및 확장성** 기능을 사용하도록 설정합니다. 

    :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-enable-high-availability-scalability.png" alt-text="데이터 관리 게이트웨이- 고가용성 및 확장성 사용":::
2. **구성** 페이지에서 **기본 설치** 또는 **수동 설치** 링크를 사용하여 첫 번째 노드(온-프레미스 Windows 컴퓨터)에 게이트웨이를 설치합니다.

    :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-express-manual-setup.png" alt-text="데이터 관리 게이트웨이 - 기본 설치 또는 수동 설치":::

    > [!NOTE]
    > 기본 설치 옵션을 사용하면 암호화 없이 노드 간 통신이 수행됩니다. 노드 이름은 컴퓨터 이름과 동일합니다. 노드 간 통신을 암호화해야 하거나 원하는 노드 이름을 지정하려면 수동 설치를 사용하세요. 노드 이름은 나중에 편집할 수 없습니다.
3. **기본 설치** 를 선택하는 경우
    1. 게이트웨이가 성공적으로 설치되면 다음 메시지가 표시됩니다.

        :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/express-setup-success.png" alt-text="데이터 관리 게이트웨이 - 성공적인 기본 설치":::
    2. [여기서 설명하는 지침](data-factory-data-management-gateway.md#configuration-manager)에 따라 게이트웨이에 대한 데이터 관리 구성 관리자를 시작합니다. 게이트웨이 이름, 노드 이름, 상태 등이 표시됩니다.

        :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png" alt-text="게이트웨이 이름, 노드 이름 및 상태를 볼 수 있는 위치를 보여 주는 스크린샷":::
4. **수동 설치** 를 선택하는 경우 :
    1. Microsoft 다운로드 센터에서 설치 패키지를 다운로드하고 실행하여 게이트웨이를 컴퓨터에 설치합니다.
    2. **구성** 페이지에서 **인증 키** 를 사용하여 게이트웨이를 등록합니다.
    
        :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-authentication-key.png" alt-text="인증 키를 사용할 위치를 보여 주는 스크린샷":::
    3. **새 게이트웨이 노드** 페이지에서 게이트웨이 노드에 사용자 지정 **이름** 을 제공할 수 있습니다. 기본적으로 노드 이름은 컴퓨터 이름과 동일합니다.    

        :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-name.png" alt-text="데이터 관리 게이트웨이 - 이름 지정":::
    4. 다음 페이지에서 **노드 간 통신에 암호화 사용** 여부를 선택할 수 있습니다. 암호화를 사용하지 않으려면(기본값) **건너뛰기** 를 클릭합니다.

        :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-node-encryption.png" alt-text="데이터 관리 게이트웨이 - 암호화 사용":::  
    
        > [!NOTE]
        > 암호화 모드 변경은 논리 게이트웨이에 단일 게이트웨이 노드가 있는 경우에만 지원됩니다. 게이트웨이에 노드가 여러 개 있을 때 암호화 모드를 변경하려면, 하나의 노드를 제외한 모든 노드를 삭제하고 암호화 모드를 변경한 다음 노드를 다시 추가합니다.
        > 
        > TLS/SSL 인증서 사용에 대한 요구 사항 목록은 [TLS/SSL 인증서 요구 사항](#tlsssl-certificate-requirements) 섹션을 참조하세요. 
    5. 게이트웨이가 성공적으로 설치된 후 [구성 관리자 시작]을 클릭합니다.
    
        :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/manual-setup-launch-configuration-manager.png" alt-text="수동 설치 - 구성 관리자 시작":::     
    6. 연결 상태, **게이트웨이 이름** 및 **노드 이름** 을 보여 주는 노드(온-프레미스 Windows 컴퓨터)에서 데이터 관리 게이트웨이 구성 관리자가 표시됩니다.  

        :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-installation-success.png" alt-text="데이터 관리 게이트웨이 - 성공적인 설치":::

        > [!NOTE]
        > Azure VM에서 게이트웨이를 프로비전하는 경우 [이 Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.datafactory/mutiple-vms-with-data-management-gateway)을 사용할 수 있습니다. 이 스크립트에서는 논리 게이트웨이를 만들고, 설치된 데이터 관리 게이트웨이 소프트웨어로 VM을 설정하고, 논리 게이트웨이에 해당 VM을 등록합니다. 
6. Azure Portal에서 **게이트웨이** 페이지를 시작합니다. 
    1. 포털의 데이터 팩터리 홈페이지에서 **연결된 서비스** 를 클릭합니다.
    
        :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-home-page.png" alt-text="연결된 서비스 타일이 강조 표시된 스크린샷":::
    2. **게이트웨이** 를 선택하여 **게이트웨이** 페이지를 표시합니다.
    
        :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/linked-services-gateway.png" alt-text="데이터 팩터리 홈페이지":::
    4. **게이트웨이** 페이지가 표시됩니다.   

        :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/gateway-first-node-portal-view.png" alt-text="단일 노드가 있는 게이트웨이 보기"::: 
7. 도구 모음에서 **노드 추가** 를 클릭하여 논리 게이트웨이에 노드를 추가합니다. 기본 설치를 사용하려는 경우 게이트웨이에 노드로 추가될 온-프레미스 컴퓨터에서 이 단계를 수행합니다. 

    :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png" alt-text="데이터 관리 게이트웨이 - 노드 추가 메뉴":::
8. 이 단계는 첫 번째 노드 설정과 비슷합니다. 수동 설치 옵션을 선택한 경우 구성 관리자 UI를 사용하여 노드 이름을 설정할 수 있습니다. 

    :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/install-second-gateway.png" alt-text="구성 관리자 - 두 번째 게이트웨이 설치":::
9. 게이트웨이가 노드에 성공적으로 설치되면 구성 관리자 도구에서 다음 화면이 표시됩니다.  

    :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/second-gateway-installation-successful.png" alt-text="구성 관리자 - 성공적인 두 번째 게이트웨이 설치":::
10. 포털에서 **게이트웨이** 페이지를 열면 두 개의 게이트웨이 노드가 다음과 같이 표시됩니다. 

    :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png" alt-text="포털에 표시된 두 개의 노드가 있는 게이트웨이":::
11. 게이트웨이 노드를 삭제하려면 도구 모음에서 **노드 삭제** 를 클릭하고, 삭제할 노드를 선택한 다음, 도구 모음에서 **삭제** 를 클릭합니다. 이 작업은 선택한 노드를 그룹에서 삭제합니다. 이 작업은 노드(온-프레미스 Windows 컴퓨터)에서 데이터 관리 게이트웨이 소프트웨어를 제거하는 것이 아닙니다. 게이트웨이를 제거하려면 온-프레미스의 [제어판]에 있는 **프로그램 추가/제거** 를 사용합니다. 노드에서 게이트웨이를 제거하면 포털에서 자동으로 삭제됩니다.   

## <a name="upgrade-an-existing-gateway"></a>기존 게이트웨이 업그레이드
고가용성 및 확장성 기능을 사용하도록 기존 게이트웨이를 업그레이드할 수 있습니다. 이 기능은 2.12.xxxx 버전 이상의 데이터 관리 게이트웨이가 있는 노드에서만 작동합니다. 데이터 관리 게이트웨이 구성 관리자의 **도움말** 탭에서 컴퓨터에 설치된 데이터 관리 게이트웨이의 버전을 확인할 수 있습니다. 

1. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)에서 MSI 설치 패키지를 다운로드하고 실행하여 온-프레미스 컴퓨터의 게이트웨이를 최신 버전으로 업데이트합니다. 자세한 내용은 [설치](data-factory-data-management-gateway.md#installation) 섹션을 참조하세요.  
2. Azure Portal로 이동합니다. 데이터 팩터리에 대한 **Data Factory 페이지** 를 시작합니다. [연결된 서비스] 타일을 클릭하여 **연결된 서비스 페이지** 를 시작합니다. 게이트웨이를 선택하여 **게이트웨이 페이지** 를 시작합니다. 다음 이미지와 같이 **미리 보기 기능** 을 클릭하여 활성화합니다. 

    :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-existing-gateway-enable-high-availability.png" alt-text="데이터 관리 게이트웨이 - 미리 보기 기능 활성화":::   
2. 포털에서 미리 보기 기능이 활성화되면 모든 페이지를 닫습니다. 새 미리 보기 UI(사용자 인터페이스)를 확인하려면 **게이트웨이 페이지** 를 다시 엽니다.
 
    :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview-success.png" alt-text="데이터 관리 게이트웨이 - 성공적인 미리 보기 기능 활성화":::

    :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-preview.png" alt-text="데이터 관리 게이트웨이 - 미리 보기 UI":::

    > [!NOTE]
    > 업그레이드하는 동안 첫 번째 노드의 이름은 컴퓨터의 이름입니다. 
3. 이제 노드를 추가합니다. **게이트웨이** 페이지에서 **노드 추가** 를 클릭합니다.  

    :::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-add-node-menu.png" alt-text="데이터 관리 게이트웨이 - 노드 추가 메뉴":::

    이전 섹션의 지침에 따라 노드를 설정합니다. 

### <a name="installation-best-practices"></a>설치 모범 사례

- 게이트웨이에 대한 호스트 컴퓨터에서 전원 계획을 구성하여 컴퓨터는 최대 절전 모드로 합니다. 호스트 컴퓨터가 최대 절전 모드인 경우 게이트웨이가 데이터 요청에 응답하지 않습니다.
- 게이트웨이에 연결된 인증서를 백업합니다.
- 이상적인 성능을 위해 모든 노드에서 비슷한 구성(권장)을 유지해야 합니다. 
- 고가용성을 보장하려면 노드를 둘 이상 추가합니다.  

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 인증서 요구 사항
Integration Runtime 노드 간의 통신 보안에 사용되는 TLS/SSL 인증서에 대한 요구 사항은 다음과 같습니다.

- 인증서는 공개적으로 신뢰할 수 있는 X509 v3 인증서여야 합니다. 공용(타사) CA(인증 기관)에서 발급한 인증서를 사용하는 것이 좋습니다.
- 각 Integration Runtime 노드는 자격 증명 관리자 애플리케이션을 실행하는 클라이언트 컴퓨터 뿐만 아니라 이 인증서를 신뢰해야 합니다. 
  > [!NOTE]
  > 자격 증명 관리자 애플리케이션은 복사 마법사/Azure Portal에서 자격 증명을 안전하게 설정하는 데 사용됩니다. 또한 이 프로그램은 온-프레미스/프라이빗 데이터 저장소와 동일한 네트워크 내의 어떤 머신에서도 실행할 수 있습니다.
- 와일드 카드 인증서가 지원됩니다. FQDN 이름이 **node1.domain.contoso.com** 인 경우 **_.domain.contoso.com_* 을 인증서의 주체 이름으로 사용할 수 있습니다.
- SAN 인증서는 현재 제한 때문에 주체 대체 이름의 마지막 항목만 사용되고 다른 항목은 무시되므로 권장되지 않습니다. 예: 해당 SAN이 **node1.domain.contoso.com** 및 **node2.domain.contoso.com** 인 SAN 인증서가 있으며 해당 FQDN이 **node2.domain.contoso.com** 인 컴퓨터에만 이 인증서를 사용할 수 있습니다.
- Windows Server 2012 R2에서 지원하는 TLS/SSL 인증서의 키 크기는 모두 지원됩니다.
- CNG 키를 사용하는 인증서는 지원되지 않습니다.

#### <a name="faq-when-would-i-not-enable-this-encryption"></a>FAQ: 언제 이 암호화를 사용하지 않도록 설정하나요?
암호화를 사용하도록 설정하며 인프라에 특정 비용이 추가되므로(공용 인증서 소유) 다음과 같은 경우에는 암호화를 사용하도록 설정하지 않아도 됩니다.
- Integration Runtime이 신뢰할 수 있는 네트워크 또는 IP/Sec 같은 투명한 암호화가 사용되는 네트워크에서 실행되는 경우. 이 채널 통신이 신뢰할 수 있는 네트워크 내로 제한되므로 추가 암호화는 필요하지 않을 수 있습니다.
- Integration Runtime이 프로덕션 환경에서 실행되지 않는 경우. TLS/SSL 인증서 비용을 줄이는 데 도움이 될 수 있습니다.


## <a name="monitor-a-multi-node-gateway"></a>다중 노드 게이트웨이 모니터링
### <a name="multi-node-gateway-monitoring"></a>다중 노드 게이트웨이 모니터링
Azure Portal에서 게이트웨이 노드의 상태와 함께 각 노드의 리소스 사용률(CPU, 메모리, 네트워크(수신/송신) 등)에 대한 스냅샷을 거의 실시간으로 볼 수 있습니다. 

:::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring.png" alt-text="데이터 관리 게이트웨이 - 다중 노드 모니터링":::

**게이트웨이** 페이지에서 **고급 설정** 을 활성화하여 **네트워크**(수신/송신), 게이트웨이 문제를 디버깅하는 데 유용한 **역할 및 자격 증명 상태** 및 성능 튜닝 중의 결과에 따라 수정/변경할 수 있는 **동시 작업**(실행/제한)과 같은 고급 메트릭을 확인할 수 있습니다. 다음 표에서는 **게이트웨이 노드** 목록의 열에 대해 설명합니다.  

모니터링 속성 | Description
:------------------ | :---------- 
이름 | 논리 게이트웨이 및 이 게이트웨이와 연결된 노드의 이름입니다.  
상태 | 논리 게이트웨이 및 게이트웨이 노드의 상태입니다. 예: 온라인/오프라인/제한됨/기타. 해당 상태에 대한 자세한 내용은 [게이트웨이 상태](#gateway-status) 섹션을 참조하세요. 
버전 | 논리 게이트웨이 및 각 게이트웨이 노드의 버전을 표시합니다. 논리 게이트웨이의 버전은 그룹에 있는 대다수 노드의 버전에 따라 결정됩니다. 논리 게이트웨이 설정에 다른 버전의 노드가 있으면 논리 게이트웨이와 버전 번호가 동일한 노드만 제대로 작동합니다. 다른 버전의 노드는 제한된 모드에 있으므로 수동으로 업데이트해야 합니다(자동 업데이트가 실패할 경우에만). 
사용 가능한 메모리 | 게이트웨이 노드에서 사용 가능한 메모리입니다. 이 값은 거의 실시간 스냅샷입니다. 
CPU 사용률 | 게이트웨이 노드의 CPU 사용률입니다. 이 값은 거의 실시간 스냅샷입니다. 
네트워킹(수신/송신) | 게이트웨이 노드의 네트워크 사용률입니다. 이 값은 거의 실시간 스냅샷입니다. 
동시 작업(실행/제한) | 각 노드에서 실행되는 작업 또는 태스크의 수입니다. 이 값은 거의 실시간 스냅샷입니다. 제한은 각 노드의 최대 동시 작업 수를 나타냅니다. 이 값은 컴퓨터 크기에 따라 정의됩니다. CPU/메모리/네트워크가 충분히 활용되지 않지만 작업 시간이 초과되는 고급 시나리오에서 동시 작업 실행을 스케일 업하여 제한을 늘릴 수 있습니다. 이 기능은 단일 노드 게이트웨이에서 확장성 및 가용성 기능을 사용하지 않는 경우에도 사용할 수 있습니다. 자세한 내용은 [크기 조정 고려 사항](#scale-considerations) 섹션을 참조하세요. 
역할 | 두 유형의 역할, 즉 디스패처 및 작업자가 있습니다. 모든 노드는 작업자이며, 이는 모두 작업을 실행하는 데 사용할 수 있음을 의미합니다. 디스패처 노드는 하나만 존재합니다. 이 노드는 클라우드 서비스에서 작업/태스크를 가져와서 다른 작업자 노드(자체 포함)에 전달하는 데 사용됩니다. 

:::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-advanced.png" alt-text="데이터 관리 게이트웨이 - 고급 다중 노드 모니터링":::

### <a name="gateway-status"></a>게이트웨이 상태

다음 표에서는 **게이트웨이 노드** 에 가능한 상태에 대해 설명합니다. 

상태    | 설명/시나리오
:------- | :------------------
온라인 | 노드가 Data Factory 서비스에 연결되어 있습니다.
오프라인 | 노드가 오프라인 상태입니다.
업그레이드 중 | 노드가 자동 업데이트 중입니다.
제한됨 | 연결 문제로 인해 제한되는 상태입니다. 8050 HTTP 포트 문제, 서비스 버스 연결 문제 또는 자격 증명 동기화 문제 때문일 수 있습니다. 
비활성 | 노드의 구성이 다른 주 노드의 구성과 다릅니다.<br/><br/> 다른 노드에 연결할 수 없을 때 노드가 비활성 상태일 수 있습니다. 


다음 표에서는 **논리 게이트웨이** 에 가능한 상태에 대해 설명합니다. 게이트웨이 상태는 게이트웨이 노드의 상태에 따라 달라집니다. 

상태 | 주석
:----- | :-------
등록이 필요합니다. | 이 논리 게이트웨이에 노드가 아직 등록되지 않았습니다.
온라인 | 게이트웨이 노드가 온라인 상태입니다.
오프라인 | 온라인 상태의 노드가 없습니다.
제한됨 | 이 게이트웨이의 모든 노드가 정상 상태가 아닙니다. 이 상태는 일부 노드가 중단되었을 수 있다는 경고입니다. <br/><br/>디스패처/작업자 노드의 자격 증명 동기화 문제 일 수 있습니다. 

### <a name="pipeline-activities-monitoring"></a>파이프라인/활동 모니터링
Azure Portal에서는 세분화된 노드 수준의 세부 정보가 있는 환경을 모니터링하는 파이프라인을 제공합니다. 예를 들어 어떤 활동이 어떤 노드에서 실행되었는지를 보여 줍니다. 이 정보는 네트워크 제한으로 인한 특정 노드의 성능 문제를 이해하는 데 도움이 될 수 있습니다. 

:::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-monitoring-pipelines.png" alt-text="데이터 관리 게이트웨이 - 파이프라인에 대한 다중 노드 모니터링":::

:::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-multi-node-pipeline-details.png" alt-text="데이터 관리 게이트웨이 - 파이프라인 세부 정보":::

## <a name="scale-considerations"></a>크기 조정 고려 사항

### <a name="scale-out"></a>확장
**사용 가능한 메모리가 부족하고****CPU 사용량이 많을** 경우 새 노드를 추가하면 컴퓨터 간에 로드를 확장하는 데 도움이 됩니다. 시간 제한 또는 오프라인 상태의 게이트웨이 노드로 인해 활동이 실패하는 경우 게이트웨이에 노드를 추가하면 도움이 됩니다.
 
### <a name="scale-up"></a>강화
사용 가능한 메모리와 CPU가 잘 사용되지는 않지만 유휴 용량이 0이면 노드에서 실행할 수 있는 동시 작업 수를 늘려 강화할 수 있습니다. 또한 게이트웨이 오버로드로 인해 활동 시간이 초과되는 경우에도 강화할 수 있습니다. 다음 이미지와 같이 노드의 최대 용량을 늘릴 수 있습니다. 먼저 두 배로 늘려 시작하는 것이 좋습니다.  

:::image type="content" source="media/data-factory-data-management-gateway-high-availability-scalability/data-factory-gateway-scale-considerations.png" alt-text="데이터 관리 게이트웨이 - 크기 조정 고려 사항":::


## <a name="known-issuesbreaking-changes"></a>알려진 문제점 및 변경 내용

- 현재 단일 논리 게이트웨이에 최대 4개의 실제 게이트웨이 노드를 갖출 수 있습니다. 성능 상의 이유로 5개 이상의 노드가 필요한 경우 [DMGHelp@microsoft.com](mailto:DMGHelp@microsoft.com)으로 이메일을 보내주세요.
- 다른 논리 게이트웨이의 인증 키로 게이트웨이 노드를 다시 등록하여 현재 논리 게이트웨이에서 전환할 수는 없습니다. 다시 등록하려면 해당 노드에서 게이트웨이를 제거하고 다시 설치한 다음 다른 논리 게이트웨이의 인증 키에 등록해야 합니다. 
- 모든 게이트웨이 노드에 HTTP 프록시가 필요한 경우 diahost.exe.config 및 diawp.exe.config에 프록시를 설정하고, 서버 관리자를 사용하여 모든 노드에 동일한 diahost.exe.config 및 diawip.exe.config가 있는지 확인합니다. 자세한 내용은 [프록시 설정 구성](data-factory-data-management-gateway.md#configure-proxy-server-settings) 섹션을 참조하세요. 
- 게이트웨이 구성 관리자에서 노드 간 통신에 대한 암호화 모드를 변경하려면 포털에서 하나만 제외하고 모든 노드를 삭제합니다. 그런 다음 암호화 모드를 변경한 후 노드를 다시 추가합니다.
- 노드 간 통신 채널을 암호화하도록 선택한 경우 공식 TLS 인증서를 사용합니다. 다른 컴퓨터의 인증 기관 목록에서 동일한 인증서를 신뢰할 수 없는 경우 자체 서명된 인증서로 인해 연결 문제가 발생할 수 있습니다. 
- 노드 버전이 논리 게이트웨이 버전보다 낮은 경우 게이트웨이 노드를 논리 게이트웨이에 등록할 수 없습니다. 낮은 버전의 노드(다운그레이드)를 등록할 수 있도록 포털에서 논리 게이트웨이의 모든 노드를 삭제합니다. 논리 게이트웨이의 모든 노드를 삭제하는 경우 수동으로 새 노드를 설치하고 해당 논리 게이트웨이에 등록합니다. 이 경우 기본 설치는 지원되지 않습니다.
- 클라우드 자격 증명을 사용하는 기존 논리 게이트웨이에는 기본 설치를 사용하여 노드를 설치할 수 없습니다. [설정] 탭의 게이트웨이 구성 관리자에서 자격 증명이 저장된 위치를 확인할 수 있습니다.
- 노드 간 암호화가 사용되는 기존 논리 게이트웨이에는 기본 설치를 사용하여 노드를 설치할 수 없습니다. 암호화 모드 설정에는 수동으로 인증서를 추가하는 것이 관련되므로 기본 설치가 더 이상 선택 사항이 될 수 없습니다. 
- 온-프레미스 환경에서 파일을 복사하는 경우 localhost 또는 로컬 드라이브에서 모든 노드를 통해 액세스할 수 없으므로 \\localhost 또는 C:\files를 더 이상 사용하면 안됩니다. 대신 \\ServerName\files를 사용하여 파일의 위치를 지정합니다.


## <a name="rolling-back-from-the-preview"></a>미리 보기에서 롤백 
미리 보기에서 롤백하려면 하나의 노드만 제외한 모든 노드를 삭제합니다. 어떤 노드를 삭제해도 문제가 되지 않지만 논리 게이트웨이에 하나 이상의 노드가 있도록 합니다. 컴퓨터에서 게이트웨이를 제거하거나 Azure Portal을 사용하여 노드를 삭제할 수 있습니다. Azure Portal의 **Data Factory** 페이지에서 [연결된 서비스]를 클릭하여 **연결된 서비스** 페이지를 시작합니다. 게이트웨이를 선택하여 **게이트웨이** 페이지를 시작합니다. [게이트웨이] 페이지에서 게이트웨이와 연결된 노드를 확인할 수 있습니다. 이 페이지에서는 게이트웨이에서 노드를 삭제할 수 있습니다.
 
삭제한 후에는 동일한 Azure Portal 페이지에서 **미리 보기 기능** 을 클릭하고 미리 보기 기능을 비활성화합니다. 게이트웨이를 하나의 노드 GA(일반 가용성) 게이트웨이로 다시 설정했습니다.


## <a name="next-steps"></a>다음 단계
다음 문서를 검토합니다.
- [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) - 게이트웨이에 대한 자세한 개요를 제공합니다.
- [온-프레미스 및 클라우드 저장소 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) - 단일 노드가 있는 게이트웨이를 사용하기 위한 단계별 지침이 포함된 연습을 제공합니다. 
