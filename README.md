# PROYECTO-III

    import tkinter as tk
    from tkinter import filedialog, messagebox
    from tkinter import scrolledtext

    class TextEditor:
        def __init__(self, root):
            self.root = root
            self.root.title("Editor de Texto")
            self.text_area = scrolledtext.ScrolledText(self.root, wrap=tk.WORD)
            self.text_area.pack(fill=tk.BOTH, expand=1)
            
            self.current_file = None
            self.text_undo_stack = []  #para deshacer
            self.text_redo_stack = []  #para rehacer
            self.text_area.bind("<Key>", self.add_to_undo_stack)

            # Menú
            menubar = tk.Menu(self.root)
            self.root.config(menu=menubar)

            # Menú Archivo
            file_menu = tk.Menu(menubar, tearoff=0)
            menubar.add_cascade(label="Archivo", menu=file_menu)
            file_menu.add_command(label="Abrir", command=self.open_file)
            file_menu.add_command(label="Guardar", command=self.save_file)
            file_menu.add_command(label="Guardar como", command=self.save_file_as)
            file_menu.add_separator()  #esto agrega un separador
            file_menu.add_command(label="Salir", command=self.exit_app)

            # Menú Editar
            edit_menu = tk.Menu(menubar, tearoff=0)
            menubar.add_cascade(label="Editar", menu=edit_menu)
            edit_menu.add_command(label="Deshacer", command=self.undo)
            edit_menu.add_command(label="Rehacer", command=self.redo)

            # Menú Ayuda
            help_menu = tk.Menu(menubar, tearoff=0)
            menubar.add_cascade(label="Ayuda", menu=help_menu)
            help_menu.add_command(label="Información", command=self.show_info)
            help_menu.add_command(label="Manual de usuario", command=self.open_user_manual)
            help_menu.add_command(label="Integrantes", command=self.show_team_info)

        def open_file(self):
            file_path = filedialog.askopenfilename(filetypes=[("Archivos de texto", "*.txt")])
            if file_path:
                with open(file_path, "r") as file:
                    content = file.read()
                    self.text_area.delete("1.0", tk.END)
                    self.text_area.insert(tk.END, content)
                self.current_file = file_path

        def save_file(self):
            if self.current_file:
                content = self.text_area.get("1.0", tk.END)
                with open(self.current_file, "w") as file:
                    file.write(content)
            else:
                self.save_file_as()

        def save_file_as(self):
            file_path = filedialog.asksaveasfilename(defaultextension=".txt", filetypes=[("Archivos de texto", "*.txt")])
            if file_path:
                content = self.text_area.get("1.0", tk.END)
                with open(file_path, "w") as file:
                    file.write(content)
                self.current_file = file_path

        def exit_app(self):
            self.root.destroy()

        def show_info(self):
            messagebox.showinfo("Información", "Esta es una aplicación de editor de texto.")

        def open_user_manual(self):
            # Abre el manual de usuario
            import webbrowser
            webbrowser.open("https://drive.google.com/file/d/1Md1rMoHpYiOVbkAgJAsE8CGQYHdd1TEe/view?usp=drive_link")

        def show_team_info(self):
            messagebox.showinfo("Integrantes", "Fredy Roberto Orozco Alvarado 7690-22-16980, Proyecto 3 Grupo 9")

        def undo(self):
            if self.text_undo_stack:
                text = self.text_undo_stack.pop()
                self.text_redo_stack.append(self.text_area.get("1.0", tk.END))
                self.text_area.delete("1.0", tk.END)
                self.text_area.insert(tk.END, text)

        def redo(self):
            if self.text_redo_stack:
                text = self.text_redo_stack.pop()
                self.text_undo_stack.append(self.text_area.get("1.0", tk.END))
                self.text_area.delete("1.0", tk.END)
                self.text_area.insert(tk.END, text)

        def add_to_undo_stack(self, event):
            text = self.text_area.get("1.0", tk.END)
            self.text_undo_stack.append(text)
            self.text_redo_stack = []

    if __name__ == "__main__":
        root = tk.Tk()
        app = TextEditor(root)
        root.mainloop()
