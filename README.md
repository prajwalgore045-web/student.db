# student.db
import sqlite3
import tkinter as tk
from tkinter import messagebox

con = sqlite3.connect("students.db")
cur = con.cursor()

cur.execute("""
CREATE TABLE IF NOT EXISTS students(
id INTEGER PRIMARY KEY AUTOINCREMENT,
name TEXT,
roll TEXT,
course TEXT,
phone TEXT
)
""")

con.commit()

root = tk.Tk()
root.title("Student Management System")
root.geometry("600x500")

def add_student():
    n = name_entry.get()
    r = roll_entry.get()
    c = course_entry.get()
    p = phone_entry.get()

    if n == "" or r == "" or c == "" or p == "":
        messagebox.showerror("Error","All fields required")
        return

    cur.execute("INSERT INTO students(name,roll,course,phone) VALUES(?,?,?,?)",
                (n,r,c,p))
    con.commit()

    messagebox.showinfo("Success","Student Added")

    clear_fields()
    show_students()


def show_students():
    listbox.delete(0,tk.END)

    cur.execute("SELECT * FROM students")
    rows = cur.fetchall()

    for row in rows:
        listbox.insert(tk.END,row)


def search_student():
    r = roll_entry.get()

    listbox.delete(0,tk.END)

    cur.execute("SELECT * FROM students WHERE roll=?",(r,))
    rows = cur.fetchall()

    for row in rows:
        listbox.insert(tk.END,row)


def delete_student():
    r = roll_entry.get()

    cur.execute("DELETE FROM students WHERE roll=?",(r,))
    con.commit()

    messagebox.showinfo("Deleted","Student Removed")

    clear_fields()
    show_students()


def clear_fields():
    name_entry.delete(0,tk.END)
    roll_entry.delete(0,tk.END)
    course_entry.delete(0,tk.END)
    phone_entry.delete(0,tk.END)


title = tk.Label(root,text="Student Information System",font=("Arial",18,"bold"))
title.pack(pady=10)

frame = tk.Frame(root)
frame.pack()

tk.Label(frame,text="Name").grid(row=0,column=0,padx=10,pady=5)
tk.Label(frame,text="Roll No").grid(row=1,column=0,padx=10,pady=5)
tk.Label(frame,text="Course").grid(row=2,column=0,padx=10,pady=5)
tk.Label(frame,text="Phone").grid(row=3,column=0,padx=10,pady=5)

name_entry = tk.Entry(frame,width=25)
roll_entry = tk.Entry(frame,width=25)
course_entry = tk.Entry(frame,width=25)
phone_entry = tk.Entry(frame,width=25)

name_entry.grid(row=0,column=1)
roll_entry.grid(row=1,column=1)
course_entry.grid(row=2,column=1)
phone_entry.grid(row=3,column=1)

btn_frame = tk.Frame(root)
btn_frame.pack(pady=10)

tk.Button(btn_frame,text="Add",width=12,command=add_student).grid(row=0,column=0,padx=5)
tk.Button(btn_frame,text="View",width=12,command=show_students).grid(row=0,column=1,padx=5)
tk.Button(btn_frame,text="Search",width=12,command=search_student).grid(row=0,column=2,padx=5)
tk.Button(btn_frame,text="Delete",width=12,command=delete_student).grid(row=0,column=3,padx=5)
tk.Button(btn_frame,text="Clear",width=12,command=clear_fields).grid(row=0,column=4,padx=5)

listbox = tk.Listbox(root,width=80,height=10)
listbox.pack(pady=10)

show_students()

root.mainloop()
