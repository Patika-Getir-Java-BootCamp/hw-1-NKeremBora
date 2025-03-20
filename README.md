[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/7TXVPuTD)

# Questions

## 1. Why do we need OOP? What are some major OOP languages?

-	Kodun daha yönetilebilir, tekrar kullanılabilir ve anlaşılabilir olmasını sağlar.
-	Gerçek dünya problemlerini modellemeyi kolaylaştırır (nesnelerle ilişkilendirerek).
-	Büyük projelerde modüler bir yapı sunar; bakım ve genişletme maliyeti düşer.

### OOP Dilleri:
- Java
- C++
- C#


## 2 – Interface vs Abstract class ?

### Abstract Class
-	Hem abstract (gövdesiz) hem de gövdeli metotlar içerebilir.
-  Bir sınıf ancak tek bir abstract class’ı extends edebilir.
- Ortak alanlar (field) tanımlanabilir ve constructor bulundurabilir.

### Interface
-	Genellikle sadece gövdesiz metot bildirimi içerir (Java 8+ ile default/static metotlar da eklenebilir).
- Bir sınıf birden fazla interface’i implements edebilir.
- Ortak davranış sözleşmesi (contract) tanımlamak için kullanılır.

Bir class içinde “neyi” yapabileceğinizi sabitlemek için interface, “nasıl” yapacağınızın bir kısmını ortak bırakıp bir kısmını zorunlu kılmak için abstract class kullanılır.

### Örnek Kod

```java
// Abstract class: Ortak davranışları ve değişkenleri tanımlayabilir
abstract class Animal {
    String name;

    Animal(String name) {
        this.name = name;
    }

    abstract void makeSound(); // Abstract method (gövdesiz)

    void sleep() { // Gövdeli method
        System.out.println(name + " is sleeping...");
    }
}

// Interface: Sadece metot imzaları içerir
interface CanFly {
    void fly();
}

// Bir sınıf hem abstract class'ı miras alabilir hem de bir interface'i implemente edebilir
class Bird extends Animal implements CanFly {
    Bird(String name) {
        super(name);
    }

    @Override
    void makeSound() {
        System.out.println(name + " is chirping...");
    }

    @Override
    public void fly() {
        System.out.println(name + " is flying...");
    }
}

// Kullanım örneği
public class Main {
    public static void main(String[] args) {
        Bird parrot = new Bird("Parrot");
        parrot.makeSound();
        parrot.sleep();
        parrot.fly();
    }
}
```
## 3 – Why wee need equals and hashcode ? When to override ?
### Neden gerekli?
- İki nesnenin “anlamsal” olarak eşitliğini kontrol etmek için equals metodu kullanılır.
- Nesnenin hash tabanlı veri yapılarında (HashMap, HashSet gibi) doğru şekilde yönetilmesi için hashCode gereklidir.
### Ne zaman override?
- Eğer nesneleri kendi alanlarına göre “özel” bir eşitlik mantığı ile kıyaslamak istiyorsanız (örn. kimlik numarası, kullanıcı adı vb.) mutlaka equals ve hashCode birlikte override etmelisiniz.

equals ve hashCode her zaman tutarlı çalışmalıdır. Eğer equals iki nesneyi eşit görüyorsa, hashCode’ları da aynı olmalıdır.

### Örnek Kod

```java
public class Person {
    private int id;
    private String username;

    public Person(int id, String username) {
        this.id = id;
        this.username = username;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (!(obj instanceof Person))
            return false;
        Person other = (Person) obj;
        return this.id == other.id &&
               (this.username == null ? other.username == null : this.username.equals(other.username));
    }

    @Override
    public int hashCode() {
        int result = 17;
        result = 31 * result + id;
        result = 31 * result + (username != null ? username.hashCode() : 0);
        return result;
    }
}
```

## 4 – Diamon problem in Java ? How to fix it?

### Diamond Problem
- Çoklu kalıtım (multiple inheritance) nedeniyle, bir alt sınıfın aynı metodu farklı miras zincirlerinden alması durumunda oluşan belirsizliktir.
- C++’ta klasik bir problem olarak bilinir. Java, çoklu kalıtımı sınıflarda desteklemez ama interface’lerde default metotlar ile benzer bir sorun yaratabilir.
  
### Nasıl çözülür?
- Java’da sınıflar sadece tek bir sınıftan extends edebilir, bu yüzden “class” bazında diamond yoktur.
-	Interface’lerde default metot çakışması olduğunda, alt sınıf metodu override ederek hangi davranışı seçeceğini belirtmelidir.

Bir interface’de aynı metot ismini taşıyan default metotlar varsa alt sınıfta “super.method()” şeklinde spesifik çağrım yapılabilir veya direkt override ile kendi mantığınızı yazabilirsiniz.

### Örnek Kod

```java
interface InterfaceA {
    default void greet() {
        System.out.println("Hello from InterfaceA");
    }
}

interface InterfaceB {
    default void greet() {
        System.out.println("Hello from InterfaceB");
    }
}

public class MyClass implements InterfaceA, InterfaceB {
    // Burada greet() metodu hem InterfaceA'dan hem InterfaceB'den geliyor.
    // Derleyici "hangisini kullanayım?" diye soracaktır.
}
```

