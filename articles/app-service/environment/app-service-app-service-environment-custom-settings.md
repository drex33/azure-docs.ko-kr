---
title: 사용자 지정 설정 구성
description: 전체 Azure App Service 환경에 적용되는 설정을 구성합니다. Azure Resource Manager 템플릿을 사용하여 이 작업을 수행하는 방법을 알아봅니다.
author: madsd
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: madsd
ms.custom: mvc, seodec18
ms.openlocfilehash: e95f8a4434f0ca27c57791edfe8663df9e77bbaa
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130002716"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>App Service Environment에 대한 사용자 지정 구성 설정
## <a name="overview"></a>개요
ASE(App Service Environment)는 단일 고객에게 격리되므로 App Service Environment에만 적용할 수 있는 특정 구성 설정이 있습니다. 이 문서에서는 App Service Environment에 사용 가능한 다양한 App Service Environment 관련 사용자 지정 항목을 설명합니다.

App Service Environment가 없는 경우 [템플릿에서 ASEv1을 만드는 방법](app-service-app-service-environment-create-ilb-ase-resourcemanager.md)을 참조하세요.

새 **clusterSettings** 특성의 배열을 사용하여 App Service Environment 사용자 지정을 저장할 수 있습니다. 이 특성은 *hostingEnvironments* Azure Resource Manager 엔터티의 "속성" 사전에 있습니다.

다음 약식 리소스 관리자 템플릿 코드 조각에는 **clusterSettings** 특성이 표시됩니다.

```json
"resources": [
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/hostingEnvironments",
    "name": ...,
    "location": ...,
    "properties": {
        "clusterSettings": [
            {
                "name": "nameOfCustomSetting",
                "value": "valueOfCustomSetting"
            }
        ],
        "workerPools": [ ...],
        etc...
    }
}
```

