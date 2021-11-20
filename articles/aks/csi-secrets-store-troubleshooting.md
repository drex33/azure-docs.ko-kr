---
title: AKS (Azure Kubernetes Service)에서 CSI Driver for 비밀 저장소에 대 한 Azure Key Vault 공급자 문제 해결
description: AKS (Azure Kubernetes Service)를 사용 하 여 암호 저장소 CSI 드라이버에 대 한 Azure Key Vault 공급자를 사용 하는 경우 일반적인 문제를 해결 하는 방법에 대해 알아봅니다.
author: nickomang
ms.service: container-service
ms.topic: troubleshooting
ms.date: 10/18/2021
ms.author: nickoman
ms.openlocfilehash: e23bfdfa19e00ec24abdf785246dbc7073bbcc2a
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132869443"
---
# <a name="troubleshoot-azure-key-vault-provider-for-secrets-store-csi-driver"></a>비밀 저장소 CSI 드라이버에 대 한 Azure Key Vault 공급자 문제 해결

이 문서에는 AKS (Azure Kubernetes Service)의 CSI (Azure Service)에 대 한 Azure Key Vault 공급자 사용에 대 한 일반적인 문제 및 해결을 위한 문제 해결 팁이 나와 있습니다.

## <a name="logging"></a>로깅

공급자 pod Azure Key Vault 공급자 로그를 사용할 수 있습니다. 공급자와 관련 된 문제를 해결 하기 위해 응용 프로그램 pod와 동일한 노드에서 실행 중인 공급자 pod에서 로그를 확인할 수 있습니다. 다음 명령을 실행합니다.

```bash
# find the secrets-store-provider-azure pod running on the same node as your application pod
kubectl get pods -l app=secrets-store-provider-azure -n kube-system -o wide
kubectl logs -l app=secrets-store-provider-azure -n kube-system --since=1h | grep ^E
```

다음 명령을 실행 하 여 비밀 저장소 CSI 드라이버 로그에 액세스할 수도 있습니다.

```bash
# find the secrets-store-csi-driver pod running on the same node as your application pod
kubectl get pods -l app=secrets-store-csi-driver -n kube-system -o wide
kubectl logs -l app=secrets-store-csi-driver -n kube-system --since=1h | grep ^E
```

## <a name="common-issues"></a>일반적인 문제

### <a name="failed-to-get-key-vault-token-nmi-response-failed-with-status-code-404"></a>키 자격 증명 모음 토큰을 가져오지 못함: nmi 응답이 실패 했으며 상태 코드: 404

로그/이벤트의 오류 메시지:

```bash
Warning  FailedMount  74s    kubelet            MountVolume.SetUp failed for volume "secrets-store-inline" : kubernetes.io/csi: mounter.SetupAt failed: rpc error: code = Unknown desc = failed to mount secrets store objects for pod default/test, err: rpc error: code = Unknown desc = failed to mount objects, error: failed to get keyvault client: failed to get key vault token: nmi response failed with status code: 404, err: <nil>
```

설명: *aad에서 id* 의 NMI (Node Managed identity) 구성 요소가 토큰 요청에 대해 오류를 반환 했습니다. 오류에 대 한 자세한 내용을 확인 하 고 오류를 해결 하려면 NMI pod 로그를 확인 하 고 [AZURE AD pod id 문제 해결 가이드][aad-troubleshooting]를 참조 하세요. 

> [!NOTE]
> Azure Active Directory (Azure AD)는 aad- *pod-id* 문자열에서 *aad* 로 축약 됩니다.

### <a name="keyvaultbaseclientgetsecret-failure-sending-request-statuscode0--original-error-context-canceled"></a>keyvault. BaseClient # GetSecret: 요청을 보내는 동안 오류 발생: StatusCode = 0 – 원래 오류: 컨텍스트 취소 됨

로그/이벤트의 오류 메시지:

```bash
E1029 17:37:42.461313       1 server.go:54] failed to process mount request, error: keyvault.BaseClient#GetSecret: Failure sending request: StatusCode=0 -- Original Error: context deadline exceeded
```

설명: 다음 이유 중 하나로 인해 공급자 pod가 key vault 인스턴스에 액세스할 수 없습니다.
- 방화벽 규칙이 공급자의 송신 트래픽을 차단 하 고 있습니다.
- AKS 클러스터에서 구성 된 네트워크 정책이 송신 트래픽을 차단 합니다.
- Pod 공급자는 hostNetwork에서 실행 됩니다. 정책에서이 트래픽을 차단 하거나 노드에 네트워크 jitter 있는 경우 오류가 발생할 수 있습니다. 트래픽을 차단 하도록 구성 된 정책을 확인 하 고 공급자 pod를 allowlist에 저장 합니다. 또한 Azure AD와 키 자격 증명 모음을 노드에서 연결 해야 합니다.

다음을 수행 하 여 호스트 네트워크에서 실행 되는 pod에서 Azure key vault에 대 한 연결을 테스트할 수 있습니다.

1. Pod를 만듭니다.

    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: v1
    kind: Pod
    metadata:
      name: curl
    spec:
      hostNetwork: true
      containers:
      - args:
        - tail
        - -f
        - /dev/null
        image: curlimages/curl:7.75.0
        name: curl
      dnsPolicy: ClusterFirst
      restartPolicy: Always
    EOF
    ```

1. 방금 만든 pod *로 Exec를* 실행 합니다.

    ```bash
    kubectl exec -it curl -- sh
    ```

1. Azure key vault를 사용 하 여 인증:

    ```bash
    curl -X POST 'https://login.microsoftonline.com/<AAD_TENANT_ID>/oauth2/v2.0/token' -d 'grant_type=client_credentials&client_id=<AZURE_CLIENT_ID>&client_secret=<AZURE_CLIENT_SECRET>&scope=https://vault.azure.net/.default'
    ```

1. Azure 키 자격 증명 모음에 이미 생성 된 암호를 가져옵니다.

    ```bash
    curl -X GET 'https://<KEY_VAULT_NAME>.vault.azure.net/secrets/<SECRET_NAME>?api-version=7.2' -H "Authorization: Bearer <ACCESS_TOKEN_ACQUIRED_ABOVE>"
    ```

<!-- LINKS EXTERNAL -->
[aad-troubleshooting]: https://azure.github.io/aad-pod-identity/docs/troubleshooting/
