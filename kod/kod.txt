import tkinter as tk
import sqlite3
from datetime import datetime
from tkinter import Tk, Toplevel, filedialog, Label, Button, Entry, Listbox, messagebox, Scrollbar, END


# Создание базы данных и таблицы
conn = sqlite3.connect('database.db')
c = conn.cursor()
c.execute('''CREATE TABLE IF NOT EXISTS realty (id INTEGER PRIMARY KEY, type CHARACTER VARYING, city CHARACTER VARYING, street CHARACTER VARYING, home INTEGER, flat INTEGER, space INTEGER, rooms INTEGER, time TEXT, price INTEGER, owner INTEGER)''')
c.execute('''CREATE TABLE IF NOT EXISTS employee (id INTEGER PRIMARY KEY, surname CHARACTER VARYING, name CHARACTER VARYING, middlename CHARACTER VARYING, telephone BIGINT)''')
c.execute('''CREATE TABLE IF NOT EXISTS client (id INTEGER PRIMARY KEY, surname CHARACTER VARYING, name CHARACTER VARYING, middlename CHARACTER VARYING, passport BIGINT, telephone BIGINT)''')
c.execute('''CREATE TABLE IF NOT EXISTS owner (id INTEGER PRIMARY KEY, surname CHARACTER VARYING, name CHARACTER VARYING, middlename CHARACTER VARYING, passport BIGINT, telephone BIGINT)''')
c.execute('''CREATE TABLE IF NOT EXISTS sale (id INTEGER PRIMARY KEY, id_employee INTEGER, id_client INTEGER, id_owner INTEGER, id_realty INTEGER, data DATE, cena INTEGER)''')
c.execute('''CREATE TABLE IF NOT EXISTS renta (id INTEGER PRIMARY KEY, id_employee INTEGER, id_client INTEGER, id_owner INTEGER, id_realty INTEGER, data DATE, cena INTEGER, period CHARACTER VARYING)''')
conn.commit()

# Создание главного окна
root = tk.Tk()
root.title("Главное окно")
root.geometry("800x600")

# раздел о недвижимости
# Функция для открытия окна добавления данных
def open_add_window():
    add_window = tk.Toplevel(root)
    add_window.title("Добавить объект недвижимости")
    add_window.geometry("800x600")



    # Добавление элементов интерфейса для ввода данных
    type_label = tk.Label(add_window, text="тип сделки:")
    type_label.pack()
    type_entry = tk.Entry(add_window)
    type_entry.pack()

    city_label = tk.Label(add_window, text="город:")
    city_label.pack()
    city_entry = tk.Entry(add_window)
    city_entry.pack()

    street_label = tk.Label(add_window, text="улица:")
    street_label.pack()
    street_entry = tk.Entry(add_window)
    street_entry.pack()

    home_label = tk.Label(add_window, text="номер дома:")
    home_label.pack()
    home_entry = tk.Entry(add_window)
    home_entry.pack()

    flat_label = tk.Label(add_window, text="номер квартиры:")
    flat_label.pack()
    flat_entry = tk.Entry(add_window)
    flat_entry.pack()

    space_label = tk.Label(add_window, text="площадь кв.м:")
    space_label.pack()
    space_entry = tk.Entry(add_window)
    space_entry.pack()

    rooms_label = tk.Label(add_window, text="кол-во комнат:")
    rooms_label.pack()
    rooms_entry = tk.Entry(add_window)
    rooms_entry.pack()

    time_label = tk.Label(add_window, text="срок сдачи:")
    time_label.pack()
    time_entry = tk.Entry(add_window)
    time_entry.pack()

    price_label = tk.Label(add_window, text="Цена:")
    price_label.pack()
    price_entry = tk.Entry(add_window)
    price_entry.pack()

    owner_label = tk.Label(add_window, text="код сотрудника:")
    owner_label.pack()
    owner_entry = tk.Entry(add_window)
    owner_entry.pack()

    # Функция для добавления данных в базу данных
    def add_realty():
        type = type_entry.get()
        city = city_entry.get()
        street = street_entry.get()
        home = home_entry.get()
        flat = flat_entry.get()
        space = space_entry.get()
        rooms = rooms_entry.get()
        time = time_entry.get()
        price = price_entry.get()
        owner= owner_entry.get()

        try:
            home = int(home_entry.get())
            # continue with adding the realty
        except ValueError:
            messagebox.showerror("Error", "Ошибка, некорректный ввод данных в поле номер дома")
        try:
            flat = int(flat_entry.get())
            # continue with adding the realty
        except ValueError:
            messagebox.showerror("Error", "Ошибка, некорректный ввод данных в поле номер квартиры")
        try:
            rooms = int(rooms_entry.get())
            # continue with adding the realty
        except ValueError:
            messagebox.showerror("Error", "Ошибка, некорректный ввод данных в поле кол-во комнат ")

        # Check if all fields are filled
        if type == "" or city == "" or street == "" or home == "" or flat == "" or space == "" or rooms == "" or price == "" or owner == "":
            # Display error message if any field is empty
            messagebox.showerror("Error", "Ошибка, не все нобходимые поля заполнены")
        else:
            # Add the realty details to the database
            # Code to add the realty details to the database
            pass

        c.execute("INSERT INTO realty (type, city, street, home, flat, space, rooms, time, price, owner) VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?)",
            (type, city, street, home, flat, space, rooms, time, price, owner))
        conn.commit()
    # Кнопка для добавления данных
    add_button = tk.Button(add_window, text="Добавить", command=add_realty)
    add_button.pack()

