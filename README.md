
### 1. **Sahnede Empty Object Zorunludur**
- **Kullanım**: Değişken kullanılır
* Sahneye yeni bir GameObject **eklemen gerekmez.**
* `SplashController` sahne geçişleri için **yalnızca bir araçtır.**
* `LevelComplete` tetiklendiğinde direkt sahne geçişi yapılır.
- **Yazım Örneği**:
  ```php    
    $result = $mysqli->query("SELECT * FROM pages");
    
    $mysqli->close();
  ```

- **Kullanım**: Değişken kullanılmaz ise (sahnede mevcut olduğu için) FindObjectOfType<> metodu kullanılabilir.
  ```php

  ```

- **Kullanım**: Statik sınıflar üzerinden doğrudan script metodu script adına ön ek olarak çağırılır.
  ```php
  
  ```

- **Kullanım**: Singleton ile sahnede destroy öncesinde kullanılır.
  ```php
  
  ```

### Hakkımda
Hi 👋, I'm Unity blogger from Turkey

- **Project Setup & Settings**: 
- **User Interface**: 
- **Physics**: 

<div align="center">
  <img height="150" src="assets/img/patterns.jpg"  />
</div>





### Özet (Tek Cümlelik Tanımlar):

| Koşul           | Açıklama                               | Kullanımı             |
| --------------- | -------------------------------------- | --------------------- |
| Coroutine       | Zamanla işlemleri yürütmek             |                       |
| Invoke / InvokeRepeating | Coroutine alternatifleri      |                       |
| Delegate        | Fonksiyonları değişken gibi kullanmak  |                       |
| Action          | Delegate’in kısa ve modern versiyonu   |                       |
| Func<T>         | Değer döndüren delegate                |                       |
| Event           | Olay tetiklendiğinde ilgili fonksiyonları çağırmak |           |
| Lambda ile Event Dinleme | Anlık/inline event tanımlama  |                       |
| Enum ile Event Sistemi | Event'leri gruplandırmak        |                       |
| UnityEvent      | Inspector’dan fonksiyon bağlamayı sağlamak |                   |
| EventManager    | Kendi genel olay sistemini oluşturmak  |                       |
| Input System Events | Girişleri event olarak işleme      |                       |





### Özet

Unity'de sıkça kullanılan **Coroutines**, **Delegates**, **Events** ve **Actions**; kod akışını daha esnek ve yönetilebilir hale getirmek için kullanılır. 

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

## ✅ 3. **Delegate (Temsilci Fonksiyon)** – Fonksiyonları değişken gibi kullanma

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

## ✅ 4. **Action / Action<T>** – Kısa ve güçlü event temsilcisi

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

## ✅ 5. **Func<T>** – Fonksiyondan değer döndürmek

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

## ✅ 6. **Event** – Olay bildirimi yapma

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

### 📦 Event Dağıtıcı (Deployer): Inline Lambda Expression ile fonksiyon yazmak

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

### 📦 Event Durumu (State): Karakterin ya da bir objenin birden fazla davranış durumu varsa (`Idle`, `Run`, `Attack`, `Die`) bu durumları ayrı sınıflara bölüp, eventlerle birbirine bağlamanı sağlar.

```csharp
public interface IState
{
    void Enter();
    void Execute();
    void Exit();
}
```

> Game loop içinde `currentState.Execute()` çağrılır. `Event` ile state değişimleri tetiklenir.

### 📦 Event Ayırıcı (Collector): Farklı türde event’leri tek sistemde yönetmek için kullanılır.

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

---

## ✅ 7. **UnityEvent** – Inspector üzerinden event bağlamak

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

## ✅ 8. **Kendi EventManager Sınıfını (Custom Event Sistemi) Yazmak** (Opsiyonel)

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

## ✅ 9. **Input Event Sistemi ile Bağlantılı Kullanım**

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




