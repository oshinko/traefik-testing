# traefik-testing

## 自己署名証明書を作成する

```sh
cd ./certs

# CA の鍵を作成する
openssl ecparam -genkey -name prime256v1 -out ./ca.key

# CA の証明書署名要求 (CSR) を作成する
openssl req -new -sha256 -key ./ca.key -subj "/C=JP/ST=Tokyo/L=Shibuya-ku/O=Example/OU=Example Unit/CN=Example CA" -out ./ca.csr

# CA の証明書を作成する
cat << EOF > ./ca.ext.txt
basicConstraints = critical, CA:TRUE
EOF
openssl x509 -req -sha256 -days 3650 -extfile ./ca.ext.txt -signkey ./ca.key -in ./ca.csr -out ./ca.crt

# サーバー (ドメインの証明書) の鍵を作成する
openssl ecparam -genkey -name prime256v1 -out ./server.key

# ドメインの証明書署名要求 (CSR) を作成する
openssl req -new -sha256 -key ./server.key -subj "/CN=whoami.localhost" -out ./whoami.localhost.csr

# ドメインの証明書を作成する (CA キーで署名する)
cat << EOF > ./whoami.localhost.ext.txt
basicConstraints = critical, CA:FALSE
subjectAltName = DNS:whoami.localhost
EOF
openssl x509 -req -sha256 -days 365 -extfile ./whoami.localhost.ext.txt -CA ./ca.crt -CAkey ./ca.key -CAcreateserial -in ./whoami.localhost.csr -out ./whoami.localhost.crt
```
