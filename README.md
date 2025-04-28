# pr_06
# Практическая работа 6. Аналитика с использованием сложных типов данных. Поиск и анализ продаж
# Цель
На основе данных переписи населения США проанализировать корреляцию между уровнем использования общественного транспорта по ZIP-кодам и объемом продаж организации в соответствующих регионах.
# Задачи
1.	Загрузите набор данных public_transportation_statistics_by_zip_code.csv.
2.	Скопируйте данные public_transportation_statistics_by_zip_code.csv в базу данных клиентов организации, создав для них таблицу в наборе данных.
3.	Найдите максимальное и минимальное процентное соотношение в данных. Значения ниже 0 принять как пустое значение. 
4.	Рассчитайте средний объем продаж для клиентов, проживающих в регионах с высоким уровнем использования общественного транспорта (более 10%), а также с низким уровнем использования общественного транспорта (менее или равным 10%). 
5.	Загрузить данные в pandas и построить гистограмму распределения (использовать my_data.plot.hist(y='public_transportation_pct') для построения гистограммы).
6.	Сгруппируйте клиентов по их zip_code, и посмотрите на среднее количество транзакций на одного клиента. Экспортируйте эти данные в Excel и создайте диаграмму рассеяния, чтобы лучше понять взаимосвязь между использованием общественного транспорта и продажами.
7.	На основании этого анализа, какие рекомендации вы бы дали руководству компании при рассмотрении возможностей расширения?
Выполнение практической работы:

## Выполнение практической работы
# Подготовка к выполнению заданий
Изначально необхожимо загрузить библиотеку psycopg2 при помощи кода
````
%pip install psycopg2
````
````
import psycopg2
from psycopg2 import Error
````
Для эффективной работы приложение необходимо каждый раз открывать и закрывать соединения, это можно сделать используя следующий шаблон, вместо ваш_логин и тд, необходимо вставить свои данные.
````
import psycopg2

def get_connection(database_name):
    # Функция для получения подключения к базе данных
    connection = psycopg2.connect(user="ваш_логин",
                                  password="ваш_пароль",
                                  host="ваш_хост",
                                  port="5432",
                                  database="имя_бд")
    return connection

def close_connection(connection):
    # Функция для закрытия подключения к базе данных
    if connection:
        connection.close()
        print("Соединение с PostgreSQL закрыто")

