# fullcycle-desafio-git
Implementação do desafio do módulo de Git do Curso Full Cycle 3.0 ministrado pelo Wesley Williams.


## 1. Baixe e instale o gitflow
``` shell
### Baixe e instale o gitflow
curl -sL  https://raw.githubusercontent.com/petervanderdoes/gitflow-avh/develop/contrib/gitflow-installer.sh -o gitflow-installer.sh
sudo bash gitflow-installer.sh install stable && rm -rf gitflow-installer.sh gitflow
```


## 2. Configure uma chave GPG
``` shell
echo -n "Informe seu nome e sobrenome: " && read USER_NAME
echo -n "Informe seu email: " && read USER_EMAIL
GPG_KEY_PASS=$(pwgen -cns 20 1 | tee ~/.gnupg/$(whoami)-gpg-key.pass)

gpg --list-secret-key --keyid-format long
cat << EOF > ~/.gnupg/$(whoami)-gpg-key-config.txt
    Key-Type: RSA
    Key-Length: 4096
    Expire-Date: 0
    Name-Real: ${USER_NAME}
    Name-Email: ${USER_EMAIL}
    Passphrase: ${GPG_KEY_PASS}
EOF
gpg --batch --gen-key ~/.gnupg/$(whoami)-gpg-key-config.txt
```

## 3. Adicione a chave GPG no Github
``` shell
GPG_KEY_ID=$(gpg --list-secret-keys --keyid-format long "John Doe" | grep sec | tail -1 | column -t | cut -d " " -f3 | cut -d"/" -f2)
gpg --armor --export ${GPG_KEY_ID} | xclip -sel clip

# 1. Acesse https://github.com/settings/gpg/new
# 2. Insira um titulo
# 3. Cole o conteúdo da sua chave GPG que já estará em sua área de transferência
# 4. Clique em "Adicionar chave GPG"
```

## 3. Configure a chave GPG no git
``` shell
echo "export GPG_TTY=$(tty)" >> .bashrc
git config --global user.signingkey ${GPG_KEY_ID}
git config --global tag.gpgsign true
```

## 4. Inicialize o gitflow
``` shell
git flow init
git --no-pager branch
```


## 5. Crie uma branch feature
``` shell
git flow feature start changelog

LASTEST_TAG=$(git --no-pager tag -l | sort -nr | head -n1 | tr -d "v")
CHANGELOG_TAG=$([ $(echo ${LASTEST_TAG} | wc -l) -gt 1 ] && echo ${LASTEST_TAG} || echo v0.1.0)

cat << EOF > CHANGELOG.md
# changelog

## [${CHANGELOG_TAG}] - $(date +'%Y-%m-%d')

### Added
- some feature
EOF

git commit -m "add changelog"
git log --show-signature -1
git flow feature finish changelog
```


## 6. Gere uma release
``` shell
git flow release start 0.1.0
git flow release finish 0.1.0
```


## Referências
- https://github.com/petervanderdoes/gitflow-avh/wiki/Installation
- https://keepachangelog.com/pt-BR/0.3.0/
- https://www.conventionalcommits.org/en/v1.0.0/
- https://github.com/embeddedartistry/templates
- https://semver.org/lang/pt-BR/