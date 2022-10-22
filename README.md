## zeeka_otmorozky
# Чистая установка только клиента !!!
# Установка узла Zeeka
### Обновляем систему
```
sudo apt update && sudo apt upgrade -y
```
### Устанавливаем дополнительные пакеты
```
sudo apt install wget jq git libssl-dev cmake -y
```
### Устанавливаем RUST (нажимаем 1 и ентер)
```
sudo apt install curl build-essential gcc make -y
curl https://sh.rustup.rs -sSf | sh -s -- -y
source ~ /.profilesource ~ /.cargo/env
```
### Устанавливаем ноду из репозитория (chaos-testnet):
```
cd

git clone https://github.com/zeeka-network/bazuka

cd bazuka

git pull origin master

cargo install --path .
```
### Задаем переменные, первую переменную он подставит сам, вторую задайте сами.
```
ZEEKAIP=`wget -qO- eth0.me`
ZEEKADISCORD=вставьте ваш ник из дискорда
```
### Сохраняем переменные
```
echo "export ZEEKAIP="${ZEEKAIP}"" >> $HOME/.bash_profile
echo "export ZEEKADISCORD="${ZEEKADISCORD}"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
### Сгенерируйте сид фразу в любом кошельке (12 или 24 слова)!!!
```
cd
bazuka init --seed 'сюда вставляем вашу сгенерированную сид фразу в одинарных кавычках' --network chaos --node 127.0.0.1:8765
```
### Создайте служебный файл для запуска узла (копируем все целиком) ничего менять не надо!
```
sudo tee <<EOF >/dev/null /etc/systemd/system/zeekad.service
[Unit]
Description=Zeeka node
After=network.target

[Service]
User=$USER
ExecStart=`RUST_LOG=info which bazuka` node --listen 0.0.0.0:8765 --external $ZEEKAIP:8765 --network chaos --db /root/.bazuka-chaos --bootstrap 195.201.222.205:8765 --bootstrap 34.132.176.19:8765 --discord-handle "$ZEEKADISCORD"
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
### Запускаем сервис и добавляем его в автозагрузку (можно вводить эти 3 команды поочередно. не должно быть ошибок)
```
sudo systemctl daemon-reload
sudo systemctl enable zeekad
sudo systemctl restart zeekad
```
### Смотрим логи
```
sudo journalctl -f -u zeekad
```
Oct 22 09:42:56 n9bfb4e systemd[1]: zeekad.service: Succeeded.

Oct 22 09:42:56 n9bfb4e systemd[1]: Stopped Zeeka node.

Oct 22 09:44:23 n9bfb4e systemd[1]: Started Zeeka node.

Oct 22 09:44:23 n9bfb4e bazuka[2746074]: Bazuka! v0.2.2

Oct 22 09:44:23 n9bfb4e bazuka[2746074]: Listening: 0.0.0.0:8765

Oct 22 09:44:23 n9bfb4e bazuka[2746074]: Internet endpoint: http://195.3.222.188:8765

Oct 22 09:44:23 n9bfb4e bazuka[2746074]: Wallet address: 0xa930ecd233f21f50ec2af25815033039a9113c19d106fe1903955d2774e05919

Oct 22 09:44:23 n9bfb4e bazuka[2746074]: Wallet zk address: 0x33b038b14af23c6148982c94983fe815e3ccbdb47969c05754f691d6f5af3e21

Oct 22 09:44:24 n9bfb4e bazuka[2746074]: Height: 277 Outdated states: 0 Timestamp: 1666431864 Active nodes: 0 Tx Pool: 0 Tx/Zk Pool: 0 Zk Pool: 0 Balance: 0.0ℤ

Oct 22 09:44:34 n9bfb4e bazuka[2746074]: Height: 277 Outdated states: 0 Timestamp: 1666410430 Active nodes: 28 Tx Pool: 0 Tx/Zk Pool: 0 Zk Pool: 0 Balance: 0.0ℤ

Oct 22 09:44:44 n9bfb4e bazuka[2746074]: Height: 277 Outdated states: 0 Timestamp: 1666410439 Active nodes: 58 Tx Pool: 0 Tx/Zk Pool: 0 Zk Pool: 0 Balance: 0.0ℤ

Oct 22 09:44:55 n9bfb4e bazuka[2746074]: Height: 277 Outdated states: 0 Timestamp: 1666410450 Active nodes: 81 Tx Pool: 0 Tx/Zk Pool: 0 Zk Pool: 0 Balance: 0.0ℤ

Oct 22 09:45:08 n9bfb4e bazuka[2746074]: Height: 277 Outdated states: 0 Timestamp: 1666410460 Active nodes: 116 Tx Pool: 0 Tx/Zk Pool: 0 Zk Pool: 0 Balance: 0.0ℤ

### проверяем статус узла
```
bazuka status
```

## Обновление узла
```
sudo systemctl stop zeekad
cd $HOME/bazuka
git pull origin master
cargo update
cargo install --path .
sudo systemctl restart zeekad
sudo journalctl -f -u zeekad
```
## Удаление ноды
```
sudo systemctl stop zeekad && sudo systemctl disable zeekad
rm -rf $HOME/bazuka /root/.bazuka-debug /root/.bazuka-chaos rm ~/.bazuka.yaml /usr/bin/bazuka /etc/systemd/system/zeekad.service 
sudo systemctl daemon-reload
```

