# SEI Network Atlantic-1 Türkçe Node Kurulum Rehberi

![01-sei-bilesenler](https://user-images.githubusercontent.com/102043225/180085705-a5951e2c-8fe8-4c56-aa3d-f6169d0706ae.png)

## Sistem Gereksinimleri
* **Minimum**
  * 8 GB RAM
  * 100 GB NVME SSD
  * 3.2 GHz x4 CPU

* **Önerilen**
  * 16 GB RAM
  * 500 GB NVME SSD
  * 4.2 GHz x6 CPU 
  
## Güncellemeler
 - [1.0.6beta-val-count-fix Güncellemesi](https://github.com/koltigin/Sei-Network-Atlantic-1-Turkce-Kurulum-Rehberi/blob/main/Sei-Guncelleme-1.0.6beta.md-val-count-fix.md)
 - [Sei 1.0.7beta-postfix Güncellemesi](https://github.com/koltigin/Sei-Network-Atlantic-1-Turkce-Kurulum-Rehberi/blob/main/Sei-Guncelleme-1.0.7beta-postfix.md)
 - [Sei 1.1.0beta Güncellemesi](https://github.com/koltigin/Sei-Network-Atlantic-1-Turkce-Kurulum-Rehberi/blob/main/Sei-Guncelleme-1.1.0beta.md)
 - [Sei 1.1.1beta Güncellemesi](https://github.com/koltigin/Sei-Network-Atlantic-1-Turkce-Kurulum-Rehberi/blob/main/Sei-Guncelleme-1.1.1beta.md)
 - [Sei 1.1.1beta-internal Güncellemesi](https://github.com/koltigin/Sei-Network-Atlantic-1-Turkce-Kurulum-Rehberi/blob/main/Sei-Guncelleme-1.1.2beta-internal.md)
 
# Sei Network Kurulumu

## Root Yetkisi Alma Ve Root Dizinine Geçme
```shell
sudo su
cd /root
```

## Sistemi Güncelleme

```shell
apt update && sudo apt upgrade -y
```

## Gerekli Kütüphanelerin Kurulması
```shell
apt install make clang pkg-config libssl-dev build-essential git jq ncdu bsdmainutils htop -y < "/dev/null"
```

## Go Kurulumu
```shell
ver="1.18.2"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
rm -rf /usr/local/go
tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export GOROOT=/usr/local/go" >> $HOME/.bash_profile
echo "export GOPATH=$HOME/go" >> $HOME/.bash_profile
echo "export GO111MODULE=on" >> $HOME/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```

## Değişkenleri Yükleme
* Aşağıda değiştirmeniz gereken yerler belirtilmiştir.
`$NODENAME` validator adınız
`$WALLET` sei cüzdan adınız
```shell
echo "export NODENAME=$NODENAME"  >> $HOME/.bash_profile
echo "export WALLET=$WALLET" >> $HOME/.bash_profile
echo "export CHAIN_ID=atlantic-1" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Sei Dosyaların İndirilmesi ve Kurulum
```shell
git clone --depth 1 --branch 1.0.6beta https://github.com/sei-protocol/sei-chain.git
cd sei-chain && make install
go build -o build/seid ./cmd/seid
chmod +x ./build/seid && sudo mv ./build/seid /usr/local/bin/seid
```

## Uygulamayı Yapılandırma
```shell
seid config chain-id $CHAIN_ID
seid config keyring-backend file
```

## Uygulamayı Başlatma
```shell
seid init $NODENAME --chain-id $CHAIN_ID
```

## Genesis ve Addrbook Dosyalarının İndirilmesi
```shell
curl https://raw.githubusercontent.com/sei-protocol/testnet/master/sei-incentivized-testnet/genesis.json > ~/.sei/config/genesis.json
curl https://raw.githubusercontent.com/sei-protocol/testnet/master/sei-incentivized-testnet/addrbook.json > ~/.sei/config/addrbook.json
```
## Minimum GAS Ücretinin Ayarlanması
```shell
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0usei\"/" $HOME/.sei/config/app.toml
```
## SEED ve PEERS Ayarlanması
```shell
SEEDS=""
PEERS="585727dac5df8f8662a8ff42052a9584a1f7ee95@165.22.25.77:26656,2f047e234cb8b99fe8b9fee0059a5bc45042bc97@95.216.84.188:26656,bab4849cf3918c37b04cd3714984d1765616a4b2@49.12.76.255:36656,ab955dc7f70d8613ab4b554868d4658fd70b797b@217.79.180.194:26656,39c4bcaded0d1d886f2788ae955f1939406f3e7d@65.108.198.54:26696,9db58dba3b6354177fb428caccf5167c616ad4a1@167.235.28.18:26656,2f2804434afda302c86eb89eca27503e49a8a260@65.21.131.215:26696,38b4d78c7d6582fb170f6c19330a7e37e6964212@194.163.189.114:46656,7c5ee0d66a15013f0a771055378c5316331f17ba@95.216.101.84:25646,6f71bcbe347069fc4df9b607f6b843226e8deb71@95.217.221.201:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.sei/config/config.toml
```
## Pruning'i Ayarlama
```shell
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"

sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.sei/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.sei/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.sei/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.sei/config/app.toml
```

## Prometheus'u Aktif Etme
```shell
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.sei/config/config.toml
```

## Zincir Verilerini Sıfırlama
```shell
seid tendermint unsafe-reset-all --home $HOME/.sei
```

## Servis Dosyası Oluşturma
```shell
tee $HOME/seid.service > /dev/null <<EOF
[Unit]
Description=seid
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which seid) start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

sudo mv $HOME/seid.service /etc/systemd/system/
```

## Servisi Başlatma
```shell
sudo systemctl daemon-reload
sudo systemctl enable seid
sudo systemctl restart seid
```

## Logları Kontrol Etme
```shell
journalctl -u seid -f
```

## Cüzdan Oluşturma
### Yeni Cüzdan Oluşturma
`$WALLET` bölümünü değiştirmiyoruz kurulumun başında cüzdanımıza isim belirledik.
```shell
seid keys add $WALLET --keyring-backend file
```

### Var Olan Cüzdanı İçeri Aktarma
```shell
seid keys add $WALLET --recover
```

## Faucet / Musluk
Test token almak için Discord'da [#atlantic-1-faucet](https://discord.gg/DPE4UHrR4k) kanalından şu şekilde `!faucet send CUZDAN_ADRESINIZ` mesaj atıyoruz.

## Senkronizasyonu Kontrol Etme
`false` çıktısı almaldıkça bir sonraki yani validator oluşturma adımına geçmiyoruz.
```shell
seid status 2>&1 | jq .SyncInfo
```

## Validator Oluşturma
* Aşağıdaki komutta aşağıda berlittiğim yerler dışında bir değişikli yapmanız gerekmez;
  * identity : buraya `https://keybase.io/` sitesine üye olarak size verilen kimlik numaranızı yazıyorsunuz.
  * details : kendiniz hakkında bilgiler verebilir ya da `Rues Community Supporter` yazabilirsiniz.
  * website : Varsa bir siteniz yazabilirsiniz ya da `https://forum.rues.info` olarak bırakabilirsiniz.
  
```shell
seid tx staking create-validator \
--moniker="NODENAME" \
--identity=XXXX1111XXXX1111 \
--details="Rues Community Supporter" \
--website="https://forum.rues.info" \
--amount=950000usei \
--pubkey=$(seid tendermint show-validator) \
--chain-id=CHAIN_ID \
--commission-max-change-rate=0.01 \
--commission-max-rate=0.20 \
--commission-rate=0.10 \
--min-self-delegation=1 \
--from=WALLET \
--yes
```

## Peer Paylaşımı
Bu kod ile `echo "$(seid tendermint show-node-id)@$(curl ifconfig.me):26656"` PEER adresinizi öğrenebilirsiniz
[Rues Forum](https://forum.rues.info/index.php?threads/sei-network-atlantic-1-peer-listesi-olusturuyoruz-sizde-destek-olabilirsiniz.2144/)'dan paylaşım yapabilirsiniz.

## Validator Linkinizi Paylaşma
Sei Discord [#validator-form-submissions](https://discord.gg/DPE4UHrR4k) kanalından validatorumuze [explorer](https://sei.explorers.guru/) linkini gönderiyoruz.

## Exproler
https://sei.explorers.guru/


## DAHA FAZLA SORUNUZ VARSA SEİ TÜRKİYE TELEGRAM GRUBU:

[Sei Türkiye Resmi Telegram Sayfası](https://t.me/SeiNetworkTurkish)

## FAYDALI KOMUTLAR

### Logları Kontrol Etme 
```shell
journalctl -fu seid -o cat
```

### Sistemi Başlatma
```shell
systemctl start seid
```

### Sistemi Durdurma
```shell
systemctl stop seid
```

### Sistemi Yeniden Başlatma
```shell
systemctl restart seid
```

### Node Senkronizasyon Durumu
```shell
seid status 2>&1 | jq .SyncInfo
```

### Validator Bilgileri
```shell
seid status 2>&1 | jq .ValidatorInfo
```

### Node Bilgileri
```shell
seid status 2>&1 | jq .NodeInfo
```

### Node ID Öğrenme
```shell
seid tendermint show-node-id
```

### Node IP Adresini Öğrenme
```shell
curl icanhazip.com
```

### Peer Adresinizi Öğrenme
```shell
echo "$(seid tendermint show-node-id)@$(curl ifconfig.me):26656"
```

### Cüzdanların Listesine Bakma
```shell
seid keys list
```

### Cüzdanı İçeri Aktarma
```shell
seid keys add CUZDAN_ADI --recover
```

### Cüzdanı Silme
```shell
seid keys delete CUZDAN_ADI

### Cüzdan Bakiyesine Bakma
```shell
seid query bank balances CUZDAN_ADRESI
```

### Bir Cüzdandan Diğer Bir Cüzdana Transfer Yapma
```shell
seid tx bank send CUZDAN_ADRESI GONDERILECEK_CUZDAN_ADRESI 100000000usei
```

### Proposal Oylamasına Katılma
```shell
seid tx gov vote 1 yes --from CUZDAN_ADI --chain-id=CHAIN_ID 
```

### Validatore Stake Etme / Delegate Etme
```shell
seid tx staking delegate VALOPER_ADRESI 100000000usei --from=CUZDAN_ADI --chain-id=CHAIN_ID  --gas=auto
```

### Mevcut Validatorden Diğer Validatore Stake Etme / Redelegate Etme
```shell
seid tx staking redelegate <MevcutValidatorAdresi> <StakeEdilecekYeniValidatorAdresi> 100000000usei --from=WALLET --chain-id=CHAIN_ID  --gas=auto
```

### Ödülleri Çekme
```shell
seid tx distribution withdraw-all-rewards --from=CUZDAN_ADI --chain-id=CHAIN_ID  --gas=auto
```

### Komisyon Ödüllerini Çekme
```shell
seid tx distribution withdraw-rewards VALIDATOR_ADRESI --from=CUZDAN_ADI --commission --chain-id=CHAIN_ID 
```

### Validator İsmini Değiştirme
```shell
seid tx staking edit-validator \
--moniker=YENI_NODE_ADI \
--chain-id=CHAIN_ID  \
--from=WALLET
```

### Validatoru Jail Durumundan Kurtarma 
```shell
seid tx slashing unjail \
  --broadcast-mode=block \
  --from=CUZDAN_ADI \
  --chain-id=CHAIN_ID  \
  --gas=auto
```

### Node'u Tamamen Silme 
```shell
sudo systemctl stop seid
sudo systemctl disable seid
rm /etc/systemd/system/seid.service
sudo systemctl daemon-reload
cd $HOME
rm .sei sei-chain -rf
rm $(which seid) -rf
```


# Hesaplar

[Anatolian Team](https://anatolianteam.com)

[Twitter](https://twitter.commehmetkoltigin)

[Medium](https://medium.com/@mehmetkoltigin)

[YouTube](https://www.youtube.com/channel/UCmLgaftx5e38BE0E7gpY2dA)

[Discord](https://discordapp.com/users/837933958280904737)

[Telegram](https://t.me/mehmetkoltigin)

