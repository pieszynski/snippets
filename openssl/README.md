# Generowanie certyfikatów SSL

## Samopodpisane
W każdym przypadku poza CA generowanie odbywa się w trzech etapach

- wygenerowanie klucza prywatnego (KEY)
- wygenerowanie rządania podpisu (CSR)
- wygenerowanie klucza publicznego i podpisanie go przez certyfikat nadrzędny (PEM)

### Certyfikat główny (CA: Sun)
```bash
openssl genpkey -algorithm RSA -out sun.key -aes256 -pass pass:sunSet
openssl req -x509 -new -nodes -key sun.key -sha256 -days 28105 -out sun.pem -passin pass:sunSet -config sun.cnf
```

### Pośredni (Intermediate: Jupiter)
```bash
openssl genpkey -algorithm RSA -out jupiter.key -aes256 -pass pass:jupiterEye
openssl req -new -key jupiter.key -out jupiter.csr -passin pass:jupiterEye -subj "/C=PL/O=Pieszynski/CN=Jupiter"
openssl x509 -req -in jupiter.csr -CA sun.pem -CAkey sun.key -CAcreateserial -out jupiter.pem -days 27375 -sha256 -passin pass:sunSet -extfile jupiter.cnf -extensions v3_jupiter_int
```

### Użytkownika (User: Europa)
```bash
openssl genpkey -algorithm RSA -out europa.key -aes256 -pass pass:europaFtw
openssl req -new -key europa.key -out europa.csr -passin pass:europaFtw -subj "/C=PL/O=Pieszynski/CN=Europa"
openssl x509 -req -in europa.csr -CA jupiter.pem -CAkey jupiter.key -CAcreateserial -out europa.pem -days 26645 -sha256 -passin pass:jupiterEye -extfile europa.cnf -extensions v3_europa_cn
openssl x509 -in europa.pem -noout -fingerprint -sha1 | tr -d ':'
```

## Więcej info

- [OpenSSL configuration examples](https://www.ibm.com/docs/en/hpvs/1.2.x?topic=reference-openssl-configuration-examples)
- [OpenSSL Documentation/config](https://docs.openssl.org/3.1/man5/config/)
- Vibe coding ze sprawdzaniem 😎
