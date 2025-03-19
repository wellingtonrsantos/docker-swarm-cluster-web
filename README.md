# Cluster Web com Docker Swarm e Nginx

![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Nginx](https://img.shields.io/badge/nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white)
![HTML5](https://img.shields.io/badge/html5-%23E34F26.svg?style=for-the-badge&logo=html5&logoColor=white)
![Bootstrap](https://img.shields.io/badge/bootstrap-%238511FA.svg?style=for-the-badge&logo=bootstrap&logoColor=white)
![JavaScript](https://img.shields.io/badge/javascript-%23323330.svg?style=for-the-badge&logo=javascript&logoColor=%23F7DF1E)

Este projeto demonstra a configuração de um cluster web utilizando Docker Swarm e Nginx como Load Balancer para distribuir requisições entre múltiplos containers Nginx rodando um site estático.

## 📌 Requisitos

- Docker e Docker Compose instalados
- Docker Swarm inicializado (`docker swarm init` caso ainda não esteja ativo)

## 📂 Estrutura do Projeto

```
.
├── docker-compose.yml    # Configuração dos serviços
├── nginx
│   ├── nginx.conf        # Configuração do Nginx Load Balancer
├── public                # Diretório com os arquivos estáticos
├── logs                  # Diretório para armazenar logs do Nginx
└── README.md
```

## 🚀 Como Rodar o Projeto

1. **Clone o repositório**
   ```sh
   git clone https://github.com/wellingtonrsantos/docker-swarm-cluster-web.git
   cd docker-swarm-cluster-web
   ```

2. **Inicialize o Docker Swarm (caso ainda não esteja ativo)**
   ```sh
   docker swarm init
   ```

3. **Suba os serviços**
   ```sh
   docker stack deploy -c docker-compose.yml web-cluster
   ```

4. **Acesse no navegador:**
    - http://localhost
    - Ou, se rodando em um servidor, substitua `localhost` pelo IP do servidor

## 🔍 Estrutura dos Serviços

- `web` (Nginx) → Servidores web estáticos (3 réplicas rodando o site)
- `load-balancer` (Nginx) → Distribui requisições entre os servidores `web`
- `cluster-network` → Rede overlay usada para comunicação entre os containers

## ⚙️ Configuração do Nginx Load Balancer

O Load Balancer está configurado no arquivo `nginx.conf`. Por padrão, o Nginx utiliza o algoritmo Round Robin para distribuir as requisições entre os containers web. Para utilizar o algoritmo IP Hash, é necessário comentar as linhas resolver, zone e server, e descomentar as linhas ip_hash e server, conforme abaixo:
```nginx
#resolver 127.0.0.11 valid=10s;

upstream backend {
    ip_hash;
    server web:80;
    #zone backend_zone 64k;
    #server web:80 resolve;
}
```

### Como ver os logs personalizados do Nginx:

Execute o seguinte comando:
```sh
docker exec -it $(docker ps -q -f name=web-cluster_load-balancer) tail -f /var/log/nginx/access.log
```

## ⏫ Escalando o Cluster

Para aumentar o número de réplicas dos servidores web, execute:
```sh
docker service scale web-cluster_web=5
```

## 🛑 Como Parar o Cluster

Para remover os serviços:
```sh
docker stack rm web-cluster
```

Para sair do modo Swarm (se não estiver mais usando):
```sh
docker swarm leave --force
```
