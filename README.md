# ApacheSpark
Apache Spark Installation on Centos7

Spark Kurulumu - Centos7
Öncelikle sistemin güncel olmasını sağlayarak başlayalım.
yum clean all
yum -y install epel-release
yum -y update
Ardından java kurulumu yapılacaktır.
yum install java -y
Yükledikten sonra java versiyonu alttaki komut satırı ile birlikte kontrol edilebilir.
java -version
Scala yükleme işlemine geçebiliriz. Spark yükleme esnasında Scala’yı kurar bu yüzden Java ve Python’un kurulu olduğundan emin olmalıyız.
wget http://www.scala-lang.org/files/archive/scala-2.10.1.tgz
tar xvf scala-2.10.1.tgz
sudo mv scala-2.10.1 /usr/lib
sudo ln -s /usr/lib/scala-2.10.1 /usr/lib/scala
export PATH=$PATH:/usr/lib/scala/bin
Yukarıda yapılan işlemlerde öncelikle scala url’si verilerek indirme işlemi yapılır ve dizinden çıkarmak için tar komutu kullanılır. Scala dosyasına ulaşmak için indirilen dizin yerine istediğimiz yere taşıma işlemi mv komutu ile yapılır. ln komutu ile var olan dosyaya kısayol oluşturulur.
Yükleme işlemi tamamlandıktan sonra kontrol aynı şekilde kontrol edilebilir.
scala -version
Spark kurulumunu başlatabiliriz.
wget http://www-eu.apache.org/dist/spark/spark-2.2.1/spark-2.2.1-bin-hadoop2.7.tgz
tar -xzf spark-2.2.1-bin-hadoop2.7.tgz
export SPARK_HOME=$HOME/spark-2.2.1-bin-hadoop2.7
export PATH=$PATH:$SPARK_HOME/bin
Gerekli URL adresinden dosya indirilir. Tar ile indirilen klasör dizine çıkarılır ve PATH kısımları ayarlanır.
Spark’a başlamadan önce gerekli olan ortam değişkenleri ayarlanır.
echo 'export PATH=$PATH:/usr/lib/scala/bin' >> .bash_profile
echo 'export SPARK_HOME=$HOME/spark-1.6.0-bin-hadoop2.6' >> .bash_profile
echo 'export PATH=$PATH:$SPARK_HOME/bin' >> .bash_profile
Apache Spark için gerekli olan firewall ayarlanır.
firewall-cmd --permanent --zone=public --add-port=6066/tcp
firewall-cmd --permanent --zone=public --add-port=7077/tcp
firewall-cmd --permanent --zone=public --add-port=8080-8081/tcp
firewall-cmd --reload
Apache Spark UI alttaki komut satırı ile birlikte açılır.
./sbin/start-master.sh

MULTİ NODE APACHE SPARK
Host dosyası içerisine master ve slave girişleri eklenir. Bu işlemi yapabilmek için öncelikle host dosyasını açmamız gerekir.
vi /etc/hosts
Host dosyası içerisine aşağıdaki şekilde IP ve kullanıcılar yazılarak işlemler tamamlanır.
<MASTER-IP> master
<SLAVE01-IP> slave01
<SLAVE02-IP> slave02
Master ve Slave olarak kullanılan makinelere Java kurulumu aşağıdaki satırlarla birlikte gerçekleştirilir.
sudo apt-get install python-software-properties
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java7-installer
Java kurulumu şu şekilde kontrol edilir.
java -version
Java kurulumu tamamlandıktan sonra Scala kurulumu aşağıdaki komut satırı ile gerçekleştirilir.
sudo apt-get install scala
Bunun da aynı şekilde kurulumu şu şekilde kontrol edilir.
scala -version
Master makinenin diğer slave olan makinelere şifresiz olarak erişebilmesi için SSH kurulumu yapılacaktır. 
sudo apt-get install openssh-server openssh-client
ssh-keygen -t rsa -P ""
yukarıda gösterilen SSH kurulumu ile birlikte master makine içerisinde aşağıdaki satırlar sayesinde şifresiz şekilde slave makineye bağlanabiliriz. 

ssh slave01
ssh slave02
Single Node için kurulan spark kurulumu aynı adımlarla birlikte kurulumu gerçekleştirilecektir.
wget http://www-eu.apache.org/dist/spark/spark-2.2.1/spark-2.2.1-bin-hadoop2.7.tgz
tar -xzf spark-2.2.1-bin-hadoop2.7.tgz
sudo mv spark-2.3.0-bin-hadoop2.7 /usr/local/spark
Spark çevresini ayarlamak için vi ./bashrc komutu yazılarak PATH’ler içerisine eklenir.
export PATH = $PATH:/usr/local/spark/bin
yazdığımız işlemleri kayıt edebilmek için source ~/.bashrc yazılır. Eğer mevcut PATH varsa :/usr/local/spark/bin şeklinde dizeyi devam ettirebiliriz.
Spark Master Ayarlamaları
Bu işlemler yalnızca Master makine üzerinde yapılacaktır.
spark/conf dizini içerisine girilerek template kopyası alınır ve ismi değiştirilir.
cd /usr/local/spark/conf
cp spark-env.sh.template spark-env.sh
İsmi değiştirilen bu dosya üzerinde işlemler gerçekleştireceğiz.
Alttaki komut satırı ile birlikte dosya içeriğine gireceğiz.
vi spark-env.sh
Parametreleri kendi dizin yapısına ve ip adresine bağlı olrak değiştirerek kayıt işlemini gerçekleştireceğiz.
export SPARK_MASTER_HOST='<MASTER-IP>'
export JAVA_HOME=<Path_of_JAVA_installation>

Spark Cluster’i Başlatma
cd /usr/local/spark
./sbin/start-all.sh
Yukarıdaki işlemler yapıldıktan sonra kontrol amaçlı olarak jps komutu kullanılır. Bu şekilde servisin başlayıp başlamadığı kontrol edilebilir. 
Başlatılan Spark Cluster’e aşağıdaki şekilde ulaşabiliriz.
Bu ulaşacağımız ekran Spark Master UI olarak geçmektedir. Workers olarak ayarlanan makineler buradan gösterilir.
http://<MASTER-IP>:8080/
Diğer olarak scala üzerinde yazılan kodlar için gerekli web UI’a şu şekilde ulaşacağız.
http://<MASTER_IP>:4040/

