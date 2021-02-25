# Yandex.project
import pandas as pd
from pymystem3 import Mystem
data = pd.read_csv('/datasets/data.csv')
data.info()
print(data.head(10))


# Создание функций для года, месяца, дня, часа и минуты
rng['year'] = rng['date'].dt.year 
rng['month'] = rng['date'].dt.month 
rng['day'] = rng['date'].dt.day 
rng['hour'] = rng['date'].dt.hour 
rng['minute'] = rng['date'].dt.minute 
