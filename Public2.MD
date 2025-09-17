Elbette! Unity'de sıkça kullanılan **Coroutines**, **Delegates**, **Events** ve **Actions**; kod akışını daha esnek ve yönetilebilir hale getirmek için kullanılır. Bunları aşağıda hem açıklamaları hem de **basit örnekleriyle** anlatacağım.

---

## 🔄 1. Coroutine (Zamanla İşlem Yapmak)

### ✅ Ne işe yarar?

Coroutine, işlemleri **belirli aralıklarla** veya **zamanla** gerçekleştirmek için kullanılır. `IEnumerator` tipindedir ve `StartCoroutine()` ile çağrılır.

### 📦 Örnek:

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

## 🧭 2. Delegate (Temsilci Fonksiyon)

### ✅ Ne işe yarar?

Delegate, bir veya daha fazla metodu **bir değişken gibi** tutmamıza olanak tanır. Fonksiyonları parametre olarak göndermek gibi düşünebilirsin.

### 📦 Örnek:

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

## 📣 3. Event (Olay Sistemi)

### ✅ Ne işe yarar?

`event`, delegate’in daha **kontrollü bir versiyonudur**. Dışarıdan sadece `+=` ile abone olunabilir. Oyun içinde bir olay gerçekleştiğinde (örnek: oyuncu ölünce), diğer sistemleri haberdar etmek için kullanılır.

### 📦 Örnek:

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

## 🎯 4. Action (Hazır Delegate)

### ✅ Ne işe yarar?

`Action`, delegate’in daha basitleştirilmiş ve sistem tarafından sağlanan bir versiyonudur. Parametreli veya parametresiz kullanılabilir.

### 📦 Örnek:

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

---

Bu 4 temel yapı, Unity'de hem oyun mantığı hem de event-driven (olay tabanlı) programlamada sıkça kullanılır.

---

### İsteğin üzerine ikinci yanıtta başka önemli konulara (ScriptableObject, Observer Pattern, Singleton, vs.) da değineceğim. Devam edelim mi?
