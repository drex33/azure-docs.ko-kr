---
title: Service Fabric 관리형 클러스터에 애플리케이션 비밀 배포
description: Azure Service Fabric 애플리케이션 비밀 및 이를 관리형 클러스터에 배포하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: 5b31e06b88d38ab0d5500e69b6733e739e2b3dae
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544738"
---
# <a name="deploy-application-secrets-to-a-service-fabric-managed-cluster"></a>Service Fabric 관리형 클러스터에 애플리케이션 비밀 배포

스토리지 연결 문자열, 암호, 일반 텍스트로 처리하면 안 되는 값 등 모든 민감한 정보를 비밀로 처리할 수 있습니다. Azure Key Vault를 사용하여 Service Fabric 관리형 클러스터의 키와 비밀을 관리하고 이 문서에서 활용하는 것이 좋습니다. 하지만 애플리케이션에서 비밀을 *사용* 하는 것은 클라우드 플랫폼에 구애를 받지 않으므로 그 어디에 호스트된 클러스터에도 애플리케이션을 배포할 수 있습니다.

[서비스 구성 패키지][config-package]를 통해 서비스 구성 설정을 관리하는 방법이 권장됩니다. 구성 패키지는 관리되는 상태 유효성 검사 및 자동 롤백을 사용하여 롤링 업그레이드를 통해 버전이 관리되며 업데이트할 수 있습니다. 이 방법은 전역 서비스 중단 가능성을 줄이기 때문에 기본 설정으로 사용됩니다. 암호화된 비밀도 마찬가지입니다. 서비스 패브릭에는 인증서 암호화를 사용하여 구성 패키지 Settings.xml 파일의 값을 암호화 및 해독하는 기본 기능이 포함되어 있습니다.

다음 다이어그램은 서비스 패브릭 애플리케이션에서 비밀 관리가 이루어지는 기본 흐름을 보여 줍니다.

![비밀 관리 개요][overview]

이 흐름은 주요 네 단계로 구성됩니다.

1. 데이터 암호화 인증서를 가져옵니다.
2. 클러스터에 인증서를 설치합니다.
3. 인증서를 사용하여 애플리케이션을 배포할 때 비밀 값을 암호화하여 서비스의 Settings.xml 구성 파일에 삽입합니다.
4. 동일한 암호화 인증서로 암호를 해독하여 Settings.xml 파일에서 암호화된 값을 읽습니다. 

여기에서 [Azure Key Vault][key-vault-get-started]는 인증서의 안전한 저장 위치와 Azure의 Service Fabric 관리형 클러스터 노드에 설치된 인증서를 가져오는 방법으로 사용됩니다.

애플리케이션 비밀을 구현하는 방법에 대한 예는 [애플리케이션 비밀 관리](service-fabric-application-secret-management.md)를 참조하세요.

또는 [KeyVaultReference](service-fabric-keyvault-references.md)도 지원합니다. Service Fabric KeyVaultReference 지원을 사용하면 Key Vault에 저장된 비밀의 URL을 참조하여 애플리케이션에 쉽게 비밀을 배포할 수 있습니다.

## <a name="create-a-data-encipherment-certificate"></a>데이터 암호화 인증서 만들기
고유한 키 자격 증명 모음을 만들고 인증서를 설정하려면 [Azure CLI, PowerShell, Portal 등][key-vault-certs]을 사용하여 Azure Key Vault의 지침을 따르세요.

>[!NOTE]
> 컴퓨팅 리소스 공급자가 키 자격 증명 모음에서 인증서를 가져와 클러스터 노드에 설치할 수 있도록 키 자격 증명 모음을 [템플릿 배포에 사용 설정](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI)해야 합니다.

## <a name="install-the-certificate-in-your-cluster"></a>클러스터에 인증서 설치
이 인증서는 클러스터의 각 노드에 설치해야 하며 Service Fabric 관리형 클러스터를 사용하면 이를 쉽게 수행할 수 있습니다. 관리형 클러스터 서비스는 버전별 비밀을 노드에 푸시하여 노드에 프라이빗 루트 CA를 설치하는 것처럼 자주 변경되지 않는 비밀을 설치할 수 있습니다. 대부분의 프로덕션 워크로드의 경우 [KeyVault 확장][key-vault-windows]을 사용하는 것이 좋습니다. Key Vault VM 확장은 정적 버전과 비교하여 Azure Key Vault에 저장된 인증서의 자동 새로 고침을 제공합니다.

관리형 클러스터용의 경우 3개의 값(Azure Key Vault에서 2개, 노드의 로컬 저장소 이름에 대해 결정하는 데 1개)이 필요합니다.

매개 변수 
* `Source Vault`: 다음과 같습니다. 
    * /subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1
* `Certificate URL`: 전체 개체 식별자이며 대/소문자를 구분하지 않으며 변경할 수 없습니다.
    * https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}
* `Certificate Store`: 인증서가 배치될 노드의 로컬 인증서 저장소입니다.
    * 노드의 인증서 저장소 이름(예: "MY")

Service Fabric 관리형 클러스터는 노드에 버전별 비밀을 추가하는 데 두 가지 방법을 지원합니다.

1. 초기 클러스터를 만드는 동안에만 포털에서 위의 값을 다음 영역에 삽입합니다.

![포털 비밀 입력][sfmc-secrets]

2. 만드는 중 또는 언제든지 Azure Resource Manager에

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
  "properties": {
    "vmSecrets": [
      {
        "sourceVault": {
          "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
          {
            "certificateStore": "MY",
            "certificateUrl": "https://mykeyvault1.vault.azure.net/certificates/{certificatename}/{secret-version}"
          }
        ]
      }
    ]
  }
}
```

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[key-vault-certs]: ../key-vault/certificates/quick-create-cli.md
[config-package]: service-fabric-application-and-service-manifests.md
[key-vault-windows]: ../virtual-machines/extensions/key-vault-windows.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png
[sfmc-secrets]:./media/how-to-managed-cluster-application-secrets/sfmc-secrets.png
