# traefik-testing

## 自己署名証明書を作成する

```sh
cd ./certs

# CA の鍵を作成する
openssl ecparam -genkey -name secp256k1 -out ./ca.key

# CA の証明書署名要求 (CSR) を作成する
openssl req -new -sha256 -key ./ca.key -subj "/C=JP/ST=Tokyo/O=Example/CN=Example Root CA" -out ./ca.csr

# CA の証明書を作成する
openssl x509 -req -sha256 -days 3650 -signkey ./ca.key -in ./ca.csr -out ./ca.crt

# サーバー (ドメインの証明書) の鍵を作成する
openssl ecparam -genkey -name secp256k1 -out ./server.key

# ドメインの証明書署名要求 (CSR) を作成する
openssl req -new -sha256 -key ./server.key -subj "/CN=whoami.localhost" -out ./whoami.localhost.csr

# ドメインの証明書を作成する (CA キーで署名する)
echo "subjectAltName = DNS:whoami.localhost" > ./san.txt
openssl x509 -req -sha256 -days 365 -extfile ./san.txt -CA ./ca.crt -CAkey ./ca.key -CAcreateserial -in ./whoami.localhost.csr -out ./whoami.localhost.crt
```