# Функция для открытия окна просмотра данных
def open_view_window():
    view_window = tk.Toplevel(root)
    view_window.title("Просмотр объектов недвижимости")
    view_window.geometry("800x600")

    # Получение данных из базы данных
    c.execute("SELECT * FROM realty")
    data = c.fetchall()

    scrollbar = Scrollbar(view_window)
    scrollbar.pack(side="right", fill="y")

    realty_text = Listbox(view_window, yscrollcommand=scrollbar.set)
    realty_text.pack(fill="both", expand=True)

    # Отображение данных в текстовом виджете
    #data_text = tk.Text(view_window)
    for row in data:
        realty_text.insert(tk.END, f"ID: {row[0]}")
        realty_text.insert(tk.END, f"тип сделки: {row[1]}")
        realty_text.insert(tk.END, f"город: {row[2]}")
        realty_text.insert(tk.END, f"улица: {row[3]}")
        realty_text.insert(tk.END, f"номер дома: {row[4]}")
        realty_text.insert(tk.END, f"номер квартиры: {row[5]}")
        realty_text.insert(tk.END, f"площадь кв.м: {row[6]}")
        realty_text.insert(tk.END, f"кол-во комнат: {row[7]}")
        realty_text.insert(tk.END, f"срок сдачи: {row[8]}")
        realty_text.insert(tk.END, f"цена: {row[9]}")
        realty_text.insert(tk.END, f"код владельца: {row[10]}")
        realty_text.pack()

    scrollbar.config(command=realty_text.yview)




#раздел о сотруднике
# Функция для открытия окна добавления данных
def open_work_window():
    work_window = tk.Toplevel(root)
    work_window.title("Добавить сотрудника")
    work_window.geometry("800x600")

    # Добавление элементов интерфейса для ввода данных
    surname_label = tk.Label(work_window, text="Фамилия:")
    surname_label.pack()
    surname_entry = tk.Entry(work_window)
    surname_entry.pack()

    name_label = tk.Label(work_window, text="Имя:")
    name_label.pack()
    name_entry = tk.Entry(work_window)
    name_entry.pack()

    middlename_label = tk.Label(work_window, text="Отчество:")
    middlename_label.pack()
    middlename_entry = tk.Entry(work_window)
    middlename_entry.pack()

    telephone_label = tk.Label(work_window, text="Контакты:")
    telephone_label.pack()
    telephone_entry = tk.Entry(work_window)
    telephone_entry.pack()

    # Функция для добавления данных в базу данных
    def add_employee():
        surname = surname_entry.get()
        name = name_entry.get()
        middlename = middlename_entry.get()
        telephone = telephone_entry.get()

        c.execute("INSERT INTO employee (surname, name, middlename, telephone) VALUES (?, ?, ?, ?)",
            (surname, name, middlename, telephone))
        conn.commit()
    # Кнопка для добавления данных
    add_button = tk.Button(work_window, text="Добавить", command=add_employee)
    add_button.pack()

