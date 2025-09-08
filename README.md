PATTERNS = [
    (r"AKIA[0-9A-Z]{16}", "AWS Access Key"),
    (r"AIza[0-9A-Za-z-_]{35}", "Google API Key"),
    (r"ghp_[0-9a-zA-Z]{36}", "GitHub Token"),
    (r"(?i)password\s*=\s*['\"].+['\"]", "Hardcoded Password"),
]

def scan_secrets(directory="."):
    findings = []
  for root, _, files in os.walk(directory):
        for file in files:
            if file.endswith((".py", ".env", ".js", ".json", ".yaml", ".yml")):
                path = os.path.join(root, file)
                try:
                    with open(path, encoding="utf-8", errors="ignore") as :
                        for i, line in enumerate(f, 1):
                            for pattern, desc in PATTERNS:
                                if re.search(pattern, line):
                                    findings.append((path, i, desc, line.strip()))
                except:
                    pass
    return findings

if __name__ == "__main__":
    results = scan_secrets()
    if results:
        print("🚨 Possible secrets found:\n")
        for file, line, desc, content in results:
            print(f"{desc:20} | {file}:{line} -> {content}")
    else:
        print("✅ No secrets found")
