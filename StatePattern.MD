
## 🎮 Unity'de Oyun Mantığı ve Event-Driven Yapılar için Kullanılan Diğer Teknikler

### 1. **State Pattern (Durum Deseni)**

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