# Функция для открытия окна просмотра данных
def open_worker_window():
    worker_window = tk.Toplevel(root)
    worker_window.title("Просмотр сотрудников")
    worker_window.geometry("800x600")

    # Получение данных из базы данных
    c.execute("SELECT * FROM employee")
    employee = c.fetchall()

    scrollbar = Scrollbar(worker_window)
    scrollbar.pack(side="right", fill="y")

    employee_text = Listbox(worker_window, yscrollcommand=scrollbar.set)
    employee_text.pack(fill="both", expand=True)

    # Отображение данных в текстовом виджете
    #data_text = tk.Text(view_window)
    for row in employee:
        employee_text.insert(tk.END, f"ID: {row[0]}")
        employee_text.insert(tk.END, f"Фамилия: {row[1]}")
        employee_text.insert(tk.END, f"Имя: {row[2]}")
        employee_text.insert(tk.END, f"Отчество: {row[3]}")
        employee_text.insert(tk.END, f"Контакты: {row[4]}")
        employee_text.pack()

    scrollbar.config(command=employee_text.yview)





#раздел о покупателе
# Функция для открытия окна добавления данных
def open_buy_window():
    buy_window = tk.Toplevel(root)
    buy_window.title("Добавить покупателя/арендатора")
    buy_window.geometry("800x600")

    # Добавление элементов интерфейса для ввода данных
    surname_label = tk.Label(buy_window, text="Фамилия:")
    surname_label.pack()
    surname_entry = tk.Entry(buy_window)
    surname_entry.pack()

    name_label = tk.Label(buy_window, text="Имя:")
    name_label.pack()
    name_entry = tk.Entry(buy_window)
    name_entry.pack()

    middlename_label = tk.Label(buy_window, text="Отчество:")
    middlename_label.pack()
    middlename_entry = tk.Entry(buy_window)
    middlename_entry.pack()

    passport_label = tk.Label(buy_window, text="Паспортные данные:")
    passport_label.pack()
    passport_entry = tk.Entry(buy_window)
    passport_entry.pack()

    telephone_label = tk.Label(buy_window, text="Контакты:")
    telephone_label.pack()
    telephone_entry = tk.Entry(buy_window)
    telephone_entry.pack()

    # Функция для добавления данных в базу данных
    def add_client():
        surname = surname_entry.get()
        name = name_entry.get()
        middlename = middlename_entry.get()
        passport= passport_entry.get()
        telephone = telephone_entry.get()

        c.execute("INSERT INTO client (surname, name, middlename, passport, telephone) VALUES (?, ?, ?, ?, ?)",
            (surname, name, middlename, passport, telephone))
        conn.commit()
    # Кнопка для добавления данных
    add_button = tk.Button(buy_window, text="Добавить", command=add_client)
    add_button.pack()

# Функция для открытия окна просмотра данных
def open_buyer_window():
    buyer_window = tk.Toplevel(root)
    buyer_window.title("Просмотр покупателей/арендаторов")
    buyer_window.geometry("800x600")

    # Получение данных из базы данных
    c.execute("SELECT * FROM client")
    client = c.fetchall()

    scrollbar = Scrollbar(buyer_window)
    scrollbar.pack(side="right", fill="y")

    client_text = Listbox(buyer_window, yscrollcommand=scrollbar.set)
    client_text.pack(fill="both", expand=True)

    # Отображение данных в текстовом виджете
    #data_text = tk.Text(view_window)
    for row in client:
        client_text.insert(tk.END, f"ID: {row[0]}")
        client_text.insert(tk.END, f"Фамилия: {row[1]}")
        client_text.insert(tk.END, f"Имя: {row[2]}")
        client_text.insert(tk.END, f"Отчество: {row[3]}")
        client_text.insert(tk.END, f"Паспортные данные: {row[4]}")
        client_text.insert(tk.END, f"Контакты: {row[5]}")
        client_text.pack()

    scrollbar.config(command=client_text.yview)




