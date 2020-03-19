---
layout: post
title: Çalışma Notlarım - 1
date: 2020-03-19 23:18:00 +0300
description: Jenerikler, Repository Tasarım Deseni ve Unit of Work Tasarım Deseni # Add post description (optional)
img: # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [repository-tasarim-deseni, unit-of-work-tasarim-deseni, C#-jenerikler]
---

## Generics

class Gen\<T> ile tamınlanır.

### Generics Tip Güvenliği Nasıl arttırılır

Veri tipini object olarak belirleyip generic olmadan da generic işlevselliği yapılabilir fakat gen. yapmanın yararı nedir? Genericler classı ilgilendiren tüm işlemlerde tip güvenliğini garanti altına alır.

    class Nongen {
        object ob;
        
        public object GetOb(){
            return ob;
        }
    }

    Nongen iob = new Nongen(23);
    Nongen sob = new Nongen("test");

    int v = iob.GetOb();

    iob = sob // bu derlenir ancak kavramsal olarak yanlıştır.

    v = (int) iob.getOb(); // Çalışma zamanı hatası

## İki Parametreli Jeneric Sınıf

class Gen\<T, V> olarak tanımlanır.

## Kısıtlı Tipler (constraid types)

### Temel Sınıf Kısıtlaması

    where T : temel-sınıf-adı 
ile tanımlanır.

    class A {
        public void Hello() {
            Console.WriteLine("Hello");
        }
    }

    class B : A { }
    class C { }

    class Test<T> where T : A {
        T obj;
    }
    // Test'e aktarılan tüm argümanların temel sınıfı A olmalı.

### Interface Kısıtlaması Kullanmak

Verilen herhangi bir interface kısıtlaması için tip argümanı interface veya bu interface i uygulayan bir tip olmalıdır.

    public interface IPhoneNumber {
        string Number  { get; set; }
        string Name { get; set; }
    }

    class Friend : IPhoneNumber {
        //codes..
    }

    class Supplier : IPhoneNumber  {
        //codes..
    }

    class EmailFriend {
        //codes..
    }

    class PhoneList<T> where T : IPhneNumber {
        //codes..
    }
    // PhoneList e Friend ve Supplier i uygulayabilirim ama EmailFriend hata verir.

### new() Yapılandırıcı Kısıtlamasını Kullanmak

new() yapılandırıcı kısıtlamasını kullanmak jenerik tipinde bir nesne örneği oluşturmamı sağlar. Normalde bir jenerik tip parametresinin örneğini oluşturamam.

    class MyClass() {
        public MyClass() {
            //...
        }
    }

    class Test<T> where T : new() {
        T obj;
        public Test() {
            // Bu, new() kısıtlaması sayesinde calisir.
            obj = new T(); // bir T nesnesi oluşturur
        }
    }

    static void Main() {
        Test<MyClass> x = new Test<MyClass>();
    }

#### Özetlemediğim Konular

- ##### Referans ve Değer Tipi Kısıtlamaları

- ##### İki Tip Parametresinin İlişkilendirmek İçin Kısıtlama Kullanmak

- ##### Birden Çok Kısıtlama Kullanmak

- ##### Tip Parametresinin Varsayılan Nesnesini Oluşturmak

- ##### Jenerik Yapılar

- ##### Bir Jenerik Metot Oluşturmak

- ##### Bir Jenerik Metodu Çağırmak İçin Açık Tip Argümanları Kullanmak

- ##### Jenerik Bir Metot ile Bir Kısıtlama Kullanmak

- ##### Jenerik Delegeler

- ##### Jenerik Interfaceler

ve dahası ...

## Repository Tasarım Deseni

Repository tasarım kalıbı ile veritabanından veri çekmek için yazdığımız kodları bir defa yazarak çok defa kullanmak amaçlanmıştır. Yani tekrar kullanılabilir kod mantığı üzerine kulurmuştur

Bu kalıbın iki farklı kullanımı vardır:

- Generic Repository

- Her tablo için bir repository

İkinci durum bazı özel tablolarda ve özel kontroller, ekstra işlemler için kullanılır.

Bu tasarım çok fazla sorgu gereksinimi olan sistemlerde kullanılır.

    class A
    {
        private int id;
        private string name;
    
        public A(int id, string name)
        {
            this.id = id;
            this.name = name;
        }

        public override string ToString()
        {
            return "ID: " + this.id + " Name: " + this.name;
        }
    }

    class Repository<T> where T : class
    {
        private List<T> list;

        public Repository()
        {
            this.list = new List<T>();
        }

        public void add(T item)
        {
            list.Add(item);
        }

        public void remove(T item)
        {
            list.Remove(item);
        }

        public List<T> getAll()
        {
            return list;
        }
   }

     static void Main(string[] args)
        {
            Repository<A> repository = new Repository<A>();

            A a1 = new A(1, "John Doe");
            A a2 = new A(2, "Mr. Smith");
            A a3 = new A(3, "Mrs. Smith");
            A a4 = new A(4, "Jr. Smith");

            repository.add(a1);
            repository.add(a2);
            repository.add(a3);
            repository.add(a4);

            foreach(A a in repository.getAll())
            {
                Console.WriteLine(a);
            }
        }

