# Configuração da instância rodando tanto o [Fit-Sync](https://github.com/auto-natty/Fit-Sync) quanto o [wpp-bot](https://github.com/auto-natty/wpp-bot) em Ubuntu

## 1. atualizando índice de pacotes
```
sudo apt update
```

## 2. instalar git
```
sudo apt install git
```

Fazer configuração com chave ssh pra poder clonar os repositórios https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account

## 3. Configuração e instalação FitSync

### Pré requisitos

#### - SDKMan!
Estamos usando SDKMan para instalar java de maneira mais fácil (evitar ter que ficar mexendo em env e etc. ele gerencia tudo da instalação sozinho).

Primeiro, vamos precisar instalar o unzip e zip (requisito do SDKMan):
```
sudo apt install unzip
```
```
sudo apt install zip
```

Agora a [instalação do SDKMan!](https://sdkman.io/install) de fato:
```
curl -s "https://get.sdkman.io" | bash
```
```
source "/home/ubuntu/.sdkman/bin/sdkman-init.sh"
```

#### - Java e Maven

```
sdk install java 19.0.2-open
```
PS: Se fizer `sdk list java` não aparece a 19, mas funciona, confia.
```
sdk install maven
```

#### Fit-Sync

```
git clone git@github.com:auto-natty/Fit-Sync.git
```
```
cd Fit-Sync
```

Iniciando processo
```
mvn spring-boot:run &
```

## 4. Configuração e instalação wpp-bot

### Pré requisitos

#### - Instalar nvm

Conforme [artigo recomendado da AWS](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/setting-up-node-on-ec2-instance.html
), vamos usar o nvm como gerenciador de instalação do Node (mesma coisa que o SDKMan!, facilita a instalação)

:warning:Ver se não tem uma versão mais nova na hora de instalar o **nvm**
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
```

```
. ~/.nvm/nvm.sh
```

#### - Instalar Node.js e npm
```
nvm install --lts
```

#### wpp-bot

```
git clone git@github.com:auto-natty/wpp-bot.git
```
```
cd wpp-bot/
```

```
npm install
```

Caso dê problema na instalação do pupeteer por conta do chromium, pode fazer a instalação dele com o comando abaixo e depois tentar novamente o de cima
```
sudo apt install chromium-browser
```

#### Instalar e configurar [pm2 (gerenciador de processos, vai manter o bot em pé e ajuda em mais algumas coisas como monitoramento) ](https://pm2.keymetrics.io/docs/usage/quick-start/)

```
npm install pm2@latest -g
```
```
pm2 start bin/server.js --name wpp-bot --log logs.txt --time
```

Nesse momento, o bot já vai começar a rodar. Se necessário escanear o QR code para logar no whats, pode ser acessado nos logs (`$ pm2 logs --lines 100`)

#### - Configurando pro pm2 subir sozinho ao subir a instância do ec2
```
pm2 startup
```
```
sudo env PATH=$PATH:/home/ubuntu/.nvm/versions/node/v18.17.1/bin /home/ubuntu/.nvm/versions/node/v18.17.1/lib/node_modules/pm2/bin/pm2 startup systemd -u ubuntu --hp /home/ubuntu
```