#раздел о владельце
# Функция для открытия окна добавления данных
def open_sell_window():
    sell_window = tk.Toplevel(root)
    sell_window.title("Добавить продавца/арендодателя")
    sell_window.geometry("800x600")

    # Добавление элементов интерфейса для ввода данных
    surname_label = tk.Label(sell_window, text="Фамилия:")
    surname_label.pack()
    surname_entry = tk.Entry(sell_window)
    surname_entry.pack()

    name_label = tk.Label(sell_window, text="Имя:")
    name_label.pack()
    name_entry = tk.Entry(sell_window)
    name_entry.pack()

    middlename_label = tk.Label(sell_window, text="Отчество:")
    middlename_label.pack()
    middlename_entry = tk.Entry(sell_window)
    middlename_entry.pack()

    passport_label = tk.Label(sell_window, text="Паспортные данные:")
    passport_label.pack()
    passport_entry = tk.Entry(sell_window)
    passport_entry.pack()

    telephone_label = tk.Label(sell_window, text="Контакты:")
    telephone_label.pack()
    telephone_entry = tk.Entry(sell_window)
    telephone_entry.pack()

    # Функция для добавления данных в базу данных
    def add_owner():
        surname = surname_entry.get()
        name = name_entry.get()
        middlename = middlename_entry.get()
        passport= passport_entry.get()
        telephone = telephone_entry.get()

        c.execute("INSERT INTO owner (surname, name, middlename, passport, telephone) VALUES (?, ?, ?, ?, ?)",
            (surname, name, middlename, passport, telephone))
        conn.commit()
    # Кнопка для добавления данных
    add_button = tk.Button(sell_window, text="Добавить", command=add_owner)
    add_button.pack()

# Функция для открытия окна просмотра данных
def open_seller_window():
    seller_window = tk.Toplevel(root)
    seller_window.title("Просмотр продавцов/арендодателей")
    seller_window.geometry("800x600")

    # Получение данных из базы данных
    c.execute("SELECT * FROM owner")
    owner = c.fetchall()

    scrollbar = Scrollbar(seller_window)
    scrollbar.pack(side="right", fill="y")

    owner_text = Listbox(seller_window, yscrollcommand=scrollbar.set)
    owner_text.pack(fill="both", expand=True)

    # Отображение данных в текстовом виджете
    #data_text = tk.Text(view_window)
    for row in owner:
        owner_text.insert(tk.END, f"ID: {row[0]}")
        owner_text.insert(tk.END, f"Фамилия: {row[1]}")
        owner_text.insert(tk.END, f"Имя: {row[2]}")
        owner_text.insert(tk.END, f"Отчество: {row[3]}")
        owner_text.insert(tk.END, f"Паспортные данные: {row[4]}")
        owner_text.insert(tk.END, f"Контакты: {row[5]}")
        owner_text.pack()

    scrollbar.config(command=owner_text.yview)






#регистрация продаж
# Функция для открытия окна добавления данных
#регистрация продаж
# Функция для открытия окна добавления данных
def open_sale_window():
    sale_window = tk.Toplevel(root)
    sale_window.title("Создать договор продажи")
    sale_window.geometry("800x600")

    # Добавление элементов интерфейса для ввода данных
    employee_label = tk.Label(sale_window, text="ФИО сотрудника агентства:")
    employee_label.pack()
    employee_entry = tk.Entry(sale_window)
    employee_entry.pack()

    client_label = tk.Label(sale_window, text="ФИО покупателя:")
    client_label.pack()
    client_entry = tk.Entry(sale_window)
    client_entry.pack()

    owner_label = tk.Label(sale_window, text="ФИО владельца:")
    owner_label.pack()
    owner_entry = tk.Entry(sale_window)
    owner_entry.pack()

    realty_label = tk.Label(sale_window, text="Код объекта недвижимости:")
    realty_label.pack()
    realty_entry = tk.Entry(sale_window)
    realty_entry.pack()

    data_label = tk.Label(sale_window, text="Дата (YYYY-MM-DD):")
    data_label.pack()
    data_entry = tk.Entry(sale_window)
    data_entry.pack()

    cena_label = tk.Label(sale_window, text="Цена:")
    cena_label.pack()
    cena_entry = tk.Entry(sale_window)
    cena_entry.pack()

    # Функция для добавления данных в базу данных
    def add_sale():
        employee = employee_entry.get()
        client = client_entry.get()
        owner = owner_entry.get()
        realty = realty_entry.get()
        data = data_entry.get()
        cena = cena_entry.get()

        try:
            # Валидация данных (пример)
            if not all([employee, client, owner, realty, data, cena]):
                raise ValueError("Заполните все поля!")
            float(cena)  # Проверка, что цена - число
            datetime.strptime(data, "%Y-%m-%d")  # Проверка формата даты
        except ValueError as e:
            messagebox.showerror("Ошибка ввода", str(e))
            return


        try:
            c.execute("INSERT INTO sale (employee, client, owner, realty, data, cena) VALUES (?, ?, ?, ?, ?, ?)",
                (employee, client, owner, realty, data, cena))
            conn.commit()
            messagebox.showinfo("Успех", "Запись добавлена!")
            sale_window.destroy()  # Закрываем окно после успешного добавления
        except sqlite3.Error as e:
            messagebox.showerror("Ошибка БД", str(e))

    # Кнопка для добавления данных
    add_button = tk.Button(sale_window, text="Добавить", command=add_sale)
    add_button.pack()

