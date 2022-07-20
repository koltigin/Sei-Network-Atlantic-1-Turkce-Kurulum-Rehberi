# Sei Güncellemesi - 21.07.2022
1.0.6beta-val-count-fix güncellemesi.

## Root Ana Sayfasına Giriş Yapıyoruz
```shell
sudo su
cd $HOME
```
## sei-chain Klasörünü Siliyoruz
```shell
rm sei-chain -rf
```
## Yeni Versiyonu İndiriyoruz
```shell
git clone https://github.com/sei-protocol/sei-chain.git
```
## sei-chain Klasörüne Giriş Yapıyoruz
```shell
cd sei-chain
```
## Node'u Durduruyoruz
```shell
systemctl stop seid
```
## Yeni Versionu Yüklüyoruz
```shell
git checkout master && git pull
git checkout 1.0.6beta-val-count-fix
make install
```
## Seid Sistem Dosyasını Kopyalıyoruz
```shell
cp /root/go/bin/seid /usr/local/bin/seid
```
## Node'u Yeniden Başlatıyoruz
```shell
systemctl restart seid
```
## Logları Açıyoruz
```shell
journalctl -u seid -f -o cat
```
![01-sei-bilesenler](https://user-images.githubusercontent.com/102043225/180085705-a5951e2c-8fe8-4c56-aa3d-f6169d0706ae.png)
