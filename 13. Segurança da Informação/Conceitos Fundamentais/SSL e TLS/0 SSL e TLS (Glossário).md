SSL e TLS (Secure Sockets Layer / Transport Layer Security)
#seguranca #redes #criptografia

## 1. Visão Geral

Protocolos criptográficos projetados para fornecer segurança de comunicação em uma rede de computadores.

> **Nota Importante:** O SSL (versões 1.0, 2.0, 3.0) está **depreciado** por falhas de segurança. O padrão moderno é o **TLS** (1.2 ou 1.3), mas ainda usamos o termo "SSL" por hábito.

## 2. Para que serve?

1. **Criptografia:** Garante que ninguém no meio do caminho (Man-in-the-Middle) leia os dados.
    
2. **Integridade:** Garante que os dados não foram alterados no trajeto.
    
3. **Autenticação:** Garante que o servidor é quem diz ser (via Certificado).
    

## 3. Como funciona: O Handshake (Aperto de Mão)

Antes de trocar dados, cliente e servidor negociam a segurança:

1. **Client Hello:** "Olá, eu suporto TLS 1.2 e essas cifras."
    
2. **Server Hello:** "Olá, vamos usar TLS 1.2. Aqui está meu **Certificado Digital** (Chave Pública)."
    
3. **Verificação:** O cliente checa se o certificado é válido (emitido por uma CA confiável).
    
4. **Troca de Chaves:** Cliente e Servidor geram uma **Chave de Sessão** (Simétrica) usando a Chave Pública (Assimétrica) para proteção inicial.
    
5. **Túnel Seguro:** A partir daqui, tudo é criptografado com a Chave de Sessão (que é muito mais rápida).
    

## 4. Certificados Digitais

- **CA (Certificate Authority):** A entidade que "assina" o certificado (ex: Let's Encrypt, DigiCert).
    
- **Self-Signed:** Certificado assinado por você mesmo (bom para dev/testes, ruim para produção pois o navegador alerta "Não Seguro").
    
- **Wildcard:** Protege `*.dominio.com` (todos os subdomínios).
    

## 5. Diferença: Criptografia Simétrica vs Assimétrica

- **Assimétrica (Par de Chaves):** Usada apenas no Handshake para trocar o segredo. (Lenta, mas segura para troca).
    
- **Simétrica (Chave Única):** Usada para transmitir os dados da sessão. (Rápida).
    

## 6. Links Relacionados

- [[Criptografia]]
    
- [[HTTPS]]
    
- [[mTLS]] (Quando o cliente TAMBÉM precisa de certificado)
    
- [[Man-in-the-Middle]] (O ataque que o SSL previne)