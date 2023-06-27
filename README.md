# JCL NOTLARI

# JCL Nedir?

- Job Control Language (JCL), büyük bilgisayar sistemlerindeki iş akışını kontrol etmek için kullanılan bir dildir.
- IBM'in mainframe bilgisayarlarında yaygın olarak kullanılan bir sistem programlama dilidir.
- JCL,  z/OS işletim sistemine gerçekleştirmesini istediğiniz işi(job) anlatmak için kodladığınız bir dizi ifadeden oluşur. Bu şekilde işlemlerin işletim sistemine nasıl sunulacağını belirler.
- JCL, uygulama programlarının işlenmesinde işletim sistemlerini yönlendiren bir dil görevi görür.
- JCL, bir uygulama programı ile bilgisayarın işletim sistemi (MVS) arasındaki iletişim aracıdır.
- JCL, temel olarak bir programın derlenip (compile) çalıştırıldığı (execute) bölümdür.

### Job Nedir?

Job, bir veya daha fazla ilgili programın bir sırayla yürütülmesi olarak tanımlanır. Bir uygulama programı (Örnek: COBOL, Assembler veya PL/I) ile bilgisayarın işletim sistemi arasındaki iletişimi sağlar.

Job akışı şu şekilde ilerler:

1. İNPUT
2. CONVERSION
3.  EXECUTION
4. OUTPUT

# JCL Ne Yapar?

- z/OS ‘ye kim olduğunu söyler.
- z/OS ‘ye alan ve zaman gereksinimlerini anlatır.
- z/OS ‘ye hangi programlara (load modüllerine) ihtiyacınız olduğunu söyler.
- z/OS ‘ye  program(lar)ın hangi data setlere ihtiyaç duyduğunu söyler.
- İşlerin sırasını ve bağımlılıklarını belirleyebilir, farklı programların paralel veya seri olarak çalıştırılmasını sağlayabilir ve hata durumlarına karşı yönetim adımları ekleyebilir.

# JCL İle Neler Yapılabilir?

- İşletim sistemine bir iş(job) gönderebilirsiniz.
- İşi çalıştırmak için gereken kaynakları isteyebilirsiniz.
- İşin sistem işlemesini kontrol edebilirsiniz.
- EXEC deyimindeki PARM parametresini kullanarak parametre değerlerini JCL üzerinden uygulama programına iletebilirsiniz.

## JCL Bölümleri:

1. **JOB Bölümü:** İşin kimliği, öncelik düzeyi, önbelleğe alma gereksinimleri gibi genel bilgileri içerir.
2. **EXEC Bölümü:** İşin bir parçası olan program veya programları tanımlar. Her bir programın çalıştırılma koşulları, girdi ve çıktı dosyaları gibi bilgiler bu bölümde belirtilir.
3. **DD (Data Definition) Bölümü:** İşin girdi ve çıktı dosyalarını tanımlar. Programlar arasında veri akışını yönetmek için kullanılır.

### ***Örnek bir JCL dosyasını detaylı bir şekilde inceleyelim:***

```vbnet
//CBL0004J JOB 1,NOTIFY=&SYSUID
//***************************************************/
//* Copyright Contributors to the COBOL Programming Course
//* SPDX-License-Identifier: CC-BY-4.0
//***************************************************/
//COBRUN  EXEC IGYWCL
//COBOL.SYSIN  DD DSN=&SYSUID..CBL(CBL0004),DISP=SHR
//LKED.SYSLMOD DD DSN=&SYSUID..LOAD(CBL0004),DISP=SHR
//***************************************************/
// IF RC = 0 THEN
//***************************************************/
//RUN     EXEC PGM=CBL0004
//STEPLIB   DD DSN=&SYSUID..LOAD,DISP=SHR
//ACCTREC   DD DSN=&SYSUID..DATA,DISP=SHR
//PRTLINE   DD SYSOUT=*,OUTLIM=15000
//SYSOUT    DD SYSOUT=*,OUTLIM=15000
//CEEDUMP   DD DUMMY
//SYSUDUMP  DD DUMMY
//***************************************************/
// ELSE
// ENDIF
```

```vbnet
//CBL0004J JOB 1,NOTIFY=&SYSUID
```

