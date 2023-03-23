# Eraiki zure SMTP posta bidaltzeko zerbitzaria

## hitzaurrea

SMTP-k hodeiko saltzaileetatik zuzenean eros ditzake zerbitzuak, hala nola:

* [Amazon SES SMTP](https://docs.aws.amazon.com/ses/latest/dg/send-email-smtp.html)
* [Ali hodei posta elektronikoaren push](https://www.alibabacloud.com/help/directmail/latest/three-mail-sending-methods)

Zure posta zerbitzaria ere eraiki dezakezu - bidalketa mugagabea, kostu orokor baxua.

Jarraian, pausoz pauso erakusten dugu gure posta zerbitzaria nola eraiki.

## Zerbitzariaren hautaketa

Auto-ostatatutako SMTP zerbitzariak 25, 456 eta 587 portuak irekita dituen IP publiko bat behar du.

Erabili ohi diren hodei publikoek ataka hauek blokeatu dituzte lehenespenez, eta baliteke lan-agindu bat emanez irekitzea, baina oso zaila da azken finean.

Portu hauek irekita dituen eta alderantzizko domeinu-izenak konfiguratzea onartzen duen ostalari batetik erostea gomendatzen dut.

Hemen, [Contabo](https://contabo.com) gomendatzen dut.

Contabo Munichen (Alemania) egoitza duen hosting hornitzailea da, 2003an sortua, oso prezio lehiakorrak dituena.

Erosketa-moneta euroa aukeratzen baduzu, prezioa merkeagoa izango da (8GB memoria eta 4 CPUdun zerbitzariak 529 yuan inguru balio du urtean, eta hasierako instalazio-kuota doan da urtebetez).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/UoAQkwY.webp)

Eskaera bat egiterakoan, esan `prefer AMD` , eta AMD CPUdun zerbitzariak errendimendu hobea izango du.

Jarraian, Contaboren VPS hartuko dut adibide gisa zure posta zerbitzaria nola eraiki erakusteko.

## Ubuntu sistemaren konfigurazioa

Hemengo sistema eragilea Ubuntu 22.04 da

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/smpIu1F.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/m7Mwjwr.webp)

Ssh-ko zerbitzariak `Welcome to TinyCore 13!` (beheko irudian ikusten den bezala), sistema oraindik ez dela instalatu esan nahi du. Mesedez, deskonektatu ssh eta itxaron minutu batzuk berriro saioa hasteko.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/-qKACz9.webp)

`Welcome to Ubuntu 22.04.1 LTS` agertzen denean, hasieratzea amaitu da, eta urrats hauekin jarraitu dezakezu.

### [Aukera] Hasieratu garapen-ingurunea

Urrats hau hautazkoa da.

