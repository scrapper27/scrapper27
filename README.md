
# Owen Stackhouse The goals of the application are to allow users to input their inventory 
# and organize it in a way that makes it easy to read. Additionally, users should be able to 
# find and remove inventory if it is bought or check if it is still available. It's not done 
# yet there's still a lot to be done 

import tkinter as tk
from tkinter import messagebox


#This is the code Like the home screen it's where it'll pop up with the list of what you have 
# that you can highlight over then go to the buttons to decipher what you want to do with it 
# it'll have exits input and delete buttons in those old decipher before you go 
class Application(tk.Frame):
    def __init__(self, master=None):
        super().__init__(master)
        self.master = master
        self.master.title("Text Manager")
        self.pack()
        self.text_window()

    def text_window (self):
        self.input_button = tk.Button(self, text="Input Text", command=self.input)
        self.input_button.pack(side="left")

        self.delete_button = tk.Button(self, text="Delete Text", command=self.delete)
        self.delete_button.pack(side="left")

        self.exit_button = tk.Button(self, text="Exit", command=self.master.quit)
        self.exit_button.pack(side="right")

        self.saved_items_label = tk.Label(self, text="Saved items:")
        self.saved_items_label.pack()

        self.saved_items_listbox = tk.Listbox(self, selectmode=tk.SINGLE)
        self.saved_items_listbox.pack()

        self.load_save()

    def input(self):
        Input_Window(self.master, self.saved_items_listbox)

    def delete(self):
        Delete_Window(self.master, self.saved_items_listbox)

    def load_save(self):
        with open("saved_text.txt", "r") as f:
            self.saved_items = f.read().splitlines()
        for item in self.saved_items:
            self.saved_items_listbox.insert(tk.END, item)

# This is the window where after you push inputs in the like load-up screen it'll have a tab to
#  put something you want to input like a word or phrase supposed to be stuff for a shopping place
#  but I'm trying to figure that out and once you post to save it'll save it 
class Input_Window(tk.Toplevel):
    def __init__(self, parent, saved_items_listbox):
        super().__init__(parent)
        self.title("Input Text")
        self.saved_items_listbox = saved_items_listbox
        self.text()

    def text(self):
        self.text_label = tk.Label(self, text="Enter some text:")
        self.text_label.pack()

        self.text_entry = tk.Entry(self)
        self.text_entry.pack()

        self.save_button = tk.Button(self, text="Save", command=self.save)
        self.save_button.pack()

    def save(self):
        text_input = self.text_entry.get()

        if not text_input:
            messagebox.showwarning("Empty Input", "Please enter some text")
            return

        with open("saved_text.txt", "a") as f:
            f.write(text_input + "\n")

        self.saved_items_listbox.insert(tk.END, text_input)
        messagebox.showinfo("Text Saved", "Your text has been saved to saved_text.txt")

        self.destroy()

# This is where when you highlight the text to press delete it'll take you to a screen where just basically says are you sure you won't delete it
class Delete_Window(tk.Toplevel):
    def __init__(self, parent, saved_items_listbox):
        super().__init__(parent)
        self.title("Delete Text")
        self.saved_items_listbox = saved_items_listbox
        self.text()

    def text(self):
        self.saved_items_label = tk.Label(self, text= "Are you sure you want to delete ?" )
        self.saved_items_label.pack()

        self.saved_items_listbox.pack()

        self.delete_button = tk.Button(self, text="Delete", command=self.delete)
        self.delete_button.pack()

    def delete(self):

        selected_index = self.saved_items_listbox.curselection()

        if len(selected_index) == 1:
            selected_item = self.saved_items_listbox.get(selected_index)
            self.saved_items_listbox.delete(selected_index)
            with open("saved_text.txt", "r") as f:
                lines = f.readlines()
            with open("saved_text.txt", "w") as f:
                for line in lines:
                    if line.strip() != selected_item:
                        f.write(line)
            messagebox.showinfo("Text Deleted", "The selected text has been deleted from saved_text.txt")

        self.destroy()




root = tk.Tk()
app = Application(master=root)
app.load_save()
app.mainloop()