Burada bir işin başlangıcı tanımlanmaktadır. İşin adı **"CBL0004J"** olarak belirlenmiştir. Adlandırma 8 karakterden oluşur ve JOBS bölümünde dosya adı burada belirtilen işin adıyla aynı olur. İşin öncelik düzeyi ise "1” olarak belirtilmiştir. Yani buradaki “1” işin ilk adımı olduğunu göstermektedir. Bir iş birden fazla adımdan oluşabilir ve her adım belirli bir işlemi gerçekleştirir. 

**"NOTIFY=&SYSUID"** ifadesi, iş tamamlandığında hangi kullanıcının bilgilendirileceğini belirtir. Burada **NOTIFY** parametresi, belirtilen bir kullanıcıya veya gruba tamamlanma mesajı veya bildirimi göndermek için kullanılır. 

**"&SYSUID"**,  sembolik bir parametredir ve sistem tarafından yürütme sırasında genellikle dinamik olarak çözülür. SYSUID, mevcut kullanıcının sistemdeki kimlik bilgisini temsil eder.

```vbnet
//COBRUN  EXEC IGYWCL
```

Bu ifade IBM mainframe sistemlerinde COBOL programlarını **derlemek ve çalıştırmak** için kullanılır.

**COBRUN** işin tanımlandırmasını belirtir. Burada bir işlem basamağını (step name) temsil eder.

**"EXEC IGYWCL"** ifadesi, COBOL derleyici programının adını belirtir. Bu iş adımı, **IGYWCL** programının çalıştırılmasıyla **COBOL kaynak kodunun derlendiği ve nesne kodunun oluşturulduğu** bir aşamadır. **Nesne kodu**, COBOL programının makine diline çevrilmiş formudur ve bu kod daha sonra sistemdeki bir COBOL çalıştırma ortamında yürütülebilir. **IGYWCL**, IBM COBOL derleyici programının adıdır. COBOL programlarını derlemek ve sonucunda nesne kodu üretmek için kullanılır.

**EXEC** bölümünde belirtilen program veya programlar, genellikle işin ana iş yükünü oluşturan ve belirli bir işlevi yerine getiren programlardır. Bu programlar, önceden derlenmiş COBOL kaynak kodu veya başka bir dilde yazılmış olabilir.

```vbnet
//COBOL.SYSIN  DD DSN=&SYSUID..CBL(CBL0004),DISP=SHR
```

Burada COBOL kaynak kod dosyasının yeri ve işlem modu belirtilmiştir.

- **DD**, "Data Definition" (Veri Tanımı) anlamına gelir ve bir giriş veya çıkış dosyasının tanımlandığını gösterir.
- **DSN**, "Data Set Name" (Veri Seti Adı) anlamına gelir ve bir veri setinin adını belirtir.
- **&SYSUID**, sembolik bir parametredir ve sistem tarafından yürütme sırasında mevcut kullanıcının kimlik bilgisini temsil eder.
- **..CBL(CBL0004)**, CBL0004 isimli bir COBOL kaynak kod dosyasının belirtilen veri setinde bulunduğunu gösterir. "..CBL" ifadesi veri setinin genel adını belirtirken, "(CBL0004)" ifadesi ise belirli bir üyeyi (CBL0004) işaret eder.
- **DISP=SHR**, "Disposition" (Dağıtım) anlamına gelir ve belirtilen veri setinin paylaşımlı olarak açılacağını gösterir. Bu, başka bir işlem tarafından aynı anda okunabilir veya değiştirilebilir olduğunu ifade eder.

```vbnet
//LKED.SYSLMOD DD DSN=&SYSUID..LOAD(CBL0004),DISP=SHR
```

Bu ifade ise COBOL programının derleme sonucunda oluşturulan yürütülebilir modül (load module) dosyasının yerini ve işlem modunu belirtir.

- **LKED** ifadesi yürütülebilir modül oluşturma aşamasını temsil eder. SYSLMOD ise oluşturulan modülün nereye yerleştirileceğini belirtir.
- **DD**, yine "Data Definition" anlamına gelir ve bir giriş veya çıkış dosyasının tanımlandığını gösterir.
- **DSN=&SYSUID..LOAD(CBL0004)**, veri setinin adını ve üye adını belirtir. Burada, **LOAD**, yürütülebilir modül dosyalarının saklandığı veri setini temsil eder.
- **DISP=SHR**, veri setinin paylaşımlı olarak açılacağını gösterir, yani başka bir işlem tarafından aynı anda okunabilir veya değiştirilebilir olacaktır.

