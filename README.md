
### Hakkımda
Hi 👋, I'm Unity blogger from Turkey

- **Project Setup & Settings**: 
- **User Interface**: 
- **Physics**: 

<div align="center">
  <img height="150" src="assets/img/patterns.jpg"  />
</div>



---

## 🚫 `Instantiate` ve `Destroy` Yöntemi

Sık sık `Instantiate` ve `Destroy` kullanıldığında **performans düşer** çünkü her defasında heap’te yeni nesne açılır ve Garbage Collector çalışmak zorunda kalır.

### Örnek: Puzzle parçalarını sürekli üretip yok etmek

```csharp
public class BadSpawner : MonoBehaviour
{
    public GameObject prefab;

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            // Her bastığında yeni obje oluştur
            GameObject obj = Instantiate(prefab, Random.insideUnitSphere * 5, Quaternion.identity);

            // 2 saniye sonra yok et
            Destroy(obj, 2f);
        }
    }
}
```

👉 Bu durumda:

* Space’e basıldıkça **hep yeni prefab oluşturulur**.
* Destroy edilen objeler **çöpe atılır** → Garbage Collector daha sık devreye girer.
* Mobil cihazlarda **frame drop (takılma)** görülür.

---

## ✅ Object Pooling ile (İyi Yöntem)

Senin örneğin gibi, bir defa üretilen objeler tekrar kullanılır:

---

## 📂 Object Pooling Yapısı (Örnek)

**Amaç:** Sık sık `Instantiate` / `Destroy` yerine havuz kullanarak **GC (Garbage Collector) yükünü azaltmak**.

```csharp
public class ObjectPool : MonoBehaviour
{
    public GameObject prefab;
    public int poolSize = 10;
    private Queue<GameObject> pool = new Queue<GameObject>();

    void Start()
    {
        for (int i = 0; i < poolSize; i++)
        {
            GameObject obj = Instantiate(prefab);
            obj.SetActive(false);
            pool.Enqueue(obj);
        }
    }

    public GameObject GetFromPool(Vector3 pos)
    {
        if (pool.Count > 0)
        {
            GameObject obj = pool.Dequeue();
            obj.transform.position = pos;
            obj.SetActive(true);
            return obj;
        }
        return null;
    }

    public void ReturnToPool(GameObject obj)
    {
        obj.SetActive(false);
        pool.Enqueue(obj);
    }
}
```

---

🔹 **Örnek:** Puzzle parçaları, mermiler, düşman spawn sistemi.

```csharp
public class GoodSpawner : MonoBehaviour
{
    public ObjectPool pool;

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            // Havuzdan hazır obje al
            GameObject obj = pool.GetFromPool(Random.insideUnitSphere * 5);

            // 2 saniye sonra geri gönder
            StartCoroutine(ReturnAfterDelay(obj, 2f));
        }
    }

    IEnumerator ReturnAfterDelay(GameObject obj, float delay)
    {
        yield return new WaitForSeconds(delay);
        pool.ReturnToPool(obj);
    }
}
```

👉 Burada **Instantiate/Destroy yerine SetActive(true/false)** yapıldığı için:

* Çöp oluşmaz, GC uğraşmaz.
* Performans mobil cihazlarda **çok daha akıcı** olur.

👉 `Instantiate` yerine:

```csharp
var piece = pool.GetFromPool(new Vector3(0,0,0));
pool.ReturnToPool(piece);
```

---

| Taktik            | Açıklama                                                                              |
| ----------------- | ------------------------------------------------------------------------------------- |
| `Object Pooling`  | Puzzle parçaları yeniden kullanılacaksa Instantiate/Destroy yerine Object Pool kullan |

---

## ✅ 1. **Func<T>** – Fonksiyondan değer döndürmek

### Ne işe yarar?

`Action` sadece işlem yaparken, `Func<T>` değer döndürür. Delegate’in dönüş değerli halidir.

### 📦 Örnek:

```csharp
using UnityEngine;
using System;

public class FuncExample : MonoBehaviour
{
    Func<int, int, int> addNumbers;

    void Start()
    {
        addNumbers = (a, b) => a + b;
        int result = addNumbers(3, 5);
        Debug.Log($"Toplam: {result}");  // Toplam: 8
    }
}
```

---

## ✅ 2. **Invoke / InvokeRepeating** – Zamanla fonksiyon çağırma (Alternatif Coroutine)

### Ne işe yarar?

Bir fonksiyonu belirli süre sonra ya da tekrar eden şekilde çağırmak için kullanılır.

### 📦 Örnek:

```csharp
using UnityEngine;

public class InvokeExample : MonoBehaviour
{
    void Start()
    {
        Invoke("PrintOnce", 2f);           // 2 saniye sonra bir kez
        InvokeRepeating("PrintRepeated", 1f, 3f); // 1 saniye sonra başla, 3 saniyede bir tekrar et
    }

    void PrintOnce()
    {
        Debug.Log("Tek seferlik çağrıldı.");
    }

    void PrintRepeated()
    {
        Debug.Log("Tekrar eden çağrı.");
    }
}
```

> ⚠️ `Invoke` yerine Coroutine tercih edilir çünkü daha esnektir.

---

## ✅ 3. **Coroutine** – Zamanla işlem yapma

### Ne işe yarar?

Coroutine, işlemleri **belirli aralıklarla** veya **zamanla** gerçekleştirmek için kullanılır. `IEnumerator` tipindedir ve `StartCoroutine()` ile çağrılır.

### 🎯 Amaç: Belirli aralıklarla işlem yapmak

```csharp
using UnityEngine;
using System.Collections;

public class CoroutineExample : MonoBehaviour
{
    void Start()
    {
        StartCoroutine(PrintMessages());
    }

    IEnumerator PrintMessages()
    {
        Debug.Log("Mesaj 1");
        yield return new WaitForSeconds(2f);  // 2 saniye bekler
        Debug.Log("Mesaj 2");
        yield return new WaitForSeconds(1f);
        Debug.Log("Mesaj 3");
    }
}
```

---

