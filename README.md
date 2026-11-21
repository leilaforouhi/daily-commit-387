import json
from pathlib import Path

DATA_FILE = Path("project_times.json")

def load_data():
    if DATA_FILE.exists():
        try:
            return json.loads(
                DATA_FILE.read_text(encoding="utf-8")
            )
        except json.JSONDecodeError:
            print("Data file is corrupted.")
    return {}

def save_data(data):
    DATA_FILE.write_text(
        json.dumps(data, indent=4, ensure_ascii=False),
        encoding="utf-8"
    )

def add_time(data):
    project = input("Project name: ").strip()

    if not project:
        print("Project name cannot be empty.")
        return

    try:
        hours = float(input("Hours spent: "))

        if hours <= 0:
            print("Hours must be greater than zero.")
            return

        data[project] = data.get(project, 0) + hours
        save_data(data)

        print("Time recorded successfully.")

    except ValueError:
        print("Please enter a valid number.")

def show_report(data):
    if not data:
        print("No project data available.")
        return

    total = sum(data.values())
    largest = max(data, key=data.get)

    print("\n--- Project Time Report ---")

    for project, hours in sorted(
        data.items(),
        key=lambda item: item[1],
        reverse=True
    ):
        percentage = hours / total * 100

        print(
            f"{project}: {hours:.2f} hours "
            f"({percentage:.1f}%)"
        )

    print(f"\nTotal time: {total:.2f} hours")
    print(f"Most time-consuming project: {largest}")

def main():
    data = load_data()

    while True:
        print("\n1. Add project time")
        print("2. Show report")
        print("3. Exit")

        choice = input("Choose: ")

        if choice == "1":
            add_time(data)
        elif choice == "2":
            show_report(data)
        elif choice == "3":
            break
        else:
            print("Invalid choice.")

if __name__ == "__main__":
    main()
