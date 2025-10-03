python tracker.pysort-tracker-py - PyPI# tracker.py

# Express Delivery Tracker with Animated Splash Screen


import tkinter as tk

from tkinter import ttk

import time

import threading

from datetime import datetime

import os

import platform


# Delivery status steps

statuses = [

    "✅ Picked Up – Package collected in Texas",

    "🚚 In Transit – Moving via Tesla SUV toward Florida",

    "📍 Out for Delivery – Driver approaching destination",

    "📬 Delivered – Package handed over in Florida"

]


tracking_log = []


def start_tracking():

    def run():

        tracking_number = entry.get().strip()

        if not tracking_number:

            status_label.config(text="⚠️ Please enter a tracking number first.")

            return


        entry.config(state="disabled")

        start_button.config(state="disabled")


        for i, status in enumerate(statuses, start=1):

            progress["value"] = i * 25

            log_entry = f"{datetime.now().strftime('%Y-%m-%d %H:%M:%S')} | Step {i}: {status}"

            tracking_log.append(log_entry)

            status_label.config(text=f"{tracking_number}\n\n{log_entry}")

            root.update_idletasks()

            time.sleep(3)


        delivered_time = datetime.now().strftime("%Y-%m-%d %H:%M:%S")

        final_entry = f"{delivered_time} | ✅ Delivery completed successfully!"

        tracking_log.append(final_entry)

        status_label.config(text=f"{tracking_number}\n\n{final_entry}")

        save_button.config(state="normal")


    threading.Thread(target=run).start()


def save_log():

    tracking_number = entry.get().strip()

    filename = f"tracking_{tracking_number}.txt".replace(" ", "_")

    with open(filename, "w", encoding="utf-8") as f:

        f.write("📦 Express Delivery Tracking Log\n")

        f.write(f"Tracking Number: {tracking_number}\n\n")

        for line in tracking_log:

            f.write(line + "\n")


    status_label.config(text=f"✅ Log saved as {filename}")


    if platform.system() == "Windows":

        os.startfile(filename)

    elif platform.system() == "Darwin":

        os.system(f"open '{filename}'")

    else:

        os.system(f"xdg-open '{filename}'")


# ---------- SPLASH SCREEN ----------

def show_splash():

    splash = tk.Tk()

    splash.overrideredirect(True)

    splash.geometry("420x340+500+200")


    # Splash logo

    try:

        img = tk.PhotoImage(file="splash.png")

        tk.Label(splash, image=img).pack(pady=20)

    except Exception:

        tk.Label(splash, text="📦", font=("Arial", 40)).pack(pady=20)


    # Loading text

    tk.Label(splash, text="Loading Express Delivery Tracker…", font=("Arial", 12, "italic")).pack(pady=10)


    # Progress bar

    bar = ttk.Progressbar(splash, orient="horizontal", length=300, mode="determinate", maximum=100)

    bar.pack(pady=20)


    def animate_bar():

        for i in range(101):

            bar["value"] = i

            splash.update_idletasks()

            time.sleep(0.03)  # controls speed of fill

        splash.destroy()

        build_main()


    threading.Thread(target=animate_bar).start()

    splash.mainloop()


# ---------- MAIN WINDOW ----------

def build_main():

    global root, entry, progress, status_label, start_button, save_button

    root = tk.Tk()

    root.title("Express Delivery Tracker")

    root.geometry("600x370")


    title_label = tk.Label(root, text="📦 Express Delivery Tracker", font=("Arial", 16, "bold"))

    title_label.pack(pady=10)


    tk.Label(root, text="Enter Tracking Number:", font=("Arial", 12)).pack(pady=5)

    entry = tk.Entry(root, font=("Arial", 12), width=30)

    entry.pack(pady=5)

    entry.insert(0, "EXP-TXFL-R1LSWI")  # default tracking number


    progress = ttk.Progressbar(root, orient="horizontal", length=400, mode="determinate", maximum=100)

    progress.pack(pady=15)


    status_label = tk.Label(root, text="Enter tracking number and click 'Start Tracking'", font=("Arial", 12), justify="center")

    status_label.pack(pady=10)


    start_button = tk.Button(root, text="Start Tracking", command=start_tracking, font=("Arial", 12), bg="#2196F3", fg="white")

    start_button.pack(pady=5)


    save_button = tk.Button(root, text="Save Tracking Log", command=save_log, font=("Arial", 12), bg="#4CAF50", fg="white")

    save_button.pack(pady=5)

    save_button.config(state="disabled")


    root.mainloop()


# ---------- START APP ----------

if __name__ == "__main__":

    show_splash()

@●●●●●●