```vbnet
//COBRUN  EXEC IGYWCL
//COBOL.SYSIN  DD DSN=&SYSUID..CBL(CBL0004),DISP=SHR
//LKED.SYSLMOD DD DSN=&SYSUID..LOAD(CBL0004),DISP=SHR
```

Sonuç olarak bu 3 satırda gerçekleşen işlemle ilk olarak programın derlenmesi sağlanır ve derlenilen dosyalar data sete yazdırılarak olası bir hata durumunda bu hatanın  ne olduğunu görebilme imkanı sağlar.

```vbnet
// IF RC = 0 THEN
```

Burada kullanılan bir kontrol akışı ifadesidir. **RC**, "Return Code" (Dönüş Kodu) anlamına gelir ve bir komutun veya işlemin tamamlanmasının ardından dönen bir sayısal değeri temsil eder.

Bu ifade, bir koşulun kontrol edilmesini sağlar. RC'nin değeri 0 ise (yani dönüş kodu başarıyla tamamlanan bir işlemi gösterirse), belirtilen işlemlerin veya komutların gerçekleştirilmesi gerektiği anlamına gelir. Yani, RC'nin 0 olması durumunda, belirli bir şarta uygun olarak bir dizi işlemi gerçekleştirir.

```vbnet
//RUN     EXEC PGM=CBL0004
```

Burası programın çalıştırıldığı kısımdır.

- EXEC, "Execute" (Çalıştır) anlamına gelir ve bir programın veya işlemin çalıştırılacağını gösterir.
- PGM=CBL0004, belirtilen programın adını belirtir. Burada, CBL0004 COBOL programının adını temsil eder.

Bu ifade, COBOL programının belirtilen PGM değeri olan CBL0004 ile çalıştırılacağını belirtir. COBOL programı, önceden derlenmiş bir yürütülebilir modül dosyası veya COBOL kaynak kodunun derlenmesi sonucunda oluşturulan bir nesne kod dosyası olabilir.

```vbnet
//STEPLIB   DD DSN=&SYSUID..LOAD,DISP=SHR
```

Burada bir iş adımı için yürütülebilir modül dosyalarının yeri belirtilmiştir.

- **STEPLIB** ifadesi, bir iş adımının yürütülebilir modül dosyalarının nereden alınacağını belirtir.
- **DD**, "Data Definition" (Veri Tanımı) anlamına gelir ve bir giriş veya çıkış dosyasının tanımlandığını gösterir.
- **DSN=&SYSUID..LOAD**, belirtilen veri setinin adını belirtir. &SYSUID sembolik bir parametredir ve mevcut kullanıcının kimlik bilgisini temsil eder. "..LOAD" ifadesi, yürütülebilir modül dosyalarının bulunduğu genel veri setini temsil eder.
- **DISP=SHR** ifadesi, veri setinin paylaşımlı olarak açılacağını belirtir. Bu, başka bir işlem tarafından aynı anda okunabilir veya değiştirilebilir olduğunu gösterir.

Bu ifade, yürütülebilir modül dosyalarının bulunduğu bir veri setini iş adımı için kullanılabilir hale getirir. İş adımı bu yürütülebilir modül dosyalarına STEPLIB üzerinden erişebilir ve gerektiğinde bu modülleri çağırabilir.

```vbnet
//ACCTREC   DD DSN=&SYSUID..DATA,DISP=SHR
```

Bu ifade bir giriş/çıkış dosyasının yerini belirtir.

- **ACCTREC** ifadesi, giriş/çıkış dosyasının adını belirtir. Bu ad, dosyanın ne amaçla kullanıldığını veya içeriğini yansıtabilir.
- **DD**, "Data Definition" (Veri Tanımı) anlamına gelir ve bir giriş veya çıkış dosyasının tanımlandığını gösterir.
- **DSN=&SYSUID..DATA**, belirtilen veri setinin adını belirtir. &SYSUID sembolik bir parametredir ve mevcut kullanıcının kimlik bilgisini temsil eder. "..DATA" ifadesi, veri dosyalarının bulunduğu genel veri setini temsil eder.
- **DISP=SHR** ifadesi, veri setinin paylaşımlı olarak açılacağını belirtir. Bu, başka bir işlem tarafından aynı anda okunabilir veya değiştirilebilir olduğunu gösterir.

