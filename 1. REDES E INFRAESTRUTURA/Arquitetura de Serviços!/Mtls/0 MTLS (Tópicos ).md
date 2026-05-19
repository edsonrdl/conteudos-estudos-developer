# 📚 Guia de Estudos: mTLS (Mutual TLS)

#tag #segurança #redes #infraestrutura

## Visão Geral

O mTLS (Mutual Transport Layer Security) é uma extensão do protocolo TLS onde **ambos os lados da conexão se autenticam mutuamente via certificado digital**. Enquanto o TLS comum garante apenas que o cliente conhece o servidor, o mTLS garante também que o servidor conhece e confia no cliente. É o padrão de autenticação em comunicações serviço a serviço (service mesh), APIs internas e ambientes Zero Trust.

---

## 1 Domínio: Fundamentos e Contexto

### 1.1 Visão Geral e Histórico

- **1.1.1. O que é mTLS**
    - 1.1.1.1. Extensão do TLS onde cliente e servidor trocam e validam certificados X.509 mutuamente.
    - 1.1.1.2. Diferença fundamental: no TLS padrão só o servidor apresenta certificado; no mTLS o cliente também apresenta.
- **1.1.2. Por que foi necessário**
    - 1.1.2.1. Limitações do TLS unilateral em ambientes corporativos e de microsserviços.
    - 1.1.2.2. Surgimento do modelo Zero Trust e a necessidade de "never trust, always verify".

### 1.2 Relação com TLS e a Pilha de Protocolos

- **1.2.1. Posição na pilha OSI/TCP**
    - 1.2.1.1. mTLS opera na Camada 4/5 (entre Transporte e Sessão) — acima do TCP, abaixo do HTTP.
- **1.2.2. TLS vs mTLS — comparativo**
    - 1.2.2.1. TLS: autenticação unilateral (apenas o servidor prova identidade).
    - 1.2.2.2. mTLS: autenticação bilateral (cliente e servidor provam identidade mutuamente).
    - 1.2.2.3. O request HTTP só trafega depois que o handshake mTLS é concluído.

---

## 2 Domínio: Certificados e PKI

### 2.1 Infraestrutura de Chaves Públicas (PKI)

- **2.1.1. Conceitos base**
    - 2.1.1.1. Chave pública e chave privada: o que são e como se complementam.
    - 2.1.1.2. Certificado X.509: estrutura, campos principais (CN, SAN, Validity, Issuer).
