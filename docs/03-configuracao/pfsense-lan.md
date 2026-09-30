# Configuração da LAN no pfSense

## Objetivo

Documentar a configuração da interface LAN do pfSense, utilizada como rede interna de administração e testes do laboratório.

A LAN conecta o Kali Linux ao pfSense e permite validar gateway, DNS, NAT e acesso à Internet.

## Parâmetros da LAN

| Parâmetro | Valor |
|---|---|
| Interface | LAN |
| Interface virtual | em1 |
| Rede | 192.168.1.0/24 |
| Endereço do pfSense | 192.168.1.1/24 |
| Gateway da LAN | 192.168.1.1 |
| DNS dos clientes | 192.168.1.1 |
| DHCP | Habilitado, se utilizado |
| Função | Administração e testes |

A instalação padrão do pfSense utiliza `192.168.1.1/24` como endereço da LAN e normalmente disponibiliza DHCP nessa interface [313].

## Configuração da interface

Acesse:

```text
Interfaces > LAN
```

Configure:

```text
Enable: habilitado
Description: LAN
IPv4 Configuration Type: Static IPv4
IPv4 Address: 192.168.1.1
Subnet mask: /24
IPv4 Upstream Gateway: None
```

A LAN não deve receber um gateway upstream próprio. O pfSense atua como gateway dos clientes LAN; o gateway externo pertence à interface WAN.

Em interfaces com IPv4 estático, o endereço é configurado manualmente e permanece associado à interface até ser alterado [312].

## Configuração DHCP

Acesse:

```text
Services > DHCP Server > LAN
```

Se o DHCP for utilizado, configure:

```text
Enable DHCP server on LAN interface: habilitado
Range inicial: 192.168.1.100
Range final: 192.168.1.199
Gateway: 192.168.1.1
DNS server: 192.168.1.1
```

A faixa DHCP fica dentro da rede `192.168.1.0/24`, mas não inclui o próprio endereço do pfSense.

O serviço DHCP fornece aos clientes endereço IPv4 e informações necessárias para acesso à rede, como gateway e DNS [310].

## Cliente Kali Linux

O Kali Linux está conectado à LAN e utiliza o pfSense como gateway e servidor DNS.

Configuração esperada:

| Parâmetro | Valor |
|---|---|
| Endereço IP | 192.168.1.100 ou endereço DHCP |
| Máscara | 255.255.255.0 |
| Gateway | 192.168.1.1 |
| DNS | 192.168.1.1 |

Comandos para verificar a configuração no Kali:

```bash
ip -br addr
ip route
cat /etc/resolv.conf
```

Resultado esperado:

```text
default via 192.168.1.1
nameserver 192.168.1.1
```

## Regras de firewall da LAN

Acesse:

```text
Firewall > Rules > LAN
```

A configuração deve permitir, conforme o objetivo do laboratório:

- Consultas DNS para o pfSense.
- DHCP, quando o cliente usar endereço automático.
- Acesso HTTPS e HTTP à Internet.
- ICMP para testes de conectividade, se desejado.
- Acesso administrativo ao pfSense a partir da LAN.

As regras devem ser revisadas para evitar permissões mais amplas do que o necessário.

## Fluxo de saída

O tráfego da LAN para a Internet segue este caminho:

```text
Kali Linux
  -> 192.168.1.1
  -> pfSense LAN
  -> NAT de saída
  -> pfSense WAN 10.0.2.15
  -> Gateway 10.0.2.2
  -> Internet
```

## Validação

### Teste do gateway

```bash
ping -c 4 192.168.1.1
```

Resultado esperado:

```text
0% packet loss
```

### Teste de resolução DNS

```bash
nslookup example.com 192.168.1.1
```

Resultado esperado:

```text
Server: 192.168.1.1
Address: 192.168.1.1#53
```

### Teste de acesso HTTPS

```bash
curl -I [https://example.com](https://example.com)
```

Resultado esperado:

```text
HTTP/2 200
```

## Resultado

A LAN foi configurada com sucesso:

- O pfSense responde em `192.168.1.1`.
- O Kali alcança o gateway da LAN.
- O Kali utiliza o pfSense como DNS.
- O pfSense resolve nomes externos.
- O tráfego HTTPS sai pela WAN.
- O NAT de saída está funcionando.

## Evidências

### Dashboard do pfSense

![Dashboard do pfSense](../02-baseline/evidencias/pfsense-dashboard-baseline.png)

### Testes realizados pelo Kali

![Testes de DNS e HTTPS no Kali](../02-baseline/evidencias/dns-e-https-kali.png)

## Observações de segurança

A LAN é considerada uma rede confiável para administração e testes neste laboratório. Essa confiança deve ser reduzida em ambientes reais, utilizando:

- Regras específicas por origem e destino.
- Acesso administrativo limitado.
- Autenticação forte.
- Registro de eventos.
- Segmentação entre usuários, servidores e dispositivos de teste.

## Próximos passos

- Documentar a configuração da WAN.
- Documentar a configuração da DMZ.
- Criar regras específicas entre LAN e DMZ.
- Validar o bloqueio de tráfego iniciado pela DMZ para a LAN.
- Registrar os logs do firewall durante os testes.
