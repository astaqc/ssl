# Self-signed pan-domain certificate
This tool is used to issue pan-domain certificates to facilitate debugging in the development environment.

Please do not use it in a production environment, or purchase an official certificate in the production environment.
Or go to [Let's Encrypt](https://letsencrypt.org/) to apply for a free certificate
(Support multiple domain names and pan domain names).

## Advantages
1. You can create any website certificate, you only need to import the root certificate once instead of multiple times;
1. Reduce repetitive and unnecessary input of organizational information, and only need to enter the domain name when creating a certificate;
1. The pan-domain certificate can reduce the `nginx` configuration, for example, if you want to simulate a CDN:
Suppose your project website is `example.dev`, and the CDN website is set to `cdn.example.dev`,
You only need to configure a website in `nginx`, and fill in `example.dev` at the same time in `server_name`
And `cdn.example.dev`, they can use the same `*.example.dev` certificate.
1. Now you only need one certificate to complete all project websites!

Use `SAN` to support multi-domain and pan-domain:
```ini
subjectAltName=DNS:*.one.dev,DNS:one.dev,DNS:*.two.dev,DNS:two.dev,DNS:*.three.dev,DNS:three.dev
```

## System Requirements
1. Linux, openssl
1. Use `hosts` or `dnsmasq` to resolve your locally developed domain name in advance,
For example, point `example.dev` to `127.0.0.1`

## Use
```bash
./gen.cert.sh <domain> [<domain2>] [<domain3>] [<domain4>] ...
```
Replace `<domain>` with your domain name, for example `example.dev`

The output of the run looks like this:

![Output Screen](docs/output.png)

If there are multiple project websites, you can add all the websites, separated by spaces.

The generated certificate is located at:
```text
out/<domain>/<domain>.crt
out/<domain>/<domain>.bundle.crt
```

The certificate is valid for 2 years, you can modify `ca.cnf` to modify this period.

The root certificate is located at:
`out/root.crt`
After success, import the root certificate into the operating system and trust this certificate.

The validity period of the root certificate is 20 years, you can modify `gen.root.sh` to modify this period.

The certificate private key is located at:
`out/cert.key.pem`

Among them, `<domain>.bundle.crt` is the certificate of the spliced ​​CA, which can be added to the `nginx` configuration.
Then you can happily use `https` to visit your local development website.

## Clear
You can run `flush.sh` to clear all history, including root certificates and website certificates.

## Configuration
You can modify `ca.cnf` to modify the age of your certificate.
```ini
default_days = 730
```

You can modify `gen.root.sh` to customize your root certificate name and organization.

You can also modify `gen.cert.sh` to customize your website certificate organization.

## Reference / Thanks
[Vault and self signed SSL certificates](http://dunne.io/vault-and-self-signed-ssl-certificates)

[Using OpenSSL to create a self-signed SSL certificate memo](http://wangye.org/blog/archives/732/)

[Provide subjectAltName to openssl directly on command line](http://security.stackexchange.com/questions/74345/provide-subjectaltname-to-openssl-directly-on-command-line)

## About Let's Encrypt client
The official client `certbot` [too complicated](https://github.com/Neilpang/acme.sh/issues/386), recommended to use [acme.sh](https://github.com/Neilpang/acme .sh/wiki/%E8%AF%B4%E6%98%8E).

## About .dev domain names
[Chrome to force .dev domains to HTTPS via preloaded HSTS](https://ma.ttias.be/chrome-force-dev-domains-https-via-preloaded-hsts/) ([2017-9-16]( https://chromium-review.googlesource.com/c/chromium/src/+/669923))

## About Chrome Trust Certificate Issue
I saw that some people reported that the certificate cannot be trusted under Chrome, please refer to [this document](docs/chrome-trust.md)