- **2.1.2. Certificate Authority (CA)**
    - 2.1.2.1. O que é uma CA e seu papel como âncora de confiança.
    - 2.1.2.2. CA pública (Let's Encrypt, DigiCert) vs CA privada/interna (usada em mTLS corporativo).
    - 2.1.2.3. Chain of trust: Root CA → Intermediate CA → Leaf Certificate.

### 2.2 Ciclo de Vida dos Certificados

- **2.2.1. Emissão**
    - 2.2.1.1. Geração do par de chaves e do CSR (Certificate Signing Request).
    - 2.2.1.2. Assinatura pela CA e distribuição do certificado.
- **2.2.2. Renovação e Revogação**
    - 2.2.2.1. Tempo de validade e rotação automática de certificados.
    - 2.2.2.2. CRL (Certificate Revocation List) e OCSP (Online Certificate Status Protocol).
- **2.2.3. Armazenamento seguro**
    - 2.2.3.1. Onde guardar a chave privada: Secrets Managers (Vault, AWS Secrets Manager, GCP KMS).

---

## 3 Domínio: O Handshake mTLS

### 3.1 Fluxo Completo do Handshake

- **3.1.1. Fase 1 — Negociação**
    - 3.1.1.1. ClientHello: cliente anuncia versão TLS suportada, cipher suites e Client Random.
    - 3.1.1.2. ServerHello: servidor escolhe cipher suite e envia Server Random.
- **3.1.2. Fase 2 — Autenticação do Servidor (igual ao TLS comum)**
    - 3.1.2.1. Servidor envia seu certificado X.509.
    - 3.1.2.2. Cliente valida o certificado contra sua lista de CAs confiáveis.
- **3.1.3. Fase 3 — Autenticação do Cliente (exclusivo mTLS)**
    - 3.1.3.1. CertificateRequest: servidor solicita o certificado do cliente.
    - 3.1.3.2. Cliente envia seu certificado + CertificateVerify (prova posse da chave privada via assinatura).
    - 3.1.3.3. Servidor valida o certificado do cliente contra sua CA confiável.
- **3.1.4. Fase 4 — Estabelecimento do canal seguro**
    - 3.1.4.1. Geração das chaves de sessão simétricas (usando Diffie-Hellman ou RSA key exchange).
    - 3.1.4.2. Finished: ambos confirmam que o handshake foi íntegro.
    - 3.1.4.3. A partir daqui: todos os dados (requests HTTP, gRPC etc.) trafegam cifrados.

### 3.2 O que acontece se a validação falhar

- **3.2.1. Certificado inválido ou expirado**
    - 3.2.1.1. Handshake é abortado com TLS Alert — conexão encerrada antes de qualquer request.
- **3.2.2. CA desconhecida**
    - 3.2.2.1. Servidor rejeita o certificado do cliente se a CA emissora não estiver no trust store.
- **3.2.3. Certificate mismatch**
    - 3.2.3.1. Falha no SAN/CN: identidade declarada no certificado não bate com a esperada.

---

## 4 Domínio: Implementação e Casos de Uso

### 4.1 Onde mTLS é Aplicado

- **4.1.1. Service Mesh (Istio, Linkerd, Consul Connect)**
    - 4.1.1.1. mTLS automático entre sidecars — transparente para a aplicação.
    - 4.1.1.2. Certificados de curta duração emitidos e rotacionados pelo control plane.
- **4.1.2. APIs internas e B2B**
    - 4.1.2.1. Substituição de API Keys por identidade criptográfica.
    - 4.1.2.2. Casos: Open Banking, parceiros de pagamento, integração entre empresas.
- **4.1.3. Zero Trust Network Access (ZTNA)**
    - 4.1.3.1. Cada workload possui identidade própria (SPIFFE/SPIRE).
    - 4.1.3.2. Nenhum serviço é confiável por padrão — autenticação na camada de rede.

### 4.2 Configuração Prática

- **4.2.1. Nginx / Envoy / HAProxy**
    - 4.2.1.1. Diretivas para exigir `ssl_verify_client on` e apontar o `ssl_client_certificate`.
- **4.2.2. Kubernetes + Istio**
    - 4.2.2.1. PeerAuthentication com `mode: STRICT` para forçar mTLS em todos os pods do namespace.
- **4.2.3. Linguagens e SDKs**
    - 4.2.3.1. Go: `tls.Config` com `ClientAuth: tls.RequireAndVerifyClientCert`.
    - 4.2.3.2. Java: configuração de `KeyStore` e `TrustStore` no SSLContext.
    - 4.2.3.3. Python: `ssl.SSLContext` com `CERT_REQUIRED` e carregamento de `certfile`/`keyfile`.

---

## 5 Domínio: Operação e Troubleshooting

### 5.1 Diagnóstico de Problemas

- **5.1.1. OpenSSL — inspeção e teste**
    - 5.1.1.1. `openssl s_client -connect host:port -cert client.crt -key client.key`: testa o handshake mTLS manualmente.
    - 5.1.1.2. `openssl x509 -in cert.crt -text -noout`: inspeciona campos do certificado (validade, SAN, issuer).
- **5.1.2. curl com certificado de cliente**
    - 5.1.2.1. `curl --cert client.crt --key client.key --cacert ca.crt https://api.exemplo.com`: valida a conexão mTLS end-to-end.
- **5.1.3. Wireshark**
    - 5.1.3.1. Filtro `tls.handshake.type == 13` para capturar o CertificateRequest (passo exclusivo do mTLS).
    - 5.1.3.2. Análise de TLS Alerts para identificar a fase exata da falha.

### 5.2 Erros Comuns e Soluções

- **5.2.1. `SSL: CERTIFICATE_VERIFY_FAILED`**
    - 5.2.1.1. CA do cliente não está no trust store do servidor — adicionar o certificado da CA interna.
- **5.2.2. `tls: bad certificate`**
    - 5.2.2.1. Certificado expirado, revogado ou gerado por CA diferente da esperada.
- **5.2.3. Handshake timeout / connection reset**
    - 5.2.3.1. Firewall bloqueando a porta ou não passando o full TLS handshake (problema comum com load balancers em modo TCP).

---

## Links Relacionados

[TLS/SSL](https://claude.ai/chat/11138b88-cdec-4bfc-b439-568f22e7b7e9) · [PKI e Certificados X.509](https://claude.ai/chat/11138b88-cdec-4bfc-b439-568f22e7b7e9) · [Zero Trust](https://claude.ai/chat/11138b88-cdec-4bfc-b439-568f22e7b7e9) · [Service Mesh](https://claude.ai/chat/11138b88-cdec-4bfc-b439-568f22e7b7e9) · [SPIFFE/SPIRE](https://claude.ai/chat/11138b88-cdec-4bfc-b439-568f22e7b7e9) · [OAuth 2.0 / JWT](https://claude.ai/chat/11138b88-cdec-4bfc-b439-568f22e7b7e9)