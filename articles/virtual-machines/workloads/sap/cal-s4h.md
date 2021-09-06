---
title: Azure VM에서 SAP S/4HANA 또는 BW/4HANA 배포 | Microsoft Docs
description: Azure VM에서 SAP S/4HANA 또는 BW/4HANA 배포
services: virtual-machines-linux
documentationcenter: ''
author: hobru
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/26/2021
ms.author: hobruche
ms.openlocfilehash: 6e486623db264ec02c0a15abf11366acfcba8d5c
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529668"
---
# <a name="sap-cloud-appliance-library"></a>SAP 클라우드 어플라이언스 라이브러리

[SAP 클라우드 어플라이언스 라이브러리](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8)를 사용하면 사전 구성된 SAP 시스템을 사용하여 데모 환경을 빠르게 만들 수 있습니다. 몇 번의 클릭으로 SAP 시스템을 실행할 수 있습니다. 다음 링크는 Azure에 신속하게 배포할 수 있는 몇 가지 솔루션을 강조합니다. "인스턴스 만들기" 링크를 선택하기만 하면 됩니다. 

S-User 또는 P-User로 인증해야 합니다. [SAP 커뮤니티](https://community.sap.com/)를 통해 P-User를 무료로 만들 수 있습니다.  자세한 내용은 아래를 확인하세요.

| 솔루션 | 링크 |
| -------------- | :--------- | 
| **SAP S/4HANA 2020 FPS02, 전체 활성화 어플라이언스**  2021년 4월 20일 | [인스턴스 만들기](https://cal.sap.com/registration?sguid=d48af08b-e2c6-4409-82f8-e42d5610e918&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|이 어플라이언스에는 SAP S/4HANA 핵심 함수에 대해 사전 활성화된 SAP 모범 사례를 사용하는 SAP S/4HANA 2020(FPS02)과 Service, MDG(Master Data Governance), TM(Transportation Mgmt.), PPM(Portfolio Mgmt.), HCM(Human Capital Management), Analytics, Migration Cockpit에 대한 추가 시나리오가 포함되어 있습니다. 사용자 액세스는 SAP Fiori, SAP GUI, SAP HANA Studio, Windows 원격 데스크톱 또는 전체 관리 액세스를 위한 백 엔드 운영 체제를 통해 발생합니다. |  [세부 정보](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/d48af08b-e2c6-4409-82f8-e42d5610e918) | 
| **SAP S/4HANA 2020 FPS01, 전체 활성화 어플라이언스**  2021년 4월 20일 | [인스턴스 만들기](https://cal.sap.com/registration?sguid=a0b63a18-0fd3-4d88-bbb9-4f02c13dc343&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|이 어플라이언스에는 SAP S/4HANA 핵심 함수에 대해 사전 활성화된 SAP 모범 사례를 사용하는 SAP S/4HANA 2020(FPS01)과 Service, MDG(Master Data Governance), TM(Transportation Mgmt.), PPM(Portfolio Mgmt.), HCM(Human Capital Management), Analytics, Migration Cockpit에 대한 추가 시나리오가 포함되어 있습니다. 사용자 액세스는 SAP Fiori, SAP GUI, SAP HANA Studio, Windows 원격 데스크톱 또는 전체 관리 액세스를 위한 백 엔드 운영 체제를 통해 발생합니다. |  [세부 정보](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/a0b63a18-0fd3-4d88-bbb9-4f02c13dc343) | 
| **SAP S/4HANA 2020 FPS02**  2021년 6월 10일 | [인스턴스 만들기](https://cal.sap.com/registration?sguid=c7cff775-cbf7-4cd1-a907-6eeca95a0946&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
| 이 솔루션은 간편한 프런트 엔드 액세스를 위한 원격 데스크톱을 포함하여 표준 S/4HANA 시스템 설치로 제공됩니다. 클라이언트 100에 사전 구성 및 활성화된 SAP S/4HANA Fiori UI가 포함되어 있으며, SAP S/4HANA 2020 FPS02에 SAP Fiori에 대한 SAP Note 3045635 Rapid Activation에 따라 필수 구성 요소가 활성화되어 있습니다. 추가 정보 링크를 참조하세요. | [세부 정보](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/c7cff775-cbf7-4cd1-a907-6eeca95a0946) | 
| **SAP BW/4HANA 2.0 SP07, BW/4HANA Content 2.0 SP06 포함**   2020년 2월 24일 | [인스턴스 만들기](https://cal.sap.com/registration?sguid=0f2f20f4-d012-4f76-81af-6ff15063db66&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|이 솔루션은 SAP BW/4HANA에 대한 인사이트를 제공합니다. SAP BW/4HANA는 HANA에 최적화된 차세대 Data Warehouse입니다. 기본 BW/4HANA 옵션 외에도 솔루션은 다양한 HANA 최적화 BW/4HANA 콘텐츠와 SAP Data Warehouse Cloud를 통해 하이브리드 시나리오의 다음 단계를 제공합니다. 시스템이 사전 구성되면 시나리오의 직접 구현을 시작할 수 있습니다.| [세부 정보](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/0f2f20f4-d012-4f76-81af-6ff15063db66) | 
| **SAP Yard Logistics 2009 for SAP S/4HANA**  2021년 7월 28일 | [인스턴스 만들기](https://cal.sap.com/registration?sguid=9cdf4f13-73a5-4743-a213-82e0d1a68742&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Yard Logistics 애플리케이션을 통해 보다 효율적이고 수익성 높은 공급망 물류를 실행합니다. 다양한 시각화 및 보고 도구를 사용하여 모든 야드 프로세스에 대한 가시성을 최대화하고 계획된 워크로드를 미리 볼 수 있으므로 리소스 사용을 최적화하고 단일 시스템으로 계획, 실행 및 청구를 지원할 수 있습니다.|  [세부 정보](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/9cdf4f13-73a5-4743-a213-82e0d1a68742) | 
| **SAP S/4HANA 2020 FPS02, 전체 활성화 어플라이언스**  2021년 7월 27일 | [인스턴스 만들기](https://cal.sap.com/registration?sguid=d48af08b-e2c6-4409-82f8-e42d5610e918&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) | 
|이 솔루션은 간편한 프런트 엔드 액세스를 위한 원격 데스크톱을 포함하여 표준 S/4HANA 시스템 설치로 제공됩니다. 클라이언트 100에 사전 구성 및 활성화된 SAP S/4HANA Fiori UI가 포함되어 있으며, SAP S/4HANA 2020 FPS02에 SAP Fiori에 대한 SAP Note 3045635 Rapid Activation에 따라 필수 구성 요소가 활성화되어 있습니다. 추가 정보 링크를 참조하세요.| [세부 정보](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/d48af08b-e2c6-4409-82f8-e42d5610e918) | 
| **SAP Focused Run 3.0 FP01, 미구성**  2021년 7월 21일 | [인스턴스 만들기](https://cal.sap.com/registration?sguid=82bdb96e-3578-41aa-a3e1-a6d9a8335ae1&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Focused Run은 대용량 시스템 및 애플리케이션 모니터링, 경고 및 분석이 필요한 비즈니스를 위해 특별히 설계되었습니다. 확장 가능하고 안전하며 자동화된 하나의 중앙 환경에서 모든 고객을 호스트하려는 서비스 공급자를 위한 강력한 솔루션입니다. 또한 시스템 관리, 사용자 모니터링, 통합 모니터링, 구성 및 보안 분석과 관련된 고객의 고급 요구 사항을 해결합니다.|  [세부 정보](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/82bdb96e-3578-41aa-a3e1-a6d9a8335ae1) | 
| **SAP S/4HANA 2020 FPS01 Utilities 평가판**  2021년 7월 21일 | [인스턴스 만들기](https://cal.sap.com/registration?sguid=68785eeb-a228-4aa8-8273-b4c30775590c&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|이 솔루션을 사용하면 고유한 SAP S/4HANA 2020 Utilities 시스템을 만들고 모든 영역의 전체 관리자 액세스를 포함한 실습 환경을 얻을 수 있습니다. 선택한 안내식 둘러보기를 통해 계측 데이터의 최적화된 처리, 역할 기반 FIORI 사용자 인터페이스를 통한 간소화된 청구 프로세스 및 Customer Engagement의 산업별 고객 서비스 수행을 이해할 수 있습니다.|  [세부 정보](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/68785eeb-a228-4aa8-8273-b4c30775590c)| 
| **SAP Product Lifecycle Costing 4.0 SP3 핫픽스 2**  2021년 8월 1일 | [인스턴스 만들기](https://cal.sap.com/registration?sguid=f2bf191a-7efc-48a2-b8ac-51756eb225bc&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Product Lifecycle Costing은 제품 수명 주기의 초기 단계에서 신규 제품 또는 제품 관련 견적에 대한 비용 및 기타 측면을 계산하고, 비용 동인을 빠르게 식별하며 대안을 손쉽게 시뮬레이션 및 비교할 수 있는 솔루션입니다.|  [세부 정보](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/f2bf191a-7efc-48a2-b8ac-51756eb225bc)| 




---







## <a name="setup-and-get-started-with-sap-cloud-appliance-library"></a>SAP 클라우드 어플라이언스 라이브러리 설정 및 시작

> [!NOTE]
> SAP CAL에 대한 자세한 내용은 [SAP 클라우드 어플라이언스 라이브러리](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) 웹 사이트로 이동하세요. SAP에는 [SAP 클라우드 어플라이언스 라이브러리 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience)에 대한 블로그도 있습니다.

> [!NOTE]
> 2017년 5월 29일 기준으로 SAP CAL을 배포하는 데 덜 선호되는 클래식 배포 모델 외에도 Azure Resource Manager 배포 모델을 사용할 수 있습니다. 새 리소스 관리자 배포 모델을 사용하고 클래식 배포 모델을 무시하는 것이 좋습니다.

## <a name="create-an-account-in-the-sap-cal"></a>SAP CAL에서 계정 만들기
1. SAP CAL에 처음으로 로그인하려면 SAP S-사용자 또는 SAP로 등록된 다른 사용자를 사용합니다. 그런 다음 SAP CAL에서 Azure에 어플라이언스를 배포하는 데 사용되는 SAP CAL 계정을 정의합니다. 계정 정의에서 다음을 수행해야 합니다.

    a. Azure에서 배포 모델을 선택합니다(리소스 관리자 또는 클래식).

    b. Azure 구독을 입력합니다. SAP CAL 계정은 하나의 구독에만 할당될 수 있습니다. 둘 이상의 구독이 필요한 경우 다른 SAP CAL 계정을 만들어야 합니다.

    다. Azure 구독에 배포하도록 SAP CAL 권한을 부여 합니다.

    > [!NOTE]
    > 다음 단계에서는 리소스 관리자 배포를 위한 SAP CAL 계정을 만드는 방법을 보여 줍니다. 클래식 배포 모델에 연결된 SAP CAL 계정이 이미 있는 경우 다음 단계를 따라 새 SAP CAL 계정을 *만들어야* 합니다. 새 SAP CAL 계정을 리소스 관리자 모델에 배포해야 합니다.

2. 새 SAP CAL 계정을 만듭니다. **계정** 페이지는 Azure에 대한 세 가지 선택 항목을 보여 줍니다. 

    a. **Microsoft Azure(클래식)** 는 클래식 배포 모델이며 더 이상 선호하지 않습니다.

    b. **Microsoft Azure** 는 새 리소스 관리자 배포 모델입니다.

    다. **21Vianet에서 운영하는 Windows Azure** 는 클래식 배포 모델을 사용하는 중국의 옵션입니다.

    리소스 관리자 모델을 배포하려면 **Microsoft Azure** 를 선택합니다.

    ![SAP CAL 계정 세부 정보](./media/cal-s4h/s4h-pic-2a.png)

3. Azure Portal에서 찾을 수 있는 Azure **구독 ID** 를 입력합니다.

   ![SAP CAL 계정](./media/cal-s4h/s4h-pic3c.png)

4. 정의한 Azure 구독에 배포하도록 SAP CAL에 권한을 부여하려면 **권한 부여** 를 클릭합니다. 다음 페이지가 브라우저 탭에 나타납니다.

   ![Internet Explorer 클라우드 서비스 로그인](./media/cal-s4h/s4h-pic4c.png)

5. 둘 이상의 사용자가 나열되는 경우 선택한 Azure 구독의 공동 관리자가 되도록 연결된 Microsoft 계정을 선택합니다. 다음 페이지가 브라우저 탭에 나타납니다.

   ![Internet Explorer 클라우드 서비스 확인](./media/cal-s4h/s4h-pic5a.png)

6. **Accept** 를 클릭합니다. 권한 부여에 성공한 경우 SAP CAL 계정 정의가 다시 표시됩니다. 짧은 시간 후 메시지에서 권한 부여 프로세스 성공적으로 수행되었는지 확인합니다.

7. 새로 만든 SAP CAL 계정을 사용자에게 할당하려면 오른쪽의 텍스트 상자에 **사용자 ID** 를 입력하고 **추가** 를 클릭합니다.

   ![사용자 연결에 대한 계정](./media/cal-s4h/s4h-pic8a.png)

8. SAP CAL에 로그인하는 데 사용하는 사용자로 계정을 연결하려면 **검토** 를 클릭합니다. 
 
9. 사용자와 새로 만든 SAP CAL 계정 간의 연결을 만들려면 **만들기** 를 클릭합니다.

   ![SAP CAL 계정 연결에 대한 사용자](./media/cal-s4h/s4h-pic9b.png)

다음을 할 수 있는 SAP CAL 계정을 성공적으로 만들었습니다.

- 리소스 관리자 배포 모델을 사용합니다.
- Azure 구독에 SAP 시스템을 배포합니다.

이제 Azure에서 사용자 구독에 S/4HANA 배포를 시작할 수 있습니다.

> [!NOTE]
> 계속하기 전에 필요한 Azure 코어 할당량이 있는지 확인합니다. SAP CAL의 일부 솔루션은 Azure의 M 시리즈 VM을 사용하여 일부 SAP HANA 기반 솔루션을 배포합니다. Azure 구독에 M 시리즈 코어 할당량이 없을 수 있습니다. 이 경우 Azure 지원에 문의하여 필요한 할당량을 얻어야 합니다.

> [!NOTE]
> SAP CAL에서 Azure에 솔루션을 배포하는 경우 Azure 지역 하나만 선택할 수 있는 경우가 있습니다. SAP CAL에서 제안한 것과 다른 Azure 지역에 배포하려면 SAP에서 CAL 구독을 구입해야 합니다. 또한 CAL 계정이 처음 제안된 곳 외의 Azure 지역에 제공할 수 있도록 SAP로 메시지를 열어야 할 수 있습니다.

## <a name="deploy-a-solution"></a>솔루션 배포

SAP CAL의 **솔루션** 페이지에서 솔루션을 배포해 보겠습니다. SAP CAL에는 배포를 위한 두 개의 시퀀스가 있습니다.

- 배포될 시스템을 정의하도록 하나의 페이지를 사용하는 기본 시퀀스
- VM 크기에 특정 선택 사항을 제공하는 고급 시퀀스 

여기에서 배포에 대한 기본 경로를 설명합니다.

1. **계정 세부 정보** 페이지에서 다음을 수행해야 합니다.

    a. SAP CAL 계정을 선택합니다. (리소스 관리자 배포 모델을 사용하여 배포하도록 연결된 계정을 사용합니다.)

    b. 인스턴스 **이름** 을 입력합니다.

    다. Azure **지역** 을 선택합니다. SAP CAL은 지역을 제안합니다. 다른 Azure 지역이 필요하고 SAP CAL 구독이 없는 경우 SAP로 CAL 구독을 주문해야 합니다.

    d. 8자 또는 9자로 솔루션에 대한 마스터 **암호** 를 입력합니다. 암호는 다양한 구성 요소의 관리자에서 사용됩니다.

   ![SAP CAL 기본 모드: 인스턴스 만들기](./media/cal-s4h/s4h-pic10a.png)

2. **만들기** 를 클릭하고 나타나는 메시지 상자에서 **확인** 을 클릭합니다.

   ![SAP CAL 지원되는 VM 크기](./media/cal-s4h/s4h-pic10b.png)

3. **프라이빗 키** 대화 상자에서 **저장** 을 클릭하여 SAP CAL에 프라이빗 키를 저장합니다. 프라이빗 키에 대해 암호 보호를 사용하려면 **다운로드** 를 클릭합니다. 

   ![SAP CAL 프라이빗 키](./media/cal-s4h/s4h-pic10c.png)

4. SAP CAL **경고** 메시지를 읽고 **확인** 을 클릭합니다.

   ![SAP CAL 경고](./media/cal-s4h/s4h-pic10d.png)

    이제 배포가 수행됩니다. 솔루션의 크기 및 복잡성에 따라 잠시 후에(SAP CAL에서 예측값 제공) 상태가 활성으로 표시되고 사용할 준비가 됩니다.

5. 하나의 리소스 그룹에서 다른 연결된 리소스를 사용하여 수집된 가상 머신을 찾으려면 Azure Portal로 이동합니다. 

   ![새 포털에 배포된 SAP CAL 개체](./media/cal-s4h/sapcaldeplyment_portalview.png)

6. SAP CAL 포털에서 상태는 **활성** 으로 나타납니다. 솔루션에 연결하려면 **연결** 을 클릭합니다. 다양한 구성 요소에 연결하기 위한 다른 옵션이 이 솔루션 내에서 배포됩니다.

   ![SAP CAL 인스턴스](./media/cal-s4h/active_solution.png)

7. 배포된 시스템에 연결하는 옵션 중 하나를 사용하려면 **시작 가이드** 를 클릭합니다. 

   ![인스턴스에 연결](./media/cal-s4h/connect_to_solution.png)

    설명서는 각 연결 방법에 대한 사용자 이름을 지정합니다. 해당 사용자에 대한 암호는 배포 프로세스의 시작 부분에서 정의한 마스터 암호로 설정됩니다. 설명서에서 배포된 시스템에 로그인하는 데 사용할 수 있는 암호와 함께 더 많은 기능 사용자가 나열됩니다. 

    예를 들어 Windows 원격 데스크톱 컴퓨터에 미리 설치되어 있는 SAP GUI를 사용하는 경우 S/4 시스템은 다음과 같이 표시될 수 있습니다.

   ![사전 설치된 SAP GUI의 SM50](./media/cal-s4h/gui_sm50.png)

    또는 DBACockpit을 사용하는 경우 인스턴스는 다음과 같이 표시될 수 있습니다.
 

   ![DBACockpit SAP GUI의 SM50](./media/cal-s4h/dbacockpit.png)

몇 시간 이내에 정상 SAP S/4 어플라이언스가 Azure에 배포됩니다.

SAP CAL 구독을 구입한 경우 SAP는 Azure에서 SAP CAL을 통해 배포를 완벽하게 지원합니다. 지원 큐는 BC-VCM-CAL입니다.