def generate_sale_agreement(row):
    """Генерирует текст договора продажи на основе данных из строки базы данных."""
    try:
        employee = row[1]
        buyer = row[2]
        owner = row[3]
        property_id = row[4]
        date = row[5]  # Уже a string in the database
        price = row[6]

        # Форматируем дату, если она не в нужном формате
        try:
          date_object = datetime.strptime(date, "%Y-%m-%d") # Предполагаем формат YYYY-MM-DD
          formatted_date = date_object.strftime("%d.%m.%Y")
        except ValueError:
          formatted_date = date  # Оставляем как есть, если не удалось преобразовать

        agreement_text = f"""
                            ДОГОВОР КУПЛИ-ПРОДАЖИ

    Настоящий договор заключен между:

    Продавец: {owner}
    Покупатель: {buyer}
    Агент: {employee}

    Объект недвижимости: Номер {property_id}

    Дата заключения договора: {formatted_date}

    Цена объекта недвижимости: {price} рублей

    Подписи сторон:

    ____________________ (Продавец)

    ____________________ (Покупатель)

    ____________________ (Агент)
    """
        return agreement_text
    except Exception as e:
        return f"Ошибка при создании договора: {e}"

# Функция для открытия окна просмотра данных
def open_salee_window():
    salee_window = tk.Toplevel(root)
    salee_window.title("Просмотр договоров о продаже")
    salee_window.geometry("800x600")

    try:
        # Получение данных из базы данных
        with sqlite3.connect('database.db') as conn:
            c = conn.cursor()
            c.execute("SELECT * FROM sale")
            sale = c.fetchall()
    except sqlite3.Error as e:
         messagebox.showerror("Ошибка БД", f"Ошибка при подключении к базе данных: {e}")
         return

    scrollbar = Scrollbar(salee_window)
    scrollbar.pack(side="right", fill="y")

    sale_text = Listbox(salee_window, yscrollcommand=scrollbar.set)
    sale_text.pack(fill="both", expand=True)

    # Отображение данных в текстовом виджете
    for row in sale:
        sale_text.insert(tk.END, f"Сотрудник агентства: {row[1]}")
        sale_text.insert(tk.END, f"Покупатель: {row[2]}")
        sale_text.insert(tk.END, f"Владелец: {row[3]}")
        sale_text.insert(tk.END, f"Объект недвижимости: {row[4]}")
        sale_text.insert(tk.END, f"Дата: {row[5]}")
        sale_text.insert(tk.END, f"Цена: {row[6]}")
        sale_text.insert(tk.END, "")  # Add an empty line

    scrollbar.config(command=sale_text.yview)

    def create_agreement():
        selected_index = sale_text.curselection()
        if selected_index:
            index = selected_index[0]
            record_index = index // 7  # Correct index calculation
            if 0 <= record_index < len(sale):
                selected_row = sale[record_index]
                agreement = generate_sale_agreement(selected_row)
                # Display in a new window (more suitable for larger text)
                agreement_window = Toplevel(salee_window)
                agreement_window.title("Договор купли-продажи")
                agreement_text = tk.Text(agreement_window, wrap="word") #wrap="word" to prevent cutting words
                agreement_text.insert(tk.END, agreement)
                agreement_text.pack(fill="both", expand=True)
                agreement_text.config(state="disabled") #Make it read-only

                #Add a save button
                def save_agreement():
                    filename = tk.filedialog.asksaveasfilename(defaultextension=".txt",
                                                                filetypes=[("Text files", "*.txt"),
                                                                           ("All files", "*.*")])
                    if filename:
                        try:
                            with open(filename, "w", encoding="utf-8") as f:  # Use utf-8 encoding
                                f.write(agreement)
                            messagebox.showinfo("Сохранено", "Договор сохранен")
                        except Exception as e:
                            messagebox.showerror("Ошибка", f"Ошибка сохранения файла: {e}")
                save_button = Button(agreement_window, text="Сохранить", command=save_agreement)
                save_button.pack()


            else:
                messagebox.showerror("Ошибка", "Неверный индекс выбранной записи.")
        else:
            messagebox.showinfo("Внимание", "Выберите договор для создания!")


    create_button = Button(salee_window, text="Создать договор", command=create_agreement)
    create_button.pack()





