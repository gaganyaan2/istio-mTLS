# istio-mTLS
istio-mTLS example

![alt text](https://github.com/koolwithk/istio-mTLS/blob/main/image_2021-02-12_162656.png?raw=true)

### Deploy apps and istio policy

```
kubectl create ns istio
kubectl label namespace istio istio-injection=enabled

kubectl -f apps.yml -n istio
kubectl -f PeerAuthentication.yml -n istio
```

## How to call service from pod?
```
curl -s http://app1
curl -s http://app2
```

## How to check if the traffic is going via mTLS?

1. Take the console of istio-proxy-1 
2. curl http://app1 will show "curl: (56) Recv failure: Connection reset by peer"
3. curl -kv https://app1:80 will show something like below which makes TLS handshake

```
* TLSv1.3 (IN), TLS handshake, Unknown (8):
* TLSv1.3 (IN), TLS handshake, Request CERT (13):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Client hello (1):
* TLSv1.3 (OUT), TLS Unknown, Certificate Status (22):
* TLSv1.3 (OUT), TLS handshake, Certificate (11):
* TLSv1.3 (OUT), TLS Unknown, Certificate Status (22):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_CHACHA20_POLY1305_SHA256
* ALPN, server accepted to use h2
* Server certificate:
*  subject: [NONE]
*  start date: Feb 12 07:57:56 2021 GMT
*  expire date: Feb 13 07:57:56 2021 GMT
*  issuer: O=cluster.local
*  SSL certificate verify result: self signed certificate in certificate chain (19), continuing anyway.
* Using HTTP2, server supports multi-use
* Connection state changed (HTTP/2 confirmed)
* Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
* TLSv1.3 (OUT), TLS Unknown, Unknown (23):
* SSL_write() returned SYSCALL, errno = 104
* Failed sending HTTP2 data
* nghttp2_session_send() failed: The user callback function failed(-902)
* Connection #0 to host app2 left intact
curl: (16) SSL_write() returned SYSCALL, errno = 104

```

## Notes :

1. We can not call service with https as the mTLS is only between istio-sidecar-proxy
2. We May need to delete pod which don't have the STRICT mTLS

## Reference :

1. https://istiobyexample.dev/mtls
2. https://istio.io/latest/docs/reference/config/security/peer_authentication/
3. https://istio.io/latest/docs/reference/config/networking/destination-rule/
4. https://discuss.istio.io/t/mtls-is-not-working-between-services-istio-1-9-0/9710/8
