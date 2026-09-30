# Baseline de Conectividade

**Data:** 30/09/2026  
**Ambiente:** Laboratório de redes e segurança em VirtualBox  
**Status:** Validado

## Objetivo

Registrar o estado inicial funcional da rede antes da implementação da DMZ e de controles adicionais de segurança.

## Topologia atual

```text
Kali Linux
  |
  | LAN: 192.168.1.0/24
  |
pfSense
  | LAN: 192.168.1.1
  | WAN: 10.0.2.15
  |
VirtualBox NAT
  | Gateway: 10.0.2.2
  | DNS: 10.0.2.3
  |
Internet
```

## Endereçamento

| Dispositivo | Interface | Endereço | Função |
|---|---|---:|---|
| pfSense | LAN | 192.168.1.1/24 | Gateway e DNS da LAN |
| pfSense | WAN | 10.0.2.15 | Saída para a Internet |
| Kali Linux | LAN | 192.168.1.100 | Máquina de testes |
| VirtualBox NAT | Gateway | 10.0.2.2 | Gateway upstream |
| VirtualBox NAT | DNS | 10.0.2.3 | DNS upstream |

## Serviços validados

- O Kali alcança o endereço LAN do pfSense em `192.168.1.1`.
- O pfSense responde às consultas DNS na porta 53.
- O DNS `example.com` é resolvido pelo Kali através do pfSense.
- O pfSense encaminha as consultas ao DNS `10.0.2.3`.
- O Kali acessa sites HTTPS pela WAN do pfSense.
- O NAT de saída está funcionando.

## Evidências

### Consulta DNS

Comando executado:

```bash
nslookup example.com 192.168.1.1
```

Resultado esperado:

```text
Server: 192.168.1.1
Address: 192.168.1.1#53

Name: example.com
Address: 104.20.23.154
Address: 172.66.147.243
```

### Teste HTTPS

Comando executado:

```bash
curl -I [https://example.com](https://example.com)
```

Resultado:

```text
HTTP/2 200
server: cloudflare
content-type: text/html; charset=utf-8
```

## Critério de sucesso

O baseline foi considerado aprovado porque:

1. O cliente Kali obteve resposta DNS através do pfSense.
2. O nome `example.com` foi convertido em endereços IP.
3. O acesso HTTPS externo funcionou.
4. O tráfego da LAN atravessou o pfSense usando NAT.

## Próxima alteração planejada

Adicionar a interface DMZ com a rede:

```text
192.168.2.0/24
```

Endereço do pfSense na DMZ:

```text
192.168.2.1
```

A implementação da DMZ deverá ser comparada com este baseline para verificar se a conectividade da LAN continua funcionando e se o isolamento entre LAN e DMZ está correto.