Erosotasunerako, ubuntu softwarearen instalazioa eta sistemaren konfigurazioa [github.com/wactax/ops.os/tree/main/ubuntu](https://github.com/wactax/ops.os/tree/main/ubuntu) helbidean jarri dut.

Exekutatu komando hau klik batekin instalatzeko.

```
bash <(curl -s https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

Txinako erabiltzaileek, mesedez, erabili komando hau ordez, eta hizkuntza, ordu-eremua, etab. automatikoki ezarriko dira.

```
CN=1 bash <(curl -s https://ghproxy.com/https://raw.githubusercontent.com/wactax/ops.os/main/ubuntu/boot.sh)
```

### Contabo-k IPV6 gaitzen du

Gaitu IPV6, SMTP-k IPV6 helbideekin mezu elektronikoak ere bidal ditzan.

editatu `/etc/sysctl.conf`

Aldatu edo gehitu hurrengo lerroak

```
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.default.disable_ipv6 = 0
net.ipv6.conf.lo.disable_ipv6 = 0
```

Jarraitu [contabo tutorialari: IPv6 konexioa gehitzea zure zerbitzariari](https://contabo.com/blog/adding-ipv6-connectivity-to-your-server/)

Editatu `/etc/netplan/01-netcfg.yaml` , gehitu lerro batzuk beheko irudian erakusten den moduan (Contabo VPS lehenetsitako konfigurazio fitxategiak lerro hauek ditu dagoeneko, kendu iruzkinak).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/5MEi41I.webp)

Ondoren `netplan apply` aldatutako konfigurazioa indarrean jartzeko.

Konfigurazioa arrakastatsua izan ondoren, `curl 6.ipw.cn` erabil dezakezu zure kanpoko sarearen ipv6 helbidea ikusteko.

## Klonatu konfigurazio biltegiko operazioak

```
git clone https://github.com/wactax/ops.soft.git
```

## Sortu doako SSL ziurtagiri bat zure domeinu-izenarentzat

Posta bidaltzeko SSL ziurtagiria behar da enkriptatzeko eta sinatzeko.

[Acme.sh](https://github.com/acmesh-official/acme.sh) erabiltzen dugu ziurtagiriak sortzeko.

acme.sh kode irekiko ziurtagiriak sinatzeko tresna automatizatua da,

Sartu ops.soft konfigurazio biltegian, exekutatu `./ssl.sh` , eta **goiko direktorioan** `conf` karpeta sortuko da.

Aurkitu zure DNS hornitzailea [acme.sh dnsapi-](https://github.com/acmesh-official/acme.sh/wiki/dnsapi) tik, editatu `conf/conf.sh` .

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Qjq1C1i.webp)

Ondoren, exekutatu `./ssl.sh 123.com` zure domeinu-izenerako `123.com` eta `*.123.com` ziurtagiriak sortzeko.

Lehenengo exekuzioak automatikoki instalatuko du [acme.sh](https://github.com/acmesh-official/acme.sh) eta programatutako zeregin bat gehituko du automatikoki berritzeko. `crontab -l` ikus dezakezu, honelako lerro bat dago.

```
52 0 * * * "/mnt/www/.acme.sh"/acme.sh --cron --home "/mnt/www/.acme.sh" > /dev/null
```

Sortutako ziurtagiriaren bidea `/mnt/www/.acme.sh/123.com_ecc。`

Ziurtagiriaren berritzeak `conf/reload/123.com.sh` script-a deituko du, editatu script hau, `nginx -s reload` bezalako komandoak gehi ditzakezu erlazionatutako aplikazioen ziurtagirien cachea freskatzeko.

## Eraiki SMTP zerbitzaria chasquid-ekin

[chasquid](https://github.com/albertito/chasquid) Go hizkuntzan idatzitako kode irekiko SMTP zerbitzari bat da.

Postfix eta Sendmail bezalako antzinako posta-zerbitzariko programen ordezko gisa, chasquid sinpleagoa eta erabiltzeko errazagoa da, eta bigarren mailako garapenerako ere errazagoa da.

Exekutatu `./chasquid/init.sh 123.com` automatikoki instalatuko da klik batekin (ordeztu 123.com zure igorlearen domeinu-izenarekin).

## Konfiguratu posta elektronikoko sinadura DKIM

DKIM posta elektronikoko sinadurak bidaltzeko erabiltzen da, gutunak spam gisa trata ez daitezen.

Komandoa behar bezala exekutatu ondoren, DKIM erregistroa ezartzeko eskatuko zaizu (behean erakusten den moduan).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/LJWGsmI.webp)

Gehitu TXT erregistro bat zure DNSra (behean erakusten den moduan).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/0szKWqV.webp)

## Ikusi zerbitzuaren egoera eta erregistroak

 `systemctl status chasquid` Ikusi zerbitzuaren egoera.

Funtzionamendu normalaren egoera beheko irudian ageri dena da

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/CAwyY4E.webp)

 `grep chasquid /var/log/syslog` edo `journalctl -xeu chasquid` akatsen erregistroa ikus dezakete.

## Alderantzizko domeinu-izenaren konfigurazioa

Alderantzizko domeinu-izena IP helbidea dagokion domeinu-izenarekin konpontzea ahalbidetzea da.

Alderantzizko domeinu-izena ezartzeak mezu elektronikoak spam gisa identifikatzea ekidin dezake.

Posta jasotzen denean, zerbitzari hartzaileak alderantzizko domeinu-izenaren azterketa egingo du bidaltzen duen zerbitzariaren IP helbidean, bidaltzen duen zerbitzariak alderantzizko domeinu-izen bat duen ala ez baieztatzeko.

Bidaltzen duen zerbitzariak ez badu alderantzizko domeinu-izenik edo alderantzizko domeinu-izena ez badator bat bidaltzen duen zerbitzariaren IP helbidearekin, hartzaileak mezu elektronikoa spam gisa antzeman dezake edo ukatu egin dezake.

Bisitatu [https://my.contabo.com/rdns](https://my.contabo.com/rdns) eta konfiguratu behean erakusten den moduan

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/IIPdBk_.webp)

Alderantzizko domeinu-izena ezarri ondoren, gogoratu ipv4 eta ipv6 domeinu-izenaren aurrerapen-bereizmena zerbitzarian konfiguratzea.

## Editatu chasquid.conf ostalari-izena

Aldatu `conf/chasquid/chasquid.conf` alderantzizko domeinu-izenaren baliora.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/6Fw4SQi.webp)

Ondoren, exekutatu `systemctl restart chasquid` zerbitzua berrabiarazteko.

## Egin babeskopia konf. git biltegian

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/Fier9uv.webp)

Adibidez, conf karpetaren babeskopia egiten dut nire github prozesura honela

Sortu biltegi pribatu bat lehenik

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ZSCT1t1.webp)

Sartu conf direktorioa eta bidali biltegira

```
git init
git add .
git commit -m "init"
git branch -M main
git remote add origin git@github.com:wac-tax-key/conf.git
git push -u origin main
```

## Gehitu igorlea

Korrika egin

```
chasquid-util user-add i@wac.tax
```

Bidaltzailea gehi dezake

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/khHjLof.webp)

### Egiaztatu pasahitza behar bezala ezarrita dagoela

```
chasquid-util authenticate i@wac.tax --password=xxxxxxx
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/e92JHXq.webp)

Erabiltzailea gehitu ondoren, `chasquid/domains/wac.tax/users` eguneratuko da, gogoratu biltegian bidaltzea.

## DNS gehitu SPF erregistroa

SPF ( Sender Policy Framework ) posta elektronikoaren iruzurra saihesteko erabiltzen den posta elektronikoa egiaztatzeko teknologia da.

Posta-igorle baten identitatea egiaztatzen du, igorlearen IP helbidea bat datorrela dioen domeinu-izenaren DNS erregistroekin egiaztatuz, iruzurgileek mezu elektroniko faltsuak bidaltzea eragotziz.

SPF erregistroak gehitzeak mezu elektronikoak spam gisa identifikatzea ekidin dezake ahalik eta gehien.

Zure domeinu-izenen zerbitzariak ez badu SPF mota onartzen, gehitu TXT motako erregistroa.

Adibidez, `wac.tax` -en SPF hau da

`v=spf1 a mx include:_spf.wac.tax include:_spf.google.com ~all`

`_spf.wac.tax` -erako SPF

`v=spf1 a:smtp.wac.tax ~all`

Kontuan izan `include:_spf.google.com` dudala, hau `i@wac.tax` konfiguratuko dudalako Google postontzian bidalketa-helbide gisa geroago.

## DNS konfigurazioa DMARC

DMARC (Domeinuan oinarritutako mezuen autentifikazioa, txostena eta adostasuna) laburdura da.

SPF erreboteak harrapatzeko erabiltzen da (agian konfigurazio akatsek eragindakoak edo beste norbaitek spama bidaltzeko zu zarela egiten ari da).

Gehitu TXT erregistroa `_dmarc` ,

Edukia honakoa da

```
v=DMARC1; p=quarantine; fo=1; ruf=mailto:ruf@wac.tax; rua=mailto:rua@wac.tax
```

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/k44P7O3.webp)

