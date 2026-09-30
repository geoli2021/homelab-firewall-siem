# Topologia do Laboratório

## Visão geral

Este laboratório foi criado em VirtualBox para praticar conceitos de redes e segurança da informação, incluindo firewall, segmentação de rede, DNS, NAT e DMZ.

O pfSense atua como firewall e roteador central entre a rede externa, a LAN e a DMZ.

## Diagrama lógico

```text
                         Internet
                            |
                    VirtualBox NAT
                  Gateway: 10.0.2.2
                  DNS: 10.0.2.3
                            |
                    WAN do pfSense
                    10.0.2.15/24
                            |
                  +-------------------+
                  |      pfSense      |
                  | Firewall/Roteador |
                  +-------------------+
                     |             |
        LAN 192.168.1.0/24     DMZ 192.168.2.0/24
                     |             |
          pfSense: 192.168.1.1  pfSense: 192.168.2.1
                     |             |
              Kali Linux       Servidores da DMZ
          192.168.1.100      192.168.2.0/24
```

## Componentes

| Componente | Interface/Rede | Endereço | Função |
|---|---|---:|---|
| pfSense | WAN | 10.0.2.15/24 | Acesso externo pelo NAT do VirtualBox |
| pfSense | LAN | 192.168.1.1/24 | Gateway, firewall e DNS da LAN |
| pfSense | DMZ | 192.168.2.1/24 | Gateway e isolamento da DMZ |
| Kali Linux | LAN | 192.168.1.100 | Estação de testes de segurança |
| VirtualBox NAT | Gateway | 10.0.2.2 | Saída da WAN para a Internet |
| VirtualBox NAT | DNS | 10.0.2.3 | Resolução DNS upstream |

## Redes utilizadas

### WAN

```text
Rede: 10.0.2.0/24
Gateway: 10.0.2.2
DNS upstream: 10.0.2.3
pfSense WAN: 10.0.2.15
```

A interface WAN utiliza a rede NAT do VirtualBox para permitir que o pfSense acesse a Internet.

### LAN

```text
Rede: 192.168.1.0/24
Gateway: 192.168.1.1
```

A LAN é a rede de administração e testes. O Kali Linux está conectado a essa rede e utiliza o pfSense como gateway e servidor DNS.

### DMZ

```text
Rede: 192.168.2.0/24
Gateway: 192.168.2.1
```

A DMZ é uma rede separada destinada a serviços que poderão ser expostos ou testados com maior controle. O tráfego entre LAN, DMZ e WAN será controlado por regras do firewall.

## Interfaces do pfSense

| Interface | Nome | Endereço | Estado |
|---|---|---:|---|
| em0 | WAN | 10.0.2.15 | Ativa |
| em1 | LAN | 192.168.1.1 | Ativa |
| em2 | DMZ | 192.168.2.1 | Ativa |

Os nomes reais das interfaces podem variar conforme a ordem dos adaptadores de rede configurados no VirtualBox.

## Fluxo de tráfego

### Da LAN para a Internet

```text
Kali Linux
  -> Gateway 192.168.1.1
  -> pfSense
  -> NAT para 10.0.2.15
  -> Gateway 10.0.2.2
  -> Internet
```

### Resolução DNS

```text
Kali Linux
  -> DNS 192.168.1.1
  -> DNS Resolver do pfSense
  -> DNS upstream 10.0.2.3
  -> Resposta para o Kali
```

### Da DMZ para a Internet

O tráfego da DMZ deverá passar pelo pfSense e ser controlado por regras específicas. O acesso será permitido somente quando necessário.

### Da LAN para a DMZ

A LAN poderá administrar os ativos da DMZ conforme regras explícitas do firewall.

### Da DMZ para a LAN

O tráfego iniciado pela DMZ para a LAN deverá ser bloqueado por padrão, salvo quando uma regra específica for criada.

## Objetivos de segurança

- Separar a rede de testes da rede DMZ.
- Utilizar o pfSense como ponto central de controle.
- Permitir somente o tráfego necessário entre as redes.
- Evitar que um sistema comprometido na DMZ alcance livremente a LAN.
- Registrar e validar o comportamento da rede antes e depois das mudanças.
- Usar o Kali Linux para testar conectividade e controles de segurança.

## Evidências

### Dashboard do pfSense

![Dashboard do pfSense](../02-baseline/evidencias/pfsense-dashboard-baseline.png)

O Dashboard confirma que as interfaces WAN, LAN e DMZ estão ativas, que o gateway WAN está online e que o DNS Resolver está em execução.

## Estado atual

- WAN configurada com endereço `10.0.2.15`.
- LAN configurada com endereço `192.168.1.1`.
- DMZ configurada com endereço `192.168.2.1`.
- Gateway WAN `10.0.2.2` online.
- DNS Resolver `unbound` em execução.
- Kali Linux validado na LAN.
- Resolução DNS e acesso HTTPS validados.

## Próximos passos

1. Conectar uma máquina de teste à DMZ.
2. Configurar DHCP ou IP estático na DMZ.
3. Criar regras de firewall entre LAN, DMZ e WAN.
4. Testar o isolamento da DMZ.
5. Registrar os resultados em `docs/04-testes/`.
