<a href="https://aptos.dev">
	<img width="100%" src="./.assets/aptos_banner.png" alt="Aptos Banner" />
</a>

---

[![Aptos Rust Crate Documentation (main)](https://img.shields.io/badge/docs-main-59f)](https://aptos-labs.github.io/aptos-core/)
[![License](https://img.shields.io/badge/license-Apache-green.svg)](LICENSE)
[![Lint+Test](https://github.com/aptos-labs/aptos-core/actions/workflows/lint-test.yaml/badge.svg)](https://github.com/aptos-labs/aptos-core/actions/workflows/lint-test.yaml)
[![grcov](https://img.shields.io/badge/Coverage-grcov-green)](https://ci-artifacts.aptoslabs.com/coverage/unit-coverage/latest/index.html)
[![test history](https://img.shields.io/badge/Test-History-green)](https://ci-artifacts.aptoslabs.com/testhistory/aptos/aptos/auto/ci-test.yml/index.html)
[![Automated Issues](https://img.shields.io/github/issues-search?color=orange&label=Automated%20Issues&query=repo%3Aaptos%2Faptos%20is%3Aopen%20author%3Aapp%2Fgithub-actions)](https://github.com/aptos-labs/aptos-core/issues/created_by/app/github-actions)
[![Discord chat](https://img.shields.io/discord/945856774056083548?style=flat-square)](https://discord.gg/aptoslabs)

Aptos-core strives towards being the safest and most scalable layer one blockchain solution. Today, this powers the Aptos Devnet, tomorrow Mainnet in order to create universal and fair access to decentralized assets for billions of people.

## Getting Started

* [Aptos Labs](https://aptoslabs.com/)
* [Aptos Developer Network](https://aptos.dev)
* [Getting Started](https://aptos.dev/guides/getting-started)
* [Life of a Transaction](https://aptos.dev/guides/basics-life-of-txn)
* Join us on the [Aptos Discord](https://discord.gg/aptoslabs).

## Contributing

You can learn more about contributing to the Aptos project by reading our [Contribution Guide](https://github.com/aptos-labs/aptos-core/blob/main/CONTRIBUTING.md) and by viewing our [Code of Conduct](https://github.com/aptos-labs/aptos-core/blob/main/CODE_OF_CONDUCT.md).

Aptos Core is licensed as [Apache 2.0](https://github.com/aptos-labs/aptos-core/blob/main/LICENSE).
---------------






Установка ноды:
========================

### 1. Обновление пакетов и системы:
```
sudo apt update && sudo apt upgrade -y
```

### 2. Установка зависимостей
```
sudo apt-get install jq unzip -y
```
### 3. Установка docker
```
sudo apt-get install ca-certificates curl gnupg lsb-release -y
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io -y
```

### 4. Установка docker compose
```
docker_compose_version=$(wget -qO- https://api.github.com/repos/docker/compose/releases/latest | jq -r ".tag_name")
sudo wget -O /usr/bin/docker-compose "https://github.com/docker/compose/releases/download/${docker_compose_version}/docker-compose-`uname -s`-`uname -m`"
sudo chmod +x /usr/bin/docker-compose
```

### 5. Установка aptos CLI
```
curl https://sh.rustup.rs -sSf | sh
source $HOME/.cargo/env
sudo apt install build-essential pkg-config openssl libssl-dev libclang-dev
cargo install --git https://github.com/aptos-labs/aptos-core.git aptos --branch testnet
```

### 6. Создание каталога ноды Aptos, указание имени пользователя
```
export WORKSPACE=testnet
export USERNAME=<ИМЯ НОДЫ>
mkdir ~/$WORKSPACE
cd ~/$WORKSPACE
```

### 7. Скачивание конфигурационных файлов
```
wget -qO docker-compose.yaml https://raw.githubusercontent.com/aptos-labs/aptos-core/main/docker/compose/aptos-node/docker-compose.yaml
wget -qO validator.yaml https://raw.githubusercontent.com/aptos-labs/aptos-core/main/docker/compose/aptos-node/validator.yaml
```

### 8. Генерация ключей
```
aptos genesis generate-keys --output-dir ~/$WORKSPACE/keys
```

### 9. Конфигурация Валидатора, задание ip aдреса
```
cd ~/$WORKSPACE
aptos genesis set-validator-configuration \
    --local-repository-dir ~/$WORKSPACE \
    --username $USERNAME \
    --owner-public-identity-file ~/$WORKSPACE/keys/public-keys.yaml \
    --validator-host <IP НОДЫ>:6180 \
    --stake-amount 100000000000000
```

**Пример с указанием ip и адресом full node:**
```
aptos genesis set-validator-configuration \
    --local-repository-dir ~/$WORKSPACE \
    --username $USERNAME \
    --owner-public-identity-file ~/$WORKSPACE/keys/public-keys.yaml \
    --validator-host 35.232.235.205:6180 \
    --full-node-host 34.135.169.144:6182 \
    --stake-amount 100000000000000
```
### 10. Создание файла-шаблона, который определяет ноду в сете валидаторов
```
aptos genesis generate-layout-template --output-file ~/$WORKSPACE/layout.yaml
```
### 11. Редактирование созданого шаблона, указание имя ноды, добавление root_key и chain_id:
```
nano ~/$WORKSPACE/layout.yaml

root_key: "D04470F43AB6AEAA4EB616B72128881EEF77346F2075FFE68E14BA7DEBD8095E"
users: ["<ИМЯ НОДЫ>"]
chain_id: 43
allow_new_validators: false
epoch_duration_secs: 7200
is_test: true
min_stake: 100000000000000
min_voting_threshold: 100000000000000
max_stake: 100000000000000000
recurring_lockup_duration_secs: 86400
required_proposer_stake: 100000000000000
rewards_apy_percentage: 10
voting_duration_secs: 43200
voting_power_increase_limit: 20
```
### 12. Скачивание фреймворка Aptos
```
wget https://github.com/aptos-labs/aptos-core/releases/download/aptos-framework-v0.3.0/framework.mrb -P ~/$WORKSPACE
```
### 13. Компиляция genesis.blob и waypoint.tx
```
aptos genesis generate-genesis --local-repository-dir ~/$WORKSPACE --output-dir ~/$WORKSPACE
```
### 14. Запуск docker
```
docker-compose up -d
```


**Полезные команды:**
**Проверка состояния ноды через терминал:
```
curl -s 'http://aptos-nhc.nod.run:20121/check_node?node_url=http://<IP НОДЫ>&baseline_configuration_name=ait3_validator&api_port=80'
```
---------------

**Посмотреть открытые порты:
```
sudo apt install lsof && sudo lsof -i -P -n | grep LISTEN
```
**Для ноды валидатора должны быть открыты 80, 6180 и 9101

**Для full node должны быть открыты 80/8080, 6182, 9101





















