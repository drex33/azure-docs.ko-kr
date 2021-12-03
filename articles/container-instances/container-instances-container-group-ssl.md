---
title: 사이드카 컨테이너로 TLS 사용
description: 사이드카 컨테이너에서 Nginx를 실행하여 Azure Container Instances에서 실행되는 컨테이너 그룹에 대한 SSL 또는 TLS 엔드포인트를 만듭니다.
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 5d5e609427c281b0db601084e50b09bce22a1593
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133519314"
---
# <a name="enable-a-tls-endpoint-in-a-sidecar-container"></a>사이드카 컨테이너에서 TLS 엔드포인트 사용

이 문서는 TLS/SSL 공급자를 실행하는 애플리케이션 컨테이너 및 사이드카 컨테이너를 사용하여 [컨테이너 그룹](container-instances-container-groups.md)을 만드는 방법을 보여 줍니다. 별도의 TLS 엔드포인트를 사용하여 컨테이너 그룹을 설정하여 애플리케이션 코드를 변경하지 않고 애플리케이션에 대한 TLS 연결을 사용하도록 설정합니다.

두 개의 컨테이너로 구성된 예제 컨테이너 그룹을 설정합니다.
* 퍼블릭 Microsoft [aci-helloworld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) 이미지를 사용하여 간단한 웹앱을 실행하는 애플리케이션 컨테이너입니다.
* TLS를 사용하도록 구성된 퍼블릭 [Nginx](https://hub.docker.com/_/nginx) 이미지를 실행하는 사이드카 컨테이너입니다.

이 예제에서 컨테이너 그룹은 공용 IP 주소를 사용하여 Nginx 포트 443만 노출합니다. Nginx는 포트 80에서 내부적으로 수신 대기하는 도우미 웹앱에 대한 HTTPS 요청을 라우팅합니다. 다른 포트에서 수신 대기 하는 컨테이너 응용 프로그램에 대 한 예제를 적용할 수 있습니다.

컨테이너 그룹에서 TLS를 사용하도록 설정하는 다른 방법은 [다음 단계](#next-steps)를 참조하세요.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서대로 하려면 Azure CLI 버전 2.0.55 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

## <a name="create-a-self-signed-certificate"></a>자체 서명된 인증서 만들기

Nginx를 TLS 공급자로 설정하려면 TLS/SSL 인증서가 필요합니다. 이 문서는 자체 서명된 TLS/SSL 인증서를 만들고 설정하는 방법을 보여 줍니다. 프로덕션 시나리오의 경우 인증 기관에서 인증서를 받아야 합니다.

자체 서명된 TLS/SSL 인증서를 만들려면 Azure Cloud Shell 및 많은 Linux 배포판에서 사용할 수 있는 [OpenSSL](https://www.openssl.org/) 도구를 사용하거나 운영 체제에서 동급 클라이언트 도구를 사용합니다.

먼저 로컬 작업 디렉터리에서 인증서 요청(.csr 파일)을 만듭니다.

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

프롬프트에 따라 다음 ID 정보를 추가합니다. 일반 이름에 인증서와 연결된 호스트 이름을 입력합니다. 암호를 입력하라는 메시지가 표시되면 입력하지 않고 Enter 키를 눌러 암호 추가를 건너뜁니다.

다음 명령을 실행하여 인증서 요청에서 자체 서명된 인증서(.crt 파일)를 만듭니다. 예를 들면 다음과 같습니다.

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

이제 디렉터리에 인증서 요청(`ssl.csr`), 프라이빗 키(`ssl.key`) 및 자체 서명된 인증서(`ssl.crt`)라는 세 개의 파일이 표시됩니다. 이후 단계에서는 `ssl.key`과 `ssl.crt`를 사용합니다.

## <a name="configure-nginx-to-use-tls"></a>TLS를 사용하도록 Nginx 구성

### <a name="create-nginx-configuration-file"></a>Nginx 구성 파일 만들기

이 섹션에서는 TLS를 사용하기 위한 Nginx 구성 파일을 만듭니다. 먼저 다음 텍스트를 `nginx.conf`이라는 새 파일에 복사합니다. Azure Cloud Shell에서 Visual Studio Code를 사용하여 작업 디렉터리에 파일을 만들 수 있습니다.

```console
code nginx.conf
```

`location`에서 `proxy_pass`를 앱에 대한 올바른 포트로 설정해야 합니다. 이 예제에서는 `aci-helloworld` 컨테이너에 대해 80 포트를 설정합니다.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize TLS/SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive TLS/SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80

            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>비밀 및 구성 파일 Base64 인코딩

Nginx 구성 파일, TLS/SSL 인증서 및 TLS 키를 Base64로 인코딩합니다. 다음 섹션에서는 컨테이너 그룹을 배포하는 데 사용되는 YAML 파일에 인코딩된 콘텐츠를 입력합니다.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>컨테이너 그룹 배포

이제 [YAML 파일](container-instances-multi-container-yaml.md)의 컨테이너 구성을 지정하여 컨테이너 그룹을 배포합니다.

### <a name="create-yaml-file"></a>YAML 파일 만들기

다음 YAML을 `deploy-aci.yaml`이라는 새 파일에 복사합니다. Azure Cloud Shell에서 Visual Studio Code를 사용하여 작업 디렉터리에 파일을 만들 수 있습니다.

```console
code deploy-aci.yaml
```

`secret`에 표시된 base64 인코딩 파일의 콘텐츠를 입력합니다. 예를 들어 각 base64 인코딩 파일을 `cat`하여 해당 콘텐츠를 봅니다. 배포하는 동안 이러한 파일은 컨테이너 그룹의 [비밀 볼륨](container-instances-volume-secret.md)에 추가됩니다. 이 예제에서는 비밀 볼륨이 Nginx 컨테이너에 탑재됩니다.

```yaml
api-version: 2019-12-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>컨테이너 그룹 배포

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

[az container create](/cli/azure/container#az_container_create) 명령을 사용하여 컨테이너 그룹을 배포하고, YAML 파일을 인수로 전달합니다.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>배포 상태 확인

배포 상태를 확인하려면 다음 [az container show](/cli/azure/container#az_container_show) 명령을 사용합니다.

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

성공적으로 배포하려면 출력은 다음과 유사합니다.

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-tls-connection"></a>TLS 연결 확인

브라우저를 사용하여 컨테이너 그룹의 공용 IP 주소로 이동합니다. 이 예제에 표시된 IP 주소는 `52.157.22.76`이므로 URL은 **https://52.157.22.76** 입니다. Nginx 서버 구성으로 인해 실행 중인 애플리케이션을 보려면 HTTPS를 사용해야 합니다. HTTP를 통한 연결 시도가 실패합니다.

![Azure 컨테이너 인스턴스에서 실행되는 애플리케이션을 보여주는 브라우저 스크린샷](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> 이 예제에서는 인증 기관의 인증서가 아니라 자체 서명된 인증서를 사용하기 때문에 HTTPS를 통해 사이트에 연결할 때 브라우저에서 보안 경고를 표시합니다. 페이지를 계속 진행하려면 경고를 수락하거나 브라우저 또는 인증서 설정을 조정해야 할 수 있습니다. 이는 정상적인 동작입니다.

>

## <a name="next-steps"></a>다음 단계

이 문서에서는 Nginx 컨테이너를 설정하여 컨테이너 그룹에서 실행되는 웹앱에 대한 TLS 연결을 사용하도록 설정하는 방법을 살펴보았습니다. 포트 80 이외의 포트에서 수신 대기하는 앱에 이 예제를 적용할 수 있습니다. HTTPS를 사용하도록 Nginx 구성 파일을 업데이트하여 포트 80(HTTP)에서 서버 연결을 자동으로 리디렉션할 수도 있습니다.

이 문서는 사이드카에 Nginx를 사용하지만 [Caddy](https://caddyserver.com/)와 같은 다른 TLS 공급자를 사용할 수도 있습니다.

[Azure 가상 네트워크](container-instances-vnet.md)에 컨테이너 그룹을 배포하는 경우 다음을 포함하여 백 엔드 컨테이너 인스턴스에 대해 TLS 엔드포인트를 사용하도록 설정하는 다른 옵션을 고려할 수 있습니다.

* [Azure Functions 프록시](../azure-functions/functions-proxies.md)
* [Azure API Management](../api-management/api-management-key-concepts.md)
* [Azure Application Gateway](../application-gateway/overview.md) - 샘플 [배포 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/wordpress/aci-wordpress-vnet)을 참조하세요.
