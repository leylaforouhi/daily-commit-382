
import json
from pathlib import Path
from collections import Counter

def analyze_json(data, path="root", stats=None):
    if stats is None:
        stats = Counter()

    if isinstance(data, dict):
        stats["objects"] += 1

        for key, value in data.items():
            stats["keys"] += 1
            analyze_json(value, f"{path}.{key}", stats)

    elif isinstance(data, list):
        stats["arrays"] += 1

        for index, value in enumerate(data):
            analyze_json(value, f"{path}[{index}]", stats)

    else:
        stats["values"] += 1

    return stats

def main():
    filename = input("JSON file path: ").strip()
    file_path = Path(filename)

    if not file_path.is_file():
        print("File not found.")
        return

    try:
        data = json.loads(file_path.read_text(encoding="utf-8"))
        stats = analyze_json(data)

        print("\n--- JSON Analysis ---")
        print(f"Objects: {stats['objects']}")
        print(f"Arrays: {stats['arrays']}")
        print(f"Keys: {stats['keys']}")
        print(f"Primitive values: {stats['values']}")

    except json.JSONDecodeError as error:
        print(f"Invalid JSON: {error}")
    except OSError as error:
        print(f"File error: {error}")

if __name__ == "__main__":
    main()
