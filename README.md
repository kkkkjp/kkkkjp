import pandas as pd
import matplotlib.pyplot as plt

def display_menu():
    print("Meny")
    print("1. Ange aktiefil")
    print("2. Ange indexfil")
    print("3. Ange tidsintervall")
    print("4. Visa graf")
    print("5. Avsluta")
    return input("Välj ett alternativ: ").lower()

def load_csv_file(file_name):
    try:
        df = pd.read_csv(file_name)
        return df
    except pd.errors.ParserError as e:
        print(f"Fel vid inläsning av filen: {e}")
        return pd.DataFrame()

def choose_time_interval(df):
    start_date = input("Ange startdatum (YYYY-MM-DD): ")
    end_date = input("Ange slutdatum (YYYY-MM-DD): ")

    try:
        start_date = pd.to_datetime(start_date)
        end_date = pd.to_datetime(end_date)
    except ValueError:
        print("Ogiltigt datumformat. Försök igen.")
        return None, None

    df_interval = df[df.index.between(start_date, end_date)]
    return df_interval, start_date, end_date

def process_input(df):
    choice = display_menu()

    if choice == "1":
        file_name = input("Ange aktiefil: ")
        df_aktie = load_csv_file(file_name)
        df_aktie.columns = ["Kurs"]
        df_aktie.index = pd.to_datetime(df_aktie["Datum"])
        df_aktie = df_aktie.set_index("Datum")
        df_aktie.plot(kind="line", figsize=(10, 6))
        plt.title("Aktiekurs")
        plt.xlabel("Datum")
        plt.ylabel("urs")
        plt.show()
    elif choice == "2":
        file_name = input("Ange Indexfil: ")
        df_index = load_csv_file(file_name)
        df_index.columns = ["Kurs"]
        df_index.index = pd.to_datetime(df_index["Datum"])
        df_index = df_index.set_index("Datum")
        df_index.plot(kind="line", figsize=(10, 6))
        plt.title("Index")
        plt.xlabel("Datum")
        plt.ylabel("Kurs")
        plt.show()
    elif choice == "3":
        if df_aktie.empty:
            print("Ladda in aktiedata innan du kan välja tidsintervall.")
        else:
            df_interval, start_date, end_date = choose_time_interval(df_aktie)
            if df_interval.empty:
                continue
            df_interval.plot(kind="line", figsize=(10, 6))
            plt.title("Aktiekurs")
            plt.xlabel("Datum")
            plt.ylabel("Kurs")
            plt.show()
    elif choice == "4":
        if df_aktie.empty or df_index.empty:
            print("Ladda in aktie- och indexdata innan du kan visa grafen.")
        else:
            fig, ax = plt.subplots(2, 1, figsize=(10, 6))
            ax[0].plot(df_aktie.index, df_aktie["Kurs"], label="Aktie")
            ax[1].plot(df_index.index, df_index["Kurs"], label="Index")
            ax[0].set_title("Aktiekurs")
            ax[1].set_title("Index")
            ax[0].set_xlabel("Datum")
            ax[1].set_xlabel("Datum")
            ax[0].set_ylabel("Kurs")
            ax[1].set_ylabel("Kurs")
            plt.legend(loc="upper right")
            plt.show()
    elif choice == "5":
        print("Programmet har avslutats.")
        break
    else:
        print("Ogiltigt val.")

if __name__ == "__main__":
    process_input(pd.DataFrame())

