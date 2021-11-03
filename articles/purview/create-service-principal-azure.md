---
title: Azure에서 서비스 주체 만들기
description: 이 문서에서는 Azure에서 서비스 주체를 만드는 방법을 설명합니다.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d2be616d19ec862a7bf27d55f0a35ed1737fbf86
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103134"
---
# <a name="creating-a-service-principal"></a>서비스 주체 만들기

새 를 만들거나 Azure Active Directory 테넌트에서 기존 서비스 주체를 사용할 수 있습니다.

## <a name="app-registration"></a>앱 등록

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 왼쪽 메뉴에서 **Azure Active Directory** 를 선택합니다.
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-aad.png" alt-text="Azure Active Directory 대한 링크를 보여 주는 스크린샷":::

3. **앱 등록** 및 + 새 등록 선택 **새** 
 :::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-new-reg.png" alt-text="등록에 대한 링크를 보여 주는 스크린샷":::

4. **애플리케이션** 의 이름(서비스 사용자 이름)을 입력합니다.

5. **이 조직 디렉터리의 계정만** 을 선택합니다.

6. **리디렉션 URI** 에 대해 **웹** 을 선택하고 원하는 URL을 입력합니다. 실제 또는 작업 URL일 필요는 없습니다.

7. 그런 다음, **등록** 을 선택합니다.
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-register.png" alt-text="새 앱 등록에 대한 세부 정보를 보여 주는 스크린샷":::
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-new-app.png" alt-text="새로 만든 애플리케이션을 보여 주는 스크린샷":::

## <a name="adding-a-secret-to-the-client-credentials"></a>클라이언트 자격 증명에 비밀 추가

1. **등록할** 앱을 보여 주는 앱 등록 
 :::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-app-select.png" alt-text="스크린샷에서 앱"::: 선택

2. **앱을** 보여 주는 인증서 또는 비밀 추가 
 :::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-add-secret.png" alt-text="스크린샷을"::: 클릭합니다.

3. 클라이언트 비밀 아래에서 **+ 새 클라이언트 암호를** 클릭합니다. 클라이언트 **암호** 메뉴를 보여 
 :::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-new-client-secret.png" alt-text="주는 스크린샷":::

4. **설명을** 제공하고 클라이언트 비밀 세부 정보를 보여 주는 비밀 :::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-secret-desc.png" alt-text="스크린샷에"::: 대한 **만료를** 설정합니다.
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-client-secret.png" alt-text="클라이언트 비밀을 보여 주는 스크린샷":::

5. 개요에서 **클라이언트 자격 증명** 값을 복사합니다. 앱  
 :::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-client-cred.png" alt-text="개요를 보여 주는 스크린샷":::

## <a name="adding-the-secret-to-the-key-vault"></a>키 자격 증명 모음에 비밀 추가

1. **Key Vault로** 이동 주요 자격 
 :::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-key-vault.png" alt-text="증명 모음을 보여 주는 스크린샷":::

2. **비밀 설정**  -->    -->  **+ 생성/가져오기** 선택  
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-generate-secret.png" alt-text="Key Vault의 옵션 스크린샷":::

3. 선택한 **이름을** 입력하고 **값을** 서비스 주체의 **클라이언트 암호로** 입력합니다.  
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-sp-secret.png" alt-text="비밀을 만드는 Key Vault를 보여 주는 스크린샷":::

4. **만들기** 를 선택하여 완료합니다.
