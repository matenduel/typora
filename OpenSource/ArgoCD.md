# ArgoCD란?




# Configurations



# Advanced
> Argocd Helm Chart "5.7.0" 버젼 기준
 
## Google Oauth2 OpenID 설정법
```yaml
configs:
  cm:
    create: true
    url: "https://sub.example.com"  # "http://sub.example.com"
    dex.config: |
      dexserver.disable.tls: true
      connectors:
        - config:
            issuer: https://accounts.google.com
            clientID: xxxx.apps.googleusercontent.com
            clientSecret: GOCSPX-xxxxxxxxx
          type: oidc
          id: google
          name: Google
```


## RBAC 설정법




# Trouble Shooting
## Redirection 문제
**문제**

브라우저에서 `ERR_TOO_MANY_REDIRECTS` 에러가 발생

**원인**

Ingress에서 인증서 처리를 하는 경우, service의 80 포트로 요청을 전송하게 된다.

이때, ArgoCD Server에서 80 포트로 들어온 http 요청을 https로 리다이렉트하면서 무한 리다이렉트가 발생함

**해결방법**

argocd에서 tls를 사용하지 않도록 `insecure`를 설정하면 된다. 
```yaml
configs:
  params:
    server.insecure: true
```
또는
```yaml
server:
  extraArgs:
    - --insecure
```