## 5 – Why we need Garbagge Collector ? How does it run ?

- Java’da bellek yönetimi otomatik yapılır, programcının bellek tahsis etme ve serbest bırakma süreçleriyle uğraşmasını engeller.
- Kullanılmayan nesneleri tespit edip belleği geri kazanır.
- Genellikle heap dolmaya yaklaştığında veya belli bir eşiğe ulaştığında otomatik olarak tetiklenir.
- System.gc() gibi metotlar aracılığıyla “GC isteği” gönderilebilir ama Garbage Collector’ın gerçekten o anda çalışacağı garanti değildir. JVM, kendi optimizasyonuna göre en uygun zamanda çalıştırır.
- Eskiden nesne toplanmadan önce son bir temizlik yapmak için finalize() kullanılabiliyordu; ancak bu yöntem Java 9 ve sonrasında deprecated edildi. Tavsiye edilmez, çünkü GC’nin ne zaman çalışacağı garanti olmadığı gibi finalize() sıralı (deterministic) olarak çağrılmaz.

### Çalıştırma İpuçları

Java programını çalıştırırken Garbage Collector (GC) loglarını görmek için aşağıdaki komutları kullanabilirsiniz.

```bash
# GC loglarını detaylı görmek için:
java -verbose:gc GCDemo

# Alternatif olarak, GC loglarını görmek için:
java -XX:+PrintGC GCDemo
```

## 6 – Java ‘static’ keyword usage ?
- Sınıfın örneği oluşturulmadan (instance almadan) ulaşılmak istenen üyeler için kullanılır.
- Static değişkenler (class-level variable), static metotlar ve static bloklar tanımlanabilir.
- Aynı sınıfta tüm örnekler için ortak bir değerin tutulması istendiğinde veya araç (helper) metotlar oluşturmak istediğimizde kullanışlıdır.

Genellikle static yapılar stateless (durumsuz) olmalıdır. Çünkü statik üyeler (metotlar ve değişkenler) sınıf seviyesinde paylaşıldığı için, global bir durum (state) yaratmak tehlikeli olabilir.

## 7 – Immutability means ? Where, How and Why to use it ?

### Nedir?
Bir nesnenin oluşturulduktan sonra iç durumunun (alanlarının değerlerinin) değiştirilememesidir.

### Nerede / Nasıl?
- Genelde String nesneleri immutabledır.
- Value Object (örneğin; Para, Tarih gibi) oluştururken de immutable tasarım tercih edilir.

### Neden?
- Thread-safe bir yapı sağlar, senkronizasyon zorluğunu azaltır.
- Yan etkileri (side-effect) önleyerek hata yapmayı zorlaştırır.
- Cache gibi optimizasyonlar için çok uygundur.

Immutable nesneler oluştururken, koleksiyonları da kopyalayarak saklamak gerekir (savunma kopyası – defensive copy) ki dışarıdan değiştirilemesin.
### Örnek Kod

```java
public final class ImmutablePerson {
    private final String name;
    private final List<String> hobbies;

    public ImmutablePerson(String name, List<String> hobbies) {
        this.name = name;
        // Savunma kopyası: dışarıdan gelen liste kopyalanır.
        this.hobbies = new ArrayList<>(hobbies);
    }

    public String getName() {
        return name;
    }

    public List<String> getHobbies() {
        // Koleksiyonun direkt referansını vermek yerine,
        // ya kopyasını döndürürüz ya da değiştirilemez (unmodifiable) bir versiyonunu
        return Collections.unmodifiableList(new ArrayList<>(hobbies));
    }
}
```

## 8 – Composition and Aggregation means and differences ?

### Kompozisyon
- “Sahip-olma” ilişkisi (Has-A) olup, bağlı nesneler olmadan üst nesnenin anlamlı çalışamayacağı bir durumdur.
- Örn: “Araba” ve “Motor” ilişkisi. Motor olmadan araba çalışmaz, motor da genellikle arabaya özeldir.

### Agregasyon
- Daha zayıf “Sahip-olma” ilişkisi. İki nesne birbirine bağımlı değildir, bir nesnenin ömrü diğerine bağlı olmayabilir.
- rn: “Sınıf” ve “Öğrenci”. Öğrenci, sınıfın dışında da var olabilir.

## 9 – Cohesion and Coupling means and differences ?

### Cohesion (Uyumluluk)
- Bir sınıf ya da modül içindeki öğelerin ne kadar ilgili/odaklı olduğunu anlatır.
- Yüksek cohesion, bakımı ve anlaşılabilirliği kolay kod demektir.

### Coupling (Bağlılık)
- Farklı sınıf veya modüllerin birbirlerine ne kadar bağımlı olduğunu ifade eder.
- Düşük coupling, sistemdeki parçaların birbirine daha az bağımlı olduğu, değişikliğin etkisinin minimumda kaldığı anlamına gelir.

“High cohesion, low coupling” prensibi, SOLID gibi tasarım ilkelerinin de temelidir.

