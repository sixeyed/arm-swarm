# Generating TLS certs for seuring Swarm nodes

See https://docs.docker.com/engine/security/https/

You will need [OpenSSL]().

## First create the Certificate Authority (CA)

This uses a random password for the CA. You can replace the location details in the `subj` parameter.

```
mkdir  ~/certs && cd  ~/certs
openssl rand -base64 32 > docker-ca.password
openssl genrsa -aes256 -passout file:docker-ca.password -out docker-ca-key.pem 4096
openssl req -subj "/C=UK/ST=London/L=London/O=Docker/OU=." -new -x509 -days 3650 -passin file:docker-ca.password -key docker-ca-key.pem -sha256 -out docker-ca.pem
```

> You only do this once, and use the CA cert files to generate the other certs


## Create Server Certificates

You'll use these certs in the Docker Engine configuration to secure the channel.

You need to set your own server details in the command:

* DNS name in the CN field (`linux-01.sixeyed` in this example)
* IP addres in `subjectAltName1` (`192.168.1.200` below)

```
mkdir  ~/certs/linux-01 && cd  ~/certs/linux-01
openssl genrsa -out server-key.pem 4096
openssl req -subj "/CN=linux-01.sixeyed" -sha256 -new -key server-key.pem -out server.csr
echo subjectAltName = DNS:linux-01.sixeyed,IP:192.168.1.200,IP:127.0.0.1 >> extfile.cnf
echo extendedKeyUsage = serverAuth >> extfile.cnf
openssl x509 -req -days 3650 -sha256 -in server.csr -CA ../docker-ca.pem -CAkey ../docker-ca-key.pem -CAcreateserial -out server-cert.pem -extfile extfile.cnf -passin file:../docker-ca.password
```

> Repeat this for every node in your cluster - use variables instead of hardcoding :)

## Create Client Certificate

You'll use this in your Docker CLI configuration to talk to the remote Engine over TLS. 

The client cert uses the same CA as the server certs, so you can use one client cert to authenticate with every server.

```
mkdir ~/certs/client
cd ~/certs/client
openssl genrsa -out key.pem 4096
openssl req -subj '/CN=client' -new -key key.pem -out client.csr
echo extendedKeyUsage = clientAuth > extfile-client.cnf
openssl x509 -req -days 3650 -sha256 -in client.csr -CA ../docker-ca.pem -CAkey ../docker-ca-key.pem -CAcreateserial -out cert.pem -extfile extfile-client.cnf -passin file:../docker-ca.password
```