#регистрация аренд
# Функция для открытия окна добавления данных
def open_rent_window():
    rent_window = tk.Toplevel(root)
    rent_window.title("Создать договор аренды")
    rent_window.geometry("800x600")

    # Добавление элементов интерфейса для ввода данных
    employee_label = tk.Label(rent_window, text="ФИО сотрудника агентства:")
    employee_label.pack()
    employee_entry = tk.Entry(rent_window)
    employee_entry.pack()

    client_label = tk.Label(rent_window, text="ФИО покупателя:")
    client_label.pack()
    client_entry = tk.Entry(rent_window)
    client_entry.pack()

    owner_label = tk.Label(rent_window, text="ФИО владельца:")
    owner_label.pack()
    owner_entry = tk.Entry(rent_window)
    owner_entry.pack()

    realty_label = tk.Label(rent_window, text="Код объекта недвижимости:")
    realty_label.pack()
    realty_entry = tk.Entry(rent_window)
    realty_entry.pack()

    data_label = tk.Label(rent_window, text="Дата:")
    data_label.pack()
    data_entry = tk.Entry(rent_window)
    data_entry.pack()

    cena_label = tk.Label(rent_window, text="Цена:")
    cena_label.pack()
    cena_entry = tk.Entry(rent_window)
    cena_entry.pack()

    period_label = tk.Label(rent_window, text="Срок сдачи:")
    period_label.pack()
    period_entry = tk.Entry(rent_window)
    period_entry.pack()

    # Функция для добавления данных в базу данных
    def add_renta():
        employee = employee_entry.get()
        client = client_entry.get()
        owner = owner_entry.get()
        realty= realty_entry.get()
        data = data_entry.get()
        cena = cena_entry.get()
        period= period_entry.get()


        c.execute("INSERT INTO renta (employee, client, owner, realty, data, cena, period) VALUES (?, ?, ?, ?, ?, ?, ?)",
            (employee, client, owner, realty, data, cena, period))
        conn.commit()
        messagebox.showinfo("Успех", "Запись добавлена!")
        rent_window.destroy()
    # Кнопка для добавления данных
    add_button = tk.Button(rent_window, text="Добавить", command=add_renta)
    add_button.pack()
def generate_rent_agreement(row):
    """Генерирует текст договора аренды на основе данных из строки базы данных."""
    try:
        employee = row[1]  # ID уже не нужен в договоре, начинается с сотрудника
        client = row[2]
        owner = row[3]
        realty = row[4] # Была пропущена переменная realty
        data = row[5]
        cena = row[6]
        period = row[7]

        # Форматируем дату
        try:
          date_object = datetime.strptime(data, "%Y-%m-%d") # Предполагаем формат YYYY-MM-DD
          formatted_date = date_object.strftime("%d.%m.%Y")
        except ValueError:
          formatted_date = data  # Оставляем как есть, если не удалось преобразовать

        agreement_text = f"""
                            ДОГОВОР АРЕНДЫ

    Настоящий договор заключен между:

    Арендодатель: {owner}
    Арендатор: {client}
    Агент: {employee}

    Объект недвижимости: Номер {realty}

    Дата заключения договора: {formatted_date}

    Цена за месяц: {cena} рублей
    Срок аренды: {period} месяцев

    Подписи сторон:

    ____________________ (Арендодатель)

    ____________________ (Арендатор)

    ____________________ (Агент)
    """
        return agreement_text
    except Exception as e:
        return f"Ошибка при создании договора: {e}"

