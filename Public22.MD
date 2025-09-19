Elbette! Unity'de sıkça kullanılan **Coroutines**, **Delegates**, **Events** ve **Actions**; kod akışını daha esnek ve yönetilebilir hale getirmek için kullanılır. Bunları aşağıda hem açıklamaları hem de **basit örnekleriyle** anlatacağım.

---

## ✅ 1. **Coroutine** – Zamanla işlem yapma

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

## ✅ 2. **Delegate (Temsilci Fonksiyon)** – Fonksiyonları değişken gibi kullanma

### Ne işe yarar?

Delegate, bir veya daha fazla metodu **bir değişken gibi** tutmamıza olanak tanır. Fonksiyonları parametre olarak göndermek gibi düşünebilirsin.

### 🎯 Amaç: Fonksiyonları dinamik olarak atayıp çalıştırmak

```csharp
using UnityEngine;

public class DelegateExample : MonoBehaviour
{
    public delegate void MyDelegate();  // Delegate tanımı
    MyDelegate myDelegate;

    void Start()
    {
        myDelegate = SayHello;
        myDelegate += SayBye;

        myDelegate();  // İki fonksiyonu da çağırır
    }

    void SayHello()
    {
        Debug.Log("Merhaba!");
    }

    void SayBye()
    {
        Debug.Log("Güle güle!");
    }
}
```

---

## ✅ 3. **Action / Action<T>** – Kısa ve güçlü event temsilcisi

### Ne işe yarar?

`Action`, delegate’in daha basitleştirilmiş ve sistem tarafından sağlanan bir versiyonudur. Parametreli veya parametresiz kullanılabilir.

### 🎯 Amaç: Delegate yerine daha sade bir şekilde fonksiyon tutmak

```csharp
using UnityEngine;
using System;

public class ActionExample : MonoBehaviour
{
    Action onJump;

    void Start()
    {
        onJump += JumpMessage;
    }

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            onJump?.Invoke();
        }
    }

    void JumpMessage()
    {
        Debug.Log("Zıplama gerçekleşti!");
    }
}
```

> `Action<int>` ile parametre de alabilirsin:

```csharp
Action<int> onDamageTaken = (damage) => Debug.Log($"Hasar alındı: {damage}");
```

---

## ✅ 4. **Event** – Olay bildirimi yapma

### Ne işe yarar?

`event`, delegate’in daha **kontrollü bir versiyonudur**. Dışarıdan sadece `+=` ile abone olunabilir. Oyun içinde bir olay gerçekleştiğinde (örnek: oyuncu ölünce), diğer sistemleri haberdar etmek için kullanılır.

### 🎯 Amaç: Bir olay gerçekleştiğinde diğer sınıflara haber vermek

### 📦 Event Yayıncı (Publisher):

```csharp
using UnityEngine;

public class EventPublisher : MonoBehaviour
{
    public delegate void DeathEvent();
    public static event DeathEvent OnPlayerDeath;

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.K))  // K tuşuna basınca
        {
            Debug.Log("Oyuncu öldü.");
            OnPlayerDeath?.Invoke();  // Olayı tetikle
        }
    }
}
```

### 📦 Event Dinleyici (Subscriber):

```csharp
using UnityEngine;

public class EventSubscriber : MonoBehaviour
{
    void OnEnable()
    {
        EventPublisher.OnPlayerDeath += HandlePlayerDeath;
    }

    void OnDisable()
    {
        EventPublisher.OnPlayerDeath -= HandlePlayerDeath;
    }

    void HandlePlayerDeath()
    {
        Debug.Log("EventSubscriber: Oyuncu öldü haberi alındı.");
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
