# Sei 1.1.0beta Güncellemesi
3223245. bloğa ulaşanlar 1.1.1beta güncellemesini yapabilir.

## Güncelleme Yapmadan Önce 
```shell
seid status 2>&1 | jq .SyncInfo
```
Yukarıdaki kodun çıktısında aşağıdaki resimdeki gibi  `"latest_block_height": "3223245",` yazıyorsa güncellemeyi yapabilirsiniz.
![sei-1 1 1-update](https://user-images.githubusercontent.com/102043225/185730874-f4ef2b53-95c4-49be-92e9-f3d023332505.JPG)

## Güncelleme 
```shell
cd $HOME && rm $HOME/sei-chain -rf
git clone https://github.com/sei-protocol/sei-chain.git && cd $HOME/sei-chain
git checkout 1.1.1beta
make install
sudo systemctl restart seid && journalctl -fu seid -o cat
```

## Hata Alırsanız
`status=2/INVALIDARGUMENT` şeklinde hata alırsanız `GO` yüklüyoruz.
```shell
ver="1.18.4"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
rm -rf /usr/local/go
tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm -rf "go$ver.linux-amd64.tar.gz"
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```

Daha sonra güncellemeyi yüklüyoruz.
```shell
cd $HOME && rm $HOME/sei-chain -rf
git clone https://github.com/sei-protocol/sei-chain.git && cd $HOME/sei-chain
git checkout 1.1.1beta
make install
```
```shell
go build -o build/seid ./cmd/seid
chmod +x ./build/seid && sudo mv ./build/seid /usr/local/bin/seid
mv ~/go/bin/seid /usr/local/bin/seid
mv $HOME/sei-chain $HOME/sei
systemctl restart seid
journalctl -fu seid -o cat
```
