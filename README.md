Bu alıştırmada, Haziran 2008’den Haziran 2018’e kadar olan ABD Petrol ve Gaz üretimini analiz edeceğiz. Dataset[available on Kaggle](https://www.kaggle.com/djzurawski/us-oil-and-gas-production-june-2008-to-june-2018)


1. Kaggle’dan `U.S._natural_gas_production.csv` dosyasını indir.
2. Challenge klasöründe Terminal üzerinden `mkdir` komutunu kullanarak `data` isminde bir klasör oluştur. 
3. CSV dosyanı bu `data` klasörünün içine koy.

Veri analizine başlarken `.py` dosyaları yazmayız; Notebook içinde kalırız.
VS Code’u **Data Engineering** kısmında, tek seferde çalışacak bir Python script hazırlarken kullanacağız.
Bu challenge için tamamen boş bir Notebook sağladık. Dataset’i keşfederken düzenli ve anlaşılır bir Notebook tutmanız için adımları birlikte takip edeceğiz. Talimatlar ile Notebook arasında gidip geleceksiniz. İyi eğlenceler!


Şimdi Jupyter Notebook’u başlatarak başlayalım:

```bash
jupyter notebook
```

 `oil_and_gas.ipynb` dosyasına tıklayarak notebook’u açın. Notlarınızı ve Python kodunuzu burada yazacaksınız.

## Dokümantasyona Başlama

Notebook’unuzda varsayılan olarak bir code cell olmalı, bu hücrenin türünü **Markdown** olarak değiştirin ve başlık oluşturmak için aşağıdakileri kopyala-yapıştır yapın: 

```markdown
# U.S. Oil and Gas Production Analysis

Analyzing the [Kaggle Dataset](https://www.kaggle.com/djzurawski/us-oil-and-gas-production-june-2008-to-june-2018) with information about Oil and Gas production in the US from June 2008 to June 2018.
```

 `⇧ + ↩` ile kaydedip edit modundan çıkın.

## Modülleri Yükleme

Sonraki code cell’de, veri keşfi sırasında ihtiyaç duyacağımız kütüphaneleri import etmek istiyoruz. Aşağıdakileri kopyalayıp yapıştır:

```python
import numpy as np
import pandas as pd
import matplotlib
```

`⇧ + ↩` ile kodu çalıştırın.. Kod, Jupyter Session boyunca hatırlanacaktır (_remembered_)(terminaldeki jupyter notebook` sürecini `Ctrl+C` ile durdurana veya kernel’i yeniden başlatana kadar).

## CSV’den Veri Yükleme

Bir sonraki hücrenin türünü Markdown olarak değiştir ve aşağıdakini içine yapıştır:

```markdown
---

Let's load the Gas production:
```

Unutmayın, Jupyter notebook yalnızca Python kodu yazmak için değildir; öyle olsaydı VS Code içinde kalırdık!


Altına yeni bir code cell ekleyin ve gas üretimini yüklemek için şu iki satırı yazın. Ardından `DataFrame` i  `.head()` function ile görüntüleyin: .

```python
file = "data/U.S._natural_gas_production.csv"
gas_df = pd.read_csv(file, decimal=",")
gas_df.head(3)
```

Type `⇧ + ↩` ile çalıştırın. DataFrame’in [**`head(n)`**](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.head.html) çıktısını göreceksiniz.

CSV yükledikten sonra DataFrame’in yapısı ve boyutu hakkında bilgi almak için genelde hemen şu cell’leri ekleriz:

```python
# DataFrame'de kaç satır ve kaç sütun olduğunu kontrol et
gas_df.shape
```

```python
# DataFrame'de mevcut olan tüm sütun adlarını göster
gas_df.columns
```

```python
# Her sütun hakkında veri türü ve null olmayan değer sayısı gibi ek bilgiler göster
gas_df.info()
```

Bu son komutla, Month sütununun object olarak yüklendiğini ve bir tarih olarak tanınmadığını görebiliriz! Pandas’a biraz yardımcı olup bu sütun üzerinde yükleme sonrası bir işlem yapmamız gerekiyor. Bunu bir sonraki bölümde yapalım:

## Month Sütununu datetime’a Dönüştürme

pd.to_datetime() dökümantasyonunu okuyun. [`pd.to_datetime()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.to_datetime.html). Yeni bir code cell ekleyerek Month column’ını gerçek bir datetime nesnesine dönüştürmeyi deneyin.

### Kodunu Test Et

Yeni bir **markdown**  hücresi ekle ve içine şunu yaz:

```markdown
### Check your code
```
Şimdi sütunumuzun başarıyla dönüştürülüp dönüştürülmediğine bakalım.

u challenge için make kullanmayacağız. Bunun yerine, test aracı nbresult’ı doğrudan Notebook içinde kullanacağız.

Yeni bir hücreye aşağıdakini kopyalayıp yapıştır:

```python
month_type = gas_df['Month'].dtype
```

ve nbresult aracıyla değişkenlerini inceleyecek başka bir hücreye şunu ekle:

```python
from nbresult import ChallengeResult

result = ChallengeResult('date',
    month_type=month_type,
)
result.write()
```
Şimdi kodunu şu komutla kontrol edebilirsin:

```python
print(result.check())
```

Eğer %100 aldıysan, değişiklikleri commit et ve bir sonraki bölüme devam et.

Bunu yaptıktan sonra Month sütunundaki değerleri datetime objeleri olarak ele alabilirsin. Notebook’una yeni bir hücre ekle ve Month sütunu ile yeni şekillerde etkileşime geçmek için aşağıdaki kodları çalıştır:

```python
gas_df['Month'].dt.year.head()
```

```python
gas_df['Month'].dt.month.tail()
```


## Satırları Gruplama

Yeni bir keşif bloğuna başladığımız için, bir Markdown hücresi ekleyip içine şunu yaz:
```markdown
---

## Yearly Gas production
```

Artık DataFrame’i hazırladığımıza göre, ilk iş odaklı sorumuzu cevaplamayı deneyebiliriz:

ABD’deki her eyaletin ve tüm ABD’nin toplam yıllık gaz üretimi ne kadardır?

Bu soruyu cevaplamak için, Month sütununun yılına göre satırları aggregate etmemiz gerekiyor. Yeni bir code cell ekle ve bu aggregation’ı yaz. Ortaya çıkan DataFrame’i yearly_gas_df isimli bir değişkene ata.


<details><summary markdown='span'>Hint
</summary>

DataFrame.groupby() ve DataFrameGroupBy.sum() dökümantasyonuna göz at. [`DataFrame.groupby()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.groupby.html) ve [`DataFrameGroupBy.sum()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.core.groupby.DataFrameGroupBy.sum.html).

</details>

<details><summary markdown='span'>⛓️‍💥 Getting an error <code>TypeError: datetime64 type does not support sum operations</code>?
</summary>

  Gruplamaya çalıştığımız DataFrame’de Month sütunu da var. Bu sütun bir tarih tipi ve tarihler toplanamaz, sadece sayısal sütunlar toplanabilir. sum() işlemi uygulanırken Pandas’a yalnızca sayısal sütunları kullanmasını söylemenin bir yolunu dökümantasyondan bulmaya çalış.

</details>

### Kodunu Test Et

Yeni bir **markdown** hücresi ekle:

```markdown

### Check your code
```
Şimdi DataFrame’in doğru şekilde gruplandığını doğrulayalım. Yeni bir code cell’e aşağıdaki kodu kopyalayıp yapıştır:

```python
index_year = yearly_gas_df.index[0]
yearly_gas_shape = yearly_gas_df.shape
us_total = yearly_gas_df.iloc[0,0]
```

ve değişkenlerini nbresult ile test etmek için başka bir hücreye şunları ekle:

```python
from nbresult import ChallengeResult

result = ChallengeResult('full_gas',
    index_year=index_year,
    yearly_gas_shape=yearly_gas_shape,
    us_total=us_total
)
result.write()

print(result.check())
```

Eğer %100 aldıysan, commit et ve bir sonraki bölüme geç.

## Sonuçları Plot Etme

Bu DataFrame’i başarılı şekilde aggregate ettiğimize göre, şimdi[`DataFrame.filter()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.filter.html) ve [`DataFrame.plot()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.plot.html) fonksiyonlarını kullanarak U.S. toplam üretimini plot etme zamanı.

<details><summary markdown='span'>View solution
</summary>

Plot konusuna ileride çok daha detaylı gireceğiz, ama şimdilik aggregate ettiğimiz DataFrame’in U.S. sütununu (toplam üretimi) hızlıca aşağıdaki satırla plot edebiliriz:

```python
plot = yearly_gas_df.filter(items=['U.S.']).plot(kind="bar")
plot.set_xlabel("Year");
```

</details>

## Boolean Indexing ile Satırları Eleme

Bir önceki bölümde, 2008 ve 2018 yıllarına ait üretim verisinin tam olmadığını gördük (sadece yılın yarısı mevcut). Yalnızca tam yıllarla çalışmak istediğimiz için, `yearly_gas_df` DataFrame’inin ilk ve son satırlarını yıl bilgisine dayanarak elemek istiyoruz.

Sadece tüm 12 ayın üretim verisine sahip yıllardan oluşan `filtered_yearly_gas_df` cadında yeni bir DataFrame oluştur.

<details><summary markdown='span'>Hint
</summary>

 DataFrame’in index’ine bakabilir ve[`np.logical_and`](https://docs.scipy.org/doc/numpy/reference/generated/numpy.logical_and.html) kullanarak bir [**boolean condition**](https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#boolean-indexing) oluşturabilirsin.

</details>


Yeni DataFrame’ini plot edebilir misin?

### Kodunu Test Et
Sonraki adıma geçmeden önce, bir önceki adımları doğru uyguladığını kontrol et.

Yeni bir Markdown hücresi ekle:

```markdown
### Check your code
```

Sonra yeni bir code cell’e şu kodu kopyala:

```python
from nbresult import ChallengeResult

result = ChallengeResult('filtered_gas',
    yearly_gas=filtered_yearly_gas_df.shape
)
result.write()

print(result.check())
```

Eğer %100 aldıysan, commit et ve bir sonraki bölüme geç.


## Birden Fazla Sütunu Plot Etme

Şimdi Notebook’ta yeni bir bölüm oluşturarak ABD’deki farklı eyaletlerin gaz üretimini analiz edelim. İlk olarak bir **Markdown** hücresi ekleyip şunu yaz:

```markdown
## State production

Let's have a look at the yearly production of some specific states
```

Ardından, ilk sütun dışındaki tüm sütunlara bakarak mevcut eyaletleri görelim. Bunun için DataFrame’in `columns` attribute’unu kullanarak sütun isimlerini temiz bir şekilde yazdırabiliriz: 

```python
filtered_yearly_gas_df.columns[1:].sort_values()
```

:question: Yeni bir cell ekleyip, seçtiğin dört eyaletin gaz üretiminin line chart’larını plot edecek kodu yaz. Başlangıç noktası olarak filtered_yearly_gas_df` DataFrame’ini kullanabilirsin.

<details><summary markdown='span'>View solution
</summary>
Colorado, Louisiana, Ohio ve Utah’ı seçmek isteseydik şöyle yapabilirdik:

```python
plot = filtered_yearly_gas_df.filter(items=['Colorado', 'Louisiana', 'Ohio', 'Utah']).plot()
plot.set_xlabel("Year");
```
</details>



## İkinci Bir CSV Yükleme

Kullandığımız Kaggle dataset’i sadece gaz üretimi hakkında bilgi içermiyor, aynı zamanda **crude oil** hakkında da bilgi içeriyor. Notebook’ta yeni bir bölüm oluşturmak için şu Markdown hücresini ekle:

```markdown
---

## Comparing with Crude Oil Production
```

data klasörü içindeki U.S._crude_oil_production.csv dosyasını yükle ve oil_df adında yeni bir DataFrame’e ata:

- [`pd.read_csv()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html) fonksiyonuyla yükle.
- `Month` sütununu bir `datetime` objesine dönüştür. 

<details><summary markdown='span'>Hint
</summary>

 [`pd.read_csv()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html) dökümantasyonunda, `Month` sütununu daha yükleme sırasında `datetime`a dönüştürmene yardımcı olabilecek bir parametre görüyor musun? 

</details>



Bu yeni veriyle benzer adımları takip edelim. Yeni bir Markdown hücresi ekleyip aşağıdakini yaz:

```markdown
---

## Yearly Oil production
```

Now, aggregate the rows based on the year of the `Month` column. Insert a new code cell and code this aggregation using `DataFrame.groupby()` and `DataFrame.sum()`, assigning the result to a new variable called `yearly_oil_df`.  Let's plot the yearly total U.S. Crude Oil production from our new dataframe.  Do you notice anything that might prevent us from doing this? Take a look at the your column names through the `yearly_oil_df.columns` attribute.

<details><summary markdown='span'>Hint
</summary>

 [`str.strip`](https://pandas.pydata.org/docs/reference/api/pandas.Series.str.strip.html#pandas.Series.str.strip) dökümantasyonuna bir göz at.

</details>

Sütun adları sorununu çözdükten sonra, `U.S. Crude Oil` sütununu plot etmek için yeni bir hücre ekle: 

```python
yearly_oil_df.filter(items=['U.S. Crude Oil']).plot(kind='bar');
```
Aynı şekilde, `yearly_oil_df` DataFrame’ini de sadece 12 tam ay verisi olan yılları içerecek şekilde filtreleyelim. Bu yeni DataFrame’i `filtered_yearly_oil_df` değişkenine ata.  

Kodunu Test Et

Yeni bir **Markdown** hücresi ekle:

```markdown
### Check your code
```

ve ardından değişkenlerini inceleyip test etmek için şu kodu ekle:

```python
from nbresult import ChallengeResult

result = ChallengeResult('oil',
    filtered_oil_shape=filtered_yearly_oil_df.shape,
    filtered_oil_index_year=filtered_yearly_oil_df.index[0],
    us_total=filtered_yearly_oil_df.iloc[0,0]
)
result.write()

print(result.check())
```

Eğer %100 aldıysan, commit et ve bir sonraki bölüme geç.

## Dataset’leri Birleştirme

Şimdi, hem Oil hem de Gas üretimini yıl bazında içeren, ABD toplamlarını barındıran yeni bir DataFrame oluşturalım. Notebook’unda yeni bir bölüm açmak için yeni bir Markdown hücresi ekle:

```markdown
## Merging Oil and Gas Production
```
Sonra, `filtered_yearly_oil_df` ve `filtered_yearly_gas_df` DataFrame’lerinden yalnızca toplam ABD üretimini içeren sütunları filtreleyerek `total_gas` ve `total_oil` adında iki DataFrame oluştur. Bu yeni DataFrame’lerdeki sütun adlarını Gas ve Crude Oil olarak yeniden adlandır. [Rename the columns](https://stackoverflow.com/questions/11346283/renaming-columns-in-pandas).

Bu iki DataFrame’i oluşturduktan sonra, ikisini birleştirerek tek bir DataFrame oluştur ve bunu `merged_df` değişkenine ata. Bunun için pd.concat() [`pd.concat()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.concat.html) fonksiyonunu kullanmalı ve axis parametresini doğru ayarlamayı unutmamalısın!


Son olarak, merged_df DataFrame’ini plot et ve orijinal Kaggle dataset’inde belirtilen birimlere uygun olacak şekilde legend’i ayarlamaya çalış.[Kaggle dataset](https://www.kaggle.com/djzurawski/us-oil-and-gas-production-june-2008-to-june-2018).

<details><summary markdown='span'>View solution
</summary>

```python
plot = merged_df.plot(kind="bar")
plot.set_xlabel("Year")
plot.legend(['Gas (Millions of Cubic feet)', 'Crude Oil (Thousands of barrels)']);
```

</details>

### Kodunu Test Et

Yeni bir **markdown** hücresi ekle:

```markdown
### Check your code
```

Değişkenlerini test etmek için şu kodu çalıştır:

```python
from nbresult import ChallengeResult

result = ChallengeResult('merged_dataframes',
    merged_df_shape=merged_df.shape,
    yearly_oil_2009=merged_df.iloc[0]["Crude Oil"],
)
result.write()

print(result.check())
```

Hepsi bu kadar; bir Jupyter Notebook üzerinden ilk Data Analysis çalışmanı tamamladığın için tebrikler! :clap:
