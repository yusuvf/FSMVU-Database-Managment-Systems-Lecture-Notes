# Database Managment Systems

Bu doküman, FSMVÜ Bilgisayar Mühendisliği bölümünün duayen isimlerinden olan **Ali Nizam** hocamızın verdiği Veritabanı Yönetim Sistemleri dersinin notları düzenlenerek oluşturulmuştur.

 **Dökümanı hazırlayan kişi, derste çözülen örneklerin hepsinin bu dokümanda olduğunu ve her çözümün doğru olduğunu kesinlikle iddia etmemektedir ve sorumluluk kabul etmemektedir.**

 Eğer bir hata tespit ederseniz bana mail yoluyla ulaşabilirsiniz: *yusuf.donmez@outlook.com.tr*

## Relational Algebra

σ --> Selection

π --> Projection

ρ --> Rename Relation

γ --> Group by

∪ --> Union

∩ --> Intersection

x --> Cross Join

-(minus) --> Subtraction

⋈ -> Natural Join

⟕ -> Left Outer Join

⟖ -> Right Outer Join



### Örnekler

Bu bölümdeki örnekler **RelaX - relational algebra calculator** isimli editörde çalışacak şekilde yazılmıştır. İlgili editöre [buraya tıklayarak ulaşabilirsiniz.](https://dbis-uibk.github.io/relax/calc.htm)

ReLaX editöründe sol kısımdaki kutucuğa tıklayarak ilgili data setini seçmeniz gerekmektedir. (*Herhangi bir örneğe farklı bir data seti yüklemeniz gerekebilir.*)

```
σ model>2000 ∧ maker='A' (Product)
```

---

```
π maker (Product)
```

---

```
 π   maker   (σ model>2000 (Product))
```

---

>**PC ve Laptop'ların hangi fiyatlarda satıldığını bulunuz.**
```
π price (PC) ∪ π price (Laptop)
```

---

>**Laptop'lardan farklı PC fiyatlarını  bulunuz.**
```
π price (PC) - π price (Laptop)
```

---

>**Hem  PC ve hem de laptop olarak üretmemiş modelleri bulunuz**
- **1. Çözüm**
```
π model (Product) - (π model (PC) ∪ π model (Laptop))
```
- **2. Çözüm**
```
(π model (Product) - π model (PC)) ∩ (π model (Product) - π model Laptop))
```

---

>**Hiç PC veya laptop'tan birisi olarak üretmemiş modelleri bulunuz**
- **1. Çözüm**
```
(π model (Product) - π model (PC)) ∪ (π model (Product) - π model (Laptop))
```
- **2. Çözüm**
```
PCUretmeyenler = (π model (Product) - π model (PC))
LaptopUretmeyenler = (π model (Product) - π model (Laptop))
PCUretmeyenler ∪ LaptopUretmeyenler
```

---

>**Product tablosunu tekrar isimlendiriniz.**
```
ρ p (Product)
```

---

>**PC'lerin bilgilerine üreticilerinin isimlerini ekleyerek yazdırınız.**
```
σ PC.model=Product.model (PC ⨯ Product)
```

---

>**A üreticisi tarafından üretilen PC'lerin hızlarını bulunuz.**
```
π PC.speed (σ Product.maker='A' (σ Product.model=PC.model (PC ⨯ Product)))
```

---

>**Aynı hızda çalışan ve aynı fiyata satılan PC ve laptop modellerini bulunuz.**
```
σ PC.speed = Laptop.speed AND PC.price=Laptop.price (PC x Laptop)
```

---

>**Dersleri profösör isimlerleri ile birlikte gösteriniz**
```
σ professor.employee_id=lecture.lectured_by (professor x lecture)
```
***Bileşke ile çözüm***
```
professor ⨝ professor.employee_id=lecture.lectured_by lecture
```

---

>**Öğrencileri aldıkları derslerle birlikte gösteriniz**
- **1. Çözüm**
```
π student.name, lecture.title (lecture ⨝(lecture.lecture_id=attends.lecture_id)  attends ⨝ (attends.student_id=student.student_id)  student )
```
- **2. Çözüm**
```
π student.name, lecture.title (lecture ⨝  attends ⨝  student )
```

---

>**Profesörün veridiği dersleri gösteriniz.**
```
 π professor.name,lecture.title (professor  ⟕ professor.employee_id=lecture.lectured_by lecture)
```

---

>**Dersleri veren profesörleri gösteriniz.**
```
 π professor.name,lecture.title (professor  ⟖ professor.employee_id=lecture.lectured_by lecture)
```

---

>**Hiç ders vermeyen profesörleri gösteriniz.**
```
σ lecture.lecture_id = NULL (π professor.name,lecture.title,lecture.lecture_id (professor  ⟕ professor.employee_id=lecture.lectured_by lecture))
```

---

>**Hiç ders vermeyen profesörleri gösteriniz. (Outer join kullanmadan bulunuz.)**
- **1. Yöntem**
```
dersVermeyenId = π professor.employee_id (professor) - π lecture.lectured_by (lecture)
```
```
professor ⨝ dersVermeyenId
```
- **2. Yöntem**
```
π professor.employee_id,professor.name (professor) -
π professor.employee_id,professor.name (professor ⨝ professor.employee_id=lecture.lectured_by lecture)
```

---

>**25403 nolu öğrenci ile tüm derslerde aynı notu alan öğrencileri (id) bulunuz.**
```
S = π grade.lecture_id,grade.grade (σ grade.student_id=25403 (grade))
R = π  grade.student_id, grade.lecture_id,grade.grade (σ grade.student_id≠25403 (grade))
```
```
R ÷ S
```

---

>**Derslerdeki ortalama notları bulunuz.**
```
γ lecture_id; AVG(grade)->ortalamaNot  (grade)
```

---

>**profesör tiplerine göre kaçar adet profesör olduğunu bulunuz.**
```
γ type; count(*)-> adet  (professor)
```

---

>**Birden fazla ders veren profesörleri bulunuz.**
```
verilenDersSayisi= γ lectured_by; count(*)-> adet  (lecture)
```
```
σ adet>1 verilenDersSayisi
```

---

>**Verdiği tüm derslerin kredisi aynı olan prefesörleri bulunuz.**
```
 dersMinMax= γ lectured_by; min(credits)-> min, max(credits) -> max (lecture)
 ```
 ```
σ min=max (dersMinMax)
```

---

>**Bir profesör kaç adet ders vermektedir.**
```
σ title ='Math' (γ lectured_by; COUNT(*)->adet, AVG(credits)->ortalamaKredi, MAX(credits)-> maxKredi (lecture))
```
```
γ count(*)->adet, max(credits)->mk lecture
```

---


>**1. Tüm okulda  en yüksek notu alan öğrencinin ismini bulunuz.**
```
yuksekNotIliskisi=γ max(grade)-> maxNot grade
```
```
ogrenciIDIliskisi=yuksekNotIliskisi ⨝ maxNot = grade  grade
```
```
ogrenciIDIliskisi ⨝ grade.student_id=student.student_id  student
```
**2. Kredisi 3' üzerinde olan derslerde en yüksek notu alan öğrencinin ismini bulunuz**
```
Ders3Uzeri = σ credits>3 lecture
```
```
Not3Uzeri = Ders3Uzeri ⨝ lecture.lecture_id=grade.lecture_id grade
```
```
GradeMax3Uzeri = γ max(grade)-> maxNot Not3Uzeri
```
```
GradeMax3Uzeri ⨝ maxNot=grade.grade (Not3Uzeri ⨝ grade.student_id=student.student_id student)
```
>**3. Profesörlerin ortalama kaç kredilik ders verdiğini isimleriyle birlikte bulunuz.**
```
γ professor.employee_id, professor.name; AVG(lecture.credits)->kredi (professor ⨝ professor.employee_id=lecture.lectured_by lecture)
```
>**4. Hiç ders vermeyen profesörleri bulunuz**

>**5. Kant'in verdiği her ders için en yüksek notu bulunuz.**

>**6. Derslerin ön şartını sağlayan öğrencilerin isimlerini bulunuz**

>**7. Schopenhauer'ın aldığı dersleri alan diğer öğrencilerin isimlerini bulunuz.**

# SQL


>**Tüm satır ve sütunları seçmek**
```SQL
select * from employees;
```

---

>**İsim, soyisim ve maaş sütunları seçmek**
```SQL
select first_name,last_name,salary from employees;
```

---

>**maaş >10000 olan kişilerin İsim, soyisim ve maaş sütunları seçmek**
```SQL
SELECT first_name,last_name,salary
FROM employees
WHERE salary>10000
```

---

>**maaş >10000 ve 90 nolu departmanda çalışan kişilerin İsim, soyisim ve maaş sütunları seçmek**
```SQL
SELECT first_name,last_name,salary
FROM employees
WHERE salary>10000
AND department_id=90
```

---

>**İsmi A harfi ile başlayan kişileri bulunuz.**
```SQL
SELECT first_name,last_name,salary
FROM employees
WHERE first_name LIKE 'A%'
```

---

>**İsminin ikinci harfi olan kişileri bulunuz.**
```SQL
SELECT first_name,last_name,salary
FROM employees
WHERE first_name LIKE '_A%'
```

---

>**İçinde % geçenleri bulunuz.**
```SQL
SELECT first_name,last_name,salary
FROM employees
WHERE first_name LIKE '%\%%' ESCAPE '\'
```

---

>**Maaşı 10000 ile 20000 dolar arasında olan personelleri bulunuz.**
```SQL
SELECT first_name,last_name,salary
FROM employees
WHERE salary >= 10000
AND salary <= 20000
```

---

>**İsmi 'Adam' ile 'Clara 'arasında olan personelleri bulunuz.**
```SQL
SELECT first_name,last_name,salary
FROM employees
WHERE first_name BETWEEN 'Adam' AND 'Clara'
```

---

>**AC_MGR, SA_REP veya IT_PROG ünvanı olan personelleri bulunuz**
```SQL
SELECT first_name,last_name,salary,job_id
FROM employees
WHERE job_id = 'AC_MGR' OR job_id = 'SA_REP' OR job_id = 'IT_PROG'
```
```SQL
SELECT first_name,last_name,salary,job_id
FROM employees
WHERE job_id IN ('AC_MGR', 'SA_REP','IT_PROG')
```

---

>**Unvanı AC_MGR, SA_REP veya IT_PROG haricinde olan personelleri bulunuz.**
```SQL
SELECT first_name,last_name,salary,job_id
FROM employees
WHERE job_id NOT IN ('AC_MGR', 'SA_REP','IT_PROG')
```

---

>**Commission_pct NULL olan personelleri bulunuz.**
```SQL
SELECT first_name,last_name,salary,job_id,Commission_pct
FROM employees
WHERE commission_pct IS NOT NULL
```

---

>**Kişi bazında ödenenen toplam maaşları gösteriniz.**
```SQL
SELECT first_name,
        last_name,salary,job_id,Commission_pct,
        NVL(commission_pct,0) AS commission_pct_null0,
        salary+salary*commission_pct total_salary,
        salary+salary*NVL(commission_pct,0) total_salaryWithNVL
FROM employees
```

---

>**Tablodaki tüm kolanlar + özel bir kolonu gösterme**
```SQL
select department_id,e.* from employees e
```

---

>**Alias kullanımı**
```SQL
SELECT first_name,
        last_name,salary,job_id,Commission_pct,
        NVL(commission_pct,0) AS commission_pct_null0,
        salary+salary*commission_pct total_salary,
        salary+salary*NVL(commission_pct,0) total_salaryWithNVL
FROM employees
WHERE salary+salary*NVL(commission_pct,0)>15000
```

---

>**Unvanı AC_MGR, SA_REP, IT_PROG haricinde veya boş olmayan olan personelleri bulunuz**
```SQL
SELECT first_name,last_name,salary,job_id
FROM employees
WHERE job_id NOT IN ('AC_MGR', 'SA_REP','IT_PROG')
   OR job_id IS NOT NULL
```

---

>**Unvanı AC_MGR, SA_REP, IT_PROG haricinde ve boş olmayan olan personelleri bulunuz**
```SQL
SELECT first_name,last_name,salary,job_id
FROM employees
WHERE job_id NOT IN ('AC_MGR', 'SA_REP','IT_PROG')
 AND job_id IS NOT NULL
```

---

>**Order By kullanımı**
- **Ad'a göre sıralayınız.**
```SQL
SELECT first_name ||' '||last_name name,salary,job_id
FROM employees
ORDER BY name DESC,salary DESC
```

---

>**DISTINCT**
```SQL
SELECT DISTINCT * FROM employees
```

---


>**Toplam kayıt sayısı**
```SQL
SELECT COUNT(*) FROM employees
```

---

>**Departmanların toplam personel sayısı**
```SQL
SELECT (department_id+1)+1,COUNT(*)
FROM employees
GROUP BY department_id+1
```

---

>**Departmanların toplam personel sayısı**
```SQL
SELECT NVL(department_id,0)+10,job_id,COUNT(*) ,AVG(salary),MIN(salary),SUM(salary)
FROM employees
GROUP BY NVL(department_id,0),job_id
ORDER BY NVL(department_id,0),job_id
```

---

>**Toplam aylık ödemesi 100000 dolardan fazla olan birimleri bulunuz**
```SQL
SELECT department_id, SUM(salary)
FROM employees
WHERE job_id='SA_REP'
GROUP BY department_id
HAVING SUM(salary)>100000
```

---

>**İsim ve soyismi aynı olan farklı kişileri bulunuz.**
```SQL
   SELECT first_name,last_name, COUNT(*)
   FROM employees
   GROUP BY first_name,last_name
   HAVING COUNT(*)>1
```

---

>**En düşük ve en yüksek maaş arasındaki fark 5000 dolardan fazla olan unvanları bulunuz.**
```SQL
    SELECT job_id,MIN(salary),max(salary)
    FROM employees
    GROUP BY job_id
    HAVING max(salary) - MIN(salary) > 5000
```

>**İçerisinde 3000 TL üzerinde maaş aalan kişilerin çalıştığı ortalama maaşı 5000'den fazla ve çalışan sayısı 10 üzerinde  olan birimleri bulunuz.**
```SQL
SELECT department_id, AVG(salary) ,count(*)
FROM employees
WHERE salary>3000
GROUP BY department_id
HAVING AVG(salary)>5000 AND count(*)>10;
```

---

>**100 numaralı kişinin çalıştığı birimin adını getiriniz.**
```SQL
SELECT * FROM departments
WHERE department_id = (SELECT department_id
                        FROM employees
                        WHERE employee_id=100);
```

---

>**En düşük maaşı alan kişileri bulunuz.**
```SQL
SELECT * FROM employees
WHERE salary= (SELECT min(salary)
              FROM employees)
```

---

**Birden fazla kayıt dönen alt sorgular**

>**ST Clerklerin çalıştığı departmanların isimlerini bulunuz.**
```SQL
SELECT * FROM departments
WHERE department_id IN (SELECT department_id
                        FROM employees
                        WHERE job_id='SA_REP');
```

---

>**Birden fazla birimde kullanılan unvanların (job_id) bulunuz.**
```SQL
SELECT job_id,COUNT(DISTINCT department_id)
FROM employees
GROUP BY job_id
HAVING COUNT(DISTINCT department_id)>1
ORDER BY job_id
```

---

>**100 numaralı kişiyle aynı unvan ve aynı departmanda çalışan kişiler varsa bulunuz.**
- **Hatalı çözüm**
```SQL
SELECT * FROM employees
WHERE department_id IN (SELECT department_id
                        FROM employees
                        WHERE employee_id=148)
     AND job_id IN   (SELECT job_id
                        FROM employees
                        WHERE employee_id=148);
```
- **Doğru çözüm**
```SQL
SELECT * FROM employees
WHERE (department_id,job_id) IN (SELECT department_id,job_id
                              FROM employees
                              WHERE employee_id=148)
```

---

>**148 numaralı kişiyle aynı maaş ve aynı departmanda çalışan kişiler varsa bulunuz.**
- **Hatalı çözüm**
```SQL
SELECT * FROM employees
WHERE department_id IN (SELECT department_id
                        FROM employees
                        WHERE employee_id=148)
     AND salary IN   (SELECT salary
                        FROM employees
                        WHERE employee_id=148);
```
- **Doğru Çözüm**
```SQL
SELECT * FROM employees
WHERE (department_id,salary) IN (SELECT department_id,salary
                              FROM employees
                              WHERE employee_id=148)
```

---

>**Maaşı 5000'den küçük olan kişilerle aynı birim ve maaşta çalışan kişileri bulunuz.**
```SQL
SELECT count(*) FROM employees
WHERE job_id IN (SELECT job_id
                        FROM employees
                        WHERE salary<5000)
     AND salary IN   (SELECT salary
                        FROM employees
                        WHERE salary<5000);
```
```SQL
SELECT count(*) FROM employees
WHERE (job_id,salary) IN (SELECT job_id,salary
                               FROM employees
                               WHERE salary<5000)
```                               

---

>**Her birimin en düşük maaşlı çalışanın ismini bulunuz.**
```SQL
SELECT * FROM employees
WHERE (department_id,salary) IN (SELECT department_id,min(salary)
                            FROM employees
                            GROUP BY department_id)
```

---

>**Kendi ünvan maaş aralığının orta noktasının üzerinde maaş alan kişileri bulunuz.**
```SQL
SELECT *
FROM employees ust
WHERE job_id = (SELECT job_id
                 FROM jobs alt
                 WHERE ust.job_id=alt.job_id
                   AND ust.salary>(alt.min_salary+alt.max_salary)/2)
```

---

>**Kendi biriminin ortalamasından daha fazla maaş alan kişileri bulunuz.**
```SQL
SELECT * FROM employees ust
WHERE ust.salary> (SELECT AVG(salary)
                   FROM employees alt
                   WHERE alt.department_id=ust.department_id)
```

---

>**Kendi yöneticisinden falza maaş alan kişileri bulunuz.**
```SQL
SELECT * FROM employees ust
WHERE ust.salary > (SELECT alt.salary FROM employees alt
                    WHERE alt.employee_id=ust.manager_id)
```

---

>**100'nolu birimde çalışanları inline view yöntemiyle iki aşamalı sorgulayınız.**
```SQL
SELECT *
FROM (SELECT * FROM employees WHERE department_id=100) empYuz
```

---

>**Row id ve ron numarasını seçiniz.**
```SQL
SELECT rownum,rowid,first_name,last_name
FROM employees
```

---

>**Maaş ortalaması en yüksek 5 departmanı bulunuz.**
```SQL
SELECT rownum,department_id, ortalama_maas
    FROM (SELECT department_id,TRUNC(AVG(salary)) ortalama_maas
          FROM employees
          GROUP BY department_id
          ORDER BY ortalama_maas DESC)
WHERE rownum<6
```

---

>**Sıralamada en yüksek maaşı alan 5. kişiyi bulunuz.**
```SQL
SELECT sira_no,emp5.* FROM (
            SELECT rownum sira_no,employee_id,first_name,last_name,salary
            FROM employees
            WHERE rownum<6
            ORDER BY salary DESC) emp5
WHERE sira_no=5
```

---

>**Personelin  departman isimlerini gösteriniz.**
```SQL
SELECT first_name, last_name, (SELECT department_name
                               FROM departments
                               WHERE department_id=ust.department_id) department_name
FROM employees ust
```

---

>**Unvanının maaş ortalaması birimini maaş ortalamasından fazla olan kişileri bulunuz.**
```SQL
SELECT * FROM employees ust
WHERE (SELECT AVG(salary)
      FROM employees
      WHERE job_id=ust.job_id )>
      (SELECT AVG(salary)
      FROM employees
      WHERE department_id=ust.department_id)
```

---    

>**Çalışanı olmayan birimleri bulunuz.**
```SQL
SELECT * FROM departments
WHERE department_id NOT IN (SELECT department_id FROM employees WHERE department_id IS NOT NULL)
```

---

>**En az bir çalışanı olan birimleri bulunuz.**
```SQL
SELECT * FROM departments
WHERE department_id IN (SELECT department_id FROM employees WHERE department_id IS NOT NULL)
```
```SQL
SELECT * FROM departments ust
WHERE  'X' IN (SELECT 'X'
               FROM employees
               WHERE department_id=ust.department_id
                 AND department_id IS NOT NULL)
```

## EXISTS

```SQL
SELECT * FROM departments ust
WHERE  EXISTS (SELECT *
               FROM employees
               WHERE department_id=ust.department_id)
```

---

>**Çalışanı olmayan birimleri bulunuz.**
```SQL
SELECT * FROM departments ust
WHERE  NOT EXISTS (SELECT *
               FROM employees
               WHERE department_id=ust.department_id)
```

---

>**Region (Europe) bölgesinde çalışan kişileri bulunuz.**
```SQL
SELECT * FROM employees
WHERE department_id IN
(SELECT department_id FROM departments
   WHERE location_id IN
  (SELECT location_id FROM locations
   WHERE country_id IN
     (SELECT country_id FROM countries
     WHERE region_id  IN
        (SELECT region_id FROM regions
        WHERE region_name='Europe'))))
```

---

>**En yüksek maaş alan kişinin çalıştığı ülkeyi bulunuz.**
```SQL
SELECT * FROM countries
WHERE country_id IN
(SELECT country_id  FROM locations
WHERE location_id  IN
 (SELECT location_id FROM departments
 WHERE department_id IN
 (SELECT department_id FROM employees
 WHERE salary= (SELECT MAX(salary) FROM employees))))
```

>**Ortalama maaşı 10000'den fazla olup,içinde 5000 doların altında maaş alan en az bir çalışanı olan birimleri bulunuz.**

**???**

---

>**Kartezyen Çarpım**
```SQL
SELECT * FROM departments, employees
WHERE departments.department_id=100
```

---

>**Bileşke (Oracle)**
```SQL
SELECT * FROM departments, employees
WHERE departments.department_id=employees.department_id
```
```SQL
SELECT * FROM departments d, employees e
WHERE d.department_id=e.department_id
```

---

>**ANSI**
```SQL
SELECT * FROM departments d
JOIN employees e
ON (d.department_id=e.department_id)
```

---

>**Bileşke + kısıt**
```SQL
SELECT * FROM departments d, employees e
WHERE d.department_id=e.department_id
AND salary>10000
```

---

>**ANSI**
```SQL
SELECT * FROM (SELECT * FROM departments d
JOIN employees e
ON (d.department_id=e.department_id))
WHERE salary>10000
```

---

>**INNER JOIN**
```SQL
SELECT * FROM departments d
INNER JOIN employees e
USING (department_id)
WHERE department_id=50
```

---

>**NATURAL JOIN**
```SQL
SELECT * FROM departments d
NATURAL JOIN employees e
WHERE department_id=50
```

**Non equi Join**
>**Kendi unvan maaş aralığından fazla maaş alan kişileri bununuz.**
```SQL
SELECT * FROM employees e
JOIN jobs j
ON (e.job_id=j.job_id AND salary NOT BETWEEN min_salary AND max_salary)
```

---

>**Kendi biriminin ortalamasından fazla maaş alan kişileri bulunuz.**
```SQL
(SELECT department_id, AVG(salary) maas
 FROM employees
 GROUP BY department_id) dept_avg
 ```
```SQL
SELECT * FROM employees e
INNER  JOIN (SELECT department_id, AVG(salary) maas
 FROM employees
 GROUP BY department_id) dept_avg
USING (department_id)
WHERE e.salary>dept_avg.maas
```

## Quiz Çözümleri

>**Hem kalem hem silgi alanları bulunuz**

```
FATURA_BILGI=(FATURA  ⨝ FATURA_DETAY)

(σ urun_id='Kalem' FATURA_BILGI) ∩ (σ urun_id='Silgi' FATURA_BILGI)
```

>**Aldığı toplam kalem sayısı 500'den fazla olan müşteri isimleri**
```
FATURA_BILGI=(FATURA  ⨝ FATURA_DETAY)

MUSTERI_KALEM = γ musteri_id; SUM(Adet)-> toplam_adet (σ urun_id='Kalem'(FATURA_BILGI))

π adi, soyadi (MUSTERI ⨝ musteri.musteri_id= musteri_kalem.musteri_id(σ toplam_adet>500 (MUSTERI_KALEM)))
```

>**2013 yılında kalem alamayan müşterileri SQL ile bulunuz.**
```SQL
SELECT DISTINCT adres_id FROM musteri
WHERE musteri_id NOT IN  
			(SELECT musteri_id FROM fatura
			WHERE fatura_no IN
				 (SELECT fatura_no
					FROM fatura_detay
					WHERE urun_id='Kalem')
			AND fatura_tarihi=2013)
```

## Örneklerin Devamı

>**Amerikada çalışan kişileri bulunuz.**
```SQL
SELECT * 
FROM employees
INNER JOIN departments USING (department_id)
INNER JOIN locations USING (location_id)
INNER JOIN countries USING (country_id)
INNER JOIN regions USING (region_id)
WHERE region_name='Americas'
```

```SQL
SELECT * 
FROM employees e
JOIN departments d ON (d.department_id=e.department_id)
JOIN locations l ON (l.location_id=d.location_id)
JOIN countries c ON (c.country_id=l.country_id)
JOIN regions r ON (r.region_id=c.region_id)
WHERE r.region_name='Americas'
```


>**Çalışanlarının hiç birisi ünvan değiştirmeyen birimleri bulunuz**
```SQL
SELECT * FROM departments
WHERE department_id NOT IN 
    (SELECT NVL(department_id,0) FROM employees
     WHERE job_id IN (SELECT job_id 
                     FROM job_history))
```

>**Amirinden çok maaş alan kişileri bulunuz.**
```SQL
SELECT *
FROM employees calisan
JOIN employees amir
  ON calisan.manager_id=amir.employee_id
   AND calisan.salary>=amir.salary
```

```SQL
SELECT *
FROM employees calisan,employees amir
WHERE calisan.manager_id=amir.employee_id
   AND calisan.salary>amir.salary
```

>**Aynı unvanı taşıyan aynı birimde çalışan, ancak maaşı birbirinde farklı olan kişileri bulunuz.**
```SQL
SELECT *
FROM employees kaynak
JOIN employees hedef
  ON (kaynak.department_id=hedef.department_id 
  AND kaynak.job_id=hedef.job_id
  AND kaynak.salary<>hedef.salary
  AND kaynak.employee_id<>hedef.employee_id)
``` 
   


>**Çalışan sayısı aynı ancak toplam ödenen maaşları farklı olan birimleri bulunuz.**
```SQL
SELECT * FROM 
    (SELECT NVL(department_id,-1) department_id, COUNT(*) calisan_sayi, SUM (salary) toplam_maas
    FROM employees
    GROUP BY department_id) kaynak,
    (SELECT NVL(department_id,-1) department_id, COUNT(*) calisan_sayi, SUM (salary) toplam_maas
    FROM employees
    GROUP BY department_id) hedef
WHERE kaynak.department_id>hedef.department_id
  AND kaynak.calisan_sayi=hedef.calisan_sayi
  AND kaynak.toplam_maas<>hedef.toplam_maas
ORDER BY kaynak.department_id
```
>**Alternatif çözüm**
```SQL
CREATE OR REPLACE VIEW DEPT_COUNT AS (SELECT NVL(department_id,-1) department_id, COUNT(*) calisan_sayi, SUM (salary) toplam_maas
                        FROM employees
                        GROUP BY department_id)
```
```SQL
SELECT * FROM  DEPT_COUNT kaynak, DEPT_COUNT hedef
WHERE kaynak.department_id>hedef.department_id
  AND kaynak.calisan_sayi=hedef.calisan_sayi
  AND kaynak.toplam_maas<>hedef.toplam_maas
ORDER BY kaynak.department_id
```

>**Hangi birimlerde ST_MAN'lere ödenen toplam maaş ST_CLERK'lere ödenen toplamdan azdır.**
```SQL
SELECT * 
FROM  (SELECT department_id, SUM(salary) toplam_maas
       FROM employees
       WHERE job_id = 'ST_MAN'
       GROUP BY department_id) dept_st_man
JOIN (SELECT department_id, SUM(salary) toplam_maas
      FROM employees
      WHERE job_id = 'ST_CLERK'
      GROUP BY department_id) dept_st_clerk
ON (dept_st_man.department_id=dept_st_clerk.department_id
   AND dept_st_man.toplam_maas<dept_st_clerk.toplam_maas)
```

>**Aynı unvanı taşıyan, ancak maaşı birbirinde farklı olan kişilerin sayısını unvanlara göre gruplayarak  bulunuz.**

>??

---

>**OUTER JOIN**
```SQL
SELECT * FROM employees e,departments d
WHERE e.department_id=d.department_id(+)
```
```SQL
SELECT e.department_id,department_name,COUNT(*) 
FROM employees e,departments d
WHERE e.department_id=d.department_id
GROUP BY e.department_id,department_name
```

>**Birim bilgisi olmayan veya eşleşmeyen kişi sayısını bulunuz.**
```SQL
SELECT COUNT(*) FROM employees e,departments d
WHERE e.department_id=d.department_id(+)
AND d.department_id IS NULL
```

>**Birimdeki kişi sayıları (kişi çalışmayan birimler dahil) getiriniz.**
```SQL
SELECT d.department_id,department_name,COUNT(e.department_id) 
FROM employees e,departments d
WHERE e.department_id(+)=d.department_id
GROUP BY d.department_id,department_name
```

>**Aşağıdaki sorguyu ANSI biçiminde yazınız.**
```SQL
SELECT * FROM employees e,departments d
WHERE e.department_id=d.department_id(+)
```
```SQL
SELECT * 
FROM employees e
LEFT OUTER JOIN departments d
ON (e.department_id=d.department_id)
```
```SQL
SELECT * 
FROM employees e
LEFT OUTER JOIN departments d
ON (e.department_id=d.department_id)
```
```SQL
SELECT * 
FROM employees e
FULL OUTER JOIN departments d
ON (e.department_id=d.department_id)
```
```SQL
SELECT * 
FROM employees e
RIGHT OUTER JOIN departments d
ON (e.department_id=d.department_id)
WHERE salary>5000
```
```SQL
SELECT * 
FROM employees e,departments d
WHERE salary>5000
AND e.department_id(+)=d.department_id
```


>**Kartezyen**
```SQL
SELECT * FROM employees,departments
```

>**Çalışanı olmayan birimleri bulunuz.**
```SQL
SELECT department_id FROM departments
MINUS
SELECT department_id birim_id FROM employees
```

```SQL
SELECT * FROM (SELECT department_id FROM departments
MINUS
SELECT department_id birim_id FROM employees)
WHERE department_id>100
```
```SQL
SELECT * FROM (SELECT department_id birim_id FROM departments
MINUS
SELECT department_id FROM employees)
WHERE department_id>100
```
```SQL
SELECT department_id FROM departments
MINUS
SELECT department_id birim_id FROM employees
ORDER BY department_id
```

```SQL
SELECT department_id FROM departments
UNION ALL
SELECT department_id birim_id FROM employees
```

## Bileşke küme operatörleri kullanarak.

>**Yönetici oldukları birimde çalışmayan kişileri bulunuz.**
```SQL
    SELECT * FROM employees
    WHERE employee_id IN 
    (SELECT manager_id FROM 
    (SELECT manager_id,department_id FROM departments
    MINUS
    SELECT employee_id,department_id FROM employees))
```    
    
>**Maaş ortalaması (binlik) aynı olan birimleri bulunuz.**
```SQL    
    SELECT * FROM (SELECT department_id, TRUNC(AVG(salary),-3) ortalama_bin
    FROM employees
    GROUP BY department_id) kaynak,
    (SELECT department_id, TRUNC(AVG(salary),-3) ortalama_bin
    FROM employees
    GROUP BY department_id) hedef
    WHERE kaynak.department_id>hedef.department_id
     AND kaynak.ortalama_bin=hedef.ortalama_bin
```

>**Hem AD_PRES, Hem de ST_Clerk çalışan  ancak SA_REP çalışmayan birimleri bulunuz**

```SQL
(SELECT department_id FROM employees
WHERE job_id = 'AD_PRES' 
INTERSECT 
SELECT department_id FROM employees
WHERE job_id = 'ST_CLERK') 
MINUS
SELECT department_id FROM employees
WHERE job_id = 'SA_REP'
```

>**Ülkelerdeki çalışan sayılarını hiç çalışan olayan ülkeleri de dahil ederek getiriniz.**

```SQL
select c.country_id,c.country_name,count(e.employee_id) 
from employees e,departments d,locations l,countries c
WHERE e.department_id(+)=d.department_id
AND d.location_id(+)=l.location_id
AND l.country_id(+)=c.country_id
GROUP BY c.country_id,c.country_name
```


>**Hiç bir çalışanı birimin ortalama maaşını almayan birimleri bulunuz.**

>**Aynı sayıda personelin işe girdiği yılları bulunuz (TO_CHAR(hire_date,'YYYY'))**

>**Çalışanı olan ve olmayan birim sayılarını tek SQL 'de bulunuz.**

>**?**

---

```SQL
SELECT * FROM birim
```

>**Ekleme Tek kayıt**
```SQL
INSERT INTO birim
(department_id,department_name,manager_id)
VALUES
(150,'IT',100)
```

>**Ekleme ?ok kayıt**
```SQL
INSERT INTO birim
(department_id,department_name,manager_id)
(SELECT department_id+500,department_name,manager_id FROM birim)
```

>**Ekleme ?ok kayıt - Tablo yapısı aynı**
```SQL
INSERT INTO birim
(SELECT department_id+1000,department_name,manager_id,location_id FROM birim)
```

>**Değişiklik**
```SQL
UPDATE birim
SET location_id=100,manager_id=101
WHERE location_id is NULL
```
```SQL
UPDATE birim
SET department_name='Da??t?m'
WHERE department_id =50
```
```SQL
UPDATE departments
SET department_id =500
WHERE department_id =90
```

>**Silme**
```SQL
DELETE FROM birim
WHERE department_id>270
```

>**En yüksek maaş alan kişinin maaşını 20000 dolar yapınız.**
```SQL
UPDATE calisan
SET salary=24000
WHERE salary=(SELECT max(salary) FROM calisan)
```

>**Ortalamanın üstünde maaş alan kişilerin maaşlarını ortalama mma? olarak değiştiriniz.**
```SQL
UPDATE calisan
SET salary=(SELECT AVG(salary) FROM calisan)
WHERE salary>(SELECT AVG(salary) FROM calisan)
```
```SQL
UPDATE (SELECT * FROM calisan
        WHERE department_id=50)
SET commission_pct=0.1
```

>**Hiç kimsenin çalışmadığı birimleri siliniz.**
```SQL
DELETE FROM birim 
WHERE department_id IN (SELECT department_id FROM birim
                        MINUS
                        SELECT department_id FROM calisan)
```
```SQL
UPDATE  (SELECT * FROM calisan
         INNER JOIN birim USING (department_id)
         INNER JOIN locations USING (location_id)
         WHERE city='Seattle')
SET salary=5000
```

>**Hatalı işlem**
```SQL
UPDATE  (SELECT * FROM calisan c
         JOIN birim b ON (c.department_id>b.department_id)
         INNER JOIN locations l USING (location_id)
         WHERE city='Seattle')
SET salary=5000
```

>**Kişilerin maaşlarını kendi birim ortalama maaşına eşitleyiniz.**
```SQL
UPDATE calisan ust
SET salary=(SELECT AVG(salary) 
            FROM calisan 
            WHERE department_id=ust.department_id)
```

>**Yanlış ??z?m**
```SQL
UPDATE calisan ust
SET salary=(SELECT department_id,AVG(salary) 
            FROM calisan )
```

>**Kendi biriminin ortalaması üstünde maaş alan kişileri kendi birim ortalama maaşını indiriniz.**

**??**

>
```SQL
CREATE TABLE MUSTERI
(MUSTERI_ID NUMBER,
 ADI VARCHAR2(30),
 SOYADI VARCHAR2(30))
 ```
  ```SQL
CREATE TABLE calisan1 AS (SELECT * FROM employees)
  ```
  ```SQL
 ALTER TABLE musteri ADD (musteri_temsilcisi DATE);
  ```
   ```SQL
 DROP TABLE  musteri ;
  ```
   ```SQL
 SELECT * FROM musteri
  ```
  ```SQL
CREATE TABLE MUSTERI
(MUSTERI_ID NUMBER,
 ADI VARCHAR2(30),
 SOYADI VARCHAR2(30))
  ```
  ```SQL
SELECT * FROM USER_TABLES
 ```

>**Bir script ile tüm tablolara giren_kullanıcı VARCHAR2(10) kolonu ekleyiniz.**
 ```SQL
ALTER TABLE musteri ADD (dogum_tarihi DATE);
SELECT 'ALTER TABLE '||table_name||' ADD (giren_kullanici VARCHAR2(10));' FROM USER_TABLES
 ```

 ```SQL
SELECT 'ALTER TABLE '||table_name||' DROP COLUMN giren_kullanici;' FROM USER_TABLES
 ```
  ```SQL
select * from user_tab_columns
where table_name='EMPLOYEES'
 ```
 ```SQL
TRUNCATE TABLE calisan
 ```
  ```SQL
ALTER TABLE musteri
ADD CONSTRAINT pk_musteri PRIMARY KEY (musteri_id)
 ```
 ```SQL
ALTER TABLE musteri
ADD CONSTRAINT pk_musteri PRIMARY KEY (musteri_id)
 ```
  ```SQL
SELECT * FROM user_constraints
 ```
 ```SQL
 ALTER TABLE musteri ADD (musteri_temsilcisi NUMBER(6) NOT NULL);
  ```
   ```SQL
ALTER TABLE musteri 
ADD CONSTRAINT musteri_unique UNIQUE (musteri_temsilcisi)
 ```
  ```SQL
ALTER TABLE musteri 
ADD CONSTRAINT fk_musteri_emp
FOREIGN KEY (musteri_temsilcisi)
REFERENCES employees (employee_id)
 ```

 ```SQL
UPDATE musteri
SET dogum_tarihi=TO_DATE('2/1/1899','DD/MM/YYYY')
 ```
  ```SQL
ALTER TABLE musteri
ADD CONSTRAINT ck_buyuk_1900
CHECK (dogum_tarihi > TO_DATE('1/1/1900','DD/MM/YYYY'))
 ```
 ```SQL
CREATE VIEW dept_emp_count AS
(SELECT d.department_id,department_name,COUNT(e.department_id) personel_adet
FROM employees e,departments d
WHERE e.department_id(+)=d.department_id
GROUP BY d.department_id,department_name)
 ```
 ```SQL
CREATE VIEW personel_view AS (SELECT employee_id,first_name,last_name,department_id FROM employees)
 ```
  ```SQL
SELECT * FROM personel_view
 ```

>**Yelkilendirme**
 ```SQL
CREATE user ogrenci1 identified by ogrenci1;
 ```

 ```SQL
GRANT CREATE SESSION TO OGRENCI1;

GRANT CREATE TABLE TO ogrenci1

alter user OGRENCI1 quota unlimited on system;
 ```

 ```SQL
SELECT * FROM hr.employees

GRANT all ON hr.employees TO OGRENCI1

REVOKE SELECT ON hr.employees FROM OGRENCI1

CREATE ROLE HR_ROLE 
 ```

 ```SQL
GRANT SELECT ON hr.employees TO hr_role;
GRANT SELECT ON hr.departments TO hr_role;
GRANT SELECT ON hr.jobs TO hr_role;
 ```

 ```SQL
GRANT HR_ROLE TO ogrenci1
 ```

>**Rolle ilgili yetkilendirme Oracle express 11g sürümünde tekrar bağlanınca aktifleşiyor.**

 ```SQL
CREATE SYNONYM employees FOR hr.employees
 ```


# *To Be Continued..*
