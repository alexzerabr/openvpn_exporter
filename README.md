# Exportador Prometheus para OpenVPN

Fork do projeto original com suporte ao OpenVPN 2.5+, suporte a curingas (wildcards) para arquivos de status, imagem Docker e compatibilidade com sistemas Unix como FreeBSD.

---

Este repositório fornece o código de um exportador simples de métricas Prometheus para o [OpenVPN](https://openvpn.net/). Atualmente, ele pode analisar arquivos gerados pela opção `--status` do OpenVPN, nos seguintes formatos:

- Estatísticas de cliente,
- Estatísticas de servidor com `--status-version 2` (separado por vírgulas),
- Estatísticas de servidor com `--status-version 3` (separado por tabulação).

É comum executar múltiplas instâncias do OpenVPN em um único sistema (por exemplo, vários servidores, vários clientes ou uma mistura dos dois). Este exportador pode ser configurado para coletar e exportar o status de **vários arquivos de status** usando a flag de linha de comando `-openvpn.status_paths`. Os caminhos devem ser separados por vírgula. As métricas de todos os arquivos de status são exportadas via porta TCP 9176.

Consulte a função `main()` desta ferramenta para uma lista completa de flags disponíveis.

---

## 📊 Exemplo de métricas expostas

### Estatísticas de clientes

Para arquivos de status de clientes, o exportador gera métricas como estas:

```
openvpn_client_auth_read_bytes_total{status_path="..."} 3.08854782e+08
openvpn_client_post_compress_bytes_total{status_path="..."} 4.5446864e+07
openvpn_client_post_decompress_bytes_total{status_path="..."} 2.16965355e+08
openvpn_client_pre_compress_bytes_total{status_path="..."} 4.538819e+07
openvpn_client_pre_decompress_bytes_total{status_path="..."} 1.62596168e+08
openvpn_client_tcp_udp_read_bytes_total{status_path="..."} 2.92806201e+08
openvpn_client_tcp_udp_write_bytes_total{status_path="..."} 1.97558969e+08
openvpn_client_tun_tap_read_bytes_total{status_path="..."} 1.53789941e+08
openvpn_client_tun_tap_write_bytes_total{status_path="..."} 3.08764078e+08
openvpn_status_update_time_seconds{status_path="..."} 1.490092749e+09
openvpn_up{status_path="..."} 1
```

### Estatísticas de servidor

Para arquivos de status de servidor (versão 2 ou 3), o exportador gera métricas como:

```
openvpn_server_client_received_bytes_total{common_name="...",connection_time="...",real_address="...",status_path="...",username="...",virtual_address="..."} 139583
openvpn_server_client_sent_bytes_total{common_name="...",connection_time="...",real_address="...",status_path="...",username="...",virtual_address="..."} 710764
openvpn_server_route_last_reference_time_seconds{common_name="...",real_address="...",status_path="...",virtual_address="..."} 1.493018841e+09
openvpn_status_update_time_seconds{status_path="..."} 1.490089154e+09
openvpn_up{status_path="..."} 1
openvpn_server_connected_clients 1
```

---

## 🚀 Uso

Exemplo de execução:

```sh
openvpn_exporter -openvpn.status_paths /etc/openvpn/openvpn-status.log
```

### Flags suportadas

| Flag                        | Descrição                                                                 |
|----------------------------|---------------------------------------------------------------------------|
| `-openvpn.status_paths`    | Caminhos dos arquivos de status do OpenVPN (suporta múltiplos, separados por vírgula ou curinga `*`) |
| `-web.listen-address`      | Endereço onde o exportador irá escutar (padrão `:9176`)                   |
| `-web.telemetry-path`      | Caminho HTTP onde as métricas serão expostas (padrão `/metrics`)          |
| `-ignore.individuals`      | Ignora métricas individuais de clientes (padrão `false`)                  |

---

## 🐳 Docker

Para usar com Docker, monte seu arquivo de status para `/etc/openvpn_exporter/server.status`:

```sh
docker run -p 9176:9176 \
  -v /caminho/para/seu/status.log:/etc/openvpn_exporter/server.status \
  openvpn-exporter -openvpn.status_paths /etc/openvpn_exporter/server.status
```

As métricas estarão disponíveis em:

```
http://localhost:9176/metrics
```

---

## 📦 Binário standalone

Você pode baixar binários pré-compilados na página de [releases](https://github.com/alexzerabr/openvpn_exporter/releases).

Ou compilá-lo você mesmo:

```sh
git clone https://github.com/alexzerabr/openvpn_exporter
cd openvpn_exporter
go build
./openvpn_exporter -openvpn.status_paths /caminho/para/seu/status.log
```

---

## 🖥️ Compatibilidade com FreeBSD

Este exportador funciona corretamente no **FreeBSD**, desde que:

- O OpenVPN esteja configurado com `--status` e `status-version 2`
- O status seja escrito em um local legível pelo usuário que executa o exportador
- O Go esteja instalado para compilação local do binário

Exemplo de configuração no `openvpn.conf`:

```conf
status /var/tmp/openvpn/status.log
status-version 2
```

---

## 🧩 Integração com Prometheus

No seu `prometheus.yml`:

```yaml
- job_name: 'openvpn'
  static_configs:
    - targets: ['localhost:9176']
```

Reinicie o Prometheus após editar.

---

## 📦 Download

Você pode baixar os binários prontos na seção [Releases](https://github.com/alexzerabr/openvpn_exporter/releases).

### Plataformas suportadas

| Plataforma         | Arquivo                                  |
|--------------------|-------------------------------------------|
| Linux (amd64)      | `openvpn_exporter_linux_amd64.tar.gz`    |
| Linux (ARMv7)      | `openvpn_exporter_rpi_armv7.tar.gz`      |
| Linux (ARM64)      | `openvpn_exporter_linux_arm64.tar.gz`    |
| Windows (amd64)    | `openvpn_exporter_windows_amd64.zip`     |
| macOS (amd64)      | `openvpn_exporter_darwin_amd64.tar.gz`   |
| macOS (ARM64)      | `openvpn_exporter_darwin_arm64.tar.gz`   |
| FreeBSD (amd64)    | `openvpn_exporter_freebsd_amd64.tar.gz`  |

### Executando o Exporter

```bash
./openvpn_exporter --web.listen-address=":9176" --openvpn.status_paths="/etc/openvpn/openvpn-status.log"


## 📌 Observações finais

- Projeto útil mesmo após ter sido arquivado
- Leve, simples e funcional
- Ideal para ambientes que utilizam OpenVPN Community Edition

---

💡 **Sugestão:** Se você mantiver múltiplos servidores OpenVPN, utilize curinga como `-openvpn.status_paths "/var/tmp/openvpn/*.log"` para exportar tudo de uma vez.
