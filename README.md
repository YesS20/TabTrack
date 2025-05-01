import time
import csv
import pandas as pd
from datetime import datetime
import pygetwindow as gw
import os

LOG_FILE = "tab_usage_log.csv"

def get_active_window_title():
    try:
        win = gw.getActiveWindow()
        return win.title if win else "Unknown"
    except Exception as e:
        return "Error"

def init_log():
    if not os.path.exists(LOG_FILE):
        with open(LOG_FILE, "w", newline="", encoding='utf-8') as f:
            writer = csv.writer(f)
            writer.writerow(["Window Title", "Start Time", "End Time", "Duration (seconds)"])

def log_window_activity(title, start, end):
    duration = round((end - start).total_seconds(), 2)
    with open(LOG_FILE, "a", newline="", encoding='utf-8') as f:
        writer = csv.writer(f)
        writer.writerow([title, start.strftime('%Y-%m-%d %H:%M:%S'),
                         end.strftime('%Y-%m-%d %H:%M:%S'), duration])

def main():
    print("TabTrack is running. Press Ctrl+C to stop.")
    init_log()
    last_title = get_active_window_title()
    start_time = datetime.now()

    try:
        while True:
            current_title = get_active_window_title()
            if current_title != last_title:
                end_time = datetime.now()
                log_window_activity(last_title, start_time, end_time)
                last_title = current_title
                start_time = end_time
            time.sleep(1)
    except KeyboardInterrupt:
        print("Stopped.")
        end_time = datetime.now()
        log_window_activity(last_title, start_time, end_time)

if name == "__main__":
    main()
