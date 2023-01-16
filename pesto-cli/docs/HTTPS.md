# HTTPS support

docker run --rm -p 4000:8080 -e PESTO_USE_SSL='1' algo-service:1.0.0.dev0

TODO: add --ssl option to `run docker` and `test` command

## Method 1: PESTO generated certificate
Work with automatically generated SSL certificate :

And call the webservice with `https://localhost:8443/api/v1`. The certificate validity must be ignored in this case as
it is not issued from a valid Certificate Authority.

## Method 2: mkcert generated certificate
Use `mkcert` to generate a certificate and valid CA (the certificate will be considered valid for any call to the
webservice from the local computer where the CA is installed).

1. Install [mkcert](https://github.com/FiloSottile/mkcert) or use a docker image
2. Execute the following commands:  
First one to install the local root CA into the system:
```shell
mkcert -install
```
Second one to generate the certificate and key to start the webservice:
```shell
# generate the cert and key
mkcert localhost 127.0.0.1 ::1
# rename the generated files
mv localhost+2-key.pem key.pem
mv localhost+2.pem cert.pem
# put them in an archive for PESTO requirements
tar czf ssl.tar.gz cert.pem key.pem
```

Declare the resulting archive as a requirement in `requirements.json` (update the `from` path):
```json
{
  "environments": {
  },
  "requirements": {
    "ssl": {
      "from": "file:///path/to/your/ssl.tar.gz",
      "to": "/etc/pesto/ssl/"
    }
  },
  "dockerBaseImage": "python:3.8-buster"
}
```

## Method 3: Let's Encrypt certificate
You need to get a DNS domain registered and follow the instructions from 
[Let's Encrypt](https://letsencrypt.org/getting-started/) documentation.

You can then add your key and certificate:
```shell
tar czf ssl.tar.gz cert.pem key.pem
```

Declare the resulting archive as a requirement in `requirements.json` (update the `from` path):
```json
{
  "environments": {
  },
  "requirements": {
    "ssl": {
      "from": "file:///path/to/your/ssl.tar.gz",
      "to": "/etc/pesto/ssl/"
    }
  },
  "dockerBaseImage": "python:3.8-buster"
}
```
