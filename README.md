<h1 align="center">Starknet Pathfinder Node Kurulumu

## StarkNet, StarkWare tarafından geliştirilen bir Ethereum ikincil katman çözümüdür.

## Merkeziyetsizliğe bir adım daha


### Pathfinder Düğümü için Donanım Tavsiyeleri

Optimum performans ve güvenilirlik için, bir Pathfinder düğümü çalıştırırken aşağıdaki donanım önerilir:

 - CPU: Intel Core i7-9700 veya AMD Ryzen 7 3700X
 - Bellek: 32GB
 - Depolama: 1TB SSD
 - Ağ: Gigabit Ethernet

   

<h1 align="center">Hazırlık

## Gereksinimler



  ## Docker’ın kurulu olduğundan emin olun. Ubuntu için, şu komutu kullanarak Docker’ı yükleyebilirsiniz:
  
  ```
sudo snap install docker
  ```

 ## Kurulum ve Çalıştırma
 ## Veri Dizini Hazırlama: Kalıcı dosyaların saklanacağı bir veri dizini oluşturun:
 
  ```
 mkdir -p $HOME/pathfinder
  ```

   ## Pathfinder Düğümünü Başlatma: Aşağıdaki komutla Pathfinder düğümünü Docker kullanarak çalıştırın:

  ```
 sudo docker run \
  --name pathfinder \
  --restart unless-stopped \
  --detach \
  -p 9545:9545 \
  --user "$(id -u):$(id -g)" \
  -e RUST_LOG=info \
  -e PATHFINDER_ETHEREUM_API_URL="https://goerli.infura.io/v3/<proje-id>" \
  -v $HOME/pathfinder:/usr/share/pathfinder/data \
  eqlabs/pathfinder
  ```
 

# Logları İzleme: Düğüm loglarını görüntülemek için:

 ```
  sudo docker logs -f pathfinder
   ```

 # Pathfinder Düğümünü Durdurma: Düğümü durdurmak için:

 ```
  sudo docker stop pathfinder
   ```

 Bu kurulum, Pathfinder düğümünün arka planda çalışmasını ve otomatik yeniden başlatma özelliğini sağlar.

 ## Pathfinder Docker Görüntüsünü Güncelleme

 Yeni bir Pathfinder sürümü mevcut olduğunda düğüm şu şekilde bir uyarı mesajı kaydedecektir:

  ```
WARN New pathfinder release available! Please consider updating your node! release=0.4.5
 ```


 ## Güncelleme Adımları

1-En Son Docker Görüntüsünü Çekme:

  ```
sudo docker pull eqlabs/pathfinder
 ```

2-Mevcut Container’ı Durdurup Kaldırma:


  ```
sudo docker stop pathfinder
sudo docker rm pathfinder
 ```

3-Yeni Görüntü ile Container’ı Yeniden Oluşturma:
 
 ```
sudo docker run \
  --name pathfinder \
  --restart unless-stopped \
  --detach \
  -p 9545:9545 \
  --user "$(id -u):$(id -g)" \
  -e RUST_LOG=info \
  -e PATHFINDER_ETHEREUM_API_URL="https://goerli.infura.io/v3/<proje-id>" \
  -v $HOME/pathfinder:/usr/share/pathfinder/data \
  eqlabs/pathfinder
 ```

 ## Docker Compose Kullanımı

 Alternatif olarak, docker-compose kullanılabilir.

 # Kurulum:

1-pathfinder klasörünü oluşturun:
  
  ```
mkdir -p pathfinder
 ```

2-docker-compose.yaml dosyasını bu klasörde oluşturun. Ardından:

 ```
docker-compose up -d
 ```

3-Logları kontrol edin:

 ```
docker-compose logs -f
 ```


  ## Veritabanı Anlık Görüntüleri (Snapshots)

Mainnet veya testnet ağları için tüm geçmişi yeniden senkronize etmek uzun sürebilir. Bu süreci hızlandırmak için, belirli bir blok seviyesine kadar olan tüm durum ve geçmişi içeren veritabanı anlık görüntü dosyalarını kullanabilirsiniz.

Veritabanı dosyaları Cloudflare R2 üzerinde barındırılmaktadır.

# Rclone Aracını Kullanarak İndirme: RClone yapılandırma dosyanıza aşağıdakileri ekleyin:

   ```
[pathfinder-snapshots]
type = s3
provider = Cloudflare
env_auth = false
access_key_id = 7635ce5752c94f802d97a28186e0c96d
secret_access_key = 529f8db483aae4df4e2a781b9db0c8a3a7c75c82ff70787ba2620310791c7821
endpoint = https://cbf011119e7864a873158d83f3304e27.r2.cloudflarestorage.com
acl = private
 ```

# Ardından sıkıştırılmış veritabanını şu komutla indirin:

  ```
rclone copy -P pathfinder-snapshots:pathfinder-snapshots/testnet_0.9.0_880310.sqlite.zst .
  ```

  ## Veritabanı Anlık Görüntülerini Sıkıştırmadan Çıkartma:
  
```
zstd -T0 -d testnet_0.9.0_880310.sqlite.zst -o goerli.sqlite
  ```

  

