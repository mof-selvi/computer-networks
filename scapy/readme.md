# Scapy Örnekleri

Scapy ağ paketlerini kontrol edebilen bir araçtır.

Yüklemek için detaylar sitesinde bulunmaktadır: [scapy.net](https://scapy.net/)

Lab cihazlarımızda Anaconda bulunduğu için biz aşağıdaki yöntem ile kurulum yapacağız:
```shell
conda install -c conda-forge scapy
```

Bu kodu `conda` environment'ınız aktif haldeyken çalıştırdığınızda gerekli Python paketleri yüklenecektir.

Windows kullanıcısı iseniz [Npcap](https://npcap.com/) kurmanız da gerekebilir.

---

## Paket yakalama

```python
from scapy.all import sniff

def packet_callback(packet):
    print(packet.summary())

sniff(iface='eth0', prn=packet_callback, count=10)
```

Yukarıdaki kod `eth0` adlı ağ arayüzündeki 10 paketi okur ve ekrana yazdırır.

Eğer yukarıdaki kodu çalıştırdığınızda hata alıyorsanız:

1. Scapy kurulmamış olabilir. Sadece `import` satırını bırakarak paketin bulunup bulunamadığını test ediniz.
2. Npcap kurulmamış olabilir.
3. Ağ arayüzünüz `eth0` olmayabilir. Kontrol etmek için Linux'ta `ifconfig`, Windows'ta `ipconfig` komutlarını komut satırında kullanabilirsiniz. Alternatif olarak, bir string yerine `None` değeri vererek varsayılan ağ üzerinden paket yakalama işlemi yapabilirsiniz:

```python
from scapy.all import sniff

def packet_callback(packet):
    print(packet.summary())

sniff(iface=None, prn=packet_callback, count=10)
```

---

## Paket Oluşturma ve Gönderme

```python

from scapy.all import *

packet = IP(dst="www.google.com")/ICMP()

response = sr1(packet, timeout=2)

if response:
    response.show()
else:
    print("Bir cevap alınamadı.")

```

Yukarıdaki kod ile Google'ın sunucularına bir paket göndermiş olduk. Dönen cevabı ve içindeki IP adreslerini inceleyiniz.

    Hangi IP adresleri nereye aittir?

Farklı alan adları kulanarak dönen cevapları kontrol ediniz.

Cevap almakta hata yaşıyorsanız `timeout` değerini yükseltmeyi deneyebilirsiniz.

    Bazı sunucular bu yöntem ile (herhangi bir HTTP isteği içermeyen) paket almayı kabul etmiyor olabilir.

---

## Sahte Kaynak IP Adresi ile Paket Oluşturma


```python
from scapy.all import *

packet = IP(src="192.168.1.100", dst="www.example.com")/ICMP()

send(packet)
```

Yukarıdaki kodun etkisini deneyimlemek zordur. Fakat kısaca; `dst` ile belirtilen hedef adrese sanki `src` ile belirtilen adresten gönderilmiş gibi bir paket iletilmeye çalışılmaktadır.

Kodu Scapy'nin kodlama dinamiklerine alışmak için incelemeniz tavsiye edilir.

--- 


## Açık TCP Portlarını Tarama


```python
from scapy.all import *

target = "192.168.1.1"

ports = range(1, 101)

for port in ports:
    packet = IP(dst=target)/TCP(dport=port, flags="S")
    response = sr1(packet, timeout=1, verbose=0)
    if response and response.haslayer(TCP) and response[TCP].flags == 18:
        print(f"Port {port} is open")
```

Yukarıdaki kod ile `target` değişkeninde belirtilen adres üzerinde 1-101 arasında TCP iletişimi için açık olan portlar taranabilmektedir.

### Alıştırma
    Yanınızdaki arkadaşınızın cihazının yerel IP (LAN) adresini öğrenip daha geniş bir aralıkta üzerinde tarama yapınız.

### Alıştırma
    Bulunduğunuz labdaki hoca bilgisayarının LAN IP adresini uzaktan tespit etmeye çalışınız.

    Bu IP adresini kullanarak açık olan portları tarayınız. Hangi portların neden açık olabileceğini açıklamaya çalışınız. (Portları Google'layabilirsiniz.)

> Not: Lablarımızdaki cihazlarımız daha etkili bir ders işleyişi için yerel ağ içerisinde pek çok erişime açık hale getirilmiştir. Lütfen zararlı bir kod çalıştırmayı veya yetkilendirme ayarlarıyla oynamayı denemeyiniz.