# Функция для открытия окна просмотра данных
def open_rentt_window():
    rentt_window = tk.Toplevel(root)
    rentt_window.title("Просмотр договоров аренды")
    rentt_window.geometry("800x600")

    # Получение данных из базы данных
    c.execute("SELECT * FROM renta")
    renta = c.fetchall()

    scrollbar = Scrollbar(rentt_window)
    scrollbar.pack(side="right", fill="y")

    renta_text = Listbox(rentt_window, yscrollcommand=scrollbar.set)
    renta_text.pack(fill="both", expand=True)

    # Отображение данных в текстовом виджете
    #data_text = tk.Text(view_window)
    for row in renta:
        renta_text.insert(tk.END, f"Сотрудник агентства: {row[1]}")
        renta_text.insert(tk.END, f"Арендатель: {row[2]}")
        renta_text.insert(tk.END, f"Владелец: {row[3]}")
        renta_text.insert(tk.END, f"Код объекта недвижимости: {row[4]}")
        renta_text.insert(tk.END, f"Дата заключения сделки: {row[5]}")
        renta_text.insert(tk.END, f"Цена за месяц: {row[6]}")
        renta_text.insert(tk.END, f"Срок аренды: {row[7]}")
        renta_text.pack()

    scrollbar.config(command=renta_text.yview)

    def create_agreement():
        selected_index = renta_text.curselection()
        if selected_index:
            index = selected_index[0]
            record_index = index // 9  # Now ID displayed in listbox. So record occupies 9 lines
            if 0 <= record_index < len(renta):
                selected_row = renta[record_index]
                agreement = generate_rent_agreement(selected_row)

                agreement_window = Toplevel(rentt_window)
                agreement_window.title("Договор аренды")
                agreement_text = tk.Text(agreement_window, wrap="word")
                agreement_text.insert(tk.END, agreement)
                agreement_text.pack(fill="both", expand=True)
                agreement_text.config(state="disabled")  # Make it read-only

                def save_agreement():
                    filename = filedialog.asksaveasfilename(defaultextension=".txt",
                                                            filetypes=[("Text files", "*.txt"),
                                                                       ("All files", "*.*")])
                    if filename:
                        try:
                            with open(filename, "w", encoding="utf-8") as f:
                                f.write(agreement)
                            messagebox.showinfo("Сохранено", "Договор сохранен")
                        except Exception as e:
                            messagebox.showerror("Ошибка", f"Ошибка сохранения файла: {e}")

                save_button = Button(agreement_window, text="Сохранить", command=save_agreement)
                save_button.pack()

            else:
                messagebox.showerror("Ошибка", "Неверный индекс выбранной записи.")
        else:
            messagebox.showinfo("Внимание", "Выберите договор для создания!")

    create_button = Button(rentt_window, text="Создать договор", command=create_agreement)
    create_button.pack()

# Кнопки для открытия окон добавления и просмотра данных
add_button = tk.Button(root, text="Добавить объект недвижимости", command=open_add_window)
add_button.place (x='1', y='1')
view_button = tk.Button(root, text="Просмотреть объекты недвижимости", command=open_view_window)
view_button.place (x='1', y='30')
work_button= tk.Button(root, text="Добавить сотрудника", command=open_work_window)
work_button.place (x='500', y='1')
worker_button= tk.Button(root, text="Просмотреть сотрудников", command=open_worker_window)
worker_button.place (x='500', y='30')
buy_button= tk.Button(root, text="Добавить покупателя/арендатора", command=open_buy_window)
buy_button.place (x='1', y='200')
buyer_button= tk.Button(root, text="Просмотреть покупателей/арендаторов", command=open_buyer_window)
buyer_button.place (x='1', y='230')
sell_button= tk.Button(root, text="Добавить продавца/арендодателя", command=open_sell_window)
sell_button.place (x='1', y='100')
seller_button= tk.Button(root, text="Просмотреть продавцов/арендодателей", command=open_seller_window)
seller_button.place (x='1', y='130')
sale_button= tk.Button(root, text="Добавить договор продажи", command=open_sale_window)
sale_button.place (x='500', y='100')
salee_button= tk.Button(root, text="Просмотреть договора продажи", command=open_salee_window)
salee_button.place (x='500', y='130')
rent_button= tk.Button(root, text="Добавить договор аренды", command=open_rent_window)
rent_button.place (x='500', y='200')
rentt_button= tk.Button(root, text="Просмотреть договора аренды", command=open_rentt_window)
rentt_button.place (x='500', y='230')

root.mainloop()