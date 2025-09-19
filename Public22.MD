Harika bir yaklaşım! Unity’de **oyun mantığı** ve **event-driven programlama** üzerine odaklanmak, daha **modüler**, **okunabilir** ve **bakımı kolay** kodlar yazmanı sağlar. İlk mesajda verdiğim `Coroutine`, `Delegate`, `Event` ve `Action` konuları bu amaca hizmet eden temel tekniklerdi.

---

## ✅ 1. **Coroutine** – Zamanla işlem yapma

### 🎯 Amaç: Belirli aralıklarla işlem yapmak

```csharp
using UnityEngine;
using System.Collections;

public class CoroutineExample : MonoBehaviour
{
    void Start()
    {
        StartCoroutine(WaitAndPrint());
    }

    IEnumerator WaitAndPrint()
    {
        Debug.Log("Başladı");
        yield return new WaitForSeconds(2f);  // 2 saniye bekle
        Debug.Log("2 saniye sonra");
    }
}
```

---

## ✅ 2. **Delegate** – Fonksiyonları değişken gibi kullanma

### 🎯 Amaç: Fonksiyonları dinamik olarak atayıp çalıştırmak

```csharp
using UnityEngine;

public class DelegateExample : MonoBehaviour
{
    public delegate void MyDelegate();  // Delegate tanımı
    MyDelegate myDelegate;

    void Start()
    {
        myDelegate = Hello;
        myDelegate += Bye;

        myDelegate();  // İkisini de çalıştırır
    }

    void Hello() => Debug.Log("Merhaba!");
    void Bye() => Debug.Log("Görüşürüz!");
}
```

---

## ✅ 3. **Action / Action<T>** – Kısa ve güçlü event temsilcisi

### 🎯 Amaç: Delegate yerine daha sade bir şekilde fonksiyon tutmak

```csharp
using UnityEngine;
using System;

public class ActionExample : MonoBehaviour
{
    Action onJump;

    void Start()
    {
        onJump += () => Debug.Log("Zıplama gerçekleşti!");
    }

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            onJump?.Invoke();
        }
    }
}
```

> `Action<int>` ile parametre de alabilirsin:

```csharp
Action<int> onDamageTaken = (damage) => Debug.Log($"Hasar alındı: {damage}");
```

---

## ✅ 4. **Event** – Olay bildirimi yapma

### 🎯 Amaç: Bir olay gerçekleştiğinde diğer sınıflara haber vermek

### 📦 Event Yayıncı (Publisher):

```csharp
using UnityEngine;

public class Player : MonoBehaviour
{
    public delegate void OnDeathHandler();
    public static event OnDeathHandler OnDeath;

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.K))
        {
            Debug.Log("Oyuncu öldü.");
            OnDeath?.Invoke();  // Olayı tetikle
        }
    }
}
```

### 📦 Event Dinleyici (Subscriber):

```csharp
using UnityEngine;

public class UIManager : MonoBehaviour
{
    void OnEnable()
    {
        Player.OnDeath += ShowGameOver;
    }

    void OnDisable()
    {
        Player.OnDeath -= ShowGameOver;
    }

    void ShowGameOver()
    {
        Debug.Log("Game Over UI gösteriliyor.");
    }
}
```

---

## ✅ 5. **UnityEvent** – Inspector üzerinden event bağlamak

### Ne işe yarar?

UnityEvent, Unity Inspector’dan fonksiyon bağlamaya yarayan event sistemidir. `UnityEngine.Events` namespace’inde bulunur. Oyun tasarımcısı kod yazmadan event’leri kontrol edebilir.

### 🎯 Amaç: Kod yazmadan Inspector’dan fonksiyon atamak

```csharp
using UnityEngine;
using UnityEngine.Events;

public class UnityEventExample : MonoBehaviour
{
    public UnityEvent onButtonPressed;

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.E))
        {
            onButtonPressed?.Invoke();  // Inspector’dan bağlı fonksiyonlar çalışır
        }
    }
}
```

> Inspector'da `onButtonPressed` alanı görünür ve başka GameObject’lerin fonksiyonları buraya bağlanabilir.

---

## ✅ 6. **Kendi EventManager Sınıfını (Custom Event Sistemi) Yazmak** (Opsiyonel)

###  Ne işe yarar?

Basit oyunlar için kendi Event sistemini yazmak işine yarayabilir. Bu sistemde `Dictionary<string, Action>` gibi yapılarla olay isimleri üzerinden işlemler yapılır.

### 🎯 Amaç: Genel olay yönetimi yapmak

```csharp
using System;
using System.Collections.Generic;

public static class EventManager
{
    private static Dictionary<string, Action> events = new();

    public static void Subscribe(string eventName, Action listener)
    {
        if (!events.ContainsKey(eventName))
            events[eventName] = listener;
        else
            events[eventName] += listener;
    }

    public static void Unsubscribe(string eventName, Action listener)
    {
        if (events.ContainsKey(eventName))
            events[eventName] -= listener;
    }

    public static void Publish(string eventName)
    {
        if (events.ContainsKey(eventName))
            events[eventName]?.Invoke();
    }
}
```

### 📦 Kullanım:

```csharp
// Abone ol
EventManager.Subscribe("GameOver", OnGameOver);

// Event tetikle
EventManager.Publish("GameOver");
```

---

## 🔚 Özet (Tek Cümlelik Tanımlar)

| Teknik       | Ne İşe Yarar?                                      |
| ------------ | -------------------------------------------------- |
| Coroutine    | Zamanla işlemleri yürütmek                         |
| Delegate     | Fonksiyonları değişken gibi kullanmak              |
| Event        | Olay tetiklendiğinde ilgili fonksiyonları çağırmak |
| Action       | Delegate’in kısa ve modern versiyonu               |
| UnityEvent   | Inspector’dan fonksiyon bağlamayı sağlamak         |
| EventManager | Kendi genel olay sistemini oluşturmak              |

---

İstersen bu yapıları içeren **mini bir sistem örneği** (mesela: “oyuncu can kaybedince UI güncelle”) oluşturabiliriz. Uygulamalı öğrenmek istersen haber ver!
