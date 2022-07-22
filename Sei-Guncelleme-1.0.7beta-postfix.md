# Sei 1.0.7beta-postfix Güncellemesi
836963. blok ulaşanlar 1.07beta-postfix güncellemesini yapabilir.

## Güncelleme Yapmadan Önce 
```shell
seid status 2>&1 | jq .SyncInfo
```
Yukarıdaki kodun çıktısında aşağıdaki resimdeki gibi  `"latest_block_height": "836963",` yazıyorsa güncellemeyi yapabilirsiniz.

![image](https://user-images.githubusercontent.com/102043225/180481222-7212f03e-3b33-4ba6-861a-844ac9d226f0.png)

```shell
cd $HOME
rm $HOME/sei-chain -rf
git clone https://github.com/sei-protocol/sei-chain.git
cd $HOME/sei-chain
git checkout 1.0.7beta-postfix
make install
mv ~/go/bin/seid /usr/local/bin/seid
systemctl restart seid && journalctl -fu seid -o cat
```
