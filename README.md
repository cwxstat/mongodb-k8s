# mongodb-k8s
## MongoDB on Kubernetes

# Step 1

Generate keys.  The steps are all done in a docker container.

```bash

docker run -it --rm -v ${PWD}:/work -w /work debian bash


apt-get update && apt-get install -y curl &&
curl -L https://github.com/cloudflare/cfssl/releases/download/v1.5.0/cfssl_1.5.0_linux_amd64 -o /usr/local/bin/cfssl && \
curl -L https://github.com/cloudflare/cfssl/releases/download/v1.5.0/cfssljson_1.5.0_linux_amd64 -o /usr/local/bin/cfssljson && \
chmod +x /usr/local/bin/cfssl && \
chmod +x /usr/local/bin/cfssljson

# generate ca in /tmp
cfssl gencert -initca ./tls/ca-csr.json | cfssljson -bare /tmp/ca

# generate certificate in /tmp
cfssl gencert \
  -ca=/tmp/ca.pem \
  -ca-key=/tmp/ca-key.pem \
  -config=./tls/ca-config.json \
  -hostname="mongo,mongo.mongodb.svc.cluster.local,mongo.default.svc,localhost,127.0.0.1,mongo.pigbot.svc.cluster.local,34.117.143.215,34.66.213.165,mongo.cwxstat.io" \
  -profile=default \
  ./tls/ca-csr.json | cfssljson -bare /tmp/mongo-certs


# copy keys from tmp to certs
mkdir certs
cp /tmp/ca* ./certs/.
cp /tmp/mong* ./certs/.

# now exit docker
exit
```

# Step 2

Mongodb requires a merged key. But first you may
need to correct the ownership

```bash

# Here I'm using codespaces, may need to use your own account.
chown -R codespace.codespace ./certs



```
