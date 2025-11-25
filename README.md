# passwordGenerator
from cryptography.fernet import Fernet
import string
import random
import os

# Load or create key
if os.path.exists("key.key"):
    key = open("key.key", "rb").read()
else:
    key = Fernet.generate_key()
    open("key.key", "wb").write(key)

cipher = Fernet(key)

while True:
    print("\n1. Generate Password")
    print("2. View Passwords")
    print("3. Exit")

    ch = input("Enter choice: ")

    if ch == "1":
        length = int(input("Enter password length: "))
        chars = string.ascii_letters + string.digits + string.punctuation

        password = "".join(random.choice(chars) for _ in range(length))
        encrypted = cipher.encrypt(password.encode())

        acc = input("Enter account name: ")

        with open("passwords.txt", "a") as f:
            f.write(acc + " : " + encrypted.decode() + "\n")

        print("\nYour Password =", password)
        print("Saved successfully!\n")

    elif ch == "2":
        if not os.path.exists("passwords.txt"):
            print("No passwords saved yet.")
            continue


        print("\nSaved Passwords:")
        for line in open("passwords.txt", "r"):
            acc, enc = line.strip().split(" : ")
            dec = cipher.decrypt(enc.encode()).decode()
            print(acc, "-->", dec)

    elif ch == "3":
        break

    else:
        print("Invalid choice!")
