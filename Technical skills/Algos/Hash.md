```python
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        seen = {}
        for num in nums:
            if num in seen and seen[num] >= 1:
                return True
            seen[num] = seen.get(num, 0) + 1
        return False
```

Со словарем в Python можно выполнять различные операции для управления его содержимым. Вот основные действия, которые можно выполнять со словарем:

1. **Создание словаря:**

• d = {} — создание пустого словаря.

• d = {'key': 'value', 'name': 'John'} — создание словаря с начальными значениями.

2. **Добавление и обновление элементов:**

• d['key'] = 'value' — добавление нового элемента или обновление значения по существующему ключу.

1. **Получение значения по ключу:**

• d['key'] — извлечение значения по ключу. Если ключ отсутствует, будет вызвана ошибка KeyError.

• d.get('key', 'default_value') — безопасное получение значения с указанием значения по умолчанию, если ключ не существует.

2. **Проверка наличия ключа:**

• 'key' in d — возвращает True, если ключ существует в словаре, иначе — False.

3. **Удаление элементов:**

• del d['key'] — удаляет элемент по ключу. Если ключ не существует, вызовет ошибку KeyError.

• d.pop('key') — удаляет элемент и возвращает его значение. Если ключ отсутствует, можно указать значение по умолчанию.

• d.popitem() — удаляет и возвращает последнюю пару ключ-значение (по порядку вставки).

4. **Получение всех ключей, значений и пар:**

• d.keys() — возвращает все ключи в словаре.

• d.values() — возвращает все значения в словаре.

• d.items() — возвращает все пары ключ-значение.

5. **Изменение значений:**

• d['key'] = new_value — обновление значения для существующего ключа.

6. **Очистка словаря:**

• d.clear() — удаляет все элементы из словаря.

7. **Копирование словаря:**

• d.copy() — создает поверхностную копию словаря.

8. **Объединение словарей:**

  

• d.update(other_dict) — обновляет словарь d элементами из other_dict. Если ключи совпадают, значения в d заменяются на значения из other_dict.

  

9. **Получение количества элементов:**

  

• len(d) — возвращает количество пар ключ-значение в словаре.

  

Эти операции позволяют эффективно работать с данными, хранящимися в словарях.








https://leetcode.com/problems/contains-duplicate


1. [Two Sum](https://leetcode.com/problems/two-sum/solutions/3619262/3-method-s-c-java-python-beginner-friendly/)
2. [Roman to Integer](https://leetcode.com/problems/roman-to-integer/solutions/3651672/best-method-c-java-python-beginner-friendly/)
3. [Palindrome Number](https://leetcode.com/problems/palindrome-number/solutions/3651712/2-method-s-c-java-python-beginner-friendly/)
4. [Maximum Subarray](https://leetcode.com/problems/maximum-subarray/solutions/3666304/beats-100-c-java-python-beginner-friendly/)
5. [Remove Element](https://leetcode.com/problems/remove-element/solutions/3670940/best-100-c-java-python-beginner-friendly/)
6. [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/solutions/3672475/4-method-s-c-java-python-beginner-friendly/)
7. [Add Two Numbers](https://leetcode.com/problems/add-two-numbers/solutions/3675747/beats-100-c-java-python-beginner-friendly/)
8. [Majority Element](https://leetcode.com/problems/majority-element/solutions/3676530/3-methods-beats-100-c-java-python-beginner-friendly/)
9. [Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/solutions/3676877/best-method-100-c-java-python-beginner-friendly/)