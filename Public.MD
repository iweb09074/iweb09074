Evet, Unity’de oyun mantığı ve event-driven programlamayı destekleyen **başka teknikler** de vardır. İlk mesajlarda bahsettiğimiz Coroutine, Delegate, Event, Action, UnityEvent gibi temel teknikler dışında da **farklı örneklerle uygulanabilecek** bazı teknikler şunlardır:

---

## 🔧 1. **Func<T>** – Fonksiyondan değer döndürmek

### 🎯 Ne işe yarar?

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

## 🔄 2. **Invoke / InvokeRepeating** – Zamanla fonksiyon çağırma (Alternatif Coroutine)

### 🎯 Ne işe yarar?

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

## 🧱 3. **Events ile Enum Kullanımı** – Olayları türlere göre ayırmak

### 🎯 Ne işe yarar?

Farklı türde event’leri tek sistemde yönetmek için kullanılır.

### 📦 Örnek:

```csharp
public enum GameEventType
{
    OnStart,
    OnGameOver,
    OnLevelComplete
}

public static class EnumEventManager
{
    private static Dictionary<GameEventType, Action> events = new();

    public static void Subscribe(GameEventType type, Action listener)
    {
        if (!events.ContainsKey(type))
            events[type] = listener;
        else
            events[type] += listener;
    }

    public static void Publish(GameEventType type)
    {
        if (events.ContainsKey(type))
            events[type]?.Invoke();
    }
}
```

> Bu sayede tek event sistemiyle farklı türde olaylar yönetilebilir.

---

## 🧪 4. **Lambda Expression ile Olay Dinlemek** – Inline fonksiyon yazmak

### 🎯 Ne işe yarar?

Event’lere kısa fonksiyonlarla anında abone olmayı sağlar.

### 📦 Örnek:

```csharp
using UnityEngine;
using System;

public class LambdaExample : MonoBehaviour
{
    event Action onFire;

    void Start()
    {
        onFire += () => Debug.Log("Ateş edildi!");
    }

    void Update()
    {
        if (Input.GetMouseButtonDown(0))
        {
            onFire?.Invoke();
        }
    }
}
```

---

## 📦 5. **Input Event Sistemi ile Bağlantılı Kullanım**

Unity’nin **Yeni Input System** kullanılıyorsa, event-driven kontrol için `performed`, `started`, `canceled` gibi olaylar dinlenebilir.

### 🎯 Ne işe yarar?

Kullanıcı girdilerini olay tabanlı olarak işler.

### 📦 Örnek:

```csharp
using UnityEngine;
using UnityEngine.InputSystem;

public class InputExample : MonoBehaviour
{
    public PlayerInput playerInput;

    void OnEnable()
    {
        playerInput.actions["Jump"].performed += ctx => Jump();
    }

    void Jump()
    {
        Debug.Log("Zıplama tuşuna basıldı.");
    }
}
```

---

## 🔚 SONUÇ – Ekstra Teknikler Tablosu

| Teknik / Yapı                | Açıklama                      |
| ---------------------------- | ----------------------------- |
| **Func<T>**                  | Değer döndüren delegate       |
| **Invoke / InvokeRepeating** | Coroutine alternatifleri      |
| **Enum ile Event Sistemi**   | Event'leri gruplandırmak      |
| **Lambda ile Event Dinleme** | Anlık/inline event tanımlama  |
| **Yeni Input System Events** | Girişleri event olarak işleme |

---

İstersen bu tekniklerin birkaçıyla oluşturulmuş **küçük senaryo** da hazırlayabilirim (örneğin: "Fareye tıklanınca skor artar, skor değeri başka bir sınıfa iletilir" gibi).

Devam etmek ister misin?
