# Sei 1.1.0beta Güncellemesi
836963. blok ulaşanlar 1.1.0beta güncellemesini yapabilir.

## Güncelleme Yapmadan Önce 
```shell
seid status 2>&1 | jq .SyncInfo
```
Yukarıdaki kodun çıktısında aşağıdaki resimdeki gibi  `"latest_block_height": "1862331",` yazıyorsa güncellemeyi yapabilirsiniz.

![sei-guncelleme-101-beta](https://user-images.githubusercontent.com/102043225/182709586-44018d01-551d-41b8-a85d-0724eb080a3f.JPG)

```shell
cd $HOME && rm $HOME/sei-chain -rf
git clone https://github.com/sei-protocol/sei-chain.git && cd $HOME/sei-chain
git checkout 1.1.0beta
make install
sudo systemctl daemon-reload
systemctl restart seid && journalctl -fu seid -o cat
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
git checkout 1.1.0beta
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