**clusterSettings** 특성을 리소스 관리자 템플릿에 포함하여 App Service Environment를 업데이트할 수 있습니다.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Azure 리소스 탐색기를 사용하여 App Service Environment 업데이트
또는 [Azure 리소스 탐색기](https://resources.azure.com)를 사용하여 App Service Environment를 업데이트할 수 있습니다.  

1. 리소스 탐색기에서 App Service Environment 노드(**subscriptions** > **resourceGroups** > **providers** > **Microsoft.Web** > **hostingEnvironments**)로 이동합니다. 그런 다음 업데이트하려는 특정 App Service Environment를 클릭합니다.
2. 오른쪽 창의 위쪽 도구 모음에서 **읽기/쓰기** 를 클릭하여 리소스 탐색기에서 대화형 편집을 허용합니다.  
3. 파란색 **편집** 단추를 클릭하여 리소스 관리자 템플릿을 편집 가능하도록 설정합니다.
4. 오른쪽 창의 아래쪽으로 스크롤합니다. **clusterSettings** 특성은 거의 맨 아래쪽에 있습니다. 여기에서 해당 값을 입력하거나 업데이트할 수 있습니다.
5. **clusterSettings** 특성에서 원하는 구성 값 배열을 입력하거나 복사하여 붙여넣습니다.  
6. 오른쪽 창의 위쪽에 있는 녹색 **PUT** 단추를 클릭하여 App Service Environment에 대한 변경 내용을 커밋합니다.

변경 내용을 제출한 후 변경 내용이 적용되려면 App Service Environment의 프런트 엔드 수에 약 30 분을 곱한 만큼의 시간이 필요합니다.
예를 들어 App Service Environment에 4개의 프런트 엔드가 있는 경우 구성 업데이트를 완료하는 데 약 2시간이 걸립니다. 구성 변경이 롤아웃되는 동안 App Service Environment에서 다른 크기 조정 작업 또는 구성 변경 작업을 수행할 수 없습니다.

## <a name="enable-internal-encryption"></a>내부 암호화 사용

App Service Environment는 시스템 내에서 내부 구성 요소나 통신을 볼 수 없는 블랙 박스 시스템으로 작동합니다. 높은 처리량을 활성화하기 위해 내부 구성 요소 사이에는 기본적으로 암호화가 사용되지 않습니다. 시스템은 트래픽을 모니터링하거나 액세스하는 데 액세스할 수 없기 때문에 보안이 유지됩니다. 엔드투엔드에서 데이터 경로를 완벽하게 암호화해야 하는 규정 준수 요구 사항이 있는 경우에는 clusterSetting을 사용하여 전체 데이터 경로의 암호화를 사용하도록 설정하는 방법이 있습니다.  

```json
"clusterSettings": [
    {
        "name": "InternalEncryption",
        "value": "true"
    }
],
```
InternalEncryption을 true로 설정하면 프런트 엔드 및 작업자 간에 ASE에서 내부 네트워크 트래픽을 암호화하고, 페이지 파일을 암호화하고, 작업자 디스크도 암호화합니다. InternalEncryption clusterSetting을 활성화하면 시스템 성능에 영향을 줄 수 있습니다. InternalEncryption을 활성화하도록 변경하는 경우 변경이 완전히 전파될 때까지 ASE는 불안정한 상태가 됩니다. ASE에 있는 인스턴스의 수에 따라 변경 내용의 전체 전파를 완료하는 데 몇 시간이 걸릴 수 있습니다. ASE를 사용하는 동안에는 InternalEncryption을 사용하도록 설정하지 않는 것이 좋습니다. 현재 사용 중인 ASE에서 InternalEncryption을 사용하도록 설정해야 하는 경우 작업이 완료될 때까지 백업 환경으로 트래픽을 전환하는 것이 좋습니다. 


## <a name="disable-tls-10-and-tls-11"></a>TLS 1.0 및 TLS 1.1 사용 안 함

앱 단위로 TLS 설정을 관리하려는 경우 [TLS 설정 적용](../configure-ssl-bindings.md#enforce-tls-versions) 설명서와 함께 제공되는 지침을 사용할 수 있습니다. 

ASE의 모든 앱에 대해 인바운드 TLS 1.0 및 TLS 1.1 트래픽을 모두 사용 안 함으로 설정하려면 다음 **clusterSettings** 항목을 설정할 수 있습니다.

```json
"clusterSettings": [
    {
        "name": "DisableTls1.0",
        "value": "1"
    }
],
```

설정 이름에는 1.0이 표시되지만 구성되면 TLS 1.0 및 TLS 1.1이 모두 사용되지 않습니다.

## <a name="change-tls-cipher-suite-order"></a>TLS 암호화 그룹 순서 변경
ASE는 기본값에서 암호화 그룹 변경을 지원합니다. 기본 암호화 세트는 다중 테넌트 서비스에서 사용되는 것과 동일한 세트입니다. 암호화 그룹을 변경하면 전체 App Service 배포에 영향을 주므로 단일 테넌트 ASE에서만 가능합니다. ASE에는 두 가지 암호화 그룹(TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 및 TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256)이 필요합니다. 가장 강력하고 최소의 암호화 그룹 세트를 사용하여 ASE를 작동하려면 두 개의 필수 암호화만 사용합니다. 필요한 암호화만 사용하도록 ASE를 구성하려면 아래와 같이 **clusterSettings** 를 수정합니다. 

```json
"clusterSettings": [
    {
        "name": "FrontEndSSLCipherSuiteOrder",
        "value": "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384,TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256"
    }
],
```

> [!WARNING]
> 암호화 그룹에 대해 SChannel에서 이해할 수 없는 잘못된 값이 설정된 경우 서버에 대한 모든 TLS 통신의 작동이 중지될 수 있습니다. 이러한 경우 *clusterSettings* 에서 **FrontEndSSLCipherSuiteOrder** 항목을 제거하고 업데이트된 Resource Manager 템플릿을 제출하여 기본 암호화 제품군 설정으로 되돌려야 합니다.  이 기능을 주의하여 사용하세요.

## <a name="get-started"></a>시작하기
Azure 빠른 시작 Resource Manager 템플릿 사이트에는 [App Service Environment 만들기](https://azure.microsoft.com/resources/templates/web-app-ase-create/)에 대한 기본 정의에 템플릿이 포함되어 있습니다.

<!-- LINKS -->

<!-- IMAGES -->
