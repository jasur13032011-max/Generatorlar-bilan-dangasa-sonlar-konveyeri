# Generatorlar-bilan-dangasa-sonlar-konveyeri
Python tilida **dangasa (lazy) evaluation** — bu qiymatlarni oldindan to'liq hisoblab, xotirada saqlash o'rniga, faqat ularga ehtiyoj tug'ilganda (so'ralganda) birma-bir ishlab chiqarish yondashuvidir. Bu katta hajmdagi yoki cheksiz ma'lumotlar bilan ishlashda xotirani (RAM) tejashning eng samarali usuli hisoblanadi.

Quyida generatorlar va iterator protokoli yordamida yaratilgan to'liq konveyer kodi va uning ishlash mantig'i keltirilgan:

```python
import time

# --- 1. GENERATOR FUNKSIYA ---
def numbers(n):
    """Sonlarni dangasa tarzda yield qiluvchi generator funksiya."""
    print(f"[INFO] Generator ishga tushdi! (n={n})")
    for i in range(1, n + 1):
        yield i

# --- 2. MAXSUS ITERATOR KLASSI (Ekvivalent) ---
class NumberIterator:
    """Xuddi shu xatti-harakatni iterator protokoli orqali bajaruvchi klass."""
    def __init__(self, n):
        self.n = n
        self.current = 0

    def __iter__(self):
        # __iter__ o'zini qaytarishi shart
        return self

    def __next__(self):
        self.current += 1
        if self.current > self.n:
            raise StopIteration
        return self.current


if __name__ == "__main__":
    
    print("=== 1-QISM: Generatorning dangasaligi va next() ni namoyish qilish ===")
    gen = numbers(3)
    print("Generator yaratildi, lekin yuqoridagi [INFO] xabari hali chiqmadi.")
    print("Chunki generator funksiya chaqirilganda darhol ishga tushmaydi, u faqat iteratsiya boshlangandagina ishlaydi.\n")
    
    print("Birinchi elementni olish uchun next() chaqiramiz:")
    print("Natija:", next(gen))
    print("Ikkinchi element:")
    print("Natija:", next(gen))
    print("-" * 50)


    print("=== 2-QISM: Generator ifodasi (Generator Expression) va sum() ===")
    # Oraliq ro'yxat [1, 4, 9, 16, 25] xotirada umuman yaratilmaydi!
    # Qiymatlar bittadan oqib kelib, to'g'ridan-to'g'ri yig'indiga qo'shiladi.
    squares_sum = sum(x**2 for x in numbers(5))
    print(f"1 dan 5 gacha bo'lgan sonlar kvadratlari yig'indisi: {squares_sum}")
    print("-" * 50)


    print("=== 3-QISM: For siklida bir xil iste'mol qilinishi ===")
    print("--- Generator bilan for sikli ---")
    for num in numbers(3):
        print(f"Iste'mol qilindi: {num}")

    print("\n--- Maxsus iterator klassi bilan for sikli ---")
    custom_iter = NumberIterator(3)
    for num in custom_iter:
        print(f"Iste'mol qilindi: {num}")
    print("-" * 50)


    print("=== 4-QISM: Generatorning bir martaligi (Exhaustion) ===")
    g = numbers(3)
    print("Birinchi marta ro'yxatga o'giramiz:", list(g))
    print("Ikkinchi marta xuddi shu generatorni o'qishga harakat qilamiz:", list(g))
    print("Izoh: Generatorlar bir martalik (stateful) bo'ladi. Barcha elementlar "
          "ishlatib bo'lingach, u bo'shab qoladi va uni qayta boshidan o'qib bo'lmaydi.")

```

### Muhim tushunchalar va xulosalar:

1. **Dangasa baholash (Lazy Evaluation):** `numbers(n)` funksiyasi ichida `yield` ishlatilgani bois, u chaqirilganda butun ro'yxatni xotirada shakllantirmaydi. Har safar keyingi qiymat so'ralgandagina bitta elementni hosil qiladi.
2. **`__iter__` va `__next__` protokoli:** Python'dagi `for` sikli va boshqa funksiyalar obyekt bilan ishlashda shu protokoldan foydalanadi. Generatorlar avtomatik ravishda bu interfeysni ta'minlaydi, maxsus `NumberIterator` klassida эsa buni qo'lda yozib chiqdik.
3. **Xotirani tejash:** `sum(x**2 for x in numbers(1000000))` kabi ifodalarda millionlab elementli ro'yxat xotirada joy band qilmaydi, xotira sarfi doimiy ($O(1)$) darajada saqlanib qoladi.
