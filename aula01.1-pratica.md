## Subindo imagem para o Docker Hub

#### Primeiro Passo

Crie sua conta no seguinte link [Docker Hub](https://hub.docker.com).

#### Segundo Passo

Após a confirmação da criação do seu usuário. Você já pode se autenticar utilizando o terminal da sua máquina:

```sh
docker login
```

Informe seu usuário e senha, e você será autenticado com sucesso.

#### Terceiro Passo

A sua imagem deve seguir o seguinte padrão:

> seuusuario/nomedaimagem:versão
> - seuusuario: nome do seu usuário no Docker Hub
> - nomedaimagem: nome que você deseja deixar público para baixar
> - versão: o número da versão que você está enviando

Agora que sua imagem está no padrão correto, enviamos ela para o repositório do Docker Hub com o seguinte comando:

```sh
docker push seuusuario/nomedaimagem:versão
```

#### Quarto Passo

Acesse o Link [Docker Hub](https://hub.docker.com) e lá você irá conseguir visualizar o repositório que acabou de criar.

#### Quinto Passo
Para testarmos se conseguimos buscar nossa imagem no Docker Hub, vamos remover a imagem e baixá-la novamente.

```sh
docker rm -f nomecontainer # Utilizamos caso a imagem esteja em uso
docker rmi seuusuario/nomeimagem:versão
docker pull seuusuario/nomeimagem:versão
```
