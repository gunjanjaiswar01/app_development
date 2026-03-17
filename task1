import tkinter as tk
from tkinter import ttk,messagebox
import sqlite3
from datetime import date,datetime,timedelta
import matplotlib.pyplot as plt

# DATABASE
conn = sqlite3.connect("fitness.db")
cursor = conn.cursor()

cursor.execute("""
CREATE TABLE IF NOT EXISTS users(
username TEXT PRIMARY KEY,
password TEXT
)
""")

cursor.execute("""
CREATE TABLE IF NOT EXISTS fitness(
id INTEGER PRIMARY KEY AUTOINCREMENT,
date TEXT,
steps INTEGER,
exercise TEXT,
workout_time INTEGER,
calories INTEGER
)
""")

conn.commit()

# ---------------- LOGIN WINDOW ----------------

def login():

    username = user_entry.get()
    password = pass_entry.get()

    cursor.execute("SELECT * FROM users WHERE username=? AND password=?",(username,password))
    result = cursor.fetchone()

    if result:
        login_window.destroy()
        open_app()
    else:
        messagebox.showerror("Error","Invalid Login")


def register():

    username = user_entry.get()
    password = pass_entry.get()

    try:
        cursor.execute("INSERT INTO users VALUES(?,?)",(username,password))
        conn.commit()
        messagebox.showinfo("Success","User Registered")
    except:
        messagebox.showerror("Error","User already exists")


login_window = tk.Tk()
login_window.title("Fitness Tracker Login")
login_window.geometry("300x250")

tk.Label(login_window,text="Fitness Tracker",font=("Arial",16,"bold")).pack(pady=10)

tk.Label(login_window,text="Username").pack()
user_entry = tk.Entry(login_window)
user_entry.pack()

tk.Label(login_window,text="Password").pack()
pass_entry = tk.Entry(login_window,show="*")
pass_entry.pack()

tk.Button(login_window,text="Login",command=login,bg="green",fg="white").pack(pady=5)
tk.Button(login_window,text="Register",command=register,bg="blue",fg="white").pack()

# ---------------- MAIN APP ----------------

def open_app():

    root = tk.Tk()
    root.title("Fitness Tracker")
    root.geometry("750x550")
    root.configure(bg="#f5f7fa")

    title = tk.Label(root,text="Fitness Tracker Dashboard",
    font=("Arial",20,"bold"),bg="#f5f7fa")
    title.pack(pady=10)

    frame = tk.Frame(root,bg="white",bd=2,relief="groove")
    frame.pack(pady=10)

    tk.Label(frame,text="Steps").grid(row=0,column=0,padx=10,pady=10)
    steps_entry = tk.Entry(frame)
    steps_entry.grid(row=0,column=1)

    tk.Label(frame,text="Exercise").grid(row=1,column=0,padx=10,pady=10)
    exercise_entry = tk.Entry(frame)
    exercise_entry.grid(row=1,column=1)

    tk.Label(frame,text="Workout Time").grid(row=2,column=0,padx=10,pady=10)
    time_entry = tk.Entry(frame)
    time_entry.grid(row=2,column=1)

    tk.Label(frame,text="Calories").grid(row=3,column=0,padx=10,pady=10)
    cal_entry = tk.Entry(frame)
    cal_entry.grid(row=3,column=1)

    # SAVE DATA
    def save_data():

        try:
            steps = int(steps_entry.get())
            exercise = exercise_entry.get()
            time = int(time_entry.get())
            cal = int(cal_entry.get())

            cursor.execute(
            "INSERT INTO fitness(date,steps,exercise,workout_time,calories) VALUES(?,?,?,?,?)",
            (str(date.today()),steps,exercise,time,cal)
            )

            conn.commit()

            messagebox.showinfo("Saved","Activity Saved")

        except:
            messagebox.showerror("Error","Enter valid data")

    tk.Button(root,text="Save Activity",command=save_data,
    bg="#27ae60",fg="white",font=("Arial",12)).pack(pady=5)

    # ---------------- DAILY GOAL ----------------

    progress_frame = tk.Frame(root,bg="white",bd=2,relief="groove")
    progress_frame.pack(pady=10)

    tk.Label(progress_frame,text="Daily Goal Progress (10000 Steps)").pack()

    progress = ttk.Progressbar(progress_frame,length=350)
    progress.pack(pady=10)

    def update_progress():

        cursor.execute("SELECT SUM(steps) FROM fitness WHERE date=?",(str(date.today()),))
        result = cursor.fetchone()[0]

        if result:
            progress['value'] = min((result/10000)*100,100)
        else:
            progress['value'] = 0

    tk.Button(root,text="Update Dashboard",
    command=update_progress,bg="#2c3e50",fg="white").pack()

    # ---------------- WEEKLY GRAPH ----------------

    def weekly_graph():

        week_dates=[]
        week_steps=[]

        for i in range(7):

            day = date.today() - timedelta(days=i)
            cursor.execute("SELECT SUM(steps) FROM fitness WHERE date=?",(str(day),))
            result = cursor.fetchone()[0]

            week_dates.append(str(day))
            week_steps.append(result if result else 0)

        week_dates.reverse()
        week_steps.reverse()

        plt.figure(figsize=(6,4))
        plt.plot(week_dates,week_steps,marker='o')
        plt.title("Weekly Steps Progress")
        plt.xlabel("Date")
        plt.ylabel("Steps")
        plt.grid(True)
        plt.show()

    tk.Button(root,text="Weekly Steps Graph",
    command=weekly_graph,bg="#e67e22",fg="white").pack(pady=5)

    # ---------------- CALORIES PIE CHART ----------------

    def calories_chart():

        cursor.execute("SELECT exercise,SUM(calories) FROM fitness GROUP BY exercise")
        data = cursor.fetchall()

        if len(data)==0:
            messagebox.showinfo("Info","No data available")
            return

        labels=[i[0] for i in data]
        calories=[i[1] for i in data]

        plt.figure(figsize=(5,5))
        plt.pie(calories,labels=labels,autopct='%1.1f%%')
        plt.title("Calories Burned by Exercise")
        plt.show()

    tk.Button(root,text="Calories Pie Chart",
    command=calories_chart,bg="#8e44ad",fg="white").pack(pady=5)

    root.mainloop()

login_window.mainloop()