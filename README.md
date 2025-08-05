# HealthGuard
Smart Health Checker built using Python
# HealthGuard– Smart Health Checker 🩺💻

This is a Python-based smart health checker app that calculates BMI (Body Mass Index) and gives health status using a simple GUI built with Tkinter.

## 🔥 Features

* Easy-to-use GUI built using Tkinter
* Input height and weight in centimeters and kilograms
* BMI is calculated and categorized (Underweight, Normal, Overweight, Obese)
* Clean and responsive interface
* Helpful for beginners learning Python and health apps

## 🖼️ Screenshots

You can find the screenshots in the repository.

## 🚀 How to Run

Make sure Python is installed on your system.

Steps:

1. Download or clone this repository.
2. Open `HealthGuard.py` in any Python IDE or editor.
3. Run the script.


# python HealthGuard.py

# CODE #
import tkinter as tk
from tkinter import messagebox
import matplotlib.pyplot as plt
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas


def calculate_bmi(weight, height_cm):
    height_m = height_cm / 100
    return round(weight / (height_m ** 2), 2)


def get_bmi_status(bmi):
    if bmi < 18.5:
        return "Underweight"
    elif 18.5 <= bmi < 25:
        return "Normal"
    elif 25 <= bmi < 30:
        return "Overweight"
    else:
        return "Obese"


def assess_heart_risk(age, bmi):
    if age > 50 or bmi > 30:
        return "High"
    elif age > 35 or bmi > 25:
        return "Medium"
    else:
        return "Low"


def analyze_symptoms(symptoms):
    keywords = ["chest", "pain", "shortness", "breath", "tired", "pressure"]
    return any(word in symptoms.lower() for word in keywords)


def mental_health_check():
    questions = [
        "Do you feel anxious often?",
        "Do you sleep well at night?",
        "Do you feel sad or down frequently?",
        "Do you enjoy activities as before?",
        "Do you feel stressed most days?"
    ]
    score = 0
    for q in questions:
        response = messagebox.askyesno("Mental Health Check", q)
        score += int(response)
    if score >= 4:
        return "Mental Health: Needs Attention"
    elif score >= 2:
        return "Mental Health: Moderate"
    else:
        return "Mental Health: Good"


def personalized_advice(bmi):
    if bmi > 25:
        return "Exercise regularly, eat healthy, consult a dietician."
    elif bmi < 18.5:
        return "Include more calories and proteins in your diet."
    else:
        return "Maintain your current lifestyle. Stay active."


def generate_pdf_report(name, age, weight, height, bmi, status, risk, advice):
    c = canvas.Canvas("Health_Report.pdf", pagesize=letter)
    c.setFont("Helvetica", 12)

    c.drawString(100, 750, f"Health Report – {name}")
    c.drawString(100, 730, f"Age: {age}")
    c.drawString(100, 710, f"Weight: {weight} kg")
    c.drawString(100, 690, f"Height: {height} cm")
    c.drawString(100, 670, f"BMI: {bmi} ({status})")
    c.drawString(100, 650, f"Heart Risk: {risk}")
    c.drawString(100, 630, f"Advice: {advice}")
    c.save()


def show_chart(bmi, age):
    labels = 'BMI Contribution', 'Age Contribution'
    sizes = [bmi, age]
    colors = ['lightblue', 'lightcoral']
    plt.pie(sizes, labels=labels, colors=colors, autopct='%1.1f%%', startangle=140)
    plt.axis('equal')
    plt.title('Health Risk Breakdown')
    plt.show()


def run_health_app(username):
    def analyze():
        try:

            age = entry_age.get().strip()
            weight = entry_weight.get().strip()
            height = entry_height.get().strip()

            if not age.isdigit() or not weight.replace('.', '', 1).isdigit() or not height.replace('.', '',
                                                                                                   1).isdigit():
                raise ValueError("Please enter valid numbers.")

            age = int(age)
            weight = float(weight)
            height = float(height)

            bmi = calculate_bmi(weight, height)
            status = get_bmi_status(bmi)
            risk = assess_heart_risk(age, bmi)
            advice = personalized_advice(bmi)

            result = f"{username},\n\nBMI: {bmi} ({status})\nHeart Risk: {risk}"
            if analyze_symptoms(entry_symptoms.get()):
                result += "\nSymptoms may suggest a heart condition. Please consult a doctor."
            result += f"\n\n{mental_health_check()}\n\nAdvice: {advice}"

            generate_pdf_report(username, age, weight, height, bmi, status, risk, advice)
            messagebox.showinfo("Health Summary", result)
            show_chart(bmi, age)

        except ValueError as e:
            messagebox.showerror("Input Error", str(e))
        except Exception as e:
            messagebox.showerror("Unexpected Error", f"An unexpected error occurred: {str(e)}")

    root = tk.Tk()
    root.title("HealthGuard+ Pro")
    root.geometry("400x400")

    tk.Label(root, text=f"Welcome, {username}!", font=("Arial", 14)).pack(pady=10)
    tk.Label(root, text="Age").pack()
    entry_age = tk.Entry(root)
    entry_age.pack()

    tk.Label(root, text="Weight (kg)").pack()
    entry_weight = tk.Entry(root)
    entry_weight.pack()

    tk.Label(root, text="Height (cm)").pack()
    entry_height = tk.Entry(root)
    entry_height.pack()

    tk.Label(root, text="Symptoms").pack()
    entry_symptoms = tk.Entry(root)
    entry_symptoms.pack()

    tk.Button(root, text="Analyze My Health", command=analyze).pack(pady=15)
    root.mainloop()


def simple_login():
    def submit_name():
        username = entry_name.get().strip()
        if username == "":
            messagebox.showerror("Error", "Please enter your name.")
        else:
            login.destroy()
            run_health_app(username)

    login = tk.Tk()
    login.title("Login - HealthGuard+ Pro")
    login.geometry("300x150")

    tk.Label(login, text="Enter Your Name", font=("Arial", 12)).pack(pady=10)
    entry_name = tk.Entry(login)
    entry_name.pack(pady=5)

    tk.Button(login, text="Login", command=submit_name).pack(pady=10)
    login.mainloop()


simple_login()