## Unit of Work Pattern

Bu kalıp, iş katmanınızda yaptığınız her değişikliği anlık olarak veritabanına anlık olarak yansıtılmaması ve işlemlerin biriktirilerek toplu olarak bir defa veritabanı bağlantısı açarak yapılmasıdır.

    // A ve B sınıfını databasedeki bir tablo olduğunu varsayalım.
    class A
    {
        private int id;
        private string name;
    
        public A(int id, string name)
        {
            this.id = id;
            this.name = name;
        }

        public override string ToString()
        {
            return "ID: " + this.id + " Name: " + this.name;
        }
    }

    class B
    {
        private int id;
        private string task;
        
        public B(int id, string task)
        {
            this.id = id;
            this.task = task;
        }

        public override string ToString()
        {
            return "ID: " + this.id + " Task: " + this.task;
        }
    }
    
    // Burası bir tablo için işlemler yapabileceğiniz sınıf.
    class Repository<T> where T : class
    {
        private List<T> list;

        public Repository()
        {
            this.list = new List<T>();
        }

        public void add(T item)
        {
            list.Add(item);
        }

        public void remove(T item)
        {
            list.Remove(item);
        }

        public List<T> getAll()
        {
            return list;
        }

        public void save()
        {
            //save
        }
    }

    // Burası bir çok tabloya erişebileceğiniz sınıf.
    class UnitOfWork
    {
        // database connection will be here

        public Repository<A> repositoryA;
        public Repository<B> repositoryB;

        public UnitOfWork()
        {
            repositoryA = new Repository<A>();
            repositoryB = new Repository<B>();
        }

        public void save()
        {
            // save
        }

    static void Main(string[] args)
        {

            UnitOfWork unitOfWork = new UnitOfWork();

            A a1 = new A(1, "John Doe");
            A a2 = new A(2, "Mr. Smith");
            A a3 = new A(3, "Mrs. Smith");
            A a4 = new A(4, "Jr. Smith");

            B b1 = new B(1, "task1");
            B b2 = new B(2, "task2");
            B b3 = new B(3, "task3");
            B b4 = new B(4, "task4");

            unitOfWork.repositoryA.add(a1);
            unitOfWork.repositoryA.add(a2);
            unitOfWork.repositoryA.add(a3);
            unitOfWork.repositoryA.add(a4);


            unitOfWork.repositoryB.add(b1);
            unitOfWork.repositoryB.add(b2);
            unitOfWork.repositoryB.add(b3);
            unitOfWork.repositoryB.add(b4);

            
            foreach (A a in unitOfWork.repositoryA.getAll())
            {
                Console.WriteLine(a);
            }
            foreach (B b in unitOfWork.repositoryB.getAll())
            {
                Console.WriteLine(b);
            }

            unitOfWork.save();
        }

**Bu Makaleyi Hazırlarken Edindiğim Notlar :**

- **internal:** objeden veya kalıtım almış sınıftan oluşamaz, sadece sınıfın içinden

- **readonly:** sadece constructor içinde değiştirilir.

- **Guid:**  Açılımı Global Unique Identifier, kendileri 16 byte integer olup özel bir tanımlayıcı(unique identifier) gereken tüm bilgisayar ve networklerde kullanabilirsiniz.

        Guid id = Guid.NewGuid();
- **??= işareti:** Sağdaki değeri sadece soldaki değer null ise sola atar.

        int? x = null;
        x ??= 5; // if x == null then x = 5

        //başka bir örnek
        int? a = 10, b = 20, c = null;
        a = c ?? b; // a = 20 olur

- **Transient:** Her erişimde yeni bir instance oluşturulur. Lightweight ve stateless services için en iyisidir.

- **Scoped:** Her request için yeni bir instance ile devam eder.

- **Singleton:**  Uygulama ayağa kaltığı andan itibaren aynı instance ile devam eder.

**Kaynaklar:**

- *Herkes için C# 4.0 : Helbert Schildt*

- *Tasarım Desenleri ve Mimarileri : Ali Kaya - Engin Bulut*

- [Barış Ceviz'in Makalesi](https://medium.com/@peacecwz/net-core-2-0-dependency-injection-i%C3%A7erisinde-ef-core-ile-repository-pattern-kullan%C4%B1m%C4%B1-b%C3%B6l%C3%BCm-1-3e8e0bfa6b7a)

- [Design Patterns: Asp.Net Core Web API, services, and repositories
Part 1: Introduction](https://www.forevolve.com/en/articles/2017/08/11/design-patterns-web-api-service-and-repository-part-1/)

>**NOT:** Bu yazdıklarım yukarda belirtmiş olduğum kaynaklardan aldığım notlardır.