Parametro bakoitzaren esanahia honakoa da

### p (politika)

SPF (Sender Policy Framework) edo DKIM (DomainKeys Identified Mail) egiaztapena huts egiten duten mezu elektronikoak nola kudeatu adierazten du. p parametroa hiru balio hauetako batean ezar daiteke:

* bat ere ez: ez da ekintzarik egiten, egiaztapenaren emaitza bakarrik igortzen zaio igorleari posta elektroniko bidezko txostenaren mekanismoaren bidez.
* Berrogeialdia: Sar ezazu egiaztapena gainditu ez duen posta spam karpetan, baina ez du mezua zuzenean baztertuko.
* ukatu: zuzenean ukatu egiaztapena huts egiten duten mezu elektronikoak.

### fo (huts egiteko aukerak)

Txosten mekanismoak itzultzen duen informazio kopurua zehazten du. Balio hauetako batean ezar daiteke:

* 0: jakinarazi mezu guztien baliozkotze-emaitzen berri
* 1: Egiaztatzen huts egiten duten mezuak soilik jakinarazi
* d: Domeinu-izena egiaztatzeko hutsegiteak soilik jakinarazi
* s: SPF egiaztatzeko hutsegiteak soilik jakinarazi
* l: DKIM egiaztatzeko hutsegiteak soilik jakinarazi

### rua & ruf

