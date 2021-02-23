# Yandex.project
import pandas as pd
from pymystem3 import Mystem

# открытие файлов
data = pd.read_csv('/datasets/data.csv')
data.info()
print(data.head(10))

# обработка поисков
print('Количество пропусков в столбце days_employed:', data['days_employed'].isna().sum())
data.loc[data['days_employed'].isna(), 'days_employed'] = data['days_employed'].median()
print('Количество пропусков в столбце days_employed после заполнения:', data['days_employed'].isna().sum())

# находим все индивидуалные значения в столбце
data_dict1 = data['children'].value_counts()
print(data_dict1.head(10))

# удаление строк из столбца методом drop
data = data.drop(data[data['children'] == -1].index).reset_index(drop=True)
data = data.drop(data[data['children'] == 20].index).reset_index(drop=True)
data_dict12 = data['children'].value_counts()
print(data_dict12.head(10))

# замена типов данных методом astype()
#проводим замену данных в столбцах 'days_employed' и 'total_income' методом ".astype()""
data['days_employed'] = data['days_employed'].astype('int')
data['total_income'] = data['total_income'].astype('int')
#выводим общую информацию о датафрейме, чтобы убедиться в изменении типов данных
data.info()

# ОБРАБОТКА ДУБЛИКАТОВ

#рассчет количества дубликатов в data
print('Количество дубликатов найденные в data:', data.duplicated().sum())

#удаление дубликатов из таблицы
data = data.drop_duplicates().reset_index(drop=True)
print('Количество дубликатов в data после удаления:', data.duplicated().sum())

#поиск скрытых дубликатов с учетом регистра в столбце education
data_dict = data['education'].value_counts()
print(data_dict.head(10))

#изменение регистра в столбце 'education'
data['education'] = data['education'].str.lower()
print(data['education'].value_counts())

#рассчет количества дубликатов в data после проведения проверки на скрытые 
#дубликаты с учетом регистра в столбце education
print('Количество дубликатов в data:', data.duplicated().sum())

#удаление найденных дубликатов с учетом регистра из таблицы 
data = data.drop_duplicates().reset_index(drop=True)
print('Количество дубликатов в data после удаления:', data.duplicated().sum())

# ЛЕММАТИЗАЦИЯ 

m = Mystem()
def new_lemma(purpose):
    lemman = m.lemmatize(purpose)
    if ('недвижимость' in lemman) or ('жилье' in lemman):
        return 'недвижимость'
    elif ('автомобиль') in lemman:
        return 'автомобиль'
    elif ('свадьба') in lemman:
        return 'свадьба'
    elif ('образование') in lemman: 
        return 'образование'
data['lemma'] = data['purpose'].apply(new_lemma)
print(data['lemma'].value_counts())

# КАТЕГОРИЗАЦИЯ ДАННЫХ 
#в проекте я буду категоризировать данные из столбца "total_income"
#рассмотри два примера категоризации данных
def categor_income(total_count):
    if total_count <= 50000:
                return 'маленькая заработная плата'
    if total_count <= 100000:
                return 'средняя заработная плата'
    if 100000 <= total_count <= 500000:
                return 'высокая заработная плата'
    if total_count > 500000:
                return 'очень высокая заработная плата'
data['var_total_income'] = data['total_income'].apply(categor_income)
print(data['var_total_income'].value_counts())

# пример функции нахождения зависимостей между величинами
def addiction_data(koff_addiction):#функция использующая на вход столбец по которому будем искать зависимость
    #находим количество должников по каждой категории столбца "koff_addiction"
    data_group_ad = data.groupby([koff_addiction])['debt'].agg(['sum', 'count','mean'])
    #считаем общее количество клиентов обратившихся в банк
    sum_data_ad = data['debt'].count()
    #считаем процент по каждой категории столбца "koff_addiction"
    #data_group_sum = data_group_ad['sum'] / data_group_ad['count'] * 100
    data_group_sum = data_group_ad['mean'] * 100
    #возвращаем значение "data_group_sum"
    return data_group_sum

# Проверка столбов 'open_plan', 'is_apartment', 'studio' на информативность

print('Количество объектов с открытой планировкой')
print(data[('open_plan')].value_counts())

print()

print('Количество объектов - аппартаментов')
print(data[('is_apartment')].value_counts())

print()

print('Количество объектов - студий')
print(data[('studio')].value_counts())
Столбцы 'open_plan', 'is_apartment', 'studio' на мой взгляд не несут никакой полезной в этом проекте информации. В процентном соотношении количество аппартаментов и студий в выборге состовляют менее 1%, из чего можно сделать выбор, что данные параметры никак не могут повляить в целом на наше исследование. С количеством аппартаментов обстоит дело также, так как их количество мало относительно общей выборки. ЧТО-ТО ТУТ НУЖНО СКАЗАТЬ ПРО ЯНДЕКС НЕДВИЖИМОСТЬ И КОЛИЧЕСТВТО НА ДАННЫЙ МОМЕНТ АППАРАТМЕНТОВ
