
### Hakkımda
Hi 👋, I'm Unity blogger from Turkey

- **Engineering**: 
- **Project Setup & Settings**: 
- **User Interface**: 
- **Physics**: 
- **3rd Libraries**: 

---

<div align="center">
  <img height="450" src="assets/img/patterns.jpg"  />
</div>

---

# Correct collider/trigger settings in the Unity scene

## 1. The Most Important Unity Physics Rule

For `OnTriggerEnter` to work:

- ✅ One object must have a Rigidbody
- ✅ Both objects must have Collider
- ✅ In colliders with triggers, `Is Trigger` must be enabled

---

## Last Working Structure

### Player

* Rigidbody / uygun fizik bileşeni
  * `Use Gravity`: Kapalı
* Collider
* Tag = Player

### PlayerCollision

* Collider
* Is Trigger ✔
* PlayerCollision Script
* Tag = Obstacle

### EndTrigger

* Collider
* Is Trigger ✔
* EndTrigger Script
* Tag = Obstacle

### GameManager

* GameOverUI assigned
* CompleteLevelUI assigned

---

## Test

Press Play:

* Once the player enters the trigger area:

  * `CompleteLevelUI` should open
  * Type `build index` in the console