Bu ifade, iş adımının giriş/çıkış işlemleri için bir veri dosyasının yerini belirlemektedir. İş adımı bu veri dosyasına ACCTREC üzerinden erişebilir ve gerektiğinde dosyayı okuyabilir veya değiştirebilir.

```vbnet
//PRTLINE   DD SYSOUT=*,OUTLIM=15000
```

Bu ifade bir yazıcı çıktı dosyasının yerini ve belirli bir çıktı sınırını belirtir.

- **PRTLINE** ifadesi, yazıcı çıktı dosyasının adını veya tanımını belirtir. Bu ad, dosyanın ne amaçla kullanıldığını veya içeriğini yansıtabilir.
- **DD,** "Data Definition" (Veri Tanımı) anlamına gelir ve bir giriş veya çıkış dosyasının tanımlandığını gösterir.
- **SYSOUT**=*, çıktının yazıcıya yönlendirileceğini gösterir. "*" karakteri, çıktının standart yazıcıya yönlendirileceğini ifade eder.
- **OUTLIM**=15000 ifadesi, çıktı sınırını belirtir. Burada, 15000 belirli bir sınır değeridir ve çıktı dosyasının en fazla 15000 satıra kadar çıkmasına izin verir

```vbnet
//SYSOUT    DD SYSOUT=*,OUTLIM=15000
```

Bu ifade bir sistem çıktı dosyasının yerini ve belirli bir çıktı sınırını belirtir.

- **SYSOUT** ifadesi, sistem çıktı dosyasının adını veya tanımını belirtir. Bu ad, çıktının ne amaçla kullanıldığını veya içeriğini yansıtabilir.
- **DD**, "Data Definition" (Veri Tanımı) anlamına gelir ve bir giriş veya çıkış dosyasının tanımlandığını gösterir.
- **SYSOUT**=*, çıktının sistem çıktı aygıtına yönlendirileceğini gösterir. "*" karakteri, çıktının standart sistem çıktı aygıtına yönlendirileceğini ifade eder.
- **OUTLIM**=15000 ifadesi, çıktı sınırını belirtir. Burada, 15000 belirli bir sınır değeridir ve çıktı dosyasının en fazla 15000 satıra kadar çıkmasına izin verir.

```vbnet
//CEEDUMP   DD DUMMY
```

Bu ifade bir çekirdek bellek dökümünün **oluşturulmayacağını** belirtir.

- **CEEDUMP** ifadesi, çekirdek bellek dökümünün adını veya tanımını belirtir. Bu ad, dökümün ne amaçla kullanıldığını veya içeriğini yansıtabilir.
- **DD**, "Data Definition" (Veri Tanımı) anlamına gelir ve bir giriş veya çıkış dosyasının tanımlandığını gösterir.
- **DUMMY**, bir özel sistem veri seti türüdür. DUMMY ifadesi, bir çekirdek bellek dökümü oluşturulmayacağını ve gerçek bir dosyaya yazılmayacağını gösterir.

Bu ifade, bir iş adımının çekirdek bellek dökümü oluşturmasını engeller ve yerine hiçbir şey yapmadığını belirtir ve pasif durumda kalır. Bu, çekirdek bellek dökümünün gereksiz olduğu veya iş adımı için önemsiz olduğu durumlarda kullanılabilir.

```vbnet
//SYSUDUMP  DD DUMMY
```

Bu ifade sistem dump (sistem dökümü) oluşturulmayacağını belirtir.

- **SYSUDUMP** ifadesi, sistem dump'unun adını veya tanımını belirtir. Bu ad, dökümün ne amaçla kullanıldığını veya içeriğini yansıtabilir.
- **DD**, "Data Definition" (Veri Tanımı) anlamına gelir ve bir giriş veya çıkış dosyasının tanımlandığını gösterir.
- **DUMMY**, bir özel sistem veri seti türüdür. DUMMY ifadesi, bir sistem dump (döküm) oluşturulmayacağını ve gerçek bir dosyaya yazılmayacağını gösterir.

Bu ifade, bir iş adımının sistem dump oluşturmasını engeller ve yerine hiçbir şey yapmadığını belirtir. Sistem dump, bir programın veya işlemin beklenmeyen bir hata veya çökme durumunda sistem durumunu incelemek için kullanılan bir dökümdür. Ancak bazı durumlarda sistem dump oluşturmak gerekmeyebilir veya iş adımı için önemsiz olabilir.