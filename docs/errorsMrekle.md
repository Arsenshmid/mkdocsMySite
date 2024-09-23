```python
import pprint
import json
import hashlib

def sha3_myFunc(data):
    return hashlib.sha3_256(data.encode()).hexdigest()

with open('tx.json','r') as file:
    data = json.load(file)

sortedData = sorted(data, key=lambda x: x['block'])

if len(sortedData)%2!=0:
    sortedData.append(sortedData[-1])

# Пока не остался один корневой хэш
while len(sortedData) > 1:
    # Создаем новый список для хранения хэшей следующего уровня
    new_hashes = []
    
    # Проходим по списку парами (по два элемента за раз)
    for i in range(0, len(sortedData), 2):
        
        # Проверяем, есть ли пара для текущего элемента
        if i + 1 < len(sortedData):
            # Если есть, объединяем хэши двух элементов и создаем новый хэш
            combined = sha3_myFunc(sortedData[i]['hash'] + sortedData[i + 1]['hash'])
        else:
            # Если пары нет (нечетное количество), дублируем последний элемент
            combined = sha3_myFunc(sortedData[i]['hash'] + sortedData[i]['hash'])
        
        # Добавляем новый хэш в список нового уровня
        new_hashes.append({'hash': combined})
    
    # Обновляем список хэшей для следующего уровня
    sortedData = new_hashes

# Выводим финальный корневой хэш дерева Меркла
print(sortedData[0]['hash'])

```