* rua (Reporting URI for Agregate reports): txosten agregatuak jasotzeko helbide elektronikoa
* ruf (Reporting URI for Forensic reports): txosten zehatzak jasotzeko helbide elektronikoa

## Gehitu MX erregistroak mezu elektronikoak Google Mail-era birbidaltzeko

Helbide unibertsalak onartzen dituen doako postontzi korporatiborik aurkitu ezin nuenez (Catch-All, domeinu-izen honetara bidalitako edozein mezu elektroniko jaso ditzake, aurrizkietan murrizketarik gabe), chasquid erabili dut mezu elektroniko guztiak nire Gmail-eko postontzira birbidaltzeko.

**Ordaindutako negozio-postontzi propioa baduzu, ez aldatu MXa eta saltatu urrats hau.**

Editatu `conf/chasquid/domains/wac.tax/aliases` , ezarri birbidaltzeko postontzia

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/OBDl2gw.webp)

`*` mezu elektroniko guztiak adierazten ditu, `i` goian sortutako erabiltzaile igorlearen helbide elektronikoaren aurrizkia da. Posta birbidaltzeko, erabiltzaile bakoitzak lerro bat gehitu behar du.

Ondoren, gehitu MX erregistroa (zuzenean seinalatu dut hemen alderantzizko domeinu-izenaren helbidea, beheko irudiko lehen lerroan agertzen den moduan).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/7__KrU8.webp)

Konfigurazioa amaitu ondoren, beste helbide elektroniko batzuk erabil ditzakezu `i@wac.tax` eta `any123@wac.tax` helbide elektronikoetara mezu elektronikoak bidaltzeko, Gmail-en mezu elektronikoak jaso ditzakezun ikusteko.

Hala ez bada, egiaztatu chasquid erregistroa ( `grep chasquid /var/log/syslog` ).

## Bidali mezu elektroniko bat i@wac.tax helbidera Google Mail-ekin

Google Mail-ek mezua jaso ondoren, i.wac.tax@gmail.com-en ordez `i@wac.tax` ekin erantzutea espero nuen.

Bisitatu [https://mail.google.com/mail/u/1/#settings/accounts](https://mail.google.com/mail/u/1/#settings/accounts) eta egin klik "Gehitu beste helbide elektroniko bat".

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/PAvyE3C.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/_OgLsPT.webp)

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/XIUf6Dc.webp)

Ondoren, idatzi bidalitako mezu elektronikoan jasotako egiaztapen-kodea.

Azkenik, igorle-helbide lehenetsi gisa ezar daiteke (helbide berarekin erantzuteko aukerarekin batera).

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/a95dO60.webp)

Modu honetan, SMTP posta zerbitzariaren ezarpena osatu dugu eta, aldi berean, Google Mail erabiltzen dugu mezu elektronikoak bidaltzeko eta jasotzeko.

## Bidali proba-mezu bat konfigurazioa arrakastatsua den egiaztatzeko

Sartu `ops/chasquid`

Exekutatu `direnv allow` (direnv aurreko gako bakarreko hasierako prozesuan instalatu da eta kako bat gehitu da shell-ean)

gero korrika

```
user=i@wac.tax pass=xxxx to=iuser.link@gmail.com ./sendmail.coffee
```

Parametroen esanahia honakoa da

* erabiltzailea: SMTP erabiltzaile-izena
* pasa: SMTP pasahitza
* nori: hartzaile

Proba mezu elektroniko bat bidal dezakezu.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/ae1iWyM.webp)

Gmail erabiltzea gomendatzen da proba-mezuak jasotzeko konfigurazioak arrakastatsuak diren egiaztatzeko.

### TLS enkriptatzea estandarra

Beheko irudian ikusten den bezala, blokeo txiki hau dago, eta horrek esan nahi du SSL ziurtagiria behar bezala gaitu dela.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/SrdbAwh.webp)

Ondoren, egin klik "Erakutsi jatorrizko posta elektronikoa"

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/qQQsdxg.webp)

### DKIM

Beheko irudian ikusten den bezala, Gmail jatorrizko posta-orriak DKIM bistaratzen du, hau da, DKIM konfigurazioa arrakastatsua da.

![](https://pub-b8db533c86124200a9d799bf3ba88099.r2.dev/2023/03/an6aXK6.webp)

Egiaztatu Jasotako mezu elektronikoaren goiburuan, eta igorlearen helbidea IPV6 dela ikus dezakezu, hau da, IPV6 ere behar bezala konfiguratuta dagoela esan nahi du.
