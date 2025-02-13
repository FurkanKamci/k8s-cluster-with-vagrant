#### Ön Hazırlık :
İlk olarak sunucuya virtual box kurulumu yapılması gerekiyor. Sonra link den vagrant dosyasının indirilip yüklenmesi gerekiyor.

#### Link :
https://developer.hashicorp.com/vagrant/install

#### Vagrant ile oluşturduğunuz sanal makineler üzerinde çalışmak için kullanabileceğiniz bazı temel komutlar şunlardır:
```bash
vagrant up # Klasörün içerisinde terminal açarız ve bu komutu çalıştırırız. Klasörde bulunan Vagrantfile ı okuyarak cluster'ı oluşturmaya başlar.

# Tanımlı tüm makineleri (veya belirli bir makineyi vagrant up <makine_adi>) başlatır.
vagrant status

# Sanal makinelerin durumlarını (çalışıyor, kapalı vs.) listeler.
vagrant ssh <makine_adi>

# Belirtilen sanal makineye SSH üzerinden bağlanmanızı sağlar.
# Örneğin:
    vagrant ssh master # Bağlandıktan sonra `sudo su ` ile Root kullanıcısına geçip işlemlerimizi yapabiliriz.
    vagrant ssh worker1

vagrant halt # Çalışan makineleri düzgün bir şekilde kapatır.
vagrant destroy # Makineleri kapatıp tamamen siler. (Bu işlem, makineleri yeniden oluşturmanızı gerektirebilir.)
vagrant reload # Yapılandırma değişikliklerini uygulamak için makineleri yeniden başlatır.
vagrant provision # Vagrantfile'da tanımlı provisioning (kurulum/konfigürasyon) scriptlerini yeniden çalıştırır.
vagrant validate # Vagrantfile 'ı kontrol eder.
```