try:
    # Подключение к базе данных
    connection = get_connection("mybd")
    cursor = connection.cursor()

  
    '''
    cursor.execute(create_table_query)
    connection.commit()
    print("Таблица 'public_transportation_statistics_by_zip_code' успешно создана")

except (Exception, psycopg2.Error) as error:
    print("Ошибка при подключении или работе с PostgreSQL:", error)

finally:
    # Закрытие подключения к базе данных
    if connection:
        close_connection(connection)
````
# Задание 1.	Загрузите набор данных
Загружаем данные public_transportation_statistics_by_zip_code.csv с GitHub: https://github.com/BosenkoTM/SQL-for-Begginer-DataAnalytics/blob/main/Datasets/public_transportation_statistics_by_zip_code.csv2. 
# Задание 2. Скопируйте данные public_transportation_statistics_by_zip_code.csv в базу данных клиентов организации, создав для них таблицу в наборе данных.
Создадим таблицу при помощи этого кода
````
  # Создание таблицы public_transportation_statistics_by_zip_code
    create_table_query = '''
    CREATE TABLE public_transportation_statistics_by_zip_code(
        zip_code character varying(10) NOT NULL PRIMARY KEY,
        public_transportation_pct numeric(15,2) NOT NULL,
        public_transportation_population integer
    );
    '''
````
# Результат выполнения
![image](https://github.com/user-attachments/assets/8b103b2c-cd48-41b1-a00a-a26406e328aa)

![image](https://github.com/user-attachments/assets/962ab22d-0e59-4cd3-8d8b-cd050861471b)

Затем необходимо заполнить эту таблицу данными, сделаем это
````
# Путь к CSV-файлу
    csv_file_path = r"C:\Users\damdi\Desktop\уник\Практикум sql\ПР6\public_transportation_statistics_by_zip_code.csv"

    # Проверка существования файла
    if not os.path.exists(csv_file_path):
        print(f"ОШИБКА: Файл '{csv_file_path}' не найден.")
        raise False # Возвращаем False при неудаче
    
    # Вставка данных из CSV-файла 
    with open(csv_file_path, 'r') as file:
        copy_query = """
        COPY public_transportation_statistics_by_zip_code(zip_code, public_transportation_pct, public_transportation_population)
        FROM STDIN WITH (FORMAT csv, HEADER true, DELIMITER ',');
        """
        cursor.copy_expert(copy_query, file)
        connection.commit()
        print("Данные успешно вставлены в таблицу 'public_transportation_statistics_by_zip_code'")

except (Exception, psycopg2.Error) as error:
    print("Ошибка при подключении или работе с PostgreSQL:", error)

````

# Результат выполнения
![image](https://github.com/user-attachments/assets/dca483cc-88ab-4432-9fb6-6b1e2b858d3b)

# Задание 3. Найдите максимальное и минимальное процентное соотношение в данных. Значения ниже 0 принять как пустое значение.
Для выполнения этого задания сформулируем следующий запрос:
````
query = """
    SELECT 
        MAX(public_transportation_pct) AS max_pct,
        MIN(public_transportation_pct) AS min_pct
    FROM public_transportation_statistics_by_zip_code
    WHERE public_transportation_pct >= 0;
    """
    cursor.execute(query)
    result = cursor.fetchone()
    max_pct, min_pct = result
    print(f"Максимальное процентное соотношение: {max_pct}")
    print(f"Минимальное процентное соотношение: {min_pct}")
````
# Результат выполнения
![image](https://github.com/user-attachments/assets/011170c4-d965-44fa-b65f-529729e61694)

# Задание 4. Рассчитайте средний объем продаж для клиентов, проживающих в регионах с высоким уровнем использования общественного транспорта (более 10%), а также с низким уровнем использования общественного транспорта (менее или равным 10%).
Для выполнения этого задания необходимо объединить таблицы public_transportation_statistics_by_zip_code и sales, сделаем это при помощи JOIN
````
    query = """
    SELECT 
    CASE 
        WHEN pts.public_transportation_pct > 10 THEN 'Высокий уровень'
        ELSE 'Низкий уровень'
    END AS "Уровень использования транспорта",
    AVG(s.sales_amount) AS "Средний объем продаж"
FROM 
    sales s
JOIN 
    customers c ON s.customer_id = c.customer_id
JOIN 
    public_transportation_statistics_by_zip_code pts ON c.postal_code = pts.zip_code
GROUP BY 
    CASE 
        WHEN pts.public_transportation_pct > 10 THEN 'Высокий уровень'
        ELSE 'Низкий уровень'
    END;
    """
    cursor.execute(query)
    results = cursor.fetchall()

# Вывод результатов
    print("Уровень использования транспорта, средний объем продаж")
    for row in results:
        category, avg_sales = row
        print(f"{category}, {avg_sales}")
````
# Результат выполнения 
![image](https://github.com/user-attachments/assets/88267082-c98e-4539-b1b6-8144f840231a)

# Задание 5. Загрузить данные в pandas и построить гистограмму распределения (использовать my_data.plot.hist(y='public_transportation_pct') для построения гистограммы).
Гистограмма группирует данные по диапазонам значений (например, процентов) и показывает, сколько раз значения попадают в эти диапазоны (частоту). Соотвественно, создадим гистограмму с критериями процент  и частота использования

````
    # SQL-запрос для получения данных
    query = "SELECT * FROM public_transportation_statistics_by_zip_code WHERE public_transportation_pct >= 0;"
    
    # Загрузка в DataFrame
    my_data = pandas.read_sql(query, connection)

    # Построение гистограммы распределения
    my_data.plot.hist(y='public_transportation_pct')
    plt.title('Распределение использования общественного транспорта')
    plt.xlabel('Процент использования общественного транспорта')
    plt.ylabel('Частота использования')
    plt.show()
````
# Результат выполнения 
![image](https://github.com/user-attachments/assets/7d0a2b0e-9052-434d-b7db-555c6de2626b)

# Задание 6. Сгруппируйте клиентов по их zip_code, и посмотрите на среднее количество транзакций на одного клиента. Экспортируйте эти данные в Excel и создайте диаграмму рассеяния, чтобы лучше понять взаимосвязь между использованием общественного транспорта и продажами.
Чтобы выполнить это задание, сначала необходимо соединить таблицы custumers и public_transportation_statistics_by_zip_code с помощью JOIN, далее действуем, как указано в задании
````
    query = """
    SELECT 
        pts.zip_code,
        COUNT(DISTINCT c.customer_id) AS customer_count,
        COUNT(*) / COUNT(DISTINCT c.customer_id) AS avg_transactions,
        AVG(s.sales_amount) AS avg_sales_amount,
        pts.public_transportation_pct
    FROM 
        customers c
    JOIN 
        sales s ON c.customer_id = s.customer_id
    JOIN 
        public_transportation_statistics_by_zip_code pts ON c.postal_code = pts.zip_code
    WHERE public_transportation_pct >= 0
    GROUP BY 
        pts.zip_code, pts.public_transportation_pct;
    """
    
    # Загрузка  в DataFrame
    data = pandas.read_sql(query, connection)

    # Экспорт данных в CSV
    csv_file = "analysis_sales.csv"
    data.to_csv(csv_file, index=False)
    print(f"Данные успешно экспортированы в файл: {csv_file}")

    # Построение диаграммы рассеяния
    plt.scatter(data['public_transportation_pct'], data['avg_sales_amount'])
    plt.title('Взаимосвязь между использованием общественного транспорта и продажами')
    plt.xlabel('Процент использования общественного транспорта')
    plt.ylabel('Средний объем продаж')
    plt.show()
````
# Результат выполнения 
![image](https://github.com/user-attachments/assets/e32822d2-df64-42ec-88c4-02d87864957a)

# Задание 7.	На основании этого анализа, какие рекомендации вы бы дали руководству компании при рассмотрении возможностей расширения?
Анализ диаграммы рассеивания  демонстрирует, что корреляция обратнопропорциональная. То есть, чем выше процент пользования общественного транспорта, тем ниже средний объем продаж. 
На основе этого анализа, я бы дала следующие рекомендации: выбирать локации, где доля пользования общественным транспортом минимальна. Это позволит увеличить потенциал продаж в данных районах; создать специальные программы лояльности для жителей районов с низким использованием общественного транспорта, чтобы мотивировать их к покупкам через удобные им каналы.

# Выводы
Анализ данных переписи населения США позволил выявить обратную корреляцию между уровнем использования общественного транспорта и объемом продаж организации. На основе этого можно сделать вывод, что стратегия развития бизнеса должна учитывать особенности каждого региона. В районах с низким уровнем пользования общественным транспортом приоритет следует отдавать физическим точкам продаж, тогда как в районах с высоким уровнем — развивать онлайн-каналы и логистические решения.

## Структура репозитория:
- `analysis_sales.csv` — Экспортированный файл из 6 задания.
- `Damdinova_Kristina_Takhirovna` — Jupyter Notebook с выполнением всех заданий.
