Harika bir yaklaşım! Unity’de **oyun mantığı** ve **event-driven programlama** üzerine odaklanmak, daha **modüler**, **okunabilir** ve **bakımı kolay** kodlar yazmanı sağlar. İlk mesajda verdiğim `Coroutine`, `Delegate`, `Event` ve `Action` konuları bu amaca hizmet eden temel tekniklerdi.

Şimdi bu yaklaşımda devam ederek Unity’de **olay tabanlı ve oyun mantığını kolaylaştıran başka hangi teknikler** kullanılır, ona bakalım:

---

## 🎮 Unity'de Oyun Mantığı ve Event-Driven Yapılar için Kullanılan Diğer Teknikler

### 1. **UnityEvent** (Inspector'dan Event bağlamak)

#### ✅ Ne işe yarar?

UnityEvent, Unity Inspector’dan fonksiyon bağlamaya yarayan event sistemidir. `UnityEngine.Events` namespace’inde bulunur. Oyun tasarımcısı kod yazmadan event’leri kontrol edebilir.

#### 📦 Örnek:

```csharp
using UnityEngine;
using UnityEngine.Events;

public class UnityEventExample : MonoBehaviour
{
    public UnityEvent onButtonPressed;

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.B))
        {
            onButtonPressed?.Invoke();
        }
    }
}
```

> Inspector'da `onButtonPressed` alanı görünür ve başka GameObject’lerin fonksiyonları buraya bağlanabilir.

---

### 2. **State Pattern (Durum Deseni)**

#### ✅ Ne işe yarar?

Karakterin ya da bir objenin birden fazla davranış durumu varsa (`Idle`, `Run`, `Attack`, `Die`) bu durumları ayrı sınıflara bölüp, eventlerle birbirine bağlamanı sağlar.

#### 📦 Kullanım:

```csharp
public interface IState
{
    void Enter();
    void Execute();
    void Exit();
}
```

> Game loop içinde `currentState.Execute()` çağrılır. `Event` ile state değişimleri tetiklenir.

---

### 3. **EventManager Sınıfı (Custom Event Sistemi)**

#### ✅ Ne işe yarar?

Basit oyunlar için kendi Event sistemini yazmak işine yarayabilir. Bu sistemde `Dictionary<string, Action>` gibi yapılarla olay isimleri üzerinden işlemler yapılır.

#### 📦 Mini Örnek:

```csharp
public static class EventManager
{
    private static Dictionary<string, Action> events = new Dictionary<string, Action>();

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

#### 📦 Kullanımı:

```csharp
EventManager.Subscribe("GameOver", OnGameOver);
EventManager.Publish("GameOver");
```

---

## Özetle

| Teknik                 | Ne İçin Kullanılır?                | Event-Driven Uygunluğu |
| ---------------------- | ---------------------------------- | ---------------------- |
| Coroutine              | Zamanla işlem yapmak               | ❌                      |
| Delegate               | Fonksiyonu değişken gibi taşımak   | ✅                      |
| Event                  | Olay bildirimi yapmak              | ✅✅                     |
| Action                 | Delegate’in kısa yolu              | ✅                      |
| UnityEvent             | Inspector üzerinden event yönetimi | ✅✅                     |
| EventManager           | Kendi Event sistemini kurmak       | ✅✅                     |

---

Eğer senin için uygun olursa, bu bilgilerle küçük bir **modüler oyun yapısı** örneği üzerinden bir senaryo (örnek: "oyuncu can kaybedince UI'ya bilgi gitmesi") oluşturabiliriz.
İster misin böyle bir örnek?
