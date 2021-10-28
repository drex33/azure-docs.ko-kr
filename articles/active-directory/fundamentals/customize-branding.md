---
title: 조직의 로그인 페이지에 브랜딩 추가 - Azure AD
description: Azure Active Directory 로그인 페이지에 조직의 브랜딩을 추가하는 방법에 대한 지침입니다.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 07/03/2021
ms.author: ajburnle
ms.reviewer: kexia
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11da4de1ec4629971f6c0b3836ed38440942ace1
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129231333"
---
# <a name="add-branding-to-your-organizations-azure-active-directory-sign-in-page"></a>조직의 Azure Active Directory 로그인 페이지에 브랜딩 추가
조직의 로고 및 사용자 지정 색 구성표를 사용하여 Azure AD(Azure Active Directory) 로그인 페이지에 일관된 모양과 느낌을 제공합니다. 사용자가 Microsoft 365와 같은 조직의 웹 기반 앱에 로그인할 때 로그인 페이지가 나타납니다. 여기서는 Azure AD를 ID 공급자로 사용합니다.

>[!NOTE]
>사용자 지정 브랜딩을 추가하려면 Azure Active Directory Premium 1, Premium 2 또는 Office 365(Office 365 앱의 경우) 라이선스가 있어야 합니다. 라이선스 및 버전에 대한 자세한 내용은 [Azure AD Premium에 등록](active-directory-get-started-premium.md)을 참조하세요.<br><br>Azure AD Premium 버전은 Azure Active Directory의 전 세계 인스턴스를 사용하여 중국의 고객에게 제공됩니다. Azure AD Premium 버전은 현재 중국의 21Vianet이 운영하는 Azure 서비스에서는 지원되지 않습니다. 자세한 내용은 [Azure Active Directory 포럼](https://feedback.azure.com/forums/169401-azure-active-directory/)을 사용하여 문의하세요.

## <a name="customize-your-azure-ad-sign-in-page"></a>Azure AD 로그인 페이지 사용자 지정
사용자가 조직의 테넌트별 앱(예: `https://outlook.com/contoso.com`)에 로그인하거나 도메인 변수(예: `https://passwordreset.microsoftonline.com/?whr=contoso.com`)를 전달할 때 표시되는 Azure AD 로그인 페이지를 사용자 지정할 수 있습니다.

사용자 지정 브랜딩은 사용자가 www\.office.com과 같은 사이트로 이동할 때 즉시 표시되지 않습니다. 대신, 사용자 지정된 브랜딩이 표시되기 전에 사용자는 로그인해야 합니다. 사용자가 로그인한 후 브랜딩이 나타나는 데 15분 이상이 걸릴 수 있습니다. 

> [!NOTE]
> **모든 브랜딩 요소는 선택 사항이며 변경되지 않은 경우 기본값으로 유지됩니다.** 예를 들어 배경 이미지가 없는 배너 로고를 지정하는 경우 로그인 페이지는 대상 사이트(예: Microsoft 365)의 기본 배경 이미지를 사용하여 로고를 표시합니다.<br><br>또한 로그인 페이지 브랜딩은 개인 Microsoft 계정에 적용되지 않습니다. 사용자 또는 비즈니스 게스트가 개인 Microsoft 계정을 사용하여 로그인하는 경우 로그인 페이지는 조직의 브랜딩을 반영하지 않습니다.

### <a name="to-customize-your-branding"></a>브랜딩을 사용자 지정하려면
1. 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **Azure Active Directory** 를 선택한 다음, **회사 브랜딩** 을 선택하고, **구성** 을 선택합니다.

    ![Contoso - 구성 옵션이 강조 표시된 회사 브랜딩 페이지](media/customize-branding/company-branding-configure-button.png)

3. **회사 브랜딩 구성** 페이지에서 다음 정보 중 일부 또는 모두를 제공합니다.

    >[!IMPORTANT]
    >이 페이지에 추가한 모든 사용자 지정 이미지에는 이미지 크기(픽셀) 및 잠재적으로 파일 크기(KB) 제한이 포함됩니다. 이러한 제한 사항으로 인해 사진 편집기를 사용하여 적절한 크기의 이미지를 만들어야 할 가능성이 높습니다.

    - **일반 설정**

        ![일반 설정을 완료한 회사 브랜딩 구성 페이지](media/customize-branding/configure-company-branding-general-settings.png)

        - **언어** 언어는 기본값으로 자동 설정되고 변경될 수 없습니다.
        
        - **로그인 페이지 배경 이미지** .png 또는 .jpg 이미지 파일을 선택하여 로그인 페이지의 배경으로 표시합니다. 이미지는 브라우저 중앙에 고정되고 표시 가능한 공간의 크기에 맞게 조정됩니다. 크기가 1920x1080픽셀보다 크거나 파일 크기가 300,000바이트보다 큰 이미지는 선택할 수 없습니다.
        
            강한 주체 포커스가 없는 이미지를 사용하는 것이 좋습니다. 예를 들어 불투명 흰색 상자가 화면 가운데에 나타나며, 표시 가능한 공간의 크기에 따라 이미지의 모든 부분을 처리할 수 있습니다.

        - **배너 로고** 사용자가 **My Apps** 포털 페이지에서 사용자 이름을 입력한 후에 .png 또는 .jpg 버전의 로고를 선택하여 로그인 페이지에 표시합니다.
            
            이미지는 60픽셀보다 높거나 280픽셀보다 넓을 수 없으며 파일은 10KB보다 클 수 없습니다. 배경이 로고 배경과 일치하지 않을 수 있으므로 투명 이미지를 사용하는 것이 좋습니다. 이미지 주위에 패딩을 추가하지 않는 것이 좋습니다. 그렇지 않으면 로고가 작게 보입니다. 

        - **사용자 이름 힌트** 사용자 이름을 잊어버린 경우 사용자에게 표시되는 힌트 텍스트를 입력합니다. 이 텍스트는 링크 또는 코드 없는 유니코드여야 하고 64자를 초과할 수 없습니다. 게스트가 앱에 로그인하는 경우 이 힌트를 추가하는 것이 좋습니다.

        - **로그인 페이지 텍스트 및 서식 지정.** 로그인 페이지의 맨 아래에 표시되는 텍스트를 입력합니다. 이 텍스트를 사용하여 지원 센터에 대한 전화 번호 또는 법적 설명과 같은 추가 정보를 주고 받을 수 있습니다. 이 텍스트는 유니코드여야 하며 1024자를 초과할 수 없습니다.

           입력한 로그인 페이지 텍스트를 사용자 지정할 수 있습니다. 새 단락을 시작하려면 Enter 키를 두 번 사용합니다. 굵게, 기울임꼴, 밑줄 또는 클릭 가능한 링크를 포함하도록 텍스트 서식을 변경할 수도 있습니다. 다음 구문을 사용하여 텍스트에 서식을 추가합니다. 

          > 하이퍼링크: `[text](link)` 
          
          > 굵게: `**text**` 또는 `__text__` 
          
          > 기울임꼴: `*text*` 또는 `_text_` 
          
          > 밑줄: `++text++` 
         
          > [!IMPORTANT]
          > 로그인 페이지 텍스트로 추가된 하이퍼링크는 데스크톱 및 모바일 애플리케이션과 같은 네이티브 환경에서 텍스트로 렌더링됩니다.

    - **고급 설정**
            
        ![고급 설정을 완료한 회사 브랜딩 구성 페이지](media/customize-branding/configure-company-branding-advanced-settings.png)   

        - **로그인 페이지 배경색** 낮은 대역폭 연결 상황에서 배경 이미지 대신 표시되는 16진수 색(예: 흰색 #FFFFFF)을 지정합니다. 배너 로고 또는 조직 색의 기본 색을 사용하는 것이 좋습니다.

        - **사각형 로고 이미지** 새 Windows 10 Enterprise 디바이스에 대한 설정 프로세스 중에 사용자에게 표시할 조직 로고의 .png(기본 설정) 또는 .jpg 이미지를 선택합니다. 이 이미지는 Windows 인증에만 사용하고, 다른 Windows 10 환경의 배포 또는 암호 항목 페이지에 [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)을 사용하는 테넌트에만 나타납니다. 경우에 따라 동의 대화 상자에도 표시될 수 있습니다.
        
            이미지는 크기가 240x240픽셀보다 클 수 없고 파일 크기가 10KB 미만이어야 합니다. 배경이 로고 배경과 일치하지 않을 수 있으므로 투명 이미지를 사용하는 것이 좋습니다. 이미지 주위에 패딩을 추가하지 않는 것이 좋습니다. 그렇지 않으면 로고가 작게 보입니다.
    
        - **사각형 로고 이미지, 어두운 테마** 위의 사각형 로고 이미지와 동일합니다. 이 로고 이미지는 기본 제공 환경(OOBE)에서 Windows 10 Azure AD 조인 화면을 사용하는 등 어두운 배경에서 사용될 때 사각형 로고 이미지를 대신합니다.  로고가 흰색, 짙은 파란색 및 검은색 배경에서 잘 보이면 이 이미지를 추가할 필요가 없습니다. 
        
            >[!IMPORTANT]
            > 투명 로고는 사각형 로고 이미지에서 지원됩니다. 그러나 투명 로고에 사용되는 색상표는 사각형 로고 이미지를 사용하는 Microsoft 365 앱과 서비스 내에서 사용되는 배경(예: 흰색, 연한 회색, 진한 회색, 검은색 배경)과 충돌할 수 있습니다. 사각형 이미지 로고가 모든 상황에서 올바르게 렌더링되도록 하려면 단색 배경을 사용해야 할 수 있습니다.
        
        - **로그인 상태를 유지하는 옵션 표시** 명시적으로 로그아웃할 때까지 사용자가 Azure AD에 로그인된 상태를 유지하도록 선택할 수 있습니다. **아니요** 를 선택하면 이 옵션은 숨겨지며, 사용자는 브라우저를 닫았다가 다시 열 때마다 로그인해야 합니다.

            이 기능은 언어별 개체가 아닌 기본 브랜딩 개체에서만 사용할 수 있습니다. 로그인 상태를 유지하는 옵션을 구성하고 문제를 해결하는 방법에 대한 자세한 내용은 [Azure AD 계정에 대한 '로그인 상태 유지' 프롬프트 구성](keep-me-signed-in.md)을 참조하세요.
        
            >[!NOTE]
            >SharePoint Online과 Office 2010의 일부 기능은 로그인 상태를 유지하도록 선택할 수 있는 사용자에 따라 달라집니다. 이 옵션을 **아니요** 로 설정하는 경우 사용자에게 로그인을 요청하는 예상치 못한 메시지가 추가로 표시될 수 있습니다.
   

3. 브랜딩 추가를 완료한 후에 **저장** 을 선택합니다.

    이 프로세스에서 첫 번째 사용자 지정 브랜딩 구성을 생성하면 테넌트에 대한 기본값이 됩니다. 추가 구성이 있는 경우 기본 구성을 선택할 수 있습니다.
    
    >[!IMPORTANT]
    >회사 브랜딩을 테넌트에 더 추가하려면 **Contoso - 회사 브랜딩** 페이지에서 **새 언어** 를 선택해야 합니다. 그러면 **구성 회사 브랜딩** 페이지가 열립니다. 여기서는 위와 동일한 단계를 따르면 됩니다.

## <a name="update-your-custom-branding"></a>사용자 지정 브랜딩 업데이트
사용자 지정 브랜딩을 만든 후에 다시 돌아가서 원하는 대로 변경할 수 있습니다.

### <a name="to-edit-your-custom-branding"></a>사용자 지정 브랜딩을 편집하려면
1. 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **Azure Active Directory** 를 선택한 다음, **회사 브랜딩** 을 선택하고, **구성** 을 선택합니다.

    ![Contoso - 기본 구성이 표시된 회사 브랜딩 페이지](media/customize-branding/company-branding-default-config.png)

3. **회사 브랜딩 구성** 페이지에서 이 문서에 있는 [Azure AD 로그인 페이지 사용자 지정](#customize-your-azure-ad-sign-in-page) 섹션의 설명에 따라 정보를 추가하거나, 제거하거나, 변경합니다.

4. **저장** 을 선택합니다.

   로그인 페이지 브랜딩의 변경 내용을 보려면 최대 1시간이 걸릴 수 있습니다.

## <a name="add-language-specific-company-branding-to-your-directory"></a>디렉터리에 언어별 회사 브랜딩 추가
기본 언어에서 원래 구성의 언어를 변경할 수 없습니다. 그러나 다른 언어의 구성이 필요한 경우 새 구성을 만들 수 있습니다.

### <a name="to-add-a-language-specific-branding-configuration"></a>언어별 브랜딩 구성을 추가하려면

1. 디렉터리에 대한 글로벌 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **Azure Active Directory** 를 선택한 다음, **회사 브랜딩** 을 선택하고, **새 언어** 를 선택합니다.

    ![Contoso - 새 언어 옵션이 강조 표시된 회사 브랜딩 페이지](media/customize-branding/company-branding-new-language.png)

3. **회사 브랜딩 구성** 페이지에서 언어(예: 프랑스어)를 선택한 다음, 이 문서에 있는 [Azure AD 로그인 페이지 사용자 지정](#customize-your-azure-ad-sign-in-page) 섹션의 설명에 따라 번역된 정보를 추가합니다.

4. **저장** 을 선택합니다.

    **Contoso - 회사 브랜딩** 페이지는 새로운 프랑스어 구성을 표시하도록 업데이트됩니다.

    ![Contoso - 새 언어 구성이 표시된 회사 브랜딩 페이지](media/customize-branding/company-branding-french-config.png)

## <a name="add-your-custom-branding-to-pages"></a>페이지에 사용자 지정 브랜딩 추가
`?whr=yourdomainname` 텍스트에서 URL의 끝을 수정하여 페이지에 사용자 지정 브랜딩을 추가합니다. 이 특정 수정은 MFA(Multi-Factor Authentication) 설정 페이지, SSPR(셀프 서비스 암호 재설정) 설정 페이지 및 로그인 페이지를 비롯한 다양한 페이지에서 작동합니다.

애플리케이션에서 브랜딩을 위해 사용자 지정된 URL을 지원하는지 여부는 특정 애플리케이션에 따라 달라지며, 페이지에 사용자 지정 브랜딩을 추가하기 전에 확인해야 합니다.

**예:**

**원본 URL:** https://aka.ms/MFASetup<br>
**사용자 지정 URL:** `https://account.activedirectory.windowsazure.com/proofup.aspx?whr=contoso.com`

**원본 URL:** https://aka.ms/SSPR<br>
**사용자 지정 URL:** `https://passwordreset.microsoftonline.com/?whr=contoso.com`
