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

# *To Be Continued..*
