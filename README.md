# Homelab de Segurança: Firewall, WAF e SIEM

Laboratório em VirtualBox para praticar segmentação de rede, firewall (pfSense), WAF (ModSecurity) e monitoramento/detecção (Wazuh). Cada fase é documentada com configuração, testes e lições aprendidas.

> Todo o ambiente é isolado e usa apenas dados fictícios. Nenhuma informação de redes ou sistemas reais é publicada.

## Objetivos

- Entender na prática o fluxo de tráfego entre zonas WAN, LAN e DMZ.
- Criar e testar regras de firewall, NAT e port forwarding.
- Validar o efeito de cada regra com Nmap (antes e depois).
- Proteger uma aplicação vulnerável com WAF e ajustar falsos positivos.
- Coletar logs e gerar alertas em um SIEM, mapeando detecções ao MITRE ATT&CK.

## Arquitetura

| Zona | Interface | VirtualBox | Sub-rede | Função |
|---|---|---|---|---|
| WAN | em0 | NAT | DHCP (10.0.2.0/24) | Saída para a internet |
| LAN | em1 | Rede Interna `LAN` | 192.168.1.0/24 | Estação atacante (Kali) e administração |
| DMZ | em2 | Rede Interna `DMZ` | 192.168.2.0/24 | Servidor web alvo com WAF |

Fluxo resumido:

    Internet -> [WAN em0] -> pfSense -> [LAN em1] -> Kali Linux
                                     -> [DMZ em2] -> Ubuntu + DVWA + ModSecurity

O diagrama completo fica em `docs/network-diagram.png`.

## Máquinas virtuais

| VM | Sistema | Função | RAM sugerida |
|---|---|---|---|
| pfSense | pfSense (FreeBSD 64-bit) | Firewall/roteador | 2 GB |
| Kali | Kali Linux | Atacante | 2 GB |
| Alvo web | Ubuntu Server + DVWA | Aplicação vulnerável | 2 GB |
| SIEM | Wazuh (OVA) | Coleta e detecção | 4 GB |

## Fases

| Fase | Descrição | Status |
|---|---|---|
| [01 - Instalação do pfSense](01-pfsense-setup/) | VM, interfaces, instalação, troubleshooting | Em andamento |
| [02 - Regras de firewall e DMZ](02-firewall-rules/) | Regras, NAT, segmentação, testes com Nmap | Planejado |
| [03 - WAF com ModSecurity](03-waf-modsecurity/) | OWASP CRS, SQLi/XSS, falsos positivos | Planejado |
| [04 - SIEM com Wazuh](04-siem-wazuh/) | Agentes, regras customizadas, ATT&CK | Planejado |

## Destaques a entregar

- [ ] Comparativo Nmap antes e depois de cada regra de firewall.
- [ ] Prova de que a DMZ não inicia conexões para a LAN.
- [ ] Regra customizada no ModSecurity e ajuste de falsos positivos.
- [ ] Detecção de brute force SSH e port scan no Wazuh.

## Problemas resolvidos

| Sintoma | Causa | Solução |
|---|---|---|
| `CPU doesn't support long mode` | VM 32 bits ou virtualização indisponível | Tipo FreeBSD (64-bit), VT-x/AMD-V ativo na BIOS e Hyper-V desativado |
| `Cannot connect to installer daemon` | Placa de rede PCnet não suportada | Trocar para Intel PRO/1000 MT Desktop (82540EM) |

## Estrutura do repositório

- `README.md`
- `01-pfsense-setup/`
- `02-firewall-rules/`
- `03-waf-modsecurity/`
- `04-siem-wazuh/`
- `evidence/` (saídas do Nmap, logs e capturas)
- `docs/` (diagramas e tabelas de IP)

## Boas práticas adotadas

- Rede totalmente isolada (NAT + redes internas), sem modo Bridged.
- Scans e ataques executados somente dentro das redes do laboratório.
- Senhas, chaves e configurações sensíveis removidas antes de publicar.
- Snapshots das VMs antes de mudanças importantes.

## Referências

- [Documentação do pfSense](https://docs.netgate.com/pfsense/en/latest/)
- [Documentação do Wazuh](https://documentation.wazuh.com/)
- [OWASP Core Rule Set](https://coreruleset.org/)
- [MITRE ATT&CK](https://attack.mitre.org/)

## Autor

[Getulio Coelho Oliveira]
