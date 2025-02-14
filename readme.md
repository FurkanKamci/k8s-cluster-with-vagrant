#### Ön Hazırlık :
İlk olarak sunucuya virtual box kurulumu yapılması gerekiyor. Sonra link den vagrant dosyasının indirilip yüklenmesi gerekiyor.

##### Dosya paylaşımı yapabilmek için değişken eklenmesi gerekiyor.
- Windows için : setx VAGRANT_DISABLE_VBOXSYMLINKCREATE 1

#### Link :
https://developer.hashicorp.com/vagrant/install

#### Vagrant ile oluşturduğunuz sanal makineler üzerinde çalışmak için kullanabileceğiniz bazı temel komutlar şunlardır:
```bash
vagrant up # Klasörün içerisinde terminal açarız ve bu komutu çalıştırırız. Klasörde bulunan Vagrantfile ı okuyarak cluster'ı oluşturmaya başlar.

vagrant ssh <makine_adi> # Belirtilen VM ismi ile SSH üzerinden bağlanmanızı sağlar.
# Örneğin:
vagrant ssh master # Bağlandıktan sonra `sudo su ` ile Root kullanıcısına geçip işlemlerimizi yapabiliriz.
vagrant ssh worker1

vagrant status # Sanal makinelerin durumlarını (çalışıyor, kapalı vs.) listeler.
vagrant halt # Çalışan makineleri düzgün bir şekilde kapatır.
vagrant destroy # Makineleri kapatıp tamamen siler. (Bu işlem, makineleri yeniden oluşturmanızı gerektirebilir.)
vagrant reload # Yapılandırma değişikliklerini uygulamak için makineleri yeniden başlatır.
vagrant provision # Vagrantfile'da tanımlı provisioning (kurulum/konfigürasyon) scriptlerini yeniden çalıştırır.
vagrant validate # Vagrantfile 'ı kontrol eder.
```
