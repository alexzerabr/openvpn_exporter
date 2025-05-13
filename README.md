# openvpn\_exporter

> Prometheus exporter para métricas de status do OpenVPN (clientes e servidores).

## 📦 Recursos

* Suporte a múltiplos arquivos de status (com wildcards)
* Compatível com OpenVPN 2.5+ (status v2 e v3)
* Gera métricas Prometheus:

  * Clientes: bytes, pacotes, etc.
  * Servidores: tráfego por usuário, conexões ativas
* Binários nativos para Linux, macOS, Windows, FreeBSD e ARM
* Imagem Docker mínima

## 🚀 Instalação

### Usando binário

1. Baixe em [Releases](https://github.com/alexzerabr/openvpn_exporter/releases).
2. Extraia e execute:

   ```bash
   ./openvpn_exporter --openvpn.status_paths /caminho/para/status.log
   ```

### Compilando do código

```bash
git clone https://github.com/alexzerabr/openvpn_exporter.git
cd openvpn_exporter
go build -ldflags "-X main.version=v1.0.0" -o openvpn_exporter main.go
```

## 🛠️ Uso

```bash
openvpn_exporter \
  --web.listen-address ":9176" \
  --web.telemetry-path "/metrics" \
  --openvpn.status_paths "/etc/openvpn/*.status" \
  --ignore.individuals=false
```

Acesse as métricas em `http://localhost:9176/metrics`.

## 🐳 Docker

```bash
docker run -d --name openvpn-exporter \
  -p 9176:9176 \
  -v /caminho/para/status.log:/etc/openvpn_exporter/status.log \
  alexzerabr/openvpn-exporter \
  --openvpn.status_paths /etc/openvpn_exporter/status.log
```

## 🎉 Releases

Arquivos disponíveis para:

* Linux (amd64, armv7, arm64)
* macOS (amd64, arm64)
* Windows (amd64)
* FreeBSD (amd64)

Confira em [Releases](https://github.com/alexzerabr/openvpn_exporter/releases).

## 📄 Licença

Apache License 2.0 — veja o [LICENSE](LICENSE) para detalhes.