## 10 - Heap and Stack means and differences ?

### Stack
- Metot çağrıları, yerel değişkenler (primitive tipler, referanslar) bu alanda tutulur.
- LIFO (Last In First Out) şeklinde çalışır, çok hızlı erişim sunar ama sınırlı ömürlüdür (metot bitince yok olurlar).
  
### Heap
- Nesnelerin asıl verileri heap bölgesinde saklanır.
- Büyük bir bellek alanıdır, GC tarafından yönetilir.
- Erişim ve yönetim stack’e göre daha yavaştır.

## 11 – Exception (İstisna) nedir? Hangi tipleri vardır?
Programın normal akışını bozan beklenmeyen hatalara (durumlara) verilen isimdir.
### Türleri
- **Checked Exception:** Derleme (compile-time) sırasında kontrol edilir. Kodun try-catch veya throws kullanarak yönetmesi beklenir (IOException, SQLException vb.).
- **Unchecked Exception (RuntimeException):** Derleyici tarafından zorunlu tutulmaz, program çalışırken ortaya çıkar (NullPointerException, ArithmeticException vb.).
- **Error:** JVM kaynaklı ciddi hatalar (OutOfMemoryError gibi). Genellikle yakalamaya veya yönetmeye çalışmayız.

@ControllerAdvice, istisnaları global ölçekte yakalamaya yarar. İstisna türlerine göre farklı metodlar tanımlayarak, özelleştirilmiş hata mesajları veya loglama yapabilirsiniz.

### Örnek Kod

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(IOException.class)
    public ResponseEntity<?> handleIOException(IOException e) {
        // Uygun bir cevap (Response) oluşturabilir,
        // log atabilir, kullanıcıya hata mesajı dönebilirsiniz.
        return new ResponseEntity<>(e.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<?> handleGenericException(Exception e) {
        // Diğer tüm istisnalar
        return new ResponseEntity<>(e.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```
## 12 – How to summarize ‘clean code’ as short as possible ?

- Okunabilirliği yüksek, bakımı ve test edilebilirliği kolay, gereksiz karmaşıklıktan arınmış kod yazma yaklaşımıdır.
- Kod, yazanın değil; okuyanın kolay anlayacağı şekilde yazılmalıdır.

## 13 - What is the method of hiding in Java ?

- Bir alt sınıfta, üst sınıftaki static metodu aynı imza ile tekrar tanımlarsanız (aynı isim ve parametreler), overriding değil “hiding” gerçekleşir.
- Bu durumda hangi metot çağrılacağı, sınıfın (referans tipinin değil) gerçek tipi yerine, değişkenin derleme zamanındaki tipiyle belirlenir.
- Static metotlar polymorphic davranmazlar, bu yüzden “override” yerine “hide” söz konusudur.

### Örnek Kod

```java
lass UstSinif {
    public static void selamVer() {
        System.out.println("UstSinif - statik metot");
    }
}

class AltSinif extends UstSinif {
    // UstSinif'deki statik metodu aynı imza ile yeniden tanımladık.
    public static void selamVer() {
        System.out.println("AltSinif - statik metot");
    }
}

public class Test {
    public static void main(String[] args) {
        UstSinif nesne2 = new AltSinif(); // Referans tipi UstSinif
        nesne2.selamVer(); // Referans tipi UstSinif olduğu için UstSinif'ın metodu çağrılır.
        AltSinif.selamVer(); // Doğrudan sınıf ismi üzerinden çağırırsak: AltSinif'ın metodu çağrılır.
    }
}
```
## 14 - What is the difference between abstraction and polymorphism in Java ?
- **Abstraction (Soyutlama):** Nesnenin veya sınıfın “ne yapacağını” (metot imzalarını) tanımlar; “nasıl yapacağını” ise gizler. Örneğin, abstract class veya interface yapılarıyla “sözleşme” (metot isimleri ve parametreleri) belirlenir, ama bu metotların içi (implementasyon) soyut kalır.
- **Polymorphism (Çok Biçimlilik):** Aynı metot imzasını paylaşan alt sınıfların bu metodu farklı şekillerde gerçekleştirmesidir. Kod çalışırken (runtime) hangi sınıfın metodu çağrılacaksa ona göre davranış değişir (override mekanizması).

### Örnek Kod

```java
// 1. Soyutlama için interface (veya abstract class) kullanıyoruz.
interface Animal {
    void speak(); // Sadece metot imzası, içi yok
}

// 2. Alt sınıflar, farklı davranışlar (polymorphism) uygular.
class Dog implements Animal {
    @Override
    public void speak() {
        System.out.println("Hav hav!");
    }
}

class Cat implements Animal {
    @Override
    public void speak() {
        System.out.println("Miyav!");
    }
}

public class Main {
    public static void main(String[] args) {
        // 3. Polymorphism: Animal tipindeki değişken,
        //    farklı implementasyonlara (Dog, Cat) ait metotları çağırabilir.
        Animal myDog = new Dog();
        Animal myCat = new Cat();

        myDog.speak(); // Konsolda "Hav hav!"
        myCat.speak(); // Konsolda "Miyav!"
    }
}
```
